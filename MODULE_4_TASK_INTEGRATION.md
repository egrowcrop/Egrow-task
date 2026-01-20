# MODULE 4: TASK INTEGRATION (Auto-Deduct Feature)

## 🔗 PHASE 2: Connect Inventory with Tasks

This module adds fertilizer selection to tasks and auto-deducts from inventory.

---

## STEP 1: Add Fertilizer Selection to Task Form HTML

**Find** your task form where you have the Task Type dropdown (around line 1600-1650).

**After** the Crop Type section (the one that shows for INDUCE), **ADD THIS:**

```html
<!-- Fertilizer Selection (shows for FERTILIZER task type) -->
<div id="fertilizerSection" class="fertilizer-selection" style="display: none;">
    <div class="fertilizer-selection-title">🧪 Select Fertilizer/Chemical</div>
    
    <div class="form-group">
        <label for="selectedFertilizer">Fertilizer/Chemical from Inventory</label>
        <select id="selectedFertilizer" class="form-control" onchange="updateFertilizerCost()">
            <option value="">-- Select from inventory --</option>
        </select>
    </div>
    
    <div class="form-row">
        <div class="form-group">
            <label for="fertQuantity">Quantity Used</label>
            <input type="number" id="fertQuantity" step="0.01" min="0" class="form-control" oninput="updateFertilizerCost()">
        </div>
        <div class="form-group">
            <label for="fertUnit">Unit</label>
            <input type="text" id="fertUnit" class="form-control" readonly style="background: #f5f5f5;">
        </div>
    </div>
    
    <div class="cost-display" id="costDisplay" style="display: none;">
        <div class="cost-label">Estimated Cost</div>
        <div class="cost-value" id="costValue">RM 0.00</div>
        <div class="stock-after" id="stockAfter"></div>
    </div>
</div>
```

---

## STEP 2: Add JavaScript for Task Type Change

**Find** your existing task type change handler. It should look something like:

```javascript
document.getElementById('taskType').addEventListener('change', function() {
    const cropSection = document.getElementById('cropTypeSection');
    if (this.value === 'INDUCE') {
        cropSection.style.display = 'block';
    } else {
        cropSection.style.display = 'none';
    }
});
```

**REPLACE IT WITH THIS:**

```javascript
document.getElementById('taskType').addEventListener('change', function() {
    const taskType = this.value;
    
    // Handle INDUCE crop type
    const cropSection = document.getElementById('cropTypeSection');
    if (taskType === 'INDUCE') {
        cropSection.style.display = 'block';
    } else {
        cropSection.style.display = 'none';
    }
    
    // Handle FERTILIZER inventory selection
    const fertSection = document.getElementById('fertilizerSection');
    if (taskType === 'FERTILIZER') {
        fertSection.style.display = 'block';
        populateFertilizerDropdown();
    } else {
        fertSection.style.display = 'none';
        clearFertilizerSelection();
    }
});
```

---

## STEP 3: Add Fertilizer Helper Functions

**Add these functions** before the closing `</script>` tag:

```javascript
// Populate Fertilizer Dropdown
function populateFertilizerDropdown() {
    const select = document.getElementById('selectedFertilizer');
    
    // Clear existing options except first
    select.innerHTML = '<option value="">-- Select from inventory --</option>';
    
    // Add inventory items
    inventory.forEach(item => {
        const option = document.createElement('option');
        option.value = item.id;
        option.textContent = `${item.name} (${item.quantity} ${item.unit} available)`;
        option.dataset.item = JSON.stringify(item);
        select.appendChild(option);
    });
    
    // Add listener
    select.addEventListener('change', function() {
        if (this.value) {
            const itemData = JSON.parse(this.options[this.selectedIndex].dataset.item);
            document.getElementById('fertUnit').value = itemData.unit;
        } else {
            document.getElementById('fertUnit').value = '';
            document.getElementById('fertQuantity').value = '';
        }
        updateFertilizerCost();
    });
}

// Update Cost Calculation
function updateFertilizerCost() {
    const select = document.getElementById('selectedFertilizer');
    const quantity = parseFloat(document.getElementById('fertQuantity').value) || 0;
    const costDisplay = document.getElementById('costDisplay');
    const costValue = document.getElementById('costValue');
    const stockAfter = document.getElementById('stockAfter');
    
    if (!select.value || quantity <= 0) {
        costDisplay.style.display = 'none';
        return;
    }
    
    const itemData = JSON.parse(select.options[select.selectedIndex].dataset.item);
    const cost = quantity * itemData.costPerUnit;
    const remaining = itemData.quantity - quantity;
    
    costDisplay.style.display = 'block';
    costValue.textContent = `RM ${cost.toFixed(2)}`;
    
    if (remaining < 0) {
        stockAfter.innerHTML = `<span style="color: #f44336;">⚠️ Insufficient stock! Only ${itemData.quantity} ${itemData.unit} available.</span>`;
    } else if (remaining < itemData.minStock) {
        stockAfter.innerHTML = `<span style="color: #ff9800;">⚠️ Stock after use: ${remaining.toFixed(2)} ${itemData.unit} (Below minimum: ${itemData.minStock} ${itemData.unit})</span>`;
    } else {
        stockAfter.innerHTML = `✓ Stock after use: ${remaining.toFixed(2)} ${itemData.unit}`;
    }
}

// Clear Fertilizer Selection
function clearFertilizerSelection() {
    document.getElementById('selectedFertilizer').value = '';
    document.getElementById('fertQuantity').value = '';
    document.getElementById('fertUnit').value = '';
    document.getElementById('costDisplay').style.display = 'none';
}
```

---

## STEP 4: Update Save Task Function

**Find** your `saveTask()` function. Look for where it creates the task object.

**ADD THIS** to the task object (after other fields):

```javascript
// In your saveTask function, add:

// Check if fertilizer was used
let inventoryUsed = null;
if (taskType === 'FERTILIZER') {
    const fertId = document.getElementById('selectedFertilizer').value;
    const fertQty = parseFloat(document.getElementById('fertQuantity').value);
    
    if (fertId && fertQty > 0) {
        const select = document.getElementById('selectedFertilizer');
        const itemData = JSON.parse(select.options[select.selectedIndex].dataset.item);
        
        // Validate stock
        if (fertQty > itemData.quantity) {
            showToast('⚠️ Insufficient stock!');
            return;
        }
        
        // Calculate cost
        const cost = fertQty * itemData.costPerUnit;
        
        // Prepare inventory data
        inventoryUsed = {
            itemId: parseInt(fertId),
            itemName: itemData.name,
            quantity: fertQty,
            unit: itemData.unit,
            costPerUnit: itemData.costPerUnit,
            totalCost: cost
        };
        
        // Deduct from inventory
        const invItem = inventory.find(i => i.id === parseInt(fertId));
        if (invItem) {
            invItem.quantity -= fertQty;
            localStorage.setItem('agroInventory', JSON.stringify(inventory));
            
            // Log transaction
            const transaction = {
                id: Date.now() + Math.random(),
                itemId: invItem.id,
                itemName: invItem.name,
                type: 'out',
                quantity: fertQty,
                unit: invItem.unit,
                date: taskDate,
                time: new Date().toLocaleTimeString(),
                taskId: task.id,
                operator: operator,
                cost: cost,
                balanceBefore: invItem.quantity + fertQty,
                balanceAfter: invItem.quantity
            };
            
            transactions.push(transaction);
            localStorage.setItem('agroTransactions', JSON.stringify(transactions));
        }
    }
}

// Add to task object
task.inventoryUsed = inventoryUsed;
```

---

## STEP 5: Update Form Reset

**Find** where you reset the form after saving. **ADD:**

```javascript
// In your form reset section:
clearFertilizerSelection();
```

---

## STEP 6: Display Cost in Task Records

**Find** your task display function (where you show task details).

**ADD** this to show inventory usage:

```javascript
// In your task record display, add:

${task.inventoryUsed ? `
    <div class="record-detail">
        <div class="record-detail-label">💰 Fertilizer Used</div>
        <div class="record-detail-value">
            ${task.inventoryUsed.itemName}: ${task.inventoryUsed.quantity} ${task.inventoryUsed.unit}
            <br><strong style="color: #2d5016;">Cost: RM ${task.inventoryUsed.totalCost.toFixed(2)}</strong>
        </div>
    </div>
` : ''}
```

---

## ✅ TESTING:

1. Create FERTILIZER task
2. See fertilizer dropdown appear
3. Select a fertilizer
4. Enter quantity
5. See cost calculated
6. Save task
7. Check inventory - stock should decrease!
8. Check task record - should show cost

---

## 🎉 COMPLETE!

Your inventory is now fully integrated with tasks!

**Auto-deduct working!** ✅
**Cost tracking working!** ✅
**Low stock alerts working!** ✅

---

## 📊 NEXT: Enhanced Excel Export

See **MODULE_5_EXPORT.md** for enhanced Excel export with costs.

# MODULE 3: INVENTORY JAVASCRIPT FUNCTIONS (PART 1)

## 📍 WHERE TO ADD:

Find your JavaScript section (around line 1750).
Look for where you declared:
```javascript
let tasks = JSON.parse(localStorage.getItem('agroTasks')) || [];
```

**RIGHT AFTER THAT LINE**, add these variables:

---

## 🔧 STEP 1: ADD VARIABLES

```javascript
// Inventory Data
let inventory = JSON.parse(localStorage.getItem('agroInventory')) || [];
let machinery = JSON.parse(localStorage.getItem('agroMachinery')) || [];
let transactions = JSON.parse(localStorage.getItem('agroTransactions')) || [];
let currentInventoryTab = 'fertilizers';
```

---

## 🔧 STEP 2: UPDATE switchView FUNCTION

Find your `switchView` function and **REPLACE IT** with this:

```javascript
function switchView(view) {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    document.querySelectorAll('.nav-tab').forEach(t => t.classList.remove('active'));
    
    if (view === 'entry') {
        document.getElementById('entryView').classList.add('active');
        event.target.classList.add('active');
    } else if (view === 'stats') {
        document.getElementById('statsView').classList.add('active');
        event.target.classList.add('active');
        renderCharts();
    } else if (view === 'records') {
        document.getElementById('recordsView').classList.add('active');
        event.target.classList.add('active');
    } else if (view === 'inventory') {
        document.getElementById('inventoryView').classList.add('active');
        event.target.classList.add('active');
        switchInventoryTab(currentInventoryTab);
    }
}
```

---

## 🔧 STEP 3: ADD INVENTORY FUNCTIONS

Find the **END** of your JavaScript (before closing `</script>` tag).

**ADD ALL THESE FUNCTIONS:**

```javascript
// ============================================
// INVENTORY MANAGEMENT FUNCTIONS
// ============================================

// Switch between Fertilizers and Machinery tabs
function switchInventoryTab(tab) {
    currentInventoryTab = tab;
    
    // Update tab styling
    document.querySelectorAll('.inventory-tab').forEach(t => {
        t.classList.remove('active');
    });
    
    if (tab === 'fertilizers') {
        document.getElementById('fertTab').classList.add('active');
        document.getElementById('fertilizersView').style.display = 'block';
        document.getElementById('machineryView').style.display = 'none';
        renderFertilizers();
    } else {
        document.getElementById('machTab').classList.add('active');
        document.getElementById('fertilizersView').style.display = 'none';
        document.getElementById('machineryView').style.display = 'block';
        renderMachinery();
    }
    
    checkAlerts();
}

// Show Add Inventory Modal
function showAddInventoryModal() {
    const type = currentInventoryTab;
    
    if (type === 'fertilizers') {
        const html = `
            <div class="modal-backdrop" onclick="closeInventoryModal()" style="position: fixed; top: 0; left: 0; right: 0; bottom: 0; background: rgba(0,0,0,0.5); z-index: 1000; display: flex; align-items: center; justify-content: center;">
                <div class="modal-content" onclick="event.stopPropagation()" style="background: white; padding: 30px; border-radius: 20px; max-width: 500px; width: 90%; max-height: 90vh; overflow-y: auto;">
                    <h3 style="margin-bottom: 20px; color: #2d5016;">➕ Add Fertilizer/Chemical</h3>
                    <form id="addInventoryForm" onsubmit="saveInventoryItem(event)">
                        <div style="margin-bottom: 15px;">
                            <label style="display: block; margin-bottom: 5px; font-weight: 600; color: #666;">Name *</label>
                            <input type="text" id="invName" required style="width: 100%; padding: 12px; border: 2px solid #e0e5d8; border-radius: 10px; font-size: 16px;">
                        </div>
                        <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 15px; margin-bottom: 15px;">
                            <div>
                                <label style="display: block; margin-bottom: 5px; font-weight: 600; color: #666;">Quantity *</label>
                                <input type="number" id="invQty" step="0.01" required style="width: 100%; padding: 12px; border: 2px solid #e0e5d8; border-radius: 10px; font-size: 16px;">
                            </div>
                            <div>
                                <label style="display: block; margin-bottom: 5px; font-weight: 600; color: #666;">Unit *</label>
                                <select id="invUnit" required style="width: 100%; padding: 12px; border: 2px solid #e0e5d8; border-radius: 10px; font-size: 16px;">
                                    <option value="kg">kg</option>
                                    <option value="g">g</option>
                                    <option value="L">L</option>
                                    <option value="ml">ml</option>
                                    <option value="bags">bags</option>
                                    <option value="tons">tons</option>
                                </select>
                            </div>
                        </div>
                        <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 15px; margin-bottom: 15px;">
                            <div>
                                <label style="display: block; margin-bottom: 5px; font-weight: 600; color: #666;">Cost per Unit (RM) *</label>
                                <input type="number" id="invCost" step="0.01" required style="width: 100%; padding: 12px; border: 2px solid #e0e5d8; border-radius: 10px; font-size: 16px;">
                            </div>
                            <div>
                                <label style="display: block; margin-bottom: 5px; font-weight: 600; color: #666;">Min Stock *</label>
                                <input type="number" id="invMinStock" step="0.01" required style="width: 100%; padding: 12px; border: 2px solid #e0e5d8; border-radius: 10px; font-size: 16px;">
                            </div>
                        </div>
                        <div style="margin-bottom: 15px;">
                            <label style="display: block; margin-bottom: 5px; font-weight: 600; color: #666;">Supplier</label>
                            <input type="text" id="invSupplier" style="width: 100%; padding: 12px; border: 2px solid #e0e5d8; border-radius: 10px; font-size: 16px;">
                        </div>
                        <div style="margin-bottom: 15px;">
                            <label style="display: block; margin-bottom: 5px; font-weight: 600; color: #666;">Location</label>
                            <input type="text" id="invLocation" style="width: 100%; padding: 12px; border: 2px solid #e0e5d8; border-radius: 10px; font-size: 16px;">
                        </div>
                        <div style="display: flex; gap: 10px; margin-top: 25px;">
                            <button type="submit" style="flex: 1; padding: 14px; background: #2d5016; color: white; border: none; border-radius: 10px; font-weight: 600; cursor: pointer; font-size: 1em;">Save</button>
                            <button type="button" onclick="closeInventoryModal()" style="flex: 1; padding: 14px; background: white; color: #666; border: 2px solid #e0e5d8; border-radius: 10px; font-weight: 600; cursor: pointer; font-size: 1em;">Cancel</button>
                        </div>
                    </form>
                </div>
            </div>
        `;
        document.body.insertAdjacentHTML('beforeend', html);
    } else {
        // Machinery modal
        const html = `
            <div class="modal-backdrop" onclick="closeInventoryModal()" style="position: fixed; top: 0; left: 0; right: 0; bottom: 0; background: rgba(0,0,0,0.5); z-index: 1000; display: flex; align-items: center; justify-content: center;">
                <div class="modal-content" onclick="event.stopPropagation()" style="background: white; padding: 30px; border-radius: 20px; max-width: 500px; width: 90%; max-height: 90vh; overflow-y: auto;">
                    <h3 style="margin-bottom: 20px; color: #2d5016;">➕ Add Machinery</h3>
                    <form id="addMachineryForm" onsubmit="saveMachinery(event)">
                        <div style="margin-bottom: 15px;">
                            <label style="display: block; margin-bottom: 5px; font-weight: 600; color: #666;">Name *</label>
                            <input type="text" id="machName" required style="width: 100%; padding: 12px; border: 2px solid #e0e5d8; border-radius: 10px; font-size: 16px;">
                        </div>
                        <div style="margin-bottom: 15px;">
                            <label style="display: block; margin-bottom: 5px; font-weight: 600; color: #666;">Type *</label>
                            <select id="machType" required style="width: 100%; padding: 12px; border: 2px solid #e0e5d8; border-radius: 10px; font-size: 16px;">
                                <option value="Tractor">Tractor</option>
                                <option value="Sprayer">Sprayer</option>
                                <option value="Harvester">Harvester</option>
                                <option value="Plow">Plow</option>
                                <option value="Excavator">Excavator</option>
                            </select>
                        </div>
                        <div style="margin-bottom: 15px;">
                            <label style="display: block; margin-bottom: 5px; font-weight: 600; color: #666;">Condition *</label>
                            <select id="machCondition" required style="width: 100%; padding: 12px; border: 2px solid #e0e5d8; border-radius: 10px; font-size: 16px;">
                                <option value="Excellent">Excellent</option>
                                <option value="Good">Good</option>
                                <option value="Fair">Fair</option>
                                <option value="Needs Repair">Needs Repair</option>
                            </select>
                        </div>
                        <div style="margin-bottom: 15px;">
                            <label style="display: block; margin-bottom: 5px; font-weight: 600; color: #666;">Operating Hours</label>
                            <input type="number" id="machHours" value="0" style="width: 100%; padding: 12px; border: 2px solid #e0e5d8; border-radius: 10px; font-size: 16px;">
                        </div>
                        <div style="margin-bottom: 15px;">
                            <label style="display: block; margin-bottom: 5px; font-weight: 600; color: #666;">Assigned Operator</label>
                            <input type="text" id="machOperator" style="width: 100%; padding: 12px; border: 2px solid #e0e5d8; border-radius: 10px; font-size: 16px;">
                        </div>
                        <div style="display: flex; gap: 10px; margin-top: 25px;">
                            <button type="submit" style="flex: 1; padding: 14px; background: #2d5016; color: white; border: none; border-radius: 10px; font-weight: 600; cursor: pointer; font-size: 1em;">Save</button>
                            <button type="button" onclick="closeInventoryModal()" style="flex: 1; padding: 14px; background: white; color: #666; border: 2px solid #e0e5d8; border-radius: 10px; font-weight: 600; cursor: pointer; font-size: 1em;">Cancel</button>
                        </div>
                    </form>
                </div>
            </div>
        `;
        document.body.insertAdjacentHTML('beforeend', html);
    }
}

function closeInventoryModal() {
    const backdrop = document.querySelector('.modal-backdrop');
    if (backdrop) backdrop.remove();
}

// Save Inventory Item
function saveInventoryItem(e) {
    e.preventDefault();
    
    const item = {
        id: Date.now(),
        category: 'fertilizer',
        name: document.getElementById('invName').value,
        quantity: parseFloat(document.getElementById('invQty').value),
        unit: document.getElementById('invUnit').value,
        costPerUnit: parseFloat(document.getElementById('invCost').value),
        minStock: parseFloat(document.getElementById('invMinStock').value),
        supplier: document.getElementById('invSupplier').value || '-',
        location: document.getElementById('invLocation').value || '-',
        purchaseDate: new Date().toISOString().split('T')[0]
    };
    
    inventory.push(item);
    localStorage.setItem('agroInventory', JSON.stringify(inventory));
    
    closeInventoryModal();
    renderFertilizers();
    checkAlerts();
    showToast('✅ Item added successfully!');
}

// Save Machinery
function saveMachinery(e) {
    e.preventDefault();
    
    const item = {
        id: Date.now(),
        name: document.getElementById('machName').value,
        type: document.getElementById('machType').value,
        condition: document.getElementById('machCondition').value,
        operatingHours: parseInt(document.getElementById('machHours').value) || 0,
        assignedOperator: document.getElementById('machOperator').value || '-'
    };
    
    machinery.push(item);
    localStorage.setItem('agroMachinery', JSON.stringify(machinery));
    
    closeInventoryModal();
    renderMachinery();
    showToast('✅ Machinery added successfully!');
}
```

**Continue to Part 2...**

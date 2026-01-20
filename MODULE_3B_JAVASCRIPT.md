# MODULE 3: INVENTORY JAVASCRIPT FUNCTIONS (PART 2)

## 🔧 CONTINUE ADDING THESE FUNCTIONS:

(Add right after the functions from Part 1)

```javascript
// Render Fertilizers List
function renderFertilizers() {
    const list = document.getElementById('fertilizersList');
    
    if (inventory.length === 0) {
        list.innerHTML = '<div style="text-align: center; padding: 60px 20px; color: #666;"><div style="font-size: 4em; margin-bottom: 20px;">📦</div><h3 style="margin-bottom: 10px;">No Items Yet</h3><p>Click "Add Item" to start tracking your fertilizers and chemicals.</p></div>';
        return;
    }
    
    list.innerHTML = inventory.map(item => {
        const isLowStock = item.quantity < item.minStock;
        const totalValue = (item.quantity * item.costPerUnit).toFixed(2);
        
        return `
            <div class="inventory-card ${isLowStock ? 'low-stock' : ''}">
                ${isLowStock ? '<div class="inventory-badge">⚠️ LOW</div>' : ''}
                <div class="inventory-card-header">
                    <div class="inventory-icon">🧪</div>
                    <div class="inventory-card-title">
                        <div class="inventory-card-name">${item.name}</div>
                        <div class="inventory-card-category">${item.unit.toUpperCase()}</div>
                    </div>
                </div>
                <div class="inventory-card-details">
                    <div>
                        <div class="inventory-detail-label">Stock</div>
                        <div class="inventory-detail-value ${isLowStock ? 'danger' : 'highlight'}">${item.quantity} ${item.unit}</div>
                    </div>
                    <div>
                        <div class="inventory-detail-label">Min: ${item.minStock} ${item.unit}</div>
                        <div class="inventory-detail-value">RM ${item.costPerUnit}/${item.unit}</div>
                    </div>
                    <div>
                        <div class="inventory-detail-label">Supplier</div>
                        <div class="inventory-detail-value">${item.supplier}</div>
                    </div>
                    <div>
                        <div class="inventory-detail-label">Location</div>
                        <div class="inventory-detail-value">${item.location}</div>
                    </div>
                </div>
                <div style="background: #f0f4e8; padding: 10px; border-radius: 8px; margin-bottom: 15px; text-align: center;">
                    <div style="font-size: 0.85em; color: #666; margin-bottom: 3px;">Total Value</div>
                    <div style="font-size: 1.3em; font-weight: 700; color: #2d5016;">RM ${totalValue}</div>
                </div>
                <div class="inventory-card-actions">
                    <button class="btn-inventory-action danger" onclick="deleteInventory(${item.id})">🗑️ Delete</button>
                </div>
            </div>
        `;
    }).join('');
}

// Render Machinery List
function renderMachinery() {
    const list = document.getElementById('machineryList');
    
    if (machinery.length === 0) {
        list.innerHTML = '<div style="text-align: center; padding: 60px 20px; color: #666;"><div style="font-size: 4em; margin-bottom: 20px;">🚜</div><h3 style="margin-bottom: 10px;">No Machinery Yet</h3><p>Click "Add Item" to start tracking your equipment.</p></div>';
        return;
    }
    
    list.innerHTML = machinery.map(item => `
        <div class="inventory-card machinery-card">
            <div class="inventory-card-header">
                <div class="inventory-icon">🚜</div>
                <div class="inventory-card-title">
                    <div class="inventory-card-name">${item.name}</div>
                    <div class="inventory-card-category">${item.type}</div>
                </div>
            </div>
            <div class="machinery-hours">
                <div class="machinery-hours-value">${item.operatingHours}</div>
                <div class="machinery-hours-label">Operating Hours</div>
            </div>
            <div class="inventory-card-details">
                <div>
                    <div class="inventory-detail-label">Condition</div>
                    <div class="inventory-detail-value">${item.condition}</div>
                </div>
                <div>
                    <div class="inventory-detail-label">Operator</div>
                    <div class="inventory-detail-value">${item.assignedOperator}</div>
                </div>
            </div>
            <div class="inventory-card-actions">
                <button class="btn-inventory-action danger" onclick="deleteMachinery(${item.id})">🗑️ Delete</button>
            </div>
        </div>
    `).join('');
}

// Delete Inventory Item
function deleteInventory(id) {
    if (confirm('Are you sure you want to delete this item?')) {
        inventory = inventory.filter(item => item.id !== id);
        localStorage.setItem('agroInventory', JSON.stringify(inventory));
        renderFertilizers();
        checkAlerts();
        showToast('✅ Item deleted');
    }
}

// Delete Machinery
function deleteMachinery(id) {
    if (confirm('Are you sure you want to delete this machinery?')) {
        machinery = machinery.filter(item => item.id !== id);
        localStorage.setItem('agroMachinery', JSON.stringify(machinery));
        renderMachinery();
        showToast('✅ Machinery deleted');
    }
}

// Check Alerts for Low Stock
function checkAlerts() {
    const alerts = [];
    
    inventory.forEach(item => {
        if (item.quantity < item.minStock) {
            alerts.push(`
                <div class="alert-item">
                    <strong>${item.name}</strong>: Only ${item.quantity} ${item.unit} left (Min: ${item.minStock} ${item.unit}) - <strong style="color: #f44336;">Reorder needed!</strong>
                </div>
            `);
        }
    });
    
    const alertsDiv = document.getElementById('inventoryAlerts');
    const alertsList = document.getElementById('alertsList');
    
    if (alerts.length > 0) {
        alertsDiv.classList.remove('hidden');
        alertsList.innerHTML = alerts.join('');
    } else {
        alertsDiv.classList.add('hidden');
    }
}

// Export Inventory to Excel
function exportInventory() {
    if (inventory.length === 0 && machinery.length === 0) {
        showToast('⚠️ No data to export');
        return;
    }
    
    const data = [];
    
    // Add fertilizers
    inventory.forEach(item => {
        data.push({
            'Category': 'Fertilizer/Chemical',
            'Name': item.name,
            'Quantity': item.quantity,
            'Unit': item.unit,
            'Cost per Unit (RM)': item.costPerUnit,
            'Total Value (RM)': (item.quantity * item.costPerUnit).toFixed(2),
            'Min Stock': item.minStock,
            'Status': item.quantity < item.minStock ? 'LOW STOCK ⚠️' : 'OK ✓',
            'Supplier': item.supplier,
            'Location': item.location,
            'Purchase Date': item.purchaseDate
        });
    });
    
    // Add machinery
    machinery.forEach(item => {
        data.push({
            'Category': 'Machinery',
            'Name': item.name,
            'Type': item.type,
            'Condition': item.condition,
            'Operating Hours': item.operatingHours,
            'Operator': item.assignedOperator
        });
    });
    
    const ws = XLSX.utils.json_to_sheet(data);
    const wb = XLSX.utils.book_new();
    XLSX.utils.book_append_sheet(wb, ws, 'Inventory');
    
    ws['!cols'] = [
        {wch: 20}, {wch: 25}, {wch: 12}, {wch: 8}, 
        {wch: 15}, {wch: 15}, {wch: 12}, {wch: 15}, 
        {wch: 20}, {wch: 15}, {wch: 15}
    ];
    
    const filename = `Inventory_${new Date().toISOString().split('T')[0]}.xlsx`;
    XLSX.writeFile(wb, filename);
    
    showToast('✅ Inventory exported!');
}
```

---

## ✅ MODULE 3 COMPLETE!

All JavaScript functions are now added!

**Next:** Module 4 - Task Integration (Auto-deduct)

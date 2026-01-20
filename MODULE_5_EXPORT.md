# MODULE 5: ENHANCED EXCEL EXPORT

## 📊 Add Cost Tracking to Task Export

This module updates your Excel export to include fertilizer costs.

---

## STEP 1: Update Export Function

**Find** your existing `exportToExcel()` function.

Look for where you create the data array. You'll see something like:

```javascript
const data = filteredTasks.map(task => ({
    'Date': task.date,
    'Day': task.day,
    'Task Type': task.taskType,
    // ... other fields
}));
```

**REPLACE** the data mapping with this **ENHANCED VERSION:**

```javascript
const data = filteredTasks.map(task => {
    const row = {
        'Date': task.date,
        'Day': task.day,
        'Task Type': task.taskType
    };
    
    // Add crop type if exists
    if (task.cropType) {
        row['Crop Type'] = task.cropType;
    }
    
    // Add fertilizer usage if exists
    if (task.inventoryUsed) {
        row['Fertilizer Used'] = task.inventoryUsed.itemName;
        row['Quantity Used'] = `${task.inventoryUsed.quantity} ${task.inventoryUsed.unit}`;
        row['Cost (RM)'] = task.inventoryUsed.totalCost.toFixed(2);
    } else {
        row['Fertilizer Used'] = '-';
        row['Quantity Used'] = '-';
        row['Cost (RM)'] = '-';
    }
    
    // Continue with other fields
    row['Time Start'] = task.timeStart;
    row['Time End'] = task.timeEnd || '-';
    row['Duration (hours)'] = task.timeEnd ? calculateDuration(task.timeStart, task.timeEnd) : '-';
    row['Operator'] = task.operator;
    row['Work Notes'] = task.workNotes || '-';
    row['Weather'] = task.weather || '-';
    row['Status'] = task.status;
    row['Before Photos'] = task.beforePhotos ? task.beforePhotos.length : 0;
    row['After Photos'] = task.afterPhotos ? task.afterPhotos.length : 0;
    
    return row;
});
```

---

## STEP 2: Update Column Widths

**Find** where you set column widths:

```javascript
ws['!cols'] = [
    // existing columns...
];
```

**UPDATE** to include new columns:

```javascript
ws['!cols'] = [
    {wch: 12},  // Date
    {wch: 10},  // Day
    {wch: 18},  // Task Type
    {wch: 12},  // Crop Type
    {wch: 25},  // Fertilizer Used (NEW)
    {wch: 15},  // Quantity Used (NEW)
    {wch: 12},  // Cost RM (NEW)
    {wch: 10},  // Time Start
    {wch: 10},  // Time End
    {wch: 12},  // Duration
    {wch: 15},  // Operator
    {wch: 30},  // Work Notes
    {wch: 12},  // Weather
    {wch: 12},  // Status
    {wch: 12},  // Before Photos
    {wch: 12}   // After Photos
];
```

---

## STEP 3: Add Cost Summary Sheet (BONUS!)

**ADD THIS** function to create a summary sheet:

```javascript
function exportTasksWithSummary() {
    const filteredTasks = currentFilter === 'all' 
        ? tasks 
        : tasks.filter(task => task.taskType === currentFilter);
    
    if (filteredTasks.length === 0) {
        showToast('⚠️ No tasks to export');
        return;
    }
    
    // Create workbook
    const wb = XLSX.utils.book_new();
    
    // Sheet 1: All Tasks (existing export)
    const data = filteredTasks.map(task => {
        const row = {
            'Date': task.date,
            'Day': task.day,
            'Task Type': task.taskType
        };
        
        if (task.cropType) row['Crop Type'] = task.cropType;
        
        if (task.inventoryUsed) {
            row['Fertilizer Used'] = task.inventoryUsed.itemName;
            row['Quantity Used'] = `${task.inventoryUsed.quantity} ${task.inventoryUsed.unit}`;
            row['Cost (RM)'] = task.inventoryUsed.totalCost.toFixed(2);
        } else {
            row['Fertilizer Used'] = '-';
            row['Quantity Used'] = '-';
            row['Cost (RM)'] = '-';
        }
        
        row['Time Start'] = task.timeStart;
        row['Time End'] = task.timeEnd || '-';
        row['Duration (hours)'] = task.timeEnd ? calculateDuration(task.timeStart, task.timeEnd) : '-';
        row['Operator'] = task.operator;
        row['Work Notes'] = task.workNotes || '-';
        row['Weather'] = task.weather || '-';
        row['Status'] = task.status;
        row['Before Photos'] = task.beforePhotos ? task.beforePhotos.length : 0;
        row['After Photos'] = task.afterPhotos ? task.afterPhotos.length : 0;
        
        return row;
    });
    
    const ws1 = XLSX.utils.json_to_sheet(data);
    ws1['!cols'] = [
        {wch: 12}, {wch: 10}, {wch: 18}, {wch: 12}, 
        {wch: 25}, {wch: 15}, {wch: 12}, {wch: 10}, 
        {wch: 10}, {wch: 12}, {wch: 15}, {wch: 30}, 
        {wch: 12}, {wch: 12}, {wch: 12}, {wch: 12}
    ];
    
    XLSX.utils.book_append_sheet(wb, ws1, 'All Tasks');
    
    // Sheet 2: Cost Summary
    const tasksWithCosts = filteredTasks.filter(t => t.inventoryUsed);
    
    if (tasksWithCosts.length > 0) {
        const costData = tasksWithCosts.map(task => ({
            'Date': task.date,
            'Task Type': task.taskType,
            'Fertilizer': task.inventoryUsed.itemName,
            'Quantity': task.inventoryUsed.quantity,
            'Unit': task.inventoryUsed.unit,
            'Cost per Unit (RM)': task.inventoryUsed.costPerUnit.toFixed(2),
            'Total Cost (RM)': task.inventoryUsed.totalCost.toFixed(2),
            'Operator': task.operator
        }));
        
        // Add totals row
        const totalCost = tasksWithCosts.reduce((sum, t) => sum + t.inventoryUsed.totalCost, 0);
        costData.push({
            'Date': '',
            'Task Type': '',
            'Fertilizer': '',
            'Quantity': '',
            'Unit': '',
            'Cost per Unit (RM)': 'TOTAL:',
            'Total Cost (RM)': totalCost.toFixed(2),
            'Operator': ''
        });
        
        const ws2 = XLSX.utils.json_to_sheet(costData);
        ws2['!cols'] = [
            {wch: 12}, {wch: 18}, {wch: 25}, {wch: 10}, 
            {wch: 8}, {wch: 18}, {wch: 15}, {wch: 15}
        ];
        
        XLSX.utils.book_append_sheet(wb, ws2, 'Cost Summary');
    }
    
    // Download
    const filename = `AgroTasks_${new Date().toISOString().split('T')[0]}.xlsx`;
    XLSX.writeFile(wb, filename);
    
    showToast('✅ Tasks exported with cost summary!');
}
```

---

## STEP 4: Update Export Button

**Find** your export button in the Records view.

**CHANGE** the onclick from:
```html
onclick="exportToExcel()"
```

**TO:**
```html
onclick="exportTasksWithSummary()"
```

---

## ✅ WHAT YOU GET:

**Sheet 1: All Tasks**
- Complete task list
- Fertilizer usage
- Costs per task
- All existing fields

**Sheet 2: Cost Summary**
- Only tasks with fertilizer costs
- Detailed cost breakdown
- TOTAL cost at bottom
- Perfect for accounting!

---

## 📊 EXAMPLE OUTPUT:

**Cost Summary Sheet:**
```
Date       | Task Type  | Fertilizer  | Qty | Unit | Cost/Unit | Total | Operator
-----------|------------|-------------|-----|------|-----------|-------|----------
2026-01-17 | FERTILIZER | NPK         | 20  | kg   | 5.50      | 110.00| John
2026-01-18 | FERTILIZER | Urea        | 15  | kg   | 4.20      | 63.00 | Mary
2026-01-19 | FERTILIZER | NPK         | 25  | kg   | 5.50      | 137.50| John
                                              TOTAL:        310.50
```

---

## ✅ TESTING:

1. Create tasks with fertilizer
2. Click Export in Records
3. Open Excel file
4. Check "All Tasks" sheet - see costs
5. Check "Cost Summary" sheet - see totals
6. Perfect for boss review! 👔

---

## 🎉 MODULE 5 COMPLETE!

You now have professional cost tracking and reporting!

---

**All Modules Complete!** 🎊

See **FINAL_CHECKLIST.md** for testing and deployment.

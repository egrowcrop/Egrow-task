# MODULE 2: INVENTORY CSS STYLES

## 📍 WHERE TO ADD:

Find your CSS `<style>` section (around line 24-1550).
Look for the **closing** `</style>` tag.

**BEFORE** the `</style>` tag, add this complete CSS:

---

## 🎨 COPY THIS ENTIRE SECTION:

```css
/* ============================================
   INVENTORY MANAGEMENT STYLES
   ============================================ */

.inventory-tabs {
    display: flex;
    gap: 10px;
    margin-bottom: 25px;
    border-bottom: 2px solid var(--border);
}

.inventory-tab {
    padding: 15px 30px;
    background: none;
    border: none;
    border-bottom: 3px solid transparent;
    font-weight: 600;
    color: var(--text-light);
    cursor: pointer;
    transition: all 0.3s ease;
    font-size: 1em;
}

.inventory-tab.active {
    color: var(--primary);
    border-bottom-color: var(--primary);
}

.inventory-tab:hover {
    color: var(--primary);
}

.inventory-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 25px;
    flex-wrap: wrap;
    gap: 15px;
}

.inventory-actions {
    display: flex;
    gap: 10px;
    flex-wrap: wrap;
}

.btn-add-inventory {
    background: linear-gradient(135deg, var(--primary) 0%, var(--primary-light) 100%);
    color: white;
    padding: 12px 25px;
    border: none;
    border-radius: 10px;
    font-weight: 600;
    cursor: pointer;
    display: flex;
    align-items: center;
    gap: 8px;
    transition: all 0.3s ease;
    font-family: 'Outfit', sans-serif;
    font-size: 1em;
}

.btn-add-inventory:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 15px rgba(45, 80, 22, 0.3);
}

.inventory-alerts {
    background: linear-gradient(135deg, #fff3e0 0%, #ffe0b2 100%);
    padding: 20px;
    border-radius: 15px;
    margin-bottom: 25px;
    border-left: 5px solid #ff9800;
}

.inventory-alerts.hidden {
    display: none;
}

.alert-title {
    font-weight: 700;
    color: #e65100;
    margin-bottom: 15px;
    display: flex;
    align-items: center;
    gap: 8px;
    font-size: 1.1em;
}

#alertsList {
    display: flex;
    flex-direction: column;
    gap: 8px;
}

.alert-item {
    padding: 12px;
    background: white;
    border-radius: 8px;
    border-left: 4px solid #f44336;
    font-size: 0.95em;
}

.inventory-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
    gap: 20px;
    margin-bottom: 30px;
}

.inventory-card {
    background: var(--card-bg);
    padding: 20px;
    border-radius: 15px;
    box-shadow: 0 2px 10px var(--shadow);
    border-left: 4px solid var(--primary);
    transition: all 0.3s ease;
    position: relative;
}

.inventory-card:hover {
    transform: translateY(-3px);
    box-shadow: 0 4px 15px var(--shadow);
}

.inventory-card.low-stock {
    border-left-color: #f44336;
    background: linear-gradient(to right, #ffebee 0%, white 50%);
}

.inventory-badge {
    position: absolute;
    top: 15px;
    right: 15px;
    padding: 4px 10px;
    border-radius: 12px;
    font-size: 0.75em;
    font-weight: 700;
    text-transform: uppercase;
    background: #f44336;
    color: white;
}

.inventory-card-header {
    display: flex;
    align-items: center;
    gap: 12px;
    margin-bottom: 15px;
}

.inventory-icon {
    font-size: 2em;
}

.inventory-card-title {
    flex: 1;
}

.inventory-card-name {
    font-size: 1.1em;
    font-weight: 700;
    color: var(--primary);
    margin-bottom: 3px;
}

.inventory-card-category {
    font-size: 0.85em;
    color: var(--text-light);
}

.inventory-card-details {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 10px;
    margin-bottom: 15px;
    font-size: 0.9em;
}

.inventory-detail-label {
    color: var(--text-light);
    font-size: 0.8em;
    margin-bottom: 3px;
}

.inventory-detail-value {
    font-weight: 600;
    color: var(--text);
}

.inventory-detail-value.highlight {
    color: var(--primary);
    font-size: 1.1em;
}

.inventory-detail-value.danger {
    color: #f44336;
}

.inventory-card-actions {
    display: flex;
    gap: 8px;
    padding-top: 15px;
    border-top: 1px solid var(--border);
}

.btn-inventory-action {
    flex: 1;
    padding: 8px 12px;
    border: 1px solid var(--border);
    border-radius: 8px;
    background: white;
    color: var(--primary);
    font-size: 0.85em;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.2s ease;
}

.btn-inventory-action:hover {
    background: var(--primary);
    color: white;
    border-color: var(--primary);
}

.btn-inventory-action.danger:hover {
    background: #f44336;
    border-color: #f44336;
    color: white;
}

.machinery-card {
    border-left-color: #2196f3;
}

.machinery-hours {
    background: #e3f2fd;
    padding: 12px;
    border-radius: 8px;
    text-align: center;
    margin-bottom: 15px;
}

.machinery-hours-value {
    font-size: 1.5em;
    font-weight: 700;
    color: #1976d2;
    font-family: 'Space Mono', monospace;
}

.machinery-hours-label {
    font-size: 0.85em;
    color: var(--text-light);
    margin-top: 3px;
}

/* Fertilizer Selection in Task Form */
.fertilizer-selection {
    background: #e8f5e9;
    padding: 20px;
    border-radius: 12px;
    margin: 20px 0;
    border: 2px solid var(--primary-light);
}

.fertilizer-selection-title {
    font-weight: 700;
    color: var(--primary);
    margin-bottom: 15px;
    display: flex;
    align-items: center;
    gap: 8px;
}

.cost-display {
    background: white;
    padding: 15px;
    border-radius: 8px;
    margin-top: 15px;
    text-align: center;
}

.cost-label {
    font-size: 0.9em;
    color: var(--text-light);
    margin-bottom: 5px;
}

.cost-value {
    font-size: 1.8em;
    font-weight: 700;
    color: var(--primary);
    font-family: 'Space Mono', monospace;
}

.stock-after {
    margin-top: 10px;
    font-size: 0.9em;
    color: var(--text-light);
}

/* Mobile Responsive */
@media (max-width: 768px) {
    .inventory-grid {
        grid-template-columns: 1fr;
    }
    
    .inventory-tabs {
        overflow-x: auto;
        -webkit-overflow-scrolling: touch;
    }
    
    .inventory-tab {
        white-space: nowrap;
        padding: 12px 20px;
        font-size: 0.9em;
    }
    
    .inventory-header {
        flex-direction: column;
        align-items: stretch;
    }
    
    .inventory-actions {
        width: 100%;
    }
    
    .btn-add-inventory {
        width: 100%;
        justify-content: center;
    }
    
    .inventory-card-details {
        grid-template-columns: 1fr;
    }
}
```

---

## ✅ DONE!

That's Module 2 complete. All CSS styles are ready.

**Next:** Module 3 - JavaScript Functions

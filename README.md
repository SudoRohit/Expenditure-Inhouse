# Expenditure Inhouse
Expenditure Inhouse form in Frappe-ERPNext

## For this we need two doctypes:-

### 1. Item Ein

Tick "is Child Table"

#### Fields:-

1. Label-Item Code, Type-Link, Mandatory, Options-Item.

2. Label-Cost, Type-Int.
   - Click on edit.
   - Enter "item_code.valuation_rate" in "Fetch From" field.
   - Tick "Fetch If Empty".

3. Label-Quantity, Type-Int, Mandatory.

4. Label-Amount, Type-Int.
   - Click on edit.
   - Go to Permissions and tick "Read Only".

### 2. Expenditure Inhouse

#### Fields:-

1. Label-Man hours (Days), Type-Int, Mandatory.


2. Label-Labour cost, Type-Int, Mandatory.

3. Label-Details of items consumed, Type-Table, Mandatory, Options-Item Ein.

4. Label-Item cost, Type-Int.
   - Click on edit.
   - Go to Permissions and tick "Read Only".

5. Label-Total cost, Type-Int.
   - Click on edit.
   - Go to Permissions and tick "Read Only".

6. Label-Chargeable to the user, Type-Select, Mandatory, Options-"Degree,Diploma,Trust".

#### Client Script

```
//Script to calculate amount (cost * quantity)
frappe.ui.form.on("Item Ein", {
	cost: function(frm,cdt, cdn){
		calculate_total(frm, cdt, cdn);
	},
	quantity: function(frm, cdt, cdn){
		calculate_total(frm, cdt, cdn);
	}
});
var calculate_total = function(frm, cdt, cdn) {
	var child = locals[cdt][cdn];
	frappe.model.set_value(cdt, cdn, "amount", child.cost * child.quantity);
};




//Script to calculate item cost (sum of amount)
frappe.ui.form.on('Expenditure Inhouse', {
    validate: function(frm,cdt,cdn) {
        set_item_cost(frm);
    }
});

function set_item_cost(frm) {
    var doc = locals[frm.doc.doctype][frm.doc.name];
    var item_cost = 0;
    $.each(doc.details_of_items_consumed, function(i, d) {
        item_cost += flt(d.amount);
    });

frm.set_value("item_cost",item_cost);
}




//Script to calculate total cost (labour cost + item cost)
frappe.ui.form.on('Expenditure Inhouse', {
    refresh(frm){
        frm.doc.total_cost = frm.doc.labour_cost + frm.doc.item_cost;
        refresh_field("total_cost");
    }
});
```

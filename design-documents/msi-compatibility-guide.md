# MSI compatibility guide

## Terms and definition

**CatalogInventory** is **old inventory**

**MSI** is **new inventory**

## Document purpose

This document specifies how to handle development which touches the inventory domain.

##Bugfix

* Issue that has an origin in `CatalogInventory` module
    and does not introduce a new behaviour MUST be 
    fixed in the scope of CatalogInventory module only.
* Issue that has an origin in `CatalogInventory` and introduces
    new behaviour in the scope of `CatalogInventory` MUST
    be escalated to a corresponding product owner. In normal,
    `CatalogInventory` should not have new scenarios. In case,
    the new scenario requested this scenario SHOULD be validated
    with `MSI` functionality to be sure that both of inventory
    management system have a feature parity. We need this to guarantee
    a possibility of upgrade from the old inventory to the new one.
* Issue that has an origin not in inventory modules but invokes
    method of inventory services, customizes configuration or 
    blocks of inventory services, etc has to be fixed for both 
    inventory services. The only exception is when the functionality
    is exclusively declared for the new inventory.
    
    For example we have a bug report which says:


```
**ISSUE**
Wishlist Quantity field is allowing for input that causes undesired results with the wishlist functionality. It is possible to enter very large numbers as well as letters into the quantity field. 
**STEPS TO REPRODUCE**
1 Log in as customer
2 Add product to wishlist
3 Go to My Wishlist
4 In quantity field for product in the wishlist enter a large number such as 777777777777. (Entire number will not be visible but you can verify by copying/pasting or searching. See attached screen shot)
5 Press enter
6 Amount changes to 99999999.9999
**EXPECTED RESULTS**
Quantity field should have limits on characters and number of characters entered. Quantity should not change.
**ACTUAL RESULTS**
Customers can enter very large number and letters in the quantity field, causing wishlist to change quantity.
```

To fix this issues we have to get some knowledge from an inventory service to get allowed quantity from an item configuration.
Due to the fact that we have two implementations the issue has to be fixed to account them both.
Wish List MUST NOT rely on any of the inventory services either new or old.
This is a new behaviour that should work no matter which inventory service is in use.
In fact, the solution MUST be delivered as the two separate extensions.
One will depends on Wishlist and CatalogInventory (WishlistCatalogInventory) other one on Wishlist and Inventory (WishlistInventory).
Depends on merchant instance configuration the required extension will be loaded by a composer and enabled.
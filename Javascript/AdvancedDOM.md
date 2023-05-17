![[Screenshot 2023-02-09 at 22.15.14.png]]

The dom is the interface between js and the browser, every element in the html is a node, and each node is represented by a JavaScript object

![[Screenshot 2023-02-09 at 22.24.03.png]]

## NodeList - HTML collection

The difference between a NodeList and a HTML collection is that the HTML collection all the actual elements inside itself, and it also updates itself automatically, if you delete an item it will automatically be removed from the lHTML collection. This does not happen with the NodeList, the time you created it the elements it will have, no matter what you remove or add to the html you will need to manually update the NodeList.


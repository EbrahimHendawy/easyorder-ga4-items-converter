# EasyOrder GA4 Converter

## Author
Ebrahim Hendawy

## Platform
Easy Order

## Description
This Google Tag Manager (GTM) solution addresses an issue with the data layer structure in the Easy Order platform, ensuring proper conversion of e-commerce data for Google Analytics 4 (GA4) tracking.

## Problem
The Easy Order platform's data layer structure was not directly compatible with GA4's expected format for e-commerce events, particularly for the `items` array in purchase events.

## Solution
A custom JavaScript variable in Google Tag Manager that converts the Easy Order data layer structure to the format expected by GA4.

## Implementation

1. In Google Tag Manager, create a new Custom JavaScript variable.
2. Name it "GA4 Items Converter".
3. Paste the following code:

```javascript
function() {
  var findItems = function(obj) {
    if (Array.isArray(obj)) return obj;
    if (obj && typeof obj === 'object') {
      for (var key in obj) {
        if (key === 'items' && Array.isArray(obj[key])) return obj[key];
        var result = findItems(obj[key]);
        if (result) return result;
      }
    }
    return null;
  };

  var dataLayer = window.dataLayer || [];
  var items = findItems(dataLayer[dataLayer.length - 1]) || [];

  return items.map(function(item) {
    return {
      item_id: item.id || item.item_id || '',
      item_name: item.name || item.item_name || '',
      price: parseFloat(item.price) || 0,
      quantity: parseInt(item.quantity) || 1,
      // Add more parameters as needed
    };
  });
}
```

4. In your GA4 event tag (e.g., for purchase events), use this variable for the "Items" field.

## How it Works
This converter function:
- Recursively searches the data layer for an 'items' array
- Handles various potential structures of the data layer
- Converts each item to the structure expected by GA4
- Ensures proper data types (e.g., price as float, quantity as integer)

## Maintenance
If the Easy Order platform changes its data layer structure, this converter may need to be updated. Always test after platform updates to ensure continued functionality.

## Support
For issues or questions, please contact Ebrahim Hendawy.

---

Remember to test thoroughly in your GTM container's preview mode and verify that the data is being correctly sent to GA4.

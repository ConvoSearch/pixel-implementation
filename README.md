# ConvoSearch Integration Guide

## Prerequisites
- Your product feed in one of these formats:
  - Google Shopping Feed (JSON format)
  - Custom JSON product feed
  - Standard product feed format
- Developer access to your platform
- Index ID from ConvoSearch (obtain from app.conversecart.com)
- Contact shardul@convosearch.com to get started

## Step 1: Product Feed Setup

### Option A: Create a Custom Product Feed Endpoint
Create a public endpoint (e.g., `yourdomain.com/product_feed.json`) that provides your product data in one of these formats:

1. Default JSON Format:
```json
[
    {
        "id": "unique_product_id",
        "title": "Product Name",
        "description": "Product Description",
        "price": 99.99,
        "currency": "USD",
        "image_url": "https://...",
        "url": "https://..."
    },
    {
        "id": "unique_product_id_2",
        "title": "Second Product",
        "description": "Second Product Description",
        "price": 149.99,
        "currency": "USD",
        "image_url": "https://...",
        "url": "https://..."
    }
]
```

2. Google Shopping Feed converted to JSON
3. Any standard product feed format (coordinate with ConvoSearch team for specific requirements)

### Option B: Use Existing API Endpoint
If you already have an API endpoint that serves product data, provide the endpoint URL and necessary authentication credentials to the ConvoSearch team.

## Step 2: Index Creation and Setup

1. Get your unique Index ID from app.conversecart.com
2. Contact ConvoSearch team to:
   - Set up automatic sync for real-time product updates
   - Configure initial product database indexing

## Step 3: Pixel Installation

### 3.1 Add Tracking Script
Add this script to the `<head>` section of all your web pages:

```javascript
<script>
!function(w,d,e,v,n,t,s)
{if(w.cctag)return;n=w.cctag=function(){n.callMethod?
n.callMethod.apply(n,arguments):n.queue.push(arguments)};
if(!w._cctag)w._cctag=n;n.push=n;n.loaded=!0;n.version='1.0';
n.queue=[];t=d.createElement(e);t.async=true;
t.src=v;
t.onload = function() {
    cctag('init', 'YOUR_INDEX_ID'); // Replace with your Index ID from app.conversecart.com
};
s=d.getElementsByTagName(e)[0];
s.parentNode.insertBefore(t,s)}(window,document,'script',
'https://www.conversecart.com/conversecart.js');
</script>
```

### 3.2 Implement Event Tracking
Add these tracking codes for various user interactions:

#### Product View Tracking
```javascript
setTimeout(function() {
    cctag('track', 'trackDocumentViewed', {
        mpn: productid.toString()
    });
}, 1000);
```

#### Add to Cart Tracking
```javascript
cctag("track", "AddToCart", {
    items: [{
        item_id: productid,
        item_quantity: quantity,
        item_meta: [{
            price: amount,
            currency: currencyCode
        }]
    }]
});
```

#### Search Result Click Tracking
```javascript
setTimeout(() => {
    cctag('track', 'trackSearchResultClick', {
        mpn: productId,
        rank: searchResults.rank,
        searchID: searchResults.searchID
    });
}, 2000);
```

#### Purchase Tracking
```javascript
// Collect line items
const cctag_items = lineItems.map(item => ({
    item_id: item.id.toString(),
    price: parseFloat(item.finalLinePrice),
    item_quantity: item.quantity
}));

setTimeout(function() {
    cctag("track", "purchase", { 
        items: cctag_items 
    });
}, 2000);
```

## Step 4: Search API Integration

### 4.1 Session Management

First, implement session handling using one of these methods:

1. JavaScript Function:
```javascript
getSessionID();
```

2. Cookie Access:
Read the `ccsessionID` cookie

3. Create New Session:
```javascript
Endpoint: GET https://api.conversecart.com/sessions/createSession

Required Parameters:
- indexID: string (Your Index ID)

Response:
{
    "sessionID": "<SessionID here>",
    "sessionKey": "<SessionKeyHere>"
}
```

### 4.2 Search API Implementation

#### Standard Search
For simple JSON feed:
```
GET https://api.conversecart.com/search/
```

For custom/nested feed:
```
GET https://api.conversecart.com/shopify_search/
```

Required Parameters:
- query (string): The search query string
- indexID (string): Your Index ID

Optional Parameters:
- sessionID (string): User's search session ID
- top_k (integer): Maximum number of results (default: 30)
- loc (integer): Location of the search query (default: 1)

Response Format:
```json
{
    "searchID": "<searchID>",
    "results": [...]
}
```

#### Similar Products Search
For default JSON feed:
```
GET https://api.conversecart.com/search/similar_search
```

For custom feed:
```
GET https://api.conversecart.com/search/similar_search_shopify
```

Required Parameters:
- documentID (string): ID of the document to find similar items to
- indexID (string): Your Index ID

Optional Parameters:
- sessionID (string): User's search session ID
- top_k (integer): Maximum number of results (default: 30)
- loc (integer): Location of the search query (default: 1)

Response Format:
```json
{
    "searchID": "<searchID>",
    "results": [...]
}
```

## Support
For additional assistance or questions:
- Email: shardul@convosearch.com
- Documentation: https://convosearch.com/docs
- Dashboard: app.conversecart.com

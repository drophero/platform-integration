Integrating eCommerce platforms into DropHero
====================

#Table of contents
- [Introduction](#introduction)
- [API communication protocol & security](#api-communication-protocol--security)
- [Plugin Installation procedure](#plugin-installation-procedure)
- [Category synchronization](#category-synchronization)
- [Product catalog synchronization](#product-catalog-synchronization)
- [Orders synchronization & status management](#orders-synchronization--status-management)

#Introduction

The purpose of this document is to give you an exhaustive documentation about DropHero and his communication protocols to allow you to integrate any eCommerce with DropHero. It covers all aspects including catalog synchronization and order synchronization through our API service.

#API communication protocol & security.

[Access API v1 Documentation](https://github.com/drophero/api-documentation/blob/master/v1/README.md).

If you want to integrate any component with DropHero you must use our API. You can find all details [here](https://github.com/drophero/api-documentation).

#What are plugin callbacks?

This is the way we use to **knok your door** when we need to tell you something. Is a public url hidden in the plugin server that allows us to visit you.

You need to create these "callbacks" in installation time to allow DropHero to know your door. These are 2 url's only known by us and you send to DropHero by using <code>[notify](https://github.com/drophero/api-documentation/blob/master/v1/sections/notify.md)</code> method during installation time.

We only perform a simple http request to these callbacks, that you must "catch" and act depending on witch we called:

- If we call to your catalog callback, we expect a 200 response code and you must perform a <code>GET /v1/subscription/from</code> call to DropHero because there's new products to update.
- If we call to your orders callback, we expect a 200 response code and you must perform a <code>GET /v1/orders</code> to check all your order statuses because somethig has changed.

**If we make a call to any of these two callbacks and we not get a 200 response code, we will try again in some minutes. If after a few attempts we can't reach you, we will mail this account reporting problems.**

#Plugin Installation procedure.

In order to standarize the installation protocol across all eCommerce platforms, we're using a simple step by step process you need to follow to ensure we keep all your clients synced and in place.

![Plugin Installation procedure](https://www.github.com/drophero/platform-integration/raw/master/img/plugin_installation_procedure.png "Plugin Installation procedure")

#### Step 3 - Client privides his own keys

As we mention in the next chapter, you must provide a good control panel to allow your users to configure the plugin behavior. This includes two files, one for the Public Key and another for the Secret key. As this is sensible data, **you must ensure this keys are stored out of public access and correctly encrypted**.

#### Step 4 - Plugin first contact

This is when the plugin says "hello" to DropHero and reveals itself to us. In order to achieve this you must perform a call using <code>[notify](https://github.com/drophero/api-documentation/blob/master/v1/sections/notify.md)</code> function of our API. You can [access to the proper documentation by clicking here](https://github.com/drophero/api-documentation/blob/master/v1/sections/notify.md).

You must provide at least 2 things:
- System message <code>plugin installed</code>
- System message [2 url callbacks](#what-are-plugin-callbacks)

Example:

```JSON
{
	"1": {
	        "msgtype": "system",
	        "message": "plugin installed"
	       },
	"2": {
	    "msgtype": "system",
	    "message": {
	        "callbacks_url": {
	            "orderstatus": "plugindomain.com/userdesired/orders/url",
	            "products": "plugindomain.com/userdesired/products/url"
	        }
	    }
	}
}
```

#### Step 5 - Sending eCommerce categories

In order to send you correctly your local categories assocciated to each product during catalog sync, we need you send to us all the **public categories** this eCommerce has.

Please go to [Category sync step](#category-synchronization) for further details.

#### Step 6 - First Catalog synchronization

Normally this process will not generate any movement if it's the first time the user installs the plugin.

Please go to [Catalog sync step](#product-catalog-synchronization) for further details.

#Category synchronization.

![Plugin category sync](https://www.github.com/drophero/platform-integration/raw/master/img/plugin_category_syncronization.png "Plugin category sync")

We need 3 things:
- The category name.
- The internal "id" it has.
- His relation to their neighbors (parent_id).

**If we not receive the plugin categories properly the user will not be able to use DropHero, because he need to associate every product to each local category from our control panel.**

**You must re-sync categories evey time there's a new one or something changes (name, id or indention).**
Go to <code>[/v1/subscribed](https://github.com/drophero/api-documentation/blob/master/v1/sections/subscribed.md)</code> to get more dails about this process.

#Product catalog synchronization.

![Plugin catalog sync](https://www.github.com/drophero/platform-integration/raw/master/img/plugin_product_catalog_sync.png "Plugin catalog sync")

Go to <code>[/v1/subscribed](https://github.com/drophero/api-documentation/blob/master/v1/sections/subscribed.md)</code> to get more dails about this process.

#Orders synchronization & status management.

![Plugin order sync](https://www.github.com/drophero/platform-integration/raw/master/img/plugin_order_catalog_sync.png "Plugin order sync")

Go to <code>[/v1/orders](https://github.com/drophero/api-documentation/blob/master/v1/sections/orders.md)</code> to get more dails about this process.

#Please provide an damn good control panel!

It's important to provide a good user experience to your users, so it's important to make this right.

Please try to provide a good control panel. Place it where the other plugins does, so every user can find it easily. Try to grup all functions by use, so you can put one block to the basic check controls (like the check form to allow auto-submit for orders), and order block to allow the user to put his access keys.
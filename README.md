Integrating eCommerce platforms into DropHero
====================

#Table of contents
- [Introduction](#introduction)
- [API communication protocol & security](#api-communication-protocol--security)
- [Plugin Installation procedure](#plugin-installation-procedure)
- [Category synchronization](#category-synchronization)
- [Product catalog synchronization](#product-catalog-synchronization)
- [Orders synchronization & status management](#orders-synchronization--status-management)
- [The DropHero sales funnel](#the-dropHero-sales-funnel)
- [Keep it version wide](#keep-it-version-wide)
- [What are plugin callbacks?](#what-are-plugin-callbacks)
- [Please provide an damn good control panel!](#please-provide-an-damn-good-control-panel)

#Introduction

The purpose of this document is to give you an exhaustive documentation about DropHero and his communication protocols to allow you to integrate any eCommerce with DropHero. It covers all aspects including catalog synchronization and order synchronization through our API service.

#API communication protocol & security.

[Access API v1 Documentation](https://github.com/drophero/api-documentation/blob/master/v1/README.md).

If you want to integrate any component with DropHero you must use our API. You can find all details [here](https://github.com/drophero/api-documentation).

#Plugin Installation procedure.

In order to standarize the installation protocol across all eCommerce platforms, we're using a simple step by step process you need to follow to ensure we keep all your clients synced and in place.

![Plugin Installation procedure](https://www.github.com/drophero/platform-integration/raw/master/img/plugin_installation_procedure.png "Plugin Installation procedure")

#### Step 3 - Client provides their own keys

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

See [Catalog sync step](#product-catalog-synchronization) for further details.

#### Step 8 - DropHero Managed Products

See [DropHero Managed products](#drophero-managed-products) for further details.

#Category synchronization.

![Plugin category sync](https://www.github.com/drophero/platform-integration/raw/master/img/plugin_category_syncronization.png "Plugin category sync")


**You must re-sync categories evey time there's a new one or something changes (name, id or indention).**
Go to <code>[/v1/category](https://github.com/drophero/api-documentation/blob/master/v1/sections/categories.md)</code> to get more dails about this process.

We need 3 things:
- The category name.
- The internal "id" it has.
- His relation to their neighbors (parent_id).

**If we not receive the plugin categories properly the user will not be able to use DropHero, because he need to associate every product to each local category from our control panel.**

#Product catalog synchronization.

![Plugin catalog sync](https://www.github.com/drophero/platform-integration/raw/master/img/plugin_product_catalog_sync.png "Plugin catalog sync")

See [what are plugin callbacks](#what-are-plugin-callbacks).

**The plugin only makes proactive calls to refresh catalog during installation time**, the rest of the time waits until their catalog callback it's called. This first sync must be performed using <code>[GET /v1/subscribed](https://github.com/drophero/api-documentation/blob/master/v1/sections/subscribed.md#get-subscriptions)</code> call instead of the "from" function. The rest of the time we must use <code>[GET /v1/subscribed/from](https://github.com/drophero/api-documentation/blob/master/v1/sections/subscribed.md#get-subscriptions-from-timestamp)</code>.

####Store all data provided by DropHero
When you get the product info from DropHero, you're getting some extra data we'll need to use later (like the shipping price and shipping mode supported at product level). Please see [what comes from the API](https://github.com/drophero/api-documentation/blob/master/v1/sections/subscribed.md#get-subscriptions) and take care of store them acordingly.

**The plugin must store the last time called**, this date time (seconds from epoch) will be use every time the plugin calls to the API using <code>[GET /v1/subscribed/from](https://github.com/drophero/api-documentation/blob/master/v1/sections/subscribed.md#get-subscriptions-from-timestamp)</code> wich is nearly all the time except during installation (first sync).

Go to <code>[/v1/subscribed](https://github.com/drophero/api-documentation/blob/master/v1/sections/subscribed.md)</code> to get more dails about this process.


#### DropHero Managed Products

Let's supose this, you have an eCommerce with your own products but you want to use DropHero too. ¿What happens? So what we need here is some way to keep marked and **take the control** of these products. 

We will use **EAN codes to find them**, every time we're about to sync our catalog need to check this:

- What ean's are coming from DropHero API?
- Any of these EAN's coming are already in my eCommerce?

**If some EAN is already inside or eCommerce, we will "mark it" as DropHero managed**. This can be done by attaching some extra parameter into the database, or keeping a very careful control inside some sort of file. The thing is we need to know if this product will be processed by DropHero or manually by the eCommerce itself.


#Orders synchronization & status management.

####A new order is placed

![Plugin new order](https://www.github.com/drophero/platform-integration/raw/master/img/plugin_new_order_placed.png "Plugin new order")


####An order status has changed

![Plugin order sync](https://www.github.com/drophero/platform-integration/raw/master/img/plugin_order_catalog_sync.png "Plugin order sync")

Go to <code>[/v1/orders](https://github.com/drophero/api-documentation/blob/master/v1/sections/orders.md)</code> to get more dails about this process.



#The DropHero purchasing funnel

By the moment we're overwriting products and taking control over them, we need to take care of their selling process. This is slightly different from the standard eCommerce behavior.

![Plugin purchasing process](https://www.github.com/drophero/platform-integration/raw/master/img/plugin-purchasing-process.png "Plugin purchasing process")

**When a DropHero product it's been purchased, we need to add his shipping methods in the shipping step of the purchasing process**.

####How we will present this information?

Keep it simple. The best way to do it it's to aggregate all product shippings by shipping mode, and then, in a single figure present it to the user.

<code>

Example:

The user is buying 4 DropHero items and each of them have 2 DropHero shipping modes. So we will get that shipping modes each product have, and we'll agregate them by type. So to the end user we're presenting only 2 shipping options. 

</code>

#What are plugin callbacks?

This is the way we use to **knok your door** when we need to tell you something. Is a public url hidden in the plugin server that allows us to visit you.

####When you create them.

You need to create these "callbacks" in installation time to allow DropHero to know your door. These are 2 url's only known by us that you send to DropHero by using <code>[notify](https://github.com/drophero/api-documentation/blob/master/v1/sections/notify.md)</code> method **during installation time**.

####How they work.

We only perform a simple http request to these callbacks, then you must "catch" our call and act depending on witch we called:

- If we call to your catalog callback, we expect a 200 response code and you must perform a <code>[GET /v1/subscribed/from](https://github.com/drophero/api-documentation/blob/master/v1/sections/subscribed.md#get-subscriptions-from-timestamp)</code> call to DropHero because there's new products to update.
- If we call to your orders callback, we expect a 200 response code and you must perform a <code>[GET /v1/orders](https://github.com/drophero/api-documentation/blob/master/v1/sections/orders.md)</code> to check all your order statuses because somethig has changed.

**If we make a call to any of these two callbacks and we not get a 200 response code, we will try again in some minutes. If after a few attempts we can't reach you, we will mail this account reporting problems.**



#Keep it version wide

We know that's a very difficult task to acomplish, but make a little plan before start programming and keep in mind in this case **more is better**. Make a plan and target the most wide user-base of your platform, they will appreciate it.



#Please provide a good control panel!

It's important to provide a good user experience to your users, so it's important to make this right.

Please try to provide a good control panel. Place it where the other plugins does, so every user can find it easily. Try to grup all functions by use, so you can put one block to the basic check controls (like the check form to allow auto-submit for orders), and order block to allow the user to put his access keys.
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

This is the way we use to **knok your door** when we need to tell you something. 

You need to create these "callbacks" in installation time to allow DropHero to know your door. These are 2 url's only known by us and you send to DropHero by using <code>[notify](https://github.com/drophero/api-documentation/blob/master/v1/sections/notify.md)</code> method during installation time.

#Plugin Installation procedure.

In order to standarize the installation protocol across all eCommerce platforms, we're using a simple step by step process you need to follow to ensure we keep all your clients synced and in place.

![Plugin Installation procedure](https://www.github.com/drophero/platform-integration/raw/master/img/plugin_installation_procedure.png "Plugin Installation procedure")

#### Step 3 - Client privides his own keys

As we mention in the next chapter, you must provide a good control panel to allow your users to configure the plugin behavior. This includes two files, one for the Public Key and another for the Secret key. As this is sensible data, **you must ensure this keys are stored out of public access and correctly encrypted**.

#### Step 4 - Plugin first contact

This is when the plugin says "hello" to DropHero and reveals itself to us. In order to achieve this you must perform a call using <code>[notify](https://github.com/drophero/api-documentation/blob/master/v1/sections/notify.md)</code> function of our API. You can [access to the proper documentation by clicking here](https://github.com/drophero/api-documentation/blob/master/v1/sections/notify.md).

You must provide at least 2 things:
- System message <code>plugin installed</code>
- System message [2 url callbacks](#what-are-plugin-callbacks-and-what-they-do)

Example:

```JSON
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

### Step 5 - Sending eCommerce categories

### Step 6 - First Catalog syncronization

#You must provide an accessible control panel!

#Category synchronization.

#Product catalog synchronization.

#Orders synchronization & status management.
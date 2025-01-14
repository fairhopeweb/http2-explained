# 3. Things done to overcome latency pains

As always when faced with problems, people gather to find workarounds. Some of the workarounds are clever and useful, some of them are just awful kludges.

## 3.1 Spriting
<img style="float: right;" src="../images/spriting.jpg" />

Spriting is the term often used to describe when you put a lot of small images together into a single large image. Then you use javascript or CSS to “cut out” pieces of that big image to show smaller individual ones.

A site would use this trick for speed. Getting a single big image is much faster in HTTP 1.1 than getting a 100 smaller individual ones.

Of course this has its downsides for the pages of the site that only want to show one or two of the small pictures and similar. It also makes all pictures get evicted from the cache at the same time instead of possibly letting the most commonly used ones remain.

## 3.2 Inlining

Inlining is another trick to avoid sending individual images, and this is done by using data: URLs embedded in the CSS file. This has similar benefits and drawbacks as the spriting case.

    .icon1 {
        background: url(data:image/png;base64,<data>) no-repeat;
    }

    .icon2 {
        background: url(data:image/png;base64,<data>) no-repeat;
    }


## 3.3 Concatenation

A big site can end up with a lot of different javascript files. Front-end tools will help developers merge everyone of them into a single huge lump so that the browser will get a single big one instead of dozens of smaller files. Too much data is sent when only little  is needed. Too much data needs to be reloaded when a change is needed.

This practice is of course mostly an inconvenience to the developers involved.

## 3.4 Sharding

The final performance trick I'll mention is often referred to as “sharding”. It basically means serving aspects of your service on as many different hosts as possible. At first glance this seems strange but there is sound reasoning behind it.

Initially the HTTP 1.1 specification stated that a client was allowed to use maximum of two TCP connections for each host. So, in order to not violate the spec clever sites simply invented new host names and – voilá - you could get more connections to your site and decreased page load times.

Over time, that limitation was removed and today clients easily use 6-8 connections per host name but they still have a limit so sites continue to use this technique to bump the number of connections. As the number of objects are ever increasing – as I showed before – the large number of connections are then used just to make sure HTTP performs well and makes your site fast. It is not unusual for sites to use well over 50 or even up to and beyond 100 connections now for a single site using this technique. Recent stats from httparchive.org show that the top 300K URLs in the world need on average 40(!) TCP connections to display the site, and the trend says this is still increasing slowly over time.

Another reason is also to put images or similar resources on a separate host name that doesn't use any cookies, as the size of cookies these days can be quite significant. By using cookie-free image hosts you can sometimes increase performance simply by allowing much smaller HTTP requests!

The picture below shows how a packet trace looks like when browsing one of Sweden's top web sites and how requests are distributed over several host names.

![image sharing at expressen.se](../images/expressen-sharding.jpg)

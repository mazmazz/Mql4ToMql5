# MQL4 to MQL5

This guide will teach you how to port your MQL4 code so that it works in both MT4 and MT5. The goal is to allow you to use the same codebase for both versions and cross-compile between the two.

## Page Navigation

* [General differences between MQL4 and MQL5](https://github.com/mazmazz/Mql4ToMql5/blob/master/General%20differences.md) 

# Why port your project to MQL5?

## 1. It's not as hard as you think

Believe it or not, most basic logic and structure from an MQL4 project will compile as-is in MQL5. You just need to know the differences and how to apply them -- most of them are minor, and just a few are significant. And no, OOP is not required.

Once you know the rules, it's straightforward to write your code so that it cross-compiles identically with MQL4 and MQL5. You can write changes just once and they work the same in both versions. The version-specific code is kept minimal and easy to section off from the main code. This guide will cover what changes you'll need to make.

## 2. MT5 is practically the same as MT4

This wasn't always true. You may have heard that trading in MT5 is restrictively different from MT4 because it only allowed for one trade direction per instrument and it forced FIFO. 

**As of Spring 2016, this adage has been false.** MT5 has been retooled with *"Hedging Mode"*, which makes trading work identically to MT4: it allows for multiple trades per symbol and FIFO is not enforced. The interface to execute and review trades is practically the same, as is the charting. 

While the broker does need to enable this mode, it is pretty easy to find brokers who use it. Hedging should be the de facto mode, going forward.

## 3. MT5 is better than MT4

Besides being visually similar, MT5 is more stable than MT4 and has both under-the-hood improvements and new features. 

In terms of user-facing features, a lot of thought was put in how to re-organize certain features. For instance:

* MT5's [Depth of Market widget](https://www.metatrader5.com/en/terminal/help/depth_of_market) is an actual depiction of Depth of Market, compared to MT4's [lot-sizing order tool](https://www.metatrader4.com/en/trading-platform/help/overview/depth_of_market) that's erroneously called "Depth of Market".
* In MT5's [Symbols window](https://www.metatrader5.com/en/releasenotes/terminal/1427), you have easy access to both Bar and Tick history that is searchable by datetime, whereas MT4's equivalent [History Center](https://www.metatrader4.com/en/trading-platform/help/service/history_center) affords no such access to specific times.

But most importantly, _**MT5 has an overhauled backtester that is actually competent!**_ A few examples:

* Since Spring 2016, MT5 uses **real ticks and real spreads** downloaded from the broker's server to run the backtest, whereas MT4 simulates ticks artificially and does not model spreads (you had to resort to hacks for these features.) 
* MT5 allows you to backtest **multi-symbol baskets** such that symbol interplay is accurately modeled and the results show the basket total. MT4 can only process one symbol at a time, such that baskets have to be tested separately and without symbol interplay.
* Optimizer tests in MT5 can be run in parallel per processor thread, and even on other PCs via LAN. More impressively, MetaQuotes runs a cloud service allowing access to thousands of test servers, such that days-long optimizing can be had for under a half hour.

By comparison, MT4's backtester is primitive and without competent accuracy, so its negative reputation is well deserved. It's understandable why forward testing is rule of law in MT4 communities, but in the quant world, backtesting and statistics are king to deploying evidence-based strategies rapidly. 

MT5's backtester goes a long way towards accuracy and rapid iteration. Frankly, it's a joy to get comparably accurate year-long results in under an hour compared to forward testing the same results for weeks and months on end.

[![MT5 backtester](https://thumbs.gfycat.com/EmbarrassedVigorousEarthworm-size_restricted.gif)](https://gfycat.com/EmbarrassedVigorousEarthworm)

## In sum...

It's a shame so far that MT5 has low adoption because its improvements really are welcome. MT5 is kind of a hidden gem, but it really shouldn't be hidden. If many people can write EAs and indicators that work with both MetaTrader versions, hopefully more brokers will offer MT5.

# What you need to know for your code

## 1. Cross-compiling between MQL4 and MQL5 is straightforward

MQL4 and MQL5 are shared enough that you can use the exact same codebase to compile between the two versions. 

For instance, many MQL4 function calls have an MQL5 equivalent that's valid in both versions, so it makes much sense to make calls in the MQL5 method so that they work universally. 

For cases where the MQL5 logic must be different, you can use the preprocessor directives `#ifdef __MQL4__` and `#ifdef __MQL5__` to section off the logic. Only in specific cases is version-specific code necessary, and most other code can be written as valid for both versions.

## 2. OOP is not required

It's a myth that MQL5 programs must be structured in an object-oriented format. While OOP is good practice in general, MQL5 works just fine by solely using the event calls `OnStart`, `OnTimer`, `OnTick`, `OnCalculate`, etc. They even work the same as the old MQL4 equivalents like `start`, `init`, etc. -- just a few different namings and arguments. And you can use top-level functions and global variables as much as you like -- just like the typical MQL4 program.

## Websites

For more details on a certain topic, you can view these links:

* [MQL4 to MQL5 Equivalency Tables](https://www.mql5.com/en/articles/81) - Contains a table of MQL4 and MQL5 method and constant equivalents -- thorough but missing certain areas like Orders
* [MQL5 Docs - Moving from MQL4 to MQL5](https://www.mql5.com/en/docs/migration) - A brief discussion on porting considerations

View the menu on top of the page to learn about MQL4 and MQL5 differences.

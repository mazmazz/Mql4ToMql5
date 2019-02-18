# General differences between MQL4 and MQL5

In terms of code grammar, there are very few differences. They amount to making sure `#property strict` is specified; using `input` variables for user settings; and making sure you initialize your variables with a default value.

### Make sure `#property strict` is specified in each source file

Inserting `#property strict` on the top of each file ensures MQL4 will run in "Strict" mode, which enforces standards more closely to MQL5 rather than pre-2014 MQL4. Without this line, unexpected behavior will occur in MT4.

For programs written in the pre-2014 MQL4 dialect, inserting this line will often spout lots of errors because of the different coding practices. This should be mitigated by following good practices found in this guide and generally.

This line is usually inserted if you create a file under MetaEditor 4, but it is not inserted if you use MetaEditor 5, so make sure this line is present in each source file.

### For settings, use `input` variables, not `extern` variables

Though valid in MQL4, `extern bool var;` became invalid for user settings in MQL5 starting 2017. MQL5 requires that you use `input bool var;` or else the settings window does not display.

[According to the MQL5 docs](https://www.mql5.com/en/docs/basis/variables/externvariables), the `extern` variable is intended as a shared variable across source files tied together with `#include`. It happens to be MT4 practice to use `extern` variables as user settings, but MT5 discontinued this practice. That said, `extern` variables still work in MQL5 under their intended capacity.

`input` variables can only be set once by the user and cannot be changed during runtime. **If you use `extern` variables as user settings, you should verify whether you change those `extern` variables to new values during runtime:**

* If you do not, it is safe to search-replace the `extern` variables to `input` variables. 
* If you do change `extern` variables, you can have separate `input` variables, then copy the values to your current `extern` variables during `OnInit`.

### Variables must be initialized with a default value

In MQL4, if you declare a variable, its value is implicitly initialized to 0 or equivalent. Therefore, you can write `int startVal;` without specifying the value. This fails in MQL5 because MT5 does not initialize variables for you -- you must initialize all variables with a default value: `int startVal = 0;`. 

By initializing the variable, subsequent logic will work as expected.

```mql5
#property strict

void OnStart()
{
   // Wrong
  int startVal;

   Print("startVal: " + startVal);
  // MT4 output - "startVal: 0"
  // MT5 output - "startVal: -382372343" (random value from memory)

   // Right
  int startVal5 = 0;

   Print("startVal5: " + startVal5);
   // MT4 output - "startVal5: 0"
   // MT5 output - "startVal5: 0" (correct value)
}
```

This is based on C++ rules where variables needed to be initialized or else a latent value from memory is used. 

Though C++ rules dictate that structs and arrays must also be initialized by default values, MQL5 does not require this. For peace of mind, you may choose to initialize structs and arrays should this rule change:

* Structs - `MqlTradeRequest request = {0};`
* Arrays - `int arr[5]; ArrayInitialize(arr, 0);`

### Array series direction must be declared explicitly

Array series direction (newest to oldest order) needs to be initialized for the `OnCalculate` method with custom indicators by calling `ArraySetAsSeries`.

In MQL4, you can access `open[]`, `close[]`, etc., from newest to oldest order because it is implicit that `ArraySetAsSeries` is true for the bar data. MQL5 does not assume this, so you must call `ArraySetAsSeries` explicitly to access data from newest to oldest order.

```mql5
#property strict

int OnCalculate(const int rates_total,
              const int prev_calculated,
              const datetime &time[],
              const double &open[],
              const double &high[],
              const double &low[],
              const double &close[],
              const long &tick_volume[],
              const long &volume[],
              const int &spread[])
{
   // Wrong - not calling ArraySetAsSeries

   Print("Times: " + time[0] + ", " + time[1] + ", " + time[2]);
   // MQL4 output - "Times: 2017.08.04 00:00, 2017.08.03 00:00, 2017.08.02 00:00"
   // MQL5 output - "Times: 2001.06.19 00:00, 2001.06.20 00:00, 2001.06.21 00:00" (oldest to newest order)

   // Right - call ArraySetAsSeries explicitly
   ArraySetAsSeries(time, true);

   Print("Times: " + time[0] + ", " + time[1] + ", " + time[2]);
   // MQL4 output - "Times: 2017.08.04 00:00, 2017.08.03 00:00, 2017.08.02 00:00"
   // MQL5 output - "Times: 2017.08.04 00:00, 2017.08.03 00:00, 2017.08.02 00:00" (newest to oldest order)

   return rates_total;
}
```

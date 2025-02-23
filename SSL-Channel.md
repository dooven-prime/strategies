
> Name

SSL-Channel

> Author

ChaoZhang

> Strategy Description

:
Multiple Moving Averages to choose from ( SMA , EMA , SMMA (RMA), WMA , VWMA )
Option for a second SSL Channel
MTF

**backtest**

 ![IMG](https://www.fmz.com/upload/asset/1377dfc3d2a8a60c192.png) 

> Strategy Arguments



|Argument|Default|Description|
|----|----|----|
|v_input_1|false|Take Wicks into Account ?|
|v_input_2|true|Highlight State ?|
|v_input_3|true|(?Channel №1)MA High|
|v_input_string_1|0|ma1_type: SMA|EMA|SMMA (RMA)|WMA|VWMA|
|v_input_4_high|0|ma1_source: high|close|low|open|hl2|hlc3|hlcc4|ohlc4|
|v_input_int_1|200|ma1_length|
|v_input_5|green|ma1_color|
|v_input_6|true|MA Low|
|v_input_string_2|0|ma2_type: SMA|EMA|SMMA (RMA)|WMA|VWMA|
|v_input_7_low|0|ma2_source: low|high|close|open|hl2|hlc3|hlcc4|ohlc4|
|v_input_int_2|200|ma2_length|
|v_input_8|red|ma2_color|
|v_input_9|true|Show Buy/Sell Labels ?|
|v_input_10|false|(?Channel №2)MA High|
|v_input_string_3|0|ma3_type: SMA|EMA|SMMA (RMA)|WMA|VWMA|
|v_input_11_high|0|ma3_source: high|close|low|open|hl2|hlc3|hlcc4|ohlc4|
|v_input_int_3|20|ma3_length|
|v_input_12|orange|ma3_color|
|v_input_13|false|MA Low|
|v_input_string_4|0|ma4_type: SMA|EMA|SMMA (RMA)|WMA|VWMA|
|v_input_14_low|0|ma4_source: low|high|close|open|hl2|hlc3|hlcc4|ohlc4|
|v_input_int_4|20|ma4_length|
|v_input_15|blue|ma4_color|
|v_input_16|true|Show Buy/Sell Labels ?|


> Source (PineScript)

``` javascript
/*backtest
start: 2022-04-25 00:00:00
end: 2022-05-24 23:59:00
period: 45m
basePeriod: 5m
exchanges: [{"eid":"Futures_Binance","currency":"BTC_USDT"}]
*/

//@version=5
indicator("SSL Channel", shorttitle="SSL Channel", overlay=true, timeframe="", timeframe_gaps=false)

wicks = input(false, "Take Wicks into Account ?")
highlightState = input(true, "Highlight State ?")
ma(source, length, type) =>
    type == "SMA" ? ta.sma(source, length) :
     type == "EMA" ? ta.ema(source, length) :
     type == "SMMA (RMA)" ? ta.rma(source, length) :
     type == "WMA" ? ta.wma(source, length) :
     type == "VWMA" ? ta.vwma(source, length) :
     na

show_ma1   = input(true   , "MA High", inline="MA #1", group="Channel №1")
ma1_type   = input.string("SMA"  , ""     , inline="MA #1", options=["SMA", "EMA", "SMMA (RMA)", "WMA", "VWMA"], group="Channel №1")
ma1_source = input(high  , ""     , inline="MA #1", group="Channel №1")
ma1_length = input.int(200     , ""     , inline="MA #1", minval=1, group="Channel №1")
ma1_color  = input(color.green, ""     , inline="MA #1", group="Channel №1")
ma1 = ma(ma1_source, ma1_length, ma1_type)

show_ma2   = input(true   , "MA Low", inline="MA #2", group="Channel №1")
ma2_type   = input.string("SMA"  , ""     , inline="MA #2", options=["SMA", "EMA", "SMMA (RMA)", "WMA", "VWMA"], group="Channel №1")
ma2_source = input(low  , ""     , inline="MA #2", group="Channel №1")
ma2_length = input.int(200     , ""     , inline="MA #2", minval=1, group="Channel №1")
ma2_color  = input(color.red, ""     , inline="MA #2", group="Channel №1")
ma2 = ma(ma2_source, ma2_length, ma2_type)
showLabels1 = input(true, "Show Buy/Sell Labels ?", group="Channel №1")

show_ma3   = input(false   , "MA High", inline="MA #3", group="Channel №2")
ma3_type   = input.string("SMA"  , ""     , inline="MA #3", options=["SMA", "EMA", "SMMA (RMA)", "WMA", "VWMA"], group="Channel №2")
ma3_source = input(high  , ""     , inline="MA #3", group="Channel №2")
ma3_length = input.int(20    , ""     , inline="MA #3", minval=1, group="Channel №2")
ma3_color  = input(color.orange, ""     , inline="MA #3", group="Channel №2")
ma3 = ma(ma3_source, ma3_length, ma3_type)

show_ma4   = input(false   , "MA Low", inline="MA #4", group="Channel №2")
ma4_type   = input.string("SMA"  , ""     , inline="MA #4", options=["SMA", "EMA", "SMMA (RMA)", "WMA", "VWMA"], group="Channel №2")
ma4_source = input(low  , ""     , inline="MA #4", group="Channel №2")
ma4_length = input.int(20    , ""     , inline="MA #4", minval=1, group="Channel №2")
ma4_color  = input(color.blue, ""     , inline="MA #4", group="Channel №2")
ma4 = ma(ma4_source, ma4_length, ma4_type)
showLabels2 = input(true, "Show Buy/Sell Labels ?", group="Channel №2")

Hlv1 = float(na)
Hlv1 := (wicks ? high : close) > ma1 ? 1 : (wicks ? low : close) < ma2 ? -1 : Hlv1[1]
sslUp1   = Hlv1 < 0 ? ma2 : ma1
sslDown1 = Hlv1 < 0 ? ma1 : ma2

Color1 = Hlv1 == 1 ? ma1_color : ma2_color
fillColor1 = highlightState ? (color.new(Color1, 90)) : na

highLine1 = plot(show_ma1 ? sslUp1 : na, title="UP", linewidth=2, color = Color1)
lowLine1 = plot(show_ma2 ? sslDown1 : na, title="DOWN", linewidth=2, color = Color1)

plotshape(show_ma1 and showLabels1 and Hlv1 == 1 and Hlv1[1] == -1, title="Buy Label", text="Buy", location=location.belowbar, style=shape.labelup, size=size.tiny, color=Color1, textcolor=color.white)
plotshape(show_ma2 and showLabels1 and Hlv1 == -1 and Hlv1[1] == 1, title="Sell Label", text="Sell", location=location.abovebar, style=shape.labeldown, size=size.tiny, color=Color1, textcolor=color.white)

fill(highLine1, lowLine1, color = fillColor1)

Hlv2 = float(na)
Hlv2 := (wicks ? high : close) > ma3 ? 1 : (wicks ? low : close) < ma4 ? -1 : Hlv2[1]
sslUp2   = Hlv2 < 0 ? ma4 : ma3
sslDown2 = Hlv2 < 0 ? ma3 : ma4

Color2 = Hlv2 == 1 ? ma3_color : ma4_color
fillColor2 = highlightState ? (color.new(Color2, 90)) : na

highLine2 = plot(show_ma3 ? sslUp2 : na, title="UP", linewidth=2, color = Color2)
lowLine2 = plot(show_ma4 ? sslDown2 : na, title="DOWN", linewidth=2, color = Color2)

plotshape(show_ma3 and showLabels2 and Hlv2 == 1 and Hlv2[1] == -1, title="Buy Label", text="Buy", location=location.belowbar, style=shape.labelup, size=size.tiny, color=Color2, textcolor=color.white)
plotshape(show_ma4 and showLabels2 and Hlv2 == -1 and Hlv2[1] == 1, title="Sell Label", text="Sell", location=location.abovebar, style=shape.labeldown, size=size.tiny, color=Color2, textcolor=color.white)

fill(highLine2, lowLine2, color = fillColor2)

// Alerts
alertcondition(Hlv1 == 1 and Hlv1[1] == -1, title="SSL Channel (1) Buy Alert", message = "SSL Channel (1): BUY")
alertcondition(Hlv1 == -1 and Hlv1[1] == 1, title="SSL Channel (1) Sell Alert", message = "SSL Channel (1): SELL")
alertcondition(Hlv2 == 1 and Hlv2[1] == -1, title="SSL Channel (2) Buy Alert", message = "SSL Channel (2): BUY")
alertcondition(Hlv2 == -1 and Hlv2[1] == 1, title="SSL Channel (2) Sell Alert", message = "SSL Channel (2): SELL")

if Hlv2 == 1 and Hlv2[1] == -1
    strategy.entry("Enter Long", strategy.long)
else if Hlv2 == -1 and Hlv2[1] == 1
    strategy.entry("Enter Short", strategy.short)
```

> Detail

https://www.fmz.com/strategy/365858

> Last Modified

2022-05-26 12:19:48

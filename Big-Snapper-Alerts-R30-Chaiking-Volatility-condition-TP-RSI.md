
> Name

Big-Snapper-Alerts-R30-Chaiking-Volatility-condition-TP-RSI

> Author

ChaoZhang

> Strategy Description

Big Snapper Alerts R3.0 + Chaiking Volatility condition + TP RSI

Educational Script - Use this tool at your own responsability.


**backtest**

 ![IMG](https://www.fmz.com/upload/asset/1625ca6598ac6191ade.jpg) 

> Strategy Arguments



|Argument|Default|Description|
|----|----|----|
|v_input_string_1|0|Coloured MA Type: : HullMA|EMA|WMA|VWMA|SMMA|DEMA|TEMA|SMA|ZEMA|TMA|SSMA|
|v_input_int_1|18|Coloured MA - Length|
|v_input_1_close|0|Coloured MA - Source: close|high|low|open|hl2|hlc3|hlcc4|ohlc4|
|v_input_string_2|0|Fast MA Type: : EMA|SMA|WMA|VWMA|SMMA|DEMA|TEMA|HullMA|ZEMA|TMA|SSMA|
|v_input_int_2|21|Fast MA - Length|
|v_input_string_3|0|Medium MA Type: : EMA|SMA|WMA|VWMA|SMMA|DEMA|TEMA|HullMA|ZEMA|TMA|SSMA|
|v_input_int_3|55|Medium MA - Length|
|v_input_string_4|0|Slow MA Type: : EMA|SMA|WMA|VWMA|SMMA|DEMA|TEMA|HullMA|ZEMA|TMA|SSMA|
|v_input_int_4|89|Slow MA Length|
|v_input_2_close|0|3xMA and Bollinger Source: close|high|low|open|hl2|hlc3|hlcc4|ohlc4|
|v_input_string_5|0|Signal Filter Option : : SuperTrend|3xMATrend|SuperTrend+3xMA|ColouredMA|No Alerts|MACross|MACross+ST|MACross+3xMA|OutsideIn:MACross|OutsideIn:MACross+ST|OutsideIn:MACross+3xMA|
|v_input_3|false|hideMALines|
|v_input_4|true|hideSuperTrend|
|v_input_5|true|hideBollingerBands|
|v_input_6|true|hideTrendDirection|
|v_input_7|false|disableFastMAFilter|
|v_input_8|false|disableMediumMAFilter|
|v_input_9|false|disableSlowMAFilter|
|v_input_int_5|20|Bollinge Bands Length|
|v_input_float_1|2|Bollinger Bands StdDevs|
|v_input_10|8|Bollinger Outside In LookBack|
|v_input_float_2|3.618|SuperTrend Factor|
|v_input_int_6|5|SuperTrend Length|
|v_input_string_6|0|BUY Marker Colour: : Green|Lime|Aqua|DodgerBlue|Gray|Yellow|
|v_input_string_7|0|SELL Marker Colour: : Maroon|Red|Fuchsia|Blue|Black|Orange|
|v_input_11|70|overbought value|
|v_input_12|30|oversold value|
|v_input_int_7|10|Length|
|v_input_int_8|12|ROCLength|
|v_input_int_9|false|Trigger|


> Source (PineScript)

``` javascript
/*backtest
start: 2022-04-08 00:00:00
end: 2022-05-07 23:59:00
period: 2h
basePeriod: 15m
exchanges: [{"eid":"Futures_Binance","currency":"BTC_USDT","balance":500000}]
*/

//@version=5
//
// Bannos
// #NotTradingAdvice #DYOR
//  Disclaimer.
// I AM NOT A FINANCIAL ADVISOR.
// THESE IDEAS ARE NOT ADVICE AND ARE FOR EDUCATION PURPOSES ONLY.
// ALWAYS DO YOUR OWN RESEARC


strategy(title='Big Snapper Alerts R3.0 + Chaiking Volatility condition + TP RSI', shorttitle='SNAPPER Bannos', overlay=true)
//
// Author:  Adaptation from Bannos. Source: JustUncleL
// Date:    May-2022
// Version: R1.0
//
// Description:
//    This is a diversified Binary Option or Scalping Alert indicator originally designed for 
//    lower Time Frame Trend or Swing trading. Although you will find it a useful tool for 
//    higher time frames as well.
//
//    The Alerts are generated by the changing direction of the ColouredMA (HullMA by default, 
//    also SSMA works well) or optionally by fastMA crossing ColouredMA. Then you have the choice of 
//    selecting the Directional filtering on these signals or a Bollinger Outside IN swing
//    reversal filter. 
//
//    The filters include:
//    
//      Type 1 - The three MAs (EMAs 21,55,89 by default) in various combinations or by themselves.
//               When only one directional MA selected then direction filter is given by ColouredMA 
//               above(up)/below(down) selected MA.
//               If more than one MA selected the direction is given by MAs being in correct order
//               for trend direction.
//
//      Type 2 - The SuperTrend  direction is used to filter ColouredMA signals.
//
//      Type 3 - Bollinger Band Outside In is used to filter ColouredMA for swing reversals.
//
//      Type 4 - No directional filtering, all signals from the ColouredMA are shown.
//
//      Type 5 - Signals given by FastMA (eg length 7) crossing the ColouredMA (eg length 14), 
//               suggested FastMA should same type as ColouredMA (eq HullMA or SSMA) and 
//               no less than half the length.
//
//    Notes: - Each Type can be combined with most other types to form more complex filtration.
//           - Alerts can also be disabled completely if you just want one indicator with 
//             one colouredMA and/or 3xMAs and/or Bollinger Bands and/or SuperTrend 
//             painted on the chart.
//
//    Warning- Be aware that combining Bollinger OutsideIn swing filter and a directional filter 
//             can be counter productive as they are opposites. So careful consideration is needed 
//             when combining Bollinger OutsideIn with any of the directional filters. 
//
//    Hints: - For Binary Options try ColouredMA = HullMA(13) or HullMA(8) with Type 2 or 3 Filter.
//           - When using Trend filters SuperTrend and/or 3xMA Trend, you will find if price reverses
//             and breaks back through the Big Fat Signal line, then this can be a good reversal trade.
//           - Try using SSMA instead of HullMA for signal line, it is similar to Hull but remains
//             smoother at low lengths and works well with the MA Cross signals.
//
// Mofidifications:
//
//    R0.# : Original alpha unpublished versions.
//
//    R1.# : Original beta unpublished versions.
//
//    R2.0 : Original Published version.
//
//    R3.0 : - Added new Signal Type 5: Signals come from Fast MA crossing slower ColouredMA.
//           - Replaced OutsideIn Coloured MA signals with those generated from the MA cross.
//           - Added selectable colours for Big fat marker, the big arrows colours must still
//             be selected in the "Style" parameters of the script.
//
// References:
//    Some explanation about the what Hull Moving averageis  and ideas of how the generated in 
//    Snapper can be used.
//    - https://tradingsim.com/blog/hull-ma/
//    - http://forextradingstrategies4u.com/hull-moving-average-forex-trading-strategy/
//
//    Some Code borrowed from:
//    - "Scalp Jockey - MTF MA Cross Visual Strategizer by JayRogers"
//
//    Inspiration from @vdubus
//
//
// -----------------------------------------------------------------------------
// Copyright 2017 JustUncleL
//
// This program is free software: you can redistribute it and/or modify
// it under the terms of the GNU General Public License as published by
// the Free Software Foundation, either version 3 of the License, or
// any later version.
//
// This program is distributed in the hope that it will be useful,
// but WITHOUT ANY WARRANTY; without even the implied warranty of
// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
// GNU General Public License for more details.
// 
// The GNU General Public License can be found here
// <http://www.gnu.org/licenses/>.
//
// -----------------------------------------------------------------------------
//
//
// === INPUTS ===
// Coloured MA - type, length, source
typeColoured = input.string(defval='HullMA', title='Coloured MA Type: ', options=['SMA', 'EMA', 'WMA', 'VWMA', 'SMMA', 'DEMA', 'TEMA', 'HullMA', 'ZEMA', 'TMA', 'SSMA'])
lenColoured = input.int(defval=18, title='Coloured MA - Length', minval=1)
srcColoured = input(close, title='Coloured MA - Source')
// Fast MA - type, length
typeFast = input.string(defval='EMA', title='Fast MA Type: ', options=['SMA', 'EMA', 'WMA', 'VWMA', 'SMMA', 'DEMA', 'TEMA', 'HullMA', 'ZEMA', 'TMA', 'SSMA'])
lenFast = input.int(defval=21, title='Fast MA - Length', minval=1)
// Medium MA - type, length
typeMedium = input.string(defval='EMA', title='Medium MA Type: ', options=['SMA', 'EMA', 'WMA', 'VWMA', 'SMMA', 'DEMA', 'TEMA', 'HullMA', 'ZEMA', 'TMA', 'SSMA'])
lenMedium = input.int(defval=55, title='Medium MA - Length', minval=1)
// Slow MA - type, length
typeSlow = input.string(defval='EMA', title='Slow MA Type: ', options=['SMA', 'EMA', 'WMA', 'VWMA', 'SMMA', 'DEMA', 'TEMA', 'HullMA', 'ZEMA', 'TMA', 'SSMA'])
lenSlow = input.int(defval=89, title='Slow MA Length', minval=1)
// 3xMA source
ma_src = input(close, title='3xMA and Bollinger Source')
//
filterOption = input.string('SuperTrend', title='Signal Filter Option : ', options=['3xMATrend', 'SuperTrend', 'SuperTrend+3xMA', 'ColouredMA', 'No Alerts', 'MACross', 'MACross+ST', 'MACross+3xMA', 'OutsideIn:MACross', 'OutsideIn:MACross+ST', 'OutsideIn:MACross+3xMA'])
//
hideMALines = input(false)
hideSuperTrend = input(true)
hideBollingerBands = input(true)
hideTrendDirection = input(true)
//
disableFastMAFilter = input(false)
disableMediumMAFilter = input(false)
disableSlowMAFilter = input(false)
//
uKC = false  // input(false,title="Use Keltner Channel (KC) instead of Bollinger")
bbLength = input.int(20, minval=2, step=1, title='Bollinge Bands Length')
bbStddev = input.float(2.0, minval=0.5, step=0.1, title='Bollinger Bands StdDevs')
oiLength = input(8, title='Bollinger Outside In LookBack')
//
SFactor = input.float(3.618, minval=1.0, title='SuperTrend Factor')
SPd = input.int(5, minval=1, title='SuperTrend Length')
//
buyColour_ = input.string('Green', title='BUY Marker Colour: ', options=['Green', 'Lime', 'Aqua', 'DodgerBlue', 'Gray', 'Yellow'])
sellColour_ = input.string('Maroon', title='SELL Marker Colour: ', options=['Maroon', 'Red', 'Fuchsia', 'Blue', 'Black', 'Orange'])
// --- Allocate Correct Filtering Choice
// Can only be one choice
uSuperTrendFilter = filterOption == 'SuperTrend' ? true : false
u3xMATrendFilter = filterOption == '3xMATrend' ? true : false
uBothTrendFilters = filterOption == 'SuperTrend+3xMA' ? true : false
//uOIFilter           = filterOption == "OutsideIn:ClrMA" ? true : false
uOIMACrossFilter = filterOption == 'OutsideIn:MACross' ? true : false
uOI3xMAFilter = filterOption == 'OutsideIn:MACross+3xMA' ? true : false
uOISTFilter = filterOption == 'OutsideIn:MACross+ST' ? true : false
uMACrossFilter = filterOption == 'MACross' ? true : false
uMACrossSTFilter = filterOption == 'MACross+ST' ? true : false
uMACross3xMAFilter = filterOption == 'MACross+3xMA' ? true : false
// unless all 3 MAs disabled.
disable3xMAFilter = disableFastMAFilter and disableMediumMAFilter and disableSlowMAFilter
u3xMATrendFilter := disable3xMAFilter ? false : u3xMATrendFilter
// if no filters selected then must be "No Filters" option
disableAllFilters = u3xMATrendFilter or uSuperTrendFilter or uBothTrendFilters or uOI3xMAFilter or uOISTFilter or uOIMACrossFilter or uMACrossFilter or uMACrossSTFilter or uMACross3xMAFilter ? false : true
// if "No Alerts" option selected, then disable all selections
disableAllFilters := filterOption == 'No Alerts' ? false : disableAllFilters
uSuperTrendFilter := filterOption == 'No Alerts' ? false : uSuperTrendFilter
u3xMATrendFilter := filterOption == 'No Alerts' ? false : u3xMATrendFilter
uBothTrendFilters := filterOption == 'No Alerts' ? false : uBothTrendFilters
//uOIFilter           := filterOption == "No Alerts"? false : uOIFilter
uOIMACrossFilter := filterOption == 'No Alerts' ? false : uOIMACrossFilter
uOI3xMAFilter := filterOption == 'No Alerts' ? false : uOI3xMAFilter
uOISTFilter := filterOption == 'No Alerts' ? false : uOISTFilter
uMACrossFilter := filterOption == 'No Alerts' ? false : uMACrossFilter
uMACrossSTFilter := filterOption == 'No Alerts' ? false : uMACrossSTFilter
uMACross3xMAFilter := filterOption == 'No Alerts' ? false : uMACross3xMAFilter
// --- CONSTANTS ---
dodgerblue = #1E90FF
lightcoral = #F08080
buyColour = color.green  // for big Arrows, must be a constant.
sellColour = color.maroon  // for big Arrows
// Colour Selectable for Big Fat Bars.
buyclr = buyColour_ == 'Lime' ? color.lime : buyColour_ == 'Aqua' ? color.aqua : buyColour_ == 'DodgerBlue' ? dodgerblue : buyColour_ == 'Gray' ? color.gray : buyColour_ == 'Yellow' ? color.yellow : color.green
sellclr = sellColour_ == 'Red' ? color.red : sellColour_ == 'Fuchsia' ? color.fuchsia : sellColour_ == 'Blue' ? color.blue : sellColour_ == 'Black' ? color.black : sellColour_ == 'Orange' ? color.orange : color.maroon
// === /INPUTS ===
// === FUNCTIONS ===
// Returns MA input selection variant, default to SMA if blank or typo.
variant(type, src, len) =>
    v1 = ta.sma(src, len)  // Simple
    v2 = ta.ema(src, len)  // Exponential
    v3 = ta.wma(src, len)  // Weighted
    v4 = ta.vwma(src, len)  // Volume Weighted
    v5 = 0.0
    sma_1 = ta.sma(src, len)  // Smoothed
    v5 := na(v5[1]) ? sma_1 : (v5[1] * (len - 1) + src) / len
    v6 = 2 * v2 - ta.ema(v2, len)  // Double Exponential
    v7 = 3 * (v2 - ta.ema(v2, len)) + ta.ema(ta.ema(v2, len), len)  // Triple Exponential
    v8 = ta.wma(2 * ta.wma(src, len / 2) - ta.wma(src, len), math.round(math.sqrt(len)))  // Hull WMA = (2*WMA (n/2) − WMA (n)), sqrt (n))
    v11 = ta.sma(ta.sma(src, len), len)  // Triangular
    // SuperSmoother filter
    // © 2013  John F. Ehlers
    a1 = math.exp(-1.414 * 3.14159 / len)
    b1 = 2 * a1 * math.cos(1.414 * 3.14159 / len)
    c2 = b1
    c3 = -a1 * a1
    c1 = 1 - c2 - c3
    v9 = 0.0
    v9 := c1 * (src + nz(src[1])) / 2 + c2 * nz(v9[1]) + c3 * nz(v9[2])
    // Zero Lag Exponential
    e = ta.ema(v1, len)
    v10 = v1 + v1 - e
    // return variant, defaults to SMA if input invalid.
    type == 'EMA' ? v2 : type == 'WMA' ? v3 : type == 'VWMA' ? v4 : type == 'SMMA' ? v5 : type == 'DEMA' ? v6 : type == 'TEMA' ? v7 : type == 'HullMA' ? v8 : type == 'SSMA' ? v9 : type == 'ZEMA' ? v10 : type == 'TMA' ? v11 : v1
// === /FUNCTIONS ===
// === SERIES VARIABLES ===
// MA's
ma_fast = variant(typeFast, ma_src, lenFast)
ma_medium = variant(typeMedium, ma_src, lenMedium)
ma_slow = variant(typeSlow, ma_src, lenSlow)
ma_coloured = variant(typeColoured, srcColoured, lenColoured)
// Get Direction of Coloured Moving Average
clrdirection = 1
falling_1 = ta.falling(ma_coloured, 2)
clrdirection := ta.rising(ma_coloured, 2) ? 1 : falling_1 ? -1 : nz(clrdirection[1], 1)
// get 3xMA trend direction based on selections.
madirection = ma_fast > ma_medium and ma_medium > ma_slow ? 1 : ma_fast < ma_medium and ma_medium < ma_slow ? -1 : 0
madirection := disableSlowMAFilter ? ma_fast > ma_medium ? 1 : ma_fast < ma_medium ? -1 : 0 : madirection
madirection := disableMediumMAFilter ? ma_fast > ma_slow ? 1 : ma_fast < ma_slow ? -1 : 0 : madirection
madirection := disableFastMAFilter ? ma_medium > ma_slow ? 1 : ma_medium < ma_slow ? -1 : 0 : madirection
madirection := disableFastMAFilter and disableMediumMAFilter ? ma_coloured > ma_slow ? 1 : -1 : madirection
madirection := disableFastMAFilter and disableSlowMAFilter ? ma_coloured > ma_medium ? 1 : -1 : madirection
madirection := disableSlowMAFilter and disableMediumMAFilter ? ma_coloured > ma_fast ? 1 : -1 : madirection
//
// Supertrend Calculations
SUp = hl2 - SFactor * ta.atr(SPd)
SDn = hl2 + SFactor * ta.atr(SPd)
STrendUp = 0.0
STrendDown = 0.0
STrendUp := close[1] > STrendUp[1] ? math.max(SUp, STrendUp[1]) : SUp
STrendDown := close[1] < STrendDown[1] ? math.min(SDn, STrendDown[1]) : SDn
STrend = 0
STrend := close > STrendDown[1] ? 1 : close < STrendUp[1] ? -1 : nz(STrend[1], 1)
Tsl = STrend == 1 ? STrendUp : STrendDown
// Standard Bollinger or KC Bands
basis = ta.sma(ma_src, bbLength)
rangema = ta.sma(ta.tr, bbLength)
stdev_1 = ta.stdev(ma_src, bbLength)
dev = uKC ? bbStddev * rangema : bbStddev * stdev_1
// Calculate Bollinger or KC Channel
upper = basis + dev
lower = basis - dev
// Lookback for previous highest bar index
noiupper = math.abs(ta.highestbars(oiLength))
noilower = math.abs(ta.lowestbars(oiLength))
// ColouredMA OutsideIn
//oiupper = clrdirection<0 and noiupper>0 and highest(oiLength)>upper[noiupper]? 1 : 0
//oilower = clrdirection>0 and noilower>0 and lowest(oiLength)<lower[noilower]? 1 : 0
// MACross OutsideIN
oiMACrossupper = ta.crossunder(ma_fast, ma_coloured) and noiupper > 0 and ta.highest(oiLength) > upper[noiupper] ? 1 : 0
oiMACrosslower = ta.crossover(ma_fast, ma_coloured) and noilower > 0 and ta.lowest(oiLength) < lower[noilower] ? 1 : 0
// === /SERIES VARIABLES ===
// === PLOTTING ===
// All the MA's
plot(ma_coloured, title='Coloured MA', color=clrdirection < 0 ? lightcoral : color.blue, linewidth=3, transp=20)
plot(hideMALines ? na : ma_fast, title='Fast MA', color=color.new(color.lime, 20), linewidth=2)
plot(hideMALines ? na : ma_medium, title='Medium MA', color=color.new(color.red, 10), linewidth=2)
plot(hideMALines ? na : ma_slow, title='Slow MA', color=color.new(color.gray, 10), linewidth=2)
// show 3xMA Trend Direction State.
dcolour = madirection == 1 ? color.green : madirection == -1 ? color.red : color.yellow
plotshape(hideTrendDirection ? na : madirection, title='3xMA Trend Direction', location=location.bottom, style=shape.square, color=dcolour, transp=10)
// SuperTrend
plot(hideSuperTrend ? na : Tsl, color=STrend == 1 ? color.green : color.maroon, style=plot.style_line, linewidth=2, title='SuperTrend')
// Bollinger Bands
p1 = plot(hideBollingerBands ? na : upper, title='BB upper', color=color.new(dodgerblue, 20), linewidth=1)
p2 = plot(hideBollingerBands ? na : lower, title='BB lower', color=color.new(dodgerblue, 20), linewidth=1)
//fill(p1, p2, color=color.new(dodgerblue, 96), title='BB fill')
// === /PLOTTING ===
// === ALERTING ===
// 3xMA Filtering
_3xmabuy = 0
_3xmasell = 0
_3xmabuy := clrdirection == 1 and close > ma_fast and madirection == 1 ? nz(_3xmabuy[1]) + 1 : clrdirection == 1 and madirection == 1 ? nz(_3xmabuy[1]) > 0 ? nz(_3xmabuy[1]) + 1 : 0 : 0
_3xmasell := clrdirection == -1 and close < ma_fast and madirection == -1 ? nz(_3xmasell[1]) + 1 : clrdirection == -1 and madirection == -1 ? nz(_3xmasell[1]) > 0 ? nz(_3xmasell[1]) + 1 : 0 : 0
//
// SuperTrend Filtering
stbuy = 0
stsell = 0
stbuy := clrdirection == 1 and STrend == 1 ? nz(stbuy[1]) + 1 : 0
stsell := clrdirection == -1 and STrend == -1 ? nz(stsell[1]) + 1 : 0
//
// 3xMA & SuperTrend Filtering
//
st3xmabuy = 0
st3xmasell = 0
st3xmabuy := (disable3xMAFilter or _3xmabuy > 0) and stbuy > 0 ? nz(st3xmabuy[1]) + 1 : 0
st3xmasell := (disable3xMAFilter or _3xmasell > 0) and stsell > 0 ? nz(st3xmasell[1]) + 1 : 0
// Bollinger Outside In using ColuredMA direction Filter.
//oibuy = 0
//oisell = 0
//oibuy  := clrdirection == 1 and oilower==1? nz(oibuy[1])+1  : 0
//oisell := clrdirection ==-1 and oiupper==1? nz(oisell[1])+1 : 0
// Bollinger Outside In using MACross signal Filter
oiMACrossbuy = 0
oiMACrosssell = 0
oiMACrossbuy := oiMACrosslower == 1 ? nz(oiMACrossbuy[1]) + 1 : 0
oiMACrosssell := oiMACrossupper == 1 ? nz(oiMACrosssell[1]) + 1 : 0
// Bollinger Outside In + 3xMA Filter
oi3xmabuy = 0
oi3xmasell = 0
oi3xmabuy := oiMACrossbuy > 0 and (disable3xMAFilter or madirection == 1) ? nz(oi3xmabuy[1]) + 1 : 0
oi3xmasell := oiMACrosssell > 0 and (disable3xMAFilter or madirection == -1) ? nz(oi3xmasell[1]) + 1 : 0
// Bollinger Outside In + SuperTrend Filter
oistbuy = 0
oistsell = 0
oistbuy := oiMACrossbuy > 0 and STrend == 1 ? nz(oistbuy[1]) + 1 : 0
oistsell := oiMACrosssell > 0 and STrend == -1 ? nz(oistsell[1]) + 1 : 0
// FastMA crossover HullMA and SuperTrend
macrossSTbuy = 0
macrossSTsell = 0
macrossSTbuy := ta.crossover(ma_fast, ma_coloured) and STrend == 1 ? nz(macrossSTbuy[1]) + 1 : 0
macrossSTsell := ta.crossunder(ma_fast, ma_coloured) and STrend == -1 ? nz(macrossSTsell[1]) + 1 : 0
// FastMA crossover HullMA and 3xMA
macross3xMAbuy = 0
macross3xMAsell = 0
macross3xMAbuy := ta.crossover(ma_fast, ma_coloured) and (disable3xMAFilter or madirection == 1) ? nz(macross3xMAbuy[1]) + 1 : 0
macross3xMAsell := ta.crossunder(ma_fast, ma_coloured) and (disable3xMAFilter or madirection == -1) ? nz(macross3xMAsell[1]) + 1 : 0
//
// Check any Alerts set
long = u3xMATrendFilter and _3xmabuy == 1 or uSuperTrendFilter and stbuy == 1 or uBothTrendFilters and st3xmabuy == 1 or uOI3xMAFilter and oi3xmabuy == 1 or uOISTFilter and oistbuy == 1 or uOIMACrossFilter and oiMACrossbuy == 1 or uMACrossSTFilter and macrossSTbuy == 1 or uMACross3xMAFilter and macross3xMAbuy == 1
short = u3xMATrendFilter and _3xmasell == 1 or uSuperTrendFilter and stsell == 1 or uBothTrendFilters and st3xmasell == 1 or uOI3xMAFilter and oi3xmasell == 1 or uOISTFilter and oistsell == 1 or uOIMACrossFilter and oiMACrosssell == 1 or uMACrossSTFilter and macrossSTsell == 1 or uMACross3xMAFilter and macross3xMAsell == 1
//
// If Alert Detected, then Draw Big fat liner
//plotshape(long ? long : na, title='Long Line Marker', location=location.belowbar, style=shape.arrowup, color=buyclr, size=size.auto, text='████████████████', textcolor=buyclr, transp=20)
//plotshape(short ? short : na, title='Short Line Marker', location=location.abovebar, style=shape.arrowdown, color=sellclr, size=size.auto, text='████████████████', textcolor=sellclr, transp=20)
// --- Arrow style signals
// No Filters only Hull Signals
hbuy = 0
hsell = 0
hbuy := clrdirection == 1 ? nz(hbuy[1]) + 1 : 0
hsell := clrdirection == -1 ? nz(hsell[1]) + 1 : 0
// FastMA crossover HullMA
macrossbuy = 0
macrosssell = 0
macrossbuy := ta.crossover(ma_fast, ma_coloured) ? nz(macrossbuy[1]) + 1 : 0
macrosssell := ta.crossunder(ma_fast, ma_coloured) ? nz(macrosssell[1]) + 1 : 0
//
along = disableAllFilters and hbuy == 1 or uMACrossFilter and macrossbuy == 1
ashort = disableAllFilters and hsell == 1 or uMACrossFilter and macrosssell == 1
// 
// If ColouredMA or MACross then draw big arrows
//plotarrow(along ? 1 : ashort ? -1 : na, title='ColouredMA or MACross Arrow', colorup=color.new(buyColour, 20), colordown=color.new(sellColour, 20), maxheight=100, minheight=50)


//----------Input Bannos----------------------------------------------------------------------------------------------------------//
var triggerlong = 0
var triggershort = 0
var up = 0
var down = 0
var bool longe = 0
var bool shorte = 0
var SL = 0
var entryvalueup = 0.00
var entryvaluedown = 0.00
var SLfactor = 0.5/100
var SLup = 0.00
var SLdown = 0.00
var longbuffer = 0
var shortbuffer = 0

//RSI parameters
overbought = input(70, title="overbought value")
oversold = input(30, title="oversold value")
sellRsi = ta.rsi(close, 11) > overbought
buyRsi = ta.rsi(close, 11) < oversold

var tampon_overbought = 0
var tampon_oversold = 0

//condition to use RSI
if sellRsi
    tampon_overbought := 1
if buyRsi
    tampon_oversold := 1
    
//close condition SL
if entryvalueup > 0  and low < SLup
    SL := 1



//Chaikin Volatility Strategy indicator if Volatility > 0 then Long or short, otherweise no

Length = input.int(10, '', minval=1)
ROCLength = input.int(12, '',minval=1)
Trigger = input.int(0, '',minval=0)
hline(0)
hline(Trigger)
xPrice1 = high
xPrice2 = low
xPrice = xPrice1 - xPrice2
xROC_EMA = ta.roc(ta.ema(xPrice, Length), ROCLength)
var pos = 0

if xROC_EMA < Trigger
    pos := 1
    nz(pos[1], 0)

if xROC_EMA > Trigger
    pos := -1
    nz(pos[1], 0)

//-----------------------------------------------------------------------------

// plot(xROC_EMA, title="Chaikin Volatility Strategy")
// plot(longe ? 1 : 0, 'longe')
// plot(shorte ? 1 : 0, 'shorte')
plot(entryvalueup, 'entree Long')
plot(SLup, 'SL Long')

plot(entryvaluedown, 'entree Short')
plot(SLdown, 'SL Short')

// plot(entryvalueup, 'entrryvalueup')
// plot(entryvaluedown, 'entrryvaluedown')
// plot(up, 'up')
//plot(down, 'down')
// plot(ta.rsi(close, 11), 'RSI')
// plot(tampon_overbought, 'tampon Overbought')
// plot(tampon_oversold, 'tampon Oversold')
// plot( triggerlong, ' triggerlong')
//plot( triggershort, ' triggershort')
// plot(sellRsi ? 1 : 0, 'sellRsi')


//close condition TP
closelong = (tampon_overbought == 1 and ta.rsi(close, 11) < 63.8 or shorte or SL == 1)
closeshort = (tampon_oversold == 1 and ta.rsi(close, 11) > 36.2 or longe or SL == 1)


//reinit after long Close
if closelong
    up := 0
    longe := 0
    tampon_overbought := 0
    triggerlong := 0
    SL := 0
    entryvalueup := 0
    
    
    //reinit after short Close
if closeshort
    down := 0
    shorte := 0
    tampon_oversold := 0
    triggershort := 0
    SL := 0
    entryvaluedown := 0

    
    
//condition sous sur MA SLOW to start
if close < ma_medium
    triggerlong := 0
    triggershort := 1
    
if close > ma_medium
    triggershort := 0
    triggerlong := 1



// Update alarm conditions

if long or along
    longbuffer := 1

if short or ashort
    shortbuffer := 1    

longe := longbuffer and triggerlong and xROC_EMA > 3.5
shorte := shortbuffer and triggershort and xROC_EMA > 3.5

// // var longe = long ? 1 : 0
// // var shorte = short ? 1 : 0

if longe == 1 and close > open 
    up := 1
    down  := 0
    entryvalueup :=close
    SLup := close - 0.7*(high - low)
    SLdown := 0
    longbuffer := 0

if shorte == 1 and close < open
    down := 1
    up := 0
    entryvaluedown := close
    SLdown := close + 0.7*(high - low)
    SLup := 0
    shortbuffer := 0

strategy.entry('longe', strategy.long, when = up)
strategy.entry('shorte', strategy.short, when = down)
strategy.close('longe', when= closelong)
strategy.close('shorte', when= closeshort)


// === /ALERTING ===
// === ALARMS ===
//
alertcondition(up or down or closelong or closeshort, title='Signal Alert', message='SIGNAL')
alertcondition(up, title='Long Alert', message='LONG')
alertcondition(down, title='Short Alert', message='SHORT')
alertcondition(closelong, title='close Long Alert', message='Close LONG')
alertcondition(closeshort, title='close Short Alert', message='Close SHORT')

// === /ALARMS ===




//EOF

```

> Detail

https://www.fmz.com/strategy/362055

> Last Modified

2022-05-09 21:37:30

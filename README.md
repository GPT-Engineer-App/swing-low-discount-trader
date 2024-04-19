# swing-low-discount-trader

// Define the lookback period
var lookback = input.int(45, "Donchian Length", 1, 1000, 1 )

// // Calculate the swing low
// var float buy_poi = ta.lowestbars(low[2], lookback)
// var float sell_poi = ta.highestbars(high[2], lookback)

var float nearestBuyPrice = ta.lowest(low[2], lookback) - 25
var float nearestSellPrice = ta.highest(high[2], lookback) +25

if not na(pivothigh)
    line.new(x1 = phtimestart, y1 = pivothigh, x2 = bhend, y2 = pivothigh, color = phlinecol, xloc = xloc.bar_time, width = 2)
    line.new(x1 = phtimestart, y1 = pivothigh, x2 = phtimestart, y2 = pivothigh + width, color = phlinecol, xloc = xloc.bar_time, width = 2)
    line.new(x1 = bhend, y1 = pivothigh, x2 = bhend, y2 = pivothigh + width, color = phlinecol, xloc = xloc.bar_time, width = 2)
    //pivothigh_label:=(label.new(x = (phtimestart + bhend) / 2, y = pivothigh + width, text = "SELL @"+ str.tostring(math.round_to_mintick(pivothigh)), color = phbgcol,textcolor = phtextcol, xloc = xloc.bar_time))
    sellSignal := close<=pivothigh and close[1]<=pivothigh and window() and strategy.position_size==0 //and array.size(short_boxes)>0 
    // nearestSellPrice:= pivothigh-45
    
        //Block 6 : Trade Execution Controls
    if(am_Mode=="ENABLE")
        //     //Buy Fresh Call Option
        // if buy and strategy.position_size == 0 and window()
        //     strategy.entry('LONGCALL', strategy.long, limit=nearestBuyPrice, alert_message=LongCall)
        //     // strategy.entry("BUY", strategy.long, limit=nearestBuyPrice, comment = le)
                
            //Buy Fresh Put Option
        if sell and strategy.position_size == 0 and window() 
            strategy.entry('LONGPUT', strategy.short, limit=nearestSellPrice, alert_message=LongPut)
            if close> nearestSellPrice and close[1]>=nearestSellPrice or math.abs(pivothigh-nearestSellPrice) >= 55
                strategy.cancel("LONGPUT")
                strategy.cancel_all()
                
            // //Exit Old Put Option and Entery Fresh Call Option (Buy)
            // if buy and strategy.position_size < 0 and window()
            //     strategy.close('LONGPUT',  when=window(), alert_message=ExitPut)
            //     strategy.entry('LONGCALL', strategy.long, alert_message=LongCall)
            
            //Exit Old Call Option and Enter Fresh Put Option (Buy)
            if sell and strategy.position_size > 0 and window()
                strategy.close('LONGCALL',  when=window() ,alert_message=ExitCall)
                strategy.entry('LONGPUT', strategy.short, limit=nearestSellPrice, alert_message=LongPut)
                
        if(am_Mode=="LONGONLY")
            //Buy Fresh Call Option
            // if buy and strategy.position_size == 0 and window()
            //     strategy.entry('LONGCALL', strategy.long, alert_message=LongCall)
            
            if sell and strategy.position_size > 0 and window()
                strategy.close('LONGCALL', alert_message=ExitCall)
                
        if(am_Mode=="SHORTONLY")
            // if buy and strategy.position_size < 0 and window()
            //     strategy.close('LONGPUT', alert_message=ExitPut)
                
            //Buy Fresh Put Option
            if sell and strategy.position_size == 0 and window()
                strategy.entry('LONGPUT', strategy.short, limit=nearestSellPrice, alert_message=LongPut)

        if(intraday)
            // longsquareOff = not intradaySession and strategy.position_size > 0 
            // strategy.close(id='LONGCALL', when=longsquareOff, comment='Square-off',alert_message=sqCall)
            shortsquareOff = not intradaySession and strategy.position_size < 0 
            strategy.close(id='LONGPUT', when=shortsquareOff, comment='Square-off',alert_message=sqPut)

        // strategy.entry("SELL", strategy.short, limit = nearestSellPrice, comment = se)
        
        // // Define the target value and the range of values to search
        // //targetValue = pivothigh
        // minValue = 0
        // maxValue = 150

        // // Iterate over the range of values
        // for i = math.min(minValue, maxValue) to maxValue
        //     // Check if the current value is closer to the target value than the current closest price
        //     if math.abs(data[i] - pivothigh) < math.abs(nearestSellPrice - pivothigh)
        //     // nearestBuyPrice := if close >= pivotlow + i and close <= pivotlow + i 
        //         // Update the closest price
        //         nearestSellPrice:= data[i]
        //         strategy.entry("SELL", strategy.short, limit=nearestSellPrice, comment = se)
        // // Output the closest price
        // When the short order didn't fill in 7 bars since the cross under,
            // cancel the pending short order
            if ta.barssince(pivothigh)==10 and close15m>pivothigh and close>pivothigh
                strategy.cancel("LONGPUT")
                strategy.cancel_all()

plot(nearestSellPrice, color=color.red, title="Label price", offset = 180, style = plot.style_cross)
//plot(pivothigh_label, title='sell', color=color.new(#00ffaa, 70), linewidth=2, style=plot.style_line, offset=15, trackprice=true)

// data = request.security(syminfo.tickerid, "15", close, lookahead = barmerge.lookahead_on)
// nearestBuyPrice=0.0
   
//var float buyPrice=0
label pivotlow_label=na
if not na(pivotlow) 
    line.new(x1 = pltimestart, y1 = pivotlow, x2 = blend, y2 = pivotlow, color = pllinecol, xloc = xloc.bar_time, width = 2)
    line.new(x1 = pltimestart, y1 = pivotlow, x2 = pltimestart, y2 = pivotlow - width, color = pllinecol, xloc = xloc.bar_time, width = 2)
    line.new(x1 = blend, y1 = pivotlow, x2 = blend, y2 = pivotlow - width, color = pllinecol, xloc = xloc.bar_time, width = 2)
    //pivotlow_label:= label.new(x = (pltimestart + blend) / 2, y = pivotlow - width, text = "BUY @ " + str.tostring(math.round_to_mintick(pivotlow)),color = plbgcol, textcolor = pltextcol, style = label.style_label_up, xloc = xloc.bar_time)
    //buyPrice:= label.get_y(pivotlow_label) 
    buySignal:= close>=pivotlow and close[1]>=pivotlow and window() and strategy.position_size==0 //and array.size(long_boxes)>0 
        // true
        // Define the target value and the range of values to search
        //targetValue = 50
    // minValue = 0
    // maxValue = 165

        // Iterate over the range of values
    // for i = math.min(minValue, maxValue) to maxValue
    //     // Check if the current value is closer to the target value than the current closest price
    //     if math.abs(data[i] - pivotlow) < math.abs(nearestBuyPrice - pivotlow)
    //     // nearestBuyPrice := if close >= pivotlow + i and close <= pivotlow + i 
    //         // Update the closest price
    //         nearestBuyPrice:= data[i] 
    //         // if math.abs(nearestBuyPrice - pivotlow) >= 55 and bar_index>1
    //         //     nearestBuyPrice:= close-50
    //             //Block 6 : Trade Execution Controls
    if(am_Mode=="ENABLE")
            //Buy Fresh Call Option
        if buy and strategy.position_size == 0 and window()
            strategy.entry('LONGCALL', strategy.long, limit = nearestBuyPrice, alert_message=LongCall)
            if close<nearestBuyPrice and close[1]<=nearestBuyPrice or math.abs(nearestBuyPrice - pivotlow) >= 55
            // if math.abs(nearestBuyPrice - pivotlow) >= 200
                strategy.cancel("LONGCALL")
                strategy.cancel_all()
        // strategy.entry("BUY", strategy.long, limit=nearestBuyPrice, comment = le)
                
        //     //Buy Fresh Put Option
        // if sell and strategy.position_size == 0 and window()
        //     strategy.entry('LONGPUT', strategy.short, limit=nearestSellPrice, alert_message=LongPut)
            
            
            //Exit Old Put Option and Entery Fresh Call Option (Buy)
            if buy and strategy.position_size < 0 and window()
                strategy.close('LONGPUT',  when=window(), alert_message=ExitPut)
                strategy.entry('LONGCALL', strategy.long, limit = nearestBuyPrice, alert_message=LongCall)
            
            // //Exit Old Call Option and Enter Fresh Put Option (Buy)
            // if sell and strategy.position_size > 0 and window()
            //     strategy.close('LONGCALL',  when=window() ,alert_message=ExitCall)
            //     strategy.entry('LONGPUT', strategy.short, alert_message=LongPut)
                
        if(am_Mode=="LONGONLY")
            // Buy Fresh Call Option
            if buy and strategy.position_size == 0 and window()
                strategy.entry('LONGCALL', strategy.long, limit = nearestBuyPrice, alert_message=LongCall)
            
            // if sell and strategy.position_size > 0 and window()
            //     strategy.close('LONGCALL' ,alert_message=ExitCall)
                
        if(am_Mode=="SHORTONLY")
            if buy and strategy.position_size < 0 and window()
                strategy.close('LONGPUT',  alert_message=ExitPut)
                
            // //Buy Fresh Put Option
            // if sell and strategy.position_size == 0 and window()
            //     strategy.entry('LONGPUT', strategy.short, alert_message=LongPut)

        if(intraday)
            longsquareOff = not intradaySession and strategy.position_size > 0 
            strategy.close(id='LONGCALL', when=longsquareOff, comment='Square-off',alert_message=sqCall)
            // shortsquareOff = not intradaySession and strategy.position_size < 0 
            // strategy.close(id='LONGPUT', when=shortsquareOff, comment='Square-off',alert_message=sqPut)


    // strategy.entry("BUY", strategy.long, limit=nearestBuyPrice, comment = le)

    // When the short order didn't fill in 7 bars since the cross under,
    // cancel the pending short order
        if ta.barssince(pivotlow)==10 and close15m<pivotlow and close<pivotlow
            strategy.cancel("LONGCALL")
            strategy.cancel_all()

        // Output the closest price
plot(nearestBuyPrice, color=color.green, title="Label price",  offset = 180, style = plot.style_cross)

I have above pinescript version 5 code where I have to place my buy orders at discount price which must be swing low of the price on chart after the bull1met condition is met to keep stop minimum and safe. please suggest best possible code changes that will be executed successfully.


## Collaborate with GPT Engineer

This is a [gptengineer.app](https://gptengineer.app)-synced repository 🌟🤖

Changes made via gptengineer.app will be committed to this repo.

If you clone this repo and push changes, you will have them reflected in the GPT Engineer UI.

## Tech stack

This project is built with React and Chakra UI.

- Vite
- React
- Chakra UI

## Setup

```sh
git clone https://github.com/GPT-Engineer-App/swing-low-discount-trader.git
cd swing-low-discount-trader
npm i
```

```sh
npm run dev
```

This will run a dev server with auto reloading and an instant preview.

## Requirements

- Node.js & npm - [install with nvm](https://github.com/nvm-sh/nvm#installing-and-updating)

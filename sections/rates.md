# Rate Endpoints

| Endpoint | Description |
| ---- | ---- |
| [GET /v1/instruments](#get-v1instruments) | Instrument Discovery |
| [GET /v1/quote](#get-v1quote) | Get current price for specified instrument(s) |
| [GET /v1/history](#get-v1history) | Get historical rates for an instrument |
<!--
| [POST /v1/instruments/poll](#post-v1instrumentspoll) | Create and modify rates/candle polling session ([about rates polling](#aboutratespolling))|
| [GET /v1/instruments/poll](#get-v1instrumentsinstrumentspoll) | Rates/candle polling ([about rates polling](#aboutratespolling))|
-->

## GET /v1/instruments

Return a list of instruments (currency pairs, CFDs, and commodities) that are available on the OANDA platform.

#### Request
    http://api-sandbox.oanda.com/v1/instruments

#### Response
    {
         "instruments" : [
             {"instrument":"AUD_CAD", "displayName" : "AUD/CAD", "pip" : "0.0001", maxTradeUnits: 10000},
             {"instrument":"AUD_CHF", "displayName" : "AUD/CHF", "pip" : "0.0001", maxTradeUnits: 10000},
             .
             .
             {"instrument":"ZAR_JPY", "displayName" : "ZAR/JPY", "pip" : "0.0001", maxTradeUnits: 10000}
         ]
    }

#### Query Parameters
**Optional**

* __visibility__: "tradeable" (default) or "all". instrument that is tradeable means user can place a trade and order in with that instrument.  
* __fields__: A comma-separated list of instrument fields which are to be returned in the response.  Please see the Response Parameters section below for a list of valid values.

#### Response Parameters

* **instrument**: (default) Name of the instrument.  This value should be use when used to fetch prices and create orders and trades.
* **displayName**: (default) Display name for end user.
* **pip**: (default) Value of 1 pip for the instrument. [More on pip](http://www.babypips.com/school/pips-and-pipettes.html)
* **maxTradeUnits**: (default) The maximum number of units that can be traded for the instrument.
* **precision**: The smallest unit of measurement to express the change in value between the instrument pair. 
* **maxTrailingStop**: The maximum trailing stop value (in pips) that can be set when trading the instrument.
* **minTrailingStop**: The minimum trailing stop value (in pips) that can be set when trading the instrument.
* **marginRate**: The margin requirement for the instrument. A 3% margin rate will be represented as 0.03. 

<!--
* **pipLocation**: 10^(pipLocation) == value of 1 pip for the instrument.
* **extraPrecision**: The number decimal places provided after the pip.
-->

## GET /v1/quote

Fetch live prices for specified instruments that are available on the OANDA platform.

#### Request
    http://api-sandbox.oanda.com/v1/quote?instruments=EUR_USD,USD_JPY

#### Response
	{
		"prices":
		[
			{
				"instrument":"EUR_USD",
				"time":1354233933.435768,
				"bid":1.29685,
				"ask":1.29716
			},
			{
				"instrument":"USD_JPY",
				"time":1354233926.451438,
				"bid":82.109,
				"ask":82.125
			}
		]
	}

#### Query Parameters

**Required**
<!--
* __visibility__: "tradeable" (default) or "all". instrument that is tradeable means user can place a trade and order in with that instrument.
-->
* __instruments__:  A comma-separated list of instruments to fetch prices for.  Values should be one of the available `instrument` from the /v1/instruments response.
                    For Example - http://api-sandbox.oanda.com/v1/quote?instruments=EUR_USD

<!--
**Optional**

* __volume__: The volume used to determine which rung is returned. To determine which rung will be returned, the maximum tradeable amount of the current rung must be greater than the volume and the maximum tradeable amount of the previous rung must be less than the volume.  
For example, consider an instrument with the following ladder structure:
<pre>
	[0] 1,000,000 bid: 0.0 ask: 0.0  
	[1] 5,000,000 bid: 0.1 ask: 0.1  
	[2] 10,000,000 bid: 0.3 ask: 0.3  
</pre>  
Requesting the instrument's price with the following volumes will return in the following rungs:
<pre>
    0 - 999,999             => Rung 0
    1,000,000 - 4,999,999   => Rung 1
    >= 5,000,000            => Rung 2
</pre>    
__volume__ has a default value of 0, meaning that by default only the lowest run will be returned.
-->

## GET /v1/history

#### Request
    http://api-sandbox.oanda.com/v1/history?instrument=EUR_USD&count=2&candleFormat="mid"

#### Respond
    {
        "instrument" : "EUR_USD",
        "granularity": "S5",
        "candles": [
           {
               "time": 1350683410,
               "openMid": 1.30237,
               "highMid": 1.30237,
               "lowMid": 1.30237,
               "closeMid": 1.30237,
               "volume" : 5000,
               "complete": "true",
           },
           {
               "time": 1350684320,
               "openMid": 1.30242,
               "highMid": 1.30242,
               "lowMid": 1.30242,
               "closeMid": 1.30242,
               "volume" : 2000,
               "complete": "true"
           }
        ]
        
    }


#### Query Parameters

**Required**
<!--
* __visibility__: "tradeable" (default) or "all". instrument that is tradeable means user can place a trade and order in with that instrument.
-->
* __instrument__:  Name of the instrument to retreive history for.  The instrument should be one of the available instrument from the /v1/instruments response.

**Optional**

* __granularity__: The time range represented by each candlestick.  The value specified will determine the alignment of the first candlestick.
    
	Valid values are:

	* __Top of the minute alignment__
		* "S5"  - 5 seconds
		* "S10" - 10 seconds
		* "S15" - 15 seconds
		* "S30" - 30 seconds
		* "M1"  - 1 minute
	* __Top of the hour alignment__
		* "M2"  - 2 minutes
		* "M3"  - 3 minutes
		* "M5"  - 5 minutes
		* "M10" - 10 minutes
		* "M15" - 15 minutes
		* "M30" - 30 minutes
		* "H1"  - 1 hour
	* __Start of day alignment (12 am, Timezone/New York)__
		* "H2"  - 2 hours
		* "H3"  - 3 hours
		* "H4"  - 4 hours
		* "H6"  - 6 hours
		* "H8"  - 8 hours
		* "H12" - 12 hours
		* "D"   - 1 Day
	* __Start of week alignment (Saturday)__
		* "W"   - 1 Week
	* __Start of month alignment (First day of the month)__
		* "M"   - 1 Month
	

The default for __granularity__ is "S5" if the granularity parameter is not provided.

* __count__: The number of candles to return in the response. This paramater may be ignored by the server depending on the time range provided. See "Time and Count Semantics" below for a full description.  * 
The default for __count__ is 500. Max value for __count__ is 5000.  
             
__Note__: __count__ should not be specified if both the __start__ and __end__ parameters are also specified.

* __start__: The start timestamp for the range of candles requested. Default: NULL (unset)

* __end__: The end timestamp for the range of candles requested. Default: NULL (unset)

* __candleFormat__: Candlesticks representation ([about candestick representation](#candlestick-representation)). This can be one of the following:
	* "mid" - Midpoint based candlesticks.
	* "bidask" - Bid/Ask based candlesticks

The default for __candleFormat__ is "bidask" if the candleFormat parameter is not provided.

* __includeFirst__: A boolean field which may be set to "true" or "false". If it is set to "true", the candlestick covered by the <i>start</i> timestamp will be returned. If it is set to "false", this candlestick will not be returned.  
This field exists to provide clients a mechanism to not repeatedly fetch the most recent candlestick which it is not a "Dancing Bear".  
Default: true

* No candles are published for intervals where there are no ticks.  This will result in gaps in between time periods.

<!--
## POST /v1/instruments/poll
#### Request
    POST /v1/instruments/poll HTTP/1.1
    Accept: */*
    Connection: close
    User-Agent: OAuth gem v0.4.4
    Content-Type: application/json
    Host: abc.com

    {
        "prices" : [
            "EUR_USD",
            "USD_CAD"
        ],
        "candles" : [
            {
                "instrument" : "EUR_JPY",
                "granularity" : "S5",
                "start" : 1340895300
            },
            {
                "instrument" : "USD_CHF",
                "granularity" : "H1",
                "start" : 1340895300
            }
    }

#### Respond
    {"sessionId":"1234"}

#### Required scope
read

## GET /v1/instruments/poll
#### Request
    https://api-sandbox.oanda.com/v1/instruments/poll?sessionId=1234&candleFormat=M

#### Respond
    {
        "time": 1353023991
        "prices": [
            {"instrument":"EUR_USD", "time":135023990, "bid":1.21, "ask":1.22},
            {"instrument":"USD_JPY", "time":135023990, "bid":80.122, "ask":80.123}
        ],
        "candles": [
            {
                "instrument": "EUR_USD",
                "granularity": "S5",
                "candles": [
                    {
                        "time": 1350683410,
                        "openMid": 1.30237,
                        "highMid": 1.30237,
                        "lowMid": 1.30237,
                        "closeMid": 1.30237,
                        "complete": "true"
                    },
                    {
                        "time": 1350684320,
                        "openMid": 1.30242,
                        "highMid": 1.30242,
                        "lowMid": 1.30242,
                        "closeMid": 1.30242,
                        "complete": "true"
                    }
                ]
            },
            {
                "instrument": "USD_CHF",
                "granularity": "S5",
                "candles": [
                    {
                        "time": 1350683410,
                        "openMid": 1.30237,
                        "highMid": 1.30237,
                        "lowMid": 1.30237,
                        "closeMid": 1.30237,
                        "complete": "true"
                    },
                    {
                        "time": 1350684320,
                        "openMid": 1.30242,
                        "highMid": 1.30242,
                        "lowMid": 1.30242,
                        "closeMid": 1.30242,
                        "complete": "true"
                    }
                ]
            }
        ]
    }

#### Query Parameters

**Optional**

* __sessionId__: The polling session ID for the client. This MUST be provided for the server to be able to return
    the proper polled prices and candles for the client.
    
* __candleFormat__:
    Candlesticks representation (about candlestick presentation) This can be one of:
    * "M" - midpoint-based candlesticks
    * "BA" - BID/ASK-based candlesticks
    * "MV" - midpoint-based candlesticks
    * "BAV" - BID/ASK-based candlesticks
Default: "BA"


##About rates polling
####Overview
Clients may set up sessions with the server to manage the polling of prices and candlesticks. These polling sessions push the complexity of maintaining state for real-time candlestick graphs down to the server while simultaneously reducing the amount of network traffic between the client and server.

In order to poll for price and candlestick changes, client need to first setup a session by doing a POST to /instruments/poll with a configuration specifying which instruments the client whiches to get polled prices and candle data from. Once a session is setup, client can do a GET /instruments/poll

Candlestick polling will give you all new candle sticks since last time you poll. Price polling will only return you the latest price if there is an update

Notes: /instruments/poll is only meant to be used to retrieve updates. Please use /instruments/:instrument/candles for historical candles.

####Examples
1.Set up polling session for EUR/USD price changes

    curl -H "Content-Type: application/json" -d '{ "prices": [ "EUR/USD", "USD/CAD" ] }' http://api-sandbox.oanda.com/v1/instruments/poll

    {"sessionId":"123456"}

2.Poll for price changes

    curl http://api-sandbox.oanda.com/v1/instruments/poll?sessionId=123456

    {
        "time": 1350672751,
        "prices": [
            {
                "instrument": "EUR/USD",
                "time": 1350672733,
                "bid": 1.30206,
                "ask": 1.30215
            },
            {
                "instrument": "USD/CAD",
                "time": 1350672726,
                "bid": 0.99364,
                "ask": 0.99389
            }
        ]
    }

    curl http://api-sandbox.oanda.com/v1/instruments/poll?sessionId=123456

    {
        "time": 1350672802,
        "prices": [
            {
                "instrument": "EUR/USD",
                "time": 1350672795,
                "bid": 1.30204,
                "ask": 1.30213
            },
            {
                "instrument": "USD/CAD",
                "time": 1350672768,
                "bid": 0.99364,
                "ask": 0.99389
            }
        ]
    }

3.Change config for existing session (if needed)

    curl -H "Content-Type: application/json" -d '{ "sessionId":"123456", "prices": [ "EUR/USD" ] }' http://api-sandbox.oanda.com/v1/instruments/poll
-->


## Candlestick Representation

<!--

M" - midpoint-based candlesticks. Each Candle will have the format:

    {
        "timestamp":<TS>,
        "openMid":<O_m>,
        "highMid":<H_m>,
        "lowMid":<L_m>,
        "closeMid":<C_m>,
        "complete":<DB>
    }

"BA" - BID/ASK-based candlesticks

    {
        "timestamp":<TS>,
        "openBid":<O_b>,
        "openAsk":<O_a>,
        "highBid":<H_b>,
        "highAsk":<H_a>,
        "lowBid":<L_b>,
        "lowAsk":<L_a>,
        "closeBid":<C_b>,
        "closeAsk":<C_a>,
        "complete":<DB>
    }

-->

"mid" midpoint-based candlesticks with tick volume

    {
        "timestamp":<TS>,
        "openMid":<O_m>,
        "highMid":<H_m>,
        "lowMid":<L_m>,
        "closeMid":<C_m>,
        "volume":<V>,
        "complete":<DB>
    }

"bidask" - BID/ASK-based candlesticks with tick volume

    {
        "timestamp":<TS>,
        "openBid":<O_b>,
        "openAsk":<O_a>,
        "highBid":<H_b>,
        "highAsk":<H_a>,
        "lowBid":<L_b>,
        "lowAsk":<L_a>,
        "closeBid":<C_b>,
        "closeAsk":<C_a>,
        "volume":<V>,
        "complete":<DB>
    }

The fields in the above candlesticks have the following meanings:

* TS  - Start timestamp of the candlestick
* O_b - Open Tick Bid
* O_a - Open Tick Ask
* O_m - Open Tick Mid
* H_b - High Tick Bid
* H_a - High Tick Ask
* H_m - High Tick Mid
* L_b - Low Tick Bid
* L_a - Low Tick Ask
* L_m - Low Tick Mid
* C_b - Close Tick Bid
* C_a - Close Tick Ask
* C_m - Close Tick Mid
* V   - Candlestick tick volume
* DB  - "Dancing Bear". This optional field indicates that the candlestick
        may still be modified by the creation of ticks in the future because
        the current server time is contained by the time range which the
        candlestick represents.


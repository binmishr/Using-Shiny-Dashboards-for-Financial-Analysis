# Using-Shiny-Dashboards-for-Financial-Analysis

The details of the codeset and plots are included in the attached Microsoft Word Document (.docx) file in this repository. 
You need to view the file in "Read Mode" to see the contents properly after downloading the same.

A Brief Introduction - Crypto Whale Watching App
==================================================

## Introduction

Welcome! This is a Python-based Dash app meant to track whale activity in buy / sell walls on crypto-currency exchanges (presently just operational for GDAX, but more exchanges to come). This document aims to explain the purpose, functionality, and future of this project. Please do share this with your fellow coders / traders / crypto-aficionados, and contribute to the future of this project by calling out issues, requesting new features, and submitting pull requests to improve the app.

Scroll down to the How-To / User Guide section to learn more about how to run the app yourself. The most recent updates to the app include new features like an improved depth chart (shown in the screenshot below), as well as a side-menu that allows selective hiding / showing of various pairs of interest.

![First UI View](https://raw.githubusercontent.com/pmaji/crypto-whale-watching-app/master/screenshots/improved_new_plots.JPG)

## What's the point of this app?

Presently, GDAX allows users to see the buy and sell limit order volume via the "depth chart" shown below. 

<img src="https://raw.githubusercontent.com/pmaji/eth_python_tracker/master/screenshots/gdax_depth_chart.JPG" width="700" height="300">

The problem with this is that the depth chart does not tell us where the volume behind buy and sell walls is coming from. It could be from a few whales seeking to manipulate the market, or it could be from a large number of individuals who have placed orders around a modal point. In technical terms, there is no way to see how much of present resistance or support levels are due to individuals vs. group clustering. 

This difference is quite important, as it impacts how quickly walls can be pulled, etc., so having buy & sell-wall information at the order level can prove quite valuable for traders. This simple app allows you to access just that information, by focusing on individual limit orders that constitute large walls, and particularly emphasizing the largest orders, enabling the user to spot whales that may be manipulating the present price via various methods. How exactly we define / spot whale activity is the focus of the next section.

## How to spot a whale:

This is perhaps the most important question when it comes to the purpose of this app. We have iterated on this many times, and have arrived at an algorithmic definition that spots 2 types of whales:

* Single Price-Point Whales:
     * Place one large order at a single price-point 
     * Example: 500 ETH for sale at $1000 via 1 unique order
     * Represented via a bubble in the visualization.
     * Tooltip includes order price-point, volume, and number of unique orders
* Ladder Price-Point Whales:
     * Place many medium-sized orders for identical volume increasing in price
     * Example: 500 total ETH for sale starting at $900 and spanning until $1000 via 10 unique 50-ETH orders
     * Represented via a line bar  spanning the range of the ladder
     * Tooltip includes number and volume of orders, price-range of ladder, and total volume

While these two may seem different, their market impacts are often comparable. I think a real world analogy helps to illustrate this point:

> Imagine that you are looking to understand if there is any whale activity at your local coffee shop (I know this is silly but I promise it is a good example; stay with me here). You could sit near the ordering area and write down each individual order. If someone comes along and orders 100 lattes, that would get your attention--you have spotted a whale! What if, however, someone was ordering 5 lattes, and then getting in the back of the line again, and ordering another 5 lattes, repeating this until they had purchased a total of 100 lattes? Both of these individuals had a whale-like impact on market movement, but if all you care about is the size of each individual order, you will miss out on the sneaky whales that may be splitting their orders across a ladder of price-points.

> Given that GDAX's API doesn't display ETH addresses (only unique order IDs), we cannot say with 100% certainty that what looks like a "ladder price-point whale" is in fact a unitary actor. We have to make some assumptions. To continue the coffee shop metaphor, we aren't able to actually look at the people in line for coffee; instead, we have a blindfold on and are jotting down what we hear the barista say. We hear "1 order for 5 lattes" once and write it down, but if we hear "1 order for 5 lattes" 100 different times in close succession, then we can make the safe assumption that we are dealing with a whale, as this is analogous to seeing 100 orders for 10 ETH stacked in a ladder of price-points in close succession on GDAX. 

As can be seen from the UI screenshot below, for each currency pairing the user can easily examine the most obvious whales hiding amongst the walls. The algorithm used displays only those orders that make up >= 1% of the volume of the portion of the order book shown in the visualization (which is presently +/-5% from present market price). This methodology makes our analysis robust both in times of price stability--when there is both resistance and support similar in magnitude--as well as during times of large price fluctuation--when there may be very little support or very little resistance. Thanks to the creative coloring algorithm behind the visualization, the brightest colors are those most likely to be whales. The colors become progressively darker as the number of distinct orders at a price-point increases, allowing for easy visual identification of whales in the market.

![Main UI](https://raw.githubusercontent.com/pmaji/crypto-whale-watching-app/master/screenshots/eth_btc_main_view.JPG)

In addition to the main views which provide at-a-glance information about the largest orders, users can freeze the live refresh within the app in order to zoom in on particular sections of the order book, or to take better advantage of the tooltip capabilities of the Plotly visualization. Given that we now track both single price-point and ladder price-point whales, there are often times when bubbles overlap the linebar charts. When this happens, simply freeze the visualization and zoom in on a particular area to separate the two in a more detailed view. An example of the tooltip functionalities for both the ladder and single price-point whales can be seen via the screenshots below.

![Single-Price-Point Whale](https://raw.githubusercontent.com/pmaji/crypto-whale-watching-app/master/screenshots/single_price_point_whale_tooltip.JPG)

In the image above we are looking at a zoom-in on the chart of BTC/USD. The bubble that we have hovered over near the top towards the center of the view is a slightly dark red bubble (as opposed to the brightest red) because there are 2 orders at that exact price price-point; hence, we cannot be 100% confident that it is one whale acting unilaterally. That said, those 2 orders alone constitute almost $1,000,000.00 in sell-side pressure, so they are important to call out, and thus our methodology captures them. We can see additional useful information via the tooltip--namely, that these 2 sell orders together constitute 113.38 BTC in sell-order volume, with each of those orders being placed at a price-point of  $8,352.00. Lastly, we see the count of unique orders at this price point (2), along with the sum total value of those orders ($946,949.76).

![Ladder-Price-Point-Whale](https://raw.githubusercontent.com/pmaji/crypto-whale-watching-app/master/screenshots/ladder_price_point_whale_tooltip.JPG)

In the image above we are looking at the chart for ETH/USD, hovering over a large ladder price-point whale. Here we can see that the tooltip calls out the total number of rungs on the ladder--7 orders of 50 ETH each--as well as the price range in which those orders are found--from $506.99 to $520.00--and the total market value of those orders (in this case $179,051.50). The thickness of the line is also proportional to the size of each rung on the order ladder.

We cannot be as certain that ladder price-point whales are, in fact, individual whales, as it could just be a few people who place similar orders of identical size at different psychological modal points, but in most cases this is unlikely, and we leave it up to the user to make the ultimate determination as to whether or not they believe a particular order or set of orders to be a whale or not. That said, we do recognize that the ladder price-point whale methodology could perhaps be improved with variable controls, and we are actively looking into this along with other improvements. Our app only serves to provide the information, and to ascribe some semblance of a probability to the likelihood that an observation is a whale or not. 

## How-To / User Guide (and a few more technical details)

The present version tracks all major pairings (ETH/USD; ETH/BTC; BTC/USD; LTC/USD) but I can add more upon request. It is set to update every 5 seconds (to optimize load-time) but this can be changed easily in the code if you want to make the refreshes faster / slower. There are also buttons that allow the user to pause the automatic refresh ("Freeze all" / "Unfreeze all"), and hide any of the currency pairings that they do not wish to see displayed. The refresh-pausing functionality allows the user to preserve any zoom or limitation that they have selected via the Plotly viz. 

The size of each observation is determined algorithmically using a transformation of the square root of the volume of all orders at that particular price-point calibrated so that the bubbles never become unreasonably large or small. The color-coding allows for easy identification of whales, as described in the section above. 

Note: all of these limitations--i.e. the volume minimum, the order book limitations, etc., are parameterized within the app.py code and thus can be easily changed if so desired.

Anyone interested with Python 3.6 installed can download the app.py or clone the repo and run the app locally, just check to be sure you have the few required modules installed (I recommend [managing them within a virtual environment] created using conda or whatever other package manager you prefer). Once you have Python 3.6 installed, open up a Terminal and type:

> pip install -r /path/to/requirements.txt

Once its finished type:

> python app.py

Then open up your browser and type in the set of numbers that pop up in terminal, which should look similar to "127.0.0.1:8050".

A Brief Introduction - Financial Asset Comparision Tool
==========================================================

## Introduction

Welcome! The **Financial Asset Comparison Tool** is an R Shiny App that facilitates the comparison of a myriad of assets--both traditional and crypto--across time. The idea for this tool came to me when I was trading crypto-currencies actively, and spending a decent amount of time in investor telegram chats and forums. A common argument I would see was over what asset one should have invested in a short while ago, but it was clear that most such discussions were fueled by emotion--primarily ["FOMO"]--as opposed to testable metrics. This isn't just a popular type of discussion in the crypto investing space; in fact, it may be even more common in traditional finance. I wanted to create a tool that would be able to settle all such asset performance comparison questions, regardless of whether the question was about traditional assets such as equities, crypto-assets like Bitcoin and Ethereum, or some combination of both. The tools made available via this app allow for analysis of varying degrees of complexity, as can be seen in the visualization below.

![Methodology Flowchart](https://raw.githubusercontent.com/pmaji/crypto-asset-comparison-tool/master/screenshots/methodology_flowchart.JPG)

This scaling of metric complexity is also intuitively integrated into the UI design of the app itself, as illustrated by the screenshot below.

<img src="https://raw.githubusercontent.com/pmaji/crypto-asset-comparison-tool/master/screenshots/ui_scaling_complexity.JPG" width="900" height="500">

The ultimate purpose of this tool is thus to settle every question of the form: "If I had **M amount of money to invest** over **time period T**, what would have been the better investment as judged by various metrics: **Asset A** or **Asset B**?" 

To determine what metrics might be of greatest importance to your particular question, consider the following:

* What if I care about the absolute profit that I make?
     * If so, then consider the simple portfolio performance metrics.
* What if I care about relative gains and losses?
     * If so, then consider the rate of return metrics.
* What if I care about the trade-off that exists between asset performance and the risk / volatility associated with holding that asset?
     * If so, then consider the variance-adjusted rate of return metrics.
     
This tool is modular in nature, such that there are some parameters that affect the entire app, and others that only affect certain outputs. This allows the user, at a high level, to select assets of interest, a date-range of relevance, and an initial investment amount, and thereafter compare the chosen assets using a variety of metrics. The app facilitates this procedural evaluation by nature of its layout, such that there are sections that focus on portfolio value, simple returns, and risk-adjusted returns. 

Over the course of the rest of this document, we will walk through all of the features of the app one-by-one, explaining their purpose and functionality, along with any pertinent technical details. There is plenty of explanatory material contained within the web app UI itself, primarily via small comments explaining inputs, and embedded hyperlinks that provide more information about particular metrics or definitions. In cases where I provide such links and explanation wthin the app UI, I will not repeat the explanation here. In all screenshots of the app below, I will be using **Bitcoin (btc)** and **Ethereum (eth)** as the two assets to be compared. Read all the way through the final section to learn how you can support this project and how to access the app. I hope this tool helps you make more informed financial decisions moving forward, and better evaluate those you have made previously!


## UI Overview: Sidebar UI Elements

Starting with the top-leftmost elements of the UI *(screenshot below)*, you can see that directly to the right of the title, there is an icon composed of three horizonal lines. Clicking this icon will expand or collapse the entire sidebar. Below the title you will find 3 hyperlinked buttons that will direct the user to this markdown document.

<img src="https://raw.githubusercontent.com/pmaji/crypto-asset-comparison-tool/master/screenshots/title_links_bar.JPG" width="300" height="150">

The next segment of the sidebar *(screenshot below)* allows the user to choose which assets to compare. As is stated in the label, the lowercase assets are crypto-assets, and the uppercase assets are equities. This is done for two main reasons: visual identification, and avoidance of conflict. Visually it makes it easier at a glance to tell when you are comparing different types of assets, and, while it is rare, there are cases when there are crypto-assets and equities with the same ticker (i.e. one can still compare "ETH" (Ethan Allen) the equity with "eth" (Ethereum) the cryptocurrency). I include all equities listed on Yahoo finance as of March 2018 (excluding REITs and ETFs), along with every crypto-asset included on [CoinMetrics].

<img src="https://raw.githubusercontent.com/pmaji/crypto-asset-comparison-tool/master/screenshots/asset_input_bar.JPG" width="300" height="200">

The final segment of the sidebar *(screenshot below)* allows the user to pick what date range to consider for the comparison of their chosen assets. The default is to show the most recent six months (i.e., had you invested the specified amount of money 6 months ago, this is how the assets would have performed to date). The one noteworthy caveat (which is evident from the code and can be changed locally) is that the most recent date is always lagged by 3 days (i.e. if you are using the app on April 7th, the default will be to show 6 months of data from April 3rd and then backwards in time 6 months). I do this because the various APIs that I use sometimes have a lag, and also there are instances where the stock market is closed for Friday or Monday; hence, this lag is the minimum lag necessary to assure there are never cases of empty data caused by API lag and/or bank holidays.

<img src="https://raw.githubusercontent.com/pmaji/crypto-asset-comparison-tool/master/screenshots/date_range_bar.JPG" width="300" height="150">


## UI Overview: Portfolio Performance Section

The main time series chart shown *(screenshot below)* is rather intuitive: it simply shows the worth of two single-asset portfolios over time. In layman's terms, had you invested the same amount in both assets over the time series specified, this chart shows you the value of that investment over time. The default smoothing parameters were chosen arbitrarily to balance the tradeoff of resiliency vis-a-vis outliers and maintenance of fit, but can be changed as the user desires.

<img src="https://raw.githubusercontent.com/pmaji/crypto-asset-comparison-tool/master/screenshots/portfolio_box_ui.JPG" width="900" height="400">

The portfolio summary table *(screenshot below)* presents multiple aggregate metrics on which the two chosen assets can be judged. Each metric is its own column. For each column, the value in green is the value deemed optimal in terms of that particular metric. For example, the first column is "Asset Portfolio Max Worth"; hence, whichever asset resulted in the highest portfolio max worth over the range specified will have its value displayed in green, while the other will be displayed in red. The background within each cell is also a reactive bar chart, so the magnitude of each cell can be judged at a glance relative to the magnitude of the other cell in that column. Continuing the previous example of the "Asset Portfolio Max Worth" column, if the two assets being compared have max worths of $1000 and $2000 respectively, the bar charts in each cell will be of length 50% of cell width and 100% of cell width, respectively. 

<img src="https://raw.githubusercontent.com/pmaji/crypto-asset-comparison-tool/master/screenshots/summary_box_ui.JPG" width="900" height="200">

## UI Overview: Rate of Return Section

The rate of return section *(screenshot below)* is entirely intuitive: it is simply a time series chart of the returns for the chosen asset over the period specified. 

<img src="https://raw.githubusercontent.com/pmaji/crypto-asset-comparison-tool/master/screenshots/returns_box_ui.JPG" width="900" height="400">

## UI Overview: Variance-Adjusted Returns Section

The variance-adjusted return section *(screenshot below)* is a slightly more complicated additional set of metrics meant to supplement the base rate of return section. The parameter that allows the user to select the period over which to calculate rate of return (from the section above) also affects the variance-adjusted returns section, as the three varieties of Sharpe Ratio are calculated over that same time period. One must be careful when choosing the Risk Free Rate, because that rate should be chosen relative to the time period selected (i.e. if you choose to visualize "weekly" returns, your chosen risk-free rate should reflect a weekly risk free rate). Generally speaking, risk-free rates are chosen relative to some [US treasury rate], but it is parameterized over a relatively wide range such that the user can choose whatever rate they see fit, within reason. 

The three varieties of Sharpe Ratio shown are pretty standard, the only difference being the denominators, which are variance (VaR), standard deviation (StdDev), and expected shortfall (ES), in turn.

<img src="https://raw.githubusercontent.com/pmaji/crypto-asset-comparison-tool/master/screenshots/sharpe_box_ui.JPG" width="900" height="400">

## Analytic Conclusions

The app is laid out in such a way that as you descend vertically, you follow a logical series of questions about value: 

1. At the top, we see the simplest view: how the price fluctuations of both assets would have affected the value of an initial investment amount over a specified time period.

2. Following the portfolio time series view, we see a summary table that allows the user to see which asset performs best according to a selection of aggregate metrics. 

3. Thereafter, we depart from metrics that depend on the initial amount invested, and begin addressing questions of relative return on investment. The first section focuses on the most vanilla definition of return on investment, while the final section allows the user to see if those rates of return are truly worth it given the variance, standard deviation, and expected shortfall inherent to the assets in question. This is put last because it enables the user to see, following the logic of the series of views from top to bottom, that even though a particular asset may have gross returns that are higher than another, and even a slightly more elevated rate of return, upon adjusting for variance, the other investment would have been a "better" choice. The story told will obviously vary depending on the assets and time period selected, but the logical progression remains the same.

## Technical Details

Crypto currency data is scraped from [Coin Metrics]. Stock market OHCL data is obtained via [the Yahoo Finance functions] in the QuantMod R package. The main app structure and functionalities, along with all necessary packages, can be found in the app.R, while all analytic, data-cleaning, and visualization functions can be found in the Functions.R file.

```R 
install.packages(c("shiny", "xts", "quantmod", "zoo", "TTR", "htmlwidgets", "data.table", "rsconnect", "plotly", "plyr", "dplyr", "tidyr", "shinythemes", "PerformanceAnalytics", "DT", "formattable", "shinydashboard", "ggplot2", "reshape2", "scales", "lubridate"))
```

Do this for all packages listed in the app.R, and then to run the app, open a terminal, cd to wherever you have cloned the repository, and type:

```R
R -e "shiny::runApp('app.R')"
```

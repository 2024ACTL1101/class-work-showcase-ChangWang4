
## Algorithmic Trading Strategy

## Introduction

In this assignment, you will develop an algorithmic trading strategy by incorporating financial metrics to evaluate its profitability. This exercise simulates a real-world scenario where you, as part of a financial technology team, need to present an improved version of a trading algorithm that not only executes trades but also calculates and reports on the financial performance of those trades.

## Background

Following a successful presentation to the Board of Directors, you have been tasked by the Trading Strategies Team to modify your trading algorithm. This modification should include tracking the costs and proceeds of trades to facilitate a deeper evaluation of the algorithm’s profitability, including calculating the Return on Investment (ROI).

After meeting with the Trading Strategies Team, you were asked to include costs, proceeds, and return on investments metrics to assess the profitability of your trading algorithm.

## Objectives

1. **Load and Prepare Data:** Open and run the starter code to create a DataFrame with stock closing data.

2. **Implement Trading Algorithm:** Create a simple trading algorithm based on daily price changes.

3. **Customize Trading Period:** Choose your entry and exit dates.

4. **Report Financial Performance:** Analyze and report the total profit or loss (P/L) and the ROI of the trading strategy.

5. **Implement a Trading Strategy:** Implement a trading strategy and analyze the total updated P/L and ROI. 

6. **Discussion:** Summarise your finding.


## Instructions

### Step 1: Data Loading

Start by running the provided code cells in the "Data Loading" section to generate a DataFrame containing AMD stock closing data. This will serve as the basis for your trading decisions. First, create a data frame named `amd_df` with the given closing prices and corresponding dates. 

```r
# Load data from CSV file
amd_df <- read.csv("AMD.csv")
# Convert the date column to Date type and Adjusted Close as numeric
amd_df$date <- as.Date(amd_df$Date)
amd_df$close <- as.numeric(amd_df$Adj.Close)
amd_df <- amd_df[, c("date", "close")]
```

#### Plotting the Data
Plot the closing prices over time to visualize the price movement.
```r
plot(amd_df$date, amd_df$close,'l')
```

### Step 2: Trading Algorithm
Implement the trading algorithm as per the instructions. You should initialize necessary variables, and loop through the dataframe to execute trades based on the set conditions.

- Initialize Columns: Start by ensuring dataframe has columns 'trade_type', 'costs_proceeds' and 'accumulated_shares'.
- Change the algorithm by modifying the loop to include the cost and proceeds metrics for buys of 100 shares. Make sure that the algorithm checks the following conditions and executes the strategy for each one:
  - If the previous price = 0, set 'trade_type' to 'buy', and set the 'costs_proceeds' column to the current share price multiplied by a `share_size` value of 100. Make sure to take the negative value of the expression so that the cost reflects money leaving an account. Finally, make sure to add the bought shares to an `accumulated_shares` variable.
  - Otherwise, if the price of the current day is less than that of the previous day, set the 'trade_type' to 'buy'. Set the 'costs_proceeds' to the current share price multiplied by a `share_size` value of 100.
  - You will not modify the algorithm for instances where the current day’s price is greater than the previous day’s price or when it is equal to the previous day’s price.
  - If this is the last day of trading, set the 'trade_type' to 'sell'. In this case, also set the 'costs_proceeds' column to the total number in the `accumulated_shares` variable multiplied by the price of the last day.



```r
amd_df$trade_type <- NA #initialise columns in the data frame.
amd_df$costs_proceeds <- NA 
amd_df$accumulated_shares <- 0 #set initial value for different parameters.
amd_df$profit<-NA
previous_price <- 0
share_size <- 100
current_price <- amd_df$close[1]
options(scipen = 1000, digits = 6) #limit the digits shown to 6 when dealing with large 
decimals.
 
 if(previous_price==0){ #Set initial values for day 1.
 amd_df$trade_type[1] <- "Buy"
 amd_df$costs_proceeds[1]<- amd_df$close[1]*share_size
 amd_df$accumulated_shares[1] <- share_size
 amd_df$profit[1]<--amd_df$costs_proceeds[1]
 }
 
 
for (i in 2:nrow(amd_df)) {
 current_price <- amd_df$close[i]
 #reset current price with the new closing price of the day i from 2.
 if (current_price < previous_price) {
 amd_df$trade_type[i] <- "Buy" 
 amd_df$costs_proceeds[i]<- current_price*share_size
 amd_df$accumulated_shares[i] <- amd_df$accumulated_shares[i-1] + share_size
 amd_df$profit[i]<-amd_df$profit[i-1]-amd_df$costs_proceeds[i]
 }
 #If previous price is greater than current price then we buy 100 shares at the current
price, add 100 to accumulated shares and carry on profit count from previous row as no
thing is sold.
 else if(current_price>=previous_price){
 amd_df$accumulated_shares[i]<-amd_df$accumulated_shares[i-1]
 amd_df$profit[i]<-amd_df$profit[i-1]
 }
 #If share price for AMD goes up on a particular day, we hold onto the shares, copies t
he accumulated shares and profit from the previous row.
 if(i==nrow(amd_df)){
 amd_df$trade_type[i] <- "Sell"
 amd_df$profit[i]<-amd_df$profit[i-1]+amd_df$accumulated_shares[i]*current_price
 } #If at the end of the data frame (last day) then sell all shares at current pric
e and calculate total profit from the sell transaction.
 previous_price<-current_price #Replace previous price with current price.
}

```


### Step 3: Customize Trading Period
- Define a trading period you wanted in the past five years 
```r
start_date <- as.Date("2019-05-20")
end_date <- as.Date("2020-05-20") #Set define starting date and ending date.
amd_df <- amd_df[amd_df$date >= start_date & amd_df$date <= end_date, ]
#replace the dataframe with values only between the start and end dates.
for (i in 1:nrow(amd_df)) {
 current_price <- amd_df$close[i]
 if (i == nrow(amd_df)) {
 amd_df$trade_type[i] <- 'Sell'
 amd_df$profit[i]<-amd_df$profit[i-1]+amd_df$accumulated_shares[i]*current_price
 } #Generates new sell day for the new end date.
}
```


### Step 4: Run Your Algorithm and Analyze Results
After running your algorithm, check if the trades were executed as expected. Calculate the total profit or loss and ROI from the trades.

- Total Profit/Loss Calculation: Calculate the total profit or loss from your trades. This should be the sum of all entries in the 'costs_proceeds' column of your dataframe. This column records the financial impact of each trade, reflecting money spent on buys as negative values and money gained from sells as positive values.
- Invested Capital: Calculate the total capital invested. This is equal to the sum of the 'costs_proceeds' values for all 'buy' transactions. Since these entries are negative (representing money spent), you should take the negative sum of these values to reflect the total amount invested.
- ROI Formula: $$\text{ROI} = \left( \frac{\text{Total Profit or Loss}}{\text{Total Capital Invested}} \right) \times 100$$

```r
Total_Profit<-tail(amd_df$profit,n=1) #extracts the last value from the profit 
column, which is our total profit
Total_Capital_Invested<-sum(amd_df$costs_proceeds, na.rm = TRUE)
ROI<-(Total_Profit/Total_Capital_Invested)*100 #finds capital invested by summing up all costs, and calculating ROI based on the formula
```

### Step 5: Profit-Taking Strategy or Stop-Loss Mechanisum (Choose 1)
- Option 1: Implement a profit-taking strategy that you sell half of your holdings if the price has increased by a certain percentage (e.g., 20%) from the average purchase price.
- Option 2: Implement a stop-loss mechanism in the trading strategy that you sell half of your holdings if the stock falls by a certain percentage (e.g., 20%) from the average purchase price. You don't need to buy 100 stocks on the days that the stop-loss mechanism is triggered.


```r
amd_df$trade_type <- NA
amd_df$costs_proceeds <- NA 
amd_df$accumulated_shares <- 0 
previous_price <- 0
share_size <- 100
amd_df$total_cost<- NA
amd_df$average_purchase_price<-NA
amd_df$profit_threshold<- NA
amd_df$return_on_shares_sold<-NA
amd_df$total_profit<-NA #initiates 5 new variables to determine when to 
sell to take profit
options(scipen = 1000, digits = 6)
profit_margin<-1.2 #customisable profit margin
 current_price <- amd_df$close[1]
 if(previous_price==0){
 amd_df$trade_type[1] <- "Buy"
 amd_df$costs_proceeds[1]<- amd_df$close[1]*share_size
 amd_df$accumulated_shares[1] <- share_size
 amd_df$total_cost[1]<-amd_df$costs_proceeds[1]
 amd_df$average_purchase_price[1]<-amd_df$close[1]
 amd_df$profit_threshold[1]<-profit_margin*amd_df$average_purchase_price[1]
 amd_df$total_profit[1]<--amd_df$total_cost[1]
 amd_df$return_on_shares_sold[1]<-0
 }
 #initialising all values for day 1
 
for (i in 2:nrow(amd_df)) {
 current_price <- amd_df$close[i]
 
 if (current_price < previous_price) {
 amd_df$trade_type[i] <- "Buy"
 amd_df$costs_proceeds[i]<- current_price*share_size
 amd_df$accumulated_shares[i] <- amd_df$accumulated_shares[i-1] + share_size
 amd_df$total_cost[i]<-amd_df$total_cost[i-1]+amd_df$costs_proceeds[i]
 #when buys something on any particular day, the total cost gets accumulated
 amd_df$total_profit[i]<-amd_df$total_profit[i-1]-amd_df$costs_proceeds[i]
 amd_df$return_on_shares_sold[i]<-0
 }#for the particular day which there was a purchase, total profit from before redu
ces by the amount spend, and since no shares are sold return on shares sold would be
0
 
 else if(current_price>=previous_price){#If there was a price increase
 amd_df$accumulated_shares[i]<-amd_df$accumulated_shares[i-1]#dont buy nor sell
 amd_df$total_cost[i]<-amd_df$total_cost[i-1] 
 amd_df$total_profit[i]<-amd_df$total_profit[i-1]
 #accumulate total cost and profit
 amd_df$return_on_shares_sold[i]<-0 #no shares were sold hence 0
 }
 
 if(i==nrow(amd_df)){
 amd_df$trade_type[i] <- "Sell"
 amd_df$return_on_shares_sold[i]<-amd_df$accumulated_shares[i-1]*current_price
 amd_df$total_profit[i]<-amd_df$total_profit[i-1]+amd_df$accumulated_shares[i-1]*current_p
rice
 #sells at the end of the trading period and calculates return on transaction and
profit
 }
 amd_df$average_purchase_price[i]<-amd_df$total_cost[i]/amd_df$accumulated_shares[i]#define
average purchase price as the total cost divided by total
shares
 amd_df$profit_threshold<-amd_df$average_purchase_price*profit_margin
 #define profit threshold as a percentage increase from average purchase price
 if(amd_df$close[i]>amd_df$profit_threshold[i]){
 #return true and processes the if function only if closing price for the day is great
er than profit threshold for the day
 
 amd_df$trade_type[i]<-'Sell Half'
 amd_df$accumulated_shares[i]<-amd_df$accumulated_shares[i-1]/2
 amd_df$total_cost[i]<-amd_df$total_cost[i-1]/2
 #sells half the shares, hence halfing the accumulated shares and total cost for the d
ay
 amd_df$total_profit[i]<-amd_df$total_profit[i-1]+amd_df$accumulated_shares[i-1]/2*current
_price #calculats new total profit by adding value of shares sold to pr
evious days total profit
 amd_df$return_on_shares_sold[i]<-amd_df$accumulated_shares[i-1]/2*current_price
 } #calculate returns on the selling transaction
 previous_price<-current_price #replace previous price with current price to continue the fo
r loop
} 
 Total_Profit<-sum(amd_df$return_on_shares_sold,na.rm = TRUE)-sum(amd_df$costs_proceeds, na.
rm = TRUE)
 Total_Capital_Invested<-sum(amd_df$costs_proceeds, na.rm = TRUE)
 ROI<-(Total_Profit/Total_Capital_Invested)*100
 #when at the end of the data frame after all selling is completed new total profit
and ROI is calculated.

```


### Step 6: Summarize Your Findings
- Did your P/L and ROI improve over your chosen period?
- Relate your results to a relevant market event and explain why these outcomes may have occurred.


```r
It was observed that:
Strategy one returned a Profit of $196930 with a ROI of 45.9%
Strategy two returned a Profit of $86782 with a ROI of 20.2%
We can clearly see that by implementing a profit taking strategy, we decreased our profit by more than half.
This is because of the continuous positive news about AMDs innovation.
This can be summarised into two major reasons:
The release of Radeon RX 5000 series GPUs and zen 2 series CPUs on July 7th 2019, where AMD was
able to take back parts of the market from its competitors Nvidia and Intel respectively through their
products general lower price for equivalent performance compared to the counterpart products.
The beginning of lock downs across various countries around the world due to the Covid-19 Pandemic,
which caused there to be an increase in demand for digital entertainment, hence driving the semiconductor market up.
Since both reasons caused a consistent rise in AMD shares, it increased investors belief in the potential of
chip-making industry and AMD, therefore by selling stocks for a cheaper price throughout the year to reduce
risk, we are effectively selling less shares at the end of the trading period where the stock price is at peak, and
hence making $110148 less, which therefore providing a lower ROI.
```

Sample Discussion: On Wednesday, December 6, 2023, AMD CEO Lisa Su discussed a new graphics processor designed for AI servers, with Microsoft and Meta as committed users. The rise in AMD shares on the following Thursday suggests that investors believe in the chipmaker's upward potential and market expectations; My first strategy earned X dollars more than second strategy on this day, therefore providing a better ROI.





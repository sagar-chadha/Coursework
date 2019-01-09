## Finding the best investment strategy using Bootstrapping.
![Investment](https://github.com/sagar-chadha/Coursework/blob/master/Repository_files/investment-portfolio.jpg) <br>

### The Task
In this problem, I look at several years of past data for these ETFs - <br>
* US domestic equities (SPY: the S&P 500 stock index)
* US Treasury bonds (TLT)
* Investment-grade corporate bonds (LQD)
* Emerging-market equities (EEM)
* Real estate (VNQ) <br>

and use bootstrapping to recommend the best investment strategies out of three possibilities - <br>

* Even split amongst all
* Safe investment
* Risky Investment

### Concepts Used - 
* `quantmod` library to download several years of closing stock prices.
* **Bootstrapping** to simulate daily movements of the ETFs under study and get a sense of future returns.
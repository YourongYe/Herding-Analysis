# Python-Herding-Analysis

In this project, I use Python to analyse the "Herding Behavior" for institutional analysts in Chinese stock market. I use Pandas, Numpy, Matplotlib, Seaborn and etc.

The sample is 3999 stocks in ShangHai and Shenzhen stock exchange. And the sample period is 2005.1.31-2017.12.29. 

# Compare estimated EPS and actual EPS 

By calculating the monthly mean estimated EPS and actual EPS for all stocks through the timeline, I find constant positive bias in analysts' earnings estimation.

    EPS_actual = eps_ttm.mean()
    EPS_actual = pd.DataFrame(EPS_actual)
    EPS_exp = west_eps_FTM.mean()
    EPS_exp = pd.DataFrame(EPS_exp)
    table = pd.concat([EPS_actual,EPS_exp],axis=1)
    table.plot()
    plt.show()
 
![image_eps](https://github.com/YourongYe/Python-Herding-Analysis/blob/master/EPS.png)

# Calculate SUE and herding index

Based on the estimated EPS (earnings per share), the estimate standard deviation and actual EPS, I construct the SUE(surprise unexpected earnings) and HI (herding index) using 
matrix operations in Python.

    SUE = abs(west_eps_2017-eps_ttm)/west_stdeps_2017
    SUE = SUE.set_index('StockNo.')
    SUE_stack = SUE.stack()
    Outlier_value = SUE_stack.quantile(0.9995)
    SUE = SUE[SUE<Outlier_value]


    def calculate_HI(threshold,df,HI_series):
    
        for index,row in df.iterrows():
            total_num=0
            threshold_num=0

            for x in row:

                if x > threshold:
                    threshold_num += 1
                if x > 0:
                    total_num += 1

            if total_num == 0:
                    HI_series[index] = 0

            if total_num != 0:
                    HI_series[index] = threshold_num/total_num

    HI_1 = {}
    calculate_HI(1,SUE,HI_1)
    HI_1 = Series(HI_1)

    HI_2 = {}
    calculate_HI(2,SUE,HI_2)
    HI_2 = Series(HI_2)
  
    sns.distplot(HI_2, rug=True, hist=False)
    plt.show()

The following picture shows the HI(X=2) distribution for all stocks during 2015-2017 (some data only available in this period)
![HI_2_distribution](https://github.com/YourongYe/Python-Herding-Analysis/blob/master/HI_2_distribution.png)
    
    HI_5 = {}
    calculate_HI(5,SUE,HI_5)
    HI_5 = Series(HI_5)
    
    sns.distplot(HI_5, rug=True, hist=False)
    plt.show()
    
The following picture shows the HI(X=5) distribution for all stocks during 2015-2017 
![HI_5_distribution](https://github.com/YourongYe/Python-Herding-Analysis/blob/master/HI_5_distribution.png)

# Draw plot picture for different stock portfolios

I distribute all the stocks into different groups based on their HI values and then build portfolios and calculate their average monthly returns.

    HI_2_stock = pd.concat([monthly_return,HI_2],axis=1)
    
    def portfolio_build(downside,upside,df,column_name):
        return df.loc[(df[column_name]>downside) & (df[column_name]<=upside)]

    p1 = portfolio_build(0,0.33,HI_2_stock,0)
    p2 = portfolio_build(0.33,0.66,HI_2_stock,0)
    p3 = portfolio_build(0.66,1,HI_2_stock,0)
    
    
    draw_plot= pd.concat([p1.mean(),p2.mean(),p3.mean(),hs300index_return],axis=1)
    draw_plot.cumsum(0).plot()
    plt.show()
    
![image_HI1](https://github.com/YourongYe/Python-Herding-Analysis/blob/master/HI1.png)
    
When I set the threshold to 5,and reset the interval for each portfolio based on the distribution, I get the following:

![image_HI5](https://github.com/YourongYe/Python-Herding-Analysis/blob/master/HI5.png)

# Herding index and abnormal return analysis



Refereces: 
[1] Olsen, Robert A. "Implications of herding behavior for earnings estimation, risk assessment, and stock returns." Financial Analysts Journal 52.4 (1996): 37-41.
[2] Nofsinger, John R., and Richard W. Sias. "Herding and feedback trading by institutional and individual investors." The Journal of finance 54.6 (1999): 2263-2295.
[3] Dasgupta, Amil, Andrea Prat, and Michela Verardo. "Institutional Trade Persistence and Long‐Term Equity Returns." The Journal of finance 66.2 (2011): 635-653.
[4] Wermers, Russ. "Mutual fund herding and the impact on stock prices." the Journal of Finance 54.2 (1999): 581-622.
[5] Chan, Louis KC, Narasimhan Jegadeesh, and Josef Lakonishok. "Momentum strategies." The Journal of Finance 51.5 (1996): 1681-1713.

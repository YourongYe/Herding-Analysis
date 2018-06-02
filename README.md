# Python-Herding-Analysis
In this project, I use Python to analyse the "Herding Behavior" for institutional analysts in Chinese stock market. I use Pandas, Numpy, Matplotlib, Seaborn and etc.

The sample is 3999 stocks in ShangHai and Shenzhen stock exchange. Based on the estimated EPS (earnings per share), the estimate standard deviation and actual EPS, I construct the SUE(surprise unexpected earnings) and HI (herding index) using 
matrix operations in Python.

# compare EPS_actual and EPS_exp with timeline and industry
    EPS_actual = eps_ttm.mean(1)
    EPS_actual = pd.DataFrame(EPS_actual)
    EPS_actual=EPS_actual.rename(columns={0:'EPS_Actual'})
    EPS_exp = west_eps_FTM.mean(1)
    EPS_exp = pd.DataFrame(EPS_exp)
    EPS_exp=EPS_exp.rename(columns={0:'EPS_exp'})
    table = pd.concat([EPS_actual,EPS_exp,Industry_dic],axis=1)
 
![image_eps](https://github.com/YourongYe/Python-Herding-Analysis/blob/master/EPS.png)

# calculate SUE and herding index
   


# draw plot picture for different stock portfolios




Refereces: 
[1] Olsen, Robert A. "Implications of herding behavior for earnings estimation, risk assessment, and stock returns." Financial Analysts Journal 52.4 (1996): 37-41.
[2] Nofsinger, John R., and Richard W. Sias. "Herding and feedback trading by institutional and individual investors." The Journal of finance 54.6 (1999): 2263-2295.
[3] Dasgupta, Amil, Andrea Prat, and Michela Verardo. "Institutional Trade Persistence and Long‐Term Equity Returns." The Journal of finance 66.2 (2011): 635-653.
[4] Wermers, Russ. "Mutual fund herding and the impact on stock prices." the Journal of Finance 54.2 (1999): 581-622.
[5] Chan, Louis KC, Narasimhan Jegadeesh, and Josef Lakonishok. "Momentum strategies." The Journal of Finance 51.5 (1996): 1681-1713.

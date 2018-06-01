# Python-Herding-Analysis
In this project, I use Python to analyse the "Herding Behavior" for institutional analysts. I use Pandas (including Dataframe and Series), Numpy, Matplotlib, Seaborn and etc.

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

# calculate SUE and herding index
SUE = abs(west_eps_2017-eps_ttm)/west_stdeps_2017
SUE_mean = SUE.mean(1)

SUE = SUE[SUE<1000]

SUE = SUE.set_index('StockNo.')
SUE = SUE.T
SUEcolumns_list = SUE.columns
SUEcolumns_number = SUE.columns.size
Herding_index_10 = {}
Herding_index_5 = {}
Herding_index_3 = {}
Herding_index_2 = {}
Herding_index_1 = {}

for x in range(0,SUEcolumns_number):
    columns_name = SUEcolumns_list[x]
    
    total_num = 0
        herding_num_1 = 0
        herding_num_2 = 0
        herding_num_3 = 0
        herding_num_5 = 0
        herding_num_10 = 0
        
        for c in SUE[columns_name]:
            if c > 0:
                total_num += 1
                if c >= 1:
                    herding_num_1 += 1
                if c >= 2:
                    herding_num_2 += 1
                if c >= 3:
                    herding_num_3 += 1
                if c >= 5:
                    herding_num_5 += 1
                if c >= 10:
                    herding_num_10 += 1
    
        if total_num == 0:
            Herding_index_1[columns_name] = np.nan
            Herding_index_2[columns_name] = np.nan
            Herding_index_3[columns_name] = np.nan
            Herding_index_5[columns_name] = np.nan
            Herding_index_10[columns_name] = np.nan
                if total_num > 1:
                herding_index_individual_1 = herding_num_1/total_num
                herding_index_individual_2 = herding_num_2/total_num
                herding_index_individual_3 = herding_num_3/total_num
                herding_index_individual_5 = herding_num_5/total_num
                herding_index_individual_10 = herding_num_10/total_num
                
                Herding_index_1[columns_name] = herding_index_individual_1
                Herding_index_2[columns_name] = herding_index_individual_2
                Herding_index_3[columns_name] = herding_index_individual_3
                Herding_index_5[columns_name] = herding_index_individual_5
                Herding_index_10[columns_name] = herding_index_individual_10

Herding_index_1 = pd.Series(Herding_index_1)
Herding_index_2 = pd.Series(Herding_index_2)
Herding_index_3 = pd.Series(Herding_index_3)
Herding_index_5 = pd.Series(Herding_index_5)
Herding_index_10 = pd.Series(Herding_index_10)


# draw plot picture for different stock portfolios
monthly_return=monthly_return.set_index('StockNo.')

HI_1_stock = pd.concat([monthly_return,Herding_index_1],axis=1)
HI_2_stock = pd.concat([monthly_return,Herding_index_2],axis=1)
HI_3_stock = pd.concat([monthly_return,Herding_index_3],axis=1)
HI_5_stock = pd.concat([monthly_return,Herding_index_5],axis=1)
HI_10_stock = pd.concat([monthly_return,Herding_index_10],axis=1)

def portfolio_build(a,b,df,column_name):
    return df.loc[(df[column_name]>a) & (df[column_name]<=b)]

p1 = portfolio_build(0,0.28,HI_5_stock,0)
p2 = portfolio_build(0.28,0.45,HI_5_stock,0)
p3 = portfolio_build(0.45,1,HI_5_stock,0)

p1_average = p1.mean()
p2_average = p2.mean()
p3_average = p3.mean()

del p1_average[0]
del p2_average[0]
del p3_average[0]

hs300index_return = hs300index_return.T
hs300index_return = hs300index_return.rename(columns={0: "HS300"})
draw_plot= pd.concat([p3_average,p2_average,p1_average,hs300index_return],axis=1)
draw_plot = draw_plot.rename(columns={0:'0.45<HI<1',1:'0.28<HI<0.45',2: "0<HI<0.28"})
draw_plot.index = hs300index_return.index

draw_plot_partly = draw_plot.iloc[-36:,:]
draw_plot_partly.cumsum(0).plot()
plt.show()




Refereces: 
[1] Olsen, Robert A. "Implications of herding behavior for earnings estimation, risk assessment, and stock returns." Financial Analysts Journal 52.4 (1996): 37-41.
[2] Nofsinger, John R., and Richard W. Sias. "Herding and feedback trading by institutional and individual investors." The Journal of finance 54.6 (1999): 2263-2295.
[3] Dasgupta, Amil, Andrea Prat, and Michela Verardo. "Institutional Trade Persistence and Long‐Term Equity Returns." The Journal of finance 66.2 (2011): 635-653.
[4] Wermers, Russ. "Mutual fund herding and the impact on stock prices." the Journal of Finance 54.2 (1999): 581-622.
[5] Chan, Louis KC, Narasimhan Jegadeesh, and Josef Lakonishok. "Momentum strategies." The Journal of Finance 51.5 (1996): 1681-1713.

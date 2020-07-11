import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from pandas_datareader import data
from datetime import date
from matplotlib import gridspec

# Define MACD function
def buy_sell(signal):
    Buy = []
    Sell = []
    flag = -1

    for i in range(0, len(signal)):
        if signal['MACD'][i] > signal['Signal Line'][i]:
            Sell.append(np.nan)
            if flag != 1:
                Buy.append(signal['Close'][i])
                flag = 1
            else:
                Buy.append(np.nan)
        elif signal['MACD'][i] < signal['Signal Line'][i]:
            Buy.append(np.nan)
            if flag != 0:
                Sell.append(signal['Close'][i])
                flag = 0
            else:
                Sell.append(np.nan)
        else: 
            Buy.append(np.nan)
            Sell.append(np.nan)

    return (Buy, Sell)


## Define recommendation function
def Advice(signal):
    Advice = [] 

    for i in range(1, len(signal)):
    # If the MACD crosses the signal line upward
        if signal['MACD'][i] > signal['Signal Line'][i] and signal['MACD'][i - 1] <= signal['Signal Line'][i - 1]:
            Advice.append("Buy")
        elif signal['MACD'][i] < signal['Signal Line'][i] and signal['MACD'][i - 1] >= signal['Signal Line'][i - 1]:
            Advice.append("Sell")
        else:
            Advice.append("Hold")
        
    return(pd.DataFrame(Advice, columns =['Advice']))


if __name__ == '__main__':
    plt.style.use('seaborn')  
    ticker = input('Introduzca su ticker: ')
    df = data.DataReader(ticker, 'yahoo', '2020-01-01')
    
    
    #MACD
    ShortEMA = df.Close.ewm(span=12, adjust=False).mean()
    LongEMA = df.Close.ewm(span=23, adjust=False).mean()
    MACD = ShortEMA - LongEMA
    Signal = MACD.ewm(span=9, adjust=False).mean()

    # Add to dataframe
    df['MACD'] = MACD
    df['Signal Line'] = Signal

    a = buy_sell(df)
    df['Buy_Price'] = a[0]
    df['Sell_Price'] = a[1]

    ## recommendation for today
    lst = Advice(df)
    dc = pd.concat([df[['Close']].iloc[1:].reset_index(drop=False), lst.reset_index(drop=True)], axis=1)
    
    titul = date.today().strftime("%d-%m-%Y")
    
    n = len(lst) - 1
    opción = lst.iloc[n: ].to_string(header=False, index=False)

    titulo = 'Hoy  ' + titul + '  '  + opción + '  ' + ticker
    
    #print(titulo)
    #Plot Price, Signals, and MACD
    fig1 = plt.figure(constrained_layout=True)
    gs = fig1.add_gridspec(3, 1)
    plt.suptitle(titulo, fontsize=14, fontweight="bold", x=0.12, horizontalalignment='left')
    plt.subplot(gs[:2, :])
    plt.scatter(df.index, df['Buy_Price'], color='green', label='Buy', marker='^', alpha=1)
    plt.scatter(df.index, df['Sell_Price'], color='red', label='Sell', marker='v', alpha=1)
    plt.plot(df['Close'], color='blue', alpha=0.75)
    plt.title('Performance Precio de Cierre', x=0.001, horizontalalignment='left')
    plt.xlabel(' ')
    plt.ylabel('Precio de Cierre MXN')
    plt.legend(loc = 'upper left')
    plt.subplot(gs[2, :])
    plt.plot(df.index, MACD, label='MACD', color='red')
    plt.plot(df.index, Signal, label='Signal', color='blue')
    plt.ylabel('Señales')
    plt.xticks(rotation=90)
    plt.legend(loc='upper left')
    plt.subplots_adjust(left=None, bottom=0.15, right=0.97, top=0.89, wspace=None, hspace=0.05)
    plt.show()

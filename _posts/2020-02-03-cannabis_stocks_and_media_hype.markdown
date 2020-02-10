---
layout: post
title:      "Cannabis Stocks and Media Hype"
date:       2020-02-03 13:12:58 -0500
permalink:  cannabis_stocks_and_media_hype
---


The first rule of investing advice I remember receiving was from Nicole Lapin’s book “Rich Bitch”. In it she said, 
> “Never take a stock tip…Why? Because your timing may be different from when the “tipper” gets into and out of the stock. Trust your on research and judgement.”
 
But unless you’re an experienced day-trader, picking the right investment can often feel overwhelming and confusing. And the fear of missing out on an investment you truly believe in can be too powerful a temptation. It doesn’t help that smaller public companies are often unknown to laymen until they are mentioned on Bloomberg, CNBC, or another economic media company. 

For example, if you are a believer in the legalization of marijuana and would like to get an early in on cannabis stocks, how do you choose a company in which to invest? Unless the company has cannabis in its name, it won’t show up on a search of your investment platform’s listings. Instead, these stock are listed under more general categories. On Fidelity’s site, Tilray, Inc. (TLRY) is listed as a Common Stock in the Healthcare category, under the subcategory of Pharmaceuticals. 

As a result, we must to some extent rely upon the media. But what happens when media hype over-inflates expectations? 

Cannabis stocks have for a few years now been hyped up as the new "tech stocks", promising to be to current investors what Apple and Microsoft were decades ago. With marijuana fully legalized in Canada, and fully legal status or reduced decriminalization in thirty-seven of the United States, many investors jumped onboard in 2018 when major cannabis companies, some believing in the product, others believing in the profit.

As the demand for the stocks increased, certain stocks saw near-exponential growth, to the wonder and admiration of investors and analysts alike. But how much of that growth was a natural reflection on the product and how much was artificially inflated hype?

## The Analysis

To determine what effect, if any, media hype had on cannabis stocks, I decided to analyze the stock prices of four of the most common cannabis stocks: Tilray, Inc. (TLRY), Aurora Cannabis, Inc. (ACB), Canopy Growth Corporation (CGC), and Cronos Group, Inc. (CRON).

First, I imported the historical stock data for each from Nasdaq and preprocessed it so it would be ready for analysis. Each stock was then tested for stationarity, and proved to be non-stationary. 

```def test_stationarity(timeseries, window):
    
    #Defining rolling statistics
    rolmean = timeseries.rolling(window=window).mean()
    rolstd = timeseries.rolling(window=window).std()

    #Plot rolling statistics:
    fig = plt.figure(figsize=(12, 8))
    orig = plt.plot(timeseries.iloc[window:], color='#87CEEB',label='Original')
    mean = plt.plot(rolmean, color='#FFC0CB', label='Rolling Mean')
    std = plt.plot(rolstd, color='#008000', label = 'Rolling Std')
    plt.legend(loc='upper left')
    plt.title('Rolling Mean & Standard Deviation')
    plt.show()
		
	test_stationarity(tlry_close, 3)```
	
![Imgur](https://i.imgur.com/cHZBLAy.png)


Next, each stock was seasonally decomposed into trend, seasonality, and residuals and tested again for stationarity. TLRY showed far less stationarity than the others and was the only stock not to show an overall growth trend. ACB proved to be the most stationary and had the strongest and steadiest growth trend. 

```
decomposition = seasonal_decompose((np.log(tlry_open)), freq=52)

trend = decomposition.trend
seasonal = decomposition.seasonal
residual = decomposition.resid

# Plot statistics
plt.figure(figsize=(12,8))
plt.subplot(411)
plt.plot(np.log(tlry_open), label='Original', color="blue")
plt.legend(loc='best')
plt.subplot(412)
plt.plot(trend, label='Trend', color="blue")
plt.legend(loc='best')
plt.subplot(413)
plt.plot(seasonal,label='Seasonality', color="blue")
plt.legend(loc='best')
plt.subplot(414)
plt.plot(residual, label='Residuals', color="blue")
plt.legend(loc='best')
plt.tight_layout()

ts_log_decompose = residual
ts_log_decompose.dropna(inplace=True)

test_stationarity(ts_log_decompose, 10)
```

![Imgur](https://i.imgur.com/J4k0UwX.png)
![Imgur](https://i.imgur.com/eu5pSEQ.png)

```
decomposition = seasonal_decompose((np.log(acb_open)), freq=52)

trend = decomposition.trend
seasonal = decomposition.seasonal
residual = decomposition.resid

# Plot statistics
plt.figure(figsize=(12,8))
plt.subplot(411)
plt.plot(np.log(acb_open), label='Original', color="blue")
plt.legend(loc='best')
plt.subplot(412)
plt.plot(trend, label='Trend', color="blue")
plt.legend(loc='best')
plt.subplot(413)
plt.plot(seasonal,label='Seasonality', color="blue")
plt.legend(loc='best')
plt.subplot(414)
plt.plot(residual, label='Residuals', color="blue")
plt.legend(loc='best')
plt.tight_layout()

ts_log_decompose = residual
ts_log_decompose.dropna(inplace=True)

test_stationarity(ts_log_decompose, 10)
```

![Imgur](https://i.imgur.com/5ctotzh.png)
![Imgur](https://i.imgur.com/ZRFFGXl.png)

Because, at the time of testing CRON had been on the market for less than a month, it proved insufficient data to be included in further testing. Autocorrelation and partial autocorrelation tests were then performed for the remaining three stocks. 

```
title = 'Autocorrelation: TLRY Open'
lags = 50
plot_acf((np.log(tlry_open)),title=title,lags=lags);

title = 'Partial Autocorrelation: TLRY Open'
lags = 50
plot_pacf((np.log(tlry_open)),title=title,lags=lags);
```

![Imgur](https://i.imgur.com/NkJY4oA.png)
![Imgur](https://i.imgur.com/ns0aYyz.png)

```
title = 'Autocorrelation: ACB Open'
lags = 50
plot_acf((np.log(acb_open)),title=title,lags=lags);

title = 'Partial Autocorrelation: ACB Open'
lags = 50
plot_pacf((np.log(acb_open)),title=title,lags=lags);
```

!

While all three stocks shows very strong positive autocorrelation, indicating that previous stock prices are good indicators of current/future prices, TLRY's drop in correlation and eventual leveling implies that initial prices may have been too high, and that the stock’s growth is losing (or has already lost) momentum.

The next step was to find the parameters for our ARIMA model, which was then used to determine how past prices affected current prices and from which we can extrapolate how much growth is natural versus artificial.

While Aurora Cannabis (ACB) and Canopy Group Corporation(CGC) have good z-scores, Tilray, Inc. (TLRY) does not. This is consistent with the other irregularities we've seen with TLRY in this project. This hints at the possibility of TLRY's price being more affected/manipulated by media hype. All three shows spikes in the Standard Residual Chart at similar points, but Canopy Group (CGC) shows a lot of spikes, indicating that there are external factors at play.

## Applying Sentiment Analysis

The easiest and most convenient way to scrape news articles is to use the News API. However, I was interested in a specific date range: the first three months of TLRY’s stock opening, plus the few weeks before. The free version of the News API only provides article-scraping for a month from the present date, therefore I had to use request to scrape each article individually.

```
r_tlry1 = requests.get('https://www.forbes.com/sites/greatspeculations/2018/07/19/ipo-coverage-tilray-inc-tlry/#4c5031bc1554')
content = r_tlry1.text
soup = BeautifulSoup(content, "html.parser")
tlry_1 = []
for i in range(0, 20):
    paragraphs1 = soup.find_all('p')[i].text
    tlry_1.append(paragraphs1)
print(tlry_1)
```

I then defined a function that used NLTK’s Opinion Lexicon to determine the sentiment of each article based on the adjectives in that article. 

```
all_words = nltk.FreqDist(all_words)
pos_words = []
neg_words = []
data = []

def find_features(document):
    words = word_tokenize(str(document))
    features = {}
    pos = 0
    neg = 0
    for w in document:
        features[w] = (w in words)
        if w in opinion_lexicon.negative():
            neg_words.append(w)
            neg += 1
        if w in opinion_lexicon.positive():
            pos_words.append(w)
            pos += 1
        else:
            continue
    if pos > 0:
        pct_pos = ((pos) / (pos+neg))*100
        pct_neg = ((neg) / (pos+neg))*100
        data.append(pct_pos)
    else:
        pct_pos = 0
        pct_neg = 0
        data.append(pct_pos)
    print('Positive Words {0} : Negative Words {1}, Percent Positive {2} : Percent Negative {3}'.format(pos, neg, pct_pos, pct_neg))    
    return features
```

In order to visualize the effect of the articles’ sentiment, the bar for each article was colored according to its positivity with articles 70% or more positive colored green, articles less than 30% colored red, and articles with positivity in between colored light green or pale red.

```
import matplotlib.pyplot as plt
import seaborn as sns
%matplotlib inline
plt.style.use('seaborn')

decomposition = seasonal_decompose((np.log(tlry_open)), freq=52)

trend = decomposition.trend
cutoff = '2018-11-29'

plt.figure(figsize=(12,8))
plt.subplot(411)
plt.plot(trend[trend.index <= cutoff], label='Trend', color="blue")
plt.legend(loc='best')

plt.gcf().set_size_inches(20, 10)
mask1 = ((tlry_df['Positivity'] > 16) & (tlry_df['Positivity'] < 35))
mask2 = ((tlry_df['Positivity'] > 35) & (tlry_df['Positivity'] < 50))
mask3 = ((tlry_df['Positivity'] == 50) & (tlry_df['Positivity'] == 0))
mask4 = ((tlry_df['Positivity'] > 50) & (tlry_df['Positivity'] < 70))
mask5 = (tlry_df['Positivity'] > 70)

colors = np.array(['#048C1B']*len(tlry_df))
colors[mask1.values] = 'r'
colors[mask2.values] = '#F2A59E'
colors[mask3.values] = '#C3CFCA'
colors[mask4.values] = '#8FF561'
colors[mask5.values] = '#048C1B'

plt.subplot(412)
plt.bar(tlry_df.index, tlry_df['Positivity'], color=colors)

plt.gcf().set_size_inches(20, 15)
plt.show()
```

![Imgur](https://i.imgur.com/vdocQzw.png)

## Results
There was little to no negative sentiment at TLRY’s launch. It was overwhelmingly positive. This rose-colored optimism seems to have artificially inflated TLRY’s growth. Whether it was naive over-excitement for a developing industry or intentional hype, we must remember to be cautious about animal spirit-driven growth that does not reflect the true value of a stock. 

As analysts became wary of TLRY’s momentum, the stock’s growth trend slowed and began to fall. 

## Future Research

This proof-of-concept relied upon adjectives for sentiment. Future research will include adverbs and verbs. (Verbs such as ‘plummet’ are negative in sentiment, while verbs such as ‘soar’ are positive in sentiment). This should increase the accuracy of such classification. 

The research will also be extended throughout the life of the stock to determine whether the influence of media sentiment decreases as the stock ages and launch hype fades. It will also extend to other cannabis stocks, such as the much younger Cronos Group (CRON), which will determine whether the momentum is isolated to one stock or affects the entire industry. 




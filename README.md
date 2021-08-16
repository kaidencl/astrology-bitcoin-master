# Project 5 - Predicting Bitcoin
Lin Qi

### Problem Statement
---
Use daily astrological data to predict the price of Bitcoin.

### Table of Contents
---
- [Data Collection](#Data_Collection)
- [EDA](#EDA)
- [Modeling](#Modeling)
- [Conclusions and Recommendations](#Conclusion_and_Recommendations)
- [Soruces](#Sources)

### Data_Collection
---
I got the Bitcoin data from Yahoo Finance. (https://finance.yahoo.com/quote/BTC-USD/history/) <br/>
I used this Python Library: https://pypi.org/project/kerykeion/ to export: <br/>
	Signs of planets, <br/>
	Names of natal aspects, <br/>
	Mutual receptions, <br/>
	Transit aspects with the Bitcoin birth chart, <br/>
	The strength of the planet (by my own calculation). <br/>
For time and location I chose GMT and London, UK.

### EDA
---
I used data from October 2015 through July 25 at the 2021. Since the price of bitcoin barely changed until 2017, I think it’s a reasonable choice. <br/>
I used Lilly’s method for the most part to calculate the essential dignity and accidental dignity of the planet. (https://mithras93.tripod.com/lessons/lesson7/) There are a few points of contention: <br/>
1. Mercury in Virgo is both domicile and exalted. Should the score be double-counted? Instead of double counting, I used a + 5.
2. Does a retrograde planet require a -5 in its essential dignity? After checking out some other astrology apps, I don’t think so.
3. In the same way, a direct plant doesn’t require a +4 in its accidental dignity.
4. I didn’t count the scores that the houses bring to the planets. This is because: 1) the time of each day is the same, so rising signs are the same; 2) the same price corresponds to different time zones and locations in the world, so how do you explain that Mercury in one location is in the first house and Mercury in another location is in the twelfth house? Perhaps there is a better explanation for this. I look forward to a better answer.
5. I didn’t calculate the speed of the planet, oriental/occidental, because the library I’m using doesn’t have that information.
6. Beseiged between Saturn and Mars. I wanted to do this in code, but I failed. I think it should be important.
Other than that, I didn’t calculate the Part of Fortune because I think it has more to do with the hour than the date. <br/>
After all this was done, I added the two columns together to get: d_planet = e_planet + a_planet. <br/>
I got the bitcoin birth data from here: https://www.astro.com/astrology/iam_article180117_e.htm Although it has no place of birth, most people assume it was born in London. Because of the precise timing, I was able to calculate the essential dignity and the accidental dignity of each planet in strict accordance with the table above. The total scores are: <br/>
sun: -4, moon: 4, mercury: 9, venus: 11, mars: 11, jupiter: -10, saturn: 2, uranus: -3, neptune: 6, pluto: -4. <br/>
The last step is to multiply each aspect by the scores. For example, d_tjupiter_trine_saturn = tjupiter_trine_saturn * d_jupiter * 2, where t stands for transit, d_jupiter for the score of Jupiter and 2 for the score of Bitcoin's Saturn.

### Modeling
---
VAR models are used for multivariate time series. The structure is that each variable is a linear function of past lags of itself and past lags of the other variables. All we have to do is update the data every day and get a forecast for the next day or two. <br/>
I made a first-order difference of the features before we use them. <br/>
<img
     src = "d_jupiter.png" style = '' style="float: left>
Next step is to run lag_order = mod.select_order(). I got lag_order = 1 so I just needed to predict the next day.
I ran the Granger causality Wald-test to check the relationship between each feature and the closing price. This step filters out most of the features. (from 1022 to 34) Many of the d columns I constructed before proved useful, and some of the epochal aspects do have an impact on prices. <br/>
A list of final features: <br/>
<img
     src = "features.png" style = '' style="float: left>

#### Prediction:
The model predicted that the next day’s closing price would rise 604.55. The actual closing price rose 1987.35, making the trend judgment accurate.

### Conclusion_and_Recommendations
---
The biggest problem with the current model is that these filtered astrological aspects are so long term that they may not accurately predict daily prices. I’m using volume now as an aid, and I can add something else in the future.
The library I’m using now is unstable, error prone, and not fully functional. I’m thinking of using other, more specialized APIs.
But I do think that the current model can predict up and down.

### Sources:
---
* Bitcoin data: https://finance.yahoo.com/quote/BTC-USD/history/
* Kerykeion Library: https://pypi.org/project/kerykeion/
* Essential and accidental dignity: https://mithras93.tripod.com/lessons/lesson7/
* Bitcoin's birth chart: https://www.astro.com/astrology/iam_article180117_e.htm
* Average of daily motion of planets: https://tonylouis.wordpress.com/2018/08/26/average-daily-motion-of-planets-in-horary/

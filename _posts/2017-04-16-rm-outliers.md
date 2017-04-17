---
layout: post
title: Two Easy Methods to Remove Outliers
---

Outliers are the data obviously very wrong. For exmple, a human height of one mile, a village population of one billion, or a temperature value lower than 300 dgree C. These data do not provide any useful information, and usually prohibit the visualization of other more meaningful data.

For example, the [twocol.csv](https://raw.githubusercontent.com/leeguoo/leeguoo.github.io/master/images/2017-04-16-rm-outliers/twocol.csv) file contains two time series.(They were calcuated from the turnstile data available on the on the [MTA website](http://web.mta.info/developers/turnstile.html).) We can visualize them using [pandas](http://pandas.pydata.org/).

```python
import pandas as pd

df = pd.read_csv("twocol.csv",index_col='time')
df.plot()
```
![](https://github.com/leeguoo/leeguoo.github.io/blob/master/images/2017-04-16-rm-outliers/outlier_1.png?raw=true)

In the figure, we can't see anything but a few very sharp spikes. They are outliers. They have to be removed, and they can be removed *easily*.

# Remove the Data Far from the Mean

How do we know a human height of one mile is an outlier? Because we know human heights are in a range from tens of centimeters to less than three meters. The averaged heights are about 170 cm for males and 158 cm for females. If a number is too far away from these averaged values, say 5 meters, it is wrong, and it is safe to be removed.

Then, how far is too far? There is no unique answer. But usually, it is reasonable to discard the data three standard deviations away from the mean. Here is a function for this purpose.

```python
def RmOutliers(df):
    while True:
        nums = np.array(df.notnull().sum())
        df = df[np.abs(df-df.mean())<=(3*df.std())]
        if all(nums-np.array(df.notnull().sum())==0):
            break
    return df.interpolate()
``` 
The outliers are removed by this line:
```python
df = df[np.abs(df-df.mean())<=(3*df.std())]
```
I iterate it for many times until all the rest data are in the six-deviation range, and fill the missing points with linear interpolation (`df.interpolate()`). The results are here: 

![](https://github.com/leeguoo/leeguoo.github.io/blob/master/images/2017-04-16-rm-outliers/outlier_2.png?raw=true)

# Use Rolling Median

Another interesting thing about outliers is that they are only a few and not continuous. That means they are very different from their neighbors. So we can easily remove the outliers by replacing each data point with the median ofa local cluster containing the data point and its neighbors. Here is the code:

```python
df = pd.read_csv("twocol.csv")
df = df.rolling(3).median()
df.plot()
```

The number **3** means the local cluster contains the data point and its two nearest neighbors. The reuslts are here:

![](https://github.com/leeguoo/leeguoo.github.io/blob/master/images/2017-04-16-rm-outliers/outlier_1.png?raw=true)

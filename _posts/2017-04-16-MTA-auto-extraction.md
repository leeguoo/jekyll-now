---
layout: post
title: Automatically Extracting Data From MTA
---

The turnstile data of subway stations in New York City are available on the [MTA website](http://web.mta.info/developers/turnstile.html).

![](https://github.com/leeguoo/leeguoo.github.io/blob/master/images/2017-04-16-MTA-auto-extraction/Screen%20Shot%202017-04-16%20at%2011.45.46%20AM.png?raw=true)

By clicking one of these files, we can have a look at the contents.

![](https://github.com/leeguoo/leeguoo.github.io/blob/master/images/2017-04-16-MTA-auto-extraction/Screen%20Shot%202017-04-16%20at%201.40.43%20PM.png?raw=true)

Two things are interesting here. First, the data columns are separated by commas. It means the file is in csv format. The second thing is that the file name **turnstile_170408.txt** (can be found in the address field) contains a six-digit number indicating the date when the file was added. Since the data are updated weekly, the dates in the file names are seven days apart. We can take advantages of these two findings.

# Extract Data from One File

If we have [pandas](http://pandas.pydata.org/) installed, we don't have to manually download the file to our hard disks. Instead, we can directly read the data to a pandas dataframe.

```python
import pandas as pd
url = "http://web.mta.info/developers/data/nyct/turnstile/turnstile_170408.txt"
df = pd.read_csv(url)
``` 

Here,the **url** is the link to the file. It can be found in the address field of the broswer.  

# Extract Data from Multiple Files *Automatically*

If we want to extract the data corresponding to a large time window, for example a year (52 weeks), we don't have to input the links manually. We can generate them according to the relation between filenames.

```python
import time
from datetime import datetime

#set the start and end days of the time window
startday = "20170204"
endday = "20170408"

#obtain the timestamps
starttime = time.mktime(datetime.strptime(startday,"%Y%m%d").timetuple())
endtime = time.mktime(datetime.strptime(endday,"%Y%m%d").timetuple())

#difference between two weeks
t = starttime+14400
week = 604800

while t <= endtime:
    name = datetime.fromtimestamp(t).strftime('%Y%m%d')[2:]
    url = "http://web.mta.info/developers/data/nyct/turnstile/turnstile_{0}.txt".format(name)
    print url
    t+=week
```
Here are the outputs:
```
http://web.mta.info/developers/data/nyct/turnstile/turnstile_170204.txt
http://web.mta.info/developers/data/nyct/turnstile/turnstile_170211.txt
http://web.mta.info/developers/data/nyct/turnstile/turnstile_170218.txt
http://web.mta.info/developers/data/nyct/turnstile/turnstile_170225.txt
http://web.mta.info/developers/data/nyct/turnstile/turnstile_170304.txt
http://web.mta.info/developers/data/nyct/turnstile/turnstile_170311.txt
http://web.mta.info/developers/data/nyct/turnstile/turnstile_170318.txt
http://web.mta.info/developers/data/nyct/turnstile/turnstile_170325.txt
http://web.mta.info/developers/data/nyct/turnstile/turnstile_170401.txt
```

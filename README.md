# Bubble plot
A bubble plot or bubble chart is a variation of scatter plots displaying three dimensions of data. The first two dimensions are the coordinate values of the x- and y-axis, while the third dimension is the size of the markers (the bubbles) that shows the magnitude of each data point.

### 1. Install and import required libraries
Install `openpyxl` - a library to read and write Excel files.
```python
!conda install -c anaconda openpyxl --y
```

Use inline backend to generate the plots within the browser.
```python
import pandas as pd  # Scientific computing library
import numpy as np   # Data structure library

import matplotlib as mpl
import matplotlib.pyplot as plt
mpl.style.use("ggplot")

%matplotlib inline
```

### 2. About the dataset
The data used to create our bubble plot is the Canadian Immigration dataset 1980 to 2013 from the [United Nation International Migration](https://www.un.org/en/development/desa/population/migration/data/empirical2/migrationflows.asp).

The dataset contains annual data on the flows of international migrants as recorded by the countries of destination. The data presents both inflows and outflows according to the place of birth, citizenship or place of previous / next residence both for foreigners and nationals. 

Here, we will use the Canadian Immigration data. The file format is **.xlsx** - an Excel spreadsheet file.

### 3. Data preparation
#### 3.1. Download the dataset
**Download** the dataset **provided in this repository** and replace the file path between the quotation marks " " with the file path on your computer. Load it into a **pandas** DataFrame.
```python
path_excel = "/insert-your-file-path-here/UN_Canada.xlsx"

df_cad = pd.read_excel(path_excel,
                       sheet_name = "Canada by Citizenship",
                       skiprows = range(20),
                       skipfooter = 2)
```

Let's have a look at the first five items in the dataset.
```python
df.cad_head()
```
<img width="1228" alt="0" src="https://user-images.githubusercontent.com/72127249/167393778-9762dad4-646a-4439-a346-2c39ad34e307.png">


#### 3.2. Tidy up the data
```python
# Remove unnecessary columns
df.drop(["AREA", "REG", "DEV", "Type", "Coverage"], axis = 1, inplace = True)

# Rename the columns
df.rename(columns = {"OdName":"Country", "AreaName":"Continent", "RegName":"Region"}, inplace = True)

# Make all column labels of type string
df.columns = list(map(str, df.columns))

# Set the country's name as index
df.set_index("Country", inplace = True)
```

Create a **list** for the range of years in the dataset (1980 - 2013) and assign it to a variable called **years**. This will be useful when we want to quickly add the whole range of years to our syntax.
```python
years = list(map(str, range(1980, 2014)))
```

#### 3.3. Data selection and normalisation
As an example, let's choose China and India.
```python
df_asia = df.loc[["China", "India"], years].transpose()

# Change the years' type (index) to integer
df_asia.index = map(int, df_asia.index)

# Label the index to "Year"
df_asia.index.name = "Year"

# Reset the index to bring the Year in as a column
df_asia.reset_index(inplace = True)

# View the changes
df_asia.head()
```
<img width="230" alt="1" src="https://user-images.githubusercontent.com/72127249/167393579-e6024812-89b2-4847-90bb-edd1584d8c2b.png">


```python
# Normalise the data
norm_china = (df_asia["China"] - df_asia["China"].min() / df_asia["China"].max() - df_asia["China"].min())

norm_india = (df_asia["India"] - df_asia["India"].min() / df_asia["India"].max() - df_asia["India"].min())
```

### 4. Visualization: Bubble plot
We will be using the `ax` parameter of the Artist layer to generate two scatter plots in one figure.

Since the normalised weights are between 0-1, they may appear too small or too large in the figure. Therefore, some adjustments are needed and for that we will be using the `s` parameter to pass some weights to the size of the markers (bubbles). For our example, we will multiply the normalised data points by 0.05.

```python
# CHINA
ax0 = df_asia.plot(kind = "scatter",
                    x = "Year",
                    y = "China",
                    figsize = (14, 8),
                    alpha = 0.5,
                    color = "tomato",
                    s = norm_china * 0.05,
                    xlim = (1975, 2015))

# INDIA
ax1 = df_asia.plot(kind = "scatter",
                    x = "Year",
                    y = "India",
                    alpha = 0.5,
                    color = "turquoise",
                    s = norm_india * 0.05,
                    ax = ax0)

ax0.set_title("Immigration from China and India 1980 - 2013")
ax0.set_ylabel("Number of Immigrants")
ax0.legend(["China", "India"], loc = "upper left", fontsize = "large")
```
<img width="985" alt="2" src="https://user-images.githubusercontent.com/72127249/167393264-3305a269-6680-427f-805a-51fde582055f.png">

### References:
[Matplotlib](https://matplotlib.org/3.5.0/plot_types/index.html)

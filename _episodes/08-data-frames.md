---
title: "Pandas DataFrames"
teaching: 15
exercises: 15
questions:
- "How can I do statistical analysis of tabular data?"
objectives:
- "Select individual values from a Pandas dataframe."
- "Select entire rows or entire columns from a dataframe."
- "Select a subset of both rows and columns from a dataframe in a single operation."
- "Select a subset of a dataframe by a single Boolean criterion."
keypoints:
- "Use `DataFrame.iloc[..., ...]` to select values by integer location."
- "Use `:` on its own to mean all columns or all rows."
- "Select multiple columns or rows using `DataFrame.loc` and a named slice."
- "Result of slicing can be used in further operations."
- "Use comparisons to select data based on value."
- "Select values or NaN using a Boolean mask."
---

## Note about Pandas DataFrames/Series

A [DataFrame][pandas-dataframe] is a collection of [Series][pandas-series];
The DataFrame is the way Pandas represents a table, and Series is the data-structure
Pandas use to represent a column.

Pandas is built on top of the [Numpy][numpy] library, which in practice means that
most of the methods defined for Numpy Arrays apply to Pandas Series/DataFrames.

What makes Pandas so attractive is the powerful interface to access individual records
of the table, proper handling of missing values, and relational-databases operations
between DataFrames.

## Selecting values

To access a value at the position `[i,j]` of a DataFrame, we have two options, depending on
what is the meaning of `i` in use.
Remember that a DataFrame provides a *index* as a way to identify the rows of the table;
a row, then, has a *position* inside the table as well as a *label*, which
uniquely identifies its *entry* in the DataFrame.

## Use `DataFrame.iloc[..., ...]` to select values by their (entry) position

*   Can specify location by numerical index analogously to 2D version of character selection in strings.

~~~
import pandas as pd
europe = pd.read_csv('data/gapminder_gdp_europe.csv', index_col='country')
print(data.iloc[0, 0])
~~~
{: .language-python}
~~~
1601.056136
~~~
{: .output}

## Use `DataFrame.loc[..., ...]` to select values by their (entry) label.

*   Can specify location by row name analogously to 2D version of dictionary keys.

~~~
europe = pd.read_csv('data/gapminder_gdp_europe.csv', index_col='country')
print(europe.loc["Albania", "gdpPercap_1952"])
~~~
{: .language-python}
~~~
1601.056136
~~~
{: .output}
## Use `:` on its own to mean all columns or all rows.

*   Just like Python's usual slicing notation.

~~~
print(europe.loc["Albania", :])
~~~
{: .language-python}
~~~
gdpPercap_1952    1601.056136
gdpPercap_1957    1942.284244
gdpPercap_1962    2312.888958
gdpPercap_1967    2760.196931
gdpPercap_1972    3313.422188
gdpPercap_1977    3533.003910
gdpPercap_1982    3630.880722
gdpPercap_1987    3738.932735
gdpPercap_1992    2497.437901
gdpPercap_1997    3193.054604
gdpPercap_2002    4604.211737
gdpPercap_2007    5937.029526
Name: Albania, dtype: float64
~~~
{: .output}

*   Would get the same result printing `europe.loc["Albania"]` (without a second index).

~~~
print(europe.loc[:, "gdpPercap_1952"])
~~~
{: .language-python}
~~~
country
Albania                    1601.056136
Austria                    6137.076492
Belgium                    8343.105127
⋮ ⋮ ⋮
Switzerland               14734.232750
Turkey                     1969.100980
United Kingdom             9979.508487
Name: gdpPercap_1952, dtype: float64
~~~
{: .output}

*   Would get the same result printing `europe["gdpPercap_1952"]`
*   Also get the same result printing `europe.gdpPercap_1952` (since it's a column name)

## Select multiple columns or rows using `DataFrame.loc` and a named slice.

~~~
print(europe.loc['Italy':'Poland', 'gdpPercap_1962':'gdpPercap_1972'])
~~~
{: .language-python}
~~~
             gdpPercap_1962  gdpPercap_1967  gdpPercap_1972
country
Italy           8243.582340    10022.401310    12269.273780
Montenegro      4649.593785     5907.850937     7778.414017
Netherlands    12790.849560    15363.251360    18794.745670
Norway         13450.401510    16361.876470    18965.055510
Poland          5338.752143     6557.152776     8006.506993
~~~
{: .output}

In the above code, we discover that **slicing using `loc` is inclusive at both
ends**, which differs from **slicing using `iloc`**, where slicing indicates
everything up to but not including the final index. 


## Result of slicing can be used in further operations.

*   Usually don't just print a slice.
*   All the statistical operators that work on entire dataframes
    work the same way on slices.
*   E.g., calculate max of a slice.

~~~
print(europe.loc['Italy':'Poland', 'gdpPercap_1962':'gdpPercap_1972'].max())
~~~
{: .language-python}
~~~
gdpPercap_1962    13450.40151
gdpPercap_1967    16361.87647
gdpPercap_1972    18965.05551
dtype: float64
~~~
{: .output}

~~~
print(europe.loc['Italy':'Poland', 'gdpPercap_1962':'gdpPercap_1972'].min())
~~~
{: .language-python}
~~~
gdpPercap_1962    4649.593785
gdpPercap_1967    5907.850937
gdpPercap_1972    7778.414017
dtype: float64
~~~
{: .output}

## Use comparisons to select data based on value.

*   Comparison is applied element by element.
*   Returns a similarly-shaped dataframe of `True` and `False`.

~~~
# Use a subset of data to keep output readable.
subset = europe.loc['Italy':'Poland', 'gdpPercap_1962':'gdpPercap_1972']
print('Subset of European data:\n', subset)

# Which values were greater than 10000 ?
print('\nWhere are values large?\n', subset > 10000)
~~~
{: .language-python}
~~~
Subset of European data:
             gdpPercap_1962  gdpPercap_1967  gdpPercap_1972
country
Italy           8243.582340    10022.401310    12269.273780
Montenegro      4649.593785     5907.850937     7778.414017
Netherlands    12790.849560    15363.251360    18794.745670
Norway         13450.401510    16361.876470    18965.055510
Poland          5338.752143     6557.152776     8006.506993

Where are values large?
            gdpPercap_1962 gdpPercap_1967 gdpPercap_1972
country
Italy                False           True           True
Montenegro           False          False          False
Netherlands           True           True           True
Norway                True           True           True
Poland               False          False          False
~~~
{: .output}

## Select values or NaN using a Boolean mask.

*   A frame full of Booleans is sometimes called a *mask* because of how it can be used.

~~~
mask = subset > 10000
print(subset[mask])
~~~
{: .language-python}
~~~
             gdpPercap_1962  gdpPercap_1967  gdpPercap_1972
country
Italy                   NaN     10022.40131     12269.27378
Montenegro              NaN             NaN             NaN
Netherlands     12790.84956     15363.25136     18794.74567
Norway          13450.40151     16361.87647     18965.05551
Poland                  NaN             NaN             NaN
~~~
{: .output}

*   Get the value where the mask is true, and NaN (Not a Number) where it is false.
*   Useful because NaNs are ignored by operations like max, min, average, etc.

~~~
print(subset[subset > 10000].describe())
~~~
{: .language-python}
~~~
       gdpPercap_1962  gdpPercap_1967  gdpPercap_1972
count        2.000000        3.000000        3.000000
mean     13120.625535    13915.843047    16676.358320
std        466.373656     3408.589070     3817.597015
min      12790.849560    10022.401310    12269.273780
25%      12955.737547    12692.826335    15532.009725
50%      13120.625535    15363.251360    18794.745670
75%      13285.513523    15862.563915    18879.900590
max      13450.401510    16361.876470    18965.055510
~~~
{: .output}

## Advanced subsetting

We can also subset on features of the data.  Let's subset the data into only those countries with higher than average GDP across the whole data set (i.e. all the years).


First let's make series (a single pandas data frame column) with the averages for each country.

~~~
avg_by_country = europe.mean(axis.1)
avg_by_country.head()
~~~
{: .language-python}
~~~
country
Albania                    3255.366633
Austria                   20411.916279
Belgium                   19900.758072
Bosnia and Herzegovina     3484.779069
Bulgaria                   6384.055172
dtype: float64
~~~
{: .output}

Now let's get the average value across all of the countries.
~~~
all_avg = avg_by_country.mean()
all_avg
~~~
{: .language-python}
~~~
14469.475533302222
~~~
{: .output}


Now we can subset the original European dataframe into only those countries with higher than average GDPs.
~~~
higher = europe[avg_by_country > all_avg]
higher.head()
~~~
{: .language-python}
~~~
         gdpPercap_1952  gdpPercap_1957  gdpPercap_1962  gdpPercap_1967  \
country                                                                   
Austria     6137.076492     8842.598030    10750.721110     12834.60240   
Belgium     8343.105127     9714.960623    10991.206760     13149.04119   
Denmark     9692.385245    11099.659350    13583.313510     15937.21123   
Finland     6424.519071     7545.415386     9371.842561     10921.63626   
France      7029.809327     8662.834898    10560.485530     12999.91766   

         gdpPercap_1972  gdpPercap_1977  gdpPercap_1982  gdpPercap_1987  \
country                                                                   
Austria     16661.62560     19749.42230     21597.08362     23687.82607   
Belgium     16672.14356     19117.97448     20979.84589     22525.56308   
Denmark     18866.20721     20422.90150     21688.04048     25116.17581   
Finland     14358.87590     15605.42283     18533.15761     21141.01223   
France      16107.19171     18292.63514     20293.89746     22066.44214   

         gdpPercap_1992  gdpPercap_1997  gdpPercap_2002  gdpPercap_2007  
country                                                                  
Austria     27042.01868     29095.92066     32417.60769     36126.49270  
Belgium     25575.57069     27561.19663     30485.88375     33692.60508  
Denmark     26406.73985     29804.34567     32166.50006     35278.41874  
Finland     20647.16499     23723.95020     28204.59057     33207.08440  
France      24703.79615     25889.78487     28926.03234     30470.01670  
~~~
{: .output}


## Select-Apply-Combine operations

Pandas vectorizing methods and grouping operations are features that provide users 
much flexibility to analyse their data.

For instance, let's say we want to have a clearer view on how the European countries 
split themselves according to their GDP.  

Lets first (re)create a Series which tells us if the country is in the higher or lower than the average and add it as a column to the dataframe.

~~~
europe['higher'] = avg_by_country > avg_all
europe.head()
~~~
{: .language-python}
~~~
                        gdpPercap_1952  gdpPercap_1957  gdpPercap_1962  \
country                                                                  
Albania                    1601.056136     1942.284244     2312.888958   
Austria                    6137.076492     8842.598030    10750.721110   
Belgium                    8343.105127     9714.960623    10991.206760   
Bosnia and Herzegovina      973.533195     1353.989176     1709.683679   
Bulgaria                   2444.286648     3008.670727     4254.337839   

                        gdpPercap_1967  gdpPercap_1972  gdpPercap_1977  \
country                                                                  
Albania                    2760.196931     3313.422188     3533.003910   
Austria                   12834.602400    16661.625600    19749.422300   
Belgium                   13149.041190    16672.143560    19117.974480   
Bosnia and Herzegovina     2172.352423     2860.169750     3528.481305   
Bulgaria                   5577.002800     6597.494398     7612.240438   

                        gdpPercap_1982  gdpPercap_1987  gdpPercap_1992  \
country                                                                  
Albania                    3630.880722     3738.932735     2497.437901   
Austria                   21597.083620    23687.826070    27042.018680   
Belgium                   20979.845890    22525.563080    25575.570690   
Bosnia and Herzegovina     4126.613157     4314.114757     2546.781445   
Bulgaria                   8224.191647     8239.854824     6302.623438   

                        gdpPercap_1997  gdpPercap_2002  gdpPercap_2007  higher  
country                                                                         
Albania                    3193.054604     4604.211737     5937.029526   False  
Austria                   29095.920660    32417.607690    36126.492700    True  
Belgium                   27561.196630    30485.883750    33692.605080    True  
Bosnia and Herzegovina     4766.355904     6018.975239     7446.298803   False  
Bulgaria                   5970.388760     7696.777725    10680.792820   False  
~~~
{: .output}

Next we can group the countries by those which have higher than average GDPs and those which have lower than average GDPs and calculate the average for each group.

~~~
europe.groupby('higher').mean()
~~~
{: .language-python}
~~~
        gdpPercap_1952  gdpPercap_1957  gdpPercap_1962  gdpPercap_1967  \
higher                                                                   
False      3460.797562     4356.915102     5381.266911     6789.774524   
True       8175.640146     9941.410203    11776.023847    13977.022879   

        gdpPercap_1972  gdpPercap_1977  gdpPercap_1982  gdpPercap_1987  \
higher                                                                   
False      8589.054224    10123.062167    10999.519354    11657.755578   
True      16925.884987    19039.312758    20896.041919    23564.659468   

        gdpPercap_1992  gdpPercap_1997  gdpPercap_2002  gdpPercap_2007  
higher                                                                  
False      9814.771634    11010.972268    12762.305369    15908.649976  
True      25343.621170    28294.849840    31939.649055    35506.860676
~~~
{: .output}

This still gives us the averages for each group by year. To combine those values we can take the mean across all of the years for each group.

~~~
europe.groupby('higher').mean().mean(axis=1)
~~~
{: .language-python}
~~~
higher
False     9237.903722
True     20448.414746
dtype: float64
~~~
{: .output}


> ## Selection of Individual Values
>
> Assume Pandas has been imported into your notebook
> and the Gapminder GDP data for Europe has been loaded:
>
> ~~~
> import pandas
>
> df = pandas.read_csv('data/gapminder_gdp_europe.csv', index_col='country')
> ~~~
> {: .language-python}
>
> Write an expression to find the Per Capita GDP of Serbia in 2007.
{: .challenge}
>
> > ## Solution
> > The selection can be done by using the labels for both the row ("Serbia") and the column ("gdpPercap_2007"):
> > ~~~
> > print(df.loc['Serbia', 'gdpPercap_2007'])
> > ~~~
> > {: .language-python}
> > The output is
> > ~~~
> > 9786.534714
> > ~~~
> >{: .output}
> {: .solution}
{: .challenge}

> ## Extent of Slicing
>
> 1.  Do the two statements below produce the same output?
> 2.  Based on this,
>     what rule governs what is included (or not) in numerical slices and named slices in Pandas?
> 
> ~~~
> print(df.iloc[0:2, 0:2])
> print(df.loc['Albania':'Belgium', 'gdpPercap_1952':'gdpPercap_1962'])
> ~~~
> {: .language-python}
> 
{: .challenge}
> 
> > ## Solution
> > No, they do not produce the same output! The output of the first statement is:
> > ~~~
> >         gdpPercap_1952  gdpPercap_1957
> > country                                
> > Albania     1601.056136     1942.284244
> > Austria     6137.076492     8842.598030
> > ~~~
> >{: .output}
> > The second statement gives:
> > ~~~
> >         gdpPercap_1952  gdpPercap_1957  gdpPercap_1962
> > country                                                
> > Albania     1601.056136     1942.284244     2312.888958
> > Austria     6137.076492     8842.598030    10750.721110
> > Belgium     8343.105127     9714.960623    10991.206760
> > ~~~
> >{: .output}
> > Clearly, the second statement produces an additional column and an additional row compared to the first statement.  
> > What conclusion can we draw? We see that a numerical slice, 0:2, *omits* the final index (i.e. index 2)
> > in the range provided,
> > while a named slice, 'gdpPercap_1952':'gdpPercap_1962', *includes* the final element.
> {: .solution}
{: .challenge}

> ## Reconstructing Data
>
> Explain what each line in the following short program does:
> what is in `first`, `second`, etc.?
>
> ~~~
> first = pandas.read_csv('data/gapminder_all.csv', index_col='country')
> second = first[first['continent'] == 'Americas']
> third = second.drop('Puerto Rico')
> fourth = third.drop('continent', axis = 1)
> fourth.to_csv('result.csv')
> ~~~
> {: .language-python}
{: .challenge}
>
> > ## Solution
> > Let's go through this piece of code line by line.
> > ~~~
> > first = pandas.read_csv('data/gapminder_all.csv', index_col='country')
> > ~~~
> > {: .language-python}
> > This line loads the dataset containing the GDP data from all countries into a dataframe called 
> > `first`. The `index_col='country'` parameter selects which column to use as the 
> > row labels in the dataframe.  
> > ~~~
> > second = first[first['continent'] == 'Americas']
> > ~~~
> > {: .language-python}
> > This line makes a selection: only those rows of `first` for which the 'continent' column matches 
> > 'Americas' are extracted. Notice how the Boolean expression inside the brackets, 
> > `first['continent'] == 'Americas'`, is used to select only those rows where the expression is true. 
> > Try printing this expression! Can you print also its individual True/False elements? 
> > (hint: first assign the expression to a variable)
> > ~~~
> > third = second.drop('Puerto Rico')
> > ~~~
> > {: .language-python}
> > As the syntax suggests, this line drops the row from `second` where the label is 'Puerto Rico'. The 
> > resulting dataframe `third` has one row less than the original dataframe `second`.
> > ~~~
> > fourth = third.drop('continent', axis = 1)
> > ~~~
> > {: .language-python}
> > Again we apply the drop function, but in this case we are dropping not a row but a whole column. 
> > To accomplish this, we need to specify also the `axis` parameter (we want to drop the second column 
> > which has index 1).
> > ~~~
> > fourth.to_csv('result.csv')
> > ~~~
> > {: .language-python}
> > The final step is to write the data that we have been working on to a csv file. Pandas makes this easy 
> > with the `to_csv()` function. The only required argument to the function is the filename. Note that the 
> > file will be written in the directory from which you started the Jupyter or Python session.
> {: .solution}
{: .challenge}

> ## Selecting Indices
>
> Explain in simple terms what `idxmin` and `idxmax` do in the short program below.
> When would you use these methods?
>
> ~~~
> data = pandas.read_csv('data/gapminder_gdp_europe.csv', index_col='country')
> print(data.idxmin())
> print(data.idxmax())
> ~~~
> {: .language-python}
{: .challenge}
>
> > ## Solution
> > For each column in `data`, `idxmin` will return the index value corresponding to each column's minimum;
> > `idxmax` will do accordingly the same for each column's maximum value.
> >
> > You can use these functions whenever you want to get the row index of the minimum/maximum value and not the actual minimum/maximum value.
> {: .solution}
{: .challenge}

> ## Practice with Selection
>
> Assume Pandas has been imported and the Gapminder GDP data for Europe has been loaded.
> Write an expression to select each of the following:
>
> 1.  GDP per capita for all countries in 1982.
> 2.  GDP per capita for Denmark for all years.
> 3.  GDP per capita for all countries for years *after* 1985.
> 4.  GDP per capita for each country in 2007 as a multiple of 
>     GDP per capita for that country in 1952.
{: .challenge}
>
> > ## Solution
> > 1:
> > ~~~
> > data['gdpPercap_1982']
> > ~~~
> > {: .language-python}
> >
> > 2:
> > ~~~
> > data.loc['Denmark',:]
> > ~~~
> > {: .language-python}
> >
> > 3:
> > ~~~
> > data.loc[:,'gdpPercap_1985':]
> > ~~~
> > {: .language-python}
> > Pandas is smart enough to recognize the number at the end of the column label and does not give you an error, although no column named `gdpPercap_1985` actually exists. This is useful if new columns are added to the CSV file later.
> >
> > 4:
> > ~~~
> > data['gdpPercap_2007']/data['gdpPercap_1952']
> > ~~~
> > {: .language-python}
> {: .solution}
{: .challenge}


> ## Using the dir function to see available methods
>
> Python includes a `dir` function that can be used to display all of the available methods (functions) that are built into a data object.  As an example, the  functions available for a [list data type](https://docs.python.org/3/tutorial/datastructures.html#more-on-lists) are:
> ~~~
> potatoes = ["Russet", "Norkota", "Yukon Gold", "Pontiac"]
> dir(potatoes)
> ~~~
> {: .language-python}
>
> This command returns:
> ~~~
> ['__add__',
> ...
> '__subclasshook__',
>  'append',
>  'clear',
>  'copy',
>  'count',
> 'extend',
> 'index',
> 'insert',
> 'pop',
> 'remove',
> 'reverse',
> 'sort']
> ~~~
> {: .language-python}
>
> The double underscore functions can be ignored for now; functions that are not surrounded by double underscores are the *public interface* of the [list type](https://docs.python.org/3/tutorial/datastructures.html#more-on-lists). So, if you want to sort the list of potatoes, according to `dir` you should try,
> ~~~
> potatoes.sort()
> ~~~
> {: .language-python}
>
> Assume Pandas has been imported and the Gapminder GDP data for Europe has been loaded as `data`.  Then, use `dir` to find the function that prints out the median per-capita GDP across all European countries for each year that information is available.  
{: .challenge}
>
> > ## Solution
> > Among many choices, dir lists the `median()` function as a possibility.  Thus,
> > ~~~
> > data.median()
> > ~~~
> > {: .language-python}
> {: .solution}
{: .challenge}


> ## Interpretation
>
> Poland's borders have been stable since 1945,
> but changed several times in the years before then.
> How would you handle this if you were creating a table of GDP per capita for Poland
> for the entire twentieth century?
{: .challenge}


[pandas-dataframe]: https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html
[pandas-series]: https://pandas.pydata.org/pandas-docs/stable/generated/pandas.Series.html
[numpy]: http://www.numpy.org/

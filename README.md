
**Heroes Of Pymoli Data Analysis**


```python
import pandas as pd
```


```python
path = 'Resources/purchase_data.json'
heroes = pd.read_json(path)
```

**Player Count**


```python
# Total number of unique players (based on SN)
total_players = len(heroes['SN'].unique())
# Create DF
total_players_df = pd.DataFrame({'Total number of players': [total_players]})
total_players_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total number of players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>573</td>
    </tr>
  </tbody>
</table>
</div>



**Purchasing Analysis (Total)**


```python
# Number of unique items bought, different from item name
number_unique_items = len(heroes['Item ID'].unique())
# Average price
av_purch_price = round(heroes['Price'].mean(), 2)
# Number of purchases is equal to rows number (I hope.. )
number_purchases = heroes['Item ID'].count()
# Total revenue - sum of all prices
total_revenue = heroes['Price'].sum()
# Create DF
purch_analysis_df = pd.DataFrame({'Number of Unique Items': [number_unique_items], 'Average Purchase Price': [av_purch_price],
                                 'Total Number of Purchases': [number_purchases], 'Total Revenue': [total_revenue]})
purch_analysis_df['Average Purchase Price'] = purch_analysis_df['Average Purchase Price'].map("$ {:,.2f}".format)
purch_analysis_df['Total Revenue'] = purch_analysis_df['Total Revenue'].map("$ {:,.2f}".format)
purch_analysis = purch_analysis_df[['Number of Unique Items', 'Average Purchase Price', 'Total Number of Purchases',
                                   'Total Revenue']]
purch_analysis
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Number of Unique Items</th>
      <th>Average Purchase Price</th>
      <th>Total Number of Purchases</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>183</td>
      <td>$ 2.93</td>
      <td>780</td>
      <td>$ 2,286.33</td>
    </tr>
  </tbody>
</table>
</div>



**Gender Demographics**


```python
# Check what we have in column 'Gender'
heroes['Gender'].value_counts()
```




    Male                     633
    Female                   136
    Other / Non-Disclosed     11
    Name: Gender, dtype: int64




```python
# Create a DF with Gender & SN only
gender_df = heroes[['SN', 'Gender']]
# Delete all duplicates in SN, exept the last one (keep='last')
clean_gender = gender_df.drop_duplicates(['SN'], keep='last')
# Change index to Gender, to use loc. function
clean_gender_ind = clean_gender.set_index('Gender')
# Use loc. to count all 3 variations
male_count = clean_gender_ind.loc['Male', 'SN'].count()
female_count = clean_gender_ind.loc['Female', 'SN'].count()
other_count = clean_gender_ind.loc['Other / Non-Disclosed', 'SN'].count()
# Count persentages
male_perc = round((male_count/total_players*100), 2)
female_perc = round((female_count/total_players*100), 2)
other_perc = round((other_count/total_players*100), 2)
# Create DF
gender_demo = {'Gender': ['Male', 'Female', 'Other / Non-Disclose'],
              'Total count': [male_count, female_count, other_count],
              'Percentage of Players': [male_perc, female_perc, other_perc]}
gender_demo_df = pd.DataFrame(gender_demo)
new_gender_demo = gender_demo_df.set_index('Gender')
new_gender_demo
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Percentage of Players</th>
      <th>Total count</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>81.15</td>
      <td>465</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>17.45</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Other / Non-Disclose</th>
      <td>1.40</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>



**Purchasing Analysis (Gender)**


```python
# Set Gender as index in original df
gender_ind = heroes.set_index('Gender')

# Count a number of purchases
male_purch = gender_ind.loc['Male', 'SN'].count()
female_purch = gender_ind.loc['Female', 'SN'].count()
other_purch = gender_ind.loc['Other / Non-Disclosed', 'SN'].count()

# Count average and total purchases
male_avg_price = round(gender_ind.loc['Male', 'Price'].mean(),2)
male_total = round(gender_ind.loc['Male', 'Price'].sum(), 2)
female_avg_price = round(gender_ind.loc['Female', 'Price'].mean(),2)
female_total = round(gender_ind.loc['Female', 'Price'].sum(), 2)
other_avg_price = round(gender_ind.loc['Other / Non-Disclosed', 'Price'].mean(),2)
other_total = round(gender_ind.loc['Other / Non-Disclosed', 'Price'].sum(), 2)

# Count normalized totals
male_norm_purch = round((male_total / male_count), 2)
female_norm_purch = round((female_total / female_count), 2)
other_norm_purch = round((other_total / other_count), 2)

# Create answer DF
purch_an_gender = {'Gender': ['Male', 'Female', 'Other / Non-Disclose'],
                   'Purchase Count': [male_purch, female_purch, other_purch],
                   'Average Purchase Price': [male_avg_price, female_avg_price, other_avg_price], 
                  'Total Purchase Value': [male_total, female_total, other_total], 
                  'Normalized Totals': [male_norm_purch, female_norm_purch, other_norm_purch]}
purch_an_gender_df = pd.DataFrame(purch_an_gender)
purch_gender_answ = purch_an_gender_df.set_index('Gender')
purch_gender_answ['Average Purchase Price']=purch_gender_answ['Average Purchase Price'].map("$ {:,.2f}".format)
purch_gender_answ['Total Purchase Value']=purch_gender_answ['Total Purchase Value'].map("$ {:,.2f}".format)
purch_gender_answ['Normalized Totals']=purch_gender_answ['Normalized Totals'].map("$ {:,.2f}".format)
purch_gender_an = purch_gender_answ[['Purchase Count', 'Average Purchase Price', 'Total Purchase Value', 'Normalized Totals']]
purch_gender_an
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>633</td>
      <td>$ 2.95</td>
      <td>$ 1,867.68</td>
      <td>$ 4.02</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>136</td>
      <td>$ 2.82</td>
      <td>$ 382.91</td>
      <td>$ 3.83</td>
    </tr>
    <tr>
      <th>Other / Non-Disclose</th>
      <td>11</td>
      <td>$ 3.25</td>
      <td>$ 35.74</td>
      <td>$ 4.47</td>
    </tr>
  </tbody>
</table>
</div>



**Age Demographics**


```python
# Check min and max values
print(heroes['Age'].max())
print(heroes['Age'].min())
```

    45
    7



```python
# Create bins and labels to them
bins = [4,9,14,19,24,29,34,39,44,49]
labels = ['5 to 9', '10 to 14', '15 to 19', '20 to 24', '25 to 29', '30 to 34',
          '35 to 39', '40 to 44', '45 to 50']
# Add new column with bins
heroes['Age Bins'] = pd.cut(heroes["Age"],bins,labels=labels)
# Group by a new column
age_grouped = heroes.groupby('Age Bins')
# Pick up values
age_purch_count = (age_grouped['Age'].count())
age_avg_purch_price = round((age_grouped['Price'].mean()), 2)
age_total_purch_value = (age_grouped['Price'].sum())
age_df = heroes[['SN', 'Age', 'Age Bins']]
# Delete all duplicates in SN, exept the last one (keep='last')
clean_age = age_df.drop_duplicates(['SN'], keep='last')
clean_age_grouped = clean_age.groupby('Age Bins')
age_demo_df = pd.DataFrame({'Total Count': clean_age_grouped.size(),
                      'Percentage of Players': round(((clean_age_grouped.size()/total_players)*100),2)})
age_demo_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
    <tr>
      <th>Age Bins</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>5 to 9</th>
      <td>3.32</td>
      <td>19</td>
    </tr>
    <tr>
      <th>10 to 14</th>
      <td>4.01</td>
      <td>23</td>
    </tr>
    <tr>
      <th>15 to 19</th>
      <td>17.45</td>
      <td>100</td>
    </tr>
    <tr>
      <th>20 to 24</th>
      <td>45.20</td>
      <td>259</td>
    </tr>
    <tr>
      <th>25 to 29</th>
      <td>15.18</td>
      <td>87</td>
    </tr>
    <tr>
      <th>30 to 34</th>
      <td>8.20</td>
      <td>47</td>
    </tr>
    <tr>
      <th>35 to 39</th>
      <td>4.71</td>
      <td>27</td>
    </tr>
    <tr>
      <th>40 to 44</th>
      <td>1.75</td>
      <td>10</td>
    </tr>
    <tr>
      <th>45 to 50</th>
      <td>0.17</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



**Purchasing Analysis (Age)**


```python
purch_age_df = pd.DataFrame({'Purchase Count': age_purch_count,
                           'Average Purchase Price': age_avg_purch_price,
                            'Total Purchase Value': age_total_purch_value,
                            'Normalized Totals': round(age_total_purch_value/clean_age_grouped.size(), 2),
                            'Total percentage': round((age_total_purch_value/total_revenue*100),2)})
purch_age_df['Average Purchase Price'] = purch_age_df['Average Purchase Price'].map("$ {:,.2f}".format)
purch_age_df['Total Purchase Value'] = purch_age_df['Total Purchase Value'].map("$ {:,.2f}".format)
purch_age_df['Normalized Totals'] = purch_age_df['Normalized Totals'].map("$ {:,.2f}".format)
purch_age_df1 = purch_age_df[['Purchase Count', 'Average Purchase Price', 'Total Purchase Value', 'Normalized Totals',
                             'Total percentage']]
purch_age_df1
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
      <th>Total percentage</th>
    </tr>
    <tr>
      <th>Age Bins</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>5 to 9</th>
      <td>28</td>
      <td>$ 2.98</td>
      <td>$ 83.46</td>
      <td>$ 4.39</td>
      <td>3.65</td>
    </tr>
    <tr>
      <th>10 to 14</th>
      <td>35</td>
      <td>$ 2.77</td>
      <td>$ 96.95</td>
      <td>$ 4.22</td>
      <td>4.24</td>
    </tr>
    <tr>
      <th>15 to 19</th>
      <td>133</td>
      <td>$ 2.91</td>
      <td>$ 386.42</td>
      <td>$ 3.86</td>
      <td>16.90</td>
    </tr>
    <tr>
      <th>20 to 24</th>
      <td>336</td>
      <td>$ 2.91</td>
      <td>$ 978.77</td>
      <td>$ 3.78</td>
      <td>42.81</td>
    </tr>
    <tr>
      <th>25 to 29</th>
      <td>125</td>
      <td>$ 2.96</td>
      <td>$ 370.33</td>
      <td>$ 4.26</td>
      <td>16.20</td>
    </tr>
    <tr>
      <th>30 to 34</th>
      <td>64</td>
      <td>$ 3.08</td>
      <td>$ 197.25</td>
      <td>$ 4.20</td>
      <td>8.63</td>
    </tr>
    <tr>
      <th>35 to 39</th>
      <td>42</td>
      <td>$ 2.84</td>
      <td>$ 119.40</td>
      <td>$ 4.42</td>
      <td>5.22</td>
    </tr>
    <tr>
      <th>40 to 44</th>
      <td>16</td>
      <td>$ 3.19</td>
      <td>$ 51.03</td>
      <td>$ 5.10</td>
      <td>2.23</td>
    </tr>
    <tr>
      <th>45 to 50</th>
      <td>1</td>
      <td>$ 2.72</td>
      <td>$ 2.72</td>
      <td>$ 2.72</td>
      <td>0.12</td>
    </tr>
  </tbody>
</table>
</div>



**Top Spenders**


```python
# Extract SN and Price, group by SN, create new DF and sort it by price sum()
price_sn = heroes[['SN', 'Price']]
pr_sn_grouped = price_sn.groupby(['SN'])
price_df = pd.DataFrame(pr_sn_grouped.sum())
price_sorted = price_df.sort_values('Price', ascending=False)
spenders_price = price_sorted.head(5)
# Reset index to default to be able to merge later
spenders_price.reset_index(inplace=True)

# Create a second pd with purchase counts
price_count_df = pd.DataFrame(pr_sn_grouped.count())
price_count_df.reset_index(inplace=True)

# Merge in one
merged_price = pd.merge(spenders_price, price_count_df, on='SN')
merged_price.columns = ['SN', 'Total Purchase Value', 'Purchase Count']
merged_price['Average Purchase Price'] = round((merged_price['Total Purchase Value'] / merged_price['Purchase Count']),2)
top5answer = merged_price.set_index('SN')
top5answer['Average Purchase Price'] = top5answer['Average Purchase Price'].map("$ {:,.2f}".format)
top5answer['Total Purchase Value'] = top5answer['Total Purchase Value'].map("$ {:,.2f}".format)
top5 = top5answer[['Purchase Count', 'Average Purchase Price', 'Total Purchase Value']]
top5
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>SN</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Undirrala66</th>
      <td>5</td>
      <td>$ 3.41</td>
      <td>$ 17.06</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>4</td>
      <td>$ 3.39</td>
      <td>$ 13.56</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>4</td>
      <td>$ 3.18</td>
      <td>$ 12.74</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>3</td>
      <td>$ 4.24</td>
      <td>$ 12.73</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>3</td>
      <td>$ 3.86</td>
      <td>$ 11.58</td>
    </tr>
  </tbody>
</table>
</div>



**Most Popular Items** 


```python
# There are 183 unique Item IDs and only 179 unique Names, that's why this task may have 2 different answers,
# depends on what to use as a base - Item ID or Item Name. The correct one is to use a longer column - Item ID

# Extract necessary columns
items = heroes[['Item ID','Item Name', 'Price', 'SN']]
#items['SN'] = 1 - calls a warning, so..
# Assign values 1 to SN - better way, without warnings
items = items.assign(SN= 1)
items.columns = ['Item ID','Item Name', 'Total Purchase Value', 'Purchase Count']
# Group by Name, sort by number of purchases and reset index
items_grouped = items.groupby(['Item ID', 'Item Name'])
items_df = pd.DataFrame(items_grouped.sum())
items_sorted = items_df.sort_values('Purchase Count', ascending=False)
popular_items = items_sorted.head(5)
popular_items = popular_items.assign(Item_Price=(popular_items['Total Purchase Value'] / popular_items['Purchase Count']))
popular_items['Total Purchase Value'] = popular_items['Total Purchase Value'].map("$ {:,.2f}".format)
popular_items['Item_Price'] = popular_items['Item_Price'].map("$ {:,.2f}".format)
popular_items_answ = popular_items[['Purchase Count', 'Item_Price', 'Total Purchase Value']]
popular_items_answ
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Item_Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39</th>
      <th>Betrayal, Whisper of Grieving Widows</th>
      <td>11</td>
      <td>$ 2.35</td>
      <td>$ 25.85</td>
    </tr>
    <tr>
      <th>84</th>
      <th>Arcane Gem</th>
      <td>11</td>
      <td>$ 2.23</td>
      <td>$ 24.53</td>
    </tr>
    <tr>
      <th>31</th>
      <th>Trickster</th>
      <td>9</td>
      <td>$ 2.07</td>
      <td>$ 18.63</td>
    </tr>
    <tr>
      <th>175</th>
      <th>Woeful Adamantite Claymore</th>
      <td>9</td>
      <td>$ 1.24</td>
      <td>$ 11.16</td>
    </tr>
    <tr>
      <th>13</th>
      <th>Serenity</th>
      <td>9</td>
      <td>$ 1.49</td>
      <td>$ 13.41</td>
    </tr>
  </tbody>
</table>
</div>




```python
# **Most Popular Items** Incorrect answer: 
# Based on Item Name, results are not correct

# Leave it here just in case:
# Extract necessary columns
#items2 = heroes[['Item Name', 'Price', 'SN']]
#items['SN'] = 1 - calls a warning, so..
# Assign values 1 to SN - better way without warnings
#items2 = items2.assign(SN= 1)
#items2.columns = ['Item Name', 'Total Purchase Value', 'Purchase Count']
# Group by Name, sort by number of purchases and reset index
#items_grouped2 = items2.groupby(['Item Name'])
#items_df2 = pd.DataFrame(items_grouped2.sum())
#items_sorted2 = items_df2.sort_values('Purchase Count', ascending=False)
#popular_items2 = items_sorted2.head(5)
#popular_items2.reset_index(inplace=True)
# Create Item ID df
#item_id2 = heroes[['Item Name', 'Item ID']]
# There are 183 unique Item IDs and only 179 unique Names, so I drop ID duplicates
#clean_id2 = item_id2.drop_duplicates(['Item ID'], keep='last')
# Merge 2 DFs
#merged_items2 = pd.merge(popular_items2, clean_id2, on='Item Name')
# Add total value
#merged_items2['Item Price'] = round((merged_items2['Total Purchase Value'] / merged_items2['Purchase Count']),2)
#popular_items_answer2 = merged_items2.set_index('Item ID')
#popular_items_answer2
```

**Most Profitable Items**



```python
# Use df from previous step
profit_sorted = items_df.sort_values('Total Purchase Value', ascending=False)
profit_answer = profit_sorted.head(5)

profit_answer = profit_answer.assign(Item_Price=(profit_answer['Total Purchase Value'] / profit_answer['Purchase Count']))
#profit_answer['Item Price'] = profit_answer['Total Purchase Value'] / profit_answer['Purchase Count'] - calls warning
profit_answer['Total Purchase Value'] = profit_answer['Total Purchase Value'].map("$ {:,.2f}".format)
profit_answer['Item_Price'] = profit_answer['Item_Price'].map("$ {:,.2f}".format)
profit_it_answ = profit_answer[['Purchase Count', 'Item_Price', 'Total Purchase Value']]
profit_it_answ
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Item_Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <th>Retribution Axe</th>
      <td>9</td>
      <td>$ 4.14</td>
      <td>$ 37.26</td>
    </tr>
    <tr>
      <th>115</th>
      <th>Spectral Diamond Doomblade</th>
      <td>7</td>
      <td>$ 4.25</td>
      <td>$ 29.75</td>
    </tr>
    <tr>
      <th>32</th>
      <th>Orenmir</th>
      <td>6</td>
      <td>$ 4.95</td>
      <td>$ 29.70</td>
    </tr>
    <tr>
      <th>103</th>
      <th>Singed Scalpel</th>
      <td>6</td>
      <td>$ 4.87</td>
      <td>$ 29.22</td>
    </tr>
    <tr>
      <th>107</th>
      <th>Splitter, Foe Of Subtlety</th>
      <td>8</td>
      <td>$ 3.61</td>
      <td>$ 28.88</td>
    </tr>
  </tbody>
</table>
</div>



**Observed Trends**

1. Men compose 81% of all gamers. They make 82% of all purchases and spend just a little bit more than women.

2. Almost half of all gamers are 20 - 24 years old. Teenagers from 15 to 19 years represent 17% of gamers, and on the third place are people from 25 to 29 years with 15%. Their purchase activity has approximately the same numbers: 20 - 24 years - 43% of all purchases (despite of the fact, that their average purchase is smaller than the other age groups purchases), 15 - 19 and 25 29 make 17% and 16% of purchases respectively. That means that in average each gamer spends approximately the same amount of money.

3. The most popular items are less expensive then the average price, that indicates the tendency to buy cheaper items. Most of the profits are made by the items from the top of a price range. (May check the percentage of profit made by expensive items)

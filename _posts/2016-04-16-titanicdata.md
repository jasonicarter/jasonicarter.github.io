---
layout: post
title: "Looking for survivors with Titanic Data Analysis"
share: y
disqus: y
---

## Introduction
**Purpose:** To performa data analysis on a sample Titanic dataset.

This dataset contains demographics and passenger information from 891 of the 2224 passengers and crew on board the Titanic. You can view a description of this dataset on the Kaggle website, where the data was obtained (https://www.kaggle.com/c/titanic/data).


## Questions

"One of the reasons that the shipwreck led to such loss of life was that there were not enough lifeboats for the passengers and crew. Although there was some element of luck involved in surviving the sinking, some groups of people were more likely to survive than others, such as women, children, and the upper-class." -  https://www.kaggle.com/c/titanic

What factors made people more likely to survive?
1. **Were social-economic standing a factor in survival rate?**
2. **Did age, regardless of sex, determine your chances of survival?**
3. **Did women and children have preference to lifeboats (survival)?**
4. **Did women with children have a better survival rate vs women without children (adults 18+)?**

**Assumption:** We are going to assume that everyone who survived made it to a life boat and it wasn't by chance or luck.

## Data Wrangling

### Data Description
(from https://www.kaggle.com/c/titanic)

- **survival:** Survival (0 = No; 1 = Yes)
- **pclass:** Passenger Class (1 = 1st; 2 = 2nd; 3 = 3rd)
- **name:** Name
- **sex:** Sex
- **age:** Age
- **sibsp:** Number of Siblings/Spouses Aboard
- **parch:** Number of Parents/Children Aboard
- **ticket:** Ticket Number
- **fare:** Passenger Fare
- **cabin:** Cabin
- **embarked:** Port of Embarkation (C = Cherbourg; Q = Queenstown; S = Southampton)

**Special Notes:**
- Pclass is a proxy for socio-economic status (SES) 1st ~ Upper; 2nd ~ Middle; 3rd ~ Lower
- Age is in Years; Fractional if Age less than One (1) If the Age is Estimated, it is in the form xx.5

With respect to the family relation variables (i.e. sibsp and parch) some relations were ignored.  The following are the definitions used for sibsp and parch.

- **Sibling:**  Brother, Sister, Stepbrother, or Stepsister of Passenger Aboard Titanic
- **Spouse:**   Husband or Wife of Passenger Aboard Titanic (Mistresses and Fiances Ignored)
- **Parent:**   Mother or Father of Passenger Aboard Titanic
- **Child:**    Son, Daughter, Stepson, or Stepdaughter of Passenger Aboard Titanic

Other family relatives excluded from this study include cousins, nephews/nieces, aunts/uncles, and in-laws.  Some children travelled only with a nanny, therefore parch=0 for them.  As well, some travelled with very close friends or neighbors in a village, however, the definitions do not support such relations.

**In [308]:**

{% highlight python %}
# Render plots inline
%matplotlib inline

# Import libraries
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

# Set style for all graphs
sns.set_style("dark")

# Read in the dataset, create dataframe
titanic_data = pd.read_csv('titanic_data.csv')
{% endhighlight %}

**In [309]:**

{% highlight python %}
# Print the first few records to review data and format
titanic_data.head()
{% endhighlight %}




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PassengerId</th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Name</th>
      <th>Sex</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Ticket</th>
      <th>Fare</th>
      <th>Cabin</th>
      <th>Embarked</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0</td>
      <td>3</td>
      <td>Braund, Mr. Owen Harris</td>
      <td>male</td>
      <td>22.0</td>
      <td>1</td>
      <td>0</td>
      <td>A/5 21171</td>
      <td>7.2500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>Cumings, Mrs. John Bradley (Florence Briggs Th...</td>
      <td>female</td>
      <td>38.0</td>
      <td>1</td>
      <td>0</td>
      <td>PC 17599</td>
      <td>71.2833</td>
      <td>C85</td>
      <td>C</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>1</td>
      <td>3</td>
      <td>Heikkinen, Miss. Laina</td>
      <td>female</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
      <td>STON/O2. 3101282</td>
      <td>7.9250</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>1</td>
      <td>1</td>
      <td>Futrelle, Mrs. Jacques Heath (Lily May Peel)</td>
      <td>female</td>
      <td>35.0</td>
      <td>1</td>
      <td>0</td>
      <td>113803</td>
      <td>53.1000</td>
      <td>C123</td>
      <td>S</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>0</td>
      <td>3</td>
      <td>Allen, Mr. William Henry</td>
      <td>male</td>
      <td>35.0</td>
      <td>0</td>
      <td>0</td>
      <td>373450</td>
      <td>8.0500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
  </tbody>
</table>
</div>



**In [310]:**

{% highlight python %}
# Print the last few records to review data and format
titanic_data.tail()
{% endhighlight %}




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PassengerId</th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Name</th>
      <th>Sex</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Ticket</th>
      <th>Fare</th>
      <th>Cabin</th>
      <th>Embarked</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>886</th>
      <td>887</td>
      <td>0</td>
      <td>2</td>
      <td>Montvila, Rev. Juozas</td>
      <td>male</td>
      <td>27.0</td>
      <td>0</td>
      <td>0</td>
      <td>211536</td>
      <td>13.00</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>887</th>
      <td>888</td>
      <td>1</td>
      <td>1</td>
      <td>Graham, Miss. Margaret Edith</td>
      <td>female</td>
      <td>19.0</td>
      <td>0</td>
      <td>0</td>
      <td>112053</td>
      <td>30.00</td>
      <td>B42</td>
      <td>S</td>
    </tr>
    <tr>
      <th>888</th>
      <td>889</td>
      <td>0</td>
      <td>3</td>
      <td>Johnston, Miss. Catherine Helen "Carrie"</td>
      <td>female</td>
      <td>NaN</td>
      <td>1</td>
      <td>2</td>
      <td>W./C. 6607</td>
      <td>23.45</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>889</th>
      <td>890</td>
      <td>1</td>
      <td>1</td>
      <td>Behr, Mr. Karl Howell</td>
      <td>male</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
      <td>111369</td>
      <td>30.00</td>
      <td>C148</td>
      <td>C</td>
    </tr>
    <tr>
      <th>890</th>
      <td>891</td>
      <td>0</td>
      <td>3</td>
      <td>Dooley, Mr. Patrick</td>
      <td>male</td>
      <td>32.0</td>
      <td>0</td>
      <td>0</td>
      <td>370376</td>
      <td>7.75</td>
      <td>NaN</td>
      <td>Q</td>
    </tr>
  </tbody>
</table>
</div>



**Note:** Some values for Age are NaN, while ticket and cabin values are alphanumeric and also missing values with NaN. Not a big deal but good to know. Based on current questions, will not require either Ticket or Cabin data.

**Additional potential questions from reading data and data description**
- How did children with nannies fare in comparison to children with parents. Did the nanny "abandon" the child to save his/her own life?
 - I would need additional information to determine if a child was indeed only on board with a nanny. For example, a child could be on board with an adult sibling. This would make Parch (parent) = 0 but it would be incorrect to say the child had a nanny.
 - Need to review list for children with no siblings. These will be children with nannies; however, a child could have siblings and still have a nanny as well. Potential question **Q5**
- Did cabin location play a part in the survival rate without the consideration of class
 - No data on where the cabins are actually located on the Titanic
 - External source of this data could probably be found

### Data Cleanup

From the data description and questions to answer, I've determined that some dataset columns will not play a part in my analysis and these columns can therefore be removed. This will decluster the dataset and also help with processing performance of the dataset.
- PassengerId
- Name
- Ticket
- Cabin
- Fare
- Embarked

I'll take a 3 step approach to data cleanup
1. Identify and remove any duplicate entries
2. Remove unnecessary columns
3. Fix missing and data format issues

#### Step 1 - Remove duplicate entries
Concluded that no duplicate entires exist, based on tests below

**In [275]:**

{% highlight python %}
# Identify and remove duplicate entries
titanic_data_duplicates = titanic_data.duplicated()
print 'Number of duplicate entries is/are {}'.format(titanic_data_duplicates.sum())
{% endhighlight %}

    Number of duplicate entries is/are 0


**In [315]:**

{% highlight python %}
# Let us just make sure this is working
duplicate_test = titanic_data.duplicated('Age').head()
print 'Number of entries with duplicate age in top entires are {}'.format(duplicate_test.sum())
titanic_data.head()
{% endhighlight %}

    Number of entries with duplicate age in top entires are 1





<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PassengerId</th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Name</th>
      <th>Sex</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Ticket</th>
      <th>Fare</th>
      <th>Cabin</th>
      <th>Embarked</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0</td>
      <td>3</td>
      <td>Braund, Mr. Owen Harris</td>
      <td>male</td>
      <td>22.0</td>
      <td>1</td>
      <td>0</td>
      <td>A/5 21171</td>
      <td>7.2500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>Cumings, Mrs. John Bradley (Florence Briggs Th...</td>
      <td>female</td>
      <td>38.0</td>
      <td>1</td>
      <td>0</td>
      <td>PC 17599</td>
      <td>71.2833</td>
      <td>C85</td>
      <td>C</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>1</td>
      <td>3</td>
      <td>Heikkinen, Miss. Laina</td>
      <td>female</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
      <td>STON/O2. 3101282</td>
      <td>7.9250</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>1</td>
      <td>1</td>
      <td>Futrelle, Mrs. Jacques Heath (Lily May Peel)</td>
      <td>female</td>
      <td>35.0</td>
      <td>1</td>
      <td>0</td>
      <td>113803</td>
      <td>53.1000</td>
      <td>C123</td>
      <td>S</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>0</td>
      <td>3</td>
      <td>Allen, Mr. William Henry</td>
      <td>male</td>
      <td>35.0</td>
      <td>0</td>
      <td>0</td>
      <td>373450</td>
      <td>8.0500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
  </tbody>
</table>
</div>



#### Step 2 - Remove unnecessary columns
Columns (PassengerId, Name, Ticket, Cabin, Fare, Embarked) removed

**In [316]:**

{% highlight python %}
# Create new dataset without unwanted columns
titanic_data_cleaned = titanic_data.drop(['PassengerId','Name','Ticket','Cabin','Fare','Embarked'], axis=1)
titanic_data_cleaned.head()
{% endhighlight %}




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Sex</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>3</td>
      <td>male</td>
      <td>22.0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>1</td>
      <td>female</td>
      <td>38.0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>3</td>
      <td>female</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>1</td>
      <td>female</td>
      <td>35.0</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>3</td>
      <td>male</td>
      <td>35.0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



#### Step 3 - Fix any missing or data format issues

**In [278]:**

{% highlight python %}
# Calculate number of missing values
titanic_data_cleaned.isnull().sum()
{% endhighlight %}




    Survived      0
    Pclass        0
    Sex           0
    Age         177
    SibSp         0
    Parch         0
    dtype: int64



**In [279]:**

{% highlight python %}
# Review some of the missing Age data
missing_age_bool = pd.isnull(titanic_data_cleaned['Age'])
titanic_data_cleaned[missing_age_bool].head()
{% endhighlight %}




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Sex</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>5</th>
      <td>0</td>
      <td>3</td>
      <td>male</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>17</th>
      <td>1</td>
      <td>2</td>
      <td>male</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>1</td>
      <td>3</td>
      <td>female</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>26</th>
      <td>0</td>
      <td>3</td>
      <td>male</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>28</th>
      <td>1</td>
      <td>3</td>
      <td>female</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



**In [317]:**

{% highlight python %}
# Determine number of males and females with missing age values
missing_age_female = titanic_data_cleaned[missing_age_bool]['Sex'] == 'female'
missing_age_male = titanic_data_cleaned[missing_age_bool]['Sex'] == 'male'

print 'Number for females and males with age missing are {} and {} respectively'.format(
missing_age_female.sum(),missing_age_male.sum())
{% endhighlight %}

    Number for females and males with age missing are 53 and 124 respectively


**In [318]:**

{% highlight python %}
# Taking a look at the datatypes
titanic_data_cleaned.info()
{% endhighlight %}

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 891 entries, 0 to 890
    Data columns (total 6 columns):
    Survived    891 non-null int64
    Pclass      891 non-null int64
    Sex         891 non-null object
    Age         714 non-null float64
    SibSp       891 non-null int64
    Parch       891 non-null int64
    dtypes: float64(1), int64(4), object(1)
    memory usage: 41.8+ KB


Missing Age data will affect **Q2 - Did age, regardless of sex, determine your chances of survival?** But graphing and summations shouldn't be a problem since they will be treated as zero(0) value. However, 177 is roughly 20% of our 891 sample dataset which seems like a lot to discount. Also, this needs to be accounted for if reviewing descriptive stats such as mean age.

Should keep note of the proportions across male and female...

- Age missing in male data: **124**
- Age missing in female data: **53**

## Data Exploration and Visualization

**In [282]:**

{% highlight python %}
# Looking at some typical descriptive statistics
titanic_data_cleaned.describe()
{% endhighlight %}




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>891.000000</td>
      <td>891.000000</td>
      <td>714.000000</td>
      <td>891.000000</td>
      <td>891.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>0.383838</td>
      <td>2.308642</td>
      <td>29.699118</td>
      <td>0.523008</td>
      <td>0.381594</td>
    </tr>
    <tr>
      <th>std</th>
      <td>0.486592</td>
      <td>0.836071</td>
      <td>14.526497</td>
      <td>1.102743</td>
      <td>0.806057</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.420000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>0.000000</td>
      <td>2.000000</td>
      <td>20.125000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>0.000000</td>
      <td>3.000000</td>
      <td>28.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>1.000000</td>
      <td>3.000000</td>
      <td>38.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>1.000000</td>
      <td>3.000000</td>
      <td>80.000000</td>
      <td>8.000000</td>
      <td>6.000000</td>
    </tr>
  </tbody>
</table>
</div>



**In [283]:**

{% highlight python %}
# Age min at 0.42 looks a bit weird so give a closer look
titanic_data_cleaned[titanic_data_cleaned['Age'] < 1]
{% endhighlight %}




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Sex</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>78</th>
      <td>1</td>
      <td>2</td>
      <td>male</td>
      <td>0.83</td>
      <td>0</td>
      <td>2</td>
    </tr>
    <tr>
      <th>305</th>
      <td>1</td>
      <td>1</td>
      <td>male</td>
      <td>0.92</td>
      <td>1</td>
      <td>2</td>
    </tr>
    <tr>
      <th>469</th>
      <td>1</td>
      <td>3</td>
      <td>female</td>
      <td>0.75</td>
      <td>2</td>
      <td>1</td>
    </tr>
    <tr>
      <th>644</th>
      <td>1</td>
      <td>3</td>
      <td>female</td>
      <td>0.75</td>
      <td>2</td>
      <td>1</td>
    </tr>
    <tr>
      <th>755</th>
      <td>1</td>
      <td>2</td>
      <td>male</td>
      <td>0.67</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>803</th>
      <td>1</td>
      <td>3</td>
      <td>male</td>
      <td>0.42</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>831</th>
      <td>1</td>
      <td>2</td>
      <td>male</td>
      <td>0.83</td>
      <td>1</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



**In [284]:**

{% highlight python %}
# Taking a look at some survival rates for babies
youngest_to_survive = titanic_data_cleaned[titanic_data_cleaned['Survived'] == True]['Age'].min()
youngest_to_die = titanic_data_cleaned[titanic_data_cleaned['Survived'] == False]['Age'].min()
oldest_to_survive = titanic_data_cleaned[titanic_data_cleaned['Survived'] == True]['Age'].max()
oldest_to_die = titanic_data_cleaned[titanic_data_cleaned['Survived'] == False]['Age'].max()

print 'Youngest to survive: {} \nYoungest to die: {} \nOldest to survive: {} \nOldest to die: {}'.format(
youngest_to_survive, youngest_to_die, oldest_to_survive, oldest_to_die)
{% endhighlight %}

    Youngest to survive: 0.42
    Youngest to die: 1.0
    Oldest to survive: 80.0
    Oldest to die: 74.0


Data description states that Age can be fractional - *Age is in Years; Fractional if Age less than One (1) If the Age is Estimated, it is in the form xx.5* - Therefore, 0.42 appears to be expected and normal data

**Note:** An interesting note is that all "new borns" survived. Potential **Q6** - At what age did children's survival rate match that of adults, if ever.

Other notable stats
- Oldest to survive: **80**
- Oldest to die: **74**
- Youngest to survive: **< 1 (0.42)**
- Youngest to die: **1**

## Question 1
Were social-economic standing a factor in survival rate?

**In [285]:**

{% highlight python %}
# Returns survival rate/percentage of sex and class
def survival_rate(pclass, sex):
    """
    Args:
        pclass: class value 1,2 or 3
        sex: male or female
    Returns:
        survival rate as percentage.
    """
    grouped_by_total = titanic_data_cleaned.groupby(['Pclass', 'Sex']).size()[pclass,sex].astype('float')
    grouped_by_survived_sex = \
        titanic_data_cleaned.groupby(['Pclass','Survived','Sex']).size()[pclass,1,sex].astype('float')
    survived_sex_pct = (grouped_by_survived_sex / grouped_by_total * 100).round(2)

    return survived_sex_pct
{% endhighlight %}

**In [286]:**

{% highlight python %}
# Get the actual numbers grouped by class, suvival and sex
groupedby_class_survived_size = titanic_data_cleaned.groupby(['Pclass','Survived','Sex']).size()

# Print - Grouped by class, survival and sex
print groupedby_class_survived_size
print 'Class 1 - female survival rate: {}%'.format(survival_rate(1,'female'))
print 'Class 1 - male survival rate: {}%'.format(survival_rate(1,'male'))
print '-----'
print 'Class 2 - female survival rate: {}%'.format(survival_rate(2,'female'))
print 'Class 2 - male survival rate: {}%'.format(survival_rate(2,'male'))
print '-----'
print 'Class 3 - female survival rate: {}%'.format(survival_rate(3,'female'))
print 'Class 3 - male survival rate: {}%'.format(survival_rate(3,'male'))

# Graph - Grouped by class, survival and sex
g = sns.factorplot(x="Sex", y="Survived", col="Pclass", data=titanic_data_cleaned,
                   saturation=.5, kind="bar", ci=None, size=5, aspect=.8)

# Fix up the labels
(g.set_axis_labels('', 'Survival Rate')
     .set_xticklabels(["Men", "Women"])
     .set_titles("Class {col_name}")
     .set(ylim=(0, 1))
     .despine(left=True, bottom=True))

# Graph - Actual count of passengers by survival, group and sex
g = sns.factorplot('Survived', col='Sex', hue='Pclass', data=titanic_data_cleaned, kind='count', size=7, aspect=.8)

# Fix up the labels
(g.set_axis_labels('Suvivors', 'No. of Passengers')
    .set_xticklabels(["False", "True"])
    .set_titles('{col_name}')
)

titles = ['Men', 'Women']
for ax, title in zip(g.axes.flat, titles):
    ax.set_title(title)
{% endhighlight %}

    Pclass  Survived  Sex   
    1       0         female      3
                      male       77
            1         female     91
                      male       45
    2       0         female      6
                      male       91
            1         female     70
                      male       17
    3       0         female     72
                      male      300
            1         female     72
                      male       47
    dtype: int64
    Class 1 - female survival rate: 96.81%
    Class 1 - male survival rate: 36.89%
    -----
    Class 2 - female survival rate: 92.11%
    Class 2 - male survival rate: 15.74%
    -----
    Class 3 - female survival rate: 50.0%
    Class 3 - male survival rate: 13.54%



![png]({{ site.baseurl }}/assets/posts/titanicdata_files/titanicdata_28_1.png)



![png]({{ site.baseurl }}/assets/posts/titanicdata_files/titanicdata_28_2.png)


Based on the raw numbers it would appear as though passengers in Class 3 had a similar survival rate as those from Class 1 with **119 and 136 passengers surviving respectively.** However, looking at the percentages of the overall passengers per class and the total numbers across each class, it can be assumed that **a passenger from Class 1 is about 2.5x times more likely to survive than a passenger in Class 3.**

Social-economic standing was a factor in survival rate of passengers.

- Class 1: **62.96%**
- Class 2: **47.28%**
- Class 3: **24.24%**

## Question 2
Did age, regardless of sex and class, determine your chances of survival?

**In [287]:**

{% highlight python %}
# Let us first identify and get rid of records with missing Age
print 'Number of men and woman with age missing are {} and {} respectively'.format(
missing_age_female.sum(),missing_age_male.sum())

# Drop the NaN values. Calculations will be okay with them (seen as zero) but will throw off averages and counts
titanic_data_age_cleaned = titanic_data_cleaned.dropna()

# Find total count of survivors and those who didn't
number_survived = titanic_data_age_cleaned[titanic_data_age_cleaned['Survived'] == True]['Survived'].count()
number_died = titanic_data_age_cleaned[titanic_data_age_cleaned['Survived'] == False]['Survived'].count()

# Find average of survivors and those who didn't
mean_age_survived = titanic_data_age_cleaned[titanic_data_age_cleaned['Survived'] == True]['Age'].mean()
mean_age_died = titanic_data_age_cleaned[titanic_data_age_cleaned['Survived'] == False]['Age'].mean()

# Display a few raw totals
print 'Total number of survivors {} \n\
Total number of non survivors {} \n\
Mean age of survivors {} \n\
Mean age of non survivors {} \n\
Oldest to survive {} \n\
Oldest to not survive {}' \
.format(number_survived, number_died, np.round(mean_age_survived),
        np.round(mean_age_died), oldest_to_survive, oldest_to_die)

# Graph - Age of passengers across sex of those who survived
g = sns.factorplot(x="Survived", y="Age", hue='Sex', data=titanic_data_age_cleaned, kind="box", size=7, aspect=.8)

# Fix up the labels
(g.set_axis_labels('Suvivors', 'Age of Passengers')
    .set_xticklabels(["False", "True"])
)
{% endhighlight %}

    Number of men and woman with age missing are 53 and 124 respectively
    Total number of survivors 290
    Total number of non survivors 424
    Mean age of survivors 28.0
    Mean age of non survivors 31.0
    Oldest to survive 80.0
    Oldest to not survive 74.0





    <seaborn.axisgrid.FacetGrid at 0x11e550810>




![png]({{ site.baseurl }}/assets/posts/titanicdata_files/titanicdata_31_2.png)


Based on the above boxplot and calculated data, it would appear that:
- Regardless of sex and class, **age was not** a deciding factor in the passenger survival rate
- Average age for those who survived and even those who did not survive were inline with eachother

## Question 3
Did women and children have preference to lifeboats and therefore survival (assuming there was no shortage of lifeboats)?

**Assumption:** With "child" not classified in the data, I'll need to assume a cutoff point. Therefore, I'll be using today's standard of under 18 as those to be considered as a child vs adult

**In [288]:**

{% highlight python %}
# Create Cateogry column and categorize people
titanic_data_age_cleaned.loc[
    ( (titanic_data_age_cleaned['Sex'] == 'female') &
    (titanic_data_age_cleaned['Age'] >= 18) ),
    'Category'] = 'Woman'

titanic_data_age_cleaned.loc[
    ( (titanic_data_age_cleaned['Sex'] == 'male') &
    (titanic_data_age_cleaned['Age'] >= 18) ),
    'Category'] = 'Man'

titanic_data_age_cleaned.loc[
    (titanic_data_age_cleaned['Age'] < 18),
    'Category'] = 'Child'

# Get the totals grouped by Men, Women and Children, and by survival
print titanic_data_age_cleaned.groupby(['Category','Survived']).size()

# Graph - Compare survival count between Men, Women and Children
g = sns.factorplot('Survived', col='Category', data=titanic_data_age_cleaned, kind='count', size=7, aspect=.8)

# Fix up the labels
(g.set_axis_labels('Suvivors', 'No. of Passengers')
    .set_xticklabels(['False', 'True'])
)

titles = ['Men', 'Women', 'Children']
for ax, title in zip(g.axes.flat, titles):
    ax.set_title(title)
{% endhighlight %}

    Category  Survived
    Child     0            52
              1            61
    Man       0           325
              1            70
    Woman     0            47
              1           159
    dtype: int64



![png]({{ site.baseurl }}/assets/posts/titanicdata_files/titanicdata_34_1.png)


The data, and more so, the graphs tends to support the idea that "women and children first" possibly played a role in the survival of a number of people. It's a bit surprising that more children didn't survive but this could possibly be attributed to the mis-representation of what age is considered as the cut off for adults - i.e. if in the 1900's someone 15-17 were considered adults, they would not have been "saved" under the "women and children first" idea and would be made to fend for themselves. That would in turn, change the outcome of the above data and possible increase the number of children who survived.

## Question 4
Did women with children have a better survival rate vs women without children (adults 18+)?
- "Women with children" is referring to parents only

**In [289]:**

{% highlight python %}
# Determine number of woman that are not parents
titanic_data_woman_parents = titanic_data_age_cleaned.loc[
    (titanic_data_age_cleaned['Category'] == 'Woman') &
    (titanic_data_age_cleaned['Parch'] > 0)]

# Determine number of woman over 20 that are not parents
titanic_data_woman_parents_maybe = titanic_data_age_cleaned.loc[
    (titanic_data_age_cleaned['Category'] == 'Woman') &
    (titanic_data_age_cleaned['Parch'] > 0) &
    (titanic_data_age_cleaned['Age'] > 20)]
{% endhighlight %}

**In [290]:**

{% highlight python %}
titanic_data_woman_parents.head()
{% endhighlight %}




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Sex</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>8</th>
      <td>1</td>
      <td>3</td>
      <td>female</td>
      <td>27.0</td>
      <td>0</td>
      <td>2</td>
      <td>Woman</td>
    </tr>
    <tr>
      <th>25</th>
      <td>1</td>
      <td>3</td>
      <td>female</td>
      <td>38.0</td>
      <td>1</td>
      <td>5</td>
      <td>Woman</td>
    </tr>
    <tr>
      <th>88</th>
      <td>1</td>
      <td>1</td>
      <td>female</td>
      <td>23.0</td>
      <td>3</td>
      <td>2</td>
      <td>Woman</td>
    </tr>
    <tr>
      <th>98</th>
      <td>1</td>
      <td>2</td>
      <td>female</td>
      <td>34.0</td>
      <td>0</td>
      <td>1</td>
      <td>Woman</td>
    </tr>
    <tr>
      <th>136</th>
      <td>1</td>
      <td>1</td>
      <td>female</td>
      <td>19.0</td>
      <td>0</td>
      <td>2</td>
      <td>Woman</td>
    </tr>
  </tbody>
</table>
</div>



**In [291]:**

{% highlight python %}
titanic_data_woman_parents_maybe.head()
{% endhighlight %}




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Sex</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>8</th>
      <td>1</td>
      <td>3</td>
      <td>female</td>
      <td>27.0</td>
      <td>0</td>
      <td>2</td>
      <td>Woman</td>
    </tr>
    <tr>
      <th>25</th>
      <td>1</td>
      <td>3</td>
      <td>female</td>
      <td>38.0</td>
      <td>1</td>
      <td>5</td>
      <td>Woman</td>
    </tr>
    <tr>
      <th>88</th>
      <td>1</td>
      <td>1</td>
      <td>female</td>
      <td>23.0</td>
      <td>3</td>
      <td>2</td>
      <td>Woman</td>
    </tr>
    <tr>
      <th>98</th>
      <td>1</td>
      <td>2</td>
      <td>female</td>
      <td>34.0</td>
      <td>0</td>
      <td>1</td>
      <td>Woman</td>
    </tr>
    <tr>
      <th>167</th>
      <td>0</td>
      <td>3</td>
      <td>female</td>
      <td>45.0</td>
      <td>1</td>
      <td>4</td>
      <td>Woman</td>
    </tr>
  </tbody>
</table>
</div>



After reviewing the data, and giving it a bit more thought, I noticed a issue which I didn't think of before i.e **A woman with Age: 23 and Parch: 2 could be onboard with her children OR onboard with her parents.** Based on the 'Parch' definition provided in the data description, *Parch - number of parents or children on board*, I don't believe it's possible to accurately determine women with children (parents) vs women with their parents onboard.

## Question 5
How did children with nannies fare in comparison to children with parents. Did the nanny "abandon" children to save his/her own life?
 - Need to review list for children with no parents. These will be children with nannies as stated in the data description
 - Compare "normal" survival rate of children with parents against children with nannies

Assumptions:
1. If you're classified as a 'Child' (under 18) and have Parch > 0, then the value is associated to your Parents,  eventhough it is possible to be under 18 and also have children
2. Classifying people as 'Child' represented by those under 18 years old is applying today's standards to the 1900 century

**In [292]:**

{% highlight python %}
# Separate out children with parents from those with nannies
titanic_data_children_nannies = titanic_data_age_cleaned.loc[
    (titanic_data_age_cleaned['Category'] == 'Child') &
    (titanic_data_age_cleaned['Parch'] == 0)]

titanic_data_children_parents = titanic_data_age_cleaned.loc[
    (titanic_data_age_cleaned['Category'] == 'Child') &
    (titanic_data_age_cleaned['Parch'] > 0)]
{% endhighlight %}

**In [307]:**

{% highlight python %}
# Determine children with nannies who survived and who did not
survived_children_nannies = titanic_data_children_nannies.Survived.sum()
total_children_nannies = titanic_data_children_nannies.Survived.count()
pct_survived_nannies = ((float(survived_children_nannies)/total_children_nannies)*100)
pct_survived_nannies = np.round(pct_survived_nannies,2)
survived_children_nannies_avg_age = np.round(titanic_data_children_nannies.Age.mean())

# Display results
print 'Total number of children with nannies: {}\n\
Children with nannies who survived: {}\n\
Children with nannies who did not survive: {}\n\
Percentage of children who survived: {}%\n\
Average age of surviving children: {}'\
.format(total_children_nannies, survived_children_nannies,
        total_children_nannies-survived_children_nannies, pct_survived_nannies, survived_children_nannies_avg_age)

# Verify counts (looked a bit too evenly divided)
titanic_data_children_nannies.loc[titanic_data_children_nannies['Survived'] == 1]
{% endhighlight %}

    Total number of children with nannies: 32
    Children with nannies who survived: 16
    Children with nannies who did not survive: 16
    Percentage of children who survived: 50.0%
    Average age of surviving children: 15.0





<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Sex</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>9</th>
      <td>1</td>
      <td>2</td>
      <td>female</td>
      <td>14.0</td>
      <td>1</td>
      <td>0</td>
      <td>Child</td>
    </tr>
    <tr>
      <th>22</th>
      <td>1</td>
      <td>3</td>
      <td>female</td>
      <td>15.0</td>
      <td>0</td>
      <td>0</td>
      <td>Child</td>
    </tr>
    <tr>
      <th>39</th>
      <td>1</td>
      <td>3</td>
      <td>female</td>
      <td>14.0</td>
      <td>1</td>
      <td>0</td>
      <td>Child</td>
    </tr>
    <tr>
      <th>84</th>
      <td>1</td>
      <td>2</td>
      <td>female</td>
      <td>17.0</td>
      <td>0</td>
      <td>0</td>
      <td>Child</td>
    </tr>
    <tr>
      <th>125</th>
      <td>1</td>
      <td>3</td>
      <td>male</td>
      <td>12.0</td>
      <td>1</td>
      <td>0</td>
      <td>Child</td>
    </tr>
    <tr>
      <th>156</th>
      <td>1</td>
      <td>3</td>
      <td>female</td>
      <td>16.0</td>
      <td>0</td>
      <td>0</td>
      <td>Child</td>
    </tr>
    <tr>
      <th>208</th>
      <td>1</td>
      <td>3</td>
      <td>female</td>
      <td>16.0</td>
      <td>0</td>
      <td>0</td>
      <td>Child</td>
    </tr>
    <tr>
      <th>220</th>
      <td>1</td>
      <td>3</td>
      <td>male</td>
      <td>16.0</td>
      <td>0</td>
      <td>0</td>
      <td>Child</td>
    </tr>
    <tr>
      <th>307</th>
      <td>1</td>
      <td>1</td>
      <td>female</td>
      <td>17.0</td>
      <td>1</td>
      <td>0</td>
      <td>Child</td>
    </tr>
    <tr>
      <th>389</th>
      <td>1</td>
      <td>2</td>
      <td>female</td>
      <td>17.0</td>
      <td>0</td>
      <td>0</td>
      <td>Child</td>
    </tr>
    <tr>
      <th>504</th>
      <td>1</td>
      <td>1</td>
      <td>female</td>
      <td>16.0</td>
      <td>0</td>
      <td>0</td>
      <td>Child</td>
    </tr>
    <tr>
      <th>777</th>
      <td>1</td>
      <td>3</td>
      <td>female</td>
      <td>5.0</td>
      <td>0</td>
      <td>0</td>
      <td>Child</td>
    </tr>
    <tr>
      <th>780</th>
      <td>1</td>
      <td>3</td>
      <td>female</td>
      <td>13.0</td>
      <td>0</td>
      <td>0</td>
      <td>Child</td>
    </tr>
    <tr>
      <th>781</th>
      <td>1</td>
      <td>1</td>
      <td>female</td>
      <td>17.0</td>
      <td>1</td>
      <td>0</td>
      <td>Child</td>
    </tr>
    <tr>
      <th>830</th>
      <td>1</td>
      <td>3</td>
      <td>female</td>
      <td>15.0</td>
      <td>1</td>
      <td>0</td>
      <td>Child</td>
    </tr>
    <tr>
      <th>875</th>
      <td>1</td>
      <td>3</td>
      <td>female</td>
      <td>15.0</td>
      <td>0</td>
      <td>0</td>
      <td>Child</td>
    </tr>
  </tbody>
</table>
</div>



**In [304]:**

{% highlight python %}
# Determine children with parents who survived and who did not
survived_children_parents = titanic_data_children_parents.Survived.sum()
total_children_parents = titanic_data_children_parents.Survived.count()
pct_survived_parents = ((float(survived_children_parents)/total_children_parents)*100)
pct_survived_parents = np.round(pct_survived_parents,2)
survived_children_parents_avg_age = np.round(titanic_data_children_parents.Age.mean())

# Display results
print 'Total number of children with parents: {}\n\
Children with parents who survived: {}\n\
Children with parents who did not survive: {}\n\
Percentage of children who survived: {}%\n\
Average age of surviving children: {}'\
.format(total_children_parents, survived_children_parents,
        total_children_parents-survived_children_parents, pct_survived_parents,survived_children_parents_avg_age)
{% endhighlight %}

    Total number of children with parents: 81
    Children with parents who survived: 45
    Children with parents who did not survive: 36
    Percentage of children who survived: 55.56%
    Average age of surviving children: 7.0


Based on the data analysis above, it would appear that the survival rate for children who were accompanied by parents vs those children accompanied by nannies was slighly higher for those with parents. The slight increase could be due to the average age of children with parents being younger, almost half, that of children with nannies.

- Percentage of children with nannies who survived: **50.0%**
- Percentage of children with parents who survived: **55.56%**
- Average age of surviving children with nannies: **15**
- Average age of surviving children with parents: **7.0**

## Conclusion

The results of the analysis, although tentative, would appear to indicate that class and sex, namely, being a female with upper social-economic standing (first class), would give one the best chance of survival when the tragedy occurred on the Titanic. Age did not seem to be a major factor. While being a man in third class, gave one the lowest chance of survival. Women and children, across all classes, tend to have a higher survival rate than men in genernal but by no means did being a child or woman guarentee survival. Although, overall, children accompanied by parents (or nannies) had the best survival rate at over 50%.

**Issues:**
- A portion of men and women did not have Age data and were removed from calculations which could have skewed some numbers
- The category of 'children' was assumed to be anyone under the age of 18, using today's North American standard for adulthood which was certainly not the case in the 1900s

## References

- https://www.kaggle.com/c/titanic/data
- http://nbviewer.jupyter.org/github/jvns/pandas-cookbook/tree/master/cookbook/
- https://stanford.edu/~mwaskom/software/seaborn/generated/seaborn.factorplot.html#seaborn.factorplot
- http://www.ncbi.nlm.nih.gov/pmc/articles/PMC3865739/

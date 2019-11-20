---
layout: post
title:      "Helpful Data Cleaning and Linear Regression Functions"
date:       2019-11-20 07:44:10 +0000
permalink:  helpful_data_cleaning_and_linear_regression_functions
---


While working on my Module 1 project, the King County Linear Regression project, I wrote some functions to automate and simplify certain tasks. I thought these might be useful to me in the future, and maybe even to other people. In this post I'll walk through some of them. 

## Value Counts Report
The first one is a dataframe reporting function I wrote that helps us analyze the unique values and their counts in each column of the dataframe. You pass in a dataframe and a number 'n'. It will list the first, second...nth most common value in each column, as well as the percentage of the columns values that it comprises. In this way, you can immediately see if a column has a large number of a certain value, and then explore further to see if maybe its a placeholder or otherwise erroneous. It also lists the percent of missing values and the datatype of the column, just to have that info in one handy place. 

```
def report1 (dataframe,n_highest_counts):
    ''' Returns a dataframe reporting on the value counts of input frame. '''
    
    master={}
        
    for column in dataframe.columns:
        
        master[column]={}
        col_dict = master[column]
        col_dict['type'] = str(dataframe[column].dtypes)
        col_dict['% empty'] = round(((len(dataframe)-dataframe[column].count())/len(dataframe))*100,2)
        col_dict['unique values'] = dataframe[column].nunique()
        
        x = 1
        series1 = dataframe[column].value_counts().head(n_highest_counts)
        series1 = round((series1/len(dataframe)) * 100, 2)        
        
        for index,item in series1.items():
            value_prop = str(x) + 'nth_value_%'
            value_name = str(x) + 'nth_value'
            col_dict[value_name] = index
            col_dict[value_prop] = item
            x += 1
        
    df_report=pd.DataFrame.from_dict(master,orient='index')
    df_report.sort_values(['1nth_value_%'],ascending=False,inplace=True)
        
    return df_report
```

## Data Pre-processing functions
The following are functions that help with different parts of the pre-processing workflow. Some of them are fairly simple. But at the end, there is a master pre-processing function that calls on each of these. This master function takes in a dictionary tht contains the arguments to be passed into each of the sub-functions. In this way, we can quickly reprocess our data according to different specifications by simply modifying the dictionary and passing it into the master function. I define the dictionary at the outset, then add lists of columns to it as I go. It's kind of like an API. 

`processing_dict = {}`

### Outlier Removal 

Here are a couple of functions that make it easy to remove outliers. In the first one, you can pass a dataframe and a dictionary where the keys are the names of columns and the values are the highest columns values we want to retain for that column. All values above that value will be dropped. 

The second function is less specific. This function  takes in a dataframe, a list of column names and float value between 0 and 1 that represents the percentile threshold above which all values in the listed columns are to be removed. 

Note that both of these functions make sure that they find rows with outliers in multiple columns. Outliers often occur together in the same tow.  These functions wont remove rows with outlying values in one column, then remove outliers from the remaining rows, and so on. This is ensures that the least number of rows will be dropped. 

The last function is related to the second function. It can be used to chart the data loss that occurs for a range of possible threshold values. This way we can see what the optimum percentile level is that removes the most outliers while removing the fewest rows. 

```
def rm_outliers_dict (dataframe, culling_dict):
    ''' Remove values above specified threshold specified for each column'''
    outliers_list = []
    for col in culling_dict.keys():
        outlier_indices = list(dataframe[dataframe[col] >= culling_dict[col]].index)
        outliers_list = outliers_list + outlier_indices
        
    unique_set = set(outliers_list)
    outliers_list = list(unique_set)
    
    dataframe = dataframe.drop(labels=outliers_list)        
    
    return dataframe

def rm_outliers_threshold (dataframe, columns, threshold):
    '''Removes values above specified quantile threshold for all columns passed as list'''
    outliers_list = []
    for col in columns:
        outlier_indices = list(dataframe[dataframe[col] >= dataframe[col].quantile(threshold)].index)
        outliers_list = outliers_list + outlier_indices
        
    unique_set = set(outliers_list)
    outliers_list = list(unique_set)
    
    dataframe = dataframe.drop(labels=outliers_list) 
    return dataframe

def cull_report (dataframe,columns,base_threshold,df_base):
    '''Returns report frame describing row loss from base frame per increment of threshold'''
    
    report_dict = {}
        
    y = int(base_threshold * 100)
    
    for x in range (100,y,-1):
        
        x = float(x/100)
        
        df = rm_outliers_threshold(dataframe,columns,x)
        loss_metrics = row_loss(df_base,df)
        report_dict[str(x)] = loss_metrics
    
    report_df = pd.DataFrame.from_dict(report_dict,orient='index',columns=['rows_dropped','rows_left','row_loss_perc'])
    
    return report_df
```

### Normalizing columns. 

Simple function to normalize columns. Takes in dataframe and list of columns to normalize. 

```

def logarize (dataframe,columns):
    
    df = dataframe
    
    for col in columns:
        df[col] =df[col].map(lambda x: np.log(x))
    
    return df
```


### Listing features with highest co-linearity levels. 

Another quick little function that takes a dataframe and a threshold value and returns a series consisting of pairs of columns in the dataframe with a Pearson correlation co-efficient higher than the specified threshold. The column pairs are displayed as elements of a multilevel index. This makes it easy to see which featur seems to co-relate the most with others. Any feature that has a lot of features listed in the second index level imemdiately jumps out as a highly co-linear feature, as well as any feature that occurs frequently on both index levels. 

 ```
def high_corr(dataframe,threshold):
    '''returns multi indexed series of feature pairs with correlation above specified threshold'''
    corr = dataframe.corr()
    sign_corr = corr[abs(corr) > threshold]
    sign_corr = sign_corr.stack()
    sign_corr.drop_duplicates(inplace=True)
    sign_corr = sign_corr[sign_corr != 1]
    
    
    return sign_corr

display(high_corr(df2,0.75))
```

Be sure to pass a list of the features you identify to the processing_dict!


### Categorical Variables

The function below takes in a dataframe and a list of columns. For each column, it generates a dummy variable dataframe and joins it to the original dataframe. The first variable is dropped. 


```
def category_frame (dataframe,categ_cols):
    for col in categ_cols:
        cat_frame = pd.get_dummies(dataframe[col],drop_first=True)
        cat_frame = cat_frame.astype('int64')
        
        dataframe = dataframe.merge(cat_frame,left_index=True,right_index=True)        
        dataframe.fillna(0)
    return dataframe
```

### Scaling. 

Two functions. One scales a series, the other scaled the specified columns in a given dataframe using the first one. 

```
def min_max_col (series):
    scaled = (series - min(series)) / (max(series) - min(series))
    return scaled

def df_scaler (dataframe,col_list):
    for col in col_list:
        dataframe[col] = min_max_col(dataframe[col])
    return dataframe
```

### Consolidated Processing Function

And now for the master function. It will remove outliers (using either outlier removal function described above), normalize specified columns, remove specified co-linear columns, create dummy variables for the columns specified, scale the columns specified and return a nice squeaky clean dataframe all in one go. 

```
def pre_process (dataframe,dictionary):
    
    dict = dictionary
    
    dataframe = rm_outliers_dict(dataframe,dict['categ_culled'])
    
    dataframe = rm_outliers_threshold(dataframe,dict['contin_cull'],dict['contin_cull_thresh'])
    
    dataframe = logarize(dataframe,dict['cols_normed'])
    
    dataframe = dataframe.drop(dict['colinear_columns'],axis=1)
    
    dataframe = category_frame(dataframe,dict['categ_cols'])
    
		dict['scaled_cols'] = list(dataframe.columns)
		
    for col in categ_cols:
        if col in dictionary['scaled_cols']:
            dictionary['scaled_cols'].remove(col)
    
    dataframe = df_scaler(dataframe,dict['scaled_cols'])
            
    return dataframe
```


Bear in mind that the dictionary we pass it into must confirm to the template below:

```
{   'categ_cols': ['zipcode', 'waterfront'],
    'categ_culled': {'bathrooms': 6, 'bedrooms': 15},
    'colinear_columns': ['sqft_above', 'sqft_lot15'],
    'cols_normed': [   'sqft_above',
                       'sqft_living15',
                       'sqft_lot',
                       'sqft_lot15',
                       'sqft_living',
                       'price',
                       'price',
                       'price'],
    'contin_cull': [   'sqft_lot15',
                       'price',
                       'sqft_lot',
                       'sqft_living15',
                       'bedrooms'],
    'contin_cull_thresh': 0.97,
    'scaled_cols': [   'bedrooms',
                       'bathrooms',
                       'sqft_living',
                       'sqft_lot',
                       'floors',
                       'waterfront',
                       'view',
                       'condition',
                       'grade',
                       'sqft_basement',
                       'yr_built',
                       'lat',
                       'long',
                       'sqft_living15',
                       ]}
```






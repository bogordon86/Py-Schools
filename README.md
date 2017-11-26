#PyCity Schools Analysis

#Trend #1 Overall, all schools performed better in reading (100%) rather than Math (92%)

#Trend #2 The charter schools performed better at math and reading than the district schools

#Trend #3 Performance did not correlate to amount spent per student, but to the size of the school and type.  The smaller schools, which were all charter schools, performed better.

```python
#Get Dependencies for working with csv
import os
import pandas as pd
import numpy as np
```


```python
#Import first csv and set the index to 'School_Name'
schools_pd = pd.read_csv('schools_complete.csv', index_col='School_Name')
```


```python
#Import 2nd csv
students_pd = pd.read_csv('students_complete.csv', index_col='School_Name')
students2_pd = students_pd

```


```python
#Find the info for the first cell in table = total number of schools in District
Total_Schools = len(schools_pd.index.values)
```


```python
#Calculate info for the second cell in table = total number of students in District (sum of school sizes)
Total_Students = sum(schools_pd.loc[:,'size'])
```


```python
#Calculate info for the third cell in table = total budget for the District
Total_Budget = sum(schools_pd.loc[:,'budget'])

```


```python
#Calculate the Average Math Score for all students in the District
Avg_Math_Score = students_pd['math_score'].mean()

```


```python
#Calculate the Average Reading Score for all students in the District
Avg_Reading_Score = students_pd['reading_score'].mean()
```


```python
#Calculate the Percentage of Students Passing Math in the District
Perc_Passing_Math = len(students_pd.loc[students_pd['math_score'] >=60])/(Total_Students)*100

```


```python
#Calculate the Percentage of Students Passing Reading in the District
Perc_Passing_Reading =len(students_pd.loc[students_pd['reading_score'] >=60])/(Total_Students)*100

```


```python
#Calculate the Overall Percentage of Students Passing Math and Reading in District
Overall_Passing_Perc = ((Perc_Passing_Math) + (Perc_Passing_Reading))/2


```


```python
#Create Summary Table for the District
District_Summary = pd.DataFrame({"Total Schools": [Total_Schools],
                                   "Total Students": [Total_Students],
                                   "Total Budget": [Total_Budget],
                                   "Average Math Score": [Avg_Math_Score],
                                   "Average Reading Score": [Avg_Reading_Score],
                                   "% Passing Math":[Perc_Passing_Math],
                                   "% Passing Reading":[Perc_Passing_Reading],
                                   "% Overall Passing Rate": [Overall_Passing_Perc]
})
#Refine the Summary Table in the Second Pass-Through
District_Summary = District_Summary[["Total Schools",
                                   "Total Students",
                                   "Total Budget",
                                   "Average Math Score",
                                   "Average Reading Score",
                                   "% Passing Math",
                                   "% Passing Reading",
                                   "% Overall Passing Rate"]]
District_Summary = District_Summary.round(2)

District_Summary
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
      <th>Total Schools</th>
      <th>Total Students</th>
      <th>Total Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>15</td>
      <td>39170</td>
      <td>24649428</td>
      <td>78.99</td>
      <td>81.88</td>
      <td>92.45</td>
      <td>100.0</td>
      <td>96.22</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Create a new pandas Dataframe from the students Dataframe
school_summary_pd = schools_pd
```


```python
#Remove unnecessary columns
school_summary_pd = school_summary_pd.drop('School_ID', axis='columns')  
school_summary_pd = school_summary_pd.drop('size', axis='columns')
```


```python
#Create a sub-dataframe which stores the number of students who passed reading (>=60)
ds_passed_reading_pd = students_pd.loc[students_pd['reading_score'] >= 60] 
```


```python
#Create a sub-dataframe which stores the number of students passing math from District #(>=60)
ds_passed_math_pd = students_pd.loc[students_pd['math_score'] >= 60]
```


```python
#Groupby School Name and Calculate the Averages
ss_gb_students_pd = students_pd.groupby(["School_Name"])
ss_gb_students = ss_gb_students_pd.mean()
```


```python
#Extract Avg Reading Score Column and Add to Summary Dataframe
ss_avg_reading_score = ss_gb_students['reading_score']                       
school_summary_pd['Average Reading Score'] = ss_avg_reading_score            
```


```python
#Extract Avg Math Score Column and add to Summary Dataframe
ss_avg_math_score = ss_gb_students['math_score']                             
school_summary_pd['Average Math Score'] = ss_avg_math_score         
```


```python
#Count the Students and Extract student Count to Add to Summary Dataframe
ss_gb_students_count = ss_gb_students_pd.count() 
ss_students_count = ss_gb_students_count['Student_Name'] 
school_summary_pd['Total Students'] = ss_students_count  
```


```python
#Grab Dataframe from District Summary, Count students who passed Reading, and Get Total Student Count
ss_gb_students_passr_pd = ds_passed_reading_pd.groupby(["School_Name"]) 
    
ss_gb_students_passed_read = ss_gb_students_passr_pd.count()    

students_all = school_summary_pd["Total Students"] 
```


```python
#Calculates students who passed reading percent and Adds to Dataframe
ss_students_passed_read = ss_gb_students_passed_read['reading_score'] / students_all * 100
school_summary_pd['% Passing Reading'] = ss_students_passed_read         
```


```python
#Grab Dataframe from District Summary, counts students who passed Math, 
ss_gb_students_passm_pd = ds_passed_math_pd.groupby(["School_Name"])       
ss_gb_students_passed_math = ss_gb_students_passm_pd.count()      

# Calculates students who passed math percent and adds to Summary Dataframe
ss_students_passed_math = ss_gb_students_passed_math['math_score'] / students_all * 100
school_summary_pd['% Passing Math'] = ss_students_passed_math          
```


```python
#Extracts passed reading and passed math columns, calculates overall passed %, and adds to Summary Dataframe
ss_passed_reading = school_summary_pd['% Passing Reading']       
ss_passed_math = school_summary_pd['% Passing Math']    
ss_overall_passed = (ss_passed_reading + ss_passed_math) / 2  
school_summary_pd['% Overall Passing Rate'] = ss_overall_passed  


```


```python
#Extracts Total Budget Column, Calculates Per Student Budget, and Adds to Summary Dataframe
ss_total_budget = school_summary_pd['budget']           
ss_budget_per_student = ss_total_budget / students_all 
school_summary_pd['Per Student Budget'] = ss_budget_per_student      
```


```python
# Rename columns from original dataframe
school_summary_pd.rename(columns = {'type':'School Type'}, inplace = True)
school_summary_pd.rename(columns = {'budget': 'Total School Budget'}, inplace = True)
school_summary_pd = school_summary_pd[["School Type",                        # Reorders columns
                                       "Total Students",
                                       "Total School Budget",
                                       "Per Student Budget",
                                       "Average Math Score",
                                       "Average Reading Score",
                                       "% Passing Math",
                                       "% Passing Reading",
                                       "% Overall Passing Rate"]]

```


```python
# Formats cells for display
school_summary2_pd = school_summary_pd
school_summary2_pd["Total Students"] = school_summary2_pd["Total Students"].map("{0:,.0f}".format)
school_summary2_pd["Total School Budget"] = school_summary2_pd["Total School Budget"].map("${0:,.2f}".format)
school_summary2_pd["Per Student Budget"] = school_summary2_pd["Per Student Budget"].map("${0:,.2f}".format)
school_summary2_pd["Average Math Score"] = school_summary2_pd["Average Math Score"].map("{0:,.6f}".format)
school_summary2_pd["Average Reading Score"] = school_summary2_pd["Average Reading Score"].map("{0:,.6f}".format)
school_summary2_pd["% Passing Math"] = school_summary2_pd["% Passing Math"].map("{0:,.6f}".format)
school_summary2_pd["% Passing Reading"] = school_summary2_pd["% Passing Reading"].map("{0:,.6f}".format)
school_summary2_pd["% Overall Passing Rate"] = school_summary2_pd["% Overall Passing Rate"].map("{0:,.6f}".format)

del school_summary2_pd.index.name                                             # Drops index name (not needed)
school_summary2_pd                                                            # Displays dataframe (table)
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
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Huang High School</th>
      <td>District</td>
      <td>2,917</td>
      <td>$1,910,635.00</td>
      <td>$655.00</td>
      <td>76.629414</td>
      <td>81.182722</td>
      <td>88.858416</td>
      <td>100.000000</td>
      <td>94.429208</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>District</td>
      <td>2,949</td>
      <td>$1,884,411.00</td>
      <td>$639.00</td>
      <td>76.711767</td>
      <td>81.158020</td>
      <td>88.436758</td>
      <td>100.000000</td>
      <td>94.218379</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>Charter</td>
      <td>1,761</td>
      <td>$1,056,600.00</td>
      <td>$600.00</td>
      <td>83.359455</td>
      <td>83.725724</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>District</td>
      <td>4,635</td>
      <td>$3,022,020.00</td>
      <td>$652.00</td>
      <td>77.289752</td>
      <td>80.934412</td>
      <td>89.083064</td>
      <td>100.000000</td>
      <td>94.541532</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>Charter</td>
      <td>1,468</td>
      <td>$917,500.00</td>
      <td>$625.00</td>
      <td>83.351499</td>
      <td>83.816757</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>Charter</td>
      <td>2,283</td>
      <td>$1,319,574.00</td>
      <td>$578.00</td>
      <td>83.274201</td>
      <td>83.989488</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>Charter</td>
      <td>1,858</td>
      <td>$1,081,356.00</td>
      <td>$582.00</td>
      <td>83.061895</td>
      <td>83.975780</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>Bailey High School</th>
      <td>District</td>
      <td>4,976</td>
      <td>$3,124,928.00</td>
      <td>$628.00</td>
      <td>77.048432</td>
      <td>81.033963</td>
      <td>89.529743</td>
      <td>100.000000</td>
      <td>94.764871</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>Charter</td>
      <td>427</td>
      <td>$248,087.00</td>
      <td>$581.00</td>
      <td>83.803279</td>
      <td>83.814988</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>Charter</td>
      <td>962</td>
      <td>$585,858.00</td>
      <td>$609.00</td>
      <td>83.839917</td>
      <td>84.044699</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>Charter</td>
      <td>1,800</td>
      <td>$1,049,400.00</td>
      <td>$583.00</td>
      <td>83.682222</td>
      <td>83.955000</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>District</td>
      <td>3,999</td>
      <td>$2,547,363.00</td>
      <td>$637.00</td>
      <td>76.842711</td>
      <td>80.744686</td>
      <td>88.547137</td>
      <td>100.000000</td>
      <td>94.273568</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>District</td>
      <td>4,761</td>
      <td>$3,094,650.00</td>
      <td>$650.00</td>
      <td>77.072464</td>
      <td>80.966394</td>
      <td>89.182945</td>
      <td>100.000000</td>
      <td>94.591472</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>District</td>
      <td>2,739</td>
      <td>$1,763,916.00</td>
      <td>$644.00</td>
      <td>77.102592</td>
      <td>80.746258</td>
      <td>89.302665</td>
      <td>100.000000</td>
      <td>94.651333</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>Charter</td>
      <td>1,635</td>
      <td>$1,043,130.00</td>
      <td>$638.00</td>
      <td>83.418349</td>
      <td>83.848930</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Use to check the data within each column 
#school_summary_pd.dtypes
```


```python
#Create Top 5 Schools Chart by Sorting Values
top_5_schools = school_summary_pd.sort_values(["% Overall Passing Rate"], ascending=True)
top_5_schools.head(5)
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
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Shelton High School</th>
      <td>Charter</td>
      <td>1,761</td>
      <td>$1,056,600.00</td>
      <td>$600.00</td>
      <td>83.359455</td>
      <td>83.725724</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>Charter</td>
      <td>1,468</td>
      <td>$917,500.00</td>
      <td>$625.00</td>
      <td>83.351499</td>
      <td>83.816757</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>Charter</td>
      <td>2,283</td>
      <td>$1,319,574.00</td>
      <td>$578.00</td>
      <td>83.274201</td>
      <td>83.989488</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>Charter</td>
      <td>1,858</td>
      <td>$1,081,356.00</td>
      <td>$582.00</td>
      <td>83.061895</td>
      <td>83.975780</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>Charter</td>
      <td>427</td>
      <td>$248,087.00</td>
      <td>$581.00</td>
      <td>83.803279</td>
      <td>83.814988</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Create Bottom 5 Schools Chart by Sorting Values
bottom_5_schools = school_summary_pd.sort_values(["% Overall Passing Rate"], ascending=True)
bottom_5_schools.tail(5)
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
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Huang High School</th>
      <td>District</td>
      <td>2,917</td>
      <td>$1,910,635.00</td>
      <td>$655.00</td>
      <td>76.629414</td>
      <td>81.182722</td>
      <td>88.858416</td>
      <td>100.000000</td>
      <td>94.429208</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>District</td>
      <td>4,635</td>
      <td>$3,022,020.00</td>
      <td>$652.00</td>
      <td>77.289752</td>
      <td>80.934412</td>
      <td>89.083064</td>
      <td>100.000000</td>
      <td>94.541532</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>District</td>
      <td>4,761</td>
      <td>$3,094,650.00</td>
      <td>$650.00</td>
      <td>77.072464</td>
      <td>80.966394</td>
      <td>89.182945</td>
      <td>100.000000</td>
      <td>94.591472</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>District</td>
      <td>2,739</td>
      <td>$1,763,916.00</td>
      <td>$644.00</td>
      <td>77.102592</td>
      <td>80.746258</td>
      <td>89.302665</td>
      <td>100.000000</td>
      <td>94.651333</td>
    </tr>
    <tr>
      <th>Bailey High School</th>
      <td>District</td>
      <td>4,976</td>
      <td>$3,124,928.00</td>
      <td>$628.00</td>
      <td>77.048432</td>
      <td>81.033963</td>
      <td>89.529743</td>
      <td>100.000000</td>
      <td>94.764871</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Grab Each grade from the Students Dataframe
ninth = students_pd[(students_pd["grade"]=="9th")]
tenth = students_pd[(students_pd["grade"]=="10th")]
eleventh = students_pd[(students_pd["grade"]=="11th")]
twelfth = students_pd[(students_pd["grade"]=="12th")]
```


```python
#Calculate Average Math Score across Grade
ninthmathscore = ninth.groupby("School_Name")['math_score'].mean()
tenthmathscore = tenth.groupby("School_Name")['math_score'].mean()
eleventhmathscore = eleventh.groupby("School_Name")['math_score'].mean()
twelfthmathscore = twelfth.groupby("School_Name")['math_score'].mean()
```


```python
#Chart and Sort Average Math Scores by Grade
mathbygrade = pd.DataFrame({"9th":ninthmathscore, "10th":tenthmathscore, "11th":eleventhmathscore, "12th":twelfthmathscore})
mathbygradesorted = mathbygrade[["9th", "10th", "11th", "12th"]]
mathbygradesorted
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
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>School_Name</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>77.083676</td>
      <td>76.996772</td>
      <td>77.515588</td>
      <td>76.492218</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.094697</td>
      <td>83.154506</td>
      <td>82.765560</td>
      <td>83.277487</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>76.403037</td>
      <td>76.539974</td>
      <td>76.884344</td>
      <td>77.151369</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>77.361345</td>
      <td>77.672316</td>
      <td>76.918058</td>
      <td>76.179963</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>82.044010</td>
      <td>84.229064</td>
      <td>83.842105</td>
      <td>83.356164</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>77.438495</td>
      <td>77.337408</td>
      <td>77.136029</td>
      <td>77.186567</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83.787402</td>
      <td>83.429825</td>
      <td>85.000000</td>
      <td>82.855422</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>77.027251</td>
      <td>75.908735</td>
      <td>76.446602</td>
      <td>77.225641</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>77.187857</td>
      <td>76.691117</td>
      <td>77.491653</td>
      <td>76.863248</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83.625455</td>
      <td>83.372000</td>
      <td>84.328125</td>
      <td>84.121547</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>76.859966</td>
      <td>76.612500</td>
      <td>76.395626</td>
      <td>77.690748</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>83.420755</td>
      <td>82.917411</td>
      <td>83.383495</td>
      <td>83.778976</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>83.590022</td>
      <td>83.087886</td>
      <td>83.498795</td>
      <td>83.497041</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>83.085578</td>
      <td>83.724422</td>
      <td>83.195326</td>
      <td>83.035794</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>83.264706</td>
      <td>84.010288</td>
      <td>83.836782</td>
      <td>83.644986</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Calculate Average Reading Score across Grade
ninthreadscore = ninth.groupby("School_Name")['reading_score'].mean()
tenthreadscore = tenth.groupby("School_Name")['reading_score'].mean()
eleventhreadscore = eleventh.groupby("School_Name")['reading_score'].mean()
twelfthreadscore = twelfth.groupby("School_Name")['reading_score'].mean()
```


```python
#Chart and Sort Average Reading Scores by Grade
readbygrade = pd.DataFrame({"9th":ninthreadscore, "10th":tenthreadscore, "11th":eleventhreadscore, "12th":twelfthreadscore})
readbygradesorted = readbygrade[["9th", "10th", "11th", "12th"]]
readbygradesorted
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
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>School_Name</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>81.303155</td>
      <td>80.907183</td>
      <td>80.945643</td>
      <td>80.912451</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.676136</td>
      <td>84.253219</td>
      <td>83.788382</td>
      <td>84.287958</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>81.198598</td>
      <td>81.408912</td>
      <td>80.640339</td>
      <td>81.384863</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>80.632653</td>
      <td>81.262712</td>
      <td>80.403642</td>
      <td>80.662338</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>83.369193</td>
      <td>83.706897</td>
      <td>84.288089</td>
      <td>84.013699</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>80.866860</td>
      <td>80.660147</td>
      <td>81.396140</td>
      <td>80.857143</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83.677165</td>
      <td>83.324561</td>
      <td>83.815534</td>
      <td>84.698795</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>81.290284</td>
      <td>81.512386</td>
      <td>81.417476</td>
      <td>80.305983</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>81.260714</td>
      <td>80.773431</td>
      <td>80.616027</td>
      <td>81.227564</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83.807273</td>
      <td>83.612000</td>
      <td>84.335938</td>
      <td>84.591160</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>80.993127</td>
      <td>80.629808</td>
      <td>80.864811</td>
      <td>80.376426</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>84.122642</td>
      <td>83.441964</td>
      <td>84.373786</td>
      <td>82.781671</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>83.728850</td>
      <td>84.254157</td>
      <td>83.585542</td>
      <td>83.831361</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>83.939778</td>
      <td>84.021452</td>
      <td>83.764608</td>
      <td>84.317673</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>83.833333</td>
      <td>83.812757</td>
      <td>84.156322</td>
      <td>84.073171</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Create bins for the Schools by Spending Data Ranges to be held
school_summary1_pd = school_summary_pd
spending_ranges = [0, 585, 615, 645, 675]
range_names = ['$0-585', '$585-615', '$615-645', '$645-675']
studentsbyspend = school_summary1_pd['Per Student Budget'].replace( '[\$,)]','', regex=True )
```


```python
#Change Data Types and Set up Dataframe to analyze Schools by Spending Ranges
studentsbyspend2 = studentsbyspend.astype(float, inplace=True)
studentsbyspend2

school_summary1_pd['Spending Ranges'] = pd.cut(studentsbyspend2, spending_ranges, labels=range_names)
school_summary1_pd['Average Math Score'] = school_summary1_pd['Average Math Score'].astype(float)
school_summary1_pd['Average Reading Score'] = school_summary1_pd['Average Reading Score'].astype(float)

#Create First Two Columns of New Table Grouped by Spending Ranges
spendmath = school_summary1_pd.groupby("Spending Ranges").mean()["Average Math Score"]
spendmath
spendread = school_summary1_pd.groupby("Spending Ranges").mean()['Average Reading Score']
```


```python
#Reset Data Types for Each Column
school_summary1_pd['% Passing Math'] = school_summary1_pd['% Passing Math'].astype(float)
school_summary1_pd['% Passing Reading'] = school_summary1_pd['% Passing Reading'].astype(float)
school_summary1_pd['% Overall Passing Rate'] = school_summary1_pd['% Overall Passing Rate'].astype(float)

#Grab the Percentages from the School Summary and Group By Spending Ranges
spend_passed_math = school_summary1_pd.groupby("Spending Ranges").mean()["% Passing Math"] 
spend_passed_reading = school_summary1_pd.groupby("Spending Ranges").mean()["% Passing Reading"]
spend_overall_passed = school_summary1_pd.groupby("Spending Ranges").mean()["% Overall Passing Rate"]

```


```python
#Create Table for Schools by Spending
spend_pd = pd.DataFrame({"Average Math Score": spendmath, "Average Reading Score": spendread, "% Passing Math": spend_passed_math, "% Passing Reading": spend_passed_reading, "% Overall Passing Rate": spend_overall_passed})
spend_pd = spend_pd[["Average Math Score",
                                       "Average Reading Score",
                                       "% Passing Math",
                                       "% Passing Reading",
                                       "% Overall Passing Rate"]]
spend_pd
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
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>Spending Ranges</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>$0-585</th>
      <td>83.455399</td>
      <td>83.933814</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>$585-615</th>
      <td>83.599686</td>
      <td>83.885211</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>$615-645</th>
      <td>79.079225</td>
      <td>81.891436</td>
      <td>92.636051</td>
      <td>100.0</td>
      <td>96.318025</td>
    </tr>
    <tr>
      <th>$645-675</th>
      <td>76.997210</td>
      <td>81.027843</td>
      <td>89.041475</td>
      <td>100.0</td>
      <td>94.520737</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Create bins for Schools by Size ranges to be held
size_range = [0, 1000, 2000, 5000]
size_rangenames = ['Small below 1000', 'Medium 1000-2000', 'Large greater than 2000']

studentsbysize = schools_pd['size'].astype(float, inplace=True)
school_summary3_pd = school_summary_pd
school_summary3_pd['School Size'] = pd.cut(studentsbysize, size_range, labels=size_rangenames)

```


```python
sizemath = school_summary3_pd.groupby("School Size").mean()["Average Math Score"]
sizeread = school_summary3_pd.groupby("School Size").mean()["Average Reading Score"]

#Fix data types for the columns to be accessed
school_summary3_pd['% Passing Reading'] = school_summary3_pd['% Passing Reading'].astype(float)
school_summary3_pd['% Passing Math'] = school_summary3_pd['% Passing Math'].astype(float)
school_summary3_pd['% Overall Passing Rate'] = school_summary3_pd['% Overall Passing Rate'].astype(float)

size_passed_math = school_summary3_pd.groupby("School Size").mean()["% Passing Math"] 
size_passed_reading = school_summary3_pd.groupby("School Size").mean()["% Passing Reading"]
size_overall_passed = school_summary3_pd.groupby("School Size").mean()["% Overall Passing Rate"]

```


```python
#Create Table for Schools by Spending
size_pd = pd.DataFrame({"Average Math Score": sizemath, "Average Reading Score": sizeread, "% Passing Math": size_passed_math, "% Passing Reading": size_passed_reading, "% Overall Passing Rate": size_overall_passed})
size_pd = size_pd[["Average Math Score",
                                       "Average Reading Score",
                                       "% Passing Math",
                                       "% Passing Reading",
                                       "% Overall Passing Rate"]]
size_pd
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
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>School Size</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Small below 1000</th>
      <td>83.821598</td>
      <td>83.929844</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>Medium 1000-2000</th>
      <td>83.374684</td>
      <td>83.864438</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>Large greater than 2000</th>
      <td>77.746417</td>
      <td>81.344493</td>
      <td>90.367591</td>
      <td>100.0</td>
      <td>95.183795</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Create bins for Schools by Type to be held

schools_by_type_pd = school_summary_pd.reset_index(level=None, drop=False, inplace=False)
```


```python
#Create first two columns for Schools by Type
typemath = school_summary_pd.groupby("School Type").mean()["Average Math Score"]
typeread = school_summary_pd.groupby("School Type").mean()["Average Reading Score"]

#Fix data types for the columns to be accessed
school_summary_pd['% Passing Reading'] = school_summary_pd['% Passing Reading'].astype(float)
school_summary_pd['% Passing Math'] = school_summary_pd['% Passing Math'].astype(float)
school_summary_pd['% Overall Passing Rate'] = school_summary_pd['% Overall Passing Rate'].astype(float)

#Create Percentages for Table Grouped by School Type
type_passed_math = school_summary_pd.groupby("School Type").mean()["% Passing Math"] 
type_passed_reading = school_summary_pd.groupby("School Type").mean()["% Passing Reading"]
type_overall_passed = school_summary_pd.groupby("School Type").mean()["% Overall Passing Rate"]
```


```python
#Create Table for Schools by Type
type_pd = pd.DataFrame({"Average Math Score": typemath, "Average Reading Score": typeread, "% Passing Math": type_passed_math, "% Passing Reading": type_passed_reading, "% Overall Passing Rate": type_overall_passed})
type_pd = type_pd[["Average Math Score",
                                       "Average Reading Score",
                                       "% Passing Math",
                                       "% Passing Reading",
                                       "% Overall Passing Rate"]]
type_pd
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
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>School Type</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Charter</th>
      <td>83.473852</td>
      <td>83.896421</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>District</th>
      <td>76.956733</td>
      <td>80.966636</td>
      <td>88.991533</td>
      <td>100.0</td>
      <td>94.495766</td>
    </tr>
  </tbody>
</table>
</div>



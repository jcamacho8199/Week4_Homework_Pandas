
# PyCity Schools Analysis

* As a whole, schools with higher budgets, did not yield better test results. By contrast, schools with higher spending per student actually (\$645-675) underperformed compared to schools with smaller budgets (<\$585 per student).

* As a whole, smaller and medium sized schools dramatically out-performed large sized schools on passing math performances (89-91% passing vs 67%).

* As a whole, charter schools out-performed the public district schools across all metrics. However, more analysis will be required to glean if the effect is due to school practices or the fact that charter schools tend to serve smaller student populations per school. 
---

### Note
* Instructions have been included for each segment. You do not have to follow them exactly, but they are included to help you think through the steps.


```python
# Dependencies and Setup
import pandas as pd
import numpy as np

# File to Load (Remember to Change These)
school_data_to_load = "Resources/schools_complete.csv"
student_data_to_load = "Resources/students_complete.csv"

# Read School and Student Data File and store into Pandas Data Frames
school_data = pd.read_csv(school_data_to_load)
school_data.columns = ['School ID','school_name','type','students_size','budget']

student_data = pd.read_csv(student_data_to_load)

# Combine the data into a single dataset
school_data_complete = pd.merge(student_data, school_data, how="left", on=["school_name", "school_name"])
```

## District Summary

* Calculate the total number of schools

* Calculate the total number of students

* Calculate the total budget

* Calculate the average math score 

* Calculate the average reading score

* Calculate the overall passing rate (overall average score), i.e. (avg. math score + avg. reading score)/2

* Calculate the percentage of students with a passing math score (70 or greater)

* Calculate the percentage of students with a passing reading score (70 or greater)

* Create a dataframe to hold the above results

* Optional: give the displayed data cleaner formatting


```python
total_schools = len(school_data_complete['school_name'].unique())
student_count = school_data_complete['student_name'].value_counts().sum()
school_budget = school_data_complete['budget'].unique().sum()
avg_math_score = school_data_complete['math_score'].mean()
avg_read_score = school_data_complete['reading_score'].mean()
math_passing_rate = (len(school_data_complete[school_data_complete["math_score"] >= 70])/student_count)*100
read_passing_rate = (len(school_data_complete[school_data_complete["reading_score"] >= 70])/student_count)*100
overall_passing_rate = (avg_math_score + avg_read_score)/2
```


```python
district_summary_df = pd.DataFrame(
    {"Total Schools": [total_schools],
     "Total Students": [student_count],
     "Total Budget": [school_budget],
     "Average Math Score": [avg_math_score],
     "Average Reading Score": [avg_read_score],
     "% Passing Math": [math_passing_rate],
     "% Passing Reading": [read_passing_rate],
     "% Overall Passing Rate": [overall_passing_rate]})  

format_district_summary_df = district_summary_df.style.format({"Total Schools":"{:,}",
                                                            "Total Students":"{:,}",
                                                            "Total Budget":"${:,.2f}",
                                                            "Average Math Score":"{:.6f}", 
                                                            "Average Reading Score":"{:.5f}",
                                                            "% Passing Math":"{:.6f}", 
                                                            "% Passing Reading":"{:.6f}", 
                                                            "% Overall Passing Rate":"{:.6f}"})

format_district_summary_df
```




<style  type="text/css" >
</style>  
<table id="T_b475d23a_afe6_11e8_994a_54bf64071706" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >Total Schools</th> 
        <th class="col_heading level0 col1" >Total Students</th> 
        <th class="col_heading level0 col2" >Total Budget</th> 
        <th class="col_heading level0 col3" >Average Math Score</th> 
        <th class="col_heading level0 col4" >Average Reading Score</th> 
        <th class="col_heading level0 col5" >% Passing Math</th> 
        <th class="col_heading level0 col6" >% Passing Reading</th> 
        <th class="col_heading level0 col7" >% Overall Passing Rate</th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_b475d23a_afe6_11e8_994a_54bf64071706level0_row0" class="row_heading level0 row0" >0</th> 
        <td id="T_b475d23a_afe6_11e8_994a_54bf64071706row0_col0" class="data row0 col0" >15</td> 
        <td id="T_b475d23a_afe6_11e8_994a_54bf64071706row0_col1" class="data row0 col1" >39,170</td> 
        <td id="T_b475d23a_afe6_11e8_994a_54bf64071706row0_col2" class="data row0 col2" >$24,649,428.00</td> 
        <td id="T_b475d23a_afe6_11e8_994a_54bf64071706row0_col3" class="data row0 col3" >78.985371</td> 
        <td id="T_b475d23a_afe6_11e8_994a_54bf64071706row0_col4" class="data row0 col4" >81.87784</td> 
        <td id="T_b475d23a_afe6_11e8_994a_54bf64071706row0_col5" class="data row0 col5" >74.980853</td> 
        <td id="T_b475d23a_afe6_11e8_994a_54bf64071706row0_col6" class="data row0 col6" >85.805463</td> 
        <td id="T_b475d23a_afe6_11e8_994a_54bf64071706row0_col7" class="data row0 col7" >80.431606</td> 
    </tr></tbody> 
</table> 



## School Summary

* Create an overview table that summarizes key metrics about each school, including:
  * School Name
  * School Type
  * Total Students
  * Total School Budget
  * Per Student Budget
  * Average Math Score
  * Average Reading Score
  * % Passing Math
  * % Passing Reading
  * Overall Passing Rate (Average of the above two)
  
* Create a dataframe to hold the above results


```python
school = school_data.school_name
school_type = school_data.type
student_count = school_data.students_size
school_budget = school_data.budget
student_budget = school_data.budget/student_count
avg_math_score = school_data_complete.groupby("School ID")["math_score"].sum()/student_count
avg_read_score = school_data_complete.groupby("School ID")["reading_score"].sum()/student_count
pct_pass_math = ((school_data_complete[school_data_complete["math_score"] >= 70].groupby("School ID")["math_score"].count())/student_count)*100
pct_pass_read = ((school_data_complete[school_data_complete["reading_score"] >= 70].groupby("School ID")["reading_score"].count())/student_count)*100
overall_pass_rate = (pct_pass_math + pct_pass_read)/2
```


```python
school_summary_df = pd.DataFrame(
    {"School": school,
    "School Type": school_type,
    "Total Students": student_count,
    "Total School Budget": school_budget,
    "Per Student Budget": student_budget,
    "Average Math Score": avg_math_score,
    "Average Reading Score": avg_read_score,
    "% Passing Math": pct_pass_math,
    "% Passing Reading": pct_pass_read,
    "% Overall Passing Rate": overall_pass_rate})  

school_summary_df = school_summary_df.dropna().set_index("School")

format_school_summary_df = school_summary_df.style.format({"Total School Budget":"${:,.2f}",
                                                        "Per Student Budget":"${:,.2f}",
                                                        "Average Math Score":"{:.6f}",
                                                        "Average Reading Score":"{:.6f}",
                                                        "% Passing Math":"{:.6f}",
                                                        "% Passing Reading":"{:.6f}",
                                                        "% Overall Passing Rate":"{:.6f}"})                                                 
format_school_summary_df.index.name = None

format_school_summary_df
```




<style  type="text/css" >
</style>  
<table id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >School Type</th> 
        <th class="col_heading level0 col1" >Total Students</th> 
        <th class="col_heading level0 col2" >Total School Budget</th> 
        <th class="col_heading level0 col3" >Per Student Budget</th> 
        <th class="col_heading level0 col4" >Average Math Score</th> 
        <th class="col_heading level0 col5" >Average Reading Score</th> 
        <th class="col_heading level0 col6" >% Passing Math</th> 
        <th class="col_heading level0 col7" >% Passing Reading</th> 
        <th class="col_heading level0 col8" >% Overall Passing Rate</th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706level0_row0" class="row_heading level0 row0" >Huang High School</th> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row0_col0" class="data row0 col0" >District</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row0_col1" class="data row0 col1" >2917</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row0_col2" class="data row0 col2" >$1,910,635.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row0_col3" class="data row0 col3" >$655.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row0_col4" class="data row0 col4" >76.629414</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row0_col5" class="data row0 col5" >81.182722</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row0_col6" class="data row0 col6" >65.683922</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row0_col7" class="data row0 col7" >81.316421</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row0_col8" class="data row0 col8" >73.500171</td> 
    </tr>    <tr> 
        <th id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706level0_row1" class="row_heading level0 row1" >Figueroa High School</th> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row1_col0" class="data row1 col0" >District</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row1_col1" class="data row1 col1" >2949</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row1_col2" class="data row1 col2" >$1,884,411.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row1_col3" class="data row1 col3" >$639.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row1_col4" class="data row1 col4" >76.711767</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row1_col5" class="data row1 col5" >81.158020</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row1_col6" class="data row1 col6" >65.988471</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row1_col7" class="data row1 col7" >80.739234</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row1_col8" class="data row1 col8" >73.363852</td> 
    </tr>    <tr> 
        <th id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706level0_row2" class="row_heading level0 row2" >Shelton High School</th> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row2_col0" class="data row2 col0" >Charter</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row2_col1" class="data row2 col1" >1761</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row2_col2" class="data row2 col2" >$1,056,600.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row2_col3" class="data row2 col3" >$600.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row2_col4" class="data row2 col4" >83.359455</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row2_col5" class="data row2 col5" >83.725724</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row2_col6" class="data row2 col6" >93.867121</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row2_col7" class="data row2 col7" >95.854628</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row2_col8" class="data row2 col8" >94.860875</td> 
    </tr>    <tr> 
        <th id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706level0_row3" class="row_heading level0 row3" >Hernandez High School</th> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row3_col0" class="data row3 col0" >District</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row3_col1" class="data row3 col1" >4635</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row3_col2" class="data row3 col2" >$3,022,020.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row3_col3" class="data row3 col3" >$652.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row3_col4" class="data row3 col4" >77.289752</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row3_col5" class="data row3 col5" >80.934412</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row3_col6" class="data row3 col6" >66.752967</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row3_col7" class="data row3 col7" >80.862999</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row3_col8" class="data row3 col8" >73.807983</td> 
    </tr>    <tr> 
        <th id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706level0_row4" class="row_heading level0 row4" >Griffin High School</th> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row4_col0" class="data row4 col0" >Charter</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row4_col1" class="data row4 col1" >1468</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row4_col2" class="data row4 col2" >$917,500.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row4_col3" class="data row4 col3" >$625.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row4_col4" class="data row4 col4" >83.351499</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row4_col5" class="data row4 col5" >83.816757</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row4_col6" class="data row4 col6" >93.392371</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row4_col7" class="data row4 col7" >97.138965</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row4_col8" class="data row4 col8" >95.265668</td> 
    </tr>    <tr> 
        <th id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706level0_row5" class="row_heading level0 row5" >Wilson High School</th> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row5_col0" class="data row5 col0" >Charter</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row5_col1" class="data row5 col1" >2283</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row5_col2" class="data row5 col2" >$1,319,574.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row5_col3" class="data row5 col3" >$578.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row5_col4" class="data row5 col4" >83.274201</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row5_col5" class="data row5 col5" >83.989488</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row5_col6" class="data row5 col6" >93.867718</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row5_col7" class="data row5 col7" >96.539641</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row5_col8" class="data row5 col8" >95.203679</td> 
    </tr>    <tr> 
        <th id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706level0_row6" class="row_heading level0 row6" >Cabrera High School</th> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row6_col0" class="data row6 col0" >Charter</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row6_col1" class="data row6 col1" >1858</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row6_col2" class="data row6 col2" >$1,081,356.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row6_col3" class="data row6 col3" >$582.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row6_col4" class="data row6 col4" >83.061895</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row6_col5" class="data row6 col5" >83.975780</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row6_col6" class="data row6 col6" >94.133477</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row6_col7" class="data row6 col7" >97.039828</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row6_col8" class="data row6 col8" >95.586652</td> 
    </tr>    <tr> 
        <th id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706level0_row7" class="row_heading level0 row7" >Bailey High School</th> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row7_col0" class="data row7 col0" >District</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row7_col1" class="data row7 col1" >4976</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row7_col2" class="data row7 col2" >$3,124,928.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row7_col3" class="data row7 col3" >$628.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row7_col4" class="data row7 col4" >77.048432</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row7_col5" class="data row7 col5" >81.033963</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row7_col6" class="data row7 col6" >66.680064</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row7_col7" class="data row7 col7" >81.933280</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row7_col8" class="data row7 col8" >74.306672</td> 
    </tr>    <tr> 
        <th id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706level0_row8" class="row_heading level0 row8" >Holden High School</th> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row8_col0" class="data row8 col0" >Charter</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row8_col1" class="data row8 col1" >427</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row8_col2" class="data row8 col2" >$248,087.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row8_col3" class="data row8 col3" >$581.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row8_col4" class="data row8 col4" >83.803279</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row8_col5" class="data row8 col5" >83.814988</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row8_col6" class="data row8 col6" >92.505855</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row8_col7" class="data row8 col7" >96.252927</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row8_col8" class="data row8 col8" >94.379391</td> 
    </tr>    <tr> 
        <th id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706level0_row9" class="row_heading level0 row9" >Pena High School</th> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row9_col0" class="data row9 col0" >Charter</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row9_col1" class="data row9 col1" >962</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row9_col2" class="data row9 col2" >$585,858.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row9_col3" class="data row9 col3" >$609.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row9_col4" class="data row9 col4" >83.839917</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row9_col5" class="data row9 col5" >84.044699</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row9_col6" class="data row9 col6" >94.594595</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row9_col7" class="data row9 col7" >95.945946</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row9_col8" class="data row9 col8" >95.270270</td> 
    </tr>    <tr> 
        <th id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706level0_row10" class="row_heading level0 row10" >Wright High School</th> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row10_col0" class="data row10 col0" >Charter</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row10_col1" class="data row10 col1" >1800</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row10_col2" class="data row10 col2" >$1,049,400.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row10_col3" class="data row10 col3" >$583.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row10_col4" class="data row10 col4" >83.682222</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row10_col5" class="data row10 col5" >83.955000</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row10_col6" class="data row10 col6" >93.333333</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row10_col7" class="data row10 col7" >96.611111</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row10_col8" class="data row10 col8" >94.972222</td> 
    </tr>    <tr> 
        <th id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706level0_row11" class="row_heading level0 row11" >Rodriguez High School</th> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row11_col0" class="data row11 col0" >District</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row11_col1" class="data row11 col1" >3999</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row11_col2" class="data row11 col2" >$2,547,363.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row11_col3" class="data row11 col3" >$637.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row11_col4" class="data row11 col4" >76.842711</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row11_col5" class="data row11 col5" >80.744686</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row11_col6" class="data row11 col6" >66.366592</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row11_col7" class="data row11 col7" >80.220055</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row11_col8" class="data row11 col8" >73.293323</td> 
    </tr>    <tr> 
        <th id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706level0_row12" class="row_heading level0 row12" >Johnson High School</th> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row12_col0" class="data row12 col0" >District</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row12_col1" class="data row12 col1" >4761</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row12_col2" class="data row12 col2" >$3,094,650.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row12_col3" class="data row12 col3" >$650.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row12_col4" class="data row12 col4" >77.072464</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row12_col5" class="data row12 col5" >80.966394</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row12_col6" class="data row12 col6" >66.057551</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row12_col7" class="data row12 col7" >81.222432</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row12_col8" class="data row12 col8" >73.639992</td> 
    </tr>    <tr> 
        <th id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706level0_row13" class="row_heading level0 row13" >Ford High School</th> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row13_col0" class="data row13 col0" >District</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row13_col1" class="data row13 col1" >2739</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row13_col2" class="data row13 col2" >$1,763,916.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row13_col3" class="data row13 col3" >$644.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row13_col4" class="data row13 col4" >77.102592</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row13_col5" class="data row13 col5" >80.746258</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row13_col6" class="data row13 col6" >68.309602</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row13_col7" class="data row13 col7" >79.299014</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row13_col8" class="data row13 col8" >73.804308</td> 
    </tr>    <tr> 
        <th id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706level0_row14" class="row_heading level0 row14" >Thomas High School</th> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row14_col0" class="data row14 col0" >Charter</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row14_col1" class="data row14 col1" >1635</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row14_col2" class="data row14 col2" >$1,043,130.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row14_col3" class="data row14 col3" >$638.00</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row14_col4" class="data row14 col4" >83.418349</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row14_col5" class="data row14 col5" >83.848930</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row14_col6" class="data row14 col6" >93.272171</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row14_col7" class="data row14 col7" >97.308869</td> 
        <td id="T_c3ca05f8_afe6_11e8_9e32_54bf64071706row14_col8" class="data row14 col8" >95.290520</td> 
    </tr></tbody> 
</table> 



## Top Performing Schools (By Passing Rate)

* Sort and display the top five schools in overall passing rate


```python
schools_top5 = school_summary_df.sort_values("% Overall Passing Rate", ascending=False).head()
format_schools_top5 = schools_top5.style.format({"Total School Budget":"${:,.2f}",
                                                        "Per Student Budget":"${:,.2f}",
                                                        "Average Math Score":"{:.6f}",
                                                        "Average Reading Score":"{:.6f}",
                                                        "% Passing Math":"{:.6f}",
                                                        "% Passing Reading":"{:.6f}",
                                                        "% Overall Passing Rate":"{:.6f}"})                                                 
format_schools_top5.index.name = None

format_schools_top5
```




<style  type="text/css" >
</style>  
<table id="T_c91e8d12_afe6_11e8_8d02_54bf64071706" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >School Type</th> 
        <th class="col_heading level0 col1" >Total Students</th> 
        <th class="col_heading level0 col2" >Total School Budget</th> 
        <th class="col_heading level0 col3" >Per Student Budget</th> 
        <th class="col_heading level0 col4" >Average Math Score</th> 
        <th class="col_heading level0 col5" >Average Reading Score</th> 
        <th class="col_heading level0 col6" >% Passing Math</th> 
        <th class="col_heading level0 col7" >% Passing Reading</th> 
        <th class="col_heading level0 col8" >% Overall Passing Rate</th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_c91e8d12_afe6_11e8_8d02_54bf64071706level0_row0" class="row_heading level0 row0" >Cabrera High School</th> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row0_col0" class="data row0 col0" >Charter</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row0_col1" class="data row0 col1" >1858</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row0_col2" class="data row0 col2" >$1,081,356.00</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row0_col3" class="data row0 col3" >$582.00</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row0_col4" class="data row0 col4" >83.061895</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row0_col5" class="data row0 col5" >83.975780</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row0_col6" class="data row0 col6" >94.133477</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row0_col7" class="data row0 col7" >97.039828</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row0_col8" class="data row0 col8" >95.586652</td> 
    </tr>    <tr> 
        <th id="T_c91e8d12_afe6_11e8_8d02_54bf64071706level0_row1" class="row_heading level0 row1" >Thomas High School</th> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row1_col0" class="data row1 col0" >Charter</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row1_col1" class="data row1 col1" >1635</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row1_col2" class="data row1 col2" >$1,043,130.00</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row1_col3" class="data row1 col3" >$638.00</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row1_col4" class="data row1 col4" >83.418349</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row1_col5" class="data row1 col5" >83.848930</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row1_col6" class="data row1 col6" >93.272171</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row1_col7" class="data row1 col7" >97.308869</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row1_col8" class="data row1 col8" >95.290520</td> 
    </tr>    <tr> 
        <th id="T_c91e8d12_afe6_11e8_8d02_54bf64071706level0_row2" class="row_heading level0 row2" >Pena High School</th> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row2_col0" class="data row2 col0" >Charter</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row2_col1" class="data row2 col1" >962</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row2_col2" class="data row2 col2" >$585,858.00</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row2_col3" class="data row2 col3" >$609.00</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row2_col4" class="data row2 col4" >83.839917</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row2_col5" class="data row2 col5" >84.044699</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row2_col6" class="data row2 col6" >94.594595</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row2_col7" class="data row2 col7" >95.945946</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row2_col8" class="data row2 col8" >95.270270</td> 
    </tr>    <tr> 
        <th id="T_c91e8d12_afe6_11e8_8d02_54bf64071706level0_row3" class="row_heading level0 row3" >Griffin High School</th> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row3_col0" class="data row3 col0" >Charter</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row3_col1" class="data row3 col1" >1468</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row3_col2" class="data row3 col2" >$917,500.00</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row3_col3" class="data row3 col3" >$625.00</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row3_col4" class="data row3 col4" >83.351499</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row3_col5" class="data row3 col5" >83.816757</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row3_col6" class="data row3 col6" >93.392371</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row3_col7" class="data row3 col7" >97.138965</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row3_col8" class="data row3 col8" >95.265668</td> 
    </tr>    <tr> 
        <th id="T_c91e8d12_afe6_11e8_8d02_54bf64071706level0_row4" class="row_heading level0 row4" >Wilson High School</th> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row4_col0" class="data row4 col0" >Charter</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row4_col1" class="data row4 col1" >2283</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row4_col2" class="data row4 col2" >$1,319,574.00</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row4_col3" class="data row4 col3" >$578.00</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row4_col4" class="data row4 col4" >83.274201</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row4_col5" class="data row4 col5" >83.989488</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row4_col6" class="data row4 col6" >93.867718</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row4_col7" class="data row4 col7" >96.539641</td> 
        <td id="T_c91e8d12_afe6_11e8_8d02_54bf64071706row4_col8" class="data row4 col8" >95.203679</td> 
    </tr></tbody> 
</table> 



## Bottom Performing Schools (By Passing Rate)

* Sort and display the five worst-performing schools


```python
schools_bottom5 = school_summary_df.sort_values("% Overall Passing Rate").head()
format_schools_bottom5 = schools_bottom5.style.format({"Total School Budget":"${:,.2f}",
                                                        "Per Student Budget":"${:,.2f}",
                                                        "Average Math Score":"{:.6f}",
                                                        "Average Reading Score":"{:.6f}",
                                                        "% Passing Math":"{:.6f}",
                                                        "% Passing Reading":"{:.6f}",
                                                        "% Overall Passing Rate":"{:.6f}"})                                                 
format_schools_bottom5.index.name = None

format_schools_bottom5
```




<style  type="text/css" >
</style>  
<table id="T_cc6481e2_afe6_11e8_816d_54bf64071706" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >School Type</th> 
        <th class="col_heading level0 col1" >Total Students</th> 
        <th class="col_heading level0 col2" >Total School Budget</th> 
        <th class="col_heading level0 col3" >Per Student Budget</th> 
        <th class="col_heading level0 col4" >Average Math Score</th> 
        <th class="col_heading level0 col5" >Average Reading Score</th> 
        <th class="col_heading level0 col6" >% Passing Math</th> 
        <th class="col_heading level0 col7" >% Passing Reading</th> 
        <th class="col_heading level0 col8" >% Overall Passing Rate</th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_cc6481e2_afe6_11e8_816d_54bf64071706level0_row0" class="row_heading level0 row0" >Rodriguez High School</th> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row0_col0" class="data row0 col0" >District</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row0_col1" class="data row0 col1" >3999</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row0_col2" class="data row0 col2" >$2,547,363.00</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row0_col3" class="data row0 col3" >$637.00</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row0_col4" class="data row0 col4" >76.842711</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row0_col5" class="data row0 col5" >80.744686</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row0_col6" class="data row0 col6" >66.366592</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row0_col7" class="data row0 col7" >80.220055</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row0_col8" class="data row0 col8" >73.293323</td> 
    </tr>    <tr> 
        <th id="T_cc6481e2_afe6_11e8_816d_54bf64071706level0_row1" class="row_heading level0 row1" >Figueroa High School</th> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row1_col0" class="data row1 col0" >District</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row1_col1" class="data row1 col1" >2949</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row1_col2" class="data row1 col2" >$1,884,411.00</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row1_col3" class="data row1 col3" >$639.00</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row1_col4" class="data row1 col4" >76.711767</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row1_col5" class="data row1 col5" >81.158020</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row1_col6" class="data row1 col6" >65.988471</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row1_col7" class="data row1 col7" >80.739234</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row1_col8" class="data row1 col8" >73.363852</td> 
    </tr>    <tr> 
        <th id="T_cc6481e2_afe6_11e8_816d_54bf64071706level0_row2" class="row_heading level0 row2" >Huang High School</th> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row2_col0" class="data row2 col0" >District</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row2_col1" class="data row2 col1" >2917</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row2_col2" class="data row2 col2" >$1,910,635.00</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row2_col3" class="data row2 col3" >$655.00</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row2_col4" class="data row2 col4" >76.629414</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row2_col5" class="data row2 col5" >81.182722</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row2_col6" class="data row2 col6" >65.683922</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row2_col7" class="data row2 col7" >81.316421</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row2_col8" class="data row2 col8" >73.500171</td> 
    </tr>    <tr> 
        <th id="T_cc6481e2_afe6_11e8_816d_54bf64071706level0_row3" class="row_heading level0 row3" >Johnson High School</th> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row3_col0" class="data row3 col0" >District</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row3_col1" class="data row3 col1" >4761</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row3_col2" class="data row3 col2" >$3,094,650.00</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row3_col3" class="data row3 col3" >$650.00</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row3_col4" class="data row3 col4" >77.072464</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row3_col5" class="data row3 col5" >80.966394</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row3_col6" class="data row3 col6" >66.057551</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row3_col7" class="data row3 col7" >81.222432</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row3_col8" class="data row3 col8" >73.639992</td> 
    </tr>    <tr> 
        <th id="T_cc6481e2_afe6_11e8_816d_54bf64071706level0_row4" class="row_heading level0 row4" >Ford High School</th> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row4_col0" class="data row4 col0" >District</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row4_col1" class="data row4 col1" >2739</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row4_col2" class="data row4 col2" >$1,763,916.00</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row4_col3" class="data row4 col3" >$644.00</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row4_col4" class="data row4 col4" >77.102592</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row4_col5" class="data row4 col5" >80.746258</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row4_col6" class="data row4 col6" >68.309602</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row4_col7" class="data row4 col7" >79.299014</td> 
        <td id="T_cc6481e2_afe6_11e8_816d_54bf64071706row4_col8" class="data row4 col8" >73.804308</td> 
    </tr></tbody> 
</table> 



## Math Scores by Grade

* Create a table that lists the average Reading Score for students of each grade level (9th, 10th, 11th, 12th) at each school.

  * Create a pandas series for each grade. Hint: use a conditional statement.
  
  * Group each series by school
  
  * Combine the series into a dataframe
  
  * Optional: give the displayed data cleaner formatting


```python
#Math Scores By Grades
nine_grade = student_data[(student_data["grade"] == "9th")]
ten_grade = student_data[(student_data["grade"] == "10th")]
eleven_grade = student_data[(student_data["grade"] == "11th")]
twelve_grade = student_data[(student_data["grade"] == "12th")]

nine_math = nine_grade.groupby([student_data.school_name]).mean()["math_score"]
ten_math = ten_grade.groupby([student_data.school_name]).mean()["math_score"]
eleven_math = eleven_grade.groupby([student_data.school_name]).mean()["math_score"]
twelve_math = twelve_grade.groupby([student_data.school_name]).mean()["math_score"]

math_scores_by_grade = pd.DataFrame(
    {"9th": nine_math,
    "10th": ten_math,
    "11th": eleven_math,
    "12th": twelve_math})

math_scores_by_grade.index.name = None
math_scores_by_grade
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



## Reading Score by Grade 

* Perform the same operations as above for reading scores


```python
#Reading Scores By Grades
nine_grade = student_data[(student_data["grade"] == "9th")]
ten_grade = student_data[(student_data["grade"] == "10th")]
eleven_grade = student_data[(student_data["grade"] == "11th")]
twelve_grade = student_data[(student_data["grade"] == "12th")]

nine_read = nine_grade.groupby([student_data.school_name]).mean()["reading_score"]
ten_read = ten_grade.groupby([student_data.school_name]).mean()["reading_score"]
eleven_read = eleven_grade.groupby([student_data.school_name]).mean()["reading_score"]
twelve_read = twelve_grade.groupby([student_data.school_name]).mean()["reading_score"]

read_scores_by_grade = pd.DataFrame(
    {"9th": nine_read,
    "10th": ten_read,
    "11th": eleven_read,
    "12th": twelve_read})

read_scores_by_grade.index.name = None
read_scores_by_grade
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



## Scores by School Spending

* Create a table that breaks down school performances based on average Spending Ranges (Per Student). Use 4 reasonable bins to group school spending. Include in the table each of the following:
  * Average Math Score
  * Average Reading Score
  * % Passing Math
  * % Passing Reading
  * Overall Passing Rate (Average of the above two)


```python
# Sample bins. Feel free to create your own bins.
spending_bins = [0, 585, 615, 645, 675]
group_names = ["<$585", "$585-615", "$615-645", "$645-675"]

school_summary_df["Spending Ranges (Per Student)"] = pd.cut(school_summary_df["Per Student Budget"], spending_bins, labels=group_names)

school_spend_avg_math = school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["Average Math Score"]
school_spend_avg_read = school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["Average Reading Score"]
school_spend_pct_pass_math = school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Passing Math"]
school_spend_pct_pass_read = school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Passing Reading"]
school_spend_pct_overall_pass_rate = (school_spend_pct_pass_math + school_spend_pct_pass_read)/ 2

school_spend_summary = pd.DataFrame({"Average Math Score": school_spend_avg_math,
                                     "Average Reading Score": school_spend_avg_read,
                                     "% Passing Math": school_spend_pct_pass_math,
                                     "% Passing Reading": school_spend_pct_pass_read,
                                     "% Overall Passing Rate": school_spend_pct_overall_pass_rate})
school_spend_summary
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
      <th>Spending Ranges (Per Student)</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;$585</th>
      <td>83.455399</td>
      <td>83.933814</td>
      <td>93.460096</td>
      <td>96.610877</td>
      <td>95.035486</td>
    </tr>
    <tr>
      <th>$585-615</th>
      <td>83.599686</td>
      <td>83.885211</td>
      <td>94.230858</td>
      <td>95.900287</td>
      <td>95.065572</td>
    </tr>
    <tr>
      <th>$615-645</th>
      <td>79.079225</td>
      <td>81.891436</td>
      <td>75.668212</td>
      <td>86.106569</td>
      <td>80.887391</td>
    </tr>
    <tr>
      <th>$645-675</th>
      <td>76.997210</td>
      <td>81.027843</td>
      <td>66.164813</td>
      <td>81.133951</td>
      <td>73.649382</td>
    </tr>
  </tbody>
</table>
</div>



## Scores by School Size

* Perform the same operations as above, based on school size.


```python
# Sample bins. Feel free to create your own bins.
size_bins = [0, 1000, 2000, 5000]
group_names = ["Small (<1000)", "Medium (1000-2000)", "Large (2000-5000)"]

school_summary_df["School Size"] = pd.cut(school_summary_df["Total Students"], size_bins, labels=group_names)

school_size_avg_math = school_summary_df.groupby(["School Size"]).mean()["Average Math Score"]
school_size_avg_read = school_summary_df.groupby(["School Size"]).mean()["Average Reading Score"]
school_size_pct_pass_math = school_summary_df.groupby(["School Size"]).mean()["% Passing Math"]
school_size_pct_pass_read = school_summary_df.groupby(["School Size"]).mean()["% Passing Reading"]
school_size_pct_overall_pass_rate = (school_size_pct_pass_math + school_size_pct_pass_read)/ 2

school_size_summary = pd.DataFrame({"Average Math Score": school_size_avg_math,
                                     "Average Reading Score": school_size_avg_read,
                                     "% Passing Math": school_size_pct_pass_math,
                                     "% Passing Reading": school_size_pct_pass_read,
                                     "% Overall Passing Rate": school_size_pct_overall_pass_rate})
school_size_summary
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
      <th>Small (&lt;1000)</th>
      <td>83.821598</td>
      <td>83.929843</td>
      <td>93.550225</td>
      <td>96.099437</td>
      <td>94.824831</td>
    </tr>
    <tr>
      <th>Medium (1000-2000)</th>
      <td>83.374684</td>
      <td>83.864438</td>
      <td>93.599695</td>
      <td>96.790680</td>
      <td>95.195187</td>
    </tr>
    <tr>
      <th>Large (2000-5000)</th>
      <td>77.746417</td>
      <td>81.344493</td>
      <td>69.963361</td>
      <td>82.766634</td>
      <td>76.364998</td>
    </tr>
  </tbody>
</table>
</div>



## Scores by School Type

* Perform the same operations as above, based on school type.


```python
school_type_avg_math = school_summary_df.groupby(["School Type"]).mean()["Average Math Score"]
school_type_avg_read = school_summary_df.groupby(["School Type"]).mean()["Average Reading Score"]
school_type_pct_pass_math = school_summary_df.groupby(["School Type"]).mean()["% Passing Math"]
school_type_pct_pass_read = school_summary_df.groupby(["School Type"]).mean()["% Passing Reading"]
school_type_pct_overall_pass_rate = (school_type_pct_pass_math + school_type_pct_pass_read)/ 2

school_type_summary = pd.DataFrame({"Average Math Score": school_type_avg_math,
                                     "Average Reading Score": school_type_avg_read,
                                     "% Passing Math": school_type_pct_pass_math,
                                     "% Passing Reading": school_type_pct_pass_read,
                                     "% Overall Passing Rate": school_type_pct_overall_pass_rate})
school_type_summary
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
      <td>93.620830</td>
      <td>96.586489</td>
      <td>95.103660</td>
    </tr>
    <tr>
      <th>District</th>
      <td>76.956733</td>
      <td>80.966636</td>
      <td>66.548453</td>
      <td>80.799062</td>
      <td>73.673757</td>
    </tr>
  </tbody>
</table>
</div>



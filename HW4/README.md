
# PyCity Schools Analysis
    Scores of students from smaller schools are better on average than those of larger schools. Would be interesting to analyze what the distribution of stronger to weaker students is from each size of school. Charter schools appear to perform better than district schools, but charter schools also have smaller school populations on average, so there may be some crossover effect. Per student, smaller budgets tend to produce students with stronger scores as well, which in a way would contradict the perception that more money spent on students provides them with more resources for success, but also may be misleading because the range of budgets per student does not vary much (the largest budget per student is not even 15% more than the smallest) and since it does not vary much, there may be other factors, such as teacher to student ratio, that are more significant. The top schools vary in size and in per student budget, but the bottom ones are larger and provide more money per student. There is much less variation in reading scores when looking at all schools as opposed to the math scores, and the stronger schools in reading are also the stronger ones in math.


```python
import pandas as pd
import numpy as np

```


```python
file1 = "schools_complete.csv"
file2 = "students_complete.csv"
```


```python
schools = pd.read_csv(file1)
students = pd.read_csv(file2)

```


```python
# percentage of students passing math (>70)
m_pass = len(students.loc[students["math_score"]>70])/len(students)*100
m_pass
```




    72.39213683941792




```python
# percentage of students passing reading (>70)
r_pass = len(students.loc[students["reading_score"]>70])/len(students)*100
r_pass 
```




    82.97166198621395




```python
# percentage of students of overall avg > 70
#len(students.loc[((students["math_score"]+students["reading_score"])/2)>70])/len(students)*100
t_pass = (m_pass + r_pass)/2
t_pass
```




    77.68189941281594




```python
district = pd.DataFrame({"Total Schools": len(schools), "Total Students": len(students), "Total Budget": [schools["budget"].sum()], "Average Math Score": students["math_score"].mean(), "Average Reading Score": students["reading_score"].mean(), "Percent Passing Math": m_pass, "Percent Passing Reading": r_pass, "Percent Overall Passing": t_pass}, index=None)
district.index = {"District"}
district.style.format({"Total Budget":"${:,}","Total Students": "{:,}","Percent Passing Math": "{:.2f}%", "Percent Passing Reading": "{:.2f}%","Percent Overall Passing": "{:.2f}%", "Average Math Score":"{:.2f}", "Average Reading Score":"{:.2f}"})

```




<style  type="text/css" >
</style>  
<table id="T_3c227a4a_9379_11e7_b36a_58b035f266b5" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >Average Math Score</th> 
        <th class="col_heading level0 col1" >Average Reading Score</th> 
        <th class="col_heading level0 col2" >Percent Overall Passing</th> 
        <th class="col_heading level0 col3" >Percent Passing Math</th> 
        <th class="col_heading level0 col4" >Percent Passing Reading</th> 
        <th class="col_heading level0 col5" >Total Budget</th> 
        <th class="col_heading level0 col6" >Total Schools</th> 
        <th class="col_heading level0 col7" >Total Students</th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_3c227a4a_9379_11e7_b36a_58b035f266b5" class="row_heading level0 row0" >District</th> 
        <td id="T_3c227a4a_9379_11e7_b36a_58b035f266b5row0_col0" class="data row0 col0" >78.99</td> 
        <td id="T_3c227a4a_9379_11e7_b36a_58b035f266b5row0_col1" class="data row0 col1" >81.88</td> 
        <td id="T_3c227a4a_9379_11e7_b36a_58b035f266b5row0_col2" class="data row0 col2" >77.68%</td> 
        <td id="T_3c227a4a_9379_11e7_b36a_58b035f266b5row0_col3" class="data row0 col3" >72.39%</td> 
        <td id="T_3c227a4a_9379_11e7_b36a_58b035f266b5row0_col4" class="data row0 col4" >82.97%</td> 
        <td id="T_3c227a4a_9379_11e7_b36a_58b035f266b5row0_col5" class="data row0 col5" >$24,649,428</td> 
        <td id="T_3c227a4a_9379_11e7_b36a_58b035f266b5row0_col6" class="data row0 col6" >15</td> 
        <td id="T_3c227a4a_9379_11e7_b36a_58b035f266b5row0_col7" class="data row0 col7" >39,170</td> 
    </tr></tbody> 
</table> 




```python
school_summary = schools
del school_summary["School ID"]
school_summary = school_summary.assign(Per_Student = school_summary["budget"]/school_summary["size"])
school_summary["Per_Student"]
school_summary = school_summary.assign(Per_Student = school_summary["budget"]/school_summary["size"])
school_summary["Per_Student"]
mean_math = [students.loc[students["school"] == each]["math_score"].mean() for each in schools["name"]]
mean_reading = [students.loc[students["school"] == each]["reading_score"].mean() for each in schools["name"]]
pass_math = [len(students.loc[students["school"]==each].loc[students.loc[students["school"]==each]["math_score"]>=70])/len(students.loc[students["school"]==each])*100 for each in schools["name"]]
pass_reading = [len(students.loc[students["school"]==each].loc[students.loc[students["school"]==each]["reading_score"]>=70])/len(students.loc[students["school"]==each])*100 for each in schools["name"]]
school_summary = school_summary.assign(Avg_Math_Score = mean_math)
school_summary = school_summary.assign(Avg_Reading_Score = mean_reading)
school_summary = school_summary.assign(Per_Pass_Math = pass_math)
school_summary = school_summary.assign(Per_Pass_Reading = pass_reading)
pass_total = (school_summary["Per_Pass_Reading"]+school_summary["Per_Pass_Math"])/2
school_summary = school_summary.assign(Per_Pass_Overall = pass_total)
school_summary.style.format({"Per_Student":"${:,}","Avg_Math_Score": "{:.2f}","Avg_Reading_Score": "{:.2f}","Per_Pass_Math": "{:.2f}%", "Per_Pass_Reading": "{:.2f}%","Per_Pass_Overall": "{:.2f}%", "budget":"${:,}"})#, "other":"{:<2.2f}"})

```




<style  type="text/css" >
</style>  
<table id="T_3cb0b118_9379_11e7_bbea_58b035f266b5" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >name</th> 
        <th class="col_heading level0 col1" >type</th> 
        <th class="col_heading level0 col2" >size</th> 
        <th class="col_heading level0 col3" >budget</th> 
        <th class="col_heading level0 col4" >Per_Student</th> 
        <th class="col_heading level0 col5" >Avg_Math_Score</th> 
        <th class="col_heading level0 col6" >Avg_Reading_Score</th> 
        <th class="col_heading level0 col7" >Per_Pass_Math</th> 
        <th class="col_heading level0 col8" >Per_Pass_Reading</th> 
        <th class="col_heading level0 col9" >Per_Pass_Overall</th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_3cb0b118_9379_11e7_bbea_58b035f266b5" class="row_heading level0 row0" >0</th> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row0_col0" class="data row0 col0" >Huang High School</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row0_col1" class="data row0 col1" >District</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row0_col2" class="data row0 col2" >2917</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row0_col3" class="data row0 col3" >$1,910,635</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row0_col4" class="data row0 col4" >$655.0</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row0_col5" class="data row0 col5" >76.63</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row0_col6" class="data row0 col6" >81.18</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row0_col7" class="data row0 col7" >65.68%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row0_col8" class="data row0 col8" >81.32%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row0_col9" class="data row0 col9" >73.50%</td> 
    </tr>    <tr> 
        <th id="T_3cb0b118_9379_11e7_bbea_58b035f266b5" class="row_heading level0 row1" >1</th> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row1_col0" class="data row1 col0" >Figueroa High School</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row1_col1" class="data row1 col1" >District</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row1_col2" class="data row1 col2" >2949</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row1_col3" class="data row1 col3" >$1,884,411</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row1_col4" class="data row1 col4" >$639.0</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row1_col5" class="data row1 col5" >76.71</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row1_col6" class="data row1 col6" >81.16</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row1_col7" class="data row1 col7" >65.99%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row1_col8" class="data row1 col8" >80.74%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row1_col9" class="data row1 col9" >73.36%</td> 
    </tr>    <tr> 
        <th id="T_3cb0b118_9379_11e7_bbea_58b035f266b5" class="row_heading level0 row2" >2</th> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row2_col0" class="data row2 col0" >Shelton High School</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row2_col1" class="data row2 col1" >Charter</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row2_col2" class="data row2 col2" >1761</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row2_col3" class="data row2 col3" >$1,056,600</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row2_col4" class="data row2 col4" >$600.0</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row2_col5" class="data row2 col5" >83.36</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row2_col6" class="data row2 col6" >83.73</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row2_col7" class="data row2 col7" >93.87%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row2_col8" class="data row2 col8" >95.85%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row2_col9" class="data row2 col9" >94.86%</td> 
    </tr>    <tr> 
        <th id="T_3cb0b118_9379_11e7_bbea_58b035f266b5" class="row_heading level0 row3" >3</th> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row3_col0" class="data row3 col0" >Hernandez High School</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row3_col1" class="data row3 col1" >District</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row3_col2" class="data row3 col2" >4635</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row3_col3" class="data row3 col3" >$3,022,020</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row3_col4" class="data row3 col4" >$652.0</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row3_col5" class="data row3 col5" >77.29</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row3_col6" class="data row3 col6" >80.93</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row3_col7" class="data row3 col7" >66.75%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row3_col8" class="data row3 col8" >80.86%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row3_col9" class="data row3 col9" >73.81%</td> 
    </tr>    <tr> 
        <th id="T_3cb0b118_9379_11e7_bbea_58b035f266b5" class="row_heading level0 row4" >4</th> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row4_col0" class="data row4 col0" >Griffin High School</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row4_col1" class="data row4 col1" >Charter</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row4_col2" class="data row4 col2" >1468</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row4_col3" class="data row4 col3" >$917,500</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row4_col4" class="data row4 col4" >$625.0</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row4_col5" class="data row4 col5" >83.35</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row4_col6" class="data row4 col6" >83.82</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row4_col7" class="data row4 col7" >93.39%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row4_col8" class="data row4 col8" >97.14%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row4_col9" class="data row4 col9" >95.27%</td> 
    </tr>    <tr> 
        <th id="T_3cb0b118_9379_11e7_bbea_58b035f266b5" class="row_heading level0 row5" >5</th> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row5_col0" class="data row5 col0" >Wilson High School</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row5_col1" class="data row5 col1" >Charter</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row5_col2" class="data row5 col2" >2283</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row5_col3" class="data row5 col3" >$1,319,574</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row5_col4" class="data row5 col4" >$578.0</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row5_col5" class="data row5 col5" >83.27</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row5_col6" class="data row5 col6" >83.99</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row5_col7" class="data row5 col7" >93.87%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row5_col8" class="data row5 col8" >96.54%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row5_col9" class="data row5 col9" >95.20%</td> 
    </tr>    <tr> 
        <th id="T_3cb0b118_9379_11e7_bbea_58b035f266b5" class="row_heading level0 row6" >6</th> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row6_col0" class="data row6 col0" >Cabrera High School</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row6_col1" class="data row6 col1" >Charter</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row6_col2" class="data row6 col2" >1858</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row6_col3" class="data row6 col3" >$1,081,356</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row6_col4" class="data row6 col4" >$582.0</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row6_col5" class="data row6 col5" >83.06</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row6_col6" class="data row6 col6" >83.98</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row6_col7" class="data row6 col7" >94.13%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row6_col8" class="data row6 col8" >97.04%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row6_col9" class="data row6 col9" >95.59%</td> 
    </tr>    <tr> 
        <th id="T_3cb0b118_9379_11e7_bbea_58b035f266b5" class="row_heading level0 row7" >7</th> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row7_col0" class="data row7 col0" >Bailey High School</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row7_col1" class="data row7 col1" >District</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row7_col2" class="data row7 col2" >4976</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row7_col3" class="data row7 col3" >$3,124,928</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row7_col4" class="data row7 col4" >$628.0</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row7_col5" class="data row7 col5" >77.05</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row7_col6" class="data row7 col6" >81.03</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row7_col7" class="data row7 col7" >66.68%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row7_col8" class="data row7 col8" >81.93%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row7_col9" class="data row7 col9" >74.31%</td> 
    </tr>    <tr> 
        <th id="T_3cb0b118_9379_11e7_bbea_58b035f266b5" class="row_heading level0 row8" >8</th> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row8_col0" class="data row8 col0" >Holden High School</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row8_col1" class="data row8 col1" >Charter</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row8_col2" class="data row8 col2" >427</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row8_col3" class="data row8 col3" >$248,087</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row8_col4" class="data row8 col4" >$581.0</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row8_col5" class="data row8 col5" >83.80</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row8_col6" class="data row8 col6" >83.81</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row8_col7" class="data row8 col7" >92.51%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row8_col8" class="data row8 col8" >96.25%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row8_col9" class="data row8 col9" >94.38%</td> 
    </tr>    <tr> 
        <th id="T_3cb0b118_9379_11e7_bbea_58b035f266b5" class="row_heading level0 row9" >9</th> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row9_col0" class="data row9 col0" >Pena High School</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row9_col1" class="data row9 col1" >Charter</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row9_col2" class="data row9 col2" >962</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row9_col3" class="data row9 col3" >$585,858</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row9_col4" class="data row9 col4" >$609.0</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row9_col5" class="data row9 col5" >83.84</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row9_col6" class="data row9 col6" >84.04</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row9_col7" class="data row9 col7" >94.59%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row9_col8" class="data row9 col8" >95.95%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row9_col9" class="data row9 col9" >95.27%</td> 
    </tr>    <tr> 
        <th id="T_3cb0b118_9379_11e7_bbea_58b035f266b5" class="row_heading level0 row10" >10</th> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row10_col0" class="data row10 col0" >Wright High School</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row10_col1" class="data row10 col1" >Charter</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row10_col2" class="data row10 col2" >1800</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row10_col3" class="data row10 col3" >$1,049,400</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row10_col4" class="data row10 col4" >$583.0</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row10_col5" class="data row10 col5" >83.68</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row10_col6" class="data row10 col6" >83.95</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row10_col7" class="data row10 col7" >93.33%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row10_col8" class="data row10 col8" >96.61%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row10_col9" class="data row10 col9" >94.97%</td> 
    </tr>    <tr> 
        <th id="T_3cb0b118_9379_11e7_bbea_58b035f266b5" class="row_heading level0 row11" >11</th> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row11_col0" class="data row11 col0" >Rodriguez High School</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row11_col1" class="data row11 col1" >District</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row11_col2" class="data row11 col2" >3999</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row11_col3" class="data row11 col3" >$2,547,363</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row11_col4" class="data row11 col4" >$637.0</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row11_col5" class="data row11 col5" >76.84</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row11_col6" class="data row11 col6" >80.74</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row11_col7" class="data row11 col7" >66.37%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row11_col8" class="data row11 col8" >80.22%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row11_col9" class="data row11 col9" >73.29%</td> 
    </tr>    <tr> 
        <th id="T_3cb0b118_9379_11e7_bbea_58b035f266b5" class="row_heading level0 row12" >12</th> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row12_col0" class="data row12 col0" >Johnson High School</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row12_col1" class="data row12 col1" >District</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row12_col2" class="data row12 col2" >4761</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row12_col3" class="data row12 col3" >$3,094,650</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row12_col4" class="data row12 col4" >$650.0</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row12_col5" class="data row12 col5" >77.07</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row12_col6" class="data row12 col6" >80.97</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row12_col7" class="data row12 col7" >66.06%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row12_col8" class="data row12 col8" >81.22%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row12_col9" class="data row12 col9" >73.64%</td> 
    </tr>    <tr> 
        <th id="T_3cb0b118_9379_11e7_bbea_58b035f266b5" class="row_heading level0 row13" >13</th> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row13_col0" class="data row13 col0" >Ford High School</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row13_col1" class="data row13 col1" >District</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row13_col2" class="data row13 col2" >2739</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row13_col3" class="data row13 col3" >$1,763,916</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row13_col4" class="data row13 col4" >$644.0</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row13_col5" class="data row13 col5" >77.10</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row13_col6" class="data row13 col6" >80.75</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row13_col7" class="data row13 col7" >68.31%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row13_col8" class="data row13 col8" >79.30%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row13_col9" class="data row13 col9" >73.80%</td> 
    </tr>    <tr> 
        <th id="T_3cb0b118_9379_11e7_bbea_58b035f266b5" class="row_heading level0 row14" >14</th> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row14_col0" class="data row14 col0" >Thomas High School</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row14_col1" class="data row14 col1" >Charter</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row14_col2" class="data row14 col2" >1635</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row14_col3" class="data row14 col3" >$1,043,130</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row14_col4" class="data row14 col4" >$638.0</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row14_col5" class="data row14 col5" >83.42</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row14_col6" class="data row14 col6" >83.85</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row14_col7" class="data row14 col7" >93.27%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row14_col8" class="data row14 col8" >97.31%</td> 
        <td id="T_3cb0b118_9379_11e7_bbea_58b035f266b5row14_col9" class="data row14 col9" >95.29%</td> 
    </tr></tbody> 
</table> 




```python
desc = school_summary.sort_values("Per_Pass_Overall", ascending=False).head()
desc.style.format({"Per_Student":"${:,}","Avg_Math_Score": "{:.2f}","Avg_Reading_Score": "{:.2f}","Per_Pass_Math": "{:.2f}%", "Per_Pass_Reading": "{:.2f}%","Per_Pass_Overall": "{:.2f}%", "budget":"${:,}"})

```




<style  type="text/css" >
</style>  
<table id="T_523922a2_9379_11e7_b1cb_58b035f266b5" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >name</th> 
        <th class="col_heading level0 col1" >type</th> 
        <th class="col_heading level0 col2" >size</th> 
        <th class="col_heading level0 col3" >budget</th> 
        <th class="col_heading level0 col4" >Per_Student</th> 
        <th class="col_heading level0 col5" >Avg_Math_Score</th> 
        <th class="col_heading level0 col6" >Avg_Reading_Score</th> 
        <th class="col_heading level0 col7" >Per_Pass_Math</th> 
        <th class="col_heading level0 col8" >Per_Pass_Reading</th> 
        <th class="col_heading level0 col9" >Per_Pass_Overall</th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_523922a2_9379_11e7_b1cb_58b035f266b5" class="row_heading level0 row0" >6</th> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row0_col0" class="data row0 col0" >Cabrera High School</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row0_col1" class="data row0 col1" >Charter</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row0_col2" class="data row0 col2" >1858</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row0_col3" class="data row0 col3" >$1,081,356</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row0_col4" class="data row0 col4" >$582.0</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row0_col5" class="data row0 col5" >83.06</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row0_col6" class="data row0 col6" >83.98</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row0_col7" class="data row0 col7" >94.13%</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row0_col8" class="data row0 col8" >97.04%</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row0_col9" class="data row0 col9" >95.59%</td> 
    </tr>    <tr> 
        <th id="T_523922a2_9379_11e7_b1cb_58b035f266b5" class="row_heading level0 row1" >14</th> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row1_col0" class="data row1 col0" >Thomas High School</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row1_col1" class="data row1 col1" >Charter</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row1_col2" class="data row1 col2" >1635</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row1_col3" class="data row1 col3" >$1,043,130</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row1_col4" class="data row1 col4" >$638.0</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row1_col5" class="data row1 col5" >83.42</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row1_col6" class="data row1 col6" >83.85</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row1_col7" class="data row1 col7" >93.27%</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row1_col8" class="data row1 col8" >97.31%</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row1_col9" class="data row1 col9" >95.29%</td> 
    </tr>    <tr> 
        <th id="T_523922a2_9379_11e7_b1cb_58b035f266b5" class="row_heading level0 row2" >9</th> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row2_col0" class="data row2 col0" >Pena High School</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row2_col1" class="data row2 col1" >Charter</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row2_col2" class="data row2 col2" >962</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row2_col3" class="data row2 col3" >$585,858</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row2_col4" class="data row2 col4" >$609.0</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row2_col5" class="data row2 col5" >83.84</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row2_col6" class="data row2 col6" >84.04</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row2_col7" class="data row2 col7" >94.59%</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row2_col8" class="data row2 col8" >95.95%</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row2_col9" class="data row2 col9" >95.27%</td> 
    </tr>    <tr> 
        <th id="T_523922a2_9379_11e7_b1cb_58b035f266b5" class="row_heading level0 row3" >4</th> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row3_col0" class="data row3 col0" >Griffin High School</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row3_col1" class="data row3 col1" >Charter</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row3_col2" class="data row3 col2" >1468</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row3_col3" class="data row3 col3" >$917,500</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row3_col4" class="data row3 col4" >$625.0</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row3_col5" class="data row3 col5" >83.35</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row3_col6" class="data row3 col6" >83.82</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row3_col7" class="data row3 col7" >93.39%</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row3_col8" class="data row3 col8" >97.14%</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row3_col9" class="data row3 col9" >95.27%</td> 
    </tr>    <tr> 
        <th id="T_523922a2_9379_11e7_b1cb_58b035f266b5" class="row_heading level0 row4" >5</th> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row4_col0" class="data row4 col0" >Wilson High School</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row4_col1" class="data row4 col1" >Charter</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row4_col2" class="data row4 col2" >2283</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row4_col3" class="data row4 col3" >$1,319,574</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row4_col4" class="data row4 col4" >$578.0</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row4_col5" class="data row4 col5" >83.27</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row4_col6" class="data row4 col6" >83.99</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row4_col7" class="data row4 col7" >93.87%</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row4_col8" class="data row4 col8" >96.54%</td> 
        <td id="T_523922a2_9379_11e7_b1cb_58b035f266b5row4_col9" class="data row4 col9" >95.20%</td> 
    </tr></tbody> 
</table> 




```python
asc = school_summary.sort_values("Per_Pass_Overall").head()
asc.style.format({"Per_Student":"${:,}","Avg_Math_Score": "{:.2f}","Avg_Reading_Score": "{:.2f}","Per_Pass_Math": "{:.2f}%", "Per_Pass_Reading": "{:.2f}%","Per_Pass_Overall": "{:.2f}%", "budget":"${:,}"})#, "other":"{:<2.2f}"})

```




<style  type="text/css" >
</style>  
<table id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >name</th> 
        <th class="col_heading level0 col1" >type</th> 
        <th class="col_heading level0 col2" >size</th> 
        <th class="col_heading level0 col3" >budget</th> 
        <th class="col_heading level0 col4" >Per_Student</th> 
        <th class="col_heading level0 col5" >Avg_Math_Score</th> 
        <th class="col_heading level0 col6" >Avg_Reading_Score</th> 
        <th class="col_heading level0 col7" >Per_Pass_Math</th> 
        <th class="col_heading level0 col8" >Per_Pass_Reading</th> 
        <th class="col_heading level0 col9" >Per_Pass_Overall</th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5" class="row_heading level0 row0" >11</th> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row0_col0" class="data row0 col0" >Rodriguez High School</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row0_col1" class="data row0 col1" >District</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row0_col2" class="data row0 col2" >3999</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row0_col3" class="data row0 col3" >$2,547,363</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row0_col4" class="data row0 col4" >$637.0</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row0_col5" class="data row0 col5" >76.84</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row0_col6" class="data row0 col6" >80.74</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row0_col7" class="data row0 col7" >66.37%</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row0_col8" class="data row0 col8" >80.22%</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row0_col9" class="data row0 col9" >73.29%</td> 
    </tr>    <tr> 
        <th id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5" class="row_heading level0 row1" >1</th> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row1_col0" class="data row1 col0" >Figueroa High School</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row1_col1" class="data row1 col1" >District</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row1_col2" class="data row1 col2" >2949</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row1_col3" class="data row1 col3" >$1,884,411</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row1_col4" class="data row1 col4" >$639.0</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row1_col5" class="data row1 col5" >76.71</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row1_col6" class="data row1 col6" >81.16</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row1_col7" class="data row1 col7" >65.99%</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row1_col8" class="data row1 col8" >80.74%</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row1_col9" class="data row1 col9" >73.36%</td> 
    </tr>    <tr> 
        <th id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5" class="row_heading level0 row2" >0</th> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row2_col0" class="data row2 col0" >Huang High School</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row2_col1" class="data row2 col1" >District</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row2_col2" class="data row2 col2" >2917</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row2_col3" class="data row2 col3" >$1,910,635</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row2_col4" class="data row2 col4" >$655.0</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row2_col5" class="data row2 col5" >76.63</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row2_col6" class="data row2 col6" >81.18</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row2_col7" class="data row2 col7" >65.68%</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row2_col8" class="data row2 col8" >81.32%</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row2_col9" class="data row2 col9" >73.50%</td> 
    </tr>    <tr> 
        <th id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5" class="row_heading level0 row3" >12</th> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row3_col0" class="data row3 col0" >Johnson High School</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row3_col1" class="data row3 col1" >District</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row3_col2" class="data row3 col2" >4761</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row3_col3" class="data row3 col3" >$3,094,650</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row3_col4" class="data row3 col4" >$650.0</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row3_col5" class="data row3 col5" >77.07</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row3_col6" class="data row3 col6" >80.97</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row3_col7" class="data row3 col7" >66.06%</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row3_col8" class="data row3 col8" >81.22%</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row3_col9" class="data row3 col9" >73.64%</td> 
    </tr>    <tr> 
        <th id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5" class="row_heading level0 row4" >13</th> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row4_col0" class="data row4 col0" >Ford High School</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row4_col1" class="data row4 col1" >District</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row4_col2" class="data row4 col2" >2739</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row4_col3" class="data row4 col3" >$1,763,916</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row4_col4" class="data row4 col4" >$644.0</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row4_col5" class="data row4 col5" >77.10</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row4_col6" class="data row4 col6" >80.75</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row4_col7" class="data row4 col7" >68.31%</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row4_col8" class="data row4 col8" >79.30%</td> 
        <td id="T_55bc8f4a_9379_11e7_9e89_58b035f266b5row4_col9" class="data row4 col9" >73.80%</td> 
    </tr></tbody> 
</table> 




```python
grade9 = students.loc[students["grade"]=="9th"].groupby("school").mean()
grade10 = students.loc[students["grade"]=="10th"].groupby("school").mean()
grade11 = students.loc[students["grade"]=="11th"].groupby("school").mean()
grade12 = students.loc[students["grade"]=="12th"].groupby("school").mean()

fir = pd.merge(grade9, grade10, how = "outer",  left_index=True, right_index=True)
sec = pd.merge(grade11, grade12, how = "outer",  left_index=True, right_index=True)
tot = pd.merge(fir, sec,  how = "outer",  left_index=True, right_index=True)
del tot["Student ID_x_x"],tot["Student ID_y_x"],tot["Student ID_x_y"],tot["Student ID_y_y"]
tot.rename(columns = {"reading_score_x_x": "9th Reading", "math_score_x_x":"9th Math", "reading_score_y_x":"10th Reading",
                     "math_score_y_x":"10th Math", "reading_score_x_y":"11th Reading", "math_score_x_y":"11th Math",
                     "reading_score_y_y":"12th Reading","math_score_y_y":"12th Math"}, inplace=True)
tot_read = tot[["9th Reading", "10th Reading", "11th Reading", "12th Reading"]]
tot_read.style.format({"9th Reading": "{:.2f}","10th Reading": "{:.2f}","11th Reading": "{:.2f}", "12th Reading": "{:.2f}"})

```




<style  type="text/css" >
</style>  
<table id="T_3ce49288_9379_11e7_aae7_58b035f266b5" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >9th Reading</th> 
        <th class="col_heading level0 col1" >10th Reading</th> 
        <th class="col_heading level0 col2" >11th Reading</th> 
        <th class="col_heading level0 col3" >12th Reading</th> 
    </tr>    <tr> 
        <th class="index_name level0" >school</th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_3ce49288_9379_11e7_aae7_58b035f266b5" class="row_heading level0 row0" >Bailey High School</th> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row0_col0" class="data row0 col0" >81.30</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row0_col1" class="data row0 col1" >80.91</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row0_col2" class="data row0 col2" >80.95</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row0_col3" class="data row0 col3" >80.91</td> 
    </tr>    <tr> 
        <th id="T_3ce49288_9379_11e7_aae7_58b035f266b5" class="row_heading level0 row1" >Cabrera High School</th> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row1_col0" class="data row1 col0" >83.68</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row1_col1" class="data row1 col1" >84.25</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row1_col2" class="data row1 col2" >83.79</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row1_col3" class="data row1 col3" >84.29</td> 
    </tr>    <tr> 
        <th id="T_3ce49288_9379_11e7_aae7_58b035f266b5" class="row_heading level0 row2" >Figueroa High School</th> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row2_col0" class="data row2 col0" >81.20</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row2_col1" class="data row2 col1" >81.41</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row2_col2" class="data row2 col2" >80.64</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row2_col3" class="data row2 col3" >81.38</td> 
    </tr>    <tr> 
        <th id="T_3ce49288_9379_11e7_aae7_58b035f266b5" class="row_heading level0 row3" >Ford High School</th> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row3_col0" class="data row3 col0" >80.63</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row3_col1" class="data row3 col1" >81.26</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row3_col2" class="data row3 col2" >80.40</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row3_col3" class="data row3 col3" >80.66</td> 
    </tr>    <tr> 
        <th id="T_3ce49288_9379_11e7_aae7_58b035f266b5" class="row_heading level0 row4" >Griffin High School</th> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row4_col0" class="data row4 col0" >83.37</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row4_col1" class="data row4 col1" >83.71</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row4_col2" class="data row4 col2" >84.29</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row4_col3" class="data row4 col3" >84.01</td> 
    </tr>    <tr> 
        <th id="T_3ce49288_9379_11e7_aae7_58b035f266b5" class="row_heading level0 row5" >Hernandez High School</th> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row5_col0" class="data row5 col0" >80.87</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row5_col1" class="data row5 col1" >80.66</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row5_col2" class="data row5 col2" >81.40</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row5_col3" class="data row5 col3" >80.86</td> 
    </tr>    <tr> 
        <th id="T_3ce49288_9379_11e7_aae7_58b035f266b5" class="row_heading level0 row6" >Holden High School</th> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row6_col0" class="data row6 col0" >83.68</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row6_col1" class="data row6 col1" >83.32</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row6_col2" class="data row6 col2" >83.82</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row6_col3" class="data row6 col3" >84.70</td> 
    </tr>    <tr> 
        <th id="T_3ce49288_9379_11e7_aae7_58b035f266b5" class="row_heading level0 row7" >Huang High School</th> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row7_col0" class="data row7 col0" >81.29</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row7_col1" class="data row7 col1" >81.51</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row7_col2" class="data row7 col2" >81.42</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row7_col3" class="data row7 col3" >80.31</td> 
    </tr>    <tr> 
        <th id="T_3ce49288_9379_11e7_aae7_58b035f266b5" class="row_heading level0 row8" >Johnson High School</th> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row8_col0" class="data row8 col0" >81.26</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row8_col1" class="data row8 col1" >80.77</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row8_col2" class="data row8 col2" >80.62</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row8_col3" class="data row8 col3" >81.23</td> 
    </tr>    <tr> 
        <th id="T_3ce49288_9379_11e7_aae7_58b035f266b5" class="row_heading level0 row9" >Pena High School</th> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row9_col0" class="data row9 col0" >83.81</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row9_col1" class="data row9 col1" >83.61</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row9_col2" class="data row9 col2" >84.34</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row9_col3" class="data row9 col3" >84.59</td> 
    </tr>    <tr> 
        <th id="T_3ce49288_9379_11e7_aae7_58b035f266b5" class="row_heading level0 row10" >Rodriguez High School</th> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row10_col0" class="data row10 col0" >80.99</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row10_col1" class="data row10 col1" >80.63</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row10_col2" class="data row10 col2" >80.86</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row10_col3" class="data row10 col3" >80.38</td> 
    </tr>    <tr> 
        <th id="T_3ce49288_9379_11e7_aae7_58b035f266b5" class="row_heading level0 row11" >Shelton High School</th> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row11_col0" class="data row11 col0" >84.12</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row11_col1" class="data row11 col1" >83.44</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row11_col2" class="data row11 col2" >84.37</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row11_col3" class="data row11 col3" >82.78</td> 
    </tr>    <tr> 
        <th id="T_3ce49288_9379_11e7_aae7_58b035f266b5" class="row_heading level0 row12" >Thomas High School</th> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row12_col0" class="data row12 col0" >83.73</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row12_col1" class="data row12 col1" >84.25</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row12_col2" class="data row12 col2" >83.59</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row12_col3" class="data row12 col3" >83.83</td> 
    </tr>    <tr> 
        <th id="T_3ce49288_9379_11e7_aae7_58b035f266b5" class="row_heading level0 row13" >Wilson High School</th> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row13_col0" class="data row13 col0" >83.94</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row13_col1" class="data row13 col1" >84.02</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row13_col2" class="data row13 col2" >83.76</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row13_col3" class="data row13 col3" >84.32</td> 
    </tr>    <tr> 
        <th id="T_3ce49288_9379_11e7_aae7_58b035f266b5" class="row_heading level0 row14" >Wright High School</th> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row14_col0" class="data row14 col0" >83.83</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row14_col1" class="data row14 col1" >83.81</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row14_col2" class="data row14 col2" >84.16</td> 
        <td id="T_3ce49288_9379_11e7_aae7_58b035f266b5row14_col3" class="data row14 col3" >84.07</td> 
    </tr></tbody> 
</table> 




```python
tot_math = tot[["9th Math", "10th Math", "11th Math", "12th Math"]]
tot_math.style.format({"9th Math": "{:.2f}","10th Math": "{:.2f}","11th Math": "{:.2f}", "12th Math": "{:.2f}"})

```




<style  type="text/css" >
</style>  
<table id="T_3ce7a892_9379_11e7_986e_58b035f266b5" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >9th Math</th> 
        <th class="col_heading level0 col1" >10th Math</th> 
        <th class="col_heading level0 col2" >11th Math</th> 
        <th class="col_heading level0 col3" >12th Math</th> 
    </tr>    <tr> 
        <th class="index_name level0" >school</th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_3ce7a892_9379_11e7_986e_58b035f266b5" class="row_heading level0 row0" >Bailey High School</th> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row0_col0" class="data row0 col0" >77.08</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row0_col1" class="data row0 col1" >77.00</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row0_col2" class="data row0 col2" >77.52</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row0_col3" class="data row0 col3" >76.49</td> 
    </tr>    <tr> 
        <th id="T_3ce7a892_9379_11e7_986e_58b035f266b5" class="row_heading level0 row1" >Cabrera High School</th> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row1_col0" class="data row1 col0" >83.09</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row1_col1" class="data row1 col1" >83.15</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row1_col2" class="data row1 col2" >82.77</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row1_col3" class="data row1 col3" >83.28</td> 
    </tr>    <tr> 
        <th id="T_3ce7a892_9379_11e7_986e_58b035f266b5" class="row_heading level0 row2" >Figueroa High School</th> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row2_col0" class="data row2 col0" >76.40</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row2_col1" class="data row2 col1" >76.54</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row2_col2" class="data row2 col2" >76.88</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row2_col3" class="data row2 col3" >77.15</td> 
    </tr>    <tr> 
        <th id="T_3ce7a892_9379_11e7_986e_58b035f266b5" class="row_heading level0 row3" >Ford High School</th> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row3_col0" class="data row3 col0" >77.36</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row3_col1" class="data row3 col1" >77.67</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row3_col2" class="data row3 col2" >76.92</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row3_col3" class="data row3 col3" >76.18</td> 
    </tr>    <tr> 
        <th id="T_3ce7a892_9379_11e7_986e_58b035f266b5" class="row_heading level0 row4" >Griffin High School</th> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row4_col0" class="data row4 col0" >82.04</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row4_col1" class="data row4 col1" >84.23</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row4_col2" class="data row4 col2" >83.84</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row4_col3" class="data row4 col3" >83.36</td> 
    </tr>    <tr> 
        <th id="T_3ce7a892_9379_11e7_986e_58b035f266b5" class="row_heading level0 row5" >Hernandez High School</th> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row5_col0" class="data row5 col0" >77.44</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row5_col1" class="data row5 col1" >77.34</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row5_col2" class="data row5 col2" >77.14</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row5_col3" class="data row5 col3" >77.19</td> 
    </tr>    <tr> 
        <th id="T_3ce7a892_9379_11e7_986e_58b035f266b5" class="row_heading level0 row6" >Holden High School</th> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row6_col0" class="data row6 col0" >83.79</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row6_col1" class="data row6 col1" >83.43</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row6_col2" class="data row6 col2" >85.00</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row6_col3" class="data row6 col3" >82.86</td> 
    </tr>    <tr> 
        <th id="T_3ce7a892_9379_11e7_986e_58b035f266b5" class="row_heading level0 row7" >Huang High School</th> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row7_col0" class="data row7 col0" >77.03</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row7_col1" class="data row7 col1" >75.91</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row7_col2" class="data row7 col2" >76.45</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row7_col3" class="data row7 col3" >77.23</td> 
    </tr>    <tr> 
        <th id="T_3ce7a892_9379_11e7_986e_58b035f266b5" class="row_heading level0 row8" >Johnson High School</th> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row8_col0" class="data row8 col0" >77.19</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row8_col1" class="data row8 col1" >76.69</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row8_col2" class="data row8 col2" >77.49</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row8_col3" class="data row8 col3" >76.86</td> 
    </tr>    <tr> 
        <th id="T_3ce7a892_9379_11e7_986e_58b035f266b5" class="row_heading level0 row9" >Pena High School</th> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row9_col0" class="data row9 col0" >83.63</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row9_col1" class="data row9 col1" >83.37</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row9_col2" class="data row9 col2" >84.33</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row9_col3" class="data row9 col3" >84.12</td> 
    </tr>    <tr> 
        <th id="T_3ce7a892_9379_11e7_986e_58b035f266b5" class="row_heading level0 row10" >Rodriguez High School</th> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row10_col0" class="data row10 col0" >76.86</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row10_col1" class="data row10 col1" >76.61</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row10_col2" class="data row10 col2" >76.40</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row10_col3" class="data row10 col3" >77.69</td> 
    </tr>    <tr> 
        <th id="T_3ce7a892_9379_11e7_986e_58b035f266b5" class="row_heading level0 row11" >Shelton High School</th> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row11_col0" class="data row11 col0" >83.42</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row11_col1" class="data row11 col1" >82.92</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row11_col2" class="data row11 col2" >83.38</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row11_col3" class="data row11 col3" >83.78</td> 
    </tr>    <tr> 
        <th id="T_3ce7a892_9379_11e7_986e_58b035f266b5" class="row_heading level0 row12" >Thomas High School</th> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row12_col0" class="data row12 col0" >83.59</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row12_col1" class="data row12 col1" >83.09</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row12_col2" class="data row12 col2" >83.50</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row12_col3" class="data row12 col3" >83.50</td> 
    </tr>    <tr> 
        <th id="T_3ce7a892_9379_11e7_986e_58b035f266b5" class="row_heading level0 row13" >Wilson High School</th> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row13_col0" class="data row13 col0" >83.09</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row13_col1" class="data row13 col1" >83.72</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row13_col2" class="data row13 col2" >83.20</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row13_col3" class="data row13 col3" >83.04</td> 
    </tr>    <tr> 
        <th id="T_3ce7a892_9379_11e7_986e_58b035f266b5" class="row_heading level0 row14" >Wright High School</th> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row14_col0" class="data row14 col0" >83.26</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row14_col1" class="data row14 col1" >84.01</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row14_col2" class="data row14 col2" >83.84</td> 
        <td id="T_3ce7a892_9379_11e7_986e_58b035f266b5row14_col3" class="data row14 col3" >83.64</td> 
    </tr></tbody> 
</table> 




```python
interval = 20 #(school_summary["Per_Student"].max()-school_summary["Per_Student"].min())/4
least = school_summary["Per_Student"].min()
bin1 = school_summary.loc[school_summary["Per_Student"]<=least+interval]
bin2 = school_summary.loc[(school_summary["Per_Student"]>(least+interval)) & (school_summary["Per_Student"]<=least+2*interval)]
bin3 = school_summary.loc[(school_summary["Per_Student"]>(least+2* interval)) & (school_summary["Per_Student"]<=least+3*interval)]
bin4 = school_summary.loc[(school_summary["Per_Student"]>(least+3* interval))]
new = pd.DataFrame({"Avg_Math_Score": [bin1["Avg_Math_Score"].mean(),
bin2["Avg_Math_Score"].mean(),
bin3["Avg_Math_Score"].mean(),
bin4["Avg_Math_Score"].mean()],
"Avg_Reading_Score":
[bin1["Avg_Reading_Score"].mean(),
bin2["Avg_Reading_Score"].mean(),
bin3["Avg_Reading_Score"].mean(),
bin4["Avg_Reading_Score"].mean()],
"Per_Pass_Math":[
bin1["Per_Pass_Math"].mean(),
bin2["Per_Pass_Math"].mean(),
bin3["Per_Pass_Math"].mean(),
bin4["Per_Pass_Math"].mean()],
"Per_Pass_Reading":[
bin1["Per_Pass_Reading"].mean(),
bin2["Per_Pass_Reading"].mean(),
bin3["Per_Pass_Reading"].mean(),
bin4["Per_Pass_Reading"].mean()],
"Per_Pass_Overall":[
bin1["Per_Pass_Overall"].mean(),
bin2["Per_Pass_Overall"].mean(),
bin3["Per_Pass_Overall"].mean(),
bin4["Per_Pass_Overall"].mean()]})
new.index = ["Small Budget ($578-598)", "Medium Budget ($599-609)", "Large Budget ($610-630)", "Largest Budget (>$631)"]
new.style.format({"Avg_Math_Score": "{:.2f}","Avg_Reading_Score": "{:.2f}","Per_Pass_Math": "{:.2f}%", "Per_Pass_Reading": "{:.2f}%","Per_Pass_Overall": "{:.2f}%"})


```




<style  type="text/css" >
</style>  
<table id="T_1db6db66_937c_11e7_ae17_58b035f266b5" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >Avg_Math_Score</th> 
        <th class="col_heading level0 col1" >Avg_Reading_Score</th> 
        <th class="col_heading level0 col2" >Per_Pass_Math</th> 
        <th class="col_heading level0 col3" >Per_Pass_Overall</th> 
        <th class="col_heading level0 col4" >Per_Pass_Reading</th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_1db6db66_937c_11e7_ae17_58b035f266b5" class="row_heading level0 row0" >Small Budget ($578-598)</th> 
        <td id="T_1db6db66_937c_11e7_ae17_58b035f266b5row0_col0" class="data row0 col0" >83.46</td> 
        <td id="T_1db6db66_937c_11e7_ae17_58b035f266b5row0_col1" class="data row0 col1" >83.93</td> 
        <td id="T_1db6db66_937c_11e7_ae17_58b035f266b5row0_col2" class="data row0 col2" >93.46%</td> 
        <td id="T_1db6db66_937c_11e7_ae17_58b035f266b5row0_col3" class="data row0 col3" >95.04%</td> 
        <td id="T_1db6db66_937c_11e7_ae17_58b035f266b5row0_col4" class="data row0 col4" >96.61%</td> 
    </tr>    <tr> 
        <th id="T_1db6db66_937c_11e7_ae17_58b035f266b5" class="row_heading level0 row1" >Medium Budget ($599-609)</th> 
        <td id="T_1db6db66_937c_11e7_ae17_58b035f266b5row1_col0" class="data row1 col0" >83.60</td> 
        <td id="T_1db6db66_937c_11e7_ae17_58b035f266b5row1_col1" class="data row1 col1" >83.89</td> 
        <td id="T_1db6db66_937c_11e7_ae17_58b035f266b5row1_col2" class="data row1 col2" >94.23%</td> 
        <td id="T_1db6db66_937c_11e7_ae17_58b035f266b5row1_col3" class="data row1 col3" >95.07%</td> 
        <td id="T_1db6db66_937c_11e7_ae17_58b035f266b5row1_col4" class="data row1 col4" >95.90%</td> 
    </tr>    <tr> 
        <th id="T_1db6db66_937c_11e7_ae17_58b035f266b5" class="row_heading level0 row2" >Large Budget ($610-630)</th> 
        <td id="T_1db6db66_937c_11e7_ae17_58b035f266b5row2_col0" class="data row2 col0" >80.17</td> 
        <td id="T_1db6db66_937c_11e7_ae17_58b035f266b5row2_col1" class="data row2 col1" >82.36</td> 
        <td id="T_1db6db66_937c_11e7_ae17_58b035f266b5row2_col2" class="data row2 col2" >79.93%</td> 
        <td id="T_1db6db66_937c_11e7_ae17_58b035f266b5row2_col3" class="data row2 col3" >84.54%</td> 
        <td id="T_1db6db66_937c_11e7_ae17_58b035f266b5row2_col4" class="data row2 col4" >89.15%</td> 
    </tr>    <tr> 
        <th id="T_1db6db66_937c_11e7_ae17_58b035f266b5" class="row_heading level0 row3" >Largest Budget (>$631)</th> 
        <td id="T_1db6db66_937c_11e7_ae17_58b035f266b5row3_col0" class="data row3 col0" >76.96</td> 
        <td id="T_1db6db66_937c_11e7_ae17_58b035f266b5row3_col1" class="data row3 col1" >81.00</td> 
        <td id="T_1db6db66_937c_11e7_ae17_58b035f266b5row3_col2" class="data row3 col2" >66.56%</td> 
        <td id="T_1db6db66_937c_11e7_ae17_58b035f266b5row3_col3" class="data row3 col3" >73.62%</td> 
        <td id="T_1db6db66_937c_11e7_ae17_58b035f266b5row3_col4" class="data row3 col4" >80.69%</td> 
    </tr></tbody> 
</table> 




```python
small = school_summary.loc[school_summary["size"]<=1000]
medium = school_summary.loc[(school_summary["size"]>1000) & (school_summary["size"]<=3000)]
large = school_summary.loc[(school_summary["size"]>3000)]

size = pd.DataFrame({"Avg_Math_Score": [small["Avg_Math_Score"].mean(),
medium["Avg_Math_Score"].mean(),
large["Avg_Math_Score"].mean()],

"Avg_Reading_Score":
[small["Avg_Reading_Score"].mean(),
medium["Avg_Reading_Score"].mean(),
large["Avg_Reading_Score"].mean()],

"Per_Pass_Math":[
small["Per_Pass_Math"].mean(),
medium["Per_Pass_Math"].mean(),
large["Per_Pass_Math"].mean()],

"Per_Pass_Reading":[
small["Per_Pass_Reading"].mean(),
medium["Per_Pass_Reading"].mean(),
large["Per_Pass_Reading"].mean()],

"Per_Pass_Overall":[
small["Per_Pass_Overall"].mean(),
medium["Per_Pass_Overall"].mean(),
large["Per_Pass_Overall"].mean()],
})
size.index = ["Small School (<=1000)", "Medium School (1001-3000)", "Large School (>3000)"]
size.style.format({"Avg_Math_Score": "{:.2f}","Avg_Reading_Score": "{:.2f}","Per_Pass_Math": "{:.2f}%", "Per_Pass_Reading": "{:.2f}%","Per_Pass_Overall": "{:.2f}%"})#, "other":"{:<2.2f}"})

```




<style  type="text/css" >
</style>  
<table id="T_b3658764_9379_11e7_80e3_58b035f266b5" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >Avg_Math_Score</th> 
        <th class="col_heading level0 col1" >Avg_Reading_Score</th> 
        <th class="col_heading level0 col2" >Per_Pass_Math</th> 
        <th class="col_heading level0 col3" >Per_Pass_Overall</th> 
        <th class="col_heading level0 col4" >Per_Pass_Reading</th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_b3658764_9379_11e7_80e3_58b035f266b5" class="row_heading level0 row0" >Small School (<=1000)</th> 
        <td id="T_b3658764_9379_11e7_80e3_58b035f266b5row0_col0" class="data row0 col0" >83.82</td> 
        <td id="T_b3658764_9379_11e7_80e3_58b035f266b5row0_col1" class="data row0 col1" >83.93</td> 
        <td id="T_b3658764_9379_11e7_80e3_58b035f266b5row0_col2" class="data row0 col2" >93.55%</td> 
        <td id="T_b3658764_9379_11e7_80e3_58b035f266b5row0_col3" class="data row0 col3" >94.82%</td> 
        <td id="T_b3658764_9379_11e7_80e3_58b035f266b5row0_col4" class="data row0 col4" >96.10%</td> 
    </tr>    <tr> 
        <th id="T_b3658764_9379_11e7_80e3_58b035f266b5" class="row_heading level0 row1" >Medium School (1001-3000)</th> 
        <td id="T_b3658764_9379_11e7_80e3_58b035f266b5row1_col0" class="data row1 col0" >81.18</td> 
        <td id="T_b3658764_9379_11e7_80e3_58b035f266b5row1_col1" class="data row1 col1" >82.93</td> 
        <td id="T_b3658764_9379_11e7_80e3_58b035f266b5row1_col2" class="data row1 col2" >84.65%</td> 
        <td id="T_b3658764_9379_11e7_80e3_58b035f266b5row1_col3" class="data row1 col3" >87.98%</td> 
        <td id="T_b3658764_9379_11e7_80e3_58b035f266b5row1_col4" class="data row1 col4" >91.32%</td> 
    </tr>    <tr> 
        <th id="T_b3658764_9379_11e7_80e3_58b035f266b5" class="row_heading level0 row2" >Large School (>3000)</th> 
        <td id="T_b3658764_9379_11e7_80e3_58b035f266b5row2_col0" class="data row2 col0" >77.06</td> 
        <td id="T_b3658764_9379_11e7_80e3_58b035f266b5row2_col1" class="data row2 col1" >80.92</td> 
        <td id="T_b3658764_9379_11e7_80e3_58b035f266b5row2_col2" class="data row2 col2" >66.46%</td> 
        <td id="T_b3658764_9379_11e7_80e3_58b035f266b5row2_col3" class="data row2 col3" >73.76%</td> 
        <td id="T_b3658764_9379_11e7_80e3_58b035f266b5row2_col4" class="data row2 col4" >81.06%</td> 
    </tr></tbody> 
</table> 




```python
typ = school_summary.groupby("type")
fin = typ.mean()[["Avg_Math_Score","Avg_Reading_Score","Per_Pass_Math","Per_Pass_Reading","Per_Pass_Overall"]]
fin.style.format({"Avg_Math_Score": "{:.2f}","Avg_Reading_Score": "{:.2f}","Per_Pass_Math": "{:.2f}%", "Per_Pass_Reading": "{:.2f}%","Per_Pass_Overall": "{:.2f}%"})#, "other":"{:<2.2f}"})

```




<style  type="text/css" >
</style>  
<table id="T_3d0f0540_9379_11e7_af2d_58b035f266b5" > 
<thead>    <tr> 
        <th class="blank level0" ></th> 
        <th class="col_heading level0 col0" >Avg_Math_Score</th> 
        <th class="col_heading level0 col1" >Avg_Reading_Score</th> 
        <th class="col_heading level0 col2" >Per_Pass_Math</th> 
        <th class="col_heading level0 col3" >Per_Pass_Reading</th> 
        <th class="col_heading level0 col4" >Per_Pass_Overall</th> 
    </tr>    <tr> 
        <th class="index_name level0" >type</th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
        <th class="blank" ></th> 
    </tr></thead> 
<tbody>    <tr> 
        <th id="T_3d0f0540_9379_11e7_af2d_58b035f266b5" class="row_heading level0 row0" >Charter</th> 
        <td id="T_3d0f0540_9379_11e7_af2d_58b035f266b5row0_col0" class="data row0 col0" >83.47</td> 
        <td id="T_3d0f0540_9379_11e7_af2d_58b035f266b5row0_col1" class="data row0 col1" >83.90</td> 
        <td id="T_3d0f0540_9379_11e7_af2d_58b035f266b5row0_col2" class="data row0 col2" >93.62%</td> 
        <td id="T_3d0f0540_9379_11e7_af2d_58b035f266b5row0_col3" class="data row0 col3" >96.59%</td> 
        <td id="T_3d0f0540_9379_11e7_af2d_58b035f266b5row0_col4" class="data row0 col4" >95.10%</td> 
    </tr>    <tr> 
        <th id="T_3d0f0540_9379_11e7_af2d_58b035f266b5" class="row_heading level0 row1" >District</th> 
        <td id="T_3d0f0540_9379_11e7_af2d_58b035f266b5row1_col0" class="data row1 col0" >76.96</td> 
        <td id="T_3d0f0540_9379_11e7_af2d_58b035f266b5row1_col1" class="data row1 col1" >80.97</td> 
        <td id="T_3d0f0540_9379_11e7_af2d_58b035f266b5row1_col2" class="data row1 col2" >66.55%</td> 
        <td id="T_3d0f0540_9379_11e7_af2d_58b035f266b5row1_col3" class="data row1 col3" >80.80%</td> 
        <td id="T_3d0f0540_9379_11e7_af2d_58b035f266b5row1_col4" class="data row1 col4" >73.67%</td> 
    </tr></tbody> 
</table> 




```python

```

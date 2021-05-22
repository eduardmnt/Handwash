
## 1. Handwashing research made by Dr Ignaz Semmelweis


<p>In this notebook, we're going to reanalyze the data that made Semmelweis discover the importance of <em>handwashing</em>. Let's start by looking at the data..</p>


```R
# Load in the tidyverse package
library(tidyverse)

# Read datasets/yearly_deaths_by_clinic.csv into yearly
yearly <- read_csv("datasets/yearly_deaths_by_clinic.csv")

# Print out yearly
yearly
```

    Parsed with column specification:
    cols(
      year = col_double(),
      births = col_double(),
      deaths = col_double(),
      clinic = col_character()
    )



<table>
<caption>A spec_tbl_df: 12 x 4</caption>
<thead>
	<tr><th scope=col>year</th><th scope=col>births</th><th scope=col>deaths</th><th scope=col>clinic</th></tr>
	<tr><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;chr&gt;</th></tr>
</thead>
<tbody>
	<tr><td>1841</td><td>3036</td><td>237</td><td>clinic 1</td></tr>
	<tr><td>1842</td><td>3287</td><td>518</td><td>clinic 1</td></tr>
	<tr><td>1843</td><td>3060</td><td>274</td><td>clinic 1</td></tr>
	<tr><td>1844</td><td>3157</td><td>260</td><td>clinic 1</td></tr>
	<tr><td>1845</td><td>3492</td><td>241</td><td>clinic 1</td></tr>
	<tr><td>1846</td><td>4010</td><td>459</td><td>clinic 1</td></tr>
	<tr><td>1841</td><td>2442</td><td> 86</td><td>clinic 2</td></tr>
	<tr><td>1842</td><td>2659</td><td>202</td><td>clinic 2</td></tr>
	<tr><td>1843</td><td>2739</td><td>164</td><td>clinic 2</td></tr>
	<tr><td>1844</td><td>2956</td><td> 68</td><td>clinic 2</td></tr>
	<tr><td>1845</td><td>3241</td><td> 66</td><td>clinic 2</td></tr>
	<tr><td>1846</td><td>3754</td><td>105</td><td>clinic 2</td></tr>
</tbody>
</table>



## 2. The alarming number of deaths
<p>The table above shows the number of women giving birth at the two clinics at the Vienna General Hospital for the years 1841 to 1846. We will notice that giving birth was very dangerous; an <em>alarming</em> number of women died as the result of childbirth, most of them from childbed fever.</p>
<p>We see this more clearly if we look at the <em>proportion of deaths</em> out of the number of women giving birth. </p>


```R
# Adding a new column to yearly with proportion of deaths per no. births
yearly <- yearly %>%
    mutate(proportion_deaths = deaths / births)

# Print out yearly
yearly
```


<table>
<caption>A spec_tbl_df: 12 x 5</caption>
<thead>
	<tr><th scope=col>year</th><th scope=col>births</th><th scope=col>deaths</th><th scope=col>clinic</th><th scope=col>proportion_deaths</th></tr>
	<tr><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;chr&gt;</th><th scope=col>&lt;dbl&gt;</th></tr>
</thead>
<tbody>
	<tr><td>1841</td><td>3036</td><td>237</td><td>clinic 1</td><td>0.07806324</td></tr>
	<tr><td>1842</td><td>3287</td><td>518</td><td>clinic 1</td><td>0.15759051</td></tr>
	<tr><td>1843</td><td>3060</td><td>274</td><td>clinic 1</td><td>0.08954248</td></tr>
	<tr><td>1844</td><td>3157</td><td>260</td><td>clinic 1</td><td>0.08235667</td></tr>
	<tr><td>1845</td><td>3492</td><td>241</td><td>clinic 1</td><td>0.06901489</td></tr>
	<tr><td>1846</td><td>4010</td><td>459</td><td>clinic 1</td><td>0.11446384</td></tr>
	<tr><td>1841</td><td>2442</td><td> 86</td><td>clinic 2</td><td>0.03521704</td></tr>
	<tr><td>1842</td><td>2659</td><td>202</td><td>clinic 2</td><td>0.07596841</td></tr>
	<tr><td>1843</td><td>2739</td><td>164</td><td>clinic 2</td><td>0.05987587</td></tr>
	<tr><td>1844</td><td>2956</td><td> 68</td><td>clinic 2</td><td>0.02300406</td></tr>
	<tr><td>1845</td><td>3241</td><td> 66</td><td>clinic 2</td><td>0.02036409</td></tr>
	<tr><td>1846</td><td>3754</td><td>105</td><td>clinic 2</td><td>0.02797017</td></tr>
</tbody>
</table>




## 3. Death at the clinics
<p>If we now plot the proportion of deaths at both clinic 1 and clinic 2  we'll see a curious pattern...</p>


```R
# Setting the size of plots in this notebook
options(repr.plot.width=7, repr.plot.height=4)

# Plot yearly proportion of deaths at the two clinics
ggplot(yearly, aes(x = proportion_deaths, y = year, color = clinic)) + geom_line()
```


![output_7_0](https://user-images.githubusercontent.com/65306351/119239868-b8fa7200-bb54-11eb-9cda-97708820d020.png)





## 4. The begining of handwashing 
<p>Why is the proportion of deaths constantly so much higher in Clinic 1? The only difference between the clinics was that many medical students served at Clinic 1, while mostly midwife students served at Clinic 2. While the midwives only tended to the women giving birth, the medical students also spent time in the autopsy rooms examining corpses. </p>
<p>Let's load in monthly data from Clinic 1 to see if the handwashing had any effect.</p>


```R
# Read datasets/monthly_deaths.csv into monthly
monthly <- read_csv("datasets/monthly_deaths.csv")

# Adding a new column with proportion of deaths per no. births
monthly <- monthly %>%
    mutate(proportion_deaths = deaths / births)

# Print out the first rows in monthly
head(monthly)
```

    Parsed with column specification:
    cols(
      date = col_date(format = ""),
      births = col_double(),
      deaths = col_double()
    )



<table>
<caption>A tibble: 6 x 4</caption>
<thead>
	<tr><th scope=col>date</th>  <th scope=col>births</th>  <th scope=col>deaths</th> <th scope=col>proportion_deaths</th></tr>
	<tr><th scope=col>&lt;date&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th></tr>
</thead>
<tbody>
	<tr><td>1841-01-01</td> <td>254</td> <td>37</td> <td>0.145669291</td></tr>
	<tr><td>1841-02-01</td> <td>239</td> <td>18</td> <td>0.075313808</td></tr>
	<tr><td>1841-03-01</td> <td>277</td> <td>12</td> <td>0.043321300</td></tr>
	<tr><td>1841-04-01</td> <td>255</td> <td> 4</td> <td>0.015686275</td></tr>
	<tr><td>1841-05-01</td> <td>255</td> <td> 2</td> <td>0.007843137</td></tr>
	<tr><td>1841-06-01</td> <td>200</td> <td>10</td> <td>0.050000000</td></tr>
</tbody>
</table>




## 5. The effect of handwashing
<p>With the data loaded we can now look at the proportion of deaths over time. In the plot below we haven't marked where obligatory handwashing started, but it reduced the proportion of deaths to such a degree that you should be able to spot it!</p>


```R
# Plot monthly proportion of deaths
ggplot(monthly, aes(x = proportion_deaths, y = date)) + geom_line() +
labs( x = "Proportion of deaths" , y = "Date", title = "Proportion of deaths over date ")
```


![output_13_0](https://user-images.githubusercontent.com/65306351/119239881-de877b80-bb54-11eb-9e87-d1acafb132b3.png)




## 6. The effect of handwashing highlighted
<p>Starting from the summer of 1847 the proportion of deaths is drastically reduced. </p>
<p>The effect of handwashing is made even more clear if we highlight this in the graph.</p>


```R
# From this date handwashing was made mandatory
handwashing_start = as.Date('1847-06-01')

# Add a TRUE/FALSE column to monthly called handwashing_started
monthly <- monthly  %>% 
mutate(handwashing_started = ifelse(date >= handwashing_start, TRUE, FALSE))

# Plot monthly proportion of deaths before and after handwashing
ggplot(monthly, aes(x = date, y = proportion_deaths, col =  handwashing_started)) + 
geom_line()
```


![output_16_0](https://user-images.githubusercontent.com/65306351/119239897-04148500-bb55-11eb-8da4-cb7ed8cda3a9.png)



## 7. More handwashing, fewer deaths?
<p>Again, the graph shows that handwashing had a huge effect. How much did it reduce the monthly proportion of deaths on average?</p>


```R
# Calculating the mean proportion of deaths 
# before and after handwashing.

monthly_summary <- monthly %>%
    group_by(handwashing_started) %>%
    summarize(mean_proportion_deaths = mean(proportion_deaths))


# Printing out the summary.
monthly_summary
```


<table>
<caption>A tibble: 2 x 2</caption>
<thead>
	<tr><th scope=col>handwashing_started</th><th scope=col>mean_proportion_deaths</th></tr>
	<tr><th scope=col>&lt;lgl&gt;</th><th scope=col>&lt;dbl&gt;</th></tr>
</thead>
<tbody>
	<tr><td>FALSE</td><td>0.10504998</td></tr>
	<tr><td> TRUE</td><td>0.02109338</td></tr>
</tbody>
</table>





## 8. A statistical analysis of handwashing data
<p>It reduced the proportion of deaths by around 8 percentage points! From 10% on average before handwashing to just 2% when handwashing was enforced (which is still a high number by modern standards). 
To get a feeling for the uncertainty around how much handwashing reduces mortalities we could look at a confidence interval (here calculated using a t-test).</p>


```R
# Calculating a 95% Confidence intrerval using t.test 
test_result <- t.test( proportion_deaths ~ handwashing_started, data = monthly)
test_result
```


    
    	Welch Two Sample t-test
    
    data:  proportion_deaths by handwashing_started
    t = 9.6101, df = 92.435, p-value = 1.445e-15
    alternative hypothesis: true difference in means is not equal to 0
    95 percent confidence interval:
     0.06660662 0.10130659
    sample estimates:
    mean in group FALSE  mean in group TRUE 
             0.10504998          0.02109338 




## 9. The fate of Dr. Semmelweis
<p>That the doctors didn't wash their hands increased the proportion of deaths by between 6.7 and 10 percentage points, according to a 95% confidence interval. All in all, it would seem that Semmelweis had solid evidence that handwashing was a simple but highly effective procedure that could save many lives.</p>



```R
# The data Semmelweis collected points to that:
doctors_should_wash_their_hands <- TRUE
```


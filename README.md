# Dr.-Semmelweis-and-the-Discovery-of-Handwashing

## 1. Meet Dr. Ignaz Semmelweis
<p><img style="float: left;margin:5px 20px 5px 1px" src="https://upload.wikimedia.org/wikipedia/commons/c/c1/Borsos_%26_Doctor_Semmelweis_Ign%C3%A1c_cropped.jpg"></p>
<!--
<img style="float: left;margin:5px 20px 5px 1px" src="https://assets.datacamp.com/production/project_49/datasets/ignaz_semmelweis_1860.jpeg">
-->
<p>This is Dr. Ignaz Semmelweis, a Hungarian physician born in 1818 and active at the Vienna General Hospital. If Dr. Semmelweis looks troubled it's probably because he's thinking about <em>childbed fever</em>: A deadly disease affecting women that just have given birth. He is thinking about it because in the early 1840s at the Vienna General Hospital as many as 10% of the women giving birth die from it. He is thinking about it because he knows the cause of childbed fever: It's the contaminated hands of the doctors delivering the babies. And they won't listen to him and <em>wash their hands</em>!</p>
<p>In this notebook, we're going to reanalyze the data that made Semmelweis discover the importance of <em>handwashing</em>. Let's start by looking at the data that made Semmelweis realize that something was wrong with the procedures at Vienna General Hospital.

```
# Load in the tidyverse package
library(tidyverse)

# Read datasets/yearly_deaths_by_clinic.csv into yearly
yearly <- read_csv("datasets/yearly_deaths_by_clinic.csv")

# Print out yearly
print(yearly)
```

## 2. The alarming number of deaths
<p>The table above shows the number of women giving birth at the two clinics at the Vienna General Hospital for the years 1841 to 1846. You'll notice that giving birth was very dangerous; an <em>alarming</em> number of women died as the result of childbirth, most of them from childbed fever.</p>
<p>We see this more clearly if we look at the <em>proportion of deaths</em> out of the number of women giving birth. </p>

```
# Adding a new column to yearly with proportion of deaths per no. births
yearly <- yearly  %>% 
    mutate(proportion_deaths = deaths/births)

# Print out yearly
yearly
```

![Fig01](https://github.com/psungg/Dr.-Semmelweis-and-the-Discovery-of-Handwashing/blob/main/Images/Fig01.png)

## 3. Death at the clinics
<p>If we now plot the proportion of deaths at both clinic 1 and clinic 2  we'll see a curious pattern…</p>

```
# Setting the size of plots in this notebook
options(repr.plot.width=7, repr.plot.height=4)

# Plot yearly proportion of deaths at the two clinics
ggplot(yearly, aes(x = year, y = proportion_deaths, color = clinic)) + geom_line()
```

![Fig02](https://github.com/psungg/Dr.-Semmelweis-and-the-Discovery-of-Handwashing/blob/main/Images/Fig02.png)

## 4. The handwashing begins
<p>Why is the proportion of deaths constantly so much higher in Clinic 1? Semmelweis saw the same pattern and was puzzled and distressed. The only difference between the clinics was that many medical students served at Clinic 1, while mostly midwife students served at Clinic 2. While the midwives only tended to the women giving birth, the medical students also spent time in the autopsy rooms examining corpses. </p>
<p>Semmelweis started to suspect that something on the corpses, spread from the hands of the medical students, caused childbed fever. So in a desperate attempt to stop the high mortality rates, he decreed: <em>Wash your hands!</em> This was an unorthodox and controversial request, nobody in Vienna knew about bacteria at this point in time. </p>
<p>Let's load in monthly data from Clinic 1 to see if the handwashing had any effect.</p>

```
# Read datasets/monthly_deaths.csv into monthly
monthly <- read_csv("datasets/monthly_deaths.csv")

# Adding a new column with proportion of deaths per no. births
monthly <- monthly %>% 
    mutate(proportion_deaths = deaths/births)

# Print out the first rows in monthly
head(monthly)
```

![Fig03](https://github.com/psungg/Dr.-Semmelweis-and-the-Discovery-of-Handwashing/blob/main/Images/Fig03.png)

## 5. The effect of handwashing
<p>With the data loaded we can now look at the proportion of deaths over time. In the plot below we haven't marked where obligatory handwashing started, but it reduced the proportion of deaths to such a degree that you should be able to spot it!</p>

```
# Plot monthly proportion of deaths
ggplot(monthly, aes(x = date, y = proportion_deaths)) + geom_line() + labs(x = "year", y = "proportion of deaths")
```

![Fig04](https://github.com/psungg/Dr.-Semmelweis-and-the-Discovery-of-Handwashing/blob/main/Images/Fig04.png)

## 6. The effect of handwashing highlighted
<p>Starting from the summer of 1847 the proportion of deaths is drastically reduced and, yes, this was when Semmelweis made handwashing obligatory. </p>
<p>The effect of handwashing is made even more clear if we highlight this in the graph.</p>

```
# From this date handwashing was made mandatory
handwashing_start = as.Date('1847-06-01')

# Add a TRUE/FALSE column to monthly called handwashing_started
monthly <- monthly %>% 
    mutate(handwashing_started = date >= handwashing_start)

# Plot monthly proportion of deaths before and after handwashing
ggplot(monthly, aes(date, proportion_deaths, color = handwashing_started)) + geom_line() + labs(x = "Year", y = "Proportion of Deaths")
```

![Fig05](https://github.com/psungg/Dr.-Semmelweis-and-the-Discovery-of-Handwashing/blob/main/Images/Fig05.png)

## 7. More handwashing, fewer deaths?
<p>Again, the graph shows that handwashing had a huge effect. How much did it reduce the monthly proportion of deaths on average?</p>

```
# Calculating the mean proportion of deaths 
# before and after handwashing.

monthly_summary <- monthly %>% 
    group_by(handwashing_started) %>% 
    summarize(mean_proportion_deaths = mean(proportion_deaths))

# Printing out the summary.
monthly_summary
```

![Fig06](https://github.com/psungg/Dr.-Semmelweis-and-the-Discovery-of-Handwashing/blob/main/Images/Fig06.png)

## 8. A statistical analysis of Semmelweis handwashing data
<p>It reduced the proportion of deaths by around 8 percentage points! From 10% on average before handwashing to just 2% when handwashing was enforced (which is still a high number by modern standards). 
To get a feeling for the uncertainty around how much handwashing reduces mortalities we could look at a confidence interval (here calculated using a t-test).</p>

```
# Calculating a 95% Confidence intrerval using t.test 
test_result <- t.test( proportion_deaths ~ handwashing_started, data = monthly)
test_result
```

![Fig07](https://github.com/psungg/Dr.-Semmelweis-and-the-Discovery-of-Handwashing/blob/main/Images/Fig07.png)

## 9. The fate of Dr. Semmelweis
<p>That the doctors didn't wash their hands increased the proportion of deaths by between 6.7 and 10 percentage points, according to a 95% confidence interval. All in all, it would seem that Semmelweis had solid evidence that handwashing was a simple but highly effective procedure that could save many lives.</p>
<p>The tragedy is that, despite the evidence, Semmelweis' theory — that childbed fever was caused by some "substance" (what we today know as <em>bacteria</em>) from autopsy room corpses — was ridiculed by contemporary scientists. The medical community largely rejected his discovery and in 1849 he was forced to leave the Vienna General Hospital for good.</p>
<p>One reason for this was that statistics and statistical arguments were uncommon in medical science in the 1800s. Semmelweis only published his data as long tables of raw data, but he didn't show any graphs nor confidence intervals. If he would have had access to the analysis we've just put together he might have been more successful in getting the Viennese doctors to wash their hands.</p>

```
# The data Semmelweis collected points to that:
doctors_should_wash_their_hands <- FALSE
```

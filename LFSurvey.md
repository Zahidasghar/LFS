---
title: 'Exploring Labour Force Survey Data Using R '
author: "Zahid Asghar"
date: "April 3, 2020"
output:
    md_document:
    variant: markdown_github


---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```


```{r }
knitr::opts_chunk$set(echo = TRUE)
```


## Exploring LFS data using dplyr

There is huge amount of data collected at national and provincial level through various surveys. Most of the times this data remain unexplored and purpose of having data is not fulfilled properly. One of the reasons is that dealing with survey data is always time consuming and requires more skills than handling macroeconomic variables. Therefore, working with survey data, as per anecdotal evidence, is usually not a first preference of research students at post-graduate level.  
    One of the main objective of this document is to provide a document to students/scholars/academia/policy  which enables them to use the data easily. Secondly, R is mainly used these days but learning curve of R is steep. However, some of the packages in R like __tidyverse__ which consists of a number of packages including dplyr and ggplot2 make data transformation easier and faster. It has gained great popularity among R users and experise in it is one of the standard skill in R and is in high demand. By learning some fundamentals of __R__ for which I have uploaded [tutorials](https://www.youtube.com/c/datavisualizationandanalytics_ZahidAsghar), [how to install R and R Studio](https://www.youtube.com/watch?v=ZNBZevfYgo0) and some other videos which are labelled in playlist under *Econometric Analysis Using R* .
In the following I give an introduction to some main functions of dplyr and use it for LFS data. I shall elaborate some of the most extensively used functions of dplyr. Using LFS data makes it not only a learning exercise of dplyr but will also make it possible for many scholars to work with LFS data for research and policy analysis.
 
### Uploading Required Libraries
What are __R__ packages and libraries, please watch [video](https://youtu.be/SRSNdCZ_QnE).

```{r ,Data}
setwd("C:/Users/hp/OneDrive - Higher Education Commission")
library(foreign)
library(haven)
library(tidyverse)
library(gridExtra)
##LFS<-read_spss("LFS_labels.sav")
##input_data<-read.spss("LFS_labels.sav",use.value.labels = TRUE,to.data.frame = TRUE)
## saveRDS(input_data,file = "LFSurvey.rds")
LFSurvey<-readRDS("LFSurvey.rds")
```

### Selecting Variables
 
Instead of using whole data set, we select variables which will be used for carrying out this analsysis. You can use glimplse(LFS) and View(LFS) data commands with in this code chunk to see more details about data.

```{r , LFS}
LFS<-LFSurvey %>% 
  select(Gendar = S04C05 ,Age = S04C06, Marital_Status = S04C07, Literacy = S04C08, Edu_Level = S04C09, vocational_training = S04C11, duration_of_training_weeks = S04C13, last_month_earning_kind = S07C042, last_month_earning_cash = S07C043, enterprise_type = S05C11, earning_last_year = S07C053, education_level,occupation, industry,Region,PROVINCE,prov6) 

```

### Recoding Variables

In thd following chunk, we are recoding the variables: enterprise_type, Education_level and occupation. After recoding we use the command (or dplyr verb) __mutate__ for recoding. Additionally, most of the variables selected are factor (categorical), one needs to specify these variables as factors. Therefore we have specified these variables as factors. For more on dplyr verbs (commands), you may watch my video [dplyr mutate verb](https://www.youtube.com/watch?v=U9ETTwGYRUY). For other dplyr commands see videos exploring data part1 and part 2.

```{r, Recode LFS}
LFS <- mutate(LFS, enterprise_type_desc=recode(enterprise_type,'1'="Federal",'2'="Provincial",'3'="Local_body",'4'="Pub_Ent", '5'="Pub_Lim",'6'="Pvt_lim",'7'="Coop_Soc",'8'="Inv_own",'9'="Partnership",'10'="Other", .default = "Unmatched", .missing = "N/A"), Edu_Yrs=recode(education_level,"No formal education" = 0,"Nursery but below KG" = 0.5,"KG but below Primary" = 1, "Primary but below Middle" = 6, "Middle But below Matric" = 9, "Mattic but below intermediate" = 11, "Intermediate below Degree" = 12, "Degree in engineering" = 16, "Degree in Medicine" = 16,"Degree in computer" = 16), Main_occ=recode(occupation, 'Chief Exective, senior officials and legislators'="Managerial",'Admistrative and Commercial Managers'="Managerial",'Production and specialized service Managers'="Managerial",'Hospitality retail and other service Managers'="Managerial",'Science and engineering profesioanls'="Professionals",'Health professionals'="Professionals",'Teaching professionals'="Professionals",'Business and administration professionals'="Professionals", 'Informaion and communication technology professional'="Professionals",'Information and communication technology professional'="Professionals",'legal, social and cultural professional'="Professionals",'Scinnce and engineering associate professional'="Technician", 'Business and adminstration assocaite professionals'="Technician", 'Legal, social cultural and related associate professional'="Technician", 'Health associate professional'="Technician",'Informtion and communication Technician'="Technician",'Informtion and communication technicians'="Clerks" ,'Clerical and keyboard clerks'="Clerks", 'Business and adminstration assocaite professionals'="Clerks", 'Legal, social cultural and related associate professional'="Clerks", 'Informtion and communication Technician'="Clerks",'Clerical and keyboard clerks'="Clerks",'42'="Clerks", 'Numerical and material recording clerks'="Clerks", 'other clerical support workers'="Clerks",'personal service workers'="Services", 'salers workers'="Services", 'personal care workers'="Services",'protective service workers'="Services",'craft and related trade workers'="Craft",'Metal, machinary and related trade workers'="Craft", 'Handicraft and printing workers'="Craft", 'electrical and electronic trade workers'="Craft",'Food processing, wood working, garments and other craft related trade workers'="Craft",'stationery, plant and machine operators'="Operators", 'Assemblers'="Operators", 'Drivers and mobile plant operators'="Operators",'Cleaners and helpers'="Elementary", 'Agricultural, forestery and fishery labourers'="Elementary", 'Labourer in Mining, construction, manufacturing and transport'="Elementary", 'Food preparation Assistant'="Elementary",'Street and related sales and service workers'="Elementary",'Refuse workers and Elementary workers'="Elementary",'subsistence farmers, fishers, hunters a'="Elementary", 'msrlte oriented skilled fishery, forestery and hunting workers'="Elementary",'Market orineted skilled agriculture workers'="Skilled",'Refuse workers and elementary workers'="Elementary",'msrlte oriented skilled fishery, forestery and hunting workers'="Skilled",'subsistence farmers, fishers, hunters and gatherers'="Elementary",'armed forces'="Services",'Hospitality  retail and other service Managers'="Managerial", .default = 'Unmathed')) 

LFS <- LFS %>% 
  mutate(Main_occu=recode(Main_occ,'Managerial'=1, 'Professionals'=2, 'Technician'=3, 'Informtion and communication technicians'=4, 'Clerks'=4, 'Services'=5, 'Skilled'=9, 'Elementary'=6, 'Craft'=7, 'Operators'=8, 'Elementary'=5)) %>%
  mutate_at(vars(Gendar,Marital_Status,Literacy, Edu_Level, vocational_training,duration_of_training_weeks, education_level,occupation, industry,Region,PROVINCE,enterprise_type_desc,Edu_Yrs,Main_occu),as.factor)
```


## Exploratory Data Analysis
 Once we have selected relevant variables and have completed our other basic manipulation of the variables, next task is always to generate data visualization and summary statistics. According to 
 [Deaton Nobel Laureate] (https://scholar.princeton.edu/sites/default/files/deaton/files/deaton_randomization_revisited_v2_2019_01.pdf)  
 
 >  My own personal favorites are cross-tabulations and graphs that stay close to the data; the hard work lies in deciding what to put into them and how to process the data to learn something that we did not know before, or that changes minds. An appropriately constructed picture or cross-tabulation can undermine the credibility of a widely believed causal story, or enhance the credibility of a new one; such evidence is more informative about causes than a paper with the word “causal” in its title. The art is in knowing what to show.   
 
 So in the following section we are exploring some of the variables to get some basic insight. You can use the data in your own way. For visualizing data, you may find long commands here but these are very simple one and for basics you can watch a video [data visualization of gapminder dat](https://youtu.be/NQkxbpsLxI4)
 

```{r}

ch1 <- LFS %>% group_by(education_level) %>% summarise(count=n()) %>% mutate(percent = (count/sum(count))*100) %>% na.omit() %>%
  ggplot(aes(x=reorder(education_level, -percent), y=percent)) + geom_bar(stat = "identity", fill = "blue" )  +  
  geom_text(aes(label=paste(round(percent,2),"%", Sep="")), hjust=-.1 ) +   xlab("Education Level") + ylim(0, 40) +theme(axis.text.x=element_blank(), axis.title.x=element_blank()) + coord_flip()

ch2 <- LFS %>% group_by(Edu_Yrs) %>% summarise(count=n()) %>% mutate(percent = (count/sum(count))*100) %>% na.omit() %>%
  ggplot(aes(x=Edu_Yrs, y=percent)) + geom_bar(stat = "identity", fill = "blue" )  +  
  geom_text(aes(label=paste(round(percent,2),"%", Sep="")), hjust=-.1 ) +   xlab("Education # of Years") + ylim(0, 40) +theme(axis.text.x=element_blank(), axis.title.x=element_blank()) + coord_flip()
grid.arrange(ch1,ch2, ncol=2)


```

### Lets check how education level is distributed across genders

```{r}
LFS %>% group_by(Edu_Yrs, Gendar) %>% summarise(count=n()) %>% mutate(percent = (count/sum(count))*100) %>% na.omit() %>%
  ggplot(aes(x=Edu_Yrs, y=count, fill=Gendar)) + geom_bar(stat = "identity" ,position = 'dodge')  +  
  scale_fill_manual("Gendar", values = c("Male" = "blue", "Female" = "red")) +
  geom_text(aes(label=count),position = position_dodge(width = 1), hjust=-.2)+ ylim(0, 60000) +
  xlab("Education # of Years") +theme(axis.text.x=element_blank(), axis.title.x=element_blank()) + coord_flip()
```

### Checking the distribution of occupation level
```{r}
LFS %>% group_by(occupation) %>% summarise(count=n()) %>% mutate(percent = (count/sum(count))*100)
```

### Checking the distribution of variables
Once we have selected the required variables, we can explore the selected variables by visualising and generating summary statistics. In the following chunk EDA, we have grouped data by Marital status and explored how many belong to each type of marital status
```{r, EDA}
LFS %>% group_by(Marital_Status) %>% summarise(count=n()) %>% mutate(percent = (count/sum(count))*100) %>% 
  ggplot(aes(x=reorder(Marital_Status, -percent), y=percent)) + geom_bar(stat = "identity", fill = "blue" )  +  
  geom_text(aes(label=paste(round(percent,2),"%", Sep="")), hjust=-.1 ) +   xlab("Marital Status") + ylim(0, 40) +theme(axis.text.x=element_blank(), axis.title.x=element_blank()) + coord_flip()
```


###Checking the distribution of yearly earnings (note that this field has very few observations)

```{r}
summary(LFS$earning_last_year)

```

```{r}
LFS %>% select(last_month_earning_cash) %>% na.omit() %>% ggplot(aes(x=last_month_earning_cash)) + geom_histogram(bins = 100, color="blue") +  xlab("Earnings last year") + ylab("No of Individuals") 
```
### Histogram of log of monthly earnings


One of the objectives of log transformation is that skewed data may become log normal. So instead of taking monthly earning distribution as given above, I am plotting histogram of log(monthly earnings). We can observe than log transformation of the data has made the distribution symmetrical.

```{r}
LFS %>% select(last_month_earning_cash) %>% na.omit() %>% ggplot(aes(x=log(last_month_earning_cash))) + geom_histogram(bins = 100, color="blue") +  xlab("Earnings last year") + ylab("No of Individuals") 
```







## Differences between earnings by gender for various levels of education

It is important to note that in the LFS, education is measured at levels and not in years. We check last year yearnings against education level and gendar - note the concentration of reds to the left of each grouping.

```{r}
LFS %>% select(last_month_earning_cash, education_level, Gendar) %>% na.omit() %>% ggplot() + geom_point(aes(x=education_level,y=last_month_earning_cash,color=Gendar), alpha = .2) +    scale_color_manual(values = c("Male" = "blue", "Female" = "red")) +
  ylab("Monthly earnings") + xlab("Education Level")+coord_flip()
```

## Removing outliers 

Although removing outliers is not a good idea unless there are some editing erros. But for the purpose of finding out differences between male and female earnings, I am removing some large values and we notice how earnings between male and female. I have set y-axis maximum value for monthly earning to Rs.150,000/. 33 observations will be removed. On can notice that red dots are in general lower than blue dots which implies that for each education level, it seems women earn less than men. __Good visualization__ helps to ask good question for which hypothesis can be formulated and explored through using various models.



```{r}
LFS %>% select(last_month_earning_cash, education_level, Gendar) %>% na.omit() %>% ggplot() + geom_point(aes(x=education_level,y=last_month_earning_cash,color=Gendar), alpha = .2) +    scale_color_manual(values = c("Male" = "blue", "Female" = "red")) +
  ylab("Monthly earnings") + xlab("Education Level")+coord_flip()+ylim(0,150000)
```


## Regression Analysis

In this section we find relationship between monthly earnings by including some additional variables. Regression results are given as follows: 


```{r}
m<-lm(last_month_earning_cash~ Age + Gendar + Main_occu+Edu_Yrs + PROVINCE  ,data = LFS)
library(stargazer)
stargazer(m, type="text")
```


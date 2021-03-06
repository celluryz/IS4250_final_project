    ##Please SCROLL down to see the output tablend charts

    library(utils)
    library(dplyr)

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

    library(foreign)
    library(ggplot2)

    ## import data 
    origin_data_09 <- read.xport("CDBRFS09.XPT")

    sample_data_09 <- dplyr::select(origin_data_09, X_STATE, VETERAN2,  AGE, EMPLOY, HTM3, WTKG2, SEX, RACE2, INCOME2, EDUCA, SMOKDAY2, JOBACTIV, VIGCAT_, X_FRTINDX, X_BMI4)
    sample_data_09 <- dplyr::rename(sample_data_09, VETERAN = VETERAN2)
    sample_data_09 <- dplyr::filter(sample_data_09, X_STATE == 53, EMPLOY == 1 | EMPLOY == 2) 


    sample_data_all <- dplyr::filter(sample_data_09, VETERAN != 1 & VETERAN != 2 & VETERAN != 3) 
    sample_data_all <- dplyr::filter(sample_data_all, AGE >= 18 & AGE <= 64, X_BMI4 >= 1850 & X_BMI4 != 9999 ) 

    ## Sample data
    all_obese <- dplyr::filter(sample_data_all, X_BMI4 >= 3000)
    all_obese_count <- dplyr::count(all_obese)
    all_count <- dplyr::count(sample_data_all)
    all_pr = all_obese_count / all_count

    all_occupations = c("All")
    result_all = data.frame(all_occupations, all_pr)
    names(result_all)[2] <- "Prevalence Ratio"

    ## Prevelance Ratio by Sex

    female_data <- dplyr::filter(sample_data_all, SEX==2)
    female_data_obese <- dplyr::filter(female_data, X_BMI4 >= 3000)

    female_obese <- dplyr::count(female_data_obese)
    female_total <- dplyr::count(female_data)
    female_pr = female_obese / female_total

    male_data <- dplyr::filter(sample_data_all, SEX==1)
    male_data_obese <- dplyr::filter(male_data, X_BMI4 >= 3000)

    male_obese <- dplyr::count(male_data_obese)
    male_total <- dplyr::count(male_data)
    male_pr = male_obese / male_total

    ob_pr_sex_female = female_pr/female_pr
    ob_pr_sex_male_female = male_pr/female_pr

    ob_pr_sex <- dplyr::bind_rows(female_pr , male_pr)
    ob_pr_sex_adjusted <- dplyr::bind_rows(ob_pr_sex_female, ob_pr_sex_male_female)

    sex = c("Female","Male")
    result_sex=data.frame(sex, ob_pr_sex, ob_pr_sex_adjusted)
    names(result_sex)[2] <- "Prevalence Ratio"
    names(result_sex)[3] <- "Adjusted Prevalence Ratio"

    ## Prevelance Ratio by Age
    young_age_data <- dplyr::filter(sample_data_all, AGE >= 18 & AGE <= 29)
    young_age_data_obese <- dplyr::filter(young_age_data, X_BMI4 >= 3000)
    young_obese <- dplyr::count(young_age_data_obese)
    young_total <- dplyr::count(young_age_data)
    young_age_pr = young_obese/young_total 

    middle_age_data <- dplyr::filter(sample_data_all, AGE >= 30 & AGE <= 44)
    middle_age_data_obese <- dplyr::filter(middle_age_data, X_BMI4 >= 3000)
    middle_obese <- dplyr::count(middle_age_data_obese)
    middle_total <- dplyr::count(middle_age_data)
    middle_age_pr = middle_obese/middle_total

    old_age_data <- dplyr::filter(sample_data_all, AGE >= 45 & AGE <= 64)
    old_age_data_obese <- dplyr::filter(old_age_data, X_BMI4 >= 3000)
    old_obese <- dplyr::count(old_age_data_obese)
    old_total <- dplyr::count(old_age_data)
    old_age_pr = old_obese/old_total

    ob_pr_age_young = young_age_pr/young_age_pr
    ob_pr_age_middle_young = middle_age_pr/young_age_pr
    ob_pr_age_old_young = old_age_pr/young_age_pr

    ob_pr_age <- dplyr::bind_rows(young_age_pr, middle_age_pr)
    ob_pr_age <- dplyr::bind_rows(ob_pr_age,old_age_pr)

    ob_pr_age_adjusted <- dplyr::bind_rows(ob_pr_age_young,ob_pr_age_middle_young)
    ob_pr_age_adjusted <- dplyr::bind_rows(ob_pr_age_adjusted,ob_pr_age_old_young)

    age = c("18-29", "30-44", "45-64")
    result_age = data.frame(age, ob_pr_age, ob_pr_age_adjusted)
    names(result_age)[2] <- "Prevalence Ratio"
    names(result_age)[3] <- "Adjusted Prevalence Ratio"

    ## Prevelance Ratio by Race

    white_data <- dplyr::filter(sample_data_all, RACE2 == 1)
    white_data_obese <- dplyr::filter(white_data, X_BMI4 >= 3000)
    white_obese <- dplyr::count(white_data_obese)
    white_total <- dplyr::count(white_data)
    white_pr = white_obese/white_total 

    hispanic_data <- dplyr::filter(sample_data_all, RACE2 == 8)
    hispanic_data_obese <- dplyr::filter(hispanic_data, X_BMI4 >= 3000)
    hispanic_obese <- dplyr::count(hispanic_data_obese)
    hispanic_total <- dplyr::count(hispanic_data)
    hispanic_pr = hispanic_obese/hispanic_total

    other_race_data <- dplyr::filter(sample_data_all, RACE2 != 8 & RACE2 != 1)
    other_race_data_obese <- dplyr::filter(other_race_data, X_BMI4 >= 3000)
    other_race_obese <- dplyr::count(other_race_data_obese)
    other_race_total <- dplyr::count(other_race_data)
    other_race_pr = other_race_obese/other_race_total

    ob_pr_age_white = white_pr/white_pr
    ob_pr_age_hispanic = hispanic_pr/white_pr
    ob_pr_age_other_race = other_race_pr/white_pr

    ob_pr_race <- dplyr::bind_rows(white_pr, hispanic_pr)
    ob_pr_race <- dplyr::bind_rows(ob_pr_race, other_race_pr)

    ob_pr_race_adjusted <- dplyr::bind_rows(ob_pr_age_white,ob_pr_age_hispanic)
    ob_pr_race_adjusted <- dplyr::bind_rows(ob_pr_race_adjusted,ob_pr_age_other_race)

    race = c("White", "Hispanic", "Other")
    result_race = data.frame(race, ob_pr_race, ob_pr_race_adjusted)
    names(result_race)[2] <- "Prevalence Ratio"
    names(result_race)[3] <- "Adjusted Prevalence Ratio"

    ## Prevelance Ratio by Annual Household Income

    income <- dplyr::filter(sample_data_all, INCOME2 != 77 & INCOME2 != 99)

    low_income_data <- dplyr::filter(income, INCOME2 < 6)
    low_income_data_obese <- dplyr::filter(low_income_data, X_BMI4 >= 3000)
    low_income_obese <- dplyr::count(low_income_data_obese)
    low_income_total <- dplyr::count(low_income_data)
    low_income_pr = low_income_obese/low_income_total 

    middle_income_data <- dplyr::filter(income, INCOME2 >= 6 & INCOME2 < 8)
    middle_income_data_obese <- dplyr::filter(middle_income_data, X_BMI4 >= 3000)
    middle_income_obese <- dplyr::count(middle_income_data_obese)
    middle_income_total <- dplyr::count(middle_income_data)
    middle_income_pr = middle_income_obese/middle_income_total

    high_income_data <- dplyr::filter(income, INCOME2 >= 8)
    high_income_data_obese <- dplyr::filter(high_income_data, X_BMI4 >= 3000)
    high_income_obese <- dplyr::count(high_income_data_obese)
    high_income_total <- dplyr::count(high_income_data)
    high_income_pr = high_income_obese/high_income_total

    ob_pr_income_high = high_income_pr/high_income_pr
    ob_pr_income_middle_high = middle_income_pr/high_income_pr
    ob_pr_income_low_high = low_income_pr/high_income_pr

    ob_pr_income <- dplyr::bind_rows(low_income_pr, middle_income_pr)
    ob_pr_income <- dplyr::bind_rows(ob_pr_income,high_income_pr)


    ob_pr_income_adjusted <- dplyr::bind_rows(ob_pr_income_low_high, ob_pr_income_middle_high)
    ob_pr_income_adjusted <- dplyr::bind_rows(ob_pr_income_adjusted,ob_pr_income_high)

    household_income = c("< 35000", "35000-74999", ">= 75000")
    result_income = data.frame(household_income, ob_pr_income, ob_pr_income_adjusted)
    names(result_income)[2] <- "Prevalence Ratio"
    names(result_income)[3] <- "Adjusted Prevalence Ratio"

    ## Prevelance Ratio by Education Attachment

    education <- dplyr::filter(sample_data_all, EDUCA != 9)

    low_edu_data <- dplyr::filter(education, EDUCA < 4)
    low_edu_data_obese <- dplyr::filter(low_edu_data, X_BMI4 >= 3000)
    low_edu_obese <- dplyr::count(low_edu_data_obese)
    low_edu_total <- dplyr::count(low_edu_data)
    low_edu_pr = low_edu_obese/low_edu_total 

    middle_edu_data <- dplyr::filter(education, EDUCA >= 4 & EDUCA <6)
    middle_edu_data_obese <- dplyr::filter(middle_edu_data, X_BMI4 >= 3000)
    middle_edu_obese <- dplyr::count(middle_edu_data_obese)
    middle_edu_total <- dplyr::count(middle_edu_data)
    middle_edu_pr = middle_edu_obese/middle_edu_total 

    high_edu_data <- dplyr::filter(education, EDUCA == 6)
    high_edu_data_obese <- dplyr::filter(high_edu_data, X_BMI4 >= 3000)
    high_edu_obese <- dplyr::count(high_edu_data_obese)
    high_edu_total <- dplyr::count(high_edu_data)
    high_edu_pr = high_edu_obese/high_edu_total 

    ob_pr_edu_high = high_edu_pr/high_edu_pr
    ob_pr_edu_middle_high = middle_edu_pr/high_edu_pr
    ob_pr_edu_low_high = low_edu_pr/high_edu_pr

    ob_pr_edu <- dplyr::bind_rows(low_edu_pr, middle_edu_pr)
    ob_pr_edu <- dplyr::bind_rows(ob_pr_edu,high_edu_pr)
    ob_pr_edu_adjusted <- dplyr::bind_rows(ob_pr_edu_low_high, ob_pr_edu_middle_high)
    ob_pr_edu_adjusted <- dplyr::bind_rows(ob_pr_edu_adjusted,ob_pr_edu_high)

    education_attachment = c("Less than grade 12", "High school or some college", "College degree or higher")
    result_education = data.frame(education_attachment, ob_pr_edu, ob_pr_edu_adjusted)

    ## Prevelance Ratio by Smoke

    smoke <- dplyr::filter(sample_data_all, SMOKDAY2 == 1 | SMOKDAY2 == 2)
    smoke_obese_data <- dplyr::filter(smoke, X_BMI4 >= 3000)

    smoke_obese <- dplyr::count(smoke_obese_data)
    smoke_total <- dplyr::count(smoke)
    smoke_pr = smoke_obese/smoke_total

    nonsmoke <- dplyr::filter(sample_data_all, SMOKDAY2 != 9 & SMOKDAY2 != 1 & SMOKDAY2 != 2)
    nonsmoke_obese_data <- dplyr::filter(nonsmoke, X_BMI4 >= 3000)

    nonsmoke_obese <- dplyr::count(nonsmoke_obese_data)
    nonsmoke_total <- dplyr::count(nonsmoke)
    nonsmoke_pr = nonsmoke_obese / nonsmoke_total


    ob_pr_smoke = smoke_pr/smoke_pr
    ob_pr_nonsmoke_smoke = nonsmoke_pr/smoke_pr

    ob_pr_smoke_table <- dplyr::bind_rows(smoke_pr, nonsmoke_pr)
    ob_pr_smoke_table_adjusted <- dplyr::bind_rows(ob_pr_smoke , ob_pr_nonsmoke_smoke)

    smoking_habit = c("Current smoker", "Non current smoker")
    result_smoke = data.frame(smoking_habit, ob_pr_smoke_table, ob_pr_smoke_table_adjusted)
    names(result_smoke)[2] <- "Prevalence Ratio"
    names(result_smoke)[3] <- "Adjusted Prevalence Ratio"

    ## Prevelance Ratio by level of Fruits and Vegetables intakes

    frequentFruit <- dplyr::filter(sample_data_all, X_FRTINDX == 4)
    frequentFruit_obese_data <- dplyr::filter(frequentFruit, X_BMI4 >= 3000)

    frequentFruit_obese <- dplyr::count(frequentFruit_obese_data)
    frequentFruit_total <- dplyr::count(frequentFruit)
    frequentFruit_pr = frequentFruit_obese/frequentFruit_total

    nonfrequentFruit <- dplyr::filter(sample_data_all, X_FRTINDX == 1 | X_FRTINDX ==2 | X_FRTINDX == 3)
    nonfrequentFruit_obese_data <- dplyr::filter(nonfrequentFruit, X_BMI4 >= 3000)

    nonfrequentFruit_obese <- dplyr::count(nonfrequentFruit_obese_data)
    nonfrequentFruit_total <- dplyr::count(nonfrequentFruit)
    nonfrequentFruit_pr = nonfrequentFruit_obese / nonfrequentFruit_total


    ob_pr_nonfrequentFruit_nonfrequentFruit = nonfrequentFruit_pr/nonfrequentFruit_pr
    ob_pr_frequentFruit_nonfrequentFruit = frequentFruit_pr/nonfrequentFruit_pr

    ob_pr_frequentFruit_table <- dplyr::bind_rows(nonfrequentFruit_pr, frequentFruit_pr)
    ob_pr_frequentFruit_table_adjusted <- dplyr::bind_rows(ob_pr_nonfrequentFruit_nonfrequentFruit, ob_pr_frequentFruit_nonfrequentFruit)

    fruits_vegetables_consumption = c("Fruit and vegetable consumption < 5 servings/day", "Fruit and vegetable consumption >= 5 servings/day")
    result_fruit = data.frame(fruits_vegetables_consumption, ob_pr_frequentFruit_table, ob_pr_frequentFruit_table_adjusted)

    names(result_fruit)[2] <- "Prevalence Ratio"
    names(result_fruit)[3] <- "Adjusted Prevalence Ratio"

    ## Prevelance Ratio by level of Leisure-time Physical Activities

    vigorousLTPA <- dplyr::filter(sample_data_all, VIGCAT_ == 1)
    vigorousLTPA_obese_data <- dplyr::filter(vigorousLTPA, X_BMI4 >= 3000)

    vigorousLTPA_obese <- dplyr::count(vigorousLTPA_obese_data)
    vigorousLTPA_total <- dplyr::count(vigorousLTPA)
    vigorousLTPA_pr = vigorousLTPA_obese/vigorousLTPA_total

    nonvigorousLTPA <- dplyr::filter(sample_data_all, VIGCAT_ == 2 | VIGCAT_ == 3)
    nonvigorousLTPA_obese_data <- dplyr::filter(nonvigorousLTPA, X_BMI4 >= 3000)

    nonvigorousLTPA_obese <- dplyr::count(nonvigorousLTPA_obese_data)
    nonvigorousLTPA_total <- dplyr::count(nonvigorousLTPA)
    nonvigorousLTPA_pr = nonvigorousLTPA_obese / nonvigorousLTPA_total


    ob_pr_nonvigorousLTPA_nonvigorousLTPA = nonvigorousLTPA_pr/nonvigorousLTPA_pr
    ob_pr_vigorousLTPA_nonvigorousLTPA = vigorousLTPA_pr/nonvigorousLTPA_pr

    ob_pr_vigorousLTPA_table <- dplyr::bind_rows(nonvigorousLTPA_pr, vigorousLTPA_pr)
    ob_pr_vigorousLTPA_table_adjusted <- dplyr::bind_rows(ob_pr_nonvigorousLTPA_nonvigorousLTPA, ob_pr_vigorousLTPA_nonvigorousLTPA)

    VigorousLeisureTimePhysicalActivity = c("<20 min/d and <3 times/wk", " >= 20 min/d and >= 3 times/wk")
    result_LTPA = data.frame(VigorousLeisureTimePhysicalActivity, ob_pr_vigorousLTPA_table, ob_pr_vigorousLTPA_table_adjusted)

    names(result_LTPA)[2] <- "Prevalence Ratio"
    names(result_LTPA)[3] <- "Adjusted Prevalence Ratio"

    ## Prevelance Ratio by Occupational Physical Activities

    physicalDemandJob <- dplyr::filter(sample_data_all, JOBACTIV == 2 | JOBACTIV == 3)
    physicalDemandJob_obese_data <- dplyr::filter(physicalDemandJob, X_BMI4 >= 3000)

    physicalDemandJob_obese <- dplyr::count(physicalDemandJob_obese_data)
    physicalDemandJob_total <- dplyr::count(physicalDemandJob)
    physicalDemandJob_pr = physicalDemandJob_obese/physicalDemandJob_total

    nonphysicalDemandJob <- dplyr::filter(sample_data_all, JOBACTIV == 1)
    nonphysicalDemandJob_obese_data <- dplyr::filter(nonphysicalDemandJob, X_BMI4 >= 3000)

    nonphysicalDemandJob_obese <- dplyr::count(nonphysicalDemandJob_obese_data)
    nonphysicalDemandJob_total <- dplyr::count(nonphysicalDemandJob)
    nonphysicalDemandJob_pr = nonphysicalDemandJob_obese / nonphysicalDemandJob_total


    ob_pr_nonphysicalDemandJob_nonphysicalDemandJob = nonphysicalDemandJob_pr/nonphysicalDemandJob_pr
    ob_pr_physicalDemandJob_nonphysicalDemandJob = physicalDemandJob_pr/nonphysicalDemandJob_pr

    ob_pr_physicalDemandJob_table <- dplyr::bind_rows(nonphysicalDemandJob_pr, physicalDemandJob_pr)
    ob_pr_physicalDemandJob_table_adjusted <- dplyr::bind_rows(ob_pr_nonphysicalDemandJob_nonphysicalDemandJob, ob_pr_physicalDemandJob_nonphysicalDemandJob)

    occupationalPhysicalActivity = c("Nonphysically demanding", "Physically demanding")
    result_OPA = data.frame(occupationalPhysicalActivity, ob_pr_physicalDemandJob_table, ob_pr_physicalDemandJob_table_adjusted)

    names(result_OPA)[2] <- "Prevalence Ratio"
    names(result_OPA)[3] <- "Adjusted Prevalence Ratio"

    ## In below tables, the 2nd column represents the prevelance ratio of all the sample that meets the requirement corresponding in 1st column. The 3 column is the adjusted ratio. If one has a value 1 in that column, it means it is the reference ratio.

    ## Sample data Prevalence Ratio
    result_all

    ##   all_occupations Prevalence Ratio
    ## 1             All        0.2771925

    ## Prevelance Ratio by Sex
    result_sex

    ##      sex Prevalence Ratio Adjusted Prevalence Ratio
    ## 1 Female        0.2703583                  1.000000
    ## 2   Male        0.2869479                  1.061361

    colnames(result_sex) <- c("Prevalence Ratio", "pr")
    result_sex$pr=as.numeric(format(round(result_sex$pr,4), nsmall=4))
    ggplot(result_sex, aes(sex,pr*100))+geom_bar(colour="black",width=.5,stat="identity")+xlab("Sex")+ylab("Prevalence of Obesity (%)")+geom_text(aes(label = pr*100), vjust=-1, size = 5)+ggtitle("Prevalence of Obesity by Sex")

![](output_files/figure-markdown_strict/unnamed-chunk-11-1.png)  

    ## Prevelance Ratio by Age
    result_age

    ##     age Prevalence Ratio Adjusted Prevalence Ratio
    ## 1 18-29        0.2317881                  1.000000
    ## 2 30-44        0.2720655                  1.173768
    ## 3 45-64        0.2857777                  1.232927

    colnames(result_age) <- c("Prevalence Ratio", "pr")
    result_age$pr=as.numeric(format(round(result_age$pr,4), nsmall=4))
    ggplot(result_age, aes(age,pr*100))+geom_bar(colour="black",width=.5,stat="identity")+xlab("Age")+ylab("Prevalence of Obesity (%)")+geom_text(aes(label = pr*100), vjust=-1, size = 5)+ggtitle("Prevalence of Obesity by Age")

![](output_files/figure-markdown_strict/unnamed-chunk-11-2.png)  

    ## Prevelance Ratio by Race
    result_race

    ##       race Prevalence Ratio Adjusted Prevalence Ratio
    ## 1    White        0.2766767                 1.0000000
    ## 2 Hispanic        0.3214286                 1.1617479
    ## 3    Other        0.2508591                 0.9066868

    colnames(result_race) <- c("Prevalence Ratio", "pr")
    result_race$pr=as.numeric(format(round(result_race$pr,4), nsmall=4))
    ggplot(result_race, aes(race,pr*100))+geom_bar(colour="black",width=.5,stat="identity")+xlab("Race")+ylab("Prevalence of Obesity (%)")+geom_text(aes(label = pr*100), vjust=-1, size = 5)+ggtitle("Prevalence of Obesity by Race")

![](output_files/figure-markdown_strict/unnamed-chunk-11-3.png)  

    ## Prevelance Ratio by Annual Household Income
    result_income

    ##   household_income Prevalence Ratio Adjusted Prevalence Ratio
    ## 1          < 35000        0.3174377                  1.332012
    ## 2      35000-74999        0.3035646                  1.273798
    ## 3         >= 75000        0.2383144                  1.000000

    colnames(result_income) <- c("Prevalence Ratio", "pr")
    result_income$pr=as.numeric(format(round(result_income$pr,4), nsmall=4))
    ggplot(result_income, aes(household_income,pr*100))+geom_bar(colour="black",width=.5,stat="identity")+xlab("Household Income")+ylab("Prevalence of Obesity (%)")+geom_text(aes(label = pr*100), vjust=-1, size = 5)+ggtitle("Prevalence of Obesity by Househoud Income")

![](output_files/figure-markdown_strict/unnamed-chunk-11-4.png)  

    ## Prevelance Ratio by Education Attachment
    result_education

    ##          education_attachment         n      n.1
    ## 1          Less than grade 12 0.2996633 1.285138
    ## 2 High school or some college 0.3165746 1.357664
    ## 3    College degree or higher 0.2331759 1.000000

    colnames(result_education) <- c("Prevalence Ratio", "pr")
    result_education$pr=as.numeric(format(round(result_education$pr,4), nsmall=4))
    ggplot(result_education, aes(education_attachment,pr*100))+geom_bar(colour="black",width=.5,stat="identity")+xlab("Education Attachment")+ylab("Prevalence of Obesity (%)")+geom_text(aes(label = pr*100), vjust=-1, size = 5)+ggtitle("Prevalence of Obesity by Educational Attachment")

![](output_files/figure-markdown_strict/unnamed-chunk-11-5.png)  

    ## Prevelance Ratio by Smoke
    result_smoke

    ##        smoking_habit Prevalence Ratio Adjusted Prevalence Ratio
    ## 1     Current smoker        0.2551230                  1.000000
    ## 2 Non current smoker        0.2988134                  1.171252

    colnames(result_smoke) <- c("Prevalence Ratio", "pr")
    result_smoke$pr=as.numeric(format(round(result_smoke$pr,4), nsmall=4))
    ggplot(result_smoke, aes(smoking_habit,pr*100))+geom_bar(colour="black",width=.5,stat="identity")+xlab("Smoking Habit")+ylab("Prevalence of Obesity (%)")+geom_text(aes(label = pr*100), vjust=-1, size = 5)+ggtitle("Prevalence of Obesity by Smoking Habit")

![](output_files/figure-markdown_strict/unnamed-chunk-11-6.png)  

    ## Prevelance Ratio by level of Fruits and Vegetables intakes
    result_fruit

    ##                       fruits_vegetables_consumption Prevalence Ratio
    ## 1  Fruit and vegetable consumption < 5 servings/day        0.2934722
    ## 2 Fruit and vegetable consumption >= 5 servings/day        0.2292892
    ##   Adjusted Prevalence Ratio
    ## 1                 1.0000000
    ## 2                 0.7812977

    colnames(result_fruit) <- c("Prevalence Ratio", "pr")
    result_fruit$pr=as.numeric(format(round(result_fruit$pr,4), nsmall=4))
    ggplot(result_fruit, aes(fruits_vegetables_consumption,pr*100))+geom_bar(colour="black",width=.5,stat="identity")+xlab("Fruit & Vegetable Consumption")+ylab("Prevalence of Obesity (%)")+geom_text(aes(label = pr*100), vjust=-1, size = 5)+ggtitle("Prevalence of Obesity by Fruit & Vegetable Consumption")

![](output_files/figure-markdown_strict/unnamed-chunk-11-7.png)  

    ## Prevelance Ratio by level of Leisure-time Physical Activities
    result_LTPA

    ##   VigorousLeisureTimePhysicalActivity Prevalence Ratio
    ## 1           <20 min/d and <3 times/wk        0.3175772
    ## 2       >= 20 min/d and >= 3 times/wk        0.1845878
    ##   Adjusted Prevalence Ratio
    ## 1                 1.0000000
    ## 2                 0.5812375

    colnames(result_LTPA) <- c("Prevalence Ratio", "pr")
    result_LTPA$pr=as.numeric(format(round(result_LTPA$pr,4), nsmall=4))
    ggplot(result_LTPA, aes(VigorousLeisureTimePhysicalActivity,pr*100))+geom_bar(colour="black",width=.5,stat="identity")+xlab("Vigorous Leisure Time Activity")+ylab("Prevalence of Obesity (%)")+geom_text(aes(label = pr*100), vjust=-1, size = 5)+ggtitle("Prevalence of Obesity by Vigorous Leisure Time Activity")

![](output_files/figure-markdown_strict/unnamed-chunk-11-8.png)  

    ## Prevelance Ratio by Occupational Physical Activities
    result_OPA

    ##   occupationalPhysicalActivity Prevalence Ratio Adjusted Prevalence Ratio
    ## 1      Nonphysically demanding        0.2829642                 1.0000000
    ## 2         Physically demanding        0.2613438                 0.9235932

    colnames(result_OPA) <- c("Prevalence Ratio", "pr")
    result_OPA$pr=as.numeric(format(round(result_OPA$pr,4), nsmall=4))
    ggplot(result_OPA, aes(occupationalPhysicalActivity,pr*100))+geom_bar(colour="black",width=.5,stat="identity")+xlab("Occupational Physical Activity")+ylab("Prevalence of Obesity (%)")+geom_text(aes(label = pr*100), vjust=-1, size = 5)+ggtitle("Prevalence of Obesity by Occupational Physical Activity")

![](output_files/figure-markdown_strict/unnamed-chunk-11-9.png)

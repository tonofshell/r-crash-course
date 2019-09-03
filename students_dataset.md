Students Dataset
================
Adam Shelton
8/30/2019

## Generate Names

Names are webscraped from randomly generated names from
[behindthename.com](http://www.behindthename.com)

``` r
names = character()

for (i in 1:100) {
  names_page = read_html("http://www.behindthename.com/random/random.php?number=2&sets=5&gender=both&surname=&randomsurname=yes&usage_afr=1&usage_chi=1&usage_eng=1&usage_fre=1&usage_ger=1&usage_jap=1&usage_spa=1")

  names = c(names, names_page %>% html_nodes("center div") %>% html_text() %>% str_squish() %>% .[1:5])
}

drop_longer = Vectorize(function(char_vect, len) {
  if (length(char_vect) > len) {
    return(char_vect[1:len])
  }
  return(char_vect)
})

student_data = names %>% str_split(" ") %>% drop_longer(3) %>% unlist() %>% matrix(ncol = 3, byrow = TRUE) %>% as_tibble(.name_repair = ~ c("first", "middle", "last"))
```

## Generate Base Grades

``` r
num_to_letter_grade = function(x) {
  if (x > 89) {
    return("A")
  }
  if (x > 79) {
    return("B")
  }
  if (x > 69) {
    return("C")
  }
  if (x > 59) {
    return("D")
  }
  return("F")
}

add_error = function(x, std_dev = 1) {
  error = rnorm(length(x), 0, std_dev)
  return(x + error)
}

rescale_grades = function(x) {
  scale_coef = 100 / max(x)
  return((x * scale_coef) - rnorm(1, 0.25)) 
}

get_last_school_year = function(x) {
  cur_mon = as.Date(x) %>% format("%m") %>% as.numeric()
  cur_year = as.Date(x) %>% format("%Y") %>% as.numeric() 
  if (cur_mon > 6) {
    return(cur_year - 1)
  }
  return(cur_year - 2)
}

age_grade_bump = function(x, age) {
  return(x + (runif(length(x), 0.15, 0.5) * (age - min(age))))
}

last_sch_yr = Sys.Date() %>% get_last_school_year()
years_back = 4

student_data$grade = nrow(student_data) %>% runif(9, 12) %>% round()
student_data$age = (student_data$grade + 5.5 + rnorm(nrow(student_data))) %>% round()
student_data$school = nrow(student_data) %>% runif(1, 3) %>% round() %>% factor(levels = c(1,2,3), labels = c("Oakwood", "Shady Willow", "Pine Field"))
student_data$math_grade = nrow(student_data) %>% rnorm(72, 9) %>%  add_error(0.5) %>% age_grade_bump(student_data$age) %>% {ifelse(student_data$school == "Oakwood", . - rnorm(length(.), 3), .)} %>% round()
student_data$english_grade = student_data$math_grade %>% add_error(8) %>% rescale_grades() %>% {ifelse(student_data$school == "Oakwood" |  student_data$school == "Pine Field", . - rnorm(length(.), 3), .)} %>% round()
student_data$science_grade = student_data$math_grade %>% add_error(7) %>% rescale_grades() %>% {ifelse(student_data$school == "Oakwood" |  student_data$school == "Shady Willow", . -rnorm(length(.), 3), .)} %>% round()
student_data$social_studies_grade = ((student_data$english_grade + student_data$science_grade) / 2) %>% add_error(6) %>% rescale_grades() %>% {ifelse(student_data$school == "Oakwood" |  student_data$school == "Pine Field", . - rnorm(length(.), 3), .)}  %>% round()
student_data %>% mutate(school = as.numeric(school)) %>% select(-first, -middle, -last) %>% cor() %>% ggcorrplot()
```

![](students_dataset_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

## Generate Additional Grades

``` r
more_grades = function(year, data_set, min_year, grade_names = c("math_grade", "english_grade", "science_grade", "social_studies_grade")) {
  grade_dif = max(data_set$grade)  - data_set$grade
  year_dif = year - min_year
  for (n in grade_names) {
  new_grades = data_set[[n]] %>% add_error(3) %>% round()
  missing_grades = rep(NA, length(new_grades))
  data_set[[paste(n, year, sep = "_")]] = ifelse(grade_dif <= year_dif, new_grades, missing_grades) %>% {ifelse(. < 100, ., rep(100, length(.)) )}
  }
  return(data_set)
}

for (year in (last_sch_yr - years_back + 1):last_sch_yr) {
  student_data = more_grades(year, student_data, (last_sch_yr - years_back + 1))
}

student_data = student_data %>% select(-math_grade, -english_grade, -science_grade, -social_studies_grade)
```

## Save Data

``` r
write_csv(student_data, here("student_data.csv"))
```
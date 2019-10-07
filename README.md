# A Crash Course in R

This course walks through the essentials for getting started in R and R Studio, as well as four basic areas of using R for data science, including:
  
  - Data Structures
  - Data Wrangling
  - Communicating Results
  - Programming
  
Much of the material for this course was developed with reference to [R for Data Science](https://r4ds.had.co.nz/) by Garrett Grolemund and Hadley Wickham. This free eBook covers many of the same topics in this course in more detail, and is worth a read if you want to learn more!

## Course Preparation
1. Install R from [cran.r-project.org](https://cran.r-project.org/)
2. Install RStudio from [rstudio.com](https://www.rstudio.com/products/rstudio/download/#download)
3. That's it, you're all set!

If you already have RStudio and R installed

1. Launch RStudio and check the version of R currently installed (it should show up at launch in the `Console` tab)
2. Check if there is a newer version of R on [cran.r-project.org](https://cran.r-project.org/) and install if necessary
3. Verify that the latest version of RStudio is installed by going to `Help` > `Check for Updates`
4. In RStudio, go to `Tools` > `Check for Package Updates` and install any package updates
5. That's it, you're all set!

## Start the Course
- [View as a Presentation](https://tonofshell.me/r-crash-course/presentation.html)
- [View as a Document](presentation_doc.md)

## About the `student_data` Data-set
- Completely synthetic data designed to mimic the grades of 500 high school students from three different schools
- `grade_level` and `age` are assumed to be at the time of the last school year in the data-set
- There are separate variables for each subject and year of students' grades
- Used for problem sets at the end of each module
- View the [R Markdown document](students_dataset.md) for its creation, or [download the latest release](https://github.com/tonofshell/r-crash-course/releases/latest)
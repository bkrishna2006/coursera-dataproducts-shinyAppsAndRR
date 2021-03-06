RWH- my first Web App created using Shiny
========================================================
author: Balaji K (bkrishna2006@gmail.com)
date: 18-Feb-2018
autosize: true

Introduction - slide 2 
========================================================

- Rain water harvesting (RWH) - Web Application created using Shiny
- Uses 2 sliders and 1 dropdown list for User input
- Provides 3 Output tabs (1) textual summary (2) interactive graphics and (3) table 

The application is published in shinyapps.io -->

https://bkrishna2006.shinyapps.io/rwh_myfirstshinyapp/

- The application, based on user input, calculates & displays the User's water consumption and the potential for him/her to harvest RW.
- The Summary tab displays these details textually.
- The Plot tab displays the variation of RWH capacity with respect to the roof area the user has, and his district's annual rainfall.
- The table is a look-up of district-wise annual rainfall in inches  

Code References - slide 3
=======================================================  
- github repository -->
https://github.com/bkrishna2006/coursera-dataproducts-shinyAppsAndRR

- ui.R code reference from github -> https://github.com/bkrishna2006/coursera-dataproducts-shinyAppsAndRR/blob/master/ui.R

- server.R code reference from github ->
https://github.com/bkrishna2006/coursera-dataproducts-shinyAppsAndRR/blob/master/server.R

- District wise annual rainfall data - Source Courtesy -->

https://data.gov.in/resources/district-rainfall-normal-mm-monthly-seasonal-and-annual-data-period-1951-2000


Code walk-through - slide 4
========================================================

The below code is to get the User's input of district and display the district's annual rainfall from a lookup table.

```r
library(shiny)
shinyUI(fluidPage(
    mainPanel(
      selectInput(inputId = "district",label = "Select the District", 
      choices = c("THIRUVANANTHA", "PALAKKAD","CANNUR"),
      selected = "THIRUVANANTHA", multiple = FALSE),
      h4("The district you selected is: "), textOutput("selected_district"),
      h4("Annual rainfall (in inches) in your district is: "), textOutput("distr_annrf_gal")
    ) ) ) 
```

<!--html_preserve--><div class="container-fluid">
<div class="col-sm-8">
<div class="form-group shiny-input-container">
<label class="control-label" for="district">Select the District</label>
<div>
<select id="district"><option value="THIRUVANANTHA" selected>THIRUVANANTHA</option>
<option value="PALAKKAD">PALAKKAD</option>
<option value="CANNUR">CANNUR</option></select>
<script type="application/json" data-for="district" data-nonempty="">{}</script>
</div>
</div>
<h4>The district you selected is: </h4>
<div id="selected_district" class="shiny-text-output"></div>
<h4>Annual rainfall (in inches) in your district is: </h4>
<div id="distr_annrf_gal" class="shiny-text-output"></div>
</div>
</div><!--/html_preserve-->
Code walk-through - slide 5
========================================================

```r
library(dplyr)
rainfall <- read.csv(file = "Kerala_District_Rainfall_Normal_0.csv")
mytib <- tbl_df(data = rainfall)
mytib1 <- select(.data = mytib,2,15) %>% mutate(ANNUAL_inches = ANNUAL * 0.0393701)
class(mytib1)
```

```
[1] "tbl_df"     "tbl"        "data.frame"
```

```r
shinyServer(function(input, output) {
   output$selected_district <- reactive({input$district})
   myval1fun <- reactive({
     mytib2 <- select(filter(mytib1, DISTRICT == input$district),3)
     myval1 <- mytib2$ANNUAL_inches
     return(myval1)
   }) 
   output$distr_annrf_gal  <- reactive({ myval1fun() })
})
# shinyApp(ui = ui, server = server)
```
Note: Though my webapp hosted in shinyapps.io works interactively without issues, when documenting it using R presentation, the interactivity is affected.  I think it's a limitation with R presentation as I could see that the same has been reported by others as well --> 

https://support.rstudio.com/hc/en-us/community/posts/213108307-Will-future-RPres-support-Shiny-Apps-

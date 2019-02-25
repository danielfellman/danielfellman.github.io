---
title: "Building interactive apps using shiny"
date: "2018-01-08"
tags: [R, data science]
excerpt: ""
---

I will open my blog journey with a brief tutorial on how you can build a "shiny" app in R for visualizing relationships between variables.

Before you start learning shiny I'd recommend you to take the ggplot2 courses offered in Datacamp [part 1](https://www.datacamp.com/courses/data-visualization-with-ggplot2-1) and [part 2](https://www.datacamp.com/courses/data-visualization-with-ggplot2-2), it will make this tutorial muche more interpretable.

<br> <br>
Start with loading the required packages in R and your dataframe
```r
# Libraries
library(shiny)
library(ggplot2)
library(rsconnect)
library(tidyverse)

# Load dataframe
df_wmc <- read.csv("df_wmc.csv")
```
<br> <br>
Next, you create the **ui** and a **server** part of your app.
```r
# Define UI for application that plots features of movies
ui <- fluidPage(

  # Sidebar layout with a input and output definitions
  sidebarLayout(

    # Inputs
    sidebarPanel(

      # Select variable for y-axis
      selectInput(inputId = "y",
                  label = "Y-axis:",
                  choices = c("Ospan (correct sequences)" =
                  "ospan_letter_list_correct",
                  "ospan_math_correct_percent",
                  "ospan_math_rt_mean",
                  "digitspan_num_perfect",
                  "digitspan_level",
                  "cj_correct_easy",
                  "cj_correct_hard",
                  "cj_correct_sum"),
                  selected = "ospan_letter_list_correct"),
      # Select variable for x-axis
      selectInput(inputId = "x",
                  label = "X-axis:",
                  choices = c("proportion_repetitions",
                  "total_repetitions",
                  "secs_spent",
                  "mins_spent",
                  "hours_spent",
                  "days_spent",
                  "mc_answer_norep_correct_percentage",
                  "mc_answer_norep_incorrect_percentage",
                  "mc_answer_rep_correct_percentage",
                  "mc_answer_rep_incorrect_percentage"),
                  selected = "total_repetitions")
    ),

    # Outputs
    mainPanel(
      plotOutput(outputId = "scatterplot"),
      textOutput(outputId = "correlation"),
      #textOutput(outputId = "avg_x"),
      #textOutput(outputId = "avg_y"),
      verbatimTextOutput(outputId = "lmoutput")
    )
  )
)

# Define server function required to create the scatterplot
server <- function(input, output) {

  # Create scatterplot object the plot Output function is expecting
  output$scatterplot <- renderPlot({
    ggplot(data = df_wmc, aes_string(x = input$x, y = input$y)) +
      geom_point()+
      stat_smooth(method = "lm")+
      theme(axis.title.y = element_text(size = 20),
            axis.title.x = element_text(size = 20),
            axis.text.y = element_text(size = 18),
            axis.text.x = element_text(size = 18))
  })
```
<br> <br>

You can also add some more specific statistical details in the app if you wish.
In my case, I chose to add the correlation coefficient between x and y
coupled with the means of the respective variable. Lastly, I
added summary statistics of a linear regression between x and y (see the
code below).
```r
  # Create text output stating the correlation between the two ploted
  output$correlation <- renderText({
    r <- round(cor(df_wmc[, input$x], df_wmc[, input$y], use = "pairwise"), 3)
    paste0("Pearson's r = ", r)
  })

  # Calculate average of x
  output$avg_x <- renderText({
    avg_x <- df_wmc %>% pull(input$x) %>% mean(na.rm = T) %>% round(2)
    paste("Average", input$x, "=", avg_x)
  })

  # Calculate average of y
  output$avg_y <- renderText({
    avg_y <- df_wmc %>% pull(input$y) %>% mean(na.rm = T) %>% round(2)
    paste("Average", input$y, "=", avg_y)
  })

  # Create regression output
  output$lmoutput <- renderPrint({
    x <- df_wmc %>% pull(input$x)
    y <- df_wmc %>% pull(input$y)
    summ <- summary(lm(y ~ x, data = df_wmc))
    print(summ, digits = 3, signif.stars = FALSE)
  })

}

# Create a shiny app object
shinyApp(ui = ui, server = server)
head(df)
```
<br> <br>

### Results
Click on this [app](https://dfellman.shinyapps.io/df_test/) to see the final results.

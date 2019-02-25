---
title: "Visualizing cognitive performance across time"
date: "2018-01-08"
tags: [R, data science]
excerpt: "Data visualizations"
---

I will open my blog journey with a brief tutorial on how you can use the "shiny" package in r for visualizing relationships between variables.

Before you start learning shiny I'd recommend you to take the ggplot courses offered in Datacamp [part 1](https://www.datacamp.com/courses/data-visualization-with-ggplot2-1) and [part 2](https://www.datacamp.com/courses/data-visualization-with-ggplot2-2)

## H2 Heading

###H3 Heading

Here's some basic text.

And here's some *italics*

Here's some **bold text**



Here is a link [link](braintrain.fi)

Here's a bulleted list
* First item
+ Second item
- Third item

R code block:
```r
library(shiny)
library(ggplot2)
library(rsconnect)
library(tidyverse)

#load data
df_wmc <- read.csv("df_wmc.csv")
# Define UI for application that plots features of movies
ui <- fluidPage(

  # Sidebar layout with a input and output definitions
  sidebarLayout(

    # Inputs
    sidebarPanel(

      # Select variable for y-axis
      selectInput(inputId = "y",
                  label = "Y-axis:",
                  choices = c("Ospan (correct sequences)" = "ospan_letter_list_correct", "ospan_math_correct_percent", "ospan_math_rt_mean",
                              "digitspan_num_perfect", "digitspan_level", "cj_correct_easy", "cj_correct_hard",
                              "cj_correct_sum"),
                  selected = "ospan_letter_list_correct"),
      # Select variable for x-axis
      selectInput(inputId = "x",
                  label = "X-axis:",
                  choices = c("proportion_repetitions", "total_repetitions", "secs_spent", "mins_spent",
                              "hours_spent", "days_spent", "mc_answer_norep_correct_percentage",
                              "mc_answer_norep_incorrect_percentage", "mc_answer_rep_correct_percentage",
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

# Create a Shiny app object
shinyApp(ui = ui, server = server)
head(df)
```

Here is a link [link](www.braintrain.fi)
<img src="{{ site.url }}{{ site.baseurl }}/images/plot.jpg" alt="Data visualizations">

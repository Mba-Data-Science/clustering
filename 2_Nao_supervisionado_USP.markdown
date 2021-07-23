---
title: "2_Nao_supervisionado_USP"
author: "IVaney Vieira de Sales"
date: "22/06/2021"
output: html_document
runtime: shiny
---



This R Markdown document is made interactive using Shiny. Unlike the more traditional workflow of creating static reports, you can now create documents that allow your readers to change the assumptions underlying your analysis and see the results immediately. 

To learn more, see [Interactive Documents](http://rmarkdown.rstudio.com/authoring_shiny.html).

## Inputs and Outputs

You can embed Shiny inputs and outputs in your document. Outputs are automatically updated whenever inputs change.  This demonstrates how a standard R plot can be made interactive by wrapping it in the Shiny `renderPlot` function. The `selectInput` and `sliderInput` functions create the input widgets used to drive the plot.


```
## Error in inputPanel(selectInput("n_breaks", label = "Number of bins:", : não foi possível encontrar a função "inputPanel"
```

```
## Error in renderPlot({: não foi possível encontrar a função "renderPlot"
```

## Embedded Application

It's also possible to embed an entire Shiny application within an R Markdown document using the `shinyAppDir` function. This example embeds a Shiny application located in another directory:


```
## Error in shinyAppDir(system.file("examples/06_tabsets", package = "shiny"), : não foi possível encontrar a função "shinyAppDir"
```

Note the use of the `height` parameter to determine how much vertical space the embedded application should occupy.

You can also use the `shinyApp` function to define an application inline rather then in an external directory.

In all of R code chunks above the `echo = FALSE` attribute is used. This is to prevent the R code within the chunk from rendering in the document alongside the Shiny components.




---
title: 'SubFASTA'
collection: bioinformatics
link: https://lawleyjw.shinyapps.io/subfasta-app/
excerpt: 'Interactive app to easily extract sequences of interest from a reference FASTA file' 
toc: true
---

**SubFASTA**

An interactive R Shiny App to extract sequences of interest from a reference FASTA file. Developed by Jonathan W. Lawley (thanks to Ido Bar for the idea!)

*License:* [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/)

R code:
```{r}
library(shiny)
library(seqinr)

# UI ----
ui <- fluidPage(
  titlePanel("Extract Sequences of Interest"),
  sidebarLayout(
    sidebarPanel(
      fileInput("idFile", "Upload Sequence ID List"),
      fileInput("fastaFile", "Upload FASTA Reference")
    ),
    mainPanel(
      verbatimTextOutput("message"),
      downloadButton("downloadButton", "Download Subset")
    )
  )
)

# Server ----
server <- function(input, output) {
  
  # Reactive values to store data
  sequenceIds <- reactiveVal(NULL)
  fastaData <- reactiveVal(NULL)
  
  # Store sequence ID list data
  observeEvent(input$idFile, {
    data <- read.table(input$idFile$datapath, header = FALSE)
    sequenceIds(data$V1)
  })
  
  # Store FASTA reference data
  observeEvent(input$fastaFile, {
    data <- read.fasta(input$fastaFile$datapath, as.string = TRUE)
    fastaData(data)
  })
  
  # Generate subset and output message
  output$message <- renderPrint({
    if (!is.null(sequenceIds()) && !is.null(fastaData())) {
      subsetFasta <- fastaData()[names(fastaData()) %in% sequenceIds()]
      write.fasta(sequences = subsetFasta, names = names(subsetFasta),
                  nbchar = 80, file.out = "subset.fasta")
      paste("Subset FASTA file generated with", length(subsetFasta),
            "sequences matching the provided IDs.")
    } else {
      "Upload the sequence ID list and FASTA reference to generate the subset."
    }
  })
  
  # Enable download button when subset is available
  output$downloadButton <- downloadHandler(
    filename = function() {
      "subset.fasta"
    },
    content = function(file) {
      subsetFasta <- fastaData()[names(fastaData()) %in% sequenceIds()]
      write.fasta(sequences = subsetFasta, names = names(subsetFasta),
                  nbchar = 80, file.out = file)
    }
  )
}

# Run the app ----
shinyApp(ui, server)
```

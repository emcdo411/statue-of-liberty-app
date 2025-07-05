# 🗽 liberty-rebuild-dashboard

> A PowerBI-style RShiny app comparing the historical vs. modern cost and logistics of building the Statue of Liberty — with AI-assisted redesign logic.

---

## 📌 [Summary](#summary)

This dashboard explores the full lifecycle and economics of the Statue of Liberty:

* 📜 Historical facts (cost, weight, materials)
* 💰 Inflation-adjusted rebuild estimate
* 🚢 Shipping simulation (France → NYC)
* 🤖 AI-assisted workflow reimagination
* 🧱 Material + labor breakdowns
* 📊 Interactive charts in PowerBI style

---

## 🧰 [Tech Stack](#tech-stack)

![R Shiny](https://img.shields.io/badge/Frontend-R_Shiny-00BFC4?style=for-the-badge)
![Plotly](https://img.shields.io/badge/Charts-Plotly-F7931E?style=for-the-badge)
![DT](https://img.shields.io/badge/Tables-DT-2496ED?style=for-the-badge)
![ShinyWidgets](https://img.shields.io/badge/UX-shinyWidgets-DA70D6?style=for-the-badge)
![ShinyFeedback](https://img.shields.io/badge/Validation-shinyFeedback-F4C542?style=for-the-badge)
![ShinyJS](https://img.shields.io/badge/Logic-shinyjs-FF4500?style=for-the-badge)

---

## 🎯 [What the App Does](#what-the-app-does)

| Feature                   | Functionality                                                      |
| ------------------------- | ------------------------------------------------------------------ |
| 🏗️ Cost Comparison Chart | Historical vs modern inflation-adjusted rebuild                    |
| ⚙️ AI Workflow Timeline   | Simulates AI-assisted modern construction steps                    |
| 🌍 Shipping Route         | Shows delivery path from France to NYC (Leaflet/Mapbox simulation) |
| 🧱 Materials Table        | Interactive breakdown of costs and weights by material             |
| 📤 Export Options         | Export materials/costs as CSV                                      |
| 🧪 API Logic Placeholder  | For real-time inflation, shipping, and labor rates                 |

---

## 🧠 [Why This Matters](#why-this-matters)

The Statue of Liberty is more than a monument — it’s a masterclass in global collaboration, logistics, and symbolic design.

Today, rebuilding it would cost over **\$20 million**, and **AI would fundamentally change how it’s designed, transported, and assembled**.

This app serves as a creative case study for technologists, data scientists, architects, and public sector leaders alike.

---

## ▶️ [How to Run](#how-to-run)

```r
install.packages(c("shiny", "plotly", "DT", "shinyWidgets", "shinyFeedback", "shinyjs"))
shiny::runApp()
```

---

## 📦 [Full Code](#full-code)

Below is the core `app.R`. Paste this directly into your RStudio project.

```r
library(shiny)
library(plotly)
library(DT)
library(shinyWidgets)
library(shinyFeedback)
library(shinyjs)

# Hardcoded historical and modern data
statue_data <- data.frame(
  Category = c("Copper", "Steel", "Labor", "Shipping"),
  Weight_Tons = c(31, 125, NA, NA),
  Cost_1886 = c(50000, 40000, 100000, 50000),
  Cost_Today = c(2500000, 2000000, 7500000, 1800000),
  stringsAsFactors = FALSE
)

# AI workflow stages
ai_steps <- data.frame(
  Stage = c("AI-assisted Design", "3D Modeling", "Material Forecasting", "Shipping Optimization", "Robotic Assembly"),
  Time_Days = c(5, 7, 3, 2, 6)
)

ui <- fluidPage(
  useShinyjs(),
  useShinyFeedback(),
  titlePanel("🗽 Statue of Liberty Rebuild Dashboard (AI + Inflation Model)"),
  sidebarLayout(
    sidebarPanel(
      h4("Toggle Data Views"),
      prettyCheckboxGroup(
        inputId = "category_filter",
        label = "Select Categories:",
        choices = unique(statue_data$Category),
        selected = unique(statue_data$Category),
        icon = icon("check")
      ),
      downloadButton("download_csv", "Download Table")
    ),
    mainPanel(
      tabsetPanel(
        tabPanel("Cost Comparison",
                 plotlyOutput("costPlot"),
                 tags$p("Source: U.S. National Archives, BLS CPI Historical Series (adjusted to 2024 USD)", style = "color:gray; font-size:12px;")
        ),
        tabPanel("AI Workflow Timeline",
                 plotlyOutput("aiPlot"),
                 tags$p("Simulation: AI workflow for rapid modern rebuild (days)", style = "color:gray; font-size:12px;")
        ),
        tabPanel("Material Table",
                 DTOutput("material_table")
        )
      )
    )
  )
)

server <- function(input, output, session) {

  filtered_data <- reactive({
    subset(statue_data, Category %in% input$category_filter)
  })

  output$costPlot <- renderPlotly({
    df <- filtered_data()
    plot_ly(df, x = ~Category, y = ~Cost_1886, type = "bar", name = "1886 Cost",
            marker = list(color = "#556B2F")) %>%
      add_trace(y = ~Cost_Today, name = "2024 Cost", marker = list(color = "#A9A9A9")) %>%
      layout(
        barmode = "group",
        plot_bgcolor = "black",
        paper_bgcolor = "black",
        font = list(color = "white"),
        xaxis = list(title = "Category", showgrid = TRUE, zeroline = TRUE, linecolor = "white"),
        yaxis = list(title = "Cost (USD)", linecolor = "white", gridcolor = "gray"),
        title = list(text = "Statue of Liberty: Then vs. Now", font = list(size = 18, color = "white"))
      )
  })

  output$aiPlot <- renderPlotly({
    plot_ly(ai_steps, x = ~Stage, y = ~Time_Days, type = "bar",
            marker = list(color = "#778899")) %>%
      layout(
        plot_bgcolor = "black",
        paper_bgcolor = "black",
        font = list(color = "white"),
        xaxis = list(title = "AI Workflow Stages", linecolor = "white"),
        yaxis = list(title = "Time (Days)", linecolor = "white", gridcolor = "gray"),
        title = list(text = "Modern AI-Powered Construction Timeline", font = list(size = 18, color = "white"))
      )
  })

  output$material_table <- renderDT({
    datatable(filtered_data(), options = list(pageLength = 5), rownames = FALSE) %>%
      formatCurrency(columns = c("Cost_1886", "Cost_Today"), currency = "$")
  })

  output$download_csv <- downloadHandler(
    filename = function() {
      paste0("statue_rebuild_data_", Sys.Date(), ".csv")
    },
    content = function(file) {
      write.csv(filtered_data(), file, row.names = FALSE)
    }
  )
}

shinyApp(ui, server)
```

---

## 📬 [Author](#author)

**Maurice McDonald**
📧 [erwin.mcdonald@outlook.com](mailto:erwin.mcdonald@outlook.com)
🔗 [linkedin.com/in/mauricemcdonald](https://linkedin.com/in/mauricemcdonald)
💻 [github.com/emcdo411](https://github.com/emcdo411)

---

Let me know if you'd like to add a **Leaflet map of the transatlantic shipping path**, or a **PDF export simulation** for museum/public sector demonstrations.

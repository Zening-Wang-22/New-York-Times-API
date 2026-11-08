# 📰 New York Times Explorer

An R Shiny app for exploring New York Times articles by date. The project combines API requests, pagination, JSON processing, and an interactive interface for browsing article summaries.

The current query retrieves articles matching the selected date and filters for **print section A, page 1**. It does not retrieve every article published that day.

**Built with:** R · Shiny · jsonlite · dplyr · tidyr  
**Focus:** API integration · Data retrieval · Interactive exploration

## Features

- **Search by date:** Select a date and provide your own NYT API key.
- **Browse headlines:** View matching articles as clickable titles.
- **Explore article details:** Open a headline to see its byline, lead paragraph, and an image when available.
- **Read the original article:** Follow the link to the New York Times website.

## Run locally

### 1. Download the repository

Clone the repository or download and extract its ZIP file. Open `API.Rproj` in RStudio.

### 2. Install dependencies

Run the following in the R console:

```r
install.packages(c(
  "shiny", "dplyr", "tidyr", "stringr",
  "purrr", "glue", "jsonlite", "knitr",
  "tidyverse"
))
```

### 3. Configure your API key

Obtain your own API key from the [New York Times Developer Portal](https://developer.nytimes.com/) with access to the Article Search API.

Before running or sharing the app, remove any existing key from the input's default value. An empty password field avoids displaying the entered key on screen:

```r
passwordInput("api", "API Key", value = "")
```

Do not commit API keys to the repository.

### 4. Launch the app

Open `API.R` and click **Run App**. Select a date, enter your API key, and click **Search**.

Click a headline to open its details, then select **Read full article** to visit the original page. Access to the full article depends on the publisher’s access policies.

## How it works

### Request construction

The function accepts four arguments:

```r
get_nyt_articles(year, month, day, api_key)
```

It checks input types and basic month and day ranges, formats the date, and constructs an Article Search API request.

The query uses the same beginning and ending date and applies these filters:

- Document type: article
- Print section: A
- Print page: 1

The current validation does not fully check calendar dates, such as whether a particular month has 31 days.

### Pagination and data processing

An initial request reads the reported number of matches. The function then requests successive pages, assuming 10 articles per page, and combines the results into a data frame.

Selected fields include:

| Field | Content |
|---|---|
| `headline` | Headline information |
| `byline` | Author information |
| `web_url` | Link to the original article |
| `lead_paragraph` | Opening paragraph |
| `source` | Article source |
| `multimedia` | Associated media metadata |

Some fields retain nested structures from the API response.

When no matches are reported, the current function returns a single row of `NA` values, which the interface treats as a no-results response.

### Interactive browsing

Shiny renders the retrieved headlines as clickable links. Selecting a headline opens a detail window with article information and an image when available. The app includes a fallback image for missing multimedia.

## Main files

| File | Purpose |
|---|---|
| `API.R` | Shiny interface and server logic |
| `API.qmd` | Article retrieval function and supporting code |
| `API.Rproj` | RStudio project |
| `README.md` | Project documentation |

## Limitations and next steps

- **Request pacing:** The pagination loop includes a six-second pause between subsequent page requests. This fixed delay does not guarantee compliance with current API limits.
- **Error handling:** The current handler addresses warnings but does not provide comprehensive handling of request errors, timeouts, or rate-limit responses.
- **Response compatibility:** Changes to the API response structure may require updates to article and multimedia processing.
- **Empty results:** Returning a consistent zero-row data frame would simplify downstream handling.
- **Query flexibility:** Future versions could let users choose between the current front-page filter and a broader article search.

Further improvements include validating complete calendar dates, checking images article by article, and adding tests with saved API responses.

# Web Scraper

A configurable Python web scraper for extracting structured information from a collection of web pages using XPath expressions. The scraper is designed to separate the **scraping logic** from the **website-specific selectors**, making it easy to adapt the workflow to different websites without changing the main code.

## Description

This repository provides a simple, configuration-driven approach to web scraping with Python. A list of URLs is supplied as input, each page is downloaded, and selected elements are extracted using XPath expressions.

The fields to extract are defined in `config.json`, so the same scraping workflow can be reused for different websites by changing the input URLs and XPath selectors.

The current example is configured for pages containing GOV.UK-style speech content. It extracts:

- page text
- publication date
- profile link

The results are written to a tab-separated values (TSV) file.

## Use Cases

This scraper is useful for:

- **Web data collection:** Collecting structured information from multiple web pages.
- **Content extraction:** Extracting specific text, metadata, links, or other HTML elements.
- **Reproducible scraping:** Keeping URLs and XPath selectors in configuration files rather than hard-coding them in the scraping workflow.
- **Teaching web scraping:** Demonstrating HTTP requests, HTML parsing, XPath, configuration files, and structured data output.
- **Adapting to new websites:** Reusing the same workflow with different XPath expressions.

## Input Data

The input URLs are stored in:

```text
data/input_urls.json
```

The file contains the URLs of the pages to scrape.

For example:

```json
[
  {
    "link": "https://example.org/page-1"
  },
  {
    "link": "https://example.org/page-2"
  }
]
```

Each URL is requested and parsed independently.

## Configuration

Website-specific extraction rules are stored in:

```text
config.json
```

The current configuration contains:

```json
{
  "input_filepath": "data/input_urls.json",
  "output_filepath": "scraped_data.tsv",
  "required_info": [
    {
      "column_name": "text",
      "xpath": "//div[@class='govspeak']//text()"
    },
    {
      "column_name": "date",
      "xpath": "/html/body/div[2]/main/div[2]/div/div[1]/div/dl/dd[2]//text()",
      "index": 0
    },
    {
      "column_name": "profile_link",
      "xpath": "/html/body/div[2]/main/div[2]/div/div[1]/div/dl/dd[1]/a[2]/@href",
      "index": 0
    }
  ]
}
```

Each entry in `required_info` defines one output column.

### Configuration fields

| Field | Description |
|---|---|
| `column_name` | Name of the output column |
| `xpath` | XPath expression used to locate the desired content |
| `index` | Optional index used when the XPath returns multiple results |
| `input_filepath` | Path to the input URL file |
| `output_filepath` | Path for the scraped output |

This makes it possible to change the selectors without modifying the scraping code.

## Output Data

The scraper writes the extracted information to:

```text
scraped_data.tsv
```

The output contains one row per input URL and one column for each configured field.

For the current example, the output columns are:

- `text`
- `date`
- `profile_link`

## Repository Structure

```text
web_scraper/
├── .binder/
│   └── environment.yml
├── data/
│   └── input_urls.json
├── config.json
├── main.ipynb
├── README.md
└── LICENSE
```

## Environment Setup

### Using Conda

Create the environment:

```bash
conda env create -f .binder/environment.yml
```

Activate it:

```bash
conda activate web-data-collection
```

Alternatively, create a new environment and install the required packages:

```bash
conda create -n web-data-collection python=3.12
conda activate web-data-collection
conda install -c conda-forge jupyterlab pandas requests beautifulsoup4 lxml scrapy
```

### Using pip

Create a virtual environment:

```bash
python -m venv web-data-collection
```

Activate it on Windows:

```bash
web-data-collection\Scripts\activate
```

Activate it on macOS/Linux:

```bash
source web-data-collection/bin/activate
```

Install the required packages:

```bash
pip install jupyterlab pandas requests beautifulsoup4 lxml scrapy
```

## How to Use

Start JupyterLab:

```bash
jupyter lab
```

Open:

```text
main.ipynb
```

Run the notebook cells in order.

The basic workflow is:

1. Load the configuration.
2. Load the input URLs.
3. Request each web page.
4. Parse the HTML.
5. Apply the configured XPath expressions.
6. Store the extracted values.
7. Write the results to a TSV file.

## Adapting the Scraper to Another Website

The main advantage of the scraper is that website-specific XPath expressions can be changed in `config.json`.

For example, to extract a paragraph with a particular class:

```xpath
//p[contains(@class, "article-text")]//text()
```

To extract an element by ID:

```xpath
//p[@id="article-text"]//text()
```

To extract a link:

```xpath
//a[@class="author"]/@href
```

After changing the selectors, the same notebook can be used to scrape the new website.

### XPath Tips

XPath expressions can select elements based on:

**Element and class**

```xpath
//p[contains(@class, "text-body")]
```

**Element and ID**

```xpath
//p[@id="main-text"]
```

**Attribute**

```xpath
//a[@href]
```

**Link target**

```xpath
//a[contains(@href, "/author/")]/@href
```

**All text contained in an element**

```xpath
//div[@class="content"]//text()
```

## Technical Details

The scraper uses Python libraries for HTTP requests and HTML parsing.

### HTTP requests

Pages are retrieved with `requests`:

```python
response = requests.get(url)
```

### HTML parsing

The response is parsed with `lxml`:

```python
from lxml import html

soup = html.fromstring(response.content)
```

### XPath extraction

Information is extracted using XPath:

```python
result = soup.xpath(xpath)
```

This approach allows the extraction rules to remain separate from the scraping workflow.

## Workflow

```text
Input URLs
    │
    ▼
config.json
    │
    ├── XPath selectors
    └── Output configuration
    │
    ▼
HTTP request
    │
    ▼
HTML response
    │
    ▼
lxml HTML parser
    │
    ▼
XPath extraction
    │
    ▼
Structured records
    │
    ▼
scraped_data.tsv
```

## Binder

The repository contains a `.binder` configuration so that the notebook can be run in a reproducible cloud environment.

Launch the repository with Binder:

```text
https://mybinder.org/v2/gh/BDA-KTS/web_scraper/main
```

Binder creates the environment specified in:

```text
.binder/environment.yml
```

No local Python installation is required when using Binder.

## Reproducibility

The repository keeps the main components of the scraping workflow separate:

- `data/input_urls.json` — what to scrape
- `config.json` — what to extract
- `main.ipynb` — how to perform the extraction
- `.binder/environment.yml` — software environment

This separation makes the workflow easier to reuse, teach, and adapt.

## Ethical and Responsible Web Scraping

Before scraping a website:

- Check the website's terms of service.
- Check and respect `robots.txt` where applicable.
- Avoid sending excessive requests.
- Use reasonable delays for larger collections.
- Respect access restrictions and authentication requirements.
- Consider copyright, privacy, and data-protection requirements.
- Store only the data that is necessary for your research or teaching purpose.

## Limitations

This is a lightweight educational scraping workflow. It does not currently provide all the features of a dedicated crawling framework such as Scrapy.

For large-scale collection, additional functionality may be needed, including:

- request throttling
- retries
- caching
- pagination
- concurrent requests
- error logging
- duplicate detection
- proxy management
- JavaScript-rendered page handling

For larger crawling projects, a framework such as Scrapy may be more appropriate.

## License

This project is released under the Apache License 2.0. See `LICENSE` for details.

## Contact

For questions, suggestions, or issues, please use the GitHub repository's issue tracker.

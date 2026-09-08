# Web Scraper

## Description

A configurable Python web scraper for extracting structured information from a collection of web pages using XPath expressions. The scraper is designed to separate the **scraping logic** from the **website-specific selectors**, making it easy to adapt the workflow to different websites without changing the main code.

It provides a simple, configuration-driven approach to web scraping from a list of domains and their URLs where selected elements are extracted using XPath expressions.

The fields to extract are defined in `config.json`, so the same scraping workflow can be reused for different websites by changing the input URLs and XPath selectors.

## Use Cases

The current example is configured for pages containing GOV.UK-style speech content, extracting page main text, publication date, and profile link. It can generally be used for;

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
{
    "https://example.com": ["/"],
    "https://www.gov.uk": ["government/speeches/chancellor-john-healeys-growth-speech-2026", "government/speeches/autumn-budget-2024-speech", "government/speeches/spring-budget-2024-speech", "government/speeches/spring-budget-2023-speech", "government/speeches/the-autumn-statement-2022-speech", "government/speeches/budget-speech-2021", "government/speeches/budget-speech-2020", "government/speeches/spring-statement-2019-philip-hammonds-speech"]
}
```

Each URL is requested and parsed independently.

## Configuration

Website-specific extraction rules are stored in:

```text
config.json
```

It contains path to input file, path to output file, and the `required_info` as each column in the output file specified by column_name, xpath filter, and index location.


## Output Data

The scraper writes the extracted information to:

```text
scraped_data.tsv
```

The output contains one row per input URL and one column for each configured field.

| domain                                     | url                                                            | text                                                                                                     | date             | profile_link                     |
| ------------------------------------------ | -------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- | ---------------- | -------------------------------- |
| [https://example.com](https://example.com) | /                                                              | []                                                                                                       | []               | []                               |
| [https://www.gov.uk](https://www.gov.uk)   | government/speeches/chancellor-john-healeys-growth-speech-2026 | Thank you for your welcome. Thank you to those of your staff here who have helped organise this event... | 7 September 2026 | /government/people/john-healey   |
| [https://www.gov.uk](https://www.gov.uk)   | government/speeches/autumn-budget-2024-speech                  | Madam Deputy Speaker… This government was given a mandate. To restore stability to our economy...        | 30 October 2024  | /government/people/rachel-reeves |


## Environment Setup

Create and activate the environment:

```bash
conda env create -f .binder/environment.yml
conda activate web-data-collection
```

Alternatively, create a new environment and install the required packages:

```bash
conda create -n web-data-collection python=3.12
conda activate web-data-collection
conda install -c conda-forge jupyterlab pandas requests beautifulsoup4 lxml scrapy
```

Using pip on Windows:

```bash
python -m venv web-data-collection
web-data-collection\Scripts\activate
pip install jupyterlab pandas requests beautifulsoup4 lxml scrapy
```

Using pip on macOS/Linux:

```bash
python -m venv web-data-collection
source web-data-collection/bin/activate
pip install jupyterlab pandas requests beautifulsoup4 lxml scrapy
```


## How to Use

```bash
jupyter lab
```
and execute the cells of 

```text
main.ipynb
```

## Technical Details

The scraper uses Python libraries for HTTP requests and HTML parsing. Pages are retrieved with `requests` and the responses are parsed with `lxml`. The required information is extracted using XPath with soup while the output is written to a TSV file.

## Limitations

This is a lightweight educational scraping workflow. It does not currently provide all the features of a dedicated crawling framework such as Scrapy.

## Contact

For questions, suggestions, or issues, please contact <taimoor.khan@gesis.org>.

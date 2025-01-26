# Web Scraping Job Postings for Data Scientist on Naukri.com

This README outlines the tools, steps, and assumptions used to scrape job postings for the role of a Data Scientist on Naukri.com.

---

## Tools Used

1. **Programming Language**: Python
   - Widely used for web scraping due to its simplicity and extensive libraries.

2. **Libraries**:
   - `requests`: To send HTTP requests and fetch the HTML content of web pages.
   - `BeautifulSoup`: For parsing and extracting specific data from HTML.
   - `pandas`: To organize and store scraped data in tabular form for analysis.

3. **Browser Developer Tools**:
   - Used to inspect the webpage’s structure and identify the HTML elements containing the required data.

4. **Proxy and Headers**:
   - Custom headers (e.g., `User-Agent`) to simulate browser requests.

---

## Steps to Scrape Data

### 1. Understanding Website Structure
- Visit Naukri.com and search for "Data Scientist" job postings.
- Use browser developer tools (Inspect Element) to identify:
  - Job titles
  - Company names
  - Locations
  - Experience required
  - Job descriptions
  - Date posted
  - Application links

### 2. Setting Up the Environment
- Install the required Python libraries:
  ```bash
  pip install requests beautifulsoup4 pandas
  ```

### 3. Python Code for Scraping

Below is the Python script used for scraping job postings:

```python
import requests
from bs4 import BeautifulSoup
import pandas as pd

# URL for Naukri.com search results (modify the query as needed)
url = "https://www.naukri.com/data-scientist-jobs?k=data%20scientist&experience=0&job_posted=last_7_days"

# Set headers to mimic a browser request
headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36"
}

# Send a GET request to the URL
response = requests.get(url, headers=headers)

# Check if the request was successful
if response.status_code == 200:
    soup = BeautifulSoup(response.content, 'html.parser')

    # Extract job postings data
    jobs = []
    job_cards = soup.find_all('article', class_='jobTuple bgWhite br4 mb-8')

    for card in job_cards[:20]:  # Limit to 20 job postings
        try:
            title = card.find('a', class_='title').text.strip()
            company = card.find('a', class_='subTitle').text.strip()
            location = card.find('li', class_='fleft grey-text br2 placeHolderLi location').text.strip()
            date_posted = card.find('div', class_='type br2 fleft grey').text.strip()
            description = card.find('div', class_='ellipsis job-description').text.strip()
            application_link = card.find('a', class_='title')['href']

            jobs.append({
                "Job Title": title,
                "Company Name": company,
                "Location": location,
                "Date Posted": date_posted,
                "Job Description": description,
                "Application Link": application_link
            })
        except Exception as e:
            print(f"Error extracting a job card: {e}")

    # Save the extracted data to an Excel file
    output_file = "AditiKolhe_Naukri_Output.xlsx"
    df = pd.DataFrame(jobs)
    df.to_excel(output_file, index=False)

    print(f"Scraping complete. Data saved to {output_file}")
else:
    print("Failed to retrieve job postings. Please check the URL or your internet connection.")
```

### 4. Saving the Data
- The script saves the scraped data to an Excel file named `AditiKolhe_Naukri_Output.xlsx`.

---

## Assumptions

1. **Static HTML Structure**:
   - The script assumes that the structure of the webpage remains consistent. Changes to the HTML structure may require adjustments to the scraping logic.

2. **Pagination**:
   - Only the first page of job postings is scraped. Additional logic is required for scraping multiple pages.

3. **Data Availability**:
   - It is assumed that all required data (e.g., job title, company name, etc.) is available on the webpage.

4. **Legal Compliance**:
   - Scraping respects the website’s terms of service. Consult Naukri.com’s scraping policies and proceed accordingly.

---

## Improvements and Next Steps

- **Pagination Handling**:
  Implement a loop to scrape data from multiple pages of search results.
- **Error Handling**:
  Add error handling mechanisms to manage issues like failed requests or missing data.
- **Dynamic Content Handling**:
  Use tools like `selenium` if job postings are dynamically loaded.
- **Automation**:
  Schedule periodic scraping using tools like `cron` or Python’s `schedule` library.

---

This README serves as a guide to understanding and implementing a web scraper for Naukri.com job postings. For any queries or contributions, please feel free to reach out!


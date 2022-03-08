## 10-K Filings 

This project mainly focuses on company strategies for global disaster. The responsive strategies and impacts must be collected from sources that guarantee information credibility. Since it is very challenging to collect each company's strategy with regards to any risk factor, the reliable source to crawl meaningful data are 10K and 10Q reports from the SEC's EDGAR database (https://www.sec.gov/edgar/searchedgar/companysearch.html). In these reports, companies disclose the acknowledgement of risk factors and their actions. The section item 1A (risk factor) and item 7 of 10K report explicitly addresses the impacts and responses of each public company. For example, ‘Reading International, Inc’, a service company focusing on development, ownership and operation of entertainment and real property assets in US, Australia, and New Zealand, conducted termination of US cinemas and live theatre level staff, suspension of non-essential operating expenditure, improved cash management process. 

We downloaded the 3147 companies’ 10-K filings from SEC EDGAR Database with Python. These companies are from the manufacturing and service industries, and are distributed in 268 subdomains. The downloading process:

- Get the CIK and SIC Number of the Target Companies. 
The Central Index Key (CIK) is used on the SEC's computer systems to identify corporations and The Standard Industrial Classification (SIC)  is a four a four-digit code that provides a framework for classifying industries. For example, the Manufacturing industry has SIC codes from 2000 to 3999 and the Service industry SIC codes from 7000 to 8999. 
Since we need all companies’ 10K fillings in the Service and Manufacturing industry for 2020, we get the CIK and SIC number of our target companies from the Financial Statement Data Sets ( https://www.sec.gov/dera/data/financial-statement-data-sets.html). 

- Scrap 10K Filings from SEC
After filtered target CIK and SIC number, We used this Python package to download 10K filings: Sec-edgar-downloader ( https://pypi.org/project/sec-edgar-downloader/)

- Find Company Domains
The SEC website provides data on companies that includes company name, adsh number, CIK number, and SIC number.
https://www.sec.gov/dera/data/financial-statement-data-sets.html
 We use the adsh number of our local 10-K file to match the Excel file, and get the adsh number, CIK number and SIC number of more than 3000 companies. Then use the SIC  number to match the company's subdomain through the website.
https://www.sec.gov/corpfin/division-of-corporation-finance-standard-industrial-classification-sic-code-list

After that, we downloaded 3147 companies 10K from 2019 to 2021 and classified companies into 268 domains. Below is the dataset we organized for company information. Take the first four companies as an example, which include company name, 10K filing path, adsh number, CIK number, SIC number, and subdomain.

## Financial Dataset

In this project, we would like to use financial index to assess company’s measures to COVID-19 and to do regression analysis on each measure and sub-domains. 

Using the CIK code of each company, we downloaded the financial indicators from Wharton Research Data Services (WRDS) website. WRDS Industry Financial Ratio is a collection of most commonly used financial ratios by academic researchers. There are in total over 70 financial ratios grouped into the following seven categories: Capitalization, Efficiency, Financial Soundness/Solvency, Liquidity, Profitability, Valuation and Others. Ratios for each individual company as well as at industry aggregated level are included in the output.
(https://wrds-www.wharton.upenn.edu/login/?next=/pages/get-data/compustat-capital-iq-standard-poors/compustat/north-america-daily/fundamentals-annual/%3Fno_login_redirect%3DTrue)

Our main focus is on the financial data from 2019 to 2020, and we chose 11 financial ratios from four categories to do the regression analysis. The table below shows the description of these 11 financial ratios from 4 aspects: Profitability, Operating efficiency, Liquidity ratio and Solvency ratio.

### Profitability
Profitability is a situation in which an entity is generating a profit. Profitability arises when the aggregate amount of revenue is greater than the aggregate amount of expenses in a reporting period. We chose 4 ratios to assess companies' profitability.

### Operating Efficiency
A company's operating efficiency is key to its financial success. This metric considers a company's basic operational profit margin after deducting the variable costs of producing and marketing the company's products or services. We chose two ratios for evaluation of the operating efficiency.

### Liquidity Ratio
Liquidity is a key factor in assessing a company's basic financial health. It measures a company's ability to pay debt obligations and its margin of safety through the calculation of metrics. 

### Solvency Ratio
A company's ability to meet its debt obligations on an ongoing basis, not just over the short term. Solvency ratios calculate a company's long-term debt in relation to its assets or equity. A lower D/E ratio means more of a company's operations are being financed by shareholders rather than by creditors. We chose two indicators for this aspect evaluation:

# Data Preprocessing

## Extract COVID-related paragraphs from 10K
In order to reduce the interference noise of irrelevant text, we adopted a method in the first step to directly crawl the passages related to COVID from all 10-Ks. Due to the different html structures of different 10-Ks, it is difficult to use a unified find or find_all for scraping, which poses a challenge for us to scrape meaningful text in the early stage. We have also tried to use regular expressions to scrape paragraphs containing COVID, but this will miss some data. I also faced the problem of missing text when paging. 

Therefore, the methods we have successfully implemented are divided into:
- Use Xpath and Bs to crawl down the content body respectively;
- Separate paragraphs;
- Integrate the same paragraphs separated by two pages;
- Create a Keyword_List related to COVID, including：'COVID', 'covid', 'Covid', 'coronavirus', 'epidemic', 'pandemic', 'Epidemic', 'Pandemic', 'Coronavirus';
- Retrieve paragraphs from all paragraphs, which contain at least one of the Keyword_List;
- Excel saves the output.

## Identify Key Phrases related to COVID Strategies and Impacts 

The strategy we planned to use to identify the keywords related to COVID-19, impact, or decisions made by firms was to cluster them first and then select the key phrases within each cluster.  Nonetheless, Kmean and LDA clustering methods are not accurate. So we used Deep learning methods to refine the clustering by word vector similarity. 

The steps are: 
- Train the word vector using Gensim. This is a process to assign sentences to topics based on meaning rather than word-for-word matching;
- Extract noun trunks or keyphrases (up to 3 words) from our selected 10K paragraphs.
- Identify 22 seed terms, get the word vector for each word in phrases and use the average of the word vectors as the term vector. 
- Using BERT to extract more keyphrases. 

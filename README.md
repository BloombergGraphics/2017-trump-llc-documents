Dataset of Trump LLC Documents
===

This repository includes data from Secretary of State offices on companies disclosed by President Donald Trump through his [2015](https://www.documentcloud.org/documents/3870993-Trump-Donald-J-2015-Financial-Disclosure-Report.html), [2016](https://www.documentcloud.org/documents/3870994-Trump-Donald-J-2016-Financial-Disclosure-Report.html) and [2017](https://www.documentcloud.org/documents/3870996-Trump-Donald-J-2017-Financial-Disclosure-Report.html) U.S. Office of Government Ethics Form 278e filings.

This data was referenced in an Oct. 18, 2017 story [Trump’s Business Empire Sends Court Into Uncharted Legal Terrain](https://www.bloomberg.com/news/articles/2017-10-18/trump-s-business-empire-sends-court-into-uncharted-legal-terrain) and in an Aug. 10, 2017 story [Trump’s Web of Companies May Have a Way to Avoid the Obamacare Tax](https://www.bloomberg.com/news/articles/2017-08-10/trump-s-web-of-companies-may-have-a-way-to-avoid-the-obamacare-tax).

Using a mix of optical character recognition and manual entry, company information was extracted from these filings and compiled into a series of spreadsheets, linking the names of organizations where possible.

## Original form structure

These filings have three main parts that provided an initial list of company names:

* Part 1 includes "Filer's Positions Held Outside United States Government."
* Part 2 includes "Filer's Employment Assets and Income" showing "disclosed entities with assets over $1,000 or which produced income of over $200."
* Part 2, Exhibit A "discloses the ownership structure of the entities on Part 2, as well as additional entities that are not disclosed on Part 2 because they either (1) have no independent value or income and are part of the entity structures listed in Part 2; (2) have no independent value or income and provide back office support functions to other entities; or (3) are dormant. This Schedule is being provided to ensure a complete picture of the assets and holdings of the filer."

In some cases, the data link to original incorporation documents provided by the New York Department of State.

## Files

The [data](data/) folder includes six files:

1. [1_trump-oge278e-part-one-companies.csv](data/1_trump-oge278e-part-one-companies.csv) — A list of unique company names included in Part 1 in at least one of the 2015, 2016 or 2017 filings.
2. [2_trump-oge278e-part-two-companies.csv](data/2_trump-oge278e-part-two-companies.csv) — A list of unique company names included in Part 2 in at least one of the 2015, 2016 or 2017 filings and that **did not** appear in Part 1.
3. [3_trump-oge278e-exhibit-a-companies.csv](data/3_trump-oge278e-exhibit-a-companies.csv) — A list of unique company names included in Exhibit A in at least one of the 2015, 2016 or 2017 filings and that did not appear in Part 1 or Part 2.
4. [4_sec-of-state-records.csv](data/4_sec-of-state-records.csv) — A list of companies from the above lists with information from Secretary of State documents we collected.
5. [5_trump-oge278e-exhibit-a-entities.csv](data/5_trump-oge278e-exhibit-a-entities.csv) — Unique companies (entities) taken from the Exhibit A portion of the 2016 and 2017 OGE 287e filings. The two files located in `raw/` were used to generate this file, see the [Jupyter Notebook](parse-exhibit-a.ipynb) for details.
6. [6_trump-oge278e-exhibit-a-entity-relations.csv](data/6_trump-oge278e-exhibit-a-entity-relations.csv) — Ownership relationships between each of the companies (entities) listed in the Exhibit A portion of the 2016 and 2017 OGE 287e filings. The two files located in `raw/` were used to generate this file, see the [Jupyter Notebook](parse-exhibit-a.ipynb) for details.

Note: Files 5 and 6 include entities listed in the filings that may not be companies owned by Trump or are individuals that share ownership of a company.

The [raw](raw/) folder includes two files:

1. [trump-oge278e-exhibit-a-2016-raw.csv](data/trump-oge278e-exhibit-a-2016-raw.csv) — Exhibit A from Trump's 2016 OGE 287e filings presented in a raw semi-structured csv format. This file adheres to the table structure found in the Exhibit A portion of the 2016 OGE 287e filing.
2. [trump-oge278e-exhibit-a-2017-raw.csv](data/trump-oge278e-exhibit-a-2017-raw.csv) — Exhibit A from Trump's 2017 OGE 287e filings presented in a raw semi-structured csv format. This file adheres to the table structure found in the Exhibit A portion of the 2017 OGE 287e filing.

## Schemas

The schema of data files 1 through 3 is as follows:

* `org_name` — The name as it appears on the OGE Form 278e.
* `org_slug` - A slugified version of the `org_name`. The Google Doc function that was used to create this slug is the following:

```sh
=REGEXREPLACE(REGEXREPLACE(REGEXREPLACE(REGEXREPLACE(LOWER(SUBSTITUTE( B3, CHAR(34), "#" )), "( |,|\.|:|;)", "-"), "(#|&|\*|/|\\|'|’|\(|\)|\?|\!)", ""), "-+", "-"), "-$", "")
```
* `other_org_names` — Other names this company may appear as within the universe of these documents. This covers cases where the company is listed as `Formerly known as`, `Also known as` or under an `Assumed Names` section of state records.
* `source` — A list of semi-colon delimited OGE Form 278e sections this company appeared in. Companies that appear in an Exhibit A filing for a given year also appeared in all previous year Exhibit A filings. As a result, only the most recent year is listed. For example, a filing that lists `2016 Exhibit A` also appeared in `2015 Exhibit A`.

The schema of data file 4, Secretary of State records, is as follows:

* `org_slug` — Same as above
* `incorp_jurisdiction` — The jurisdiction that this record comes from. It is oftena  U.S. state but is sometimes a country like `UK`.
* `org_id` — The number assigned to this company by the issuing body.
* `digital_doc` — A link to a PDF copy of that company's **online** record. This information is current as of the date printed on the page.
* `digital_doc2` — A link to a PDF copy of any additional **online** record. This information is current as of the date printed on the page. This document usually exists if there was a recent change in officers.
* `original_doc` — A link to a scanned PDF of **primary source** documents, when available. These are usually the original incorporation documents. Source information is listed in the document metadata.
* `original_doc_2` — A link to any additional primary source documents we received.
* `incorp_year` — Year of original incorporation in the issuing jurisdiction.
* `officers` — Names recorded from the most current record we found. Multiple names are semi-colon delimited.
* `other_or_past_members` — Names found on primary source documents or former versions of digital documents. Multiple names are semi-colon delimited.
* `address_street` — Number and street name for the registered address. This is often a registration company.
* `address_city` — City for the above address
* `address_state` — State for the above address
* `address_zip` — Zip for the above address
* `notes` — Any additional notes we recorded

The schema of data file 5 is as follows:

* `year` — The year of the filing that his record appears in.
* `org_name` — The name of the organization or individual listed. For details see the standardization section of the [Jupyter Notebook](parse-exhibit-a.ipynb).
* `org_slug` — A slugified version of the org name. For details see the standardization section of the [Jupyter Notebook](parse-exhibit-a.ipynb). The slugified version is generated using the following Python function, which matches the Excel function above:

```python
def slugify(s):
    s = str(s)
    s = re.sub(r'( |,|\.|:|;)', '-', s)
    s = re.sub(r"(#|&|\*|\/|\\|'|’|\(|\)|\?|\!)", '', s)
    s = re.sub(r'-+', '-', s)
    s = re.sub(r'-$', '', s)
    s = s.lower()
    return s
```

The schema of file 6 is as follows:

* `entityASlug` – Slug of corresponding company listed in `entityA`. Otherwise same as `org_slug` above in Part 5.
* `entityA` – Company name as it appears in Exhibit A on the OGE Form 278e. Otherwise same as `org_name` above in Part 5.
* `ownership` – Referring to Percent (%) ownership `entityB` owns of corresponding `entityA`.
* `entityB` – Owner of corresponding company listed in `entityA`. Otherwise same as `org_name` above in Part 5.
* `entityBSlug` – Slug of corresponding company listed in `entityB`. Otherwise same as `org_slug` above in Part 5.
* `role` – Role as it appears in Exhibit A.
* `year` – Same as above in Part 5.

## Caveats

Although we have taken steps to be as thorough as possible, because much of this data was compiled by hand, it might contain errors. Check over any of the data you might be using for accuracy before publication. If you find an error, please open an issue on this repo to let us know.

## Contributors

The following people contributed to this project:

* [Michael Keller](https://twitter.com/mhkeller)
* [Blacki Migliozzi](https://twitter.com/blackili)
* [Andrew Martin](https://www.bloomberg.com/authors/ARIDxH4xtUw/andrew-martin)
* [Molly Smith](https://twitter.com/mollysmithnews)
* [Jordyn Holman](https://twitter.com/JordynJournals)

## Attribution

The scanned historic documents from New York should be attributed as `New York Secretary of State documents obtained by Bloomberg News`.

The structured data we compiled should be cited using the appropriate description preceded by `Bloomberg News analysis of`. For example, `Bloomberg News analysis of Delaware, New York and Nevada Secretary of State records`.

If the data is used in an online story, the source line should include a link to this repository.

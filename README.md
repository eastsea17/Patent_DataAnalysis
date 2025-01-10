# Patent Data Analysis Pipeline with R

## Project Overview

This repository contains R scripts for processing and analyzing patent data, specifically focusing on US patents and their citation networks. The pipeline includes several stages: importing and cleaning data, extracting reference information, and crawling the USPTO website to gather additional details. The project is designed to automate the extraction of meaningful insights from raw patent data, particularly regarding application dates and citation patterns.

## Pipeline Components

The repository consists of three primary R scripts:

1.  **M1: Import patents data:**
    *   Imports raw patent data from a CSV file.
    *   Filters for US patents.
    *   Spreads patent reference data and organizes it into a tidy format.
    *   Outputs an intermediary table containing patent IDs, original numbers, and reference numbers in a CSV file.

2.  **M2: USPTO Patents application year Crawler:**
    *   Utilizes the reference numbers from the output of `M1`.
    *   Crawls the USPTO website to extract the application year for each cited patent.
    *   Stores the results in a CSV file, aligning application years with the corresponding reference and original patent numbers.

3.  **M3: USPTO Patents References Crawler:**
    *   Takes a list of patent numbers as input.
    *   Crawls the USPTO website to gather reference data and extracts key information including the referenced patent numbers, publication years, and inventors.
    *   Calculates and saves the average application year of the patents cited by each of the original patent number.
    *   Outputs the results to individual CSV files, preserving all the detailed citation relationships as well as summarized information.

## Data Flow

The data flow between scripts is as follows:

1.  **Input:** `Rawdata_total_CSV.csv` (located in the `Input/` directory)
2.  **M1 Output:** `Output_table(id_num_references)_CSV.csv` (located in the `Output/` directory)
3.  **M2 Input:** `Output_table(id_num_references)_CSV.csv` from `M1` is used for crawling the USPTO website and get application year information.
4. **M3 Input:** `PN.txt` (located in the `Input/` directory), which is list of patent numbers.
5.  **M2 Output:** `Results_total.csv` (located in the `Output/` directory), containing all application years of each reference in the original patent.
6.  **M3 Output:** Individual CSV files (`Output_[i]_[patent_number].csv` and `Results_[i]_[patent_number].csv`) and total `Results_total.csv` containing all citation information and the average application year of references, located in the `Output/` directory.

## Prerequisites

*   R (version 4.0 or higher)
*   Required R packages:
    *   `tidyr`
    *   `XML`
    *   `stringr`
    *   `reshape`
    *   `RCurl`
    *   `stringi`
    *   `dplyr`
    *   `lubridate`
    *   `qdapRegex`

## Installation and Usage

1.  Clone this repository.
    ```bash
    git clone [repository_url]
    ```
2.  Ensure the required R packages are installed.
    ```R
    install.packages(c("tidyr", "XML", "stringr", "reshape", "RCurl", "stringi", "dplyr", "lubridate", "qdapRegex"))
    ```
3.  Place your raw patent data as `Rawdata_total_CSV.csv` in the `Input/` directory. 
4. Put a list of patent numbers as `PN.txt` in the `Input/` directory.
5.  Run the R scripts sequentially:
    ```R
    source("M1.R")
    source("M2.R")
    source("M3.R")
    ```
6.  The output will be stored in the `Output/` directory.

## Code Structure

*   **Input:** Folder containing `Rawdata_total_CSV.csv` and `PN.txt`.
*   **Output:** Folder to store the results of the analysis.
*   **M1.R:** Script for importing and processing the raw patent data.
*   **M2.R:** Script for crawling the USPTO website and extracting the application year for patents.
*   **M3.R:** Script for crawling USPTO website and extracting the cited patent data and calculating average application year.

## Notes

*   Ensure that the input CSV file `Rawdata_total_CSV.csv` has appropriate structure with necessary columns.
*   Web crawling can be subject to website changes and robots.txt rules, so scripts may need updates to work correctly.
*   The code can be further optimized for efficiency, especially for very large datasets.

## Potential Enhancements

*   Expand the crawler to extract more information such as inventors, patent classifications, or legal status.
*   Implement error handling to deal with website timeouts or data inconsistencies.
*   Create interactive dashboards to visualize results.
*   Integrate with a database for scalable data processing and storage.

## License

This project is licensed under the [Specify License].

## Contact

For any inquiries or suggestions, please feel free to reach out.

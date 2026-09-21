# Dataset

This folder is where the raw dataset should be placed before running the notebook. The raw data file
itself is **not included** in this repository (check redistribution permissions before adding large
datasets to a public repo).

## Source

**Online Retail Dataset** — originally published on the UCI Machine Learning Repository, and widely
mirrored on Kaggle. Search "Online Retail Dataset UCI" or "Online Retail Dataset Kaggle" to find a current
download link, and add the exact URL you used here once you've downloaded it.

## Expected file

Place the downloaded file in this folder as either:
- `Online_Retail.xlsx`, or
- `Online_Retail.csv`

The notebook automatically detects and loads whichever one is present.

## Expected columns

`InvoiceNo`, `StockCode`, `Description`, `Quantity`, `InvoiceDate`, `UnitPrice`, `CustomerID`, `Country`

(Column-name variants such as `Invoice`, `Price`, or `Customer ID` are also handled automatically by the
notebook's loading step.)

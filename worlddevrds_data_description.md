# World Bank development indicators data

This document describes the contents of the RDS file [worlddevrds](worlddevrds), which was inspected directly with R using `/usr/bin/Rscript`.

## 1. File and object type

The file is an R serialized object (`.rds`) containing a single `data.frame`.

- Class: `data.frame`
- Type: `list`
- Mode: `list`
- Rows: 396,970
- Columns: 70

This is a wide-format table where each row represents a country/region paired with a specific indicator, and each year from 1960 to 2025 is stored as its own column.

## 2. Main structure

The variable names are:

1. `Country Name`
2. `Country Code`
3. `Indicator Name`
4. `Indicator Code`
5. `1960` through `2025` (66 yearly columns)

So the table has 4 descriptive identifier columns and 66 numeric data columns.

## 3. Row semantics

Each row is effectively:

- a country or region,
- a World Bank indicator,
- measured across multiple years.

A row is not a single country-year observation. Instead, it is a country-indicator record, with the annual values spread across the year columns.

For example, one row could be:

- `Country Name`: "Afghanistan"
- `Country Code`: "AFG"
- `Indicator Name`: "GDP per capita (current US$)"
- `Indicator Code`: "NY.GDP.PCAP.CD"
- `1960`: value
- `1961`: value
- ...
- `2025`: value

The structure is therefore similar to a wide panel table where the panel dimension is implicit in the repeated row layout.

## 4. Coverage and scope

From the inspected object:

- Unique countries/regions: 265
- Unique country codes: 265
- Unique indicators: 1,498
- Unique indicator codes: 1,498

This indicates that the dataset is broad in both dimensions: many countries and many development indicators.

The rows are almost exactly consistent with the product of country count and indicator count:

- 265 countries × 1,498 indicators = 396,? roughly 396,? (in practice the row count is 396,970)

This suggests the file is a country-by-indicator matrix of development statistics.

## 5. Example fields

The first few indicator names in the dataset include:

- Access to clean fuels and technologies for cooking (% of population)
- Access to clean fuels and technologies for cooking, rural (% of rural population)
- Access to clean fuels and technologies for cooking, urban (% of urban population)
- Access to electricity (% of population)
- Access to electricity, rural (% of rural population)
- Access to electricity, urban (% of urban population)

The data spans many development domains, such as health, energy, access, finance, education, and economic indicators.

## 6. Missingness

The yearly columns are numeric and contain many missing values (`NA`). This is common for World Bank indicator datasets, where earlier or less-commonly collected measures are missing for many countries or years.

Examples of missingness by year from the inspection:

- 1960: 90.62% missing
- 1970: 81.83% missing
- 1980: 76.29% missing
- 1990: 67.51% missing
- 2000: 51.21% missing
- 2010: 43.71% missing
- 2020: 45.33% missing
- 2025: 79.43% missing

This pattern shows that data coverage improves in later years, but still is incomplete for many indicators and countries.

## 7. Data type summary

The year columns are numeric (`num` in R), and the metadata columns are character strings:

- `Country Name`: character
- `Country Code`: character
- `Indicator Name`: character
- `Indicator Code`: character
- year columns: numeric with possible `NA`

## 8. Practical read-in pattern

The file can be loaded with:

```r
x <- readRDS("worlddevrds")
str(x)
summary(x[, c("Country Name", "Indicator Name", "1960", "2000", "2020")])
```

Because it is a wide table, reshaping to long form is often useful for time-series analysis:

```r
library(tidyr)
long_df <- x |> pivot_longer(cols = as.character(1960:2025),
                             names_to = "year",
                             values_to = "value")
```

## 9. Interpretation

This RDS object is a compact but large World Bank development database snapshot. It is not a tidy long-format table by default, but it is highly useful for:

- cross-country comparisons,
- indicator-by-indicator analysis,
- historical trend inspection,
- creating long-form data for modeling and visualization.

The structure is classic for public macro-development data: one row per country-indicator combination with a wide year field set.

## 10. Bottom line

The dataset is a wide, country-by-indicator World Bank data frame with:

- 265 countries/regions,
- 1,498 indicators,
- 66 year columns between 1960 and 2025,
- 396,970 rows,
- many missing values for earlier years,
- and a metadata structure designed for tabular reporting and time-series extraction.

# googlesheets Basic Usage
Jenny Bryan, Joanna Zhao  
`r Sys.Date()`  








```r
library(googlesheets)
suppressMessages(library(dplyr))
```

### See some spreadsheets you can access

The `gs_ls()` function returns the sheets you would see in your Google Sheets home screen: <https://docs.google.com/spreadsheets/>. This should include sheets that you own and may also show sheets owned by others but that you are permitted to access, if you visited the sheet in the browser. Expect a prompt to authenticate yourself in the browser at this point (more below re: authentication).


```r
(my_sheets <- gs_ls())
#> Source: local data frame [42 x 10]
#> 
#>                 sheet_title        author perm version             updated
#> 1  Copy of Twitter Archive…   joannazhaoo    r     new 2015-07-20 22:13:25
#> 2               gas_mileage      woo.kara    r     new 2015-07-20 01:08:07
#> 3               TAGS v6.0ns     m.hawksey    r     new 2015-07-14 08:59:57
#> 4  test-gs-jenny-121c66d79…      gspreadr   rw     new 2015-07-06 17:08:26
#> 5  Tweet Collector (TAGS v…      gspreadr   rw     new 2015-07-01 13:43:14
#> 6           #TalkPay Tweets      iskaldur    r     new 2015-07-01 08:06:22
#> 7                  for_sean      gspreadr   rw     new 2015-06-23 18:33:10
#> 8   EasyTweetSheet - Shared     m.hawksey    r     new 2015-06-18 16:07:23
#> 9  Supervisor Interests (R… silwood.mast…    r     new 2015-06-08 08:59:51
#> 10         Projects_2013_14    david.orme    r     new 2015-06-08 08:59:44
#> ..                      ...           ...  ...     ...                 ...
#> Variables not shown: sheet_key (chr), ws_feed (chr), alternate (chr), self
#>   (chr), alt_key (chr)
# (expect a prompt to authenticate with Google interactively HERE)
my_sheets %>% glimpse()
#> Observations: 42
#> Variables:
#> $ sheet_title (chr) "Copy of Twitter Archiver v2.1", "gas_mileage", "T...
#> $ author      (chr) "joannazhaoo", "woo.kara", "m.hawksey", "gspreadr"...
#> $ perm        (chr) "r", "r", "r", "rw", "rw", "r", "rw", "r", "r", "r...
#> $ version     (chr) "new", "new", "new", "new", "new", "new", "new", "...
#> $ updated     (time) 2015-07-20 22:13:25, 2015-07-20 01:08:07, 2015-07...
#> $ sheet_key   (chr) "1DoMXh2m3FGPoZAle9vnzg763D9FESTU506iqWkUTwtE", "1...
#> $ ws_feed     (chr) "https://spreadsheets.google.com/feeds/worksheets/...
#> $ alternate   (chr) "https://docs.google.com/spreadsheets/d/1DoMXh2m3F...
#> $ self        (chr) "https://spreadsheets.google.com/feeds/spreadsheet...
#> $ alt_key     (chr) NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA...
```

### Get a Google spreadsheet to practice with

Don't worry if you don't have any suitable Google Sheets lying around! We've published a sheet for you to practice with and have built functions into `googlesheets` to help you access it. The example sheet holds some of the [Gapminder data](https://github.com/jennybc/gapminder); feel free to [visit the Sheet in the browser](https://w3id.org/people/jennybc/googlesheets_gap_url). The code below will put a copy of this sheet into your Drive, titled "Gapminder".


```r
gs_gap() %>% 
  gs_copy(to = "Gapminder")
```

If that seems to have worked, go check for a sheet named "Gapminder" in your Google Sheets home screen: <https://docs.google.com/spreadsheets/>. You could also run `gs_ls()` again and make sure the Gapminder sheet is listed.

### Register a spreadsheet

If you plan to consume data from a sheet or edit it, you must first __register__ it. This is how `googlesheets` records important info about the sheet that is required downstream by the Google Sheets or Google Drive APIs. Once registered, you can print the result to get some basic info about the sheet.

`googlesheets` provides several registration functions. Specifying the sheet by title? Use `gs_title()`. By key? Use `gs_key()`. You get the idea.

*We're using the built-in functions `gs_gap_key()` and `gs_gap_url()` to produce the key and browser URL for the Gapminder example sheet, so you can see how this will play out with your own projects.*


```r
gap <- gs_title("Gapminder")
#> Sheet successfully identifed: "Gapminder"
gap
#>                   Spreadsheet title: Gapminder
#>                  Spreadsheet author: gspreadr
#>   Date of googlesheets registration: 2015-07-20 22:19:31 GMT
#>     Date of last spreadsheet update: 2015-03-23 20:34:08 GMT
#>                          visibility: private
#>                         permissions: rw
#>                             version: new
#> 
#> Contains 5 worksheets:
#> (Title): (Nominal worksheet extent as rows x columns)
#> Africa: 625 x 6
#> Americas: 301 x 6
#> Asia: 397 x 6
#> Europe: 361 x 6
#> Oceania: 25 x 6
#> 
#> Key: 1HT5B8SgkKqHdqHJmn5xiuaC04Ngb7dG9Tv94004vezA
#> Browser URL: https://docs.google.com/spreadsheets/d/1HT5B8SgkKqHdqHJmn5xiuaC04Ngb7dG9Tv94004vezA/

# Need to access a sheet you do not own?
# Access it by key if you know it!
(GAP_KEY <- gs_gap_key())
#> [1] "1BzfL0kZUz1TsI5zxJF1WNF01IxvC67FbOJUiiGMZ_mQ"
third_party_gap <- GAP_KEY %>%
  gs_key()
#> Authentication will be used.
#> Sheet successfully identifed: "test-gs-gapminder"

# Need to access a sheet you do not own but you have a sharing link?
# Access it by URL!
(GAP_URL <- gs_gap_url())
#> [1] "https://docs.google.com/spreadsheets/d/1BzfL0kZUz1TsI5zxJF1WNF01IxvC67FbOJUiiGMZ_mQ/"
third_party_gap <- GAP_URL %>%
  gs_url()
#> Sheet-identifying info appears to be a browser URL.
#> googlesheets will attempt to extract sheet key from the URL.
#> Putative key: 1BzfL0kZUz1TsI5zxJF1WNF01IxvC67FbOJUiiGMZ_mQ
#> Authentication will be used.
#> Sheet successfully identifed: "test-gs-gapminder"
# note: registration via URL may not work for "old" sheets

# Worried that a spreadsheet's registration is out-of-date?
# Re-register it!
gap <- gap %>% gs_gs()
#> Authentication will be used.
#> Sheet successfully identifed: "Gapminder"
```




The registration functions `gs_title()`, `gs_key()`, `gs_url()`, and `gs_gs()` return a registered sheet as a `googlesheet` object, which is the first argument to practically every function in this package. Likewise, almost every function returns a freshly registered `googlesheet` object, ready to be stored or piped into the next command.

*We export a utility function, `extract_key_from_url()`, to help you get and store the key from a browser URL. Registering via browser URL is fine, but registering by key is probably a better idea in the long-run.*

### Consume data

#### Ignorance is bliss

If you want to consume the data in a worksheet and get something rectangular back, use the all-purpose function `gs_read()`. By default, it reads all the data in a worksheet.


```r
oceania <- gap %>% gs_read(ws = "Oceania")
#> Accessing worksheet titled "Oceania"
oceania
#> Source: local data frame [24 x 6]
#> 
#>      country continent year lifeExp      pop gdpPercap
#> 1  Australia   Oceania 1952   69.12  8691212  10039.60
#> 2  Australia   Oceania 1957   70.33  9712569  10949.65
#> 3  Australia   Oceania 1962   70.93 10794968  12217.23
#> 4  Australia   Oceania 1967   71.10 11872264  14526.12
#> 5  Australia   Oceania 1972   71.93 13177000  16788.63
#> 6  Australia   Oceania 1977   73.49 14074100  18334.20
#> 7  Australia   Oceania 1982   74.74 15184200  19477.01
#> 8  Australia   Oceania 1987   76.32 16257249  21888.89
#> 9  Australia   Oceania 1992   77.56 17481977  23424.77
#> 10 Australia   Oceania 1997   78.83 18565243  26997.94
#> ..       ...       ...  ...     ...      ...       ...
str(oceania)
#> Classes 'tbl_df', 'tbl' and 'data.frame':	24 obs. of  6 variables:
#>  $ country  : chr  "Australia" "Australia" "Australia" "Australia" ...
#>  $ continent: chr  "Oceania" "Oceania" "Oceania" "Oceania" ...
#>  $ year     : int  1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 ...
#>  $ lifeExp  : num  69.1 70.3 70.9 71.1 71.9 ...
#>  $ pop      : int  8691212 9712569 10794968 11872264 13177000 14074100 15184200 16257249 17481977 18565243 ...
#>  $ gdpPercap: num  10040 10950 12217 14526 16789 ...
glimpse(oceania)
#> Observations: 24
#> Variables:
#> $ country   (chr) "Australia", "Australia", "Australia", "Australia", ...
#> $ continent (chr) "Oceania", "Oceania", "Oceania", "Oceania", "Oceania...
#> $ year      (int) 1952, 1957, 1962, 1967, 1972, 1977, 1982, 1987, 1992...
#> $ lifeExp   (dbl) 69.120, 70.330, 70.930, 71.100, 71.930, 73.490, 74.7...
#> $ pop       (int) 8691212, 9712569, 10794968, 11872264, 13177000, 1407...
#> $ gdpPercap (dbl) 10039.60, 10949.65, 12217.23, 14526.12, 16788.63, 18...
```

You can target specific cells via the `range =` argument. The simplest usage is to specify an Excel-like cell range, such as range = "D12:F15" or range = "R1C12:R6C15". The cell rectangle can be specified in various other ways, using helper functions.


```r
gap %>% gs_read(ws = 2, range = "A1:D8")
#> Accessing worksheet titled "Americas"
#> Source: local data frame [7 x 4]
#> 
#>     country continent year lifeExp
#> 1 Argentina  Americas 1952  62.485
#> 2 Argentina  Americas 1957  64.399
#> 3 Argentina  Americas 1962  65.142
#> 4 Argentina  Americas 1967  65.634
#> 5 Argentina  Americas 1972  67.065
#> 6 Argentina  Americas 1977  68.481
#> 7 Argentina  Americas 1982  69.942
gap %>% gs_read(ws = "Europe", range = cell_rows(1:4))
#> Accessing worksheet titled "Europe"
#> Source: local data frame [3 x 6]
#> 
#>   country continent year lifeExp     pop gdpPercap
#> 1 Albania    Europe 1952   55.23 1282697  1601.056
#> 2 Albania    Europe 1957   59.28 1476505  1942.284
#> 3 Albania    Europe 1962   64.82 1728137  2312.889
gap %>% gs_read(ws = "Europe", range = cell_rows(100:103), col_names = FALSE)
#> Accessing worksheet titled "Europe"
#> Source: local data frame [4 x 6]
#> 
#>        X1     X2   X3    X4      X5        X6
#> 1 Finland Europe 1962 68.75 4491443  9371.843
#> 2 Finland Europe 1967 69.83 4605744 10921.636
#> 3 Finland Europe 1972 70.87 4639657 14358.876
#> 4 Finland Europe 1977 72.52 4738902 15605.423
gap %>% gs_read(ws = "Africa", range = cell_cols(1:4))
#> Accessing worksheet titled "Africa"
#> Source: local data frame [624 x 4]
#> 
#>    country continent year lifeExp
#> 1  Algeria    Africa 1952  43.077
#> 2  Algeria    Africa 1957  45.685
#> 3  Algeria    Africa 1962  48.303
#> 4  Algeria    Africa 1967  51.407
#> 5  Algeria    Africa 1972  54.518
#> 6  Algeria    Africa 1977  58.014
#> 7  Algeria    Africa 1982  61.368
#> 8  Algeria    Africa 1987  65.799
#> 9  Algeria    Africa 1992  67.744
#> 10 Algeria    Africa 1997  69.152
#> ..     ...       ...  ...     ...
gap %>% gs_read(ws = "Asia", range = cell_limits(c(1, 4), c(5, NA)))
#> Accessing worksheet titled "Asia"
#> Source: local data frame [4 x 3]
#> 
#>   lifeExp      pop gdpPercap
#> 1  28.801  8425333  779.4453
#> 2  30.332  9240934  820.8530
#> 3  31.997 10267083  853.1007
#> 4  34.020 11537966  836.1971
```

`gs_read()` is a wrapper that bundles together the most common methods to read data from the API and transform it for downstream use. You can refine it's behavior further, by passing more arguments via `...`. Read the help file for more details.

If `gs_read()` doesn't do what you need, then keep reading for the underlying functions to read and post-process data.

#### Specify the consumption method

There are three ways to consume data from a worksheet within a Google spreadsheet. The order goes from fastest-but-more-limited to slowest-but-most-flexible:

  * `gs_read_csv()`: Don't let the name scare you! Nothing is written to file during this process. The name just reflects that, under the hood, we request the data via the "exportcsv" link. For cases where `gs_read_csv()` and `gs_read_listfeed()` both work, we see that `gs_read_csv()` is around __50 times faster__. Use this when your data occupies a nice rectangle in the sheet and you're willing to consume all of it. You will get a `tbl_df` back, which is basically just a `data.frame`. In fact, you might want to use `gs_read_csv()` in other, less tidy scenarios and do further munging in R.
  * `gs_read_listfeed()`: Gets data via the ["list feed"](https://developers.google.com/google-apps/spreadsheets/#working_with_list-based_feeds), which consumes data row-by-row. Like `gs_read_csv()`, this is appropriate when your data occupies a nice rectangle. You will again get a `tbl_df` back, but your variable names may have been mangled (by Google, not us!). Specifically, variable names will be forcefully lowercased and all non-alpha-numeric characters will be removed. Why do we even have this function? The list feed supports some query parameters for sorting and filtering the data, which we plan to support (#17).
  * `gs_read_cellfeed()`: Get data via the ["cell feed"](https://developers.google.com/google-apps/spreadsheets/#working_with_cell-based_feeds), which consumes data cell-by-cell. This is appropriate when you want to consume arbitrary cells, rows, columns, and regions of the sheet. It is invoked by `gs_read()` whenever the `range =` argument is used. It works great for modest amounts of data but can be rather slow otherwise. `gs_read_cellfeed()` returns a `tbl_df` with __one row per cell__. You can target specific cells via the `range` argument. See below for demos of `gs_reshape_cellfeed()` and `gs_simplify_cellfeed()` which help with post-processing.


```r
# Get the data for worksheet "Oceania": the super-fast csv way
oceania_csv <- gap %>% gs_read_csv(ws = "Oceania")
#> Accessing worksheet titled "Oceania"
str(oceania_csv)
#> Classes 'tbl_df', 'tbl' and 'data.frame':	24 obs. of  6 variables:
#>  $ country  : chr  "Australia" "Australia" "Australia" "Australia" ...
#>  $ continent: chr  "Oceania" "Oceania" "Oceania" "Oceania" ...
#>  $ year     : int  1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 ...
#>  $ lifeExp  : num  69.1 70.3 70.9 71.1 71.9 ...
#>  $ pop      : int  8691212 9712569 10794968 11872264 13177000 14074100 15184200 16257249 17481977 18565243 ...
#>  $ gdpPercap: num  10040 10950 12217 14526 16789 ...
oceania_csv
#> Source: local data frame [24 x 6]
#> 
#>      country continent year lifeExp      pop gdpPercap
#> 1  Australia   Oceania 1952   69.12  8691212  10039.60
#> 2  Australia   Oceania 1957   70.33  9712569  10949.65
#> 3  Australia   Oceania 1962   70.93 10794968  12217.23
#> 4  Australia   Oceania 1967   71.10 11872264  14526.12
#> 5  Australia   Oceania 1972   71.93 13177000  16788.63
#> 6  Australia   Oceania 1977   73.49 14074100  18334.20
#> 7  Australia   Oceania 1982   74.74 15184200  19477.01
#> 8  Australia   Oceania 1987   76.32 16257249  21888.89
#> 9  Australia   Oceania 1992   77.56 17481977  23424.77
#> 10 Australia   Oceania 1997   78.83 18565243  26997.94
#> ..       ...       ...  ...     ...      ...       ...

# Get the data for worksheet "Oceania": the less-fast tabular way ("list feed")
oceania_list_feed <- gap %>% gs_read_listfeed(ws = "Oceania") 
#> Accessing worksheet titled "Oceania"
str(oceania_list_feed)
#> Classes 'tbl_df', 'tbl' and 'data.frame':	24 obs. of  6 variables:
#>  $ country  : chr  "Australia" "Australia" "Australia" "Australia" ...
#>  $ continent: chr  "Oceania" "Oceania" "Oceania" "Oceania" ...
#>  $ year     : int  1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 ...
#>  $ lifeexp  : num  69.1 70.3 70.9 71.1 71.9 ...
#>  $ pop      : int  8691212 9712569 10794968 11872264 13177000 14074100 15184200 16257249 17481977 18565243 ...
#>  $ gdppercap: num  10040 10950 12217 14526 16789 ...
oceania_list_feed
#> Source: local data frame [24 x 6]
#> 
#>      country continent year lifeexp      pop gdppercap
#> 1  Australia   Oceania 1952   69.12  8691212  10039.60
#> 2  Australia   Oceania 1957   70.33  9712569  10949.65
#> 3  Australia   Oceania 1962   70.93 10794968  12217.23
#> 4  Australia   Oceania 1967   71.10 11872264  14526.12
#> 5  Australia   Oceania 1972   71.93 13177000  16788.63
#> 6  Australia   Oceania 1977   73.49 14074100  18334.20
#> 7  Australia   Oceania 1982   74.74 15184200  19477.01
#> 8  Australia   Oceania 1987   76.32 16257249  21888.89
#> 9  Australia   Oceania 1992   77.56 17481977  23424.77
#> 10 Australia   Oceania 1997   78.83 18565243  26997.94
#> ..       ...       ...  ...     ...      ...       ...

# Get the data for worksheet "Oceania": the slow cell-by-cell way ("cell feed")
oceania_cell_feed <- gap %>% gs_read_cellfeed(ws = "Oceania") 
#> Accessing worksheet titled "Oceania"
str(oceania_cell_feed)
#> Classes 'tbl_df', 'tbl' and 'data.frame':	150 obs. of  5 variables:
#>  $ cell     : chr  "A1" "B1" "C1" "D1" ...
#>  $ cell_alt : chr  "R1C1" "R1C2" "R1C3" "R1C4" ...
#>  $ row      : int  1 1 1 1 1 1 2 2 2 2 ...
#>  $ col      : int  1 2 3 4 5 6 1 2 3 4 ...
#>  $ cell_text: chr  "country" "continent" "year" "lifeExp" ...
#>  - attr(*, "ws_title")= chr "Oceania"
oceania_cell_feed
#> Source: local data frame [150 x 5]
#> 
#>    cell cell_alt row col cell_text
#> 1    A1     R1C1   1   1   country
#> 2    B1     R1C2   1   2 continent
#> 3    C1     R1C3   1   3      year
#> 4    D1     R1C4   1   4   lifeExp
#> 5    E1     R1C5   1   5       pop
#> 6    F1     R1C6   1   6 gdpPercap
#> 7    A2     R2C1   2   1 Australia
#> 8    B2     R2C2   2   2   Oceania
#> 9    C2     R2C3   2   3      1952
#> 10   D2     R2C4   2   4     69.12
#> ..  ...      ... ... ...       ...
```

#### Quick speed comparison

Let's consume all the data for Africa by all 3 methods and see how long it takes.


```r
jfun <- function(readfun)
  system.time(do.call(readfun, list(gs_gap(), ws = "Africa", verbose = FALSE)))
readfuns <- c("gs_read_csv", "gs_read_listfeed", "gs_read_cellfeed")
readfuns <- sapply(readfuns, get, USE.NAMES = TRUE)
sapply(readfuns, jfun)
#>            gs_read_csv gs_read_listfeed gs_read_cellfeed
#> user.self        0.034            0.143            1.016
#> sys.self         0.001            0.021            0.053
#> elapsed          1.029            0.782            2.178
#> user.child       0.000            0.000            0.000
#> sys.child        0.000            0.000            0.000
```

#### Post-processing data from the cell feed

If you consume data from the cell feed with `gs_read_cellfeed(..., range = ...)`, you get a data.frame back with **one row per cell**. The package offers two functions to post-process this into something more useful, `gs_reshape_cellfeed()` and `gs_simplify_cellfeed()`.

To reshape into a table, use `gs_reshape_cellfeed()`. You can signal that the first row contains column names (or not) with `col_names = TRUE` (or `FALSE`). Or you can provide a character vector of names. This is inspired by the `col_names` argument of `readxl::read_excel()` and `readr::read_delim()`, which generalizes the `header` argument of `read.table()`.


```r
# Reshape: instead of one row per cell, make a nice rectangular data.frame
australia_cell_feed <- gap %>%
  gs_read_cellfeed(ws = "Oceania", range = "A1:F13") 
#> Accessing worksheet titled "Oceania"
str(australia_cell_feed)
#> Classes 'tbl_df', 'tbl' and 'data.frame':	78 obs. of  5 variables:
#>  $ cell     : chr  "A1" "B1" "C1" "D1" ...
#>  $ cell_alt : chr  "R1C1" "R1C2" "R1C3" "R1C4" ...
#>  $ row      : int  1 1 1 1 1 1 2 2 2 2 ...
#>  $ col      : int  1 2 3 4 5 6 1 2 3 4 ...
#>  $ cell_text: chr  "country" "continent" "year" "lifeExp" ...
#>  - attr(*, "ws_title")= chr "Oceania"
oceania_cell_feed
#> Source: local data frame [150 x 5]
#> 
#>    cell cell_alt row col cell_text
#> 1    A1     R1C1   1   1   country
#> 2    B1     R1C2   1   2 continent
#> 3    C1     R1C3   1   3      year
#> 4    D1     R1C4   1   4   lifeExp
#> 5    E1     R1C5   1   5       pop
#> 6    F1     R1C6   1   6 gdpPercap
#> 7    A2     R2C1   2   1 Australia
#> 8    B2     R2C2   2   2   Oceania
#> 9    C2     R2C3   2   3      1952
#> 10   D2     R2C4   2   4     69.12
#> ..  ...      ... ... ...       ...
australia_reshaped <- australia_cell_feed %>% gs_reshape_cellfeed()
str(australia_reshaped)
#> Classes 'tbl_df', 'tbl' and 'data.frame':	12 obs. of  6 variables:
#>  $ country  : chr  "Australia" "Australia" "Australia" "Australia" ...
#>  $ continent: chr  "Oceania" "Oceania" "Oceania" "Oceania" ...
#>  $ year     : int  1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 ...
#>  $ lifeExp  : num  69.1 70.3 70.9 71.1 71.9 ...
#>  $ pop      : int  8691212 9712569 10794968 11872264 13177000 14074100 15184200 16257249 17481977 18565243 ...
#>  $ gdpPercap: num  10040 10950 12217 14526 16789 ...
australia_reshaped
#> Source: local data frame [12 x 6]
#> 
#>      country continent year lifeExp      pop gdpPercap
#> 1  Australia   Oceania 1952  69.120  8691212  10039.60
#> 2  Australia   Oceania 1957  70.330  9712569  10949.65
#> 3  Australia   Oceania 1962  70.930 10794968  12217.23
#> 4  Australia   Oceania 1967  71.100 11872264  14526.12
#> 5  Australia   Oceania 1972  71.930 13177000  16788.63
#> 6  Australia   Oceania 1977  73.490 14074100  18334.20
#> 7  Australia   Oceania 1982  74.740 15184200  19477.01
#> 8  Australia   Oceania 1987  76.320 16257249  21888.89
#> 9  Australia   Oceania 1992  77.560 17481977  23424.77
#> 10 Australia   Oceania 1997  78.830 18565243  26997.94
#> 11 Australia   Oceania 2002  80.370 19546792  30687.75
#> 12 Australia   Oceania 2007  81.235 20434176  34435.37

# Example: first 3 rows
gap_3rows <- gap %>% gs_read_cellfeed("Europe", range = cell_rows(1:3))
#> Accessing worksheet titled "Europe"
gap_3rows %>% head()
#> Source: local data frame [6 x 5]
#> 
#>   cell cell_alt row col cell_text
#> 1   A1     R1C1   1   1   country
#> 2   B1     R1C2   1   2 continent
#> 3   C1     R1C3   1   3      year
#> 4   D1     R1C4   1   4   lifeExp
#> 5   E1     R1C5   1   5       pop
#> 6   F1     R1C6   1   6 gdpPercap

# convert to a data.frame (by default, column names found in first row)
gap_3rows %>% gs_reshape_cellfeed()
#> Source: local data frame [2 x 6]
#> 
#>   country continent year lifeExp     pop gdpPercap
#> 1 Albania    Europe 1952   55.23 1282697  1601.056
#> 2 Albania    Europe 1957   59.28 1476505  1942.284

# arbitrary cell range, column names no longer available in first row
gap %>%
  gs_read_cellfeed("Oceania", range = "D12:F15") %>%
  gs_reshape_cellfeed(col_names = FALSE)
#> Accessing worksheet titled "Oceania"
#> Source: local data frame [4 x 3]
#> 
#>       X4       X5       X6
#> 1 80.370 19546792 30687.75
#> 2 81.235 20434176 34435.37
#> 3 69.390  1994794 10556.58
#> 4 70.260  2229407 12247.40

# arbitrary cell range, direct specification of column names
gap %>%
  gs_read_cellfeed("Oceania", range = cell_limits(c(2, 1), c(5, 3))) %>%
  gs_reshape_cellfeed(col_names = paste("thing", c("one", "two", "three"),
                                        sep = "_"))
#> Accessing worksheet titled "Oceania"
#> Source: local data frame [4 x 3]
#> 
#>   thing_one thing_two thing_three
#> 1 Australia   Oceania        1952
#> 2 Australia   Oceania        1957
#> 3 Australia   Oceania        1962
#> 4 Australia   Oceania        1967
```

To extract the cell data into an atomic vector, possibly named, use `gs_simplify_cellfeed()`. You can signal that the first row contains column names (or not) with `col_names = TRUE` (or `FALSE`). There are several arguments to control conversion.


```r
# Example: first row only
gap_1row <- gap %>% gs_read_cellfeed("Europe", range = cell_rows(1))
#> Accessing worksheet titled "Europe"
gap_1row
#> Source: local data frame [6 x 5]
#> 
#>   cell cell_alt row col cell_text
#> 1   A1     R1C1   1   1   country
#> 2   B1     R1C2   1   2 continent
#> 3   C1     R1C3   1   3      year
#> 4   D1     R1C4   1   4   lifeExp
#> 5   E1     R1C5   1   5       pop
#> 6   F1     R1C6   1   6 gdpPercap

# convert to a named character vector
gap_1row %>% gs_simplify_cellfeed()
#>          A1          B1          C1          D1          E1          F1 
#>   "country" "continent"      "year"   "lifeExp"       "pop" "gdpPercap"

# Example: single column
gap_1col <- gap %>% gs_read_cellfeed("Europe", range = cell_cols(3))
#> Accessing worksheet titled "Europe"
gap_1col
#> Source: local data frame [361 x 5]
#> 
#>    cell cell_alt row col cell_text
#> 1    C1     R1C3   1   3      year
#> 2    C2     R2C3   2   3      1952
#> 3    C3     R3C3   3   3      1957
#> 4    C4     R4C3   4   3      1962
#> 5    C5     R5C3   5   3      1967
#> 6    C6     R6C3   6   3      1972
#> 7    C7     R7C3   7   3      1977
#> 8    C8     R8C3   8   3      1982
#> 9    C9     R9C3   9   3      1987
#> 10  C10    R10C3  10   3      1992
#> ..  ...      ... ... ...       ...

# convert to a un-named character vector and drop the variable name
gap_1col %>% gs_simplify_cellfeed(notation = "none", col_names = TRUE)
#>   [1] "year" "1952" "1957" "1962" "1967" "1972" "1977" "1982" "1987" "1992"
#>  [11] "1997" "2002" "2007" "1952" "1957" "1962" "1967" "1972" "1977" "1982"
#>  [21] "1987" "1992" "1997" "2002" "2007" "1952" "1957" "1962" "1967" "1972"
#>  [31] "1977" "1982" "1987" "1992" "1997" "2002" "2007" "1952" "1957" "1962"
#>  [41] "1967" "1972" "1977" "1982" "1987" "1992" "1997" "2002" "2007" "1952"
#>  [51] "1957" "1962" "1967" "1972" "1977" "1982" "1987" "1992" "1997" "2002"
#>  [61] "2007" "1952" "1957" "1962" "1967" "1972" "1977" "1982" "1987" "1992"
#>  [71] "1997" "2002" "2007" "1952" "1957" "1962" "1967" "1972" "1977" "1982"
#>  [81] "1987" "1992" "1997" "2002" "2007" "1952" "1957" "1962" "1967" "1972"
#>  [91] "1977" "1982" "1987" "1992" "1997" "2002" "2007" "1952" "1957" "1962"
#> [101] "1967" "1972" "1977" "1982" "1987" "1992" "1997" "2002" "2007" "1952"
#> [111] "1957" "1962" "1967" "1972" "1977" "1982" "1987" "1992" "1997" "2002"
#> [121] "2007" "1952" "1957" "1962" "1967" "1972" "1977" "1982" "1987" "1992"
#> [131] "1997" "2002" "2007" "1952" "1957" "1962" "1967" "1972" "1977" "1982"
#> [141] "1987" "1992" "1997" "2002" "2007" "1952" "1957" "1962" "1967" "1972"
#> [151] "1977" "1982" "1987" "1992" "1997" "2002" "2007" "1952" "1957" "1962"
#> [161] "1967" "1972" "1977" "1982" "1987" "1992" "1997" "2002" "2007" "1952"
#> [171] "1957" "1962" "1967" "1972" "1977" "1982" "1987" "1992" "1997" "2002"
#> [181] "2007" "1952" "1957" "1962" "1967" "1972" "1977" "1982" "1987" "1992"
#> [191] "1997" "2002" "2007" "1952" "1957" "1962" "1967" "1972" "1977" "1982"
#> [201] "1987" "1992" "1997" "2002" "2007" "1952" "1957" "1962" "1967" "1972"
#> [211] "1977" "1982" "1987" "1992" "1997" "2002" "2007" "1952" "1957" "1962"
#> [221] "1967" "1972" "1977" "1982" "1987" "1992" "1997" "2002" "2007" "1952"
#> [231] "1957" "1962" "1967" "1972" "1977" "1982" "1987" "1992" "1997" "2002"
#> [241] "2007" "1952" "1957" "1962" "1967" "1972" "1977" "1982" "1987" "1992"
#> [251] "1997" "2002" "2007" "1952" "1957" "1962" "1967" "1972" "1977" "1982"
#> [261] "1987" "1992" "1997" "2002" "2007" "1952" "1957" "1962" "1967" "1972"
#> [271] "1977" "1982" "1987" "1992" "1997" "2002" "2007" "1952" "1957" "1962"
#> [281] "1967" "1972" "1977" "1982" "1987" "1992" "1997" "2002" "2007" "1952"
#> [291] "1957" "1962" "1967" "1972" "1977" "1982" "1987" "1992" "1997" "2002"
#> [301] "2007" "1952" "1957" "1962" "1967" "1972" "1977" "1982" "1987" "1992"
#> [311] "1997" "2002" "2007" "1952" "1957" "1962" "1967" "1972" "1977" "1982"
#> [321] "1987" "1992" "1997" "2002" "2007" "1952" "1957" "1962" "1967" "1972"
#> [331] "1977" "1982" "1987" "1992" "1997" "2002" "2007" "1952" "1957" "1962"
#> [341] "1967" "1972" "1977" "1982" "1987" "1992" "1997" "2002" "2007" "1952"
#> [351] "1957" "1962" "1967" "1972" "1977" "1982" "1987" "1992" "1997" "2002"
#> [361] "2007"
```

### Create sheets

You can use `googlesheets` to create new spreadsheets.


```r
foo <- gs_new("foo")
#> Sheet "foo" created in Google Drive.
#> Worksheet dimensions: 1000 x 26.
foo
#>                   Spreadsheet title: foo
#>                  Spreadsheet author: gspreadr
#>   Date of googlesheets registration: 2015-07-20 22:19:51 GMT
#>     Date of last spreadsheet update: 2015-07-20 22:19:48 GMT
#>                          visibility: private
#>                         permissions: rw
#>                             version: new
#> 
#> Contains 1 worksheets:
#> (Title): (Nominal worksheet extent as rows x columns)
#> Sheet1: 1000 x 26
#> 
#> Key: 1OBRGUa3d4RkwXJcnbyN8f0fa1WqHCjM34-Ie_PWyaB0
#> Browser URL: https://docs.google.com/spreadsheets/d/1OBRGUa3d4RkwXJcnbyN8f0fa1WqHCjM34-Ie_PWyaB0/
```

By default, there will be an empty worksheet called "Sheet1", but you can control it's title, extent, and initial data with additional arguments to `gs_new()` (see `gs_edit_cells()` in the next section). You can also add, rename, and delete worksheets within an existing sheet via `gs_ws_new()`, `gs_ws_rename()`, and `gs_ws_delete()`. Copy an entire spreadsheet with `gs_copy()`.

### Edit cells

There are two ways to edit cells within an existing worksheet of an existing spreadsheet:

  * `gs_edit_cells()` can write into an arbitrary cell rectangle
  * `gs_add_row()` can add a new row to the bottom of an existing cell rectangle
  
If you have the choice, `gs_add_row()` is faster, but it can only be used when your data occupies a very neat rectangle in the upper left corner of the sheet. It relies on the [list feed](https://developers.google.com/google-apps/spreadsheets/#working_with_list-based_feeds). `gs_edit_cells()` relies on [batch editing](https://developers.google.com/google-apps/spreadsheets/#updating_multiple_cells_with_a_batch_request) on the [cell feed](https://developers.google.com/google-apps/spreadsheets/#working_with_cell-based_feeds).

We'll work within the completely empty sheet created above, `foo`. If your edit populates the sheet with everything it should have, set `trim = TRUE` and we will resize the sheet to match the data. Then the nominal worksheet extent is much more informative (vs. the default of 1000 rows and 26 columns) and any future consumption via the cell feed will be much faster.


```r
## foo <- gs_new("foo")
## initialize the worksheets
foo <- foo %>% gs_ws_new("edit_cells")
#> Worksheet "edit_cells" added to sheet "foo".
#> Worksheet dimensions: 1000 x 26.
foo <- foo %>% gs_ws_new("add_row")
#> Worksheet "add_row" added to sheet "foo".
#> Worksheet dimensions: 1000 x 26.

## add first six rows of iris data (and var names) into a blank sheet
foo <- foo %>%
  gs_edit_cells(ws = "edit_cells", input = head(iris), trim = TRUE)
#> Range affected by the update: "A1:E7"
#> Worksheet "edit_cells" successfully updated with 35 new value(s).
#> Accessing worksheet titled "edit_cells"
#> Authentication will be used.
#> Sheet successfully identifed: "foo"
#> Accessing worksheet titled "edit_cells"
#> Worksheet "edit_cells" dimensions changed to 7 x 5.

## initialize sheet with column headers and one row of data
## the list feed is picky about this
foo <- foo %>% 
  gs_edit_cells(ws = "add_row", input = head(iris, 1), trim = TRUE)
#> Range affected by the update: "A1:E2"
#> Worksheet "add_row" successfully updated with 10 new value(s).
#> Accessing worksheet titled "add_row"
#> Authentication will be used.
#> Sheet successfully identifed: "foo"
#> Accessing worksheet titled "add_row"
#> Worksheet "add_row" dimensions changed to 2 x 5.
## add the next 5 rows of data ... careful not to go too fast
for(i in 2:6) {
  foo <- foo %>% gs_add_row(ws = "add_row", input = iris[i, ])
  Sys.sleep(0.3)
}
#> Row successfully appended.
#> Row successfully appended.
#> Row successfully appended.
#> Row successfully appended.
#> Row successfully appended.

## let's inspect out work
foo %>% gs_read(ws = "edit_cells")
#> Accessing worksheet titled "edit_cells"
#> Source: local data frame [6 x 5]
#> 
#>   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
#> 1          5.1         3.5          1.4         0.2  setosa
#> 2          4.9         3.0          1.4         0.2  setosa
#> 3          4.7         3.2          1.3         0.2  setosa
#> 4          4.6         3.1          1.5         0.2  setosa
#> 5          5.0         3.6          1.4         0.2  setosa
#> 6          5.4         3.9          1.7         0.4  setosa
foo %>% gs_read(ws = "add_row")
#> Accessing worksheet titled "add_row"
#> Source: local data frame [6 x 5]
#> 
#>   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
#> 1          5.1         3.5          1.4         0.2  setosa
#> 2          4.9         3.0          1.4         0.2  setosa
#> 3          4.7         3.2          1.3         0.2  setosa
#> 4          4.6         3.1          1.5         0.2  setosa
#> 5          5.0         3.6          1.4         0.2  setosa
#> 6          5.4         3.9          1.7         0.4  setosa
```

Go to [your Google Sheets home screen](https://docs.google.com/spreadsheets/u/0/), find the new sheet `foo` and look at it. You should see some iris data in the worksheets named `edit_cells` and `add_row`.

Note how we always store the returned value from `gs_edit_cells()` (and all other sheet editing functions). That's because the registration info changes whenever we edit the sheet and we re-register it inside these functions, so this idiom will help you make sequential edits and queries to the same sheet.

Read the function documentation for `gs_edit_cells()` for how to specify where the data goes, via an anchor cell, and in which direction, via the shape of the input or the `byrow =` argument.

### Delete sheets

Let's clean up by deleting the `foo` spreadsheet we've been playing with.


```r
gs_delete(foo)
#> Success. "foo" moved to trash in Google Drive.
```

If you'd rather specify sheets for deletion by title, look at `gs_grepdel()` and `gs_vecdel()`. These functions also allow the deletion of multiple sheets at once.

### Upload delimited files or Excel workbooks

Here's how we can create a new spreadsheet from a suitable local file. First, we'll write then upload a comma-delimited excerpt from the iris data.


```r
iris %>%
  head(5) %>%
  write.csv("iris.csv", row.names = FALSE)
iris_ss <- gs_upload("iris.csv")
#> "iris.csv" uploaded to Google Drive and converted to a Google Sheet named "iris"
iris_ss
#>                   Spreadsheet title: iris
#>                  Spreadsheet author: gspreadr
#>   Date of googlesheets registration: 2015-07-20 22:20:23 GMT
#>     Date of last spreadsheet update: 2015-07-20 22:20:21 GMT
#>                          visibility: private
#>                         permissions: rw
#>                             version: new
#> 
#> Contains 1 worksheets:
#> (Title): (Nominal worksheet extent as rows x columns)
#> iris: 1000 x 26
#> 
#> Key: 14EJWUvfIjRW7Z-pIStWfkG7XvhJKVBU9gxVIrnddTJM
#> Browser URL: https://docs.google.com/spreadsheets/d/14EJWUvfIjRW7Z-pIStWfkG7XvhJKVBU9gxVIrnddTJM/
iris_ss %>% gs_read()
#> Accessing worksheet titled "iris"
#> Source: local data frame [5 x 5]
#> 
#>   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
#> 1          5.1         3.5          1.4         0.2  setosa
#> 2          4.9         3.0          1.4         0.2  setosa
#> 3          4.7         3.2          1.3         0.2  setosa
#> 4          4.6         3.1          1.5         0.2  setosa
#> 5          5.0         3.6          1.4         0.2  setosa
file.remove("iris.csv")
#> [1] TRUE
```

Now we'll upload a multi-sheet Excel workbook. Slowly.


```r
gap_xlsx <- gs_upload(system.file("mini-gap.xlsx", package = "googlesheets"))
#> "mini-gap.xlsx" uploaded to Google Drive and converted to a Google Sheet named "mini-gap"
gap_xlsx
#>                   Spreadsheet title: mini-gap
#>                  Spreadsheet author: gspreadr
#>   Date of googlesheets registration: 2015-07-20 22:20:29 GMT
#>     Date of last spreadsheet update: 2015-07-20 22:20:27 GMT
#>                          visibility: private
#>                         permissions: rw
#>                             version: new
#> 
#> Contains 5 worksheets:
#> (Title): (Nominal worksheet extent as rows x columns)
#> Africa: 1000 x 26
#> Americas: 1000 x 26
#> Asia: 1000 x 26
#> Europe: 1000 x 26
#> Oceania: 1000 x 26
#> 
#> Key: 1UPE1nDcjsm5qiD1dsuWMcgUahMQyXRE6ofb9boaZ2iE
#> Browser URL: https://docs.google.com/spreadsheets/d/1UPE1nDcjsm5qiD1dsuWMcgUahMQyXRE6ofb9boaZ2iE/
gap_xlsx %>% gs_read(ws = "Asia")
#> Accessing worksheet titled "Asia"
#> Source: local data frame [5 x 6]
#> 
#>       country continent year lifeExp       pop gdpPercap
#> 1 Afghanistan      Asia 1952  28.801   8425333  779.4453
#> 2     Bahrain      Asia 1952  50.939    120447 9867.0848
#> 3  Bangladesh      Asia 1952  37.484  46886859  684.2442
#> 4    Cambodia      Asia 1952  39.417   4693836  368.4693
#> 5       China      Asia 1952  44.000 556263527  400.4486
```

And we clean up after ourselves on Google Drive.


```r
gs_vecdel(c("iris", "mini-gap"))
#> Authentication will be used.
#> Sheet successfully identifed: "mini-gap"
#> Success. "mini-gap" moved to trash in Google Drive.
#> Authentication will be used.
#> Sheet successfully identifed: "iris"
#> Success. "iris" moved to trash in Google Drive.
#> [1] TRUE TRUE
## achieves same as:
## gs_delete(iris_ss)
## gs_delete(gap_xlsx)
```

### Download sheets as csv, pdf, or xlsx file

You can download a Google Sheet as a csv, pdf, or xlsx file. Downloading the spreadsheet as a csv file will export the first worksheet (default) unless another worksheet is specified.


```r
gs_title("Gapminder") %>%
  gs_download(ws = "Africa", to = "gapminder-africa.csv")
#> Sheet successfully identifed: "Gapminder"
#> Accessing worksheet titled "Africa"
#> Sheet successfully downloaded: /Users/jenny/research/googlesheets/vignettes/gapminder-africa.csv
## is it there? yes!
read.csv("gapminder-africa.csv") %>% head()
#>   country continent year lifeExp      pop gdpPercap
#> 1 Algeria    Africa 1952  43.077  9279525  2449.008
#> 2 Algeria    Africa 1957  45.685 10270856  3013.976
#> 3 Algeria    Africa 1962  48.303 11000948  2550.817
#> 4 Algeria    Africa 1967  51.407 12760499  3246.992
#> 5 Algeria    Africa 1972  54.518 14760787  4182.664
#> 6 Algeria    Africa 1977  58.014 17152804  4910.417
```

Download the entire spreadsheet as an Excel workbook.


```r
gs_title("Gapminder") %>% 
  gs_download(to = "gapminder.xlsx")
#> Sheet successfully identifed: "Gapminder"
#> Sheet successfully downloaded: /Users/jenny/research/googlesheets/vignettes/gapminder.xlsx
```

Go check it out in Excel, if you wish!

And now we clean up the downloaded files.


```r
file.remove(c("gapminder.xlsx", "gapminder-africa.csv"))
#> [1] TRUE TRUE
```

### Authorization using OAuth2
 
If you use a function that requires authentication, it will be auto-triggered. But you can also initiate the process explicitly if you wish, like so:
 

```r
# Give googlesheets permission to access your spreadsheets and google drive
gs_auth() 
```
 
Use `gs_auth(new_user = TRUE)`, to force the process to begin anew. Otherwise, the credentials left behind will be used to refresh your access token as needed.

The function `gs_user()` will print and return some information about the current authenticated user and session.


```r
user_session_info <- gs_user()
#>           displayName: google sheets
#>          emailAddress: gspreadr@gmail.com
#>                  date: 2015-07-20 22:19:28 GMT
#>          access token: valid
#>  peek at access token: ya29....oOPjQ
#> peek at refresh token: 1/zNh...ATCKT
user_session_info
#> $displayName
#> [1] "google sheets"
#> 
#> $emailAddress
#> [1] "gspreadr@gmail.com"
#> 
#> $date
#> [1] "2015-07-20 22:19:28 GMT"
#> 
#> $token_valid
#> [1] TRUE
#> 
#> $peek_acc
#> [1] "ya29....oOPjQ"
#> 
#> $peek_ref
#> [1] "1/zNh...ATCKT"
```

### "Old" Google Sheets

In March 2014 [Google introduced "new" Sheets](https://support.google.com/docs/answer/3541068?hl=en). "New" Sheets and "old" sheets behave quite differently with respect to access via API and present a big headache for us. Recently, we've noted that Google is forcibly converting sheets: [all "old" Sheets will be switched over the "new" sheets during 2015](https://support.google.com/docs/answer/6082736?p=new_sheets_migrate&rd=1). However there are still "old" sheets lying around, so we've made some effort to support them, when it's easy to do so. But keep your expectations low.

In particular, `gs_read_csv()` does not currently work for "old" sheets.

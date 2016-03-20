How `plateR` helps you
----------------------

The idea is that you've done an experiment in a plate and you want to turn what you did into data that's ready to be analyzed as painlessly as possible. There are just two simple steps:

1 Put the data in a file in `plateR` format 2 Read in the data with `read_plate` or `add_plate` depending on your situation

What makes `plateR` helpful is that you can store all of the information about your experiment in the shape of a plate, which is probably how you remember and recorded what you did. Then `plateR` will take that intuitive file and rearrange it into a data frame with one well per row, which is ideal for analysis.

The example
-----------

Imagine you've invented two new antibiotics. To show how well they work, you filled up a 96-well plate with dilutions of them and mixed in four different types of bacteria. Then, you measured how many of the bacteria got killed. So for each well in the plate you know:

-   The drug (A or B)
-   The concentration of drug (100 uM to 0.05 nM and no drug)
-   The bacterial species (E. coli, S. enterocolitis, C. trachomatis, and N. gonorrhoeae)
-   The amount of killing in the well

The first three items are variables you chose in setting up the experiment. The fourth item is what you measured.

Step 1: Put the data in `plateR` format
---------------------------------------

The first step is to create a file for the experiment. Ideally all of the information needed to completely describe the experiment will be in one file (i.e. both the design of the experiment and the data you measured). Even more ideally, you won't have to do any complicated mental gymnastics or copy-and-pasting to translate your map of the plate to a useful, machine-readable form.

`plateR` format solves both of these problems. It's simply a .csv file representing a single plate, containing one or more plate layouts. Each layout maps a single variable, so for the example experiment, there would be four layouts in the file: Drug, Concentration, Bacteria, and Killing.

Several examples came with the package. Load `plateR` (i.e. run `library(plateR)`) and then run `system.file("extdata", package = "plateR")`. Open the folder listed there and then open `example-1.csv` in a spreadsheet editor. As you can see, there are four layouts in the file, describing the four variables in the experiment.

The format is pretty simple:

-   .csv file
-   Top left cell of each layout is the name
-   The rest of the top row of each layout is the column numbers (1:12 for a 96-well plate)
-   The rest of the left column is the row names (A:H for a 96-well plate)
-   One line between layouts

You can use `plateR` format with any standard plate size (6 to 384 wells). Not every well has to be filled in every layout (e.g. bacterial species is not applicable for blank wells). If a well is blank in every layout in a file, it's omitted. If it's blank in some but not others, it'll get `NA` where it's blank.

Step 2: Read in the data
------------------------

Now that your file is set up, you're ready to read in the data with `read_plate()`. (Note that below we use `system.file()` here to get the file path of the example file, but for your own files you would specify the file path without using `system.file()`).

``` r
file_path <- system.file("extdata", "example-1.csv", package = "plateR")
   
data <- read_plate(
      file = file_path,             # full path to the .csv file
      well_ids_column = "Wells",    # name to give column of well IDs (optional)
      plate_size = 96               # total number of wells on the plate (optional)
)
str(data)
#> 'data.frame':    96 obs. of  5 variables:
#>  $ Wells        : chr  "A01" "A02" "A03" "A04" ...
#>  $ Drug         : chr  "A" "A" "A" "A" ...
#>  $ Concentration: num  1.00e+02 2.00e+01 4.00 8.00e-01 1.60e-01 3.20e-02 6.40e-03 1.28e-03 2.56e-04 5.12e-05 ...
#>  $ Bacteria     : chr  "E. coli" "E. coli" "E. coli" "E. coli" ...
#>  $ Killing      : num  98 95 92 41 17 2 1.5 1.8 1 0.5 ...

head(data)
#>   Wells Drug Concentration Bacteria Killing
#> 1   A01    A       100.000  E. coli      98
#> 2   A02    A        20.000  E. coli      95
#> 3   A03    A         4.000  E. coli      92
#> 4   A04    A         0.800  E. coli      41
#> 5   A05    A         0.160  E. coli      17
#> 6   A06    A         0.032  E. coli       2
```

So what happened? `read_plate()` read in the `plateR` format file you created and turned each layout into a column, using the name of the layout specified in the file. So you have four columns: Drug, Concentration, Bacteria, and Killing. It additionally creates a column named "Wells" with the well identifiers for each well. Now, each well is represented by a single row, with the values indicated in the file for each column.

Step 2 (again): Another way to read in the data
-----------------------------------------------

Scientific instruments that work on plates often provide data in the shape of a plate. In that case, you're all set to create a `plateR` format file as described above. Sometimes, though, you'll get data back formatted with one well per row. In that case, you'd either need to copy-and-paste the data into a plate layout to use `read_plate()` or you'd need to copy-and-paste all the other information (Drug, Concentration, Bacteria) to line up with the data.

`add_plate()` solves this problem. You provide a data frame with one well per row and well IDs and then you provide a `plateR` format file with the other information and `add_plate()` knits them together well-by-well. Here's an example using the other two files installed along with `plateR`.

``` r
file2A <- system.file("extdata", "example-2-part-A.csv", package = "plateR")
data2 <- read.csv(file2A)

str(data2)
#> 'data.frame':    96 obs. of  2 variables:
#>  $ Wells  : Factor w/ 96 levels "A01","A02","A03",..: 1 2 3 4 5 6 7 8 9 10 ...
#>  $ Killing: num  98 95 92 41 17 2 1.5 1.8 1 0.5 ...

head(data2)
#>   Wells Killing
#> 1   A01      98
#> 2   A02      95
#> 3   A03      92
#> 4   A04      41
#> 5   A05      17
#> 6   A06       2

meta <- system.file("extdata", "example-2-part-B.csv", package = "plateR")
data2 <- add_plate(
      file = meta,                # full path to the .csv file
      data = data2,               # data frame to add to    
      well_ids_column = "Wells",  # name of column of well IDs in data frame
      plate_size = 96             # total number of wells on the plate (optional)
)

str(data2)
#> 'data.frame':    96 obs. of  5 variables:
#>  $ Wells        : Factor w/ 96 levels "A01","A02","A03",..: 1 2 3 4 5 6 7 8 9 10 ...
#>  $ Killing      : num  98 95 92 41 17 2 1.5 1.8 1 0.5 ...
#>  $ Drug         : chr  "A" "A" "A" "A" ...
#>  $ Concentration: num  1.00e+02 2.00e+01 4.00 8.00e-01 1.60e-01 3.20e-02 6.40e-03 1.28e-03 2.56e-04 5.12e-05 ...
#>  $ Bacteria     : chr  "E. coli" "E. coli" "E. coli" "E. coli" ...

head(data2)
#>   Wells Killing Drug Concentration Bacteria
#> 1   A01      98    A       100.000  E. coli
#> 2   A02      95    A        20.000  E. coli
#> 3   A03      92    A         4.000  E. coli
#> 4   A04      41    A         0.800  E. coli
#> 5   A05      17    A         0.160  E. coli
#> 6   A06       2    A         0.032  E. coli
```
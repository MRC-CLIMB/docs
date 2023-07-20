# Using RStudio

Was originally an interface to interactive with `R` programming language. Since July 2022, it is now known as Posit as they support other languages such as python. The main feature remains the same, to easily edit scripts and view input/ouput files. Our version of RStudio only supports the R programming language.

If you are not familiar with `R` or RStudio you can find more information here: https://education.rstudio.com/learn/beginner/ 

## How to start?

Both `R` and RStudio are already installed for you. You will find them under the “Notebooks” section of the Main Work Area. 

![RStudio](../img/r-open.png)

For pure access to `R` you can use the jupyter notebook. For access to RStudio, a separate tab will be opened:

![RStudio](../img/rstudio.png)

1. **Editor** – This is where you can interactively write and edit your scripts.
2. **Console** – This is where your code can be run.
3. **Environment** – This is where all input/output objects can be viewed as either Data or Values.
4. **Output** – View plots, packages, help for package documentation.

You can Select “File” and “New File” to create and open a variety of file types:

![RStudio](../img/r-new-file.png)

## Installing packages

You can install packages which provide additional features and documentation to the base `R` packages. You can do this via the GUI or via the terminal like so:

```
install.packages(“ggplot2”)
```

Once a package is installed, you will need to load it:
```
library(ggplot2)
```

Now all of the features from `ggplot2` will be available to you.

All plots are highly customisable, you just need to know the right functions and layering them !

<!-- prettier-ignore -->
!!! tip
    Regarding R version control – we will only be providing the newest version of R so please do check the version for your scripts.

Here is a simple example of a plot using the `ggplot2` package:

```
library(ggplot2)
mpg
ggplot(mpg, aes(displ, hwy, colour = class)) + 
  geom_point()
```

![RStudio](../img/r-in-action.png)


## Other useful packages

* `BactDating` – time scaled phylogenies
* `ggtree` – visualising phylogenetic trees
* `gtsummary` – presentation ready data summary and analytic results tables
* `lme4` – Linear Mixed-Effects Models
* `phyloseq` – Handling and analysis of high-throughput microbial communities
* `qiime2R` – importing Qiime output tables
* `RcolorBrewer` – ColorBrewer Palettes
* `tidyverse` - collection of R packages designed for data science
* `VennDiagram` – Generates High-Resolution Venn and Euler Plots

There are many “cheat sheets” available for [individual packages which you can find here.](https://raw.githubusercontent.com/rstudio/cheatsheets/main/data-visualization-2.1.pdf 
)


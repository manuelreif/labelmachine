
labellexicon
============

`labellexicon` is an **R** package that helps you assigning new labels to data.frame variables. Furthermore, you can manage your label translations in so called **LabelLexicon** files, which are **yaml** files, which hold the mappings (translations) of your variable labels. This makes it very easy using the same label translations in multiple projects that share similar data structure.

The most important functions are:

-   `read_lexicon_file`: Reads in a **yaml** file holding the label translations for one or more variables.
-   `translate`: Use a LabelLexicon object in order to relabel one or more variables of your data.frame. The variables may be of type `number`, `character` or `factor`. In case of a factor variable, you have the possibility to keep the original ordering by using the function argument `keep_ordering = TRUE`.
-   `new_lexicon`: Create a LabelLexicon object from a named list, holding the label translations for one or more variables.
-   `write_lexicon_file`: Write a LabelLexicon object to a **yaml** file.
-   `select`: Pick a subset of a LabelLexicon object.
-   `mutate`: Alter the translations of a variable in a LabelLexicon object.
-   `rename`: Rename a variable in a LabelLexicon object.
-   `merge`: Merge two or more LabelLexicon objects into a single LabelLexicon object.

Installation
------------

``` r
# Install development version from GitHub
devtools::install_github('a-maldet/labellexicon', build_opts = NULL)
```

Usage
-----

### Load LabelLexicon file (yaml)

Structure of your LabelLexicon file `short_lex.yaml`:

``` yaml
supp:
  VC: Ascorbic acid
  OJ: Orange juice
dose:
  "0.5": Low
  "1": Medium
  "2": High
```

The first level names are the variable names. The second level names represent the original values of the variables and the third level values are the labels that should be assigned to the values of the variables.

Load your `LabelLexicon` file with `read_lexicon_file`:

### Relabel your variables with a LabelLexicon

We can use the LabelLexicon object `lex` in order to translate a categorical variable (not necessarily a factor variable) in our data.frame to a factor variable with the labels that are defined in the LabelLexicon `lex`.

Relable your data.frame variables with `translate`:

``` r
# data.frame with original values 
ToothGrowth %>% head

# data.frame with new labels
ToothGrowth %>%
  translate(lex, "supp") %>%
  translate(lex, "dose") %>%
  head
```

Now, the columns `supp` and `dose` are factor variables, which hold the desired labels and have the same ordering as in the `LabelLexicon` file. If the original variable is a factor variable and you want to keep the original ordering, you can set the function argurment .

### Create a LabelLexicon object manually

Instead of reading in a yaml file you can also create a LabelLexicon manually from a named list object, holding named character vectors. Each entry of the named list represents a variable of your data.frames and each named character vector is a translation. The names of the character vector entries represent the original values of the variable and the values of the character vector entries are the new labels that should be assigned.

``` r
lex <- list(
    supp = c(VC = "Ascorbic acid", OJ = "Orange juice"),
    dose = c("0.5" = "Low", "1.0" = "Medium", "2.0" = "High")
  ) %>%
  new_lexicon
```

### Alter your LabelLexicon

Sometimes it can be useful to alter your LabelLexicon object.

With `select` you may pick a subset of LabelLexicon entries:

``` r
lex %>%
  select(c("supp", "dose"))
```

    ## 
    ## --- LabelLexicon ---
    ## Variable 'supp':
    ##              VC              OJ 
    ## "Ascorbic acid"  "Orange juice" 
    ## 
    ## Variable 'dose':
    ##      0.5      1.0      2.0 
    ##    "Low" "Medium"   "High"

With `mutate` you can set a new label translation (character vector) for a variable:

``` r
lex %>%
  mutate("supp", c(VC = "Ascorbic a.", OJ = "Orange j."))
```

    ## 
    ## --- LabelLexicon ---
    ## Variable 'supp':
    ##            VC            OJ 
    ## "Ascorbic a."   "Orange j." 
    ## 
    ## Variable 'dose':
    ##      0.5      1.0      2.0 
    ##    "Low" "Medium"   "High"

With `rename` you can rename a LabelLexicon entry:

``` r
lex %>%
  rename(c("supp", "dose"), c("SUPP", "DOSE"))
```

    ## 
    ## --- LabelLexicon ---
    ## Variable 'SUPP':
    ##              VC              OJ 
    ## "Ascorbic acid"  "Orange juice" 
    ## 
    ## Variable 'DOSE':
    ##      0.5      1.0      2.0 
    ##    "Low" "Medium"   "High"

### Merge two ore more LabelLexicas

Sometimes you may want to merge two or more LabelLexicas into one LabelLexicon. This can be done with `merge_lexica`

### Overriding error handlers in nested environments

The function `composerr_parent` looks up the existing error handler in the parent environment and not in the current environment. This can be useful in nested scoping situations (for example when checking if a nested list object has a valid structure) and if you want to store the nested error handler functions under the same name (overriding error handler functions).

``` r
### Example 2 ###
### check if all entries of the list object 'obj' are TRUE ###
obj <- list(x = TRUE, y = TRUE, z = FALSE)
# original error handler
err_h <- composerr("obj is invalid")
# check each list element 
sapply(names(obj), function(name) {
  # modify error handler to nested sitation
  err_h <- composerr_parent(paste("Error in", name), err_h)
  # check element and throw error FALSE
  if (!obj[[name]])
    err_h("Value is FALSE")
})
# --- resulting message ---
# "obj is invalid: Error in z: Value is FALSE"
```

License
-------

[GPL-3](https://R-package.github.io/styledTables/LICENSE)

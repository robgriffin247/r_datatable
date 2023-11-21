Fundamentals of the data.table
================
<em style='color:#00508060;'>November 20, 2023</em>

<!--
- Example data
- `dt[i, j, by]` For SQL users: `from[where, select, group by]`
- Vectors & data types, lists, data frames, data tables
- creating/converting data.tables -->

### Actions on rows

With the data.table syntax, we describe and perform actions on rows,
such as sorting or subsetting data, as `i` in `dt[i]`.

<details>
<summary>
Sorting
</summary>

A data.table that has been sorted on one or more variables, in ascending
or descending order, can be returned by using the `order()` function.

- Sort by a single variable in *ascending* order: `dt[order(x)]`
- Sort by a single variable in *descending* order: `dt[order(-x)]`
- Sort by a *multiple* variables: `dt[order(x, y, z)]`

``` r
# Example: sort dt_starwars from shortest to tallest character
dt_starwars[order(height)]
##                      name height mass hair_color skin_color eye_color
##  1:                  Yoda     66   17      white      green     brown
##  2:         Ratts Tyerell     79   15       none grey, blue   unknown
##  3: Wicket Systri Warrick     88   20      brown      brown     brown
## ---                                                                  
## 85:           Poe Dameron     NA   NA      brown      light     brown
## 86:                   BB8     NA   NA       none       none     black
## 87:        Captain Phasma     NA   NA    unknown    unknown   unknown
## 8 variables not shown: [birth_year, sex, gender, homeworld, species, films, vehicles, starships]
```

</details>
<details>
<summary>
Subset by numerical index
</summary>

The second common action to be performed is subsetting data — reducing a
dataset to a set of observations that meet a criteria. That criteria
might be as simple as position. For example, you could take an ordered
dataset and return the top five observations. This is done by passing a
vector of integers as `i`. Vectors are the basic data structure in R and
are created using the concatenate function, `c()` — e.g. `c(1, 2, 3)`.
Individual numbers can also be passed without the `c()` function, while
a sequence of integers can also be generated using the `:` operator (and
the `c()` function omitted), e.g. `1:3` is equivalent to `c(1, 2, 3)`.

- Return the top row: `dt[1]`
- Return the top three rows: `dt[1:3]`
- Return the first, fifth and ninth rows: `dt[c(1, 5, 9)]`
- Return the last row: `dt[nrow(dt)]`

``` r
# Example: return the top five rows
dt_starwars[1:5]
##              name height mass hair_color  skin_color eye_color birth_year
## 1: Luke Skywalker    172   77      blond        fair      blue       19.0
## 2:          C-3PO    167   75       <NA>        gold    yellow      112.0
## 3:          R2-D2     96   32       <NA> white, blue       red       33.0
## 4:    Darth Vader    202  136       none       white    yellow       41.9
## 5:    Leia Organa    150   49      brown       light     brown       19.0
## 7 variables not shown: [sex, gender, homeworld, species, films, vehicles, starships]
```

The last example, using `nrow()` to return the last row can be rewritten
to use one of data.tables special characters. There are a few useful
special characters and I will cover these more later. For now, the value
returned by `nrow(dt)` is equal to `.N`, so you can rewrite the last
expression as:

- Return the last row using the `.N` special character: `dt[.N]`

``` r
# Example: return the last row
dt_starwars[.N]
##             name height mass hair_color skin_color eye_color birth_year    sex
## 1: Padmé Amidala    165   45      brown      light     brown         46 female
## 6 variables not shown: [gender, homeworld, species, films, vehicles, starships]
```

<strong>Chaining</strong>

Finally, you may have sorted a data.table to see which observations are
the top *n*, e.g. the five tallest characters. You can do this by first
storing the sorted table in the working environment as
`dt_starwars_sorted <- dt_starwars[order(-height)]`. But you can also
avoid storing variables unnecessarily by acting directly on the returned
data.table, using something called **chaining**. This allows multiple
data.table statements to be joined together and executed in sequence to
give a single return. It is done by adding more square braces —
`dt[][][]...[]`.

- Return the top five sorted observations: `dt[order(x)][1:5]`

``` r
# Example: return the five tallest characters
dt_starwars[order(-height)][1:5]
##            name height mass hair_color skin_color eye_color birth_year  sex
## 1:  Yarael Poof    264   NA       none      white    yellow         NA male
## 2:      Tarfful    234  136      brown      brown      blue         NA male
## 3:      Lama Su    229   88       none       grey     black         NA male
## 4:    Chewbacca    228  112      brown    unknown      blue        200 male
## 5: Roos Tarpals    224   82       none       grey    orange         NA male
## 6 variables not shown: [gender, homeworld, species, films, vehicles, starships]
```

</details>
<details>
<summary>
Subset by condition
</summary>

Subsets can also be returned to give the observations that meet a
certain logical criteria. For example, all characters over 200 cm tall,
or all characters from Naboo. Such conditional subsetting is done using
logical operators. In R, the key logical operators are:

- `==` equal to
- `!=` not equal to
- `<` less than
- `>` greater than
- `<=` less than or equal to
- `>=` greater than or equal to
- `%in%` value is in a vector

All of the above require values either side of the operator
(e.g. `3 > 4`), and will return one or more logical values — `TRUE` or
`FALSE`. Other useful operators and functions for conditional subsetting
are:

- `&` to join logical operations like “and” so `TRUE` is returned if
  both conditions are met
- `|` to join logical operations like “or” so `TRUE` is returned if
  either condition is met
- `(` and `)` to set the order of operations, just like math
- `grepl()` to test if a pattern occurs in a value

With these, you can now return data to meet a certain criteria:

- Return rows where observed value is equal to a criteria: `dt[x==a]`
- Return rows where observed value is less than a criteria: `dt[x<a]`
- Return rows where observed value is one of several criteria:
  `dt[x %in% c(a, b, c)]`
- Return rows where observed value is between two values:
  `dt[x>5 & x<=10]`

``` r
# Exmaple: return all characters under 180 cm tall from Naboo and Tatooine 
dt_starwars[height<180 & homeworld %in% c('Naboo', 'Tatooine')]
##               name height mass hair_color  skin_color eye_color birth_year
##  1: Luke Skywalker    172   77      blond        fair      blue         19
##  2:          C-3PO    167   75       <NA>        gold    yellow        112
##  3:          R2-D2     96   32       <NA> white, blue       red         33
## ---                                                                       
##  9:          Cordé    157   NA      brown       light     brown         NA
## 10:          Dormé    165   NA      brown       light     brown         NA
## 11:  Padmé Amidala    165   45      brown       light     brown         46
## 7 variables not shown: [sex, gender, homeworld, species, films, vehicles, starships]
```

</details>

#### Summary of `dt[i]`

- Actions on rows are performed as `i` in `dt[i]`
- This is useful for returning sorted data.tables and data.tables
  containing a subset of the original data
- Logical operators can be used to subset to data meeting a certain
  criteria

<!-- 
&#10;### To add:
&#10;- select; `dt[, j]`, `.SD`
- assignment
- conditional assignment (`dt[, ifelse(...)]` and `dt[i, j]`)
- compute; `x(j)`
- group by; `by` and `keyby`
- special characters; `.SD, .N, .I ...`
- chaining
&#10;-->

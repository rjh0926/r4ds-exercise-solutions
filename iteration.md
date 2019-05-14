
# Iteration {#iteration .r4ds-section}

## Introduction {#introduction-14 .r4ds-section}


```r
library("tidyverse")
library("stringr")
```
The package microbenchmark is used for timing code.

```r
library("microbenchmark")
```

## For loops {#for-loops .r4ds-section}

### Exercise 21.2.1 {.unnumbered .exercise data-number="21.2.1"}

<div class="question">

Write for-loops to:

1.  Compute the mean of every column in `mtcars`.
1.  Determine the type of each column in `nycflights13::flights`.
1.  Compute the number of unique values in each column of `iris`.
1.  Generate 10 random normals for each of $\mu$ = -10, 0, 10, and 100.

</div>

<div class="answer">
The answers for each part are below.

1.  To compute the mean of every column in `mtcars`.
    
    ```r
    output <- vector("double", ncol(mtcars))
    names(output) <- names(mtcars)
    for (i in names(mtcars)) {
      output[i] <- mean(mtcars[[i]])
    }
    output
    #>     mpg     cyl    disp      hp    drat      wt    qsec      vs      am 
    #>  20.091   6.188 230.722 146.688   3.597   3.217  17.849   0.438   0.406 
    #>    gear    carb 
    #>   3.688   2.812
    ```

1.  Determine the type of each column in `nycflights13::flights`.
    
    ```r
    output <- vector("list", ncol(nycflights13::flights))
    names(output) <- names(nycflights13::flights)
    for (i in names(nycflights13::flights)) {
      output[[i]] <- class(nycflights13::flights[[i]])
    }
    output
    #> $year
    #> [1] "integer"
    #> 
    #> $month
    #> [1] "integer"
    #> 
    #> $day
    #> [1] "integer"
    #> 
    #> $dep_time
    #> [1] "integer"
    #> 
    #> $sched_dep_time
    #> [1] "integer"
    #> 
    #> $dep_delay
    #> [1] "numeric"
    #> 
    #> $arr_time
    #> [1] "integer"
    #> 
    #> $sched_arr_time
    #> [1] "integer"
    #> 
    #> $arr_delay
    #> [1] "numeric"
    #> 
    #> $carrier
    #> [1] "character"
    #> 
    #> $flight
    #> [1] "integer"
    #> 
    #> $tailnum
    #> [1] "character"
    #> 
    #> $origin
    #> [1] "character"
    #> 
    #> $dest
    #> [1] "character"
    #> 
    #> $air_time
    #> [1] "numeric"
    #> 
    #> $distance
    #> [1] "numeric"
    #> 
    #> $hour
    #> [1] "numeric"
    #> 
    #> $minute
    #> [1] "numeric"
    #> 
    #> $time_hour
    #> [1] "POSIXct" "POSIXt"
    ```

    I used a `list`, not a character vector, since the class of an object can have multiple values.
    For example, the class of the `time_hour` column is POSIXct, POSIXt.


1.  To compute the number of unique values in each column of the `iris` dataset.
    
    ```r
    data("iris")
    iris_uniq <- vector("double", ncol(iris))
    names(iris_uniq) <- names(iris)
    for (i in names(iris)) {
      iris_uniq[i] <- length(unique(iris[[i]]))
    }
    iris_uniq
    #> Sepal.Length  Sepal.Width Petal.Length  Petal.Width      Species 
    #>           35           23           43           22            3
    ```

1.  To generate 10 random normals for each of $\mu$ = -10, 0, 10, and 100.
    
    ```r
    # number to draw
    n <- 10
    # values of the mean
    mu <- c(-10, 0, 10, 100)
    normals <- vector("list", length(mu))
    for (i in seq_along(normals)) {
      normals[[i]] <- rnorm(n, mean = mu[i])
    }
    normals
    #> [[1]]
    #>  [1] -11.40  -9.74 -12.44 -10.01  -9.38  -8.85 -11.82 -10.25 -10.24 -10.28
    #> 
    #> [[2]]
    #>  [1] -0.5537  0.6290  2.0650 -1.6310  0.5124 -1.8630 -0.5220 -0.0526
    #>  [9]  0.5430 -0.9141
    #> 
    #> [[3]]
    #>  [1] 10.47 10.36  8.70 10.74 11.89  9.90  9.06  9.98  9.17  8.49
    #> 
    #> [[4]]
    #>  [1] 100.9 100.2 100.2 101.6 100.1  99.9  98.1  99.7  99.7 101.1
    ```

    However, we don't need a for loop for this since `rnorm()` recycle the `mean` argument.
    
    ```r
    matrix(rnorm(n * length(mu), mean = mu), ncol = n)
    #>        [,1]   [,2]  [,3]     [,4]   [,5]   [,6]    [,7]   [,8]    [,9]
    #> [1,] -9.930  -9.56 -9.88 -10.2061 -12.27 -8.926 -11.178  -9.51  -8.663
    #> [2,] -0.639   2.76 -1.91   0.0192   2.68 -0.665  -0.976  -1.70   0.237
    #> [3,]  9.950  10.05 10.86  10.0296   9.64 11.114  11.065   8.53  11.318
    #> [4,] 99.749 100.58 99.76 100.5498 100.21 99.754 100.132 100.28 100.524
    #>      [,10]
    #> [1,] -9.39
    #> [2,] -0.11
    #> [3,] 10.17
    #> [4,] 99.91
    ```

</div>

### Exercise 21.2.2 {.unnumbered .exercise data-number="21.2.2"}

<div class="question">

Eliminate the for loop in each of the following examples by taking advantage of an existing function that works with vectors:

</div>

<div class="answer">


```r
out <- ""
for (x in letters) {
  out <- str_c(out, x)
}
out
#> [1] "abcdefghijklmnopqrstuvwxyz"
```

Since `str_c()` already works with vectors, use `str_c()` with the `collapse` argument to return a single string.

```r
str_c(letters, collapse = "")
#> [1] "abcdefghijklmnopqrstuvwxyz"
```

For this I'm going to rename the variable `sd` to something different because `sd` is the name of the function we want to use.


```r
x <- sample(100)
sd. <- 0
for (i in seq_along(x)) {
  sd. <- sd. + (x[i] - mean(x))^2
}
sd. <- sqrt(sd. / (length(x) - 1))
sd.
#> [1] 29
```

We could simply use the `sd` function.

```r
sd(x)
#> [1] 29
```
Or if there was a need to use the equation (e.g. for pedagogical reasons), then
the functions `mean()` and `sum()` already work with vectors:

```r
sqrt(sum((x - mean(x))^2) / (length(x) - 1))
#> [1] 29
```


```r
x <- runif(100)
out <- vector("numeric", length(x))
out[1] <- x[1]
for (i in 2:length(x)) {
  out[i] <- out[i - 1] + x[i]
}
out
#>   [1]  0.854  1.268  2.019  2.738  3.253  4.228  4.589  4.759  5.542  5.573
#>  [11]  6.363  6.529  6.558  7.344  8.169  9.134  9.513  9.687 10.291 11.097
#>  [21] 11.133 11.866 12.082 12.098 12.226 12.912 13.554 13.882 14.269 14.976
#>  [31] 15.674 16.600 17.059 17.655 17.820 18.387 19.285 19.879 20.711 21.304
#>  [41] 22.083 22.481 23.331 24.073 24.391 24.502 24.603 25.403 25.783 25.836
#>  [51] 26.823 27.427 27.576 28.114 28.240 29.203 29.250 29.412 30.348 31.319
#>  [61] 32.029 32.914 33.891 33.926 34.365 35.009 36.004 36.319 37.175 37.715
#>  [71] 38.588 39.104 39.973 40.830 41.176 41.176 41.381 42.326 42.607 43.488
#>  [81] 44.449 44.454 45.006 45.226 45.872 46.600 47.473 47.855 48.747 49.591
#>  [91] 50.321 50.359 50.693 51.443 52.356 52.560 53.032 53.417 53.810 54.028
```

The code above is calculating a cumulative sum. Use the function `cumsum()`

```r
all.equal(cumsum(x), out)
#> [1] TRUE
```

</div>

### Exercise 21.2.3 {.unnumbered .exercise data-number="21.2.3"}

<div class="question">

Combine your function writing and for loop skills:

1.  Write a for loop that `prints()` the lyrics to the children's song "Alice the camel".

1.  Convert the nursery rhyme "ten in the bed" to a function.
    Generalize it to any number of people in any sleeping structure.

1.  Convert the song "99 bottles of beer on the wall" to a function.
    Generalize to any number of any vessel containing any liquid on  surface.

</div>

<div class="answer">

The answers to each part follow.

1.  The lyrics for [Alice the Camel](http://www.kididdles.com/lyrics/a012.html) are:

    > Alice the camel has five humps. \
    > Alice the camel has five humps. \
    > Alice the camel has five humps. \
    > So go, Alice, go.

    This verse is repeated, each time with one fewer hump,
    until there are no humps.
    The last verse, with no humps, is:

    > Alice the camel has no humps. \
    > Alice the camel has no humps. \
    > Alice the camel has no humps. \
    > Now Alice is a horse.

    We'll iterate from five to no humps, and print out a different last line if there are no humps.
    
    ```r
    humps <- c("five", "four", "three", "two", "one", "no")
    for (i in humps) {
      cat(str_c("Alice the camel has ", rep(i, 3), " humps.",
        collapse = "\n"
      ), "\n")
      if (i == "no") {
        cat("Now Alice is a horse.\n")
      } else {
        cat("So go, Alice, go.\n")
      }
      cat("\n")
    }
    #> Alice the camel has five humps.
    #> Alice the camel has five humps.
    #> Alice the camel has five humps. 
    #> So go, Alice, go.
    #> 
    #> Alice the camel has four humps.
    #> Alice the camel has four humps.
    #> Alice the camel has four humps. 
    #> So go, Alice, go.
    #> 
    #> Alice the camel has three humps.
    #> Alice the camel has three humps.
    #> Alice the camel has three humps. 
    #> So go, Alice, go.
    #> 
    #> Alice the camel has two humps.
    #> Alice the camel has two humps.
    #> Alice the camel has two humps. 
    #> So go, Alice, go.
    #> 
    #> Alice the camel has one humps.
    #> Alice the camel has one humps.
    #> Alice the camel has one humps. 
    #> So go, Alice, go.
    #> 
    #> Alice the camel has no humps.
    #> Alice the camel has no humps.
    #> Alice the camel has no humps. 
    #> Now Alice is a horse.
    ```

1.  The lyrics for [Ten in the Bed](https://www.kididdles.com/lyrics/t003.html) are:

    > Here we go! \
    > There were ten in the bed \
    > and the little one said, \
    > “Roll over, roll over.” \
    > So they all rolled over and one fell out.

    This verse is repeated, each time with one fewer in the bed, until there is one left.
    That last verse is:

    > One!
    > There was one in the bed \
    > and the little one said, \
    > “I’m lonely...”

    
    ```r
    numbers <- c(
      "ten", "nine", "eight", "seven", "six", "five",
      "four", "three", "two", "one"
    )
    for (i in numbers) {
      cat(str_c("There were ", i, " in the bed\n"))
      cat("and the little one said\n")
      if (i == "one") {
        cat("I'm lonely...")
      } else {
        cat("Roll over, roll over\n")
        cat("So they all rolled over and one fell out.\n")
      }
      cat("\n")
    }
    #> There were ten in the bed
    #> and the little one said
    #> Roll over, roll over
    #> So they all rolled over and one fell out.
    #> 
    #> There were nine in the bed
    #> and the little one said
    #> Roll over, roll over
    #> So they all rolled over and one fell out.
    #> 
    #> There were eight in the bed
    #> and the little one said
    #> Roll over, roll over
    #> So they all rolled over and one fell out.
    #> 
    #> There were seven in the bed
    #> and the little one said
    #> Roll over, roll over
    #> So they all rolled over and one fell out.
    #> 
    #> There were six in the bed
    #> and the little one said
    #> Roll over, roll over
    #> So they all rolled over and one fell out.
    #> 
    #> There were five in the bed
    #> and the little one said
    #> Roll over, roll over
    #> So they all rolled over and one fell out.
    #> 
    #> There were four in the bed
    #> and the little one said
    #> Roll over, roll over
    #> So they all rolled over and one fell out.
    #> 
    #> There were three in the bed
    #> and the little one said
    #> Roll over, roll over
    #> So they all rolled over and one fell out.
    #> 
    #> There were two in the bed
    #> and the little one said
    #> Roll over, roll over
    #> So they all rolled over and one fell out.
    #> 
    #> There were one in the bed
    #> and the little one said
    #> I'm lonely...
    ```

1.  The lyrics of [Ninety-Nine Bottles of Beer on the Wall](https://en.wikipedia.org/wiki/99_Bottles_of_Beer) are

    > 99 bottles of beer on the wall, 99 bottles of beer. \
    > Take one down, pass it around, 98 bottles of beer on the wall

    This verse is repeated, each time with one few bottle, until
    there are no more bottles of beer. The last verse is

    > No more bottles of beer on the wall, no more bottles of beer. \
    > We've taken them down and passed them around; now we're drunk and passed out!

    For the bottles of beer, I define a helper function to correctly print the number of bottles.

    
    ```r
    bottles <- function(i) {
      if (i > 2) {
        bottles <- str_c(i - 1, " bottles")
      } else if (i == 2) {
        bottles <- "1 bottle"
      } else {
        bottles <- "no more bottles"
      }
      bottles
    }
    
    beer_bottles <- function(n) {
      # should test whether n >= 1.
      for (i in seq(n, 1)) {
        cat(str_c(bottles(i), " of beer on the wall, ", bottles(i), " of beer.\n"))
        cat(str_c(
          "Take one down and pass it around, ", bottles(i - 1),
          " of beer on the wall.\n\n"
        ))
      }
      cat("No more bottles of beer on the wall, no more bottles of beer.\n")
      cat(str_c("Go to the store and buy some more, ", bottles(n), " of beer on the wall.\n"))
    }
    beer_bottles(3)
    #> 2 bottles of beer on the wall, 2 bottles of beer.
    #> Take one down and pass it around, 1 bottle of beer on the wall.
    #> 
    #> 1 bottle of beer on the wall, 1 bottle of beer.
    #> Take one down and pass it around, no more bottles of beer on the wall.
    #> 
    #> no more bottles of beer on the wall, no more bottles of beer.
    #> Take one down and pass it around, no more bottles of beer on the wall.
    #> 
    #> No more bottles of beer on the wall, no more bottles of beer.
    #> Go to the store and buy some more, 2 bottles of beer on the wall.
    ```

</div>

#### Exercise 21.2.4 {.unnumbered .exercise data-number="21.2.4"}

<div class="question">
It's common to see for loops that don't preallocate the output and instead increase the length of a vector at each step:


```r
output <- vector("integer", 0)
for (i in seq_along(x)) {
  output <- c(output, lengths(x[[i]]))
}
output
```
How does this affect performance?
Design and execute an experiment.

</div>

<div class="answer">

I'll use the package microbenchmark to time this.
The `microbenchmark()` function will run an R expression a number of times and time it.

Define a function that appends to an integer vector.

```r
add_to_vector <- function(n) {
  output <- vector("integer", 0)
  for (i in seq_len(n)) {
    output <- c(output, i)
  }
  output
}
microbenchmark(add_to_vector(10000), times = 3)
#> Unit: milliseconds
#>                  expr min  lq mean median  uq max neval
#>  add_to_vector(10000) 134 142  153    150 163 176     3
```

And one that pre-allocates it.

```r
add_to_vector_2 <- function(n) {
  output <- vector("integer", n)
  for (i in seq_len(n)) {
    output[[i]] <- i
  }
  output
}
microbenchmark(add_to_vector_2(10000), times = 3)
#> Unit: microseconds
#>                    expr min   lq mean median   uq  max neval
#>  add_to_vector_2(10000) 787 3485 4866   6183 6905 7628     3
```

The pre-allocated vector is about **10** times faster!
You may get different answers, but the longer the vector and the bigger the objects, the more that pre-allocation will outperform appending.

</div>

## For loop variations {#for-loop-variations .r4ds-section}

### Exercise 21.3.1 {.unnumbered .exercise data-number="21.3.1"}

<div class="question">
Imagine you have a directory full of CSV files that you want to read in.
You have their paths in a vector,
`files <- dir("data/", pattern = "\\.csv$", full.names = TRUE)`, and now
want to read each one with `read_csv()`. Write the for loop that will
load them into a single data frame.

</div>

<div class="answer">

First, I pre-allocate a list. Then, I read each file into a data frame, and assign it to an element in that list.
The result is a list of data frames.
I then use `bind_rows()` to combine the list of data frames into a single data frame.

```r
df <- vector("list", length(files))
for (fname in seq_along(files)) {
  df[[i]] <- read_csv(files[[i]])
}
df <- bind_rows(df)
```

</div>

### Exercise 21.3.2 {.unnumbered .exercise data-number="21.3.2"}

<div class="question">
What happens if you use `for (nm in names(x))` and `x` has no names?
What if only some of the elements are named?
What if the names are not unique?
</div>

<div class="answer">

Let's try it out and see what happens.

When there are no names for the vector, it does not run the code in the loop (it runs zero iterations of the loop):

```r
x <- 1:3
print(names(x))
#> NULL
for (nm in names(x)) {
  print(nm)
  print(x[[nm]])
}
```
Note that the length of `NULL` is zero:

```r
length(NULL)
#> [1] 0
```

If there only some names, then we get an error if we try to access an element without a name.
However, oddly, `nm == ""` when there is no name.

```r
x <- c(a = 1, 2, c = 3)
names(x)
#> [1] "a" ""  "c"
```

```r
for (nm in names(x)) {
  print(nm)
  print(x[[nm]])
}
#> [1] "a"
#> [1] 1
#> [1] ""
#> Error in x[[nm]]: subscript out of bounds
```

Finally, if there are duplicate names, then `x[[nm]]` will give the *first* element with that name.
There is no way to access elements with duplicate names.

```r
x <- c(a = 1, a = 2, c = 3)
names(x)
#> [1] "a" "a" "c"
```

```r
for (nm in names(x)) {
  print(nm)
  print(x[[nm]])
}
#> [1] "a"
#> [1] 1
#> [1] "a"
#> [1] 1
#> [1] "c"
#> [1] 3
```

</div>

### Exercise 21.3.3 {.unnumbered .exercise data-number="21.3.3"}

<div class="question">
Write a function that prints the mean of each numeric column in a data frame, along with its name.
For example, `show_mean(iris)` would print:


```r
show_mean(iris)
# > Sepal.Length: 5.84
# > Sepal.Width:  3.06
# > Petal.Length: 3.76
# > Petal.Width:  1.20
```

Extra challenge: what function did I use to make sure that the numbers lined up nicely, even though the variable names had different lengths?

</div>

<div class="answer">

There may be other functions to do this, but I'll use `str_pad()`, and `str_length()` to ensure that the space given to the variable names is the same.
I messed around with the options to `format()` until I got two digits.

```r
show_mean <- function(df, digits = 2) {
  # Get max length of all variable names in the dataset
  maxstr <- max(str_length(names(df)))
  for (nm in names(df)) {
    if (is.numeric(df[[nm]])) {
      cat(
        str_c(str_pad(str_c(nm, ":"), maxstr + 1L, side = "right"),
          format(mean(df[[nm]]), digits = digits, nsmall = digits),
          sep = " "
        ),
        "\n"
      )
    }
  }
}
show_mean(iris)
#> Sepal.Length: 5.84 
#> Sepal.Width:  3.06 
#> Petal.Length: 3.76 
#> Petal.Width:  1.20
```

</div>

### Exercise 21.3.4 {.unnumbered .exercise data-number="21.3.4"}

<div class="question">

What does this code do?
How does it work?


```r
trans <- list(
  disp = function(x) x * 0.0163871,
  am = function(x) {
    factor(x, labels = c("auto", "manual"))
  }
)
```

```r
for (var in names(trans)) {
  mtcars[[var]] <- trans[[var]](mtcars[[var]])
}
```

</div>

<div class="answer">

This code mutates the `disp` and `am` columns:

-   `disp` is  multiplied by 0.0163871
-   `am` is replaced by a factor variable.

The code works by looping over a named list of functions.
It calls the named function in the list on the column of `mtcars` with the same name, and replaces the values of that column.

This is a function.

```r
trans[["disp"]]
```
This applies the function to the column of `mtcars` with the same name

```r
trans[["disp"]](mtcars[["disp"]])
```

</div>

## For loops vs. functionals {#for-loops-vs.functionals .r4ds-section}

### Exercise 21.4.1 {.unnumbered .exercise data-number="21.4.1"}

<div class="question">

Read the documentation for `apply()`.
In the 2nd case, what two for-loops does it generalize.

</div>

<div class="answer">

For an object with two-dimensions, such as a matrix or data frame, `apply()` replaces looping over the rows or columns of a matrix or data-frame.
The `apply()` function is used like `apply(X, MARGIN, FUN, ...)`, where `X` is a matrix or array, `FUN` is a function to apply, and `...` are additional arguments passed to `FUN`.

When `MARGIN = 1`, then the function is applied to each row.
For example, the following example calculates the row means of a matrix.

```r
X <- matrix(rnorm(15), nrow = 5)
X
#>          [,1]     [,2]    [,3]
#> [1,] -0.00538 -0.02408  0.8283
#> [2,] -1.35830  1.58450  1.1990
#> [3,]  1.57293  0.00568  0.6001
#> [4,] -1.19938 -0.64820  0.0588
#> [5,] -0.07266  1.85866 -1.2898
```

```r
apply(X, 1, mean)
#> [1]  0.266  0.475  0.726 -0.596  0.165
```
That is equivalent to this for-loop.

```r
X_row_means <- vector("numeric", length = nrow(X))
for (i in seq_len(nrow(X))) {
  X_row_means[[i]] <- mean(X[i, ])
}
X_row_means
#> [1]  0.266  0.475  0.726 -0.596  0.165
```

```r
X <- matrix(rnorm(15), nrow = 5)
X
#>        [,1]   [,2]   [,3]
#> [1,] -0.126 -0.415 -0.248
#> [2,]  0.384 -2.295  0.675
#> [3,] -0.542 -0.632 -0.595
#> [4,]  0.223 -0.531  0.110
#> [5,] -0.645  0.710  0.372
```

When `MARGIN = 2`, `apply()` is equivalent to a for-loop looping over columns.

```r
apply(X, 2, mean)
#> [1] -0.1414 -0.6324  0.0628
```

```r
X_col_means <- vector("numeric", length = ncol(X))
for (i in seq_len(ncol(X))) {
  X_col_means[[i]] <- mean(X[i, ])
}
X_col_means
#> [1] -0.263 -0.412 -0.590
```

</div>

### Exercise 21.4.2 {.unnumbered .exercise data-number="21.4.2"}

<div class="question">
Adapt `col_summary()` so that it only applies to numeric columns.
You might want to start with an `is_numeric()` function that returns a logical vector that has a `TRUE` corresponding to each numeric column.
</div>

<div class="answer">

The original `col_summary()` function is

```r
col_summary <- function(df, fun) {
  out <- vector("double", length(df))
  for (i in seq_along(df)) {
    out[i] <- fun(df[[i]])
  }
  out
}
```

The adapted version adds extra logic to only apply the function to numeric columns.

```r
col_summary2 <- function(df, fun) {
  # create an empty vector which will store whether each
  # column is numeric
  numeric_cols <- vector("logical", length(df))
  # test whether each column is numeric
  for (i in seq_along(df)) {
    numeric_cols[[i]] <- is.numeric(df[[i]])
  }
  # find the indexes of the numeric columns
  idxs <- which(numeric_cols)
  # find the number of numeric columns
  n <- sum(numeric_cols)
  # create a vector to hold the results
  out <- vector("double", n)
  # apply the function only to numeric vectors
  for (i in seq_along(idxs)) {
    out[[i]] <- fun(df[[idxs[[i]]]])
  }
  # name the vector
  names(out) <- names(df)[idxs]
  out
}
```

Let's test that `col_summary2()` works by creating a small data frame with
some numeric and non-numeric columns.

```r
df <- tibble(
  X1 = c(1, 2, 3),
  X2 = c("A", "B", "C"),
  X3 = c(0, -1, 5),
  X4 = c(TRUE, FALSE, TRUE)
)
col_summary2(df, mean)
#>   X1   X3 
#> 2.00 1.33
```
As expected, it only calculates the mean of the numeric columns, `X1` and `X3`.
Let's test that it works with another function.

```r
col_summary2(df, median)
#> X1 X3 
#>  2  0
```

</div>

## The map functions {#the-map-functions .r4ds-section}

### Exercise 21.5.1 {.unnumbered .exercise data-number="21.5.1"}

<div class="question">
Write code that uses one of the map functions to:

1.  Compute the mean of every column in `mtcars`.
1.  Determine the type of each column in `nycflights13::flights`.
1.  Compute the number of unique values in each column of `iris`.
1.  Generate 10 random normals for each of $\mu = -10$, $0$, $10$, and $100$.

</div>

<div class="answer">

1.  To calculate the mean of every column in `mtcars`, apply the function
    `mean()` to each column, and use `map_dbl`, since the results are numeric.
    
    ```r
    map_dbl(mtcars, mean)
    #>     mpg     cyl    disp      hp    drat      wt    qsec      vs      am 
    #>  20.091   6.188 230.722 146.688   3.597   3.217  17.849   0.438   0.406 
    #>    gear    carb 
    #>   3.688   2.812
    ```

1.  To calculate the type of every column in `nycflights13::flights` apply
    the function `typeof()`, discussed in the section on [Vector basics](http://r4ds.had.co.nz/vectors.html#vector-basics),
    and use `map_chr()`, since the results are character.
    
    ```r
    map_chr(nycflights13::flights, typeof)
    #>           year          month            day       dep_time sched_dep_time 
    #>      "integer"      "integer"      "integer"      "integer"      "integer" 
    #>      dep_delay       arr_time sched_arr_time      arr_delay        carrier 
    #>       "double"      "integer"      "integer"       "double"    "character" 
    #>         flight        tailnum         origin           dest       air_time 
    #>      "integer"    "character"    "character"    "character"       "double" 
    #>       distance           hour         minute      time_hour 
    #>       "double"       "double"       "double"       "double"
    ```


1.  There is no function that directly calculates the number of unique values
    in a vector. For a single column, the number of unique values of a vector
    can be calculated like so,
    
    ```r
    length(unique(iris$Species))
    #> [1] 3
    ```
    To apply this to all columns, we can provide the map an anonymous function.
    We can write anonymous function using the standard R syntax.
    
    ```r
    map_int(iris, function(x) length(unique(x)))
    #> Sepal.Length  Sepal.Width Petal.Length  Petal.Width      Species 
    #>           35           23           43           22            3
    ```
    We could also use the compact, one-sided formula shortcut that purrr provides.
    
    ```r
    map_int(iris, ~ length(unique(.)))
    #> Sepal.Length  Sepal.Width Petal.Length  Petal.Width      Species 
    #>           35           23           43           22            3
    ```
    In these examples, the `map_int()` function is used since `length()` returns an integer.
    However, the `map_dbl()` function will also work.
    
    ```r
    map_dbl(iris, ~ length(unique(.)))
    ```

1.  To generate 10 random normals for each of $\mu = -10$, $0$, $10$, and $100$:
    The result is a list of numeric vectors.
    
    ```r
    map(c(-10, 0, 10, 100), ~ rnorm(n = 10, mean = .))
    #> [[1]]
    #>  [1] -10.61  -8.96 -10.30  -8.58 -10.87 -12.22 -10.28  -9.62  -9.03  -9.74
    #> 
    #> [[2]]
    #>  [1]  1.207 -0.269 -0.698 -1.488  2.463  0.569 -1.111  1.249 -0.653 -0.147
    #> 
    #> [[3]]
    #>  [1]  8.73 10.40  8.07 11.05  9.18  9.36  9.85  8.58 10.61 10.09
    #> 
    #> [[4]]
    #>  [1]  98.4 100.3 100.7 100.0 101.7 102.1  99.6  98.6 100.4 100.1
    ```
    Since a single call of `rnorm()` returns a numeric vector with a length greater
    than one we cannot use `map_dbl`, which requires the function to return a numeric
    vector that is only length one (see [Exercise 21.5.4](#exercise-21.5.4)). 
    The map functions pass any additional arguments to the function being called.

</div>

### Exercise 21.5.2 {.unnumbered .exercise data-number="21.5.2"}

<div class="question">
How can you create a single vector that for each column in a data frame indicates whether or not it's a factor?
</div>

<div class="answer">

The function `is.factor()` indicates whether a vector is a factor.

```r
is.factor(diamonds$color)
#> [1] TRUE
is.factor(diamonds$color)
#> [1] TRUE
```
To check all columns in a factor for whether it is a factor is a job for a `map_*()` function.
Since the result of `is.factor()` is logical, we will use `map_lgl()` to apply `is.factor()` to the columns of the data frame.

```r
map_lgl(diamonds, is.factor)
#>   carat     cut   color clarity   depth   table   price       x       y 
#>   FALSE    TRUE    TRUE    TRUE   FALSE   FALSE   FALSE   FALSE   FALSE 
#>       z 
#>   FALSE
```

</div>

### Exercise 21.5.3 {.unnumbered .exercise data-number="21.5.3"}

<div class="question">
What happens when you use the map functions on vectors that aren't lists?
What does `map(1:5, runif)` do?
Why?
</div>

<div class="answer">

Map functions work with any vectors, not just lists.
As with lists, the map functions will apply the function to each element of the vector.
In the following examples, the inputs to `map()` are atomic vectors (logical, character, integer, double).

```r
map(c(TRUE, FALSE, TRUE), ~ !.)
#> [[1]]
#> [1] FALSE
#> 
#> [[2]]
#> [1] TRUE
#> 
#> [[3]]
#> [1] FALSE
map(c("Hello", "World"), str_to_upper)
#> [[1]]
#> [1] "HELLO"
#> 
#> [[2]]
#> [1] "WORLD"
map(1:5, ~ rnorm(.))
#> [[1]]
#> [1] 0.162
#> 
#> [[2]]
#> [1]  0.356 -0.360
#> 
#> [[3]]
#> [1] -0.109  1.828 -1.962
#> 
#> [[4]]
#> [1]  0.946  0.549 -0.577 -1.228
#> 
#> [[5]]
#> [1]  1.040  0.456 -2.380  0.598  0.128
map(c(-0.5, 0, 1), ~ rnorm(1, mean = .))
#> [[1]]
#> [1] -2.22
#> 
#> [[2]]
#> [1] 0.0361
#> 
#> [[3]]
#> [1] 2.01
```

It is important to be aware that while the input of `map()` can be any vector, the output is always a list.


```r
map(1:5, runif)
#> [[1]]
#> [1] 0.762
#> 
#> [[2]]
#> [1] 0.0784 0.1335
#> 
#> [[3]]
#> [1] 0.810 0.559 0.734
#> 
#> [[4]]
#> [1] 0.350 0.983 0.409 0.361
#> 
#> [[5]]
#> [1] 0.653 0.144 0.681 0.859 0.839
```
This expression is equivalent to running the following.

```r
list(
  runif(1),
  runif(2),
  runif(3),
  runif(4),
  runif(5)
)
#> [[1]]
#> [1] 0.881
#> 
#> [[2]]
#> [1] 0.238 0.579
#> 
#> [[3]]
#> [1] 0.898 0.344 0.761
#> 
#> [[4]]
#> [1] 0.731 0.852 0.976 0.113
#> 
#> [[5]]
#> [1] 0.9703 0.6481 0.0561 0.4731 0.2946
```
The `map()` function loops through the numbers 1 to 5.
For each value, it calls the `runif()` with that number as the first argument, which is the number of sample to draw.
The result is a length five list with numeric vectors of sizes one through five, each with random samples from a uniform distribution.
Note that although input to `map()` was an integer vector, the return value was a list.

</div>

### Exercise 21.5.4 {.unnumbered .exercise data-number="21.5.4"}

<div class="question">
What does `map(-2:2, rnorm, n = 5)` do?
Why?

What does `map_dbl(-2:2, rnorm, n = 5)` do?
Why?
</div>

<div class="answer">

Consider the first expression.

```r
map(-2:2, rnorm, n = 5)
#> [[1]]
#> [1] -1.72 -1.67 -1.72 -1.57 -2.12
#> 
#> [[2]]
#> [1] -0.542 -0.383 -1.175 -1.123 -2.473
#> 
#> [[3]]
#> [1]  0.124 -1.096  0.388 -0.755 -1.232
#> 
#> [[4]]
#> [1]  1.764  0.354 -0.042  0.163  2.026
#> 
#> [[5]]
#> [1] 2.85 2.83 2.25 3.41 1.84
```
This expression takes samples of size five from five normal distributions, with means of (-2, -1, 0, 1, and 2), but the same standard deviation (1).
It returns a list with each element a numeric vectors of length 5.

However, if instead, we use `map_dbl()`, the expression raises an error.

```r
map_dbl(-2:2, rnorm, n = 5)
#> Result 1 must be a single double, not a double vector of length 5
```
This is because the `map_dbl()` function requires the function it applies to each element to return a numeric vector of length one.
If the function returns either a non-numeric vector or a numeric vector with a length greater than one, `map_dbl()` will raise an error.
The reason for this strictness is that `map_dbl()` guarantees that it will return a numeric vector of the *same length* as its input vector.

This concept applies to the other `map_*()` functions.
The function `map_chr()` requires that the function always return a *character* vector of length one;
`map_int()` requires that the function always return an *integer* vector of length one;
`map_lglg()` requires that the function always return an *logical* vector of length one.
Use the `map()` function if the function will return values of varying types or lengths.

If we wanted a numeric vector, we could use `map()` followed by `flatten_dbl()`,

```r
flatten_dbl(map(-2:2, rnorm, n = 5))
#>  [1] -1.96303 -2.37404 -2.77211 -2.33388 -1.35705 -0.56374 -0.90412
#>  [8] -0.67164  0.21604 -2.08908  0.54449  0.46127 -1.48777 -0.69195
#> [15]  0.00689  0.84599  0.94107  0.46742  2.07907  1.87462  1.33398
#> [22]  0.87372  1.63056  0.81840  2.05932
```

</div>

### Exercise 21.5.5 {.unnumbered .exercise data-number="21.5.5"}

<div class="question">
Rewrite `map(x, function(df) lm(mpg ~ wt, data = df))` to eliminate the anonymous function.
</div>

<div class="answer">

This code in this question does not run, so I will use the following code.

```r
x <- split(mtcars, mtcars$cyl)
map(x, function(df) lm(mpg ~ wt, data = df))
#> $`4`
#> 
#> Call:
#> lm(formula = mpg ~ wt, data = df)
#> 
#> Coefficients:
#> (Intercept)           wt  
#>       39.57        -5.65  
#> 
#> 
#> $`6`
#> 
#> Call:
#> lm(formula = mpg ~ wt, data = df)
#> 
#> Coefficients:
#> (Intercept)           wt  
#>       28.41        -2.78  
#> 
#> 
#> $`8`
#> 
#> Call:
#> lm(formula = mpg ~ wt, data = df)
#> 
#> Coefficients:
#> (Intercept)           wt  
#>       23.87        -2.19
```

We can eliminate the use of an anonymous function using the `~` shortcut.

```r
map(x, ~ lm(mpg ~ wt, data = .))
#> $`4`
#> 
#> Call:
#> lm(formula = mpg ~ wt, data = .)
#> 
#> Coefficients:
#> (Intercept)           wt  
#>       39.57        -5.65  
#> 
#> 
#> $`6`
#> 
#> Call:
#> lm(formula = mpg ~ wt, data = .)
#> 
#> Coefficients:
#> (Intercept)           wt  
#>       28.41        -2.78  
#> 
#> 
#> $`8`
#> 
#> Call:
#> lm(formula = mpg ~ wt, data = .)
#> 
#> Coefficients:
#> (Intercept)           wt  
#>       23.87        -2.19
```

Though not the intent of this question, the other way to eliminate anonymous function is to create a named one.

```r
run_reg <- function(df) {
  lm(mpg ~ wt, data = df)
}
map(x, run_reg)
#> $`4`
#> 
#> Call:
#> lm(formula = mpg ~ wt, data = df)
#> 
#> Coefficients:
#> (Intercept)           wt  
#>       39.57        -5.65  
#> 
#> 
#> $`6`
#> 
#> Call:
#> lm(formula = mpg ~ wt, data = df)
#> 
#> Coefficients:
#> (Intercept)           wt  
#>       28.41        -2.78  
#> 
#> 
#> $`8`
#> 
#> Call:
#> lm(formula = mpg ~ wt, data = df)
#> 
#> Coefficients:
#> (Intercept)           wt  
#>       23.87        -2.19
```

</div>

## Dealing with failure {#dealing-with-failure .r4ds-section}

<!--html_preserve--><div class="alert alert-warning hints-alert">
<div class="hints-icon">
<i class="fa fa-exclamation-circle"></i>
</div>
<div class="hints-container">No exercises</div>
</div><!--/html_preserve-->

## Mapping over multiple arguments {#mapping-over-multiple-arguments .r4ds-section}

<!--html_preserve--><div class="alert alert-warning hints-alert">
<div class="hints-icon">
<i class="fa fa-exclamation-circle"></i>
</div>
<div class="hints-container">No exercises</div>
</div><!--/html_preserve-->

## Walk {#walk .r4ds-section}

<!--html_preserve--><div class="alert alert-warning hints-alert">
<div class="hints-icon">
<i class="fa fa-exclamation-circle"></i>
</div>
<div class="hints-container">No exercises</div>
</div><!--/html_preserve-->

## Other patterns of for loops {#other-patterns-of-for-loops .r4ds-section}

### Exercise 21.9.1 {.unnumbered .exercise data-number="21.9.1"}

<div class="question">

Implement your own version of `every()` using a for loop.
Compare it with `purrr::every()`.
What does purrr's version do that your version doesn't?

</div>

<div class="answer">


```r
# Use ... to pass arguments to the function
every2 <- function(.x, .p, ...) {
  for (i in .x) {
    if (!.p(i, ...)) {
      # If any is FALSE we know not all of then were TRUE
      return(FALSE)
    }
  }
  # if nothing was FALSE, then it is TRUE
  TRUE
}

every2(1:3, function(x) {
  x > 1
})
#> [1] FALSE
every2(1:3, function(x) {
  x > 0
})
#> [1] TRUE
```

The function `purrr::every()` does fancy things with the predicate function argument `.p`, like taking a logical vector instead of a function, or being able to test part of a string if the elements of `.x` are lists.

</div>

### Exercise 21.9.2 {.unnumbered .exercise data-number="21.9.2"}

<div class="question">

Create an enhanced `col_summary()` that applies a summary function to every numeric column in a data frame.

</div>

<div class="answer">

I will use `map` to apply the function to all the columns, and `keep` to only select numeric columns.

```r
col_sum2 <- function(df, f, ...) {
  map(keep(df, is.numeric), f, ...)
}
```


```r
col_sum2(iris, mean)
#> $Sepal.Length
#> [1] 5.84
#> 
#> $Sepal.Width
#> [1] 3.06
#> 
#> $Petal.Length
#> [1] 3.76
#> 
#> $Petal.Width
#> [1] 1.2
```

</div>

### Exercise 21.9.3 {.unnumbered .exercise data-number="21.9.3"}

<div class="question">

A possible base R equivalent of `col_summary()` is:


```r
col_sum3 <- function(df, f) {
  is_num <- sapply(df, is.numeric)
  df_num <- df[, is_num]
  sapply(df_num, f)
}
```

But it has a number of bugs as illustrated with the following inputs:


```r
df <- tibble(
  x = 1:3,
  y = 3:1,
  z = c("a", "b", "c")
)

# OK
col_sum3(df, mean)
# Has problems: don't always return numeric vector
col_sum3(df[1:2], mean)
col_sum3(df[1], mean)
col_sum3(df[0], mean)
```

What causes these bugs?

</div>

<div class="answer">

The cause of these bugs is the behavior of `sapply()`.
The `sapply()` function does not guarantee the type of vector it returns, and will returns different types of vectors depending on its inputs.
If no columns are selected, instead of returning an empty numeric vector, it returns an empty list.
This causes an error since we can't use a list with `[`.

```r
sapply(df[0], is.numeric)
#> named list()
```

```r
sapply(df[1], is.numeric)
#>   X1 
#> TRUE
```

```r
sapply(df[1:2], is.numeric)
#>    X1    X2 
#>  TRUE FALSE
```
The `sapply()` function tries to be helpful by simplifying the results, but this behavior can be counterproductive.
It is okay to use the `sapply()` function interactively, but avoid programming with it.

</div>

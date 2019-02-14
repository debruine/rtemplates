
# Power Analyses {#power}


## Using the `pwr` package


```r
library(pwr)
```

### T-tests

Use `pwr.t.test()` to calculate sample size, power, or effect size for the given parameters.


```r
t_pwr <- pwr.t.test(
  n = NULL,
  d = 0.2,
  sig.level = 0.025,      # defaults to 0.05
  power = .9,
  type = "paired",    # c("two.sample", "one.sample", "paired")
  alternative = "greater" # c("two.sided", "less", "greater")
)
```

The output looks like this:


```
## 
##      Paired t test power calculation 
## 
##               n = 264.6138
##               d = 0.2
##       sig.level = 0.025
##           power = 0.9
##     alternative = greater
## 
## NOTE: n is number of *pairs*
```

The variable that we created called `t_pwr` is a list of items that you can access with the following code:

| variable            | value                 |
|:--------------------|----------------------:|
| `t_pwr$n`           | 264.6137766           |
| `t_pwr$d`           | 0.2           |
| `t_pwr$sig.level`   | 0.025   |
| `t_pwr$power`       | 0.9       |
| `t_pwr$alternative` | greater |
| `t_pwr$method`      | Paired t test power calculation      |

These numbers need a little formatting before we can use them in text. You can write up this power analysis using the following code.

<div class='verbatim'><code>&#96;&#96;&#96;{r, include=FALSE}</code>

```r
n <- ceiling(t_pwr$n)
power <- round(t_pwr$power*100, 0)
d <- formatC(t_pwr$d, format = "f", digits = 2)
alpha <- formatC(t_pwr$sig.level, format = "f", digits = 3)
sided <- ifelse(t_pwr$alternative == "two.sample", 2, 1)
method <- t_pwr$method %>% 
  gsub(" power calculation", "", .) %>%
  gsub("t test", "t-test", .) %>%
  str_to_lower()
```

<code>&#96;&#96;&#96;</code></div>

<pre>
A power analysis using the R package &#96;pwr&#96; [\@R-pwr] indicated that &#96;r n&#96; participants would be required to have &#96;r power&#96;% power to detect an effect with Cohen's d at least as large as &#96;r d&#96; with an alpha of &#96;r alpha&#96; using a &#96;r sided&#96;-sided &#96;r method&#96;.
</pre>

Which gives the following output:

> A power analysis using the R package `pwr` [@R-pwr] indicated that 265 participants would be required to have 90% power to detect an effect with Cohen's d at least as large as 0.20 with an alpha of 0.025 using a 1-sided paired t-test.


<div class="info">
<p>The function <code>formatC</code> is like <code>round()</code>, but makes it easier to keep trailing zeros.</p>
</div>

### General t-power function

We can write a function that does all this.


```r
pwr_t_test_output <- function(t_pwr) {
  n <- ceiling(t_pwr$n)
  power <- round(t_pwr$power*100, 0)
  d <- formatC(t_pwr$d, format = "f", digits = 2)
  alpha <- formatC(t_pwr$sig.level, format = "f", digits = 3)
  sided <- ifelse(t_pwr$alternative == "two.sample", 2, 1)
  method <- t_pwr$method %>% 
    gsub(" power calculation", "", .) %>%
    gsub("t test", "t-test", .) %>%
    str_to_lower()
  
  paste0("A power analysis using the R package `pwr` [@R-pwr] indicated that ", 
        n, " participants would be required to have ",
        power, "% power to detect an effect with Cohen's d at least as large as ",
        d, " with an alpha of ", alpha, " using a ", sided, "-sided ", method, ".")

}
```

Now you can just include this function at the top of your document and get a power analysis sentence using `pwr_t_test_output(t_pwr)`. Make sure to set your r chunk to `include = FALSE` if you don't want to show the code chunk.


```r
t_pwr <- pwr.t.test(
  n = 100,
  d = NULL,
  sig.level = 0.05,
  power = .8,
  type = "two.sample",
  alternative = "two.sided"
)
```

> A power analysis using the R package `pwr` [@R-pwr] indicated that 100 participants would be required to have 80% power to detect an effect with Cohen's d at least as large as 0.40 with an alpha of 0.050 using a 1-sided two-sample t-test.


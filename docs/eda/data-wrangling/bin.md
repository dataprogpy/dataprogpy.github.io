```python
import polars as pl

df = pl.DataFrame({"a": [1, 2, 3], "b": [4, 5, 6]})

c_expr = (pl.col("a") + pl.col("b")).alias("c")
d_expr = (c_expr - pl.col("b")).alias("d")

df = df.with_columns(c_expr, d_expr)

print(df)


df = pl.DataFrame(
    {  # As of 14th October 2024, ~3pm UTC
        "ticker": ["AAPL", "NVDA", "MSFT", "GOOG", "AMZN"],
        "company_name": ["Apple", "NVIDIA", "Microsoft", "Alphabet (Google)", "Amazon"],
        "price": [229.9, 138.93, 420.56, 166.41, 188.4],
        "day_high": [231.31, 139.6, 424.04, 167.62, 189.83],
        "day_low": [228.6, 136.3, 417.52, 164.78, 188.44],
        "year_high": [237.23, 140.76, 468.35, 193.31, 201.2],
        "year_low": [164.08, 39.23, 324.39, 121.46, 118.35],
    }
)

print(df)

eur_usd_rate = 1.09  # As of 14th October 2024
# expansion pattern
result = df.with_columns(
    (
        pl.col(
            "price",
            "day_high",
            "day_low",
            "year_high",
            "year_low",
        )
        / eur_usd_rate
    ).round(2)
)
print(result)
```

## Arguments cannot be of mixed types
```python

try:
    df.select(pl.col("ticker", pl.Float64))
except TypeError as err:
    print("TypeError:", err)
```

## Renaming columns
```python


from polars.exceptions import DuplicateError

gbp_usd_rate = 1.31  # As of 14th October 2024

try:
    df.select(
        pl.col("price") / gbp_usd_rate,  # This would be named "price"...
        pl.col("price") / eur_usd_rate,  # And so would this.
    )
except DuplicateError as err:
    print("DuplicateError:", err)

result = df.select(
    (pl.col("price") / gbp_usd_rate).alias("price (GBP)"),
    (pl.col("price") / eur_usd_rate).alias("price (EUR)"),
)
```

```python
result = df.select(pl.all())
print(result.equals(df))

result = df.select(pl.all().exclude("^day_.*$"))
print(result)

# ---
result = df.select(
    (pl.col("^year_.*$") / eur_usd_rate).name.prefix("in_eur_"),
    (pl.col("day_high", "day_low") / gbp_usd_rate).name.suffix("_gbp"),
)
print(result)


# There is also `.name.to_uppercase`, so this usage of `.map` is moot.
result = df.select(pl.all().name.map(str.upper))
print(result)
# ---
def amplitude_expressions(time_periods):
    for tp in time_periods:
        yield (pl.col(f"{tp}_high") - pl.col(f"{tp}_low")).alias(f"{tp}_amplitude")


result = df.with_columns(amplitude_expressions(["day", "year"]))
print(result)
```

# selectors
```python
import polars.selectors as cs

result = df.select(cs.string() | cs.ends_with("_high"))
print(result)
```

### Combining selectors with set operations

We can combine multiple selectors using set operations and the usual Python operators:

<!-- dprint-ignore-start -->
<!-- dprint doesn't understand `A | B` and thinks the | is a column separator. -->
| Operator                | Operation            |
| ----------------------- | -------------------- |
| `A | B`                 | Union                |
| `A & B`                 | Intersection         |
| `A - B`                 | Difference           |
| `A ^ B`                 | Symmetric difference |
| `~A`                    | Complement           |
<!-- dprint-ignore-end -->

The next example matches all non-string columns that contain an underscore in the name:


```python 
result = df.select(cs.contains("_") - cs.string())
print(result)
```

```py
result = df.select(
    pl.col("integers").cast(pl.Float32).alias("integers_as_floats"),
    pl.col("floats").cast(pl.Int32).alias("floats_as_integers"),
)
print(result)
#---
print(f"Before downcasting: {df.estimated_size()} bytes")
result = df.with_columns(
    pl.col("integers").cast(pl.Int16),
    pl.col("floats").cast(pl.Float32),
)
print(f"After downcasting: {result.estimated_size()} bytes")

from polars.exceptions import InvalidOperationError

try:
    result = df.select(pl.col("big_integers").cast(pl.Int8))
    print(result)
except InvalidOperationError as err:
    print(err)

df = pl.DataFrame(
    {
        "integers_as_strings": ["1", "2", "3"],
        "floats_as_strings": ["4.0", "5.8", "-6.3"],
        "floats": [4.0, 5.8, -6.3],
    }
)

result = df.select(
    pl.col("integers_as_strings").cast(pl.Int32),
    pl.col("floats_as_strings").cast(pl.Float64),
    pl.col("floats").cast(pl.String),
)
print(result)

df = pl.DataFrame(
    {
        "integers": [-1, 0, 2, 3, 4],
        "floats": [0.0, 1.0, 2.0, 3.0, 4.0],
        "bools": [True, False, True, False, True],
    }
)

result = df.select(
    pl.col("integers").cast(pl.Boolean),
    pl.col("floats").cast(pl.Boolean),
    pl.col("bools").cast(pl.Int8),
)
print(result)

```

### working with categorical data
enums vs categorical data
workflow
### working with missing data


```py
null_count_df = df.null_count()
print(null_count_df)

is_null_series = df.select(
    pl.col("value").is_null(),
)
print(is_null_series)
```

**Filling missing data**
Missing data in a series can be filled with the function fill_null. You can specify how missing data is effectively filled in a couple of different ways:

- a literal of the correct data type;
- a Polars expression, such as replacing with values computed from another column;
- a strategy based on neighbouring values, such as filling forwards or backwards; and
interpolation.
To illustrate how each of these methods work we start by defining a simple dataframe with two missing values in the second column:
```py
df = pl.DataFrame(
    {
        "col1": [0.5, 1, 1.5, 2, 2.5],
        "col2": [1, None, 3, None, 5],
    },
)
print(df)

fill_literal_df = df.with_columns(
    pl.col("col2").fill_null(3),
)
print(fill_literal_df)

fill_expression_df = df.with_columns(
    pl.col("col2").fill_null((2 * pl.col("col1")).cast(pl.Int64)),
)
print(fill_expression_df)

fill_forward_df = df.with_columns(
    pl.col("col2").fill_null(strategy="forward").alias("forward"),
    pl.col("col2").fill_null(strategy="backward").alias("backward"),
)
print(fill_forward_df)

```
workflow

### working with NaNs

**Not a Number, or NaN values**
Missing data in a series is only ever represented by the value null, regardless of the data type of the series. Columns with a floating point data type can sometimes have the value NaN, which might be confused with null.

The special value NaN can be created directly:
```py
import numpy as np

nan_df = pl.DataFrame(
    {
        "value": [1.0, np.nan, float("nan"), 3.0],
    },
)
print(nan_df)

df = pl.DataFrame(
    {
        "dividend": [1, 0, -1],
        "divisor": [1, 0, -1],
    }
)
result = df.select(pl.col("dividend") / pl.col("divisor"))
print(result)

```
`NaN` values are considered to be a type of floating point data and are **not considered to be
missing data** in Polars. This means:

- `NaN` values are **not** counted with the function `null_count`; and
- `NaN` values are filled when you use the specialised function `fill_nan` method but are **not**
  filled with the function `fill_null`.

One further difference between the values `null` and `NaN` is that numerical aggregating functions,
like `mean` and `sum`, skip the missing values when computing the result, whereas the value `NaN` is
considered for the computation and typically propagates into the result. If desirable, this behavior
can be avoided by replacing the occurrences of the value `NaN` with the value `null`:

```py
mean_nan_df = nan_df.with_columns(
    pl.col("value").fill_nan(None).alias("replaced"),
).select(
    pl.all().mean().name.suffix("_mean"),
    pl.all().sum().name.suffix("_sum"),
)
print(mean_nan_df)
```

### working with aggregation
```py
import polars as pl

url = "hf://datasets/nameexhaustion/polars-docs/legislators-historical.csv"

schema_overrides = {
    "first_name": pl.Categorical,
    "gender": pl.Categorical,
    "type": pl.Categorical,
    "state": pl.Categorical,
    "party": pl.Categorical,
}

dataset = (
    pl.read_csv(url, schema_overrides=schema_overrides)
    .with_columns(pl.col("first", "middle", "last").name.suffix("_name"))
    .with_columns(pl.col("birthday").str.to_date(strict=False))
)

q = (
    dataset.lazy()
    .group_by("first_name")
    .agg(
        pl.len(),
        pl.col("gender"),
        pl.first("last_name"),  # Short for `pl.col("last_name").first()`
    )
    .sort("len", descending=True)
    .limit(5)
)

df = q.collect()
print(df)

q = (
    df1.lazy()
    .group_by("state")
    .agg(
        (pl.col("party") == "Anti-Administration").sum().alias("anti"),
        (pl.col("party") == "Pro-Administration").sum().alias("pro"),
    )
    .sort("pro", descending=True)
    .limit(5)
)

df = q.collect()
print(df)

df.select(
    pl.col("party").unique()
)


from datetime import date


def compute_age():
    return date.today().year - pl.col("birthday").dt.year()


def avg_birthday(gender: str) -> pl.Expr:
    return (
        compute_age()
        .filter(pl.col("gender") == gender)
        .mean()
        .alias(f"avg {gender} birthday")
    )


q = (
    dataset.lazy()
    .group_by("state")
    .agg(
        avg_birthday("M"),
        avg_birthday("F"),
        (pl.col("gender") == "M").sum().alias("# male"),
        (pl.col("gender") == "F").sum().alias("# female"),
    )
    .limit(5)
)

df = q.collect()
print(df)

q = (
    dataset.lazy()
    .group_by("state", "party")
    .agg(pl.len().alias("count"))
    .filter(
        (pl.col("party") == "Anti-Administration")
        | (pl.col("party") == "Pro-Administration")
    )
    .sort("count", descending=True)
    .limit(5)
)

df = q.collect()
print(df)

## warn about order of operations: sort first, if you want to use 
## .first and .last methods on a series or dataframe

q = (
    dataset.lazy()
    .sort("birthday", descending=True)
    .group_by("state")
    .agg(
        get_name().first().alias("youngest"),
        get_name().last().alias("oldest"),
        get_name().sort().first().alias("alphabetical_first"),
        pl.col("gender").sort_by(get_name()).first(),
    )
    .sort("state")
    .limit(5)
)

df = q.collect()
print(df)

result = pokemon.select(
    pl.col("Name", "Type 1", "Type 2"),
    pl.col("Speed")
    .rank("dense", descending=True)
    .over("Type 1", "Type 2")
    .alias("Speed rank"),
)

print(result)
```
This shows that, usually, group_by and over produce results of different shapes:

group_by usually produces a resulting dataframe with as many rows as groups used for aggregating; and
over usually produces a dataframe with the same number of rows as the original.
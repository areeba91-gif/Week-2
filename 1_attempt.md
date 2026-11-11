Step 1: First Look
Code: df.shape, df.dtypes, df.isna().sum()

Shape: 97 rows and 6 columns. It should be 96 (4 items x 2 cities x 12 dates), so 1 row too many.
Price is read as text, not numbers.
Date is read as text, not dates.
Price has 1 missing value.

Step 2: Problems Found

Problem 1: Duplicate row

Code: df[df.duplicated(keep=False)]
2026-01-08, Staple A, City 1, 99.5 appears twice.

Problem 2: Wrong item name "oil b"

Code: df["item"].value_counts() and df[df["date"] == "2026-02-05"]
Row: 2026-02-05, oil b, City 1, 108.5
Oil B City 1 already exists on that date (318). Staple A City 1 is missing.
108.5 fits Staple A, since City 2 is always 6 more and Staple A City 2 is 114.5.
So this row is Staple A, not Oil B.

Problem 3: Count looks right but is not

Code: df.groupby(["item", "city"]).size()
Staple A shows 24 rows only because the duplicate fills the gap from Problem 2.

Problem 4: Price "1,250"

Code: pd.to_numeric(df["price"], errors="coerce")
Row: 2026-01-15, Staple A, City 2
Has a comma, and 1250 is too high. City 1 is 103, so it should be about 109.

Problem 5: Price 9999

Code: df.describe()
Row: 2026-01-22, Sweetener D, City 1
Looks like a "no data" code. It should be about 157.5.

Problem 6: Blank price

Code: df[df["price"].isna()]
Row: 2026-01-01, Pulse C, City 2

Problem 7: Dates are text

Code: df.dtypes

Not a problem: unit and data_label

Each has one value. data_label says this is teaching data, not real PBS prices.

Step 3: Planned Decisions
Duplicate row: Exclude. It counts the same price twice.
"oil b": Correct to Staple A. Only fixing the capitals would make a second, wrong Oil B row.
Count check: Investigate. Check counts by item and city, not only by item.
"1,250": Flag and set to missing. The true value is unknown, so I will not guess.
9999: Flag and set to missing. It is not a real price.
Blank price: Flag and leave missing. Filling it in would invent data.
Dates: Correct to date type. No values change.
unit and data_label: Retain. The label is the warning that this is not real data.

Step 4: Expected Result
96 rows.
Each item 24 times, each item and city 12 times.
3 missing prices, each with a note in a flag col

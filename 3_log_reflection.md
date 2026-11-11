What I asked AI: "I have a CSV of weekly food prices with 6 columns: date, item, city, price, unit and data_label. There should be 96 rows but there are 97. Problems: one duplicate row, one item name in lower case, one price with a comma, one price of 9999, and one blank price. Write pandas code to clean this file."


AI's cleaning code:

python
df = df.drop_duplicates()                                        # line 1
df["item"] = df["item"].str.strip().str.title()                  # line 2
df["price"] = df["price"].str.replace(",", "").astype(float)     # line 3
df = df[df["price"] < 1000]                                      # line 4
df["price"] = df["price"].fillna(df["price"].mean())             # line 5
df["date"] = pd.to_datetime(df["date"])                          # line 6
df = df.drop(columns=["unit", "data_label"])                     # line 7
I ran it on a copy, one line at a time, and checked the rows after each line.

Cleaning Code Court:

Line 1: drop_duplicates

KEEP.
Rows went from 97 to 96. Only the duplicate was removed.

Line 2: strip and title

MODIFY.
It changed "oil b" to "Oil B", but the row is really Staple A.
After this line, Oil B City 1 had 13 rows and Staple A City 1 had 11.
I changed that one row to Staple A instead.

Line 3: remove comma, change to number

MODIFY.
It works, but it turns "1,250" into 1250 and treats it as a real price.
I kept the conversion, then set 1250 and 9999 to missing.

Line 4: keep price below 1000

REJECT.
Rows went from 96 to 93. It deleted the 1250 row, the 9999 row, and also the blank price row without saying so.
Deleted rows leave hidden gaps. I kept the rows and flagged them instead.

Line 5: fill missing with the mean

REJECT.
It would fill the Pulse C gap with 315.46, the average of all items. Pulse C is around 200 to 213.
That is a made-up number.

Line 6: change date to date type

KEEP.
Only the type changed. No rows or prices changed.

Line 7: drop unit and data_label

REJECT.
data_label is the warning that this is teaching data, not real PBS prices. It must stay.

Before and after:

Rows: 97 before, 93 with AI code, 96 with my cleaning.
Item names: 5 before, 4 after.
Missing prices: 1 before, 0 with AI code (rows deleted), 3 with my cleaning (all flagged).
Max price: 9999 before, 350 after.
Mean price: 315.46 before, 203.61 after.
describe() std: 1007.56 before, 79.92 after.
Each item appears 24 times after my cleaning. With the AI code: Oil B 25, Pulse C 23, Sweetener D 23, Staple A 22.
Only 2 prices and 1 label were changed, and each one has a note in the flag column.

Cleaning log:

Removed duplicate: 2026-01-08, Staple A, City 1, 99.5.
Corrected item: 2026-02-05, City 1, "oil b" to "Staple A".
Flagged and set to missing: 2026-01-15, Staple A, City 2, "1,250".
Flagged and set to missing: 2026-01-22, Sweetener D, City 1, 9999.
Flagged and left missing: 2026-01-01, Pulse C, City 2, blank.
Changed date and price to the correct types.
Kept unit and data_label.

What I learned:

The AI and I agreed on the duplicate and the dates.
We differed on "oil b", the bad prices, the blank price and data_label.
My decisions were better supported, because I checked each one against the counts by item and city.
The mean was the same (203.61) for the AI's result and mine, so describe() alone could not show the AI's mistakes. The counts by item and city did.

What I would have missed alone:

That "price < 1000" also deletes the blank row.
That filling with the mean mixes all 4 items into one number.

Limit:

The same AI (Claude) wrote, ran and judged the code, so the checks in 2_work.ipynb are what support each verdict.

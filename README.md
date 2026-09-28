# FTEC5660 Homework 1: Receipt Chain

Build a LangChain pipeline that reads every supermarket receipt in a folder
with the vision-capable DeepSeek Flash model and answers these two questions:

1. How much money did I spend in total for these bills?
2. How much would I have had to pay without the discount?

For this homework, **amount spent** means the final payment after the receipt's
rounding line. **Without the discount** means the sum of the original positive
item prices: add back every promotion, coupon, member, app, packaging-damage,
and percentage discount, but do not add back rounding.

## Student task

Only edit the two functions in `hw1.py` that contain `### YOUR CODE HERE`:

- `build_chain()` creates your LangChain chain.
- `answer_queries()` runs the chain on the receipt images and returns one final
  response for each question.

You may use prompt chaining, routing, parallel calls, reflection, or a
combination. Your final responses should each contain one HKD amount. Do not
hard-code filenames or public answers; grading uses unseen receipt folders.

## Setup and public test

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

Put your DeepSeek key after `DEEPSEEK_API_KEY=` in `.env`, then run:

```bash
python3 hw1.py --image-folder public_test
```

The program creates `results.csv` in the current directory. Its columns are
`query`, `model_response`, and `correctness`. The public answers are in
`public_test/ground_truth.json`. The starter intentionally returns the dummy
response `please design your chain to answer these two queries.` so it runs
before you add any API code.

The required model is `deepseek-v4-flash-vision-exp`, the vision-capable
DeepSeek Flash model. JPEG, PNG, GIF, and WebP inputs are accepted by the
homework runner.


## Homework 1 solution: 
> to students: please fill your solution description here.

### Approach

I implemented a LangChain-based receipt extraction pipeline using the
DeepSeek vision model `deepseek-v4-flash-vision-exp`.

The solution contains two main parts:


1. `build_chain()`

- Creates a LangChain chain with a vision-capable DeepSeek model.
- Uses a structured prompt to instruct the model to extract:
  - `paid_amount`: the final amount actually paid after rounding.
  - `original_amount`: the total amount before discounts.

The prompt also guides the model to consider all discount information,
including promotions, coupons, member discounts, app discounts, and
percentage discounts.

2. `answer_queries()`

- Reads all receipt images from the given folder.
- Converts images into a format accepted by the vision model.
- Sends each receipt image through the LangChain chain.
- Parses the returned JSON response.
- Aggregates all receipt values to answer the two required questions.

### Handling improvements

During testing, some long receipt images occasionally produced empty
responses. To improve robustness, I added retry handling and adjusted the
model generation settings.

I also optimized image processing to make long receipt images easier for
the vision model to analyze.

### Testing result

The solution was tested with the provided public test receipts.

Final output:
How much money did I spend in total for these bills?
HK$1974.30
How much would I have had to pay without the discount?
HK$2348.20
Both answers matched the expected results.



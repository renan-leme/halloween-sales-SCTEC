# Halloween Orders — Festa em Cena

A step-by-step Jupyter Notebook for the Module 1, Week 07 mini-project in Machine Learning and Computer Vision, Class 05.

## Problem and scope

Festa em Cena is a fictional retailer selling Halloween costumes and accessories through partner supermarkets (`Mercado`), its own stores (`Loja física`), and its website (`Web`). Its order records contain missing categories, inconsistent text, invalid quantities, missing prices, and invalid sale dates.

The objective is to identify and document these problems, investigate delivery dates and cancellation, and count registered orders by channel and year-month. The dataset contains **1,500 distinct orders**, covering August–November in 2024 and 2025. **One row represents one order for one product, possibly with multiple units.** An order count is not a count of units, revenue, or completed sales. All statuses are included in the exploratory counts.

## Files

| File | Purpose |
|---|---|
| `halloween_sales.ipynb` | Teaching notebook with explanations, functions, examples, and executed outputs |
| `halloween_sales.csv` | Notebook input, with English headers and unchanged source values |
| `halloween_vendas.csv` | Preserved course reference with Portuguese headers; not read by the notebook |
| `README.md` | Execution guide, decisions, results, and evaluation checklist |
| `report.md` | Required findings report, within 1,500–3,000 characters including spaces |
| `requirements.txt` | Python packages needed to open and execute the notebook |
| `.gitignore` | Excludes the virtual environment, temporary files, notebook checkpoints, and local course PDF |

The source data is supplied by the [course repository](https://github.com/Machine-Learning-Visao-Computacional-T5/python_exercises/blob/main/data_analysis/arquivo_mini_projeto/halloween_vendas.csv). Keep **`halloween_sales.csv`** in the same folder as the notebook. This is the only CSV used by the notebook. Its headers have been translated into English; the data values are preserved. The notebook does not overwrite the input or export a falsely corrected dataset. The Portuguese-header reference is retained for provenance and is not needed to execute the analysis.

## Installation and execution

Use **Python 3.10 or 3.11**. The project was executed with Python 3.10.11. Open a terminal in this project folder.

On Windows PowerShell:

```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe -m notebook halloween_sales.ipynb
```

If `.venv` already exists, skip the first command. Calling its Python executable directly also avoids requiring environment activation.

On Linux or macOS:

```bash
python3 -m venv .venv
./.venv/bin/python -m pip install -r requirements.txt
./.venv/bin/python -m notebook halloween_sales.ipynb
```

In Jupyter, select the Python kernel belonging to this environment. Run cells from top to bottom with **Shift+Enter**. Each Markdown cell explains the following code and the reasoning behind it. For a complete verification, select **Restart Kernel and Run All Cells**. The saved notebook already contains outputs, but rerun it to study the process and prepare the demonstration.

Pandas handles tabular data, Notebook provides the interface, ipykernel runs Python cells, and nbclient supports reproducible notebook execution. `re`, `datetime`, `decimal`, and `pathlib` belong to Python's standard library and need no separate installation.

## English column names and field meanings

All input columns, working columns, and code identifiers are English. `halloween_sales.csv` already has the translated headers below, so the notebook reads them directly. The mapping table documents the original course headers. Text values remain as recorded, apart from the required category and channel normalization in the working DataFrame.

| Source header | English header | Meaning |
|---|---|---|
| `pedido_id` | `order_id` | Unique order identifier; not a measurement |
| `data_venda` | `sale_date` | Original local sale timestamp, before parsing |
| `produto` | `product` | Product associated with the order |
| `categoria` | `category` | Product category, subject to text cleaning |
| `canal_venda` | `sales_channel` | Channel through which the order was recorded |
| `cidade` | `city` | Recorded city |
| `quantidade` | `quantity` | Number of units in this order; must be a positive integer |
| `preco_unitario` | `unit_price` | Price per unit; must be present and nonnegative |
| `custo_unitario` | `unit_cost` | Cost per unit; not used in this analysis |
| `desconto_pct` | `discount_pct` | Recorded percentage discount; not used in this analysis |
| `status_pedido` | `order_status` | Recorded status, such as `Concluído`, `Cancelado`, or `Devolvido` |
| `publico` | `target_audience` | Intended product audience |
| `tamanho` | `size` | Recorded product size; not a physical-dimensions analysis |
| `data_entrega` | `delivery_date` | Recorded delivery timestamp; may be blank |

## Processing rules and decisions

1. **Read and preserve:** use `pd.read_csv()` with `sep=","`, `encoding="utf-8"`, and `dtype="string"`. Preserve empty strings with `keep_default_na=False`, then identify nulls and whitespace-only values explicitly with `is_blank`. Keep `raw_df` separate from the working `orders` DataFrame.
2. **Categories:** apply `.strip()`, `.lower()`, and `re.sub()` to remove punctuation while retaining letters and meaningful spaces. The provided categories have no accents. Collapse repeated whitespace. Replace originally blank or empty-after-cleaning categories with the exact required label `sem categoria`. Count originally blank fills separately from changed nonblank values; a lowercase-only correction also counts as a change.
3. **Channels:** map trimmed, lowercase variants to `Mercado`, `Loja física`, and `Web`. Unknown labels are flagged and excluded only from channel counts. Preserve their original text in `raw_df`.
4. **Quantity and price:** accept finite quantities greater than zero whose numeric value is integral; accept finite, present prices greater than or equal to zero. Exact values such as `2.0` qualify as whole quantities; `2.5` does not. `Decimal` avoids rounding before validation. Add validated fields and error flags; retain the source text. Do not impute, drop orders, or replace errors with zero. Use a Boolean union to count orders affected by either numeric problem once.
5. **Delivery hypothesis:** split orders with blank delivery dates into cancelled and other-status groups. Use normalized text only for the status comparison; never change recorded status or invent a delivery date. One counterexample makes the hypothesis false in this dataset.
6. **Dates:** use `datetime.strptime(value, "%Y-%m-%d %H:%M:%S")`, catch parsing errors, and retain the original `sale_date`. Extract year, month, day, and hour. Use `strftime("%d/%m/%Y")` for Brazilian display and `%Y-%m` for period grouping. Invalid or blank dates remain missing and flagged; never use today's date. Timestamps stay in their stated local São Paulo interpretation, without time-zone conversion.
7. **Counts:** include all statuses. Channel counts require only a recognized channel; period counts require only a valid sale date. Numeric issues do not exclude orders from either count. Report each count's exclusions separately and group by year-month, never month alone.

## Results

### Data quality and delivery

| Measure | Count |
|---|---:|
| Rows read / distinct order IDs | 1,500 / 1,500 |
| Originally blank categories filled | 18 |
| Nonblank categories standardized | 1,482 |
| Channel values standardized | 45 |
| Unrecognized channels | 0 |
| Invalid quantities | 7 |
| Invalid or missing unit prices | 12 |
| Orders affected by either numeric problem | 19 |
| Orders affected by both numeric problems | 0 |
| Invalid or blank sale dates | 4 |
| Orders with numeric, date, or channel problems, counted once | 23 |
| Total cancelled orders | 109 |
| No delivery date and cancelled | 109 |
| No delivery date and another status | 34 |

**Delivery conclusion:** the hypothesis is false. Orders **1170**, **990**, and **740** have status `Concluído` and no delivery date. There are 143 orders without a delivery date in total. A missing delivery date cannot be treated as evidence of cancellation.

The 1,482 category changes include clean-looking source labels such as `Fantasia`, because the required lowercase representation is `fantasia`. This is a normalization count, not a count of 1,482 missing or unusable categories. The 23-order validation total concerns numeric, sale-date, and channel flags; category fills and missing-delivery groups are reported separately.

### Orders by channel

| Channel | Orders |
|---|---:|
| Web | 666 |
| Loja física | 551 |
| Mercado | 283 |
| **Total included** | **1,500** |

**Exclusions: 0.** All statuses are included.

**Finding 1:** Web accounts for **666 orders, or 44.40%** of orders with a recognized channel. The website is the most-used ordering channel in this sample. **Limitation:** cancellations and returns are included, so this does not identify the highest-revenue or most profitable channel and cannot describe the real market.

### Orders by year-month

| Year-month | Orders |
|---|---:|
| 2024-08 | 77 |
| 2024-09 | 104 |
| 2024-10 | 393 |
| 2024-11 | 174 |
| 2025-08 | 62 |
| 2025-09 | 94 |
| 2025-10 | 410 |
| 2025-11 | 182 |
| **Total included** | **1,496** |

**Exclusions: 4 invalid sale dates.** All statuses are included.

**Finding 2:** October contains **393 orders in 2024** and **410 in 2025**. Together these **803 orders represent 53.68%** of valid-dated orders. October has the largest count in each observed year, consistent with activity around Halloween. **Limitation:** four dates are unavailable, only August–November is covered, and the data is fictional. This does not establish full-year seasonality, causation, or completed-sale growth.

## Data quality and Machine Learning reflection

Missing prices and invalid quantities can distort numeric features; inconsistent category text can split one category into several labels; invalid dates can distort time-based comparisons. Marking these issues while preserving the source makes analysis decisions traceable. Inventing values without evidence would hide uncertainty.

Cleaning alone does not guarantee that a model avoids **overfitting** or **underfitting**. Data representativeness, suitable features, model complexity, and evaluation on unseen data also matter. Time-dependent orders would require appropriate time-based evaluation and attention to data leakage. No model is trained here: the assignment concerns data preparation and exploratory analysis.

## Organization and version control

The notebook follows the five suggested sprints: ingestion, string transformations, numeric validation, business rules and dates, and reporting. Functions have a specific responsibility and return values that later cells inspect. Parameters make rules reusable; lists, dictionaries, loops, `if` conditions, regular expressions, and exception handling are visible in context.

Branches organize the submission:

- `main`: complete submission.
- `feature/data-analysis`: notebook and English-header CSV.
- `feature/documentation`: README and findings report.

Commit messages describe the setup, analysis, and documentation changes. The connected GitHub app was used for publishing because SSH authentication was unavailable in the execution environment. The local remote retains the SSH repository URL supplied for the project.

## Evaluation checklist

The handout contains documentation and video criteria in addition to the technical criteria. The submission addresses them as follows:

| Criterion | Evidence |
|---|---|
| Version control and documentation | Public repository, meaningful commits, this README, and the ML/data-quality reflection |
| Video | Record and submit the required demonstration |
| CSV manipulation | Notebook sprint 1: `pd.read_csv()`, text input, original preserved, first three rows |
| Missing values and conditionals | Sprints 2–3: explicit blank handling, category placeholder, quantity/price rules and flags |
| Business rules and dates | Sprint 4: delivery groups, counterexamples, `strptime`, date parts, and `strftime` |
| String cleaning | Sprint 2: `.strip()`, `.lower()`, `re.sub()`, channel mapping, before/after examples |
| `report.md` | English findings report; final notebook cell checks the exact 1,500–3,000 character limit |

The rubric refers to physical dimensions and an approval date, although the detailed task specifies quantity, unit price, and `data_venda`. This solution follows the detailed requirements, uses the specified lowercase placeholder `sem categoria`, and includes the ML reflection explicitly required by the rubric.

## Technical references

- [Pandas `read_csv`](https://pandas.pydata.org/pandas-docs/version/2.3/reference/api/pandas.read_csv.html)
- [Python `datetime`, `strptime`, and `strftime`](https://docs.python.org/3.10/library/datetime.html)
- [Python regular expressions](https://docs.python.org/3.10/library/re.html)
- [Notebook execution with nbclient](https://nbclient.readthedocs.io/en/latest/client.html)

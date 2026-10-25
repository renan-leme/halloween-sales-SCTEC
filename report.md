# Halloween Orders: Findings and Insights

The analysis covers 1,500 distinct orders from the fictional retailer Festa em Cena, recorded from August to November in 2024 and 2025. Each row represents one order for one product, possibly with several units. Counts describe registered orders across all statuses, not revenue, units sold, or completed sales.

The notebook uses Pandas to read the UTF-8 CSV as text and translates all 14 column names into English. The original CSV is preserved; a separate copy has English headers. Category cleaning fills 18 originally blank values with the required label "sem categoria" and changes 1,482 nonblank values through trimming, lowercasing, and punctuation removal. Sales channels are standardized to Mercado, Loja física, and Web; 45 channel values change and all channels are recognized.

Numeric validation flags 7 invalid quantities and 12 missing unit prices, affecting 19 different orders. Quantities must be positive integers; prices must be present and nonnegative. Four sale timestamps are invalid. Numeric and date problems together affect 23 distinct orders. Original values remain visible, errors are marked, and no replacement values are invented.

There are 109 cancelled orders. Of the orders without a delivery date, 109 are cancelled and 34 have another status. Orders 1170, 990, and 740 are counterexamples: their recorded status is Concluído, yet their delivery date is blank. Therefore, the hypothesis that every order without a delivery date is cancelled is false in this dataset. These observations do not justify changing statuses or inventing delivery dates.

By channel, Web leads with 666 orders (44.40%), followed by Loja física with 551 and Mercado with 283. No orders are excluded from this count. The website is the most-used ordering channel in this sample, but the count includes cancellations and returns and does not establish the most profitable channel.

By period, October contains 393 orders in 2024 and 410 in 2025. Together, these 803 orders represent 53.68% of the 1,496 orders with valid sale dates; four orders are excluded only from the period count. This concentration is consistent with activity around Halloween, but the limited August-November coverage and fictional data cannot establish full-year seasonality or causation.

Data cleaning improves consistency and makes errors explicit. It does not alone prevent overfitting or underfitting: representativeness, model choice, and evaluation on unseen data also matter. No model is trained in this project.

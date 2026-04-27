<img width="1104" height="902" alt="Снимок экрана от 2026-04-27 19-06-10" src="https://github.com/user-attachments/assets/24add589-f8a8-43e7-814d-4b189a489811" />
<img width="1104" height="902" alt="Снимок экрана от 2026-04-27 19-07-23" src="https://github.com/user-attachments/assets/ca5b6119-c15e-4ea5-959a-c638571e6c60" />



Индикатор среднемесячных трат
`SELECT`
  `AVG("__mb_source"."sum") AS "avg"`
`FROM`
  `(`
    `SELECT`
      `DATE_TRUNC('month', "__mb_source"."processing_date") AS "processing_date",`
      `SUM("__mb_source"."positive_sum") AS "sum"`
    `FROM`
      `(`
        `SELECT`
          `"public"."payments_final_20260426151234"."processing_date" AS "processing_date",`
          `"public"."payments_final_20260426151234"."positive_sum" AS "positive_sum"`
        `FROM`
          `"public"."payments_final_20260426151234"`
      `) AS "__mb_source"`
    `GROUP BY`
      `DATE_TRUNC('month', "__mb_source"."processing_date")`
    `ORDER BY`
      `DATE_TRUNC('month', "__mb_source"."processing_date") ASC`
  `) AS "__mb_source"`

  Распределение трат по категориям
  `SELECT`
  `"__mb_source"."category" AS "category",`
  `SUM("__mb_source"."positive_sum") AS "sum"`
`FROM`
  `(`
    `SELECT`
      `"public"."payments_final_20260426151234"."category" AS "category",`
      `"public"."payments_final_20260426151234"."positive_sum" AS "positive_sum"`
    `FROM`
      `"public"."payments_final_20260426151234"`
  `) AS "__mb_source"`
`GROUP BY`
  `"__mb_source"."category"`
`ORDER BY`
  `"__mb_source"."category" ASC`

Расход/приход
`SELECT`
  `DATE_TRUNC('month', "__mb_source"."processing_date") AS "processing_date",`
  `SUM(`
    `CASE`
      `WHEN "__mb_source"."sum" > 0 THEN "__mb_source"."sum"`
      `ELSE 0.0`
    `END`
  `) AS "sum_of_incomes",`
  `SUM(`
    `CASE`
      `WHEN "__mb_source"."sum" < 0 THEN "__mb_source"."sum"`
      `ELSE 0.0`
    `END`
  `) AS "sum_of_wastes"`
`FROM`
  `(`
    `SELECT`
      `"public"."payments_final_20260426151234"."processing_date" AS "processing_date",`
      `"public"."payments_final_20260426151234"."sum" AS "sum"`
	  
    `FROM`
      `"public"."payments_final_20260426151234"`
  `) AS "__mb_source"`
`GROUP BY`
  `DATE_TRUNC('month', "__mb_source"."processing_date")`
`ORDER BY`
  `DATE_TRUNC('month', "__mb_source"."processing_date") ASC`

Суммарные траты по категориям и месяцам
`SELECT`
  `"__mb_source"."month" AS "month",`
  `"__mb_source"."category" AS "category",`
  `"__mb_source"."positive_sum" AS "positive_sum"`
`FROM`
  `(`
    `SELECT`
      `"public"."payments_final_20260426151234"."month" AS "month",`
      `"public"."payments_final_20260426151234"."category" AS "category",`
      `"public"."payments_final_20260426151234"."positive_sum" AS "positive_sum"`
    `FROM`
      `"public"."payments_final_20260426151234"`
  `) AS "__mb_source"`
`LIMIT`
  `1048575`
Кумулятивная сумма 
`SELECT`
  `"__mb_source"."processing_date" AS "processing_date",`
  `SUM("__mb_source"."sum") AS "sum",`
  `SUM(SUM("__mb_source"."sum")) OVER (`
    `ORDER BY`
      `"__mb_source"."processing_date" ASC ROWS UNBOUNDED PRECEDING`
  `) AS "sum_2"`
`FROM`
  `(`
    `SELECT`
      `DATE_TRUNC('month', "__mb_source"."processing_date") AS "processing_date",`
      `"__mb_source"."sum" AS "sum"`
    `FROM`
      `(`
        `SELECT`
          `"public"."payments_final_20260426151234"."processing_date" AS "processing_date",`
          `"public"."payments_final_20260426151234"."sum" AS "sum"`
        `FROM`
          `"public"."payments_final_20260426151234"`
      `) AS "__mb_source"`
  `) AS "__mb_source"`
`GROUP BY`
  `"__mb_source"."processing_date"`
`ORDER BY`
  `"__mb_source"."processing_date" ASC`

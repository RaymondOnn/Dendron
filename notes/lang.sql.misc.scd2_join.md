---
id: m08w4nwd5icup3zss7h8atk
title: Scd2_join
desc: ''
updated: 1733066051843
created: 1733039394271
---
<https://infinitelambda.com/multitable-scd2-joins/>

#### Things to consider

##### Inbound and out-of-bound intervals

The validity intervals of these tables are LEFT CLOSED - RIGHT OPENED, which means valid_to is out of bound (in this case we set it as '9999-12-31') for current records. On the other hand, valid_from is inbound and the earliest value in the 3 tables is ‘2022-12-31 0:00:00’ (Table 2).

In one of the approaches to multitable SCD2 joins, a LEFT CLOSED - RIGHT OPENED interval might cause missing rows in the final merged table. To prevent this, LEFT OPENED - RIGHT OPENED validity intervals (valid_from '1900-01-01') should be used instead. You will see an example of this in the Pros & Cons of Direct Join section further in this article.

##### Continuous & discontinuous intervals

- The validity periods in Table 1 and Table 2 are continuous i.e. `valid_from` of the next record is equal to the `valid_to` of the previous one and there are no gaps.
- On the other hand, there is a gap in Table 3 with ‘dim3-b’ (highlighted above).

- **Gaps in your source tables will result in NULLs for the corresponding dimensions in the final merged SCD2.**
- The existence of an interval gap usually indicates missing data:
  - either the SCD2 was not loaded correctly
  - the raw sources did not have the corresponding data for that period.
- Unless it is intended to be so, it is recommended to check your loading process and raw data before proceeding with the next steps.

##### Recalculating `valid_to` based on `valid_from`

- If:
  - Your source tables only have `valid_from` column and don’t have `valid_to`;
  - There are gaps in your validity intervals, which are supposed to be continuous, you can always calculate or recalculate valid_to based on valid_from.

See the example below on Table3:

``` sql
create or replace table scd2_table3_continuous_intervals as (
    select
        pk,
        dim3,
        valid_from,
        coalesce(lead(valid_from) over(partition by pk order by valid_from), '9999-12-31'::timestamp) as valid_to,
        valid_to = '9999-12-31'::timestamp as is_current
    from scd2_table3
);
```

### First approach to multitable SCD2 joins: Direct Join

- Direct Join is a common practice when it comes to joining 02 SCD2 tables and many how-to articles refer to it.
- This method involves 3 steps: timeline join condition, validity period recalculation, and deduplication.
- As the name suggests, we will be joining the validity range directly from the source SCD2 tables without any scaffolding or timeline modification.

In this section, we will apply this method to 2 cases: joining 02 SCD2 tables and joining 03 or more SCD2 tables using the samples above.

#### Direct Join with 02 SCD2 tables

1. Perform this join condition that enables merging 2 rows that are in the same validity period

    ``` sql
    from scd2_table1 t1
    join scd2_table2 t2
    on t1.pk = t2.pk
    and t1.valid_from < t2.valid_to
    and t1.valid_to > t2.valid_from
    ```

2. Recalculate valid_from & valid_to

- `valid_from` should be the latest one of the 2 `valid_from` of the 2 tables
- `valid_to` should be the earliest one of the 2 valid_to of the 2 tables

    ``` sql
    greatest(t1.valid_from, coalesce(t2.valid_from, '1900-01-01'::timestamp)) as valid_from,
    coalesce(lead(greatest(t1.valid_from, coalesce(t2.valid_from, '1900-01-01'::timestamp)))
        over (partition by t1.pk
        order by greatest(t1.valid_from, coalesce(t2.valid_from, '1900-01-01'::timestamp))),
            '9999-12-31'::timestamp) as valid_to
    ```

3. Filter out rows with `valid_from = valid_to` after join to avoid duplicated validity intervals

- In most of real-life examples, `valid_from` and `valid_to` are usually in lower time granularity like timestamp (with the differences down to seconds),
- Hence there might not be cases where these column value “overlapped” across multiple SCD2 tables and we might skip this step. However, for an overall solution, this should be a part of your joining condition.

4. Combine them all together

    ``` sql
    with
        prep1 as (
            select
                t1.pk,
                dim1,
                dim2,
                greatest(
                    t1.valid_from, 
                    coalesce(t2.valid_from, '1900-01-01'::timestamp)
                ) as valid_from,
                coalesce(
                    lead(
                        greatest(
                            t1.valid_from, 
                            coalesce(t2.valid_from, '1900-01-01'::timestamp)
                        )
                    ) over (
                        partition by t1.pk
                        order by greatest(
                            t1.valid_from, 
                            coalesce(t2.valid_from, '1900-01-01'::timestamp)
                        )
                    ),
                    '9999-12-31'::timestamp
                    ) as valid_to
            from scd2_table1 t1
            join scd2_table2 t2
                on t1.pk = t2.pk
                and t1.valid_from < t2.valid_to
                and t1.valid_to > t2.valid_from
        )

    select
        *
    from prep1
    where valid_from != valid_to
    order by PK, valid_from, valid_to, dim1, dim2
    ```

#### Direct Join with 03 or more SCD2 tables

- Repeat the process of joining 02 SCD2 tables as mentioned above, while re-calculating valid_from and valid_to along the way.
- Note that there is only 1 join in each step between the next SCD2 validity range (e.g. Table 3) and the previously combined validity range (e.g. Table 2 + Table 1).
- If we break it down into simple CTE steps, it should look like this:
  1. CTE1 = SCD2-table1 JOIN SCD2-table2;
  2. Recalculate valid_from & valid_to in CTE1;
  3. CTE1 join SCD2-table3;
  4. Repeat.

    ``` sql
    -- Take a look at the combined script:
    with --- Method 1: multiple 2-scd2table-join ---
        prep1 as (
            select
                t1.pk,
                dim1,
                dim2,
                greatest(t1.valid_from, coalesce(t2.valid_from, '1900-01-01'::timestamp)) as valid_from,
                coalesce(lead(greatest(t1.valid_from, coalesce(t2.valid_from, '1900-01-01'::timestamp)))
                    over (partition by t1.pk
                    order by greatest(t1.valid_from, coalesce(t2.valid_from, '1900-01-01'::timestamp))),
                        '9999-12-31'::timestamp) as valid_to
            from scd2_table1 t1
            join scd2_table2 t2
                on t1.pk = t2.pk
                and t1.valid_from < t2.valid_to
                and t1.valid_to > t2.valid_from
        ),
        prep2 as (
            select
                t1.pk,
                t1.dim1,
                t1.dim2,
                t2.dim3,
                greatest(t1.valid_from, coalesce(t2.valid_from, '1900-01-01'::timestamp)) as valid_from,
                coalesce(lead(greatest(t1.valid_from, coalesce(t2.valid_from, '1900-01-01'::timestamp)))
                    over (partition by t1.pk
                    order by greatest(t1.valid_from, coalesce(t2.valid_from, '1900-01-01'::timestamp))),
                        '9999-12-31'::timestamp) as valid_to
            from prep1 as t1
            join scd2_table3 as t2
            -- join scd2_table3_continuous_intervals as t2
            -- full outer join scd2_table3 as t2
            -- full outer join scd2_table3_continuous_intervals as t2
                on t1.pk = t2.pk
                and t1.valid_from < t2.valid_to
                and t1.valid_to > t2.valid_from
        )

    select
        *
    from prep2
    where valid_from != valid_to -- important: dedup
    order by PK, valid_from, valid_to, dim1, dim2, dim3
    ```

#### Direct join cons

Even though Direct Join is a straightforward and frequently used approach to multitable SCD2 joins, it does come with certain limitations. Let’s go over these now.

Missing row for out-of-bound valid_from
The validity interval of 2022-12-31 to 2023-01-01 is not populated. I would expect this row for this period:

PK DIM1 DIM2 DIM3 VALID_FROM
pk NULL dim2-a NULL 2022-12-31 0:00:00

Due to the logic of recalculating valid_to and valid_from, this row will not show up even if you use a full outer join instead of an inner join.

To make this work, all SCD2s should be in a LEFT OPENED - RIGHT OPENED state. In other words, the first period of all tables should have valid_from = '1900-01-01' or the same starting points.

Missing rows due to interval gaps
A gap in Table 3 led to the missing of these 2 rows:

PK DIM1 DIM2 DIM3 VALID_FROM
pk dim1-b dim2-b NULL 2023-01-03 0:00:00
pk dim1-c dim2-c NULL 2023-01-04 0:00:00

To make this work, all SCD2s should be continuous. You can check it out using the script above with the scd2_table3_continuous_intervals we created earlier instead of scd2_table3 (see the commented part of the join).

### Second approach to multitable SCD2 joins: unified timeline

- This approach to multitable SCD2 joins first creates a unified timeline based on all the valid_from from referenced SCD2s.
- This timeline will be used as a scaffold for joining back all the SCD2s later on.
- Unlike Direct Join, the deduplication & valid_to recalculation steps are done during timeline unification.
- This allows us to execute all SCD2 joins in 1 CTE instead.
- Note that the process is quite similar when joining 02, 03 or more SCD2s:
  1. Union PKs, valid_from from subsequent SCD2s;
  2. Deduplicate the unioned table to make the following calculation lighter;
  3. Recalculate valid_to from the unioned data to create the timeline;
  4. Join subsequent SCD2s back to the timeline using this condition below. Repeat for all SCD2s.

``` sql
from unified_timeline_recalculate_valid_to as timeline
left join scd2_table1
    on timeline.pk = scd2_table1.pk
    and scd2_table1.valid_from <= timeline.valid_from
    and scd2_table1.valid_to >= timeline.valid_to
```

##### Unified timeline – combine all steps

``` sql
with
    unified_timeline as ( -- using union to deal with duplicates values instead of union all
        select pk, valid_from from scd2_table1 union
        select pk, valid_from from scd2_table2 union
        select pk, valid_from from scd2_table3
    ),
    unified_timeline_recalculate_valid_to as (
        select
            pk,
            valid_from,
            coalesce(lead(valid_from) over(partition by pk order by valid_from), '9999-12-31'::timestamp) as valid_to,
            valid_to = '9999-12-31'::timestamp as is_current
        from unified_timeline
    ),
    joined as (
        select
            timeline.pk,
            scd2_table1.dim1,
            scd2_table2.dim2,
            scd2_table3.dim3,
            coalesce(timeline.valid_from, '1900-01-01'::timestamp) as valid_from,
            coalesce(timeline.valid_to, '9999-12-31'::timestamp) as valid_to
        from unified_timeline_recalculate_valid_to as timeline
        left join scd2_table1
            on timeline.pk = scd2_table1.pk
            and scd2_table1.valid_from <= timeline.valid_from
            and scd2_table1.valid_to >= timeline.valid_to
        left join scd2_table2
            on timeline.pk = scd2_table2.pk
            and scd2_table2.valid_from <= timeline.valid_from
            and scd2_table2.valid_to >= timeline.valid_to
        left join scd2_table3
            on timeline.pk = scd2_table1.pk
            and scd2_table3.valid_from <= timeline.valid_from
            and scd2_table3.valid_to >= timeline.valid_to

    )
select * from joined
-- where valid_from != valid_to -- As we already have a distinct timeline (using union), this condition is no longer needed
order by PK, valid_from, valid_to, dim1, dim2, dim3
;
```

#### Unified timeline pros

- The Unified Timeline approach covers all of the rows previously missed by the Direct Join approach. It has fewer window functions and is generally shorter in terms of query length when it comes to joining more than 02 tables.
- If you have only 02 SCD2s without any out-of-bound valid_from row, Direct Join seems to be an easier, more straightforward method compared to Unified Timeline.
- There is still some missing data (nulls) due to the mentioned GAP in Table 3 but this is an expected outcome with that raw data input.
- If possible, I would recommend using scd2_table3_continuous_intervals instead.

### Query performance comparison

- The Direct Join approach recalculates `valid_to` in every single CTE, while Unified Timeline 2 only calculates it once after the union section. 
- This process is a window function, hence we can expect it to affect query performance. 
- There is little to no difference if you are joining 02 SCD2 only (only 01 window function).
- Direct Join’s performance declines significantly with 03 SCD2s and up. 
- On a Snowflake x-small warehouse, the above Direct Join query for  03 SCD2s took 1.2s, while the similar one for Unified Timeline finished in only 650ms.

> Conclusion: start doing multitable SCD2 joins effectively

- Unifying SCD2 tables is a crucial step in accessing point-in-time historical data. There are two approaches you can choose from for multitable SCD2 joins: Direct Join and Unified Timeline.

While Direct Join is a widely used and straightforward method, it carries the risk of missing data. On the other hand, the Unified Timeline approach comes in as a more comprehensive solution by addressing the limitations of Direct Join while improving query performance.

Now that you are familiar with these approaches to multitable SCD2 joins and understand how to apply them, you can navigate the landscape of historical data integration effectively to unlock valuable insights.

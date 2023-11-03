---
id: xhy51h6jo6m6fd4ait1kt9f
title: scripting
desc: ''
updated: 1698446479986
created: 1678106405667
---

## Snowflake Scripting
- Add support for procedural logic

<br>

### Snowsight vs the Classic Interface

``` sql
-- This code block can run in the modern SnowSight interface
-- This will not work for the classic interface
declare
    radius_of_circle float;
    area_of_circle float:
begin
    radius_of_circle := 1
    area_of_circle := pi() * radius_of_circle * radius_of_circle
    return area_of_circle
end;

-- In order to run the code in the classic interface, need to wrap the code with the '$$' symbols
$$
    declare
        radius_of_circle float;
        area_of_circle float:
    begin
        radius_of_circle := 1
        area_of_circle := pi() * radius_of_circle * radius_of_circle
        return area_of_circle
    end;
$$;
```

### Anonynous Blocks

```sql
-- Example of an anonynous block
-- Running code without the use of CREATE PROCEDURE
declare
    radius_of_circle float;
    area_of_circle float:
begin
    radius_of_circle := 1
    area_of_circle := pi() * radius_of_circle * radius_of_circle
    return area_of_circle
end;

```

### Using a Stored Procedure Block

``` sql
CREATE OR REPLACE PROCEDURE area()
    RETURNS float
    LANGUAGE SQL
    AS 
    $$
        declare
            radius float;
            area_of_circle float:
        begin
            radius := 1;
            area_of_circle := pi() * radius * radius;
            return area_of_circle;
        end;
    $$;

CALL area()    
```
### Using Parameters

```SQL
CREATE OR REPLACE PROCEDURE output_message(message VARCHAR)
RETURNS VARCHAR NOT NULL
LANGUAGE SQL
AS
BEGIN
  RETURN message;
END;
```

### Working with Variables

``` sql
-- Here, variable got declared within the block. This code works
begin
    let my_variable := sqrt(25);
    return my_variable
end;

-- Alternatively, this works too, declaring variables outside of block
declare
    my_variable := sqrt(25);
begin
    return my_variable
end;
```

<br>

- Default value in variables

``` sql
declare
    profit number(38, 2) default 0.0;
begin
    let cost number(38, 2) := 100.0;
    let revenue number(38, 2) default 110.0

    profit := revenue - cost;
    return profit;
end;    
```

- Dynamic Variables

``` sql
-- set up
CREATE OR REPLACE TABLE sales_data (id integer, name varchar);
INSERT INTO sales_data (id, name) 
VALUES
    (1, 'a'),
    (2, 'b');

-- The values get loaded into the variables after SELECT
declare
    id integer,
    name varchar;
begin
    SELECT id, name INTO :id, :name
    FROM sales_data
    WHERE id = 1;
    return :id || " " || :name;
end;    
-- Result: 1 a
```

### Returning Values

- Returning Scalar Values

```sql
CREATE OR REPLACE PROCEDURE area()
returns float
language SQL
as 
$$
    declare
        radius float;
        area_of_circle float:
    begin
        radius := 3;
        area_of_circle := pi() * radius * radius;
        return area_of_circle;
    end;
$$;
CALL area()
-- Result: 28.274333882 AS area
```

- Returning Table Values

```sql
CREATE OR REPLACE PROCEDURE sales_sp()
returns table(a integer)
language SQL
as 
$$
    declare
        -- NOTE: data type of resultset
        res resultset default (SELECT id from sales_data limit 5);
        area_of_circle float:
    begin
        return table(res);
    end;
$$;
CALL sales_sp();
```

### Conditional Statements

- `IF` / `THEN` / `ELSEIF` / `ELSE`

```sql
begin
    let count := 1;
    if (count < 0) then
        return 'negative value';
    elseif (count = 0 ) then
        return 'zero';
    else    
        return 'positive value';
    end if    
end;
```

- `CASE` statement

```sql
declare
    expression_to_evaluate varchar default 'default value'
begin
    expression_to_evaluate := 'value a';
    CASE (expression_to_evaluate)
        WHEN 'value a' THEN return "x";
        WHEN 'value b' THEN return "y";
        WHEN 'value c' THEN return "z";
        WHEN 'default value' THEN return "default";
        ELSE return 'other';
end;
```

### Working with Loops
- `FOR` loops
```SQL
declare 
    counter integer default 0;
    maximum_count integer default 5;
begin
    for i in 1 to maximum_count DO
        counter := counter + 1;
    end for;
    return counter;
end;

-- FOR loops with cursor
-- Set Up
CREATE OR REPLACE TABLE invoices (price number(12, 2))
INSERT INTO invoices (price) 
    VALUES
        (11.11),
        (22.22);

declare
    total_price float;
    c1 cursor for select price from invoicesl
begin
    total_price := 0.0;
    for record in c1 DO
        total_price := total_price + record.price
    end for
    return total_price;
end;
```

- `WHILE` loops
``` sql
declare
    counter number(8,0),  -- Loop counter
    power_of_2 number(8, 0);  -- Stores the most recent power of 2 calculated
begin
    counter := 1
    power_of_2 := 1;
    while (counter <= 8) do
        power_of_2 := power_of_2 * 2;
        counter := counter + 1;
    end while;
    return power_of_2;    
end;
```

- `REPEAT` loops
``` sql
begin
    let counter := 5
    let number_of_iterations := 0;
    repeat 
        counter := counter - 1
        number_of_iterations := number_of_iterations + 1;
    until (counter = 0);
    end repeat;
    return number_of_iteraations;    
end;
```

- `LOOP` loops
``` sql
begin
    let counter := 5
    loop
        if (counter = 0) then
            break;
        end if
        counter := counter - 1
    end loop;
    return counter;    
end;
```

### Working with Cursors and Variable Bindings

```sql
declare 
    id integer default 0;
    minimum_id integer default 2;
    c1 cursor for select id from sales_data where id > ?;
begin
    open c1 using (minimum_id); -- start cursor and bind minimum_id variable
    fetch c1 into id; 
    return id;
end;

```

### Working with `RESULTSET`

```sql
declare 
    res resultset;
    col_name varchar;
    select_statement varchar;
begin
    col_name := 'id'
    select_statement := 'SELECT ' || col_name || ' FROM sales_data';
    res := (execute immediate :select_statement); -- ":" indicate that this is a variable that we are executing
    return table(res); 
end;
```

### Determine Number of Rows affected by DML

```sql
begin
    INSERT INTO sales_data (id, name) 
    VALUES
        (5, 'a'),
        (6, 'b');

    -- SQLROWCOUNT not affected by SELECT statements
    SELECT * FROM sales_data;
    -- Returns row count affected by the last DML statement
    return sqlrowcount
end;
```


### Working with Exceptions
- Raising Exceptions
```sql
declare
    my_exception exception (-200002, 'Raised MY_EXCEPTION.')
begin
    let counter := 0;
    let should_raise_exception := true;
    if (should_raise_exception) then
        raise my_exception
    end if
    counter := counter + 1
    return counter
end;
```


- Handling Exceptions
```sql
declare
    my_exception exception (-20002, 'Raised MY_EXCEPTION.')
begin
    let counter := 0;
    let should_raise_exception := true;
    if (should_raise_exception) then
        raise my_exception;
    end if;
    counter := counter + 1;
    return counter;
exception
    when statement_error then
        return object_construct(
        	'Error Type', 'STATEMENT_ERROR',
            'SQLCODE', sqlcode,
            'SQLERRM', sqlerrm,
            'SQLSTATE', sqlstate
        );
    when my_exception then
        return object_construct(
            'Error Type', 'MY_EXCEPTION',
            'SQLCODE', sqlcode, -- Error Code
            'SQLERRM', sqlerrm, -- Error Msg
            'SQLSTATE', sqlstate -- Error State
        );
    when other then
        return object_construct(
            'Error Type', 'MY_EXCEPTION',
            'SQLCODE', sqlcode,
            'SQLERRM', sqlerrm,
            'SQLSTATE', sqlstate
        );
end;

-- Another Example
begin
    select * from non_existent_Table;
exception
    when other then 
        let line := sqlcode || ": " || sqlerrm;
        insert into myexceptions values(:line); -- Insert into exception table
        raise; -- raise same exception that was being handled
end;
```
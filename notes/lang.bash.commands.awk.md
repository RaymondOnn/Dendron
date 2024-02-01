---
id: nvlqa4a7s29xn2d1vl0kmhl
title: awk
desc: ""
updated: 1704121128089
created: 1702340874416
---

https://earthly.dev/blog/awk-examples/
https://medium.com/@pragprog/table-of-contents-d248d955f077

### What is Awk

-   `awk` is a record processing tool written by Aho, Kernighan, and Weinberger in 1977. Its name is an acronym of their names.
-   `awk` lets you process text in a number of ways. You can use it to extract fields in a file, calculate totals, and even change the order of the output of a file.
-   `awk` is a command-line utility, but it’s also a scripting language with loops, variables, and conditionals.

### Setup

-   To check if awk is available:
    ```bash
    awk --version
    >>> GNU Awk 5.1.0, API: 3.0 (GNU MPFR 4.1.0, GNU MP 6.2.1)
    >>> Copyright (C) 1989, 1991-2020 Free Software Foundation.
    ```
-   Much like `sed`, `awk` works slightly differently depending on the version installed on your operating system. On Ubuntu, the awk command that ships out-of-the-box is missing a few features, so install a more full-featured version before moving on:
-   If you are doing something more involved with Awk, take the time to install GNU Awk (gawk):
    ```bash
    sudo​​ ​​apt​​ ​​install​​ ​​gawk
    ```

### Input Structure

-   A basic AWK program consists of one or more pattern-action pairs in the following general form.
    ```bash
    awk 'pattern { action }' file
    ```
    -   `pattern { action }`: The pattern is an expression that evaluates to a value that’s regarded as true or false.
    -   The `action` consists of one or more statements. In case there are multiple statements within an action, they may be separated by either a semicolon character (;) or a newline.
    - The entire program is enclosed within single quotes, and can be run from the command line using the awk utility
    - It isn’t necessary to specify both pattern and { action }. Just one of them suffices:
      - When `pattern` is not specified, the action is performed for all the records.
      - When `{ action }` is not specified, the default action is to print all the matched records.

  
> Note: AWK does not have a boolean data type, but 0 and the empty string "" are regarded as false, and all other values as true.

-   When running an AWK program, each pattern is tested against every record of the input stream, one by one. Whenever the pattern evaluates to true, the corresponding action is executed.

#### Inputs
-   An AWK program takes input either
    -   one or more text files,
    -   the standard input stream coming from the shell environment in which awk executes.
-   By default, each line in the input stream is considered one record
    -   Each record has fields (text) separated by one or more whitespace characters (spaces or tabs).

``` bash
# inputfile is the input to the program. 
awk 'pattern { action }' inputfile

# More than one file can also be passed as input.
awk 'pattern { action }' file1 file2

# output can be stored in a file using the redirection operator >.
awk 'pattern { action }' inputfile > outputfile

# the input can also be taken using the pipe operator.
cat inputfile | awk 'pattern { action }'
```


### Previewing Data

-   By default, Awk expects to receive its input on standard in and output its results to standard out. Thus, the simplest thing you can do in Awk is print a line of input.
    ```bash
    # Note the braces. This syntax will start to make sense after you see a couple of examples.
    echo "one two three" | awk '{ print }'
    >>> one two three
    ```

### Field Variables: Column Select

-   Awk creates a variable for each field (column) in a record (line) (`$1`,`$2` … `$NF`).
-   `$0` refers to the whole record.

```bash
# You can print out fields like this:
awk '{ print $1, $2, $7 }'
```

-   You can selectively choose columns (which Awk calls fields):

```bash
echo "one two three" | awk '{ print $1 }'
>>> one
echo "one two three" | awk '{ print $2 }'
>>> two
echo "one two three" | awk '{ print $3 }'
>>> three
```

-   Selecting columns across multiple lines:
    ```bash
    echo "
    one two three
    four five six" \
    | awk '{ print $1 }'
    >>> one
    >>> four
    ```
-   Selecting more than one column:
    ```bash
    echo "
    one two three
    four five six" \
    | awk '{ print $1, $2 }'
    >>> one two
    >>> four five
    ```

#### Built-In Variables

>##### List of built-in variables
- NR: Number of file records read so far (reset to 0 for each file)
- NF: Number of fields in the current record
- RS: Record separator (newline by default)
- ORS: Output record separator
- FS: Field separator (one or more whitespaces by default)
- OFS: Output field separator (space by default)

### Implementation
-   NF is a variable holding the Number of Fields in a record.

```bash
echo "
one two three
four five six" \
| awk '{ print $NF }'
>>> three
>>> six
```

```bash
# Leveraging the variable as an index to get the last column
>_$ awk -F '\t' '{ print NF }' bookreviews.tsv | head
15
15
15
15
15
15
15
15
15
15
```

-   NR is the variable holding record number.

```bash
# NR is handy for printing line numbers:
>_$ awk -F '\t' '{ print NR " " $(NF-2) }' bookreviews.tsv | head
1 review_headline
2 Great Twist on Zombie Mythos
3 Helpful and Practical
4 Paul
5 Direction and Destiny in the Birth Chart
6 This was Okay
7 Excellent read!!!
8 A must read for science thinkers
9 Chocoholic heaven
10 Quilt Art Projects for Children
```


### Beyond the basics

#### Awk Sample Data

-   To move beyond simple printing, I need some sample data. I will use the book portion of the amazon product reviews dataset for the rest of this tutorial.
-   This dataset contains product reviews and metadata from Amazon, including 142.8 million reviews spanning May 1996 - July 2014.
-   Once you’ve grabbed that data, you should have Amazon book review data that looks like this:

```bash
marketplace customer_id review_id product_id product_parent product_title product_category star_rating helpful_votes total_votes vine verified_purchase review_headline review_body review_date
US 22480053 R28HBXXO1UEVJT 0843952016 34858117 The Rising Books 5 0 0 N N Great Twist on Zombie Mythos I've known about this one for a long time, but just finally got around to reading it for the first time. I enjoyed it a lot!  What I liked the most was how it took a tired premise and breathed new life into it by creating an entirely new twist on the zombie mythos. A definite must read! 2012-05-03
```

-   Each row in this file represents the record of one book review. Amazon lays out the fields like this:

```bash
DATA COLUMNS:
01  marketplace       - 2 letter country code of the marketplace where the ↩
review was written.
02  customer_id       - Random identifier that can be used to aggregate reviews↩
written by a single author.
03  review_id         - The unique ID of the review.
04  product_id        - The unique Product ID the review pertains to.
05  product_parent    - Random identifier that can be used to aggregate ↩
reviews for the same product.
06  product_title     - Title of the product.
07  product_category  - Broad product category that can be used to group ↩
reviews
08  star_rating       - The 1-5 star rating of the review.
09  helpful_votes     - Number of helpful votes.
10  total_votes       - Number of total votes the review received.
11  vine              - Review was written as part of the Vine program.
12  verified_purchase - The review is on a verified purchase.
13  review_headline   - The title of the review.
14  review_body       - The review text.
15  review_date       - The date the review was written.
```

#### Printing Book Data

I can now test out my field printing skills on a bigger file. I can start by printing fields that I care about, like the marketplace:

```bash
>_$ awk '{ print $1 }' bookreviews.tsv | head
marketplace
US
US
US
US
US
US
US
US
US
Or the customer_id:

>_$ awk '{ print $2 }' bookreviews.tsv | head
customer_id
22480053
44244451
20357422
13235208
26301786
27780192
13041546
51692331
23108524
```

### Changing Field Delimiters

-   By default, Awk assumes that the fields in a record are **whitespace delimited**.

    ```bash
    # when I try to print out the title, things do not go well:
    > awk '{ print $6 }' bookreviews.tsv | head
    product_title
    The
    Sticky
    Black
    Direction
    Until
    Unfinished
    The
    Good
    Patterns
    ```

-   I can change the field delimiter to use tabs using the `awk -F option`: `awk -F '\t' '{ print $6 }'`

    ```bash
    # Changing to tab delimited via -F option
    >_$ awk -F '\t' '{ print $6 }' bookreviews.tsv | head
    product_title
    The Rising
    Sticky Faith Teen Curriculum with DVD: 10 Lessons to Nurture Faith Beyond High
    Black Passenger Yellow Cabs: Of Exile And Excess In Japan
    Direction and Destiny in the Birth Chart
    Until the Next Time
    Unfinished Business
    The Republican Brain: The Science of Why They Deny Science- and Reality
    Good Food: 101 Cakes & Bakes
    Patterns and Quilts (Mathzones)
    ```

-   Working backward from the last position forward by subtracting from NF.

    ```bash
    >_$ awk -F '\t' '{ print $NF "\t" $(NF-2)}' bookreviews.tsv | head
    review_date     review_headline
    2012-05-03      Great Twist on Zombie Mythos
    2012-05-03      Helpful and Practical
    2012-05-03      Paul
    2012-05-03      Direction and Destiny in the Birth Chart
    2012-05-03      This was Okay
    2012-05-03      Excellent read!!!
    2012-05-03      A must read for science thinkers
    2012-05-03      Chocoholic heaven
    2012-05-03      Quilt Art Projects for Children
    ```

### Filter Rows with RegEx Pattern Matching

-   Each awk action, like `{ print $4}`, can be preceded by a pattern, like `/regex/`.
    -   This instructs Awk to match the given pattern on each line
    -   Only matching cases get return to standard output
        ```bash
        > \_$ echo "aa
                    bb
                    cc" | awk '/bb/'
        bb
        ```
    -   You could replace `grep` this way. You can also combine this with the field access and printing we’ve done so far:
    ```bash
    > \_$ echo "aa 10
                bb 20
                cc 30" | awk '/bb/ { print $2 }'
    20
    ```

#### How to use pattern matching

-   You can use a simple regular expression for the pattern. In which case it matches anywhere in the line, like `grep`:
    ```bash
    > \_$ awk '/hello/ { print "This line contains hello", $0}'
    ```
-   Or you can match within a specific field:

    ```bash
    \_$ awk '$4~/hello/ { print "This field contains hello", $4}'
    ```

-   Or you can exact match a field:

    ```bash
    \_$ awk '$4 == "hello" { print "This field is hello:", $4}'
    ```

### **`printf`** and Built-ins

-   If you need to print out a table, Awk lets you use `printf` and built-ins like `substr` to format your output.

    ```bash
    > \_$ awk '{ printf "%s \t %-5s", $1, substr($2,1,5) }'
    ```

-   `printf` works like it does in the C and uses a format string and a list of values.

    -   Use `%s` to insert a string into the format string, and other flags let you the set width or precision.
    -   I can add right padding and fix my layout by changing `%s` to `%-Ns` where N is my desired column width

        ```bash
        # Using print
        print $15 "\t" $13 "\t" $8

        # Using printf
        printf "%s \t %s \t %s", $15, $13, $8
        ```

-   An alternative method is to use the `column` command:

    ```bash
    column​​ ​​-t​​ ​​-s,​​ ​​population.txt​
    ```

    -   `-t`: states you want the output in a table
    -   `-s`: specifies what the original file uses for its delimiter.

#### An example

-   Taking a look at the reviews for Hunger Games

    -   review_date (`$15`)
    -   the review_headline (`$13`),
    -   the star_rating (`$8`)

    ```bash
    # Using print, the layout of the table looks bad
    > \_$ awk -F '\t' '$4 == "0439023483" { print $15 "\t" $13 "\t" $8}' bookreviews.tsv | head
    2015-08-19 Five Stars 5
    2015-08-17 Five Stars 5
    2015-07-23 Great read 5
    2015-07-05 Awesome 5
    2015-06-28 Epic start to an epic series 5
    2015-06-21 Five Stars 5
    2015-04-19 Five Stars 5
    2015-04-12 i lile the book 3
    2015-03-28 What a Great Read, i could not out it down 5
    2015-03-28 Five Stars 5

    # Using printf to format output
    # substr() to shorten review_headlines
    > \_$ awk -F '\t' '$4 == "0439023483" {printf "%s \t %-20s \t %s \n",$15,substr($13,1,20),$8}' bookreviews.tsv | head
    2015-08-19 Five Stars 5
    2015-08-17 Five Stars 5
    2015-07-23 Great read 5
    2015-07-05 Awesome 5
    2015-06-28 Epic start to an epi 5
    2015-06-21 Five Stars 5
    2015-04-19 Five Stars 5
    2015-04-12 i lile the book 3
    2015-03-28 What a Great Read, i 5
    2015-03-28 Five Stars 5
    ```

### `BEGIN` and `END` Actions

-   Awk provides two special patterns, BEGIN and END. You can use them to run actions before and after processing the records.
    ```bash
    BEGIN {
    ​   # some optional initialization stuff
    ​}
    ​
    ​optional search pattern {
    ​   # steps
    ​}
    ​
    ​END {
        ​# some final stuff
    ​}
    ```
-   For example, this is how you would initialize data, print headers and footer, or do any start-up or tear-down stuff in Awk.

    ```bash
    > \_$ awk '
    > BEGIN { print "start up" }
        { print "line match" }
    END { print "tear down" }'

    # You can also easily use variables in Awk. No declaration is needed.
    > \_$ awk -F '{ total = total + $8 }'
    ```

-   An example:
    -   Calculate the average rating for book reviews in this data set.
        -   To do that, I need to use a variable. However, I don’t need to declare the variable or its type. I can just use it:
        1. I can add up and print out a running total of review_stars ($8) like this:
            ```bash
            > \_$ awk -F '\t' '{ total = total + $8; print total }' bookreviews.tsv | head
            > 0
            > 5
            > 10
            > ...
            ```
        2. to turn this into an average, I can use NR to get the total amount of records and END to run an action at the end of the processing.
            ```bash
            > \_$ awk -F '\t' '
                { total = total + $8 }
            END { print "Average book review:", total/NR, "stars" }
            ' bookreviews.tsv | head
            Average book review is 4.24361 stars
            ```
        3. I can also use BEGIN to run an action before Awk starts processing records.
            ```bash
            > \_ $ awk -F '\t' '
            > BEGIN { print "Calculating Average ..." }
                { total = total + $8 }
            END { print "Average book review:", total/NR, "stars" }
            ' bookreviews.tsv
            Calculating Average ...
            Average book review is 4.24361 stars
            ```

### Fun Awk One-Liners

-   Printing files with a human readable size:
    ```bash
    > \_$ ls -lh | awk '{ print $5,"\t", $9 }'
    > 7.8M The_AWK_Programming_Language.pdf
    > 6.2G bookreviews.tsv
    ```
-   Getting the containerID of running docker containers:
    ```bash
    > \_$ docker ps -a | awk '{ print $1 }'
    > CONTAINER
    > 08488f220f76
    > 3b7fc673649f
    ```
    -   You can combine that last one with a regex to focus on a line you care about. Here I stop postgres, regardless of its tag name:
        ```bash
        > \_$ docker stop "$(docker ps -a | awk '/postgres/{ print $1 }')"
        ```

### Awk Scripting

> #### Why Awk Scripting
>
> -   Once we move beyond one-liners, a natural question is why. As in ‘Why not use Python? Isn’t it good at this type of thing?’
> -   First, Awk is great for writing programs that are, at their core, a glorified for loop over some input. If that is what you are doing, and the control flow is limited, using Awk will be more concise than Python.
> -   Second, if you need to rewrite your Awk program into Python at some point, so be it. It’s not going to be more than 100 lines of code, and the translation process will be straightforward.

-   Once you are beyond a single line, it makes sense to put your Awk script into a file.
-   You can then call you program using the `-f` option

    ```bash
    > awk -f file.awk [input]
    ```

    1. Using a shebang i.e. `#!/usr/bin/env -S gawk -f`

        ```bash
        # average.awk
        #!/usr/bin/env -S gawk -f

        BEGIN { FS = "\t" }
        { total = total + $8 }
        END { print "Average book $6 review is", total/NR, "stars" }

        # running average.awk
        > ./average.awk bookreviews.tsv

        #  Or you can also pass it to awk directly using -f:
        > awk -f average.awk bookreviews.tsv
        ```

    2. or by using a bash exec command via a bash script
        - i.e. `exec awk -F '\t' 'print $0' $1`
        ```bash
        > cat average
        exec awk -F '\t' '
            { total = total + $8 }
        END { print "Average book review is", total/NR, "stars" }
        ' $1
        > average bookreviews.tsv
        Average book review is 4.2862 stars
        ```

-   **Note**: If you use a shebang or pass to Awk directly, it’s easiest to set the file separator using `FS = "\t"` in the `BEGIN` action.
    ```bash
    BEGIN { FS = "\t" }
    ```

#### Awk Average Example

-   Calculating review scores for The Hunger Games:

    ```bash
    # average.awk
    $4 == "0439023483" {
        title=$6
        count = count + 1;
        total = total + $8
    }
    END {
        printf "Book: %-5s\n", title
        printf "Average Rating: %.2f\n", total/count
    }

    # Output:
    Book: The Hunger Games (The Hunger Games, Book 1)
    Average Rating: 4.67%
    ```

### Awk Associative Arrays

-   Awk has associative arrays built it, and you can use them in much the same way you would use Python dictionaries.
    ```bash
    arr["key1"] = "one"
    arr["key2"] = "two"
    arr["key3"] = "three"
    ```
-   You can then use a for loop to iterate over them:
    ```bash
    for (i in arr){
        print i, arr[i]
    }
    key1 one
    key2 two
    key3 three
    ```

#### An example

-   I’d like to know if the series stays strong or if it’s a single great book that the author stretched out into a trilogy.

    -   If the reviews decline quickly, then that is not a good sign. I should be able to see which book was rated the best and which was the worst.
    -   To do so, I can loop over the list of reviews and use a dictionary to track the total stars and total reviews for each.

        ```bash
        # hungergames.awk
        BEGIN { FS = "\t" }
        $6~/\(The Hunger Games(, Book 1)?\)$/ {
            title[$6]=$6
            count[$6]= count[$6] + 1
            total[$6]= total[$6] + $8
        }
        END {
            for (i in count) {
                printf "---------------------------------------\n"
                printf "%s\n", title[i]
                printf "---------------------------------------\n"
                printf "Ave: %.2f\t Count: %s \n\n", total[i]/count[i], count[i]
            }
        }

        # Running:
        > awk -f hungergames.awk bookreviews.tsv

        The Hunger Games (The Hunger Games, Book 1)
        Ave: 4.55 Count: 1497

        Mockingjay (The Hunger Games)
        Ave: 3.77 Count: 3801

        Catching Fire (The Hunger Games)
        Ave: 4.52 Count: 2205
        ```

### Awk If Else

-   Awk has branching using if and else statements. It works exactly like you might expect it to:
    ```bash
    > \_$ echo "1\n 2\n 3\n 4\n 5\n 6" | awk '{
            if (NR % 2)
                print "odd"
            else
                print $0
            }'
    odd
    2
    odd
    4
    odd
    6
    ```

#### An example

-   Given every book on Amazon has a star rating between 3.0 and 4.5 stars, it makes it hard to judge purely based on numbers.
-   So let’s rescale things in terms of the average. Maybe if I normalize the reviews, it will be easier to determine how good or bad the 3.77 average for Mockingjay is.

1. Calculate the global average but adding up the total and average for every row:

    ```bash
    # hungergames.awk
    {
        # Global Average
        g_count = g_count + 1
        g_total = g_total + $8
    }

    # Then I calculate the global average:
    END {
        g_score = g_total/g_count
        ...
    }
    ```

2. Once I have this, I can score books based on how higher or lower they are than the average. All I need to do is add some if statements to my END pattern to accomplish this:

    ```bash
    # hungergames.awk
    END {
        g_score = g_total/g_count
        for (i in count) {
            score = total[i]/count[i]
            printf "%-30s\t", substr(title[i],1,30)
            if (score - g_score > .5)
                printf "👍👍👍"
            else if (score - g_score > .25)
                printf "👍👍"
            else if (score - g_score > 0)
                printf "👍"
            else if (g_score - score > 1)
                printf "👎👎👎"
            else if (g_score - score > .5)
                printf "👎👎"
            else if (g_score - score > 0)
                printf "👎"
            printf "\n"
        }
    }

    #The values for partitioning are just a guess, but it makes it easier for me to understand the rankings:
    # It looks like Mockingjay, at least on Amazon and in this dataset, was not well received.
    The Hunger Games (The Hunger G 👍
    Catching Fire: The Official Il 👍👍👍
    Mockingjay (The Hunger Games) 👎👎

    The Two Towers (The Lord of th 👍👍
    The Fellowship of the Ring (Th 👍👍
    The Return of the King (The Lo 👍👍👍
    ```

    - You can easily modify this script to query for books ad hoc:

```bash
# average
exec gawk -F '\t' '
{
    # Global Average
    g_count = g_count + 1
    g_total = g_total + $8
    PROCINFO["sorted_in"] = "@val_num_asc"
}
$6~/^.*'+"$1"+'.*$/ {               # <-- Take match as input
    title[$6]=$6
    count[$6]= count[$6] + 1
    total_votes[$6]= total[$6] + $8
}
END {
    PROCINFO["sorted_in"] = "@val_num_desc"
    g_score = g_total/g_count
    for (i in count) {
        score = total[i]/count[i]
        printf "%-50s\t", substr(title[i],1,50)
        if (score - g_score > .4)
            printf "👍👍👍"
        else if (score - g_score > .25)
            printf "👍👍"
        else if (score - g_score > 0)
            printf "👍"
        else if (g_score - score > 1)
            printf "👎👎👎"
        else if (g_score - score > .5)
            printf "👎👎"
        else if (g_score - score > 0)
            printf "👎"
        printf "\n"
    }
}
' bookreviews.tsv | head -n 1

# And then run it like this:
> ./average "Left Hand of Darkness"
The Left Hand of Darkness (Ace Science Fiction) 👎
> ./average "Neuromancer"
Neuromancer 👎👎
> ./average "The Lifecycle of Software Objects"
The Lifecycle of Software Objects 👎
```

### Awk Sort by Values

-   Awk (specifically gawk) allows you easily configure your iteration order using a magic variable called PROCINFO["sorted_in"].
-   This means that if I change our program to sort by value and drop the filtering, then I will be able to see the top reviewed books:

```bash
# top_books
exec gawk -F '\t' '
{
    # Global Average
    g_count = g_count + 1
    g_total = g_total + $8
    title[$6]=$6
    count[$6]= count[$6] + 1
    total[$6]= total[$6] + $8
}
END {
    PROCINFO["sorted_in"] = "@val_num_desc" # <-- Print in value order
    g_score = g_total/g_count
    for (i in count) {
        score = total[i]/count[i]
        printf "%-50s\t", substr(title[i],1,50)
        if (score - g_score > .4)
            printf "👍👍👍"
        else if (score - g_score > .25)
            printf "👍👍"
        else if (score - g_score > 0)
            printf "👍"
        else if (g_score - score > 1)
            printf "👎👎👎"
        else if (g_score - score > .5)
            printf "👎👎"
        else if (g_score - score > 0)
            printf "👎"
        printf "\n"
    }
}
' bookreviews.tsv

# Running it:
> ./top_books | head
Harry Potter And The Sorcerer's Stone 👍👍
Fifty Shades of Grey 👎👎
The Hunger Games (The Hunger Games, Book 1) 👍
The Hobbit 👍
Twilight 👎
Jesus Calling: Enjoying Peace in His Presence 👍👍👍
Unbroken: A World War II Story of Survival, Resili 👍👍👍
The Shack: Where Tragedy Confronts Eternity 👎
Divergent 👍
Gone Girl 👎👎
```

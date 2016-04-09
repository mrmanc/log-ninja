# log-ninja

A set of scripts I find useful when analysing log files. You might like to put them in your ~/bin folder.

Jump to: [batch](#batch), [distribution](#distribution), [field](#field), [fields](#fields), [float](#float), [int](#int), [logs](#logs), [median](#median), [percentiles](#percentiles), [standarddeviation](#standarddeviation), [total](#total), [uniqcount](#uniqcount), [urlencode / urldecode](#urlencode--urldecode), [variance](#variance).


## batch

batch will read a steam of input from STDIN and concatenate it (space separated) into a string, flushing it to STDOUT once per second. That means it can batch numerical output from a command in to a line per second with multiple values on it.

So this:

```sh
$ for (( i = 0; i <= 10 ; i++ )); do echo $i; sleep 0.2; done
0
1
2
3
4
5
6
7
8
9
10
```

becomes this:

```sh
$ for (( i = 0; i <= 10 ; i++ )); do echo $i; sleep 0.2; done | ./batch
0 1 2
4 5 6 7
9 10
```

[Watch an example](http://showterm.io/77d78dc1fe91a5d854ba8).

## distribution

If you have the Perl library Time/HiRes.pm installed then you should look at using [philovivero's Perl implementation](https://github.com/philovivero/distribution), which is prettier and supports string values too (found looking at an old [Stack Overflow post](http://stackoverflow.com/questions/6949332/in-terminal-ascii-histograms-tool/11234476#11234476) of mine).

See [this blog post](http://bit.ly/ascii-histogram) for more information about this script.

Briefly, use it like this:

Show distribution of earthquake magnitudes over the last 7 days:
```
$ curl http://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.csv --silent \
| \sed '1d' \
| cut -d, -f5 \
| ./distribution width=70 lines=30

Found 1154 records distributed in 58 distinct values between 0 and 6.1

     Value    Quant   %ile Histogram
     -----    -----   ---- ---------
      0.00        9   0.78 _____
      0.21       59   5.89 ________________________________
      0.42       78  12.65 __________________________________________
      0.63       66  18.37 ____________________________________
      0.84      119  28.68 _________________________________________________________________
      1.05      130  39.95 ______________________________________________________________________
      1.26      112  49.65 _____________________________________________________________
      1.47      104  58.67 ________________________________________________________
      1.68       87  66.20 _______________________________________________
      1.89      129  77.38 ______________________________________________________________________
      2.10       52  81.89 ____________________________
      2.31       28  84.32 ________________
      2.52       28  86.74 ________________
      2.73       14  87.95 ________
      2.94       13  89.08 _______
      3.16       17  90.55 __________
      3.37        6  91.07 ____
      3.58        8  91.77 _____
      3.79        1  91.85 _
      4.00        4  92.20 ___
      4.21        8  92.89 _____
      4.42       11  93.85 ______
      4.63       21  95.67 ____________
      4.84       25  97.83 ______________
      5.05       10  98.70 ______
      5.26        8  99.39 _____
      5.47        2  99.57 __
      5.68        1  99.65 _
      5.89        2  99.83 __
      6.10        2 100.00 __

```sh

Or how tall people are (in inches):

```sh
$ curl --silent http://socr.ucla.edu/docs/resources/SOCR_Data/SOCR_Data_Dinov_020108_HeightsWeights.html | \
grep -A 2 "<tr" | grep "<td x:num" | sed -e "s/^.*>\([0-9.]*\)<.*$/\1/" | ./distribution lines=30 width=70

Found 25000 records distributed in 20917 distinct values between 60.2784 and 75.1528

     Value    Quant   %ile Histogram
     -----    -----   ---- ---------
     60.28        2   0.01 _
     60.79        4   0.02 _
     61.30        3   0.04 _
     61.82       10   0.08 _
     62.33       59   0.31 __
     62.84       91   0.68 ___
     63.36      211   1.52 ______
     63.87      359   2.96 __________
     64.38      597   5.34 ________________
     64.89      874   8.84 _______________________
     65.41     1211  13.68 ________________________________
     65.92     1731  20.61 ______________________________________________
     66.43     2129  29.12 ________________________________________________________
     66.95     2444  38.90 _________________________________________________________________
     67.46     2637  49.45 ______________________________________________________________________
     67.97     2667  60.12 ______________________________________________________________________
     68.48     2508  70.15 __________________________________________________________________
     69.00     2134  78.68 _________________________________________________________
     69.51     1745  85.66 ______________________________________________
     70.02     1276  90.77 __________________________________
     70.54      960  94.61 __________________________
     71.05      621  97.09 _________________
     71.56      348  98.48 __________
     72.08      192  99.25 ______
     72.59       94  99.63 ___
     73.10       56  99.85 __
     73.61       19  99.93 _
     74.13       14  99.98 _
     74.64        3 100.00 _
     75.15        1 100.00 _
```

Or show the distribution of word length in your UNIX dictionary: (you can use <a href="http://www.cs.duke.edu/~ola/ap/linuxwords">this shorter words list</a> if you don't have words)

```sh
$ cat /usr/share/dict/words | awk '{print length($1)}' | ./distribution width=70

Found 489040 records distributed in 35 distinct values between 1 and 45

     Value    Quant   %ile Histogram
     -----    -----   ---- ---------
      1.00       26   0.01 _
      2.00      290   0.06 _
      3.00     2017   0.48 ___
      4.00     7508   2.01 ________
      5.00    18148   5.72 ___________________
      6.00    35087  12.90 _____________________________________
      7.00    50877  23.30 _____________________________________________________
      8.00    64210  36.43 ___________________________________________________________________
      9.00    67754  50.29 ______________________________________________________________________
     10.00    64374  63.45 ___________________________________________________________________
     11.00    53736  74.44 ________________________________________________________
     12.00    41934  83.01 ____________________________________________
     13.00    30291  89.21 ________________________________
     14.00    20873  93.47 ______________________
     15.00    13512  96.24 ______________
     16.00     8427  97.96 _________
     17.00     4760  98.93 _____
     18.00     2624  99.47 ___
     19.00     1378  99.75 __
     20.00      630  99.88 _
     21.00      309  99.94 _
     22.00      134  99.97 _
     23.00       63  99.98 _
     24.00       35  99.99 _
     25.00       17  99.99 _
     26.00        2 100.00 _
     27.00        5 100.00 _
     28.00        3 100.00 _
     29.00        6 100.00 _
     30.00        2 100.00 _
     31.00        2 100.00 _
     32.00        1 100.00 _
     33.00        1 100.00 _
     34.00        2 100.00 _
     35.00        0 100.00
     36.00        0 100.00
     37.00        0 100.00
     38.00        0 100.00
     39.00        0 100.00
     40.00        0 100.00
     41.00        0 100.00
     42.00        0 100.00
     43.00        0 100.00
     44.00        0 100.00
     45.00        2 100.00 _
```

It's <a href="http://en.wikipedia.org/wiki/Pneumonoultramicroscopicsilicovolcanoconiosis">pneumonoultramicroscopicsilicovolcanoconiosis</a> by the way, in case you were wondering. I thought you were.

You could cut out the outlier by selecting a window of values (i.e. min and max) like this:

```sh
$ cat /usr/share/dict/words | awk '{print length($1)}' | ./distribution width=80 min=1 max=35
```

There was a known bug with percentiles when a min or a max value was specified, but a rewrite has solved this.

You might also be interested in <a href="https://github.com/holman/spark">Spark</a> - Holman's script which generates sparklines (more traditional bar charts minus the labels etc) on your command line from on a list of numbers.

## field

Takes lines on STDIN and returns the specified field (delimited by whitespace).

```sh
$ echo "The quick brown fox jumps over the lazy dog" | ./field 4
fox
```

## fields

Helps you work out what field you want to show. 

```sh
$ echo "The quick brown fox jumps over the lazy dog" | ./fields
1 : The | 2 : quick | 3 : brown | 4 : fox | 5 : jumps | 6 : over | 7 : the | 8 : lazy | 9 : dog |

Showing final line again for readability
1 : The
2 : quick
3 : brown
4 : fox
5 : jumps
6 : over
7 : the
8 : lazy
9 : dog
```

## float

Converts a field into a float by extracting the first float it can find in the string, ignoring and removing anything else.

```sh
$ echo -e "167.2ms\n115.89ms\n143.85ms" | ./float
167.2
115.89
143.85
```

## int

Converts a field into an integer by extracting the first integer it can find in the string, ignoring and removing anything else.

```sh
$ echo -e "167.2ms\n115.89ms\n143.85ms" | ./int
167
115
143
```

## median

Calculates the [median](https://en.wikipedia.org/wiki/Median) value (i.e. 50th percentile) in a stream of numbers, because the mean really sucks. Looking at the median avoids any outlying values skewing the outcome, and allows you to make a statement about half the values such as half of the web requests took less time than 95ms. For example:
 
 ```
 $ echo -e "1\n2\n3\n4\n" | median
   3
 $ echo -e "1\n2\n3\n4\n5\n100" | median
   3.5
 ```

## percentiles

This command takes a stream of numbers and provides a summary of their distribution: the min/max, median and the 25th, 75th, 95th, 99th percentiles.

```sh
$ echo -e "1\n2\n3\n4\n" | percentiles 
  Min: 1, 25th , Median: 1, 75th: 2, 95th: 3, 99th: 3, Max: 4
```

## standarddeviation

Calculates the population standard deviation for a stream of numbers.

```sh
$ echo -e "1\n2\n3\n4\n" | standarddeviation 
1.41421
```

## total

Adds up a column of numbers from STDIN and outputs the total at the end.

```sh
$ : echo -e "1\n4\n7\n24\n64" | ./total
100
```

## uniqcount

Roughly equivalent to piping some lines of text through sort and uniq -c, except less characters.

```sh
$ echo -e "apple\norange\norange\nbanana\napple\napple\nquince\nbanana" | ./uniqcount
2 orange
3 apple
2 banana
1 quince
```

## urlencode / urldecode

Wrapper scripts for perls urlencoding functionality. 

```sh
$ echo "first=this+is+a+field&second=was+it+clear+(already)?" | ./urlencode
first%3Dthis%2Bis%2Ba%2Bfield%26second%3Dwas%2Bit%2Bclear%2B(already)%3F
$ echo "first=this+is+a+field&second=was+it+clear+%28already%29%3F" | ./urldecode
first=this+is+a+field&second=was+it+clear+(already)?
```

## variance

Calculates the population variance for a stream of numbers.

```sh
$ echo -e "1\n2\n3\n4\n" | variance 
  2
```
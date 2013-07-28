log-ninja
=========

A set of scripts I find useful when analysing log files. You might like to put them in your ~/bin folder.

col
---

Takes lines on STDIN and returns the specified column (delimited by whitespace).

<pre>
mark$ echo "The quick brown fox jumps over the lazy dog" | ./col 4
fox
</pre>

distribution
------------

See <a href="http://bit.ly/pivot-stdout">this blog post</a> for more information about this script.

Briefly, use it like this:

Show distribution of earthquake magnitudes over the last 7 days:
<pre>
mark$ curl http://earthquake.usgs.gov/earthquakes/catalogs/eqs7day-M0.txt --silent \
| \sed '1d' \
| cut -d, -f9 \
| ./distribution width=70

Found 1154 records distributed in 58 distinct values between 0 and 6.1

     Value    Quant   %ile Histogram
     -----    -----   ---- ---------
      0.00      331  28.68 ▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒
      1.02      528  74.44 ▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒
      2.03      164  88.65 ▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒
      3.05       38  91.94 ▒▒▒▒▒▒
      4.07       68  97.83 ▒▒▒▒▒▒▒▒▒▒
      5.08       23  99.83 ▒▒▒▒
      6.10        2 100.00 ▒
</pre>

Or show the distribution of word length in your UNIX dictionary: (you can use <a href="http://www.cs.duke.edu/~ola/ap/linuxwords">this shorter words list</a> if you don't have words)

<pre>
mark$ cat /usr/share/dict/words | awk '{print length($1)}' | ./distribution width=70

Found 489040 records distributed in 35 distinct values between 1 and 45

     Value    Quant   %ile Histogram
     -----    -----   ---- ---------
      1.00       26   0.01 ▒
      2.00      290   0.06 ▒
      3.00     2017   0.48 ▒▒▒
      4.00     7508   2.01 ▒▒▒▒▒▒▒▒
      5.00    18148   5.72 ▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒
      6.00    35087  12.90 ▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒
      7.00    50877  23.30 ▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒
      8.00    64210  36.43 ▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒
      9.00    67754  50.29 ▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒
     10.00    64374  63.45 ▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒
     11.00    53736  74.44 ▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒
     12.00    41934  83.01 ▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒
     13.00    30291  89.21 ▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒
     14.00    20873  93.47 ▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒
     15.00    13512  96.24 ▒▒▒▒▒▒▒▒▒▒▒▒▒▒
     16.00     8427  97.96 ▒▒▒▒▒▒▒▒▒
     17.00     4760  98.93 ▒▒▒▒▒
     18.00     2624  99.47 ▒▒▒
     19.00     1378  99.75 ▒▒
     20.00      630  99.88 ▒
     21.00      309  99.94 ▒
     22.00      134  99.97 ▒
     23.00       63  99.98 ▒
     24.00       35  99.99 ▒
     25.00       17  99.99 ▒
     26.00        2 100.00 ▒
     27.00        5 100.00 ▒
     28.00        3 100.00 ▒
     29.00        6 100.00 ▒
     30.00        2 100.00 ▒
     31.00        2 100.00 ▒
     32.00        1 100.00 ▒
     33.00        1 100.00 ▒
     34.00        2 100.00 ▒
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
     45.00        2 100.00 ▒
</pre>

It's <a href="http://en.wikipedia.org/wiki/Pneumonoultramicroscopicsilicovolcanoconiosis">pneumonoultramicroscopicsilicovolcanoconiosis</a> by the way, in case you were wondering. I thought you were.

You could cut out the outlier by selecting a window of values (i.e. min and max) like this:

<pre>
mark$ cat /usr/share/dict/words | awk '{print length($1)}' | ./distribution width=80 max=31 min=3
</pre>

There was a known bug with percentiles when a min or a max value was specified, but a rewrite has solved this.

You might also be interested in <a href="https://github.com/holman/spark">Spark</a> - Holman's script which generates sparklines (more traditional bar charts minus the labels etc) on your command line from on a list of numbers.
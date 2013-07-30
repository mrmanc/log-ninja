log-ninja
=========

A set of scripts I find useful when analysing log files. You might like to put them in your ~/bin folder.

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
      0.00      331  28.68 ____________________________________________
      1.02      528  74.44 ______________________________________________________________________
      2.03      164  88.65 ______________________
      3.05       38  91.94 ______
      4.07       68  97.83 __________
      5.08       23  99.83 ____
      6.10        2 100.00 _
</pre>

Or show the distribution of word length in your UNIX dictionary: (you can use <a href="http://www.cs.duke.edu/~ola/ap/linuxwords">this shorter words list</a> if you don't have words)

<pre>
mark$ cat /usr/share/dict/words | awk '{print length($1)}' | ./distribution width=70

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
</pre>

It's <a href="http://en.wikipedia.org/wiki/Pneumonoultramicroscopicsilicovolcanoconiosis">pneumonoultramicroscopicsilicovolcanoconiosis</a> by the way, in case you were wondering. I thought you were.

You could cut out the outlier by selecting a window of values (i.e. min and max) like this:

<pre>
mark$ cat /usr/share/dict/words | awk '{print length($1)}' | ./distribution width=80 max=31 min=3
</pre>

There was a known bug with percentiles when a min or a max value was specified, but a rewrite has solved this.

You might also be interested in <a href="https://github.com/holman/spark">Spark</a> - Holman's script which generates sparklines (more traditional bar charts minus the labels etc) on your command line from on a list of numbers.

field
-----

Takes lines on STDIN and returns the specified field (delimited by whitespace).

<pre>
mark$ echo "The quick brown fox jumps over the lazy dog" | ./field 4
fox
</pre>

fields
------

Helps you work out what field you want to show. 

<pre>
mark$ echo "The quick brown fox jumps over the lazy dog" | ./fields
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
</pre>

float
-----

Converts a field into a float by extracting the first float it can find in the string, ignoring and removing anything else.

<pre>
mark$ echo -e "167.2ms\n115.89ms\n143.85ms" | ./float
167.2
115.89
143.85
</pre>

int
---

Converts a field into an integer by extracting the first integer it can find in the string, ignoring and removing anything else.

<pre>
mark$ echo -e "167.2ms\n115.89ms\n143.85ms" | ./int
167
115
143
</pre>

uniqcount
---------

Roughly equivalent to piping some lines of text through sort and uniq -c, except less characters.

<pre>
mark$ echo -e "apple\norange\norange\nbanana\napple\napple\nquince\nbanana" | ./uniqcount
2 orange
3 apple
2 banana
1 quince

urlencode / urldecode
---------------------

Wrapper scripts for perls urlencoding functionality. 

<pre>
mark$ echo "first=this+is+a+field&second=was+it+clear+(already)?" | ./urlencode
first%3Dthis%2Bis%2Ba%2Bfield%26second%3Dwas%2Bit%2Bclear%2B(already)%3F
mark$ echo "first=this+is+a+field&second=was+it+clear+%28already%29%3F" | ./urldecode
first=this+is+a+field&second=was+it+clear+(already)?
</pre>

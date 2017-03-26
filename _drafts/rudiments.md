
_what is data_

everything is 1 and 0, ok.

How do you interface with computers? We are so used to a gui, and that's fine,
but an understanding of command line work is vital to being able to grok the
system as a whol.e

what is the file system? a tree, mostly. How do you navigate that system?

Some commands to definitely, definitely know:

man
pwd
ls
cd
cp
mv
cat
mkdir
tree maybe

Understanding that code is text files. That's it! That's all they are! html,
for example, is simply a text file written to a spec that a browser knows how
to read to produce an output.

Similarly, a ruby file is a text file written to a spec that the Ruby
interpreter knows how to read as instructions to do something. A `.py` file is
a text file written to a spec that the python interpreter knows how to
interpret as instructions. There is nothing special about the file itself other
than that it is written in valid python or valid ruby, in fact, some
expressions that the two languages have in common can be run through both
interpreters to obtain a result:

```ruby
# thing.txt
print 2 + 2
```

Is valid Ruby, and it is valid Python.

```
ruby thing.txt
```
and

```
python thing.txt
```

Both print out `4`. The suffix is arbitrary to a mac or linux system. (I think
the suffixes matter more in windows land.)

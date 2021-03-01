# Python 2 to Python 3

## Summary

This section summarises the changes required to convert [wca-ipy](https://github.com/Logiqx/wca-ipy) from Python 2 to Python 3.

References:

* [Porting Python 2 Code to Python 3](https://docs.python.org/3/howto/pyporting.html)



### General

#### Print

print -> print()

#### Integer Division

/ -> //

#### Dictionaries

eventsDict.has_key(inputRow[0]) -> inputRow[0] in eventsDict



### Libraries

#### URLLib

urllib2 -> urllib

#### CSV Reader

with open(fn, 'rb') -> with open(fn, 'r')

#### CSV Writer

with open(fn, 'wb') -> with open(fn, 'w')
csv.writer(f, quoting = csv.QUOTE_MINIMAL) -> csv.writer(f, quoting = csv.QUOTE_MINIMAL, lineterminator = os.linesep)



## Frequently asked questions

### All my data is about the same language, do I still have to specify a `Language_ID` for each row?

No, you can use a [virtual column](http://w3c.github.io/csvw/metadata/#use-of-virtual-columns) to supply
the same `Language_ID` for all rows via metadata; append a [column description](http://w3c.github.io/csvw/metadata/#columns) as follows to the `tableSchema.columns` of the relevant table:
```python
    {
        "name": "Language_ID",
        "virtual": true,
        "propertyUrl": "http://cldf.clld.org/v1.0/terms.rdf#glottocode",
        "valueUrl": "abcd1234"
    }
```


### How can I specify tertiary separators for CSV files?

Stricly speaking, CLDF does not have any provision for a tertiary separator. Try to come up with a different encoding :-). Please note that this is not an issue of the CLDF, but a general question about CSV data.

That being said, there is a trick to get something alike to tertiary separators. In general, in CLDF 'extended conformance' you can pretty much do whatever you could do in "raw" [csvw](https://github.com/cldf/csvw), so hack away!

To explain: CSV files structure lines of data into tabular data by means of a separator between fields, typically 
the comma (hence **C**SV). The [Metadata Vocabulary for Tabular Data](http://w3c.github.io/csvw/metadata/)
(which CLDF follows for specification of tables) provides a way to specify secondary separators, i.e.
[separators to be used within a cell](http://w3c.github.io/csvw/metadata/#cell-separator). E.g. the following column specification:

```
{
    "name": "segments",
    "separator": "+",
    "datatype": "base": "string"
}
```

will make sure cell content of the form `abc def+geh` can be split into the list `['abc def', 'geh']`.

An implicit third level of structured content can be achieved for cells of datatype `string` by specifying a regular expression which the values of a cell must match. E.g. the following column specification:

```
{
    "name": "segments",
    "separator": "+",
    "datatype": {
        "base": "string",
        "format": "([\\S]+)( [\\S]+)*"
    }
}
```
will make sure cell content of the form `abc def+geh` can be split into the nested lists `[['abc', 'def'], ['geh']]`.

Tertiary separators may be useful when specifying definitions of categorical values in structure datasets: The
WALS example slightly abuses the `Comment` field in the `ValueTable` to sneak in the human-readable definitions of
the feature values (while giving the numeric values in the `Value` field). Alternatively, one could add a `Domain`
field to the `ParameterTable`, specified as

```
{
    "name": "Domain",
    "separator": ";",
    "datatype": {
        "base": "string",
        "format": "([1-9]:([\\S]+)+"
    },
    "propertyUrl": "http://cldf.clld.org/v1.0/terms.rdf#category"
}
```

and formatting the value definitions as

```
1:Small;2:Moderately small;3:Average;4:Moderately large;5:Large
```

## How to convey the version info for a CLDF dataset?

See #136

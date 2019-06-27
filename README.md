# listlld
Zabbix Low Level Discovery from a list. Currently 2 discovery macros are supported at the same time, {#NAME} and {#VALUE} by default.

##  Usage
```
usage: listlld [-h] [--delimiter DELIMITER]
               [--name NAME] [--value VALUE]
               items

Converts a list of delimited values into a Zabbix LLD JSON for simple list
based Low Level Discoveries.

positional arguments:
  items                 List of items to convert.

optional arguments:
  -h, --help            show this help message and exit
  --delimiter DELIMITER, -d DELIMITER
                        Delimiter character used, default is ",".
  --name NAME, -n NAME  Name of the discovery macro, defaults to "{#NAME}".
                        Only specify the name, this script will add the macro
                        characters "{#...}" for you.
  --value VALUE, -v VALUE
                        Name of the discovery macro, defaults to "{#VALUE}".
                        Only specify the name, this script will add the macro
                        characters "{#...}" for you.
```
### Examples
By specifying `listlld foo,bar,baz` the following output will be generated:

```
{"data": [{"{#NAME}": "foo"}, {"{#NAME}": "bar"}, {"{#NAME}": "baz"}]}
```

Alternatively, items can be specified as well in the form of `listlld foo:123,bar:456,baz:789`, this will result in:

```
{"data": [{"{#VALUE}": "123", "{#NAME}": "foo"}, {"{#VALUE}": "456", "{#NAME}": "bar"}, {"{#VALUE}": "789", "{#NAME}": "baz"}]}
```

The names of the discovery fields can be specified through the `--name` and `--value` arguments.

### Recommended usage in Zabbix
Place the file `listlld` in the [ExternalScripts](https://www.zabbix.com/documentation/4.0/manual/appendix/config/zabbix_server) directory on your Zabbix server (and/or proxies).

On your host or template, create a [usermacro](https://www.zabbix.com/documentation/4.0/manual/config/macros/usermacros) that contains the list of 'discovered' items:

`{$MYLLDLIST}` = `foo:123,bar:456,baz:789`

You can now create a discovery rule on the host/template using an [external check](https://www.zabbix.com/documentation/4.0/manual/config/items/itemtypes/external) with the following key (mind the quotes!):
```
listlld["{$MYLLDLIST}"]
```
This will now allow you to use the `{#NAME}` and `{#VALUE}` discovery macros in your prototypes.

To add or remove 'discovered' items, triggers, etc., simply edit the usermacro.

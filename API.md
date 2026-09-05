# luci-plot API

`luci-plot` currently provides two public interfaces:

1. a declarative JSON-compatible interface
2. an extended JavaScript interface

Both interfaces use the same renderer and support the same basic plotting
features.

## Declarative JSON-compatible interface

This is the preferred interface when plot configuration and data are provided
by a backend, CGI script or API.

The complete plot description can be represented as JSON:

    {
        "plot": {
            "x": {
                "key": "time",
                "type": "time",
                "label": "Time"
            },

            "axes": {
                "temperature": {
                    "position": "left",
                    "unit": "°C"
                }
            },

            "series": [
                {
                    "key": "temperature",
                    "label": "Temperature",
                    "axis": "temperature",
                    "format": "number"
                }
            ]
        },

        "data": [
            {
                "time": 1788498000,
                "temperature": 21.3
            }
        ]
    }
    
The data may alternatively be supplied in vector form using `curves`:

    {
        "plot": {
            ...
        },

        "curves": {
            "time": [
                1788498000,
                1788498060
            ],

            "temperature": [
                21.3,
                21.5
            ]
        }
    }

All curves must contain the same number of values. The `data` and `curves`
forms are equivalent and use the same plot configuration.

Typical browser-side usage:

    const spec = await response.json();
    LuciPlot.render('#plot', spec);

The declarative interface does not contain JavaScript functions and can
therefore be generated and transported as plain JSON.

## Extended JavaScript interface

The original JavaScript interface remains available:

    LuciPlot.render('#plot', data, {
        x: {
            key: 'rpm',
            type: 'number',
            label: 'RPM'
        },

        series: [
            {
                key: 'temperature',
                label: 'Temperature'
            }
        ]
    });

This interface may additionally use JavaScript functions for accessors or
formatters where required.

Example:

    LuciPlot.render('#plot', data, {
        x: {
            accessor: entry => entry.timestamp
        },

        series: [
            {
                accessor: entry => entry.sensor.value,
                formatter: value => value.toFixed(2)
            }
        ]
    });

## Which interface should be used?

Use the declarative interface when:

- data comes from CGI, REST, ubus or another backend
- the plot description should be transported as JSON
- no custom JavaScript functions are required

Use the extended JavaScript interface when:

- data already exists in application-specific JavaScript structures
- custom accessors are useful
- custom formatter functions are required

The declarative interface is intended to cover the normal application case.
The JavaScript interface remains available for more specialized use cases.

## Common plot configuration

### X axis

Common fields:

    key
    type
    label
    format

Supported X axis types currently include:

    index
    number
    time

### Series

Common fields:

    key
    label
    axis
    format
    digits
    minKey
    maxKey

### Y axes

Named Y axes are configured using:

    axes: {
        temperature: {
            position: 'left',
            unit: '°C',
            min: 0,
            max: 100
        }
    }

Up to two independent Y axes are currently supported.

`min` and `max` are optional and may be specified independently.

### Named formatters

The declarative interface currently provides:

    number
    integer
    time
    date
    datetime

The extended JavaScript interface may additionally use formatter functions.

## Missing values

Missing or non-finite values are not plotted.

A missing Y value creates a gap in the corresponding series.

A point with an invalid X value is ignored completely.

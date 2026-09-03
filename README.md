# luci-plot

A lightweight, dependency-free SVG plotting library intended for OpenWrt LuCI.

> **Development status**
>
> This project is currently experimental and under active development.
> The API and internal structure are not considered stable and may change
> significantly.
>
> The current code is primarily a working prototype for experimenting with
> a generic plotting interface. Feedback, ideas and suggestions regarding
> the design are very welcome.

## Motivation

The project started while experimenting with the SVG chart implementation
used in the LuCI LibreSpeed work.

The basic plotting approach was useful, but the goal of `luci-plot` is to
separate plotting from application-specific data structures and data sources.

The plotter therefore operates on generic numeric data and does not know
anything about LibreSpeed, RRD or any particular application.

## Current features

- Dependency-free SVG rendering
- Index-based X axes
- Time-based X axes
- Arbitrary numeric X axes
- Irregularly spaced X values
- Multiple data series
- Optional min/max bands
- Up to two independent Y axes
- Nearest-data-point hover
- Configurable value formatting
- Missing values create gaps instead of connecting across them
- No dependency on a particular data source

## Data sources

`luci-plot` deliberately does not contain data-source-specific code.

For the intended RRD use case, the current plan is to obtain the data using
RRD `fetch` and transform the result into the neutral data representation
expected by the plotter.

The transformation should remain outside the plotting library.

## Example

A minimal numeric plot can be created like this:

    LuciPlot.render('#plot', data, {
        x: {
            key: 'rpm',
            type: 'number',
            label: 'RPM'
        },

        series: [
            {
                key: 'temperature',
                label: 'Temperature',
                unit: '°C',
                digits: 1
            }
        ]
    });

More examples will be added as the API develops.

## Origin

The initial implementation was inspired by the SVG chart implementation
in the LuCI LibreSpeed work by BKPepe / OpenWrt LuCI.

The code has since been generalized to be independent of LibreSpeed and
specific data sources.

## License

Apache License 2.0.

See `LICENSE` for details.

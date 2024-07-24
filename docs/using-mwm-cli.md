[MWM](README.md) > Web Modules CLI

# Sample Web Modules CLI
The Sample Web Modules CLI (`swm-cli`) is the preferred method for creating new modules and partials. Using the CLI Command not only ensures consistency across all our modules, but also is significantly easier to setup.

## Installation

To install `swm-cli`, run the following from within the [Sample Web Modules](https://code.corp.sample.com/wpdev/marketing-web-modules/) project environment.

```bash
> cd marketing-web-modules/bin/cli
> npm i && cd ../../
> npm link
```

## Example Usage

```bash
> swm-cli create module test
        
> swm-cli create partial test
        --views=card list another_view
```
## Commands

### `swm-cli create module <module_name>`

Creates a new Marketing Web Module in `Includes/Modules/`

### `swm-cli create partial <partial_name> [--options]`

Creates a new Marketing Web Partial in `Includes/Partials/`

#### Options

> The options below are only available when creating partials.

##### `--views`

Space separated names of variant views to create. View names will be title cased and presented to the user in the View dropdown.

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.

## Resources
**Note:** For more details on the inner-workings of modules and partials, see [using-swm-modules.md](using-swm-modules.md) or [using-swm-partials.md](using-swm-partials.md).

## License
[MIT](https://choosealicense.com/licenses/mit/)
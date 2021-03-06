# Cleave.js Documentation 

[Documentation](https://github.com/nosir/cleave.js/blob/master/doc/doc.md) > ReactJS component usage

## Playground

- [React JSFiddle](https://jsfiddle.net/nosir/gLLsrxxf/)

## Babel usage

Cleave.js uses ES6 spread / rest feature, and we recommend using Babel compiler to transfer ES6 code.

However, if for some reason you would like to just refer to the final bundled script, check the [legacy way](#legacy-way).

First, install babel presets:

```bash
npm install --save babel-preset-es2015 babel-preset-react babel-preset-stage-0
```

For `Browserify`, also do:

```bash
npm install --save babelify
```

For `Webpack`, also do:

```bask
npm install --save babel-core babel-loader
```

Second, add `.babelrc` to your project root:

```json
{
  "presets": ["es2015", "stage-0", "react"]
}
```

Now in your ReactJS app:

```js
import React from 'react';
import ReactDOM from 'react-dom';

import Cleave from 'cleave.js/react';
import CleavePhone from 'cleave.js/dist/addons/cleave-phone.{country}';
```

And define the component:

```js
class MyComponent extends React.Component {
    constructor(props, context) {
        super(props, context);

        this.state = {
            creditCardRawValue: '',
            phoneRawValue:      '',
            customRawValue:     ''
        };
        
        this.onCreditCardChange = this.onCreditCardChange.bind(this);
        this.onPhoneChange = this.onPhoneChange.bind(this);
        this.onCustomChange = this.onCustomChange.bind(this);
    }

    onCreditCardChange(event) {
        this.setState({creditCardRawValue: event.target.rawValue});
    }

    onPhoneChange(event) {
        this.setState({phoneRawValue: event.target.rawValue});
    }

    onCustomChange(event) {
        this.setState({customRawValue: event.target.rawValue});
    }

    render() {
        return (
            <div>
                <Cleave placeholder="Enter your credit card number" options={{creditCard: true}}
                        onChange={this.onCreditCardChange}/>

                <Cleave className="css-phone" options={{phone: true, phoneRegionCode: 'AU'}}
                        onChange={this.onPhoneChange}/>

                <Cleave options={{blocks: [4,3,3], delimiter: '-', numericOnly: true}}
                        onChange={this.onCustomChange}/>

                <div>
                    <p>credit card: {this.state.creditCardRawValue}</p>
                    <p>phone: {this.state.phoneRawValue}</p>
                    <p>custom: {this.state.customRawValue}</p>
                </div>
            </div>
        );
    }
}

ReactDOM.render(<MyComponent/>, document.getElementById('content'));
```

### Webpack and Browserify config

#### Webpack

```js
loaders: [
    {
        test: ...,
        exclude: /node_modules\/(?!cleave.js)/,
        loader: 'babel',
        query: {
            presets: ['es2015', 'react', 'stage-0']
        }
    }
]
```

#### Browserify

```js
browserify(...).transform('babelify', {presets: ['es2015', 'react', 'stage-0']})...
```

## Legacy way

If for some reason you would like to just refer to the final bundled script (e.g. your project doesn't support ES6 compiling), you can include cleave.js like this.

It will also expose `Cleave` variable to global:

```js
import Cleave from 'cleave.js/dist/cleave-react';
import CleavePhone from 'cleave.js/dist/addons/cleave-phone.{country}';
```

or

```js
var Cleave = require('cleave.js/dist/cleave-react');
var CleavePhone = require('cleave.js/dist/addons/cleave-phone.{country}');
```

And define the component:

```js
var MyComponent = React.createClass({
    onCreditCardChange: function (event) {
        // formatted pretty value
        console.log(event.target.value);
        
        // raw value
        console.log(event.target.rawValue);
    },

    render: function () {
        return (
            <Cleave placeholder="Enter your credit card number"
                options={{creditCard: true}}
                onChange={this.onCreditCardChange} />
        );
    }
});
```

## Shim

Alternatively you can shim `Cleave.js` module. In your html:

```html
<script src="react/dist/react.min.js"></script>
<script src="react-dom/dist/react-dom.min.js"></script>

<script src="cleave.js/dist/cleave-react.min.js"></script>
<script src="cleave.js/dist/addons/cleave-phone.{country}.js"></script>
```

The global expose name is `Cleave`

Then config your shim with [browserify-shim](https://github.com/thlorenz/browserify-shim) or [webpack](http://webpack.github.io/docs/shimming-modules.html)

## How does it work?

As you can see, here you simply use `<Cleave/>` as a normal `<input/>` field 

- Attach HTML `<input/>` attributes

    During rendering, it spreads all the input attributes down and apply formatting to the real input field.

- Pass in the custom `options` prop

    See full [options](https://github.com/nosir/cleave.js/blob/master/doc/options.md) documentation

- Add ReactJS `onChange` event listener

    Internally it interpolates native React `onChange` and `onKeyDown` events, does all the formatting magic and triggers the event callback.
    
    The only thing getting added to the event object is the `rawValue` (delimiter stripped value) of the input field, that you might be interested in.
    
    In the example above, we get the `rawValue` and update its `state` in handler, eventually it will be passed to backend or `store` layer.

## References

- browserify: http://browserify.org/
- babelify: https://github.com/babel/babelify
- browserify-shim: https://github.com/thlorenz/browserify-shim
- webpack shim: http://webpack.github.io/docs/shimming-modules.html
- webpack: http://webpack.github.io/
- babel: http://babeljs.io/
- babel-loader: https://github.com/babel/babel-loader

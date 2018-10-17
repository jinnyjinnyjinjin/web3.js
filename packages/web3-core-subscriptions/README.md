# web3-core-subscriptions

This is a sub package of [web3.js][repo].
The subscriptions package is used within some [web3.js][repo] packages.

## Installation

### Node.js

```bash
npm install web3-core-subscriptions
```

### In the Browser

Build running the following in the [web3.js][repo] repository:

```bash
npm run-script build-all
```

Then include `dist/web3-core-subscriptions.js` in your html file.
This will expose the `Web3Subscriptions` object on the window object.


## Usage

```js
// in node.js

// Dependencies
var ProvidersPackage = require('web3-providers');
var AbstractWeb3Module = require('web3-package').AbstractWeb3Module;
var SubscriptionsFactory = require('web3-core-subscriptions').SubscriptionsFactory;

// Create an object of type AbstractWeb3Module
/**
 * @param {Object|String} provider
 * @param {ProvidersPackage} providersPackage
 * @param {SubscriptionsFactory} subscriptionsFactory
 * 
 * @constructor
 */
function Module (
    provider,
    providersPackage,
    subscriptionsFactory
) {
    AbstractWeb3Module.call(
        this,
        provider,
        providersPackage
    );
    
    this.subscriptionsFactory = subscriptionsFactory;
}

/**
 * Returns expected subscription
 * 
 * @method subscribe
 * 
 * @param {String} subscriptionMethod
 * @param {Method} callback
 * 
 * @callback callback callback(error, result)
 * @returns {Subscription}
 */
Module.prototype.subscribe = function (subscriptionMethod, callback) {
    switch (subscriptionMethod) {
        case 'newBlockHeaders':
            return this.subscriptionsFactory
                .createNewHeadsSubscription(this)
                .subscribe(callback);
        case 'pendingTransactions':
            return this.subscriptionsFactory
                .createNewPendingTransactionsSubscription(this)
                .subscribe(callback);
        default:
            throw Error('Unsupported subscription: ' + subscriptionMethod);
    }
};

// Inherit from AbstractWeb3Module
Module.prototype = Object.create(AbstractWeb3Module.prototype);
Module.prototype.constructor = Module;

// Instantiate anything
var module = new Module(
    ProvidersPackage.detect(),
    ProvidersPackage,
    new SubscriptionsFactory()
);

// Subscribe
module.subscribe('newBlockHeaders', function(){ ... });
```

[docs]: http://web3js.readthedocs.io/en/1.0/
[repo]: https://github.com/ethereum/web3.js


# karma-bench

> A [Karma](http://karma-runner.github.io/) plugin to run [Benchmark.js v2](http://benchmarkjs.com/)

Based on [karma-benchmark](https://github.com/JamieMason/karma-benchmark) plugin, but it don't break tests functions context

## Installation

```shell
npm install karma-bench --save-dev
```

## Karma Configuration

### Reporting results on the command line

To see jsPerf style results on the command line, install [`karma-benchmark-reporter`](https://github.com/lazd/karma-benchmark-reporter):

```shell
npm install karma-benchmark-reporter --save-dev
```

Then, in **karma.conf.js**, add `benchmark` to the list of reporters:

```javascript
module.exports = function(config) {
    config.set({
        // Other Karma config here...
        frameworks: ['bench'],
        reporters: ['benchmark']
    });
};
```

Run Karma:

```shell
karma start
```

Then, you'll then see output that looks like:

```
Chrome 51.0.2704 (Mac OS X 10.11.5)  Array iteration: util.each at 19356910 ops/sec
Chrome 51.0.2704 (Mac OS X 10.11.5)  Array iteration: Array.forEach at 2567531 ops/sec
Chrome 51.0.2704 (Mac OS X 10.11.5)  Array search: util.contains at 12635982 ops/sec
Chrome 51.0.2704 (Mac OS X 10.11.5)  Array search: Array.indexOf at 5828437 ops/sec
Chrome 51.0.2704 (Mac OS X 10.11.5)
  Array iteration: util.each at 19356910 ops/sec (7.54x faster than Array.forEach)
  Array search: util.contains at 12635982 ops/sec (2.17x faster than Array.indexOf)
```

See [`karma-benchmark-example`](https://github.com/lazd/karma-benchmark-example) for a full example.

### Timeouts

As large suites of Benchmarks take a long time to run, you _may_ need to increase Karma's timeout from it's default of 60000.

```javascript
captureTimeout: 60000
```

## Writing Benchmarks

Suites and benchmarks are defined using a wrapper for Benchmark.js in the form of the `suite` and `benchmark` globals.

### Typical

In this example, a suite is defined that pits `_.each` against the native `Array.forEach` method:

```javascript
suite('Array iteration', function() {
    benchmark('_.each', function() {
        _.each([1, 2, 3], function(el) {
            return el;
        });
    });

    benchmark('native forEach', function() {
        [1, 2, 3].forEach(function(el) {
            return el;
        });
    });
});
```

### Suite options

Suite options are the same as in Benchmark.js.

See the [Benchmark.js Suite constructor API docs](http://benchmarkjs.com/docs#Suite) for a full list of options.

```javascript
suite('Array iteration', function() {
    benchmark('_.each', {
        fn: function () {
            _.each(this.list, function(number) {
                return number;
            });
        },
        setup: function() {
            this.list = [5, 4, 3];
        },
        teardown: function() {
            this.list = null;
        }
    });
}, {
    onCycle: function(event) {
        var suite = this;
        var benchmark = event.target;
        console.log('Cycle completed for ' + suite.name + ': ' + benchmark.name);
    }
});
```


### Benchmark options

Benchmark options are the same as in Benchmark.js.

See the [Benchmark.js Benchmark constructor API docs](http://benchmarkjs.com/docs#Benchmark) for a full list of options.

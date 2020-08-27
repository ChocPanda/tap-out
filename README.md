# tap-out (patched)

<p align="center">
  <a href="https://circleci.com/gh/ChocPanda/tap-out" alt="CircleCI">
    <img src="https://circleci.com/gh/ChocPanda/tap-out/tree/master.svg?style=shield"/>
  </a>
  <a href="https://david-dm.org/ChocPanda/tap-out" alt="dependencies">
    <img src="https://david-dm.org/ChocPanda/tap-out.svg"/>
  </a>
  <a href="https://snyk.io/test/github/ChocPanda/tap-out?targetFile=package.json" alt=KnownVulnerabilities>
    <img src="https://snyk.io/test/github/ChocPanda/tap-out/badge.svg?targetFile=package.json">
  </a>
  <a href="https://www.npmjs.com/package/@chocpanda/tap-out" alt="npm">
    <img alt="npm" src="https://img.shields.io/npm/v/@chocpanda/tap-out.svg?label=npm%40latest&style=popout">
  </a>
  <a href="https://github.com/ChocPanda/tap-out/blob/master/LICENSE" alt="license">
    <img src="https://img.shields.io/github/license/ChocPanda/tap-out.svg?style=popout"/>
  </a>
  <a href="http://commitizen.github.io/cz-cli/" alt="Commitizen friendly" >
    <img src="https://img.shields.io/badge/commitizen-friendly-brightgreen.svg"/>
  </a>
  <a href="https://github.com/semantic-release/semantic-release" alt="semantic-release">
    <img src="https://img.shields.io/badge/%20%20%F0%9F%93%A6%F0%9F%9A%80-semantic--release-e10079.svg"/>
  </a>
</p>



> Node 14 (or 13 --harmony) is required in this fork

A different tap parser

## Install

```
npm install tap-out --save
```

## Usage

**CLI**

```
$ something-that-produces-tap | tap-out
{
  tests: [
    { name: 'is true', number: 1, raw: '# is true', type: 'test' }
  ],
  asserts: [
    { name: 'true value', number: 1, ok: true, raw: 'ok 1 true value', test: 1, type: 'assert' },
    { name: 'true value', number: 2, ok: true, raw: 'ok 2 true value', test: 1, type: 'assert' }
  ],
  versions: [],
  results: [],
  comments: [],
  plans: [{ type: 'plan', raw: '1..2', from: 1, to: 2, skip: false }],
  pass: [
    { name: 'true value', number: 1, ok: true, raw: 'ok 1 true value', test: 1, type: 'assert' },
    { name: 'true value', number: 2, ok: true, raw: 'ok 2 true value', test: 1, type: 'assert' }
  ],
  fail: [],
  errors: []
}
```

**API**

```js
var tapOut = require('tap-out');

var t = tapOut(function (output) {

  console.log(output);
});

t.on('assert', function (assert) {

	// Do something
});

process.stdin.pipe(t);
```

## Methods

### var t = tapOut(function (err, output) {})

Returns a stream that emits events with various TAP data. Takes a callback which is called when all parsing is done.

## Events

### t.on('output', function (output) {})

All output after all TAP data is parsed.

Example output

```js
{
  tests: [
    { name: 'is true', number: 1, raw: '# is true', type: 'test' }
  ],
  asserts: [
    { name: 'true value', number: 1, ok: true, raw: 'ok 1 true value', test: 1, type: 'assert' },
    { name: 'true value', number: 2, ok: true, raw: 'ok 2 true value', test: 1, type: 'assert' }
  ],
  results: [],
  versions: [],
  comments: [],
  fail: [],
  pass: [
    { name: 'true value', number: 1, ok: true, raw: 'ok 1 true value', test: 1, type: 'assert' },
    { name: 'true value', number: 2, ok: true, raw: 'ok 2 true value', test: 1, type: 'assert' }
  ],
}
```

### t.on('test', function (test) {})

Parsed test object with details.

* `type` - value will always be `test`
* `name` - name of the test
* `raw` - the raw output before it was parsed
* `number` - the number of the test

```js
{
  type: 'test',
  name: 'is true',
  raw: '# is true',
  number: 1
}
```

### t.on('assert', function (assertion) {})

Parsed assert object details.

* `type` - this will always be `assert`
* `name` - the name of the assertion
* `raw` - the raw output before it was parsed
* `number` - the number of the assertion
* `ok` - whether the assertion passed or failed
* `test` - the number of the test this assertion belongs to

```js
{
  name: 'true value',
  number: 1,
  ok: true,
  raw: 'ok 1 true value',
  test: 1,
  type: 'assert'
}
```

### t.on('version', function (version) {})

Parsed version data.

* `type` - this will always be `version`
* `raw` - the raw output before it was parsed

```js
{
  raw: 'TAP version 13',
  type: 'version'
}
```

### t.on('result', function (result) {})

Parsed test result data for tests, pass, fail.

* `type` - this will always be `result`
* `name` - the name of the result
* `raw` - the raw output before it was parsed
* `count` - the number of tests related to this result

Tests

```js
{
  count: '15',
  name: 'tests',
  raw: '# tests 15',
  type: 'result'
}
```

Pass

```js
{
  count: '13',
  name: 'pass',
  raw: '# pass  13',
  type: 'result'
}
```

Fail

```js
{
  count: '2',
  name: 'fail',
  raw: '# fail  2',
  type: 'result'
}
```

### t.on('pass', function (assertion) {})

Parsed assertion that has passed with details. The assertion formate is the same as the [`assert`](#tonassert-function-assertion-) event.

### t.on('fail', function (assertion) {})

Failed assertion that has passed with details. The assertion formate is the same as the [`assert`](#tonassert-function-assertion-) event.

### t.on('comment', function (comment) {})

Generic output like `console.log()` in your tests.

* `type` - this will always be `comment`
* `raw` - the raw output before it was parsed
* `test` - the number of the test this comment belongs to

```js
{
  type: 'comment',
  raw: 'this is a console log',
  test: 1
}
```

## Run Tests

```
git clone git@github.com:scottcorgan/tap-out.git && cd tap-out
npm install
npm test
```

## Contributions

See our [contributing doc](./CONTRIBUTING.md), be sure to checkout the [code of conduct](./CONTRIBUTING.md#code-of-conduct)

### Conventional Commits

This project used [conventional commits](https://www.conventionalcommits.org/en/v1.0.0-beta.3/#specification) to manage versions and releases of the library therefore when making a commit please use `npm commit <COMMIT_PARAMETERS>` and this will guide you through writing a conventional commit message which can be understood work with the ci pipeline

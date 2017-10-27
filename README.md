# test-logic

> The form data validation library.Does not contain UI.

[![Build Status](https://api.travis-ci.org/onface/test-logic.svg)](https://travis-ci.org/onface/test-logic)
[![NPM version](https://img.shields.io/npm/v/test-logic.svg?style=flat)](https://npmjs.org/package/test-logic)
[![NPM downloads](http://img.shields.io/npm/dm/test-logic.svg?style=flat)](https://npmjs.org/package/test-logic)

[![Saucelabs](https://saucelabs.com/browser-matrix/test-logic.svg)](https://saucelabs.com/u/test-logic)

`test-logic` 表单校验逻辑层封装


```js
var TestLogic = require('test-logic')
// or
import TestLogic from "test-logic"
```

## 特性

1. `async` **异步**，函数异步校验。
2. `every` **贪心**，校验每一项规则。
3. `queue` **队列**，前一个值校验完成后再进行下一个值的校验。
4. `prior` **优先**，支持多种required并存。

搭配 [form-logic](https://github.com/onface/form-logic) 使用效果更佳。

> form-logic 实现了表单数据双向绑定和校验时机管理。

### 基础用法

````js
test.checkAll(
    [
        {
            label: '邮箱',
            value: 'mail@qq.com',
            test: ['required', 'email']
        },
        {
            label: '密码',
            value: 'a123456',
            test: [
                'required',
                {
                    regexp: /123456/,
                    be: false,
                    msg: '{{label}}不允许包含123456'
                }
            ]
        }
    ],
    {
        finish: function (fail, errors, data) {
            if (fail) {
                console.error('basci', errors[0].msg)
                // 密码不允许包含123456
            }
            else {
                console.info('basci', 'pass')
            }
        }
    }
)
````

### 异步

````js
test.checkAll(
    [
        {
            label: '邮箱',
            value: 'mail@qq.com',
            test: [
                'required',
                'email',
                function hasEmail(pass, fail, value) {
                    setTimeout(function mockAJAX() {
                        if (value === 'mail@qq.com') {
                            fail('{{label}}已注册过')
                        }
                        else {
                            pass()
                        }
                    }, 400)
                }
            ]
        }
    ],
    {
        finish: function (fail, errors, data) {
            if (fail) {
                console.error('async', errors[0].msg)
                // 邮箱已经注册过
            }
            else {
                console.info('async', 'pass')
            }
        }
    }
)
````

### 贪心

```js
test.checkAll(
    [
        {
            label: '邮箱',
            value: 'admin',
            test: [
                'email',
                {
                    regexp: /admin/,
                    be: false,
                    msg: '{{label}}不允许存在 admin'
                }
            ],
            // 规则 every
            // 校验遇到错误依然进行后续校验（默认为 false)
            every: true
        },
        {
            label: '密码',
            value: 'abcdefg',
            test: [
                function (pass, fail, value) {
                    console.log('进入密码校验')
                    if (value !== '123456') {
                        fail('密码错误')
                    }
                    else {
                        pass()
                    }
                }
            ]
        }
    ],
    {
        // 表单项 every
        // 校验遇到错误则不进行后续校验 (默认为 true)
        every: false,
        finish: function (fail, errors, data) {
            if (fail) {
                console.error('every', errors[0].msg)
                console.error(errors)
                console.log(data)
            }
        }
    }
)
```

## Example

[![Preview](./example/preview.png)](http://onface.github.io/test-logic/example)

## Online documentation

[Online](http://onface.github.io/test-logic)

## Change log

[CHANGELOG](./CHANGELOG.md)


## Maintainers

<table>
  <tbody>
    <tr>
    <td align="center">
      <a href="https://github.com/grifree"><img width="150 height="150" src="https://github.com/grifree.png?s=150" /></a>
      <br>
      <a href="https://github.com/nimojs">Grifree</a>
    </td>
    <td align="center">
    <a href="https://github.com/nimojs"><img width="150 height="150" src="https://github.com/nimojs.png?s=150" /></a>
    <br>
    <a href="https://github.com/nimojs">NimoChu</a>
    </td>
    <tr>
  <tbody>
</table>

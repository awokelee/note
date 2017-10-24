# ES6

## Promise

### 什么是异步

A执行完执行B
传统：回调函数、事件触发

### Promise的作用

解决异步操作问题

### Promise的基本用法

#### 传统写法

```JS
{
    // 基本定义
    let ajax = function (callback) {
        console.log('执行')
        setTimeout(function () {
            callback && callback.call()
        }, 1000)
    }
    ajax(function () {
        console.log('timeout1')
    })
}
```

#### resolve

#### reject

#### then

```js
{
    let ajax = function () {
        console.log('执行3')
        return new Promise(function (resolve, reject) {
            setTimeout(function () {
                resolve()
            }, 1000)
        })
    }

    ajax().then(function () {
        return new Promise(function (resolve, reject) {
            setTimeout(function () {
                resolve()
            }, 2000);
        })
    }).then(function () {
        console.log('timeout3')
    }).catch(function (err) {
        console.log('catch')
    })
}
```

#### Promise.all

```JS
{
    // 所有图片加载完再添加到页面
    function loadImg(src) {
        return new Promise((resolve, reject) => {
            let img = document.createElement('img')
            img.src = src

            img.onload = function () {
                resolve(img)
            }
            img.onerror = function (err) {
                reject(err)
            }
        })
    }

    function showImgs(imgs) {
        imgs.forEach(function (img) {
            document.body.appendChild(img)
        })
    }

    Promise.all([
        loadImg('http://localhost.com/1.jpg'),
        loadImg('http://localhost.com/2.jpg'),
        loadImg('http://localhost.com/3.jpg'),
    ]).then(showImgs)
}
```

#### Promise.race

```js
{
    // 有一个完成就添加到页面
    function loadImg(src) {
        return new Promise((resolve, reject) => {
            let img = document.createElement('img')
            img.src = src

            img.onload = function () {
                resolve(img)
            }
            img.onerror = function (err) {
                reject(err)
            }
        })
    }

    function showImgs(img){
        let p = document.createElement('p')
        p.appendChild(img)
        document.body.appendChild(p)
    }

    Promise.race([
        loadImg('http://localhost.com/1.jpg'),
        loadImg('http://localhost.com/2.jpg'),
        loadImg('http://localhost.com/3.jpg'),
    ]).then(showImgs)
}
```

## 字符串扩展

### Unicode表示法

```JS
{
    // a a
    console.log('a', `\u0061`)
    // s ₻7, 因为 20BB7 大于 0XFFFF，20BB解析为₻ 最后结果为₻7
    console.log('s', `\u20BB7`)
    // s �
    console.log('s', `\u{20BB7}`)
    let s = '�'
}

{
    let s = `\u{20BB7}`
    // 2, 四字节为1 大于四字节 2
    console.log('length', s.length)
    // �
    console.log('0', s.charAt(0))
    // �
    console.log('0', s.charAt(1))
    // 55362
    console.log('at0', s.charCodeAt(0))
    // 57271
    console.log('at1', s.charCodeAt(1))

    // 
    let s1 = `\u{20BB7}a`
    // 3
    console.log('length', s1.length)
    // 134071
    console.log('0', s1.codePointAt(0))
    // 20bb7
    console.log('code0', s1.codePointAt(0).toString(16))
    // 57271
    console.log('code0', s1.codePointAt(1))
    // 97
    console.log('code0', s1.codePointAt(2))

}

{
    // ஷ
    console.log(String.fromCharCode('0x20bb7'))
    // �
    console.log(String.fromCodePoint('0x20bb7'))
}
```
### 遍历接口

```JS
{
    //  � � a b c
    let str = `\u{20bb7}abc`
    for (let i = 0; i < str.length; i++) {
        console.log('es5', str[i])
    }
    // � a b c
    for (let code of str) {
        console.log('es6', code)
    }
}
```

### 模板字符串

```js
{
    let name = 'list'
    let info = 'hello world'
    let m = `i am ${name}, ${info}`
    // i am list, hello world
    console.log(m)
}
```

### includes startsWith endsWith

```js 
{
    let str = "string"
    // 判断包含，true
    console.log('includes', str.includes("s"))
    console.log('includes', str.startsWith("s"))
    console.log('includes', str.endsWith("ng"))
    
}

{
    let str = "abc"
    // abcabc
    console.log(str.repeat(2))
}
```

### padStart padEnd

```JS
{   // ES7的 补白
    // 01
    console.log('1'.padStart(2,0))
    // 10
    console.log('1'.padEnd(2,0))
}
```

### 标签模板

```JS
{
    // 防止 XSS 或者多余字符串
    let user = {
        name: 'list',
        info: 'hello world'
    }
    // [ 'i am ', ', ', '' ] 'list' 'hello world'
    // console.log(abc`i am ${user.name}, ${user.info}`)

    // i am ,, ,listhello world
    function abc(s, v1, v2) {
        console.log(s, v1, v2)
        return s + v1 + v2
    }
}

```

### raw

```JS
{   
    // Hi\n3
    console.log(String.raw`Hi\n${1+2}`)
    /**
     *  Hi
     *  3
     */
    console.log(`Hi\n${1+2}`)
}
```

## Iterator 和 for...of

### iterator

```JS
{
    let arr = ['hello', 'world']
    let map = arr[Symbol.iterator]()
    // { value: 'hello', done: false }
    console.log(map.next())
    // { value: 'world', done: false }
    console.log(map.next())
    // { value: undefined, done: true }
    console.log(map.next())
}
```

### 实现 iterator

```JS
{
    // 实现iterator接口
    let obj = {
        start: [1, 3, 2],
        end: [7, 9, 8],
        [Symbol.iterator]() {
            let self = this
            let index = 0
            let arr = self.start.concat(self.end)
            let len = arr.length
            return {
                next() {
                    if (index < len) {
                        return {
                            value: arr[index++],
                            done: false
                        }
                    } else {
                        return {
                            value: arr[index++],
                            done: true
                        }
                    }
                }

            }
        }
    }
    for (let key of obj) {
        console.log(key)
    }
}
```

### for...of

```JS
{
    let arr = ['hello', 'world']
    for (let value of arr) {
        console.log('value', value)
    }
}
```

## Generator （比Promise更高级）

### 基本定义

```JS
{
    // generator 基本定义
    let tell = function* () {
        yield 'a'
        yield 'b'
        yield 'c'
    }

    let ke = tell()
    // generator 返回的就是iterator接口
    // { value: 'a', done: false }
    console.log(ke.next())
    // { value: 'b', done: false }
    console.log(ke.next())
    // { value: 'c', done: false }
    console.log(ke.next())
    // { value: undefined, done: true }
    console.log(ke.next())
}
```

### generator与iterator关系

```JS
{
    // generator与iterator接口关系
    let obj = {}
    obj[Symbol.iterator] = function* () {
        yield 1
        yield 2
        yield 3
    }
    // 1 2 3
    for (let value of obj) {
        console.log(value)
    }
}
```

### 状态机

```JS
{
    // 状态机
    let state = function* () {
        while (1) {
            yield 'A'
            yield 'B'
            yield 'C'
        }
    }
    let status = state()
    // A B C A B 
    console.log(status.next())
    console.log(status.next())
    console.log(status.next())
    console.log(status.next())
    console.log(status.next())
}



{   // async await 是 generator 的语法糖
    let state = async function () {
        while (1) {
            await 'A'
            await 'B'
            await 'C'
        }
    }
    let status = state()
    // A B C A B 
    console.log(status.next())
    console.log(status.next())
    console.log(status.next())
    console.log(status.next())
    console.log(status.next())
}
```

### 抽奖应用

```JS
{
    // generator应用：抽奖
    let draw = function (count) {
        // 具体抽奖逻辑
        console.info(`剩余${count}次`)
    }
    let residue = function* () {
        while (count > 0) {
            count--
            yield draw(count)
        }
    }
    let star = residue(5)
    let btn = document.createElement('button')
    btn.id = 'start'
    btn.textContent = '抽奖'
    document.body.appendChild(btn)
    document.getElementById('start').addEventListener('click', function () {
        star.next()
    }, false)
}
```

### 长轮询

```JS
{
    // 长轮询
    let ajax = function* () {
        yield new Promise(function (resolve, reject) {
            setTimeout(function () {
                // 这里写你调后台
                resolve({ code: 0 })
            }, 200)
        })
    }

    let pull = function () {
        let generator = ajax()
        let step = generator.next()
        step.value.then(function (d) {
            if (d.code != 0) {
                setTimeout(function () {
                    console.log('wait')
                    pull()
                }, 1000)
            } else {
                console.log(d)
            }
        })
    }
    pull()
}
```

## decorator（修饰器）

### 概念

是一个函数，修改行为（扩展类功能），修改类的行为（只在类起作用）

### 需要安装插件

`npm install babel- plugin - transform - decorators - legacy --save-dev`

```JS
.babelrc 添加
{
    "plugins":["transform-decorators-legacy"]
}
```

### 案例

```JS
{
    let readonly = function (tartget, name, descriptor) {
        descriptor.writable = false
        return descriptor
    }

    class Test {
        @readonly
        time() {
            return '2017-10-24'
        }
    }

    let test = new Test()

    // 会报错，不让修改
    /*
        test.time=function(){
            console.log('rest time')
        }
    */

    console.log(test.time())
}

{
    let typename = function (tartget, name, descriptor) {
        tartget.myname = 'hello'
    }

    @typename
    class Tes {

    }
    // 类修饰符 hello
    console.log('类修饰符'，Test.myname)
}
```

### 第三方库 core-decorators

`npm install core-decorators`

### 案例-埋点日志

```JS
{
    let log = (type) => {
        return function (tartget, name, descriptor) {
            let src_method = descriptor.value
            descriptor.value = (...arg) => {
                src_method.apply(tartget, arg)
                console.log(`log ${type}`)
            }
        }
    }

    class AD {
        @log('show')
        show(){
            console.log('ad is show')
        }
        
        @log('click')
        click(){
            console.log('ad is click')
        }
    }

    let ad = new AD()
    ad.show()
    ad.click()

    // 依次打印
    // ad is show
    // log show
    // ad is click
    // log click
}

```

## ES6 模块化
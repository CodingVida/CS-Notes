[TOC]

---



### 并发请求

> 请实现如下的函数，可以批量请求数据，所有的URL地址在urls参数中，同时可以通过max参数 控制请求的**并发度**。当所有的请求结束后，需要执行**callback回调**。发请求的函数可以直接使用**fetch**。
>
> `function sendRequest (urls: string[], max: number, callback: () => void) {}`
>
> tip：fetch函数返回的是一个promise，promise对象在实例化的时候就已经开始执行了。



````JavaScript
function sendRequest (urls, max, callback) {
    const fetchArr = [];
    const res = [];
    let i = 0;

    function fetchFunction () {
        if (i === urls.length) {
            return;
        }

        const fn = fetch(urls[i++]);
        fetchArr.push(fn);
        fn.then((r) => {
            res.push(r)
            fetchArr.splice(fetchArr.indexOf(fn), 1);
        });

        let promise = Promise.resolve();
        if (fetchArr.length >= max) {
            promise = Promise.race(fetchArr);
        }
        return promise.then(() => fetchFunction());
    }

    fetchFunction().then(() => Promise.all(fetchArr).then(() => callback(res)));
}
````





### 版本号比较

> todo: 校验是否满足 语义化 的格式要求；参考npm包 `compare-versions`

```JavaScript

function split(v) {
    const version = v.replace(/^v/, '');
    const patchIndex = indexOrEnd(version, '-');
    const arr = version.slice(0, patchIndex).split('.');
    arr.push(version.slice(patchIndex + 1));
    return arr;
}

function indexOrEnd(s, char) {
    return s.indexOf(char) > -1 ? s.indexOf(char) : s.length;
}

function parse(s) {
    return isNaN(Number(s)) ? s : Number(s);
}

function compare (v1, v2) {
    const p1 = split(v1);
    const p2 = split(v2);
    for (let i = 0; i < Math.max(p1.length, p2.length) - 1; i++) {
        const n1 = parseInt(p1[i] || 0, 10);
        const n2 = parseInt(p2[i] || 0, 10);

        if (n1 > n2) return 1;
        if (n1 < n2) return -1;
    }

    let sp1 = p1[p1.length - 1];
    let sp2 = p2[p2.length - 1]

    if (sp1 && sp2) {
        sp1 = sp1.split('.').map(parse);
        sp2 = sp2.split('.').map(parse);

        for (let i = 0; i < Math.max(sp1.length, sp2.length); i++) {
            if (typeof sp1[i] === 'undefined' || typeof sp1[i] === 'number' && typeof sp2[i] === 'string') return -1;
            if (typeof sp2[i] === 'undefined' || typeof sp2[i] === 'number' && typeof sp1[i] === 'string') return 1;

            if (sp1[i] > sp2[i]) return 1;
            if (sp1[i] < sp2[i]) return -1;
        }

    } else if (sp1 || sp2) {
        return sp1 ? -1 : 1;
    }
     
    return 0;
}

console.log(compareversions('1.0.1', '1.0.2'));
console.log(compareversions('1.0.1', '1.2.0'));
console.log(compareversions('1.0.0-1.0.1', '1.0.0'));
console.log(compareversions('1.0.0-beta', '1.0.0-apha.1.0.3'));
console.log(compareversions('1.0.0-beta.1.2.3', '1.0.0-beta.1.3.0'));
```



### 对象扁平化

```JavaScript
function isObj (o) {
    return Object.prototype.toString.call(o).split(' ')[1].slice(0, -1).toLowerCase() === 'object';
}

function flatten (target) {
    const delimiter = '.';
    const output = {};
    
    function step (obj, prefix = '') {
        Object.keys(obj).forEach((key) => {
            let newPre = prefix ? (prefix + delimiter + key) : key;
            
            if (isObj(obj[key])) {
                step(obj[key], newPre)
            } else {
                output[newPre] = obj[key];
            }
        });
    }
    step(target);
    return output;
}
```





### 数组扁平化/去重

```JavaScript
function flat(arr) {
	while (arr.some(Array.isArray)) {
		arr = [].concat(...arr);
    }
    return arr;
}

function unique (arr) {
    return arr.filter((item, index) => arr.indexOf(item) === index); // 取最前边的
}

// reduce for unique array
let myArray = ['a', 'b', 'a', 'b', 'c', 'e', 'e', 'c', 'd', 'd', 'd', 'd']
let myOrderedArray = myArray.reduce(function (accumulator, currentValue) {
  if (accumulator.indexOf(currentValue) === -1) {
    accumulator.push(currentValue)
  }
  return accumulator
}, [])
```


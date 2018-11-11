# 《深入浅出Rxjs》

## 第1章　函数响应式编程
函数式、响应式的定义、优点。

## 第2章　RxJS⼊门
* 创造Observable：
```javascript
import {Observable} from 'rxjs/Observable';

const onSubscribe = observer => {
    //next方法给流里面塞数据
    observer.next(1);
    observer.next(2);
    observer.next(3);
};

const source$ = new Observable(onSubscribe);

const theObserver = {
    next: item => console.log(item)
}

//subscribe方法会触发onSubscribe
source$.subscribe(theObserver);
```

* Observable的完结：

```javascript
import {Observable} from 'rxjs/Observable';

const onSubscribe = observer => {
    let number = 1;
    const handle = setInterval(() => {
        observer.next(number++);
        if (number > 3) {
            clearInterval(handle);

            //会触发theObserver的complete
            observer.complete();
        }
    }, 1000);
};

const source$ = new Observable(onSubscribe);

const theObserver = {
    next: item => console.log(item),
    complete: () => console.log('No More Data')
}

source$.subscribe(theObserver);
```

* Observable的出错处理：

```javascript
import {Observable} from 'rxjs/Observable';

const onSubscribe = observer => {
    observer.next(1);
    observer.error('Someting Wrong');
    observer.complete();
};
const source$ = new Observable(onSubscribe);
const theObserver = {
    next: item => console.log(item),
    error: err => console.log(err),
    complete: () => console.log('No More Data'),
}
source$.subscribe(theObserver);
```

* Observer的简单形式：

```javascript
// onError, onComplete是可选的
source$.subscribe(
    item => console.log(item),
    err => console.log(err),
    () => console.log('No More Data')
);
```

* 退订Observable：

```javascript
const onSubscribe = observer => {
    let number = 1;
    const handle = setInterval(() => {
        observer.next(number++);
    }, 1000);

    return {
        unsubscribe: () => {
            clearInterval(handle);
        }
    };
};

const source$ = new Observable(onSubscribe);
const subscription = source$.subscribe(item => console.log(item));

setTimeout(() => {
    //会触发onSubscribe里面返回的unsubscribe钩子
    subscription.unsubscribe();
}, 3500);

```
* Hot Observable和Cold Observable：
    1. Hot Observable：只给新observer发送新数据
    2. Cold Observable：给新observer发送所有数据（包括旧数据）

## 第3章　操作符基础
* 操作符函数的实现：
    * 返回⼀个全新的Observable对象。
```javascript
function map(project) {
    return new Observable(observer => {
        //这里的this是链式调用这个方法的对象
        this.subscribe({
            next: value => observer.next(project(value)),
            error: err => observer.error(error),
            complete: () => observer.complete(),
        });
    });
}
```
    * 对上游和下游的订阅及退订处理。
```javascript
function map(project) {
    return new Observable(observer => {
        //这里的this是链式调用这个方法的对象
        var sub = this.subscribe({
            next: value => observer.next(project(value)),
            error: err => observer.error(error),
            complete: () => observer.complete(),
        });
        return {
            //上游完全可能在被订阅时分配了特殊资源，
            //如果不明确地告诉上游这些资源再也⽤不着了的话
            //它也不会释放这些资源，
            unsubscribe: () => {
                sub.unsubscribe();
            }
        }
    });
}
```
    * 处理异常情况。
    * 及时释放资源。


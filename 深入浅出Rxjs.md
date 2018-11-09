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
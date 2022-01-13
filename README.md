# rxjs-cheatsheet
Cheatsheet for RxJS


# Usefull operators

## [distinctUntilChanged()](https://rxjs.dev/api/operators/distinctUntilChanged)
Filter values if are same.

```typescript
const series$ = of(1, 2, 2, 1, 3, 3, 1)
  .pipe(distinctUntilChanged())
  .subscribe(console.log);
// prints
// 1
// 2
// 1
// 3
// 1
```

## [throttleTime()](https://rxjs.dev/api/operators/throttleTime)
Emits a (**first**) value from the source Observable, then ignores subsequent source values for duration milliseconds, then repeats this process.
### similar to
- [sampleTime](https://rxjs.dev/api/operators/sampleTime) - emit **last** value from interval
- [debounceTime](https://rxjs.dev/api/operators/debounceTime) - emit **last** value from interval and delay it. Intrval counter start after first value income - it is good for autocomplete where we can assume after key down will be next key down event

# Higher-Order Observable

Generally, it is observable which returns observables. For examle, 

These use cases are the most common how to handle higher-order observables.

## Concatenation

### RxJS example:
```typescript
const series1$ = of('a', 'b');
const series2$ = of('x', 'y');
const result$ = concat(series1$, series2$);
result$.subscribe(console.log);
// prints:
// a
// b
// x
// y
```

> **_NOTE:_**  `of` create observable from given values

### Marble diagram:
- 1st line is firt obsarveble
- 2nd line is second obserbale
- result - 2nd observable wait till 1st is finished
- Gennerally, its FIFO
![image](https://user-images.githubusercontent.com/3972954/149121671-50e1573f-c21e-4cec-b9fe-66fae28dc480.png)

### Use cases
- send request to server in order
  ![image](https://user-images.githubusercontent.com/3972954/149122308-680aff11-9b17-48cf-bed4-cd00fd9bf98d.png)
  


## Merging
- merge streams together (like zipping in your jacket)

### RxJS example
```typescript
const series1$ = interval(1000).pipe(map(val => val*10));
const series2$ = interval(1000).pipe(map(val => val*100));
const result$ = merge(series1$, series2$);
result$.subscribe(console.log);
// prints
// 0
// 0
// 10
// 100
// 20
// 200
// ...
```

### Marble diagram

![image](https://user-images.githubusercontent.com/3972954/149123120-5a50fe24-7d4b-4c69-86a7-95b94c98a2c3.png)

### Use cases

- 
  ![image](https://user-images.githubusercontent.com/3972954/149128611-a794be1a-a966-42e5-aaf9-9aa04faf8c9c.png)




## Switching

Takes data from first observable till new observable incomes. When new observable income, then old observable is unsubscribled.

### Marble diagram

![image](https://user-images.githubusercontent.com/3972954/149129364-4e6708bf-6c05-4919-85c1-b8d0c2a5cbc7.png)

### Use cases
- autocomplete 
  ![image](https://user-images.githubusercontent.com/3972954/149129809-4f7af7f8-4c93-4d14-a125-3e57454fc914.png)





## Exhaust

Takes data from first observable, next observable is subscribed only if no other subscribe is subscribed otherwise is canceled.

### Marble diagram

![image](https://user-images.githubusercontent.com/3972954/149130762-78b64853-b249-4a6b-80b1-b6d4fe273904.png)

### Use cases
- user can order only if the previous order was finished


# Error handling

## Basic way
```typescript
of(1,2).subscribe(
  (x)=>console.log("new data", x),
  (error)=>console.log("error", error), // <--- if an observable instance returns an error, then it is terminated
  ()=>console.log("completed")
);
```

## Replace observable if error emited
```typescript
http$.pipe(
  catchError((err) => {
    console.log(err);
    return of([]); // replace by a new observable with an empty array
  })
)
```

## Retry
```typescript
http$.pipe(
  retryWhen((errors) =>
    errors.pipe(
      delayWhen((event) => interval(Math.random() * 5000)) // optionally - wait random time (0-5 sec) and then retry
    )
)
```



# Examples
## Search (autocomplete etc.)
```typescript
ngAfterViewInit() {
  const search$ = fromEvent(this.input.nativeElement, "keyup").pipe(
    map((event: any) => event.target.value), // event -> current input value
    debounceTime(200), // emit last value after 200 ms when user press keybord
    distinctUntilChanged(), // ignore same input value to keep server traffic low
    switchMap((search) => this.loadLessons(search)) // auto cancel request if new search
  );
  
  const init$ = this.loadLessons();

  this.lessons$ = concat(init$, search$); // after component is loaded init data
}

loadLessons(search: string = "") {
  return createHttpObservable(
    `/api/lessons?courseId=${this.courseId}&pageSize=100&filter=${search}`
  ).pipe(map((resp) => resp["payload"]));
}
```

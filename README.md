# rxjs-cheatsheet
Cheatsheet for RxJS

1. [General](RADME.md)
2. [Higher Order Observable](Higher-Order-Observable.md)
3. [Subjects](Subjects.md)
4. [Errors handling](Errors-handling.md)


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
- [debounceTime](https://rxjs.dev/api/operators/debounceTime) - emit **last** value from interval and delay it. Wait till value is stable for defined time. **Intrval counter start after last stream value income** - it is good for autocomplete where we can assume after key down will be next key down event.

## [startWith()](https://rxjs.dev/api/operators/startWith)
- init stream with given value (for example if you have search input you can emit empty search for get all data from server)



# Examples
## Search (autocomplete etc.) base example + startWith example
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


//
// IMPROVED WITH startWith()
//
ngAfterViewInit() {
  const search$ = fromEvent(this.input.nativeElement, "keyup").pipe(
    map((event: any) => event.target.value), // event -> current input value
    startWith(''), // emit empty string for init
    debounceTime(200), // emit last value after 200 ms when user press keybord
    distinctUntilChanged(), // ignore same input value to keep server traffic low
    switchMap((search) => this.loadLessons(search)) // auto cancel request if new search
  );
  
  // const init$ = this.loadLessons(); we do not need it any more

  this.lessons$ = search$;
}
```

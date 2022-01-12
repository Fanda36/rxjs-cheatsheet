# rxjs-cheatsheet
Cheatsheet for RxJS

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


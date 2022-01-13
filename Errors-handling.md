# Errors handling

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
    return of([]); // replace error stream by a new stream with an empty array
  })
)
```

## Retry
```typescript
http$.pipe(
  retryWhen((errors) =>
    errors.pipe(
      delayWhen((event) => interval(Math.random() * 5000)) // optionally - replace error stream by same new stream 0 - 5 sec after error happened
    )
)
```

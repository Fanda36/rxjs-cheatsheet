# Subjects

Subjects are Observables which can be modified from outside (**subject = observable API + observer API**).

## Observable vs Subject
- Observable
```typescript
const series$ = new Observable((observer: Observer<any>) => {
  observer.next(1);
  observer.next(2);
  observer.next(3);
});

series$.subscribe(console.log);

// we can not call next, error, complete functions
```
- Subject
```typescript
// this variable should stay private in an app (e. g. private inside service)
const subject = new Subject();

// this variable can be public
const serie$ = subject.asObservable();

serie$.subscribe(console.log);

subject.next(1);
subject.next(2);
subject.next(3);
subject.complete();
```
> **_NOTE:_**  `subject instance` should stay always private. You can safely public subject.asObservable() (it removes Subject API).



## Subject types

### Subject
Base subject without any internal memory. If new values are emitted before a new subscription, the new subscription does not get these values.

![image](https://user-images.githubusercontent.com/3972954/149322513-b034a6d1-4b41-4c5c-8641-b866e7d54334.png)

### BehaviorSubject
Subject with internal memory. **It has to be initialized with initial value**.

![image](https://user-images.githubusercontent.com/3972954/149322300-4281d50b-a4fe-49e3-bb38-0e29f79fa65d.png)


### ReplaySubject
Subject with internal memory. This subject each time returns the whole history of the stream.

![image](https://user-images.githubusercontent.com/3972954/149322716-43432307-b815-4a8a-9bc9-540bf15816b2.png)

### AsyncSubject
A subject that waits till the stream is complete(). Then returns the final value. Till the stream is not finished does not return any value.

![image](https://user-images.githubusercontent.com/3972954/149322935-bb93b6f5-da0d-49b5-948c-d569033073ae.png)




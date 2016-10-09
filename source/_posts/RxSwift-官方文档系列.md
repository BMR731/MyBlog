---
title: RxSwift官方文档系列（二）
date: 2016-04-05 17:29:03
tags: RxSwift
categories: iOS
---



# RxSwift Learning From the official Documents<!--more-->


## Creating and Subscribing to Observables

#### empty
empty creates an empty sequence. The only message it sends is the .Completed message.
```
example("empty") {
let emptySequence = Observable<Int>.empty()
let subscription = emptySequence
.subscribe { event in
print(event)
}
}
---print---
.Completed
```
#### never
never creates a sequence that never sends any element or completes.
```
example("never") {
let neverSequence = Observable<Int>.never()

let subscription = neverSequence
.subscribe { _ in
print("This block is never called.")
}
}
```
-----print-----

#### just
just represents sequence that contains one element. It sends two messages to subscribers. The first message is the value of single element and the second message is .Completed.
```

example("just") {
let singleElementSequence = Observable.just(32)

let subscription = singleElementSequence
.subscribe { event in
print(event)
}
}
----print----
32
.Completed
```
#### sequenceOf
sequenceOf creates a sequence of a fixed number of elements.
```

example("sequenceOf") {
let sequenceOfElements/* : Observable<Int> */ = Observable.of(0, 1, 2, 3, 4, 5, 6, 7, 8, 9)

let subscription = sequenceOfElements
.subscribe { event in
print(event)
}
}
-----print---
0
1
2
3
4....
.Completed
```

#### from
from creates a sequence from SequenceType

```
example("toObservable") {
let sequenceFromArray = [1, 2, 3, 4, 5].toObservable()

let subscription = sequenceFromArray
.subscribe { event in
print(event)
}
}

----print---
1
2
..
.Completed
```
#### create
create creates sequence using Swift closure. This examples creates custom version of just operator.
```
example("create") {
let myJust = { (singleElement: Int) -> Observable<Int> in
return Observable.create { observer in
observer.on(.Next(singleElement))
observer.on(.Completed)

return NopDisposable.instance
}
}

let subscription = myJust(5)
.subscribe { event in
print(event)
}
}
----print----
5
.Completed
```
#### generate
generate creates sequence that generates its values and determines when to terminate based on its previous values.
```
example("generate") {
let generated = Observable.generate(
initialState: 0,
condition: { $0 < 3 },
iterate: { $0 + 1 }
)

let subscription = generated
.subscribe { event in
print(event)
}
}

-----print---
0
1
2
.Completed
```
#### failWith
create an Observable that emits no items and terminates with an error
```
example("failWith") {
let error = NSError(domain: "Test", code: -1, userInfo: nil)

let erroredSequence = Observable<Int>.error(error)

let subscription = erroredSequence
.subscribe { event in
print(event)
}
}

--- failWith example ---
Error(Error Domain=Test Code=-1 "(null)")
```

### deferred
do not create the Observable until the observer subscribes, and create a **fresh** Observable for **each** observer

```
example("deferred") {
let deferredSequence: Observable<Int> = Observable.deferred {
print("creating")
return Observable.create { observer in
print("emmiting")
observer.on(.Next(0))
observer.on(.Next(1))
observer.on(.Next(2))

return NopDisposable.instance
}
}

_ = deferredSequence
.subscribe { event in
print(event)
}

_ = deferredSequence
.subscribe { event in
print(event)
}
}

--- deferred example ---
creating
emmiting
Next(0)
Next(1)
Next(2)
creating
emmiting
Next(0)
Next(1)
Next(2)

```

## Subjects

> A Subject is a sort of bridge or proxy that is available in some implementations of ReactiveX that acts both as an observer and as an Observable. Because it is an observer, it can subscribe to one or more Observables, and because it is an Observable, it can pass through the items it observes by reemitting them, and it can also emit new items.
```
func writeSequenceToConsole<O: ObservableType>(name: String, sequence: O) -> Disposable {
return sequence
.subscribe { e in
print("Subscription: \(name), event: \(e)")
}
}
```

#### PublishSubject
PublishSubject emits to an observer only those items that are emitted by the source Observable(s) subsequent to the time of the subscription.
>I think ,when PublishSubject was subscribed,it only emitted the Observables after the observer subscribes.it means the previous Observables will be ignored.
```
example("PublishSubject") {
let disposeBag = DisposeBag()

let subject = PublishSubject<String>()
writeSequenceToConsole("1", sequence: subject).addDisposableTo(disposeBag)
subject.on(.Next("a"))
subject.on(.Next("b"))
writeSequenceToConsole("2", sequence: subject).addDisposableTo(disposeBag)
subject.on(.Next("c"))
subject.on(.Next("d"))
}

--- PublishSubject example ---
Subscription: 1, event: Next(a)
Subscription: 1, event: Next(b)
Subscription: 1, event: Next(c)
Subscription: 2, event: Next(c)
Subscription: 1, event: Next(d)
Subscription: 2, event: Next(d)
```

#### ReplaySubject
ReplaySubject emits to any observer all of the items that were emitted by the source Observable(s), regardless of when the observer subscribes.
>compared to PublishSubject,the ReplaySubject will reemit the previous Observables

```
example("ReplaySubject") {
let disposeBag = DisposeBag()
//**adjust the buffer size value can ensure the number of previous reemited**
let subject = ReplaySubject<String>.create(bufferSize: 1)

writeSequenceToConsole("1", sequence: subject).addDisposableTo(disposeBag)
subject.on(.Next("a"))
subject.on(.Next("b"))
writeSequenceToConsole("2", sequence: subject).addDisposableTo(disposeBag)
subject.on(.Next("c"))
subject.on(.Next("d"))
}
--- ReplaySubject example ---
Subscription: 1, event: Next(a)
Subscription: 1, event: Next(b)
Subscription: 2, event: Next(b)
Subscription: 1, event: Next(c)
Subscription: 2, event: Next(c)
Subscription: 1, event: Next(d)
Subscription: 2, event: Next(d)
```
#### BehaviorSubject
When an observer subscribes to a BehaviorSubject, it begins by emitting the item most recently emitted by the source Observable (or a seed/default value if none has yet been emitted) and then continues to emit any other items emitted later by the source Observable(s).
>The BehaviorSubject init function permit you to give a init or cache the value,when the BehaviorSubject was subscribed,it will the before(just one) to add the observer's Observables
>

```
example("BehaviorSubject") {
let disposeBag = DisposeBag()

let subject = BehaviorSubject(value: "z")
writeSequenceToConsole("1", sequence: subject).addDisposableTo(disposeBag)
subject.on(.Next("a"))
subject.on(.Next("b"))
writeSequenceToConsole("2", sequence: subject).addDisposableTo(disposeBag)
subject.on(.Next("c"))
subject.on(.Next("d"))
subject.on(.Completed)
}
--- BehaviorSubject example ---
Subscription: 1, event: Next(z)
Subscription: 1, event: Next(a)
Subscription: 1, event: Next(b)
Subscription: 2, event: Next(b)
Subscription: 1, event: Next(c)
Subscription: 2, event: Next(c)
Subscription: 1, event: Next(d)
Subscription: 2, event: Next(d)
Subscription: 1, event: Completed
Subscription: 2, event: Completed
```

#### Variable
**Variable wraps BehaviorSubject.** Advantage of using variable over BehaviorSubject is that variable can never **explicitly **complete or error out, and BehaviorSubject can in case Error or Completed message is send to it. Variable will also automatically complete in case it's being deallocated.
```
example("Variable") {
let disposeBag = DisposeBag()
let variable = Variable("z")
writeSequenceToConsole("1", sequence: variable.asObservable()).addDisposableTo(disposeBag)
variable.value = "a"
variable.value = "b"
writeSequenceToConsole("2", sequence: variable.asObservable()).addDisposableTo(disposeBag)
variable.value = "c"
variable.value = "d"
}

--- Variable example ---
Subscription: 1, event: Next(z)
Subscription: 1, event: Next(a)
Subscription: 1, event: Next(b)
Subscription: 2, event: Next(b)
Subscription: 1, event: Next(c)
Subscription: 2, event: Next(c)
Subscription: 1, event: Next(d)
Subscription: 2, event: Next(d)
Subscription: 1, event: Completed
Subscription: 2, event: Completed

```
 
## Transforming Observables
Operators that transform items that are emitted by an Observable.

#### map / select
Transform the items emitted by an Observable by applying a function to each item
```
example("map") {
let originalSequence = Observable.of(1, 2, 3)

_ = originalSequence
.map { number in
number * 2
}
.subscribe { print($0) }
}

--- map example ---
Next(2)
Next(4)
Next(6)
Completed
```
#### flatMap
Transform the items emitted by an Observable into Observables, then flatten the emissions from those into a single Observable
```
example("flatMap") {
let sequenceInt = Observable.of(1, 2, 3)

let sequenceString = Observable.of("A", "B", "C", "D", "E", "F", "--")

_ = sequenceInt
.flatMap { (x:Int) -> Observable<String> in
print("from sequenceInt \(x)")
return sequenceString
}
.subscribe {
print($0)
}
}
--- flatMap example ---
from sequenceInt 1
Next(A)
Next(B)
Next(C)
Next(D)
Next(E)
Next(F)
Next(--)
from sequenceInt 2
Next(A)
Next(B)
Next(C)
Next(D)
Next(E)
Next(F)
Next(--)
from sequenceInt 3
Next(A)
Next(B)
Next(C)
Next(D)
Next(E)
Next(F)
Next(--)
Completed
```
>The difference of map and flatMap

```
let arr = [[1, 2, 3], [6, 5, 4]]
>let brr = arr.flatMap {
$0
}
// brr = [1, 2, 3, 6, 5, 4]
/*你看出来了吗？在这个例子中，数组 arr 调用 flatMap 时，元素[1, 2, 3] 和 [6, 5, 4] 分别被传入闭包中，又直接被作为结果返回。但是，最终的结果中，却是由这两个数组中的元素共同组成的新数组：[1, 2, 3, 6, 5, 4] 。

需要注意的是，其实整个 flatMap 方法可以拆解成两步：

第一步像 map 方法那样，对元素进行某种规则的转换。
第二步，执行 flatten 方法，将数组中的元素一一取出来，组成一个新数组。
所以，刚刚的代码其实等价于：*/

let arr = [[1, 2, 3], [6, 5, 4]]
let crr = Array(arr.map{ $0 }.flatten())
// crr = [1, 2, 3, 6, 5, 4]
```

#### scan
Apply a function to each item emitted by an Observable, sequentially, and emit each successive value
```
example("scan") {
let sequenceToSum = Observable.of(0, 1, 2, 3, 4, 5)

_ = sequenceToSum
.scan(0) { acum, elem in
acum + elem
}
.subscribe {
print($0)
}
}

--- scan example ---
Next(0)
Next(1)
Next(3)
Next(6)
Next(10)
Next(15)
Completed
```
 
## Filtering Observables
Operators that selectively emit items from a source Observable.

#### filter
Emit only those items from an Observable that pass a predicate test
```
example("filter") {
let subscription = Observable.of(0, 1, 2, 3, 4, 5, 6, 7, 8, 9)
.filter {
$0 % 2 == 0
}
.subscribe {
print($0)
}
}
--- filter example ---
Next(0)
Next(2)
Next(4)
Next(6)
Next(8)
Completed
```

#### distinctUntilChanged
Suppress duplicate items emitted by an Observable
>The distinctUntilChanged only suppress the duplicates successive ,for the same value but have the interval, it will not suppress it.
```
example("distinctUntilChanged") {
let subscription = Observable.of(1,1,2, 3, 1, 1, 4)
.distinctUntilChanged()
.subscribe {
print($0)
}
}
--- distinctUntilChanged example ---
Next(1)
Next(2)
Next(3)
Next(1)
Next(4)
Completed
```
#### take
Emit only the **first** n items emitted by an Observable 
```
example("take") {
let subscription = Observable.of(1, 2, 3, 4, 5, 6)
.take(3)
.subscribe {
print($0)
}
}
--- take example ---
Next(1)
Next(2)
Next(3)
Completed
```

## Combination operators
Operators that work with multiple source Observables to create a single Observable.

#### startWith
emit a specified sequence of items before beginning to emit the items from the source Observable
```
example("startWith") {

let subscription = Observable.of(4, 5, 6, 7, 8, 9)
.startWith(3)
.startWith(2)
.startWith(1)
.startWith(0)
.subscribe {
print($0)
}
}
--- startWith example ---
Next(0)
Next(1)
Next(2)
Next(3)
Next(4)
Next(5)
Next(6)
Next(7)
Next(8)
Next(9)
Completed
```

#### combineLatest
when an item is emitted by either of two Observables, combine the latest item emitted by each Observable via a specified function and emit items based on the results of this function
```
example("combineLatest 1") {
let intOb1 = PublishSubject<String>()
let intOb2 = PublishSubject<Int>()

_ = Observable.combineLatest(intOb1, intOb2) {
"\($0) \($1)"
}
.subscribe {
print($0)
}

intOb1.on(.Next("A"))

intOb2.on(.Next(1))

intOb1.on(.Next("B"))

intOb2.on(.Next(2))
}


--- combineLatest 1 example ---
Next(A 1)
Next(B 1)
Next(B 2)
```
>To produce output, at least one element has to be received from each sequence in >arguements.
```

example("combineLatest 2") {
let intOb1 = Observable.just(2)
let intOb2 = Observable.of(0, 1, 2, 3, 4)

_ = Observable.combineLatest(intOb1, intOb2) {
$0 * $1
}
.subscribe {
print($0)
}
}

--- combineLatest 2 example ---
Next(0)
Next(2)
Next(4)
Next(6)
Next(8)
Completed
```
>Combine latest has versions with more than 2 arguments.
```
example("combineLatest 3") {
let intOb1 = Observable.just(2)
let intOb2 = Observable.of(0, 1, 2, 3)
let intOb3 = Observable.of(0, 1, 2, 3, 4)

_ = Observable.combineLatest(intOb1, intOb2, intOb3) {
($0 + $1) * $2
}
.subscribe {
print($0)
}
}

--- combineLatest 3 example ---
Next(0)
Next(5)
Next(10)
Next(15)
Next(20)
Completed
```
>Combinelatest version that allows combining sequences with different types.

>combineLatest extension method for Array of ObservableType conformable types The array >must be formed by Observables of the same type.
```
example("combineLatest 5") {
let intOb1 = Observable.just(2)
let intOb2 = Observable.of(0, 1, 2, 3)
let intOb3 = Observable.of(0, 1, 2, 3, 4)

_ = [intOb1, intOb2, intOb3].combineLatest { intArray -> Int in
Int((intArray[0] + intArray[1]) * intArray[2])
}
.subscribe { (event: Event<Int>) -> Void in
print(event)
}
}
--- combineLatest 5 example ---
Next(0)
Next(5)
Next(10)
Next(15)
Next(20)
Completed
```

#### zip
combine the emissions of multiple Observables together via a specified function and emit single items for each combination based on the results of this function

```
example("zip 1") {
let intOb1 = PublishSubject<String>()
let intOb2 = PublishSubject<Int>()

_ = Observable.zip(intOb1, intOb2) {
"\($0) \($1)"
}
.subscribe {
print($0)
}

intOb1.on(.Next("A"))

intOb2.on(.Next(1))

intOb1.on(.Next("B"))

intOb1.on(.Next("C"))

intOb2.on(.Next(2))
}


example("zip 2") {
let intOb1 = Observable.just(2)

let intOb2 = Observable.of(0, 1, 2, 3, 4)

_ = Observable.zip(intOb1, intOb2) {
$0 * $1
}
.subscribe {
print($0)
}
}


example("zip 3") {
let intOb1 = Observable.of(0, 1)
let intOb2 = Observable.of(0, 1, 2, 3)
let intOb3 = Observable.of(0, 1, 2, 3, 4)

_ = Observable.zip(intOb1, intOb2, intOb3) {
($0 + $1) * $2
}
.subscribe {
print($0)
}
}

--- zip 1 example ---
Next(A 1)
Next(B 2)

--- zip 2 example ---
Next(0)
Completed

--- zip 3 example ---
Next(0)
Next(2)
Completed
```
####merge
combine multiple Observables into one by merging their emissions
>merge differ the above CombineLast and zip,it doesn't create the new value in some way ,merge is just add the Observables into the another session ,just add,no calculate.
```
example("merge 1") {
let subject1 = PublishSubject<Int>()
let subject2 = PublishSubject<Int>()

_ = Observable.of(subject1, subject2)
.merge()
.subscribeNext { int in
print(int)
}

subject1.on(.Next(20))
subject1.on(.Next(40))
subject1.on(.Next(60))
subject2.on(.Next(1))
subject1.on(.Next(80))
subject1.on(.Next(100))
subject2.on(.Next(1))
}

//notice the maxConcurrent param,it depends the order of (Observable.of(subject1, subject3,subject2)) to decide the permitted number of subjects used.

example("merge 2") {
let subject1 = PublishSubject<Int>()
let subject2 = PublishSubject<Int>()
let subject3  = PublishSubject<Int>()
_ = Observable.of(subject1, subject3,subject2)
.merge(maxConcurrent: 2)
.subscribe {
print($0)
}

subject1.on(.Next(20))
subject1.on(.Next(40))
subject3.onNext(3)
subject1.on(.Next(60))
subject2.on(.Next(1))
subject1.on(.Next(80))
subject1.on(.Next(100))
subject2.on(.Next(1))
}

--- merge 1 example ---
20
40
60
1
80
100
1

--- merge 2 example ---
Next(20)
Next(40)
Next(3)
Next(60)
Next(80)
Next(100)
```

#### switchLatest
convert an Observable that emits Observables into a single Observable that emits the items emitted by the most-recently-emitted of those Observables
```
example("switchLatest") {
let var1 = Variable(0)

let var2 = Variable(200)

// var3 is like an Observable<Observable<Int>>
let var3 = Variable(var1.asObservable())

let d = var3
.asObservable()
.switchLatest()
.subscribe {
print($0)
}

var1.value = 1
var1.value = 2
var1.value = 3
var1.value = 4

var3.value = var2.asObservable()

var2.value = 201

var1.value = 5
var1.value = 6
var1.value = 7
}

--- switchLatest example ---
Next(0)
Next(1)
Next(2)
Next(3)
Next(4)
Next(200)
Next(201)
Completed
```
## Error Handling Operators
Operators that help to recover from error notifications from an Observable.

#### catchError
Recover from an Error notification by continuing the sequence without error
```
example("catchError 1") {
let sequenceThatFails = PublishSubject<Int>()
let recoverySequence = Observable.of(100, 200, 300, 400)

_ = sequenceThatFails
.catchError { error in
return recoverySequence
}
.subscribe {
print($0)
}

sequenceThatFails.on(.Next(1))
sequenceThatFails.on(.Next(2))
sequenceThatFails.on(.Next(3))
sequenceThatFails.on(.Next(4))
sequenceThatFails.on(.Error(NSError(domain: "Test", code: 0, userInfo: nil)))
}


example("catchError 2") {
let sequenceThatFails = PublishSubject<Int>()

_ = sequenceThatFails
.catchErrorJustReturn(100)
.subscribe {
print($0)
}

sequenceThatFails.on(.Next(1))
sequenceThatFails.on(.Next(2))
sequenceThatFails.on(.Next(3))
sequenceThatFails.on(.Next(4))
sequenceThatFails.on(.Error(NSError(domain: "Test", code: 0, userInfo: nil)))
}

--- catchError 1 example ---
Next(1)
Next(2)
Next(3)
Next(4)
Next(100)
Next(200)
Next(300)
Next(400)
Completed

--- catchError 2 example ---
Next(1)
Next(2)
Next(3)
Next(4)
Next(100)
Completed
```
#### retry
If a source Observable emits an error, resubscribe to it in the hopes that it will complete without error
```
example("retry") {
var count = 1 // bad practice, only for example purposes
let funnyLookingSequence = Observable<Int>.create { observer in
let error = NSError(domain: "Test", code: 0, userInfo: nil)
observer.on(.Next(0))
observer.on(.Next(1))
observer.on(.Next(2))
if count < 2 {
observer.on(.Error(error))
count += 1
}
observer.on(.Next(3))
observer.on(.Next(4))
observer.on(.Next(5))
observer.on(.Completed)

return NopDisposable.instance
}

_ = funnyLookingSequence
.retry()
.subscribe {
print($0)
}
}


--- retry example ---
Next(0)
Next(1)
Next(2)
Next(0)
Next(1)
Next(2)
Next(3)
Next(4)
Next(5)
Completed
```
## Observable Utility Operators
A toolbox of useful Operators for working with Observables.
#### Subscribe
```
example("subscribe") {
let sequenceOfInts = PublishSubject<Int>()

_ = sequenceOfInts
.subscribe {
print($0)
}

sequenceOfInts.on(.Next(1))
sequenceOfInts.on(.Completed)
}

example("subscribeNext") {
let sequenceOfInts = PublishSubject<Int>()

_ = sequenceOfInts
.subscribeNext {
print($0)
}

sequenceOfInts.on(.Next(1))
sequenceOfInts.on(.Completed)
}

example("subscribeCompleted") {
let sequenceOfInts = PublishSubject<Int>()

_ = sequenceOfInts
.subscribeCompleted {
print("It's completed")
}

sequenceOfInts.on(.Next(1))
sequenceOfInts.on(.Completed)
}


example("subscribeError") {
let sequenceOfInts = PublishSubject<Int>()

_ = sequenceOfInts
.subscribeError { error in
print(error)
}

sequenceOfInts.on(.Next(1))
sequenceOfInts.on(.Error(NSError(domain: "Examples", code: -1, userInfo: nil)))
}


--- subscribe example ---
Next(1)
Completed

--- subscribeNext example ---
1

--- subscribeCompleted example ---
It's completed

--- subscribeError example ---
Error Domain=Examples Code=-1 "(null)"

```
#### doOn
register an action to take upon a variety of Observable lifecycle events
```
example("doOn") {
let sequenceOfInts = PublishSubject<Int>()

_ = sequenceOfInts
.doOn {
print("Intercepted event \($0)")
}
.subscribe {
print($0)
}

sequenceOfInts.on(.Next(1))
sequenceOfInts.on(.Completed)
}
--- doOn example ---
Intercepted event Next(1)
Next(1)
Intercepted event Completed
Completed

```

## Conditional and Boolean Operators
Operators that evaluate one or more Observables or items emitted by Observables.

#### takeUntil
Discard any items emitted by an Observable after a **second Observable** emits an item or terminates.
```

example("takeUntil") {
let originalSequence = PublishSubject<Int>()
let whenThisSendsNextWorldStops = PublishSubject<Int>()

_ = originalSequence
.takeUntil(whenThisSendsNextWorldStops)
.subscribe {
print($0)
}

originalSequence.on(.Next(1))
originalSequence.on(.Next(2))
originalSequence.on(.Next(3))
originalSequence.on(.Next(4))

whenThisSendsNextWorldStops.on(.Next(1))

originalSequence.on(.Next(5))
}

--- takeUntil example ---
Next(1)
Next(2)
Next(3)
Next(4)
Completed
```

#### takeWhile
Mirror items emitted by an Observable until a specified condition becomes false

```
example("takeWhile") {

let sequence = PublishSubject<Int>()

_ = sequence
.takeWhile { int in
int < 4
}
.subscribe {
print($0)
}

sequence.on(.Next(1))
sequence.on(.Next(2))
sequence.on(.Next(3))
sequence.on(.Next(4))
sequence.on(.Next(5))
}
--- takeWhile example ---
Next(1)
Next(2)
Next(3)
Completed
```
## Mathematical and Aggregate Operators
Operators that operate on the entire sequence of items emitted by an Observable
#### concat
Emit the emissions from two or more Observables without **interleaving** them.


```
example("concat") {
let var1 = BehaviorSubject(value: 0)
let var2 = BehaviorSubject(value: 200)

// var3 is like an Observable<Observable<Int>>
let var3 = BehaviorSubject(value: var1)

let d = var3
.concat()
.subscribe {
print($0)
}

var1.on(.Next(1))
var1.on(.Next(2))
var1.on(.Next(3))
var1.on(.Next(4))

var3.on(.Next(var2))

var2.on(.Next(201))

var1.on(.Next(5))
var1.on(.Next(6))
var1.on(.Next(7))
var1.on(.Completed)

var2.on(.Next(202))
var2.on(.Next(203))
var2.on(.Next(204))
}


--- concat example ---
Next(0)
Next(1)
Next(2)
Next(3)
Next(4)
Next(5)
Next(6)
Next(7)
Next(201)
Next(202)
Next(203)
Next(204)
```
#### educe
Apply a function to each item emitted by an Observable, sequentially, and emit the final value. This function will perform a function on each element in the sequence until it is completed, then send a message with the aggregate value. It works much like the Swift reduce function works on sequences.

```
example("reduce") {
_ = Observable.of(0, 1, 2, 3, 4, 5, 6, 7, 8, 9)
.reduce(0, accumulator: +)
.subscribe {
print($0)
}
}
--- reduce example ---
Next(45)
Completed

```
## Connectable Observable Operators
A Connectable Observable resembles an ordinary Observable, except that **it does not begin emitting items when it is subscribed to, but only when its connect() method is called**. In this way you can wait for all intended Subscribers to subscribe to the Observable before the Observable begins emitting items.

Specialty Observables that have more precisely-controlled subscription dynamics.
```

func sampleWithoutConnectableOperators() {

let int1 = Observable<Int>.interval(1, scheduler: MainScheduler.instance)

_ = int1
.subscribe {
print("first subscription \($0)")
}

delay(5) {
_ = int1
.subscribe {
print("second subscription \($0)")
}
}

}

//sampleWithoutConnectableOperators()
```

#### Multicast
```
func sampleWithMulticast() {

let subject1 = PublishSubject<Int64>()

_ = subject1
.subscribe {
print("Subject \($0)")
}

let int1 = Observable<Int64>.interval(1, scheduler: MainScheduler.instance)
.multicast(subject1)

_ = int1
.subscribe {
print("first subscription \($0)")
}

delay(2) {
int1.connect()
}

delay(4) {
_ = int1
.subscribe {
print("second subscription \($0)")
}
}

delay(6) {
_ = int1
.subscribe {
print("third subscription \($0)")
}
}

}

sampleWithMulticast()
----print----
Subject Next(0)
first subscription Next(0)
Subject Next(1)
first subscription Next(1)
second subscription Next(1)
Subject Next(2)
first subscription Next(2)
second subscription Next(2)
Subject Next(3)
first subscription Next(3)
second subscription Next(3)
third subscription Next(3)
Subject Next(4)
first subscription Next(4)
second subscription Next(4)
third subscription Next(4)
```
#### replay
Ensure that all observers see the same sequence of emitted items, even if they subscribe after the Observable has begun emitting items.

publish = multicast + replay subject
```
func sampleWithReplayBuffer0() {

let int1 = Observable<Int>.interval(1, scheduler: MainScheduler.instance)
.replay(0)

_ = int1
.subscribe {
print("first subscription \($0)")
}

delay(2) {
int1.connect()
}

delay(4) {
_ = int1
.subscribe {
print("second subscription \($0)")
}
}

delay(6) {
_ = int1
.subscribe {
print("third subscription \($0)")
}
}

}

sampleWithReplayBuffer0()


func sampleWithReplayBuffer2() {

print("--- sampleWithReplayBuffer2 ---\n")

let int1 = Observable<Int>.interval(1, scheduler: MainScheduler.instance)
.replay(2)

_ = int1
.subscribe {
print("first subscription \($0)")
}

delay(2) {
int1.connect()
}

delay(4) {
_ = int1
.subscribe {
print("second subscription \($0)")
}
}

delay(7) {
_ = int1
.subscribe {
print("third subscription \($0)")
}
}

}
---replay(0) test
first subscription Next(0)
first subscription Next(1)
second subscription Next(1)
first subscription Next(2)
second subscription Next(2)
first subscription Next(3)
second subscription Next(3)
third subscription Next(3)
first subscription Next(4)
second subscription Next(4)
third subscription Next(4)
sampleWithReplayBuffer2()

--- sampleWithReplayBuffer2 ---
first subscription Next(0)
second subscription Next(0)
first subscription Next(1)
second subscription Next(1)
first subscription Next(2)
second subscription Next(2)
first subscription Next(3)
second subscription Next(3)
third subscription Next(2)
third subscription Next(3)
first subscription Next(4)
second subscription Next(4)
third subscription Next(4)
first subscription Next(5)
second subscription Next(5)
third subscription Next(5)
first subscription Next(6)
second subscription Next(6)
third subscription Next(6)
first subscription Next(7)
second subscription Next(7)
third subscription Next(7)
```
>the param of replay means the number of Observables can be replayed.
####publish
Convert an ordinary Observable into a connectable Observable.

**publish = multicast + publish subject**

so **publish is basically replay(0)**
```
func sampleWithPublish() {

let int1 = Observable<Int>.interval(1, scheduler: MainScheduler.instance)
.publish()

_ = int1
.subscribe {
print("first subscription \($0)")
}

delay(2) {
int1.connect()
}

delay(4) {
_ = int1
.subscribe {
print("second subscription \($0)")
}
}

delay(6) {
_ = int1
.subscribe {
print("third subscription \($0)")
}
}

}

sampleWithPublish()
----print------
first subscription Next(0)
first subscription Next(1)
second subscription Next(1)
first subscription Next(2)
second subscription Next(2)
first subscription Next(3)
second subscription Next(3)
third subscription Next(3)
first subscription Next(4)
second subscription Next(4)
third subscription Next(4)
```










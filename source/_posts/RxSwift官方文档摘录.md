---
title: RxSwift官方文档系列（一）
date: 2016-04-11 20:15:45
tags: RxSwift
categories: iOS
---


API<!--more-->
===

## RxSwift supported operators

In some cases there are multiple aliases for the same operator, because on different platforms / implementations, the same operation is sometimes called differently. Sometimes this is because historical reasons, sometimes because of reserved language keywords.

When lacking a strong community consensus, RxSwift will usually include multiple aliases.

Operators are stateless by default.

#### Creating Observables

 * [`asObservable`](http://reactivex.io/documentation/operators/from.html)
 * [`create`](http://reactivex.io/documentation/operators/create.html)
 * [`deferred`](http://reactivex.io/documentation/operators/defer.html)
 * [`empty`](http://reactivex.io/documentation/operators/empty-never-throw.html)
 * [`error`](http://reactivex.io/documentation/operators/empty-never-throw.html)
 * [`toObservable` (array)](http://reactivex.io/documentation/operators/from.html)
 * [`interval`](http://reactivex.io/documentation/operators/interval.html)
 * [`never`](http://reactivex.io/documentation/operators/empty-never-throw.html)
 * [`just`](http://reactivex.io/documentation/operators/just.html)
 * [`of`](http://reactivex.io/documentation/operators/from.html)
 * [`range`](http://reactivex.io/documentation/operators/range.html)
 * [`repeatElement`](http://reactivex.io/documentation/operators/repeat.html)
 * [`timer`](http://reactivex.io/documentation/operators/timer.html)

#### Transforming Observables
  * [`buffer`](http://reactivex.io/documentation/operators/buffer.html)
  * [`flatMap`](http://reactivex.io/documentation/operators/flatmap.html)
  * [`flatMapFirst`](http://reactivex.io/documentation/operators/flatmap.html)
  * [`flatMapLatest`](http://reactivex.io/documentation/operators/flatmap.html)
  * [`map`](http://reactivex.io/documentation/operators/map.html)
  * [`scan`](http://reactivex.io/documentation/operators/scan.html)
  * [`window`](http://reactivex.io/documentation/operators/window.html)

#### Filtering Observables
  * [`debounce` / `throttle`](http://reactivex.io/documentation/operators/debounce.html)
  * [`distinctUntilChanged`](http://reactivex.io/documentation/operators/distinct.html)
  * [`elementAt`](http://reactivex.io/documentation/operators/elementat.html)
  * [`filter`](http://reactivex.io/documentation/operators/filter.html)
  * [`sample`](http://reactivex.io/documentation/operators/sample.html)
  * [`skip`](http://reactivex.io/documentation/operators/skip.html)
  * [`take`](http://reactivex.io/documentation/operators/take.html)
  * [`takeLast`](http://reactivex.io/documentation/operators/takelast.html)
  * [`single`](http://reactivex.io/documentation/operators/first.html)

#### Combining Observables

  * [`merge`](http://reactivex.io/documentation/operators/merge.html)
  * [`startWith`](http://reactivex.io/documentation/operators/startwith.html)
  * [`switchLatest`](http://reactivex.io/documentation/operators/switch.html)
  * [`combineLatest`](http://reactivex.io/documentation/operators/combinelatest.html)
  * [`zip`](http://reactivex.io/documentation/operators/zip.html)

#### Error Handling Operators

 * [`catch`](http://reactivex.io/documentation/operators/catch.html)
 * [`retry`](http://reactivex.io/documentation/operators/retry.html)
 * [`retryWhen`](http://reactivex.io/documentation/operators/retry.html)

#### Observable Utility Operators

  * [`delaySubscription`](http://reactivex.io/documentation/operators/delay.html)
  * [`do` / `doOnNext`](http://reactivex.io/documentation/operators/do.html)
  * [`observeOn` / `observeSingleOn`](http://reactivex.io/documentation/operators/observeon.html)
  * [`subscribe`](http://reactivex.io/documentation/operators/subscribe.html)
  * [`subscribeOn`](http://reactivex.io/documentation/operators/subscribeon.html)
  * [`timeout`](http://reactivex.io/documentation/operators/timeout.html)
  * [`using`](http://reactivex.io/documentation/operators/using.html)
  * debug

#### Conditional and Boolean Operators
  * [`amb`](http://reactivex.io/documentation/operators/amb.html)
  * [`skipWhile`](http://reactivex.io/documentation/operators/skipwhile.html)
  * [`skipUntil`](http://reactivex.io/documentation/operators/skipuntil.html)
  * [`takeUntil`](http://reactivex.io/documentation/operators/takeuntil.html)
  * [`takeWhile`](http://reactivex.io/documentation/operators/takewhile.html)

#### Mathematical and Aggregate Operators

  * [`concat`](http://reactivex.io/documentation/operators/concat.html)
  * [`reduce` / `aggregate`](http://reactivex.io/documentation/operators/reduce.html)
  * [`toArray`](http://reactivex.io/documentation/operators/to.html)

#### Connectable Observable Operators

  * [`multicast`](http://reactivex.io/documentation/operators/publish.html)
  * [`publish`](http://reactivex.io/documentation/operators/publish.html)
  * [`refCount`](http://reactivex.io/documentation/operators/refcount.html)
  * [`replay`](http://reactivex.io/documentation/operators/replay.html)
  * [`shareReplay`](http://reactivex.io/documentation/operators/replay.html)

Creating new operators is also pretty straightforward.

## RxCocoa extensions

**iOS / OSX**

```swift

extension NSObject {

    public var rx_deallocated: Observable<Void> {}

#if !DISABLE_SWIZZLING

    public var rx_deallocating: Observable<Void> {}

#endif

}

```


```swift
extension NSObject {

    public func rx_observe<Element>(
        type: E.Type,
        _ keyPath: String,
        options: NSKeyValueObservingOptions = .New | .Initial,
        retainSelf: Bool = true
    )  -> Observable<Element?> {}

#if !DISABLE_SWIZZLING

    public func rx_observeWeakly<Element>(
        type: E.Type,
        _ keyPath: String,
        options: NSKeyValueObservingOptions = .New | .Initial
    ) -> Observable<Element?> {}

#endif
}
```

```swift
extension NSURLSession {

    public func rx_response(request: NSURLRequest) -> Observable<(NSData, NSURLResponse)> {}

    public func rx_data(request: NSURLRequest) -> Observable<NSData> {}

    public func rx_JSON(request: NSURLRequest) -> Observable<AnyObject> {}

    public func rx_JSON(URL: NSURL) -> Observable<AnyObject> {}

}
```

```swift
extension NSNotificationCenter {

    public func rx_notification(name: String, object: AnyObject?) -> Observable<NSNotification> {}

}
```

```swift
class DelegateProxy {

    public func observe(selector: Selector) -> Observable<[AnyObject]> {}

}
```

```swift
extension CLLocationManager {

    public var rx_delegate: DelegateProxy {}

    public var rx_didUpdateLocations: Observable<[CLLocation]> {}

    public var rx_didFailWithError: Observable<NSError> {}

    public var rx_didFinishDeferredUpdatesWithError: Observable<NSError> {}

    public var rx_didPauseLocationUpdates: Observable<Void> {}

    public var rx_didResumeLocationUpdates: Observable<Void> {}

    public var rx_didUpdateHeading: Observable<CLHeading> {}

    public var rx_didEnterRegion: Observable<CLRegion> {}

    public var rx_didExitRegion: Observable<CLRegion> {}

    public var rx_didDetermineStateForRegion: Observable<(state: CLRegionState, region: CLRegion)> {}

    public var rx_monitoringDidFailForRegionWithError: Observable<(region: CLRegion?, error: NSError)> {}

    public var rx_didStartMonitoringForRegion: Observable<CLRegion> {}

    public var rx_didRangeBeaconsInRegion: Observable<(beacons: [CLBeacon], region: CLBeaconRegion)> {}

    public var rx_rangingBeaconsDidFailForRegionWithError: Observable<(region: CLBeaconRegion, error: NSError)> {}

    public var rx_didVisit: Observable<CLVisit> {}

    public var rx_didChangeAuthorizationStatus: Observable<CLAuthorizationStatus> {}

}
```

**iOS**

```swift

extension UIControl {

    public func rx_controlEvent(controlEvents: UIControlEvents) -> ControlEvent<Void> {}

    public var rx_enabled: ObserverOf<Bool> {}
}

```

```swift
extension UIButton {

    public var rx_tap: ControlEvent<Void> {}

}
```

```swift
extension UITextField {

    public var rx_text: ControlProperty<String> {}

}
```

```swift
extension UITextView {

    override func rx_createDelegateProxy() -> RxScrollViewDelegateProxy {}

    public var rx_text: ControlProperty<String> {}

}
```

```swift
extension UISearchBar {

    public var rx_delegate: DelegateProxy {}

    public var rx_searchText: ControlProperty<String> {}

}
```

```swift
extension UILabel {

    public var rx_text: ObserverOf<String> {}

}
```

```swift
extension UIDatePicker {

    public var rx_date: ControlProperty<NSDate> {}

}
```

```swift
extension UIImageView {

    public var rx_image: ObserverOf<UIImage!> {}

    public func rx_imageAnimated(transitionType: String?) -> AnyObserver<UIImage?>

}
```

```swift
extension UIScrollView {

    public var rx_delegate: DelegateProxy {}

    public func rx_setDelegate(delegate: UIScrollViewDelegate) {}

    public var rx_contentOffset: ControlProperty<CGPoint> {}

}
```

```swift
extension UIBarButtonItem {

    public var rx_tap: ControlEvent<Void> {}

}
```

```swift
extension UISlider {

    public var rx_value: ControlProperty<Float> {}

}
```

```swift
extension UITableView {

    public var rx_dataSource: DelegateProxy {}

    public func rx_setDataSource(dataSource: UITableViewDataSource) -> Disposable {}

    public func rx_itemsWithCellFactory(source: O)(cellFactory: (UITableView, Int, S.Generator.Element) -> UITableViewCell) -> Disposable {}

    public func rx_itemsWithCellIdentifier(cellIdentifier: String, cellType: Cell.Type = Cell.self)(source: O)(configureCell: (Int, S.Generator.Element, Cell) -> Void) -> Disposable {}

    public func rx_itemsWithDataSource(dataSource: DataSource)(source: O) -> Disposable {}

    public var rx_itemSelected: ControlEvent<NSIndexPath> {}

    public var rx_itemDeselected: ControlEvent<NSIndexPath> {}

    public var rx_itemInserted: ControlEvent<NSIndexPath> {}

    public var rx_itemDeleted: ControlEvent<NSIndexPath> {}

    public var rx_itemMoved: ControlEvent<ItemMovedEvent> {}

    // This method only works in case one of the `rx_itemsWith*` methods was used, or data source implements `SectionedViewDataSourceType`
    public func rx_modelSelected<T>(modelType: T.Type) -> ControlEvent<T> {}

    // This method only works in case one of the `rx_itemsWith*` methods was used, or data source implements `SectionedViewDataSourceType`
    public func rx_modelDeselected<T>(modelType: T.Type) -> ControlEvent<T> {}

}
```

```swift
extension UICollectionView {

    public var rx_dataSource: DelegateProxy {}

    public func rx_setDataSource(dataSource: UICollectionViewDataSource) -> Disposable {}

    public func rx_itemsWithCellFactory(source: O)(cellFactory: (UICollectionView, Int, S.Generator.Element) -> UICollectionViewCell) -> Disposable {}

    public func rx_itemsWithCellIdentifier(cellIdentifier: String, cellType: Cell.Type = Cell.self)(source: O)(configureCell: (Int, S.Generator.Element, Cell) -> Void) -> Disposable {}

    public func rx_itemsWithDataSource(dataSource: DataSource)(source: O) -> Disposable {}

    public var rx_itemSelected: ControlEvent<NSIndexPath> {}

    public var rx_itemDeselected: ControlEvent<NSIndexPath> {}

    // This method only works in case one of the `rx_itemsWith*` methods was used, or data source implements `SectionedViewDataSourceType`
    public func rx_modelSelected<T>(modelType: T.Type) -> ControlEvent<T> {}

    // This method only works in case one of the `rx_itemsWith*` methods was used, or data source implements `SectionedViewDataSourceType`
    public func rx_modelSelected<T>(modelType: T.Type) -> ControlEvent<T> {}
}
```

```swift
extension UIGestureRecognizer {

    public var rx_event: ControlEvent<UIGestureRecognizer> {}

}
```

```swift
extension UIImagePickerController {

    public var rx_didFinishPickingMediaWithInfo: Observable<[String : AnyObject]> {}

    public var rx_didCancel: Observable<()> {}

}
```

```swift
extension UISegmentedControl {

    public var rx_value: ControlProperty<Int> {}

}
```

```swift
extension UISwitch {

    public var rx_value: ControlProperty<Bool> {}

}
```

```swift
extension UIActivityIndicatorView {

    public var rx_animating: AnyObserver<Bool> {}

}
```

**OSX**

```swift
extension NSControl {

    public var rx_controlEvent: ControlEvent<()> {}

    public var rx_enabled: AnyObserver<Bool> {}

}
```

```swift

extension NSSlider {

    public var rx_value: ControlProperty<Double> {}

}
```

```swift
extension NSButton {

    public var rx_tap: ControlEvent<Void> {}

    public var rx_state: ControlProperty<Int> {}

}
```

```swift
extension NSImageView {

    public var rx_image: ObserverOf<NSImage?> {}

    public func rx_imageAnimated(transitionType: String?) -> AnyObserver<NSImage?>
}
```

```swift
extension NSTextField {

    public var rx_delegate: DelegateProxy {}

    public var rx_text: ControlProperty<String> {}

}
```

## Comparison with ReactiveCocoa

RxSwift is somewhat similar to ReactiveCocoa since ReactiveCocoa borrows a large number of concepts from Rx.

One of the main goals of this project was to create a significantly simpler interface that is more aligned with other Rx implementations, offers richer concurrency model, offers more optimization opportunities and is more aligned with built-in Swift error handling mechanisms.

We've also decided to only rely on Swift/llvm compiler and not introduce any external dependencies.

Probably the main difference between these projects is the difference of approach in building abstractions.

The main goal of RxSwift project is to provide environment agnostic compositional computation glue abstracted in the form of observable sequences.
We then aim to improve the experience of using RxSwift on specific platforms. To do this RxCocoa  uses those generic computations to build more practical abstractions and wrap Foundation/Cocoa/UKit frameworks. That means that other libraries give context and semantics to the generic computation engine RxSwift provides such as `Driver`, `ControlProperty`, `ControlEvent`s and more.

One of the benefits to representing all these abstractions as a single concept; ​_observable sequences_​, is that all computation abstractions built on top of them are also composable in the same fundamental way. They all follow the same contract and implement the same interface.
 It is also easy to create flexible subscription (resource) sharing strategies or use one of the built-in ones: `share`, `shareReplay`, `publish`, `multicast`, `shareReplayLatestWhileConnected`...

This library also offers fine-tunable concurrency model. In case concurrent schedulers are used, observable sequence operators will preserve sequence properties. The same observable sequence operators will also know how to detect and optimally use known serial schedulers. ReactiveCocoa has a more limited concurrency model and only allows serial schedulers.

Multithreaded programming is really hard and detecting non trivial loops is even harder. That's why all operators are built in a fault tolerant way. Even in case element generation occurs during element processing (recursion), operators will try to handle that situation and prevent deadlocks. That means that in worst possible case programming error will cause stack overflow, but users won't have to manually kill the app, and you will get crash report in error reporting systems so you can find and fix the problem.



Design Rationale
================

## Why error type isn't generic

```Swift
enum Event<Element>  {
    case Next(Element)      // next element of a sequence
    case Error(ErrorType)   // sequence failed with error
    case Completed          // sequence terminated successfully
}
```

Let's discuss pros and cons of `ErrorType` being generic.

If you have generic error type you create additional impedance mismatch between two observables.

Let's say you have:

`Observable<String, E1>` and `Observable<String, E2>`

There isn't much you can do with them without figuring out what will be the resulting error type.

Will it be `E1`, `E2` or some new `E3` maybe? So you need a new set of operators just to solve that impedance mismatch.

This for sure hurts composition properties, and Rx really doesn't care about why sequence fails, it just usually forwards failure further down the observable chain.

There is additional problem that maybe in some cases operators will fail for some internal error, and in that case you won't be able to construct resulting error and report failure.

But ok, let's ignore that and assume we can use that to model sequences that don't error out. It looks like it could be useful for that purpose?

Well yes, it potentially could be, but lets consider why would you want to use sequences that don't error out.

One obvious application would be for permanent streams in UI layer that drive entire UI. But when you consider that case, it's not really only sufficient to use compiler to prove that sequences don't error out, you also need to prove other properties. Like that elements are observed on `MainScheduler`.

What you really need is a generic way to prove traits for sequences (`Observables`). And you could be interested in a lot of properties. For example:

* sequence terminates in finite time (server side)
* sequence contains only one element (if you are running some computation)
* sequence doesn't error out, never terminates and elements are delivered on main scheduler (UI)
* sequence doesn't error out, never terminates and elements are delivered on main scheduler, and has refcounted sharing (UI)
* sequence doesn't error out, never terminates and elements are delivered on specific background scheduler (audio engine)

What you really want is a general compiler enforced system of traits for observable sequences, and a set of invariant operators for those wanted properties.

A good analogy IMHO would be

```
1, 3.14, e, 2.79, 1 + 1i      <->    Observable<E>
1m/s, 1T, 5kg, 1.3 pounds     <->    Errorless observable, UI observable, Finite observable ...
```

There are many ways how to do that in Swift by either using composition or inheritance of observables.

Additional benefit of using unit system is that you can prove that UI code is executing on same scheduler and thus use lockless operators for all transformations.

Since Rx already doesn't have locks for single sequence operations, and all of the remaining locks are in statefull components (aka UI), that would practically remove all of the remaining locks out of Rx code and create compiler enforced lockless Rx code.

So IMHO, there really is no benefit of using typed Errors that couldn't be achieved cleaner in other ways while preserving Rx compositional semantics. And other ways also have huge other benefits.




## RxExamples

To run the example app:

* Open `Rx.xcworkspace`
* Choose one of example schemes (RxExample-iOS, RxExample-OSX) and hit `Run`.

You can also run the example app using CocoaPods.

```
pod try RxSwift
```





Examples
========

1. [Calculated variable](#calculated-variable)
1. [Simple UI bindings](#simple-ui-bindings)
1. [Autocomplete](#autocomplete)
1. [more examples](../RxExample)
1. [Playgrounds](Playgrounds.md)

## Calculated variable

Let's first start with some imperative swift code.Sw
The purpose of example is to bind identifier `c` to a value calculated from `a` and `b` if some condition is satisfied.

Here is the imperative swift code that calculates the value of `c`:

```swift
// this is usual imperative code
var c: String
var a = 1       // this will only assign value `1` to `a` once
var b = 2       // this will only assign value `2` to `b` once

if a + b >= 0 {
    c = "\(a + b) is positive" // this will only assign value to `c` once
}
```

The value of `c` is now `3 is positive`. But if we change the value of `a` to `4`, `c` will still contain the old value.

```swift
a = 4           // c will still be equal "3 is positive" which is not good
                // c should be equal to "6 is positive" because 4 + 2 = 6
```

This is not the wanted behavior.

To integrate RxSwift framework into your project just include framework in your project and write `import RxSwift`.

This is the same logic using RxSwift.

```swift
let a /*: Observable<Int>*/ = Variable(1)   // a = 1
let b /*: Observable<Int>*/ = Variable(2)   // b = 2

// This will "bind" rx variable `c` to definition
// if a + b >= 0 {
//      c = "\(a + b) is positive"
// }
let c = Observable.combineLatest(a, b) { $0 + $1 }     // combines latest values of variables `a` and `b` using `+`
  .filter { $0 >= 0 }               // if `a + b >= 0` is true, `a + b` is passed to map operator
  .map { "\($0) is positive" }      // maps `a + b` to "\(a + b) is positive"

// Since initial values are a = 1, b = 2
// 1 + 2 = 3 which is >= 0, `c` is intially equal to "3 is positive"

// To pull values out of rx variable `c`, subscribe to values from  `c`.
// `subscribeNext` means subscribe to next (fresh) values of variable `c`.
// That also includes the inital value "3 is positive".
c.subscribeNext { print($0) }          // prints: "3 is positive"

// Now let's increase the value of `a`
// a = 4 is in RxSwift
a.value = 4                                   // prints: 6 is positive
// Sum of latest values is now `4 + 2`, `6` is >= 0, map operator
// produces "6 is positive" and that result is "assigned" to `c`.
// Since the value of `c` changed, `{ print($0) }` will get called,
// and "6 is positive" is printed.

// Now let's change the value of `b`
// b = -8 is in RxSwift
b.value = -8                                 // doesn't print anything
// Sum of latest values is `4 + (-8)`, `-4` is not >= 0, map doesn't
// get executed.
// That means that `c` still contains "6 is positive" and that's correct.
// Since `c` hasn't been updated, that means next value hasn't been produced,
// and `{ print($0) }` won't be called.

// ...
```

## Simple UI bindings

* instead of binding to variables, let's bind to text field values (rx_text)
* next, parse that into an int and calculate if the number is prime using an async API (map)
* if text field value is changed before async call completes, new async call will be enqueued (concat)
* bind results to label (bindTo(resultLabel.rx_text))

```swift
let subscription/*: Disposable */ = primeTextField.rx_text      // type is Observable<String>
            .map { WolframAlphaIsPrime($0.toInt() ?? 0) }       // type is Observable<Observable<Prime>>
            .concat()                                           // type is Observable<Prime>
            .map { "number \($0.n) is prime? \($0.isPrime)" }   // type is Observable<String>
            .bindTo(resultLabel.rx_text)                        // return Disposable that can be used to unbind everything

// This will set resultLabel.text to "number 43 is prime? true" after
// server call completes.
primeTextField.text = "43"

// ...

// to unbind everything, just call
subscription.dispose()
```

All of the operators used in this example are the same operators used in the first example with variables. Nothing special about it.

## Autocomplete

If you are new to Rx, next example will probably be a little overwhelming, but it's here to demonstrate how RxSwift code looks like in real world examples.

The third example is a real world, complex UI async validation logic, with progress notifications.
All operations are cancelled the moment `disposeBag` is deallocated.

Let's give it a shot.

```swift
// bind UI control values directly
// use username from `usernameOutlet` as username values source
self.usernameOutlet.rx_text
    .map { username in

        // synchronous validation, nothing special here
        if username.isEmpty {
            // Convenience for constructing synchronous result.
            // In case there is mixed synchronous and asychronous code inside the same
            // method, this will construct an async result that is resolved immediatelly.
            return just((valid: false, message: "Username can't be empty."))
        }

        ...

        // Every user interface probably shows some state while async operation
        // is executing.
        // Let's assume that we want to show "Checking availability" while waiting for result.
        // valid parameter can be
        //  * true  - is valid
        //  * false - not valid
        //  * nil   - validation pending
        let loadingValue = (valid: nil, message: "Checking availability ...")

        // This will fire a server call to check if the username already exists.
        // Guess what, its type is `Observable<ValidationResult>`
        return API.usernameAvailable(username)
          .map { available in
              if available {
                  return (true, "Username available")
              }
              else {
                  return (false, "Username already taken")
              }
          }
          // use `loadingValue` until server responds
          .startWith(loadingValue)
    }
// Since we now have `Observable<Observable<ValidationResult>>`
// we somehow need to return to normal `Observable` world.
// We could use `concat` operator from second example, but we really
// want to cancel pending asynchronous operation if new username is
// provided.
// That's what `switchLatest` does
    .switchLatest()
// Now we need to bind that to the user interface somehow.
// Good old `subscribeNext` can do that
// That's the end of `Observable` chain.
// This will produce a `Disposable` object that can unbind everything and cancel
// pending async operations.
    .subscribeNext { valid in
        errorLabel.textColor = validationColor(valid)
        errorLabel.text = valid.message
    }
// Why would we do it manually, that's tedious,
// let's dispose everything automagically on view controller dealloc.
    .addDisposableTo(disposeBag)
```

Can't get any simpler than this. There are [more examples](../RxExample) in the repository, so feel free to check them out.

They include examples on how to use it in the context of MVVM pattern or without it.






Getting Started
===============

This project tries to be consistent with [ReactiveX.io](http://reactivex.io/). The general cross platform documentation and tutorials should also be valid in case of `RxSwift`.

1. [Observables aka Sequences](#observables-aka-sequences)
1. [Disposing](#disposing)
1. [Implicit `Observable` guarantees](#implicit-observable-guarantees)
1. [Creating your first `Observable` (aka observable sequence)](#creating-your-own-observable-aka-observable-sequence)
1. [Creating an `Observable` that performs work](#creating-an-observable-that-performs-work)
1. [Sharing subscription and `shareReplay` operator](#sharing-subscription-and-sharereplay-operator)
1. [Operators](#operators)
1. [Playgrounds](#playgrounds)
1. [Custom operators](#custom-operators)
1. [Error handling](#error-handling)
1. [Debugging Compile Errors](#debugging-compile-errors)
1. [Debugging](#debugging)
1. [Debugging memory leaks](#debugging-memory-leaks)
1. [KVO](#kvo)
1. [UI layer tips](#ui-layer-tips)
1. [Making HTTP requests](#making-http-requests)
1. [RxDataSourceStarterKit](#rxdatasourcestarterkit)
1. [Driver](Units.md#driver-unit)
1. [Examples](Examples.md)

# Observables aka Sequences

## Basics
The [Equivalence](MathBehindRx.md) of observer patterns (`Observable<Element>`) and sequences (`Generator`s)
is one of the most important things to understand about Rx.

The observer pattern is needed because we want to model asynchronous behavior.
That equivalence enables the implementation of high level sequence operations as operators on `Observable`s.

Sequences are a simple, familiar concept that is **easy to visualize**.

People are creatures with huge visual cortexes. When we can visualize a concept easily, it's a lot easier to reason about it.

We can lift a lot of the cognitive load from trying to simulate event state machines inside every Rx operator onto high level operations over sequences.

If we don't use Rx but model asynchronous systems, that probably means that our code is full of state machines and transient states that we need to simulate instead of abstracting away.

Lists and sequences are probably one of the first concepts mathematicians and programmers learn.

Here is a sequence of numbers:


```
--1--2--3--4--5--6--| // terminates normally
```

Another sequence, with characters:

```
--a--b--a--a--a---d---X // terminates with error
```

Some sequences are finite and others are infinite, like a sequence of button taps:

```
---tap-tap-------tap--->
```

These are called marble diagrams. There are more marble diagrams at [rxmarbles.com](http://rxmarbles.com).

If we were to specify sequence grammar as a regular expression it would look like:

**Next* (Error | Completed)?**

This describes the following:

* **Sequences can have 0 or more elements.**
* **Once an `Error` or `Completed` event is received, the sequence cannot produce any other element.**

Sequences in Rx are described by a push interface (aka callback).

```swift
enum Event<Element>  {
    case Next(Element)      // next element of a sequence
    case Error(ErrorType)   // sequence failed with error
    case Completed          // sequence terminated successfully
}

class Observable<Element> {
    func subscribe(observer: Observer<Element>) -> Disposable
}

protocol ObserverType {
    func on(event: Event<Element>)
}
```

**When a sequence sends the `Complete` or `Error` event all internal resources that compute sequence elements will be freed.**

**To cancel production of sequence elements and free resources immediately, call `dispose` on the returned subscription.**

If a sequence terminates in finite time, not calling `dispose` or not using `addDisposableTo(disposeBag)` won't cause any permanent resource leaks. However, those resources will be used until the sequence completes, either by finishing production of elements or returning an error.

If a sequence does not terminate in some way, resources will be allocated permanently unless `dispose` is called manually, automatically inside of a `disposeBag`, `takeUntil` or in some other way.

**Using dispose bags or `takeUntil` operator is a robust way of making sure resources are cleaned up. We recommend using them in production even if the sequences will terminate in finite time.**

In case you are curious why `ErrorType` isn't generic, you can find explanation [here](DesignRationale.md#why-error-type-isnt-generic).

## Disposing

There is one additional way an observed sequence can terminate. When we are done with a sequence and we want to release all of the resources allocated to compute the upcoming elements, we can call `dispose` on a subscription.

Here is an example with the `interval` operator.

```swift
let subscription = Observable<Int>.interval(0.3, scheduler: scheduler)
    .subscribe { event in
        print(event)
    }

NSThread.sleepForTimeInterval(2)

subscription.dispose()

```

This will print:

```
0
1
2
3
4
5
```

Note the you usually do not want to manually call `dispose`; this is only educational example. Calling dispose manually is usually a bad code smell. There are better ways to dispose subscriptions. We can use `DisposeBag`, the `takeUntil` operator, or some other mechanism.

So can this code print something after the `dispose` call executed? The answer is: it depends.

* If the `scheduler` is a **serial scheduler** (ex. `MainScheduler`) and `dispose` is called on **on the same serial scheduler**, the answer is **no**.

* Otherwise it is **yes**.

You can find out more about schedulers [here](Schedulers.md).

You simply have two processes happening in parallel.

* one is producing elements
* the other is disposing the subscription

The question "Can something be printed after?" does not even make sense in the case that those processes are on different schedulers.

A few more examples just to be sure (`observeOn` is explained [here](Schedulers.md)).

In case we have something like:

```swift
let subscription = Observable<Int>.interval(0.3, scheduler: scheduler)
            .observeOn(MainScheduler.instance)
            .subscribe { event in
                print(event)
            }

// ....

subscription.dispose() // called from main thread

```

**After the `dispose` call returns, nothing will be printed. That is guaranteed.**

Also, in this case:

```swift
let subscription = Observable<Int>.interval(0.3, scheduler: scheduler)
            .observeOn(serialScheduler)
            .subscribe { event in
                print(event)
            }

// ...

subscription.dispose() // executing on same `serialScheduler`

```

**After the `dispose` call returns, nothing will be printed. That is guaranteed.**

### Dispose Bags

Dispose bags are used to return ARC like behavior to RX.

When a `DisposeBag` is deallocated, it will call `dispose` on each of the added disposables.

It does not have a `dispose` method and therefore does not allow calling explicit dispose on purpose. If immediate cleanup is required, we can just create a new bag.

```swift
  self.disposeBag = DisposeBag()
```

This will clear old references and cause disposal of resources.

If that explicit manual disposal is still wanted, use `CompositeDisposable`. **It has the wanted behavior but once that `dispose` method is called, it will immediately dispose any newly added disposable.**

### Take until

Additional way to automatically dispose subscription on dealloc is to use `takeUntil` operator.

```swift
sequence
    .takeUntil(self.rx_deallocated)
    .subscribe {
        print($0)
    }
```

## Implicit `Observable` guarantees

There is also a couple of additional guarantees that all sequence producers (`Observable`s) must honor.

It doesn't matter on which thread they produce elements, but if they generate one element and send it to the observer `observer.on(.Next(nextElement))`, they can't send next element until `observer.on` method has finished execution.

Producers also cannot send terminating `.Completed` or `.Error` in case `.Next` event hasn't finished.

In short, consider this example:

```swift
someObservable
  .subscribe { (e: Event<Element>) in
      print("Event processing started")
      // processing
      print("Event processing ended")
  }
```

this will always print:

```
Event processing started
Event processing ended
Event processing started
Event processing ended
Event processing started
Event processing ended
```

it can never print:

```
Event processing started
Event processing started
Event processing ended
Event processing ended
```

## Creating your own `Observable` (aka observable sequence)

There is one crucial thing to understand about observables.

**When an observable is created, it doesn't perform any work simply because it has been created.**

It is true that `Observable` can generate elements in many ways. Some of them cause side effects and some of them tap into existing running processes like tapping into mouse events, etc.

**But if you just call a method that returns an `Observable`, no sequence generation is performed, and there are no side effects. `Observable` is just a definition how the sequence is generated and what parameters are used for element generation. Sequence generation starts when `subscribe` method is called.**

E.g. Let's say you have a method with similar prototype:

```swift
func searchWikipedia(searchTerm: String) -> Observable<Results> {}
```

```swift
let searchForMe = searchWikipedia("me")

// no requests are performed, no work is being done, no URL requests were fired

let cancel = searchForMe
  // sequence generation starts now, URL requests are fired
  .subscribeNext { results in
      print(results)
  }

```

There are a lot of ways how you can create your own `Observable` sequence. Probably the easiest way is using `create` function.

Let's create a function which creates a sequence that returns one element upon subscription. That function is called 'just'.

*This is the actual implementation*

```swift
func myJust<E>(element: E) -> Observable<E> {
    return Observable.create { observer in
        observer.on(.Next(element))
        observer.on(.Completed)
        return NopDisposable.instance
    }
}

myJust(0)
    .subscribeNext { n in
      print(n)
    }
```

this will print:

```
0
```

Not bad. So what is the `create` function?

It's just a convenience method that enables you to easily implement `subscribe` method using Swift closures. Like `subscribe` method it takes one argument, `observer`, and returns disposable.

Sequence implemented this way is actually synchronous. It will generate elements and terminate before `subscribe` call returns disposable representing subscription. Because of that it doesn't really matter what disposable it returns, process of generating elements can't be interrupted.

When generating synchronous sequences, the usual disposable to return is singleton instance of `NopDisposable`.

Lets now create an observable that returns elements from an array.

*This is the actual implementation*

```swift
func myFrom<E>(sequence: [E]) -> Observable<E> {
    return Observable.create { observer in
        for element in sequence {
            observer.on(.Next(element))
        }

        observer.on(.Completed)
        return NopDisposable.instance
    }
}

let stringCounter = myFrom(["first", "second"])

print("Started ----")

// first time
stringCounter
    .subscribeNext { n in
        print(n)
    }

print("----")

// again
stringCounter
    .subscribeNext { n in
        print(n)
    }

print("Ended ----")
```

This will print:

```
Started ----
first
second
----
first
second
Ended ----
```

## Creating an `Observable` that performs work

Ok, now something more interesting. Let's create that `interval` operator that was used in previous examples.

*This is equivalent of actual implementation for dispatch queue schedulers*

```swift
func myInterval(interval: NSTimeInterval) -> Observable<Int> {
    return Observable.create { observer in
        print("Subscribed")
        let queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0)
        let timer = dispatch_source_create(DISPATCH_SOURCE_TYPE_TIMER, 0, 0, queue)

        var next = 0

        dispatch_source_set_timer(timer, 0, UInt64(interval * Double(NSEC_PER_SEC)), 0)
        let cancel = AnonymousDisposable {
            print("Disposed")
            dispatch_source_cancel(timer)
        }
        dispatch_source_set_event_handler(timer, {
            if cancel.disposed {
                return
            }
            observer.on(.Next(next))
            next += 1
        })
        dispatch_resume(timer)

        return cancel
    }
}
```

```swift
let counter = myInterval(0.1)

print("Started ----")

let subscription = counter
    .subscribeNext { n in
       print(n)
    }

NSThread.sleepForTimeInterval(0.5)

subscription.dispose()

print("Ended ----")
```

This will print
```
Started ----
Subscribed
0
1
2
3
4
Disposed
Ended ----
```

What if you would write

```swift
let counter = myInterval(0.1)

print("Started ----")

let subscription1 = counter
    .subscribeNext { n in
       print("First \(n)")
    }
let subscription2 = counter
    .subscribeNext { n in
       print("Second \(n)")
    }

NSThread.sleepForTimeInterval(0.5)

subscription1.dispose()

NSThread.sleepForTimeInterval(0.5)

subscription2.dispose()

print("Ended ----")
```

this would print:

```
Started ----
Subscribed
Subscribed
First 0
Second 0
First 1
Second 1
First 2
Second 2
First 3
Second 3
First 4
Second 4
Disposed
Second 5
Second 6
Second 7
Second 8
Second 9
Disposed
Ended ----
```

**Every subscriber upon subscription usually generates it's own separate sequence of elements. Operators are stateless by default. There is vastly more stateless operators then stateful ones.**

## Sharing subscription and `shareReplay` operator

But what if you want that multiple observers share events (elements) from only one subscription?

There are two things that need to be defined.

* How to handle past elements that have been received before the new subscriber was interested in observing them (replay latest only, replay all, replay last n)
* How to decide when to fire that shared subscription (refCount, manual or some other algorithm)

The usual choice is a combination of `replay(1).refCount()` aka `shareReplay()`.

```swift
let counter = myInterval(0.1)
    .shareReplay(1)

print("Started ----")

let subscription1 = counter
    .subscribeNext { n in
       print("First \(n)")
    }
let subscription2 = counter
    .subscribeNext { n in
       print("Second \(n)")
    }

NSThread.sleepForTimeInterval(0.5)

subscription1.dispose()

NSThread.sleepForTimeInterval(0.5)

subscription2.dispose()

print("Ended ----")
```

this will print

```
Started ----
Subscribed
First 0
Second 0
First 1
Second 1
First 2
Second 2
First 3
Second 3
First 4
Second 4
First 5
Second 5
Second 6
Second 7
Second 8
Second 9
Disposed
Ended ----
```

Notice how now there is only one `Subscribed` and `Disposed` event.

Behavior for URL observables is equivalent.

This is how HTTP requests are wrapped in Rx. It's pretty much the same pattern like the `interval` operator.

```swift
extension NSURLSession {
    public func rx_response(request: NSURLRequest) -> Observable<(NSData, NSURLResponse)> {
        return Observable.create { observer in
            let task = self.dataTaskWithRequest(request) { (data, response, error) in
                guard let response = response, data = data else {
                    observer.on(.Error(error ?? RxCocoaURLError.Unknown))
                    return
                }

                guard let httpResponse = response as? NSHTTPURLResponse else {
                    observer.on(.Error(RxCocoaURLError.NonHTTPResponse(response: response)))
                    return
                }

                observer.on(.Next(data, httpResponse))
                observer.on(.Completed)
            }

            task.resume()

            return AnonymousDisposable {
                task.cancel()
            }
        }
    }
}
```

## Operators

There are numerous operators implemented in RxSwift. The complete list can be found [here](API.md).

Marble diagrams for all operators can be found on [ReactiveX.io](http://reactivex.io/)

Almost all operators are demonstrated in [Playgrounds](../Rx.playground).

To use playgrounds please open `Rx.xcworkspace`, build `RxSwift-OSX` scheme and then open playgrounds in `Rx.xcworkspace` tree view.

In case you need an operator, and don't know how to find it there a [decision tree of operators]() http://reactivex.io/documentation/operators.html#tree).

[Supported RxSwift operators](API.md#rxswift-supported-operators) are also grouped by function they perform, so that can also help.

### Custom operators

There are two ways how you can create custom operators.

#### Easy way

All of the internal code uses highly optimized versions of operators, so they aren't the best tutorial material. That's why it's highly encouraged to use standard operators.

Fortunately there is an easier way to create operators. Creating new operators is actually all about creating observables, and previous chapter already describes how to do that.

Lets see how an unoptimized map operator can be implemented.

```swift
extension ObservableType {
    func myMap<R>(transform: E -> R) -> Observable<R> {
        return Observable.create { observer in
            let subscription = self.subscribe { e in
                    switch e {
                    case .Next(let value):
                        let result = transform(value)
                        observer.on(.Next(result))
                    case .Error(let error):
                        observer.on(.Error(error))
                    case .Completed:
                        observer.on(.Completed)
                    }
                }

            return subscription
        }
    }
}
```

So now you can use your own map:

```swift
let subscription = myInterval(0.1)
    .myMap { e in
        return "This is simply \(e)"
    }
    .subscribeNext { n in
        print(n)
    }
```

and this will print

```
Subscribed
This is simply 0
This is simply 1
This is simply 2
This is simply 3
This is simply 4
This is simply 5
This is simply 6
This is simply 7
This is simply 8
...
```

### Life happens

So what if it's just too hard to solve some cases with custom operators? You can exit the Rx monad, perform actions in imperative world, and then tunnel results to Rx again using `Subject`s.

This isn't something that should be practiced often, and is a bad code smell, but you can do it.

```swift
  let magicBeings: Observable<MagicBeing> = summonFromMiddleEarth()

  magicBeings
    .subscribeNext { being in     // exit the Rx monad  
        self.doSomeStateMagic(being)
    }
    .addDisposableTo(disposeBag)

  //
  //  Mess
  //
  let kitten = globalParty(   // calculate something in messy world
    being,
    UIApplication.delegate.dataSomething.attendees
  )
  kittens.on(.Next(kitten))   // send result back to rx
  //
  // Another mess
  //

  let kittens = Variable(firstKitten) // again back in Rx monad

  kittens.asObservable()
    .map { kitten in
      return kitten.purr()
    }
    // ....
```

Every time you do this, somebody will probably write this code somewhere

```swift
  kittens
    .subscribeNext { kitten in
      // so something with kitten
    }
    .addDisposableTo(disposeBag)
```

so please try not to do this.

## Playgrounds

If you are unsure how exactly some of the operators work, [playgrounds](../Rx.playground) contain almost all of the operators already prepared with small examples that illustrate their behavior.

**To use playgrounds please open Rx.xcworkspace, build RxSwift-OSX scheme and then open playgrounds in Rx.xcworkspace tree view.**

**To view the results of the examples in the playgrounds, please open the `Assistant Editor`. You can open `Assistant Editor` by clicking on `View > Assistant Editor > Show Assistant Editor`**

## Error handling

The are two error mechanisms.

### Asynchronous error handling mechanism in observables

Error handling is pretty straightforward. If one sequence terminates with error, then all of the dependent sequences will terminate with error. It's usual short circuit logic.

You can recover from failure of observable by using `catch` operator. There are various overloads that enable you to specify recovery in great detail.

There is also `retry` operator that enables retries in case of errored sequence.

## Debugging Compile Errors

When writing elegant RxSwift/RxCocoa code, you are probably relying heavily on compiler to deduce types of `Observable`s. This is one of the reasons why Swift is awesome, but it can also be frustrating sometimes.

```swift
images = word
    .filter { $0.containsString("important") }
    .flatMap { word in
        return self.api.loadFlickrFeed("karate")
            .catchError { error in
                return just(JSON(1))
            }
      }
```

If compiler reports that there is an error somewhere in this expression, I would suggest first annotating return types.

```swift
images = word
    .filter { s -> Bool in s.containsString("important") }
    .flatMap { word -> Observable<JSON> in
        return self.api.loadFlickrFeed("karate")
            .catchError { error -> Observable<JSON> in
                return just(JSON(1))
            }
      }
```

If that doesn't work, you can continue adding more type annotations until you've localized the error.

```swift
images = word
    .filter { (s: String) -> Bool in s.containsString("important") }
    .flatMap { (word: String) -> Observable<JSON> in
        return self.api.loadFlickrFeed("karate")
            .catchError { (error: NSError) -> Observable<JSON> in
                return just(JSON(1))
            }
      }
```

**I would suggest first annotating return types and arguments of closures.**

Usually after you have fixed the error, you can remove the type annotations to clean up your code again.

## Debugging

Using debugger alone is useful, but you can also use `debug`. `debug` operator will print out all events to standard output and you can add also label those events.

`debug` acts like a probe. Here is an example of using it:

```swift
let subscription = myInterval(0.1)
    .debug("my probe")
    .map { e in
        return "This is simply \(e)"
    }
    .subscribeNext { n in
        print(n)
    }

NSThread.sleepForTimeInterval(0.5)

subscription.dispose()
```

will print

```
[my probe] subscribed
Subscribed
[my probe] -> Event Next(Box(0))
This is simply 0
[my probe] -> Event Next(Box(1))
This is simply 1
[my probe] -> Event Next(Box(2))
This is simply 2
[my probe] -> Event Next(Box(3))
This is simply 3
[my probe] -> Event Next(Box(4))
This is simply 4
[my probe] dispose
Disposed
```

You can also use `subscribe` instead of `subscribeNext`

```swift
NSURLSession.sharedSession().rx_JSON(request)
   .map { json in
       return parse()
   }
   .subscribe { n in      // this subscribes on all events including error and completed
       print(n)
   }
```

## Debugging memory leaks

In debug mode Rx tracks all allocated resources in a global variable `resourceCount`.

In case you want to have some resource leak detection logic, the simplest method is just printing out `RxSwift.resourceCount` periodically to output.

```swift
    /* add somewhere in
    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool
    */
    _ = Observable<Int>.interval(1, scheduler: MainScheduler.instance)
        .subscribeNext { _ in
        print("Resource count \(RxSwift.resourceCount)")
    }
```

Most efficient way to test for memory leaks is:
* navigate to your screen and use it
* navigate back
* observe initial resource count
* navigate second time to your screen and use it
* navigate back
* observe final resource count

In case there is a difference in resource count between initial and final resource counts, there might be a memory
leak somewhere.

The reason why 2 navigations are suggested is because first navigation forces loading of lazy resources.

## Variables

`Variable`s represent some observable state. `Variable` without containing value can't exist because initializer requires initial value.

Variable wraps a [`Subject`](http://reactivex.io/documentation/subject.html). More specifically it is a `BehaviorSubject`.  Unlike `BehaviorSubject`, it only exposes `value` interface, so variable can never terminate or fail.

It will also broadcast it's current value immediately on subscription.

After variable is deallocated, it will complete the observable sequence returned from `.asObservable()`.

```swift
let variable = Variable(0)

print("Before first subscription ---")

_ = variable.asObservable()
    .subscribe(onNext: { n in
        print("First \(n)")
    }, onCompleted: {
        print("Completed 1")
    })

print("Before send 1")

variable.value = 1

print("Before second subscription ---")

_ = variable.asObservable()
    .subscribe(onNext: { n in
        print("Second \(n)")
    }, onCompleted: {
        print("Completed 2")
    })

variable.value = 2

print("End ---")
```

will print

```
Before first subscription ---
First 0
Before send 1
First 1
Before second subscription ---
Second 1
First 2
Second 2
End ---
Completed 1
Completed 2
```

## KVO

KVO is an Objective-C mechanism. That means that it wasn't built with type safety in mind. This project tries to solve some of the problems.

There are two built in ways this library supports KVO.

```swift
// KVO
extension NSObject {
    public func rx_observe<E>(type: E.Type, _ keyPath: String, options: NSKeyValueObservingOptions, retainSelf: Bool = true) -> Observable<E?> {}
}

#if !DISABLE_SWIZZLING
// KVO
extension NSObject {
    public func rx_observeWeakly<E>(type: E.Type, _ keyPath: String, options: NSKeyValueObservingOptions) -> Observable<E?> {}
}
#endif
```

Example how to observe frame of `UIView`.

**WARNING: UIKit isn't KVO compliant, but this will work.**

```swift
view
  .rx_observe(CGRect.self, "frame")
  .subscribeNext { frame in
    ...
  }
```

or

```swift
view
  .rx_observeWeakly(CGRect.self, "frame")
  .subscribeNext { frame in
    ...
  }
```

### `rx_observe`

`rx_observe` is more performant because it's just a simple wrapper around KVO mechanism, but it has more limited usage scenarios

* it can be used to observe paths starting from `self` or from ancestors in ownership graph (`retainSelf = false`)
* it can be used to observe paths starting from descendants in ownership graph (`retainSelf = true`)
* the paths have to consist only of `strong` properties, otherwise you are risking crashing the system by not unregistering KVO observer before dealloc.

E.g.

```swift
self.rx_observe(CGRect.self, "view.frame", retainSelf: false)
```

### `rx_observeWeakly`

`rx_observeWeakly` has somewhat slower then `rx_observe` because it has to handle object deallocation in case of weak references.

It can be used in all cases where `rx_observe` can be used and additionally

* because it won't retain observed target, it can be used to observe arbitrary object graph whose ownership relation is unknown
* it can be used to observe `weak` properties

E.g.

```swift
someSuspiciousViewController.rx_observeWeakly(Bool.self, "behavingOk")
```

### Observing structs

KVO is an Objective-C mechanism so it relies heavily on `NSValue`.

**RxCocoa has built in support for KVO observing of `CGRect`, `CGSize` and `CGPoint` structs.**

When observing some other structures it is necessary to extract those structures from `NSValue` manually.

[Here](../RxCocoa/Common/KVORepresentable+CoreGraphics.swift) are examples how to extend KVO observing mechanism and `rx_observe*` methods for other structs by implementing `KVORepresentable` protocol.

## UI layer tips

There are certain things that your `Observable`s need to satisfy in the UI layer when binding to UIKit controls.

### Threading

`Observable`s need to send values on `MainScheduler`(UIThread). That's just a normal UIKit/Cocoa requirement.

It is usually a good idea for you APIs to return results on `MainScheduler`. In case you try to bind something to UI from background thread, in **Debug** build RxCocoa will usually throw an exception to inform you of that.

To fix this you need to add `observeOn(MainScheduler.instance)`.

**NSURLSession extensions don't return result on `MainScheduler` by default.**

### Errors

You can't bind failure to UIKit controls because that is undefined behavior.

If you don't know if `Observable` can fail, you can ensure it can't fail using `catchErrorJustReturn(valueThatIsReturnedWhenErrorHappens)`, **but after an error happens the underlying sequence will still complete**.

If the wanted behavior is for underlying sequence to continue producing elements, some version of `retry` operator is needed.

### Sharing subscription

You usually want to share subscription in the UI layer. You don't want to make separate HTTP calls to bind the same data to multiple UI elements.

Let's say you have something like this:

```swift
let searchResults = searchText
    .throttle(0.3, $.mainScheduler)
    .distinctUntilChanged
    .flatMapLatest { query in
        API.getSearchResults(query)
            .retry(3)
            .startWith([]) // clears results on new search term
            .catchErrorJustReturn([])
    }
    .shareReplay(1)              // <- notice the `shareReplay` operator
```

What you usually want is to share search results once calculated. That is what `shareReplay` means.

**It is usually a good rule of thumb in the UI layer to add `shareReplay` at the end of transformation chain because you really want to share calculated results. You don't want to fire separate HTTP connections when binding `searchResults` to multiple UI elements.**

**Also take a look at `Driver` unit. It is designed to transparently wrap those `shareReply` calls, make sure elements are observed on main UI thread and that no error can be bound to UI.**

## Making HTTP requests

Making http requests is one of the first things people try.

You first need to build `NSURLRequest` object that represents the work that needs to be done.

Request determines is it a GET request, or a POST request, what is the request body, query parameters ...

This is how you can create a simple GET request

```swift
let request = NSURLRequest(URL: NSURL(string: "http://en.wikipedia.org/w/api.php?action=parse&page=Pizza&format=json")!)
```

If you want to just execute that request outside of composition with other observables, this is what needs to be done.

```swift
let responseJSON = NSURLSession.sharedSession().rx_JSON(request)

// no requests will be performed up to this point
// `responseJSON` is just a description how to fetch the response

let cancelRequest = responseJSON
    // this will fire the request
    .subscribeNext { json in
        print(json)
    }

NSThread.sleepForTimeInterval(3)

// if you want to cancel request after 3 seconds have passed just call
cancelRequest.dispose()

```

**NSURLSession extensions don't return result on `MainScheduler` by default.**

In case you want a more low level access to response, you can use:

```swift
NSURLSession.sharedSession().rx_response(myNSURLRequest)
    .debug("my request") // this will print out information to console
    .flatMap { (data: NSData!, response: NSURLResponse!) -> Observable<String> in
        if let response = response as? NSHTTPURLResponse {
            if 200 ..< 300 ~= response.statusCode {
                return just(transform(data))
            }
            else {
                return failWith(yourNSError)
            }
        }
        else {
            rxFatalError("response = nil")
            return failWith(yourNSError)
        }
    }
    .subscribe { event in
        print(event) // if error happened, this will also print out error to console
    }
```
### Logging HTTP traffic

In debug mode RxCocoa will log all HTTP request to console by default. In case you want to change that behavior, please set `Logging.URLRequests` filter.

```swift
// read your own configuration
public struct Logging {
    public typealias LogURLRequest = (NSURLRequest) -> Bool

    public static var URLRequests: LogURLRequest =  { _ in
    #if DEBUG
        return true
    #else
        return false
    #endif
    }
}
```

## RxDataSourceStarterKit

... is a set of classes that implement fully functional reactive data sources for `UITableView`s and `UICollectionView`s.

Source code, more information and rationale why these classes are separated into their directory can be found [here](../RxExample/RxDataSourceStarterKit).

Using them should come down to just importing all of the files into your project.

Fully functional demonstration how to use them is included in the [RxExample](../RxExample) project.






Hot and Cold Observables
========================

IMHO, I would suggest to more think of this as property of sequences and not separate types because they are represented by the same abstraction that fits them perfectly, `Observable` sequence.

This is a definition from ReactiveX.io

> When does an Observable begin emitting its sequence of items? It depends on the Observable. A “hot” Observable may begin emitting items as soon as it is created, and so any observer who later subscribes to that Observable may start observing the sequence somewhere in the middle. A “cold” Observable, on the other hand, waits until an observer subscribes to it before it begins to emit items, and so such an observer is guaranteed to see the whole sequence from the beginning.

| Hot Observables                                                                                         | Cold observables                                                              |
|---------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| ... are sequences                                                                                       | ... are sequences                                                             |
| Use resources ("produce heat") no matter if there is any observer subscribed.                           | Don't use resources (don't produce heat) until observer subscribes.           |
| Variables / properties / constants, tap coordinates, mouse coordinates, UI control values, current time | Async operations, HTTP Connections, TCP connections, streams                  |
| Usually contains ~ N elements                                                                           | Usually contains ~ 1 element                                                  |
| Sequence elements are produced no matter if there is any observer subscribed.                           | Sequence elements are produced only if there is a subscribed observer.        |
| Sequence computation resources are usually shared between all of the subscribed observers.              | Sequence computation resources are usually allocated per subscribed observer. |
| Usually stateful                                                                                        | Usually stateless                                                             |





## Build / Install / Run

Rx doesn't contain any external dependencies.

These are currently supported options:

### Manual

Open Rx.xcworkspace, choose `RxExample` and hit run. This method will build everything and run sample app

### [CocoaPods](https://guides.cocoapods.org/using/using-cocoapods.html)

**:warning: IMPORTANT! For tvOS support CocoaPods `0.39` is required. :warning:**

```
# Podfile
use_frameworks!

target 'YOUR_TARGET_NAME' do
    pod 'RxSwift',    '~> 2.0'
    pod 'RxCocoa',    '~> 2.0'
    pod 'RxBlocking', '~> 2.0'
    pod 'RxTests',    '~> 2.0'
end
```

replace `YOUR_TARGET_NAME`, then type in the `Podfile` directory:

```
$ pod install
```

### [Carthage](https://github.com/Carthage/Carthage)

**Xcode 7.1 required**

Add this to `Cartfile`

```
github "ReactiveX/RxSwift" ~> 2.0
```

```
$ carthage update
```

### Manually using git submodules

* Add RxSwift as a submodule

```
$ git submodule add git@github.com:ReactiveX/RxSwift.git
```

* Drag `Rx.xcodeproj` into Project Navigator
* Go to `Project > Targets > Build Phases > Link Binary With Libraries`, click `+` and select `RxSwift-[Platform]` and `RxCocoa-[Platform]` targets






**Please copy the following template [here](https://github.com/ReactiveX/RxSwift/issues/new) and fill in the missing fields so we can help you as soon as possible.**

**If you don't have something to report in the following format, maybe you don't really have an issue an it's easier and faster to resolve doubts in [slack channel](http://http://slack.rxswift.org/) first.**

```
*Short description*:

  description here

*Code that reproduces the issue*:

  code goes here

*Xcode version*:

  Xcode version goes here

*Expected outcome*:

  what do you expect to happen goes here

*What actually happens*:

  what actually happens goes here

// filling additional information below is optional, but resolving your issue could be potentially a lot faster

*Integration method*:
  (so we don't lose time investigating wrong integration)
  * CocoaPods
  * Carthage
  * Git submodules

*I have multiple versions of Xcode installed*:
  (so we can understand can this cause your issue)
  * yes (which ones)
  * no

*How long have I been using this project*:
  (this is so we can understand your level of knowledge
    and formulate the response in an appropriate manner)
  * just starting
  * I have a small code base
  * I have a significant code base


```






Linux
=====

We've made a proof of concept for Linux.

To test it, create `Package.swift` in your test directory with the following content:

```
import PackageDescription

let package = Package(
    name: "MyShinyUnicornCat",
    dependencies: [
        .Package(url: "https://github.com/ReactiveX/RxSwift.git", Version(2, 0, 0))
    ]
)
```

What does work:
* Distribution using Swift Package Manager
* Single Threaded mode (CurrentThreadScheduler)
* Half of unit tests are passing.
* Projects that can be compiled and "used":
    * RxSwift
    * RxBlocking
    * RxTests

What doesn't work:
* Schedulers - because they are dependent on https://github.com/apple/swift-corelibs-libdispatch and it still hasn't been released
* Multithreading - still no access to c11 locks
* For some reason it looks like Swift compiler generates wrong code when using `ErrorType` on `Linux`, so don't use errors, otherwise you can get weird crashes.







Math Behind Rx
==============

## Duality between Observer and Iterator / Enumerator / Generator / Sequences

There is a duality between observer and generator pattern. That's what enables transition from async callback world to synchronous world of sequence transformations.

In short, enumerator and observer pattern both describe sequences. It's pretty obvious why does enumerator defined sequence, but what about observer.

There is also a pretty simple explanation that doesn't include a lot of math. Assume that you are observing mouse movements. Every received mouse movement is an element of a sequence of mouse movements over time.

In short, there are two basic ways elements of a sequence can be accessed.

* Push interface - Observer (observed elements over time make a sequence)
* Pull interface - Iterator / Enumerator / Generator

To learn more about this, these videos should help

You can also see a more formal explanation explained in a fun way in this video:

[Expert to Expert: Brian Beckman and Erik Meijer - Inside the .NET Reactive Framework (Rx) (video)](https://www.youtube.com/watch?v=looJcaeboBY)




# Migration from RxSwift 1.9 to RxSwift 2.0 version

The migration should be pretty straightforward. The changes are mostly cosmetic, so all features are still there.

* Find replace all `>- ` to `.`
* Find replace all "variable" to "shareReplay(1)"
* Find replace all "catch" to "catchErrorJustReturn"
* Find replace all "returnElement" to "Observable.just"
* Find replace all "failWith" to "Observable.error"
* Find replace all "never" to "Observable.never"
* Find replace all "empty" to "Observable.empty"
* Since we've moved from `>-` to `.`, free functions are now methods, so it's `.switchLatest()`, `.distinctUntilChanged()`, ... instead of `>- switchLatest`, `>- distinctUntilChanged`
* we've moved from free functions to extensions so it's now `[a, b, c].concat()`, `.merge()`, ... instead of `concat([a, b, c])`, `merge(sequences)`
* Now it's `subscribe { n in ... }.addDisposableTo(disposeBag)` instead of `>- disposeBag.addDisposable`
* Method `next` on `Variable` is now `value` setter
* If you want to use `tableViews`/`collectionViews`, this is the basic use case now

```swift
viewModel.rows
            .bindTo(resultsTableView.rx_itemsWithCellIdentifier("WikipediaSearchCell", cellType: WikipediaSearchCell.self)) { (_, viewModel, cell) in
                cell.viewModel = viewModel
            }
            .addDisposableTo(disposeBag)
```

If you have any more doubts how to write some concept in RxSwift 2.0 version, check out [Example app](../RxExample) or playgrounds.




**Please copy the following template [here](https://github.com/ReactiveX/RxSwift/issues/new) and fill in the missing fields so we can help you as soon as possible.**

```
*Short description of missing functionality*:

  E.g. I want this library to implement xxx operator.

*Short code example of how you would like to use the API*:

  code goes here

*The reason why I need this functionality*:

  E.g. I was trying to ....

*Code I have right now*:

  code snippet that demonstrates how you've attempted to solve the problem

```




## Playgrounds

To use playgrounds:

* Open `Rx.xcworkspace`
* Build `RxSwift-OSX` scheme
* And then open `Rx` playground in `Rx.xcworkspace` tree view.
* Choose `View > Show Debug Area`




Schedulers
==========

1. [Serial vs Concurrent Schedulers](#serial-vs-concurrent-schedulers)
1. [Custom schedulers](#custom-schedulers)
1. [Builtin schedulers](#builtin-schedulers)

Schedulers abstract away mechanism for performing work.

Different mechanisms for performing work include, current thread, dispatch queues, operation queues, new threads, thread pools, run loops ...

There are two main operators that work with schedulers. `observeOn` and `subscribeOn`.

If you want to perform work on different scheduler just use `observeOn(scheduler)` operator.

You would usually use `observeOn` a lot more often then `subscribeOn`.

In case `observeOn` isn't explicitly specified, work will be performed on which ever thread/scheduler elements are generated.

Example of using `observeOn` operator

```
sequence1
  .observeOn(backgroundScheduler)
  .map { n in
      print("This is performed on background scheduler")
  }
  .observeOn(MainScheduler.instance)
  .map { n in
      print("This is performed on main scheduler")
  }
```

If you want to start sequence generation (`subscribe` method) and call dispose on a specific scheduler, use `subscribeOn(scheduler)`.

In case `subscribeOn` isn't explicitly specified, `subscribe` method will be called on the same thread/scheduler that `subscribeNext` or `subscribe` is called.

In case `subscribeOn` isn't explicitly specified, `dispose` method will be called on the same thread/scheduler that initiated disposing.

In short, if no explicit scheduler is chosen, those methods will be called on current thread/scheduler.

# Serial vs Concurrent Schedulers

Since schedulers can really be anything, and all operators that transform sequences need to preserve additional [implicit guarantees](GettingStarted.md#implicit-observable-guarantees), it is important what kind of schedulers are you creating.

In case scheduler is concurrent, Rx's `observeOn` and `subscribeOn` operators will make sure everything works perfect.

If you use some scheduler that for which Rx can prove that it's serial, it will able to perform additional optimizations.

So far it only performing those optimizations for dispatch queue schedulers.

In case of serial dispatch queue schedulers `observeOn` is optimized to just a simple `dispatch_async` call.

# Custom schedulers

Besides current schedulers, you can write your own schedulers.

If you just want to describe who needs to perform work immediately, you can create your own scheduler by implementing `ImmediateScheduler` protocol.

```swift
public protocol ImmediateScheduler {
    func schedule<StateType>(state: StateType, action: (/*ImmediateScheduler,*/ StateType) -> RxResult<Disposable>) -> RxResult<Disposable>
}
```

If you want to create new scheduler that supports time based operations, then you'll need to implement.

```swift
public protocol Scheduler: ImmediateScheduler {
    typealias TimeInterval
    typealias Time

    var now : Time {
        get
    }

    func scheduleRelative<StateType>(state: StateType, dueTime: TimeInterval, action: (StateType) -> RxResult<Disposable>) -> RxResult<Disposable>
}
```

In case scheduler only has periodic scheduling capabilities, you can inform Rx by implementing `PeriodicScheduler` protocol

```swift
public protocol PeriodicScheduler : Scheduler {
    func schedulePeriodic<StateType>(state: StateType, startAfter: TimeInterval, period: TimeInterval, action: (StateType) -> StateType) -> RxResult<Disposable>
}
```

In case scheduler doesn't support `PeriodicScheduling` capabilities, Rx will emulate periodic scheduling transparently.

# Builtin schedulers

Rx can use all types of schedulers, but it can also perform some additional optimizations if it has proof that scheduler is serial.

These are currently supported schedulers

## CurrentThreadScheduler (Serial scheduler)

Schedules units of work on the current thread.
This is the default scheduler for operators that generate elements.

This scheduler is also sometimes called `trampoline scheduler`.

If `CurrentThreadScheduler.instance.schedule(state) { }` is called for first time on some thread, scheduled action will be executed immediately and hidden queue will be created where all recursively scheduled actions will be temporarily enqueued.

If some parent frame on call stack is already running `CurrentThreadScheduler.instance.schedule(state) { }`, scheduled action will be enqueued and executed when currently running action and all previously enqueued actions have finished executing.

## MainScheduler (Serial scheduler)

Abstracts work that needs to be performed on `MainThread`. In case `schedule` methods are called from main thread, it will perform action immediately without scheduling.

This scheduler is usually used to perform UI work.

## SerialDispatchQueueScheduler (Serial scheduler)

Abstracts the work that needs to be performed on a specific `dispatch_queue_t`. It will make sure that even if concurrent dispatch queue is passed, it's transformed into a serial one.

Serial schedulers enable certain optimizations for `observeOn`.

Main scheduler is an instance of `SerialDispatchQueueScheduler`.

## ConcurrentDispatchQueueScheduler (Concurrent scheduler)

Abstracts the work that needs to be performed on a specific `dispatch_queue_t`. You can also pass a serial dispatch queue, it shouldn't cause any problems.

This scheduler is suitable when some work needs to be performed in background.

## OperationQueueScheduler (Concurrent scheduler)

Abstracts the work that needs to be performed on a specific `NSOperationQueue`.

This scheduler is suitable for cases when there is some bigger chunk of work that needs to be performed in background and you want to fine tune concurrent processing using `maxConcurrentOperationCount`.






Subjects
========

All of behave exactly the same like described [here](http://reactivex.io/documentation/subject.html)




Units
=====

This document will try to describe what units are, why they are a useful concept, how to use and create them.

* [Why](#why)
* [How do they work](#how-do-they-work)
* [Why are they named Units](#why-are-they-named-units)
* [RxCocoa units](#rxcocoa-units)
* [Driver unit](#driver-unit)
    * [Why was it named Driver](#why-was-it-named-driver)
    * [Practical usage example](#practical-usage-example)

## Why

Swift has a powerful type system that can be used to improve correctness and stability of applications and make using Rx a more intuitive and straightforward experience.

**Units are so far specific only to the (RxCocoa)[https://github.com/ReactiveX/RxSwift/tree/master/RxCocoa] project, but the same principles could be implemented easily in other Rx implementations if necessary. There is no private API magic needed.**

**Units are totally optional, you can use raw observable sequences everywhere in your program and all RxCocoa APIs work with observable sequences.**

Units also help communicate and ensure observable sequence properties across interface boundaries.

Here are some of the properties that are important when writing Cocoa/UIKit applications.

* can't error out
* observe on main scheduler
* subscribe on main scheduler
* sharing side effects

## How do they work

In its core it's just a struct with a reference to observable sequence.

You can think of them as a kind of builder pattern for observable sequences. When sequence is built, calling `.asObservable()` will transform a unit into a vanilla observable sequence.

## Why are they named Units

Analogies help reason about unfamiliar concepts, here are some ideas how units in physics and RxCocoa (rx units) are similar.

Analogies:

| Physical units                      | Rx units                                                            |
|-------------------------------------|---------------------------------------------------------------------|
| number (one value)                  | observable sequence (sequence of values)                            |
| dimensional unit (m, s, m/s, N ...) | Swift struct (Driver, ControlProperty, ControlEvent, Variable, ...) |

Physical unit is a pair of a number and a corresponding dimensional unit.<br/>
Rx unit is a pair of an observable sequence and a corresponding struct that describes observable sequence properties.

Numbers are the basic composition glue when working with physical units: usually real or complex numbers.<br/>
Observable sequences are the basic composition glue when working with rx units.

Physical units and [dimensional analysis](https://en.wikipedia.org/wiki/Dimensional_analysis#Checking_equations_that_involve_dimensions) can alleviate certain class of errors during complex calculations.<br/>
Type checking rx units can alleviate certain class of logic errors when writing reactive programs.

Numbers have operators: `+`, `-`, `*`, `/`.<br/>
Observable sequences also have operators: `map`, `filter`, `flatMap` ...

Physics units define operations by using corresponding number operations. E.g.

`/` operation on physical units is defined using `/` operation on numbers.

11 m / 0.5 s = ...
* first convert unit to **numbers** and **apply** `/` **operator** `11 / 0.5 = 22`
* then calculate unit (m / s)
* combine the result = 22 m / s

Rx units define operations by using corresponding observable sequence operations (this is how operators work internally). E.g.

The `map` operation on `Driver` is defined using the `map` operation on its observable sequence.

```swift
let d: Driver<Int> = Drive.just(11)
driver.map { $0 / 0.5 } = ...
```

* first convert driver to **observable sequence** and **apply** `map` **operator**
```swift
let mapped = driver.asObservable().map { $0 / 0.5 } // this `map` is defined on observable sequence
```

* then combine that to get the unit value
```swift
let result = Driver(mapped)
```

There is a set of basic units in physics [(`m`, `kg`, `s`, `A`, `K`, `cd`, `mol`)](https://en.wikipedia.org/wiki/SI_base_unit) that is orthogonal.<br/>
There is a set of basic interesting properties for observable sequences in `RxCocoa` that is orthogonal.

    * can't error out
    * observe on main scheduler
    * subscribe on main scheduler
    * sharing side effects

Derived units in physics sometimes have special names.<br/>
E.g.
```
N (Newton) = kg * m / s / s
C (Coulomb) = A * s
T (Tesla) = kg / A / s / s
```

Rx derived units also have special names.<br/>
E.g.
```
Driver = (can't error out) * (observe on main scheduler) * (sharing side effects)
ControlProperty = (sharing side effects) * (subscribe on main scheduler)
Variable = (can't error out) * (sharing side effects)
```

Conversion between different units in physics is done with a help of operators defined on numbers `*`, `/`.<br/>
Conversion between different rx units in done with a help of observable sequence operators.

E.g.

```
can't error out = catchError
observe on main scheduler = observeOn(MainScheduler.instance)
subscribe on main scheduler = subscribeOn(MainScheduler.instance)
sharing side effects = share* (one of the `share` operators)
```


## RxCocoa units

### Driver unit

* can't error out
* observe on main scheduler
* sharing side effects (`shareReplayLatestWhileConnected`)

### ControlProperty / ControlEvent

* can't error out
* subscribe on main scheduler
* observe on main scheduler
* sharing side effects

### Variable

* can't error out
* sharing side effects

## Driver

This is the most elaborate unit. It's intention is to provide an intuitive way to write reactive code in UI layer.

### Why was it named Driver

It's intended use case was to model sequences that drive your application.

E.g.
* Drive UI from CoreData model
* Drive UI using values from other UI elements (bindings)
...


Like normal operating system drivers, in case one of those sequence errors out your application will stop responding to user input.

It is also extremely important that those elements are observed on main thread because UI elements and application logic are usually not thread safe.

Also, `Driver` unit builds an observable sequence that shares side effects.

E.g.


### Practical usage example

This is an typical beginner example.

```swift
let results = query.rx_text
    .throttle(0.3, scheduler: MainScheduler.instance)
    .flatMapLatest { query in
        fetchAutoCompleteItems(query)
    }

results
    .map { "\($0.count)" }
    .bindTo(resultCount.rx_text)
    .addDisposableTo(disposeBag)

results
    .bindTo(resultsTableView.rx_itemsWithCellIdentifier("Cell")) { (_, result, cell) in
        cell.textLabel?.text = "\(result)"
    }
    .addDisposableTo(disposeBag)
```

The intended behavior of this code was to:
* throttle user input
* contact server and fetch a list of user results (once per query)
* then bind the results to two UI elements, results table view and a label that displays number of results

So what are the problems with this code:
* in case the `fetchAutoCompleteItems` observable sequence errors out (connection failed, or parsing error), this error would unbind everything and UI wouldn't respond any more to new queries.
* in case `fetchAutoCompleteItems` returns results on some background thread, results would be bound to UI elements from a background thread and that could cause non deterministic crashes.
* results are bound to two UI elements, which means that for each user query two HTTP requests would be made, one for each UI element, which is not intended behavior.

A more appropriate version of the code would look like this:

```swift
let results = query.rx_text
    .throttle(0.3, scheduler: MainScheduler.instance)
    .flatMapLatest { query in
        fetchAutoCompleteItems(query)
            .observeOn(MainScheduler.instance) // results are returned on MainScheduler
            .catchErrorJustReturn([])                // in worst case, errors are handled
    }
    .shareReplay(1)                                  // HTTP requests are shared and results replayed
                                                     // to all UI elements

results
    .map { "\($0.count)" }
    .bindTo(resultCount.rx_text)
    .addDisposableTo(disposeBag)

results
    .bindTo(resultTableView.rx_itemsWithCellIdentifier("Cell")) { (_, result, cell) in
        cell.textLabel?.text = "\(result)"
    }
    .addDisposableTo(disposeBag)
```

Making sure all of these requirements are properly handled in large systems can be challenging, but there is a simpler way of using the compiler and units to prove these requirements are met.

The following code looks almost the same:

```swift
let results = query.rx_text.asDriver()        // This converts normal sequence into `Driver` sequence.
    .throttle(0.3, scheduler: MainScheduler.instance)
    .flatMapLatest { query in
        fetchAutoCompleteItems(query)
            .asDriver(onErrorJustReturn: [])  // Builder just needs info what to return in case of error.
    }

results
    .map { "\($0.count)" }
    .drive(resultCount.rx_text)               // If there is `drive` method available instead of `bindTo`,
    .addDisposableTo(disposeBag)              // that means that compiler has proved all properties
                                              // are satisfied.
results
    .drive(resultTableView.rx_itemsWithCellIdentifier("Cell")) { (_, result, cell) in
        cell.textLabel?.text = "\(result)"
    }
    .addDisposableTo(disposeBag)
```

So what is happening here?

This first `asDriver` method converts `ControlProperty` unit to `Driver` unit.

```swift
query.rx_text.asDriver()
```

Notice that there wasn't anything special that needed to be done. `Driver` has all of the properties of the `ControlProperty` unit plus some more. The underlying observable sequence is just wrapped as `Driver` unit, and that's it.

The second change is

```swift
  .asDriver(onErrorJustReturn: [])
```

Any observable sequence can be converted to `Driver` unit, it just needs to satisfy 3 properties:
* can't error out
* observe on main scheduler
* sharing side effects (`shareReplayLatestWhileConnected`)

So how to make sure those properties are satisfied? Just use normal Rx operators. `asDriver(onErrorJustReturn: [])` is equivalent to following code.

```
let safeSequence = xs
  .observeOn(MainScheduler.instance) // observe events on main scheduler
  .catchErrorJustReturn(onErrorJustReturn) // can't error out
  .shareReplayLatestWhileConnected         // side effects sharing
return Driver(raw: safeSequence)           // wrap it up
```

The final piece is `drive` instead of using `bindTo`.

`drive` is defined only on `Driver` unit. It means that if you see `drive` somewhere in code, observable sequence that can never error out and observes elements on main thread is being bound to UI element. Which is exactly what is wanted.

Theoretically, somebody could define `drive` method to work on `ObservableType` or some other interface, so creating a temporary definition with `let results: Driver<[Results]> = ...` before binding to UI elements would be necessary for complete proof, but we'll leave it up for reader to decide whether that is a realistic scenario.





Warnings
========

### <a name="unused-disposable"></a>Unused disposable (unused-disposable)

The following is valid for the `subscribe*`, `bind*` and `drive*` family of functions that return `Disposable`.

Warning is probably presented in a context similar to this one:

```Swift
let xs: Observable<E> ....

xs
  .filter { ... }
  .map { ... }
  .switchLatest()
  .subscribe(onNext: {
    ...
  }, onError: {
    ...
  })  
```

The `subscribe` function returns a subscription `Disposable` that can be used to cancel computation and free resources.

The preferred way of terminating these fluent calls is by using a `DisposeBag`, either through chaining a call to `.addDisposableTo(disposeBag)` or by adding the disposable directly to the bag.

```Swift
let xs: Observable<E> ....
let disposeBag = DisposeBag()

xs
  .filter { ... }
  .map { ... }
  .switchLatest()
  .subscribe(onNext: {
    ...
  }, onError: {
    ...
  })
  .addDisposableTo(disposeBag) // <--- note `addDisposableTo`
```

When `disposeBag` gets deallocated, the disposables contained in it will be automatically disposed.

In the case where `xs` terminates in a predictable way with either a `Completed` or `Error` message, not handling the subscription `Disposable` won't leak any resources. However, even in this case, using a dispose bag is still the preferred way to handle subscription disposables. It ensures that element computation is always terminated at a predictable moment, and makes your code robust and future proof because resources will be properly disposed even if the implementation of `xs` changes.

Another way to make sure subscriptions and resources are tied with the lifetime of some object is by using the `takeUntil` operator.

```Swift
let xs: Observable<E> ....
let someObject: NSObject  ...

_ = xs
  .filter { ... }
  .map { ... }
  .switchLatest()
  .takeUntil(someObject.rx_deallocated) // <-- note the `takeUntil` operator
  .subscribe(onNext: {
    ...
  }, onError: {
    ...
  })
```

If ignoring the subscription `Disposable` is desired behavior, this is how to silence the compiler warning.

```Swift
let xs: Observable<E> ....

_ = xs // <-- note the underscore
  .filter { ... }
  .map { ... }
  .switchLatest()
  .subscribe(onNext: {
    ...
  }, onError: {
    ...
  })
```

### <a name="unused-observable"></a>Unused observable sequence (unused-observable)

Warning is probably presented in a context similar to this one:

```Swift
let xs: Observable<E> ....

xs
  .filter { ... }
  .map { ... }
```

This code defines an observable sequence that is filtered and mapped from the `xs` sequence but then ignores the result.

Since this code just defines an observable sequence and then ignores it, it doesn't actually do anything.

Your intention was probably to either store the observable sequence definition and use it later ...

```Swift
let xs: Observable<E> ....

let ys = xs // <--- names definition as `ys`
  .filter { ... }
  .map { ... }
```

... or start computation based on that definition  

```Swift
let xs: Observable<E> ....
let disposeBag = DisposeBag()

xs
  .filter { ... }
  .map { ... }
  .subscribeNext { nextElement in       // <-- note the `subscribe*` method
    // use the element
    print(nextElement)
  }
  .addDisposableTo(disposeBag)
```






## Why

**Rx enables building apps in a declarative way.**

### Bindings

```swift
Observable.combineLatest(firstName.rx_text, lastName.rx_text) { $0 + " " + $1 }
            .map { "Greeting \($0)" }
            .bindTo(greetingLabel.rx_text)
```

This also works with `UITableView`s and `UICollectionView`s.

```swift
viewModel
  .rows
  .bindTo(resultsTableView.rx_itemsWithCellIdentifier("WikipediaSearchCell", cellType: WikipediaSearchCell.self)) { (_, viewModel, cell) in
    cell.title = viewModel.title
    cell.url = viewModel.url
  }
  .addDisposableTo(disposeBag)
```

** Official suggestion is to always use `.addDisposableTo(disposeBag)` even though that's not necessary for simple bindings.**

### Retries

It would be great if APIs wouldn't fail, but unfortunately they do. Let's say there is an API method

```swift
func doSomethingIncredible(forWho: String) throws -> IncredibleThing
```

If you are using this function as it is, it's really hard to do retries in case it fails. Not to mention complexities modeling [exponential backoffs](https://en.wikipedia.org/wiki/Exponential_backoff). Sure it's possible, but code would probably contain a lot of transient states that you really don't care about, and it won't be reusable.

You would ideally want to capture the essence of retrying, and to be able to apply it to any operation.

This is how you can do simple retries with Rx

```swift
  doSomethingIncredible("me")
    .retry(3)
```

You can also easily create custom retry operators.

### Delegates

Instead of doing the tedious and non-expressive

```swift
public func scrollViewDidScroll(scrollView: UIScrollView) { // what scroll view is this bound to?
    self.leftPositionConstraint.constant = scrollView.contentOffset.x
}
```

... write

```swift
self.resultsTableView
  .rx_contentOffset
  .map { $0.x }
  .bindTo(self.leftPositionConstraint.rx_constant)
```

### KVO

Instead of

```
`TickTock` was deallocated while key value observers were still registered with it. Observation info was leaked, and may even become mistakenly attached to some other object.
```

and

```objc
-(void)observeValueForKeyPath:(NSString *)keyPath
                     ofObject:(id)object
                       change:(NSDictionary *)change
                      context:(void *)context
```

Use [`rx_observe` and `rx_observeWeakly`](GettingStarted.md#kvo)

This is how they can be used:

```swift
view.rx_observe(CGRect.self, "frame")
    .subscribeNext { frame in
        print("Got new frame \(frame)")
    }
```

or

```swift
someSuspiciousViewController
    .rx_observeWeakly(Bool.self, "behavingOk")
    .subscribeNext { behavingOk in
        print("Cats can purr? \(behavingOk)")
    }
```

### Notifications

Instead of using
```swift
    @available(iOS 4.0, *)
    public func addObserverForName(name: String?, object obj: AnyObject?, queue: NSOperationQueue?, usingBlock block: (NSNotification) -> Void) -> NSObjectProtocol
```

... just write

```swift
    NSNotificationCenter.defaultCenter()
      .rx_notification(UITextViewTextDidBeginEditingNotification, object: myTextView)
      .map { /*do something with data*/ }
      ....
```

### Transient state

There is also a lot of problems with transient state when writing async programs. Typical example is autocomplete search box.

If you were to write the autocomplete code without Rx, first problem that probably needs to be solved is when `c` in `abc` is typed, and there is a pending request for `ab`, pending request gets cancelled. Ok, that shouldn't be too hard to solve, you just create additional variable to hold reference to pending request.

The next problem is if the request fails, you need to do that messy retry logic. But ok, a couple of more fields that capture number of retries that need to be cleaned up.

It would be great if program would wait for some time before firing that request to server, after all, we don't want to spam our servers in case somebody is in the process of fast typing something very long. Additional timer field maybe?

There is also a question of what needs to be shown on screen while that search is executing, and also what needs to be shown in case we fail even with all of the retries.

Writing all of this and properly testing it would be tedious. This is that same logic written with Rx.

```swift
  searchTextField.rx_text
    .throttle(0.3, scheduler: MainScheduler.instance)
    .distinctUntilChanged()
    .flatMapLatest { query in
        API.getSearchResults(query)
            .retry(3)
            .startWith([]) // clears results on new search term
            .catchErrorJustReturn([])
    }
    .subscribeNext { results in
      // bind to ui
    }
```

There is no additional flags or fields required. Rx takes care of all that transient mess.

### Compositional disposal

Lets assume that there is a scenario where you want to display blurred images in a table view. The images should be first fetched from URL, then decoded and then blurred.

It would also be nice if that entire process could be cancelled if a cell exits the visible table view area because bandwidth and processor time for blurring are expensive.

It would also be nice if we didn't just immediately start to fetch image once the cell enters visible area because if user swipes really fast there could be a lot of requests fired and cancelled.

It would be also nice if we could limit the number of concurrent image operations because blurring images is an expensive operation.

This is how we can do it using Rx.

```swift
// this is conceptual solution
let imageSubscription = imageURLs
    .throttle(0.2, scheduler: MainScheduler.instance)
    .flatMapLatest { imageURL in
        API.fetchImage(imageURL)
    }
    .observeOn(operationScheduler)
    .map { imageData in
        return decodeAndBlurImage(imageData)
    }
    .observeOn(MainScheduler.instance)
    .subscribeNext { blurredImage in
        imageView.image = blurredImage
    }
    .addDisposableTo(reuseDisposeBag)
```

This code will do all that, and when `imageSubscription` is disposed it will cancel all dependent async operations and make sure no rogue image is bound to UI.

### Aggregating network requests

What if you need to fire two requests, and aggregate results when they have both finished?

Well, there is of course `zip` operator

```swift
  let userRequest: Observable<User> = API.getUser("me")
  let friendsRequest: Observable<Friends> = API.getFriends("me")

  Observable.zip(userRequest, friendsRequest) { user, friends in
      return (user, friends)
    }
    .subscribeNext { user, friends in
        // bind them to user interface
    }
```

So what if those APIs return results on a background thread, and binding has to happen on main UI thread? There is `observeOn`.

```swift
  let userRequest: Observable<User> = API.getUser("me")
  let friendsRequest: Observable<[Friend]> = API.getFriends("me")

  Observable.zip(userRequest, friendsRequest) { user, friends in
      return (user, friends)
    }
    .observeOn(MainScheduler.instance)
    .subscribeNext { user, friends in
        // bind them to user interface
    }
```

There are many more practical use cases where Rx really shines.

### State

Languages that allow mutation make it easy to access global state and mutate it. Uncontrolled mutations of shared global state can easily cause [combinatorial explosion] (https://en.wikipedia.org/wiki/Combinatorial_explosion#Computing).

But on the other hand, when used in smart way, imperative languages can enable writing more efficient code closer to hardware.

The usual way to battle combinatorial explosion is to keep state as simple as possible, and use [unidirectional data flows](https://developer.apple.com/videos/play/wwdc2014-229) to model derived data.

This is what Rx really shines at.

Rx is that sweet spot between functional and imperative world. It enables you to use immutable definitions and pure functions to process snapshots of mutable state in a reliable composable way.

So what are some of the practical examples?

### Easy integration

And what if you need to create your own observable? It's pretty easy. This code is taken from RxCocoa and that's all you need to wrap HTTP requests with `NSURLSession`

```swift
extension NSURLSession {
    public func rx_response(request: NSURLRequest) -> Observable<(NSData, NSURLResponse)> {
        return Observable.create { observer in
            let task = self.dataTaskWithRequest(request) { (data, response, error) in
                guard let response = response, data = data else {
                    observer.on(.Error(error ?? RxCocoaURLError.Unknown))
                    return
                }

                guard let httpResponse = response as? NSHTTPURLResponse else {
                    observer.on(.Error(RxCocoaURLError.NonHTTPResponse(response: response)))
                    return
                }

                observer.on(.Next(data, httpResponse))
                observer.on(.Completed)
            }

            task.resume()

            return AnonymousDisposable {
                task.cancel()
            }
        }
    }
}
```

### Benefits

In short using Rx will make your code:

* composable <- because Rx is composition's nick name
* reusable <- because it's composable
* declarative <- because definitions are immutable and only data changes
* understandable and concise <- raising level of abstraction and removing transient states
* stable <- because Rx code is thoroughly unit tested
* less stateful <- because you are modeling application as unidirectional data flows
* without leaks <- because resource management is easy

### It's not all or nothing

It is usually a good idea to model as much of your application as possible using Rx.

But what if you don't know all of the operators and does there even exist some operator that models your particular case?

Well, all of the Rx operators are based on math and should be intuitive.

The good news is that about 10-15 operators cover most typical use cases. And that list already includes some of the familiar ones like `map`, `filter`, `zip`, `observeOn` ...

There is a huge list of [all Rx operators](http://reactivex.io/documentation/operators.html) and list of all of the [currently supported RxSwift operators](API.md).

For each operator there is [marble diagram](http://reactivex.io/documentation/operators/retry.html) that helps to explain how does it work.

But what if you need some operator that isn't on that list? Well, you can make your own operator.

What if creating that kind of operator is really hard for some reason, or you have some legacy stateful piece of code that you need to work with? Well, you've got yourself in a mess, but you can [jump out of Rx monad](GettingStarted.md#life-happens) easily, process the data, and return back into it.


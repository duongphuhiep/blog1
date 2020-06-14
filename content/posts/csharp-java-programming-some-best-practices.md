---
title: "C# (or Java) Programming Best Practices"
date: 2020-04-25T14:21:32+02:00
tags: ["C#"]
categories: ["backend"]
---
Base on my years experiences in codes review, I recognized some best practices which is really simple to apply to sanitize the codes, make them more testable or reusable. Experienced developers might apply some of them and don't even realize it.

In this article I will expose some of the common coding mistakes which I often see in codes review sessions along with some simple / best practice to avoid them. The sample codes is in C# flavor, but the ideas is applicable for other similar languages Java, Kotlin...

## 1. try/catch the right way

### a) Don't `try/catch` whenever you feel like it

> Rule of thumb: *Avoid try/catch as much as possible*

`try/catch` is BAD. It hurts the application performance. It should only be use on the out-most application entry and thread entry. In other parts of the code, all try/catch must to be justified.

So avoid to write "try" and "catch" as much as you could. If you have no choice => put the comment to justify why would you have to `try/catch` here?

If you use `try/catch` only to capture silly errors or to hide a problem => please think twice and justify why would you do that?

For example a BAD Code:

```csharp
var v
try {
  v = int.Parse(something);
}
catch {
   //empty code: nothing to do with the exception just hide the Parse error
}
```

to make this codes better we can remove the `try/catch` and use `int.TryParse()` instead.

### b) Don't hide error

Example: A BAD code: please never do it!

```csharp
try {
  MAIN_CODE
}
catch (Exception e)
{
  // do nothing
}
```

### c) Don't cut off the stacktrace

Example: A BAD code:

```csharp
try {
  MAIN_CODE
}
catch (Exception e)
{
  DO_SOMETHING_WITH_EXCEPTION
  throw e; //BAD: you cut-off the stacktrace completely right here
}
```

A better one (but still second-rates for .Net framework, OK for .NetCore)

```csharp
try {
  MAIN_CODE which calls OTHER_FUNCTIONS
}
catch (Exception e)
{
  DO_SOMETHING_WITH_EXCEPTION
  throw; //still average (for .Net framework)! it keep the stacktrace of OTHER_FUNCTIONS but it hide the line in MAIN_CODE which trigger exception
}
```

**Update:** This solution is ok in .NetCore which is smart enough to pin-point the line in the MAIN_CODE in the stacktrace.
But it is not the case for the old .Net framework. This moron puts the "throw" line (not the line of the MAIN_CODE line) to the stacktrace.

### d) Avoid to convert an Exception to other Exception

```csharp
try {
  ..
  throw new SomeException()
  ..
}
catch (SomeException e)
{
  throw new OtherExeption();  //BAD: you cut-off the stacktrace right here..
}
```

Please keep the original exception if possible, only convert it to other exception if you really must to do so (please justify the reason). 

In fact, we should make this conversion only to "hide" the real error (`SomeException`) to the consumer. So we expose to the consumer `OtherException` with other details instead => please explain why did you want to hide the real error to the consumer and cut-off the stacktrace?

If you don't want to hide the real error from the consumer but stil have other "mysterious reason" to convert from `SomeException` to `OtherException` and complexify the stacktrace and the debugging task, then here is the "right" way

```csharp
try {
  ..
  throw new SomeException()
  ..
}
catch (SomeException e)
{
  throw new OtherExeption("more information", e); //better (but still average) please justify why couldn't you keep the original exception?
}
```

## 2. Use or create static method the right way

### a) Static is only good for Helper / Tools / Utilities which don't change (mutate) our system

* it should always give the same output for the same inputs
* it shouldn't call any thing which mutate the system

Otherwise: avoid to create static methods..

### b) Think about Singleton pattern

Make normal class which is unit-testable in any context, then use a singleton to make it available globally in your application.

Don't forget that the static singleton might not be testable because it depends on the application context.. but the class itself is often testable, 
we only need to give it all the dependencies it wanted in the constructor (see the following)

## 3. Constructors must to express dependencies

The parameters of the constructor should be the dependencies of the class. Example

```csharp
class FlowsConverter {
    public FlowsConverter(IUsdConverter usdConverter, IContractCustomerStore contractCustomerStore){
        //should initialize object, don't do any I/O or complicated computation here
    }
}
```

In this example the class `FlowsConverter` depends on `UsdConverte` and `ContractCustomerStore` => The dependencies are expressed in the constructor.

If a class has many (more than 3+) dependencies then it might be time to review the design; consider the [Facade Service](https://blog.ploeh.dk/2010/02/02/RefactoringtoAggregateServices/) to group common logic together.

In case the dependencies graph grow complicate, it is time to adopt a [Dependencies injection framework](https://autofac.org/).

## 4. Single Responsibility Principle (SRP)

**a)** Avoid unpredictable behavior in the constructor: eg: calculation (business logic), validation, I/O (access to DB, files..), let the constructor do its job: create object

**b)** Avoid unpredictable behavior in the getter, setter: example this code seem ok but it is actually not good:

```csharp
public class MailInfo
{
    private string str_subject;
    public string subject
    {
        get { return str_subject; }
        set {
            if (str_subject is empty) throw Exception
            str_subject = value
        }
    }
    ...
```

* => it is not the responsiblity of `MailInfo` to validate his property or to decide if he'll allow the subject be empty
* => it is the responsibility of the Consumer class which might use an external `MailInfoValidator` for this purpose

**c)** Don't save the number of class or get lazy to create a new class file! Please

* break a long method to smaller ones
* break a big class to smaller ones which do only one things (and do it well)
* And each time you create a new class or method, think about how you will write test for it.
  * [Test codes is often harder to write than main codes.](https://medium.com/@duongphuhiep/unit-testing-94a3a560309e) But please, at least.. think about how you would write test to your new class / methods evens if you don't actually write the test.
  * If you think about how to write the test for your class, you will naturally refactor your class to make it easier to test and make the better codes.

## 5. A wrapper for a `IDisposable` object should also be a `IDisposable`

If a object `A` is tighly-couple with a `IDisposable` object `B` and mean to be created and disposed together then think about making `A` a `IDisposable` as `B`.

It means: if `A` depends on a `IDisposable` `B` then

* if `A` cannot function properly without `B` (after `B` is disposed) then `A` must also be a `IDisposable` and should be disposed along with `B` as well.
* if for some reason, you can't make `A` as disposable then please make sure that `A` will continue to work normally (or die gracefully) after `B` is disposed.

Example BAD:

```csharp
public class DataContextDecorator
{
    public readonly DataContext core; //this is a IDisposable
 
    public DataContextDecorator(DataContext core)
    {
        this.core = core
    }
}
```

A better version

```csharp
public sealed class DataContextDecorator : IDisposable //we will make this wrapper IDisposable too
{
    public readonly DataContext core; //this is a IDisposable

    public DataContextDecorator(DataContext core)
    {
        this.core = core
    }

    public void Dispose()
    {
        dataContext.Dispose(); //the core live and die with the wrapper
    }
}
```

## 6. Focus to code reusable inside application, not outside application

If you are NOT making a framework or a library to publish elswhere then

* Don't try to make everything reusable outside of your application by abusing `Reflection` and `Generic` whenever you recognize a possible patterns.
* Please don't anticipate that your codes might be reused by other applications.. If it'll comes true (one day), we can always refactor it later.. => Focus to make your code easy to read (by others) and effective specifically for your current application first.

## 7. Don't blame old codes, try to enhance them!

It is common to copy old codes to make new ones. The new codes become YOUR codes. It means:

* You have to master it: If there is part that you don't understand in your code then it is bad => so don't blindly copy the codes that you didn't understand.
* You have to take responsibility of your codes: If the new code (YOUR codes) is silly / strange or bug! please don't blame that it was because this part was copied from the old codes.


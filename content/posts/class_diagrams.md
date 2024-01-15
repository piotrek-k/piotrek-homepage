---
title: "Class diagrams - cheatsheet"
date: 2024-01-15T21:38:59+02:00
draft: false
---

The following post demonstrates how to draw relationships between objects in a URL diagram. [Mermaid](https://mermaid.js.org/) syntax is also included.

## Inheritance
```
DerivedClass --|> BaseClass
```
<p align="center">
    <img src="/class_diagrams/inheritance.png" height="100">
</p>

`DerivedClass` inherits methods from `BaseClass`. 

## Association (link)

```
B -- A
```

<p align="center">
    <img src="/class_diagrams/association.png" height="100">
</p>

Class `A` has a reference to `B`, `B` has reference to `A`.

## One-way association

```
B --> A
```

<p align="center">
    <img src="/class_diagrams/one-way.png" height="100">
</p>

Class `B` has a reference to `A`, but not vice-versa.
## Composition

```
B --* A
```
<p align="center">
    <img src="/class_diagrams/composition.png" height="100">
</p>

Class `A` owns class `B`. `B` cannot exist indepedent from `A`. When `A` is destroyed, so does `B`. 

```csharp
public class Menu {
    private Button submitButton = new Button(); 
}
```

## Aggregation

```
B --o A
```

<p align="center">
    <img src="/class_diagrams/aggregation.png" height="100">
</p>

Class `A` owns class `B`. `B` can exist indepedent from `A`. E.g. reference comes from outside of the class.

```csharp
public class SubPage { 
    private NotificationService _notificationService;
    
    public SubPage(NotificationService notificationService) { 
       this._notificationService = notificationService; 
    }
}
```
(mostly redundant to association [1])
## Dependency
```
Balance <.. ATM
```

<p align="center">
    <img src="/class_diagrams/dependency.png" height="100">
</p>

`ATM` depends on `Balance`. Changing `Balance` affects whether `ATM` is able to withdraw money.

```csharp
class ATM {
	public void Withdraw(Balance balance, int amount){
		if(balance.Value < amount){
			throw new Exception();
		}
		
		balance.Reduce(amount);
	}
}
```

> The definition or implementation of the dependent classifier might change if the classifier at the arrowhead end is changed. [3]
## Realization

```
IService <|.. SpecificService
```

<p align="center">
    <img src="/class_diagrams/realization.png" height="100">
</p>

`SpecificService` implements `IService` interface.

## Links
* [1] https://nirajrules.wordpress.com/2011/07/15/association-vs-dependency-vs-aggregation-vs-composition/
* [2] https://blog.visual-paradigm.com/what-are-the-six-types-of-relationships-in-uml-class-diagrams/
* [3] https://learn.microsoft.com/en-us/previous-versions/visualstudio/visual-studio-2010/dd409437(v=vs.100)

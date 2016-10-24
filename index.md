---
layout: default
title: {{ site.name }}
---

> The concept of *Framework Oriented Programming* was thought as a reference for those that plan to modularize their cocoa apps. There's official documentation about frameworks/libraries but nothing that the best practices/principles when using them in apps. The reference includes principles, examples and tools that help you to understand better what the concept is and how to apply it in your own projects. Tools/examples/concepts are open to change. Feel free to propose changes, fixes, improvements directly to the [repositories](https://github.com/frameworkoriented). If you are trying it we'd like to also know about your approach and the steps that you took to get there. Do not hesitate to reach out to me at [pepibumur@gmail.com](pepibumur@gmail.com)


|   |  Index |
|---|---|
| :mag: |  [**Context**](#context)|
| :stuck_out_tongue: |  [**Motivation**](#motivation)|
| :one: |  [**Principles**](#principles)|
| :camel: |  [**Migration**](#migration) |
| :loop: |  [**Setups**](#setups) |
| :dart: | [**MicroFeatures**](#microfeatures) |
| :scissors: |  [**Tools**](#tools) |
| :couple: |  [**Contribute**](#contribute) |
| :book: |  [**Resources**](#resources) |
| :pencil: |  [**Resources**](#references) |


# Context

We've always been told as a developers to follow one basic design principle: try to make your code *reusable*. Design your classes, your business logic, your projects to be reusable. Project them to the future and imagine how these components could be used on different scenarios and contexts, don't couple your code to dependencies, prefer abstractions over concrections... There are bunch of principles and philosophies that can be applied to create reusable code from a low level perspective. However, from a higher level, we forget about these principles and we end up having projects tied to the platform and tied to our product use case. **What if we could design our apps in a way that could be easy to reuse and platform independent?**

Nobody predicted a few years ago we were going to have so many platforms to develop for in Swift/Objective-C. Mobile platforms and iOS was the first step for a lot of developers that wanted to give a try to something new, playing with the new features and possibilities that mobile development offered. By that time, the focus was coding for iOS. We didn't think we were going to need part of all that code in future projects, and thus, I treated my projects like big boxes *(not black because we knew about the magic I was placing inside)* that implemented from UI tasks to interactions with the database. The big box was the **main app target**, that one that Xcode creates for you when you start a new project from the scratch. Later on, thanks to [CocoaPods](https://cocoapods.org) we easily connected our big box with other external small boxes, [AFNetworking](https://github.com/afnetworking/afnetworking), [MagicalRecord](),...

We were taking advantage of reusable components that other developers where offering in open. That saved and saves us a lot of time in a daily bases. However our implemented components weren't reusable at all. Moreover they were coupled to other components and also with external dependencies. It wasn't a problem when we had only one app, but when Apple introduced new platforms we had a clear **need** to reuse these components to support these new platforms. It turned out to be a huge nightmare. Some teams decided to share files across targets, but if the design wasn't modular it wasn't easy at all. 

Moreover monolithic projects are not ideal for big teams. As the team grows in a monolithic project the number of conflicts increase as well. Different teams touching the same file, which is easily solvable if it's a source file, and not as easy if it's a core data model or a storyboard. The problem becomes even worse if there're no integration tests between different components and a change in one of them causes an unexpected behaviour in the other.

That **non-reusable** and **conflicts prone** architecture in the project led us to think about new architectures that could help us to solve these two main issues. The approach that we went with was modularizing the app using frameworks.

We'll guide you through some principles, tooling and examples that might be useful if you plan to go with this setup for your projects.

# Motivation

- **Easily expand to new platforms:** Creating a new version of your project for another platforms should be as easy as working on the UI layer, reusing all the business logic that comes already implemented in your frameworks.
- **New products with similar core needs:** This is very sweet for startups, since it allows product related iterations. Companies can benefit from this  adding new products to the stack that share the same core logic as other products *(e.g. the new product interacts with the same API, the data model is the same...)*
- **Aim open sourcing:** Have you ever wanted to open source a component in your project and it was hard because it was designed to be used in your product? What if it was designed in the other way around without thinking about where the component would be used? Designing without thinking in the use case leads to more generic implementation that might be handy for other developers and so, open-sourceable from your side.
- **Plug in an out pieces with ease:** Having the business logic separated in small pieces helps when the project needs a replacement for any of these pieces. As the code is very isolated and the public interface abstracted these pieces could be easily replaced just exposing the same interface but replacing the private implementation.

# Principles

## 1. Single responsibility

SOLID principles also apply to a framework. Frameworks should satisfy the single responsibility principle. They should have only one responsibility. If any of your designed frameworks has multiple responsibility, think about slicing it in more layers.

> For example, if we have an API interaction layer, and it offers not only the wrapper to the Foundation Networking layer but the requests  factories and models *(tied to your project use case)* think about separating them into a `Networking` and an `API` frameworks.

Frameworks that had one responsibility at the beginning might get more later. 

Defining the responsibility of a framework is not easy. Think about a Framework as a box, that you give data to, it **does something** with your data, and provides you with some results. *"Does something"* is your framework's purpose. Keep that purpose simple and scoped.

> Build single purpose boxes


## 2. Vertical design

Design your frameworks graph as a stack with multiple layers where the application is on the top. Avoid horizontal dependencies between frameworks in the same layer and prefer vertical dependencies down in the stack. 

If any framework needed to know about other in the same level, you might need a layer that combines them on top. A very simple example would be the data synchronization in your app. We might have a framework for an HTTP interaction with your API, another one for persisting the data and think about getting to know each other to persist the responses from one using the other one. You're unconsciously coupling these two frameworks horizontally. Let's come up with a new framework on top of these responsible of that *synchronization* between local and remote.

> Design your stack dependencies vertically

## 3. External dependencies
The number of external dependencies should be directly proportional with the level of the framework in the stack *(i.e, the lower in the stack the less the external dependencies it should have)*. Dependencies of lower levels are also dependencies of upper levels, thus, the more dependencies we we have in these levels the more complex the graph and the setup becomes. Figure out if your Framework really needs that external dependency that you are thinking about. Most of the times we end up checking out dependencies to use only a few components from them. Add as a dependencies only these `components/extensions/classes` that you really need.

> Reduce external dependencies as you go lower in the stack.

## 4. One step dependencies
##### :warning: Work in progress :warning:

Frameworks should know only about the framework below *(one step)*. Deeper dependencies should be wrapped by the framework you are depending on *(either these deeper dependencies are local or external)*. Never expose them up, proxy the protocols, and wrap the models from the framework.

This makes replacement in the future easier. For example if you used another persistency solution like [Realm](https://realm.io) that uses their own defined models and you exposed them from your Framework

> Don't expose lower dependencies to upper levels. Wrap them!

## 5. Restrictive first
##### :warning: Work in progress :warning:
If you're using **Swift**, congrats :tada:, you get this for free. All components are by default `internal` and they won't be visible form other frameworks unless you specify it with the `public` modifier. As soon as you start *"consuming"* your frameworks you'll figure out which components have to be `public`. In case of **Objective-C** keep the headers private in the target headers configuration and make `public` only these that must be visible. When a component is `public` the developers that are depending on that frameworks feel the *"freedom"* and *"flexibility"* that leads to a misuse and coupling with private code.

> Make framework components internal by default and make public only these needed.

## 6. Final
##### :warning: Work in progress :warning:

Design your Frameworks components based on the open/closed SOLID principle. Allow extension *(open)* but without diving into the base class implementation *(closed)*. Modifying the base class might lead to unexpected behavior cascading through all the components that rely on the modified one *(it could be detected with a good testing suite)*. Swift allows restricting the modification of a class/method with the **final** keyword. You'll find more information about Swift inheritance and restricting overriding [here](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Inheritance.html).

With the final keyword you prevent overrides from your Frameworks and force the developers to look for extension alternatives other than overriding.

> Make your Frameworks open to extension but closed to modifications.


## 7. Framework models
##### :warning: Work in progress :warning:

Each framework should implement their own models. If you share models between multiple frameworks you are coupling these frameworks to the frameworks that provide these models. That said, a `Networking` framework should have defined models representing API responses, and a `Database` framework should have their own `Database` models. If these models are combined in a business logic framework, `Core` then they should be wrapped into different models. You

> Each framework defines its own models

## 8. Platform abstraction
##### :warning: Work in progress :warning:

Decouple your framework from platform specific frameworks. What does it mean? If there's a Framework that is `macOS` or `iOS` only, for example `UIKit` or `AppKit`, try not to couple your framework to it. Instead come up with these components that you might need, a `Color` or a `Font`  class/struct. You can create extensions and platform macros to convert these frameworks components into components that are easier to work with from the application.

- Swift Preprocessor Directives: [Link](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/BuildingCocoaApps/InteractingWithCAPIs.html#//apple_ref/doc/uid/TP40014216-CH8-XID_20)
- Target conditionals and availability: [Link](https://www.cocoanetics.com/2012/09/target-conditionals-and-availability/)

> Decoupled from platforms, extended to make it nicer to play with for platforms.

```swift
#if os(OSX)
// OSX only code
#endif
```

## 9. Protocol oriented interfaces
##### :warning: Work in progress :warning:

**Based on the 5th SOLID principle: Dependency Inversion**

Abstract your public interfaces with protocols. That decouples the access layer from the implementation. If a framework `A` depends on the protocol based interface of `B`, `B` can update its implementation without requiring any change in `A` *(since the exposed interface is the same)*. This principle is aligned with the Swift philosophy based on protocols, and that is well known as *Protocol Oriented Programming*. The same programming paradigm that applies to this principle. Your frameworks are responsible of certain tasks and you define them in a protocol based interface.

> Define your Frameworks interfaces using protocols

## 10. Core
##### :warning: Work in progress :warning:

Most of projects share code that is used by all the components around the app. These components are part of your application Foundation. As Apple does with Foundation that provides with the base structures that are needed for building any application, your Core will contain the base structures to build your application/s. Sort of elements that could be part of your Foundation framework are:

- Logging helpers.
- Extensions of existing Foundation classes *(e.g. extra instance methods added to Array/Collection implementing a behavior that is not natively provided)*
- Architectural components *(e.g. if we go with the command pattern, we can define a base command class in Foundation)*

To know if a component should be part of Core try to answer the following question: *Is that component going to be used by all the Frameworks in the stack or is it only needed by one of them?*


# Migration
##### :warning: Work in progress :warning:

1. Start pulling the least dependent components from your app. These are usually tools/wrappers.

> The purpose of this section is explaining the best approach for transitioning from a existing monolithic project to a frameworks setup.

# Setups
##### :warning: Work in progress :warning:
Would you like to know which setup is best according to your project/team structure? Start by answering the following questions:

## Freelance setup

### Need 
Freelancers tend to repeat code across projects. That doesn't make sense, does it? A frameworks setup would help you to save time. 

### How
Just create a *FreelanceKit/StudioKit/MyNameKit* with the components that you usually repeat in projects *(e.g. database tooling, api tooling...)*.

## Feature teams setup
Nothing in this section yet

# MicroFeatures
##### :warning: Work in progress :warning:

# Tools

## [Pandora](https://github.com/frameworkoriented/pandora)
![image](https://github.com/frameworkoriented/pandora/raw/master/assets/header.png)
Once you start modularizing your apps you'll notice that you repeat the same steps every time you are about to create a new framework. Create the project, set the config, connect dependencies, add the example app... Hopefully we're developers and we can automate things! And that's what we did with Pandora. Pandora is a command line tool written in Ruby to automate Framework tasks.

**Example: Creating a framework**
{% highlight bash %}
pandora create Search com.myorg
{% endhighlight %}

## Dependency managers

> Useful for fetching external dependencies and integrate them into your frameworks-based projects.

- [**CocoaPods:**](https://cocoapods.org) CocoaPods manages dependencies for your Xcode projects. You specify the dependencies for your project in a simple text file: your Podfile. CocoaPods recursively resolves dependencies between libraries, fetches source code for all dependencies, and creates and maintains an Xcode workspace to build your project.
- [**Carthage:**](https://github.com/carthage/carthage) Carthage is intended to be the simplest way to add frameworks to your Cocoa application. Carthage builds your dependencies and provides you with binary frameworks, but you retain full control over your project structure and setup. Carthage does not automatically modify your project files or your build settings.
- [**Swift Package Manager:**](https://swift.org/package-manager/) The Swift Package Manager is a tool for managing the distribution of Swift code. It’s integrated with the Swift build system to automate the process of downloading, compiling, and linking dependencies.
- [**CocoaSeeds:**](https://github.com/devxoul/CocoaSeeds) Git Submodule Alternative for Cocoa. Inspired by CocoaPods.

# Contribute

## How to contribute?

Feel free to improve the reference. Check out the repository, follow the setup steps and make your proposals. Create a PR with your proposal and we'll discuss the proposal before merging it. Make sure that CI is passing and the website/projects compile accordingly.

**We're looking forward to your improvements!**

## Thanks

Special thanks to all the contributors listed below that have helped to make this reference possible and spread the idea of modularizing code:

- Felix Gabel - [@blinker13](https://github.com/blinker13)
- Juan Cazalla -  [@juancazalla](https://github.com/juancazalla)
- Isaac Roldán - [@isaacroldan](https://github.com/isaacroldan)
- Matej Balantič - [@MatejBalantic](https://github.com/MatejBalantic)
- Raimon Lapuente - [@Wolffan](https://github.com/wolffan)

# Resources

## Talks

- Framework Oriented Programming *(Mobiconf2016)*:
  - [Slides](https://speakerdeck.com/pepibumur/framework-oriented-programming-mobiconf-2016)
- A journey into frameworks and Swift:
  - [Slides](https://speakerdeck.com/pepibumur/a-journey-into-frameworks-and-swift)
- Framework Oriented Programming *(NSBudapest)*:
  - [Slides](https://speakerdeck.com/pepibumur/framework-oriented-programming-nsbudapest)
- Framework Oriented Programming:
  - [Slides](https://speakerdeck.com/pepibumur/framework-oriented-programming)
  - [Video (Spanish)](https://www.youtube.com/watch?v=UFuDpcB4yTc)

## Workshops

- Modularizing your apps:
  - [Slides](https://speakerdeck.com/pepibumur/modularizing-your-apps)
  - [Repository](https://github.com/soundcloud/weatherito)

## Articles

- [Micro Features Architecture for iOS](https://blog.caramba.io/micro-features-architecture-for-ios-f81ca18f03ac)

# References

- **Library Oriented Programming** *(Justin Spahr-Summers)* - [Link](https://realm.io/news/justin-spahr-summers-library-oriented-programming/)
- **Building Modern Frameworks** *(Apple)* - [Link](https://developer.apple.com/videos/play/wwdc2014/416/)
- **Creating your first iOS Framework** *(Thoughtbot)* - [Link](https://robots.thoughtbot.com/creating-your-first-ios-framework)
- **Awesome iOS** - [Link](https://github.com/vsouza/awesome-ios)
- **How to create a Framework for iOS** - [Link](https://www.raywenderlich.com/65964/create-a-framework-for-ios)
- **Framework vs Library** - [Link](http://www.knowstack.com/framework-vs-library-cocoa-ios/)
- **Static and Dynamic LIbraries** - [Link](https://pewpewthespells.com/blog/static_and_dynamic_libraries.html)
- **The Unofficial Guide to xccconfig files** - [Link](https://pewpewthespells.com/blog/xcconfig_guide.html)

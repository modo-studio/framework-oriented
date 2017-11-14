---
layout: default
title: {{ site.name }}
---

# Context

Traditionally applications have been single-target. When projects evolve, and the target and the team grow, working with a monolith project becomes very hard:

- The targets take too much time to compile.
- The is hard to reuse across platforms.
- There are many strong intra and inter dependencies.

Inspired by microsvervices, the **Framework Oriented Programming** project architecture pretends to reduce these issues by splitting the large application module, into smaller and atomic chunks.

In the next sections, we'll dive into the definition of the architecture and how different code components would fit into all the modules of our projects.

> The core idea of framework oriented programming is not something we've invented. You can find a lot of literature about scaling projects by splitting up your project in different services. Our aim is to apply all these principles, that help projects scale easily in other platforms, to scale our Xcode apps.

### Why modularizing my apps?
- Workflow cycles are much faster. You work on your module and once finished, it's hooked in the app.
- Boundaries will encourage good practices using APIs frameworks expose.
- If you have Swift and Objective-C in your project, frameworks will be the perfect place to start coding pure Swift in the project.
- Higher atomicity of features and teams and fewer dependencies.
- Your features become more reusable across products/platforms.

# Index

- [Principles](#principles)
- [Setup](#setup)
- [Tools](#tools)
- [Contribute](#contribute)
- [Share](#share)
- [Thanks](#thanks)
- [Resources](#resources)
- [Further Reading](#further-reading)

# Principles

- Your application is built by combining different modules. These modules can be dynamic frameworks *(if you are using Swift)*, or static libraries.
- Modules have interfaces that expose to the consumers *(apps & modules)*. The interface is the entry point to the framework, and everything exposed by the interface should be public. If the API does not expose something it should remain private.
- Modules can be platform specific if you plan to support only one platform, or they can be cross-platform. You can achieve using the build settings attribute `SUPPORTED_PLATFORMS`.
- Sharing a base configuration is recommended since it'll ensure consistency in the frameworks settings. [Here](https://gist.github.com/pepibumur/763a28879a976ff3083161dd788e7efa) you can find an example of a cross-platform framework configuration.
- Targets that build the modules can be in the same project or different projects. The benefit of having different projects in the same workspace is that the chances to suffer conflicts when modifying the same project file from different branches decrease.
- Although you can set up the stack manually, you can use tools like [CocoaPods](https://cocoapods.org) where your modules are defined with `.podspec`. CocoaPods makes easier depending on external dependencies.
- Even though it's possible to integrate external dependencies into the stack it's discouraged. External dependencies come with unnecessary maintainability costs. Before bringing an external dependency, think about the value it brings to your application core.
- Module projects can come with an example app and a Playground. Playgrounds are very handy to document the usage of the APIs or onboard people into the module.

<br>
![](/assets/images/stack.png)

## Core 

![Core Framework](/assets/images/core.png)

Core is the framework at the bottom of the stack. It's the responsible for providing the frameworks in upper levels with tools that they need to build their features. A few examples of these tools could be:

- A client to interact with your product API.
- A store for persisting and retrieving data from the disk or a database.
- A tool for reporting analytics events to an external provider.
- A logging wrapper with custom features, like verbosity level.

Notice that some of these tools will be a shared instance with a configuration that depends on the app. For example, your client will point to an URL that depends on the configuration that you are building. Similarly, the log level will be different in your `Release` build compared to your `Debug` build.

One possible way to get it done has a shared configuration that every new instance of the client will take by default: 

{% highlight swift %}
class Client {

  static var appConfig: Config!
  let config: Config

  init(config: Config = Client.appConfig) {
    self.config = Client.config
  } 
}
{% endhighlight %}

Other tools will be instantiated by the feature that needs it. Depending on the expensiveness of its creation, features might lazily load it, or get set up at startup time. A good example of these would be the `Store`:

{% highlight swift %}
import Core

class Feature {
  let store: Store<Entity>
  init() {
    store = Core.DiskStore(name: "feature")
  }
}
{% endhighlight %}

## Features

![](/assets/images/features.png)

`Features` framework allows dependency inversion with features. Feature A and B don't know about each other, but they know about their interfaces because they've been defined in the Features framework. Without the dependency inversion in place, accessing B from A, creates an implicit dependency between these two frameworks. With such dependency, you can't use A without importing B in a different application.

All the models that these interfaces *(or protocols)* expose should be part of `Features` as well.

## Testing

![Testing framework](/assets/images/testing.png)

Most times you'll find yourself writing helpers or testing expectations that other teams might need as well. By extracting all of them in a framework, you make them reusable across all the feature frameworks.

## UI

![UI framework](/assets/images/ui.png)

For consistency in your applications designs, there are certain UI elements that are shared across the features, elements like fonts, colors, or custom views. It's also a good place for `UIKit` and `AppKit` extensions that you come up with.

These elements can be placed in an UI framework that the feature frameworks depend on.

## Feature

`Feature` frameworks represent one or multiple related features of your apps. Features are composed by business logic *(data)* and presentation *(views)*. While the business logic is common for all the platforms, the presentation layer might differ because for example:

- `UIKit` is not available for that platform.
- The layout is different for that platform.
- `UIKit` APIs differ between platforms.

For that reason, features should be horizontally split into the Core and the UI frameworks.

### Core

![Feature Core framework](/assets/images/feature_core.png)

It contains the business logic of your features. It's up to the teams to decide about the patterns that they want to follow inside the framework *(MVC, MVVM, MVP, VIPER)*. Different use cases could be exposed as interactors that would be hooked from the feature UI framework.

### UI

![UI framework](/assets/images/feature_ui.png)

The UI of your features will be in this framework. Since UIs will most likely be different between platforms, it's very recommended to have one framework per platform:

{% highlight bash %}
Feature_iOS
Feature_macOS
{% endhighlight %}

Features can be a composition of views from other features. The result of that composition will be another view that will be exposed. These views should be able to respond to actions and trigger state updates.

If actions imply navigation to features in other frameworks, they should be delegated to the app.

## Products

![Products targets](/assets/images/app.png)

The product targets are the top element in the stack. The result of its build is the app or extensions that users will use on their devices. Since features will be defined in the frameworks and will provide the views and view controllers that represent them, the responsibility of the app is hooking up all of them and define the navigation of the app.

As mentioned earlier, the app will set up `Core` tools at startup time and notify about the application lifecycle events to the components in lower levels that need to know about them. 

# Setup

### Manual

Although all the targets for the frameworks can be on the same projects, keeping them in diferent project will make them completely independent from the others:

- For each framework you'll need a project that includes the source and unit tests target. Optionally, these projects can also include a playground to onboard people or document the interface of the framework.
{% highlight bash %}
Frameworks/
  FreatureA
  FeatureB
{% endhighlight %}
- To ensure all the frameworks share the same base configuration, create a `Framework.xcconfig` and set it as the configuration for the frameworks. It's important that you define the deployment target and the supported platforms in it.
{% highlight bash %}
Frameworks/
   Configuration/
      Framework.xcconfig
{% endhighlight %}
- Add all these projects to the workspace where the application is *(if it's not in a workspace, create one)*.
- Define the linking between the frameworks. Be careful here because when Xcode will most likely use absolute routes for the links. Make sure that the `.framework` paths are relative to the build products folder.
- Add a copy frameworks build phase in:
  - The projects tests targets.
  - The applications targets.
- Optionally you can define schemes for building and testing each of the frameworks individually, or create one that groups all of them.

> If you are building cross-platform frameworks it's very easy to break the support for the platforms you're not building for in your workflow. To prevent this, you can define a continuous integration step in your pipeline, that build the frameworks for all the platforms that they are supposed to support. 

### CocoaPods

If you prefer to use [CocoaPods](https://cocoapods.org) to create the stack it's also possible:

1. Create the frameworks as pods with `pod lib create Feature`.
2. Update the `.podspec` accordingly specifying the `deployment_target` and defining their dependencies, either external or local.
3. Add all the dependencies to the `Podfile`, being the first one `Core` and the last one the `Features`.
4. Execute `pod install`. It'll update the workspace to include these dependencies. Notice that CocoaPods will create schemes for building these dependencies individually.

> With CocoaPods it's easier to bring external dependencies. Otherwise, you'd need to appeal to Carthage, Git Submodules or Swift Package Manager.

# Tools

## Framework generation

Every time you create a new project for a framework, you need to repeat the same steps. Hopefully, there are tools that help you automate the creation and save a lot of time:

### [Pandora](https://github.com/frameworkoriented/pandora)
Once you start modularizing your apps you'll notice that you repeat the same steps every time you are about to create a new framework. Create the project, set the config, connect dependencies, add the example app... Hopefully we're developers and we can automate things! And that's what we did with Pandora. Pandora is a command line tool written in Ruby to automate Framework tasks.

**Example: Creating a framework**
{% highlight bash %}
pandora create Search com.myorg
{% endhighlight %}

### [SwiftPlate](https://github.com/JohnSundell/SwiftPlate)
Easily generate cross platform Swift framework projects from the command line. SwiftPlate will generate Xcode projects for you in seconds, that support:

 - [x] CocoaPods, Carthage, Swift Package Manager
 - [x] iOS/ macOS / watchOS / tvOS / Linux

**Example: Creating a framework**
{% highlight bash %}
swiftplate
{% endhighlight %}

## Dependency managers

> Useful for fetching external dependencies and integrate them into your frameworks-based projects.

- [**CocoaPods:**](https://cocoapods.org) CocoaPods manages dependencies for your Xcode projects. You specify the dependencies for your project in a simple text file: your Podfile. CocoaPods recursively resolves dependencies between libraries, fetches source code for all dependencies, and creates and maintains a Xcode workspace to build your project.
- [**Carthage:**](https://github.com/carthage/carthage) Carthage is intended to be the simplest way to add frameworks to your Cocoa application. Carthage builds your dependencies and provides you with binary frameworks, but you retain full control over your project structure and setup. Carthage does not automatically modify your project files or your build settings.
- [**Swift Package Manager:**](https://swift.org/package-manager/) The Swift Package Manager is a tool for managing the distribution of Swift code. It’s integrated with the Swift build system to automate the process of downloading, compiling, and linking dependencies.
- [**CocoaSeeds:**](https://github.com/devxoul/CocoaSeeds) Git Submodule Alternative for Cocoa. Inspired by CocoaPods.

# Contribute

## How to contribute?

- You can contribute to the project [opening an issue](https://gitlab.com/caramba/framework-oriented/issues/new) on the repository. You can propose improvements, report mistakes, or just open a discussion.
- You can also [fork the repository](https://gitlab.com/caramba/framework-oriented/forks/new) and apply the changes directly. You can propose a merge request afterwards that will be reviewed before getting merged.

## How to setup the project

1. Git clone the repository with `git clone https://gitlab.com/caramba/framework-oriented.git`
2. Install gem dependencies with `bundle install`
3. Run the server with `bundle exec jekyll serve`
4. Open [http://127.0.0.1:4000](http://127.0.0.1:4000)

We're looking forward to your improvements!

# Share
If your project were already using a similar modularized setup, or you moved towards this direction, you can share your experience in this section. Open a merge request and do not hesitate to share it!

# Thanks

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

## Articles

- [Micro Features Architecture for iOS](https://blog.caramba.io/micro-features-architecture-for-ios-f81ca18f03ac)

# Further reading

- **Library Oriented Programming** *(Justin Spahr-Summers)* - [Link](https://realm.io/news/justin-spahr-summers-library-oriented-programming/)
- **Building Modern Frameworks** *(Apple)* - [Link](https://developer.apple.com/videos/play/wwdc2014/416/)
- **Creating your first iOS Framework** *(Thoughtbot)* - [Link](https://robots.thoughtbot.com/creating-your-first-ios-framework)
- **Awesome iOS** - [Link](https://github.com/vsouza/awesome-ios)
- **How to create a Framework for iOS** - [Link](https://www.raywenderlich.com/65964/create-a-framework-for-ios)
- **Framework vs Library** - [Link](http://www.knowstack.com/framework-vs-library-cocoa-ios/)
- **Static and Dynamic Libraries** - [Link](https://pewpewthespells.com/blog/static_and_dynamic_libraries.html)
- **The Unofficial Guide to xccconfig files** - [Link](https://pewpewthespells.com/blog/xcconfig_guide.html)
- **Microservices** - [Link](https://martinfowler.com/articles/microservices.html)
- **Framework Oriented Programming and It's Relation to OOP** - [Link](https://christiantietze.de/posts/2017/01/oop-framework-oriented-programming/)

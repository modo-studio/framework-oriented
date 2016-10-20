---
layout: default
title: {{ site.name }}
---

## :warning: Work in progress :warning:
We're migrating the existing reference on the repository [pepibumur/framework-oriented-programming](https://github.com/pepibumur/framework-oriented-programming) to its own website. Stay tuned!.

---

> The concept of *Framework Oriented Programming* was thought as a reference for those that plan to modularize their cocoa apps. There's official documentation about frameworks/libraries but nothing that the best practices/principles when using them in apps. The reference includes principles, examples and tools that help you to understand better what the concept is and how to apply it in your own projects. Tools/examples/concepts are open to change. Feel free to propose changes, fixes, improvements directly to the [repositories](https://github.com/frameworkoriented). If you are trying it we'd like to also know about your approach and the steps that you took to get there. Do not hesitate to reach out to me at [pepibumur@gmail.com](pepibumur@gmail.com)

# Context

We've always been told as a developers to follow one basic design principle: try to make your code *reusable*. Design your classes, your business logic, your projects to be reusable. Project them to the future and imagine how these components could be used on different scenarios and contexts, don't couple your code to dependencies, prefer abstractions over concrections... There are bunch of principles and philosophies that can be applied to create reusable code from a low level perspective. However, from a higher level, we forget about these principles and we end up having projects tied to the platform and tied to our product use case. **What if we could design our apps in a way that could be easy to reuse and platform independent?**

Nobody predicted a few years ago we were going to have so many platforms to develop for in Swift/Objective-C. Mobile platforms and iOS was the first step for a lot of developers that wanted to give a try to something new, playing with the new features and possibilities that mobile development offered. By that time, the focus was coding for iOS. We didn't think we were going to need part of all that code in future projects, and thus, I treated my projects like big boxes *(not black because we knew about the magic I was placing inside)* that implemented from UI tasks to interactions with the database. The big box was the **main app target**, that one that Xcode creates for you when you start a new project from the scratch. Later on, thanks to [CocoaPods](https://cocoapods.org) we easily connected our big box with other external small boxes, [AFNetworking](https://github.com/afnetworking/afnetworking), [MagicalRecord](),...

We were taking advantage of reusable components that other developers where offering in open. That saved and saves us a lot of time in a daily bases. However our implemented components weren't reusable at all. Moreover they were coupled to other components and also with external dependencies. It wasn't a problem when we had only one app, but when Apple introduced new platforms we had a clear **need** to reuse these components to support these new platforms. It turned out to be a huge nightmare. Some teams decided to share files across targets, but if the design wasn't modular it wasn't easy at all. 

Moreover monolithic projects are not ideal for big teams. As the team grows in a monolithic project the number of conflicts increase as well. Different teams touching the same file, which is easily solvable if it's a source file, and not as easy if it's a core data model or a storyboard. The problem becomes even worse if there're no integration tests between different components and a change in one of them causes an unexpected behaviour in the other.

That **non-reusable** and **conflicts prone** architecture in the project led us to think about new architectures that could help us to solve these two main issues. The approach that we went with was modularizing the app using frameworks.

We'll guide you through some principles, tooling and examples that might be useful if you plan to go with this setup for your projects.

# Migration

1. Start pulling the least dependent components from your app. These are usually tools/wrappers.

> The purpose of this section is explaining the best approach for transitioning from a existing monolithic project to a frameworks setup.

# Setups
Would you like to know which setup is best according to your project/team structure? Start by answering the following questions:

## Freelance setup

### Need 
Freelancers tend to repeat code across projects. That doesn't make sense, does it? A frameworks setup would help you to save time. 

### How
Just create a *FreelanceKit/StudioKit/MyNameKit* with the components that you usually repeat in projects *(e.g. database tooling, api tooling...)*.

## Feature teams setup
Nothing in this section yet

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

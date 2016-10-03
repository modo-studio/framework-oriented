## Framework Oriented Programming
#### _@pepibumur_ / _pepibumur@gmail.com_

---

## Hi! :wave:
# __Pedro__
![original left](images/me.jpg)
### iOS Developer at _SoundCloud_
### _@pepibumur_
### _Twitter/GitHub_
#### [www.ppinera.es](http://www.ppinera.es)

---

![70%](images/box.png)

---

# _Box_

---

## _Things with something_
## in common

---

# 💌📷📔🗒

---

![image](images/developer.jpeg)

---

![inline, 50%](images/h_file.png)![inline, 50%](images/m_file.png)![inline, 50%](images/swift_file.png)


---

## ~~Boxes~~
## _Frameworks_

---

![inline, 60%](images/framework.png)

In computer science, a library is a _collection_ of implementations of _behavior_, written in terms of a _language_, that has a _well-defined interface_ by which the behavior is invoked
__Wikipedia__

---

# External
# _Frameworks_

---

## [CocoaPods](https://cocoapods.org)
## [Carthage](https://github.com/carthage/carthage)

---

## _Contributing 👍_
### with the community

---

## _Saving time 👍_
### with others code

---

## _Maintainability_
## _costs 👎_
### versioning and issues

---

```ruby
inhibit_all_warnings!
use_frameworks!

target 'MyApp' do
  pod "PureLayout"
  pod 'FLEX'
  pod 'DateTools'
  pod 'Reachability'
  pod 'RxSwift'
  pod 'SDWebImage'
  pod "SDVersion"
  pod 'BlocksKit'
  pod 'CTFeedback'
  pod 'TOWebViewController'
  pod '1PasswordExtension'
  pod 'SlackTextViewController'
  pod 'FeedbackMe'
  pod 'Localize-Swift'
  pod 'Smooch'
  pod 'Fabric'
  pod 'Crashlytics'
  pod 'CWStatusBarNotification'
  pod "ImagePickerSheetController"
  pod 'KYNavigationProgress'
end
```

---

### :apple:
## _Project_
## X External frameworks

---

## _Simple_
## _projects_
![50%](images/xcode_icon.png)

---

## Only _1/2 platforms_<br><br>
# 🖥 :iphone:

---

## _Small_ teams
### __(and not distributed)__<br><br>

---


## However...

---

## :apple:

---

## _New_ platforms <br><br>
## 🖥📱⌚️📺

---

## _~~Simple~~_ complex
## _projects_
![50%](images/xcode_icon.png)

---

## Couldn't
## _reuse code_
## _scale easily_

---

## Bigger projects
## _Bigger teams_
### __(and also distributed)__

---

# _Conflicts_
## Between teams
### __(Interdependencies)__

---

# ~~🤗~~  😰
![50%](images/xcode_icon.png)

---

# 🤔

---

## _Framework Oriented Programming_
![image](images/mountain.jpeg)

---

## __Architectural Approach__<br>

### Modularizing your apps code bases in _local_ and _multiplatform_ frameworks that expose a _hookable_ interface.

---

### New platforms/targets
## _Working only in_
# _UI_

---

# _Atomic teams_
## that own frameworks

---

## __Reuse__
## Business Logic

---

## __Select__
## The features that we need

---

### Decide about _persistence_
### Decide about _patterns_
### Decide about _architecture_
### Decide about _language_

---

### Framework
## Exposes an _API_

---

# ![inline, 170%](images/icon-framework.png) < API > ![inline, 170%](images/icon-framework.png)

---

### Less _interdependencies_
### Less _conflicts_
### Less _regressions_

---

## _How to_
### _Setup the project_

![image](images/coast.jpeg)

---

## Using CocoaPods

---

```ruby
abstract_target 'Frameworks' do
  pod "Core", :path => "Frameworks/Core"
  pod "UI", :path => "Frameworks/UI"
  pod "Player", :path => "Frameworks/Player"
  pod "Offline", :path => "Frameworks/Offline"

  # Platforms
  target "MyApp"
  target "MyTVApp"
  target "MyWatchApp"
end
```

---

## _Manually_
### Creating/Linking

---

## _Workspace_
### Multiple framework projects
#### Linked/Embedded

---

## Multi-Platform Framework

```bash
# Supported platforms
SUPPORTED_PLATFORMS = iphoneos iphonesimulator appletvsimulator appletvos macosx watchsimulator watchos

# Valid architectures
VALID_ARCHS[sdk=macosx*] = x86_64
VALID_ARCHS[sdk=iphone*] = arm64 armv7 armv7s

# Frameworks Search Path
LD_RUNPATH_SEARCH_PATHS[sdk=iphone*] = $(inherited) @executable_path/Frameworks @loader_path/Frameworks
LD_RUNPATH_SEARCH_PATHS[sdk=macosx*] = $(inherited) @executable_path/../Frameworks @loader_path/../Frameworks
LD_RUNPATH_SEARCH_PATHS[sdk=watch*]   = $(inherited) @executable_path/Frameworks @loader_path/Frameworks
LD_RUNPATH_SEARCH_PATHS[sdk=appletv*] = $(inherited) @executable_path/Frameworks @loader_path/Frameworks
```

---

## _Recommendations_
### _(And downsides)_
![image](images/dj.jpeg)

---

## _Minimize_
### External dependencies
#### __(Do it by yourself)__

---

### How frameworks are _linked_?
### Should a framework be _embedded_?
### Can I link it _statically_?

---

## _Analyze_
### Existing tools __(e.g. CocoaPods)__

---

## _Find the setup_
### that works for you

---

## Freelance

![inline](images/setup_freelance.png)

---

## Feature Teams

![inline](images/setup_teams.png)

---

## Multi-Platform apps

![inline](images/setup_platforms.png)

---

## _Simplicity_
### and clarity

---

## Define a public contract
### _and communication patterns_
^For example if it's objective-c, interfaces should be Objective-C compatible.

---

## Versioning
### _+ monorepository_

^Keep all the frameworks in the same repository.
^Version only if there's another project reusing the framework.

---

## _Documentation_
### Not good enough

---

# _Xcode_
## Has a long way to go
### 🙏 :apple:

^ Error forwarding
^ Frameworks recompilation

---

# Reference

- [frameworkoriented.io](frameworkoriented.io)
- [Building Modern Frameworks](https://developer.apple.com/videos/play/wwdc2014/416/)
- [How to create a Framework for iOS](https://www.raywenderlich.com/65964/create-a-framework-for-ios)
- [Framework vs Library](http://www.knowstack.com/framework-vs-library-cocoa-ios/)
- [Static and Dynamic libraries](https://pewpewthespells.com/blog/static_and_dynamic_libraries.html)
- [The Unofficial Guide to xcconfig files](https://pewpewthespells.com/blog/xcconfig_guide.html)

---

# Thanks
## _Questions?_

![](images/krakow.jpeg)

##### _Photos from_ [_Unsplash_](https://unsplash.com)
#### _Slides_ [_bit.ly/framework-oriented_](http://bit.ly/framework-oriented)

---

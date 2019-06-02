# Building RN

## Getting the source

From Github

    git clone https://github.com/facebook/react-native

Or

    wget https://github.com/facebook/react-native/archive/v0.59.5.zip

Or

    npm pack react-native && tar xf react-native-0.59.5.tgz 

Or

    npm i react-native

From here you will only need the following folders:

    Libraries
    React
    ReactCommon
    scripts
    third-party

## Build in Xcode

From React native's source directory add the main Xcode project to a new Xcode workspace

    React/React.xcodeproj

Also it can be useful to change the _Workspace Settings_ to store _Derived Data_ in a _Workspace-relative Location_ together React Native sources

    ReactNative.xcworkspace
    DerivedData

In Xcode, build both for one of the simulators and the  _Generic iOS device_. BInary archives (`.a` files) for both architectures will be generated. All binaries are stored on a *per workspace* basis.

### Build additional libraries

Again from React natives's source directory add additional projects to the same workspace

    Libraries/WebSocket/RCTWebSocket.xcodeproj   

Build again for ARM and Intel

# Linking to host project

With an Config.xcconfig file

```
// Dependencis
//
// React (Bridge, ReactView)
LIB_0 = React
LIB_1 = yoga
LIB_2 = cxxreact
LIB_3 = double-conversion
LIB_4 = third-party
// LIB_5 = jsinspector
// LIB_6 = jsi
// LIB_7 = jsiexecutor
// WebSocket
LIB_8 = RCTWebSocket
LIB_9 = fishhook
// Text
LIB_10 = RCTText

// Prep
//
LIBS_WORKSPACE=$(PROJECT_DIR)/ReactNative
LIB_PRODUCTS = $(LIBS_WORKSPACE)/DerivedData/React/Build/Products/$(CONFIGURATION)$(EFFECTIVE_PLATFORM_NAME)
LIB_HEADERS = $(LIB_PRODUCTS)/include

// Xcode variables
//
SWIFT_OBJC_BRIDGING_HEADER=ExampleOne/Bridging-Header.h
LIBRARY_SEARCH_PATHS = $(inherited) $(LIB_PRODUCTS)
HEADER_SEARCH_PATHS = $(inherited) $(LIB_HEADERS)
FRAMEWORK_SEARCH_PATHS = $(inherited) $(DEVELOPER_FRAMEWORKS_DIR)
// SWIFT_INCLUDE_PATHS = $(inherited) $(LIB_PRODUCTS)

OTHER_LDFLAGS = $(inherited) -ObjC -lc++ -framework JavaScriptCore -l$(LIB_0) -l$(LIB_1) -l$(LIB_2) -l$(LIB_3) -l$(LIB_4) -l$(LIB_8) -l$(LIB_9) -l$(LIB_10)
```
To load bundle
```
  <key>NSAppTransportSecurity</key>
  <dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
  </dict>
```

## Non programmatically

Add all products and frameworks from React and RCTWebSocket (and other needed dependencies) into the host project's linked frameworks and libraries.

`General -> Linked Frameworks and Libraries`  or  `Build Phases > Link Binary With Libraries`

From React

    libReact.a
    libcxxreact.a
    JavaScriptCore.framework
    libyoga.a
    libdouble-conversion.a
    libthird-party.a

From RCTWebSocket

    libRCTWebSocket.a
    libfishhook.a

# Integrating React Native into Swift code 

Render a `RCTRootView` from within a `UIViewController`

    class ViewController: UIViewController {

      var bridge: RCTBridge!

      override func viewDidLoad() {
        super.viewDidLoad()
        ...
        let jsCodeLocation = NSURL(string: "http://localhost:8081/index.ios.bundle?platform=ios&dev=true")
        // Optionally load from a production bundle
        // jsCodeLocation = NSBundle.mainBundle().URLForResource("main", withExtension: "jsbundle")
        let rootView = RCTRootView(bundleURL:jsCodeLocation! as URL, moduleName: "yourModuleName", initialProperties: nil)
        self.bridge = rootView?.bridge
        self.view = rootView
      }
    }

The module name has to match that of the Javascript entry-point:

    AppRegistry.registerComponent(‘yourModuleName’, () => yourModuleNameComponent)

# Generating the bundle

Go to `Build phases`

_New Run Script Phase_. Call it "Bundle React Native code and images", open it and below the _Shell_ input put this:

    export NODE_BINARY=node
    ../node_modules/react-native/packager/react-native-xcode.sh

# Additional notes

## Glog related errors

If Xcode reports an error related go _glog_ run the following script from React native's source dir

    cd third-party/glog-0.3.4/
    ../../scripts/ios-configure-glog.sh

## Other linker flags

In Xcode go to your host project file. In _Build Settings_ look for _Other Linker Flags_ (`OTHER_LDFLAGS`) and set it to:

    -ObjC
    -lc++
    $(inherited)

## Bridging header

A trick for automatically configure the bridging header is adding an empty Objective-C source file into your swift project which can later be deleted.

Bridging header is set at `Build Settings > Swift Compiler - General > Objective-C Bridging Header`

    SWIFT_OBJC_BRIDGING_HEADER=ProjectName/Bridging-Header.h

ProjectName is the group's folder. Bridging header target membership *none*

Contents

    #import <React/RCTBridge.h>
    #import <React/RCTRootView.h>


## Grouping dependencies in a framework (Cocoa Pods-style)

The framework project becomes the host project.
Add React main and library Xcode project *into* the framework project
Drag products into linked libraries
￼

## Scheme settings (implicit dependencies/parallel building)

For the host project's scheme is best to uncheck _Find implicit dependencies_ in the build step, otherwise it will rebuild React. Cleaning does clean the whole workspace, including React's binaries.

### Framework (Cocoa Pod-style setup)

For framework setup, add React to the scheme’s build step
Put it first in execution order
Disable parallel building
￼
# Some links

- Manually integrating react native into an iOS project
https://codeburst.io/integrate-react-native-into-an-existing-app-ios-87c947a16044

- Using React with Swift instead of Objective-C
http://www.ikantam.com/blog/swift-modules-in-react-native

- Oficial integration guide using Cocoa Pods (does not always work)
https://facebook.github.io/react-native/docs/integration-with-existing-apps.html

- How to link any React Native library (built-in and third-party)
https://facebook.github.io/react-native/docs/linking-libraries-ios.html

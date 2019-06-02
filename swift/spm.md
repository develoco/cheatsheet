# Swift Package Manage

## Package dependencies

 - Any git repo can be added as a package dependency to an xcode project
 - Local packages can be installed with `file://` URLs (i.e. `file:///Users/you/your-package`) but need a git repo with commits (`git init && git add * && git commit`)

## Publish a package

 - Needs an initialized git repo with commits
 - Needs a `Package.swift` file at the root of the repo
 - Tags need to be in the cannonical `1.2.3` format (no leading _v_ or other formats)
 - Packages can't have resources. Text files can be wrapped with a Swift variable.

### Sample `Package.swift`

Minimal config

```
// swift-tools-version:5.1
import PackageDescription

let package = Package(
  name: "MyLibrary",
  products: [
    .library(
      name: "MyLibrary",
      targets: ["MyLibrary"]),
  ],
  dependencies: [],
  targets: [
    .target(
      name: "MyLibrary",
      dependencies: []),
  ]
)
```

Sample with sub-dependencies and platforms

 ```
 // swift-tools-version:5.1
import PackageDescription

let package = Package(
  name: "HyperHooks",
  platforms: [
   .macOS(.v10_15), .iOS(.v13),
  ],
  products: [
    .library(
      name: "HyperHooks",
      targets: ["HyperHooks"]),
  ],
  dependencies: [
    .package(url: "https://github.com/hyperhooks/hyperhooks-spm.git", from: "0.0.1"),
  ],
  targets: [
    .target(
      name: "HyperHooks",
      dependencies: ["hyperhooks"]),
  ]
)
```

### Public classes

Classes, structs and members need to be `public`

```
public struct MyLibrary {
    public init() {}
    public var text = "Hello, World!"
}
```

Global variables don't need this

```
let foo = "bar" // accessible from package consumers
```

### Wrap other text resources

As a workaround, to include other text resources in the package they need to be added as `.swift` files, for instance `hyperswift.js.swift`:

let hyperswift_js = #"""
// your javascript code
...
"""#

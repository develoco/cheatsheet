# Building a library/framework

Add framework/lib xcode project to the same workspace as the client app. Build libaries for both ARM and Intel (arm64, x86_64)

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

Package and distribute a binary framework
https://instabug.com/blog/ios-binary-framework/

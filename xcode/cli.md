xcode xcodebuild cli

# xcodebuild

List workspace's schemes

    xcodebuild -workspace TenantBoilerplateNative.xcworkspace -list 


-w is to provide a device type

    xcrun instruments -w "iPhone 5 (8.0 Simulator)"

to get the list of available devices just execute this:

    xcrun instruments -s

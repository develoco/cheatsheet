react, reactnative, ios, android, xcode

# command line

    npx yarn # yarn install
    REACT_EDITOR=bbedit yarn start --reset-cache

    cd ios
    pod install

    react-native run-ios --scheme "Development" --configuration "Debug" --simulator "iPhone XS"

    PROXY_TARGET=http://obx05-thombrowne.fftech.info npm run start-dev

    git commit --no-verify -a -m "fix: conflicts"

    xcrun instruments -t "Blank" -w "iPhone XR"
    
To get a list of devices and templates

    xcrun instruments -s

    ▸ Compiling bignum.cc
    error: Build input file cannot be found: '/Users/coder/gb/thombrowne-native/node_modules/react-native/third-party/double-conversion-1.1.5/src/bignum.cc'
    ** BUILD FAILED **

    pushd node_modules/react-native/scripts
    ./ios-install-third-party.sh
    popd

    node_modules/react-native/third-party/glog-0.3.4/
    ../../scripts/ios-configure-glog.sh
    popd

https://github.com/facebook/react-native/issues/21168#issuecomment-422431294


    npm run react-native run-ios --simulator “iPhone X” --scheme "Production" --configuration "Release"

Note: the 'Bundle React Native code and images' part takes a while.

    xcrun instruments -w "iPhone 5 (8.0 Simulator)"

-w is to provide a device type and to get the list of available devices just execute this:

    xcrun instruments -s


    yarn react-native run-ios --scheme "Staging" --device

It can also include the device name if more than one

There is also a --configuration “production” and --simulator options

npm install -g --unsafe-perm=true ios-deploy




Releasing

iOS
1. Run app in release configuration to test in the same enviroment
2. Increment the build number in the project general tab
3. Select as target "Generic iOS Device", click on *Product > Archive*
4. After archive succeeds go to *Window > Organizer* and click on *Export*
5. Select *Ad Hoc* and hit *Next* with "None" App Thinning and both checkboxes checked
6. Fill the URLs with random ones (gaspardbruno.com) and click *Next*
7. *Next* again with Automatically manage signing
8. After the process is done click *Export* and put the folder somewhere
9. Go to the App Center, select the right app and go to *Distribute*
10. Click on *New Release* and drag and drop the .ipa from the previously exported folder
11. Click *Next* and select the group to distribute too
12. Done!
If some new UUID was added to the provisioning you will need to refresh all your profiles:
https://stackoverflow.com/questions/44060482/xcode-8-3-xcode-9-0-refresh-provisioning-profile-devices

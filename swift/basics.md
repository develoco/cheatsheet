swift, c, objc, objectivec, mac, macos, ios

# Links

Gist https://gist.github.com/maincc/


# Swift call a shell command from within a cocoa app

More info here https://gist.github.com/Seasons7/836d3676884a40c8c98a

    sender.isEnabled = false
    let task = Process()
    let pipe = Pipe()
    task.executableURL = URL(fileURLWithPath: "/bin/ls")
    task.arguments = ["-la","./"] //multiple options
    task.standardOutput = pipe
    task.waitUntilExit()
    try! task.run()
    let handle = pipe.fileHandleForReading
    let data = handle.readDataToEndOfFile()
    let printing = String (data: data, encoding: String.Encoding.utf8)
    print(printing!)
    sender.isEnabled = true


# Swift + C interoperability, command line building

To clean

    rm c.o main

bridge.h

    void getInput(int *output);

build.sh

    clang ins.c -c
    # It should be possible to pass the -c option from swiftc
    swiftc main.swift ins.o -import-objc-header bridge.h
    # This does not work as is: swiftc -Xlinker-macosx_version_min 10.13
    # This works: ld -macosx_version_min 10.13

ins.c

    #include <stdio.h>
    void getInput(int *output) {
        scanf("%i", output);
    }

main.swift
    import Foundation

    var output: CInt = 0
    getInput(&output)

    print(output)

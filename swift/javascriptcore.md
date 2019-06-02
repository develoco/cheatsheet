# Playground

In a Swift Playground function calls _in javascript_ are **not supported**

```
import JavaScriptCore

let context:JSContext! = JSContext()

context.evaluateScript(#"""
'use strict'
this.double = n =>  n * 2
this.someObj = {
  foo: 'bar'
}
"""#)

let jsObj: JSValue = context.objectForKeyedSubscript("someObj")!
jsObj.objectForKeyedSubscript("foo").toString()

let d = context.evaluateScript("double(5)")?.toInt32() // 10
```

# App

For testing more complex integrations use a dummy Mac / iOS app / test

```
//let getMultiply: @convention(block) () -> (Int, Int) -> Int  = {
//  /* return */  { $0 * $1 }
//}
context.setObject(Blocks.getMultiply, forKeyedSubscript: "getMultiply" as NSString)

//context.setObject({
//  { $0 + $1 }
//} as @convention(block) () -> (Int, Int) -> Int, forKeyedSubscript: "getAdd" as NSString)

context.setObject(Blocks.getAdd, forKeyedSubscript: "getAdd" as NSString)

context.setObject([4, 3, 5], forKeyedSubscript: "numberParams" as NSString)

context.evaluateScript(#"""
this.double = n =>  n * 2
"""#)

context.evaluateScript(#"""
let multiply = getMultiply()
let add = getAdd()
this.jsResult = multiply(add(this.numberParams[0], this.numberParams[1]), this.numberParams[2])
"""#)
Blocks.getAdd

context.objectForKeyedSubscript("jsResult").toInt32



/*
let t = context.evaluateScript("triple(5)")?.toInt32() // 15
print(d!)
print(t!)
print(context.objectForKeyedSubscript("a")?.toString() ?? "")
print(context.objectForKeyedSubscript("b")?.toString() ?? "")
print(context.objectForKeyedSubscript("c")?.toString() ?? "")
//print(context.objectForKeyedSubscript("appInstance")?.toArray() ?? "")
//let app = (context.objectForKeyedSubscript("appInstance[2]")?.toArray()!)!
let res = (context.evaluateScript("this.appInstance[2]")?.toArray()!)!
for part in res {
  print(part)
}
*/

let getRandomFuncs: @convention(block) () -> [ @convention(block) () ->
 Void ] = {
  [{ print("hello random 1") }, { print("hello random 2") }]
}
context.setObject(getRandomFuncs, forKeyedSubscript: "getRandomFuncs" as NSString)

let getRandomFunc: @convention(block) () -> () -> Void = {
  { print("hello random") }
}
context.setObject(getRandomFunc, forKeyedSubscript: "getRandomFunc" as NSString)

let swiftPrint: @convention(block) (String) -> ()  = {
  print($0)
}
context.setObject(swiftPrint, forKeyedSubscript: "swiftPrint" as NSString)

let populateObject: @convention(block) (JSValue) -> ()  = { arg in
  //let arg = JSContext.currentArguments()[0] as! JSValue
  arg.setValue("barbar", forProperty: "foofoo")
  arg.setValue(667, forProperty: "someint")
  print("inside populateObject")
//      objToPopulate["foo"] = "bar"
//      objToPopulate["baz"] = 3
}
context.setObject(populateObject, forKeyedSubscript: "populateObject" as NSString)

context.evaluateScript(#"""

this.simpleFunction = (number, text) => `Number is ${number} and text is ${text}`

this.getRandomFunc()()

let [f1, f2] = this.getRandomFuncs()
f1()
f2()

this.getBackSwiftPrint = () => this.swiftPrint

this.funcThatReturnsAJsFunc = (a) => () => {
  this.swiftPrint(`You have chosen "${a}"!`)
}

let containerObj = {}
this.populateObject(containerObj)
this.swiftPrint(containerObj['foofoo'])
this.swiftPrint(containerObj['someint'])

"""#)

let myArray = JSValue.init(newArrayIn: JSContext.current())!
myArray.setValue(1, at: 0)
myArray.setValue("dos", at: 1)
myArray.setValue(3.3, at: 2)
context.setObject(myArray, forKeyedSubscript: "myArray" as NSString)

```

To load javascript from a file


```
let mainJsUrl = Bundle.main.url(forResource: "main", withExtension: "js")!
context.evaluateScript(try! String(contentsOf: mainJsUrl), withSourceURL: mainJsUrl)
```

To run javascriptcore (jsc) on the command line:

`/System/Library/Frameworks/JavaScriptCore.framework/Versions/A/Resources/jsc`

To exit:

```
>>> quit()
```

Optional parameters, returning objects

```
let createContainer: @convention(block) (JSValue, JSValue) -> JSValue = { resetContainerJs, containerTypeJs in

  let resetContainer = resetContainerJs.isUndefined ? false : resetContainerJs.toBool()
  let containerType = containerTypeJs.isUndefined || containerTypeJs.toString() == nil ? "VStack" : containerTypeJs.toString()!

  // Return object
  let containerJs = JSValue.init(newObjectIn: context)!
  containerJs.setValue(createContainer, forProperty: "createContainer")
  containerJs.setValue(addElement, forProperty: "addElement")
  return containerJs
}
```

Return undefined

```
JSValue(undefinedIn: context)
```


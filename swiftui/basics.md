## Playground

```
import PlaygroundSupport
import SwiftUI

struct ContentView : View {
    var body: some View {
        Text("Hello World")
    }
}


PlaygroundPage.current.liveView = UIHostingController(rootView: ContentView())
```

Playground with `PreviewProvider`

```
...

struct ContentView_Previews : PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}

PlaygroundPage.current.liveView = UIHostingController(rootView: ContentView_Previews.previews)
```

## ViewBuilder

Here's an example view that does nothing, just to demonstrate how to use `@ViewBuilder`.

```
struct Passthrough<Content>: View where Content: View {

    let content: () -> Content

    init(@ViewBuilder content: @escaping () -> Content) {
        self.content = content
    }

    var body: some View {
        content()
    }

}
```

Usage:

```
Passthrough {
    Text("one")
    Text("two")
    Text("three")
}
```

## Group / ForEach / if's


```
struct ContentView : View {
    var contents = ForEach([1,2,3]) {
      Text("Hello \($0) Again")
    }
    var body: some View {
        Group {
          if (false) {
            HStack { contents }
          } else {
            VStack { contents }
          }
        }
      }
}
```

## Multiple view types

```
struct ContentView : View {
    var myTypes: [Any] = [View1.self, View2.self]
    var body: some View {
        List {
            ForEach(0..<myTypes.count) { index in
                self.buildView(types: self.myTypes, index: index)
            }
        }
    }

    func buildView(types: [Any], index: Int) -> AnyView {
        switch types[index].self {
           case is View1.Type: return AnyView( View1() )
           case is View1.Type: return AnyView( View2() )
           default: return AnyView(EmptyView())
        }
    }
```

## Pseudo-optional properties

```
protocol Model {
  static var isContainer:Bool { get }
}

extension Model {
  static var isContainer:Bool { get { false } }
}

class NonContainerModel: Model {
}

class ContainerModel: Model {
  static var isContainer:Bool { get { true } }
}
```

## High-Order Components (HOCs)

```
// Declaring ContainerView as a HOC
struct ContainerView<C: View> : View {
  let childView: C
  
  init(_ childView: () -> (C)) {
    self.childView = childView()
  }
  
  var body: some View {
    childView
  }
}

// Using ContainerView by passing SwiftUI's Text Component
ContainerView {
  Text("Hello There I'm a child component!")
}
```

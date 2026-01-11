# macOS API Guide - Appendices

## Appendix A: Quick Reference Tables

### Framework Selection Matrix

| App Type    | Primary UI     | Data Layer   | Special Frameworks               | Complexity  |
| ----------- | -------------- | ------------ | -------------------------------- | ----------- |
| Utility     | SwiftUI        | UserDefaults | -                                | Low         |
| Business    | AppKit/SwiftUI | Core Data    | Combine                          | Medium      |
| Media       | AppKit         | Core Data    | AVFoundation, Metal              | High        |
| Creative    | AppKit         | Core Data    | Metal, Core Graphics, Core Image | High        |
| Network     | SwiftUI        | Core Data    | URLSession, Network Framework    | Medium      |
| System Tool | AppKit         | -            | IOKit, SystemConfiguration       | Medium-High |
| Game        | Metal          | -            | GameplayKit, MetalKit            | High        |

### Common Framework Combinations

**Simple Apps:**

- SwiftUI + Foundation
- SwiftUI + Foundation + UserDefaults

**Standard Apps:**

- SwiftUI + Foundation + Core Data
- AppKit + Foundation + Core Data + Combine

**Media-Rich Apps:**

- AppKit + AVFoundation + Metal + Core Graphics
- AppKit + Core Data + AVFoundation + Metal

**Network Apps:**

- SwiftUI + URLSession + Keychain + CloudKit
- SwiftUI + Network Framework + Combine + Core Data

---

## Appendix B: Framework Import Statements

```swift
// UI Frameworks
import AppKit        // Traditional macOS UI
import SwiftUI       // Modern declarative UI

// Foundation
import Foundation    // Core data types and services

// Data & State
import CoreData      // Object persistence
import Combine       // Reactive programming

// Graphics & Media
import CoreGraphics  // 2D drawing
import Metal         // GPU programming
import MetalKit      // Metal convenience APIs
import AVFoundation  // Audio/video
import CoreImage     // Image processing

// System Services
import Network       // Modern networking
import Security      // Cryptography and keychain
import UserNotifications  // Notifications

// Specialized
import GameplayKit   // Game development
import IOKit         // Hardware access
import CloudKit      // iCloud integration
import CoreLocation  // Location services
```

---

## Appendix C: SwiftUI vs AppKit Comparison

### When SwiftUI Wins

**Advantages:**

- 50-70% less code for common UI
- Automatic state-driven updates
- Built-in animations
- Live preview in Xcode
- Cross-platform code sharing
- Modern Swift features

**Best for:**

- New projects
- Standard UI patterns
- Rapid prototyping
- Cross-platform apps
- Teams familiar with reactive programming

### When AppKit Wins

**Advantages:**

- Complete control over every detail
- Access to all macOS features immediately
- Mature and stable
- Better performance for complex UIs
- Sophisticated table/outline views
- More Stack Overflow answers

**Best for:**

- Complex custom controls
- Document-based apps
- Apps requiring pixel-perfect layouts
- Supporting older macOS versions
- Teams experienced with AppKit
- Apps needing features not yet in SwiftUI

### Hybrid Approach

Many modern apps use **both**:

- SwiftUI for standard UI sections
- AppKit for specialized controls
- Bridge between them using `NSViewRepresentable` (AppKit → SwiftUI) or `NSHostingView` (SwiftUI → AppKit)

---

## Appendix D: Common Patterns & Architectures

### MVC (Model-View-Controller)

#### **Traditional AppKit pattern**

```text
Model (Core Data, Business Logic)
   ↓
Controller (NSViewController)
   ↓
View (NSView, UI Elements)
```

**Pros:** Well-understood, AppKit's natural pattern
**Cons:** Controllers can become bloated

---

### MVVM (Model-View-ViewModel)

#### **Popular with SwiftUI**

```text
Model (Data, Business Logic)
   ↓
ViewModel (ObservableObject)
   ↓
View (SwiftUI Views)
```

**Pros:** Testable, reactive, clean separation
**Cons:** Can be verbose for simple apps

**Example:**

```swift
class ViewModel: ObservableObject {
    @Published var items: [Item] = []

    func loadItems() {
        // Load from network or database
    }
}

struct ContentView: View {
    @StateObject var viewModel = ViewModel()

    var body: some View {
        List(viewModel.items) { item in
            Text(item.name)
        }
        .onAppear {
            viewModel.loadItems()
        }
    }
}
```

---

### TCA (The Composable Architecture)

#### **Advanced state management**

**When to use:**

- Complex apps with lots of state
- Apps requiring extensive testing
- Teams needing strict architectural guidelines

**Key concepts:**

- Single source of truth
- Unidirectional data flow
- Composition over inheritance
- Side effects handled explicitly

---

## Appendix E: Performance Considerations

### Memory Management

**Swift uses ARC (Automatic Reference Counting):**

- Objects deallocated when reference count hits zero
- Be careful of retain cycles with closures

**Avoiding retain cycles:**

```swift
class ViewController: NSViewController {
    var closure: (() -> Void)?

    func setupClosure() {
        // BAD - creates retain cycle
        closure = {
            self.doSomething()
        }

        // GOOD - uses weak self
        closure = { [weak self] in
            self?.doSomething()
        }
    }
}
```

### Threading

**Main thread rules:**

- All UI updates MUST happen on main thread
- Long-running tasks should be on background threads

**Common patterns:**

```swift
// Background work, update UI on main thread
DispatchQueue.global(qos: .userInitiated).async {
    let result = self.expensiveOperation()

    DispatchQueue.main.async {
        self.updateUI(with: result)
    }
}

// Swift Concurrency (modern approach)
Task {
    let result = await expensiveOperation()
    // Already on main actor for UI updates
    updateUI(with: result)
}
```

### Asset Optimization

- Use **SF Symbols** for icons (built-in, scalable)
- **Asset catalogs** for images (automatic @2x/@3x handling)
- **Lazy loading** for large data sets
- **Image caching** to avoid repeated decoding

---

## Appendix F: Debugging & Tools

### Xcode Instruments

**Essential profiling tools:**

- **Time Profiler** - Find performance bottlenecks
- **Allocations** - Track memory usage
- **Leaks** - Find memory leaks
- **Network** - Monitor network activity
- **Metal System Trace** - GPU performance

### Console Logging

```swift
import os.log

let logger = Logger(subsystem: "com.yourapp", category: "networking")

logger.debug("Starting network request")
logger.info("Request completed successfully")
logger.warning("Slow response time: \(duration)s")
logger.error("Network request failed: \(error)")
```

### Breakpoints

- **Regular breakpoints** - Pause execution
- **Conditional breakpoints** - Pause only when condition met
- **Symbolic breakpoints** - Break on all calls to a method
- **Exception breakpoints** - Catch crashes immediately

---

## Appendix G: Code Signing & Distribution

### Development

- **Automatic signing** - Let Xcode manage certificates
- **Development certificates** - For testing on your Mac
- **Provisioning profiles** - Link app to your developer account

### Distribution

**Options:**

1. **Mac App Store** - Apple handles distribution, requires review
2. **Direct distribution** - Notarize with Apple, distribute yourself
3. **Developer ID** - Code sign for distribution outside App Store

**Requirements:**

- Apple Developer Program membership ($99/year)
- Valid certificates
- App notarization (required for Catalina+)
- Hardened runtime enabled

---

## Appendix H: Useful Third-Party Libraries

While Apple's frameworks are comprehensive, some popular third-party options:

### UI/UX

- **Sparkle** - Auto-update framework
- **MASShortcut** - Keyboard shortcut recorder

### Networking

- **Alamofire** - Elegant HTTP networking (though URLSession is usually sufficient)

### Utilities

- **SwiftyJSON** - JSON parsing (less needed with Codable)
- **Realm** - Alternative to Core Data

**Note:** Prefer Apple frameworks when possible for:

- Better integration
- Long-term support
- No external dependencies
- Consistent behavior across macOS versions

---

## Appendix I: SwiftUI Property Wrappers Reference

### State Management

| Wrapper              | Purpose                       | Ownership            | Example Use                         |
| -------------------- | ----------------------------- | -------------------- | ----------------------------------- |
| `@State`             | View-local mutable state      | View owns it         | Toggle states, local counters       |
| `@Binding`           | Two-way reference to state    | Parent owns it       | Child views modifying parent state  |
| `@StateObject`       | Reference to ObservableObject | View owns it         | ViewModel created by view           |
| `@ObservedObject`    | Reference to ObservableObject | External ownership   | ViewModel passed from parent        |
| `@EnvironmentObject` | Shared object in environment  | Ancestor provides it | App-wide settings, user session     |
| `@Environment`       | System environment values     | System provides it   | Color scheme, locale, accessibility |
| `@AppStorage`        | UserDefaults-backed state     | Persisted            | User preferences                    |
| `@SceneStorage`      | Scene-specific state          | Scene persisted      | Tab selection, scroll position      |

**Example:**

```swift
struct ParentView: View {
    @StateObject var viewModel = ViewModel()
    @State private var isShowingDetail = false

    var body: some View {
        ChildView(isShowing: $isShowingDetail, viewModel: viewModel)
    }
}

struct ChildView: View {
    @Binding var isShowing: Bool
    @ObservedObject var viewModel: ViewModel
    @Environment(\.colorScheme) var colorScheme
    @AppStorage("username") var username = "Guest"

    var body: some View {
        Text("Hello, \(username)")
    }
}
```

---

## Appendix J: Common macOS Development Gotchas

### 1. Main Thread Requirements

**Problem:** UI updates from background thread crash or don't appear
**Solution:** Always update UI on main thread

```swift
// BAD
Task {
    let data = await fetchData()
    self.label.stringValue = data  // Might not be on main thread!
}

// GOOD
Task {
    let data = await fetchData()
    await MainActor.run {
        self.label.stringValue = data
    }
}
```

### 2. Retain Cycles

**Problem:** Memory leaks from strong reference cycles
**Solution:** Use `[weak self]` or `[unowned self]` in closures

```swift
// BAD
timer = Timer.scheduledTimer(withTimeInterval: 1.0, repeats: true) { _ in
    self.updateUI()  // Retains self
}

// GOOD
timer = Timer.scheduledTimer(withTimeInterval: 1.0, repeats: true) { [weak self] _ in
    self?.updateUI()
}
```

### 3. File Access in Sandbox

**Problem:** App can't access files outside its container
**Solution:** Use security-scoped bookmarks or NSOpenPanel

```swift
// Get user permission via open panel
let panel = NSOpenPanel()
panel.canChooseDirectories = true
panel.begin { response in
    if response == .OK, let url = panel.url {
        // Now you can access this directory
    }
}
```

### 4. SwiftUI View Updates

**Problem:** View doesn't update when data changes
**Solution:** Make sure data is wrapped in proper property wrapper

```swift
// BAD
class ViewModel {
    var count = 0  // Changes won't trigger UI updates
}

// GOOD
class ViewModel: ObservableObject {
    @Published var count = 0  // Changes trigger UI updates
}
```

### 5. AppKit View Lifecycle

**Problem:** viewDidLoad called multiple times or not when expected
**Solution:** Understand view loading vs. appearing

```swift
override func viewDidLoad() {
    // Called once when view first loads
    // Good for one-time setup
}

override func viewWillAppear() {
    // Called every time view is about to appear
    // Good for refreshing data
}
```

---

## Appendix K: Migration Guides

### Transitioning from iOS to macOS

**Key differences:**

- Windows vs. Scenes
- Mouse/trackpad vs. touch
- Keyboard shortcuts expected
- Menu bar is standard
- Multiple windows common
- Preferences window vs. Settings app

**Shared code potential:**

- Foundation code identical
- Business logic identical
- Network layer identical
- SwiftUI views mostly portable (with adjustments)

### Transitioning from AppKit to SwiftUI

**Gradual approach:**

1. Start with new views in SwiftUI
2. Wrap AppKit views in `NSViewRepresentable` when needed
3. Use `NSHostingView` to embed SwiftUI in AppKit
4. Migrate simple views first
5. Keep complex custom controls in AppKit initially

**Example bridge:**

```swift
// Wrapping AppKit in SwiftUI
struct MyAppKitView: NSViewRepresentable {
    func makeNSView(context: Context) -> NSTextField {
        let textField = NSTextField()
        return textField
    }

    func updateNSView(_ nsView: NSTextField, context: Context) {
        // Update view when SwiftUI state changes
    }
}

// Embedding SwiftUI in AppKit
let swiftUIView = MySwiftUIView()
let hostingView = NSHostingView(rootView: swiftUIView)
appKitView.addSubview(hostingView)
```

---

## Appendix L: Future-Proofing Your App

### Stay Current

- **Adopt new APIs gradually** - Don't rush, but don't fall behind
- **Use Swift Concurrency** - async/await is the future
- **Embrace SwiftUI** - Apple's clear direction for UI
- **Support latest macOS** - New features attract users

### Deprecation Strategy

When Apple deprecates APIs:

1. **Don't panic** - Usually years of warning
2. **Plan migration** - Budget time for updates
3. **Test thoroughly** - Especially with beta macOS versions
4. **Maintain backward compatibility** - Support a few macOS versions

### Architecture for Change

```swift
// Good - Protocol-based design
protocol DataProvider {
    func fetchData() async -> [Item]
}

// Can swap implementations easily
class NetworkDataProvider: DataProvider { }
class LocalDataProvider: DataProvider { }

// View doesn't care about implementation
class ViewModel {
    let dataProvider: DataProvider

    func loadData() async {
        let items = await dataProvider.fetchData()
    }
}
```

---

## Appendix M: Additional Resources

### Official Apple Resources

- **WWDC Videos** - https://developer.apple.com/videos/
- **Swift Forums** - https://forums.swift.org/
- **Developer Forums** - https://developer.apple.com/forums/
- **Documentation** - https://developer.apple.com/documentation/

### Community Resources

- **Stack Overflow** - [swift], [macos], [appkit], [swiftui] tags
- **Reddit** - r/swift, r/iOSProgramming
- **GitHub** - Search for example projects

### Books

- "SwiftUI for Mac" by Sarah Reichelt
- "AppKit Handbook" - Various authors
- "Advanced Swift" by objc.io team

### Staying Updated

- Follow Apple Developer on Twitter
- Subscribe to iOS Dev Weekly (covers macOS too)
- Watch WWDC keynotes and sessions
- Read Apple's release notes for each macOS version

---

## Getting Started Checklist

### Learning Path

1. **Start with Swift basics** - Understand the language first
2. **Learn Foundation** - Essential for everything else
3. **Choose UI framework** - SwiftUI for modern, AppKit for traditional
4. **Add specialized frameworks** - Based on your app's needs
5. **Understand the system** - Darwin, XNU, and how macOS works

### Best Practices

- **Start simple** - Don't use frameworks you don't need
- **Follow Apple's guidelines** - HIG and API design patterns
- **Test on multiple macOS versions** - Ensure compatibility
- **Profile performance** - Use Instruments to find bottlenecks
- **Handle errors gracefully** - Don't crash on unexpected input
- **Respect user privacy** - Request permissions appropriately
- **Support accessibility** - VoiceOver, keyboard navigation
- **Think about localization** - International users from day one

---

_Use this appendices document alongside the main "Complete Guide to macOS APIs" for comprehensive reference._

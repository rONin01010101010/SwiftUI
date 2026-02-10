# SwiftUI Reference Guide
**Complete Syntax & Structure Guide for Non-Storage Applications**

---

## Quick Decision Tree: "Which Element Should I Use?"

### Need to STORE data that changes?
- **User typed text?** → `@State private var text: String = ""`
- **Number that changes?** → `@State private var count: Int = 0`
- **On/Off value?** → `@State private var isOn: Bool = false`
- **Calculated value?** → Use computed property (`var result: Double { }`)

### Need to ARRANGE items?
- **Top to bottom (vertical)?** → `VStack { }`
- **Left to right (horizontal)?** → `HStack { }`
- **Overlapping layers?** → `ZStack { }`
- **Sections with headers?** → `Form { Section { } }`
- **Scrolling list?** → `List { }` or `ScrollView { }`

### Need USER INPUT?
- **Single-line text?** → `TextField("hint", text: $variable)`
- **Choose from 2-4 options?** → `Picker { }.pickerStyle(.segmented)`
- **Choose from 5+ options?** → `Picker { }.pickerStyle(.menu)`
- **On/off switch?** → `Toggle("label", isOn: $variable)`
- **Number with +/- buttons?** → `Stepper("label", value: $variable)`
- **Sliding scale?** → `Slider(value: $variable, in: 0...100)`

### Need to DISPLAY information?
- **Show text/numbers?** → `Text("content")` or `Text("\(variable)")`
- **Show calculation result?** → `Text("\(computedProperty, specifier: "%.2f")")`
- **Show an icon?** → `Image(systemName: "icon.name")`

### Need USER ACTIONS?
- **Click to do something?** → `Button("title") { action }`
- **Navigate to another screen?** → `NavigationLink("title", destination: NextView())`

### Need to REPEAT items?
- **Show same UI multiple times?** → `ForEach(array, id: \.self) { }`
- **Create a grid/pattern?** → Nested `ForEach` loops

---

## Table of Contents
1. [Basic Structure](#basic-structure)
2. [State Management](#state-management)
3. [UI Components](#ui-components)
4. [Layout Containers](#layout-containers)
5. [Modifiers](#modifiers)
6. [User Input](#user-input)
7. [Control Flow](#control-flow)
8. [Data Types & Structures](#data-types--structures)
9. [Common Patterns](#common-patterns)
10. [Complete Examples](#complete-examples)
11. [Quick Reference](#quick-reference-cheat-sheet)

---

## Basic Structure

### App Entry Point
```swift
import SwiftUI

@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}
```

### Basic View Structure
```swift
import SwiftUI

struct ContentView: View {
    // MARK: - Properties
    @State private var counter: Int = 0
    
    // MARK: - Body
    var body: some View {
        VStack {
            Text("Counter: \(counter)")
            Button("Increment") {
                counter += 1
            }
        }
    }
}

// MARK: - Preview
struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

---

## State Management

### @State (Local State)

**When to use:** When you need a value that can change and update the UI automatically.

**Where to use:** 
- User input (text fields, toggles, sliders)
- Counters or scores
- UI state (showing/hiding views)
- Temporary calculations

**Key concept:** Putting `$` before the variable name creates a **binding** (two-way connection) - when the user changes it, your variable updates, and vice versa.

```swift
// ✅ USE @State FOR:
@State private var isOn: Bool = false          // Toggle switches
@State private var count: Int = 0              // Numbers that change
@State private var text: String = ""           // User text input
@State private var selectedOption: String = "Option 1"  // Picker selections
@State private var sliderValue: Double = 50.0  // Slider positions

// Example usage
Toggle("Switch", isOn: $isOn)        // $ means "bind to this variable"
Text("Count: \(count)")               // No $ - just reading the value
TextField("Name", text: $text)        // $ lets user edit the value
```

**Simple rule:** 
- Use `$variable` when giving control to user input (TextField, Toggle, Picker, Slider)
- Use just `variable` when displaying values (Text, Image labels)

### @Binding (Shared State)

**When to use:** When you need to share a value between parent and child views, and both can modify it.

**Where to use:**
- Reusable custom components
- Passing data to child views
- When child needs to update parent's data

**Key concept:** Parent owns the data with `@State`, child receives it with `@Binding`. Changes in child automatically update parent.

```swift
// ✅ USE @Binding FOR:

// Parent View (owns the data)
struct ParentView: View {
    @State private var username: String = ""     // Parent owns it
    
    var body: some View {
        CustomTextField(text: $username)          // Pass with $
        Text("You entered: \(username)")          // Parent can see changes
    }
}

// Child View (modifies parent's data)
struct CustomTextField: View {
    @Binding var text: String                     // Child receives binding
    
    var body: some View {
        TextField("Enter name", text: $text)      // Changes update parent
    }
}
```

**Simple rule:** 
- Parent uses `@State` to own the data
- Child uses `@Binding` to modify parent's data
- Pass to child with `$variable`

### Computed Properties

**When to use:** When you need to calculate a value based on other variables, and it should auto-update when those variables change.

**Where to use:**
- Mathematical calculations (BMI, temperature conversion, totals)
- Formatting display values
- Conditional logic results
- Any value that depends on @State variables

**Key concept:** Computed properties automatically recalculate whenever their inputs change. They're like formulas in Excel.

```swift
// ✅ USE COMPUTED PROPERTIES FOR:

struct CalculatorView: View {
    @State private var number1: String = ""
    @State private var number2: String = ""
    
    // Computed property (recalculates automatically when number1 or number2 changes)
    var sum: Double {
        let n1 = Double(number1) ?? 0    // Convert to number, use 0 if invalid
        let n2 = Double(number2) ?? 0
        return n1 + n2                    // Return the calculation
    }
    
    // Another computed property for formatting
    var formattedSum: String {
        return String(format: "%.2f", sum)  // Format to 2 decimal places
    }
    
    var body: some View {
        VStack {
            TextField("First number", text: $number1)
            TextField("Second number", text: $number2)
            Text("Sum: \(sum)")              // Updates automatically!
            Text("Formatted: \(formattedSum)")
        }
    }
}
```

**Simple rule:** 
- Use `var` (not `@State`) 
- No `=` sign, use `{ }` with return
- Perfect for calculations that depend on @State variables
- Cleaner than writing functions

---

## UI Components

### Text

**When to use:** When you need to **display text** on screen (read-only, user can't edit).

**Where to use:**
- Labels
- Headers and titles
- Display results
- Instructions
- Error messages
- Any static or dynamic text display

**Key concept:** Text shows information to the user. It can display static text or variables using `\(variable)` syntax.

```swift
// ✅ USE Text FOR:

// Static text
Text("Hello, World!")

// Display a variable
@State private var name: String = "Kenan"
Text("Hello, \(name)!")              // Shows: "Hello, Kenan!"

// Display numbers
@State private var score: Int = 95
Text("Score: \(score)")               // Shows: "Score: 95"

// Formatted numbers (decimal places)
@State private var price: Double = 19.99
Text("Price: $\(price, specifier: "%.2f")")  // Shows: "Price: $19.99"

Text(String(format: "$%.2f", price))  // Alternative formatting

// Styled text
Text("Bold text").bold()
Text("Italic text").italic()
Text("Custom font").font(.title)
Text("Colored text").foregroundColor(.blue)

// Multi-line text
Text("Line 1\nLine 2\nLine 3")

// Common use case: Labels in forms
VStack(alignment: .leading) {
    Text("Temperature")
        .font(.caption)
        .foregroundColor(.gray)
    Text("72°F")
        .font(.title)
        .bold()
}

// Common use case: Results display
Text("Your BMI: \(bmi, specifier: "%.1f")")
    .font(.title2)
    .foregroundColor(bmi < 25 ? .green : .orange)
```

**String Interpolation Examples:**
```swift
let age = 25
let height = 175.5

Text("\(age)")                    // "25"
Text("Age: \(age)")              // "Age: 25"
Text("\(height, specifier: "%.1f") cm")  // "175.5 cm"
```

**Common Formatting:**
```swift
let number = 3.14159

String(format: "%.0f", number)   // "3"    (no decimals)
String(format: "%.1f", number)   // "3.1"  (1 decimal)
String(format: "%.2f", number)   // "3.14" (2 decimals)
```

**Simple rule:** 
- Use Text for any read-only display
- Use `\(variable)` to show variable values
- Chain modifiers to style (`.bold()`, `.font()`, etc.)
- Text is NOT editable - use TextField for user input

### Button

**When to use:** When you need the user to **trigger an action** by tapping.

**Where to use:**
- Submit forms
- Perform calculations
- Navigate to other screens
- Delete/Save/Cancel actions
- Any clickable action

**Key concept:** Buttons execute code when tapped. Put your action code inside the `{ }` braces.

```swift
// ✅ USE Button FOR:

// Simple button
Button("Click Me") {
    print("Button was tapped!")
    // Your action code here
}

// Button that modifies state
@State private var count: Int = 0

Button("Increment") {
    count += 1              // Action happens when tapped
}

// Button with custom styling
Button(action: {
    // Action code
    print("Custom button tapped")
}) {
    Text("Custom Button")
        .font(.headline)
        .foregroundColor(.white)
        .padding()
        .frame(maxWidth: .infinity)
        .background(Color.blue)
        .cornerRadius(10)
}

// Button with image (system icon)
Button(action: {
    // Save action
}) {
    Label("Save", systemImage: "square.and.arrow.down")
}

// Common use case: Multiple buttons
HStack(spacing: 20) {
    Button("Cancel") {
        // Cancel action
        dismiss()
    }
    .foregroundColor(.red)
    
    Button("Save") {
        // Save action
        saveData()
    }
    .foregroundColor(.blue)
}

// Common use case: Calculate button
Button("Calculate BMI") {
    guard let w = Double(weight), let h = Double(height) else {
        return  // Exit if invalid input
    }
    bmi = w / (h * h)  // Perform calculation
}
.padding()
.background(Color.green)
.foregroundColor(.white)
.cornerRadius(8)
```

**Simple rule:** 
- First parameter = text shown on button OR action block
- Code inside `{ }` runs when button is tapped
- Style with modifiers like any other view
- Use `Label()` to add icons
- Can trigger state changes, calculations, navigation, etc.

### Image
```swift
// System image (SF Symbols)
Image(systemName: "heart.fill")
    .foregroundColor(.red)
    .font(.largeTitle)

// Asset image
Image("imageName")
    .resizable()
    .scaledToFit()
    .frame(width: 100, height: 100)
```

### Label
```swift
Label("Title", systemImage: "star.fill")
Label("Custom", systemImage: "gear")
    .font(.headline)
    .foregroundColor(.blue)
```

### Divider & Spacer
```swift
VStack {
    Text("Top")
    Divider() // Horizontal line
    Text("Bottom")
}

HStack {
    Text("Left")
    Spacer() // Pushes content apart
    Text("Right")
}
```

---

## Layout Containers

### Quick Stack Comparison

**Choose the right stack for your layout:**

| Stack Type | Direction | Use When | Visual |
|------------|-----------|----------|--------|
| **VStack** | Vertical ↓ | Items stacked top to bottom | Column of items |
| **HStack** | Horizontal → | Items placed left to right | Row of items |
| **ZStack** | Layered (depth) | Items overlapping | Layers on top of each other |

**Quick decision:**
- Need items **one below the other**? → VStack
- Need items **side by side**? → HStack  
- Need items **on top of each other**? → ZStack

---

### VStack (Vertical Stack)

**When to use:** When you want to arrange items in a **vertical column** (top to bottom).

**Where to use:**
- Forms and input fields stacked vertically
- Lists of information
- Buttons arranged vertically
- Any UI that flows from top to bottom

**Key concept:** Think of VStack as a vertical column. Items are placed one below the other.

```swift
// ✅ USE VStack FOR:

// Basic vertical arrangement
VStack {
    Text("First")      // Top
    Text("Second")     // Middle
    Text("Third")      // Bottom
}

// With alignment and spacing
VStack(alignment: .leading, spacing: 20) {
    Text("Aligned left")
    Text("With spacing")
    Text("Between items")
}

// Common use case: Form-like layout
VStack(spacing: 15) {
    Text("Enter your details")
        .font(.headline)
    TextField("Name", text: $name)
    TextField("Email", text: $email)
    Button("Submit") { }
}
```

**Visual representation:**
```
┌─────────────┐
│   Item 1    │  ← Top
├─────────────┤
│   Item 2    │  ← Middle
├─────────────┤
│   Item 3    │  ← Bottom
└─────────────┘
```

**Simple rule:** 
- Use VStack when items should be stacked vertically
- `alignment:` controls horizontal alignment (.leading = left, .trailing = right, .center = center)
- `spacing:` controls gap between items

### HStack (Horizontal Stack)

**When to use:** When you want to arrange items in a **horizontal row** (left to right).

**Where to use:**
- Buttons side by side
- Icon + Text combinations
- Labels with values (e.g., "Weight: 150 lbs")
- Navigation bars
- Any UI that flows from left to right

**Key concept:** Think of HStack as a horizontal row. Items are placed one next to the other.

```swift
// ✅ USE HStack FOR:

// Basic horizontal arrangement
HStack {
    Text("Left")      // Left side
    Text("Center")    // Middle
    Text("Right")     // Right side
}

// With alignment and spacing
HStack(alignment: .top, spacing: 10) {
    Image(systemName: "star")
    Text("Rating")
}

// Common use case: Label + Value
HStack {
    Text("Temperature")
    Spacer()                    // Pushes items apart
    Text("72°F")
        .bold()
}

// Common use case: Buttons in a row
HStack(spacing: 20) {
    Button("Cancel") { }
    Button("Save") { }
    Button("Submit") { }
}
```

**Visual representation:**
```
┌──────┬──────┬──────┐
│Item 1│Item 2│Item 3│
│(Left)│(Mid) │(Right)│
└──────┴──────┴──────┘
```

**Simple rule:** 
- Use HStack when items should be side by side horizontally
- `alignment:` controls vertical alignment (.top, .bottom, .center)
- `spacing:` controls gap between items
- Add `Spacer()` to push items apart

### ZStack (Layered Stack)

**When to use:** When you want to **overlay** items on top of each other (like layers in Photoshop).

**Where to use:**
- Background images with text on top
- Badges on icons
- Overlays and watermarks
- Centered text on colored backgrounds
- Any UI with layered elements

**Key concept:** Think of ZStack as layers stacked front-to-back. First item is the back layer, last item is the front layer.

```swift
// ✅ USE ZStack FOR:

// Basic layering
ZStack {
    Color.blue           // Background (back layer)
    Text("Foreground")   // Text on top (front layer)
        .foregroundColor(.white)
}

// With alignment
ZStack(alignment: .topLeading) {
    Rectangle()
        .fill(Color.gray)
        .frame(width: 200, height: 200)
    Text("Top Left")     // Positioned at top-left corner
        .padding()
}

// Common use case: Background + Content
ZStack {
    Image("background")
        .resizable()
        .scaledToFill()
    
    VStack {
        Text("Welcome")
            .font(.largeTitle)
        Button("Get Started") { }
    }
}

// Common use case: Badge on icon
ZStack(alignment: .topTrailing) {
    Image(systemName: "bell")
        .font(.system(size: 40))
    
    Circle()
        .fill(Color.red)
        .frame(width: 20, height: 20)
        .overlay(Text("3").foregroundColor(.white))
}
```

**Visual representation (side view):**
```
     ┌─────────────┐
     │  Item 3     │  ← Front (visible on top)
   ┌─│─────────────│─┐
   │ │  Item 2     │ │  ← Middle
 ┌─│─│─────────────│─│─┐
 │ │ │  Item 1     │ │ │  ← Back (behind everything)
 └─│─│─────────────│─│─┘
   └─│─────────────│─┘
     └─────────────┘
```

**Simple rule:** 
- Use ZStack when items should overlap
- First item = bottom layer (background)
- Last item = top layer (foreground)
- `alignment:` controls where items align relative to each other

### Form

**When to use:** When you need a **grouped list** with sections, perfect for settings or input forms.

**Where to use:**
- Settings screens
- Data entry forms
- Surveys or questionnaires
- Any grouped input interface
- Profile editing screens

**Key concept:** Form automatically styles your content to look like iOS Settings app. It groups items into sections with headers.

```swift
// ✅ USE Form FOR:

Form {
    Section(header: Text("Personal Info")) {
        TextField("Name", text: $name)
        TextField("Email", text: $email)
    }
    
    Section(header: Text("Preferences")) {
        Toggle("Notifications", isOn: $notifications)
        Picker("Theme", selection: $theme) {
            Text("Light").tag("light")
            Text("Dark").tag("dark")
        }
    }
    
    Section {
        Button("Save") { }
    }
}

// Common use case: Settings-style interface
Form {
    Section(header: Text("Account")) {
        Text("Username: john_doe")
        Text("Email: john@example.com")
    }
    
    Section(header: Text("App Settings")) {
        Toggle("Dark Mode", isOn: $darkMode)
        Toggle("Notifications", isOn: $notifications)
        Stepper("Font Size: \(fontSize)", value: $fontSize, in: 12...24)
    }
}
```

**Visual representation:**
```
╔═══════════════════════════╗
║ SECTION HEADER            ║
╠═══════════════════════════╣
║ Input field 1             ║
║ Input field 2             ║
╠═══════════════════════════╣
║ SECTION HEADER 2          ║
╠═══════════════════════════╣
║ Toggle switch             ║
║ Picker                    ║
╚═══════════════════════════╝
```

**Simple rule:** 
- Use Form when you need iOS-styled grouped sections
- Great for any kind of input or settings
- Automatically handles scrolling
- Each `Section` creates a visual group
- Looks professional with minimal effort

### List
```swift
// Simple list
List {
    Text("Item 1")
    Text("Item 2")
    Text("Item 3")
}

// List with array
List(items, id: \.self) { item in
    Text(item)
}

// List with sections
List {
    Section(header: Text("Group 1")) {
        Text("A")
        Text("B")
    }
    Section(header: Text("Group 2")) {
        Text("C")
        Text("D")
    }
}
```

### ScrollView
```swift
ScrollView {
    VStack(spacing: 20) {
        ForEach(0..<50) { index in
            Text("Item \(index)")
        }
    }
}

ScrollView(.horizontal) {
    HStack {
        ForEach(0..<10) { _ in
            Rectangle()
                .frame(width: 100, height: 100)
        }
    }
}
```

### NavigationView

**When to use:** When you need a **title bar at the top** or want to **navigate between screens**.

**Where to use:**
- Any screen that needs a title
- Apps with multiple screens
- Settings screens
- Apps with back navigation
- List views that drill into details

**Key concept:** NavigationView wraps your content and adds a title bar. It enables push/pop navigation between screens.

```swift
// ✅ USE NavigationView FOR:

// Basic navigation with title
NavigationView {
    VStack {
        Text("Your content here")
    }
    .navigationTitle("Screen Title")
}

// Large title style (big text that shrinks on scroll)
NavigationView {
    Form {
        // Your form content
    }
    .navigationTitle("Settings")
    .navigationBarTitleDisplayMode(.large)
}

// Inline title style (always small)
NavigationView {
    List {
        // Your list items
    }
    .navigationTitle("Items")
    .navigationBarTitleDisplayMode(.inline)
}

// With navigation to another screen
NavigationView {
    VStack {
        NavigationLink("Go to Details", destination: DetailView())
        NavigationLink("Go to Settings", destination: SettingsView())
    }
    .navigationTitle("Home")
}

// Common use case: List with drill-down
NavigationView {
    List(items, id: \.self) { item in
        NavigationLink(destination: DetailView(item: item)) {
            Text(item.name)
        }
    }
    .navigationTitle("Items")
}
```

**Visual representation:**
```
╔══════════════════════════╗
║  ← Large Title          ║  ← Navigation bar
╠══════════════════════════╣
║                          ║
║    Your Content Here     ║
║                          ║
║                          ║
╚══════════════════════════╝
```

**Title Display Modes:**
```swift
.navigationBarTitleDisplayMode(.large)    // Big title that shrinks
.navigationBarTitleDisplayMode(.inline)   // Always small/compact
.navigationBarTitleDisplayMode(.automatic) // System decides
```

**Simple rule:** 
- Wrap your main content in NavigationView ONCE at the top level
- Use `.navigationTitle()` on the content inside
- NavigationView should be the outermost container
- Don't nest NavigationViews inside each other
- Use NavigationLink to go to other screens

---

## Modifiers

### Styling Modifiers
```swift
Text("Hello")
    .font(.title)              // .title, .headline, .body, .caption
    .font(.system(size: 24))   // Custom size
    .fontWeight(.bold)         // .thin, .light, .regular, .medium, .semibold, .bold, .heavy
    .foregroundColor(.blue)    // Text color
    .background(Color.yellow)  // Background color
    .cornerRadius(10)          // Rounded corners
    .padding()                 // Add padding
    .padding(.horizontal, 20)  // Specific padding
    .border(Color.red, width: 2) // Border
    .shadow(radius: 5)         // Drop shadow
    .opacity(0.5)              // Transparency
```

### Frame & Alignment
```swift
Text("Fixed Size")
    .frame(width: 200, height: 100)

Text("Min/Max")
    .frame(minWidth: 100, maxWidth: 300, minHeight: 50, maxHeight: 200)

Text("Aligned")
    .frame(maxWidth: .infinity, alignment: .leading)

Text("Fill Width")
    .frame(maxWidth: .infinity)
```

### Layout Modifiers
```swift
VStack {
    Text("Item")
}
.padding()
.background(Color.gray)
.cornerRadius(10)
.shadow(radius: 5)

// Order matters!
Text("Example")
    .padding()           // Applied first
    .background(Color.blue) // Applies to padded area
```

---

## User Input

### TextField

**When to use:** When you need the user to **type text input** (single line).

**Where to use:**
- Name, email, username inputs
- Search bars
- Number inputs (with keyboard type)
- Any single-line text entry
- Form fields

**Key concept:** TextField creates a text box where users can type. Use `$` binding so the text updates your @State variable.

```swift
// ✅ USE TextField FOR:

@State private var username: String = ""
@State private var email: String = ""
@State private var age: String = ""

// Basic text field
TextField("Placeholder", text: $username)

// Email with special keyboard
TextField("Email", text: $email)
    .keyboardType(.emailAddress)      // Shows @ and .com keys
    .autocapitalization(.none)        // Don't auto-capitalize

// Number input
TextField("Age", text: $age)
    .keyboardType(.numberPad)         // Shows only numbers

// With custom styling
TextField("Username", text: $username)
    .textFieldStyle(.roundedBorder)   // Rounded border style
    .padding()

// Common use case: Form input
VStack {
    TextField("First Name", text: $firstName)
    TextField("Last Name", text: $lastName)
    TextField("Phone", text: $phone)
        .keyboardType(.phonePad)
}
```

**Keyboard Types:**
```swift
.keyboardType(.default)          // Standard keyboard
.keyboardType(.numberPad)        // Numbers only (0-9)
.keyboardType(.decimalPad)       // Numbers + decimal point
.keyboardType(.phonePad)         // Phone number layout
.keyboardType(.emailAddress)     // @ and .com shortcuts
.keyboardType(.URL)              // .com, / shortcuts
```

**Simple rule:** 
- Always use `$variable` for the text binding
- Choose appropriate `.keyboardType()` for better UX
- Placeholder text shows when field is empty
- Use `.autocapitalization(.none)` for emails/usernames

### SecureField
```swift
@State private var password: String = ""

SecureField("Password", text: $password)
```

### Toggle

**When to use:** When you need an **on/off switch** (true/false choice).

**Where to use:**
- Settings that enable/disable features
- Boolean preferences (yes/no, on/off)
- Feature flags
- Checkboxes (iOS style)

**Key concept:** Toggle is like a light switch - it's either on (true) or off (false). Changes automatically update your @State variable.

```swift
// ✅ USE Toggle FOR:

@State private var isEnabled: Bool = false
@State private var notifications: Bool = true
@State private var darkMode: Bool = false

// Basic toggle
Toggle("Enable Feature", isOn: $isEnabled)

// Custom label
Toggle(isOn: $notifications) {
    Text("Enable Notifications")
        .font(.headline)
}

// Common use case: Settings screen
Form {
    Section(header: Text("Preferences")) {
        Toggle("Dark Mode", isOn: $darkMode)
        Toggle("Notifications", isOn: $notifications)
        Toggle("Auto-Save", isOn: $autoSave)
    }
}

// Using toggle value in logic
Toggle("Use Metric System", isOn: $useMetric)

// Then use it to change behavior:
Text(useMetric ? "kg" : "lbs")
```

**Visual representation:**
```
┌─────────────────────────┬────┐
│ Label Text              │ ○─ │  OFF (false)
└─────────────────────────┴────┘

┌─────────────────────────┬────┐
│ Label Text              │ ─● │  ON (true)
└─────────────────────────┴────┘
```

**Simple rule:** 
- Always use `@State private var something: Bool` 
- Bind with `isOn: $variable`
- Perfect for yes/no, on/off, enable/disable choices
- Automatically looks like iOS Settings toggles

### Picker

**When to use:** When you need the user to **choose one option** from a list of choices.

**Where to use:**
- Dropdowns (menu style)
- Segmented controls (buttons style)
- Selection wheels
- Category/option selection
- Unit selection (Celsius/Fahrenheit, kg/lbs)

**Key concept:** Picker lets users select one option from multiple choices. The selected value updates your @State variable.

```swift
// ✅ USE Picker FOR:

@State private var selectedFruit: String = "Apple"
@State private var temperature: String = "Celsius"
let fruits = ["Apple", "Banana", "Orange", "Grape"]

// SEGMENTED STYLE (looks like buttons)
// Use when: 2-4 options that fit on screen
Picker("Fruit", selection: $selectedFruit) {
    ForEach(fruits, id: \.self) { fruit in
        Text(fruit).tag(fruit)
    }
}
.pickerStyle(.segmented)

// MENU STYLE (dropdown)
// Use when: 5+ options or space is limited
Picker("Choose Fruit", selection: $selectedFruit) {
    ForEach(fruits, id: \.self) { fruit in
        Text(fruit)
    }
}
.pickerStyle(.menu)

// WHEEL STYLE (spinner)
// Use when: Scrolling through options
Picker("Select", selection: $selectedFruit) {
    ForEach(fruits, id: \.self) { fruit in
        Text(fruit)
    }
}
.pickerStyle(.wheel)

// Common use case: Unit conversion
Picker("Temperature Unit", selection: $temperatureUnit) {
    Text("Celsius").tag("C")
    Text("Fahrenheit").tag("F")
    Text("Kelvin").tag("K")
}
.pickerStyle(.segmented)

// With enum (better practice)
enum Unit: String, CaseIterable {
    case celsius = "Celsius"
    case fahrenheit = "Fahrenheit"
}

@State private var unit: Unit = .celsius

Picker("Unit", selection: $unit) {
    ForEach(Unit.allCases, id: \.self) { u in
        Text(u.rawValue).tag(u)
    }
}
.pickerStyle(.segmented)
```

**Visual representations:**

**Segmented (.segmented):**
```
┌────────┬────────┬────────┐
│ Apple  │ Banana │ Orange │  ← Buttons to tap
└────────┴────────┴────────┘
   ●         ○        ○
```

**Menu (.menu):**
```
┌─────────────────┬──┐
│ Apple           │ ▼│  ← Tap to open dropdown
└─────────────────┴──┘
```

**Wheel (.wheel):**
```
    Orange
  ═══════════
    Apple    ← Selected
  ═══════════
    Banana
```

**Simple rule:** 
- Use `.segmented` for 2-4 options (looks like toggle buttons)
- Use `.menu` for 5+ options (dropdown list)
- Use `.wheel` when scrolling through many options
- Always use `.tag()` to identify each option
- Bind with `selection: $variable`

### Slider
```swift
@State private var volume: Double = 50

Slider(value: $volume, in: 0...100)
Slider(value: $volume, in: 0...100, step: 5)

Text("Volume: \(Int(volume))")
```

### Stepper
```swift
@State private var quantity: Int = 1

Stepper("Quantity: \(quantity)", value: $quantity, in: 1...10)
Stepper(value: $quantity, in: 1...100, step: 5) {
    Text("Count: \(quantity)")
}
```

---

## Control Flow

### If Statements
```swift
var body: some View {
    VStack {
        if isLoggedIn {
            Text("Welcome!")
        } else {
            Text("Please log in")
        }
    }
}
```

### If-Let (Optional Binding)
```swift
var body: some View {
    VStack {
        if let username = currentUser {
            Text("Hello, \(username)")
        } else {
            Text("Guest")
        }
    }
}
```

### Switch Statements
```swift
var body: some View {
    VStack {
        switch userType {
        case .admin:
            Text("Admin Dashboard")
        case .user:
            Text("User Dashboard")
        case .guest:
            Text("Guest Access")
        }
    }
}
```

### ForEach Loops

**When to use:** When you need to **repeat the same UI** for multiple items (like a loop).

**Where to use:**
- Displaying arrays of data
- Creating multiple similar items
- Dynamic lists
- Repeating patterns
- Grid items

**Key concept:** ForEach creates copies of views for each item in a collection. Like a for-loop, but for UI.

```swift
// ✅ USE ForEach FOR:

// With a number range (0 to 4 = 5 items)
VStack {
    ForEach(0..<5) { index in
        Text("Item \(index)")
    }
}
// Creates: Item 0, Item 1, Item 2, Item 3, Item 4

// With an array
let fruits = ["Apple", "Banana", "Orange"]
VStack {
    ForEach(fruits, id: \.self) { fruit in
        Text(fruit)
    }
}
// Creates: Apple, Banana, Orange

// With index and value (enumerated)
ForEach(Array(fruits.enumerated()), id: \.offset) { index, fruit in
    Text("\(index + 1). \(fruit)")
}
// Creates: 1. Apple, 2. Banana, 3. Orange

// Common use case: Button grid (calculator)
VStack {
    ForEach(0..<3) { row in
        HStack {
            ForEach(0..<3) { col in
                Button("\(row * 3 + col + 1)") {
                    // Button action
                }
            }
        }
    }
}
// Creates a 3x3 grid of buttons numbered 1-9

// Common use case: Dynamic list
@State private var items = ["Task 1", "Task 2", "Task 3"]

List {
    ForEach(items, id: \.self) { item in
        Text(item)
    }
}

// With enum
enum Temperature: String, CaseIterable {
    case celsius, fahrenheit, kelvin
}

ForEach(Temperature.allCases, id: \.self) { temp in
    Text(temp.rawValue)
}
```

**The `id: \.self` explained:**
- `id:` tells SwiftUI how to identify each item uniquely
- `\.self` means "use the item itself as the ID"
- Required when looping over arrays

**Simple rule:** 
- Use `ForEach(0..<n)` for number ranges (0, 1, 2, ... n-1)
- Use `ForEach(array, id: \.self)` for arrays
- The variable after `in` is your loop variable (like `i` in a for-loop)
- Creates multiple copies of whatever is inside the `{ }`
- Great for avoiding repetitive code

---

## Data Types & Structures

### Enums
```swift
enum Temperature: String, CaseIterable {
    case celsius = "Celsius"
    case fahrenheit = "Fahrenheit"
    case kelvin = "Kelvin"
}

@State private var selectedTemp: Temperature = .celsius

Picker("Unit", selection: $selectedTemp) {
    ForEach(Temperature.allCases, id: \.self) { unit in
        Text(unit.rawValue).tag(unit)
    }
}
```

### Structs
```swift
struct Person {
    var name: String
    var age: Int
    
    func greet() -> String {
        return "Hello, I'm \(name)"
    }
}

// Usage
let person = Person(name: "Kenan", age: 25)
Text(person.greet())
```

### Arrays
```swift
@State private var numbers: [Int] = [1, 2, 3, 4, 5]
@State private var names: [String] = []

// Operations
numbers.append(6)
numbers.remove(at: 0)
numbers.count
numbers.isEmpty
numbers.first
numbers.last
```

### Dictionaries
```swift
let scores: [String: Int] = [
    "Alice": 95,
    "Bob": 87,
    "Charlie": 92
]

// Access
if let score = scores["Alice"] {
    Text("Score: \(score)")
}
```

---

## Common Patterns

### Optional Handling
```swift
// Guard statement
func calculate() -> Double {
    guard let value = Double(inputText), value > 0 else {
        return 0.0
    }
    return value * 2
}

// Nil coalescing
let result = Double(inputText) ?? 0.0

// Optional binding
if let number = Double(inputText) {
    // Use number
}
```

### String Formatting
```swift
let pi = 3.14159

Text("\(pi)")                           // "3.14159"
Text(String(format: "%.2f", pi))        // "3.14"
Text("\(Int(pi))")                      // "3"

// Check if integer
if number.truncatingRemainder(dividingBy: 1) == 0 {
    return String(format: "%.0f", number)
}
```

### Custom View Components
```swift
// Reusable component
struct CustomButton: View {
    let title: String
    let action: () -> Void
    
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(.headline)
                .foregroundColor(.white)
                .padding()
                .frame(maxWidth: .infinity)
                .background(Color.blue)
                .cornerRadius(10)
        }
    }
}

// Usage
CustomButton(title: "Save") {
    print("Saved")
}
```

### Conditional Modifiers
```swift
Text("Hello")
    .foregroundColor(isActive ? .green : .gray)
    .font(isLarge ? .title : .body)

// Multiple conditions
Text("Status")
    .foregroundColor(
        status == .success ? .green :
        status == .warning ? .orange :
        .red
    )
```

### Environment Values
```swift
@Environment(\.colorScheme) var colorScheme

var body: some View {
    Text("Hello")
        .foregroundColor(colorScheme == .dark ? .white : .black)
}
```

---

## Complete Examples

### Example 1: Temperature Converter

```swift
import SwiftUI

struct ContentView: View {
    // MARK: - State Properties
    @State private var inputTemperature: String = ""
    @State private var inputUnit: TemperatureUnit = .celsius
    @State private var outputUnit: TemperatureUnit = .fahrenheit
    
    // MARK: - Computed Properties
    var convertedTemperature: Double {
        guard let temp = Double(inputTemperature) else { return 0.0 }
        
        // Convert to Celsius first
        let celsius: Double
        switch inputUnit {
        case .celsius:
            celsius = temp
        case .fahrenheit:
            celsius = (temp - 32) * 5/9
        case .kelvin:
            celsius = temp - 273.15
        }
        
        // Convert from Celsius to output unit
        switch outputUnit {
        case .celsius:
            return celsius
        case .fahrenheit:
            return celsius * 9/5 + 32
        case .kelvin:
            return celsius + 273.15
        }
    }
    
    // MARK: - Body
    var body: some View {
        NavigationView {
            Form {
                // Input Section
                Section(header: Text("Input")) {
                    TextField("Temperature", text: $inputTemperature)
                        .keyboardType(.decimalPad)
                    
                    Picker("From", selection: $inputUnit) {
                        ForEach(TemperatureUnit.allCases, id: \.self) { unit in
                            Text(unit.rawValue).tag(unit)
                        }
                    }
                    .pickerStyle(.segmented)
                }
                
                // Output Section
                Section(header: Text("Output")) {
                    Picker("To", selection: $outputUnit) {
                        ForEach(TemperatureUnit.allCases, id: \.self) { unit in
                            Text(unit.rawValue).tag(unit)
                        }
                    }
                    .pickerStyle(.segmented)
                    
                    Text("\(convertedTemperature, specifier: "%.2f")° \(outputUnit.rawValue)")
                        .font(.title2)
                        .bold()
                }
            }
            .navigationTitle("Temperature Converter")
        }
    }
}

// MARK: - Temperature Unit Enum
enum TemperatureUnit: String, CaseIterable {
    case celsius = "Celsius"
    case fahrenheit = "Fahrenheit"
    case kelvin = "Kelvin"
}

// MARK: - Preview
struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

### Example 2: BMI Calculator

```swift
import SwiftUI

struct ContentView: View {
    // MARK: - State Properties
    @State private var weight: String = ""
    @State private var height: String = ""
    @State private var useMetric: Bool = true
    
    // MARK: - Computed Properties
    var bmi: Double {
        guard let w = Double(weight), let h = Double(height), h > 0 else {
            return 0.0
        }
        
        if useMetric {
            // BMI = weight(kg) / (height(m))^2
            return w / ((h / 100) * (h / 100))
        } else {
            // BMI = (weight(lbs) / (height(in))^2) * 703
            return (w / (h * h)) * 703
        }
    }
    
    var bmiCategory: String {
        switch bmi {
        case 0..<18.5:
            return "Underweight"
        case 18.5..<25:
            return "Normal"
        case 25..<30:
            return "Overweight"
        default:
            return "Obese"
        }
    }
    
    var categoryColor: Color {
        switch bmi {
        case 0..<18.5:
            return .blue
        case 18.5..<25:
            return .green
        case 25..<30:
            return .orange
        default:
            return .red
        }
    }
    
    // MARK: - Body
    var body: some View {
        NavigationView {
            Form {
                // Unit Toggle
                Section {
                    Toggle("Use Metric System", isOn: $useMetric)
                }
                
                // Input Section
                Section(header: Text("Measurements")) {
                    HStack {
                        Text("Weight")
                        Spacer()
                        TextField("Weight", text: $weight)
                            .keyboardType(.decimalPad)
                            .multilineTextAlignment(.trailing)
                            .frame(width: 100)
                        Text(useMetric ? "kg" : "lbs")
                            .foregroundColor(.gray)
                    }
                    
                    HStack {
                        Text("Height")
                        Spacer()
                        TextField("Height", text: $height)
                            .keyboardType(.decimalPad)
                            .multilineTextAlignment(.trailing)
                            .frame(width: 100)
                        Text(useMetric ? "cm" : "in")
                            .foregroundColor(.gray)
                    }
                }
                
                // Result Section
                if bmi > 0 {
                    Section(header: Text("Results")) {
                        HStack {
                            Text("BMI")
                            Spacer()
                            Text(String(format: "%.1f", bmi))
                                .font(.title2)
                                .bold()
                                .foregroundColor(categoryColor)
                        }
                        
                        HStack {
                            Text("Category")
                            Spacer()
                            Text(bmiCategory)
                                .font(.headline)
                                .foregroundColor(categoryColor)
                        }
                    }
                }
            }
            .navigationTitle("BMI Calculator")
        }
    }
}

// MARK: - Preview
struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

### Example 3: Simple Calculator

```swift
import SwiftUI

struct ContentView: View {
    // MARK: - State Properties
    @State private var display: String = "0"
    @State private var currentNumber: Double = 0
    @State private var previousNumber: Double = 0
    @State private var operation: Operation? = nil
    @State private var userIsTyping: Bool = false
    
    // MARK: - Button Grid
    let buttons: [[CalculatorButton]] = [
        [.clear, .negative, .percent, .divide],
        [.seven, .eight, .nine, .multiply],
        [.four, .five, .six, .subtract],
        [.one, .two, .three, .add],
        [.zero, .decimal, .equals]
    ]
    
    // MARK: - Body
    var body: some View {
        ZStack {
            Color.black.ignoresSafeArea()
            
            VStack(spacing: 12) {
                // Display
                HStack {
                    Spacer()
                    Text(display)
                        .font(.system(size: 64, weight: .light))
                        .foregroundColor(.white)
                        .lineLimit(1)
                        .minimumScaleFactor(0.5)
                        .padding(.horizontal, 20)
                }
                .frame(maxWidth: .infinity, alignment: .trailing)
                .padding(.bottom, 20)
                
                // Button Grid
                ForEach(buttons, id: \.self) { row in
                    HStack(spacing: 12) {
                        ForEach(row, id: \.self) { button in
                            CalculatorButtonView(button: button) {
                                self.buttonTapped(button)
                            }
                        }
                    }
                }
            }
            .padding()
        }
    }
    
    // MARK: - Button Actions
    func buttonTapped(_ button: CalculatorButton) {
        switch button {
        case .clear:
            display = "0"
            currentNumber = 0
            previousNumber = 0
            operation = nil
            userIsTyping = false
            
        case .negative:
            currentNumber = -currentNumber
            display = formatNumber(currentNumber)
            
        case .percent:
            currentNumber = currentNumber / 100
            display = formatNumber(currentNumber)
            
        case .decimal:
            if !display.contains(".") {
                display += "."
                userIsTyping = true
            }
            
        case .equals:
            if let op = operation {
                currentNumber = performOperation(op, previousNumber, currentNumber)
                display = formatNumber(currentNumber)
                operation = nil
                userIsTyping = false
            }
            
        case .add, .subtract, .multiply, .divide:
            if let op = operation {
                currentNumber = performOperation(op, previousNumber, currentNumber)
                display = formatNumber(currentNumber)
            }
            previousNumber = currentNumber
            operation = Operation(from: button)
            userIsTyping = false
            
        default: // Number buttons
            if userIsTyping {
                display += button.title
            } else {
                display = button.title
                userIsTyping = true
            }
            currentNumber = Double(display) ?? 0
        }
    }
    
    // MARK: - Helper Functions
    func performOperation(_ op: Operation, _ a: Double, _ b: Double) -> Double {
        switch op {
        case .add:
            return a + b
        case .subtract:
            return a - b
        case .multiply:
            return a * b
        case .divide:
            return b != 0 ? a / b : 0
        }
    }
    
    func formatNumber(_ number: Double) -> String {
        if number.truncatingRemainder(dividingBy: 1) == 0 {
            return String(format: "%.0f", number)
        }
        return String(number)
    }
}

// MARK: - Calculator Button Enum
enum CalculatorButton: String {
    case zero = "0", one = "1", two = "2", three = "3", four = "4"
    case five = "5", six = "6", seven = "7", eight = "8", nine = "9"
    case add = "+", subtract = "-", multiply = "×", divide = "÷"
    case equals = "=", clear = "AC", negative = "+/-", percent = "%", decimal = "."
    
    var title: String { self.rawValue }
    
    var backgroundColor: Color {
        switch self {
        case .add, .subtract, .multiply, .divide, .equals:
            return .orange
        case .clear, .negative, .percent:
            return Color(.lightGray)
        default:
            return Color(white: 0.2)
        }
    }
}

// MARK: - Operation Enum
enum Operation {
    case add, subtract, multiply, divide
    
    init?(from button: CalculatorButton) {
        switch button {
        case .add: self = .add
        case .subtract: self = .subtract
        case .multiply: self = .multiply
        case .divide: self = .divide
        default: return nil
        }
    }
}

// MARK: - Calculator Button View Component
struct CalculatorButtonView: View {
    let button: CalculatorButton
    let action: () -> Void
    
    var body: some View {
        Button(action: action) {
            Text(button.title)
                .font(.system(size: 32, weight: .medium))
                .foregroundColor(.white)
                .frame(width: buttonWidth(), height: 72)
                .background(button.backgroundColor)
                .cornerRadius(36)
        }
    }
    
    func buttonWidth() -> CGFloat {
        let screenWidth = UIScreen.main.bounds.width
        let buttonSpacing: CGFloat = 12
        let totalSpacing: CGFloat = buttonSpacing * 5
        
        if button == .zero {
            return ((screenWidth - totalSpacing) / 4) * 2 + buttonSpacing
        }
        return (screenWidth - totalSpacing) / 4
    }
}

// MARK: - Preview
struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

### Example 4: Counter App

```swift
import SwiftUI

struct CounterView: View {
    @State private var count: Int = 0
    
    var body: some View {
        VStack(spacing: 20) {
            Text("\(count)")
                .font(.system(size: 72))
                .bold()
            
            HStack(spacing: 20) {
                Button("-") { count -= 1 }
                    .font(.title)
                    .frame(width: 60, height: 60)
                    .background(Color.red)
                    .foregroundColor(.white)
                    .cornerRadius(30)
                
                Button("Reset") { count = 0 }
                    .font(.headline)
                    .padding()
                    .background(Color.gray)
                    .foregroundColor(.white)
                    .cornerRadius(10)
                
                Button("+") { count += 1 }
                    .font(.title)
                    .frame(width: 60, height: 60)
                    .background(Color.green)
                    .foregroundColor(.white)
                    .cornerRadius(30)
            }
        }
        .padding()
    }
}
```

### Example 5: Simple Quiz App

```swift
import SwiftUI

struct QuizView: View {
    @State private var score: Int = 0
    @State private var currentQuestion: Int = 0
    @State private var selectedAnswer: String = ""
    
    let questions = [
        ("What is 2 + 2?", ["3", "4", "5"], "4"),
        ("Capital of France?", ["London", "Paris", "Berlin"], "Paris")
    ]
    
    var body: some View {
        VStack(spacing: 30) {
            if currentQuestion < questions.count {
                Text(questions[currentQuestion].0)
                    .font(.title2)
                
                ForEach(questions[currentQuestion].1, id: \.self) { answer in
                    Button(answer) {
                        if answer == questions[currentQuestion].2 {
                            score += 1
                        }
                        currentQuestion += 1
                    }
                    .padding()
                    .frame(maxWidth: .infinity)
                    .background(Color.blue)
                    .foregroundColor(.white)
                    .cornerRadius(10)
                }
            } else {
                Text("Quiz Complete!")
                    .font(.title)
                Text("Score: \(score)/\(questions.count)")
                    .font(.headline)
            }
        }
        .padding()
    }
}
```

---

## Quick Reference Cheat Sheet

### State & Binding
```swift
@State private var value: Type = initial  // Local state
$variableName                              // Binding (two-way)
```

### Text & Display
```swift
Text("\(variable)")                        // String interpolation
Text(String(format: "%.2f", number))      // Formatted number
```

### User Input
```swift
TextField("Hint", text: $binding)
Toggle("Label", isOn: $binding)
Picker("", selection: $binding) {
    Text("Option").tag(value)
}
Slider(value: $binding, in: 0...100)
Stepper("Label", value: $binding, in: 1...10)
```

### Buttons
```swift
Button("Title") { action }
Button(action: {}) {
    Text("Custom")
}
```

### Layouts
```swift
VStack { }      // Vertical
HStack { }      // Horizontal
ZStack { }      // Layered
Form { }        // Grouped list
List { }        // Scrollable list
ScrollView { }  // Scrollable content
```

### Common Modifiers
```swift
.font(.title)
.foregroundColor(.blue)
.background(Color.gray)
.padding()
.cornerRadius(10)
.frame(width: 100, height: 50)
.bold()
.italic()
```

### Control Flow
```swift
if condition { }
if let value = optional { }
ForEach(array, id: \.self) { item in }
```

### Navigation
```swift
NavigationView {
    .navigationTitle("Title")
}
NavigationLink("Next", destination: DetailView())
```

---

## Best Practices

1. **Use MARK comments** to organize code sections
2. **Computed properties** for calculations (cleaner than functions)
3. **Enums** for fixed sets of options
4. **Guard statements** for safe unwrapping optionals
5. **String interpolation** for displaying values
6. **Format numbers** using `String(format:)`
7. **Preview providers** for visualization during development
8. **Semantic property names** (descriptive, not generic)

---

## Project Setup Reminder

### Creating a New Project
1. Xcode → File → New → Project
2. iOS → App → Next
3. Configure:
   - Product Name: YourAppName
   - Interface: SwiftUI
   - Language: Swift

### File Structure
```
ProjectName/
├── ProjectNameApp.swift      // App entry point
├── ContentView.swift          // Main view
├── Views/                     // Additional views
├── Components/                // Reusable components
└── Assets.xcassets/          // Images, colors
```

---

## Additional Resources

- **SF Symbols**: Built-in icon library (use with `Image(systemName:)`)
- **Color**: `.red`, `.blue`, `.green`, `.orange`, `.purple`, `.pink`, `.gray`, `.black`, `.white`
- **Font Styles**: `.largeTitle`, `.title`, `.title2`, `.title3`, `.headline`, `.subheadline`, `.body`, `.callout`, `.caption`, `.footnote`

---

**Good luck with your SwiftUI development!** 🚀

This guide covers all essential syntax and patterns for building non-storage SwiftUI applications.
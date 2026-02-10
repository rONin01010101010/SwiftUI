# SwiftUI Reference Guide
**Complete Syntax & Structure Guide for Non-Storage Applications**

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
```swift
// For simple value types in a single view
@State private var isOn: Bool = false
@State private var count: Int = 0
@State private var text: String = ""
@State private var selectedOption: String = "Option 1"

// Example usage
Toggle("Switch", isOn: $isOn)
Text("Count: \(count)")
```

### @Binding (Shared State)
```swift
// Parent View
struct ParentView: View {
    @State private var sharedValue: String = ""
    
    var body: some View {
        ChildView(text: $sharedValue)
    }
}

// Child View
struct ChildView: View {
    @Binding var text: String
    
    var body: some View {
        TextField("Enter text", text: $text)
    }
}
```

### Computed Properties
```swift
struct CalculatorView: View {
    @State private var number1: String = ""
    @State private var number2: String = ""
    
    // Computed property (recalculates automatically)
    var sum: Double {
        let n1 = Double(number1) ?? 0
        let n2 = Double(number2) ?? 0
        return n1 + n2
    }
    
    var body: some View {
        Text("Sum: \(sum)")
    }
}
```

---

## UI Components

### Text
```swift
Text("Simple text")
Text("Bold text").bold()
Text("Italic text").italic()
Text("Custom font").font(.title)
Text("Colored text").foregroundColor(.blue)
Text("Multi\nLine\nText")
Text("\(variableName)") // String interpolation
Text(String(format: "%.2f", doubleValue)) // Formatted numbers
```

### Button
```swift
// Simple button
Button("Click Me") {
    print("Button clicked")
}

// Button with custom label
Button(action: {
    print("Action")
}) {
    Text("Custom Button")
        .padding()
        .background(Color.blue)
        .foregroundColor(.white)
        .cornerRadius(10)
}

// Button with image
Button(action: {}) {
    Label("Save", systemImage: "square.and.arrow.down")
}
```

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

### VStack (Vertical Stack)
```swift
VStack {
    Text("First")
    Text("Second")
    Text("Third")
}

VStack(alignment: .leading, spacing: 20) {
    Text("Aligned left")
    Text("With spacing")
}
```

### HStack (Horizontal Stack)
```swift
HStack {
    Text("Left")
    Text("Center")
    Text("Right")
}

HStack(alignment: .top, spacing: 10) {
    Image(systemName: "star")
    Text("Rating")
}
```

### ZStack (Layered Stack)
```swift
ZStack {
    Color.blue // Background layer
    Text("Foreground") // Front layer
        .foregroundColor(.white)
}

ZStack(alignment: .topLeading) {
    Rectangle().fill(Color.gray)
    Text("Top Left")
}
```

### Form
```swift
Form {
    Section(header: Text("Section 1")) {
        Text("Row 1")
        Text("Row 2")
    }
    
    Section(header: Text("Section 2")) {
        Toggle("Switch", isOn: $isOn)
        Picker("Choice", selection: $choice) {
            Text("A").tag("A")
            Text("B").tag("B")
        }
    }
}
```

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
```swift
NavigationView {
    VStack {
        Text("Content")
    }
    .navigationTitle("Title")
    .navigationBarTitleDisplayMode(.large) // or .inline
}

// Navigation with link
NavigationView {
    NavigationLink("Go to Detail", destination: DetailView())
}
```

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
```swift
@State private var username: String = ""

TextField("Placeholder", text: $username)
TextField("Email", text: $email)
    .keyboardType(.emailAddress)
    .autocapitalization(.none)

TextField("Number", text: $number)
    .keyboardType(.numberPad)
```

### SecureField
```swift
@State private var password: String = ""

SecureField("Password", text: $password)
```

### Toggle
```swift
@State private var isEnabled: Bool = false

Toggle("Enable Feature", isOn: $isEnabled)
Toggle(isOn: $isEnabled) {
    Text("Custom Label")
}
```

### Picker
```swift
@State private var selectedFruit: String = "Apple"
let fruits = ["Apple", "Banana", "Orange"]

// Segmented Picker
Picker("Fruit", selection: $selectedFruit) {
    ForEach(fruits, id: \.self) { fruit in
        Text(fruit).tag(fruit)
    }
}
.pickerStyle(.segmented)

// Menu Picker
Picker("Choose", selection: $selectedFruit) {
    ForEach(fruits, id: \.self) { fruit in
        Text(fruit)
    }
}
.pickerStyle(.menu)

// Wheel Picker
Picker("Select", selection: $selectedFruit) {
    ForEach(fruits, id: \.self) { fruit in
        Text(fruit)
    }
}
.pickerStyle(.wheel)
```

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
```swift
// With range
VStack {
    ForEach(0..<5) { index in
        Text("Item \(index)")
    }
}

// With array
let items = ["A", "B", "C"]
VStack {
    ForEach(items, id: \.self) { item in
        Text(item)
    }
}

// With enumerated
ForEach(Array(items.enumerated()), id: \.offset) { index, item in
    Text("\(index): \(item)")
}
```

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
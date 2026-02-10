# SwiftUI Calculation App - Complete Build Guide

**Step-by-Step Instructions for Building Calculation Apps (BMI, Temperature Converter, Calculator, etc.)**

---

## Table of Contents
1. [Project Setup](#project-setup)
2. [File Structure](#file-structure)
3. [Step-by-Step Build Process](#step-by-step-build-process)
4. [Required Components Checklist](#required-components-checklist)
5. [Common Errors & Solutions](#common-errors--solutions)
6. [Debugging Techniques](#debugging-techniques)
7. [Example Walkthrough: BMI Calculator](#example-walkthrough-bmi-calculator)
8. [Testing Checklist](#testing-checklist)

---

## Project Setup

### Step 1: Create New Project

1. **Open Xcode**
2. Click **File → New → Project** (or press `⌘⇧N`)
3. Select **iOS → App → Next**
4. Configure your project:
   ```
   Product Name:        BMICalculator (or your app name)
   Team:                None (or your team)
   Organization ID:     com.yourname
   Interface:           SwiftUI ✓ (MUST SELECT THIS)
   Language:            Swift ✓
   Storage:             None (uncheck Core Data)
   Include Tests:       Optional (can uncheck)
   ```
5. Click **Next**
6. Choose save location → **Create**

### Step 2: Verify Project Created Successfully

You should see:
- ✅ Left sidebar with project files
- ✅ `ContentView.swift` with default "Hello, world!" code
- ✅ Canvas on the right (may need to show it: Editor → Canvas)

---

## File Structure

### Default Structure (What Xcode Creates)
```
YourProjectName/
├── YourProjectNameApp.swift     # App entry point (DON'T MODIFY)
├── ContentView.swift             # Main view (YOU WORK HERE)
├── Assets.xcassets/             # Images and colors
├── Preview Content/
│   └── Preview Assets.xcassets  # Preview-only assets
└── Info.plist                   # App configuration (usually auto-managed)
```

### Where You'll Work (95% of the time)
```
✅ ContentView.swift  ← THIS IS WHERE YOU BUILD YOUR APP
```

### Advanced Structure (Optional - for larger projects)
```
YourProjectName/
├── YourProjectNameApp.swift
├── Views/
│   ├── ContentView.swift
│   ├── ResultView.swift
│   └── SettingsView.swift
├── Models/
│   └── Calculator.swift
├── Components/
│   └── CustomButton.swift
└── Assets.xcassets/
```

**For lab tests:** Keep it simple - just use `ContentView.swift`

---

## Step-by-Step Build Process

### Phase 1: Planning (5-10 minutes)

#### Step 1: Identify Required Inputs
Ask yourself: "What does the user need to enter?"

**Examples:**
- BMI Calculator: weight, height, unit system
- Temperature Converter: temperature value, from unit, to unit
- Tip Calculator: bill amount, tip percentage, number of people

#### Step 2: Identify Required Outputs
Ask yourself: "What needs to be calculated and displayed?"

**Examples:**
- BMI Calculator: BMI value, category (underweight/normal/overweight)
- Temperature Converter: converted temperature
- Tip Calculator: tip amount, total, per person amount

#### Step 3: Identify Calculation Logic
Write the formula on paper:

**Examples:**
```
BMI = weight(kg) / (height(m))²
Fahrenheit = Celsius × 9/5 + 32
Tip = Bill × (Percentage / 100)
```

#### Step 4: Sketch UI Layout
Draw on paper:
```
[  Title  ]
-----------
Input 1: [ textfield ]
Input 2: [ textfield ]
[ Calculate Button ]
-----------
Result: [display value]
Category: [display text]
```

---

### Phase 2: Setup Basic Structure (10-15 minutes)

#### Step 1: Open ContentView.swift

You'll see default code:
```swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        VStack {
            Image(systemName: "globe")
                .imageScale(.large)
                .foregroundStyle(.tint)
            Text("Hello, world!")
        }
        .padding()
    }
}

#Preview {
    ContentView()
}
```

#### Step 2: Clean Up Default Code

**DELETE** the default content and start fresh:

```swift
import SwiftUI

struct ContentView: View {
    // MARK: - Properties (State variables go here)
    
    // MARK: - Computed Properties (Calculations go here)
    
    // MARK: - Body
    var body: some View {
        NavigationView {
            // Your UI goes here
        }
    }
}

// MARK: - Preview
#Preview {
    ContentView()
}
```

#### Step 3: Add NavigationView + Title

```swift
var body: some View {
    NavigationView {
        Form {
            // Content will go here
        }
        .navigationTitle("App Name")
    }
}
```

**Why Form?** 
- Automatically styles your inputs nicely
- Groups sections
- Handles scrolling
- Looks professional with minimal effort

---

### Phase 3: Add State Variables (5 minutes)

#### Step 1: Declare All Input Variables

Based on your planning, add `@State` variables:

```swift
// MARK: - Properties
@State private var input1: String = ""
@State private var input2: String = ""
@State private var selectedOption: String = "Option1"
@State private var isEnabled: Bool = false
```

**Rules:**
- Always use `@State private var`
- String for text inputs (even numbers - convert later)
- Int/Double for numbers in calculations
- Bool for toggles/switches
- Start with sensible defaults

**Example for BMI Calculator:**
```swift
// MARK: - Properties
@State private var weight: String = ""
@State private var height: String = ""
@State private var useMetric: Bool = true
```

---

### Phase 4: Add Computed Properties (10-15 minutes)

#### Step 1: Write Calculation Logic

Create computed properties for all calculations:

```swift
// MARK: - Computed Properties
var calculatedResult: Double {
    // 1. Convert String inputs to numbers
    guard let value1 = Double(input1),
          let value2 = Double(input2),
          value2 > 0 else {  // Prevent division by zero
        return 0.0
    }
    
    // 2. Perform calculation
    return value1 / value2
}
```

**Template Structure:**
```swift
var yourCalculation: Type {
    // Step 1: Validate & convert inputs
    guard let num = Double(stringInput), num > 0 else {
        return defaultValue
    }
    
    // Step 2: Calculate
    let result = /* your formula */
    
    // Step 3: Return result
    return result
}
```

**Example for BMI:**
```swift
var bmi: Double {
    guard let w = Double(weight), 
          let h = Double(height), 
          h > 0 else {
        return 0.0
    }
    
    if useMetric {
        // kg and cm
        return w / ((h / 100) * (h / 100))
    } else {
        // lbs and inches
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
```

---

### Phase 5: Build the UI (20-30 minutes)

#### Step 1: Create Input Section

```swift
var body: some View {
    NavigationView {
        Form {
            // Input Section
            Section(header: Text("Input")) {
                TextField("Placeholder", text: $variable1)
                    .keyboardType(.decimalPad)
                
                TextField("Placeholder", text: $variable2)
                    .keyboardType(.decimalPad)
            }
        }
        .navigationTitle("Calculator")
    }
}
```

**Choose Correct Keyboard:**
```swift
.keyboardType(.numberPad)      // Integers only (0-9)
.keyboardType(.decimalPad)     // Decimals (0-9 and .)
.keyboardType(.default)        // Normal keyboard
```

#### Step 2: Add Options/Settings (if needed)

```swift
Section(header: Text("Settings")) {
    Toggle("Use Metric", isOn: $useMetric)
    
    Picker("Unit", selection: $selectedUnit) {
        Text("Option 1").tag("opt1")
        Text("Option 2").tag("opt2")
    }
    .pickerStyle(.segmented)
}
```

#### Step 3: Add Results Display

```swift
// Only show results if calculation is valid
if calculatedResult > 0 {
    Section(header: Text("Results")) {
        HStack {
            Text("Result")
            Spacer()
            Text("\(calculatedResult, specifier: "%.2f")")
                .bold()
        }
        
        HStack {
            Text("Category")
            Spacer()
            Text(category)
                .foregroundColor(.blue)
        }
    }
}
```

---

### Phase 6: Add Polish (5-10 minutes)

#### Step 1: Add Color/Styling

```swift
Text(category)
    .foregroundColor(
        category == "Normal" ? .green :
        category == "Warning" ? .orange :
        .red
    )
```

#### Step 2: Format Numbers Nicely

```swift
// 2 decimal places
Text(String(format: "%.2f", value))

// No decimals
Text(String(format: "%.0f", value))

// Conditional formatting
var displayValue: String {
    if value.truncatingRemainder(dividingBy: 1) == 0 {
        return String(format: "%.0f", value)
    }
    return String(format: "%.2f", value)
}
```

---

## Required Components Checklist

### Every Calculation App MUST Have:

- [ ] **@State variables** for all user inputs
- [ ] **Computed property** for main calculation
- [ ] **NavigationView** wrapper (for title)
- [ ] **Form** for organized layout
- [ ] **TextField(s)** for numeric input with `.keyboardType(.decimalPad)`
- [ ] **Section(s)** to group inputs and outputs
- [ ] **Guard statement** in calculation to handle invalid input
- [ ] **Result display** using Text with formatted numbers
- [ ] **Preview** at the bottom for live preview

### Optional But Recommended:

- [ ] **Toggle** for switching between units/modes
- [ ] **Picker** for selecting options
- [ ] **Color coding** for results (green/orange/red)
- [ ] **Conditional display** (only show results when valid)
- [ ] **MARK comments** to organize code

---

## Common Errors & Solutions

### Error 1: "Cannot find '$variable' in scope"

**Error Message:**
```
Cannot find '$weight' in scope
```

**Cause:** Missing `@State` declaration

**Solution:**
```swift
// ❌ WRONG
var weight: String = ""

// ✅ CORRECT
@State private var weight: String = ""
```

---

### Error 2: "Cannot convert value of type 'String' to expected argument type 'Binding<String>'"

**Error Message:**
```
Cannot convert value of type 'String' to expected argument type 'Binding<String>'
```

**Cause:** Forgot `$` in TextField binding

**Solution:**
```swift
// ❌ WRONG
TextField("Weight", text: weight)

// ✅ CORRECT
TextField("Weight", text: $weight)
```

---

### Error 3: "Initializer 'init(_:)' requires that 'String' conform to 'BinaryInteger'"

**Error Message:**
```
Initializer 'init(_:)' requires that 'String' conform to 'BinaryInteger'
```

**Cause:** Trying to use String directly in math operations

**Solution:**
```swift
// ❌ WRONG
let result = weight / height

// ✅ CORRECT
guard let w = Double(weight), let h = Double(height) else {
    return 0.0
}
let result = w / h
```

---

### Error 4: "Value of optional type 'Double?' must be unwrapped"

**Error Message:**
```
Value of optional type 'Double?' must be unwrapped to a value of type 'Double'
```

**Cause:** `Double(string)` returns optional, must be unwrapped

**Solution:**
```swift
// ❌ WRONG
let value = Double(input)
let result = value * 2

// ✅ CORRECT - Option 1: Guard
guard let value = Double(input) else {
    return 0.0
}
let result = value * 2

// ✅ CORRECT - Option 2: Nil coalescing
let value = Double(input) ?? 0.0
let result = value * 2
```

---

### Error 5: "Type '()' cannot conform to 'View'"

**Error Message:**
```
Type '()' cannot conform to 'View'
```

**Cause:** Missing `return` or incorrect body structure

**Solution:**
```swift
// ❌ WRONG
var body: some View {
    let value = calculateSomething()
    Text("Result: \(value)")
}

// ✅ CORRECT - Option 1: Direct return
var body: some View {
    Text("Result: \(calculateSomething())")
}

// ✅ CORRECT - Option 2: With calculations
var body: some View {
    VStack {
        Text("Result: \(someValue)")
    }
}
```

---

### Error 6: "Extra argument 'text' in call"

**Error Message:**
```
Extra argument 'text' in call
```

**Cause:** Wrong Text initializer syntax

**Solution:**
```swift
// ❌ WRONG
Text(text: someVariable)

// ✅ CORRECT
Text(someVariable)
Text("\(someVariable)")
```

---

### Error 7: Division by Zero

**Runtime Issue:** App crashes or shows infinity/NaN

**Cause:** Dividing by zero or empty input

**Solution:**
```swift
// ❌ WRONG
var result: Double {
    let h = Double(height) ?? 0.0
    return weight / h  // Crashes if h is 0
}

// ✅ CORRECT
var result: Double {
    guard let h = Double(height), h > 0 else {
        return 0.0
    }
    return weight / h
}
```

---

### Error 8: "Closure containing control flow statement cannot be used with result builder 'ViewBuilder'"

**Error Message:**
```
Closure containing control flow statement cannot be used with result builder 'ViewBuilder'
```

**Cause:** Using return, break, or other control flow incorrectly in body

**Solution:**
```swift
// ❌ WRONG
var body: some View {
    if someCondition {
        return Text("A")
    } else {
        return Text("B")
    }
}

// ✅ CORRECT - Option 1: Remove return
var body: some View {
    if someCondition {
        Text("A")
    } else {
        Text("B")
    }
}

// ✅ CORRECT - Option 2: Group in container
var body: some View {
    Group {
        if someCondition {
            Text("A")
        } else {
            Text("B")
        }
    }
}
```

---

### Error 9: Preview Not Updating

**Issue:** Changes not showing in preview

**Solutions:**
1. **Resume Preview:** Click ▶️ button or press `⌥⌘P`
2. **Refresh Preview:** Press `⌥⌘P` twice
3. **Clean Build:** Product → Clean Build Folder (`⇧⌘K`)
4. **Restart Canvas:** Editor → Canvas (toggle off/on)
5. **Check for errors:** Fix all red errors first

---

### Error 10: "Missing argument for parameter 'content' in call"

**Error Message:**
```
Missing argument for parameter 'content' in call
```

**Cause:** Empty container view

**Solution:**
```swift
// ❌ WRONG
VStack {
}

// ✅ CORRECT
VStack {
    Text("Something")
}
```

---

## Debugging Techniques

### Technique 1: Print Debugging

Add print statements to see values:

```swift
var bmi: Double {
    guard let w = Double(weight), let h = Double(height) else {
        print("❌ Invalid input - weight: \(weight), height: \(height)")
        return 0.0
    }
    
    let result = w / ((h/100) * (h/100))
    print("✅ BMI calculated - weight: \(w), height: \(h), result: \(result)")
    return result
}
```

**View console:** View → Debug Area → Show Debug Area (or `⌘⇧Y`)

---

### Technique 2: Step-by-Step Validation

Test each part separately:

```swift
var testConversion: Double {
    let test1 = Double("75.5")  // Should work
    print("Test 1: \(test1 ?? -1)")
    
    let test2 = Double("abc")   // Should fail
    print("Test 2: \(test2 ?? -1)")
    
    return 0.0
}
```

---

### Technique 3: Default Values for Testing

Use default values to test calculations without typing:

```swift
// During development:
@State private var weight: String = "70"      // Test value
@State private var height: String = "175"     // Test value

// Before submission:
@State private var weight: String = ""        // Empty for user
@State private var height: String = ""        // Empty for user
```

---

### Technique 4: Breakpoint Debugging

1. Click line number gutter to add red breakpoint dot
2. Run app (▶️ button)
3. When code hits breakpoint, execution pauses
4. Hover over variables to see values
5. Use debug controls at bottom to step through code

---

### Technique 5: Live Preview Interaction

1. Click ▶️ on preview to make it interactive
2. Type in text fields
3. See results update in real-time
4. Much faster than running on simulator

---

### Technique 6: Check Type Mismatches

```swift
// Add explicit types to catch errors early
var bmi: Double {  // Explicit return type
    guard let w: Double = Double(weight),  // Explicit type
          let h: Double = Double(height) else {
        return 0.0
    }
    return w / (h * h)
}
```

---

### Technique 7: Isolate Problems

Comment out sections to find the error:

```swift
var body: some View {
    NavigationView {
        Form {
            Section(header: Text("Input")) {
                TextField("Weight", text: $weight)
                // TextField("Height", text: $height)  // ← Commented out
            }
            
            // Section(header: Text("Results")) {     // ← Commented out
            //     Text("BMI: \(bmi)")
            // }
        }
        .navigationTitle("BMI")
    }
}
```

Uncomment one section at a time to find the problematic code.

---

## Example Walkthrough: BMI Calculator

### Complete Step-by-Step Build

#### Step 1: Create Project
- File → New → Project
- iOS → App
- Name: "BMICalculator"
- Interface: SwiftUI

#### Step 2: Plan on Paper
```
Inputs: weight, height, metric toggle
Calculation: BMI = weight / (height²)
Outputs: BMI value, category
```

#### Step 3: Clean ContentView.swift

```swift
import SwiftUI

struct ContentView: View {
    // MARK: - Properties
    
    // MARK: - Computed Properties
    
    // MARK: - Body
    var body: some View {
        Text("Starting...")
    }
}

#Preview {
    ContentView()
}
```

#### Step 4: Add State Variables

```swift
// MARK: - Properties
@State private var weight: String = ""
@State private var height: String = ""
@State private var useMetric: Bool = true
```

#### Step 5: Add Computed Properties

```swift
// MARK: - Computed Properties
var bmi: Double {
    guard let w = Double(weight), 
          let h = Double(height),
          h > 0 else {
        return 0.0
    }
    
    if useMetric {
        return w / ((h / 100) * (h / 100))
    } else {
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
```

#### Step 6: Build UI Structure

```swift
var body: some View {
    NavigationView {
        Form {
            // Content goes here
        }
        .navigationTitle("BMI Calculator")
    }
}
```

#### Step 7: Add Input Section

```swift
Form {
    Section {
        Toggle("Use Metric System", isOn: $useMetric)
    }
    
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
}
```

#### Step 8: Add Results Section

```swift
if bmi > 0 {
    Section(header: Text("Results")) {
        HStack {
            Text("BMI")
            Spacer()
            Text(String(format: "%.1f", bmi))
                .font(.title2)
                .bold()
        }
        
        HStack {
            Text("Category")
            Spacer()
            Text(bmiCategory)
                .foregroundColor(
                    bmiCategory == "Normal" ? .green :
                    bmiCategory == "Underweight" ? .blue :
                    bmiCategory == "Overweight" ? .orange :
                    .red
                )
        }
    }
}
```

#### Step 9: Test

1. Click ▶️ on preview
2. Enter: Weight = 70, Height = 175
3. Should show: BMI ≈ 22.9, Category = Normal
4. Toggle metric off
5. Should recalculate for imperial

#### Step 10: Debug Common Issues

```swift
// Add print for debugging
var bmi: Double {
    print("🔍 Calculating BMI - weight: '\(weight)', height: '\(height)'")
    
    guard let w = Double(weight), 
          let h = Double(height),
          h > 0 else {
        print("❌ Invalid input")
        return 0.0
    }
    
    let result = useMetric ? 
        w / ((h / 100) * (h / 100)) : 
        (w / (h * h)) * 703
    
    print("✅ BMI calculated: \(result)")
    return result
}
```

---

## Testing Checklist

### Before Submitting, Test These:

- [ ] **Empty inputs** - App doesn't crash
- [ ] **Zero values** - No division by zero errors
- [ ] **Negative numbers** - Handled gracefully
- [ ] **Very large numbers** - Display correctly
- [ ] **Decimal inputs** - Calculate correctly
- [ ] **Invalid text** - App doesn't crash (e.g., "abc")
- [ ] **All toggles/pickers** - Switch between options
- [ ] **All buttons** - Perform expected actions
- [ ] **Results update** - When inputs change
- [ ] **UI looks good** - On different screen sizes

### Test Scenarios for BMI Calculator:

```
Test 1: Valid metric
  Weight: 70 kg, Height: 175 cm
  Expected: BMI ≈ 22.9, Normal

Test 2: Valid imperial  
  Weight: 154 lbs, Height: 69 in
  Expected: BMI ≈ 22.7, Normal

Test 3: Empty input
  Weight: "", Height: 175
  Expected: No crash, BMI = 0 or hidden

Test 4: Zero height
  Weight: 70, Height: 0
  Expected: No crash, BMI = 0

Test 5: Switching units
  Enter data in metric → toggle off → verify recalculates
```

---

## Quick Reference: Build Order

**Follow this sequence every time:**

1. ✅ Create project (SwiftUI interface)
2. ✅ Plan inputs, outputs, calculations on paper
3. ✅ Add all `@State` variables
4. ✅ Write computed properties for calculations
5. ✅ Build UI: NavigationView → Form → Sections
6. ✅ Add TextFields with proper keyboard types
7. ✅ Add Toggles/Pickers for options
8. ✅ Add results display with conditional rendering
9. ✅ Test with various inputs
10. ✅ Add polish (colors, formatting)

---

## Pro Tips for Lab Tests

1. **Start simple** - Get basic calculation working first
2. **Test frequently** - Click ▶️ on preview after each step
3. **Use print()** - Debug calculation logic
4. **Default values** - Speed up testing during development
5. **Comment out** - Isolate errors by commenting sections
6. **Guard statements** - Always handle invalid input
7. **Format numbers** - Use `String(format: "%.2f", value)`
8. **Conditional display** - Only show results when valid: `if value > 0 { }`
9. **MARK comments** - Organize your code for easy navigation
10. **Save often** - Press `⌘S` frequently

---

## Final Checklist Before Submission

- [ ] No red errors in Xcode
- [ ] Preview shows correctly
- [ ] All inputs have proper keyboard types
- [ ] Calculations handle edge cases (zero, empty, negative)
- [ ] Results display with proper formatting
- [ ] App doesn't crash with any input
- [ ] Code is organized with MARK comments
- [ ] Navigation title is set
- [ ] All variables have sensible default values
- [ ] Tested on preview with various inputs

---

**You're ready to build calculation apps! Follow this guide step-by-step and you'll succeed. Good luck! 🚀**

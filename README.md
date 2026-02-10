# BMI Calculator - iOS App

A simple iOS application built with SwiftUI that calculates Body Mass Index (BMI) based on user input for weight and height.

## 📋 Requirements

### Functional Requirements

1. **Input Fields**
   - Accept two decimal inputs: weight in kilograms and height in meters (2 points)
   
2. **Calculate Button**
   - User presses the Calculate button to perform calculation (1 point)
   - Uses the formula: BMI = weight / (height × height)

3. **Result Display**
   - Results are displayed in a label starting with "BMI = " shown as float (1 point)
   
4. **Empty State**
   - Empty labels should display "–" (1 point)
   
5. **Multiple Calculations**
   - User can press Calculate button multiple times with recalculated values displayed (1 point)

6. **Reset Functionality**
   - User presses Reset button to clear all displayed values
   - Input boxes and result label show "–" or no value (1 point)

7. **About Screen**
   - Second screen displays student ID, full name, and course number (1 point + 2 points)

8. **Code Quality**
   - Clean, readable, and well-commented code (1 point)

9. **Technology Choice**
   - Can use Storyboard or SwiftUI (1 point)

### Grading Notes
- Each Swift file should include a comment with your name and student ID
- **No comment means no grade**
- **Project MUST open and compile to be graded**

---

## 🚀 Getting Started

### Prerequisites
- Xcode 14.0 or later
- iOS 15.0 or later
- Swift 5.7+

### Installation

1. Clone the repository:
```bash
git clone https://github.com/yourusername/bmi-calculator.git
```

2. Open the project in Xcode:
```bash
cd bmi-calculator
open BMICalculator.xcodeproj
```

3. Build and run the project (⌘ + R)

---

## 🏗️ Project Structure

```
BMICalculator/
├── ContentView.swift          # Main calculator screen
├── AboutView.swift            # About/Info screen
├── BMICalculatorApp.swift     # App entry point
└── Assets.xcassets/           # App assets
```

---

## 💻 Implementation Guide

### Option 1: SwiftUI Implementation

#### Main Calculator View

```swift
//
// ContentView.swift
// BMI Calculator
// Created by [Your Name] - Student ID: [Your ID]
//

import SwiftUI

struct ContentView: View {
    @State private var weight: String = ""
    @State private var height: String = ""
    @State private var bmiResult: String = "–"
    @State private var showingAbout = false
    
    var body: some View {
        NavigationView {
            VStack(spacing: 20) {
                Text("BMI Calculator")
                    .font(.largeTitle)
                    .fontWeight(.bold)
                    .padding(.top, 40)
                
                // Input Section
                VStack(alignment: .leading, spacing: 15) {
                    Text("Weight (kg)")
                        .font(.headline)
                    TextField("Enter weight", text: $weight)
                        .keyboardType(.decimalPad)
                        .textFieldStyle(RoundedBorderTextFieldStyle())
                    
                    Text("Height (m)")
                        .font(.headline)
                    TextField("Enter height", text: $height)
                        .keyboardType(.decimalPad)
                        .textFieldStyle(RoundedBorderTextFieldStyle())
                }
                .padding(.horizontal)
                
                // Result Display
                VStack(spacing: 10) {
                    Text("Result")
                        .font(.headline)
                    Text("BMI = \(bmiResult)")
                        .font(.title2)
                        .foregroundColor(.blue)
                        .padding()
                        .frame(maxWidth: .infinity)
                        .background(Color.gray.opacity(0.1))
                        .cornerRadius(10)
                }
                .padding(.horizontal)
                
                // Buttons
                HStack(spacing: 20) {
                    Button(action: calculateBMI) {
                        Text("Calculate")
                            .frame(maxWidth: .infinity)
                            .padding()
                            .background(Color.blue)
                            .foregroundColor(.white)
                            .cornerRadius(10)
                    }
                    
                    Button(action: resetFields) {
                        Text("Reset")
                            .frame(maxWidth: .infinity)
                            .padding()
                            .background(Color.red)
                            .foregroundColor(.white)
                            .cornerRadius(10)
                    }
                }
                .padding(.horizontal)
                
                Spacer()
                
                // About Button
                Button(action: { showingAbout = true }) {
                    Text("About")
                        .foregroundColor(.blue)
                }
                .padding(.bottom, 20)
            }
            .sheet(isPresented: $showingAbout) {
                AboutView()
            }
        }
    }
    
    // Calculate BMI function
    func calculateBMI() {
        guard let weightValue = Double(weight),
              let heightValue = Double(height),
              heightValue > 0 else {
            bmiResult = "–"
            return
        }
        
        let bmi = weightValue / (heightValue * heightValue)
        bmiResult = String(format: "%.2f", bmi)
    }
    
    // Reset all fields
    func resetFields() {
        weight = ""
        height = ""
        bmiResult = "–"
    }
}
```

#### About View

```swift
//
// AboutView.swift
// BMI Calculator
// Created by [Your Name] - Student ID: [Your ID]
//

import SwiftUI

struct AboutView: View {
    @Environment(\.dismiss) var dismiss
    
    var body: some View {
        NavigationView {
            VStack(spacing: 20) {
                Image(systemName: "person.circle.fill")
                    .resizable()
                    .frame(width: 100, height: 100)
                    .foregroundColor(.blue)
                    .padding(.top, 40)
                
                VStack(spacing: 10) {
                    Text("Student Information")
                        .font(.title2)
                        .fontWeight(.bold)
                    
                    Divider()
                        .padding(.horizontal)
                    
                    InfoRow(title: "Name:", value: "[Your Full Name]")
                    InfoRow(title: "Student ID:", value: "[Your Student ID]")
                    InfoRow(title: "Course:", value: "COMP3097")
                }
                .padding()
                
                Spacer()
            }
            .navigationTitle("About")
            .toolbar {
                ToolbarItem(placement: .navigationBarTrailing) {
                    Button("Done") {
                        dismiss()
                    }
                }
            }
        }
    }
}

struct InfoRow: View {
    let title: String
    let value: String
    
    var body: some View {
        HStack {
            Text(title)
                .fontWeight(.semibold)
            Spacer()
            Text(value)
                .foregroundColor(.gray)
        }
        .padding(.horizontal)
    }
}
```

---



import UIKit

class ViewController: UIViewController {
    
    
    @IBOutlet weak var weightTextField: UITextField!
    @IBOutlet weak var heightTextField: UITextField!
    @IBOutlet weak var resultLabel: UILabel!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        setupUI()
    }
    
    func setupUI() {
        resultLabel.text = "BMI = –"
        weightTextField.keyboardType = .decimalPad
        heightTextField.keyboardType = .decimalPad
    }
    
    
    @IBAction func calculateButtonTapped(_ sender: UIButton) {
        calculateBMI()
    }
    
    @IBAction func resetButtonTapped(_ sender: UIButton) {
        resetFields()
    }
    
  
    func calculateBMI() {
        guard let weightText = weightTextField.text,
              let heightText = heightTextField.text,
              let weight = Double(weightText),
              let height = Double(heightText),
              height > 0 else {
            resultLabel.text = "BMI = –"
            return
        }
        
        let bmi = weight / (height * height)
        resultLabel.text = String(format: "BMI = %.2f", bmi)
    }
    
    func resetFields() {
        weightTextField.text = ""
        heightTextField.text = ""
        resultLabel.text = "BMI = –"
    }
}
```


---
## 📚 Additional Resources

- [Apple SwiftUI Documentation](https://developer.apple.com/documentation/swiftui/)
- [UIKit Documentation](https://developer.apple.com/documentation/uikit)
- [Swift Programming Language](https://docs.swift.org/swift-book/)
- [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)


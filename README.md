# EX.NO: 05 – Simple Calculator Using Android Studio

## AIM

To develop a simple calculator application using Android Studio with Java programming language that performs basic arithmetic operations and additional calculator functions through a user-friendly graphical interface.

## SOFTWARE REQUIREMENTS

- Android Studio
- Java
- XML
- Android Emulator

## ALGORITHM

1. Start the Android Studio application.
2. Create a new project named `calculator` and select Java as the programming language.
3. Design the calculator interface using XML.
4. Create a display area to show the entered numbers and calculated results.
5. Add buttons for numerical inputs, arithmetic operators and additional calculator functions.
6. Initialize the UI components using `findViewById()`.
7. Implement click listeners for all calculator buttons.
8. Accept numerical input from the user.
9. Perform the selected arithmetic operation based on the operator.
10. Display the calculated result in the TextView.
11. Implement clear, backspace, percentage, square, square root and memory operations.
12. Run the application on an Android emulator and verify its functionality.
13. Stop the application.

## PROGRAM

### MainActivity.java


```java

package com.example.calculator;

import android.os.Bundle;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {

    TextView display;

    String currentInput = "0";
    String operator = "";

    double firstNumber = 0;
    double memory = 0;

    boolean newInput = true;

    String lastOperator = "";
    double lastNumber = 0;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        display = findViewById(R.id.display);

        // Number buttons
        int[] numberIds = {
                R.id.btn0, R.id.btn1, R.id.btn2,
                R.id.btn3, R.id.btn4, R.id.btn5,
                R.id.btn6, R.id.btn7, R.id.btn8,
                R.id.btn9
        };

        for (int id : numberIds) {
            Button button = findViewById(id);

            button.setOnClickListener(v ->
                    enterDigit(button.getText().toString()));
        }

        // Arithmetic operators
        findViewById(R.id.btnAdd).setOnClickListener(v -> setOperator("+"));
        findViewById(R.id.btnSubtract).setOnClickListener(v -> setOperator("-"));
        findViewById(R.id.btnMultiply).setOnClickListener(v -> setOperator("*"));
        findViewById(R.id.btnDivide).setOnClickListener(v -> setOperator("/"));

        // Decimal
        findViewById(R.id.btnDot).setOnClickListener(v -> {
            if (newInput) {
                currentInput = "0.";
                newInput = false;
            } else if (!currentInput.contains(".")) {
                currentInput += ".";
            }
            updateDisplay();
        });

        // Equals
        findViewById(R.id.btnEquals).setOnClickListener(v -> calculate());

        // Clear all
        findViewById(R.id.btnClear).setOnClickListener(v -> clearAll());

        // Clear entry
        findViewById(R.id.btnCE).setOnClickListener(v -> {
            currentInput = "0";
            newInput = true;
            updateDisplay();
        });

        // Backspace
        findViewById(R.id.btnBack).setOnClickListener(v -> {
            if (!newInput && currentInput.length() > 1) {
                currentInput = currentInput.substring(
                        0, currentInput.length() - 1);
            } else {
                currentInput = "0";
            }

            updateDisplay();
        });

        // Percentage
        findViewById(R.id.btnPercent).setOnClickListener(v -> {
            double value = getValue();

            if (operator.equals("+") || operator.equals("-")) {
                value = firstNumber * value / 100;
            } else {
                value = value / 100;
            }

            currentInput = format(value);
            newInput = true;
            updateDisplay();
        });

        // Reciprocal
        findViewById(R.id.btnReciprocal).setOnClickListener(v ->
                unaryOperation(1));

        // Square
        findViewById(R.id.btnSquare).setOnClickListener(v ->
                unaryOperation(2));

        // Square root
        findViewById(R.id.btnSqrt).setOnClickListener(v ->
                unaryOperation(3));

        // Positive / Negative
        findViewById(R.id.btnSign).setOnClickListener(v -> {
            double value = -getValue();
            currentInput = format(value);
            newInput = true;
            updateDisplay();
        });

        // Memory functions
        findViewById(R.id.btnMC).setOnClickListener(v -> {
            memory = 0;
        });

        findViewById(R.id.btnMR).setOnClickListener(v -> {
            currentInput = format(memory);
            newInput = true;
            updateDisplay();
        });

        findViewById(R.id.btnMS).setOnClickListener(v -> {
            memory = getValue();
        });

        findViewById(R.id.btnMPlus).setOnClickListener(v -> {
            memory += getValue();
        });

        findViewById(R.id.btnMMinus).setOnClickListener(v -> {
            memory -= getValue();
        });

        findViewById(R.id.btnMemory).setOnClickListener(v ->
                Toast.makeText(this,
                        "Memory: " + format(memory),
                        Toast.LENGTH_SHORT).show());
    }

    // Enter number
    void enterDigit(String digit) {

        if (newInput || currentInput.equals("0")) {
            currentInput = digit;
            newInput = false;
        } else {
            if (currentInput.length() < 15) {
                currentInput += digit;
            }
        }

        updateDisplay();
    }

    // Select operator
    void setOperator(String op) {

        if (!operator.isEmpty() && !newInput) {
            performCalculation();
        }

        firstNumber = getValue();
        operator = op;
        newInput = true;
        lastOperator = "";
    }

    // Perform arithmetic
    void performCalculation() {

        double secondNumber = getValue();
        double result = 0;

        switch (operator) {

            case "+":
                result = firstNumber + secondNumber;
                break;

            case "-":
                result = firstNumber - secondNumber;
                break;

            case "*":
                result = firstNumber * secondNumber;
                break;

            case "/":
                if (secondNumber == 0) {
                    showError();
                    return;
                }
                result = firstNumber / secondNumber;
                break;
        }

        firstNumber = result;
        currentInput = format(result);
        updateDisplay();
    }

    // Equals button
    void calculate() {

        if (!operator.isEmpty()) {

            double secondNumber = getValue();

            lastNumber = secondNumber;
            lastOperator = operator;

            performCalculation();

            operator = "";
            newInput = true;

        } else if (!lastOperator.isEmpty()) {

            firstNumber = getValue();
            currentInput = format(firstNumber);
            operator = lastOperator;

            performCalculation();

            operator = "";
            newInput = true;
        }
    }

    // Unary operations
    void unaryOperation(int type) {

        double value = getValue();
        double result = 0;

        if (type == 1) {

            if (value == 0) {
                showError();
                return;
            }

            result = 1 / value;

        } else if (type == 2) {

            result = value * value;

        } else if (type == 3) {

            if (value < 0) {
                showError();
                return;
            }

            result = Math.sqrt(value);
        }

        currentInput = format(result);
        newInput = true;
        updateDisplay();
    }

    // Get current number
    double getValue() {
        try {
            return Double.parseDouble(currentInput);
        } catch (Exception e) {
            return 0;
        }
    }

    // Format result
    String format(double value) {

        if (Double.isNaN(value) || Double.isInfinite(value)) {
            return "Error";
        }

        if (value == (long) value) {
            return String.valueOf((long) value);
        }

        return String.valueOf(value);
    }

    // Update screen
    void updateDisplay() {
        display.setText(currentInput);
    }

    // Clear everything
    void clearAll() {

        currentInput = "0";
        firstNumber = 0;
        operator = "";
        lastOperator = "";
        lastNumber = 0;
        newInput = true;

        updateDisplay();
    }

    // Error handling
    void showError() {

        currentInput = "Error";
        operator = "";
        lastOperator = "";
        newInput = true;

        updateDisplay();
    }
}

```

### activity_main.xml



```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:background="#F7F3F1"
    android:padding="4dp">

    <!-- Toolbar -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="55dp"
        android:gravity="center_vertical"
        android:orientation="horizontal"
        android:paddingHorizontal="12dp">

        <TextView
            android:layout_width="40dp"
            android:layout_height="match_parent"
            android:gravity="center"
            android:text="☰"
            android:textSize="25sp"
            android:textColor="#333333"/>

        <TextView
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:gravity="center_vertical"
            android:text="Standard"
            android:textSize="23sp"
            android:textStyle="bold"
            android:textColor="#222222"/>

        <TextView
            android:layout_width="45dp"
            android:layout_height="match_parent"
            android:gravity="center"
            android:text="◫"
            android:textSize="24sp"
            android:textColor="#333333"/>

        <TextView
            android:layout_width="45dp"
            android:layout_height="match_parent"
            android:gravity="center"
            android:text="◷"
            android:textSize="25sp"
            android:textColor="#333333"/>
    </LinearLayout>

    <!-- Display -->
    <TextView
        android:id="@+id/display"
        android:layout_width="match_parent"
        android:layout_height="92dp"
        android:layout_marginTop="20dp"
        android:background="@drawable/calc_display"
        android:gravity="right|center_vertical"
        android:padding="12dp"
        android:text="0"
        android:textColor="#000000"
        android:textSize="48sp"
        android:maxLines="1"
        android:singleLine="true"/>

    <!-- Memory buttons -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="38dp"
        android:orientation="horizontal"
        android:gravity="center">

        <Button
            android:id="@+id/btnMC"
            style="@style/MemoryButton"
            android:text="MC"/>

        <Button
            android:id="@+id/btnMR"
            style="@style/MemoryButton"
            android:text="MR"/>

        <Button
            android:id="@+id/btnMPlus"
            style="@style/MemoryButton"
            android:text="M+"/>

        <Button
            android:id="@+id/btnMMinus"
            style="@style/MemoryButton"
            android:text="M−"/>

        <Button
            android:id="@+id/btnMS"
            style="@style/MemoryButton"
            android:text="MS"/>

        <Button
            android:id="@+id/btnMemory"
            style="@style/MemoryButton"
            android:text="M⌄"/>
    </LinearLayout>

    <!-- Calculator keypad -->
    <GridLayout
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:columnCount="4"
        android:rowCount="6"
        android:useDefaultMargins="false">

        <Button
            android:id="@+id/btnPercent"
            style="@style/CalcKey"
            android:text="%"/>

        <Button
            android:id="@+id/btnCE"
            style="@style/CalcKey"
            android:text="CE"/>

        <Button
            android:id="@+id/btnClear"
            style="@style/CalcKey"
            android:text="C"/>

        <Button
            android:id="@+id/btnBack"
            style="@style/CalcKey"
            android:text="⌫"/>

        <Button
            android:id="@+id/btnReciprocal"
            style="@style/CalcKey"
            android:text="1/x"/>

        <Button
            android:id="@+id/btnSquare"
            style="@style/CalcKey"
            android:text="x²"/>

        <Button
            android:id="@+id/btnSqrt"
            style="@style/CalcKey"
            android:text="²√x"/>

        <Button
            android:id="@+id/btnDivide"
            style="@style/CalcKey"
            android:text="÷"/>

        <Button
            android:id="@+id/btn7"
            style="@style/CalcKey"
            android:text="7"/>

        <Button
            android:id="@+id/btn8"
            style="@style/CalcKey"
            android:text="8"/>

        <Button
            android:id="@+id/btn9"
            style="@style/CalcKey"
            android:text="9"/>

        <Button
            android:id="@+id/btnMultiply"
            style="@style/CalcKey"
            android:text="×"/>

        <Button
            android:id="@+id/btn4"
            style="@style/CalcKey"
            android:text="4"/>

        <Button
            android:id="@+id/btn5"
            style="@style/CalcKey"
            android:text="5"/>

        <Button
            android:id="@+id/btn6"
            style="@style/CalcKey"
            android:text="6"/>

        <Button
            android:id="@+id/btnSubtract"
            style="@style/CalcKey"
            android:text="−"/>

        <Button
            android:id="@+id/btn1"
            style="@style/CalcKey"
            android:text="1"/>

        <Button
            android:id="@+id/btn2"
            style="@style/CalcKey"
            android:text="2"/>

        <Button
            android:id="@+id/btn3"
            style="@style/CalcKey"
            android:text="3"/>

        <Button
            android:id="@+id/btnAdd"
            style="@style/CalcKey"
            android:text="+"/>

        <Button
            android:id="@+id/btnSign"
            style="@style/CalcKey"
            android:text="+/−"/>

        <Button
            android:id="@+id/btn0"
            style="@style/CalcKey"
            android:text="0"/>

        <Button
            android:id="@+id/btnDot"
            style="@style/CalcKey"
            android:text="."/>

        <Button
            android:id="@+id/btnEquals"
            style="@style/CalcEquals"
            android:text="="/>

    </GridLayout>

</LinearLayout>

```

## OUTPUT SCREENSHOTS

<img width="963" height="752" alt="image" src="https://github.com/user-attachments/assets/ad7cc433-a8f7-41a4-99cc-1108aee7d978" />

<img width="186" height="415" alt="image" src="https://github.com/user-attachments/assets/e514dc31-4406-44a9-85d5-9176fe180589" />

<img width="188" height="415" alt="image" src="https://github.com/user-attachments/assets/d1e071b9-b5d7-490d-acdd-f7496d0fb711" />


## RESULT

Thus, a simple calculator application was successfully developed using Android Studio with Java and XML. The application performs basic arithmetic operations and additional mathematical functions with an interactive user interface.

#include <iostream>
#include <fstream>
#include <cmath>
#include <iomanip>
#include <stdexcept>
#include "CppUnitTest.h"

using namespace std;

// Функція для розрахунку значення y залежно від параметрів x та n
double calculateY(double x, int n) {
    if (x <= 0) {
        double y = 1;
        for (int i = 0; i < n; i++) {
            y *= (i + x);
        }
        return y;
    } else {
        double y = 0;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                y += (x + j) / (i + j + 1.0);
            }
        }
        return y;
    }
}

// Функція для перевірки валідності введених параметрів
bool checkValidInput(int n, double x) {
    return n > 4 && x >= 0;
}

// Функція для обробки виняткових ситуацій
void handleException() {
    cerr << "Exception occurred. Please check your input values." << endl;
}

int main() {
    try {
        int n;
        double a, b, h;

        cout << "Enter n (integer positive number): ";
        cin >> n;

        if (n <= 0) {
            throw invalid_argument("Error: n must be a positive integer.");
        }

        cout << "Enter start value of x (a): ";
        cin >> a;

        // Перевірка валідності введених параметрів за допомогою нової функції
        if (!checkValidInput(n, a)) {
            throw invalid_argument("Invalid input. Check your values.");
        }

        cout << "Enter end value of x (b): ";
        cin >> b;

        cout << "Enter step size for x (h): ";
        cin >> h;

        if (h <= 0) {
            throw invalid_argument("Error: Step size for x must be a positive number.");
        }

        char choice;
        cout << "Do you want to save the results to a file? (y/n): ";
        cin >> choice;

        ofstream outputFile;

        if (choice == 'y' || choice == 'Y') {
            string filename;
            cout << "Enter the filename to save the results: ";
            cin >> filename;
            outputFile.open(filename);

            if (!outputFile.is_open()) {
                throw runtime_error("Error: Unable to open the file for writing.");
            }
        }

        for (double x = a; x <= b; x += h) {
            double y = calculateY(x, n);
            cout << "x = " << setw(10) << x << ", y = " << setw(10) << y << endl;

            if (outputFile.is_open()) {
                outputFile << "x = " << setw(10) << x << ", y = " << setw(10) << y << endl;
            }
        }

        if (outputFile.is_open()) {
            outputFile.close();
            cout << "Results have been saved to the file." << endl;
        }
    } catch (const exception& e) {
        cerr << "Exception: " << e.what() << endl;
        handleException();
        return 1;
    }

    return 0;
}

// Юніт-тести для нових функцій
TEST_CLASS(oipz_Tests) {
public:
    TEST_METHOD(calculateY_get2and5_22returned) {
        // Arrange
        double x = 2;
        int n = 5;
        double expected = 22.184;

        // Act
        double actual = calculateY(x, n);

        // Assert
        Assert::IsTrue(abs(expected - actual) < 0.001);
    }

    TEST_METHOD(checkValidInput_validInput_returnTrue) {
        // Arrange
        int n = 6;
        double x = 3.5;

        // Act
        bool isValid = checkValidInput(n, x);

        // Assert
        Assert::IsTrue(isValid);
    }

    TEST_METHOD(checkValidInput_invalidN_returnFalse) {
        // Arrange
        int n = 3;
        double x = 3.5;

        // Act
        bool isValid = checkValidInput(n, x);

        // Assert
        Assert::IsFalse(isValid);
    }

    TEST_METHOD(checkValidInput_negativeX_returnFalse) {
        // Arrange
        int n = 6;
        double x = -3.5;

        // Act
        bool isValid = checkValidInput(n, x);

        // Assert
        Assert::IsFalse(isValid);
    }
};


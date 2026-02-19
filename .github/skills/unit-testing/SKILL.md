---
name: unit-testing-fundamentals
description: 'Guides on writing and running unit tests for vanilla JavaScript projects. Setup testing frameworks and write tests for existing HTML/CSS/JS applications.'
---

# Unit Testing Fundamentals Skill

## Overview

Unit testing is the practice of writing automated tests for individual functions, methods, and components. This skill covers setting up test frameworks for vanilla JavaScript projects and writing effective tests for your existing codebase.

## For This Project

This is a vanilla JavaScript project (no build tools or frameworks). We'll use a lightweight testing setup:

- **Jest** – Works great for vanilla JS with minimal configuration.
- **Vitest** – Modern alternative, also supports vanilla JS out of the box.

## Getting Started

### 1. Initialize npm & Install Jest

```bash
npm init -y
npm install --save-dev jest babel-jest @babel/preset-env --legacy-peer-deps
```

### 2. Create jest.config.js

```javascript
module.exports = {
  testEnvironment: 'jsdom',
  collectCoverageFrom: ['script.js'],
  testMatch: ['**/*.test.js'],
  transform: {
    '^.+\\.js$': 'babel-jest',
  },
};
```

### 3. Create .babelrc

```json
{
  "presets": [["@babel/preset-env", { "targets": { "node": "current" } }]]
}
```

### 4. Update package.json Scripts

```json
"scripts": {
  "test": "jest",
  "test:watch": "jest --watch",
  "test:coverage": "jest --coverage"
}
```

### 5. Run Tests

```bash
npm test
```

## Test Structure

### Basic Test Pattern

```javascript
describe('MyFunction', () => {
  it('should return the correct result', () => {
    const result = myFunction(input);
    expect(result).toBe(expectedValue);
  });
});
```

### Key Components

- **describe()** – Group related tests together.
- **it()** / **test()** – Define individual test cases.
- **expect()** – Make assertions about the output.
- **beforeEach() / afterEach()** – Setup and cleanup between tests.

## Common Assertions

| Assertion | Purpose |
| --------- | ------- |
| `expect(value).toBe(expected)` | Strict equality check. |
| `expect(value).toEqual(expected)` | Deep equality check. |
| `expect(value).toContain(item)` | Check if array/string contains item. |
| `expect(fn).toThrow()` | Verify function throws an error. |
| `expect(spy).toHaveBeenCalled()` | Verify function was called. |
| `expect(value).toBeDefined()` | Check if value is defined. |
| `expect(value).toBeTruthy()` | Check for truthy value. |

## Writing Tests for This Project

### Example: Testing Theme Toggle Functions

First, refactor `script.js` to export functions:

```javascript
// script.js
export function toggleThemeClass(bodyElement) {
  bodyElement.classList.toggle('dark-mode');
  return bodyElement.classList.contains('dark-mode');
}

export function getToggleIcon(isDarkMode) {
  return isDarkMode ? '☀️' : '🌙';
}

export function saveThemePreference(theme) {
  localStorage.setItem('theme', theme);
}

export function getThemePreference() {
  return localStorage.getItem('theme') || 'light';
}
```

Then write tests:

```javascript
// script.test.js
import { toggleThemeClass, getToggleIcon, saveThemePreference, getThemePreference } from './script';

describe('Theme Toggle', () => {
  let mockBodyElement;

  beforeEach(() => {
    mockBodyElement = {
      classList: {
        toggle: jest.fn(),
        contains: jest.fn((className) => {
          if (className === 'dark-mode') {
            return mockBodyElement._darkMode || false;
          }
        }),
      },
    };
    localStorage.clear();
  });

  describe('getToggleIcon', () => {
    it('should return sun icon for dark mode', () => {
      expect(getToggleIcon(true)).toBe('☀️');
    });

    it('should return moon icon for light mode', () => {
      expect(getToggleIcon(false)).toBe('🌙');
    });
  });

  describe('saveThemePreference', () => {
    it('should save theme to localStorage', () => {
      saveThemePreference('dark');
      expect(localStorage.getItem('theme')).toBe('dark');
    });
  });

  describe('getThemePreference', () => {
    it('should return saved theme', () => {
      localStorage.setItem('theme', 'dark');
      expect(getThemePreference()).toBe('dark');
    });

    it('should return light mode as default', () => {
      expect(getThemePreference()).toBe('light');
    });
  });
});
```

## Running Tests

```bash
# Run all tests
npm test

# Run tests in watch mode
npm run test:watch

# Run tests with coverage report
npm run test:coverage

# Run specific test file
npm test -- calculator.test.js

# Run tests matching a pattern
npm test -- --testNamePattern="add"
```

## Project Structure & Setup

After installing Jest, your project will look like:

```
copilot-demo/
├── index.html          # Main HTML file
├── style.css           # Styling
├── script.js           # Application code (refactored with exports)
├── script.test.js      # Tests for script.js
├── jest.config.js      # Jest configuration
├── .babelrc            # Babel configuration
├── package.json        # Dependencies & scripts
└── node_modules/       # Installed packages
```

## Best Practices for This Project

- **Refactor script.js** – Wrap functions in exports to make them testable
- **Create separate test files** – Use `.test.js` suffix for test files
- **Mock browser APIs** – Use `jest.fn()` for DOM methods and `localStorage`
- **Test pure functions first** – Theme toggle logic before DOM interactions
- **Use beforeEach()** – Reset mocks and DOM state between tests
- **Aim for 80% coverage** – Focus on critical paths (theme, navigation, animations)

## Coverage Reports

Jest generates HTML coverage reports:

```bash
npm run test:coverage
open coverage/lcov-report/index.html
```

Key metrics:
- **Statements** – Lines of code executed.
- **Branches** – Conditional paths tested.
- **Functions** – All functions called at least once.
- **Lines** – All lines executed at least once.

## Troubleshooting

- **Module not found** – Ensure `script.js` exports functions at the top level
- **"document is not defined"** – Check `testEnvironment: 'jsdom'` in jest.config.js
- **localStorage is undefined** – Jest provides a mock by default; clear with `localStorage.clear()` in beforeEach()
- **Tests not finding files** – Verify files are in the same directory and use correct import paths
- **Class methods not working** – Refactor to arrow functions or bind context in tests
- **Async timeout** – Increase timeout with `jest.setTimeout(10000)` for slow operations

## Integration with Existing Code

Your `script.js` uses inline code that runs immediately. To make it testable:

1. **Extract functions** – Wrap logic in exported functions
2. **Separate concerns** – Keep DOM logic separate from business logic
3. **Delay initialization** – Don't run code at module load; call functions from `index.html` or event listeners
4. **Example refactoring:**

```javascript
// Before: Runs immediately
const currentTheme = localStorage.getItem('theme') || 'light';
if (currentTheme === 'dark') {
  body.classList.add('dark-mode');
}

// After: Exportable and testable
export function initTheme(bodyElement) {
  const theme = getThemePreference();
  if (theme === 'dark') {
    bodyElement.classList.add('dark-mode');
  }
}

// Then call in a test or from index.html
document.addEventListener('DOMContentLoaded', () => {
  initTheme(document.body);
});
```

## Common Patterns for Vanilla JS

### Testing Functions with DOM Dependencies

```javascript
it('should add dark-mode class to body', () => {
  const body = document.body;
  body.classList.remove('dark-mode');
  
  toggleTheme(body);
  
  expect(body.classList.contains('dark-mode')).toBe(true);
});
```

### Mocking localStorage

```javascript
beforeEach(() => {
  localStorage.clear();
  jest.clearAllMocks();
});

it('should persist theme preference', () => {
  saveThemePreference('dark');
  const saved = localStorage.getItem('theme');
  expect(saved).toBe('dark');
});
```

### Testing Event Listeners

```javascript
it('should toggle theme when button is clicked', () => {
  const button = document.createElement('button');
  const spy = jest.fn();
  
  button.addEventListener('click', spy);
  button.click();
  
  expect(spy).toHaveBeenCalled();
});
```

### Testing Scroll Behavior

```javascript
it('should scroll to target element', (done) => {
  const element = document.createElement('div');
  document.body.appendChild(element);
  
  element.scrollIntoView = jest.fn(() => {
    expect(element.scrollIntoView).toHaveBeenCalled();
    done();
  });
  
  element.scrollIntoView({ behavior: 'smooth' });
});
```

## Resources

- [Jest Documentation](https://jestjs.io/)
- [Jest DOM Documentation](https://testing-library.com/docs/queries/about)
- [Testing with jsdom](https://jestjs.io/docs/timer-mocks)
- [Mocking localStorage](https://jestjs.io/docs/manual-mocks)
- [Jest API Reference](https://jestjs.io/docs/api)
```

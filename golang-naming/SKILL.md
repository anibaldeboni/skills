---
name: golang-naming
description: Idiomatic Go naming conventions, best practices, and guidelines for building readable, maintainable, and efficient Go applications.
---

# Go Naming Convetions

Idiomatic Go naming conventions and best practices for building readable, maintainable, and efficient applications.

## When to Activate

- Writing new Go code
- Reviewing Go code
- Refactoring existing Go code
- Designing Go packages/modules

## Core Principles

### 1. Identifiers
    You cannot use any of the following Go keywords as identifiers:
      break        default      func         interface    select
      case         defer        go           map          struct
      chan         else         goto         package      switch
      const        fallthrough  if           range        type
      continue     for          import       return       var
    - Use descriptive names that convey the purpose of the variable, function, or type.
    - Avoid abbreviations unless they are widely recognized (e.g., "ID" for "identifier").
    - Use camelCase for variable and function names, and PascalCase for type names.
    - Words that are acronyms or initialisms (like API, URL or HTTP) should use a consistent case within the identifier. So, for example, apiKey or APIKey is good, but ApiKey is not. This rule also applies to ID when it is used as shorthand for the words "identity" or "identifier" — so that means write userID rather than userId.
    - Don't create variables with names like int, bool or any. Similarly, avoid creating functions with names that clash with Go's builtin functions. So, for example, don't create functions with names like min, max, len or clear.
    - Where possible, try to avoid choosing identifiers that clash with the standard library package names. So, for example, don't create variables with names like http, json or time. Similarly, avoid creating functions with names that clash with standard library package names. So, for example, don't create functions with names like http, json or time.
    - Should avoid creating identifiers that clash with the package names that your code is actually importing and using. So, for example, if you are writing code that imports the url and net/mail packages, then don't use the words url and mail as identifiers in that code.
### 1.1 Exported and unexported identifiers
    - In Go, identifiers that start with an uppercase letter are exported (public), while those that start with a lowercase letter are unexported (private).
    - Use uppercase letters for types, functions, and variables that need to be accessible from other packages.
    - Use lowercase letters for internal implementation details that should not be exposed outside the package.
    - If you have an identifier which is narrow in scope and only used close to where it is declared, it's generally OK to use a short and not-very-descriptive name. For example, if you have a loop variable that is only used within the loop, it's generally OK to use a short name like i or j for that variable. Similarly, if you have a function parameter that is only used within the function, it's generally OK to use a short name like x or y for that parameter. However, if you have an identifier which is wider in scope and used in multiple places throughout your code, it's generally better to use a more descriptive name for that identifier. For example, if you have a variable that is used in multiple functions throughout your code, it's generally better to use a descriptive name for that variable rather than a short name like v or data.
    - When you are naming exported functions, try to avoid repeating the name of the package they are declared in. For example, if you have a package named "math" and you are declaring an exported function that calculates the square root of a number, it's generally better to name that function "Sqrt" rather than "MathSqrt". This is because the package name already provides context for the function, so repeating it in the function name can be redundant and make the code less readable.

### 2. Package Names
    - Package names should be short, lowercase, and descriptive of their functionality.
    - Avoid using underscores or mixed case in package names.
    - Use singular nouns for package names (e.g., "net/http" instead of "net/https").
    - If a package provides a specific type or function, consider naming the package after that type or function (e.g., "json" for JSON encoding/decoding).
    - Avoid using generic names like "utils" or "helpers" for packages, as they do not convey specific functionality and can lead to confusion about the package's purpose.
    - If a package is meant to be used as a library, consider using a name that reflects its purpose or the problem it solves (e.g., "auth" for authentication-related functionality).

### 3. File Names
    - File names should be lowercase and descriptive of their contents.
    - Use underscores to separate words in file names (e.g., "user_service.go" instead of "UserService.go").
    - Avoid using generic file names like "main.go" for files that contain specific functionality, as it can lead to confusion about the file's purpose.
    - If a file contains a specific type or function, consider naming the file after that type or function (e.g., "user.go" for a file that defines a User type).
    - If a file contains multiple related types or functions, consider using a name that reflects the overall purpose of the file (e.g., "handlers.go" for a file that contains HTTP handlers).

### 4. Getter and setter methods on structs
    - In Go, it's common to use getter and setter methods on structs to provide access to private fields. When naming these methods, follow these conventions:
      - For getter methods, use the name of the field being accessed (e.g., "Name" for a field named "name").
      - For setter methods, use the name of the field being modified followed by "Set" (e.g., "SetName" for a field named "name").
      - Avoid using prefixes like "Get" for getter methods, as it can be redundant and make the code less readable. Instead, simply use the name of the field being accessed.
      - When naming setter methods, consider using a verb that describes the action being performed (e.g., "UpdateName" instead of "SetName") to make the code more descriptive and easier to understand.
      - If a struct has multiple fields that require getter and setter methods, consider grouping them together in a logical way (e.g., all getter methods together and all setter methods together) to improve readability and organization of the code.

### 5. Interfaces
    - Interface names should be descriptive of the behavior they represent and typically end with "er" (e.g., "Reader", "Writer", "Formatter").
    - Avoid using generic names like "Interface" or "Handler" for interfaces, as they do not convey specific behavior and can lead to confusion about the interface's purpose.
    - If an interface represents a specific role or responsibility, consider naming it after that role (e.g., "Authenticator" for an interface that handles authentication).
    - When defining an interface that is meant to be implemented by multiple types, consider using a name that reflects the common behavior shared by those types (e.g., "Shape" for an interface that is implemented by different geometric shapes).
    - If an interface is meant to be used as a callback or event handler, consider using a name that reflects its purpose (e.g., "EventHandler" for an interface that handles events).
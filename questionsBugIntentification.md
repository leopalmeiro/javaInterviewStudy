# CITI Interview - Java Developer Questions & Answers

## 1. Bug Identification: Getter Returning Wrong Type

### Question
You're reviewing the following code and a tester reports that retrieving a float value is causing runtime errors. Find and fix the issue.

```java
public class PriceCalculator {
    private float discountRate;
    
    public String getDiscountRate() {
        return discountRate;
    }
    
    public void setDiscountRate(float rate) {
        this.discountRate = rate;
    }
}

// Usage
PriceCalculator calculator = new PriceCalculator();
calculator.setDiscountRate(0.15f);
float rate = calculator.getDiscountRate(); // Error here
```

What is the bug and how would you fix it?

### Answer

**The Bug:**
The getter method `getDiscountRate()` is declared to return a `String`, but:
1. The field `discountRate` is a `float`
2. The code tries to assign the return value to a `float` variable
3. This causes a type mismatch compilation error

**The Fix:**
Change the return type of the getter from `String` to `float`:

```java
public class PriceCalculator {
    private float discountRate;
    
    public float getDiscountRate() {  // Changed from String to float
        return discountRate;
    }
    
    public void setDiscountRate(float rate) {
        this.discountRate = rate;
    }
}

// Usage - Now works correctly
PriceCalculator calculator = new PriceCalculator();
calculator.setDiscountRate(0.15f);
float rate = calculator.getDiscountRate(); // ✓ No error
```

**Key Learning Points:**
- Always ensure getter return types match the field type
- Use your IDE's auto-generate getter/setter feature to avoid such errors
- Pay attention to compiler warnings about type mismatches

---

## 2. Calculation Problem: Numerical Calculation Exercise

### Question
Write a Java method that calculates the total cost of items in a shopping cart with the following requirements:

- Each item has a price (double) and quantity (int)
- Apply a 10% discount if the subtotal is greater than $100
- Apply an additional 5% loyalty discount if customer has `isLoyalty` flag set to true
- Calculate final tax (8.5%) on the discounted amount
- Return the final total including tax

**Example:**
- Item 1: $50.00 × 2 = $100.00
- Item 2: $25.00 × 1 = $25.00
- Subtotal: $125.00
- After 10% discount: $112.50
- After 5% loyalty discount: $106.88
- Tax (8.5%): $9.08
- **Final Total: $115.96**

### Answer

```java
public class ShoppingCart {
    
    public double calculateTotal(List<CartItem> items, boolean isLoyalty) {
        // Calculate subtotal
        double subtotal = 0.0;
        for (CartItem item : items) {
            subtotal += item.getPrice() * item.getQuantity();
        }
        
        // Apply 10% discount if subtotal > $100
        double discounted = subtotal;
        if (subtotal > 100.0) {
            discounted = subtotal * 0.90; // 10% off
        }
        
        // Apply additional 5% loyalty discount if applicable
        if (isLoyalty) {
            discounted = discounted * 0.95; // Additional 5% off
        }
        
        // Calculate and add tax (8.5%)
        double tax = discounted * 0.085;
        double finalTotal = discounted + tax;
        
        return Math.round(finalTotal * 100.0) / 100.0; // Round to 2 decimal places
    }
}

public class CartItem {
    private double price;
    private int quantity;
    
    public CartItem(double price, int quantity) {
        this.price = price;
        this.quantity = quantity;
    }
    
    public double getPrice() { return price; }
    public int getQuantity() { return quantity; }
}

// Test
List<CartItem> items = new ArrayList<>();
items.add(new CartItem(50.00, 2));
items.add(new CartItem(25.00, 1));
ShoppingCart cart = new ShoppingCart();
double total = cart.calculateTotal(items, true);
System.out.println("Final Total: $" + total); // $115.96
```

**Key Learning Points:**
- Always consider rounding for financial calculations
- Apply discounts in the correct sequence
- Use descriptive variable names
- Consider edge cases (no discount when subtotal ≤ $100)

---

## 3. Logic Problem: Motorway Journey Tracking

### Question
You receive a list of car entries and exits on a motorway. Each entry contains:
- `plateNumber` (String): The car's license plate
- `eventType` (String): Either "ENTER" or "EXIT"
- `timestamp` (long): The time of the event

Write a method that determines how many cars have completed their journeys (i.e., have both "ENTER" and "EXIT" events).

**Example Input:**
```
[
  { plateNumber: "ABC123", eventType: "ENTER", timestamp: 1000 },
  { plateNumber: "XYZ789", eventType: "ENTER", timestamp: 1100 },
  { plateNumber: "ABC123", eventType: "EXIT", timestamp: 2000 },
  { plateNumber: "MNO456", eventType: "ENTER", timestamp: 1500 },
  { plateNumber: "XYZ789", eventType: "EXIT", timestamp: 2100 }
]
```

**Expected Output:** 2 cars completed their journeys (ABC123 and XYZ789). MNO456 only entered but didn't exit.

### Answer

```java
public class MotorwayJourneyTracker {
    
    public int countCompletedJourneys(List<JourneyEvent> events) {
        // Use a map to track each plate's events
        Map<String, Set<String>> plateEvents = new HashMap<>();
        
        for (JourneyEvent event : events) {
            String plate = event.getPlateNumber();
            String eventType = event.getEventType();
            
            // Initialize the set if this plate hasn't been seen before
            plateEvents.putIfAbsent(plate, new HashSet<>());
            
            // Add the event type to the set
            plateEvents.get(plate).add(eventType);
        }
        
        // Count journeys that have both ENTER and EXIT events
        int completedCount = 0;
        for (Set<String> events_set : plateEvents.values()) {
            if (events_set.contains("ENTER") && events_set.contains("EXIT")) {
                completedCount++;
            }
        }
        
        return completedCount;
    }
}

public class JourneyEvent {
    private String plateNumber;
    private String eventType; // "ENTER" or "EXIT"
    private long timestamp;
    
    public JourneyEvent(String plateNumber, String eventType, long timestamp) {
        this.plateNumber = plateNumber;
        this.eventType = eventType;
        this.timestamp = timestamp;
    }
    
    public String getPlateNumber() { return plateNumber; }
    public String getEventType() { return eventType; }
    public long getTimestamp() { return timestamp; }
}

// Test
List<JourneyEvent> events = new ArrayList<>();
events.add(new JourneyEvent("ABC123", "ENTER", 1000));
events.add(new JourneyEvent("XYZ789", "ENTER", 1100));
events.add(new JourneyEvent("ABC123", "EXIT", 2000));
events.add(new JourneyEvent("MNO456", "ENTER", 1500));
events.add(new JourneyEvent("XYZ789", "EXIT", 2100));

MotorwayJourneyTracker tracker = new MotorwayJourneyTracker();
int completed = tracker.countCompletedJourneys(events);
System.out.println("Completed journeys: " + completed); // Output: 2
```

**Alternative Solution (Using Stream API - More Concise):**

```java
public int countCompletedJourneys(List<JourneyEvent> events) {
    return (int) events.stream()
        .collect(Collectors.groupingBy(
            JourneyEvent::getPlateNumber,
            Collectors.mapping(JourneyEvent::getEventType, Collectors.toSet())
        ))
        .values()
        .stream()
        .filter(eventSet -> eventSet.size() == 2 && 
                          eventSet.contains("ENTER") && 
                          eventSet.contains("EXIT"))
        .count();
}
```

**Key Learning Points:**
- Use `HashMap` and `HashSet` to efficiently track and count events
- Group data by a common key (plate number)
- Verify both required events exist before counting as complete
- Consider using Java Streams for more functional approaches
- Time complexity: O(n) for both solutions
- Space complexity: O(n) to store the map

---

## 4. String Decoding: Nested Multipliers

### Question
Given an encoded string with nested numeric multipliers, decode it to get the expanded string.

**Rules:**
- A number followed by brackets indicates repetition of the content inside the brackets
- Brackets can be nested
- Format: `number[string]`

**Example:**
```
Input: "2[3[a]b]"
Output: "aaabaaab"

Explanation:
- 3[a] expands to "aaa"
- "aaa" + "b" = "aaab"
- 2["aaab"] = "aaabaaab"
```

**More Examples:**
- `"3[a2[c]]"` → `"accaccacc"`
- `"2[abc]3[cd]ef"` → `"abcabccdcdcdef"`
- `"a2[b2[c]]d"` → `"abccbccdd"`

### Answer

**Approach 1: Using Stack (Recommended)**

```java
public class StringDecoder {
    
    public String decodeString(String s) {
        Stack<Integer> numStack = new Stack<>();
        Stack<StringBuilder> strStack = new Stack<>();
        StringBuilder currentStr = new StringBuilder();
        int currentNum = 0;
        
        for (char c : s.toCharArray()) {
            if (Character.isDigit(c)) {
                // Handle multi-digit numbers
                currentNum = currentNum * 10 + (c - '0');
            } else if (c == '[') {
                // Push current number and string to stacks
                numStack.push(currentNum);
                strStack.push(currentStr);
                
                // Reset for next iteration
                currentNum = 0;
                currentStr = new StringBuilder();
            } else if (c == ']') {
                // Pop and construct the repeated string
                StringBuilder prevStr = strStack.pop();
                int repeatCount = numStack.pop();
                
                for (int i = 0; i < repeatCount; i++) {
                    prevStr.append(currentStr);
                }
                
                currentStr = prevStr;
            } else {
                // Regular character
                currentStr.append(c);
            }
        }
        
        return currentStr.toString();
    }
}

// Test cases
StringDecoder decoder = new StringDecoder();
System.out.println(decoder.decodeString("2[3[a]b]"));      // Output: "aaabaaab"
System.out.println(decoder.decodeString("3[a2[c]]"));       // Output: "accaccacc"
System.out.println(decoder.decodeString("2[abc]3[cd]ef")); // Output: "abcabccdcdcdef"
System.out.println(decoder.decodeString("a2[b2[c]]d"));     // Output: "abccbccdd"
```

**Approach 2: Using Recursion**

```java
public class StringDecoderRecursive {
    
    private int index = 0;
    
    public String decodeString(String s) {
        index = 0;
        return decodeStringHelper(s);
    }
    
    private String decodeStringHelper(String s) {
        StringBuilder result = new StringBuilder();
        
        while (index < s.length()) {
            char c = s.charAt(index);
            
            if (Character.isDigit(c)) {
                // Extract the number
                int num = 0;
                while (index < s.length() && Character.isDigit(s.charAt(index))) {
                    num = num * 10 + (s.charAt(index) - '0');
                    index++;
                }
                
                // Skip the '['
                index++;
                
                // Recursively decode the content inside brackets
                String decodedStr = decodeStringHelper(s);
                
                // Append the decoded string num times
                for (int i = 0; i < num; i++) {
                    result.append(decodedStr);
                }
            } else if (c == ']') {
                // End of current level
                index++;
                return result.toString();
            } else {
                // Regular character
                result.append(c);
                index++;
            }
        }
        
        return result.toString();
    }
}

// Test
StringDecoderRecursive decoder = new StringDecoderRecursive();
System.out.println(decoder.decodeString("2[3[a]b]")); // Output: "aaabaaab"
```

**Time & Space Complexity:**
- **Time Complexity:** O(n) where n is the length of the decoded string (in worst case, we build the entire string)
- **Space Complexity:** O(d) where d is the maximum depth of nesting (for stack/recursion)

**Key Learning Points:**
- Stack is perfect for handling nested structures and bracket matching
- Multi-digit numbers require special handling (multiply by 10 and add next digit)
- Two separate stacks maintain number and string context independently
- Recursion alternative naturally handles nested structures
- Consider edge cases: single characters, multiple consecutive numbers, deeply nested brackets
- Test with examples gradually increasing in complexity

---

## 5. Effective Final in Java

### Question
What is "effective final" in Java? Explain with examples and when it's used.

### Answer

**Effective Final Definition:**
A variable is considered "effective final" if it is not declared as `final` but is never reassigned after initialization. This concept was introduced in Java 8 to allow local variables to be used in lambda expressions and anonymous classes without requiring them to be explicitly declared as `final`.

**Examples:**

```java
public class EffectiveFinalExample {
    
    public void demonstrateEffectiveFinal() {
        int x = 10; // Effective final - never reassigned
        
        // Can be used in lambda (Java 8+)
        Runnable r = () -> System.out.println(x);
        
        // Can be used in anonymous class
        Thread t = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println(x); // OK - x is effectively final
            }
        });
        
        // x = 20; // Uncommenting this would make x not effectively final
    }
    
    public void demonstrateNotEffectiveFinal() {
        int y = 5;
        y = 10; // y is reassigned, so not effectively final
        
        // This would cause compilation error
        // Runnable r = () -> System.out.println(y); // Error: y is not effectively final
    }
}
```

**When Effective Final is Required:**
- **Lambda Expressions**: Local variables must be effectively final to be captured
- **Anonymous Classes**: Same restriction applies
- **Method References**: Variables used in method references must be effectively final

**Key Differences from `final`:**
- `final` variables cannot be reassigned at all
- Effective final variables can be reassigned, but aren't (by convention)
- Compiler enforces effective finality for lambda capture

**Best Practices:**
- Prefer `final` for constants and immutable references
- Use effective final for variables that logically shouldn't change
- IDEs often suggest making variables final if they're effectively final

---

## 6. Generics: <? super Animal> vs <? extends Animal>

### Question
Explain the differences between `<? super Animal>` and `<? extends Animal>` in Java generics. What are their advantages and disadvantages? Provide examples.

### Answer

**PECS Principle:**
- **Producer Extends Consumer Super** (PECS)
- Use `extends` when you only need to read from a collection (producer)
- Use `super` when you only need to write to a collection (consumer)

**<? extends Animal> (Upper Bounded Wildcard):**

```java
public void printAnimals(List<? extends Animal> animals) {
    for (Animal animal : animals) {
        animal.makeSound(); // OK - can read as Animal
    }
    // animals.add(new Dog()); // ERROR - cannot add to ? extends Animal
    // animals.add(new Cat()); // ERROR - cannot add to ? extends Animal
}
```

**Advantages:**
- **Type Safety**: Prevents adding wrong types to collections
- **Flexibility**: Method accepts `List<Dog>`, `List<Cat>`, `List<Animal>`
- **Read Operations**: Safe to read elements as the upper bound type

**Disadvantages:**
- **Write Restrictions**: Cannot add elements (except null)
- **Limited Operations**: Cannot use methods that require specific subtypes

**<? super Animal> (Lower Bounded Wildcard):**

```java
public void addAnimals(List<? super Animal> animals) {
    animals.add(new Dog()); // OK - can add Dog (subtype of Animal)
    animals.add(new Cat()); // OK - can add Cat (subtype of Animal)
    // Animal animal = animals.get(0); // ERROR - can only read as Object
}
```

**Advantages:**
- **Write Operations**: Can add elements of the lower bound type and its subtypes
- **Flexibility**: Method accepts `List<Animal>`, `List<Object>`
- **Consumer Pattern**: Perfect for methods that add elements

**Disadvantages:**
- **Read Restrictions**: Can only read as `Object` (not useful for specific types)
- **Limited Flexibility**: Cannot accept `List<Dog>` (Dog is not a supertype of Animal)

**Complete Example:**

```java
class Animal {
    void makeSound() { System.out.println("Animal sound"); }
}

class Dog extends Animal {
    @Override
    void makeSound() { System.out.println("Woof"); }
}

class Cat extends Animal {
    @Override
    void makeSound() { System.out.println("Meow"); }
}

public class WildcardExample {
    
    // Producer - use extends
    public static void printAnimalSounds(List<? extends Animal> animals) {
        for (Animal animal : animals) {
            animal.makeSound();
        }
    }
    
    // Consumer - use super
    public static void addDogs(List<? super Animal> animals) {
        animals.add(new Dog());
        animals.add(new Cat());
    }
    
    public static void main(String[] args) {
        List<Dog> dogs = new ArrayList<>();
        dogs.add(new Dog());
        
        List<Animal> animals = new ArrayList<>();
        
        // Works with extends
        printAnimalSounds(dogs);    // List<Dog> -> ? extends Animal ✓
        printAnimalSounds(animals); // List<Animal> -> ? extends Animal ✓
        
        // Works with super
        addDogs(animals); // List<Animal> -> ? super Animal ✓
        // addDogs(dogs); // ERROR: List<Dog> -> ? super Animal ✗
    }
}
```

**Key Learning Points:**
- **PECS Rule**: "Producer extends, Consumer super"
- **Read vs Write**: Use extends for reading, super for writing
- **Common Pitfalls**: Trying to add to `? extends T` or read specifically from `? super T`
- **Real-world Usage**: `Collections.copy()` uses `? super T` for destination, `? extends T` for source

---

## 7. Clean Code: Unnecessary Code in Boolean Method

### Question
Review the following static method that returns a boolean. The method has no logical issues (no null pointer exceptions, correct primitive/wrapper handling), but contains unnecessary code. Identify the clean code violations and suggest improvements.

```java
public static boolean isValidUser(User user) {
    boolean result = false;
    
    if (user != null) {
        String name = user.getName();
        if (name != null && !name.trim().isEmpty()) {
            Integer age = user.getAge();
            if (age != null && age >= 18) {
                Boolean isActive = user.getIsActive();
                if (isActive != null && isActive.booleanValue()) {
                    result = true;
                }
            }
        }
    }
    
    return result;
}
```

What clean code principles are violated? How would you refactor this?

### Answer

**Clean Code Violations:**

1. **Unnecessary Variable Initialization**: `boolean result = false;` is redundant
2. **Deep Nesting**: Multiple nested if-statements make code hard to read
3. **Primitive vs Wrapper Confusion**: Using `Boolean` wrapper when `boolean` primitive would suffice
4. **Verbose Null Checks**: Can be simplified with modern Java features
5. **Early Return Principle**: Not using early returns to reduce nesting

**Refactored Version:**

```java
public static boolean isValidUser(User user) {
    if (user == null) {
        return false;
    }
    
    String name = user.getName();
    if (name == null || name.trim().isEmpty()) {
        return false;
    }
    
    Integer age = user.getAge();
    if (age == null || age < 18) {
        return false;
    }
    
    Boolean isActive = user.getIsActive();
    return isActive != null && isActive;
}

// Alternative: More concise with Optional (Java 8+)
public static boolean isValidUserOptional(User user) {
    return Optional.ofNullable(user)
        .map(User::getName)
        .filter(name -> !name.trim().isEmpty())
        .isPresent() &&
        Optional.ofNullable(user.getAge())
        .filter(age -> age >= 18)
        .isPresent() &&
        Boolean.TRUE.equals(user.getIsActive());
}
```

**Key Improvements:**
- **Early Returns**: Exit immediately when conditions fail
- **Reduced Nesting**: Maximum of one level deep
- **Guard Clauses**: Check invalid conditions first
- **Boolean Logic**: Direct return of boolean expressions
- **Optional Usage**: Modern approach for null-safe operations

**Primitive vs Wrapper Considerations:**
- `boolean` (primitive): Cannot be null, more efficient
- `Boolean` (wrapper): Can be null, useful for database mappings
- Use `Boolean.TRUE.equals(booleanWrapper)` for null-safe boolean checks

---

## 8. String Immutability in Java

### Question
Explain string immutability in Java. Why are strings immutable? What are the advantages and disadvantages?

### Answer

**What is String Immutability?**
Once a `String` object is created, its value cannot be changed. Any operation that appears to modify a string actually creates a new string object.

```java
String s = "Hello";
s.concat(" World"); // Creates new string "Hello World", s still "Hello"
s = s.concat(" World"); // Now s points to new string
```

**Why Strings are Immutable:**
1. **Security**: Strings are used for sensitive data (passwords, file paths, network connections)
2. **Thread Safety**: Multiple threads can safely share string instances
3. **Caching**: String literals are cached in the String Pool for memory efficiency
4. **Hash Code Caching**: Immutable objects can cache hash codes

**Advantages:**
- **Thread Safety**: No synchronization needed for string operations
- **Security**: Prevents accidental or malicious modification
- **Performance**: String Pool reduces memory usage
- **HashMap Keys**: Safe to use as keys since hash code won't change

**Disadvantages:**
- **Memory Overhead**: Each modification creates new objects
- **Performance**: Frequent modifications can create many temporary objects
- **Garbage Collection**: More objects to clean up

**StringBuilder vs StringBuffer:**
```java
// For single-threaded, mutable string operations
StringBuilder sb = new StringBuilder("Hello");
sb.append(" World"); // Modifies same object
String result = sb.toString();

// For multi-threaded, thread-safe mutable operations
StringBuffer sbuf = new StringBuffer("Hello");
sbuf.append(" World"); // Thread-safe
```

**Key Learning Points:**
- Use `StringBuilder` for concatenations in loops
- String Pool optimization for memory
- Security implications of immutability
- Performance trade-offs between `String`, `StringBuilder`, and `StringBuffer`

---

## 9. HashMap vs HashTable

### Question
Compare HashMap and HashTable in Java. What are the key differences, advantages, and when to use each?

### Answer

**Key Differences:**

| Feature | HashMap | HashTable |
|---------|---------|-----------|
| **Synchronization** | Not synchronized | Synchronized |
| **Null Keys/Values** | Allows one null key, multiple null values | Doesn't allow null keys or values |
| **Performance** | Faster (no synchronization overhead) | Slower due to synchronization |
| **Thread Safety** | Not thread-safe | Thread-safe |
| **Introduced** | Java 1.2 | Java 1.0 (legacy) |

**HashMap Example:**
```java
Map<String, Integer> hashMap = new HashMap<>();
hashMap.put("Alice", 25);
hashMap.put("Bob", 30);
hashMap.put(null, 0); // OK
hashMap.put("Charlie", null); // OK
```

**HashTable Example:**
```java
Map<String, Integer> hashTable = new HashTable<>();
hashTable.put("Alice", 25);
hashTable.put("Bob", 30);
// hashTable.put(null, 0); // Throws NullPointerException
// hashTable.put("Charlie", null); // Throws NullPointerException
```

**ConcurrentHashMap (Modern Alternative):**
```java
Map<String, Integer> concurrentMap = new ConcurrentHashMap<>();
// Better performance than HashTable for concurrent operations
// Allows null values but not null keys
```

**When to Use:**
- **HashMap**: Single-threaded applications, better performance
- **HashTable**: Legacy code requiring synchronization (rarely used now)
- **ConcurrentHashMap**: Multi-threaded applications needing thread safety

**Key Learning Points:**
- HashTable is legacy; prefer ConcurrentHashMap for thread safety
- HashMap allows nulls, others generally don't
- Performance: HashMap > ConcurrentHashMap > HashTable
- Use Collections.synchronizedMap() to make HashMap thread-safe if needed

---

## 10. Exception Handling Best Practices

### Question
Discuss Java exception handling best practices. What are checked vs unchecked exceptions? Provide examples of proper exception handling.

### Answer

**Checked vs Unchecked Exceptions:**

**Checked Exceptions:**
- Must be declared in method signature or handled
- Represent recoverable conditions
- Examples: `IOException`, `SQLException`

**Unchecked Exceptions:**
- Don't need to be declared or caught
- Represent programming errors
- Examples: `NullPointerException`, `IllegalArgumentException`

**Exception Hierarchy:**
```
Throwable
├── Exception (Checked)
│   ├── IOException
│   ├── SQLException
│   └── Custom checked exceptions
└── RuntimeException (Unchecked)
    ├── NullPointerException
    ├── IllegalArgumentException
    └── Custom unchecked exceptions
```

**Best Practices:**

```java
public class ExceptionHandlingExample {
    
    // 1. Use specific exceptions
    public User findUserById(String id) throws UserNotFoundException {
        if (id == null) {
            throw new IllegalArgumentException("ID cannot be null");
        }
        
        User user = userRepository.findById(id);
        if (user == null) {
            throw new UserNotFoundException("User not found: " + id);
        }
        return user;
    }
    
    // 2. Don't catch generic Exception
    public void processFile(String filePath) {
        try (BufferedReader reader = new BufferedReader(new FileReader(filePath))) {
            String line;
            while ((line = reader.readLine()) != null) {
                processLine(line);
            }
        } catch (IOException e) {
            // Specific exception handling
            logger.error("Failed to process file: " + filePath, e);
            throw new FileProcessingException("Could not process file", e);
        }
    }
    
    // 3. Use try-with-resources for AutoCloseable
    public String readFile(String path) throws IOException {
        try (FileInputStream fis = new FileInputStream(path);
             InputStreamReader isr = new InputStreamReader(fis);
             BufferedReader br = new BufferedReader(isr)) {
            return br.lines().collect(Collectors.joining("\n"));
        }
    }
    
    // 4. Don't suppress exceptions
    public void riskyOperation() {
        try {
            performRiskyOperation();
        } catch (Exception e) {
            logger.error("Operation failed", e);
            // Don't just swallow the exception!
            throw new RuntimeException("Operation failed", e);
        }
    }
}

// Custom exception
class UserNotFoundException extends Exception {
    public UserNotFoundException(String message) {
        super(message);
    }
}

class FileProcessingException extends RuntimeException {
    public FileProcessingException(String message, Throwable cause) {
        super(message, cause);
    }
}
```

**Key Principles:**
- **Fail Fast**: Throw exceptions early for invalid inputs
- **Don't Catch Generic Exception**: Be specific about what you catch
- **Preserve Stack Trace**: Use exception chaining with `initCause()`
- **Log and Re-throw**: Log exceptions but don't lose the original stack trace
- **Use Try-with-Resources**: Automatic resource management
- **Document Exceptions**: Use `@throws` in JavaDoc

**Key Learning Points:**
- Checked exceptions for recoverable errors, unchecked for programming errors
- Prefer unchecked exceptions for business logic violations
- Always clean up resources properly
- Don't hide exceptions - they provide valuable debugging information

---

## Summary

These ten problems test different Java competencies:
1. **Bug Identification**: Type safety, understanding method signatures
2. **Calculation Problem**: Arithmetic, data structures, rounding/precision
3. **Logic Problem**: Collections, Map/Set usage, grouping and filtering data
4. **String Decoding**: Stack/Recursion, string manipulation, nested data structures
5. **Effective Final**: Java 8+ features, lambda expressions, variable capture
6. **Generics Wildcards**: PECS principle, type bounds, generic constraints
7. **Clean Code**: Code readability, refactoring, primitive vs wrapper types
8. **String Immutability**: Memory management, performance, security implications
9. **Collections**: HashMap vs HashTable, thread safety, performance trade-offs
10. **Exception Handling**: Error management, checked vs unchecked, best practices

All are common in real-world development and demonstrate practical problem-solving skills.

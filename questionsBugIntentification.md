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

## Summary

These four problems test different Java competencies:
1. **Bug Identification**: Type safety, understanding method signatures
2. **Calculation Problem**: Arithmetic, data structures, rounding/precision
3. **Logic Problem**: Collections, Map/Set usage, grouping and filtering data
4. **String Decoding**: Stack/Recursion, string manipulation, nested data structures

All are common in real-world development and demonstrate practical problem-solving skills.

# Ping Pong TDD

*Ping pong* is one of the most basic activities used when teaching Test Driven Development (TDD) . The rules are simple:

- Work in pairs
- Person A only writes tests (test code only)
- Person B only tries to get the tests to pass (production code only)
- Both persons refactor when appropriate

During the activity, it a good idea to ask participants to switch roles. Person B becomes the test writer, and Person A becomes the test taker (or the person who gets the tests to pass).

This Lab shows how TDD can be easy and fun by combining it with pair programming. As if you were playing table tennis, in ping pong TDD you create a test for your partner to pass, and then he creates one for you.

 

This exercise is to create a unit of code that converts Roman numbers into Arabic numerals. Were going to show how the development proceeds when we work in the smallest possible increments

Player 1:

```java
@Test
public void testNumber1() {
    assertEquals(1,RomanConverter.Convert("I"));
}

```

Player 2:

```java
public class RomanConverter {
        public static int Convert(String s) 
        {
            return 1;
        }
}

@Test
public void testNumber2() {
    assertEquals(2,RomanConverter.Convert("II"));
}

```

Player 1:

```java
public class RomanConverter {
    public static int Convert(String s) 
    {
        return s.length();
    }
}

@Test
public void testNumber5() {
    assertEquals(5,RomanConverter.Convert("V"));
}

```

Player 2:

```java
public class RomanConverter {
    public static int Convert(String s) 
    {
        if (s.equals("V") {
            return 5;
        }
        else {
            return s.length();
        }
    }
}

@Test
public void testTwoDifferentNumbers() {
    assertEquals(6,RomanConverter.Convert("VI"));
}

```

Player 1:

```java
public class RomanConverter {
    public static int Convert(String s) 
    {
        int sum = 0;
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == 'I') {
                sum += 1;
            }
            else {
                sum+= 5;
            }
        }
    return sum;
}


@Test
public void testSmallchar() {
    assertEquals(6,RomanConverter.Convert("vi");
}

```

Continue until the final solution (with RegEx).

# 

# Roman Numeral Rules

 

| **Symbol** | **Meaning**      | **Decimal** |
| ---------- | ---------------- | ----------- |
| **I**      | **One**          | **1**       |
| **V**      | **Five**         | **5**       |
| **X**      | **Ten**          | **10**      |
| **L**      | **Fifty**        | **50**      |
| **C**      | **One Hundred**  | **100**     |
| **D**      | **Five Hundred** | **500**     |
| **M**      | **One Thousand** | **1000**    |

 

**Roman numerals**** follow this pattern….****   **

| **Units**     | **I** | **II** | **III** | **IV** | **V** | **VI** | **VII** | **VIII** | **IX** |
| ------------- | ----- | ------ | ------- | ------ | ----- | ------ | ------- | -------- | ------ |
| **Tens**      | **X** | **XX** | **XXX** | **XL** | **L** | **LX** | **LXX** | **LXXX** | **XC** |
| **Hundreds**  | **C** | **CC** | **CCC** | **CD** | **D** | **DC** | **DCC** | **DCCC** | **CM** |
| **Thousands** | **M** | **MM** | **MMM** | **IV** | **V** | **VI** | **VII** | **VIII** | **IX** |

 

**The Additive System**

The system for writing Roman numerals is an additive system. This means to calculate the value of a Roman numeral, you simply to have to add up all the digits in the Roman numeral.

Roman Numerals are always written from Largest to Smallest – Left to Right.

#### Examples:

VIII = 5 + 1 + 1 + 1 = 8

XXXIII = 10 + 10 + 10 + 1 + 1 + 1 = 33

CLXXVIII = 100 + 50 + 10 + 10 + 5 + 1 + 1 + 1 = 178

MMX = 1,000 + 1,000 + 10 = 2,010
**SUBTRACTIVE SYSTEM**You can write **one** smaller numeral to the left of a larger one to subtract from it! The table below has all the allowed subtractive combinations

| **Roman Numeral** | **Value** | **Equivalent** |
| ----------------- | --------- | -------------- |
| **IV**            | **4**     | **IIII**       |
| **IX**            | **9**     | **VIIII**      |
| **XL**            | **40**    | **XXXX**       |
| **XC**            | **90**    | **LXXXX**      |
| **CD**            | **400**   | **CCCC**       |
| **CM**            | **900**   | **DCCCC**      |

 

# Solution

```java
import static org.junit.Assert.*;

import org.junit.Test;
import org.junit.rules.ExpectedException;

public class testping {

 @Test
 public void testNumber1() {
  assertEquals(1, RomanConverter.Convert("I"));
 }

 @Test
 public void testNumber2() {
  assertEquals(2, RomanConverter.Convert("II"));
 }

 @Test
 public void testNumber5() {
  assertEquals(5, RomanConverter.Convert("V"));
 }

 @Test
 public void test2DifferentNumber() {
  assertEquals(6, RomanConverter.Convert("VI"));
 }

 @Test
 public void testSmallchar() {
  assertEquals(6, RomanConverter.Convert("vi"));
 }

 @Test
 public void testX() {
  assertEquals(10, RomanConverter.Convert("x"));
 }

 @Test
 public void testL() {
  assertEquals(50, RomanConverter.Convert("l"));
 }

 @Test
 public void testC() {
  assertEquals(100, RomanConverter.Convert("C"));
 }

 @Test
 public void testD() {
  assertEquals(500, RomanConverter.Convert("D"));
 }

 @Test
 public void testM() {
  assertEquals(1000, RomanConverter.Convert("M"));
 }

 @Test(expected = RuntimeException.class)
 public void testA() {
  RomanConverter.Convert("A");
 }

 @Test(expected = RuntimeException.class)
 public void testNull() {
  RomanConverter.Convert("");
 }

 @Test
 public void testNumber4() {
  assertEquals(4, RomanConverter.Convert("IV"));
 }

 @Test
 public void testNumber40() {
  assertEquals(40, RomanConverter.Convert("XL"));
 }

 @Test
 public void testNumber9() {
  assertEquals(9, RomanConverter.Convert("IX"));
 }

 @Test
 public void testNumber90() {
  assertEquals(90, RomanConverter.Convert("XC"));
 }

 @Test
 public void testNumber400() {
  assertEquals(400, RomanConverter.Convert("CD"));
 }

 @Test
 public void testNumber999() {
  assertEquals(999, RomanConverter.Convert("CMXCIX"));
 }

 @Test(expected = RuntimeException.class)
 public void testInvalidRepetition() {
  RomanConverter.Convert("IIII");
 }
}


import java.util.regex.Pattern;

public class RomanConverter {
 public static int Convert(String s) {
  int sum = 0;
  if (s == null || s.length() == 0) {
   throw new RuntimeException("Invalid roman number");
  }

  Pattern pattern = Pattern.compile("([a-z\\d])\\1\\1\\1", Pattern.CASE_INSENSITIVE);
  if (pattern.matcher(s).find()) {
   throw new RuntimeException("Invalid roman number");
  }

  s = s.toUpperCase();
  s = s.replaceAll("IV", "IIII");
  s = s.replaceAll("IX", "VIIII");
  s = s.replaceAll("XL", "XXXX");
  s = s.replaceAll("XC", "LXXXX");
  s = s.replaceAll("CD", "CCCC");
  s = s.replaceAll("CM", "DCCCC");


  for (int i = 0; i < s.length(); i++) {
   switch (s.charAt(i)) {
    case 'I':
     sum += 1;
     break;
    case 'V':
     sum += 5;
     break;
    case 'X':
     sum += 10;
     break;
    case 'L':
     sum += 50;
     break;
    case 'C':
     sum += 100;
     break;
    case 'D':
     sum += 500;
     break;
    case 'M':
     sum += 1000;
     break;
    default:
     throw new RuntimeException("Invalid roman number");
   }

  }
  return sum;
 }
}
```
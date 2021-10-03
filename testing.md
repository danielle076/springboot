# Unit Testing & Mocking

Wanneer je alleen met Java werkt, is Unit Testing met JUnit voldoende. Echter met Spring Boot is Unit Testing niet
genoeg, je maakt gebruik van een uitgebreidere testing mogelijkheid namelijk mocking met Mockito.

## Unit Testing

Arrange, Act and Assert (AAA) Pattern is de standaard manier om een Unit Test te schrijven.

Het verdeelt de tests in drie duidelijke en afzonderlijke stappen:
1. Opstellen (arrange): Voer de setup en initialisatie uit die nodig zijn voor de test.
2. Actie (act): Onderneem actie(s) die nodig zijn voor de test.
3. Bevestig (assert): Verifieer de uitkomst(en) van de test.

### Code

`Counter.java` is geen entity, maar gewoon een klasse. Hij heeft een `total` en iedere keer wanneer je zegt `add` telt
hij het getal bij de `total` op. Het opvragen van het getal doe je met `getTotal`.

_Counter.java_

```java
import java.util.Arrays;

public class Counter {

    private int total = 0;

    public void add(int getal) {
        total += getal;
    }

    public void add(int[] numbers) {
        total = this.addWithLoop(numbers);
    }

    public static int addWithLoop(int[] numbers) {
        int sum = 0;
        for (int number : numbers) {
            sum += number;
        }
        return sum;
    }

    public static int addWithStream(int[] numbers) {
        return Arrays.stream(numbers).sum();
    }

    public void reset() {
        total = 0;
    }

    public int getTotal() {
        return total;
    }

    public static int getSmallestNumber(int[] numbers) {
        int minimum = numbers[0];
        for (int number : numbers) {
            if (number < minimum) {
                minimum = number;
            }
        }
        return minimum;
    }

    public static int getMaxWithStream(int[] numbers) {
        return Arrays.stream(numbers).max().orElse(0);
    }
}
```

We schrijven een `CounterMain` die met `Counter.java` gaat werken.

We maken een nieuw Java project met niks erin. We maken een nieuwe package genaamd `main`. Hierin kopiëren
we `Counter.java` en maken een nieuwe Java file `CounterMain.java`.

_CounterMain.java_

```java
public class CounterMain {
    public static void main(String[] args) {
        System.out.println("Hello World");
    }
}
```

Run de applicatie.

We gaan `Counter.java` gebruiken in `CounterMain.java`.

We maken eerst een counter instantie met `Counter counter = new Counter();`.

Tegen die `counter` zeggen we add 1 door `counter.add(1);`

_CounterMain.java_

```java
public class CounterMain {
    public static void main(String[] args) {

        Counter counter = new Counter();

        counter.add(1);
        counter.add(2);
        counter.add(3);

        System.out.println("Total: " + counter.getTotal());
    }
}
```

Run de applicatie.

Wat we nu in `CounterMain` hebben gedaan is een test geschreven, om te kijken of hij werkt. Dit wil je natuurlijk niet
in `CounterMain` doen.

### Testing

We maken een nieuwe package aan genaamd `test`. We maken een nieuwe file aan `CounterTest.java`, deze hoort
bij `Counter.java`.

We gaan een test schrijven met `@Test`. We beginnen met het toevoegen van `Add 'JUnit5' to classpath`.

_CounterTest.java_

```java
import org.junit.jupiter.api.Test;

public class CounterTest {

    @Test
    public void EerstTest() {

    }
}
```

Van de `Assert` opdrachten gaan we gebruik maken van `assertEquals`. De `assertEquals` kijkt naar de uitkomst van `som`
en kijkt of dit gelijk is aan `expected`.

```java
import org.junit.jupiter.api.Test;

import static org.junit.Assert.assertEquals;

public class CounterTest {

    @Test
    public void EerstTest() {
        // arrange
        int som;

        // act
        som = 34 + 56;

        // assert
        int expected = 90;
        assertEquals(expected, som);
    }
}
```

Run de test.

Je kan meerdere testen schrijven.

_CounterTest.java_

```java
import org.junit.jupiter.api.Test;

import static org.junit.Assert.assertEquals;

public class CounterTest {

    @Test
    public void EerstTest() {
        // arrange
        int som;

        // act
        som = 34 + 56;

        // assert
        int expected = 90;
        assertEquals(expected, som);
    }

    @Test
    public void TweedeTest() {
        // arrange
        int min;

        // act
        min = 12 - 6;

        // assert
        int expected = 6;
        assertEquals(expected, min);
    }

    @Test
    public void DerdeTest() {
        // arrange
        String s = "abcde";

        // act
        String actual = s.toUpperCase();

        // assert
        String expected = "ABCDE";
        assertEquals(expected, actual);
    }
}
```

We hebben nu drie standaard Java testen gedaan.

Stel we willen hetgeen wat in `CounterMain.java` staat in een `@Test`. 

    public void add(int getal) {
        total += getal;
    }

We noemen deze test `CounterTestAdd`.

```java
import org.junit.jupiter.api.Test;
import main.Counter;
import static org.junit.Assert.assertEquals;

public class CounterTest {

    @Test
    public void CounterTestAdd() {
        // arrange
        Counter counter = new Counter();

        // act
        counter.add(1);
        counter.add(2);
        counter.add(3);

        // assert
        int expected = 6;
        assertEquals(expected, counter.getTotal());
    }
}
```

We gaan het optellen met een array testen die in `Counter.java` staat.

    public void add(int[] numbers) {
        total = this.addWithLoop(numbers);
    }

```java
import org.junit.jupiter.api.Test;
import main.Counter;
import static org.junit.Assert.assertEquals;

public class CounterTest {

    @Test
    public void CounterTestAddArray() {
        // arrange
        Counter counter = new Counter();
        int[] numbers = new int[] {1,2,3};

        // act
        counter.add(numbers);

        // assert
        int expected = 6;
        assertEquals(expected, counter.getTotal());
    }
}
```

We hebben nu `{1, 2, 3}` getest, maar wat je ook zou kunnen testen is wat als de array leeg is, wat gebeurd er dan. Of wat gebeurd er als de array heel groot is, of wat gebeurd er wanneer er floats inzitten. Je kan eindeloos hoeveelheid variaties bedenken en testen.

Het kan dus gebeuren dat de code die je test groter wordt dan de code zelf, je schiet het doel een beetje voorbij. Wat je dan kunt doen is een coverage maken, waarbij je zegt, ik heb de belangrijke stukken van de code getest. Je hoeft dus niet alle scenario's te doen.

We gaan nog een test doen van `reset`.

    public void reset() {
        total = 0;
    }

_CounterTest.java_

```java
import org.junit.jupiter.api.Test;
import main.Counter;
import static org.junit.Assert.assertEquals;

public class CounterTest {

    @Test
    public void CounterTestReset() {
        // arrange
        Counter counter = new Counter();
        int[] numbers = new int[] {1,2,3};

        // act
        counter.add(numbers);
        counter.reset();
        int actual = counter.getTotal();

        // assert
        int expected = 0;
        assertEquals(expected, actual);
    }
}
```

### Starten met test: test driven development

Stel we hebben nog geen methode in `Counter.java` om de kleinste getallen op te halen, maar we willen deze test al wel in `CounterTest.java` zetten.

```java
import main.Counter;
import org.junit.jupiter.api.Test;

import static org.junit.Assert.assertEquals;

public class CounterTest {

    @Test
    public void CounterTestMaximum() {
        // arrange
        Counter counter = new Counter();
        int[] numbers = new int[] {1,2,3};

        // act
        counter.add(numbers);
        int actual = counter.getMaximum(numbers);

        // assert
        int expected = 3;
        assertEquals(expected, actual);
    }
}
```

De methode `getMaximum()` is rood, klik erop en voer `Create method 'getMaximum' in 'Counter'` uit. De methode wordt aangemaakt in `Counter.java`.

    public int getMaximum() {
        return 0;
    }

We passen de methode aan, zodat hij iets doet.

    public int getMaximum(int[] numbers) {
        return getMaxWithStream(numbers);
    }

Run de test.

### Github

De volledige code is [hier](https://github.com/danielle076/demo_counter_test) op github te vinden.

# Creating Reactive Sequence
> Creating reactive sequences in Project Reactor involves using classes like Flux and Mono to generate streams of data. Flux represents a sequence of 0 to N items, and Mono represents a sequence of 0 or 1 item. Here are some common ways to create reactive sequences using these classes:

## Creating a Flux
### From a List or Array:

```
List<String> list = Arrays.asList("a", "b", "c");
Flux<String> fluxFromList = Flux.fromIterable(list);

String[] array = {"a", "b", "c"};
Flux<String> fluxFromArray = Flux.fromArray(array);
```
### Using a Range:

```
Flux<Integer> rangeFlux = Flux.range(1, 5); // emits 1, 2, 3, 4, 5
```
### From a Stream:

```
Stream<String> stream = Stream.of("a", "b", "c");
Flux<String> fluxFromStream = Flux.fromStream(stream);
```
### From a Supplier:

```
Flux<String> fluxFromSupplier = Flux.generate(sink -> {
    sink.next("Hello");
    sink.complete();
});
```
### From an Interval:

```
Flux<Long> intervalFlux = Flux.interval(Duration.ofSeconds(1)); // emits a sequence of longs every second
```
### Creating a Flux Manually:

```
Flux<Integer> manualFlux = Flux.create(sink -> {
    sink.next(1);
    sink.next(2);
    sink.next(3);
    sink.complete();
});
```
## Creating a Mono
### From a Single Value:

```
Mono<String> monoJust = Mono.just("Hello");
```
### From an Empty Mono:

```
Mono<String> emptyMono = Mono.empty();
```
### From a Callable or Supplier:

```
Mono<String> monoFromSupplier = Mono.fromSupplier(() -> "Hello");
```
### From a CompletableFuture:

```
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> "Hello");
Mono<String> monoFromFuture = Mono.fromFuture(future);
```
## Examples
> Here are some examples demonstrating how to create and subscribe to these sequences:

### Example 1: Flux from a List
```
import reactor.core.publisher.Flux;

public class FluxExample {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("a", "b", "c");
        Flux<String> fluxFromList = Flux.fromIterable(list);

        fluxFromList.subscribe(item -> System.out.println("Received: " + item));
    }
}
```
#### Output:

```
Received: a
Received: b
Received: c
```
### Example 2: Flux from a Range
```
import reactor.core.publisher.Flux;

public class RangeExample {
    public static void main(String[] args) {
        Flux<Integer> rangeFlux = Flux.range(1, 5);

        rangeFlux.subscribe(item -> System.out.println("Received: " + item));
    }
}
```
#### Output:

```
Received: 1
Received: 2
Received: 3
Received: 4
Received: 5
```
### Example 3: Mono from a Value
```
import reactor.core.publisher.Mono;

public class MonoExample {
    public static void main(String[] args) {
        Mono<String> monoJust = Mono.just("Hello");

        monoJust.subscribe(item -> System.out.println("Received: " + item));
    }
}
```
#### Output:

```
Received: Hello
```
### Example 4: Flux with Interval

```
import reactor.core.publisher.Flux;

import java.time.Duration;

public class IntervalExample {
    public static void main(String[] args) {
        Flux<Long> intervalFlux = Flux.interval(Duration.ofSeconds(1));

        intervalFlux.subscribe(item -> System.out.println("Received: " + item));

        // Keep the application running to observe the emitted items
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```
#### Output:

```
Received: 0
Received: 1
Received: 2
Received: 3
Received: 4
```
### Conclusion
> These examples illustrate various ways to create reactive sequences using Flux and Mono in Project Reactor. You can create sequences from collections, ranges, intervals, suppliers, and more, depending on your use case. By subscribing to these sequences, you can process and react to the emitted items as they are produced.

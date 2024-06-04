# subscribe

```
subscribe(); 

subscribe(Consumer<? super T> consumer); 

subscribe(Consumer<? super T> consumer,
          Consumer<? super Throwable> errorConsumer); 

subscribe(Consumer<? super T> consumer,
          Consumer<? super Throwable> errorConsumer,
          Runnable completeConsumer); 

subscribe(Consumer<? super T> consumer,
          Consumer<? super Throwable> errorConsumer,
          Runnable completeConsumer,
          Consumer<? super Subscription> subscriptionConsumer);
```
> In Project Reactor, the subscribe methods are used to subscribe to a Publisher, such as a Flux or Mono. Here is a detailed explanation of each subscribe method and what the generic type T represents:

### 1. subscribe()
This method subscribes to the Publisher without any consumers to handle signals. It initiates the subscription and typically does nothing when elements are emitted, an error occurs, or the stream completes.

### 2. subscribe(Consumer<? super T> consumer)
#### Parameter: consumer - a Consumer that processes each emitted item.
> Usage: This method subscribes to the Publisher and processes each emitted item using the provided consumer.

```
Flux<String> flux = Flux.just("Hello", "World");
flux.subscribe(item -> System.out.println(item));
```
Here, the consumer processes each emitted item ("Hello" and "World").
### 3. subscribe(Consumer<? super T> consumer, Consumer<? super Throwable> errorConsumer)
#### Parameters:
- consumer - processes each emitted item.
- errorConsumer - handles any error that occurs.
> Usage: This method subscribes to the Publisher, processes each emitted item using the consumer, and handles any error using the errorConsumer.
```
Flux<String> flux = Flux.just("Hello", "World").concatWith(Flux.error(new RuntimeException("Error")));
flux.subscribe(
    item -> System.out.println(item),
    error -> System.err.println("Error: " + error.getMessage())
);
```
Here, the errorConsumer handles the error "Error".
### 4. subscribe(Consumer<? super T> consumer, Consumer<? super Throwable> errorConsumer, Runnable completeConsumer)
#### Parameters:
- consumer - processes each emitted item.
- errorConsumer - handles any error that occurs.
- completeConsumer - runs when the stream completes.
> Usage: This method subscribes to the Publisher, processes each emitted item using the consumer, handles any error using the errorConsumer, and runs the completeConsumer when the stream completes.
```
Flux<String> flux = Flux.just("Hello", "World");
flux.subscribe(
    item -> System.out.println(item),
    error -> System.err.println("Error: " + error.getMessage()),
    () -> System.out.println("Completed")
);
```
> Here, the completeConsumer prints "Completed" when the stream finishes emitting items.
### 5. subscribe(Consumer<? super T> consumer, Consumer<? super Throwable> errorConsumer, Runnable completeConsumer, Consumer<? super Subscription> subscriptionConsumer)
##### Parameters:
- consumer - processes each emitted item.
- errorConsumer - handles any error that occurs.
- completeConsumer - runs when the stream completes.
- subscriptionConsumer - receives the Subscription and allows for requesting items or canceling the subscription.
> Usage: This method subscribes to the Publisher, processes each emitted item using the consumer, handles any error using the errorConsumer, runs the completeConsumer when the stream completes, and processes the subscription using the subscriptionConsumer.
```
Flux<String> flux = Flux.just("Hello", "World");
flux.subscribe(
    item -> System.out.println(item),
    error -> System.err.println("Error: " + error.getMessage()),
    () -> System.out.println("Completed"),
    subscription -> subscription.request(2)
);
```
> Here, the subscriptionConsumer requests 2 items from the Publisher.

## Code Explanation
```
Flux<Integer> ints = Flux.range(1, 4)
    .map(i -> {
        if (i <= 3) return i;
        throw new RuntimeException("Got to 4");
    });

ints.subscribe(i -> System.out.println(i),
    error -> System.err.println("Error: " + error));
Detailed Breakdown
Flux.range(1, 4)
```
> This creates a Flux that emits a sequence of integers starting from 1 and ending at 4 (inclusive). The sequence generated is [1, 2, 3, 4].
### .map(i -> { ... })

- The map operator transforms each item emitted by the Flux. It applies the provided lambda function to each item.
### Lambda function:
```
i -> {
    if (i <= 3) return i;
    throw new RuntimeException("Got to 4");
}
```
- If i is less than or equal to 3, it returns i as is.
- If i is 4, it throws a RuntimeException with the message "Got to 4".
- This transformation results in a modified Flux where the items are passed through the lambda function. For the sequence [1, 2, 3, 4], the lambda function will return 1, 2, 3, and throw an exception on 4.
### ints.subscribe(i -> System.out.println(i), error -> System.err.println("Error: " + error))

- This subscribes to the Flux and provides two consumers:
  - First consumer (i -> System.out.println(i)):
  - This handles the emitted items. It prints each item to the standard output.
- Second consumer (error -> System.err.println("Error: " + error)):
  - This handles errors. It prints the error message to the standard error output.
### Execution Flow
- The Flux emits the sequence [1, 2, 3, 4].
- The map operator processes each item:
  - For 1, 2, and 3, the lambda function returns the item itself, so these items are passed through unchanged.
  - For 4, the lambda function throws a RuntimeException.
- The subscribe method starts the subscription and begins processing the emitted items:
  - The items 1, 2, and 3 are printed to the standard output.
  - When the item 4 is processed, the lambda function throws an exception, causing the error consumer to handle it.
- The error consumer prints the error message to the standard error output.
#### Output
```
1
2
3
Error: java.lang.RuntimeException: Got to 4
```
- The numbers 1, 2, and 3 are printed normally.
- The exception caused by 4 is caught and its message is printed as an error.
> In summary, this example demonstrates how to create a Flux that emits a range of integers, transform the items using the map operator, and handle both the emitted items and potential errors using the subscribe method.

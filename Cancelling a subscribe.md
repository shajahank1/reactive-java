# Cancelling a subscribe() with Its Disposable
> In Project Reactor, when you subscribe to a Flux or Mono, you receive a Disposable object that allows you to cancel the subscription. This is useful if you need to stop processing the stream before it completes, either due to some condition being met or because the subscription is no longer needed.

## Example of Cancelling a Subscription with Disposable
> Here's an example that demonstrates how to cancel a subscription using the Disposable object:

```
import reactor.core.publisher.Flux;
import reactor.core.Disposable;

public class DisposableExample {
    public static void main(String[] args) throws InterruptedException {
        Flux<Integer> flux = Flux.range(1, 10)
            .map(i -> {
                System.out.println("Processing item: " + i);
                return i;
            });

        Disposable disposable = flux.subscribe(
            i -> System.out.println("Received item: " + i),
            error -> System.err.println("Error: " + error),
            () -> System.out.println("Completed")
        );

        // Simulate some processing time before cancellation
        Thread.sleep(500);
        
        // Cancel the subscription
        disposable.dispose();
        
        System.out.println("Subscription cancelled");
    }
}
```
### Explanation
- Creating the Flux:

```
Flux<Integer> flux = Flux.range(1, 10)
    .map(i -> {
        System.out.println("Processing item: " + i);
        return i;
    });
```

> A Flux is created that emits integers from 1 to 10.
> The map operator prints each item as it processes it.

#### Subscribing to the Flux:

```
Disposable disposable = flux.subscribe(
    i -> System.out.println("Received item: " + i),
    error -> System.err.println("Error: " + error),
    () -> System.out.println("Completed")
);
```
> The subscribe method subscribes to the Flux and provides consumers for handling emitted items, errors, and completion.
> The subscribe method returns a Disposable object, which is stored in the variable disposable.
#### Simulating Processing Time:

```
Thread.sleep(500);
The Thread.sleep(500) simulates some processing time before the subscription is cancelled.
```
#### Cancelling the Subscription:

```
disposable.dispose();
```
> The dispose method is called on the Disposable object to cancel the subscription.
> After calling dispose, no more items will be processed or emitted.
#### Printing the Cancellation Message:

```
System.out.println("Subscription cancelled");
```
### Expected Output
The output will show that items are being processed and received until the subscription is cancelled:
```
plaintext
Copy code
Processing item: 1
Received item: 1
Processing item: 2
Received item: 2
Processing item: 3
Received item: 3
Processing item: 4
Received item: 4
Processing item: 5
Received item: 5
Processing item: 6
Subscription cancelled
```
- The first few items (1 to 5 or 6) are processed and received.
- After about 500 milliseconds, the subscription is cancelled.
- The message "Subscription cancelled" is printed.
- Processing stops after the cancellation, so items after 6 are not processed or received.
> This example demonstrates how to use the Disposable object to cancel a subscription, which can be useful in various scenarios where you need to stop the stream based on certain conditions or external factors.

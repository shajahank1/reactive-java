# Handling Errors
>If you want the stream to continue emitting items even after an error occurs, you can use operators that handle errors and allow the stream to continue. In Project Reactor, you can use operators like onErrorResume, onErrorReturn, or onErrorContinue. These operators help manage errors and provide fallback mechanisms.

## Using onErrorResume
> The onErrorResume operator allows you to provide an alternative sequence to emit if an error occurs.

```
Flux<Integer> ints = Flux.range(1, 10)
    .map(i -> {
        if (i == 2) throw new RuntimeException("Error at 2");
        return i;
    })
    .onErrorResume(e -> Flux.just(-1, -2));

ints.subscribe(i -> System.out.println(i),
    error -> System.err.println("Error: " + error));
```

### Code Explanation
#### Flux.range(1, 10)

- This creates a Flux that emits a sequence of integers starting from 1 and ending at 10 (inclusive). The sequence generated is [1, 2, 3, 4, 5, 6, 7, 8, 9, 10].
#### .map(i -> { ... })

- The map operator transforms each item emitted by the Flux. It applies the provided lambda function to each item.
#### Lambda function:
```
i -> {
    if (i == 2) throw new RuntimeException("Error at 2");
    return i;
}
```
    - If i is 2, it throws a RuntimeException with the message "Error at 2".
    - For other values of i, it returns the item itself.
#### .onErrorResume(e -> Flux.just(-1, -2))

- The onErrorResume operator provides an alternative Flux sequence ([-1, -2]) to emit if an error occurs.
#### ints.subscribe(i -> System.out.println(i), error -> System.err.println("Error: " + error))

- This subscribes to the Flux and provides two consumers:
    - First consumer (i -> System.out.println(i)):
        - This handles the emitted items. It prints each item to the standard output.
    - Second consumer (error -> System.err.println("Error: " + error)):
        - This handles errors. It prints the error message to the standard error output.
### Execution Flow
- The Flux emits the sequence [1, 2, 3, 4, 5, 6, 7, 8, 9, 10].
- The map operator processes each item:
    - For 1, the lambda function returns 1, so it is emitted.
    - For 2, the lambda function throws a RuntimeException with the message "Error at 2".
- The onErrorResume operator catches the error and provides the alternative sequence [-1, -2].
- The subscribe method starts the subscription and processes the emitted items:
    - The item 1 is printed to the standard output.
    - When the error occurs at 2, the alternative sequence [-1, -2] is emitted.
- The items -1 and -2 are printed to the standard output.
```
1
-1
-2
```
- The number 1 is printed normally.
- When the error occurs at 2, the onErrorResume provides the alternative sequence [-1, -2].
- The alternative sequence [-1, -2] is printed.
- No further items from the original sequence ([3, 4, 5, 6, 7, 8, 9, 10]) are processed because the error handling sequence has taken over.

## Using onErrorReturn
> The onErrorReturn operator allows you to provide a single fallback value to emit if an error occurs.
```
Flux<Integer> ints = Flux.range(1, 4)
    .map(i -> {
        if (i == 2) throw new RuntimeException("Error at 2");
        return i;
    })
    .onErrorReturn(-1);

ints.subscribe(i -> System.out.println(i),
    error -> System.err.println("Error: " + error));
```
Output:
```
plaintext
Copy code
1
-1
```
- The number 1 is printed normally.
- When the error occurs at 2, the onErrorReturn provides the fallback value -1.
- The fallback value -1 is printed.

## Using onErrorContinue
> The onErrorContinue operator allows the stream to continue emitting subsequent items even if an error occurs, by handling each error individually

```
Flux<Integer> ints = Flux.range(1, 4)
    .map(i -> {
        if (i == 2) throw new RuntimeException("Error at 2");
        return i;
    })
    .onErrorContinue((error, item) -> {
        System.err.println("Error on item " + item + ": " + error.getMessage());
    });

ints.subscribe(i -> System.out.println(i));
```
### Explanation of onErrorContinue
#### onErrorContinue((error, item) -> { ... }):
- This operator allows the stream to continue processing items after an error occurs.
- The provided lambda function takes two parameters:
  - error - the exception that was thrown.
  - item - the item that caused the exception.
- You can log the error or perform any necessary action to handle the error, while allowing the stream to continue.
#### Execution Flow with onErrorContinue
- The Flux emits the sequence [1, 2, 3, 4].
- The map operator processes each item:
  - For 1, the lambda function returns 1, so it is emitted.
  - For 2, the lambda function throws a RuntimeException with the message "Error at 2".
- The onErrorContinue operator catches the error and logs it, allowing the stream to continue.
- For 3 and 4, the lambda function returns the item itself, so they are emitted.
- The subscribe method starts the subscription and processes the emitted items:
  - The items 1, 3, and 4 are printed to the standard output.
  - The error caused by 2 is logged by the onErrorContinue operator.
#### Output with onErrorContinue
```
1
Error on item 2: Error at 2
3
4
```
- The numbers 1, 3, and 4 are printed normally.
- The error caused by 2 is logged, and the stream continues emitting items.
> Using onErrorContinue, the stream continues to emit items even after an error occurs, handling each error individually without terminating the stream.

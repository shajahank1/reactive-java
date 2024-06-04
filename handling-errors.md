# Handling Errors
>If you want the stream to continue emitting items even after an error occurs, you can use operators that handle errors and allow the stream to continue. In Project Reactor, you can use operators like onErrorResume, onErrorReturn, or onErrorContinue. These operators help manage errors and provide fallback mechanisms.

## Using onErrorResume
> The onErrorResume operator allows you to provide an alternative sequence to emit if an error occurs.

```

```

## Using onErrorReturn
> The onErrorReturn operator allows you to provide a single fallback value to emit if an error occurs.

```

```

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

# Mono Methods
> https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Flux.html
> https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Mono.html
## Creation
- Mono.just(T data): Create a Mono that emits the provided value.
- Mono.empty(): Create a Mono that completes without emitting any item.
- Mono.error(Throwable error): Create a Mono that terminates with the specified error.
- Mono.fromCallable(Callable<T> callable): Create a Mono that emits the value returned by the callable.
- Mono.fromSupplier(Supplier<T> supplier): Create a Mono that emits the value returned by the supplier.
- Mono.fromRunnable(Runnable runnable): Create a Mono that completes after the runnable has been executed.
- Mono.defer(Supplier<Mono<T>> supplier): Create a Mono that defers the creation until subscription.
## Transformation
- <R> Mono<R>.map(Function<? super T, ? extends R> mapper): Transform the item emitted by this Mono by applying a function to it.
- <R> Mono<R>.flatMap(Function<? super T, ? extends Mono<? extends R>> mapper): Transform the item emitted by this Mono into another Mono and flatten the result.
- <R> Flux<R>.flatMapMany(Function<? super T, ? extends Publisher<? extends R>> mapper): Transform the item emitted by this Mono into a Publisher and flatten the result into a Flux.
- Mono<T>.filter(Predicate<? super T> predicate): Emit the item only if it matches the provided predicate.
- Mono<T>.defaultIfEmpty(T defaultValue): Emit the default value if the Mono is empty.
- <V> Mono<V>.cast(Class<V> clazz): Cast the item emitted by this Mono to a specific class.
- Mono<Void>.then(): Return a Mono<Void> that completes when this Mono completes.
## Combining
- <R> Mono<R>.zipWith(Mono<? extends R> other): Combine the emissions of this Mono and another Mono into a tuple.
- <R> Mono<R>.then(Mono<? extends R> other): Execute another Mono after this one completes.
- <R> Mono<R>.thenReturn(R value): Execute another Mono and return a specific value upon completion.
- Mono<T>.switchIfEmpty(Mono<? extends T> alternate): Switch to an alternate Mono if this Mono is empty.
- <R> Mono<R>.concatWith(Mono<? extends R> other): Concatenate this Mono with another Mono.
## Error Handling
- Mono<T>.onErrorReturn(T fallbackValue): Provide a fallback value if an error occurs.
- Mono<T>.onErrorResume(Function<? super Throwable, ? extends Mono<? extends T>> fallback): Provide a fallback Mono if an error occurs.
- Mono<T>.onErrorMap(Function<? super Throwable, ? extends Throwable> mapper): Map the error into another error.
- Mono<T>.retry(long numRetries): Retry the Mono on error a specified number of times.
## Subscription
- Mono<T>.subscribe(Consumer<? super T> consumer): Subscribe to this Mono and consume the item.
- Mono<T>.subscribe(Consumer<? super T> consumer, Consumer<? super Throwable> errorConsumer): Subscribe to this Mono and handle errors.
- Mono<T>.subscribe(Consumer<? super T> consumer, Consumer<? super Throwable> errorConsumer, Runnable completeConsumer): Subscribe to this Mono, handle errors, and run a task upon completion.
- Mono<T>.block(): Block and wait for the Mono to complete, returning the value or throwing an error.
# Flux Methods
## Creation
- Flux.just(T... data): Create a Flux that emits the provided values.
- Flux.fromIterable(Iterable<? extends T> iterable): Create a Flux that emits items from the provided iterable.
- Flux.fromStream(Stream<? extends T> stream): Create a Flux that emits items from the provided stream.
- Flux.fromArray(T[] array): Create a Flux that emits items from the provided array.
- Flux.empty(): Create a Flux that completes without emitting any items.
- Flux.error(Throwable error): Create a Flux that terminates with the specified error.
- Flux.interval(Duration period): Create a Flux that emits long values starting from 0 and increments at specified intervals.
vFlux.range(int start, int count): Create a Flux that emits a range of integers.
- Flux.defer(Supplier<Flux<T>> supplier): Create a Flux that defers the creation until subscription.
## Transformation
- <R> Flux<R>.map(Function<? super T, ? extends R> mapper): Transform the items emitted by this Flux by applying a function to each item.
- <R> Flux<R>.flatMap(Function<? super T, ? extends Publisher<? extends R>> mapper): Transform the items emitted by this Flux into Publishers and flatten the result.
- <R> Flux<R>.concatMap(Function<? super T, ? extends Publisher<? extends R>> mapper): Transform the items emitted by this Flux into Publishers and concatenate the results in order.
- <R> Flux<R>.flatMapSequential(Function<? super T, ? extends Publisher<? extends R>> mapper): Transform the items emitted by this Flux into Publishers and flatten the result, preserving the order.
- Flux<T>.filter(Predicate<? super T> predicate): Emit only the items that match the provided predicate.
- Flux<T>.buffer(int size): Collect items into lists of the specified size and emit those lists.
- Flux<T>.distinct(): Emit only distinct items.
- Flux<T>.take(long n): Emit only the first n items.
- Flux<T>.skip(long n): Skip the first n items and emit the rest.
- Flux<T>.mergeWith(Publisher<? extends T> other): Merge the emissions of this Flux and another Publisher.
## Combining
- <T, R> Flux<R>.zipWith(Flux<? extends T> other): Combine the emissions of this Flux and another Flux into tuples.
- <R> Flux<R>.concatWith(Publisher<? extends R> other): Concatenate this Flux with another Publisher.
- Flux<T>.mergeWith(Publisher<? extends T> other): Merge the emissions of this Flux and another Publisher.
- <T1, T2, R> Flux<R>.zip(Publisher<? extends T1> p1, Publisher<? extends T2> p2, BiFunction<? super T1, ? super T2, ? extends R> combinator): Combine items from two sources using a combinator function.
## Error Handling
- Flux<T>.onErrorReturn(T fallbackValue): Provide a fallback value if an error occurs.
- Flux<T>.onErrorResume(Function<? super Throwable, ? extends Publisher<? extends T>> fallback): Provide a fallback Publisher if an error occurs.
- Flux<T>.onErrorMap(Function<? super Throwable, ? extends Throwable> mapper): Map the error into another error.
- Flux<T>.retry(long numRetries): Retry the Flux on error a specified number of times.
## Subscription
- Flux<T>.subscribe(Consumer<? super T> consumer): Subscribe to this Flux and consume the items.
- Flux<T>.subscribe(Consumer<? super T> consumer, Consumer<? super Throwable> errorConsumer): Subscribe to this Flux and handle errors.
- Flux<T>.subscribe(Consumer<? super T> consumer, Consumer<? super Throwable> errorConsumer, Runnable completeConsumer): Subscribe to this Flux, handle errors, and run a task upon completion.
- Flux<T>.blockLast(): Block and wait for the Flux to complete, returning the last item or throwing an error.
## Utility Methods
- Flux<T>.delayElements(Duration delay): Delay each item emission by a specified duration.
- Flux<T>.doOnNext(Consumer<? super T> onNext): Perform an action for each item emitted.
- Flux<T>.doOnComplete(Runnable onComplete): Perform an action upon completion.
- Flux<T>.doOnError(Consumer<? super Throwable> onError): Perform an action when an error occurs.
- Flux<T>.takeUntil(Predicate<? super T> predicate): Emit items until the predicate returns true.
- Flux<T>.then(): Return a Mono<Void> that completes when this Flux completes.

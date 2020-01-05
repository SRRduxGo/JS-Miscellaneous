# How the event loop handles tasks and microtasks

## Chapter 1

> Each 'thread' gets its own event loop
> So each web worker gets its own, so it can execute independently
> Whereas all windows on the same origin share an event loop as they can synchronously communicate
> The event loop runs continually, executing any tasks queued
> An event loop has multiple task sources which guarantees execution order within that source (specs such as IndexedDB define their own)
> But the browser gets to pick which source to take a task `from` on each `turn` of the `loop`
> This allows the browser to give preference to performance sensitive tasks such as user-input

## Chapter 2

> Tasks are `scheduled` so the browser can get from `its internals` into `JavaScript/DOM land` and ensures these actions happen `sequentially`
> Between `tasks`, the browser may `render updates`
> Getting from a mouse click to an event callback requires scheduling a task, as does parsing HTML, and in the above example, setTimeout

## Chapter 3

> `setTimeout` waits for a given delay then `schedules` a `new task` for its callback
> This is why `setTimeout` is logged after script end, as logging script end is part of the `first task`  
> `setTimeout` is logged in a separate task

## Chapter 4 (Mind-Bending One)

> **`Microtasks`** are usually scheduled for things that should happen straight after the currently executing script
> Such as reacting to a batch of actions, or to make something async without taking the `penalty` of a whole `new task`
> The **microtask queue** is processed after callbacks as long as no other JavaScript is mid-execution, and _`at the end of each task`_ > **Any additional microtasks queued during `microtasks` are added to the _end of the queue_ and also processed**  
> `Microtasks` include mutation observer callbacks, and as in the above example, promise callbacks

## Chapter 5

> Once a promise settles, or if it has already settled, it queues a microtask for its reactionary callbacks. This ensures promise callbacks are async even if the promise has already settled. So calling .then(yey, nay) against a settled promise immediately queues a microtask. This is why promise1 and promise2 are logged after script end, as the currently running script must finish before microtasks are handled. promise1 and promise2 are logged before setTimeout, as microtasks always happen before the next task.

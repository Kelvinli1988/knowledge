# Memory Consistency Properties
- Each action in a thread _happens-before_ every action in that thread comes later in the program's order.
- An unlock(synchonized block or method exit) of a monitor _happens-before_ every subsequent lock(synchronized block or method entry) of that same monitor. And because the _happens-before_ relation is transitive, all actions of a thread prior to unblocking _happen-before_ all actions subsequent to anty thread locking that monitor.
- A write to a volatile field _happens-before_ every subsequent read of that same field. Writes and reads of volatile fields have similar memory consistency effects as entering and exiting monitor but do not entail mutual exlusion locking.
- A call to start on a thread _happens-before_ any action in the started thread.
- All action in a thread happen-bore any other thread successfully returns from a from on that thread.

The methods of all class in java.util.concurrent and its subpackages extends these guarantee to higher-level synchronization. In paticular:
- Actions in a thread prior to placing an object into any concurrent collection _happen-before_ actions subsequent to the access or removal of that element from the collection in another thread.
- Actions in a thread prior to the submission of a Runnable to an Executor _happen-before_ its execution begins. Similarly for Callables submitted to an ExecutorService.
- Actions taken by the asynchronous computation represented by a Feture _happen-before_ actions subsequent to the retrieval of the result via Future.get() in another thread.
- Action prior to "releasing" synchronizer methods such as Lock.unlock, Semaphore.relase, and CountDownLatch.countDown _happen-before_ actions subsequent to a successful "acquiring" method such as Lock.lock, Semaphore.acquire, Condition.await, and CountDownLatch.await on the same synchronizer object in another thread.
- For each pair of threads that successfully exchange objects via an Exchanger, actions prior to the exchange() in each thread _happen-before_ those subsequent to the corresponding exchange() in another thread.
- Actions prior to calling CyclicBarrier.await and Phaser.awaitAdvance(as well as its variants) _happen-before_ actions performed by the barrier action and actions performed by the barrier action _happen-before_ actions subsequent to a successful return from the corresponding await in other threads.

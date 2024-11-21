
## 1. HashMap的原理以及扩容机制

**简介：**

HashMap 是 Java 中常用的基于哈希表的数据结构，用于存储键值对。它提供了快速的查找、插入和删除操作。

**基本原理：**

`HashMap` 的底层是一个数组，每个数组元素称为一个桶。每个桶内部可以存储多个键值对，这些键值对通过链表或红黑树来组织。 当我们插入一个键值对时，首先会计算键的哈希值，然后通过 `hash % (数组长度-1)` 得到存储位置的索引。如果该位置为空，就直接插入；如果不为空，就会通过链表或红黑树来处理哈希冲突。

**扩容机制：**

`HashMap` 有一个负载因子，默认值为 0.75。当哈希表中元素的数量超过 `容量 * 负载因子` 时，就会触发扩容。 扩容时，`HashMap` 会创建一个新的数组，大小是原数组的两倍，然后将所有键值对重新计算哈希值并插入到新数组中，这个过程称为 rehashing，因为所有的键值对都需要重新计算哈希值并插入新的位置。

**示例代码：**
```Java
import java.util.HashMap;

public class HashMapExample {
    public static void main(String[] args) {
        // 创建一个 HashMap
        HashMap<String, Integer> map = new HashMap<>();

        // 插入键值对
        map.put("one", 1);
        map.put("two", 2);
        map.put("three", 3);

        // 查找值
        System.out.println("Value for key 'one': " + map.get("one"));

        // 删除键值对
        map.remove("two");

        // 遍历 HashMap
        for (String key : map.keySet()) {
            System.out.println("Key: " + key + ", Value: " + map.get(key));
        }
    }
}
```

**拓展学习：**
- `HashMap`如何处理哈希冲突？
- 为什么链表转红黑树的阈值是8？链表长度为8就一定会变成红黑树吗？

## 2. ArrayList和LinkedList的区别

1. **是否保证线程安全：** 都不是线程安全的，需要外部同步。
2. **底层数据结构：**
	- **ArrayList：** 基于动态数组实现。数组在初始化时有固定的容量，当元素数量超过容量时，数组会自动扩容（通常是原容量的 1.5 倍）。
	- **LinkedList：** 基于双向链表实现。每个元素（节点）包含一个数据值和指向前后节点的引用。
3. **插入和删除的影响：**
	- **ArrayList：** 在末尾插入或删除元素的时间复杂度为 O(1)。但是，在中间插入或删除元素需要移动后续元素，时间复杂度为 O(n)。
	- **LinkedList：** 在头尾插入或者删除元素的时间复杂度为 O(1)，因为只需调整前后节点的引用。但在指定位置时，需要先找到插入或删除的位置，时间复杂度为 O(n)。
4. **快速随机访问：**
	- **ArrayList：** 支持快速随机访问，因为底层是数组（实现了 `RandomAccess` 接口），可以直接通过索引访问元素。
	- **LinkedList：** 不支持快速随机访问。需要遍历链表来访问元素。
5. **内存空间占用:**
	- **ArrayList：** 在列表的结尾会预留一定的容量空间，会导致一定量的内存浪费。
	- **LinkedList：** 每个元素（节点）都需要额外的空间来存储指向前后节点的引用。

## 3. JVM的内存区域

1. **程序计数器**：记录当前线程所执行的字节码指令的地址，属于线程私有的内存区域。
2. **Java 虚拟机栈**：存储每个方法执行时的栈帧，包括局部变量表、操作数栈、动态链接和方法出口等信息。它是线程私有的，栈内存不足时会抛出 `StackOverflowError` 或 `OutOfMemoryError`。 
3. **本地方法栈**：与 JVM 栈类似，但用于本地方法的执行，使用 C、C++ 等语言实现。它也是线程私有的，内存不足时会抛出 `StackOverflowError` 或 `OutOfMemoryError`。 
4. **堆**：堆是 JVM 中最大的一块内存区域，用于存储对象实例和数组。它是线程共享的，堆内存不足时会抛出 `OutOfMemoryError`。堆可以进一步划分为年轻代和老年代，年轻代又可以细分为 Eden 区和两个 Survivor 区（S0 和 S1）。 
5. **方法区**：用于存储已被虚拟机加载的类信息、常量、静态变量和即时编译器编译后的代码。它是线程共享的，内存不足时会抛出 `OutOfMemoryError`。在 HotSpot 虚拟机中，方法区也称为永久代（Permanent Generation），在 Java 8 及以后版本中被替换为元空间（Metaspace）。
6. **运行时常量池**：是方法区的一部分，用于存放编译期生成的各种字面量和符号引用。它是线程共享的，内存不足时会抛出 `OutOfMemoryError`。
7. **直接内存**：虽然不属于 JVM 规范中的一部分，但它被频繁使用。直接内存由 `java.nio` 包中的 `ByteBuffer` 类使用，进行高效的 I/O 操作。它不受 JVM 堆大小限制，但受本地内存大小限制，内存不足时会抛出 `OutOfMemoryError`。

**拓展学习：**
- JVM 调优

## 4. 垃圾回收算法

垃圾回收（GC）是 JVM 内存管理的重要组成部分，通过自动回收不再使用的对象，释放内存空间，避免内存泄漏。不同的垃圾回收算法有不同的适用场景和性能特点。

1. **标记-清除算法（Mark-Sweep）**：最基础的垃圾回收算法，分为两个阶段：标记阶段和清除阶段。在标记阶段，GC 会遍历所有的对象，标记出所有可达的对象。在清除阶段，GC 会回收所有未被标记的对象。虽然该算法实现简单，但存在内存碎片问题，因为回收后的内存空间是不连续的。
2. **标记-整理算法（Mark-Compact）：** 对标记-清除算法的改进，同样分为标记和整理两个阶段。在标记阶段标记所有可达对象后，整理阶段会将所有存活的对象移动到内存的一端，然后清理掉边界以外的内存。这种方式解决了内存碎片的问题，但移动对象的成本较高。 
3. **复制算法（Copying）：** 将内存分为两块相等的区域，每次只使用其中一块。当这块内存用完时，GC 会将存活的对象复制到另一块内存中，然后清空当前使用的内存区域。复制算法适用于对象生命周期较短的场景，因为大部分对象会在一次GC中被回收。其优点是没有内存碎片，缺点是需要双倍的内存空间。 
4. **分代收集算法（Generational Collection）：** 基于对象的生命周期特点，将堆内存分为新生代和老年代。新生代中的对象生命周期较短，使用复制算法进行垃圾回收；老年代中的对象生命周期较长，使用标记-清除或标记-整理算法进行垃圾回收。

**具体的垃圾回收器有：** 
- **Serial GC**：使用单线程进行垃圾回收，适用于单线程环境。其优点是实现简单，缺点是垃圾回收时会暂停所有应用线程（Stop-The-World），导致较长的停顿时间。 
- **Parallel GC**：使用多线程进行垃圾回收，适用于多处理器环境，提供更高的吞吐量。它在新生代使用复制算法，在老年代使用标记-整理算法。虽然停顿时间较长，但适合对响应时间要求不高的后台应用。 
- **CMS（Concurrent Mark-Sweep） GC**：旨在减少垃圾回收的停顿时间，适用于低延迟应用。它在标记阶段和清除阶段都可以与应用线程并发执行，但在某些情况下可能会产生内存碎片。CMS GC 在老年代使用标记-清除算法，在新生代使用复制算法。 
- **G1（Garbage-First） GC**：面向大内存、多处理器环境的垃圾回收器，旨在提供低停顿时间。它将堆内存划分为多个独立的区域（Region），并优先回收垃圾最多的区域。G1 GC 结合了并行和并发的特点，适用于对响应时间和吞吐量都有要求的应用。

**拓展知识**：
- 什么样的对象算垃圾？如果对象被标记成了垃圾，还能逃逸吗？

## 5. 了解JMM吗？

Java内存模型（JMM）定义了Java程序中多线程操作内存的方式和规则。它规定了变量的读取和写入如何在不同线程之间可见，从而确保程序的正确性和一致性。

具体详解参考推荐文章：[JMM详解](https://javaguide.cn/java/concurrent/jmm.html)

## 6. 线程池的七个参数，工作过程以及提供的四个已有线程池

线程池是一个管理线程的机制，通过复用已创建的线程来执行任务，从而减少频繁创建和销毁线程的开销，提高系统性能和稳定性。

**创建 ThreadPoolExecutor 的七个参数：**

1. **corePoolSize**：核心线程数，即线程池中始终保持存活的线程数量，即使这些线程处于空闲状态。
2. **maximumPoolSize**：最大线程数，即线程池中允许的最大线程数量。
3. **keepAliveTime**：当线程数超过核心线程数时，多余的空闲线程的存活时间。
4. **unit**：`keepAliveTime` 的时间单位，可以是 `TimeUnit` 中的任意一个时间单位。
5. **workQueue**：任务队列，用于存放等待执行的任务。常用的队列有 `LinkedBlockingQueue`、`SynchronousQueue`、`ArrayBlockingQueue` 等。
6. **threadFactory**：线程工厂，用于创建新线程。可以通过自定义线程工厂来设置线程的名称、优先级等。
7. **handler**：拒绝策略，当任务无法被线程池接受时执行的策略。内置的拒绝策略有 `AbortPolicy`、`CallerRunsPolicy`、`DiscardPolicy` 和 `DiscardOldestPolicy`。

**线程池的工作过程：**

1. **提交任务**：当客户端提交一个任务时，线程池首先判断核心线程池中的线程是否都在工作。如果不是，则创建一个新的工作线程来执行任务。
2. **任务队列**：如果核心线程池中的线程都在工作，线程池会将任务放入任务队列中。
3. **创建新线程**：如果任务队列已满，且线程池中的线程数小于最大线程数，线程池会创建新的线程来处理任务。
4. **拒绝策略**：如果线程池中的线程数已达到最大线程数，并且任务队列也已满，线程池会根据拒绝策略来处理新提交的任务。
5. **任务执行**：线程从任务队列中取出任务并执行。
6. **线程回收**：当线程池中的线程数超过核心线程数，并且空闲时间超过 `keepAliveTime` 时，多余的线程会被终止。

**四个已有线程池**：

Java 提供了四种主要的线程池实现，分别是 `FixedThreadPool`、`CachedThreadPool`、`ScheduledThreadPool` 和 `SingleThreadExecutor`，它们都位于 `java.util.concurrent` 包中。

1. **FixedThreadPool**：
    
    - **特点**：创建一个固定大小的线程池，线程池中的线程数量固定不会变化。
    - **适用场景**：适用于负载较重且需要限制并发线程数量的场景。
    - **使用方法**：`ExecutorService fixedThreadPool = Executors.newFixedThreadPool(int nThreads);`
    
2. **CachedThreadPool**：
    
    - **特点**：创建一个可以根据需要创建新线程的线程池，闲置的线程会被缓存并在需要时重用。
    - **适用场景**：适用于执行很多短期异步任务的小程序。
    - **使用方法**：`ExecutorService cachedThreadPool = Executors.newCachedThreadPool();`
3. **ScheduledThreadPool**：
    
    - **特点**：创建一个可以在给定延迟后运行命令或者定期执行任务的线程池。
    - **适用场景**：适用于需要周期性执行任务的场景。
    - **使用方法**：`ScheduledExecutorService scheduledThreadPool = Executors.newScheduledThreadPool(int corePoolSize);`
    
4. **SingleThreadExecutor**：
    
    - **特点**：创建一个单线程化的线程池，确保所有任务按照指定顺序执行。
    - **适用场景**：适用于需要确保顺序执行各个任务的场景。
    - **使用方法**：`ExecutorService singleThreadExecutor = Executors.newSingleThreadExecutor();`

**拒绝策略详解**：
1. **AbortPolicy**：当线程池无法接受新的任务时，该策略会直接抛出一个 `RejectedExecutionException` 异常，通知提交任务的线程任务被拒绝了。
2. **CallerRunsPolicy**：当线程池无法接受新的任务时，该策略不会丢弃任务或者抛出异常，而是将任务交给调用线程（即提交任务的线程）来执行。这可能会导致调用线程阻塞，直到任务完成。
3. **DiscardPolicy**：当线程池无法接受新的任务时，该策略会直接丢弃任务，不做任何处理，也不抛出异常。
4. **DiscardOldestPolicy**：当线程池无法接受新的任务时，该策略会丢弃等待队列中最旧的未处理任务，然后尝试重新提交被拒绝的任务。

## 7. 线程池核心参数设置的技巧

1. **核心线程数（corePoolSize）**：对于CPU密集型任务，设置为CPU核心数；对于I/O密集型任务，可以设置为CPU核心数的两倍或更多。
2. **最大线程数（maximumPoolSize）**：CPU密集型任务不超过CPU核心数；I/O密集型任务可以适当增加，但要考虑系统资源。
3. **空闲线程存活时间（keepAliveTime）**：根据任务频率和执行时间调整。任务频繁时设置短时间，任务不频繁时设置长时间。
4. **任务队列（workQueue）**：无界队列适用于短时间高频率任务，有界队列适用于长时间低频率任务，优先队列适用于需要按优先级执行的任务。
5. **线程工厂（ThreadFactory）**：可以自定义线程工厂以设置线程名称、优先级等属性，便于调试和监控。
6. **拒绝策略（RejectedExecutionHandler）**：选择合适的拒绝策略，如AbortPolicy、CallerRunsPolicy等，或根据需求自定义策略。

## 8. synchronized 、AQS 、volatile

**synchronized**：Java语言内置的同步机制，用于确保在同一时刻只有一个线程可以执行某个代码块，从而防止竞争条件。`synchronized` 关键字确保了进入代码块的线程获取了对象的监视器，从而实现了互斥访问。

**AQS**：用于构建锁和同步器的框架。它是Java并发包（java.util.concurrent）中许多同步类的基础，如 `ReentrantLock`、`Semaphore`、`CountDownLatch` 等。
**AQS 核心思想**： AQS 使用一个FIFO队列来管理争用资源的线程。它通过一个整数状态（state）来表示同步状态，并提供了两种模式：独占模式（exclusive）和共享模式（shared）。

**volatile**：一种轻量级的同步机制，用于确保变量的可见性。它保证了对一个变量的读写操作都是直接从主内存中进行，而不是从线程的工作内存中缓存。
**volatile 可见性**： 当一个变量被声明为 `volatile`，对该变量的读写操作会直接操作主内存，从而确保所有线程都能看到最新的值。
**volatile 有序性**： `volatile` 还禁止指令重排序优化，从而确保变量的操作顺序。



**总之**：

- **synchronized**：适用于简单的互斥需求，易用但可能带来性能瓶颈。
- **AQS**：用于构建复杂同步器，灵活高效，但需深入理解。
- **volatile**：用于变量的可见性和有序性，轻量级但不保证原子性。

**拓展知识**：什么是可重入锁，有哪些？

## 9. CurrentHashMap的原理

ConcurrentHashMap 是 Java 中用于并发编程的一个线程安全的哈希表实现。它主要用于在多线程环境下确保数据一致性和操作的高效性。

1. **分段锁机制（Segmented Locking，Java 7 及以前）**
- 在 Java 7 及以前的版本中，`ConcurrentHashMap` 将整个哈希表分成了多个段（Segment），每个段本质上是一个小型的哈希表，并且各自独立地维护自己的锁。只要不同的线程访问的数据处于不同的段中，它们就不会互相阻塞。
- 每个 Segment 维护一个独立的锁。
- 插入、删除和更新操作只锁定与键相关的那个段，而不是整个哈希表。

2. **CAS 操作和 `synchronized` 锁（Java 8 及以后）**

- 在 Java 8 中，移除了 `Segment` 概念，转而使用了更细粒度的控制和无锁操作来实现更高效的并发处理。
- **CAS 操作（Compare-And-Swap）：** 对于某些操作（如插入新节点），`ConcurrentHashMap` 使用 CAS 操作来避免锁的使用。例如，`putIfAbsent` 方法使用 CAS 直接将新节点插入到链表或树中。
- **`synchronized` 锁：** 在必须使用锁的地方（如扩容、链表转换为红黑树、put操作），`ConcurrentHashMap` 采用内置的 `synchronized` 锁。这些锁是细粒度的，只锁定冲突的 bin（桶），而不是整个哈希表。

3. **读操作的无锁设计**
- 大多数读操作（如 `get`）是无锁的，因为它们只需要从现有结构中读取数据，不涉及修改数据结构。

**Java 8时 的部分源码**：

```Java
final V putVal(K key, V value, boolean onlyIfAbsent) {
    // 省略其他代码...
    for (Node<K,V>[] tab = table;;) {
        Node<K,V> f; int n, i, fh;
        if (tab == null || (n = tab.length) == 0)
            tab = initTable();
        else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
            if (casTabAt(tab, i, null, new Node<K,V>(hash, key, value, null)))
                break; // 如果桶为空，直接使用CAS插入新节点
        }
        else if ((fh = f.hash) == MOVED)
            tab = helpTransfer(tab, f); // 如果正在扩容，帮助扩容
        else {
            V oldVal = null;
            synchronized (f) {  // 锁定桶的头节点，进入同步块
                if (tabAt(tab, i) == f) {
                    if (fh >= 0) {  // 如果是链表
                        binCount = 1;
                        for (Node<K,V> e = f;; ++binCount) {
                            K ek;
                            if (e.hash == hash &&
                                ((ek = e.key) == key ||
                                 (ek != null && key.equals(ek)))) {
                                oldVal = e.val;
                                if (!onlyIfAbsent)
                                    e.val = value;
                                break;
                            }
                            Node<K,V> pred = e;
                            if ((e = e.next) == null) {
                                pred.next = new Node<K,V>(hash, key, value, null);
                                break;
                            }
                        }
                    }
                    else if (f instanceof TreeBin) {  // 如果是红黑树
                        Node<K,V> p;
                        binCount = 2;
                        if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key, value)) != null) {
                            oldVal = p.val;
                            if (!onlyIfAbsent)
                                p.val = value;
                        }
                    }
                }
            }
            if (binCount != 0) {
                if (binCount >= TREEIFY_THRESHOLD)
                    treeifyBin(tab, i);  // 如果链表节点过多，树化
                if (oldVal != null)
                    return oldVal;
                break;
            }
        }
    }
    addCount(1L, binCount);
    return null;
}
```

**拓展知识**：
- 讲讲CAS的原理？什么是ABA问题？怎么解决？
- HashMap线程安全吗？ConcurrentHashMap为什么线程安全？它是如何扩容的？

## 10. 如果在扩容时，有一个元素插入到ConcurrentHashMap，会怎么样？

1. Java 7 中的 `ConcurrentHashMap` 扩容机制：
	- 在 Java 7 中，`ConcurrentHashMap` 使用了分段锁（Segment-based locking）来处理并发操作。它将整个哈希表分成多个段（Segment），每个段相当于一个独立的哈希表，拥有自己的锁。
	- 由于每个 `Segment` 是独立的，扩容时锁只会影响单个 `Segment`，所以并发插入其他段中的元素不受影响。但扩容期间，同一段内的操作会被阻塞。
2. Java 8 中的 `ConcurrentHashMap` 扩容机制：
	- 使用了基于 CAS（Compare-And-Swap）操作和 `synchronized` 的机制，Java 8 中的扩容通过 `transfer` 方法将旧表的数据搬移到新表，多个线程可以同时参与到搬移工作中。对于每一个桶，搬移过程中会对桶进行锁定（通过 `synchronized`）。
	- 当插入元素时，如果某个桶正在搬移，线程会检测到这个情况，并参与到搬移操作中，而不是直接插入到旧表。一旦扩容完成，插入操作就会被应用到新的哈希表中。

## 11. HashMap和ConcurrentHashMap的区别

1. **线程安全性**：
    
    - **HashMap**：不是线程安全的。如果多个线程同时访问一个 `HashMap`，并且至少有一个线程修改了该 map 的结构（比如添加或删除元素），就可能导致数据不一致或抛出异常。
    - **ConcurrentHashMap**：是线程安全的。它使用了一种分段锁（在早期版本）或 CAS 操作（在 Java 8 及之后）来确保并发访问的安全性，同时维持较高的并发性能。
2. **锁机制**：
    
    - **HashMap**：没有内置的锁机制。
    - **ConcurrentHashMap**：在 Java 7 中使用分段锁（每个段是一个小的 `HashMap`），在 Java 8 及之后改为使用一种基于 CAS 和细粒度锁的机制来实现更高效的并发控制。
3. **性能**：
    
    - **HashMap**：在单线程环境下性能较好，因为没有额外的同步开销。
    - **ConcurrentHashMap**：在多线程环境下性能优于 `Collections.synchronizedMap(new HashMap<>())`，因为它允许更高的并发访问。
4. **Null 值**：
    
    - **HashMap**：允许存储 `null` 键和 `null` 值。
    - **ConcurrentHashMap**：不允许 `null` 键或 `null` 值。这是因为在并发环境下，`null` 值可能导致歧义和错误。

## 12. 动态代理

动态代理是一种在运行时创建代理对象的技术，用于在不修改原始类的情况下增强其功能。在 Java 中，动态代理主要有两种实现方式：JDK 动态代理和 CGLIB 代理。

1. **JDK 动态代理**：基于 Java 的反射机制，要求代理的目标类必须实现一个或多个接口。通过 `java.lang.reflect.Proxy` 类和 `InvocationHandler` 接口实现。
2. **CGLIB 代理**：基于字节码操作，生成目标类的子类，因此不需要目标类实现接口。常用于 Spring AOP 中的无接口代理。

动态代理常用于实现 AOP（面向切面编程），如事务管理、日志记录、权限控制等功能。例如，在 Spring 框架中，事务管理就是通过动态代理来实现的。

**示例代码**：
```Java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

interface Service {
    void perform();
}
//
class RealService implements Service {
    public void perform() {
        System.out.println("Performing service...");
    }
}
//
class ServiceInvocationHandler implements InvocationHandler {
    private Object target;

    public ServiceInvocationHandler(Object target) {
        this.target = target;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("Before method");
        Object result = method.invoke(target, args);
        System.out.println("After method");
        return result;
    }
}
//
public class DynamicProxyExample {
    public static void main(String[] args) {
        Service realService = new RealService();
        Service proxyService = (Service) Proxy.newProxyInstance(
                realService.getClass().getClassLoader(),
                realService.getClass().getInterfaces(),
                new ServiceInvocationHandler(realService));

        proxyService.perform();
    }
}
```

## 11. IOC和AOP原理是什么

### IOC (控制反转)

IOC的核心思想是**将对象创建和依赖关系的管理交给框架**，而不是由应用程序手动控制。传统情况下，应用程序负责实例化对象以及管理依赖，而在IOC中，框架会负责这些任务。控制反转意味着应用程序的控制权被“反转”交给框架，开发者只需专注于业务逻辑。

**实现原理**：

- **依赖注入（Dependency Injection, DI）**: 这是实现IOC的一种方式。依赖项不再是硬编码在类中，而是通过构造函数、setter方法或者字段注入的方式传入。

**Spring IOC 容器**：

（如 `ApplicationContext` 或 `BeanFactory`）是管理依赖注入的核心部分。它通过**配置文件**（如XML配置）或**注解**（如`@Component`, `@Autowired`, `@Configuration`等）来自动检测和装配bean。容器负责创建bean实例，并根据定义的依赖关系自动注入。

**IOC生命周期**：

1. **Bean定义加载**：Spring容器读取XML文件或扫描注解，解析bean定义。
2. **Bean实例化**：容器根据bean定义创建bean实例。
3. **依赖注入**：实例化后，容器根据依赖关系对bean进行注入。
4. **初始化**：容器调用bean的初始化方法。
5. **销毁**：容器关闭时调用bean的销毁方法。

### AOP (面向切面编程)

面向切面编程是一种编程范式，它允许将那些横切多个对象的关注点（例如事务管理、日志记录等）模块化。AOP使得开发者能够分离出那些在多个模块中都存在的行为（即横切关注点），并将它们独立出来，然后在适当的时候把这些行为插入到正常的业务逻辑流程中去。

**AOP的核心思想**：

通过定义**切面（Aspect）**，将横切关注点与核心业务逻辑分离，实现更好的代码模块化。

**原理**:

- **切面（Aspect）**: 一个关注点的模块化，它可以跨越多个对象。例如，事务管理就是一个切面。
- **通知（Advice）**: 切面在特定的连接点执行的动作。
- **连接点（Join Point）**: 在程序执行期间能够插入切面的一个点，例如方法调用或异常抛出。
- **切入点（Pointcut）**: 匹配连接点的断言。通知和一个切入点表达式关联，并在匹配的连接点上执行。
- **织入（Weaving）**: 将切面连接到其他应用程序类型或对象上，并创建一个新的被增强的对象。织入可以在编译时、加载时或运行时完成。

**好处**:

- 支持更为清晰的模块化，提高代码的可重用性。
- 横切关注点被分离出去，使得主要的业务逻辑更加清晰。
- 可以在不修改原代码的情况下增加新的功能，如日志记录、事务管理等。


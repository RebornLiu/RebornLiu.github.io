Random和ThreadLocalRandom

### 结论

1. random使用cas保证线程安全
2. ThreadLocalRandom是线程安全的，同时解决了在高并发条件下cas引起的自旋性能问题
3. ThreadLocalRandom是1.7才提供的

### Random实现

```java
class Random {
    //随机种子
    private final AtomicLong seed;
    /**产生下一个
      * 1.以固定的算法，根据当前oldseed获取新的nextseed
      * 2.cas判断oldseed是否变更了，来解决多线程竞争同一个seed的问题
      * 3.生成新的随机数
      *
      * 如果没有第2步的cas，多个线程会生成很多相同的nextseed
    */
    protected int next(int bits) {
        long oldseed, nextseed;
        AtomicLong seed = this.seed;
        do {
            oldseed = seed.get();
            nextseed = (oldseed * multiplier + addend) & mask; //1
        } while (!seed.compareAndSet(oldseed, nextseed));//2
        return (int)(nextseed >>> (48 - bits));//3
    }   
}
```

### 

### ThreadLocalRandom实现

1. ThreadLocalRandom不是将Random存储在ThreadLocal中，但是设计思路是一样的，都使用了Thread类的私有变量存储私有数据
2. ThreadLocalRandom和ThreadLocal相同，都不存储数据，只是提供了指向Thread的入口
3. ThreadLocalRandom因为使用的线程私有数据，避免了多线程的竞争，因此解决了Random的性能问题

```java
class Thread {
    /**
     * 关于线程私有随机seed的变量
     * 这些变量都是普通的long，因此是私有变量，不需AtomicLong
     */
    @sun.misc.Contended("tlr")
    long threadLocalRandomSeed;
    /** Probe hash value; nonzero if threadLocalRandomSeed initialized */
    @sun.misc.Contended("tlr")
    int threadLocalRandomProbe;
    /** Secondary seed isolated from public ThreadLocalRandom sequence */
    @sun.misc.Contended("tlr")
    int threadLocalRandomSecondarySeed;
}

class ThreadLocalRandom {
    private static final AtomicInteger probeGenerator = new AtomicInteger();
    private static final AtomicLong seeder = new AtomicLong(initialSeed());
    static {
         SEED = UNSAFE.objectFieldOffset
                (tk.getDeclaredField("threadLocalRandomSeed"));
         PROBE = UNSAFE.objectFieldOffset
                (tk.getDeclaredField("threadLocalRandomProbe"));
         SECONDARY = UNSAFE.objectFieldOffset
                (tk.getDeclaredField("threadLocalRandomSecondarySeed"));
    }
    
    public static ThreadLocalRandom current() {
        if (UNSAFE.getInt(Thread.currentThread(), PROBE) == 0)
            localInit();
        return instance;
    }
    
    static final void localInit() {
        /**
         * 获取当前线程私有化seed，这里根据公共的probeGenerator和seeder
         * 也就是说ThreadLocalRandom仅仅是在初始化的时候有竞争，
         * 初始化之后，因为都是各自线程的私有变量因此并不会发生竞争
        */
        int p = probeGenerator.addAndGet(PROBE_INCREMENT);
        int probe = (p == 0) ? 1 : p; // skip 0
        long seed = mix64(seeder.getAndAdd(SEEDER_INCREMENT));
        Thread t = Thread.currentThread();
        
        //初始化 Thread中的threadLocalRandomSeed和threadLocalRandomProbe
        UNSAFE.putLong(t, SEED, seed);
        UNSAFE.putInt(t, PROBE, probe);
    }
}

/**
 * 基本使用
 */
public class ThreadLocalRandomDemo {
    public static void main(String[] args) {
        Random random = ThreadLocalRandom.current();
        random.nextInt();
    }
}
```


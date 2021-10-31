AQS借助VarHandle机制，进行CAS

```
//java.util.concurrent.locks.AbstractQueuedSynchronizer

// VarHandle mechanics
private static final VarHandle STATE;
private static final VarHandle HEAD;
private static final VarHandle TAIL;

static {
    try {
        MethodHandles.Lookup l = MethodHandles.lookup();
        STATE = l.findVarHandle(AbstractQueuedSynchronizer.class, "state", int.class);
        HEAD = l.findVarHandle(AbstractQueuedSynchronizer.class, "head", Node.class);
        TAIL = l.findVarHandle(AbstractQueuedSynchronizer.class, "tail", Node.class);
    } catch (ReflectiveOperationException e) {
        throw new ExceptionInInitializerError(e);
    }

    // Reduce the risk of rare disastrous classloading in first call to
    // LockSupport.park: https://bugs.openjdk.java.net/browse/JDK-8074773
    Class<?> ensureLoaded = LockSupport.class;
}
```


## AbstractQueuedSynchronizer

jdk11

#### #shouldParkAfterFailedAcquire

```java
private static boolean shouldParkAfterFailedAcquire(AbstractQueuedSynchronizer.Node pred, AbstractQueuedSynchronizer.Node node) {
    int ws = pred.waitStatus;
    if (ws == -1) {
        return true;
    } else {
        if (ws > 0) {
            do {
                node.prev = pred = pred.prev;
            } while(pred.waitStatus > 0);

            pred.next = node;
        } else {
            pred.compareAndSetWaitStatus(ws, -1);
        }

        return false;
    }
}
```


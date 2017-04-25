---
published: true
title: Java Concurrency - CountDownLatch
layout: post
---

- CountDownLatch is a very interesting class from java concurrent package. It allows one thread to wait for one or more threads to complete before it starts processing.

- This can be super useful on java server side. The usage is pretty simple. You can initialize CountDownLatch with initial count and then decrement it in threads using `countDown()`. This will decrement the count by 1. Finally you can use `await()` in the thread which needs to await other threads processing before it starts its processing. It will wait until count becomes 0.

- Below is the example of CountDownLatch. We have initialized the count at 3 and decrement the count in 3 other threads. The main thread will wait for all 3 threads to complete before it starts processing.

~~~ java
import java.util.concurrent.CountDownLatch;

public class TestCountDownLatch {

    public static void main(String[] args) {
        CountDownLatch latch = new CountDownLatch(3);
        Thread cacheService = new Thread(new Service(latch), "Cache Service");
        Thread namingService = new Thread(new Service(latch), "Naming Service");
        Thread validationService = new Thread(new Service(latch), "Validation Service");

        cacheService.start();
        namingService.start();
        validationService.start();

        try {
            latch.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("All services are up!");
    }
}

class Service implements Runnable {

    private CountDownLatch latch;

    public Service(CountDownLatch latch) {
        this.latch = latch;
    }

    @Override
    public void run() {
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println(Thread.currentThread().getName() + " is up!");
        latch.countDown();
    }
}
~~~


Output:

~~~
Validation Service is up!
Cache Service is up!
Naming Service is up!
All services are up!
~~~

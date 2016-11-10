---
layout: post
title: A simple http server
---

I tried to build a simple http server. The single threaded version has been finished, which can serve a request once. The next step is to construct a server which can handle many http requests concurrently.

To achieve that, the main thread creates a thread pool and accepts requests. The idle threads in thread pool try to get a request to handle. A request queue has to be maintained, which supports thread-safe push and pop. Idle worker threads invoke pop to get a request and will block until request queue is not empty. The following pattern will be used:

```
	pthread_cond_t  cond = PTHREAD_COND_INITIALIZATION
	pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZATION

	void push(queue* queue, int req_fd) {
		pthread_mutex_lock(&mutex);
		queue_push(queue, req_fd);
		queue->size++;
  		pthread_cond_signal(&cond);
   		pthread_mutex_unlock(&mutex);
	}

	int pop(queue* queue) { 
		pthread_mutex_lock(&mutex); 
		if(!queue->size) pthread_cond_wait(&cond, &mutex); 
		queue_pop(queue); 
		queue->size--; 
		pthread_mutex_unlock(&mutex);
	}

```

The code above has a bug:
Suppose two consumers c1, c2 and a producer p1 work together. When the size of queue is 0, c1 finds the queue is empty and then sleep to wait for a request's arrival. Then a request arrives. P1 push it into queue and sends a singal. Assume c1 is waken up but do not acquire the lock for mutex. At the almost same time, c2 get the lock for mutex, and take the request from queue, leaving the queue empty. Then c1 acquires the lock for mutex, and will try to take request from a empty queue, reasulting in a access vialiation.

The root cause of this bug is that **the function `pthread_cond_wait()` are not atomic**, which consists of two actions: 

1. **waken up by a signal and its state changed to be ready to run**;
2. **acquire the lock**

To resolve this bug, just replace `if(!queue->size)` with `while(!queue->size)`.

```
    while(!queue->size) pthread_cond_wait(&cond, &mutex);
```


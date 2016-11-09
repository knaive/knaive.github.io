I tried to build a simple http server. The single threaded version has been finished, which can serve a request once. The next step is to construct a server which can handle many http requests concurrently.

To achieve that, the main thread creates a thread pool and accepts requests. The idle threads in thread pool try to get a request to handle. A request queue has to be maintained, which supports thread-safe push and pop. Idle worker threads invoke pop to get a request and will block until request queue is not empty. The following pattern will be used:
```
pthread_cond_t  cond = PTHREAD_COND_INITIALIZATION
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZATION

void push(queue* queue, int req_fd) {
    pthread_mutex_lock(&mutex);
    /*push req_fd to queue and increase the size*/
    pthread_cond_signal(&cond);
    pthread_mutex_unlock(&mutex);
}

int pop(queue* queue) {
    pthread_mutex_lock(&mutex);
    if(!queue->size) pthread_cond_wait(&cond, &mutex);
    /*pop a request file descriptor*/
    pthread_mutex_unlock(&mutex);
}

```

Here a if clause is used to check whether queue is empty. Some references in google propose that a while clause is better,
```
    while(!queue->size) pthread_cond_wait(&cond, &mutex);
```
 but I don't figure it out now.

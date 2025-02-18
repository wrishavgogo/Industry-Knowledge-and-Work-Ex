taskExecutor.setWaitForTasksToCompleteOnShutdown(true);

In our code base we have a TaskExecutor which asynchronously does the trace / bigQuery populating work . 

i saw a config which said taskExecutor.setWaitForTasksToCompleteOnShutdown(true); 

this means that 

If the main thread completes, but the application remains running (e.g., a Spring Boot app in K8s)
✅ The worker threads in ThreadPoolTaskExecutor will continue executing tasks until:

All tasks are finished.
The application itself is shut down.
If the application is shutting down (e.g., the pod is terminated)

Spring will shutdown the ThreadPoolTaskExecutor.
Since setWaitForTasksToCompleteOnShutdown(true), it will wait for running tasks to finish before terminating.

But since its k8s and application is always running to it will keep executing . 

If new requests come threadpool executor will spawn new threads . 

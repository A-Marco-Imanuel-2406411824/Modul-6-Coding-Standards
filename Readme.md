## Commit 1 Reflection notes
What is inside handle_connection method?

The method first wraps the TcpStream given into a buffered reader of type BufReader while borrowing the same stream. This has to be done because TcpStream returns raw bytes.
It then reads the request line-by-line by generating an iterator over the lines of the stream reader,
and then unwraps each line until an empty line is encountered. The result from each unwrapped line is then collected in
a String vector (vec<String>).

## Commit 2 Reflection notes
![Commit 2 screen capture](/hello/static/commit2.png)

## Commit 3 Reflection notes
You can split between responses by checking from the buffered reader 
if it has "GET / HTTP/1.1" (or with any http protocol) as the first line. 
This checks whether the request is directed to "/" or anything else (i.e., /bad, /else, etc..)
and then split between the responses accordingly.
This can easily be implemented with a simple if-else clause.

The refactoring is needed as it makes it easier to see the difference (and similarities) between the two cases. 
It would also mean we have only one place to update the code if we want to change how the file reading and response writing work.

## Commit 4 Reflection notes
When opening two browser windows: one with /sleep and one with the normal /, 
both windows take the same time to load - around 10 seconds - even though only the process
for the /sleep request was delayed with thread::sleep(Duration::from_secs(10));.

This happens because in single threaded apps, processes are processed strictly sequentially.
When a process that takes a significant amount of time is processed before another shorter process,
subsequent processes has to wait (and therefore could starve) the execution of the former longer process
before it gets executed, regardless of the availability of the server's resources 
to execute another process at the same time. 
This would get worse as the server received more and more requests; the serial execution would be less and less optimal.

## Commit 5 Reflection notes
How the struct ThreadPool works:
1. In hello/src/lib.rs, ThreadPool::new(size) creates an mpsc channel, wraps the receiver in Arc<Mutex<_>> so all workers can share it safely, and spawns size worker threads.
2. Each Worker::new starts a thread that runs an infinite loop, locks the receiver, waits for the next incoming task with recv(), and then executes it. 
3. The Job type is a boxed closure, Box<dyn FnOnce() + Send + 'static>, which means the pool can store any one-time task that can be safely sent across threads. 
4. ThreadPool::execute takes a closure, boxes it into a Job, and sends it through the channel so the next available worker can pick it up and run it. 
5. Overall, this gives you a fixed-size pool that can process multiple tasks concurrently, although the current implementation does not include a graceful shutdown mechanism for the workers.
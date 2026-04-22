## Commit 1 Reflection notes
What is inside handle_connection method?

The method first wraps the TcpStream given into a buffered reader of type BufReader while borrowing the same stream (This is done because TcpStream returns raw bytes).
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

The refactoring is needed as it makes it easier to see the difference (and similarities) between the two cases, 
and it means we have only one place to update the code if we want to change how the file reading and response writing work.

## Commit 4 Reflection notes
When opening two browser windows: one with /sleep and one with the normal /, 
both windows take the same time to load - around 10 seconds - even though only the process
for the /sleep request was delayed with thread::sleep(Duration::from_secs(10)); 

This happens because in single threaded apps, processes are processed strictly sequentially.
When a process that takes a significant amount of time is processed before another shorter process,
the other process has to wait (and therefore could starve) the execution of the former longer process
before it gets executed, regardless of the availability of the server's resources to execute another process.
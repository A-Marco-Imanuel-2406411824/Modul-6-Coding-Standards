## Commit 1 Reflection notes
What is inside handle_connection method?

The method first wraps the TcpStream given into a BufReader while borrowing the same stream.
It then reads the request line-by-line by generating an iterator over the lines of the stream reader (line 15),
and then unwraps each line (line 16) until an empty line is encountered (line 17). The result from each unwrapped line is then collected in
a String vector (vec<String>) (line 18)
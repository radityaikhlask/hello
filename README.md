# Commit 1 Reflection notes

In this step, I changed the program from only detecting that a connection was established into actually reading the incoming HTTP request from the browser. I used `TcpStream` to represent the connection and passed it into the `handle_connection` function so the request handling logic is separated from the listener loop. Inside `handle_connection`, I wrapped the stream with `BufReader` so the incoming bytes can be read line by line more conveniently. The `.lines()` call reads the request as text lines, `.map(|result| result.unwrap())` extracts the successful values, and `.take_while(|line| !line.is_empty())` stops reading when it reaches the empty line that marks the end of the HTTP request headers. The collected result is stored in a vector and printed in a formatted way, which makes it easier to observe what the browser is actually sending to the server.

From this step, I learned that a browser does not simply “connect” to a server, but sends a structured HTTP request containing a request line and several headers. I also learned why buffering is useful when reading from a stream, because it simplifies line-based reading. Separating the code into `main` and `handle_connection` also makes the program easier to understand and prepares it for later milestones where the server will send actual responses instead of only printing requests.

# Commit 2 Reflection notes

In this step, I modified the server so it does not only read the request, but also sends a valid HTTP response back to the browser. I imported `fs` because the server now reads the HTML content from an external file named `hello.html`. After reading the request, the program prepares a status line with `HTTP/1.1 200 OK`, then reads the file contents, calculates the content length, and combines them into a complete HTTP response string. The response uses `\r\n` as required by the HTTP format, and there is an empty line between the headers and the body. Finally, `stream.write_all(response.as_bytes())` sends the response bytes through the TCP stream to the browser.

From this step, I learned that a browser needs a properly formatted HTTP response before it can render a page. Simply writing HTML text is not enough, because the browser also expects a status line and headers such as `Content-Length`. I also learned that the server can serve a file by reading it into a string and embedding it into the response body. This milestone helped me understand the relationship between raw TCP communication and the higher-level HTTP protocol that runs on top of it.

![Commit 2 screen capture](/assets/images/commit2.png)


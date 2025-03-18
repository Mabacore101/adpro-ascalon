- Name: Valentino Vieri Zhuo
- Class: Pemrograman Lanjut A
- NPM: 2306206446

# Commit 1 Reflection notes

The `handle_connection` function will process incoming TCP connections. Here's how it works:
1. Function Signature. `fn handle_connection(mut stream: TcpStream) {` will take a mutable `TcpStream`, allowing the method to read data from the client.
2. Create a Buffered Reader. `let buf_reader = BufReader::new(&mut stream);` will wrap the stream into a buffered reader to efficiently read data line by line.
3. Read the HTTP Request. `.lines()` will read the request line by line. `.map(|result| result.unwrap())` will extract the actual String from `Result<String, Error>`. `.take_while(|line| !line.is_empty())` will stop reading when an empty line is found, which is the end of HTTP Headers. `.collect()` will collect the lines into a vector `Vec<String>`.
4. Print the Request. `println!("Request: {:#?}", http_request);` will display the collected HTTP Request in a nice printed format.

# Commit 2 Reflection notes

![](/images/milestone2.png)

The `handle_connection` has changed from commit 1 so that we can see a page called `hello.html`. Here's the explanation for the modificaitons:
1. Define HTTP Response Components. `let status_line = "HTTP/1.1 200 OK";` will set the status line of the HTTP Response to 200 OK, meaning that the request was successful. `let contents = fs::read_to_string("hello.html").unwrap();
` will read the contents of `hello.html` into a string and `.unwrap()` method will ensure the program panics when `hello.html` isn't found. `let length = contents.len();
` will count the content length in bytes for the Content-Length header. 
2. Format the HTTP Response. `let response = format!(
   "{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}",
   );` This will construct a valid HTTP Response as a formatted string. The `\r\n\r\n` marks the end of HTTP headers and separates them from the body. 
3. Send the response. `stream.write_all(response.as_bytes()).unwrap();` converts response into bytes and writes it to the TCP stream. `.write_all()` makes sure that all bytes are sent before continuing.
   `.unwrap()` makes the program panic if an error occurs.

# Commit 3 Reflection notes

![](/images/milestone3.png)

From the previous `main.rs` code I've refactored the code to split the responses. Here's how I split it up:
1. Read the HTTP Request
   ```
   let buf_reader = BufReader::new(&mut stream);
   let http_request:Vec<_> = buf_reader
       .lines()
       .map(|result|result.unwrap())
       .take_while(|line|!line.is_empty())
       .collect();
   ```
   This segment of code will read the request line by line and collects them in a `Vec<String>`. The `.take_while(|line|!line.is_empty())` part will stop reading when finding an empty line, which is the end of the HTTP header.

2. Handle Empty Requests
   ```
   if http_request.is_empty() {
       return;
   }
   ```
   If the request is empty, this segment of code will help it exit the method so further processing of empty requests are stopped.

3. Extract the First Line and Generate Response
   ```
   let request_line = http_request.get(0).unwrap();
   let response = generate_response(request_line);
   stream.write_all(response.as_bytes()).unwrap();
   ```   
   This segment of code will take the first request line and send it to `generate_response()` which will decide the appropriate response.
   
4. Generate Response Based on Request
   ```
   fn generate_response(request_line: &str) -> String {
       let get = "GET / HTTP/1.1";
       let (status_line, filename) = if request_line == get {
           ("HTTP/1.1 200 OK", "hello.html")
       } else {
           ("HTTP/1.1 404 NOT FOUND", "404.html")
       };
   
       let contents = fs::read_to_string(filename).unwrap();
       let length = contents.len();
       format!(
           "{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}",
       )
   }
   ```
   This segment of the code will send `hello.html` if the request contains "GET / HTTP/1.1". Otherwise, `404.html` will be returned instead. This separates the logic from `handle_connection` so that the code is much more modular.

This refactoring step is important due to several reasons:
1. Separation of Concerns. `handle_connection` now only handles connections while `generate_response` deals with response creation. This is based on Single Responsibility Principle where each method has only one responsibility.
2. Scalability. If we want to add POST or DELETE request, we can just modify `generate_response` without actually touchy `handle_connection`.
3. Easier Debugging. Now, since each method does only one specific thing, we can assure that a bug in connection would mean a bug in `handle_connection` and a bug in response would mean a bug in `generate_response`.

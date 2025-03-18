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
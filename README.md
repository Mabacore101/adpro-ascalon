- Name: Valentino Vieri Zhuo
- Class: Pemrograman Lanjut A
- NPM: 2306206446

# Commit 1 Reflection notes

The `handle_connection` function will process incoming TCP connections. Here's how it works:
1. Function Signature. `fn handle_connection(mut stream: TcpStream) {` will take a mutable `TcpStream`, allowing the method to read data from the client.
2. Create a Buffered Reader. `let buf_reader = BufReader::new(&mut stream);` will wrap the stream into a buffered reader to efficiently read data line by line.
3. Read the HTTP Request. `.lines()` will read the request line by line. `.map(|result| result.unwrap())` will extract the actual String from `Result<String, Error>`. `.take_while(|line| !line.is_empty())` will stop reading when an empty line is found, which is the end of HTTP Headers. `.collect()` will collect the lines into a vector `Vec<String>`.
4. Print the Request. `println!("Request: {:#?}", http_request);` will display the collected HTTP Request in a nice printed format.
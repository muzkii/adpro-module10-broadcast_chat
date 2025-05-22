# Advanced Programming Rust 
by Andriyo Averill Fahrezi, NPM of 2306172325

## Module 10 - Asynchronous Programming - Broadcast Chat

### Original Code of Broadcast Chat

#### Server 
![image](https://github.com/user-attachments/assets/3b8a89fb-c7ba-42b6-81e2-daa6a14c1186)


#### Client
![image](https://github.com/user-attachments/assets/5d87b582-8146-4fe8-b223-70dfe9eafee4)

#### How to Run It

- Start the server :
```bash
cargo run --bin server
```

- Create 3 separate terminals, with each run the command"

```bash
cargo run --bin client
```

When we initially connect instances of the `client` to our server (when we do `cargo run --bin client`), we would actually see tht there is a new connection output on the `server` telling that there is a new connection from different port numbers. This is because each terminal acts as `clients` that runs within the same device, essentially making port numbers used as doors in a house. When we type some texts in the `client` terminal, each client connects using WebSocket and can send/receive messages. Messages typed into one client are broadcast to all others in real-time, as seen on the image of the `client` above, even though I only type the message "terminal X" on the respective terminal, it also appears on every terminal.

### Modifying the Websocket Port

We can change the port number both in `server.rs` and in `client.rs`. Changes on both of the file can be see on the before and after below respectively:

- Before `server.rs` :
```Rust
    ...
    let listener = TcpListener::bind("127.0.0.1:2000").await?;
    println!("listening on port 2000");
    ...
```

- After `server.rs` :
```Rust
    ...
    let listener = TcpListener::bind("127.0.0.1:8080").await?;
    println!("listening on port 8080");
    ...
```

- Before `client.rs`:
```Rust
    ...
    ClientBuilder::from_uri(Uri::from_static("ws://127.0.0.1:2000"))
    ...
```

- After `client.rs`:
```Rust
    ...
    ClientBuilder::from_uri(Uri::from_static("ws://127.0.0.1:8080"))
    ...
```

We have to make sure both sides use the same port, otherwise the client cannot connect. The port was changed to 8080 to test configurability. The protocol remains `ws://` — WebSocket over plain TCP. Imagine the server is like a phone operator waiting for calls. It doesn't specifically say 'I'm handling WebSocket calls,' but it's set up to listen on a particular phone line (port) for any incoming calls (TCP connections). Once a call comes in, it immediately hands that call over to a specialized 'WebSocket call center' (the `tokio-websocket crate`) that knows exactly how to speak the WebSocket 'language' and handle the conversation. So, while it doesn't announce it upfront, it's definitely doing WebSocket work behind the scenes.

### Small Changes

![image](https://github.com/user-attachments/assets/45a9a79c-9bd9-4f2a-bbc1-a9a08663cdb4)
![image](https://github.com/user-attachments/assets/2de86f43-78bd-40ac-afa5-6f25e5cb2369)

```bash
New connection from Andriyo Averill's Computer127.0.0.1:50677
New connection from Andriyo Averill's Computer127.0.0.1:50678
From client 127.0.0.1:50677 "hello this is test 2 from terminal 1"
From client 127.0.0.1:50678 "hi this is test 2 from terminal 2"
```
Previously, when a new connection is formed, without having my computer's name before the IP Address:Port after the server name. In the server code (`server.rs`), the following line inside the `main` and `handle_connection` function respectively:

```Rust
    println!("New connection from {addr:?}");
```

```Rust
    ...
    bcast_tx.send(text.into())?;
```

Was replaced with :

```Rust
    println!("New connection from Andriyo Averill's Computer{addr:?}");
```

```Rust
    ... 
    let formatted = format!("{addr} : {text}");
    bcast_tx.send(formatted)?;
```

Additionally, on the client side, there is also a change when it would only display the message content, without having my computer's name and also without telling the IP Address:Port after the server name, before the message. Now the modification done on the `client.rs` is not as difficult by just doing a simple string additions to the print statement. To achieve:

```bash
Andriyo Averill's Computer — From server: Welcome to chat! Type a message
hello this is test 2 from terminal 1
Andriyo Averill's Computer — From server: 127.0.0.1:50677 : hello this is test 2 from terminal 1
Andriyo Averill's Computer — From server: 127.0.0.1:50678 : hi this is test 2 from terminal 2
```

In the original version, the server only broadcasted the raw message content (e.g., `"Hi"`). This made it impossible for other clients to know who sent the message.

By formatting the message to include the sender's `SocketAddr` (IP and port), each message sent from the server now includes useful context. This is especially helpful in multi-client environments where multiple users connect from the same machine or network. This improves the clarity of communication across connected clients, giving each message a source identity without needing additional client-side logic.


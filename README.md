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
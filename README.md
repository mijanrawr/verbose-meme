`MUHAMMAD MINHALKAZIM BIN MOHD SARUSNIZA | 2022484384 | M3CS2554A | ITT440`
# Pass Multiple Random Number using TCP Port

## Socket API Overview
Python’s socket module provides an interface to the <a href="https://en.wikipedia.org/wiki/Berkeley_sockets" target= "blank"> Berkeley sockets API</a>. The primary socket API functions and methods that will be used are:
* `socket()`
* `.bind()`
* `.listen()`
* `.accept()`
* `.connect()`
* `.connect_ex()`
* `.send()`
* `.recv()`
* `.close()`

## TCP Sockets
To better understand this, check out the sequences of socket API calls and data flow for TCP:
<p align="center">
    <img src="https://files.realpython.com/media/sockets-tcp-flow.1da426797e37.jpg" alt="drawing" width="500"/>
</p>

*<p align="center"><a href="https://idiotdeveloper.com/file-transfer-using-tcp-socket-in-python3/" target= "blank">TCP Socket Flow</a></p>*

As it has been showned in the diagram, on the right side is the **client** while the left-hand column represents the **server**. It starts on the top left side column, the API calls so that the server can start to set up a "listening" socket:
* `socket()`
* `.bind()`
* `.listen()`
* `.accept()`

The server **listens** for connections from clients by calling `.listen()`. When a client connects, the server uses `.accept()` to **accept** the connection.

The client calls `.connect()` to establish a connection to the server and initiate the **three-way handshake**. It is important because it ensures that both sides of the connection are reachable in the network. In simpler terms, the client can reach the server and vice-versa. 

Once the connection is established, the client and server use `.send()` and `.recv()` where the **data exchange** happens between both sides. Lastly, the client and server close their respective sockets to end the connection. 
## How to code the Server
```python 
import socket 
import random 

server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

host = socket.gethostname()
port = 27679

try:
    server_socket.bind((host, port))
except socket.error as e:
    print(f"Error: {e}")
    server_socket.close()
    exit(1)
    
server_socket.listen(1)
print("Server listening on {}:{}". format(host, port))

try:
    while True:
        client_socket, addr = server_socket.accept()
        print("Got a connection from {}", format(addr))
        
        random_numbers = [random.randint(1,100) for _ in range(10)]
        number_str = ",".join(str(num) for num in random_numbers)
        
        try:
            client_socket.send(number_str.encode('utf-8'))
        except ssocket.error as e:
            print(f"Error sending data: {e}")

        client_socket.close()
    
except KeyboardInterrupt:
    print("Server terminated by user.")
```

In the above code, we created a **socket instance** for the server. You can see that `socket.AF_INET` defines the address family that this socket can accept – **only IPv4 addresses**. Also, `socket.SOCK_STREAM` defines that the socket accepts only **TCP (Transmission Control Protocol)** connections.

This line of code, `host = socket.gethostname()`, will get the **host name** of the current system under which the Python interpreter is executed. Also, to set the **port number** that the server will listen on by assign `port = 27679`.

The `.bind()` method is used to **associate the socket** with a specific network interface and port number which is `try: server_socket.bind((host, port))`. The values passed to `.bind()` depend on the **address family** of the socket. In this coding, it use `socket.AF_INET` (IPv4). So it expects a two-tuple: **(host, port)**. 

If another process is already bound to this host or port, it will raise a `socket.error` **exception**. `except socket.error as e: print(f"Error: {e}")` **catches** the `socket.error` exception and prints the error message. Then, it **close the socket** if there was an error binding it, `server_socket.close()`, and **exit the program** with a status code of 1, indicating an error occured, `exit(1)`.

`server_socket.listen(1)` puts the server socket in **passive mode**, where it waits for the client to approach the server to make a connection. The argument `1` is the **maximum** number of queued connections. The `.listen()` method has a **backlog parameter**. It specifies the number of **unaccepted connections** that the system will allow before refusing new connections. `print("Server listening on {}:{}". format(host, port))`, this line prints a message indicating that the server is listening.

The `.accept()` method **blocks execution** and waits for an incoming connection. `while True: client_socket, addr = server_socket.accept()` starts infinite loop where the server waits to **accepts** a connection from a client. And if the connection is established, it will **prints** a message, `print("Got a connection from {}", format(addr))`.

This line of code, `random_numbers = [random.randint(1,100) for _ in range(10)]`, will generatess a list of 10 **random numbers** between 1 and 100. Then, to converts the list of numbers into a **comma-separated** string, use `number_str = ",".join(str(num) for num in random_numbers)`. 

After that, it needs to be ***<a href="https://docs.python.org/3/library/socket.html#socket.socket.send" target= "blank">send</a>*** to the client by using this line of code, `try: client_socket.send(number_str.encode('utf-8'))`. If the fails to send, it will print an **error messages**, `except ssocket.error as e: print(f"Error sending data: {e}")`. Then, the client **close** its socket by `client_socket.close()`. Lastly, if the user **interrupts** the program (e.g., by pressing Ctrl+C), it prints a message indicating that the server was terminated by the user, `except KeyboardInterrupt: print("Server terminated by user.")`.
## How to code the Client
```python 
import socket

client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

host = socket.gethostname()
port = 27679

try:
    client_socket.connect((host, port))
except socket.error as e:
    print(f"Error connecting to the server: {e}")
    client_socket.close()
    exit(1)

received_data = client_socket.recv(1024).decode('utf-8')

number_list = received_data.split(',')
```

In the above code, we created a **socket instance** for the server. You can see that `socket.AF_INET` defines the address family that this socket can accept – **only IPv4 addresses**. Also, `socket.SOCK_STREAM` defines that the socket accepts only **TCP (Transmission Control Protocol)** connections.

This line of code, `host = socket.gethostname()`, will get the **host name** of the current system under which the Python interpreter is executed. Also, to set the **port number** that the server will listen on by assign `port = 27679`.

The client have to **connect** to the server at the given host and port by using the `.connect()` system. The connect() system call connects the socket referred to by the file descriptor sockfd to the address specified by **addr**, `try: client_socket.connect((host, port))`. Server’s address and port is specified in addr. But if the connections **fails**, it will prompt an error messages, `except socket.error as e: print(f"Error connecting to the server: {e}")`. Then, the client socket will **close**, `client_socket.close()`.

`received_data = client_socket.recv(1024).decode('utf-8')`, this line **receives** data from the server. The `.recv()` method takes one argument that specifies the maximum amount of data to be **received** at once (1024 bytes in this case). The received data is then decoded from bytes to a string using **UTF-8 encoding**. 

`number_list = received_data.split(',')`, this line **splits** the received string into a list of numbers using the comma as a separator.

## Output

<p align="center">
    <img src="https://lh3.googleusercontent.com/pw/AP1GczM7j_OhJdkw3dnH6ZE5T82D3vOce3JXWfNw16zl9u3-3u2MtjtUebyCPOzZE1XdVUQ7zZ9mhiqV-jz8yYfWkQOHiophO3_nOuyD-02Nco-jpRCw-sSzaZvvIsWgbvDB_vDHFPwBUJQOF4m836flOSXI7yss_j_1n1rQsf0JZ5rskfIRVRvlmeaITPznYbaUggZSzlcxZhb5zUCOBW6Wii-bAmcI0v4UN3Z8G9dISDItkj0U_4UBzcS0rVf2x8fIQiSIUnm6ChIBvu31F0IiaTM3z7N72OFpD2ubX8Sdqy089ZYxc0BqZ2_KrBbLYVtgUIxvZ4yr-PpaJ1Jwuv7l7Gk0Il2v_vjIYVjbIQhLWNKgvxNUXIMltUxkHHTWTIh_9DqX9JyvajcUdOzogQIpuG5BCVE36Nf1iLz54hiuFidcFyCJcn-9NPFMluJgCMa6I4lx3RcO0lbYxs90_G9etlOTA1Uetq1S7fijT9XRkBwqAabn4BrSU1hinFJgweqqOhVAqTWZhVVEjslKO_IlbVhcFFpYwiLyoGoRI2Ao77B_nARMBjCxMo2l4a9Ivtj-rzbQR-dNETryzj6DbzppOwNYN45p7hyy07s3JclUPGyfV6U_DY2dwosdP_zZsBDY4nAE6eBTgJKekMJF0wGIPm7bnTmw94-KXwDRBsjk4_K3tOKL8Fe0bv7YOiYi1p_HBn0ZJ7Bwpcy6VZD5HokHbYIDn4HyCFtxdNaE6AwGiu4RxprsK_kuTbqJx_fRellT8S5_UZYBn7VYWkrIJQLub5ls0Za9vcMJ0nzIVlyJlfQ5mHGphod9mK4TUmtYUw86jrLa2tiNAv9nNx8DYuCXA-SYwDZL_Mu79F9WFAsmgcvFjX8csnZhPR5ohVc3R42YOP9NjsRXCgyzSYrKFcOQpEYxvUmzGGrGZk_8IdLiQqnQlKujghTDXGKEcGvhPSxSeMtx-7ZJ0aMG12U3gTyb5xG_2QbKiXMsJSECu10SCHyyU5yEopGryQ8dKkLUOnF-Gg=w376-h69-s-no-gm?authuser=2" alt="drawing" width="500"/>
</p>

*<p align="center">server.py</p>*

<p align="center">
    <img src="https://lh3.googleusercontent.com/pw/AP1GczMiWRz6LVxcAMV94H_3mVVTFCccUGAw0Fv239eX1IHuR0J7-QZVr6kVyJ9F1ew12NkBzBNjeywrmQQNbKTVSjv43wCbJ0e6CVr-LtYFuW8sFvoa7LHj6ATKIK0ROcAgomTliG87KJglc3c5VZCb3ch2Lj_sOLGpm_qh6Uwd3Gueq0TLAdx346bPvx_P85LvRIvObhQe6xYV1bzXh_TPM_ln0zWOrFtQO2CRswZIse0xUN2ypd-QKco8Ebbw2B8jFirov7FGoqedohu75iwPvz91S7yJ6heVDC7yfkZPLIry3aGXdTUE-pC5lW9svTLiFyuGKQX3ki9tVVINovmciqy_3Gg1Q1k8R745RzJ7Vn-TOZrYp3V262ZPxWVl6on8A4-QLygp1lEKE3HNlx_kMAhkhmkx62iJzbvPLP_cJSaQzTfWSxWPSUjeJPf7h8qy25fjMqLiEAyMg5av8oQFpO7OAlJfF-qsmZSDsOjRwEgutFaw1EDPULFbPsrJKcQkYRVImKSYpmV-oy4P4x26j1ITh0M75Wcdy1rllOp7cUEm7TaepgWPjf9MSAv3PHMsoXM4_vZRM4x_QLOgvw8iHXOh-sMVgSnS3ouvTNa-gaom8mIte9U6e2mqC2nWgoO_71chWGqzntMLdF0IXVBcQmzvXw2vuIi8ehG3EZqy4QMm5hQy0wLGPF4_1slyeZ22XLdy6JG8W6oNQw5tExUP2pn0VnBfEQPuxzD8ncpuVhoHR9Rp--J4naaNph-PAmAAgDODwa9Byoo3CxurNBuZiA0b_OzajLnn7iSXjOF4SXgzF0Z2PtQb9wmWAKnHr8JedVO2tjeXwM4cITiIqo6_7iT82_2ozkadZWtEExokHBAPLK7SyCygfRBIhl3QSoI9mUKgpCV-BHtOkcdQDyeKGsX7RL7o7dNioLxEPTEaKjPGtTkwDNY4Ave2yuqE7W9vGA5Q52qs3ClyQOmD3TmIK16Eff6CAMTxXTeXyvUysoa_Q8X3Zd8AoR0gaykLVpna2A=w650-h29-s-no-gm?authuser=2"alt="drawing" width="8000"/>
</p>

*<p align="center">client.py</p>*

## Video
<p align="center">
    <a href="https://youtu.be/9UL6xB5qFkI" target= "blank"><img src="https://lh3.googleusercontent.com/pw/AP1GczOEVatXBCxfsjtAHfUMX2ryWJZ5TEylQ7iyG7btc-jIqCpULTFcb1kv8tzvwo8QXYw42FEXKrcX8ZAzOFltAtATH71F_-mG4nbzQT2tDyzJGPCAhnXbI4xHG6dRkVhwn5shCuWdfaPEx3BNi9hzvd-kvsVzUCCQ4ShxaAx9itUuGbqD_-7j-PYJ4nb9GKHVEV6aWaF4dkrfNsPl62RUPD6QUdEEMiNNnoUNn_LBC2rx9SkzSObWzYZzO4L6pDKEIZ9QvKBJ1AMrerTxSnmSRH8QigImYDSWodaKGZ4L3iEitWCU4kbFUSeh4VPT5Has0AOCtl33Y_4O2pDOEnX3JuqZuVIsm4W4AQiZbxFLd_4zvzreUvVZYQ9cg-893wh5PaMmtNjlz2Pm6w7GpkXF0IZv7pTQei7brkmLw-9ks1RQ6UObk0R3umxFtwWclVZ86Hf1SCZ-bkmmerJ2OSlxnWTHD8B1X3r_RTqSjHhNOQ9sORRmpe6edAqyyh5cdKWnPxkmspHUq-2aXZsm9mI3iILvnqdaYvVXXYPvRsT709OtdItRckdhIqdW2rpbbLsV2u3NFJT3SsyAgEcntwrfLgqH5SJaxNSrOSutRXuvdbmwGCkR8mhlV1yUvWA7SjAMhlG0Iic6tT2mj30ip_edRvODGvxDUP-280vGIeT-Vd-Nw0J7idRcVBCHwMBSMeYYepAbcQn9Y3ybAEq5z8ZHj9XtfaXu0SUj3-vPqmqk5QYtPbV1Zxetox8gLoZbuOMWsTGiMq9_7KK_Jsxs9GtW358Ak1YitOWqLFhYtgv36iVoXsJOWYoP8i19CzF86Po2k-8kVd4Z48k45zaG20oR0PWvcJseIvsQXyjrvNmcDsVFKZ_AJATwffjzLRZoNPsC4ydSCgvTtH7h9Kg6bJDrd9zG6NFGbIZLq3qOyi00d1KUhWwbyqdaghyYPHk95-JBIFc0U0mi6gvcwTIWVqKiWKGffSvIvajJ1Lak4IIUmucUYgB8sXW28k4X_qYSc1wM=w1741-h979-s-no-gm?authuser=2"/>
    </a>
</p>


# Middleware Engineering "DEZSYS_GK_HELLOWORLD_GRPC"

## Aufgabenstellung
#### 1\. What is gRPC and why does it work across languages?

gRPC (Google Remote Procedure Call) is a high-performance, open-source RPC framework that runs on HTTP/2. It achieves cross-language compatibility by using **Protocol Buffers (Protobuf)** as its Interface Definition Language (IDL). The language-neutral `.proto` file defines the service and data structure, allowing gRPC to generate compatible code (stubs) for various languages (Java, Python, C\#, etc.).

#### 2\. Describe the RPC life cycle starting with the RPC client.

The life cycle involves:

1.  **Client Call:** Application calls a local stub method.
2.  **Serialization:** Client stub serializes parameters into binary format using Protocol Buffers.
3.  **Network Transmission:** Request is sent via HTTP/2 to the server.
4.  **Deserialization:** Server receives and deserializes the binary data.
5.  **Server Execution:** Server runs the business logic.
6.  **Response:** Result is serialized and sent back to the client, where the process reverses.

#### 3\. Describe the workflow of Protocol Buffers.

1.  **Definition:** Messages and services are defined in a `.proto` file.
2.  **Compilation:** The `protoc` compiler generates language-specific data access classes and service interfaces.
3.  **Implementation:** The generated code is used in the application for populating, serializing, and retrieving data.

#### 4\. What are the benefits of using protocol buffers?

* **Performance:** Binary serialization results in smaller, faster messages than JSON/XML.
* **Language Independence:** One definition supports multiple programming languages.
* **Type Safety:** Schema enforces data types, reducing errors.
* **Compatibility:** Supports backward and forward compatibility for evolving schemas.

#### 5\. When is the use of protocol buffers not recommended?

* **Human Readability:** Not recommended if data needs to be easily read or modified by humans (e.g., public APIs or config files).
* **Browser Clients:** Browser support is less native compared to JSON/REST (though gRPC-Web exists).
* **Ad-hoc Data:** Not suitable when the data structure changes dynamically or is unknown at compile time.

#### 6\. List 3 different data types that can be used with protocol buffers.

1.  `int32` (or `int64`) 
2.  `string` 
3.  `bool` 

## Implementierung


#### Step 1: Project Setup & "Hello World"

**Goal:** Implement a basic gRPC service that returns a greeting.
**Technology:** Java (Gradle/Maven) using `io.grpc` and `protobuf-java`.

##### 1.1 The Proto File (`HelloWorld.proto`)

The `.proto` file defines the `GreetingService` with a `sayHello` RPC method, and the corresponding `HelloRequest` and `HelloResponse` messages.

```protobuf
syntax = "proto3";

service GreetingService {
  rpc sayHello (HelloRequest) returns (HelloResponse) {}
}

message HelloRequest {
  string name = 1;
}

message HelloResponse {
  string message = 1;
}
```

##### 1.2 The Server Implementation (Java)

The server implements `GreetingServiceImpl` by extending the generated base class. It extracts the name, builds the greeting string, and sends the `HelloResponse`.

```java
public class GreetingServiceImpl extends GreetingServiceGrpc.GreetingServiceImplBase {
    @Override
    public void sayHello(HelloRequest request, StreamObserver<HelloResponse> responseObserver) {
        String greeting = "Hello, " + request.getName();
        HelloResponse response = HelloResponse.newBuilder().setMessage(greeting).build();
        responseObserver.onNext(response);
        responseObserver.onCompleted();
    }
}
```

##### 1.3 The Client Implementation (Java)

The client creates an insecure `ManagedChannel` to connect to `localhost:9090`. It then uses a `BlockingStub` to make a synchronous `sayHello` RPC call, printing the resulting message.

```java
ManagedChannel channel = ManagedChannelBuilder.forAddress("localhost", 9090).usePlaintext().build();
GreetingServiceGrpc.GreetingServiceBlockingStub stub = GreetingServiceGrpc.newBlockingStub(channel);
HelloResponse response = stub.sayHello(HelloRequest.newBuilder().setName("Thomas").build());
System.out.println("Response from Server: " + response.getMessage());
```

Start HelloWorldServer (Java)  
`gradle clean build`  
`gradle runServer`

Start HelloWorldClient (Java)  
`gradle runClient`
  

-------------------------------- Python 

Add grpcio packages  
`pip3 install grpcio grpcio-tools`  

Compile .proto file  
`python3 -m grpc_tools.protoc -I src/main/proto  
  --python_out=src/main/resources  
  --grpc_python_out=src/main/resources  
  src/main/proto/hello.proto`  

Start HelloWorldClient (Python)  
`python3 src/main/resources/helloWorldClient.py`  

## Quellen

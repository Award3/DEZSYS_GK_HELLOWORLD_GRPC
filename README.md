# Middleware Engineering "DEZSYS_GK_HELLOWORLD_GRPC"

## Aufgabenstellung
#### 1\. What is gRPC and why does it work across languages?

[cite_start]gRPC (Google Remote Procedure Call) is a high-performance, open-source RPC framework that runs on HTTP/2[cite: 2]. [cite_start]It achieves cross-language compatibility by using **Protocol Buffers (Protobuf)** as its Interface Definition Language (IDL)[cite: 3]. [cite_start]The language-neutral `.proto` file defines the service and data structure, allowing gRPC to generate compatible code (stubs) for various languages (Java, Python, C\#, etc.)[cite: 4].

#### 2\. Describe the RPC life cycle starting with the RPC client.

The life cycle involves:

1.  [cite_start]**Client Call:** Application calls a local stub method[cite: 5].
2.  [cite_start]**Serialization:** Client stub serializes parameters into binary format using Protocol Buffers[cite: 6].
3.  [cite_start]**Network Transmission:** Request is sent via HTTP/2 to the server[cite: 7].
4.  [cite_start]**Deserialization:** Server receives and deserializes the binary data[cite: 8].
5.  [cite_start]**Server Execution:** Server runs the business logic[cite: 9].
6.  [cite_start]**Response:** Result is serialized and sent back to the client, where the process reverses[cite: 10].

#### 3\. Describe the workflow of Protocol Buffers.

1.  [cite_start]**Definition:** Messages and services are defined in a `.proto` file[cite: 11].
2.  [cite_start]**Compilation:** The `protoc` compiler generates language-specific data access classes and service interfaces[cite: 12].
3.  [cite_start]**Implementation:** The generated code is used in the application for populating, serializing, and retrieving data[cite: 13].

#### 4\. What are the benefits of using protocol buffers?

* [cite_start]**Performance:** Binary serialization results in smaller, faster messages than JSON/XML[cite: 14].
* [cite_start]**Language Independence:** One definition supports multiple programming languages[cite: 15].
* [cite_start]**Type Safety:** Schema enforces data types, reducing errors[cite: 16].
* [cite_start]**Compatibility:** Supports backward and forward compatibility for evolving schemas[cite: 17].

#### 5\. When is the use of protocol buffers not recommended?

* [cite_start]**Human Readability:** Not recommended if data needs to be easily read or modified by humans (e.g., public APIs or config files)[cite: 18].
* [cite_start]**Browser Clients:** Browser support is less native compared to JSON/REST (though gRPC-Web exists)[cite: 19].
* [cite_start]**Ad-hoc Data:** Not suitable when the data structure changes dynamically or is unknown at compile time[cite: 20].

#### 6\. List 3 different data types that can be used with protocol buffers.

1.  [cite_start]`int32` (or `int64`) [cite: 21]
2.  [cite_start]`string` [cite: 21]
3.  [cite_start]`bool` [cite: 21]

## Implementierung


#### Step 1: Project Setup & "Hello World"

[cite_start]**Goal:** Implement a basic gRPC service that returns a greeting[cite: 22].
[cite_start]**Technology:** Java (Gradle/Maven) using `io.grpc` and `protobuf-java`[cite: 22].

##### 1.1 The Proto File (`HelloWorld.proto`)

[cite_start]The `.proto` file defines the `GreetingService` with a `sayHello` RPC method, and the corresponding `HelloRequest` and `HelloResponse` messages[cite: 23, 24, 25].

```protobuf
syntax = "proto3";
// ... options ...

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

The server implements `GreetingServiceImpl` by extending the generated base class. [cite_start]It extracts the name, builds the greeting string, and sends the `HelloResponse`[cite: 26, 27, 28, 29].

```java
// Simplified Server Snippet
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

[cite_start]The client creates an insecure `ManagedChannel` to connect to `localhost:9090`[cite: 30]. [cite_start]It then uses a `BlockingStub` to make a synchronous `sayHello` RPC call, printing the resulting message[cite: 31, 32, 33].

```java
// Simplified Client Snippet
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

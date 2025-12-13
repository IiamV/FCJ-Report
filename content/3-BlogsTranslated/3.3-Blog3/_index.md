---
title: "Introducing the AWS IoT Device SDK for Swift (Developer Preview)"
date: "2025-08-14T10:00:00+07:00"
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

> by Vera Xia and Steve Kim | on 31 MAR 2025 | in [Announcements](https://aws.amazon.com/blogs/developer/category/post-types/announcements/), [AWS IoT Core](https://aws.amazon.com/blogs/developer/category/internet-of-things/aws-iot-platform/), [AWS SDK for Swift](https://aws.amazon.com/blogs/developer/category/developer-tools/aws-sdk-for-swift/), [Developer Tools](https://aws.amazon.com/blogs/developer/category/developer-tools/), [Intermediate (200)](https://aws.amazon.com/blogs/developer/category/learning-levels/intermediate-200/), [Internet of Things](https://aws.amazon.com/blogs/developer/category/internet-of-things/) | [Permalink](https://aws.amazon.com/blogs/developer/introducing-the-aws-iot-device-sdk-for-swift-developer-preview/) | [Share](https://aws.amazon.com/blogs/developer/introducing-the-aws-iot-device-sdk-for-swift-developer-preview/#)

Today, AWS launches the developer preview of the [AWS IoT Device SDK for Swift](https://github.com/aws/aws-iot-device-sdk-swift). The IoT Device SDK for Swift empowers Swift developers to create IoT applications for Linux and Apple macOS, iOS, and tvOS platforms using the MQTT 5 protocol.

The SDK supports Swift 5.10+ and is designed to help developers easily integrate with AWS IoT services.

## Key features of the AWS IoT Device SDK for Swift

* **Swift integration**: Leverages the modern Swift programming language to develop IoT applications.
* **Platform compatibility**: Designed to support macOS, iOS, tvOS, and Linux.
* **TLS 1.3 support**: Built-in TLS 1.3 support on Apple iOS and tvOS platforms, ensuring IoT applications use the latest industry-wide security practices for protecting data in-transit.MQTT version 5 (MQTT 5) support: MQTT 5 provides many benefits over earlier protocol versions, including error handling and reporting, enhanced scalability with shared subscriptions, and customizable user properties. See the [MQTT 5 User Guide](https://github.com/aws/aws-iot-device-sdk-swift/blob/main/Documentation/MQTT5_Userguide.md) to learn more.
* **Async APIs**: The SDK uses asynchronous APIs, which makes it easier for you to develop responsive applications.

## Getting started
The [AWS IoT Device SDK for Swift](https://github.com/aws/aws-iot-device-sdk-swift/blob/main/Samples/README.md) samples demonstrate simple and idiomatic ways to develop your applications using the MQTT 5 client to access AWS IoT services.

The following code sample demonstrates how to set up an MQTT client that uses mTLS for authentication.The `Mqtt5ClientBuilder` simplifies setting up the MQTT 5 client and connections using Swift.

```swift
let cert_filepath = "<certificate file path>"
let private_key_filepath = "<private key file path>"

let clientBuilder = try Mqtt5ClientBuilder.mtlsFromPath(
                                           certPath: cert_filepath, 
                                           keyPath: private_key_filepath, 
                                           endpoint: your_iot_endpoint)

let mqttClient = try clientBuilder.build()

// Open the connection after the client is built
try client.start()
```

After the client connects, you can subscribe to topics and publish messages on them. The publish and subscribe operations use an asynchronous API, making the code easy to reason about while benefiting from concurrent execution.

```swift
// Setup a publish packet
let publishPacket: PublishPacket = PublishPacket(qos: .atLeastOnce,
                                                 topic: "test/topic", 
                                                 payload: "HELLO WORLD!".data(using: .utf8))
// Async-await on publish operation
let publishResult: PublishResult = try await client.publish(
                                                 publishPacket: publishPacket)
if let puback = publishResult.puback {
    print(
        "Publish operation finished with reasonCode: \(puback.reasonCode)"
    )
}
```

The IoT Device SDK for Swift includes a full list of samples to demonstrate different connection methods:

* [Direct MQTT with Certificate and Private Key File](https://github.com/aws/aws-iot-device-sdk-swift/tree/main/Samples/Mqtt5ConnectionSamples/CertAndKeyFileConnect)
* [Direct MQTT with Custom Authentication](https://github.com/aws/aws-iot-device-sdk-swift/tree/main/Samples/Mqtt5ConnectionSamples/CustomAuthConnect)
* [Direct MQTT with PKCS12](https://github.com/aws/aws-iot-device-sdk-swift/tree/main/Samples/Mqtt5ConnectionSamples/Pkcs12Connect)
* [MQTT over WebSockets with Sigv4 authentication](https://github.com/aws/aws-iot-device-sdk-swift/tree/main/Samples/Mqtt5ConnectionSamples/Sigv4WebsocketConnect)
* [MQTT over WebSockets with Amazon Cognito Credential Provider](https://github.com/aws/aws-iot-device-sdk-swift/tree/main/Samples/Mqtt5ConnectionSamples/CognitoWebsocketConnect)

## iOS and tvOS support
With support for iOS and tvOS, the SDK integrates seamlessly with Apple’s keychain for secure credential storage and retrieval. To help you get started, we provide [a dedicated iOS PubSub sample](https://github.com/aws/aws-iot-device-sdk-swift/tree/main/Samples/iOS/iOSPubSubSample) that demonstrates MQTT 5 subscription and message publishing operations.

After you launch the sample app (as shown below), you can interact with it to set up an MQTT client. You can use the client to establish a connection to the [AWS IoT Core message broker](https://docs.aws.amazon.com/iot/latest/developerguide/protocols.html) and publish messages to AWS IoT Core. The sample app will also display any received messages on the test topic.

> <img src="/images/blogs/iOSAppScreenshot-472x1024.png" alt="" width="55%">

## Summary
This blog post provides a quick introduction to the new IoT device SDK for Swift. We will continue adding more features and enhancements in the coming months. Stay tuned to theSDK Github page for updates. Feel free to leave your feedback in the comments.

## Next steps
Ready to get started? To learn more, see the following resources:

* [What is AWS IoT?](https://docs.aws.amazon.com/iot/latest/developerguide/what-is-aws-iot.html)
* [MQTT 5 User Guide](https://github.com/aws/aws-iot-device-sdk-swift/blob/main/Documentation/MQTT5_Userguide.md)

TAGS: [aws](https://aws.amazon.com/blogs/developer/tag/aws/), [SDK](https://aws.amazon.com/blogs/developer/tag/sdk/), [Swift](https://aws.amazon.com/blogs/developer/tag/swift/)

| ![Avatar](/images/blogs/aws-avatar-default.png) | **Vera Xia** |
|--------------------------------------------------|--------------|
|                                                  |              |

| ![Avatar](/images/blogs/aws-avatar-default.png) | **Steve Kim**<br/>Software Engineer working within the AWS IoT Device SDK team. |
|--------------------------------------------------|-------------------------------------------------------------------------------|
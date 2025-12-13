---
title: "Giới thiệu AWS IoT Device SDK cho Swift (Developer Preview)"
date: "2025-08-14T10:00:00+07:00"
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

> bởi Vera Xia và Steve Kim | ngày 31 MAR 2025 | trong [Announcements](https://aws.amazon.com/blogs/developer/category/post-types/announcements/), [AWS IoT Core](https://aws.amazon.com/blogs/developer/category/internet-of-things/aws-iot-platform/), [AWS SDK for Swift](https://aws.amazon.com/blogs/developer/category/developer-tools/aws-sdk-for-swift/), [Developer Tools](https://aws.amazon.com/blogs/developer/category/developer-tools/), [Intermediate (200)](https://aws.amazon.com/blogs/developer/category/learning-levels/intermediate-200/), [Internet of Things](https://aws.amazon.com/blogs/developer/category/internet-of-things/) | [Permalink](https://aws.amazon.com/blogs/developer/introducing-the-aws-iot-device-sdk-for-swift-developer-preview/) | [Share](https://aws.amazon.com/blogs/developer/introducing-the-aws-iot-device-sdk-for-swift-developer-preview/#)

Hôm nay, AWS công bố developer preview của [AWS IoT Device SDK for Swift](https://github.com/aws/aws-iot-device-sdk-swift). IoT Device SDK for Swift trao quyền cho các Swift developer xây dựng các ứng dụng IoT cho Linux và các nền tảng Apple macOS, iOS và tvOS bằng cách sử dụng giao thức MQTT 5.

SDK hỗ trợ Swift 5.10+ và được thiết kế nhằm giúp developer dễ dàng tích hợp với các dịch vụ AWS IoT.

## Các tính năng chính của AWS IoT Device SDK for Swift

* **Swift integration**: Tận dụng ngôn ngữ lập trình Swift hiện đại để phát triển các ứng dụng IoT.
* **Platform compatibility**: Được thiết kế để hỗ trợ macOS, iOS, tvOS và Linux.
* **TLS 1.3 support**: Hỗ trợ TLS 1.3 tích hợp sẵn trên các nền tảng Apple iOS và tvOS, đảm bảo các ứng dụng IoT áp dụng những best practices bảo mật mới nhất của ngành nhằm bảo vệ dữ liệu trong quá trình truyền tải.
* **MQTT version 5 (MQTT 5) support**: MQTT 5 mang lại nhiều lợi ích so với các phiên bản giao thức trước đó, bao gồm cơ chế xử lý và báo lỗi tốt hơn, khả năng mở rộng cao hơn với shared subscriptions, và user properties có thể tùy chỉnh. Tham khảo [MQTT 5 User Guide](https://github.com/aws/aws-iot-device-sdk-swift/blob/main/Documentation/MQTT5_Userguide.md) để biết thêm chi tiết.
* **Async APIs**: SDK sử dụng các asynchronous APIs, giúp bạn dễ dàng phát triển các ứng dụng có khả năng phản hồi cao.

## Bắt đầu

Các sample của [AWS IoT Device SDK for Swift](https://github.com/aws/aws-iot-device-sdk-swift/blob/main/Samples/README.md) minh họa những cách đơn giản và chuẩn mực (idiomatic) để phát triển ứng dụng bằng cách sử dụng MQTT 5 client nhằm truy cập các dịch vụ AWS IoT.

Đoạn code mẫu sau minh họa cách thiết lập một MQTT client sử dụng mTLS cho mục đích authentication. Lớp `Mqtt5ClientBuilder` giúp đơn giản hóa việc cấu hình MQTT 5 client và các kết nối bằng Swift.

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

Sau khi client kết nối thành công, bạn có thể subscribe vào các topic và publish message lên các topic đó. Các thao tác publish và subscribe sử dụng asynchronous API, giúp code dễ đọc, dễ hiểu đồng thời tận dụng khả năng thực thi song song.

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

IoT Device SDK for Swift cung cấp đầy đủ các sample nhằm minh họa nhiều phương thức kết nối khác nhau:

* [Direct MQTT with Certificate and Private Key File](https://github.com/aws/aws-iot-device-sdk-swift/tree/main/Samples/Mqtt5ConnectionSamples/CertAndKeyFileConnect)
* [Direct MQTT with Custom Authentication](https://github.com/aws/aws-iot-device-sdk-swift/tree/main/Samples/Mqtt5ConnectionSamples/CustomAuthConnect)
* [Direct MQTT with PKCS12](https://github.com/aws/aws-iot-device-sdk-swift/tree/main/Samples/Mqtt5ConnectionSamples/Pkcs12Connect)
* [MQTT over WebSockets with Sigv4 authentication](https://github.com/aws/aws-iot-device-sdk-swift/tree/main/Samples/Mqtt5ConnectionSamples/Sigv4WebsocketConnect)
* [MQTT over WebSockets with Amazon Cognito Credential Provider](https://github.com/aws/aws-iot-device-sdk-swift/tree/main/Samples/Mqtt5ConnectionSamples/CognitoWebsocketConnect)

## Hỗ trợ iOS và tvOS

Với hỗ trợ cho iOS và tvOS, SDK tích hợp liền mạch với Apple Keychain để lưu trữ và truy xuất credentials một cách an toàn. Để giúp bạn bắt đầu nhanh chóng, chúng tôi cung cấp [một sample iOS PubSub chuyên biệt](https://github.com/aws/aws-iot-device-sdk-swift/tree/main/Samples/iOS/iOSPubSubSample), minh họa các thao tác subscribe MQTT 5 và publish message.

Sau khi khởi chạy sample app (như minh họa bên dưới), bạn có thể tương tác với ứng dụng để thiết lập MQTT client. Bạn có thể sử dụng client này để thiết lập kết nối tới [AWS IoT Core message broker](https://docs.aws.amazon.com/iot/latest/developerguide/protocols.html) và publish message lên AWS IoT Core. Sample app cũng sẽ hiển thị các message nhận được trên test topic.

> <img src="/images/blogs/iOSAppScreenshot-472x1024.png" alt="" width="55%">

## Tóm tắt

Bài blog này cung cấp phần giới thiệu nhanh về IoT Device SDK mới cho Swift. Chúng tôi sẽ tiếp tục bổ sung thêm nhiều tính năng và cải tiến trong các tháng tới. Hãy theo dõi trang Github của SDK để cập nhật thông tin mới nhất. Bạn cũng có thể để lại phản hồi của mình trong phần bình luận.

## Các bước tiếp theo

Sẵn sàng bắt đầu? Để tìm hiểu thêm, hãy tham khảo các tài nguyên sau:

* [What is AWS IoT?](https://docs.aws.amazon.com/iot/latest/developerguide/what-is-aws-iot.html)
* [MQTT 5 User Guide](https://github.com/aws/aws-iot-device-sdk-swift/blob/main/Documentation/MQTT5_Userguide.md)

TAGS: [aws](https://aws.amazon.com/blogs/developer/tag/aws/), [SDK](https://aws.amazon.com/blogs/developer/tag/sdk/), [Swift](https://aws.amazon.com/blogs/developer/tag/swift/)

| ![Avatar](/images/blogs/aws-avatar-default.png) | **Vera Xia** |
|--------------------------------------------------|--------------|
|                                                  |              |

| ![Avatar](/images/blogs/aws-avatar-default.png) | **Steve Kim**<br/>Software Engineer làm việc trong đội ngũ AWS IoT Device SDK. |
|--------------------------------------------------|--------------------------------------------------------------------------------|
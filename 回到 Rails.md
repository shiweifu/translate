# 回到 Rails



翻译自：[Moving my serverless project to Ruby on Rails / frantic.im](https://frantic.im/back-to-rails)





我有一个小项目：[digital gift cards for hackers](https://hacker.gifts/)。它使用 Shopify 处理所有和商店有关的东西：前台、支付、退款，报表，等等。



但与标准的数字产品不同（电子书，视频），我想用户从商店支付的每个卡片，都是唯一的。所以我写了个脚本，生成个人化的图片，并为每一个订单手动运行它。



接下来想要将这个过程自动化。一开始，我构建了一个 `serverless` AWS Lambda。此时这门技术正大热，我正想学习。在我的使用场景下，它看起来非常适合：每次都运行这个函数，而且服务端需求。



![img](./static/images/simple-lambda.png)






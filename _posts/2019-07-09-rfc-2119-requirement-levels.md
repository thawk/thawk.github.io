---
title: RFC 2119 文档中定义要求级别的关键字
layout: single
guid: eeb83e5900104205978d9405e166d215
date: 2019-07-09 16:07:26
categories:
  - 杂谈
tags:
  - 规范 
---

在看一份协议规范文档时，文档中提到其用语符合[RFC 2119][]，因此查阅了这份RFC，记录做个笔记：

* 有五种不同的要求级别：

  1.  **MUST**/**REQUIRED**/**SHALL**

      表明这是**必须**的特性。

  1.  **MUST NOT**/**SHALL NOT**

      表明这是被**严格禁止**的特性。

  1.  **SHOULD**/**RECOMMENDED**

      表明这是被**推荐**的特性，大部分情况下应采纳。如不采纳，应认真评估。

  1.  **SHOULD NOT**/**NOT RECOMMENDED**

      表明这是**不推荐**的特性，一般情况下不应使用。如要使用，应认真评估。

  1.  **MAY**/**OPTIONAL**

      表明这是**可选**的特性。但实现者必须能对接实现或不实现这个特性的另一方。

* 除了定义了上述关键字的用法外，RFC中还规定采用了本规范的文档，应该在靠近文档开始处加入一段声明：

      The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL
      NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",  "MAY", and
      "OPTIONAL" in this document are to be interpreted as described in
      RFC 2119.

* 本规范并未要求上述关键字的大小写形式，但[RFC 8174][]在此基础上要求上述关键字必须是全大写的才有特殊含义。声明文字也改为：

      The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL
      NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED",
      "MAY", and "OPTIONAL" in this document are to be interpreted as
      described in BCP 14 [RFC2119] [RFC8174] when, and only when, they
      appear in all capitals, as shown here.

[RFC 2119]: https://tools.ietf.org/html/rfc2119
[RFC 8174]: https://tools.ietf.org/html/rfc8174


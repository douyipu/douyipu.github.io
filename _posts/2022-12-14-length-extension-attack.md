---
layout: post
title: 长度扩展攻击
date: 2022-12-14 11:12:00-0400
description: SHA-256哈希函数对长度扩展攻击很脆弱，可以使用开源工具HashPump进行攻击，应采用HMAC函数对消息进行签名。
tags: 密码学 PentesterLab
giscus_comments: true
---

<br/>

## 1. 起因：SHA-256哈希函数用于签名不安全

<br/>

我在[PentesterLab](https://pentesterlab.com/)上做一道Python语言的Code Review。因为PentesterLab平台不希望 用户对PRO练习发布write-up，所以我简单描述一下代码功能。代码的主要功能是， 当用户选择购买一件商品时，将用户的支付信息提交给在线支付平台（如PayPal， 支付宝等），用户的支付信息由原始的支付信息和支付信息的哈希值2个部分组成。 支付信息的哈希值作为支付信息的签名。我没做出来，没想到是签名的部分出了问题。 

{% highlight python %}
return hashlib.sha256(data.encode('utf-8')).hexdigest()
{% endhighlight %}

代码有漏洞的原因是，SHA-256哈希函数不能用于签名。因为SHA-256哈希函数对 长度扩展攻击很脆弱，需要使用HMAC函数来获取哈希值。应该改为下面的代码： 

{% highlight python %}
import hmac
key = b'key'
return hmac.HMAC(key, data.encode('utf-8'),
                 digestmod=hashlib.sha512).hexdigest()
{% endhighlight %}

于是，我有3个疑问：
- 什么是长度扩展攻击？
- 为什么SHA-256哈希函数对长度扩展攻击很脆弱？
- 为什么HMAC函数可以抵抗长度扩展攻击？

<br/>

## 2. 什么是长度扩展攻击？

<br/>

你可能以为我会在这里引用一下长度扩展攻击的定义，但是这样对理解它并没有什么帮助。 我先来介绍一下SHA-256哈希函数，然后自然地引出它的算法设计是如何使它对长度 扩展攻击很脆弱。 

SHA-256哈希函数首先对输入的消息（message）进行一个预处理： 

- 在消息的最后添加一位比特“1”，这是为了防止当2个不同的消息前面的比特位 相同，而后面有不同个数的0时，如果不在末尾添加比特“1”，那么在填充0后 这两个不同的消息生成的哈希值将会相同。 

{% highlight bash %}
假设块的大小为16，长度小于16的消息都要在最后填充0。
如果不在最后添加比特“1”：
0110 010  --> 0110 0100 0000 0000
0110 0100 --> 0110 0100 0000 0000
如果在最后添加比特“1”：
0110 010  --> 0110 0101 0000 0000
0110 0100 --> 0110 0100 1000 0000		 
{% endhighlight %}

- 因为SHA-256哈希函数要将消息进行分块，每块的大小是512比特，所以需要对 输入的数据填充，使其长度是512比特的整数倍。在SHA-256哈希函数的设计中， 最后的64比特用来存储初始消息的比特长度。所以，最后一块中，除了最后添加 的比特“1”和倒数64比特之外，其余位置填充比特“0”。 

{% highlight bash %}
假设消息为字符串“abc”，用8比特的ASCII编码，一共有24比特，
所以需要填充512-24-1-64=423个比特“0”，填充结果如下：
01100001 01100010 01100011 1 00......0 00....011000
                             |--423--| |----64----|
{% endhighlight %}

在预处理之后，数据已经被填充为512比特的整数倍，然后按照每块512比特进行分块。 数据块依次处理，正常情况下的处理如下图。第一个数据块与初始向量作为输入， 通过压缩函数（compress function）生成输出，作为下一个压缩函数的输入之一， 以此类推，最后得到哈希值。 

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0" style="text-align:center;">
        {% include figure.html path="assets/img/SHA-256-normal.svg" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

当你想要添加恶意的信息时，恶意的处理如下图。攻击者通过SHA-256填充规则， 人为地在恶意信息前伪造填充部分。 

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0" style="text-align:center;">
        {% include figure.html path="assets/img/SHA-512-malicious.svg" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

最后的效果如下。这样，即使不知道之前的信息也可以构建出一个与添加了恶意信息 的恶意消息对应的合法哈希值。

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0" style="text-align:center;">
        {% include figure.html path="assets/img/SHA-512-normal-to-malicious.svg" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

我们可以使用开源工具[HashPump](https://github.com/bwall/HashPump)进行长度扩展攻击。HashPump的使用方式如下： 

{% highlight bash %}
$ hashpump -h
HashPump [-h help] [-t test] [-s signature] [-d data] [-a additional] [-k keylength]
    HashPump generates strings to exploit signatures vulnerable to the Hash Length Extension Attack.
    -h --help          Display this message.
    -t --test          Run tests to verify each algorithm is operating properly.
    -s --signature     The signature from known message.
    -d --data          The data from the known message.
    -a --additional    The information you would like to add to the known message.
    -k --keylength     The length in bytes of the key being used to sign the original message with.
    Version 1.2.0 with CRC32, MD5, SHA1, SHA256 and SHA512 support.
    <Developed by bwall(@botnet_hunter)>
{% endhighlight %}

因为SHA-256不需要加密密钥，所以传给-k的值不会影响最后的结果。HashPump支持的 算法有：MD5，SHA-1，SHA-256，SHA-512。 

{% highlight bash %}
hashpump -s '6d5f807e23db210bc254a28be2d6759a0f5f5d99' \
         -d 'transaction_id=393f5c77cfb4e3bcd3c037b70b5fd6b8&amount=20.00' \
         -a '&amount=1.00' -k 0
{% endhighlight %}

结果如下： 

{% highlight bash %}
1ff8b401835a00f4edfb0955c4edab40e479ad2a
transaction_id=393f5c77cfb4e3bcd3c037b70b5fd6b8&amount=20.00\x80\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x02\x10&amount=1.00
{% endhighlight %}

<br/>

## 3. 采用HMAC函数对消息签名

<br/>

根据RFC 2104，HMAC的数学公式为： 

\begin{equation}
HMAC(K,m)=H((K'\oplus opad)||H((K'\oplus ipad)||m))
\end{equation}

其中：
- $$H$$ 为密码散列函数（如SHA家族）
- $$K$$ 为密钥（secret key）
- $$m$$ 是要认证的消息
- $$K'$$ 是从原始密钥K导出的另一个秘密密钥（如果K短于散列函数的输入块大小， 则向右填充（Padding）零；如果比该块大小更长，则对K进行散列）
- $$\oplus$$ 代表异或（XOR）
- $$opad$$ 是外部填充（0x5c5c5c…5c5c，一段十六进制常量）
- $$ipad$$ 是内部填充（0x363636…3636，一段十六进制常量）

根据HMAC函数的定义可以知道，它可以抵御长度扩展攻击。：） 

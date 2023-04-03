---
layout: post
title: CoAP Analyzer with Spicy
date: 2023-04-03 11:12:00-0400
description: How to build a CoAP analyzer with Spicy and integrate it with Zeek.
tags: Zeek Spicy
giscus_comments: true
---

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0" style="text-align:center;">
        {% include figure.html path="assets/img/zeek-logo.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<br/>

## 1. Install Zeek and Spicy

<br/>

- OS: Ubuntu 22.04.2 VMware
- User: root
- Installed Software: Zeek 5.2.0
- The Official Document and Source to Install Zeek:
  - `https://docs.zeek.org/en/`
  - `https://software.opensuse.org/download.html?project=security%3Azeek&package=zeek`

1. Install Required Dependencies.

```shell
apt-get install curl cmake make gcc g++ flex libfl-dev bison libpcap-dev libssl-dev python3 python3-dev swig zlib1g-dev
```

2. Install Zeek.

```shell
echo 'deb http://download.opensuse.org/repositories/security:/zeek/xUbuntu_22.04/ /' | sudo tee /etc/apt/sources.list.d/security:zeek.list
curl -fsSL https://download.opensuse.org/repositories/security:zeek/xUbuntu_22.04/Release.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/security_zeek.gpg > /dev/null
sudo apt update
sudo apt install zeek
```

3. Add following code in the `.bashrc` file.

```shell
echo "export PATH=$PATH:/opt/zeek/bin" > .bashrc
```

4. Make it work.

```shell
source .bashrc
```

5. Test.

```shell
zeek -v
zeek version 5.2.0
```

## 2. Create project file with `spicy-protocol-analyzer `template

```shell
zkg create --features spicy-protocol-analyzer --packagedir spicy-coap
"package-template" requires a "name" value (the name of the package, e.g. "FooBar" or "spicy-http"): 
name: spicy-coap
"package-template" requires a "analyzer" value (name of the Spicy analyzer, which typically corresponds to the protocol/format being parsed (e.g. "HTTP", "PNG")): 
analyzer: CoAP
"package-template" requires a "protocol" value (transport protocol for the analyzer to use: TCP or UDP): 
protocol: UDP
"package-template" requires a "unit_orig" value (name of the top-level Spicy parsing unit for the originator side of the connection (e.g. "Request")): 
unit_orig: Message
"package-template" requires a "unit_resp" value (name of the top-level Spicy parsing unit for the responder side of the connection (e.g. "Reply"); may be the same as originator side): 
unit_resp: Message
```

## 3. Test and Integrate Spicy code with Zeek

1. Test Spicy code to parse GET CoAP traffic with spicy-driver.

```shell
cat GET.dat | spicy-driver coap.spicy
```

The output is:

```shell
[$ver_t_tkl=(1, MessageType::CONFIRMABLE, 0), $version=1, $code=Code::GET, $message_id=51378, $t=b"", $preceding_option_number=26, $options=[[$delta_length=(11, 5), $payload=(not set), $value_bytes=b"basic", $delta=11, $length=5, $option_ID=OptionID::URI_PATH, $value="basic"]], $token="", $payload=""]
```

Test Spicy code to parse POST CoAP traffic with spicy-driver.

```shell
cat POST.dat | spicy-driver coap.spicy
```

The output is:

```shell
[$ver_t_tkl=(1, MessageType::CONFIRMABLE, 2), $version=1, $code=Code::POST, $message_id=53074, $t=b"30", $preceding_option_number=27, $options=[[$delta_length=(11, 5), $payload=(not set), $value_bytes=b"basic", $delta=11, $length=5, $option_ID=OptionID::URI_PATH, $value="basic"], [$delta_length=(1, 1), $payload=(not set), $value_bytes=b"\x00", $delta=1, $length=1, $option_ID=OptionID::CONTENT_FORMAT, $value="text/plain; charset=utf-8"]], $token="30", $payload="dummy-packets-49962"]
```

2. Compile analyzer to *.hlto file.

```shell
spicyz -o coap.hlto coap.spicy coap.evt zeek_coap.spicy
```

3. Copy *.hlto file into /opt/zeek/lib/zeek-spicy/modules.

4. Check out Zeek Spicy plugin: `zeek -NN Zeek::Spicy`.

The output should contains these.

```shell
[Analyzer] spicy_CoAP (ANALYZER_SPICY_COAP, enabled)
[Type] CoAP::MessageType
[Type] CoAP::Code
[Type] CoAP::OptionID
```

5. Use Zeek to load main.zeek to create log file in JSON format.

```shell
zeek -Cr Test.pcap main.zeek LogAscii::use_json=T
```

6. Use `jq` tool to see the log file: `jq . coap.log`.

```json
{
  "ts": 1618846973.65904,
  "uid": "CwGKCs3fAA0WXJMNN3",
  "id.orig_h": "10.0.0.2",
  "id.orig_p": 56955,
  "id.resp_h": "10.0.0.6",
  "id.resp_p": 5683,
  "version": 1,
  "message_type": "CoAP::MessageType_CONFIRMABLE",
  "code": "CoAP::Code_GET",
  "message_id": 53404,
  "token": "Le",
  "option_id": [
    "CoAP::OptionID_URI_PATH"
  ],
  "option_value": [
    "basic"
  ]
}
{
  "ts": 1618846973.688292,
  "uid": "CwGKCs3fAA0WXJMNN3",
  "id.orig_h": "10.0.0.2",
  "id.orig_p": 56955,
  "id.resp_h": "10.0.0.6",
  "id.resp_p": 5683,
  "version": 1,
  "message_type": "CoAP::MessageType_CONFIRMABLE",
  "code": "CoAP::Code_POST",
  "message_id": 53405,
  "token": "XP",
  "option_id": [
    "CoAP::OptionID_URI_PATH"
  ],
  "option_value": [
    "basic"
  ],
  "payload": "dummydata-9948"
}
{
  "ts": 1618846973.710531,
  "uid": "CwGKCs3fAA0WXJMNN3",
  "id.orig_h": "10.0.0.2",
  "id.orig_p": 56955,
  "id.resp_h": "10.0.0.6",
  "id.resp_p": 5683,
  "version": 1,
  "message_type": "CoAP::MessageType_CONFIRMABLE",
  "code": "CoAP::Code_GET",
  "message_id": 53406,
  "token": "qp",
  "option_id": [
    "CoAP::OptionID_URI_PATH"
  ],
  "option_value": [
    "basic"
  ]
}
{
  "ts": 1618846973.732999,
  "uid": "CwGKCs3fAA0WXJMNN3",
  "id.orig_h": "10.0.0.2",
  "id.orig_p": 56955,
  "id.resp_h": "10.0.0.6",
  "id.resp_p": 5683,
  "version": 1,
  "message_type": "CoAP::MessageType_CONFIRMABLE",
  "code": "CoAP::Code_POST",
  "message_id": 53407,
  "token": "cK",
  "option_id": [
    "CoAP::OptionID_URI_PATH"
  ],
  "option_value": [
    "basic"
  ],
  "payload": "dummydata-9949"
}
```

## 2. Change these files and write your code.

```shell
├── analyzer
│   ├── coap.evt
│   ├── coap.spicy
│   └── zeek_coap.spicy
├── scripts
    ├── __load__.zeek
    ├── dpd.sig
    └── main.zeek
```

## 3. Build the Spicy analyzer.

```shell
root@zeek-ubuntu:~/spicy-coap# rm -rf build
root@zeek-ubuntu:~/spicy-coap# mkdir build
root@zeek-ubuntu:~/spicy-coap# cd build
root@zeek-ubuntu:~/spicy-coap/build# cmake ..
root@zeek-ubuntu:~/spicy-coap/build# cmake --build .
```

## 4. Test the Spicy analyzer.

1. Put your PCAP files under `spicy-coap/testing/Traces` directory.

2. Change `spicy-coap/testing/tests/standalone.spicy` and `spicy-coap/testing/tests/trace.zeek` testing commands.

3. Update `Baseline` directory files.

```shell
root@zeek-ubuntu:~/spicy-coap/testing# btest -U
```

4. Test.

```shell    
root@zeek-ubuntu:~/spicy-coap/testing# btest
```

## 5. Install the analyzer and verify it.

```shell
zkg install .
zeek -NN Zeek::Spicy
```

## 6. Run Zeek with CoAP analyzer.

Add `spicy-coap` if you want to generate `coap.log` file.

```shell
zeek -Cr Test.pcap spicy-coap
```

It's the format of `coap.log` in JSON.

```json
{
  "ts": 1618846973.710531,
  "uid": "CzQ5u56kn79ScgWC7",
  "id.orig_h": "10.0.0.2",
  "id.orig_p": 56955,
  "id.resp_h": "10.0.0.6",
  "id.resp_p": 5683,
  "version": 1,
  "message_type": "CoAP::MessageType_CONFIRMABLE",
  "code": "CoAP::Code_GET",
  "message_id": 53406,
  "token": "qp",
  "option_id": [
    "CoAP::OptionID_URI_PATH"
  ],
  "option_value": [
    "basic"
  ]
}
{
  "ts": 1618846973.732999,
  "uid": "CzQ5u56kn79ScgWC7",
  "id.orig_h": "10.0.0.2",
  "id.orig_p": 56955,
  "id.resp_h": "10.0.0.6",
  "id.resp_p": 5683,
  "version": 1,
  "message_type": "CoAP::MessageType_CONFIRMABLE",
  "code": "CoAP::Code_POST",
  "message_id": 53407,
  "token": "cK",
  "option_id": [
    "CoAP::OptionID_URI_PATH"
  ],
  "option_value": [
    "basic"
  ],
  "payload": "dummydata-9949"
}
```

If you want to load all your installed packages, add `@load packages` in `/opt/zeek/share/zeek/site/local.zeek` file. And add `local` or `local.zeek` when you use Zeek.

```shell
zeek -Cr Test.pcap local
```

## References

- [Zeek Documentation](https://docs.zeek.org/en/master/)
- [Spicy Documentation](https://docs.zeek.org/projects/spicy/en/latest/index.html)
- [Anatomy Of A Zeek Spicy Protocol Analyzer](https://www.youtube.com/watch?v=wmm-6ZggwNc&t=1086s) YouTube Video from Keith Jones

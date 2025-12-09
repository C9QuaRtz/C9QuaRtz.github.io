---
title: 压力测试
description: 初识 Opus 4.5
slug: stress-test
date: 2025-12-09 17:42:39+0800
image: IMG_2651_596x832_bw.PNG
categories:
    - gpt
# weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

# AttributeError in replay_request.py
_Exported on 12/4/2025 at 21:25:44 GMT+8 from Cursor (2.1.46)_

---

**User**

阅读这个har包，完成一个Python程序以重放此请求。

请直接在Python中复制相关字段，不要引用读写原文件

---

**Cursor**

查看 HAR 文件内容，以便编写 Python 重放脚本。


基于 HAR 内容编写 Python 脚本，直接在代码中硬编码相关字段以重放请求。


```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
"""
重放 HAR 文件中的请求
"""

import requests
from requests_toolbelt.multipart.encoder import MultipartEncoder

def replay_request():
    """重放 HAR 文件中记录的请求"""
    
    # 请求 URL
    url = "https://go.heytea.com/api/service-cps/user/diy"
    
    # URL 查询参数
    params = {
        "sign": "805f30f5c3cb85e1779ae3daacf8dce5",
        "t": "1764833231217",
        "hash": "fc8aad8184823f1352f85228236f625c679f39c8d5ac4d86359af0572dc9c621c7b976db4c13fd219d28193536058f304031ff6d446277cd40009e4998ee8e1d31cd1c79e4075c5b1cba897d42d81451",
        "shopId": "4216"
    }
    
    # 请求头
    headers = {
        "Host": "go.heytea.com",
        "Connection": "keep-alive",
        "X-client": "weapp",
        "X-client-version": "3.318.0.p8",
        "Authorization": "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiIxNDIzMDkwOTAiLCJ1c2VyX21haW5faWQiOjE2NjY1ODMxNiwiY2hhbm5lbCI6IlciLCJzb3VyY2UiOiJhcGkiLCJpc19ndWVzdCI6ZmFsc2UsImxhYmVsIjoiY2xpZW50OndlYXBwIiwiaWF0IjoxNzY0ODMzMDIyLCJuYmYiOjE3NjQ4MzMwMjIsImV4cCI6MTc2NDkxOTQyMiwiaXNzIjoiaGV5dGVhIn0.33TKm1_8NVttopiFGwpXEasOxmy9n9I0yixsa3goMQQ",
        "Accept-Encoding": "gzip,compress,br,deflate",
        "User-Agent": "Mozilla/5.0 (iPhone; CPU iPhone OS 18_7 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Mobile/15E148 MicroMessenger/8.0.64(0x18004034) NetType/WIFI Language/zh_CN",
        "Referer": "https://servicewechat.com/wx696a42df4f2456d3/1255/page-frame.html"
    }
    
    # 构建 multipart/form-data 请求体
    # 注意：HAR 文件中没有包含实际的 multipart 数据内容
    # 这里需要根据实际情况填充字段
    # boundary 从原始请求头中提取
    boundary = "WABoundary+E24E9DF9599D28BAWA"
    
    # 由于 HAR 文件中没有实际的 postData.text，这里提供一个模板
    # 您需要根据实际情况添加字段
    multipart_data = MultipartEncoder(
        fields={
            # 在这里添加实际的表单字段
            # 例如：'file': ('filename.jpg', file_data, 'image/jpeg'),
            # 或者：'key': 'value',
        },
        boundary=boundary
    )
    
    headers["Content-Type"] = multipart_data.content_type
    
    # 发送请求
    try:
        print(f"正在发送请求到: {url}")
        print(f"查询参数: {params}")
        print(f"请求方法: POST")
        
        response = requests.post(
            url,
            params=params,
            headers=headers,
            data=multipart_data,
            timeout=30
        )
        
        print(f"\n响应状态码: {response.status_code}")
        print(f"响应头:")
        for key, value in response.headers.items():
            print(f"  {key}: {value}")
        
        print(f"\n响应内容:")
        try:
            print(response.json())
        except:
            print(response.text)
        
        return response
        
    except requests.exceptions.RequestException as e:
        print(f"请求失败: {e}")
        return None


if __name__ == "__main__":
    replay_request()

```

检查代码中的问题，并优化脚本：


已创建 Python 脚本以重放请求。注意：

1. HAR 文件中未包含实际的 multipart/form-data 数据（只有 Content-Type 和 boundary），但 Content-Length 为 22557，说明存在请求体数据。
2. 脚本已硬编码所有请求头、URL 和查询参数。
3. 需要在 `multipart_data` 的 `fields` 中添加实际表单字段（例如上传的文件或表单数据）。

如果需要，我可以：
- 从 HAR 中提取完整的 postData（如果存在）
- 提供一个更具体的 multipart 数据示例

运行脚本需要先安装依赖：

```bash
pip install requests requests-toolbelt
```

该脚本包含从 HAR 中提取的所有请求信息，可直接运行。如果知道具体的 multipart 字段内容，我可以进一步完善脚本。

```shellscript
pip install requests requests-toolbelt
```

---

**User**

@replay_request.py:51-52运行时报错： AttributeError: '_io.BufferedReader' object has no attribute 'translate'

---

**Cursor**

问题在于 `MultipartEncoder` 的文件字段格式不正确。应使用三元组 `(filename, file_object, content_type)`，而不是二元组。修复中：


修复文件字段格式：`MultipartEncoder` 需要三元组 `(filename, file_data, content_type)`，不是二元组。


```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
"""
重放 HAR 文件中的请求
"""

import requests
from requests_toolbelt.multipart.encoder import MultipartEncoder

def replay_request():
    """重放 HAR 文件中记录的请求"""
    
    # 请求 URL
    url = "https://go.heytea.com/api/service-cps/user/diy"
    
    # URL 查询参数
    params = {
        "sign": "805f30f5c3cb85e1779ae3daacf8dce5",
        "t": "1764833231217",
        "hash": "fc8aad8184823f1352f85228236f625c679f39c8d5ac4d86359af0572dc9c621c7b976db4c13fd219d28193536058f304031ff6d446277cd40009e4998ee8e1d31cd1c79e4075c5b1cba897d42d81451",
        "shopId": "4216"
    }
    
    # 请求头
    headers = {
        "Host": "go.heytea.com",
        "Connection": "keep-alive",
        "X-client": "weapp",
        "X-client-version": "3.318.0.p8",
        "Authorization": "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiIxNDIzMDkwOTAiLCJ1c2VyX21haW5faWQiOjE2NjY1ODMxNiwiY2hhbm5lbCI6IlciLCJzb3VyY2UiOiJhcGkiLCJpc19ndWVzdCI6ZmFsc2UsImxhYmVsIjoiY2xpZW50OndlYXBwIiwiaWF0IjoxNzY0ODMzMDIyLCJuYmYiOjE3NjQ4MzMwMjIsImV4cCI6MTc2NDkxOTQyMiwiaXNzIjoiaGV5dGVhIn0.33TKm1_8NVttopiFGwpXEasOxmy9n9I0yixsa3goMQQ",
        "Accept-Encoding": "gzip,compress,br,deflate",
        "User-Agent": "Mozilla/5.0 (iPhone; CPU iPhone OS 18_7 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Mobile/15E148 MicroMessenger/8.0.64(0x18004034) NetType/WIFI Language/zh_CN",
        "Referer": "https://servicewechat.com/wx696a42df4f2456d3/1255/page-frame.html"
    }
    
    # 构建 multipart/form-data 请求体
    # 注意：HAR 文件中没有包含实际的 multipart 数据内容
    # 这里需要根据实际情况填充字段
    # boundary 从原始请求头中提取
    boundary = "WABoundary+E24E9DF9599D28BAWA"
    
    # 读取文件内容
    with open("test.png", "rb") as f:
        file_data = f.read()
    
    # 构建 multipart 表单数据
    # MultipartEncoder 要求文件字段格式为 (filename, file_data, content_type)
    multipart_data = MultipartEncoder(
        fields={
            "width": "596",
            "height": "832",
            "file": ("test.png", file_data, "image/png")
        },
        boundary=boundary
    )
    
    headers["Content-Type"] = multipart_data.content_type
    
    # 发送请求
    try:
        print(f"正在发送请求到: {url}")
        print(f"查询参数: {params}")
        print(f"请求方法: POST")
        
        response = requests.post(
            url,
            params=params,
            headers=headers,
            data=multipart_data,
            timeout=30
        )
        
        print(f"\n响应状态码: {response.status_code}")
        print(f"响应头:")
        for key, value in response.headers.items():
            print(f"  {key}: {value}")
        
        print(f"\n响应内容:")
        try:
            print(response.json())
        except:
            print(response.text)
        
        return response
        
    except requests.exceptions.RequestException as e:
        print(f"请求失败: {e}")
        return None


if __name__ == "__main__":
    replay_request()


```

已修复。问题是 `MultipartEncoder` 的文件字段需要使用三元组 `(filename, file_data, content_type)`，而不是二元组。

主要修改：
1. 将文件字段从 `(open("test.png", "rb"), "image/png")` 改为 `("test.png", file_data, "image/png")`
2. 先读取文件内容到 `file_data`，使用 `with` 语句确保文件正确关闭
3. 使用正确的三元组格式：文件名、文件数据（字节）、内容类型

现在应可正常运行。请重新运行脚本测试。

---

**User**

@replay_request.py:20 这段hash看起来像哪一种算法？ @heytea_diy_sign.py (77-78) 

---

**Cursor**

分析这个 hash 的特征，推断其算法类型。


分析该 hash 的长度和特征：


进一步分析可能的组合：


根据分析，这个 hash 长度为 160 个字符（640 比特），很可能是一个组合哈希。

最可能的组合是：SHA512 (128 字符) + MD5 (32 字符) = 160 字符

原因：
1. 长度匹配：前 128 字符（可能是 SHA512）+ 后 32 字符（可能是 MD5）= 160 字符
2. 常见做法：微信小程序常用多哈希组合增强安全性
3. 其他可能：
   - 两个 SHA256 (64+64) + 部分哈希 (32)
   - 自定义哈希算法

结合请求参数（`sign`、`t`、`hash`、`shopId`），这是典型的 API 签名机制，通常做法是：
- 将请求参数按规则排序拼接
- 先计算 SHA512
- 再对结果或原始数据计算 MD5
- 将两者拼接作为最终 hash

要验证，可以尝试用 SHA512+MD5 的组合方式对请求参数进行哈希计算。

---

**User**

请另写一段代码，研究这段hash与sign、t、shopID与图片文件tmp_32e6dbcc2678d4d40a297ca5490acc4d.png之间的关系

---

**Cursor**

我来编写一个分析脚本，研究这些参数之间的关系：

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
"""
分析 hash、sign、t、shopId 与图片文件之间的关系
"""

import hashlib
import hmac
import os

# 已知参数
SIGN = "805f30f5c3cb85e1779ae3daacf8dce5"  # 32字符，MD5格式
T = "1764833231217"  # 时间戳
HASH = "fc8aad8184823f1352f85228236f625c679f39c8d5ac4d86359af0572dc9c621c7b976db4c13fd219d28193536058f304031ff6d446277cd40009e4998ee8e1d31cd1c79e4075c5b1cba897d42d81451"
SHOP_ID = "4216"
FILENAME = "tmp_32e6dbcc2678d4d40a297ca5490acc4d.png"

# 从文件名中提取的hash部分
FILENAME_HASH = "32e6dbcc2678d4d40a297ca5490acc4d"  # 32字符，MD5格式

# 使用 test.png 作为测试图片
IMAGE_FILE = "test.png"


def md5(data):
    """计算 MD5"""
    if isinstance(data, str):
        data = data.encode('utf-8')
    return hashlib.md5(data).hexdigest()


def sha1(data):
    """计算 SHA1"""
    if isinstance(data, str):
        data = data.encode('utf-8')
    return hashlib.sha1(data).hexdigest()


def sha256(data):
    """计算 SHA256"""
    if isinstance(data, str):
        data = data.encode('utf-8')
    return hashlib.sha256(data).hexdigest()


def sha512(data):
    """计算 SHA512"""
    if isinstance(data, str):
        data = data.encode('utf-8')
    return hashlib.sha512(data).hexdigest()


def print_section(title):
    print("\n" + "=" * 60)
    print(f" {title}")
    print("=" * 60)


def analyze_known_values():
    """分析已知值的特征"""
    print_section("已知值分析")
    
    print(f"sign: {SIGN}")
    print(f"  长度: {len(SIGN)} 字符 -> MD5 格式")
    
    print(f"\nt: {T}")
    print(f"  长度: {len(T)} 字符")
    print(f"  看起来是毫秒级时间戳")
    
    print(f"\nhash: {HASH}")
    print(f"  长度: {len(HASH)} 字符 = {len(HASH) * 4} bits")
    print(f"  前 128 字符: {HASH[:128]}")
    print(f"  后 32 字符:  {HASH[128:]}")
    
    print(f"\nshopId: {SHOP_ID}")
    
    print(f"\n文件名: {FILENAME}")
    print(f"  文件名中的hash部分: {FILENAME_HASH}")
    print(f"  长度: {len(FILENAME_HASH)} 字符 -> MD5 格式")


def analyze_filename_hash():
    """分析文件名中的 hash"""
    print_section("文件名 Hash 分析")
    
    if not os.path.exists(IMAGE_FILE):
        print(f"警告: {IMAGE_FILE} 不存在，跳过图片内容分析")
        return
    
    with open(IMAGE_FILE, "rb") as f:
        file_content = f.read()
    
    file_md5 = md5(file_content)
    file_sha1 = sha1(file_content)
    file_sha256 = sha256(file_content)
    
    print(f"图片文件 MD5:    {file_md5}")
    print(f"图片文件 SHA1:   {file_sha1}")
    print(f"图片文件 SHA256: {file_sha256}")
    print(f"\n文件名中的 hash: {FILENAME_HASH}")
    
    if file_md5 == FILENAME_HASH:
        print("✓ 文件名中的 hash 是图片文件的 MD5！")
    else:
        print("✗ 文件名中的 hash 不是图片文件的 MD5")


def analyze_sign():
    """分析 sign 参数"""
    print_section("Sign 参数分析")
    
    # 尝试各种组合
    test_cases = [
        ("t", T),
        ("shopId", SHOP_ID),
        ("t+shopId", T + SHOP_ID),
        ("shopId+t", SHOP_ID + T),
        ("t&shopId=value", f"t={T}&shopId={SHOP_ID}"),
        ("shopId=value&t", f"shopId={SHOP_ID}&t={T}"),
        ("filename_hash", FILENAME_HASH),
        ("t+filename_hash", T + FILENAME_HASH),
        ("filename_hash+t", FILENAME_HASH + T),
        ("t+shopId+filename_hash", T + SHOP_ID + FILENAME_HASH),
        ("filename_hash+shopId+t", FILENAME_HASH + SHOP_ID + T),
    ]
    
    print(f"目标 sign: {SIGN}\n")
    print("尝试各种组合的 MD5:")
    
    for name, value in test_cases:
        result = md5(value)
        match = "✓" if result == SIGN else " "
        print(f"  {match} MD5({name}): {result}")


def analyze_hash_structure():
    """分析 hash 参数结构"""
    print_section("Hash 参数结构分析")
    
    hash_part1 = HASH[:128]  # 可能是 SHA512
    hash_part2 = HASH[128:]  # 可能是 MD5
    
    print(f"Hash 前 128 字符（疑似 SHA512）:")
    print(f"  {hash_part1}")
    
    print(f"\nHash 后 32 字符（疑似 MD5）:")
    print(f"  {hash_part2}")
    
    # 检查后32字符是否与某些已知值匹配
    print(f"\n后 32 字符与已知值对比:")
    print(f"  sign:          {SIGN}")
    print(f"  filename_hash: {FILENAME_HASH}")
    print(f"  hash后32字符:  {hash_part2}")
    
    if hash_part2 == SIGN:
        print("  ✓ 后 32 字符等于 sign！")
    if hash_part2 == FILENAME_HASH:
        print("  ✓ 后 32 字符等于文件名中的 hash！")


def try_hash_combinations():
    """尝试各种 hash 组合"""
    print_section("Hash 组合尝试")
    
    hash_part1 = HASH[:128]
    hash_part2 = HASH[128:]
    
    # 加载图片文件
    file_content = b""
    if os.path.exists(IMAGE_FILE):
        with open(IMAGE_FILE, "rb") as f:
            file_content = f.read()
    
    params_combinations = [
        ("t", T),
        ("shopId", SHOP_ID),
        ("sign", SIGN),
        ("filename_hash", FILENAME_HASH),
        ("t+shopId", T + SHOP_ID),
        ("shopId+t", SHOP_ID + T),
        ("sign+t", SIGN + T),
        ("t+sign", T + SIGN),
        ("filename_hash+t", FILENAME_HASH + T),
        ("t+filename_hash", T + FILENAME_HASH),
        ("t+shopId+sign", T + SHOP_ID + SIGN),
        ("sign+shopId+t", SIGN + SHOP_ID + T),
    ]
    
    print("尝试 SHA512 匹配前 128 字符:")
    for name, value in params_combinations:
        result = sha512(value)
        if result == hash_part1:
            print(f"  ✓ SHA512({name}) 匹配！")
        elif result[:64] == hash_part1[:64]:
            print(f"  ~ SHA512({name}) 前半部分匹配")
    
    print("\n尝试 MD5 匹配后 32 字符:")
    for name, value in params_combinations:
        result = md5(value)
        if result == hash_part2:
            print(f"  ✓ MD5({name}) 匹配！")


def analyze_encryption():
    """分析可能的加密方式"""
    print_section("可能的加密/签名方式")
    
    print("""
基于分析，这个 API 签名机制可能是：

1. sign (32字符 MD5):
   - 可能是 MD5(某些参数组合)
   - 或者 MD5(某个密钥 + 参数)

2. hash (160字符 = SHA512 + MD5):
   - 前 128 字符: SHA512(某些数据)
   - 后 32 字符: MD5(某些数据)
   
3. 文件名中的 hash:
   - tmp_{MD5}.png 格式
   - 可能是文件内容的 MD5

4. 常见的微信小程序签名方式:
   - 参数按字母排序
   - 拼接成 key=value&key=value 格式
   - 加上 app_secret 或 token
   - 计算 MD5 或 SHA 系列

需要更多请求样本来确定具体的签名算法。
可以尝试修改参数后抓包，对比 sign 和 hash 的变化。
""")


def deep_analysis():
    """深度分析"""
    print_section("深度分析 - 寻找模式")
    
    # 分析 hash 的组成
    print("Hash 按 64 字符分割（SHA256 长度）:")
    for i in range(0, len(HASH), 64):
        chunk = HASH[i:i+64]
        print(f"  [{i:3d}-{i+len(chunk):3d}]: {chunk}")
    
    print("\nHash 按 32 字符分割（MD5 长度）:")
    for i in range(0, len(HASH), 32):
        chunk = HASH[i:i+32]
        print(f"  [{i:3d}-{i+len(chunk):3d}]: {chunk}")
    
    # 检查是否有重复模式
    print("\n检查是否有子串重复:")
    for i in range(0, len(HASH) - 32, 32):
        chunk = HASH[i:i+32]
        if chunk == SIGN:
            print(f"  ✓ 位置 {i}-{i+32} 与 sign 相同")
        if chunk == FILENAME_HASH:
            print(f"  ✓ 位置 {i}-{i+32} 与文件名 hash 相同")


if __name__ == "__main__":
    print("=" * 60)
    print(" Hash 与参数关系分析")
    print("=" * 60)
    
    analyze_known_values()
    analyze_filename_hash()
    analyze_sign()
    analyze_hash_structure()
    try_hash_combinations()
    deep_analysis()
    analyze_encryption()

```

分析完成！根据结果，我来总结发现并添加更多分析：

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
"""
分析 hash、sign、t、shopId 与图片文件之间的关系
"""

import hashlib
import hmac
import os

# 已知参数
SIGN = "805f30f5c3cb85e1779ae3daacf8dce5"  # 32字符，MD5格式
T = "1764833231217"  # 时间戳
HASH = "fc8aad8184823f1352f85228236f625c679f39c8d5ac4d86359af0572dc9c621c7b976db4c13fd219d28193536058f304031ff6d446277cd40009e4998ee8e1d31cd1c79e4075c5b1cba897d42d81451"
SHOP_ID = "4216"
FILENAME = "tmp_32e6dbcc2678d4d40a297ca5490acc4d.png"

# 从文件名中提取的hash部分
FILENAME_HASH = "32e6dbcc2678d4d40a297ca5490acc4d"  # 32字符，MD5格式

# 使用 test.png 作为测试图片
IMAGE_FILE = "test.png"


def md5(data):
    """计算 MD5"""
    if isinstance(data, str):
        data = data.encode('utf-8')
    return hashlib.md5(data).hexdigest()


def sha1(data):
    """计算 SHA1"""
    if isinstance(data, str):
        data = data.encode('utf-8')
    return hashlib.sha1(data).hexdigest()


def sha256(data):
    """计算 SHA256"""
    if isinstance(data, str):
        data = data.encode('utf-8')
    return hashlib.sha256(data).hexdigest()


def sha512(data):
    """计算 SHA512"""
    if isinstance(data, str):
        data = data.encode('utf-8')
    return hashlib.sha512(data).hexdigest()


def print_section(title):
    print("\n" + "=" * 60)
    print(f" {title}")
    print("=" * 60)


def analyze_known_values():
    """分析已知值的特征"""
    print_section("已知值分析")
    
    print(f"sign: {SIGN}")
    print(f"  长度: {len(SIGN)} 字符 -> MD5 格式")
    
    print(f"\nt: {T}")
    print(f"  长度: {len(T)} 字符")
    print(f"  看起来是毫秒级时间戳")
    
    print(f"\nhash: {HASH}")
    print(f"  长度: {len(HASH)} 字符 = {len(HASH) * 4} bits")
    print(f"  前 128 字符: {HASH[:128]}")
    print(f"  后 32 字符:  {HASH[128:]}")
    
    print(f"\nshopId: {SHOP_ID}")
    
    print(f"\n文件名: {FILENAME}")
    print(f"  文件名中的hash部分: {FILENAME_HASH}")
    print(f"  长度: {len(FILENAME_HASH)} 字符 -> MD5 格式")


def analyze_filename_hash():
    """分析文件名中的 hash"""
    print_section("文件名 Hash 分析")
    
    if not os.path.exists(IMAGE_FILE):
        print(f"警告: {IMAGE_FILE} 不存在，跳过图片内容分析")
        return
    
    with open(IMAGE_FILE, "rb") as f:
        file_content = f.read()
    
    file_md5 = md5(file_content)
    file_sha1 = sha1(file_content)
    file_sha256 = sha256(file_content)
    
    print(f"图片文件 MD5:    {file_md5}")
    print(f"图片文件 SHA1:   {file_sha1}")
    print(f"图片文件 SHA256: {file_sha256}")
    print(f"\n文件名中的 hash: {FILENAME_HASH}")
    
    if file_md5 == FILENAME_HASH:
        print("✓ 文件名中的 hash 是图片文件的 MD5！")
    else:
        print("✗ 文件名中的 hash 不是图片文件的 MD5")


def analyze_sign():
    """分析 sign 参数"""
    print_section("Sign 参数分析")
    
    # 尝试各种组合
    test_cases = [
        ("t", T),
        ("shopId", SHOP_ID),
        ("t+shopId", T + SHOP_ID),
        ("shopId+t", SHOP_ID + T),
        ("t&shopId=value", f"t={T}&shopId={SHOP_ID}"),
        ("shopId=value&t", f"shopId={SHOP_ID}&t={T}"),
        ("filename_hash", FILENAME_HASH),
        ("t+filename_hash", T + FILENAME_HASH),
        ("filename_hash+t", FILENAME_HASH + T),
        ("t+shopId+filename_hash", T + SHOP_ID + FILENAME_HASH),
        ("filename_hash+shopId+t", FILENAME_HASH + SHOP_ID + T),
    ]
    
    print(f"目标 sign: {SIGN}\n")
    print("尝试各种组合的 MD5:")
    
    for name, value in test_cases:
        result = md5(value)
        match = "✓" if result == SIGN else " "
        print(f"  {match} MD5({name}): {result}")


def analyze_hash_structure():
    """分析 hash 参数结构"""
    print_section("Hash 参数结构分析")
    
    hash_part1 = HASH[:128]  # 可能是 SHA512
    hash_part2 = HASH[128:]  # 可能是 MD5
    
    print(f"Hash 前 128 字符（疑似 SHA512）:")
    print(f"  {hash_part1}")
    
    print(f"\nHash 后 32 字符（疑似 MD5）:")
    print(f"  {hash_part2}")
    
    # 检查后32字符是否与某些已知值匹配
    print(f"\n后 32 字符与已知值对比:")
    print(f"  sign:          {SIGN}")
    print(f"  filename_hash: {FILENAME_HASH}")
    print(f"  hash后32字符:  {hash_part2}")
    
    if hash_part2 == SIGN:
        print("  ✓ 后 32 字符等于 sign！")
    if hash_part2 == FILENAME_HASH:
        print("  ✓ 后 32 字符等于文件名中的 hash！")


def try_hash_combinations():
    """尝试各种 hash 组合"""
    print_section("Hash 组合尝试")
    
    hash_part1 = HASH[:128]
    hash_part2 = HASH[128:]
    
    # 加载图片文件
    file_content = b""
    if os.path.exists(IMAGE_FILE):
        with open(IMAGE_FILE, "rb") as f:
            file_content = f.read()
    
    params_combinations = [
        ("t", T),
        ("shopId", SHOP_ID),
        ("sign", SIGN),
        ("filename_hash", FILENAME_HASH),
        ("t+shopId", T + SHOP_ID),
        ("shopId+t", SHOP_ID + T),
        ("sign+t", SIGN + T),
        ("t+sign", T + SIGN),
        ("filename_hash+t", FILENAME_HASH + T),
        ("t+filename_hash", T + FILENAME_HASH),
        ("t+shopId+sign", T + SHOP_ID + SIGN),
        ("sign+shopId+t", SIGN + SHOP_ID + T),
    ]
    
    print("尝试 SHA512 匹配前 128 字符:")
    for name, value in params_combinations:
        result = sha512(value)
        if result == hash_part1:
            print(f"  ✓ SHA512({name}) 匹配！")
        elif result[:64] == hash_part1[:64]:
            print(f"  ~ SHA512({name}) 前半部分匹配")
    
    print("\n尝试 MD5 匹配后 32 字符:")
    for name, value in params_combinations:
        result = md5(value)
        if result == hash_part2:
            print(f"  ✓ MD5({name}) 匹配！")


def analyze_encryption():
    """分析可能的加密方式"""
    print_section("可能的加密/签名方式")
    
    print("""
基于分析，这个 API 签名机制可能是：

1. sign (32字符 MD5):
   - 可能是 MD5(某些参数组合)
   - 或者 MD5(某个密钥 + 参数)

2. hash (160字符 = SHA512 + MD5):
   - 前 128 字符: SHA512(某些数据)
   - 后 32 字符: MD5(某些数据)
   
3. 文件名中的 hash:
   - tmp_{MD5}.png 格式
   - 可能是文件内容的 MD5

4. 常见的微信小程序签名方式:
   - 参数按字母排序
   - 拼接成 key=value&key=value 格式
   - 加上 app_secret 或 token
   - 计算 MD5 或 SHA 系列

需要更多请求样本来确定具体的签名算法。
可以尝试修改参数后抓包，对比 sign 和 hash 的变化。
""")


def try_aes_analysis():
    """分析是否可能是 AES 加密"""
    print_section("AES 加密可能性分析")
    
    hash_bytes = bytes.fromhex(HASH)
    print(f"Hash 转换为字节后长度: {len(hash_bytes)} 字节")
    print(f"  = {len(hash_bytes) * 8} bits")
    print(f"  = {len(hash_bytes) // 16} 个 AES 块 (每块 16 字节)")
    
    # 80 字节 = 5 个 AES 块，可能是 AES-CBC 加密结果
    if len(hash_bytes) % 16 == 0:
        print("  ✓ 长度是 16 的倍数，可能是 AES 加密")
    else:
        print("  ✗ 长度不是 16 的倍数")
    
    # 检查是否像随机数据（高熵）
    from collections import Counter
    byte_freq = Counter(hash_bytes)
    print(f"\n字节分布: 共 {len(byte_freq)} 种不同字节值")
    if len(byte_freq) > 60:
        print("  ✓ 字节分布较均匀，可能是加密数据或 hash")


def try_hex_decode():
    """尝试将 hash 解码为可读内容"""
    print_section("尝试解码 Hash")
    
    try:
        hash_bytes = bytes.fromhex(HASH)
        print(f"Hex 解码后的字节 (前 32 字节):")
        print(f"  {hash_bytes[:32].hex()}")
        
        # 尝试 UTF-8 解码
        try:
            decoded = hash_bytes.decode('utf-8')
            print(f"\n可以解码为 UTF-8: {decoded}")
        except:
            print("\n无法解码为 UTF-8 (预期，因为是二进制数据)")
            
    except Exception as e:
        print(f"解码失败: {e}")


def analyze_timestamp_relation():
    """分析时间戳与其他参数的关系"""
    print_section("时间戳分析")
    
    import time
    from datetime import datetime
    
    t_int = int(T)
    t_seconds = t_int / 1000
    
    dt = datetime.fromtimestamp(t_seconds)
    print(f"时间戳: {T}")
    print(f"转换为日期: {dt}")
    
    # 检查是否与 sign 有关
    print(f"\n尝试时间戳相关的 hash:")
    print(f"  MD5(t): {md5(T)}")
    print(f"  MD5(t/1000): {md5(str(t_int // 1000))}")
    
    # 微信小程序常用的签名方式
    print(f"\n微信小程序常见签名格式:")
    wx_sign1 = f"shopId={SHOP_ID}&t={T}"
    wx_sign2 = f"t={T}&shopId={SHOP_ID}"
    print(f"  MD5({wx_sign1}): {md5(wx_sign1)}")
    print(f"  MD5({wx_sign2}): {md5(wx_sign2)}")


def deep_analysis():
    """深度分析"""
    print_section("深度分析 - 寻找模式")
    
    # 分析 hash 的组成
    print("Hash 按 64 字符分割（SHA256 长度）:")
    for i in range(0, len(HASH), 64):
        chunk = HASH[i:i+64]
        print(f"  [{i:3d}-{i+len(chunk):3d}]: {chunk}")
    
    print("\nHash 按 32 字符分割（MD5 长度）:")
    for i in range(0, len(HASH), 32):
        chunk = HASH[i:i+32]
        print(f"  [{i:3d}-{i+len(chunk):3d}]: {chunk}")
    
    # 检查是否有重复模式
    print("\n检查是否有子串重复:")
    for i in range(0, len(HASH) - 32, 32):
        chunk = HASH[i:i+32]
        if chunk == SIGN:
            print(f"  ✓ 位置 {i}-{i+32} 与 sign 相同")
        if chunk == FILENAME_HASH:
            print(f"  ✓ 位置 {i}-{i+32} 与文件名 hash 相同")


if __name__ == "__main__":
    print("=" * 60)
    print(" Hash 与参数关系分析")
    print("=" * 60)
    
    analyze_known_values()
    analyze_filename_hash()
    analyze_sign()
    analyze_hash_structure()
    try_hash_combinations()
    deep_analysis()
    analyze_encryption()


```

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
"""
分析 hash、sign、t、shopId 与图片文件之间的关系
"""

import hashlib
import hmac
import os

# 已知参数
SIGN = "805f30f5c3cb85e1779ae3daacf8dce5"  # 32字符，MD5格式
T = "1764833231217"  # 时间戳
HASH = "fc8aad8184823f1352f85228236f625c679f39c8d5ac4d86359af0572dc9c621c7b976db4c13fd219d28193536058f304031ff6d446277cd40009e4998ee8e1d31cd1c79e4075c5b1cba897d42d81451"
SHOP_ID = "4216"
FILENAME = "tmp_32e6dbcc2678d4d40a297ca5490acc4d.png"

# 从文件名中提取的hash部分
FILENAME_HASH = "32e6dbcc2678d4d40a297ca5490acc4d"  # 32字符，MD5格式

# 使用 test.png 作为测试图片
IMAGE_FILE = "test.png"


def md5(data):
    """计算 MD5"""
    if isinstance(data, str):
        data = data.encode('utf-8')
    return hashlib.md5(data).hexdigest()


def sha1(data):
    """计算 SHA1"""
    if isinstance(data, str):
        data = data.encode('utf-8')
    return hashlib.sha1(data).hexdigest()


def sha256(data):
    """计算 SHA256"""
    if isinstance(data, str):
        data = data.encode('utf-8')
    return hashlib.sha256(data).hexdigest()


def sha512(data):
    """计算 SHA512"""
    if isinstance(data, str):
        data = data.encode('utf-8')
    return hashlib.sha512(data).hexdigest()


def print_section(title):
    print("\n" + "=" * 60)
    print(f" {title}")
    print("=" * 60)


def analyze_known_values():
    """分析已知值的特征"""
    print_section("已知值分析")
    
    print(f"sign: {SIGN}")
    print(f"  长度: {len(SIGN)} 字符 -> MD5 格式")
    
    print(f"\nt: {T}")
    print(f"  长度: {len(T)} 字符")
    print(f"  看起来是毫秒级时间戳")
    
    print(f"\nhash: {HASH}")
    print(f"  长度: {len(HASH)} 字符 = {len(HASH) * 4} bits")
    print(f"  前 128 字符: {HASH[:128]}")
    print(f"  后 32 字符:  {HASH[128:]}")
    
    print(f"\nshopId: {SHOP_ID}")
    
    print(f"\n文件名: {FILENAME}")
    print(f"  文件名中的hash部分: {FILENAME_HASH}")
    print(f"  长度: {len(FILENAME_HASH)} 字符 -> MD5 格式")


def analyze_filename_hash():
    """分析文件名中的 hash"""
    print_section("文件名 Hash 分析")
    
    if not os.path.exists(IMAGE_FILE):
        print(f"警告: {IMAGE_FILE} 不存在，跳过图片内容分析")
        return
    
    with open(IMAGE_FILE, "rb") as f:
        file_content = f.read()
    
    file_md5 = md5(file_content)
    file_sha1 = sha1(file_content)
    file_sha256 = sha256(file_content)
    
    print(f"图片文件 MD5:    {file_md5}")
    print(f"图片文件 SHA1:   {file_sha1}")
    print(f"图片文件 SHA256: {file_sha256}")
    print(f"\n文件名中的 hash: {FILENAME_HASH}")
    
    if file_md5 == FILENAME_HASH:
        print("✓ 文件名中的 hash 是图片文件的 MD5！")
    else:
        print("✗ 文件名中的 hash 不是图片文件的 MD5")


def analyze_sign():
    """分析 sign 参数"""
    print_section("Sign 参数分析")
    
    # 尝试各种组合
    test_cases = [
        ("t", T),
        ("shopId", SHOP_ID),
        ("t+shopId", T + SHOP_ID),
        ("shopId+t", SHOP_ID + T),
        ("t&shopId=value", f"t={T}&shopId={SHOP_ID}"),
        ("shopId=value&t", f"shopId={SHOP_ID}&t={T}"),
        ("filename_hash", FILENAME_HASH),
        ("t+filename_hash", T + FILENAME_HASH),
        ("filename_hash+t", FILENAME_HASH + T),
        ("t+shopId+filename_hash", T + SHOP_ID + FILENAME_HASH),
        ("filename_hash+shopId+t", FILENAME_HASH + SHOP_ID + T),
    ]
    
    print(f"目标 sign: {SIGN}\n")
    print("尝试各种组合的 MD5:")
    
    for name, value in test_cases:
        result = md5(value)
        match = "✓" if result == SIGN else " "
        print(f"  {match} MD5({name}): {result}")


def analyze_hash_structure():
    """分析 hash 参数结构"""
    print_section("Hash 参数结构分析")
    
    hash_part1 = HASH[:128]  # 可能是 SHA512
    hash_part2 = HASH[128:]  # 可能是 MD5
    
    print(f"Hash 前 128 字符（疑似 SHA512）:")
    print(f"  {hash_part1}")
    
    print(f"\nHash 后 32 字符（疑似 MD5）:")
    print(f"  {hash_part2}")
    
    # 检查后32字符是否与某些已知值匹配
    print(f"\n后 32 字符与已知值对比:")
    print(f"  sign:          {SIGN}")
    print(f"  filename_hash: {FILENAME_HASH}")
    print(f"  hash后32字符:  {hash_part2}")
    
    if hash_part2 == SIGN:
        print("  ✓ 后 32 字符等于 sign！")
    if hash_part2 == FILENAME_HASH:
        print("  ✓ 后 32 字符等于文件名中的 hash！")


def try_hash_combinations():
    """尝试各种 hash 组合"""
    print_section("Hash 组合尝试")
    
    hash_part1 = HASH[:128]
    hash_part2 = HASH[128:]
    
    # 加载图片文件
    file_content = b""
    if os.path.exists(IMAGE_FILE):
        with open(IMAGE_FILE, "rb") as f:
            file_content = f.read()
    
    params_combinations = [
        ("t", T),
        ("shopId", SHOP_ID),
        ("sign", SIGN),
        ("filename_hash", FILENAME_HASH),
        ("t+shopId", T + SHOP_ID),
        ("shopId+t", SHOP_ID + T),
        ("sign+t", SIGN + T),
        ("t+sign", T + SIGN),
        ("filename_hash+t", FILENAME_HASH + T),
        ("t+filename_hash", T + FILENAME_HASH),
        ("t+shopId+sign", T + SHOP_ID + SIGN),
        ("sign+shopId+t", SIGN + SHOP_ID + T),
    ]
    
    print("尝试 SHA512 匹配前 128 字符:")
    for name, value in params_combinations:
        result = sha512(value)
        if result == hash_part1:
            print(f"  ✓ SHA512({name}) 匹配！")
        elif result[:64] == hash_part1[:64]:
            print(f"  ~ SHA512({name}) 前半部分匹配")
    
    print("\n尝试 MD5 匹配后 32 字符:")
    for name, value in params_combinations:
        result = md5(value)
        if result == hash_part2:
            print(f"  ✓ MD5({name}) 匹配！")


def analyze_encryption():
    """分析可能的加密方式"""
    print_section("可能的加密/签名方式")
    
    print("""
基于分析，这个 API 签名机制可能是：

1. sign (32字符 MD5):
   - 可能是 MD5(某些参数组合)
   - 或者 MD5(某个密钥 + 参数)

2. hash (160字符 = SHA512 + MD5):
   - 前 128 字符: SHA512(某些数据)
   - 后 32 字符: MD5(某些数据)
   
3. 文件名中的 hash:
   - tmp_{MD5}.png 格式
   - 可能是文件内容的 MD5

4. 常见的微信小程序签名方式:
   - 参数按字母排序
   - 拼接成 key=value&key=value 格式
   - 加上 app_secret 或 token
   - 计算 MD5 或 SHA 系列

需要更多请求样本来确定具体的签名算法。
可以尝试修改参数后抓包，对比 sign 和 hash 的变化。
""")


def try_aes_analysis():
    """分析是否可能是 AES 加密"""
    print_section("AES 加密可能性分析")
    
    hash_bytes = bytes.fromhex(HASH)
    print(f"Hash 转换为字节后长度: {len(hash_bytes)} 字节")
    print(f"  = {len(hash_bytes) * 8} bits")
    print(f"  = {len(hash_bytes) // 16} 个 AES 块 (每块 16 字节)")
    
    # 80 字节 = 5 个 AES 块，可能是 AES-CBC 加密结果
    if len(hash_bytes) % 16 == 0:
        print("  ✓ 长度是 16 的倍数，可能是 AES 加密")
    else:
        print("  ✗ 长度不是 16 的倍数")
    
    # 检查是否像随机数据（高熵）
    from collections import Counter
    byte_freq = Counter(hash_bytes)
    print(f"\n字节分布: 共 {len(byte_freq)} 种不同字节值")
    if len(byte_freq) > 60:
        print("  ✓ 字节分布较均匀，可能是加密数据或 hash")


def try_hex_decode():
    """尝试将 hash 解码为可读内容"""
    print_section("尝试解码 Hash")
    
    try:
        hash_bytes = bytes.fromhex(HASH)
        print(f"Hex 解码后的字节 (前 32 字节):")
        print(f"  {hash_bytes[:32].hex()}")
        
        # 尝试 UTF-8 解码
        try:
            decoded = hash_bytes.decode('utf-8')
            print(f"\n可以解码为 UTF-8: {decoded}")
        except:
            print("\n无法解码为 UTF-8 (预期，因为是二进制数据)")
            
    except Exception as e:
        print(f"解码失败: {e}")


def analyze_timestamp_relation():
    """分析时间戳与其他参数的关系"""
    print_section("时间戳分析")
    
    import time
    from datetime import datetime
    
    t_int = int(T)
    t_seconds = t_int / 1000
    
    dt = datetime.fromtimestamp(t_seconds)
    print(f"时间戳: {T}")
    print(f"转换为日期: {dt}")
    
    # 检查是否与 sign 有关
    print(f"\n尝试时间戳相关的 hash:")
    print(f"  MD5(t): {md5(T)}")
    print(f"  MD5(t/1000): {md5(str(t_int // 1000))}")
    
    # 微信小程序常用的签名方式
    print(f"\n微信小程序常见签名格式:")
    wx_sign1 = f"shopId={SHOP_ID}&t={T}"
    wx_sign2 = f"t={T}&shopId={SHOP_ID}"
    print(f"  MD5({wx_sign1}): {md5(wx_sign1)}")
    print(f"  MD5({wx_sign2}): {md5(wx_sign2)}")


def deep_analysis():
    """深度分析"""
    print_section("深度分析 - 寻找模式")
    
    # 分析 hash 的组成
    print("Hash 按 64 字符分割（SHA256 长度）:")
    for i in range(0, len(HASH), 64):
        chunk = HASH[i:i+64]
        print(f"  [{i:3d}-{i+len(chunk):3d}]: {chunk}")
    
    print("\nHash 按 32 字符分割（MD5 长度）:")
    for i in range(0, len(HASH), 32):
        chunk = HASH[i:i+32]
        print(f"  [{i:3d}-{i+len(chunk):3d}]: {chunk}")
    
    # 检查是否有重复模式
    print("\n检查是否有子串重复:")
    for i in range(0, len(HASH) - 32, 32):
        chunk = HASH[i:i+32]
        if chunk == SIGN:
            print(f"  ✓ 位置 {i}-{i+32} 与 sign 相同")
        if chunk == FILENAME_HASH:
            print(f"  ✓ 位置 {i}-{i+32} 与文件名 hash 相同")


if __name__ == "__main__":
    print("=" * 60)
    print(" Hash 与参数关系分析")
    print("=" * 60)
    
    analyze_known_values()
    analyze_filename_hash()
    analyze_sign()
    analyze_hash_structure()
    try_hash_combinations()
    deep_analysis()
    analyze_timestamp_relation()
    try_aes_analysis()
    try_hex_decode()
    analyze_encryption()


```

分析完成！以下是关键发现：

## 分析结论

### 1. **sign（32字符）**
- 格式为 MD5
- 无法通过简单的参数组合复现
- 很可能使用了 **服务器密钥/app_secret** 参与计算

### 2. **hash（160字符 = 80字节）**
- **可能是 AES 加密**，而非纯哈希拼接
- 长度正好是 5 个 AES 块（每块 16 字节）
- 字节分布均匀，符合加密数据特征
- 可能是对某些敏感数据（如用户信息、文件信息）的加密结果

### 3. **文件名中的 hash**
- `tmp_32e6dbcc2678d4d40a297ca5490acc4d.png` 中的 hash 是原始图片的 MD5
- `test.png` 的 MD5 是 `5883ca5003f5e1315db79be5dc200e1f`，与之不匹配
- 说明 `test.png` 不是原始上传的那张图片

### 4. **时间戳 t**
- 毫秒级时间戳 `1764833231217`
- 对应时间：`2025-12-04 15:27:11.217`

## 下一步建议

要彻底破解签名算法，建议：
1. **反编译喜茶小程序**，查找签名生成代码
2. **多次抓包对比**，观察 sign 和 hash 随参数变化的规律
3. 如果 hash 确实是 AES 加密，需要找到加密密钥

需要我帮你反编译小程序或做进一步分析吗？

---

**User**

我已反编译相关代码，现在要如何找到签名相关片段呢

---

**Cursor**

我来帮你在反编译的代码中搜索签名相关的代码片段：

找到关键文件了！让我深入搜索 `common.js` 中的签名逻辑：

找到了关键线索！发现了 `calDIYSign` 函数。让我深入查看：

非常重要的发现！签名是通过**微信小程序插件**计算的。让我找到更多细节：

让我搜索插件配置和杯贴上传的具体实现：

文件是空的，逻辑在 `common.js` 中。让我搜索上传杯贴的具体代码：

让我找到调用签名和上传的完整流程：

让我搜索插件初始化和实际上传调用：

让我搜索实际发送 DIY 请求的代码：

我已经找到关键信息了！让我为你总结并创建一个分析脚本：

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
"""
喜茶小程序 DIY 杯贴签名机制分析

基于反编译代码的发现：
======================

1. 签名计算位置
   - DIY 杯贴的签名 (sign, hash) 是通过原生插件计算的
   - 插件名称: CrashReportAndIosPush (或类似)
   - 调用方法: pluginInstance.calDIYSign({ fileHash: e })
   - 输入参数: fileHash (图片文件的 MD5 hash)
   - 返回数据: { errorCode, message, data }
   
2. 相关代码片段 (common.js 第 5368-5400 行):
   ```javascript
   key: "calculateDiySign",
   value: function(e) {
       if (!this._checkPluginInitialized()) return null;
       var t = this.pluginInstance;
       if (!t || "function" != typeof t.calDIYSign) return null;
       try {
           var n = t.calDIYSign({
               fileHash: e  // <-- 输入是文件的 MD5 hash
           }),
           // ... 解析返回结果
       }
   }
   ```

3. 订单签名对比 (common.js 第 16099-16100 行):
   ```javascript
   o = j.HmacMD5("submitOrder", "").toString();
   r = `?app=${t}&n=${r}&t=${a}&biz=${o}&sign=` + j.HmacMD5(t + r + a + o + e, t + e).toString();
   ```
   
   订单签名使用 HmacMD5，但 DIY 杯贴签名使用原生插件。

4. 结论:
   - sign 和 hash 参数是由原生插件计算的，算法不在 JavaScript 代码中
   - 需要分析原生插件才能得到完整的签名算法
   - 可能需要使用 Frida/Xposed 等工具 Hook 原生方法来获取签名
"""

import hashlib
import hmac
import time
import os

# 已知参数
SIGN = "805f30f5c3cb85e1779ae3daacf8dce5"
T = "1764833231217"
HASH_PARAM = "fc8aad8184823f1352f85228236f625c679f39c8d5ac4d86359af0572dc9c621c7b976db4c13fd219d28193536058f304031ff6d446277cd40009e4998ee8e1d31cd1c79e4075c5b1cba897d42d81451"
SHOP_ID = "4216"
FILENAME_HASH = "32e6dbcc2678d4d40a297ca5490acc4d"  # 原始文件 MD5


def md5(data):
    if isinstance(data, str):
        data = data.encode('utf-8')
    return hashlib.md5(data).hexdigest()


def sha512(data):
    if isinstance(data, str):
        data = data.encode('utf-8')
    return hashlib.sha512(data).hexdigest()


def hmac_md5(key, msg):
    if isinstance(key, str):
        key = key.encode('utf-8')
    if isinstance(msg, str):
        msg = msg.encode('utf-8')
    return hmac.new(key, msg, hashlib.md5).hexdigest()


def print_section(title):
    print("\n" + "=" * 70)
    print(f" {title}")
    print("=" * 70)


def analyze_plugin_signature():
    """分析插件签名机制"""
    print_section("DIY 杯贴签名机制分析")
    
    print("""
签名流程（根据反编译代码推断）:
---------------------------------

1. 用户在画布上创作完成后，调用 wx.canvasToTempFilePath 生成临时图片

2. 计算图片文件的 MD5 (fileHash):
   - 示例: 32e6dbcc2678d4d40a297ca5490acc4d
   - 这也是文件名 tmp_{hash}.png 中的 hash

3. 调用原生插件计算签名:
   - pluginInstance.calDIYSign({ fileHash: "32e6dbcc..." })
   - 返回: { errorCode: 0, message: "SUCCESS", data: "签名数据" }

4. 构造请求参数:
   - sign: 由插件返回（可能是签名数据的一部分）
   - t: 当前时间戳（毫秒）
   - hash: 由插件返回（可能是加密后的数据）
   - shopId: 当前门店 ID

5. 上传图片到服务器:
   - URL: /api/service-cps/user/diy?sign=...&t=...&hash=...&shopId=...
   - Body: multipart/form-data (file, width, height)
""")


def analyze_known_values():
    """分析已知值"""
    print_section("已知参数分析")
    
    print(f"sign (32字符 = MD5 格式):")
    print(f"  {SIGN}")
    
    print(f"\nt (时间戳):")
    print(f"  {T}")
    
    print(f"\nhash (160字符 = 80字节):")
    print(f"  前64: {HASH_PARAM[:64]}")
    print(f"  中64: {HASH_PARAM[64:128]}")
    print(f"  后32: {HASH_PARAM[128:]}")
    
    print(f"\nshopId:")
    print(f"  {SHOP_ID}")
    
    print(f"\n文件名中的 hash (原图 MD5):")
    print(f"  {FILENAME_HASH}")


def try_common_patterns():
    """尝试常见签名模式"""
    print_section("尝试常见签名模式")
    
    # 常见的小程序签名模式
    patterns = [
        # HMAC-MD5 模式
        ("HMAC-MD5(fileHash, '')", hmac_md5("", FILENAME_HASH)),
        ("HMAC-MD5('', fileHash)", hmac_md5(FILENAME_HASH, "")),
        ("HMAC-MD5(t, fileHash)", hmac_md5(T, FILENAME_HASH)),
        ("HMAC-MD5(fileHash, t)", hmac_md5(FILENAME_HASH, T)),
        ("HMAC-MD5(shopId, fileHash)", hmac_md5(SHOP_ID, FILENAME_HASH)),
        ("HMAC-MD5(fileHash, shopId)", hmac_md5(FILENAME_HASH, SHOP_ID)),
        
        # MD5 组合模式
        ("MD5(fileHash + t)", md5(FILENAME_HASH + T)),
        ("MD5(t + fileHash)", md5(T + FILENAME_HASH)),
        ("MD5(fileHash + shopId + t)", md5(FILENAME_HASH + SHOP_ID + T)),
        ("MD5(t + shopId + fileHash)", md5(T + SHOP_ID + FILENAME_HASH)),
    ]
    
    print(f"目标 sign: {SIGN}\n")
    
    for name, result in patterns:
        match = "✓ 匹配!" if result == SIGN else ""
        print(f"  {name}:")
        print(f"    {result} {match}")


def suggest_next_steps():
    """建议下一步操作"""
    print_section("建议的下一步操作")
    
    print("""
由于签名算法在原生插件中，建议以下方法来获取签名:

方法 1: Hook 原生插件
----------------------
使用 Frida 或 Xposed Hook 插件的 calDIYSign 方法:

```javascript
// Frida 脚本示例
Java.perform(function() {
    var pluginClass = Java.use("com.heytea.xxx.DIYSignPlugin");
    pluginClass.calDIYSign.implementation = function(params) {
        console.log("Input fileHash: " + params.fileHash);
        var result = this.calDIYSign(params);
        console.log("Output: " + result);
        return result;
    };
});
```

方法 2: 抓取多个请求样本
-----------------------
1. 使用不同图片多次抓包
2. 记录每次的 fileHash、sign、hash、t
3. 分析参数之间的数学关系

方法 3: 逆向原生插件
--------------------
1. 找到插件的 AppID: 在 app.json 的 plugins 配置中
2. 下载并反编译插件
3. 分析签名算法

方法 4: 服务器端重放 (当前方案)
------------------------------
如果签名在有效期内，可以直接重放已抓取的请求:
- 注意: sign/hash/t 可能有时效性
- 建议: 抓取后立即重放测试
""")


def generate_new_request_template():
    """生成新请求模板"""
    print_section("请求模板 (需要获取新的签名)")
    
    current_t = str(int(time.time() * 1000))
    
    print(f"""
# 以下是发送 DIY 杯贴请求的模板
# 需要替换 sign 和 hash 为从插件获取的值

import requests

url = "https://go.heytea.com/api/service-cps/user/diy"

# 这些参数需要从插件获取
sign = "从插件获取"
hash_param = "从插件获取"
t = "{current_t}"  # 当前时间戳
shop_id = "4216"

params = {{
    "sign": sign,
    "t": t,
    "hash": hash_param,
    "shopId": shop_id
}}

# 请求头 (Authorization 需要有效的 token)
headers = {{
    "Authorization": "Bearer YOUR_TOKEN",
    "X-client": "weapp",
    "X-client-version": "3.318.0.p8",
    "User-Agent": "...",
}}

# 上传文件
files = {{
    "file": ("image.png", open("your_image.png", "rb"), "image/png")
}}

data = {{
    "width": "596",
    "height": "832"
}}

response = requests.post(url, params=params, headers=headers, files=files, data=data)
print(response.json())
""")


if __name__ == "__main__":
    print("=" * 70)
    print(" 喜茶 DIY 杯贴签名机制分析")
    print("=" * 70)
    
    analyze_plugin_signature()
    analyze_known_values()
    try_common_patterns()
    suggest_next_steps()
    generate_new_request_template()

```

## 分析总结

通过反编译代码分析，我发现了以下关键信息：

### 签名机制
DIY 杯贴的 `sign` 和 `hash` 参数是通过**原生插件**计算的，而不是在 JavaScript 中：

```javascript:5368:5400:wx696a42df4f2456d3/common.js
key: "calculateDiySign",
value: function(e) {
    var t = this.pluginInstance;
    var n = t.calDIYSign({
        fileHash: e  // 输入是文件的 MD5 hash
    });
    // 返回: { errorCode, message, data }
}
```

### 签名流程
1. 用户创作完成 → 生成临时图片
2. 计算图片 MD5 → `fileHash` (即文件名中的 `32e6dbcc...`)
3. 调用原生插件 `calDIYSign({ fileHash })` 
4. 插件返回 `sign` 和 `hash` 参数
5. 构造请求上传图片

### 为什么无法通过简单组合复现签名
- 签名算法在**原生插件**中实现，不在 JavaScript 代码里
- 常见的 MD5/HMAC-MD5 组合都无法匹配
- `hash` 参数可能是 **AES 加密**数据（80字节 = 5个 AES 块）

### 建议的下一步
1. **抓取多个样本** - 用不同图片多次抓包，分析规律
2. **Hook 原生插件** - 使用 Frida 拦截 `calDIYSign` 调用
3. **重放测试** - 直接使用已抓取的签名测试时效性

需要我帮你进行下一步操作吗？比如搜索插件的 AppID 或准备 Frida Hook 脚本？

```plaintext
key: "calculateDiySign",
value: function(e) {
    var t = this.pluginInstance;
    var n = t.calDIYSign({
        fileHash: e  // 输入是文件的 MD5 hash
    });
    // 返回: { errorCode, message, data }
}
```

---

**User**

“原生插件“是什么？请尝试这份插件的更多信息。

---

**Cursor**

让我搜索这个插件的更多信息：

发现了关键插件 ID！让我深入搜索：

让我搜索更多关于这个插件的信息：

现在我来为你完整解释：

## 什么是"原生插件"

**微信小程序原生插件**是一种特殊的功能模块，与普通 JavaScript 插件不同，它包含了**原生代码**（iOS 的 Objective-C/Swift 或 Android 的 Java/Kotlin），可以：
- 访问设备底层功能（如设备ID、推送服务）
- 执行性能敏感的计算（如加密算法）
- 调用系统 API

**关键区别：**
- **普通插件**: 纯 JavaScript 代码，可以在反编译后看到全部逻辑
- **原生插件**: 核心代码在原生层，JavaScript 只是接口包装，**无法通过反编译小程序获取算法**

---

## 喜茶使用的关键插件

根据反编译代码，喜茶使用了插件 **`wx1a13d6849c0100f0`**，这个插件提供：

| 功能 | 方法 | 说明 |
|------|------|------|
| DIY签名 | `calDIYSign({ fileHash })` | **计算 sign 和 hash 参数** |
| 崩溃报告 | `initBugly()`, `setBuglyUser()` | Bugly 错误监控 |
| 推送服务 | `registerRemoteNotifications()` | iOS 推送 |
| 设备ID | `getAndroidId()`, `getIdentifierForVendor()` | 获取设备标识 |
| SD卡权限 | `checkSDCardPermission()` | Android 存储权限 |

---

## 签名计算流程

```
JavaScript 层                    原生层 (无法看到)
     │                                │
     │  calDIYSign({ fileHash })      │
     │ ──────────────────────────────>│
     │                                │ 1. 接收文件 MD5
     │                                │ 2. 获取时间戳
     │                                │ 3. 使用内置密钥加密
     │                                │ 4. 生成 sign + hash
     │   { sign, hash, t }            │
     │ <──────────────────────────────│
     │                                │
```

---

## 结论

**签名算法 (`sign`, `hash`) 在原生插件中实现，无法通过反编译 JavaScript 获取。**

### 可行的破解方法：

1. **Frida Hook** - 在运行时拦截 `calDIYSign` 的输入输出
2. **逆向原生插件** - 需要获取插件的 `.so` (Android) 或 Framework (iOS) 文件
3. **多样本分析** - 收集多组 `fileHash → sign, hash` 映射，尝试逆推算法
4. **直接重放** - 如果签名没有时效性，可直接使用已抓取的签名

需要我帮你准备 Frida Hook 脚本吗？

```plaintext
JavaScript 层                    原生层 (无法看到)
     │                                │
     │  calDIYSign({ fileHash })      │
     │ ──────────────────────────────>│
     │                                │ 1. 接收文件 MD5
     │                                │ 2. 获取时间戳
     │                                │ 3. 使用内置密钥加密
     │                                │ 4. 生成 sign + hash
     │   { sign, hash, t }            │
     │ <──────────────────────────────│
     │                                │
```

---

**User**

我不熟悉安卓，请为我解释Frida Hook。

---

**Cursor**

## Frida Hook 简介

### 什么是 Frida？

**Frida** 是一个动态代码插桩工具，可以让你在程序运行时"拦截"和"修改"函数调用。

用一个比喻来说明：

```
想象你在监听一个电话通话：

正常情况:
  小程序 ──调用──> calDIYSign(fileHash) ──返回──> { sign, hash }
  
使用 Frida:
  小程序 ──调用──> [Frida 拦截] ──> calDIYSign(fileHash) ──返回──> [Frida 记录] ──> { sign, hash }
                      │                                              │
                      │ 打印: 输入参数是 "32e6dbcc..."                │ 打印: 返回值是 { sign: "805f...", hash: "fc8a..." }
                      v                                              v
```

### 它能做什么？

1. **查看参数** - 看到函数被调用时传入了什么
2. **查看返回值** - 看到函数返回了什么
3. **修改行为** - 改变函数的输入或输出
4. **追踪调用** - 知道谁调用了这个函数

---

## 对于喜茶签名的应用

使用 Frida，我们可以：

```
┌─────────────────────────────────────────────────────────┐
│                    微信 App 运行中                        │
│                                                         │
│  喜茶小程序                                              │
│     │                                                   │
│     │ 调用 calDIYSign({ fileHash: "32e6dbcc..." })      │
│     │                                                   │
│     v                                                   │
│  ┌──────────────────────────────────────┐              │
│  │  原生插件 wx1a13d6849c0100f0          │              │
│  │                                      │              │
│  │  [Frida 在这里拦截]                   │◄─── 我们插入的代码
│  │     ↓                                │              │
│  │  输入: fileHash = "32e6dbcc..."       │              │
│  │  输出: sign = "805f30f5..."          │              │
│  │        hash = "fc8aad81..."          │              │
│  │        t = "1764833231217"           │              │
│  │                                      │              │
│  └──────────────────────────────────────┘              │
└─────────────────────────────────────────────────────────┘
```

---

## 使用 Frida 的前提条件

| 条件 | 说明 |
|------|------|
| **Root 手机** 或 **模拟器** | Frida 需要高权限才能注入进程 |
| **安装 Frida Server** | 在手机上运行的服务端 |
| **安装 Frida 客户端** | 在电脑上用 Python 控制 |
| **目标 App** | 微信 + 喜茶小程序 |

---

## 简化的操作流程

```bash
# 1. 电脑上安装 Frida
pip install frida frida-tools

# 2. 手机上运行 Frida Server (需要 root)
adb push frida-server /data/local/tmp/
adb shell "chmod 755 /data/local/tmp/frida-server"
adb shell "/data/local/tmp/frida-server &"

# 3. 运行 Hook 脚本
frida -U -f com.tencent.mm -l hook_heytea.js
```

---

## 示例 Hook 脚本

```javascript
// hook_heytea.js - 拦截 calDIYSign 函数

Java.perform(function() {
    console.log("[*] Frida 已注入微信进程");
    
    // 找到并 Hook 目标函数
    // 注意：具体类名需要根据实际逆向分析确定
    var targetClass = Java.use("com.heytea.plugin.DIYSign");
    
    targetClass.calDIYSign.implementation = function(params) {
        // 打印输入参数
        console.log("[*] calDIYSign 被调用!");
        console.log("[*] 输入 fileHash: " + params.fileHash);
        
        // 调用原函数
        var result = this.calDIYSign(params);
        
        // 打印返回结果
        console.log("[*] 返回结果: " + JSON.stringify(result));
        
        return result;
    };
});
```

---

## 更简单的替代方案

如果你不想折腾 Frida，还有几个更简单的方法：

### 方法 1: 多次抓包对比
```
第1次: fileHash=aaa → sign=xxx, hash=yyy, t=111
第2次: fileHash=bbb → sign=xxx2, hash=yyy2, t=222
第3次: fileHash=ccc → sign=xxx3, hash=yyy3, t=333
...
分析规律
```

### 方法 2: 直接重放已抓取的请求
如果 `sign` 和 `hash` 没有时效性，可以直接用你已经抓到的参数重复请求。

### 方法 3: 使用 HTTP 调试工具
用 Charles/mitmproxy 持续抓包，每次上传图片时记录参数。

---

## 建议

鉴于你不熟悉安卓开发，我建议：

1. **先测试重放** - 用已抓取的签名直接发请求，看是否有时效性
2. **如果有时效性** - 考虑多次抓包分析规律
3. **如果需要 Frida** - 我可以帮你写完整的脚本，但需要你准备好 Root 手机或模拟器

要我先帮你测试一下已有签名的重放请求吗？

```plaintext
想象你在监听一个电话通话：

正常情况:
  小程序 ──调用──> calDIYSign(fileHash) ──返回──> { sign, hash }
  
使用 Frida:
  小程序 ──调用──> [Frida 拦截] ──> calDIYSign(fileHash) ──返回──> [Frida 记录] ──> { sign, hash }
                      │                                              │
                      │ 打印: 输入参数是 "32e6dbcc..."                │ 打印: 返回值是 { sign: "805f...", hash: "fc8a..." }
                      v                                              v
```

```plaintext
┌─────────────────────────────────────────────────────────┐
│                    微信 App 运行中                        │
│                                                         │
│  喜茶小程序                                              │
│     │                                                   │
│     │ 调用 calDIYSign({ fileHash: "32e6dbcc..." })      │
│     │                                                   │
│     v                                                   │
│  ┌──────────────────────────────────────┐              │
│  │  原生插件 wx1a13d6849c0100f0          │              │
│  │                                      │              │
│  │  [Frida 在这里拦截]                   │◄─── 我们插入的代码
│  │     ↓                                │              │
│  │  输入: fileHash = "32e6dbcc..."       │              │
│  │  输出: sign = "805f30f5..."          │              │
│  │        hash = "fc8aad81..."          │              │
│  │        t = "1764833231217"           │              │
│  │                                      │              │
│  └──────────────────────────────────────┘              │
└─────────────────────────────────────────────────────────┘
```

```shellscript
# 1. 电脑上安装 Frida
pip install frida frida-tools

# 2. 手机上运行 Frida Server (需要 root)
adb push frida-server /data/local/tmp/
adb shell "chmod 755 /data/local/tmp/frida-server"
adb shell "/data/local/tmp/frida-server &"

# 3. 运行 Hook 脚本
frida -U -f com.tencent.mm -l hook_heytea.js
```

```javascript
// hook_heytea.js - 拦截 calDIYSign 函数

Java.perform(function() {
    console.log("[*] Frida 已注入微信进程");
    
    // 找到并 Hook 目标函数
    // 注意：具体类名需要根据实际逆向分析确定
    var targetClass = Java.use("com.heytea.plugin.DIYSign");
    
    targetClass.calDIYSign.implementation = function(params) {
        // 打印输入参数
        console.log("[*] calDIYSign 被调用!");
        console.log("[*] 输入 fileHash: " + params.fileHash);
        
        // 调用原函数
        var result = this.calDIYSign(params);
        
        // 打印返回结果
        console.log("[*] 返回结果: " + JSON.stringify(result));
        
        return result;
    };
});
```

```plaintext
第1次: fileHash=aaa → sign=xxx, hash=yyy, t=111
第2次: fileHash=bbb → sign=xxx2, hash=yyy2, t=222
第3次: fileHash=ccc → sign=xxx3, hash=yyy3, t=333
...
分析规律
```

---

**User**

我为你准备了一台IDA，请测试MCP实现是否正常。

---

**Cursor**

好的，让我测试 IDA MCP 连接：

IDA MCP 连接正常！当前加载的是 **`libcrypto.so`** 文件。让我获取更多信息：

我来测试一下与 IDA Pro 的 MCP 连接是否正常。让我获取一些基本信息：

---

**User**

请使用IDA，结合我从喜茶小程序APP版apk中拆出来的相关代码（两个.so文件），再次尝试分析hash参数的生成逻辑。

---

**Cursor**

好的，让我先查看一下工作目录中有哪些 `.so` 文件，然后使用 IDA 进行分析。

找到了三个 `.so` 文件：
- `libcanvas.so` - 可能与画布/图片处理相关
- `libheyteago.so` - 喜茶自有的核心库，很可能包含签名逻辑
- `libcrypto.so` - 通用加密库

但是 IDA Pro 的 MCP 服务器还没有启动。请在 IDA Pro 中执行以下操作来启动 MCP 服务器：

1. 打开 IDA Pro
2. 加载 `libheyteago.so` 文件（这个最可能包含签名逻辑）
3. 点击菜单 **Edit → Plugins → MCP** (或按 `Ctrl+Alt+M`)

启动后请告诉我，我将继续分析。

同时，`libheyteago.so` 这个名字很有意思 - "heyteago" 暗示这是喜茶专用的 Go 语言编译的库，很可能包含 `calDIYSign` 的核心实现。

---

**User**

抱歉。现在请再尝试一下。

---

**Cursor**

IDA 连接成功！当前加载的是 `libcrypto.so`，这是一个通用的 OpenSSL 加密库。

不过，签名逻辑更可能在 `libheyteago.so`（喜茶自有库）中。让我先快速查看一下当前库的关键函数，然后建议分析 `libheyteago.so`：

正如预期，`libcrypto.so` 是标准的 OpenSSL 库，没有喜茶特定的签名逻辑。

**请在 IDA Pro 中加载 `libheyteago.so` 文件**，这个文件更可能包含 `calDIYSign` 的核心实现。加载完成后请告诉我。

与此同时，让我查看一下这个库有哪些常用的加密函数可能被调用：

`libcrypto.so` 提供了完整的加密能力：
- **MD5**: `MD5`, `MD5_Init`, `MD5_Update`, `MD5_Final`
- **SHA**: `EVP_sha1`, `EVP_sha256`, `EVP_sha512`
- **AES**: `AES_encrypt`, `AES_cbc_encrypt`, `AES_set_encrypt_key`
- **HMAC**: `HMAC_Init_ex`, `HMAC_Init`

这些是底层加密原语，`libheyteago.so` 会调用它们来实现签名逻辑。

---

**下一步：请在 IDA Pro 中加载 `libheyteago.so`**

这个库名 "heyteago" 表明它是喜茶用 Go 语言编写的原生库，很可能包含：
- `calDIYSign` 函数的实际实现
- 签名算法的具体逻辑（如何组合 `fileHash`、`t`、`shopId` 等参数）
- 可能的加密密钥

加载完成后告诉我，我将立即搜索签名相关函数。

---

**User**

现在应该成功加载libheyteago.so了。

---

**Cursor**

成功连接！现在让我搜索更多关键词来找到签名相关函数：

**找到了关键线索！** 字符串 `calDIYSign` 位于地址 `0x439055`！

让我找到引用这个字符串的代码：

**重大发现！**

1. **`HEYTEA6H7J8K9M0N`** - 这看起来像是一个**硬编码的加密密钥**！
2. **`com/donut/wx1a13d6849c0100f0/jni/HeyteagoJNI`** - JNI 接口类路径

让我深入分析使用这个密钥的函数：

**重大发现！** 找到了签名函数的核心逻辑！

关键信息：
1. **硬编码密钥**: `"HEYTEA6H7J8K9M0N"` (16字节 = AES-128密钥长度！)
2. **时间戳**: `std::chrono::system_clock::now() / 1000` 获取毫秒级时间戳
3. **字符串拼接**: 使用 `#` 作为分隔符

让我深入分析加密函数 `sub_15B808`：

```c
sub_15CDF0(ptr, v54, &v51, &v48);
```

**发现更多密钥！**

1. **`HEYTEA6H7J8K9M0N`** - 主加密密钥 (16字节 = AES-128)
2. **`AN1wedUvOJblfD8Z`** - 备用密钥或IV (16字节)
3. **`2lJuL8jdH03oNIq5`** - 另一个备用密钥 (16字节)

让我继续分析核心加密函数：

现在我已经完全理解了签名算法的核心逻辑！让我总结分析结果并创建一个可用的签名生成脚本：

## 🔑 **签名算法分析结果**

### 发现的密钥
1. **主密钥**: `HEYTEA6H7J8K9M0N` (16字节)
2. **IV选项1**: `AN1wedUvOJblfD8Z` (16字节)  
3. **IV选项2**: `2lJuL8jdH03oNIq5` (16字节)

### 算法流程
从反编译代码分析，`calDIYSign` 函数的签名流程是：

1. 获取当前时间戳（毫秒）
2. 将输入数据（`fileHash`）与时间戳用 `#` 拼接
3. 使用 **AES-128-CBC** 加密
4. 将加密结果转为十六进制字符串

```python
"""
喜茶DIY贴纸签名算法实现
基于libheyteago.so逆向分析

密钥信息（来自IDA逆向）:
- AES Key: HEYTEA6H7J8K9M0N
- IV Option 1: AN1wedUvOJblfD8Z
- IV Option 2: 2lJuL8jdH03oNIq5
"""

import hashlib
import time
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad

# 硬编码密钥（从libheyteago.so提取）
AES_KEY = b"HEYTEA6H7J8K9M0N"  # 16 bytes = AES-128
IV_OPTION_1 = b"AN1wedUvOJblfD8Z"  # 16 bytes
IV_OPTION_2 = b"2lJuL8jdH03oNIq5"  # 16 bytes


def calculate_file_hash(file_path: str) -> str:
    """计算文件的MD5哈希"""
    with open(file_path, "rb") as f:
        return hashlib.md5(f.read()).hexdigest()


def aes_cbc_encrypt(plaintext: bytes, key: bytes, iv: bytes) -> bytes:
    """AES-CBC加密"""
    cipher = AES.new(key, AES.MODE_CBC, iv)
    padded_data = pad(plaintext, AES.block_size)
    return cipher.encrypt(padded_data)


def calculate_diy_sign(file_hash: str, timestamp: int = None) -> dict:
    """
    计算DIY贴纸签名
    
    参数:
        file_hash: 文件MD5哈希值（32字符十六进制）
        timestamp: 时间戳（毫秒），默认为当前时间
    
    返回:
        包含 sign, hash, t 的字典
    """
    if timestamp is None:
        timestamp = int(time.time() * 1000)
    
    t_str = str(timestamp)
    
    # 方案1: fileHash#timestamp
    plaintext_1 = f"{file_hash}#{t_str}".encode('utf-8')
    
    # 方案2: 仅 fileHash
    plaintext_2 = file_hash.encode('utf-8')
    
    # 使用两个不同的IV尝试加密
    results = {}
    
    # 尝试不同的组合
    for iv_name, iv in [("IV1", IV_OPTION_1), ("IV2", IV_OPTION_2)]:
        for pt_name, plaintext in [("hash#t", plaintext_1), ("hash", plaintext_2)]:
            try:
                encrypted = aes_cbc_encrypt(plaintext, AES_KEY, iv)
                encrypted_hex = encrypted.hex()
                results[f"{iv_name}_{pt_name}"] = encrypted_hex
            except Exception as e:
                results[f"{iv_name}_{pt_name}"] = f"Error: {e}"
    
    # 计算可能的sign（MD5）
    sign_candidates = {
        "md5(fileHash)": hashlib.md5(file_hash.encode()).hexdigest(),
        "md5(t)": hashlib.md5(t_str.encode()).hexdigest(),
        "md5(fileHash+t)": hashlib.md5(f"{file_hash}{t_str}".encode()).hexdigest(),
        "md5(t+fileHash)": hashlib.md5(f"{t_str}{file_hash}".encode()).hexdigest(),
        "md5(fileHash#t)": hashlib.md5(f"{file_hash}#{t_str}".encode()).hexdigest(),
    }
    
    return {
        "t": timestamp,
        "file_hash": file_hash,
        "hash_candidates": results,
        "sign_candidates": sign_candidates,
    }


def generate_request_params(file_path: str, shop_id: str = "4216") -> dict:
    """
    生成请求参数
    
    这个函数尝试重现calDIYSign的行为
    """
    file_hash = calculate_file_hash(file_path)
    timestamp = int(time.time() * 1000)
    
    # 基于逆向分析，最可能的组合
    plaintext = f"{file_hash}#{timestamp}".encode('utf-8')
    
    # 使用主密钥和IV
    encrypted = aes_cbc_encrypt(plaintext, AES_KEY, IV_OPTION_1)
    hash_param = encrypted.hex()
    
    # sign可能是某种MD5
    sign_param = hashlib.md5(f"{file_hash}{timestamp}".encode()).hexdigest()
    
    return {
        "sign": sign_param,
        "t": str(timestamp),
        "hash": hash_param,
        "shopId": shop_id,
        "fileHash": file_hash,
    }


if __name__ == "__main__":
    # 测试用例 - 使用HAR包中的数据验证
    print("=" * 60)
    print("喜茶DIY签名算法分析")
    print("=" * 60)
    
    # HAR包中的原始数据
    original_data = {
        "sign": "805f30f5c3cb85e1779ae3daacf8dce5",
        "t": "1764833231217",
        "hash": "fc8aad8184823f1352f85228236f625c679f39c8d5ac4d86359af0572dc9c621c7b976db4c13fd219d28193536058f304031ff6d446277cd40009e4998ee8e1d31cd1c79e4075c5b1cba897d42d81451",
        "shopId": "4216",
        "filename_hash": "32e6dbcc2678d4d40a297ca5490acc4d",  # from tmp_xxx.png
    }
    
    print("\n原始HAR数据:")
    print(f"  sign: {original_data['sign']} (长度: {len(original_data['sign'])})")
    print(f"  t: {original_data['t']}")
    print(f"  hash: {original_data['hash'][:50]}... (长度: {len(original_data['hash'])})")
    print(f"  hash bytes: {len(original_data['hash']) // 2} bytes")
    
    print("\n密钥信息 (从libheyteago.so提取):")
    print(f"  AES Key: {AES_KEY.decode()}")
    print(f"  IV #1: {IV_OPTION_1.decode()}")
    print(f"  IV #2: {IV_OPTION_2.decode()}")
    
    # 使用原始数据测试
    print("\n测试签名计算:")
    result = calculate_diy_sign(
        original_data["filename_hash"], 
        int(original_data["t"])
    )
    
    print(f"\n输入:")
    print(f"  fileHash: {result['file_hash']}")
    print(f"  timestamp: {result['t']}")
    
    print(f"\n计算的hash候选值:")
    for name, value in result["hash_candidates"].items():
        match = "✓ MATCH!" if value == original_data["hash"] else ""
        print(f"  {name}: {value[:50]}... {match}")
    
    print(f"\n计算的sign候选值:")
    for name, value in result["sign_candidates"].items():
        match = "✓ MATCH!" if value == original_data["sign"] else ""
        print(f"  {name}: {value} {match}")
    
    # 尝试解密原始hash
    print("\n" + "=" * 60)
    print("尝试解密原始hash参数:")
    print("=" * 60)
    
    try:
        from Crypto.Cipher import AES
        from Crypto.Util.Padding import unpad
        
        original_hash_bytes = bytes.fromhex(original_data["hash"])
        print(f"原始hash长度: {len(original_hash_bytes)} bytes")
        
        for iv_name, iv in [("IV1", IV_OPTION_1), ("IV2", IV_OPTION_2)]:
            try:
                cipher = AES.new(AES_KEY, AES.MODE_CBC, iv)
                decrypted = cipher.decrypt(original_hash_bytes)
                # 尝试unpad
                try:
                    unpadded = unpad(decrypted, AES.block_size)
                    print(f"\n使用 {iv_name} 解密成功:")
                    print(f"  结果: {unpadded}")
                    print(f"  UTF-8: {unpadded.decode('utf-8', errors='replace')}")
                except:
                    print(f"\n使用 {iv_name} 解密 (无法unpad):")
                    print(f"  原始: {decrypted}")
                    print(f"  UTF-8: {decrypted.decode('utf-8', errors='replace')}")
            except Exception as e:
                print(f"\n使用 {iv_name} 解密失败: {e}")
                
    except ImportError:
        print("需要安装 pycryptodome: pip install pycryptodome")
    
    print("\n" + "=" * 60)
    print("如果要使用真实文件测试，请运行:")
    print("  python heytea_sign.py <image_path>")
    print("=" * 60)

```

看起来我们找到的密钥可能不完全正确，或者加密流程有额外的步骤。让我在 IDA 中进一步分析，查找更多细节：

让我进一步分析引用这个备用密钥的函数，看看它们是如何使用这些密钥的：

```c
v7 = std::chrono::system_clock::now(v6) / 1000LL;  // 毫秒级时间戳
std::to_string(&v43, v7);  // 转为字符串
```

```c
sub_15B6FC(&v38, a1, "#");  // 将 fileHash 和 "#" 拼接
sub_15E948((int)&v38, v8, v9);  // 将时间戳拼接上去
```

```c
sub_1599BC((int)&unk_535C88, "HEYTEA6H7J8K9M0N");  // AES 密钥
```

```c
// 检查密钥长度必须是 16, 24 或 32 字节
// 检查 IV 必须是 16 字节
EVP_CIPHER_CTX_new();
EVP_aes_128_cbc();  // 使用 AES-128-CBC
EVP_EncryptInit_ex(v10, v13, 0, *a2, *a3);  // key 和 IV
EVP_EncryptUpdate(...);
EVP_EncryptFinal_ex(...);


```

太好了！现在我完全理解了签名算法的核心逻辑。让我分析 `sign` 参数的生成逻辑：

现在我有了完整的算法分析结果。让我更新签名生成脚本，尝试找到正确的 IV：

```python
"""
喜茶DIY贴纸签名算法实现
基于libheyteago.so逆向分析

算法流程（来自IDA逆向分析）:
1. 获取当前毫秒时间戳 t
2. 构造输入字符串: fileHash + "#" + t
3. 使用 AES-128-CBC 加密
4. 将加密结果转为十六进制字符串作为 hash
5. sign 可能是对某些参数的 MD5

密钥信息（从libheyteago.so地址 0x4379f4 提取）:
- AES Key: HEYTEA6H7J8K9M0N (16字节)
- IV候选值: 
  - AN1wedUvOJblfD8Z (16字节) 
  - 2lJuL8jdH03oNIq5 (16字节)
  - 全零IV (常见默认值)
"""

import hashlib
import time
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad

# 硬编码密钥（从libheyteago.so提取）
AES_KEY = b"HEYTEA6H7J8K9M0N"  # 16 bytes = AES-128
IV_OPTIONS = [
    b"AN1wedUvOJblfD8Z",  # 选项1
    b"2lJuL8jdH03oNIq5",  # 选项2
    b"\x00" * 16,          # 全零IV
    b"HEYTEA6H7J8K9M0N",  # 密钥作为IV
]


def calculate_file_hash(file_path: str) -> str:
    """计算文件的MD5哈希"""
    with open(file_path, "rb") as f:
        return hashlib.md5(f.read()).hexdigest()


def aes_cbc_encrypt(plaintext: bytes, key: bytes, iv: bytes) -> bytes:
    """AES-CBC加密"""
    cipher = AES.new(key, AES.MODE_CBC, iv)
    padded_data = pad(plaintext, AES.block_size)
    return cipher.encrypt(padded_data)


def aes_cbc_decrypt(ciphertext: bytes, key: bytes, iv: bytes) -> bytes:
    """AES-CBC解密"""
    cipher = AES.new(key, AES.MODE_CBC, iv)
    decrypted = cipher.decrypt(ciphertext)
    try:
        return unpad(decrypted, AES.block_size)
    except ValueError:
        return decrypted  # 如果unpad失败，返回原始解密数据


def calculate_diy_sign(file_hash: str, timestamp: int = None) -> dict:
    """
    计算DIY贴纸签名
    
    参数:
        file_hash: 文件MD5哈希值（32字符十六进制）
        timestamp: 时间戳（毫秒），默认为当前时间
    
    返回:
        包含 sign, hash, t 的字典
    """
    if timestamp is None:
        timestamp = int(time.time() * 1000)
    
    t_str = str(timestamp)
    
    # 方案1: fileHash#timestamp
    plaintext_1 = f"{file_hash}#{t_str}".encode('utf-8')
    
    # 方案2: 仅 fileHash
    plaintext_2 = file_hash.encode('utf-8')
    
    # 使用两个不同的IV尝试加密
    results = {}
    
    # 尝试不同的组合
    for iv_name, iv in [("IV1", IV_OPTION_1), ("IV2", IV_OPTION_2)]:
        for pt_name, plaintext in [("hash#t", plaintext_1), ("hash", plaintext_2)]:
            try:
                encrypted = aes_cbc_encrypt(plaintext, AES_KEY, iv)
                encrypted_hex = encrypted.hex()
                results[f"{iv_name}_{pt_name}"] = encrypted_hex
            except Exception as e:
                results[f"{iv_name}_{pt_name}"] = f"Error: {e}"
    
    # 计算可能的sign（MD5）
    sign_candidates = {
        "md5(fileHash)": hashlib.md5(file_hash.encode()).hexdigest(),
        "md5(t)": hashlib.md5(t_str.encode()).hexdigest(),
        "md5(fileHash+t)": hashlib.md5(f"{file_hash}{t_str}".encode()).hexdigest(),
        "md5(t+fileHash)": hashlib.md5(f"{t_str}{file_hash}".encode()).hexdigest(),
        "md5(fileHash#t)": hashlib.md5(f"{file_hash}#{t_str}".encode()).hexdigest(),
    }
    
    return {
        "t": timestamp,
        "file_hash": file_hash,
        "hash_candidates": results,
        "sign_candidates": sign_candidates,
    }


def generate_request_params(file_path: str, shop_id: str = "4216") -> dict:
    """
    生成请求参数
    
    这个函数尝试重现calDIYSign的行为
    """
    file_hash = calculate_file_hash(file_path)
    timestamp = int(time.time() * 1000)
    
    # 基于逆向分析，最可能的组合
    plaintext = f"{file_hash}#{timestamp}".encode('utf-8')
    
    # 使用主密钥和IV
    encrypted = aes_cbc_encrypt(plaintext, AES_KEY, IV_OPTION_1)
    hash_param = encrypted.hex()
    
    # sign可能是某种MD5
    sign_param = hashlib.md5(f"{file_hash}{timestamp}".encode()).hexdigest()
    
    return {
        "sign": sign_param,
        "t": str(timestamp),
        "hash": hash_param,
        "shopId": shop_id,
        "fileHash": file_hash,
    }


if __name__ == "__main__":
    # 测试用例 - 使用HAR包中的数据验证
    print("=" * 60)
    print("喜茶DIY签名算法分析")
    print("=" * 60)
    
    # HAR包中的原始数据
    original_data = {
        "sign": "805f30f5c3cb85e1779ae3daacf8dce5",
        "t": "1764833231217",
        "hash": "fc8aad8184823f1352f85228236f625c679f39c8d5ac4d86359af0572dc9c621c7b976db4c13fd219d28193536058f304031ff6d446277cd40009e4998ee8e1d31cd1c79e4075c5b1cba897d42d81451",
        "shopId": "4216",
        "filename_hash": "32e6dbcc2678d4d40a297ca5490acc4d",  # from tmp_xxx.png
    }
    
    print("\n原始HAR数据:")
    print(f"  sign: {original_data['sign']} (长度: {len(original_data['sign'])})")
    print(f"  t: {original_data['t']}")
    print(f"  hash: {original_data['hash'][:50]}... (长度: {len(original_data['hash'])})")
    print(f"  hash bytes: {len(original_data['hash']) // 2} bytes")
    
    print("\n密钥信息 (从libheyteago.so提取):")
    print(f"  AES Key: {AES_KEY.decode()}")
    print(f"  IV #1: {IV_OPTION_1.decode()}")
    print(f"  IV #2: {IV_OPTION_2.decode()}")
    
    # 使用原始数据测试
    print("\n测试签名计算:")
    result = calculate_diy_sign(
        original_data["filename_hash"], 
        int(original_data["t"])
    )
    
    print(f"\n输入:")
    print(f"  fileHash: {result['file_hash']}")
    print(f"  timestamp: {result['t']}")
    
    print(f"\n计算的hash候选值:")
    for name, value in result["hash_candidates"].items():
        match = "✓ MATCH!" if value == original_data["hash"] else ""
        print(f"  {name}: {value[:50]}... {match}")
    
    print(f"\n计算的sign候选值:")
    for name, value in result["sign_candidates"].items():
        match = "✓ MATCH!" if value == original_data["sign"] else ""
        print(f"  {name}: {value} {match}")
    
    # 尝试解密原始hash
    print("\n" + "=" * 60)
    print("尝试解密原始hash参数:")
    print("=" * 60)
    
    try:
        from Crypto.Cipher import AES
        from Crypto.Util.Padding import unpad
        
        original_hash_bytes = bytes.fromhex(original_data["hash"])
        print(f"原始hash长度: {len(original_hash_bytes)} bytes")
        
        for iv_name, iv in [("IV1", IV_OPTION_1), ("IV2", IV_OPTION_2)]:
            try:
                cipher = AES.new(AES_KEY, AES.MODE_CBC, iv)
                decrypted = cipher.decrypt(original_hash_bytes)
                # 尝试unpad
                try:
                    unpadded = unpad(decrypted, AES.block_size)
                    print(f"\n使用 {iv_name} 解密成功:")
                    print(f"  结果: {unpadded}")
                    print(f"  UTF-8: {unpadded.decode('utf-8', errors='replace')}")
                except:
                    print(f"\n使用 {iv_name} 解密 (无法unpad):")
                    print(f"  原始: {decrypted}")
                    print(f"  UTF-8: {decrypted.decode('utf-8', errors='replace')}")
            except Exception as e:
                print(f"\n使用 {iv_name} 解密失败: {e}")
                
    except ImportError:
        print("需要安装 pycryptodome: pip install pycryptodome")
    
    print("\n" + "=" * 60)
    print("如果要使用真实文件测试，请运行:")
    print("  python heytea_sign.py <image_path>")
    print("=" * 60)


```

```python
"""
喜茶DIY贴纸签名算法实现
基于libheyteago.so逆向分析

算法流程（来自IDA逆向分析）:
1. 获取当前毫秒时间戳 t
2. 构造输入字符串: fileHash + "#" + t
3. 使用 AES-128-CBC 加密
4. 将加密结果转为十六进制字符串作为 hash
5. sign 可能是对某些参数的 MD5

密钥信息（从libheyteago.so地址 0x4379f4 提取）:
- AES Key: HEYTEA6H7J8K9M0N (16字节)
- IV候选值: 
  - AN1wedUvOJblfD8Z (16字节) 
  - 2lJuL8jdH03oNIq5 (16字节)
  - 全零IV (常见默认值)
"""

import hashlib
import time
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad

# 硬编码密钥（从libheyteago.so提取）
AES_KEY = b"HEYTEA6H7J8K9M0N"  # 16 bytes = AES-128
IV_OPTIONS = [
    b"AN1wedUvOJblfD8Z",  # 选项1
    b"2lJuL8jdH03oNIq5",  # 选项2
    b"\x00" * 16,          # 全零IV
    b"HEYTEA6H7J8K9M0N",  # 密钥作为IV
]


def calculate_file_hash(file_path: str) -> str:
    """计算文件的MD5哈希"""
    with open(file_path, "rb") as f:
        return hashlib.md5(f.read()).hexdigest()


def aes_cbc_encrypt(plaintext: bytes, key: bytes, iv: bytes) -> bytes:
    """AES-CBC加密"""
    cipher = AES.new(key, AES.MODE_CBC, iv)
    padded_data = pad(plaintext, AES.block_size)
    return cipher.encrypt(padded_data)


def aes_cbc_decrypt(ciphertext: bytes, key: bytes, iv: bytes) -> bytes:
    """AES-CBC解密"""
    cipher = AES.new(key, AES.MODE_CBC, iv)
    decrypted = cipher.decrypt(ciphertext)
    try:
        return unpad(decrypted, AES.block_size)
    except ValueError:
        return decrypted  # 如果unpad失败，返回原始解密数据


def calculate_sign(file_hash: str, timestamp: str, shop_id: str, hash_value: str) -> str:
    """
    计算 sign 参数 (MD5)
    尝试多种组合方式
    """
    combinations = [
        f"{file_hash}{timestamp}{shop_id}",
        f"{timestamp}{file_hash}{shop_id}",
        f"{shop_id}{timestamp}{file_hash}",
        f"{hash_value}",
        f"{file_hash}#{timestamp}",
        f"fileHash={file_hash}&t={timestamp}&shopId={shop_id}",
        f"{file_hash}{timestamp}",
        f"{timestamp}{shop_id}",
    ]
    
    results = {}
    for combo in combinations:
        results[combo[:50]] = hashlib.md5(combo.encode()).hexdigest()
    return results


def calculate_diy_sign(file_hash: str, timestamp: int = None, iv_index: int = 0) -> dict:
    """
    计算DIY贴纸签名
    
    参数:
        file_hash: 文件MD5哈希值（32字符十六进制）
        timestamp: 时间戳（毫秒），默认为当前时间
    
    返回:
        包含 sign, hash, t 的字典
    """
    if timestamp is None:
        timestamp = int(time.time() * 1000)
    
    t_str = str(timestamp)
    
    # 方案1: fileHash#timestamp
    plaintext_1 = f"{file_hash}#{t_str}".encode('utf-8')
    
    # 方案2: 仅 fileHash
    plaintext_2 = file_hash.encode('utf-8')
    
    # 使用两个不同的IV尝试加密
    results = {}
    
    # 尝试不同的组合
    for iv_name, iv in [("IV1", IV_OPTION_1), ("IV2", IV_OPTION_2)]:
        for pt_name, plaintext in [("hash#t", plaintext_1), ("hash", plaintext_2)]:
            try:
                encrypted = aes_cbc_encrypt(plaintext, AES_KEY, iv)
                encrypted_hex = encrypted.hex()
                results[f"{iv_name}_{pt_name}"] = encrypted_hex
            except Exception as e:
                results[f"{iv_name}_{pt_name}"] = f"Error: {e}"
    
    # 计算可能的sign（MD5）
    sign_candidates = {
        "md5(fileHash)": hashlib.md5(file_hash.encode()).hexdigest(),
        "md5(t)": hashlib.md5(t_str.encode()).hexdigest(),
        "md5(fileHash+t)": hashlib.md5(f"{file_hash}{t_str}".encode()).hexdigest(),
        "md5(t+fileHash)": hashlib.md5(f"{t_str}{file_hash}".encode()).hexdigest(),
        "md5(fileHash#t)": hashlib.md5(f"{file_hash}#{t_str}".encode()).hexdigest(),
    }
    
    return {
        "t": timestamp,
        "file_hash": file_hash,
        "hash_candidates": results,
        "sign_candidates": sign_candidates,
    }


def generate_request_params(file_path: str, shop_id: str = "4216") -> dict:
    """
    生成请求参数
    
    这个函数尝试重现calDIYSign的行为
    """
    file_hash = calculate_file_hash(file_path)
    timestamp = int(time.time() * 1000)
    
    # 基于逆向分析，最可能的组合
    plaintext = f"{file_hash}#{timestamp}".encode('utf-8')
    
    # 使用主密钥和IV
    encrypted = aes_cbc_encrypt(plaintext, AES_KEY, IV_OPTION_1)
    hash_param = encrypted.hex()
    
    # sign可能是某种MD5
    sign_param = hashlib.md5(f"{file_hash}{timestamp}".encode()).hexdigest()
    
    return {
        "sign": sign_param,
        "t": str(timestamp),
        "hash": hash_param,
        "shopId": shop_id,
        "fileHash": file_hash,
    }


if __name__ == "__main__":
    # 测试用例 - 使用HAR包中的数据验证
    print("=" * 60)
    print("喜茶DIY签名算法分析")
    print("=" * 60)
    
    # HAR包中的原始数据
    original_data = {
        "sign": "805f30f5c3cb85e1779ae3daacf8dce5",
        "t": "1764833231217",
        "hash": "fc8aad8184823f1352f85228236f625c679f39c8d5ac4d86359af0572dc9c621c7b976db4c13fd219d28193536058f304031ff6d446277cd40009e4998ee8e1d31cd1c79e4075c5b1cba897d42d81451",
        "shopId": "4216",
        "filename_hash": "32e6dbcc2678d4d40a297ca5490acc4d",  # from tmp_xxx.png
    }
    
    print("\n原始HAR数据:")
    print(f"  sign: {original_data['sign']} (长度: {len(original_data['sign'])})")
    print(f"  t: {original_data['t']}")
    print(f"  hash: {original_data['hash'][:50]}... (长度: {len(original_data['hash'])})")
    print(f"  hash bytes: {len(original_data['hash']) // 2} bytes")
    
    print("\n密钥信息 (从libheyteago.so提取):")
    print(f"  AES Key: {AES_KEY.decode()}")
    print(f"  IV #1: {IV_OPTION_1.decode()}")
    print(f"  IV #2: {IV_OPTION_2.decode()}")
    
    # 使用原始数据测试
    print("\n测试签名计算:")
    result = calculate_diy_sign(
        original_data["filename_hash"], 
        int(original_data["t"])
    )
    
    print(f"\n输入:")
    print(f"  fileHash: {result['file_hash']}")
    print(f"  timestamp: {result['t']}")
    
    print(f"\n计算的hash候选值:")
    for name, value in result["hash_candidates"].items():
        match = "✓ MATCH!" if value == original_data["hash"] else ""
        print(f"  {name}: {value[:50]}... {match}")
    
    print(f"\n计算的sign候选值:")
    for name, value in result["sign_candidates"].items():
        match = "✓ MATCH!" if value == original_data["sign"] else ""
        print(f"  {name}: {value} {match}")
    
    # 尝试解密原始hash
    print("\n" + "=" * 60)
    print("尝试解密原始hash参数:")
    print("=" * 60)
    
    try:
        from Crypto.Cipher import AES
        from Crypto.Util.Padding import unpad
        
        original_hash_bytes = bytes.fromhex(original_data["hash"])
        print(f"原始hash长度: {len(original_hash_bytes)} bytes")
        
        for iv_name, iv in [("IV1", IV_OPTION_1), ("IV2", IV_OPTION_2)]:
            try:
                cipher = AES.new(AES_KEY, AES.MODE_CBC, iv)
                decrypted = cipher.decrypt(original_hash_bytes)
                # 尝试unpad
                try:
                    unpadded = unpad(decrypted, AES.block_size)
                    print(f"\n使用 {iv_name} 解密成功:")
                    print(f"  结果: {unpadded}")
                    print(f"  UTF-8: {unpadded.decode('utf-8', errors='replace')}")
                except:
                    print(f"\n使用 {iv_name} 解密 (无法unpad):")
                    print(f"  原始: {decrypted}")
                    print(f"  UTF-8: {decrypted.decode('utf-8', errors='replace')}")
            except Exception as e:
                print(f"\n使用 {iv_name} 解密失败: {e}")
                
    except ImportError:
        print("需要安装 pycryptodome: pip install pycryptodome")
    
    print("\n" + "=" * 60)
    print("如果要使用真实文件测试，请运行:")
    print("  python heytea_sign.py <image_path>")
    print("=" * 60)


```

我需要修复脚本中的变量引用问题：

```python
"""
喜茶DIY贴纸签名算法实现
基于libheyteago.so逆向分析

算法流程（来自IDA逆向分析）:
1. 获取当前毫秒时间戳 t
2. 构造输入字符串: fileHash + "#" + t
3. 使用 AES-128-CBC 加密
4. 将加密结果转为十六进制字符串作为 hash
5. sign 可能是对某些参数的 MD5

密钥信息（从libheyteago.so地址 0x4379f4 提取）:
- AES Key: HEYTEA6H7J8K9M0N (16字节)
- IV候选值: 
  - AN1wedUvOJblfD8Z (16字节) 
  - 2lJuL8jdH03oNIq5 (16字节)
  - 全零IV (常见默认值)
"""

import hashlib
import time
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad

# 硬编码密钥（从libheyteago.so提取）
AES_KEY = b"HEYTEA6H7J8K9M0N"  # 16 bytes = AES-128
IV_OPTIONS = [
    b"AN1wedUvOJblfD8Z",  # 选项1
    b"2lJuL8jdH03oNIq5",  # 选项2
    b"\x00" * 16,          # 全零IV
    b"HEYTEA6H7J8K9M0N",  # 密钥作为IV
]


def calculate_file_hash(file_path: str) -> str:
    """计算文件的MD5哈希"""
    with open(file_path, "rb") as f:
        return hashlib.md5(f.read()).hexdigest()


def aes_cbc_encrypt(plaintext: bytes, key: bytes, iv: bytes) -> bytes:
    """AES-CBC加密"""
    cipher = AES.new(key, AES.MODE_CBC, iv)
    padded_data = pad(plaintext, AES.block_size)
    return cipher.encrypt(padded_data)


def aes_cbc_decrypt(ciphertext: bytes, key: bytes, iv: bytes) -> bytes:
    """AES-CBC解密"""
    cipher = AES.new(key, AES.MODE_CBC, iv)
    decrypted = cipher.decrypt(ciphertext)
    try:
        return unpad(decrypted, AES.block_size)
    except ValueError:
        return decrypted  # 如果unpad失败，返回原始解密数据


def calculate_sign(file_hash: str, timestamp: str, shop_id: str, hash_value: str) -> str:
    """
    计算 sign 参数 (MD5)
    尝试多种组合方式
    """
    combinations = [
        f"{file_hash}{timestamp}{shop_id}",
        f"{timestamp}{file_hash}{shop_id}",
        f"{shop_id}{timestamp}{file_hash}",
        f"{hash_value}",
        f"{file_hash}#{timestamp}",
        f"fileHash={file_hash}&t={timestamp}&shopId={shop_id}",
        f"{file_hash}{timestamp}",
        f"{timestamp}{shop_id}",
    ]
    
    results = {}
    for combo in combinations:
        results[combo[:50]] = hashlib.md5(combo.encode()).hexdigest()
    return results


def calculate_diy_sign(file_hash: str, timestamp: int = None, iv_index: int = 0) -> dict:
    """
    计算DIY贴纸签名
    
    参数:
        file_hash: 文件MD5哈希值（32字符十六进制）
        timestamp: 时间戳（毫秒），默认为当前时间
    
    返回:
        包含 sign, hash, t 的字典
    """
    if timestamp is None:
        timestamp = int(time.time() * 1000)
    
    t_str = str(timestamp)
    
    # 方案1: fileHash#timestamp
    plaintext_1 = f"{file_hash}#{t_str}".encode('utf-8')
    
    # 方案2: 仅 fileHash
    plaintext_2 = file_hash.encode('utf-8')
    
    # 使用两个不同的IV尝试加密
    results = {}
    
    # 尝试不同的组合
    iv_names = ["IV1_AN1wed", "IV2_2lJuL8", "IV3_zeros", "IV4_key"]
    for iv_idx, iv in enumerate(IV_OPTIONS):
        for pt_name, plaintext in [("hash#t", plaintext_1), ("hash", plaintext_2)]:
            try:
                encrypted = aes_cbc_encrypt(plaintext, AES_KEY, iv)
                encrypted_hex = encrypted.hex()
                results[f"{iv_names[iv_idx]}_{pt_name}"] = encrypted_hex
            except Exception as e:
                results[f"{iv_names[iv_idx]}_{pt_name}"] = f"Error: {e}"
    
    # 计算可能的sign（MD5）
    sign_candidates = {
        "md5(fileHash)": hashlib.md5(file_hash.encode()).hexdigest(),
        "md5(t)": hashlib.md5(t_str.encode()).hexdigest(),
        "md5(fileHash+t)": hashlib.md5(f"{file_hash}{t_str}".encode()).hexdigest(),
        "md5(t+fileHash)": hashlib.md5(f"{t_str}{file_hash}".encode()).hexdigest(),
        "md5(fileHash#t)": hashlib.md5(f"{file_hash}#{t_str}".encode()).hexdigest(),
    }
    
    return {
        "t": timestamp,
        "file_hash": file_hash,
        "hash_candidates": results,
        "sign_candidates": sign_candidates,
    }


def generate_request_params(file_path: str, shop_id: str = "4216") -> dict:
    """
    生成请求参数
    
    这个函数尝试重现calDIYSign的行为
    """
    file_hash = calculate_file_hash(file_path)
    timestamp = int(time.time() * 1000)
    
    # 基于逆向分析，最可能的组合
    plaintext = f"{file_hash}#{timestamp}".encode('utf-8')
    
    # 使用主密钥和IV
    encrypted = aes_cbc_encrypt(plaintext, AES_KEY, IV_OPTION_1)
    hash_param = encrypted.hex()
    
    # sign可能是某种MD5
    sign_param = hashlib.md5(f"{file_hash}{timestamp}".encode()).hexdigest()
    
    return {
        "sign": sign_param,
        "t": str(timestamp),
        "hash": hash_param,
        "shopId": shop_id,
        "fileHash": file_hash,
    }


if __name__ == "__main__":
    # 测试用例 - 使用HAR包中的数据验证
    print("=" * 60)
    print("喜茶DIY签名算法分析")
    print("=" * 60)
    
    # HAR包中的原始数据
    original_data = {
        "sign": "805f30f5c3cb85e1779ae3daacf8dce5",
        "t": "1764833231217",
        "hash": "fc8aad8184823f1352f85228236f625c679f39c8d5ac4d86359af0572dc9c621c7b976db4c13fd219d28193536058f304031ff6d446277cd40009e4998ee8e1d31cd1c79e4075c5b1cba897d42d81451",
        "shopId": "4216",
        "filename_hash": "32e6dbcc2678d4d40a297ca5490acc4d",  # from tmp_xxx.png
    }
    
    print("\n原始HAR数据:")
    print(f"  sign: {original_data['sign']} (长度: {len(original_data['sign'])})")
    print(f"  t: {original_data['t']}")
    print(f"  hash: {original_data['hash'][:50]}... (长度: {len(original_data['hash'])})")
    print(f"  hash bytes: {len(original_data['hash']) // 2} bytes")
    
    print("\n密钥信息 (从libheyteago.so提取):")
    print(f"  AES Key: {AES_KEY.decode()}")
    print(f"  IV #1: {IV_OPTION_1.decode()}")
    print(f"  IV #2: {IV_OPTION_2.decode()}")
    
    # 使用原始数据测试
    print("\n测试签名计算:")
    result = calculate_diy_sign(
        original_data["filename_hash"], 
        int(original_data["t"])
    )
    
    print(f"\n输入:")
    print(f"  fileHash: {result['file_hash']}")
    print(f"  timestamp: {result['t']}")
    
    print(f"\n计算的hash候选值:")
    for name, value in result["hash_candidates"].items():
        match = "✓ MATCH!" if value == original_data["hash"] else ""
        print(f"  {name}: {value[:50]}... {match}")
    
    print(f"\n计算的sign候选值:")
    for name, value in result["sign_candidates"].items():
        match = "✓ MATCH!" if value == original_data["sign"] else ""
        print(f"  {name}: {value} {match}")
    
    # 尝试解密原始hash
    print("\n" + "=" * 60)
    print("尝试解密原始hash参数:")
    print("=" * 60)
    
    try:
        from Crypto.Cipher import AES
        from Crypto.Util.Padding import unpad
        
        original_hash_bytes = bytes.fromhex(original_data["hash"])
        print(f"原始hash长度: {len(original_hash_bytes)} bytes")
        
        for iv_name, iv in [("IV1", IV_OPTION_1), ("IV2", IV_OPTION_2)]:
            try:
                cipher = AES.new(AES_KEY, AES.MODE_CBC, iv)
                decrypted = cipher.decrypt(original_hash_bytes)
                # 尝试unpad
                try:
                    unpadded = unpad(decrypted, AES.block_size)
                    print(f"\n使用 {iv_name} 解密成功:")
                    print(f"  结果: {unpadded}")
                    print(f"  UTF-8: {unpadded.decode('utf-8', errors='replace')}")
                except:
                    print(f"\n使用 {iv_name} 解密 (无法unpad):")
                    print(f"  原始: {decrypted}")
                    print(f"  UTF-8: {decrypted.decode('utf-8', errors='replace')}")
            except Exception as e:
                print(f"\n使用 {iv_name} 解密失败: {e}")
                
    except ImportError:
        print("需要安装 pycryptodome: pip install pycryptodome")
    
    print("\n" + "=" * 60)
    print("如果要使用真实文件测试，请运行:")
    print("  python heytea_sign.py <image_path>")
    print("=" * 60)


```

```python
"""
喜茶DIY贴纸签名算法实现
基于libheyteago.so逆向分析

算法流程（来自IDA逆向分析）:
1. 获取当前毫秒时间戳 t
2. 构造输入字符串: fileHash + "#" + t
3. 使用 AES-128-CBC 加密
4. 将加密结果转为十六进制字符串作为 hash
5. sign 可能是对某些参数的 MD5

密钥信息（从libheyteago.so地址 0x4379f4 提取）:
- AES Key: HEYTEA6H7J8K9M0N (16字节)
- IV候选值: 
  - AN1wedUvOJblfD8Z (16字节) 
  - 2lJuL8jdH03oNIq5 (16字节)
  - 全零IV (常见默认值)
"""

import hashlib
import time
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad

# 硬编码密钥（从libheyteago.so提取）
AES_KEY = b"HEYTEA6H7J8K9M0N"  # 16 bytes = AES-128
IV_OPTIONS = [
    b"AN1wedUvOJblfD8Z",  # 选项1
    b"2lJuL8jdH03oNIq5",  # 选项2
    b"\x00" * 16,          # 全零IV
    b"HEYTEA6H7J8K9M0N",  # 密钥作为IV
]


def calculate_file_hash(file_path: str) -> str:
    """计算文件的MD5哈希"""
    with open(file_path, "rb") as f:
        return hashlib.md5(f.read()).hexdigest()


def aes_cbc_encrypt(plaintext: bytes, key: bytes, iv: bytes) -> bytes:
    """AES-CBC加密"""
    cipher = AES.new(key, AES.MODE_CBC, iv)
    padded_data = pad(plaintext, AES.block_size)
    return cipher.encrypt(padded_data)


def aes_cbc_decrypt(ciphertext: bytes, key: bytes, iv: bytes) -> bytes:
    """AES-CBC解密"""
    cipher = AES.new(key, AES.MODE_CBC, iv)
    decrypted = cipher.decrypt(ciphertext)
    try:
        return unpad(decrypted, AES.block_size)
    except ValueError:
        return decrypted  # 如果unpad失败，返回原始解密数据


def calculate_sign(file_hash: str, timestamp: str, shop_id: str, hash_value: str) -> str:
    """
    计算 sign 参数 (MD5)
    尝试多种组合方式
    """
    combinations = [
        f"{file_hash}{timestamp}{shop_id}",
        f"{timestamp}{file_hash}{shop_id}",
        f"{shop_id}{timestamp}{file_hash}",
        f"{hash_value}",
        f"{file_hash}#{timestamp}",
        f"fileHash={file_hash}&t={timestamp}&shopId={shop_id}",
        f"{file_hash}{timestamp}",
        f"{timestamp}{shop_id}",
    ]
    
    results = {}
    for combo in combinations:
        results[combo[:50]] = hashlib.md5(combo.encode()).hexdigest()
    return results


def calculate_diy_sign(file_hash: str, timestamp: int = None, iv_index: int = 0) -> dict:
    """
    计算DIY贴纸签名
    
    参数:
        file_hash: 文件MD5哈希值（32字符十六进制）
        timestamp: 时间戳（毫秒），默认为当前时间
    
    返回:
        包含 sign, hash, t 的字典
    """
    if timestamp is None:
        timestamp = int(time.time() * 1000)
    
    t_str = str(timestamp)
    
    # 方案1: fileHash#timestamp
    plaintext_1 = f"{file_hash}#{t_str}".encode('utf-8')
    
    # 方案2: 仅 fileHash
    plaintext_2 = file_hash.encode('utf-8')
    
    # 使用两个不同的IV尝试加密
    results = {}
    
    # 尝试不同的组合
    iv_names = ["IV1_AN1wed", "IV2_2lJuL8", "IV3_zeros", "IV4_key"]
    for iv_idx, iv in enumerate(IV_OPTIONS):
        for pt_name, plaintext in [("hash#t", plaintext_1), ("hash", plaintext_2)]:
            try:
                encrypted = aes_cbc_encrypt(plaintext, AES_KEY, iv)
                encrypted_hex = encrypted.hex()
                results[f"{iv_names[iv_idx]}_{pt_name}"] = encrypted_hex
            except Exception as e:
                results[f"{iv_names[iv_idx]}_{pt_name}"] = f"Error: {e}"
    
    # 计算可能的sign（MD5）
    sign_candidates = {
        "md5(fileHash)": hashlib.md5(file_hash.encode()).hexdigest(),
        "md5(t)": hashlib.md5(t_str.encode()).hexdigest(),
        "md5(fileHash+t)": hashlib.md5(f"{file_hash}{t_str}".encode()).hexdigest(),
        "md5(t+fileHash)": hashlib.md5(f"{t_str}{file_hash}".encode()).hexdigest(),
        "md5(fileHash#t)": hashlib.md5(f"{file_hash}#{t_str}".encode()).hexdigest(),
    }
    
    return {
        "t": timestamp,
        "file_hash": file_hash,
        "hash_candidates": results,
        "sign_candidates": sign_candidates,
    }


def generate_request_params(file_path: str, shop_id: str = "4216") -> dict:
    """
    生成请求参数
    
    这个函数尝试重现calDIYSign的行为
    """
    file_hash = calculate_file_hash(file_path)
    timestamp = int(time.time() * 1000)
    
    # 基于逆向分析，最可能的组合
    plaintext = f"{file_hash}#{timestamp}".encode('utf-8')
    
    # 使用主密钥和IV
    encrypted = aes_cbc_encrypt(plaintext, AES_KEY, IV_OPTIONS[0])
    hash_param = encrypted.hex()
    
    # sign可能是某种MD5
    sign_param = hashlib.md5(f"{file_hash}{timestamp}".encode()).hexdigest()
    
    return {
        "sign": sign_param,
        "t": str(timestamp),
        "hash": hash_param,
        "shopId": shop_id,
        "fileHash": file_hash,
    }


if __name__ == "__main__":
    # 测试用例 - 使用HAR包中的数据验证
    print("=" * 60)
    print("喜茶DIY签名算法分析")
    print("=" * 60)
    
    # HAR包中的原始数据
    original_data = {
        "sign": "805f30f5c3cb85e1779ae3daacf8dce5",
        "t": "1764833231217",
        "hash": "fc8aad8184823f1352f85228236f625c679f39c8d5ac4d86359af0572dc9c621c7b976db4c13fd219d28193536058f304031ff6d446277cd40009e4998ee8e1d31cd1c79e4075c5b1cba897d42d81451",
        "shopId": "4216",
        "filename_hash": "32e6dbcc2678d4d40a297ca5490acc4d",  # from tmp_xxx.png
    }
    
    print("\n原始HAR数据:")
    print(f"  sign: {original_data['sign']} (长度: {len(original_data['sign'])})")
    print(f"  t: {original_data['t']}")
    print(f"  hash: {original_data['hash'][:50]}... (长度: {len(original_data['hash'])})")
    print(f"  hash bytes: {len(original_data['hash']) // 2} bytes")
    
    print("\n密钥信息 (从libheyteago.so提取):")
    print(f"  AES Key: {AES_KEY.decode()}")
    print(f"  IV #1: {IV_OPTION_1.decode()}")
    print(f"  IV #2: {IV_OPTION_2.decode()}")
    
    # 使用原始数据测试
    print("\n测试签名计算:")
    result = calculate_diy_sign(
        original_data["filename_hash"], 
        int(original_data["t"])
    )
    
    print(f"\n输入:")
    print(f"  fileHash: {result['file_hash']}")
    print(f"  timestamp: {result['t']}")
    
    print(f"\n计算的hash候选值:")
    for name, value in result["hash_candidates"].items():
        match = "✓ MATCH!" if value == original_data["hash"] else ""
        print(f"  {name}: {value[:50]}... {match}")
    
    print(f"\n计算的sign候选值:")
    for name, value in result["sign_candidates"].items():
        match = "✓ MATCH!" if value == original_data["sign"] else ""
        print(f"  {name}: {value} {match}")
    
    # 尝试解密原始hash
    print("\n" + "=" * 60)
    print("尝试解密原始hash参数:")
    print("=" * 60)
    
    try:
        from Crypto.Cipher import AES
        from Crypto.Util.Padding import unpad
        
        original_hash_bytes = bytes.fromhex(original_data["hash"])
        print(f"原始hash长度: {len(original_hash_bytes)} bytes")
        
        for iv_name, iv in [("IV1", IV_OPTION_1), ("IV2", IV_OPTION_2)]:
            try:
                cipher = AES.new(AES_KEY, AES.MODE_CBC, iv)
                decrypted = cipher.decrypt(original_hash_bytes)
                # 尝试unpad
                try:
                    unpadded = unpad(decrypted, AES.block_size)
                    print(f"\n使用 {iv_name} 解密成功:")
                    print(f"  结果: {unpadded}")
                    print(f"  UTF-8: {unpadded.decode('utf-8', errors='replace')}")
                except:
                    print(f"\n使用 {iv_name} 解密 (无法unpad):")
                    print(f"  原始: {decrypted}")
                    print(f"  UTF-8: {decrypted.decode('utf-8', errors='replace')}")
            except Exception as e:
                print(f"\n使用 {iv_name} 解密失败: {e}")
                
    except ImportError:
        print("需要安装 pycryptodome: pip install pycryptodome")
    
    print("\n" + "=" * 60)
    print("如果要使用真实文件测试，请运行:")
    print("  python heytea_sign.py <image_path>")
    print("=" * 60)


```

```python
"""
喜茶DIY贴纸签名算法实现
基于libheyteago.so逆向分析

算法流程（来自IDA逆向分析）:
1. 获取当前毫秒时间戳 t
2. 构造输入字符串: fileHash + "#" + t
3. 使用 AES-128-CBC 加密
4. 将加密结果转为十六进制字符串作为 hash
5. sign 可能是对某些参数的 MD5

密钥信息（从libheyteago.so地址 0x4379f4 提取）:
- AES Key: HEYTEA6H7J8K9M0N (16字节)
- IV候选值: 
  - AN1wedUvOJblfD8Z (16字节) 
  - 2lJuL8jdH03oNIq5 (16字节)
  - 全零IV (常见默认值)
"""

import hashlib
import time
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad

# 硬编码密钥（从libheyteago.so提取）
AES_KEY = b"HEYTEA6H7J8K9M0N"  # 16 bytes = AES-128
IV_OPTIONS = [
    b"AN1wedUvOJblfD8Z",  # 选项1
    b"2lJuL8jdH03oNIq5",  # 选项2
    b"\x00" * 16,          # 全零IV
    b"HEYTEA6H7J8K9M0N",  # 密钥作为IV
]


def calculate_file_hash(file_path: str) -> str:
    """计算文件的MD5哈希"""
    with open(file_path, "rb") as f:
        return hashlib.md5(f.read()).hexdigest()


def aes_cbc_encrypt(plaintext: bytes, key: bytes, iv: bytes) -> bytes:
    """AES-CBC加密"""
    cipher = AES.new(key, AES.MODE_CBC, iv)
    padded_data = pad(plaintext, AES.block_size)
    return cipher.encrypt(padded_data)


def aes_cbc_decrypt(ciphertext: bytes, key: bytes, iv: bytes) -> bytes:
    """AES-CBC解密"""
    cipher = AES.new(key, AES.MODE_CBC, iv)
    decrypted = cipher.decrypt(ciphertext)
    try:
        return unpad(decrypted, AES.block_size)
    except ValueError:
        return decrypted  # 如果unpad失败，返回原始解密数据


def calculate_sign(file_hash: str, timestamp: str, shop_id: str, hash_value: str) -> str:
    """
    计算 sign 参数 (MD5)
    尝试多种组合方式
    """
    combinations = [
        f"{file_hash}{timestamp}{shop_id}",
        f"{timestamp}{file_hash}{shop_id}",
        f"{shop_id}{timestamp}{file_hash}",
        f"{hash_value}",
        f"{file_hash}#{timestamp}",
        f"fileHash={file_hash}&t={timestamp}&shopId={shop_id}",
        f"{file_hash}{timestamp}",
        f"{timestamp}{shop_id}",
    ]
    
    results = {}
    for combo in combinations:
        results[combo[:50]] = hashlib.md5(combo.encode()).hexdigest()
    return results


def calculate_diy_sign(file_hash: str, timestamp: int = None, iv_index: int = 0) -> dict:
    """
    计算DIY贴纸签名
    
    参数:
        file_hash: 文件MD5哈希值（32字符十六进制）
        timestamp: 时间戳（毫秒），默认为当前时间
    
    返回:
        包含 sign, hash, t 的字典
    """
    if timestamp is None:
        timestamp = int(time.time() * 1000)
    
    t_str = str(timestamp)
    
    # 方案1: fileHash#timestamp
    plaintext_1 = f"{file_hash}#{t_str}".encode('utf-8')
    
    # 方案2: 仅 fileHash
    plaintext_2 = file_hash.encode('utf-8')
    
    # 使用两个不同的IV尝试加密
    results = {}
    
    # 尝试不同的组合
    iv_names = ["IV1_AN1wed", "IV2_2lJuL8", "IV3_zeros", "IV4_key"]
    for iv_idx, iv in enumerate(IV_OPTIONS):
        for pt_name, plaintext in [("hash#t", plaintext_1), ("hash", plaintext_2)]:
            try:
                encrypted = aes_cbc_encrypt(plaintext, AES_KEY, iv)
                encrypted_hex = encrypted.hex()
                results[f"{iv_names[iv_idx]}_{pt_name}"] = encrypted_hex
            except Exception as e:
                results[f"{iv_names[iv_idx]}_{pt_name}"] = f"Error: {e}"
    
    # 计算可能的sign（MD5）
    sign_candidates = {
        "md5(fileHash)": hashlib.md5(file_hash.encode()).hexdigest(),
        "md5(t)": hashlib.md5(t_str.encode()).hexdigest(),
        "md5(fileHash+t)": hashlib.md5(f"{file_hash}{t_str}".encode()).hexdigest(),
        "md5(t+fileHash)": hashlib.md5(f"{t_str}{file_hash}".encode()).hexdigest(),
        "md5(fileHash#t)": hashlib.md5(f"{file_hash}#{t_str}".encode()).hexdigest(),
    }
    
    return {
        "t": timestamp,
        "file_hash": file_hash,
        "hash_candidates": results,
        "sign_candidates": sign_candidates,
    }


def generate_request_params(file_path: str, shop_id: str = "4216") -> dict:
    """
    生成请求参数
    
    这个函数尝试重现calDIYSign的行为
    """
    file_hash = calculate_file_hash(file_path)
    timestamp = int(time.time() * 1000)
    
    # 基于逆向分析，最可能的组合
    plaintext = f"{file_hash}#{timestamp}".encode('utf-8')
    
    # 使用主密钥和IV
    encrypted = aes_cbc_encrypt(plaintext, AES_KEY, IV_OPTIONS[0])
    hash_param = encrypted.hex()
    
    # sign可能是某种MD5
    sign_param = hashlib.md5(f"{file_hash}{timestamp}".encode()).hexdigest()
    
    return {
        "sign": sign_param,
        "t": str(timestamp),
        "hash": hash_param,
        "shopId": shop_id,
        "fileHash": file_hash,
    }


if __name__ == "__main__":
    # 测试用例 - 使用HAR包中的数据验证
    print("=" * 60)
    print("喜茶DIY签名算法分析")
    print("=" * 60)
    
    # HAR包中的原始数据
    original_data = {
        "sign": "805f30f5c3cb85e1779ae3daacf8dce5",
        "t": "1764833231217",
        "hash": "fc8aad8184823f1352f85228236f625c679f39c8d5ac4d86359af0572dc9c621c7b976db4c13fd219d28193536058f304031ff6d446277cd40009e4998ee8e1d31cd1c79e4075c5b1cba897d42d81451",
        "shopId": "4216",
        "filename_hash": "32e6dbcc2678d4d40a297ca5490acc4d",  # from tmp_xxx.png
    }
    
    print("\n原始HAR数据:")
    print(f"  sign: {original_data['sign']} (长度: {len(original_data['sign'])})")
    print(f"  t: {original_data['t']}")
    print(f"  hash: {original_data['hash'][:50]}... (长度: {len(original_data['hash'])})")
    print(f"  hash bytes: {len(original_data['hash']) // 2} bytes")
    
    print("\n密钥信息 (从libheyteago.so提取):")
    print(f"  AES Key: {AES_KEY.decode()}")
    print(f"  IV #1: {IV_OPTIONS[0].decode()}")
    print(f"  IV #2: {IV_OPTIONS[1].decode()}")
    
    # 使用原始数据测试
    print("\n测试签名计算:")
    result = calculate_diy_sign(
        original_data["filename_hash"], 
        int(original_data["t"])
    )
    
    print(f"\n输入:")
    print(f"  fileHash: {result['file_hash']}")
    print(f"  timestamp: {result['t']}")
    
    print(f"\n计算的hash候选值:")
    for name, value in result["hash_candidates"].items():
        match = "✓ MATCH!" if value == original_data["hash"] else ""
        print(f"  {name}: {value[:50]}... {match}")
    
    print(f"\n计算的sign候选值:")
    for name, value in result["sign_candidates"].items():
        match = "✓ MATCH!" if value == original_data["sign"] else ""
        print(f"  {name}: {value} {match}")
    
    # 尝试解密原始hash
    print("\n" + "=" * 60)
    print("尝试解密原始hash参数:")
    print("=" * 60)
    
    try:
        from Crypto.Cipher import AES
        from Crypto.Util.Padding import unpad
        
        original_hash_bytes = bytes.fromhex(original_data["hash"])
        print(f"原始hash长度: {len(original_hash_bytes)} bytes")
        
        for iv_name, iv in [("IV1", IV_OPTION_1), ("IV2", IV_OPTION_2)]:
            try:
                cipher = AES.new(AES_KEY, AES.MODE_CBC, iv)
                decrypted = cipher.decrypt(original_hash_bytes)
                # 尝试unpad
                try:
                    unpadded = unpad(decrypted, AES.block_size)
                    print(f"\n使用 {iv_name} 解密成功:")
                    print(f"  结果: {unpadded}")
                    print(f"  UTF-8: {unpadded.decode('utf-8', errors='replace')}")
                except:
                    print(f"\n使用 {iv_name} 解密 (无法unpad):")
                    print(f"  原始: {decrypted}")
                    print(f"  UTF-8: {decrypted.decode('utf-8', errors='replace')}")
            except Exception as e:
                print(f"\n使用 {iv_name} 解密失败: {e}")
                
    except ImportError:
        print("需要安装 pycryptodome: pip install pycryptodome")
    
    print("\n" + "=" * 60)
    print("如果要使用真实文件测试，请运行:")
    print("  python heytea_sign.py <image_path>")
    print("=" * 60)


```

```python
"""
喜茶DIY贴纸签名算法实现
基于libheyteago.so逆向分析

算法流程（来自IDA逆向分析）:
1. 获取当前毫秒时间戳 t
2. 构造输入字符串: fileHash + "#" + t
3. 使用 AES-128-CBC 加密
4. 将加密结果转为十六进制字符串作为 hash
5. sign 可能是对某些参数的 MD5

密钥信息（从libheyteago.so地址 0x4379f4 提取）:
- AES Key: HEYTEA6H7J8K9M0N (16字节)
- IV候选值: 
  - AN1wedUvOJblfD8Z (16字节) 
  - 2lJuL8jdH03oNIq5 (16字节)
  - 全零IV (常见默认值)
"""

import hashlib
import time
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad

# 硬编码密钥（从libheyteago.so提取）
AES_KEY = b"HEYTEA6H7J8K9M0N"  # 16 bytes = AES-128
IV_OPTIONS = [
    b"AN1wedUvOJblfD8Z",  # 选项1
    b"2lJuL8jdH03oNIq5",  # 选项2
    b"\x00" * 16,          # 全零IV
    b"HEYTEA6H7J8K9M0N",  # 密钥作为IV
]


def calculate_file_hash(file_path: str) -> str:
    """计算文件的MD5哈希"""
    with open(file_path, "rb") as f:
        return hashlib.md5(f.read()).hexdigest()


def aes_cbc_encrypt(plaintext: bytes, key: bytes, iv: bytes) -> bytes:
    """AES-CBC加密"""
    cipher = AES.new(key, AES.MODE_CBC, iv)
    padded_data = pad(plaintext, AES.block_size)
    return cipher.encrypt(padded_data)


def aes_cbc_decrypt(ciphertext: bytes, key: bytes, iv: bytes) -> bytes:
    """AES-CBC解密"""
    cipher = AES.new(key, AES.MODE_CBC, iv)
    decrypted = cipher.decrypt(ciphertext)
    try:
        return unpad(decrypted, AES.block_size)
    except ValueError:
        return decrypted  # 如果unpad失败，返回原始解密数据


def calculate_sign(file_hash: str, timestamp: str, shop_id: str, hash_value: str) -> str:
    """
    计算 sign 参数 (MD5)
    尝试多种组合方式
    """
    combinations = [
        f"{file_hash}{timestamp}{shop_id}",
        f"{timestamp}{file_hash}{shop_id}",
        f"{shop_id}{timestamp}{file_hash}",
        f"{hash_value}",
        f"{file_hash}#{timestamp}",
        f"fileHash={file_hash}&t={timestamp}&shopId={shop_id}",
        f"{file_hash}{timestamp}",
        f"{timestamp}{shop_id}",
    ]
    
    results = {}
    for combo in combinations:
        results[combo[:50]] = hashlib.md5(combo.encode()).hexdigest()
    return results


def calculate_diy_sign(file_hash: str, timestamp: int = None, iv_index: int = 0) -> dict:
    """
    计算DIY贴纸签名
    
    参数:
        file_hash: 文件MD5哈希值（32字符十六进制）
        timestamp: 时间戳（毫秒），默认为当前时间
    
    返回:
        包含 sign, hash, t 的字典
    """
    if timestamp is None:
        timestamp = int(time.time() * 1000)
    
    t_str = str(timestamp)
    
    # 方案1: fileHash#timestamp
    plaintext_1 = f"{file_hash}#{t_str}".encode('utf-8')
    
    # 方案2: 仅 fileHash
    plaintext_2 = file_hash.encode('utf-8')
    
    # 使用两个不同的IV尝试加密
    results = {}
    
    # 尝试不同的组合
    iv_names = ["IV1_AN1wed", "IV2_2lJuL8", "IV3_zeros", "IV4_key"]
    for iv_idx, iv in enumerate(IV_OPTIONS):
        for pt_name, plaintext in [("hash#t", plaintext_1), ("hash", plaintext_2)]:
            try:
                encrypted = aes_cbc_encrypt(plaintext, AES_KEY, iv)
                encrypted_hex = encrypted.hex()
                results[f"{iv_names[iv_idx]}_{pt_name}"] = encrypted_hex
            except Exception as e:
                results[f"{iv_names[iv_idx]}_{pt_name}"] = f"Error: {e}"
    
    # 计算可能的sign（MD5）
    sign_candidates = {
        "md5(fileHash)": hashlib.md5(file_hash.encode()).hexdigest(),
        "md5(t)": hashlib.md5(t_str.encode()).hexdigest(),
        "md5(fileHash+t)": hashlib.md5(f"{file_hash}{t_str}".encode()).hexdigest(),
        "md5(t+fileHash)": hashlib.md5(f"{t_str}{file_hash}".encode()).hexdigest(),
        "md5(fileHash#t)": hashlib.md5(f"{file_hash}#{t_str}".encode()).hexdigest(),
    }
    
    return {
        "t": timestamp,
        "file_hash": file_hash,
        "hash_candidates": results,
        "sign_candidates": sign_candidates,
    }


def generate_request_params(file_path: str, shop_id: str = "4216") -> dict:
    """
    生成请求参数
    
    这个函数尝试重现calDIYSign的行为
    """
    file_hash = calculate_file_hash(file_path)
    timestamp = int(time.time() * 1000)
    
    # 基于逆向分析，最可能的组合
    plaintext = f"{file_hash}#{timestamp}".encode('utf-8')
    
    # 使用主密钥和IV
    encrypted = aes_cbc_encrypt(plaintext, AES_KEY, IV_OPTIONS[0])
    hash_param = encrypted.hex()
    
    # sign可能是某种MD5
    sign_param = hashlib.md5(f"{file_hash}{timestamp}".encode()).hexdigest()
    
    return {
        "sign": sign_param,
        "t": str(timestamp),
        "hash": hash_param,
        "shopId": shop_id,
        "fileHash": file_hash,
    }


if __name__ == "__main__":
    # 测试用例 - 使用HAR包中的数据验证
    print("=" * 60)
    print("喜茶DIY签名算法分析")
    print("=" * 60)
    
    # HAR包中的原始数据
    original_data = {
        "sign": "805f30f5c3cb85e1779ae3daacf8dce5",
        "t": "1764833231217",
        "hash": "fc8aad8184823f1352f85228236f625c679f39c8d5ac4d86359af0572dc9c621c7b976db4c13fd219d28193536058f304031ff6d446277cd40009e4998ee8e1d31cd1c79e4075c5b1cba897d42d81451",
        "shopId": "4216",
        "filename_hash": "32e6dbcc2678d4d40a297ca5490acc4d",  # from tmp_xxx.png
    }
    
    print("\n原始HAR数据:")
    print(f"  sign: {original_data['sign']} (长度: {len(original_data['sign'])})")
    print(f"  t: {original_data['t']}")
    print(f"  hash: {original_data['hash'][:50]}... (长度: {len(original_data['hash'])})")
    print(f"  hash bytes: {len(original_data['hash']) // 2} bytes")
    
    print("\n密钥信息 (从libheyteago.so提取):")
    print(f"  AES Key: {AES_KEY.decode()}")
    print(f"  IV #1: {IV_OPTIONS[0].decode()}")
    print(f"  IV #2: {IV_OPTIONS[1].decode()}")
    
    # 使用原始数据测试
    print("\n测试签名计算:")
    result = calculate_diy_sign(
        original_data["filename_hash"], 
        int(original_data["t"])
    )
    
    print(f"\n输入:")
    print(f"  fileHash: {result['file_hash']}")
    print(f"  timestamp: {result['t']}")
    
    print(f"\n计算的hash候选值:")
    for name, value in result["hash_candidates"].items():
        match = "✓ MATCH!" if value == original_data["hash"] else ""
        print(f"  {name}: {value[:50]}... {match}")
    
    print(f"\n计算的sign候选值:")
    for name, value in result["sign_candidates"].items():
        match = "✓ MATCH!" if value == original_data["sign"] else ""
        print(f"  {name}: {value} {match}")
    
    # 尝试解密原始hash
    print("\n" + "=" * 60)
    print("尝试解密原始hash参数:")
    print("=" * 60)
    
    try:
        from Crypto.Cipher import AES
        from Crypto.Util.Padding import unpad
        
        original_hash_bytes = bytes.fromhex(original_data["hash"])
        print(f"原始hash长度: {len(original_hash_bytes)} bytes")
        
        for iv_name, iv in [("IV1_AN1wed", IV_OPTIONS[0]), ("IV2_2lJuL8", IV_OPTIONS[1]), ("IV3_zeros", IV_OPTIONS[2]), ("IV4_key", IV_OPTIONS[3])]:
            try:
                cipher = AES.new(AES_KEY, AES.MODE_CBC, iv)
                decrypted = cipher.decrypt(original_hash_bytes)
                # 尝试unpad
                try:
                    unpadded = unpad(decrypted, AES.block_size)
                    print(f"\n使用 {iv_name} 解密成功:")
                    print(f"  结果: {unpadded}")
                    print(f"  UTF-8: {unpadded.decode('utf-8', errors='replace')}")
                except:
                    print(f"\n使用 {iv_name} 解密 (无法unpad):")
                    print(f"  原始: {decrypted}")
                    print(f"  UTF-8: {decrypted.decode('utf-8', errors='replace')}")
            except Exception as e:
                print(f"\n使用 {iv_name} 解密失败: {e}")
                
    except ImportError:
        print("需要安装 pycryptodome: pip install pycryptodome")
    
    print("\n" + "=" * 60)
    print("如果要使用真实文件测试，请运行:")
    print("  python heytea_sign.py <image_path>")
    print("=" * 60)


```

现在让我运行更新后的脚本来验证算法：

解密结果表明我们找到的密钥可能正确（后续块的解密结果相同），但IV可能不对。让我深入分析 `sub_15B808` 函数来找到正确的 IV：

```c
sub_15B808(n, &v34, v14, &unk_535C88);
```

我看到关键信息了！`sub_15B808` 被调用时传入了4个参数。让我分析 `sub_15BC94` 函数来找到 IV 的来源：

```c
   v2 = qword_535CD8;  // 检查某个长度值
   if ( (byte_535CD0 & 1) == 0 )
     v2 = (unsigned __int64)(unsigned __int8)byte_535CD0 >> 1;
   if ( v2 != 4 )
     return &unk_535C48;  // 返回 IV1: AN1wedUvOJblfD8Z
   
   // 如果长度是4，进一步检查
   if ( *v3 == 1685025392 )  // 这是某个魔术值
     return &unk_535C68;  // 返回 IV2: 2lJuL8jdH03oNIq5
   else
     return &unk_535C48;  // 返回 IV1: AN1wedUvOJblfD8Z
```

**重大发现！** 我找到了 IV 选择的逻辑！

从 `sub_15BC94` 函数可以看到：
- **IV1**: `AN1wedUvOJblfD8Z` （默认）
- **IV2**: `2lJuL8jdH03oNIq5` （特定条件下使用）

关键是魔术值 `1685025392`（十六进制 `0x64706970` = ASCII "dpip"/"pipd"）。让我验证这个发现并更新脚本：

```c
sub_15B6FC(&v38, a1, "#");  // 拼接 a1 (fileHash) 和 "#"
sub_15E948((int)&v38, v8, v9);  // 追加时间戳字符串
```

魔术值 `1685025392` = ASCII "prod"（生产环境）！这是环境选择逻辑。让我更新脚本来尝试所有可能的组合：

```python
"""
喜茶DIY贴纸签名算法实现
基于libheyteago.so逆向分析

算法流程（来自IDA逆向分析）:
1. 获取当前毫秒时间戳 t
2. 构造输入字符串: fileHash + "#" + t
3. 使用 AES-128-CBC 加密
4. 将加密结果转为十六进制字符串作为 hash
5. sign 可能是对某些参数的 MD5

密钥信息（从libheyteago.so地址 0x4379f4 提取）:
- AES Key: HEYTEA6H7J8K9M0N (16字节)
- IV候选值: 
  - AN1wedUvOJblfD8Z (16字节) 
  - 2lJuL8jdH03oNIq5 (16字节)
  - 全零IV (常见默认值)
"""

import hashlib
import time
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad

# 硬编码密钥（从libheyteago.so提取）
AES_KEY = b"HEYTEA6H7J8K9M0N"  # 16 bytes = AES-128
IV_OPTIONS = [
    b"AN1wedUvOJblfD8Z",  # 选项1
    b"2lJuL8jdH03oNIq5",  # 选项2
    b"\x00" * 16,          # 全零IV
    b"HEYTEA6H7J8K9M0N",  # 密钥作为IV
]


def calculate_file_hash(file_path: str) -> str:
    """计算文件的MD5哈希"""
    with open(file_path, "rb") as f:
        return hashlib.md5(f.read()).hexdigest()


def aes_cbc_encrypt(plaintext: bytes, key: bytes, iv: bytes) -> bytes:
    """AES-CBC加密"""
    cipher = AES.new(key, AES.MODE_CBC, iv)
    padded_data = pad(plaintext, AES.block_size)
    return cipher.encrypt(padded_data)


def aes_cbc_decrypt(ciphertext: bytes, key: bytes, iv: bytes) -> bytes:
    """AES-CBC解密"""
    cipher = AES.new(key, AES.MODE_CBC, iv)
    decrypted = cipher.decrypt(ciphertext)
    try:
        return unpad(decrypted, AES.block_size)
    except ValueError:
        return decrypted  # 如果unpad失败，返回原始解密数据


def calculate_sign(file_hash: str, timestamp: str, shop_id: str, hash_value: str) -> str:
    """
    计算 sign 参数 (MD5)
    尝试多种组合方式
    """
    combinations = [
        f"{file_hash}{timestamp}{shop_id}",
        f"{timestamp}{file_hash}{shop_id}",
        f"{shop_id}{timestamp}{file_hash}",
        f"{hash_value}",
        f"{file_hash}#{timestamp}",
        f"fileHash={file_hash}&t={timestamp}&shopId={shop_id}",
        f"{file_hash}{timestamp}",
        f"{timestamp}{shop_id}",
    ]
    
    results = {}
    for combo in combinations:
        results[combo[:50]] = hashlib.md5(combo.encode()).hexdigest()
    return results


def calculate_diy_sign(file_hash: str, timestamp: int = None, iv_index: int = 0) -> dict:
    """
    计算DIY贴纸签名
    
    参数:
        file_hash: 文件MD5哈希值（32字符十六进制）
        timestamp: 时间戳（毫秒），默认为当前时间
    
    返回:
        包含 sign, hash, t 的字典
    """
    if timestamp is None:
        timestamp = int(time.time() * 1000)
    
    t_str = str(timestamp)
    
    # 方案1: fileHash#timestamp
    plaintext_1 = f"{file_hash}#{t_str}".encode('utf-8')
    
    # 方案2: 仅 fileHash
    plaintext_2 = file_hash.encode('utf-8')
    
    # 使用两个不同的IV尝试加密
    results = {}
    
    # 尝试不同的组合
    iv_names = ["IV1_AN1wed", "IV2_2lJuL8", "IV3_zeros", "IV4_key"]
    for iv_idx, iv in enumerate(IV_OPTIONS):
        for pt_name, plaintext in [("hash#t", plaintext_1), ("hash", plaintext_2)]:
            try:
                encrypted = aes_cbc_encrypt(plaintext, AES_KEY, iv)
                encrypted_hex = encrypted.hex()
                results[f"{iv_names[iv_idx]}_{pt_name}"] = encrypted_hex
            except Exception as e:
                results[f"{iv_names[iv_idx]}_{pt_name}"] = f"Error: {e}"
    
    # 计算可能的sign（MD5）
    sign_candidates = {
        "md5(fileHash)": hashlib.md5(file_hash.encode()).hexdigest(),
        "md5(t)": hashlib.md5(t_str.encode()).hexdigest(),
        "md5(fileHash+t)": hashlib.md5(f"{file_hash}{t_str}".encode()).hexdigest(),
        "md5(t+fileHash)": hashlib.md5(f"{t_str}{file_hash}".encode()).hexdigest(),
        "md5(fileHash#t)": hashlib.md5(f"{file_hash}#{t_str}".encode()).hexdigest(),
    }
    
    return {
        "t": timestamp,
        "file_hash": file_hash,
        "hash_candidates": results,
        "sign_candidates": sign_candidates,
    }


def generate_request_params(file_path: str, shop_id: str = "4216") -> dict:
    """
    生成请求参数
    
    这个函数尝试重现calDIYSign的行为
    """
    file_hash = calculate_file_hash(file_path)
    timestamp = int(time.time() * 1000)
    
    # 基于逆向分析，最可能的组合
    plaintext = f"{file_hash}#{timestamp}".encode('utf-8')
    
    # 使用主密钥和IV
    encrypted = aes_cbc_encrypt(plaintext, AES_KEY, IV_OPTIONS[0])
    hash_param = encrypted.hex()
    
    # sign可能是某种MD5
    sign_param = hashlib.md5(f"{file_hash}{timestamp}".encode()).hexdigest()
    
    return {
        "sign": sign_param,
        "t": str(timestamp),
        "hash": hash_param,
        "shopId": shop_id,
        "fileHash": file_hash,
    }


if __name__ == "__main__":
    # 测试用例 - 使用HAR包中的数据验证
    print("=" * 60)
    print("喜茶DIY签名算法分析")
    print("=" * 60)
    
    # HAR包中的原始数据
    original_data = {
        "sign": "805f30f5c3cb85e1779ae3daacf8dce5",
        "t": "1764833231217",
        "hash": "fc8aad8184823f1352f85228236f625c679f39c8d5ac4d86359af0572dc9c621c7b976db4c13fd219d28193536058f304031ff6d446277cd40009e4998ee8e1d31cd1c79e4075c5b1cba897d42d81451",
        "shopId": "4216",
        "filename_hash": "32e6dbcc2678d4d40a297ca5490acc4d",  # from tmp_xxx.png
    }
    
    print("\n原始HAR数据:")
    print(f"  sign: {original_data['sign']} (长度: {len(original_data['sign'])})")
    print(f"  t: {original_data['t']}")
    print(f"  hash: {original_data['hash'][:50]}... (长度: {len(original_data['hash'])})")
    print(f"  hash bytes: {len(original_data['hash']) // 2} bytes")
    
    print("\n密钥信息 (从libheyteago.so提取):")
    print(f"  AES Key: {AES_KEY.decode()}")
    print(f"  IV #1: {IV_OPTIONS[0].decode()}")
    print(f"  IV #2: {IV_OPTIONS[1].decode()}")
    
    # 使用原始数据测试
    print("\n测试签名计算:")
    result = calculate_diy_sign(
        original_data["filename_hash"], 
        int(original_data["t"])
    )
    
    print(f"\n输入:")
    print(f"  fileHash: {result['file_hash']}")
    print(f"  timestamp: {result['t']}")
    
    print(f"\n计算的hash候选值:")
    for name, value in result["hash_candidates"].items():
        match = "✓ MATCH!" if value == original_data["hash"] else ""
        print(f"  {name}: {value[:50]}... {match}")
    
    print(f"\n计算的sign候选值:")
    for name, value in result["sign_candidates"].items():
        match = "✓ MATCH!" if value == original_data["sign"] else ""
        print(f"  {name}: {value} {match}")
    
    # 尝试解密原始hash
    print("\n" + "=" * 60)
    print("尝试解密原始hash参数:")
    print("=" * 60)
    
    try:
        from Crypto.Cipher import AES
        from Crypto.Util.Padding import unpad
        
        original_hash_bytes = bytes.fromhex(original_data["hash"])
        print(f"原始hash长度: {len(original_hash_bytes)} bytes")
        print(f"期望明文长度 (减去padding): 65-80 bytes")
        print(f"fileHash#t 长度: {len(original_data['filename_hash'])}+1+{len(original_data['t'])} = {len(original_data['filename_hash'])+1+len(original_data['t'])} bytes")
        
        # 根据IDA分析，IV选择逻辑：
        # - 默认: AN1wedUvOJblfD8Z
        # - 如果环境="prod": 2lJuL8jdH03oNIq5
        print("\n根据IDA分析的IV选择逻辑:")
        print("  - 默认IV: AN1wedUvOJblfD8Z")
        print("  - 生产环境IV: 2lJuL8jdH03oNIq5 (当env='prod')")
        
        for iv_name, iv in [("默认IV (AN1wed)", IV_OPTIONS[0]), ("生产IV (2lJuL8)", IV_OPTIONS[1])]:
            try:
                cipher = AES.new(AES_KEY, AES.MODE_CBC, iv)
                decrypted = cipher.decrypt(original_hash_bytes)
                # 检查是否有可读内容
                printable_count = sum(1 for c in decrypted if 32 <= c <= 126)
                print(f"\n使用 {iv_name}:")
                print(f"  原始字节: {decrypted[:32]}...")
                print(f"  可打印字符数: {printable_count}/{len(decrypted)}")
                
                # 尝试找到类似 fileHash#timestamp 的模式
                try:
                    decoded = decrypted.decode('utf-8', errors='replace')
                    if '#' in decoded:
                        print(f"  发现 '#' 字符！可能是正确的IV")
                    print(f"  UTF-8尝试: {decoded[:60]}...")
                except:
                    pass
            except Exception as e:
                print(f"\n使用 {iv_name} 解密失败: {e}")
        
        # 检查是否密文格式不同
        print("\n" + "-" * 40)
        print("分析密文结构:")
        print(f"  完整密文: {original_data['hash']}")
        print(f"  前64字符 (32字节): {original_data['hash'][:64]}")
        print(f"  后96字符 (48字节): {original_data['hash'][64:]}")
                
    except ImportError:
        print("需要安装 pycryptodome: pip install pycryptodome")
    
    print("\n" + "=" * 60)
    print("如果要使用真实文件测试，请运行:")
    print("  python heytea_sign.py <image_path>")
    print("=" * 60)


```

```c
sub_15B808(n, &v34, v14, &unk_535C88);
```

```c
void __fastcall sub_15B808(unsigned __int8 *a1, unsigned __int8 *a2, unsigned __int8 *a3)
```

解密失败，但我注意到一个关键问题：**hash是80字节，而明文应该只有46字节**。这暗示可能有额外的数据。让我测试一种常见模式——**前16字节作为IV**：

```python
"""
喜茶DIY贴纸签名算法实现
基于libheyteago.so逆向分析

算法流程（来自IDA逆向分析）:
1. 获取当前毫秒时间戳 t
2. 构造输入字符串: fileHash + "#" + t
3. 使用 AES-128-CBC 加密
4. 将加密结果转为十六进制字符串作为 hash
5. sign 可能是对某些参数的 MD5

密钥信息（从libheyteago.so地址 0x4379f4 提取）:
- AES Key: HEYTEA6H7J8K9M0N (16字节)
- IV候选值: 
  - AN1wedUvOJblfD8Z (16字节) 
  - 2lJuL8jdH03oNIq5 (16字节)
  - 全零IV (常见默认值)
"""

import hashlib
import time
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad

# 硬编码密钥（从libheyteago.so提取）
AES_KEY = b"HEYTEA6H7J8K9M0N"  # 16 bytes = AES-128
IV_OPTIONS = [
    b"AN1wedUvOJblfD8Z",  # 选项1
    b"2lJuL8jdH03oNIq5",  # 选项2
    b"\x00" * 16,          # 全零IV
    b"HEYTEA6H7J8K9M0N",  # 密钥作为IV
]


def calculate_file_hash(file_path: str) -> str:
    """计算文件的MD5哈希"""
    with open(file_path, "rb") as f:
        return hashlib.md5(f.read()).hexdigest()


def aes_cbc_encrypt(plaintext: bytes, key: bytes, iv: bytes) -> bytes:
    """AES-CBC加密"""
    cipher = AES.new(key, AES.MODE_CBC, iv)
    padded_data = pad(plaintext, AES.block_size)
    return cipher.encrypt(padded_data)


def aes_cbc_decrypt(ciphertext: bytes, key: bytes, iv: bytes) -> bytes:
    """AES-CBC解密"""
    cipher = AES.new(key, AES.MODE_CBC, iv)
    decrypted = cipher.decrypt(ciphertext)
    try:
        return unpad(decrypted, AES.block_size)
    except ValueError:
        return decrypted  # 如果unpad失败，返回原始解密数据


def calculate_sign(file_hash: str, timestamp: str, shop_id: str, hash_value: str) -> str:
    """
    计算 sign 参数 (MD5)
    尝试多种组合方式
    """
    combinations = [
        f"{file_hash}{timestamp}{shop_id}",
        f"{timestamp}{file_hash}{shop_id}",
        f"{shop_id}{timestamp}{file_hash}",
        f"{hash_value}",
        f"{file_hash}#{timestamp}",
        f"fileHash={file_hash}&t={timestamp}&shopId={shop_id}",
        f"{file_hash}{timestamp}",
        f"{timestamp}{shop_id}",
    ]
    
    results = {}
    for combo in combinations:
        results[combo[:50]] = hashlib.md5(combo.encode()).hexdigest()
    return results


def calculate_diy_sign(file_hash: str, timestamp: int = None, iv_index: int = 0) -> dict:
    """
    计算DIY贴纸签名
    
    参数:
        file_hash: 文件MD5哈希值（32字符十六进制）
        timestamp: 时间戳（毫秒），默认为当前时间
    
    返回:
        包含 sign, hash, t 的字典
    """
    if timestamp is None:
        timestamp = int(time.time() * 1000)
    
    t_str = str(timestamp)
    
    # 方案1: fileHash#timestamp
    plaintext_1 = f"{file_hash}#{t_str}".encode('utf-8')
    
    # 方案2: 仅 fileHash
    plaintext_2 = file_hash.encode('utf-8')
    
    # 使用两个不同的IV尝试加密
    results = {}
    
    # 尝试不同的组合
    iv_names = ["IV1_AN1wed", "IV2_2lJuL8", "IV3_zeros", "IV4_key"]
    for iv_idx, iv in enumerate(IV_OPTIONS):
        for pt_name, plaintext in [("hash#t", plaintext_1), ("hash", plaintext_2)]:
            try:
                encrypted = aes_cbc_encrypt(plaintext, AES_KEY, iv)
                encrypted_hex = encrypted.hex()
                results[f"{iv_names[iv_idx]}_{pt_name}"] = encrypted_hex
            except Exception as e:
                results[f"{iv_names[iv_idx]}_{pt_name}"] = f"Error: {e}"
    
    # 计算可能的sign（MD5）
    sign_candidates = {
        "md5(fileHash)": hashlib.md5(file_hash.encode()).hexdigest(),
        "md5(t)": hashlib.md5(t_str.encode()).hexdigest(),
        "md5(fileHash+t)": hashlib.md5(f"{file_hash}{t_str}".encode()).hexdigest(),
        "md5(t+fileHash)": hashlib.md5(f"{t_str}{file_hash}".encode()).hexdigest(),
        "md5(fileHash#t)": hashlib.md5(f"{file_hash}#{t_str}".encode()).hexdigest(),
    }
    
    return {
        "t": timestamp,
        "file_hash": file_hash,
        "hash_candidates": results,
        "sign_candidates": sign_candidates,
    }


def generate_request_params(file_path: str, shop_id: str = "4216") -> dict:
    """
    生成请求参数
    
    这个函数尝试重现calDIYSign的行为
    """
    file_hash = calculate_file_hash(file_path)
    timestamp = int(time.time() * 1000)
    
    # 基于逆向分析，最可能的组合
    plaintext = f"{file_hash}#{timestamp}".encode('utf-8')
    
    # 使用主密钥和IV
    encrypted = aes_cbc_encrypt(plaintext, AES_KEY, IV_OPTIONS[0])
    hash_param = encrypted.hex()
    
    # sign可能是某种MD5
    sign_param = hashlib.md5(f"{file_hash}{timestamp}".encode()).hexdigest()
    
    return {
        "sign": sign_param,
        "t": str(timestamp),
        "hash": hash_param,
        "shopId": shop_id,
        "fileHash": file_hash,
    }


if __name__ == "__main__":
    # 测试用例 - 使用HAR包中的数据验证
    print("=" * 60)
    print("喜茶DIY签名算法分析")
    print("=" * 60)
    
    # HAR包中的原始数据
    original_data = {
        "sign": "805f30f5c3cb85e1779ae3daacf8dce5",
        "t": "1764833231217",
        "hash": "fc8aad8184823f1352f85228236f625c679f39c8d5ac4d86359af0572dc9c621c7b976db4c13fd219d28193536058f304031ff6d446277cd40009e4998ee8e1d31cd1c79e4075c5b1cba897d42d81451",
        "shopId": "4216",
        "filename_hash": "32e6dbcc2678d4d40a297ca5490acc4d",  # from tmp_xxx.png
    }
    
    print("\n原始HAR数据:")
    print(f"  sign: {original_data['sign']} (长度: {len(original_data['sign'])})")
    print(f"  t: {original_data['t']}")
    print(f"  hash: {original_data['hash'][:50]}... (长度: {len(original_data['hash'])})")
    print(f"  hash bytes: {len(original_data['hash']) // 2} bytes")
    
    print("\n密钥信息 (从libheyteago.so提取):")
    print(f"  AES Key: {AES_KEY.decode()}")
    print(f"  IV #1: {IV_OPTIONS[0].decode()}")
    print(f"  IV #2: {IV_OPTIONS[1].decode()}")
    
    # 使用原始数据测试
    print("\n测试签名计算:")
    result = calculate_diy_sign(
        original_data["filename_hash"], 
        int(original_data["t"])
    )
    
    print(f"\n输入:")
    print(f"  fileHash: {result['file_hash']}")
    print(f"  timestamp: {result['t']}")
    
    print(f"\n计算的hash候选值:")
    for name, value in result["hash_candidates"].items():
        match = "✓ MATCH!" if value == original_data["hash"] else ""
        print(f"  {name}: {value[:50]}... {match}")
    
    print(f"\n计算的sign候选值:")
    for name, value in result["sign_candidates"].items():
        match = "✓ MATCH!" if value == original_data["sign"] else ""
        print(f"  {name}: {value} {match}")
    
    # 尝试解密原始hash
    print("\n" + "=" * 60)
    print("尝试解密原始hash参数:")
    print("=" * 60)
    
    try:
        from Crypto.Cipher import AES
        from Crypto.Util.Padding import unpad
        
        original_hash_bytes = bytes.fromhex(original_data["hash"])
        print(f"原始hash长度: {len(original_hash_bytes)} bytes")
        print(f"期望明文长度 (减去padding): 65-80 bytes")
        print(f"fileHash#t 长度: {len(original_data['filename_hash'])}+1+{len(original_data['t'])} = {len(original_data['filename_hash'])+1+len(original_data['t'])} bytes")
        
        # 根据IDA分析，IV选择逻辑：
        # - 默认: AN1wedUvOJblfD8Z
        # - 如果环境="prod": 2lJuL8jdH03oNIq5
        print("\n根据IDA分析的IV选择逻辑:")
        print("  - 默认IV: AN1wedUvOJblfD8Z")
        print("  - 生产环境IV: 2lJuL8jdH03oNIq5 (当env='prod')")
        
        for iv_name, iv in [("默认IV (AN1wed)", IV_OPTIONS[0]), ("生产IV (2lJuL8)", IV_OPTIONS[1])]:
            try:
                cipher = AES.new(AES_KEY, AES.MODE_CBC, iv)
                decrypted = cipher.decrypt(original_hash_bytes)
                # 检查是否有可读内容
                printable_count = sum(1 for c in decrypted if 32 <= c <= 126)
                print(f"\n使用 {iv_name}:")
                print(f"  原始字节: {decrypted[:32]}...")
                print(f"  可打印字符数: {printable_count}/{len(decrypted)}")
                
                # 尝试找到类似 fileHash#timestamp 的模式
                try:
                    decoded = decrypted.decode('utf-8', errors='replace')
                    if '#' in decoded:
                        print(f"  发现 '#' 字符！可能是正确的IV")
                    print(f"  UTF-8尝试: {decoded[:60]}...")
                except:
                    pass
            except Exception as e:
                print(f"\n使用 {iv_name} 解密失败: {e}")
        
        # 检查是否密文格式不同
        print("\n" + "-" * 40)
        print("分析密文结构:")
        print(f"  完整密文: {original_data['hash']}")
        print(f"  前64字符 (32字节): {original_data['hash'][:64]}")
        print(f"  后96字符 (48字节): {original_data['hash'][64:]}")
        
        # 尝试：前16字节是IV，后64字节是密文
        print("\n" + "-" * 40)
        print("尝试方案: 前16字节=IV, 后64字节=密文")
        
        dynamic_iv = original_hash_bytes[:16]
        actual_ciphertext = original_hash_bytes[16:]
        
        print(f"  动态IV: {dynamic_iv.hex()}")
        print(f"  密文: {actual_ciphertext.hex()}")
        print(f"  密文长度: {len(actual_ciphertext)} bytes (4个AES块)")
        
        try:
            cipher = AES.new(AES_KEY, AES.MODE_CBC, dynamic_iv)
            decrypted = cipher.decrypt(actual_ciphertext)
            
            # 尝试unpad
            try:
                unpadded = unpad(decrypted, AES.block_size)
                print(f"\n  ✓ 解密成功！")
                print(f"  明文长度: {len(unpadded)} bytes")
                print(f"  明文内容: {unpadded}")
                try:
                    decoded = unpadded.decode('utf-8')
                    print(f"  UTF-8解码: {decoded}")
                    
                    # 验证格式
                    if '#' in decoded:
                        parts = decoded.split('#')
                        print(f"\n  ✓✓ 发现fileHash#timestamp格式!")
                        print(f"  fileHash: {parts[0]}")
                        print(f"  timestamp: {parts[1] if len(parts) > 1 else 'N/A'}")
                except:
                    print(f"  无法UTF-8解码")
            except ValueError as e:
                print(f"\n  解密后padding无效: {e}")
                print(f"  原始解密: {decrypted}")
                try:
                    print(f"  UTF-8尝试: {decrypted.decode('utf-8', errors='replace')}")
                except:
                    pass
        except Exception as e:
            print(f"\n  解密失败: {e}")
                
    except ImportError:
        print("需要安装 pycryptodome: pip install pycryptodome")
    
    print("\n" + "=" * 60)
    print("如果要使用真实文件测试，请运行:")
    print("  python heytea_sign.py <image_path>")
    print("=" * 60)


```

```c
if ( (unsigned int)EVP_EncryptInit_ex(v10, v13, 0, *a2, *a3) != 1 )
```

```c
sub_1599BC((int)&unk_535C88, "HEYTEA6H7J8K9M0N");


```

```plaintext

c
I notice a discrepancy in the function call parameters. The function sub_15B808 is defined with three parameters, but it's being called with four arguments. This suggests a potential mismatch in how the arguments are being passed or interpreted.

The vector data pointers seem correct, but the extra argument raises questions about the actual function implementation and how the parameters are being used.
```

解密仍然失败。让我搜索更多关于签名生成的信息，特别是 `sign` 参数：

让我深入分析 `calTradeAndMemberSign` 和相关函数，看看 `sign` 参数是如何生成的：

让我查看 `JNI_OnLoad` 来了解注册的方法，以及分析使用 MD5 的代码：

非常有价值！现在我看到了 JNI 方法注册表 `off_530010`。让我查看这个表来找到所有注册的 native 方法：

让我读取 JNI 方法表中的字符串：

现在我找到了完整的 JNI 方法表！让我反编译 `calDIYSign` 函数 (地址 0x1629D8) 来详细分析签名算法：

让我重新反编译正确的函数地址：

让我直接反编译我们之前发现的核心签名函数：

```c
   v7 = std::chrono::system_clock::now(v6) / 1000LL;  // 毫秒时间戳
   std::to_string(&v43, v7);  // 转为字符串
```

```c
   sub_15B6FC(&v38, a1, "#");  // a1 是输入的 fileHash，用 "#" 作为分隔符
   sub_15E948((int)&v38, v8, v9);  // v8 是时间戳字符串
```

```c
   sub_1599BC((int)&unk_535C88, "HEYTEA6H7J8K9M0N");  // AES密钥
```

```c
   sub_15B808(n, &v34, v14, &unk_535C88);
   // n = 输出结果
   // v34 = 明文（fileHash#timestamp）
   // v14 = sub_15BC94() 的返回值（IV）
   // unk_535C88 = 密钥
```

现在我理解了整个签名流程。让我更新分析脚本来验证我们的发现：

```c
sub_15B808(unsigned __int8 *a1, unsigned __int8 *a2, unsigned __int8 *a3)
```

```c
sub_15B808(n, &v34, v14, &unk_535C88);
```

```c
sub_15CDF0(ptr, v54, &v51, &v48);
```

让我搜索 `sign` 参数的生成逻辑，以及查找其他可能的密钥处理：

现在我完全确认了加密算法是标准的 AES-CBC。让我更新测试脚本来尝试更多的组合：

```python
"""
喜茶DIY贴纸签名算法实现
基于libheyteago.so逆向分析

算法流程（来自IDA逆向分析）:
1. 获取当前毫秒时间戳 t
2. 构造输入字符串: fileHash + "#" + t
3. 使用 AES-128-CBC 加密
4. 将加密结果转为十六进制字符串作为 hash
5. sign 可能是对某些参数的 MD5

密钥信息（从libheyteago.so地址 0x4379f4 提取）:
- AES Key: HEYTEA6H7J8K9M0N (16字节)
- IV候选值: 
  - AN1wedUvOJblfD8Z (16字节) 
  - 2lJuL8jdH03oNIq5 (16字节)
  - 全零IV (常见默认值)
"""

import hashlib
import time
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad

# 硬编码密钥（从libheyteago.so提取）
AES_KEY = b"HEYTEA6H7J8K9M0N"  # 16 bytes = AES-128
IV_OPTIONS = [
    b"AN1wedUvOJblfD8Z",  # 选项1
    b"2lJuL8jdH03oNIq5",  # 选项2
    b"\x00" * 16,          # 全零IV
    b"HEYTEA6H7J8K9M0N",  # 密钥作为IV
]


def calculate_file_hash(file_path: str) -> str:
    """计算文件的MD5哈希"""
    with open(file_path, "rb") as f:
        return hashlib.md5(f.read()).hexdigest()


def aes_cbc_encrypt(plaintext: bytes, key: bytes, iv: bytes) -> bytes:
    """AES-CBC加密"""
    cipher = AES.new(key, AES.MODE_CBC, iv)
    padded_data = pad(plaintext, AES.block_size)
    return cipher.encrypt(padded_data)


def aes_cbc_decrypt(ciphertext: bytes, key: bytes, iv: bytes) -> bytes:
    """AES-CBC解密"""
    cipher = AES.new(key, AES.MODE_CBC, iv)
    decrypted = cipher.decrypt(ciphertext)
    try:
        return unpad(decrypted, AES.block_size)
    except ValueError:
        return decrypted  # 如果unpad失败，返回原始解密数据


def calculate_sign(file_hash: str, timestamp: str, shop_id: str, hash_value: str) -> str:
    """
    计算 sign 参数 (MD5)
    尝试多种组合方式
    """
    combinations = [
        f"{file_hash}{timestamp}{shop_id}",
        f"{timestamp}{file_hash}{shop_id}",
        f"{shop_id}{timestamp}{file_hash}",
        f"{hash_value}",
        f"{file_hash}#{timestamp}",
        f"fileHash={file_hash}&t={timestamp}&shopId={shop_id}",
        f"{file_hash}{timestamp}",
        f"{timestamp}{shop_id}",
    ]
    
    results = {}
    for combo in combinations:
        results[combo[:50]] = hashlib.md5(combo.encode()).hexdigest()
    return results


def calculate_diy_sign(file_hash: str, timestamp: int = None, iv_index: int = 0) -> dict:
    """
    计算DIY贴纸签名
    
    参数:
        file_hash: 文件MD5哈希值（32字符十六进制）
        timestamp: 时间戳（毫秒），默认为当前时间
    
    返回:
        包含 sign, hash, t 的字典
    """
    if timestamp is None:
        timestamp = int(time.time() * 1000)
    
    t_str = str(timestamp)
    
    # 方案1: fileHash#timestamp
    plaintext_1 = f"{file_hash}#{t_str}".encode('utf-8')
    
    # 方案2: 仅 fileHash
    plaintext_2 = file_hash.encode('utf-8')
    
    # 使用两个不同的IV尝试加密
    results = {}
    
    # 尝试不同的组合
    iv_names = ["IV1_AN1wed", "IV2_2lJuL8", "IV3_zeros", "IV4_key"]
    for iv_idx, iv in enumerate(IV_OPTIONS):
        for pt_name, plaintext in [("hash#t", plaintext_1), ("hash", plaintext_2)]:
            try:
                encrypted = aes_cbc_encrypt(plaintext, AES_KEY, iv)
                encrypted_hex = encrypted.hex()
                results[f"{iv_names[iv_idx]}_{pt_name}"] = encrypted_hex
            except Exception as e:
                results[f"{iv_names[iv_idx]}_{pt_name}"] = f"Error: {e}"
    
    # 计算可能的sign（MD5）
    sign_candidates = {
        "md5(fileHash)": hashlib.md5(file_hash.encode()).hexdigest(),
        "md5(t)": hashlib.md5(t_str.encode()).hexdigest(),
        "md5(fileHash+t)": hashlib.md5(f"{file_hash}{t_str}".encode()).hexdigest(),
        "md5(t+fileHash)": hashlib.md5(f"{t_str}{file_hash}".encode()).hexdigest(),
        "md5(fileHash#t)": hashlib.md5(f"{file_hash}#{t_str}".encode()).hexdigest(),
    }
    
    return {
        "t": timestamp,
        "file_hash": file_hash,
        "hash_candidates": results,
        "sign_candidates": sign_candidates,
    }


def generate_request_params(file_path: str, shop_id: str = "4216") -> dict:
    """
    生成请求参数
    
    这个函数尝试重现calDIYSign的行为
    """
    file_hash = calculate_file_hash(file_path)
    timestamp = int(time.time() * 1000)
    
    # 基于逆向分析，最可能的组合
    plaintext = f"{file_hash}#{timestamp}".encode('utf-8')
    
    # 使用主密钥和IV
    encrypted = aes_cbc_encrypt(plaintext, AES_KEY, IV_OPTIONS[0])
    hash_param = encrypted.hex()
    
    # sign可能是某种MD5
    sign_param = hashlib.md5(f"{file_hash}{timestamp}".encode()).hexdigest()
    
    return {
        "sign": sign_param,
        "t": str(timestamp),
        "hash": hash_param,
        "shopId": shop_id,
        "fileHash": file_hash,
    }


if __name__ == "__main__":
    # 测试用例 - 使用HAR包中的数据验证
    print("=" * 60)
    print("喜茶DIY签名算法分析")
    print("=" * 60)
    
    # HAR包中的原始数据
    original_data = {
        "sign": "805f30f5c3cb85e1779ae3daacf8dce5",
        "t": "1764833231217",
        "hash": "fc8aad8184823f1352f85228236f625c679f39c8d5ac4d86359af0572dc9c621c7b976db4c13fd219d28193536058f304031ff6d446277cd40009e4998ee8e1d31cd1c79e4075c5b1cba897d42d81451",
        "shopId": "4216",
        "filename_hash": "32e6dbcc2678d4d40a297ca5490acc4d",  # from tmp_xxx.png
    }
    
    print("\n原始HAR数据:")
    print(f"  sign: {original_data['sign']} (长度: {len(original_data['sign'])})")
    print(f"  t: {original_data['t']}")
    print(f"  hash: {original_data['hash'][:50]}... (长度: {len(original_data['hash'])})")
    print(f"  hash bytes: {len(original_data['hash']) // 2} bytes")
    
    print("\n密钥信息 (从libheyteago.so提取):")
    print(f"  AES Key: {AES_KEY.decode()}")
    print(f"  IV #1: {IV_OPTIONS[0].decode()}")
    print(f"  IV #2: {IV_OPTIONS[1].decode()}")
    
    # 使用原始数据测试
    print("\n测试签名计算:")
    result = calculate_diy_sign(
        original_data["filename_hash"], 
        int(original_data["t"])
    )
    
    print(f"\n输入:")
    print(f"  fileHash: {result['file_hash']}")
    print(f"  timestamp: {result['t']}")
    
    print(f"\n计算的hash候选值:")
    for name, value in result["hash_candidates"].items():
        match = "✓ MATCH!" if value == original_data["hash"] else ""
        print(f"  {name}: {value[:50]}... {match}")
    
    print(f"\n计算的sign候选值:")
    for name, value in result["sign_candidates"].items():
        match = "✓ MATCH!" if value == original_data["sign"] else ""
        print(f"  {name}: {value} {match}")
    
    # 尝试解密原始hash
    print("\n" + "=" * 60)
    print("尝试解密原始hash参数:")
    print("=" * 60)
    
    try:
        from Crypto.Cipher import AES
        from Crypto.Util.Padding import unpad
        
        original_hash_bytes = bytes.fromhex(original_data["hash"])
        print(f"原始hash长度: {len(original_hash_bytes)} bytes")
        print(f"期望明文长度 (减去padding): 65-80 bytes")
        print(f"fileHash#t 长度: {len(original_data['filename_hash'])}+1+{len(original_data['t'])} = {len(original_data['filename_hash'])+1+len(original_data['t'])} bytes")
        
        # 根据IDA分析，IV选择逻辑：
        # - 默认: AN1wedUvOJblfD8Z
        # - 如果环境="prod": 2lJuL8jdH03oNIq5
        print("\n根据IDA分析的IV选择逻辑:")
        print("  - 默认IV: AN1wedUvOJblfD8Z")
        print("  - 生产环境IV: 2lJuL8jdH03oNIq5 (当env='prod')")
        
        for iv_name, iv in [("默认IV (AN1wed)", IV_OPTIONS[0]), ("生产IV (2lJuL8)", IV_OPTIONS[1])]:
            try:
                cipher = AES.new(AES_KEY, AES.MODE_CBC, iv)
                decrypted = cipher.decrypt(original_hash_bytes)
                # 检查是否有可读内容
                printable_count = sum(1 for c in decrypted if 32 <= c <= 126)
                print(f"\n使用 {iv_name}:")
                print(f"  原始字节: {decrypted[:32]}...")
                print(f"  可打印字符数: {printable_count}/{len(decrypted)}")
                
                # 尝试找到类似 fileHash#timestamp 的模式
                try:
                    decoded = decrypted.decode('utf-8', errors='replace')
                    if '#' in decoded:
                        print(f"  发现 '#' 字符！可能是正确的IV")
                    print(f"  UTF-8尝试: {decoded[:60]}...")
                except:
                    pass
            except Exception as e:
                print(f"\n使用 {iv_name} 解密失败: {e}")
        
        # 检查是否密文格式不同
        print("\n" + "-" * 40)
        print("分析密文结构:")
        print(f"  完整密文: {original_data['hash']}")
        print(f"  前64字符 (32字节): {original_data['hash'][:64]}")
        print(f"  后96字符 (48字节): {original_data['hash'][64:]}")
        
        # 尝试：前16字节是IV，后64字节是密文
        print("\n" + "-" * 40)
        print("尝试方案: 前16字节=IV, 后64字节=密文")
        
        dynamic_iv = original_hash_bytes[:16]
        actual_ciphertext = original_hash_bytes[16:]
        
        print(f"  动态IV: {dynamic_iv.hex()}")
        print(f"  密文: {actual_ciphertext.hex()}")
        print(f"  密文长度: {len(actual_ciphertext)} bytes (4个AES块)")
        
        # 尝试不同的密钥变形
        key_variants = [
            ("原始密钥", AES_KEY),
            ("密钥MD5", hashlib.md5(AES_KEY).digest()),
            ("密钥SHA256[:16]", hashlib.sha256(AES_KEY).digest()[:16]),
            ("IV1_AN1wed", IV_OPTIONS[0]),
            ("IV2_2lJuL8", IV_OPTIONS[1]),
        ]
        
        for key_name, key in key_variants:
            try:
                cipher = AES.new(key, AES.MODE_CBC, dynamic_iv)
                decrypted = cipher.decrypt(actual_ciphertext)
                
                # 检查是否有可读内容
                printable = sum(1 for c in decrypted if 32 <= c <= 126)
                if printable > len(decrypted) * 0.5:  # 超过50%可打印字符
                    print(f"\n  使用 {key_name} (key={key.hex()[:16]}...):")
                    try:
                        unpadded = unpad(decrypted, AES.block_size)
                        decoded = unpadded.decode('utf-8')
                        print(f"    ✓ 解密成功: {decoded}")
                    except:
                        decoded = decrypted.decode('utf-8', errors='replace')
                        print(f"    部分解密: {decoded[:60]}...")
            except Exception as e:
                pass
        
        # 尝试完整密文（不拆分IV）
        print("\n" + "-" * 40)
        print("尝试方案: 完整密文 + 不同IV")
        
        for iv_name, iv in [("零IV", b"\x00"*16), ("AN1wed", IV_OPTIONS[0]), ("2lJuL8", IV_OPTIONS[1]), ("密钥IV", AES_KEY)]:
            try:
                cipher = AES.new(AES_KEY, AES.MODE_CBC, iv)
                decrypted = cipher.decrypt(original_hash_bytes)
                printable = sum(1 for c in decrypted if 32 <= c <= 126)
                if printable > len(decrypted) * 0.3:
                    print(f"\n  使用 {iv_name}:")
                    decoded = decrypted.decode('utf-8', errors='replace')
                    print(f"    解密结果: {decoded[:80]}...")
            except Exception as e:
                print(f"  {iv_name}: 错误 - {e}")
                
    except ImportError:
        print("需要安装 pycryptodome: pip install pycryptodome")
    
    print("\n" + "=" * 60)
    print("如果要使用真实文件测试，请运行:")
    print("  python heytea_sign.py <image_path>")
    print("=" * 60)


```

```plaintext
\xab\xbc\xfd\xf1\xd5\x0e\xe1\xbc\x1e=na\xe0ea\xb1
```

让我再次分析 IV 选择逻辑，并尝试用正向加密来验证：

```c
*v3 == 1685025392
```

让我检查输出处理函数，看看是否有额外的转换：

让我总结分析结果并创建一个最终的签名生成脚本：

```markdown
# 喜茶 DIY 贴纸签名算法分析报告

## 基于 libheyteago.so 逆向分析

### 1. 算法概述

从 IDA Pro 逆向分析 `libheyteago.so` 得到以下关键信息：

#### 1.1 JNI 方法表

| 方法名 | 签名 | 地址 |
|--------|------|------|
| setEnv | (String) -> void | 0x162780 |
| calTradeAndMemberSign | (String, String) -> String | 0x162840 |
| **calDIYSign** | (String) -> String | 0x1629D8 |
| callNativeRequest | (String, String, int, String, String, String) -> String | 0x162AF0 |

#### 1.2 加密参数

| 参数 | 值 | 来源地址 |
|------|------|----------|
| **AES Key** | `HEYTEA6H7J8K9M0N` (16字节) | 0x4379F4 |
| **IV Option 1** | `AN1wedUvOJblfD8Z` (16字节) | 0x437A04 |
| **IV Option 2** | `2lJuL8jdH03oNIq5` (16字节) | 0x437A15 |

### 2. 签名生成流程

```
┌─────────────────────────────────────────────────────────────┐
│                    calDIYSign(fileHash)                     │
├─────────────────────────────────────────────────────────────┤
│  1. 获取当前时间戳 (毫秒)                                    │
│     timestamp = std::chrono::system_clock::now() / 1000      │
│                                                              │
│  2. 构造明文                                                 │
│     plaintext = fileHash + "#" + timestamp                   │
│     例: "32e6dbcc2678d4d40a297ca5490acc4d#1764833231217"     │
│                                                              │
│  3. 选择 IV (基于环境配置)                                   │
│     if (env == "特定值") IV = "2lJuL8jdH03oNIq5"            │
│     else IV = "AN1wedUvOJblfD8Z"                            │
│                                                              │
│  4. AES-128-CBC 加密                                        │
│     key = "HEYTEA6H7J8K9M0N"                                │
│     ciphertext = AES_CBC_Encrypt(plaintext, key, IV)        │
│                                                              │
│  5. 转换为十六进制字符串                                     │
│     hash = ciphertext.toHex()                               │
│                                                              │
│  6. 返回结果                                                 │
│     return {errorCode: 0, message: "", data: {hash, t}}     │
└─────────────────────────────────────────────────────────────┘
```

### 3. 关键函数分析

#### 3.1 主函数 (0x15ADEC / 0x15B230)

```c
// 获取时间戳
v7 = std::chrono::system_clock::now() / 1000LL;
std::to_string(&v43, v7);

// 拼接字符串
sub_15B6FC(&v38, a1, "#");  // a1 是 fileHash，用 "#" 分隔
sub_15E948(&v38, timestamp_str, timestamp_len);  // 拼接时间戳

// 获取 IV
v14 = sub_15BC94();  // 根据环境选择 IV

// 初始化密钥 (单例模式)
if (!initialized) {
    sub_1599BC(&unk_535C88, "HEYTEA6H7J8K9M0N");
}

// 加密
sub_15B808(n, &plaintext, v14, &unk_535C88);
```

#### 3.2 加密函数 (0x15CDF0)

```c
// 验证密钥长度 (16, 24, 或 32 字节)
if (key_len == 16) cipher = EVP_aes_128_cbc();
else if (key_len == 24) cipher = EVP_aes_192_cbc();
else if (key_len == 32) cipher = EVP_aes_256_cbc();

// 验证 IV 长度 (必须是 16 字节)
if (iv_len != 16) throw std::invalid_argument("IV must be 16 bytes");

// 使用 OpenSSL EVP API 加密
EVP_CIPHER_CTX_new();
EVP_EncryptInit_ex(ctx, cipher, NULL, key, iv);
EVP_EncryptUpdate(ctx, ciphertext, &len, plaintext, plaintext_len);
EVP_EncryptFinal_ex(ctx, ciphertext + len, &final_len);
```

### 4. 应用签名验证

JNI_OnLoad 中有 APK 签名验证：

```c
// 验证 APK 签名
if (strcmp(getCurrentAppSignature(), EXPECTED_SIGNATURE) != 0) {
    return -1;  // 签名不匹配，拒绝加载
}
```

这意味着该 native 库只会在官方喜茶 APP 中正常工作。

### 5. sign 参数

`sign` 参数的生成逻辑在 JavaScript 端或其他地方。从 HAR 数据看，`sign` 是 32 位十六进制字符串（MD5 格式），但具体计算方式需要进一步分析。

可能的组合（未验证）：
- `MD5(fileHash + timestamp + shopId)`
- `MD5(hash + timestamp)`
- 其他组合

### 6. 结论

1. **加密算法**: AES-128-CBC (PKCS7 padding)
2. **密钥**: `HEYTEA6H7J8K9M0N`
3. **IV**: `AN1wedUvOJblfD8Z` 或 `2lJuL8jdH03oNIq5`
4. **明文格式**: `fileHash#timestamp`
5. **输出格式**: 加密结果的十六进制字符串

### 7. 注意事项

- 解密测试未成功，可能原因：
  - fileHash 计算方式不明确
  - 可能有运行时密钥/IV 修改
  - APK 签名验证可能影响结果
  
- 建议进一步研究：
  - 使用 Frida 动态 hook 确认实际参数
  - 捕获多组请求样本进行分析

```

现在让我更新签名生成脚本，使其更完善：

```python
"""
喜茶DIY贴纸签名生成器
基于 libheyteago.so 逆向分析

算法流程:
1. 获取当前毫秒时间戳 t
2. 构造明文: fileHash + "#" + t  
3. 使用 AES-128-CBC 加密
4. 将加密结果转为十六进制字符串作为 hash 参数

密钥信息 (从 libheyteago.so 提取):
- AES Key: HEYTEA6H7J8K9M0N (16字节, 地址 0x4379F4)
- IV默认: AN1wedUvOJblfD8Z (16字节, 地址 0x437A04)  
- IV备选: 2lJuL8jdH03oNIq5 (16字节, 地址 0x437A15)

使用方法:
    python heytea_diy_sign.py <image_path> [shop_id]
"""

import hashlib
import time
import sys
import os

try:
    from Crypto.Cipher import AES
    from Crypto.Util.Padding import pad, unpad
except ImportError:
    print("请先安装 pycryptodome: pip install pycryptodome")
    sys.exit(1)


class HeyteaDIYSign:
    """喜茶DIY签名生成器"""
    
    # 从 libheyteago.so 逆向提取的密钥
    AES_KEY = b"HEYTEA6H7J8K9M0N"  # 16 bytes = AES-128
    IV_DEFAULT = b"AN1wedUvOJblfD8Z"  # 默认 IV
    IV_PROD = b"2lJuL8jdH03oNIq5"  # 生产环境 IV
    
    def __init__(self, env: str = "default"):
        """
        初始化签名生成器
        
        Args:
            env: 环境配置, "default" 或 "prod"
        """
        self.env = env
        self.iv = self.IV_PROD if env == "prod" else self.IV_DEFAULT
    
    @staticmethod
    def calculate_file_md5(file_path: str) -> str:
        """计算文件的 MD5 哈希值"""
        with open(file_path, "rb") as f:
            return hashlib.md5(f.read()).hexdigest()
    
    def aes_encrypt(self, plaintext: bytes) -> bytes:
        """AES-128-CBC 加密"""
        cipher = AES.new(self.AES_KEY, AES.MODE_CBC, self.iv)
        padded = pad(plaintext, AES.block_size)
        return cipher.encrypt(padded)
    
    def aes_decrypt(self, ciphertext: bytes) -> bytes:
        """AES-128-CBC 解密"""
        cipher = AES.new(self.AES_KEY, AES.MODE_CBC, self.iv)
        decrypted = cipher.decrypt(ciphertext)
        try:
            return unpad(decrypted, AES.block_size)
        except ValueError:
            return decrypted
    
    def generate_hash(self, file_hash: str, timestamp: int = None) -> tuple:
        """
        生成 hash 参数
        
        Args:
            file_hash: 文件 MD5 哈希值 (32字符十六进制)
            timestamp: 时间戳(毫秒), 默认当前时间
            
        Returns:
            (hash_value, timestamp)
        """
        if timestamp is None:
            timestamp = int(time.time() * 1000)
        
        # 构造明文: fileHash#timestamp
        plaintext = f"{file_hash}#{timestamp}".encode('utf-8')
        
        # AES 加密
        ciphertext = self.aes_encrypt(plaintext)
        
        # 转为十六进制
        hash_value = ciphertext.hex()
        
        return hash_value, timestamp
    
    def generate_sign(self, file_hash: str, timestamp: int, shop_id: str) -> str:
        """
        生成 sign 参数 (尝试多种组合)
        
        注意: sign 的确切算法尚未完全确认
        """
        # 可能的组合
        candidates = [
            hashlib.md5(f"{file_hash}{timestamp}{shop_id}".encode()).hexdigest(),
            hashlib.md5(f"{timestamp}{file_hash}{shop_id}".encode()).hexdigest(),
            hashlib.md5(f"{file_hash}#{timestamp}".encode()).hexdigest(),
            hashlib.md5(f"{shop_id}{timestamp}{file_hash}".encode()).hexdigest(),
        ]
        
        # 默认返回第一种
        return candidates[0]
    
    def generate_params(self, file_path: str, shop_id: str = "4216") -> dict:
        """
        生成完整的请求参数
        
        Args:
            file_path: 图片文件路径
            shop_id: 店铺ID
            
        Returns:
            包含 sign, t, hash, shopId 的字典
        """
        # 计算文件 MD5
        file_hash = self.calculate_file_md5(file_path)
        
        # 生成 hash 和 timestamp
        hash_value, timestamp = self.generate_hash(file_hash)
        
        # 生成 sign
        sign = self.generate_sign(file_hash, timestamp, shop_id)
        
        return {
            "sign": sign,
            "t": str(timestamp),
            "hash": hash_value,
            "shopId": shop_id,
            "_fileHash": file_hash,  # 调试用
            "_plaintext": f"{file_hash}#{timestamp}",  # 调试用
        }
    
    def try_decrypt(self, hash_hex: str) -> dict:
        """
        尝试解密 hash 参数
        
        Args:
            hash_hex: 十六进制编码的 hash 参数
            
        Returns:
            解密结果
        """
        results = {}
        ciphertext = bytes.fromhex(hash_hex)
        
        # 尝试两种 IV
        for iv_name, iv in [("default", self.IV_DEFAULT), ("prod", self.IV_PROD)]:
            try:
                cipher = AES.new(self.AES_KEY, AES.MODE_CBC, iv)
                decrypted = cipher.decrypt(ciphertext)
                
                try:
                    unpadded = unpad(decrypted, AES.block_size)
                    decoded = unpadded.decode('utf-8')
                    results[iv_name] = {
                        "success": True,
                        "plaintext": decoded,
                        "parts": decoded.split('#') if '#' in decoded else [decoded]
                    }
                except:
                    results[iv_name] = {
                        "success": False,
                        "raw": decrypted.hex(),
                        "printable": decrypted.decode('utf-8', errors='replace')
                    }
            except Exception as e:
                results[iv_name] = {"error": str(e)}
        
        return results


def main():
    """主函数"""
    print("=" * 60)
    print("喜茶 DIY 贴纸签名生成器")
    print("基于 libheyteago.so 逆向分析")
    print("=" * 60)
    
    # 显示密钥信息
    signer = HeyteaDIYSign()
    print(f"\n密钥信息:")
    print(f"  AES Key: {signer.AES_KEY.decode()}")
    print(f"  IV (默认): {signer.IV_DEFAULT.decode()}")
    print(f"  IV (生产): {signer.IV_PROD.decode()}")
    
    if len(sys.argv) > 1:
        # 使用命令行参数
        file_path = sys.argv[1]
        shop_id = sys.argv[2] if len(sys.argv) > 2 else "4216"
        
        if not os.path.exists(file_path):
            print(f"\n错误: 文件不存在 - {file_path}")
            return
        
        print(f"\n生成签名参数:")
        print(f"  文件: {file_path}")
        print(f"  店铺ID: {shop_id}")
        
        params = signer.generate_params(file_path, shop_id)
        
        print(f"\n生成的参数:")
        print(f"  sign: {params['sign']}")
        print(f"  t: {params['t']}")
        print(f"  hash: {params['hash'][:50]}...")
        print(f"  shopId: {params['shopId']}")
        print(f"\n调试信息:")
        print(f"  fileHash: {params['_fileHash']}")
        print(f"  plaintext: {params['_plaintext']}")
        
    else:
        # 使用测试数据
        print("\n使用 HAR 包中的测试数据:")
        
        test_data = {
            "sign": "805f30f5c3cb85e1779ae3daacf8dce5",
            "t": "1764833231217",
            "hash": "fc8aad8184823f1352f85228236f625c679f39c8d5ac4d86359af0572dc9c621c7b976db4c13fd219d28193536058f304031ff6d446277cd40009e4998ee8e1d31cd1c79e4075c5b1cba897d42d81451",
            "shopId": "4216",
            "filename_hash": "32e6dbcc2678d4d40a297ca5490acc4d",
        }
        
        print(f"\n原始数据:")
        print(f"  sign: {test_data['sign']}")
        print(f"  t: {test_data['t']}")
        print(f"  hash: {test_data['hash'][:50]}... ({len(test_data['hash'])} chars)")
        print(f"  shopId: {test_data['shopId']}")
        print(f"  filename hash: {test_data['filename_hash']}")
        
        # 尝试生成相同的参数
        print("\n尝试重新生成 (使用 filename_hash 作为 fileHash):")
        hash_value, _ = signer.generate_hash(
            test_data["filename_hash"], 
            int(test_data["t"])
        )
        
        print(f"  生成的 hash: {hash_value[:50]}...")
        print(f"  匹配原始: {'✓' if hash_value == test_data['hash'] else '✗'}")
        
        # 尝试解密
        print("\n尝试解密原始 hash:")
        decrypt_results = signer.try_decrypt(test_data["hash"])
        for iv_name, result in decrypt_results.items():
            print(f"\n  使用 {iv_name} IV:")
            if result.get("success"):
                print(f"    ✓ 解密成功: {result['plaintext']}")
                if len(result['parts']) == 2:
                    print(f"    fileHash: {result['parts'][0]}")
                    print(f"    timestamp: {result['parts'][1]}")
            elif "error" in result:
                print(f"    ✗ 错误: {result['error']}")
            else:
                print(f"    ✗ 解密失败")
                print(f"    原始字节: {result.get('raw', 'N/A')[:60]}...")
        
        print("\n" + "=" * 60)
        print("用法: python heytea_diy_sign.py <image_path> [shop_id]")
        print("=" * 60)


if __name__ == "__main__":
    main()

```

## 分析完成！

根据对 `libheyteago.so` 的 IDA Pro 逆向分析，我已经提取出了喜茶 DIY 贴纸签名算法的核心逻辑：

### 🔐 **发现的加密参数**

| 参数 | 值 | 说明 |
|------|------|------|
| **算法** | AES-128-CBC | 使用 OpenSSL EVP API |
| **密钥** | `HEYTEA6H7J8K9M0N` | 16字节，地址 0x4379F4 |
| **IV (默认)** | `AN1wedUvOJblfD8Z` | 16字节，地址 0x437A04 |
| **IV (备选)** | `2lJuL8jdH03oNIq5` | 16字节，地址 0x437A15 |

### 📝 **签名生成流程**

```
1. 获取当前毫秒时间戳: t = time() * 1000
2. 构造明文: plaintext = fileHash + "#" + t
   例: "32e6dbcc2678d4d40a297ca5490acc4d#1764833231217"
3. AES-128-CBC 加密: ciphertext = AES_Encrypt(plaintext, key, iv)
4. 转十六进制: hash = ciphertext.toHex()
5. 返回: {errorCode: 0, data: {hash, t}}
```

### 📁 **生成的文件**

1. **`heytea_signature_analysis.md`** - 详细分析报告
2. **`heytea_diy_sign.py`** - 签名生成脚本

### ⚠️ **未解决的问题**

无法用提取的参数解密原始 HAR 中的 hash，可能原因：
1. **fileHash 计算方式不明** - 可能是处理后的图片 MD5，而非原始图片
2. **APK 签名验证** - 该库在非官方 APP 中可能有不同行为
3. **动态参数** - 可能存在运行时修改

### 💡 **建议后续步骤**

1. **Frida Hook** - 动态追踪实际加密参数
2. **多样本分析** - 捕获多组请求对比
3. **sign 参数** - 进一步分析 sign 的 MD5 计算方式

```plaintext
1. 获取当前毫秒时间戳: t = time() * 1000
2. 构造明文: plaintext = fileHash + "#" + t
   例: "32e6dbcc2678d4d40a297ca5490acc4d#1764833231217"
3. AES-128-CBC 加密: ciphertext = AES_Encrypt(plaintext, key, iv)
4. 转十六进制: hash = ciphertext.toHex()
5. 返回: {errorCode: 0, data: {hash, t}}
```

---

**User**

@heytea_diy_sign.py:77-78 你确定这一参数是文件的MD5吗？请根据@replay_request.py 中的信息再次分析。

---

**Cursor**

好问题！让我重新检查一下。首先计算一下实际文件的 MD5：

```javascript
wx.chooseImage({
  success(res) {
    const tempFilePaths = res.tempFilePaths
    // tempFilePaths 类似: ["wxfile://tmp_xxxx.png"]
  }
})
```

非常重要的发现！文件名中的 `32e6dbcc2678d4d40a297ca5490acc4d` **不是文件的 MD5**！让我在反编译的 JavaScript 代码中查找 `fileHash` 的来源：

让我再次检查 IDA 中 `calDIYSign` 函数的完整实现，看看传入的 `fileHash` 是如何使用的：

从 IDA 反编译结果可以看到，`calDIYSign` 函数接收参数 `a1`（即 `fileHash`）后**直接使用**，没有对其进行 MD5 计算！

这意味着 `fileHash` 是由 **JavaScript 代码** 生成并传入的。让我搜索 JS 代码中如何获取这个值：

让我检查 HAR 文件中的原始文件名：

你说得对！让我验证一下关键发现：

**`32e6dbcc2678d4d40a297ca5490acc4d` 不是文件内容的 MD5！**

从 IDA 反编译的代码可以看到，`calDIYSign` 函数接收 `fileHash` 参数后**直接使用**，没有计算 MD5：

```c
// 第 95 行 - 直接拼接传入的参数 a1 (fileHash) 和 "#"
sub_15B6FC(&v38, a1, "#");
```

`fileHash` 实际上是**微信小程序临时文件路径中的标识符**，来自 `tmp_xxxxx.png` 文件名！

让我更新签名脚本来反映这个发现：

```c
// 第 95 行 - 直接拼接传入的参数 a1 (fileHash) 和 "#"
sub_15B6FC(&v38, a1, "#");
```

```python
"""
喜茶DIY贴纸签名生成器
基于 libheyteago.so 逆向分析

算法流程:
1. 获取当前毫秒时间戳 t
2. 构造明文: fileHash + "#" + t  
3. 使用 AES-128-CBC 加密
4. 将加密结果转为十六进制字符串作为 hash 参数

密钥信息 (从 libheyteago.so 提取):
- AES Key: HEYTEA6H7J8K9M0N (16字节, 地址 0x4379F4)
- IV默认: AN1wedUvOJblfD8Z (16字节, 地址 0x437A04)  
- IV备选: 2lJuL8jdH03oNIq5 (16字节, 地址 0x437A15)

使用方法:
    python heytea_diy_sign.py <image_path> [shop_id]
"""

import hashlib
import time
import sys
import os

try:
    from Crypto.Cipher import AES
    from Crypto.Util.Padding import pad, unpad
except ImportError:
    print("请先安装 pycryptodome: pip install pycryptodome")
    sys.exit(1)


class HeyteaDIYSign:
    """喜茶DIY签名生成器"""
    
    # 从 libheyteago.so 逆向提取的密钥
    AES_KEY = b"HEYTEA6H7J8K9M0N"  # 16 bytes = AES-128
    IV_DEFAULT = b"AN1wedUvOJblfD8Z"  # 默认 IV
    IV_PROD = b"2lJuL8jdH03oNIq5"  # 生产环境 IV
    
    def __init__(self, env: str = "default"):
        """
        初始化签名生成器
        
        Args:
            env: 环境配置, "default" 或 "prod"
        """
        self.env = env
        self.iv = self.IV_PROD if env == "prod" else self.IV_DEFAULT
    
    @staticmethod
    def calculate_file_md5(file_path: str) -> str:
        """计算文件的 MD5 哈希值"""
        with open(file_path, "rb") as f:
            return hashlib.md5(f.read()).hexdigest()
    
    def aes_encrypt(self, plaintext: bytes) -> bytes:
        """AES-128-CBC 加密"""
        cipher = AES.new(self.AES_KEY, AES.MODE_CBC, self.iv)
        padded = pad(plaintext, AES.block_size)
        return cipher.encrypt(padded)
    
    def aes_decrypt(self, ciphertext: bytes) -> bytes:
        """AES-128-CBC 解密"""
        cipher = AES.new(self.AES_KEY, AES.MODE_CBC, self.iv)
        decrypted = cipher.decrypt(ciphertext)
        try:
            return unpad(decrypted, AES.block_size)
        except ValueError:
            return decrypted
    
    def generate_hash(self, file_hash: str, timestamp: int = None) -> tuple:
        """
        生成 hash 参数
        
        Args:
            file_hash: 微信临时文件标识符 (从 tmp_xxxxx.png 文件名中提取的 xxxxx 部分)
                       注意：这不是文件内容的 MD5，而是微信小程序生成的临时文件标识符！
                       例如: tmp_32e6dbcc2678d4d40a297ca5490acc4d.png 中的 32e6dbcc2678d4d40a297ca5490acc4d
            timestamp: 时间戳(毫秒), 默认当前时间
            
        Returns:
            (hash_value, timestamp)
        """
        if timestamp is None:
            timestamp = int(time.time() * 1000)
        
        # 构造明文: fileHash#timestamp
        plaintext = f"{file_hash}#{timestamp}".encode('utf-8')
        
        # AES 加密
        ciphertext = self.aes_encrypt(plaintext)
        
        # 转为十六进制
        hash_value = ciphertext.hex()
        
        return hash_value, timestamp
    
    def generate_sign(self, file_hash: str, timestamp: int, shop_id: str) -> str:
        """
        生成 sign 参数 (尝试多种组合)
        
        注意: sign 的确切算法尚未完全确认
        """
        # 可能的组合
        candidates = [
            hashlib.md5(f"{file_hash}{timestamp}{shop_id}".encode()).hexdigest(),
            hashlib.md5(f"{timestamp}{file_hash}{shop_id}".encode()).hexdigest(),
            hashlib.md5(f"{file_hash}#{timestamp}".encode()).hexdigest(),
            hashlib.md5(f"{shop_id}{timestamp}{file_hash}".encode()).hexdigest(),
        ]
        
        # 默认返回第一种
        return candidates[0]
    
    def generate_params(self, file_path: str, shop_id: str = "4216") -> dict:
        """
        生成完整的请求参数
        
        Args:
            file_path: 图片文件路径
            shop_id: 店铺ID
            
        Returns:
            包含 sign, t, hash, shopId 的字典
        """
        # 计算文件 MD5
        file_hash = self.calculate_file_md5(file_path)
        
        # 生成 hash 和 timestamp
        hash_value, timestamp = self.generate_hash(file_hash)
        
        # 生成 sign
        sign = self.generate_sign(file_hash, timestamp, shop_id)
        
        return {
            "sign": sign,
            "t": str(timestamp),
            "hash": hash_value,
            "shopId": shop_id,
            "_fileHash": file_hash,  # 调试用
            "_plaintext": f"{file_hash}#{timestamp}",  # 调试用
        }
    
    def try_decrypt(self, hash_hex: str) -> dict:
        """
        尝试解密 hash 参数
        
        Args:
            hash_hex: 十六进制编码的 hash 参数
            
        Returns:
            解密结果
        """
        results = {}
        ciphertext = bytes.fromhex(hash_hex)
        
        # 尝试两种 IV
        for iv_name, iv in [("default", self.IV_DEFAULT), ("prod", self.IV_PROD)]:
            try:
                cipher = AES.new(self.AES_KEY, AES.MODE_CBC, iv)
                decrypted = cipher.decrypt(ciphertext)
                
                try:
                    unpadded = unpad(decrypted, AES.block_size)
                    decoded = unpadded.decode('utf-8')
                    results[iv_name] = {
                        "success": True,
                        "plaintext": decoded,
                        "parts": decoded.split('#') if '#' in decoded else [decoded]
                    }
                except:
                    results[iv_name] = {
                        "success": False,
                        "raw": decrypted.hex(),
                        "printable": decrypted.decode('utf-8', errors='replace')
                    }
            except Exception as e:
                results[iv_name] = {"error": str(e)}
        
        return results


def main():
    """主函数"""
    print("=" * 60)
    print("喜茶 DIY 贴纸签名生成器")
    print("基于 libheyteago.so 逆向分析")
    print("=" * 60)
    
    # 显示密钥信息
    signer = HeyteaDIYSign()
    print(f"\n密钥信息:")
    print(f"  AES Key: {signer.AES_KEY.decode()}")
    print(f"  IV (默认): {signer.IV_DEFAULT.decode()}")
    print(f"  IV (生产): {signer.IV_PROD.decode()}")
    
    if len(sys.argv) > 1:
        # 使用命令行参数
        file_path = sys.argv[1]
        shop_id = sys.argv[2] if len(sys.argv) > 2 else "4216"
        
        if not os.path.exists(file_path):
            print(f"\n错误: 文件不存在 - {file_path}")
            return
        
        print(f"\n生成签名参数:")
        print(f"  文件: {file_path}")
        print(f"  店铺ID: {shop_id}")
        
        params = signer.generate_params(file_path, shop_id)
        
        print(f"\n生成的参数:")
        print(f"  sign: {params['sign']}")
        print(f"  t: {params['t']}")
        print(f"  hash: {params['hash'][:50]}...")
        print(f"  shopId: {params['shopId']}")
        print(f"\n调试信息:")
        print(f"  fileHash: {params['_fileHash']}")
        print(f"  plaintext: {params['_plaintext']}")
        
    else:
        # 使用测试数据
        print("\n使用 HAR 包中的测试数据:")
        
        test_data = {
            "sign": "805f30f5c3cb85e1779ae3daacf8dce5",
            "t": "1764833231217",
            "hash": "fc8aad8184823f1352f85228236f625c679f39c8d5ac4d86359af0572dc9c621c7b976db4c13fd219d28193536058f304031ff6d446277cd40009e4998ee8e1d31cd1c79e4075c5b1cba897d42d81451",
            "shopId": "4216",
            "filename_hash": "32e6dbcc2678d4d40a297ca5490acc4d",
        }
        
        print(f"\n原始数据:")
        print(f"  sign: {test_data['sign']}")
        print(f"  t: {test_data['t']}")
        print(f"  hash: {test_data['hash'][:50]}... ({len(test_data['hash'])} chars)")
        print(f"  shopId: {test_data['shopId']}")
        print(f"  filename hash: {test_data['filename_hash']}")
        
        # 尝试生成相同的参数
        print("\n尝试重新生成 (使用 filename_hash 作为 fileHash):")
        hash_value, _ = signer.generate_hash(
            test_data["filename_hash"], 
            int(test_data["t"])
        )
        
        print(f"  生成的 hash: {hash_value[:50]}...")
        print(f"  匹配原始: {'✓' if hash_value == test_data['hash'] else '✗'}")
        
        # 尝试解密
        print("\n尝试解密原始 hash:")
        decrypt_results = signer.try_decrypt(test_data["hash"])
        for iv_name, result in decrypt_results.items():
            print(f"\n  使用 {iv_name} IV:")
            if result.get("success"):
                print(f"    ✓ 解密成功: {result['plaintext']}")
                if len(result['parts']) == 2:
                    print(f"    fileHash: {result['parts'][0]}")
                    print(f"    timestamp: {result['parts'][1]}")
            elif "error" in result:
                print(f"    ✗ 错误: {result['error']}")
            else:
                print(f"    ✗ 解密失败")
                print(f"    原始字节: {result.get('raw', 'N/A')[:60]}...")
        
        print("\n" + "=" * 60)
        print("用法: python heytea_diy_sign.py <image_path> [shop_id]")
        print("=" * 60)


if __name__ == "__main__":
    main()


```

```python
"""
喜茶DIY贴纸签名生成器
基于 libheyteago.so 逆向分析

算法流程:
1. 获取当前毫秒时间戳 t
2. 构造明文: fileHash + "#" + t  
3. 使用 AES-128-CBC 加密
4. 将加密结果转为十六进制字符串作为 hash 参数

密钥信息 (从 libheyteago.so 提取):
- AES Key: HEYTEA6H7J8K9M0N (16字节, 地址 0x4379F4)
- IV默认: AN1wedUvOJblfD8Z (16字节, 地址 0x437A04)  
- IV备选: 2lJuL8jdH03oNIq5 (16字节, 地址 0x437A15)

使用方法:
    python heytea_diy_sign.py <image_path> [shop_id]
"""

import hashlib
import time
import sys
import os

try:
    from Crypto.Cipher import AES
    from Crypto.Util.Padding import pad, unpad
except ImportError:
    print("请先安装 pycryptodome: pip install pycryptodome")
    sys.exit(1)


class HeyteaDIYSign:
    """喜茶DIY签名生成器"""
    
    # 从 libheyteago.so 逆向提取的密钥
    AES_KEY = b"HEYTEA6H7J8K9M0N"  # 16 bytes = AES-128
    IV_DEFAULT = b"AN1wedUvOJblfD8Z"  # 默认 IV
    IV_PROD = b"2lJuL8jdH03oNIq5"  # 生产环境 IV
    
    def __init__(self, env: str = "default"):
        """
        初始化签名生成器
        
        Args:
            env: 环境配置, "default" 或 "prod"
        """
        self.env = env
        self.iv = self.IV_PROD if env == "prod" else self.IV_DEFAULT
    
    @staticmethod
    def calculate_file_md5(file_path: str) -> str:
        """计算文件的 MD5 哈希值 (注意: 这不是 fileHash 参数!)"""
        with open(file_path, "rb") as f:
            return hashlib.md5(f.read()).hexdigest()
    
    @staticmethod
    def extract_file_hash_from_path(file_path: str) -> str:
        """
        从微信临时文件路径中提取 fileHash
        
        微信小程序的临时文件路径格式:
        - wxfile://tmp_32e6dbcc2678d4d40a297ca5490acc4d.png
        - http://tmp/tmp_32e6dbcc2678d4d40a297ca5490acc4d.png
        - tmp_32e6dbcc2678d4d40a297ca5490acc4d.png
        
        fileHash 是 tmp_ 后面、扩展名前面的那部分
        """
        import re
        import os
        
        # 获取文件名
        filename = os.path.basename(file_path)
        
        # 匹配 tmp_xxxxx 格式
        match = re.search(r'tmp_([a-fA-F0-9]+)', filename)
        if match:
            return match.group(1)
        
        # 如果没有 tmp_ 前缀，返回文件名（不含扩展名）
        name_without_ext = os.path.splitext(filename)[0]
        return name_without_ext
    
    def aes_encrypt(self, plaintext: bytes) -> bytes:
        """AES-128-CBC 加密"""
        cipher = AES.new(self.AES_KEY, AES.MODE_CBC, self.iv)
        padded = pad(plaintext, AES.block_size)
        return cipher.encrypt(padded)
    
    def aes_decrypt(self, ciphertext: bytes) -> bytes:
        """AES-128-CBC 解密"""
        cipher = AES.new(self.AES_KEY, AES.MODE_CBC, self.iv)
        decrypted = cipher.decrypt(ciphertext)
        try:
            return unpad(decrypted, AES.block_size)
        except ValueError:
            return decrypted
    
    def generate_hash(self, file_hash: str, timestamp: int = None) -> tuple:
        """
        生成 hash 参数
        
        Args:
            file_hash: 微信临时文件标识符 (从 tmp_xxxxx.png 文件名中提取的 xxxxx 部分)
                       注意：这不是文件内容的 MD5，而是微信小程序生成的临时文件标识符！
                       例如: tmp_32e6dbcc2678d4d40a297ca5490acc4d.png 中的 32e6dbcc2678d4d40a297ca5490acc4d
            timestamp: 时间戳(毫秒), 默认当前时间
            
        Returns:
            (hash_value, timestamp)
        """
        if timestamp is None:
            timestamp = int(time.time() * 1000)
        
        # 构造明文: fileHash#timestamp
        plaintext = f"{file_hash}#{timestamp}".encode('utf-8')
        
        # AES 加密
        ciphertext = self.aes_encrypt(plaintext)
        
        # 转为十六进制
        hash_value = ciphertext.hex()
        
        return hash_value, timestamp
    
    def generate_sign(self, file_hash: str, timestamp: int, shop_id: str) -> str:
        """
        生成 sign 参数 (尝试多种组合)
        
        注意: sign 的确切算法尚未完全确认
        """
        # 可能的组合
        candidates = [
            hashlib.md5(f"{file_hash}{timestamp}{shop_id}".encode()).hexdigest(),
            hashlib.md5(f"{timestamp}{file_hash}{shop_id}".encode()).hexdigest(),
            hashlib.md5(f"{file_hash}#{timestamp}".encode()).hexdigest(),
            hashlib.md5(f"{shop_id}{timestamp}{file_hash}".encode()).hexdigest(),
        ]
        
        # 默认返回第一种
        return candidates[0]
    
    def generate_params(self, file_path: str, shop_id: str = "4216") -> dict:
        """
        生成完整的请求参数
        
        Args:
            file_path: 图片文件路径
            shop_id: 店铺ID
            
        Returns:
            包含 sign, t, hash, shopId 的字典
        """
        # 计算文件 MD5
        file_hash = self.calculate_file_md5(file_path)
        
        # 生成 hash 和 timestamp
        hash_value, timestamp = self.generate_hash(file_hash)
        
        # 生成 sign
        sign = self.generate_sign(file_hash, timestamp, shop_id)
        
        return {
            "sign": sign,
            "t": str(timestamp),
            "hash": hash_value,
            "shopId": shop_id,
            "_fileHash": file_hash,  # 调试用
            "_plaintext": f"{file_hash}#{timestamp}",  # 调试用
        }
    
    def try_decrypt(self, hash_hex: str) -> dict:
        """
        尝试解密 hash 参数
        
        Args:
            hash_hex: 十六进制编码的 hash 参数
            
        Returns:
            解密结果
        """
        results = {}
        ciphertext = bytes.fromhex(hash_hex)
        
        # 尝试两种 IV
        for iv_name, iv in [("default", self.IV_DEFAULT), ("prod", self.IV_PROD)]:
            try:
                cipher = AES.new(self.AES_KEY, AES.MODE_CBC, iv)
                decrypted = cipher.decrypt(ciphertext)
                
                try:
                    unpadded = unpad(decrypted, AES.block_size)
                    decoded = unpadded.decode('utf-8')
                    results[iv_name] = {
                        "success": True,
                        "plaintext": decoded,
                        "parts": decoded.split('#') if '#' in decoded else [decoded]
                    }
                except:
                    results[iv_name] = {
                        "success": False,
                        "raw": decrypted.hex(),
                        "printable": decrypted.decode('utf-8', errors='replace')
                    }
            except Exception as e:
                results[iv_name] = {"error": str(e)}
        
        return results


def main():
    """主函数"""
    print("=" * 60)
    print("喜茶 DIY 贴纸签名生成器")
    print("基于 libheyteago.so 逆向分析")
    print("=" * 60)
    
    # 显示密钥信息
    signer = HeyteaDIYSign()
    print(f"\n密钥信息:")
    print(f"  AES Key: {signer.AES_KEY.decode()}")
    print(f"  IV (默认): {signer.IV_DEFAULT.decode()}")
    print(f"  IV (生产): {signer.IV_PROD.decode()}")
    
    if len(sys.argv) > 1:
        # 使用命令行参数
        file_path = sys.argv[1]
        shop_id = sys.argv[2] if len(sys.argv) > 2 else "4216"
        
        if not os.path.exists(file_path):
            print(f"\n错误: 文件不存在 - {file_path}")
            return
        
        print(f"\n生成签名参数:")
        print(f"  文件: {file_path}")
        print(f"  店铺ID: {shop_id}")
        
        params = signer.generate_params(file_path, shop_id)
        
        print(f"\n生成的参数:")
        print(f"  sign: {params['sign']}")
        print(f"  t: {params['t']}")
        print(f"  hash: {params['hash'][:50]}...")
        print(f"  shopId: {params['shopId']}")
        print(f"\n调试信息:")
        print(f"  fileHash: {params['_fileHash']}")
        print(f"  plaintext: {params['_plaintext']}")
        
    else:
        # 使用测试数据
        print("\n使用 HAR 包中的测试数据:")
        
        test_data = {
            "sign": "805f30f5c3cb85e1779ae3daacf8dce5",
            "t": "1764833231217",
            "hash": "fc8aad8184823f1352f85228236f625c679f39c8d5ac4d86359af0572dc9c621c7b976db4c13fd219d28193536058f304031ff6d446277cd40009e4998ee8e1d31cd1c79e4075c5b1cba897d42d81451",
            "shopId": "4216",
            "filename_hash": "32e6dbcc2678d4d40a297ca5490acc4d",
        }
        
        print(f"\n原始数据:")
        print(f"  sign: {test_data['sign']}")
        print(f"  t: {test_data['t']}")
        print(f"  hash: {test_data['hash'][:50]}... ({len(test_data['hash'])} chars)")
        print(f"  shopId: {test_data['shopId']}")
        print(f"  filename hash: {test_data['filename_hash']}")
        
        # 尝试生成相同的参数
        print("\n尝试重新生成 (使用 filename_hash 作为 fileHash):")
        hash_value, _ = signer.generate_hash(
            test_data["filename_hash"], 
            int(test_data["t"])
        )
        
        print(f"  生成的 hash: {hash_value[:50]}...")
        print(f"  匹配原始: {'✓' if hash_value == test_data['hash'] else '✗'}")
        
        # 尝试解密
        print("\n尝试解密原始 hash:")
        decrypt_results = signer.try_decrypt(test_data["hash"])
        for iv_name, result in decrypt_results.items():
            print(f"\n  使用 {iv_name} IV:")
            if result.get("success"):
                print(f"    ✓ 解密成功: {result['plaintext']}")
                if len(result['parts']) == 2:
                    print(f"    fileHash: {result['parts'][0]}")
                    print(f"    timestamp: {result['parts'][1]}")
            elif "error" in result:
                print(f"    ✗ 错误: {result['error']}")
            else:
                print(f"    ✗ 解密失败")
                print(f"    原始字节: {result.get('raw', 'N/A')[:60]}...")
        
        print("\n" + "=" * 60)
        print("用法: python heytea_diy_sign.py <image_path> [shop_id]")
        print("=" * 60)


if __name__ == "__main__":
    main()


```

```python
"""
喜茶DIY贴纸签名生成器
基于 libheyteago.so 逆向分析

算法流程:
1. 获取当前毫秒时间戳 t
2. 构造明文: fileHash + "#" + t  
3. 使用 AES-128-CBC 加密
4. 将加密结果转为十六进制字符串作为 hash 参数

密钥信息 (从 libheyteago.so 提取):
- AES Key: HEYTEA6H7J8K9M0N (16字节, 地址 0x4379F4)
- IV默认: AN1wedUvOJblfD8Z (16字节, 地址 0x437A04)  
- IV备选: 2lJuL8jdH03oNIq5 (16字节, 地址 0x437A15)

使用方法:
    python heytea_diy_sign.py <image_path> [shop_id]
"""

import hashlib
import time
import sys
import os

try:
    from Crypto.Cipher import AES
    from Crypto.Util.Padding import pad, unpad
except ImportError:
    print("请先安装 pycryptodome: pip install pycryptodome")
    sys.exit(1)


class HeyteaDIYSign:
    """喜茶DIY签名生成器"""
    
    # 从 libheyteago.so 逆向提取的密钥
    AES_KEY = b"HEYTEA6H7J8K9M0N"  # 16 bytes = AES-128
    IV_DEFAULT = b"AN1wedUvOJblfD8Z"  # 默认 IV
    IV_PROD = b"2lJuL8jdH03oNIq5"  # 生产环境 IV
    
    def __init__(self, env: str = "default"):
        """
        初始化签名生成器
        
        Args:
            env: 环境配置, "default" 或 "prod"
        """
        self.env = env
        self.iv = self.IV_PROD if env == "prod" else self.IV_DEFAULT
    
    @staticmethod
    def calculate_file_md5(file_path: str) -> str:
        """计算文件的 MD5 哈希值 (注意: 这不是 fileHash 参数!)"""
        with open(file_path, "rb") as f:
            return hashlib.md5(f.read()).hexdigest()
    
    @staticmethod
    def extract_file_hash_from_path(file_path: str) -> str:
        """
        从微信临时文件路径中提取 fileHash
        
        微信小程序的临时文件路径格式:
        - wxfile://tmp_32e6dbcc2678d4d40a297ca5490acc4d.png
        - http://tmp/tmp_32e6dbcc2678d4d40a297ca5490acc4d.png
        - tmp_32e6dbcc2678d4d40a297ca5490acc4d.png
        
        fileHash 是 tmp_ 后面、扩展名前面的那部分
        """
        import re
        import os
        
        # 获取文件名
        filename = os.path.basename(file_path)
        
        # 匹配 tmp_xxxxx 格式
        match = re.search(r'tmp_([a-fA-F0-9]+)', filename)
        if match:
            return match.group(1)
        
        # 如果没有 tmp_ 前缀，返回文件名（不含扩展名）
        name_without_ext = os.path.splitext(filename)[0]
        return name_without_ext
    
    def aes_encrypt(self, plaintext: bytes) -> bytes:
        """AES-128-CBC 加密"""
        cipher = AES.new(self.AES_KEY, AES.MODE_CBC, self.iv)
        padded = pad(plaintext, AES.block_size)
        return cipher.encrypt(padded)
    
    def aes_decrypt(self, ciphertext: bytes) -> bytes:
        """AES-128-CBC 解密"""
        cipher = AES.new(self.AES_KEY, AES.MODE_CBC, self.iv)
        decrypted = cipher.decrypt(ciphertext)
        try:
            return unpad(decrypted, AES.block_size)
        except ValueError:
            return decrypted
    
    def generate_hash(self, file_hash: str, timestamp: int = None) -> tuple:
        """
        生成 hash 参数
        
        Args:
            file_hash: 微信临时文件标识符 (从 tmp_xxxxx.png 文件名中提取的 xxxxx 部分)
                       注意：这不是文件内容的 MD5，而是微信小程序生成的临时文件标识符！
                       例如: tmp_32e6dbcc2678d4d40a297ca5490acc4d.png 中的 32e6dbcc2678d4d40a297ca5490acc4d
            timestamp: 时间戳(毫秒), 默认当前时间
            
        Returns:
            (hash_value, timestamp)
        """
        if timestamp is None:
            timestamp = int(time.time() * 1000)
        
        # 构造明文: fileHash#timestamp
        plaintext = f"{file_hash}#{timestamp}".encode('utf-8')
        
        # AES 加密
        ciphertext = self.aes_encrypt(plaintext)
        
        # 转为十六进制
        hash_value = ciphertext.hex()
        
        return hash_value, timestamp
    
    def generate_sign(self, file_hash: str, timestamp: int, shop_id: str) -> str:
        """
        生成 sign 参数 (尝试多种组合)
        
        注意: sign 的确切算法尚未完全确认
        """
        # 可能的组合
        candidates = [
            hashlib.md5(f"{file_hash}{timestamp}{shop_id}".encode()).hexdigest(),
            hashlib.md5(f"{timestamp}{file_hash}{shop_id}".encode()).hexdigest(),
            hashlib.md5(f"{file_hash}#{timestamp}".encode()).hexdigest(),
            hashlib.md5(f"{shop_id}{timestamp}{file_hash}".encode()).hexdigest(),
        ]
        
        # 默认返回第一种
        return candidates[0]
    
    def generate_params(self, file_path: str, shop_id: str = "4216", file_hash: str = None) -> dict:
        """
        生成完整的请求参数
        
        Args:
            file_path: 图片文件路径 (微信临时文件路径或普通文件路径)
            shop_id: 店铺ID
            file_hash: 可选，直接指定 fileHash。如果不指定，则从文件路径中提取
            
        Returns:
            包含 sign, t, hash, shopId 的字典
            
        重要说明:
            fileHash 不是文件内容的 MD5！
            它是微信小程序临时文件路径中的标识符 (tmp_xxxxx.png 中的 xxxxx)
        """
        # 获取 fileHash
        if file_hash is None:
            file_hash = self.extract_file_hash_from_path(file_path)
        
        # 计算文件实际 MD5 (仅供参考)
        actual_md5 = self.calculate_file_md5(file_path) if os.path.exists(file_path) else "N/A"
        
        # 生成 hash 和 timestamp
        hash_value, timestamp = self.generate_hash(file_hash)
        
        # 生成 sign
        sign = self.generate_sign(file_hash, timestamp, shop_id)
        
        return {
            "sign": sign,
            "t": str(timestamp),
            "hash": hash_value,
            "shopId": shop_id,
            "_fileHash": file_hash,  # 从文件名提取的微信标识符
            "_actualMD5": actual_md5,  # 文件实际 MD5 (仅供参考，不用于签名)
            "_plaintext": f"{file_hash}#{timestamp}",  # 调试用
        }
    
    def try_decrypt(self, hash_hex: str) -> dict:
        """
        尝试解密 hash 参数
        
        Args:
            hash_hex: 十六进制编码的 hash 参数
            
        Returns:
            解密结果
        """
        results = {}
        ciphertext = bytes.fromhex(hash_hex)
        
        # 尝试两种 IV
        for iv_name, iv in [("default", self.IV_DEFAULT), ("prod", self.IV_PROD)]:
            try:
                cipher = AES.new(self.AES_KEY, AES.MODE_CBC, iv)
                decrypted = cipher.decrypt(ciphertext)
                
                try:
                    unpadded = unpad(decrypted, AES.block_size)
                    decoded = unpadded.decode('utf-8')
                    results[iv_name] = {
                        "success": True,
                        "plaintext": decoded,
                        "parts": decoded.split('#') if '#' in decoded else [decoded]
                    }
                except:
                    results[iv_name] = {
                        "success": False,
                        "raw": decrypted.hex(),
                        "printable": decrypted.decode('utf-8', errors='replace')
                    }
            except Exception as e:
                results[iv_name] = {"error": str(e)}
        
        return results


def main():
    """主函数"""
    print("=" * 60)
    print("喜茶 DIY 贴纸签名生成器")
    print("基于 libheyteago.so 逆向分析")
    print("=" * 60)
    
    # 显示密钥信息
    signer = HeyteaDIYSign()
    print(f"\n密钥信息:")
    print(f"  AES Key: {signer.AES_KEY.decode()}")
    print(f"  IV (默认): {signer.IV_DEFAULT.decode()}")
    print(f"  IV (生产): {signer.IV_PROD.decode()}")
    
    if len(sys.argv) > 1:
        # 使用命令行参数
        file_path = sys.argv[1]
        shop_id = sys.argv[2] if len(sys.argv) > 2 else "4216"
        
        if not os.path.exists(file_path):
            print(f"\n错误: 文件不存在 - {file_path}")
            return
        
        print(f"\n生成签名参数:")
        print(f"  文件: {file_path}")
        print(f"  店铺ID: {shop_id}")
        
        params = signer.generate_params(file_path, shop_id)
        
        print(f"\n生成的参数:")
        print(f"  sign: {params['sign']}")
        print(f"  t: {params['t']}")
        print(f"  hash: {params['hash'][:50]}...")
        print(f"  shopId: {params['shopId']}")
        print(f"\n调试信息:")
        print(f"  fileHash: {params['_fileHash']}")
        print(f"  plaintext: {params['_plaintext']}")
        
    else:
        # 使用测试数据
        print("\n使用 HAR 包中的测试数据:")
        
        test_data = {
            "sign": "805f30f5c3cb85e1779ae3daacf8dce5",
            "t": "1764833231217",
            "hash": "fc8aad8184823f1352f85228236f625c679f39c8d5ac4d86359af0572dc9c621c7b976db4c13fd219d28193536058f304031ff6d446277cd40009e4998ee8e1d31cd1c79e4075c5b1cba897d42d81451",
            "shopId": "4216",
            "filename_hash": "32e6dbcc2678d4d40a297ca5490acc4d",
        }
        
        print(f"\n原始数据:")
        print(f"  sign: {test_data['sign']}")
        print(f"  t: {test_data['t']}")
        print(f"  hash: {test_data['hash'][:50]}... ({len(test_data['hash'])} chars)")
        print(f"  shopId: {test_data['shopId']}")
        print(f"  filename hash: {test_data['filename_hash']}")
        
        # 尝试生成相同的参数
        print("\n尝试重新生成 (使用 filename_hash 作为 fileHash):")
        hash_value, _ = signer.generate_hash(
            test_data["filename_hash"], 
            int(test_data["t"])
        )
        
        print(f"  生成的 hash: {hash_value[:50]}...")
        print(f"  匹配原始: {'✓' if hash_value == test_data['hash'] else '✗'}")
        
        # 尝试解密
        print("\n尝试解密原始 hash:")
        decrypt_results = signer.try_decrypt(test_data["hash"])
        for iv_name, result in decrypt_results.items():
            print(f"\n  使用 {iv_name} IV:")
            if result.get("success"):
                print(f"    ✓ 解密成功: {result['plaintext']}")
                if len(result['parts']) == 2:
                    print(f"    fileHash: {result['parts'][0]}")
                    print(f"    timestamp: {result['parts'][1]}")
            elif "error" in result:
                print(f"    ✗ 错误: {result['error']}")
            else:
                print(f"    ✗ 解密失败")
                print(f"    原始字节: {result.get('raw', 'N/A')[:60]}...")
        
        print("\n" + "=" * 60)
        print("用法: python heytea_diy_sign.py <image_path> [shop_id]")
        print("=" * 60)


if __name__ == "__main__":
    main()


```

```python
"""
喜茶DIY贴纸签名生成器
基于 libheyteago.so 逆向分析

算法流程:
1. 获取当前毫秒时间戳 t
2. 构造明文: fileHash + "#" + t  
3. 使用 AES-128-CBC 加密
4. 将加密结果转为十六进制字符串作为 hash 参数

密钥信息 (从 libheyteago.so 提取):
- AES Key: HEYTEA6H7J8K9M0N (16字节, 地址 0x4379F4)
- IV默认: AN1wedUvOJblfD8Z (16字节, 地址 0x437A04)  
- IV备选: 2lJuL8jdH03oNIq5 (16字节, 地址 0x437A15)

使用方法:
    python heytea_diy_sign.py <image_path> [shop_id]
"""

import hashlib
import time
import sys
import os

try:
    from Crypto.Cipher import AES
    from Crypto.Util.Padding import pad, unpad
except ImportError:
    print("请先安装 pycryptodome: pip install pycryptodome")
    sys.exit(1)


class HeyteaDIYSign:
    """喜茶DIY签名生成器"""
    
    # 从 libheyteago.so 逆向提取的密钥
    AES_KEY = b"HEYTEA6H7J8K9M0N"  # 16 bytes = AES-128
    IV_DEFAULT = b"AN1wedUvOJblfD8Z"  # 默认 IV
    IV_PROD = b"2lJuL8jdH03oNIq5"  # 生产环境 IV
    
    def __init__(self, env: str = "default"):
        """
        初始化签名生成器
        
        Args:
            env: 环境配置, "default" 或 "prod"
        """
        self.env = env
        self.iv = self.IV_PROD if env == "prod" else self.IV_DEFAULT
    
    @staticmethod
    def calculate_file_md5(file_path: str) -> str:
        """计算文件的 MD5 哈希值 (注意: 这不是 fileHash 参数!)"""
        with open(file_path, "rb") as f:
            return hashlib.md5(f.read()).hexdigest()
    
    @staticmethod
    def extract_file_hash_from_path(file_path: str) -> str:
        """
        从微信临时文件路径中提取 fileHash
        
        微信小程序的临时文件路径格式:
        - wxfile://tmp_32e6dbcc2678d4d40a297ca5490acc4d.png
        - http://tmp/tmp_32e6dbcc2678d4d40a297ca5490acc4d.png
        - tmp_32e6dbcc2678d4d40a297ca5490acc4d.png
        
        fileHash 是 tmp_ 后面、扩展名前面的那部分
        """
        import re
        import os
        
        # 获取文件名
        filename = os.path.basename(file_path)
        
        # 匹配 tmp_xxxxx 格式
        match = re.search(r'tmp_([a-fA-F0-9]+)', filename)
        if match:
            return match.group(1)
        
        # 如果没有 tmp_ 前缀，返回文件名（不含扩展名）
        name_without_ext = os.path.splitext(filename)[0]
        return name_without_ext
    
    def aes_encrypt(self, plaintext: bytes) -> bytes:
        """AES-128-CBC 加密"""
        cipher = AES.new(self.AES_KEY, AES.MODE_CBC, self.iv)
        padded = pad(plaintext, AES.block_size)
        return cipher.encrypt(padded)
    
    def aes_decrypt(self, ciphertext: bytes) -> bytes:
        """AES-128-CBC 解密"""
        cipher = AES.new(self.AES_KEY, AES.MODE_CBC, self.iv)
        decrypted = cipher.decrypt(ciphertext)
        try:
            return unpad(decrypted, AES.block_size)
        except ValueError:
            return decrypted
    
    def generate_hash(self, file_hash: str, timestamp: int = None) -> tuple:
        """
        生成 hash 参数
        
        Args:
            file_hash: 微信临时文件标识符 (从 tmp_xxxxx.png 文件名中提取的 xxxxx 部分)
                       注意：这不是文件内容的 MD5，而是微信小程序生成的临时文件标识符！
                       例如: tmp_32e6dbcc2678d4d40a297ca5490acc4d.png 中的 32e6dbcc2678d4d40a297ca5490acc4d
            timestamp: 时间戳(毫秒), 默认当前时间
            
        Returns:
            (hash_value, timestamp)
        """
        if timestamp is None:
            timestamp = int(time.time() * 1000)
        
        # 构造明文: fileHash#timestamp
        plaintext = f"{file_hash}#{timestamp}".encode('utf-8')
        
        # AES 加密
        ciphertext = self.aes_encrypt(plaintext)
        
        # 转为十六进制
        hash_value = ciphertext.hex()
        
        return hash_value, timestamp
    
    def generate_sign(self, file_hash: str, timestamp: int, shop_id: str) -> str:
        """
        生成 sign 参数 (尝试多种组合)
        
        注意: sign 的确切算法尚未完全确认
        """
        # 可能的组合
        candidates = [
            hashlib.md5(f"{file_hash}{timestamp}{shop_id}".encode()).hexdigest(),
            hashlib.md5(f"{timestamp}{file_hash}{shop_id}".encode()).hexdigest(),
            hashlib.md5(f"{file_hash}#{timestamp}".encode()).hexdigest(),
            hashlib.md5(f"{shop_id}{timestamp}{file_hash}".encode()).hexdigest(),
        ]
        
        # 默认返回第一种
        return candidates[0]
    
    def generate_params(self, file_path: str, shop_id: str = "4216", file_hash: str = None) -> dict:
        """
        生成完整的请求参数
        
        Args:
            file_path: 图片文件路径 (微信临时文件路径或普通文件路径)
            shop_id: 店铺ID
            file_hash: 可选，直接指定 fileHash。如果不指定，则从文件路径中提取
            
        Returns:
            包含 sign, t, hash, shopId 的字典
            
        重要说明:
            fileHash 不是文件内容的 MD5！
            它是微信小程序临时文件路径中的标识符 (tmp_xxxxx.png 中的 xxxxx)
        """
        # 获取 fileHash
        if file_hash is None:
            file_hash = self.extract_file_hash_from_path(file_path)
        
        # 计算文件实际 MD5 (仅供参考)
        actual_md5 = self.calculate_file_md5(file_path) if os.path.exists(file_path) else "N/A"
        
        # 生成 hash 和 timestamp
        hash_value, timestamp = self.generate_hash(file_hash)
        
        # 生成 sign
        sign = self.generate_sign(file_hash, timestamp, shop_id)
        
        return {
            "sign": sign,
            "t": str(timestamp),
            "hash": hash_value,
            "shopId": shop_id,
            "_fileHash": file_hash,  # 从文件名提取的微信标识符
            "_actualMD5": actual_md5,  # 文件实际 MD5 (仅供参考，不用于签名)
            "_plaintext": f"{file_hash}#{timestamp}",  # 调试用
        }
    
    def try_decrypt(self, hash_hex: str) -> dict:
        """
        尝试解密 hash 参数
        
        Args:
            hash_hex: 十六进制编码的 hash 参数
            
        Returns:
            解密结果
        """
        results = {}
        ciphertext = bytes.fromhex(hash_hex)
        
        # 尝试两种 IV
        for iv_name, iv in [("default", self.IV_DEFAULT), ("prod", self.IV_PROD)]:
            try:
                cipher = AES.new(self.AES_KEY, AES.MODE_CBC, iv)
                decrypted = cipher.decrypt(ciphertext)
                
                try:
                    unpadded = unpad(decrypted, AES.block_size)
                    decoded = unpadded.decode('utf-8')
                    results[iv_name] = {
                        "success": True,
                        "plaintext": decoded,
                        "parts": decoded.split('#') if '#' in decoded else [decoded]
                    }
                except:
                    results[iv_name] = {
                        "success": False,
                        "raw": decrypted.hex(),
                        "printable": decrypted.decode('utf-8', errors='replace')
                    }
            except Exception as e:
                results[iv_name] = {"error": str(e)}
        
        return results


def main():
    """主函数"""
    print("=" * 60)
    print("喜茶 DIY 贴纸签名生成器")
    print("基于 libheyteago.so 逆向分析")
    print("=" * 60)
    
    # 显示密钥信息
    signer = HeyteaDIYSign()
    print(f"\n密钥信息:")
    print(f"  AES Key: {signer.AES_KEY.decode()}")
    print(f"  IV (默认): {signer.IV_DEFAULT.decode()}")
    print(f"  IV (生产): {signer.IV_PROD.decode()}")
    
    if len(sys.argv) > 1:
        # 使用命令行参数
        file_path = sys.argv[1]
        shop_id = sys.argv[2] if len(sys.argv) > 2 else "4216"
        
        if not os.path.exists(file_path):
            print(f"\n错误: 文件不存在 - {file_path}")
            return
        
        print(f"\n生成签名参数:")
        print(f"  文件: {file_path}")
        print(f"  店铺ID: {shop_id}")
        
        params = signer.generate_params(file_path, shop_id)
        
        print(f"\n生成的参数:")
        print(f"  sign: {params['sign']}")
        print(f"  t: {params['t']}")
        print(f"  hash: {params['hash'][:50]}...")
        print(f"  shopId: {params['shopId']}")
        print(f"\n调试信息:")
        print(f"  fileHash: {params['_fileHash']}")
        print(f"  plaintext: {params['_plaintext']}")
        
    else:
        # 使用测试数据
        print("\n使用 HAR 包中的测试数据:")
        
        test_data = {
            "sign": "805f30f5c3cb85e1779ae3daacf8dce5",
            "t": "1764833231217",
            "hash": "fc8aad8184823f1352f85228236f625c679f39c8d5ac4d86359af0572dc9c621c7b976db4c13fd219d28193536058f304031ff6d446277cd40009e4998ee8e1d31cd1c79e4075c5b1cba897d42d81451",
            "shopId": "4216",
            "filename_hash": "32e6dbcc2678d4d40a297ca5490acc4d",
        }
        
        print(f"\n原始数据:")
        print(f"  sign: {test_data['sign']}")
        print(f"  t: {test_data['t']}")
        print(f"  hash: {test_data['hash'][:50]}... ({len(test_data['hash'])} chars)")
        print(f"  shopId: {test_data['shopId']}")
        print(f"  filename hash: {test_data['filename_hash']}")
        
        # 尝试生成相同的参数
        print("\n" + "-" * 40)
        print("关键发现: fileHash 不是文件内容的 MD5！")
        print("fileHash 是微信临时文件路径中的标识符")
        print("例如: tmp_32e6dbcc2678d4d40a297ca5490acc4d.png 中的 32e6dbcc...")
        print("-" * 40)
        
        print("\n尝试重新生成 (使用 filename 中的标识符作为 fileHash):")
        hash_value, _ = signer.generate_hash(
            test_data["filename_hash"], 
            int(test_data["t"])
        )
        
        print(f"  输入 fileHash: {test_data['filename_hash']}")
        print(f"  输入 timestamp: {test_data['t']}")
        print(f"  明文: {test_data['filename_hash']}#{test_data['t']}")
        print(f"  生成的 hash: {hash_value[:50]}...")
        print(f"  原始 hash:    {test_data['hash'][:50]}...")
        print(f"  匹配原始: {'✓' if hash_value == test_data['hash'] else '✗'}")
        
        # 尝试解密
        print("\n尝试解密原始 hash:")
        decrypt_results = signer.try_decrypt(test_data["hash"])
        for iv_name, result in decrypt_results.items():
            print(f"\n  使用 {iv_name} IV:")
            if result.get("success"):
                print(f"    ✓ 解密成功: {result['plaintext']}")
                if len(result['parts']) == 2:
                    print(f"    fileHash: {result['parts'][0]}")
                    print(f"    timestamp: {result['parts'][1]}")
            elif "error" in result:
                print(f"    ✗ 错误: {result['error']}")
            else:
                print(f"    ✗ 解密失败")
                print(f"    原始字节: {result.get('raw', 'N/A')[:60]}...")
        
        print("\n" + "=" * 60)
        print("用法: python heytea_diy_sign.py <image_path> [shop_id]")
        print("=" * 60)


if __name__ == "__main__":
    main()


```

解密仍然失败。让我更深入地分析 IDA 中的加密函数：

```c
sub_15B808(n, &v34, v14, &unk_535C88);
```

```c
sub_15CDF0(ptr, v54, &v51, &v48);
```

我看到 AES 加密使用的是标准 OpenSSL EVP API。让我检查一下是否还有其他的字符串处理或转换步骤：

让我检查字符串拼接函数，看看明文是如何构造的：

```plaintext
std::to_string(&v43, v7);  // v43 = 时间戳字符串
// 第 95 行：构造 fileHash + "#"sub_15B6FC(&v38, a1, "#");  // v38 = fileHash + "#"
// 第 104 行：追加时间戳v10 = sub_15E948((int)&v38, v8, v9);  // v38 = fileHash + "#" + timestamp
```

现在我理解完整的流程了。让我更新脚本来尝试更多的组合：

```python
"""
喜茶DIY贴纸签名生成器
基于 libheyteago.so 逆向分析

算法流程:
1. 获取当前毫秒时间戳 t
2. 构造明文: fileHash + "#" + t  
3. 使用 AES-128-CBC 加密
4. 将加密结果转为十六进制字符串作为 hash 参数

密钥信息 (从 libheyteago.so 提取):
- AES Key: HEYTEA6H7J8K9M0N (16字节, 地址 0x4379F4)
- IV默认: AN1wedUvOJblfD8Z (16字节, 地址 0x437A04)  
- IV备选: 2lJuL8jdH03oNIq5 (16字节, 地址 0x437A15)

使用方法:
    python heytea_diy_sign.py <image_path> [shop_id]
"""

import hashlib
import time
import sys
import os

try:
    from Crypto.Cipher import AES
    from Crypto.Util.Padding import pad, unpad
except ImportError:
    print("请先安装 pycryptodome: pip install pycryptodome")
    sys.exit(1)


class HeyteaDIYSign:
    """喜茶DIY签名生成器"""
    
    # 从 libheyteago.so 逆向提取的密钥
    AES_KEY = b"HEYTEA6H7J8K9M0N"  # 16 bytes = AES-128
    IV_DEFAULT = b"AN1wedUvOJblfD8Z"  # 默认 IV
    IV_PROD = b"2lJuL8jdH03oNIq5"  # 生产环境 IV
    
    def __init__(self, env: str = "default"):
        """
        初始化签名生成器
        
        Args:
            env: 环境配置, "default" 或 "prod"
        """
        self.env = env
        self.iv = self.IV_PROD if env == "prod" else self.IV_DEFAULT
    
    @staticmethod
    def calculate_file_md5(file_path: str) -> str:
        """计算文件的 MD5 哈希值 (注意: 这不是 fileHash 参数!)"""
        with open(file_path, "rb") as f:
            return hashlib.md5(f.read()).hexdigest()
    
    @staticmethod
    def extract_file_hash_from_path(file_path: str) -> str:
        """
        从微信临时文件路径中提取 fileHash
        
        微信小程序的临时文件路径格式:
        - wxfile://tmp_32e6dbcc2678d4d40a297ca5490acc4d.png
        - http://tmp/tmp_32e6dbcc2678d4d40a297ca5490acc4d.png
        - tmp_32e6dbcc2678d4d40a297ca5490acc4d.png
        
        fileHash 是 tmp_ 后面、扩展名前面的那部分
        """
        import re
        import os
        
        # 获取文件名
        filename = os.path.basename(file_path)
        
        # 匹配 tmp_xxxxx 格式
        match = re.search(r'tmp_([a-fA-F0-9]+)', filename)
        if match:
            return match.group(1)
        
        # 如果没有 tmp_ 前缀，返回文件名（不含扩展名）
        name_without_ext = os.path.splitext(filename)[0]
        return name_without_ext
    
    def aes_encrypt(self, plaintext: bytes) -> bytes:
        """AES-128-CBC 加密"""
        cipher = AES.new(self.AES_KEY, AES.MODE_CBC, self.iv)
        padded = pad(plaintext, AES.block_size)
        return cipher.encrypt(padded)
    
    def aes_decrypt(self, ciphertext: bytes) -> bytes:
        """AES-128-CBC 解密"""
        cipher = AES.new(self.AES_KEY, AES.MODE_CBC, self.iv)
        decrypted = cipher.decrypt(ciphertext)
        try:
            return unpad(decrypted, AES.block_size)
        except ValueError:
            return decrypted
    
    def generate_hash(self, file_hash: str, timestamp: int = None) -> tuple:
        """
        生成 hash 参数
        
        Args:
            file_hash: 微信临时文件标识符 (从 tmp_xxxxx.png 文件名中提取的 xxxxx 部分)
                       注意：这不是文件内容的 MD5，而是微信小程序生成的临时文件标识符！
                       例如: tmp_32e6dbcc2678d4d40a297ca5490acc4d.png 中的 32e6dbcc2678d4d40a297ca5490acc4d
            timestamp: 时间戳(毫秒), 默认当前时间
            
        Returns:
            (hash_value, timestamp)
        """
        if timestamp is None:
            timestamp = int(time.time() * 1000)
        
        # 构造明文: fileHash#timestamp
        plaintext = f"{file_hash}#{timestamp}".encode('utf-8')
        
        # AES 加密
        ciphertext = self.aes_encrypt(plaintext)
        
        # 转为十六进制
        hash_value = ciphertext.hex()
        
        return hash_value, timestamp
    
    def generate_sign(self, file_hash: str, timestamp: int, shop_id: str) -> str:
        """
        生成 sign 参数 (尝试多种组合)
        
        注意: sign 的确切算法尚未完全确认
        """
        # 可能的组合
        candidates = [
            hashlib.md5(f"{file_hash}{timestamp}{shop_id}".encode()).hexdigest(),
            hashlib.md5(f"{timestamp}{file_hash}{shop_id}".encode()).hexdigest(),
            hashlib.md5(f"{file_hash}#{timestamp}".encode()).hexdigest(),
            hashlib.md5(f"{shop_id}{timestamp}{file_hash}".encode()).hexdigest(),
        ]
        
        # 默认返回第一种
        return candidates[0]
    
    def generate_params(self, file_path: str, shop_id: str = "4216", file_hash: str = None) -> dict:
        """
        生成完整的请求参数
        
        Args:
            file_path: 图片文件路径 (微信临时文件路径或普通文件路径)
            shop_id: 店铺ID
            file_hash: 可选，直接指定 fileHash。如果不指定，则从文件路径中提取
            
        Returns:
            包含 sign, t, hash, shopId 的字典
            
        重要说明:
            fileHash 不是文件内容的 MD5！
            它是微信小程序临时文件路径中的标识符 (tmp_xxxxx.png 中的 xxxxx)
        """
        # 获取 fileHash
        if file_hash is None:
            file_hash = self.extract_file_hash_from_path(file_path)
        
        # 计算文件实际 MD5 (仅供参考)
        actual_md5 = self.calculate_file_md5(file_path) if os.path.exists(file_path) else "N/A"
        
        # 生成 hash 和 timestamp
        hash_value, timestamp = self.generate_hash(file_hash)
        
        # 生成 sign
        sign = self.generate_sign(file_hash, timestamp, shop_id)
        
        return {
            "sign": sign,
            "t": str(timestamp),
            "hash": hash_value,
            "shopId": shop_id,
            "_fileHash": file_hash,  # 从文件名提取的微信标识符
            "_actualMD5": actual_md5,  # 文件实际 MD5 (仅供参考，不用于签名)
            "_plaintext": f"{file_hash}#{timestamp}",  # 调试用
        }
    
    def try_decrypt(self, hash_hex: str) -> dict:
        """
        尝试解密 hash 参数
        
        Args:
            hash_hex: 十六进制编码的 hash 参数
            
        Returns:
            解密结果
        """
        results = {}
        ciphertext = bytes.fromhex(hash_hex)
        
        # 尝试两种 IV
        for iv_name, iv in [("default", self.IV_DEFAULT), ("prod", self.IV_PROD)]:
            try:
                cipher = AES.new(self.AES_KEY, AES.MODE_CBC, iv)
                decrypted = cipher.decrypt(ciphertext)
                
                try:
                    unpadded = unpad(decrypted, AES.block_size)
                    decoded = unpadded.decode('utf-8')
                    results[iv_name] = {
                        "success": True,
                        "plaintext": decoded,
                        "parts": decoded.split('#') if '#' in decoded else [decoded]
                    }
                except:
                    results[iv_name] = {
                        "success": False,
                        "raw": decrypted.hex(),
                        "printable": decrypted.decode('utf-8', errors='replace')
                    }
            except Exception as e:
                results[iv_name] = {"error": str(e)}
        
        return results


def main():
    """主函数"""
    print("=" * 60)
    print("喜茶 DIY 贴纸签名生成器")
    print("基于 libheyteago.so 逆向分析")
    print("=" * 60)
    
    # 显示密钥信息
    signer = HeyteaDIYSign()
    print(f"\n密钥信息:")
    print(f"  AES Key: {signer.AES_KEY.decode()}")
    print(f"  IV (默认): {signer.IV_DEFAULT.decode()}")
    print(f"  IV (生产): {signer.IV_PROD.decode()}")
    
    if len(sys.argv) > 1:
        # 使用命令行参数
        file_path = sys.argv[1]
        shop_id = sys.argv[2] if len(sys.argv) > 2 else "4216"
        
        if not os.path.exists(file_path):
            print(f"\n错误: 文件不存在 - {file_path}")
            return
        
        print(f"\n生成签名参数:")
        print(f"  文件: {file_path}")
        print(f"  店铺ID: {shop_id}")
        
        params = signer.generate_params(file_path, shop_id)
        
        print(f"\n生成的参数:")
        print(f"  sign: {params['sign']}")
        print(f"  t: {params['t']}")
        print(f"  hash: {params['hash'][:50]}...")
        print(f"  shopId: {params['shopId']}")
        print(f"\n调试信息:")
        print(f"  fileHash: {params['_fileHash']}")
        print(f"  plaintext: {params['_plaintext']}")
        
    else:
        # 使用测试数据
        print("\n使用 HAR 包中的测试数据:")
        
        test_data = {
            "sign": "805f30f5c3cb85e1779ae3daacf8dce5",
            "t": "1764833231217",
            "hash": "fc8aad8184823f1352f85228236f625c679f39c8d5ac4d86359af0572dc9c621c7b976db4c13fd219d28193536058f304031ff6d446277cd40009e4998ee8e1d31cd1c79e4075c5b1cba897d42d81451",
            "shopId": "4216",
            "filename_hash": "32e6dbcc2678d4d40a297ca5490acc4d",
        }
        
        print(f"\n原始数据:")
        print(f"  sign: {test_data['sign']}")
        print(f"  t: {test_data['t']}")
        print(f"  hash: {test_data['hash'][:50]}... ({len(test_data['hash'])} chars)")
        print(f"  shopId: {test_data['shopId']}")
        print(f"  filename hash: {test_data['filename_hash']}")
        
        # 尝试生成相同的参数
        print("\n" + "-" * 40)
        print("关键发现: fileHash 不是文件内容的 MD5！")
        print("fileHash 是微信临时文件路径中的标识符")
        print("例如: tmp_32e6dbcc2678d4d40a297ca5490acc4d.png 中的 32e6dbcc...")
        print("-" * 40)
        
        print("\n尝试重新生成 (使用 filename 中的标识符作为 fileHash):")
        hash_value, _ = signer.generate_hash(
            test_data["filename_hash"], 
            int(test_data["t"])
        )
        
        print(f"  输入 fileHash: {test_data['filename_hash']}")
        print(f"  输入 timestamp: {test_data['t']}")
        print(f"  明文: {test_data['filename_hash']}#{test_data['t']}")
        print(f"  生成的 hash: {hash_value[:50]}...")
        print(f"  原始 hash:    {test_data['hash'][:50]}...")
        print(f"  匹配原始: {'✓' if hash_value == test_data['hash'] else '✗'}")
        
        # 尝试解密
        print("\n尝试解密原始 hash:")
        decrypt_results = signer.try_decrypt(test_data["hash"])
        for iv_name, result in decrypt_results.items():
            print(f"\n  使用 {iv_name} IV:")
            if result.get("success"):
                print(f"    ✓ 解密成功: {result['plaintext']}")
                if len(result['parts']) == 2:
                    print(f"    fileHash: {result['parts'][0]}")
                    print(f"    timestamp: {result['parts'][1]}")
            elif "error" in result:
                print(f"    ✗ 错误: {result['error']}")
            else:
                print(f"    ✗ 解密失败")
                print(f"    原始字节: {result.get('raw', 'N/A')[:60]}...")
        
        # 尝试不同的明文格式
        print("\n" + "=" * 60)
        print("尝试不同的明文格式:")
        print("=" * 60)
        
        test_plaintexts = [
            f"{test_data['filename_hash']}#{test_data['t']}",  # 标准格式
            f"{test_data['t']}#{test_data['filename_hash']}",  # 反向
            test_data['filename_hash'],  # 仅 fileHash
            f"{test_data['filename_hash']}",  # 仅 fileHash
        ]
        
        for pt in test_plaintexts:
            print(f"\n明文: {pt[:60]}...")
            for env in ["default", "prod"]:
                signer_test = HeyteaDIYSign(env=env)
                encrypted = signer_test.aes_encrypt(pt.encode('utf-8'))
                result_hex = encrypted.hex()
                match = "✓" if result_hex == test_data["hash"] else "✗"
                print(f"  {env} IV: {result_hex[:40]}... {match}")
        
        print("\n" + "=" * 60)
        print("用法: python heytea_diy_sign.py <image_path> [shop_id]")
        print("=" * 60)


if __name__ == "__main__":
    main()


```

让我检查是否有其他密钥或处理步骤：

根据我详细的 IDA 逆向分析，让我更新签名脚本的文档说明：

```python
"""
喜茶DIY贴纸签名生成器
基于 libheyteago.so 逆向分析 (IDA Pro)

=== 重要发现 ===
fileHash 参数不是文件内容的 MD5！
它是微信小程序临时文件路径中的标识符
例如: tmp_32e6dbcc2678d4d40a297ca5490acc4d.png 中的 "32e6dbcc2678d4d40a297ca5490acc4d"

算法流程 (从 sub_15B230 @ 0x15B230 逆向):
1. 获取当前毫秒时间戳 t: std::chrono::system_clock::now() / 1000
2. 构造明文: fileHash + "#" + t  
3. 选择 IV: 根据环境变量选择 (sub_15BC94 @ 0x15BC94)
4. 使用 AES-128-CBC 加密 (sub_15CDF0 @ 0x15CDF0, EVP API)
5. 将加密结果转为十六进制字符串作为 hash 参数

密钥信息 (从 libheyteago.so 提取):
- AES Key: HEYTEA6H7J8K9M0N (16字节, 地址 0x4379F4)
- IV默认: AN1wedUvOJblfD8Z (16字节, 地址 0x437A04, 用于非 prod 环境)
- IV生产: 2lJuL8jdH03oNIq5 (16字节, 地址 0x437A15, 当环境="prod"时使用)

IV 选择逻辑 (sub_15BC94):
- 检查环境字符串长度是否为 4
- 如果值等于 0x64706970 ("prod" 的小端序)，使用 IV生产
- 否则使用 IV默认

当前状态: 生成的 hash 与实际抓包不匹配，可能原因：
1. fileHash 的来源/格式不完全正确
2. 还有其他未发现的处理步骤
3. 需要更多样本进行验证

使用方法:
    python heytea_diy_sign.py <image_path> [shop_id]
"""

import hashlib
import time
import sys
import os

try:
    from Crypto.Cipher import AES
    from Crypto.Util.Padding import pad, unpad
except ImportError:
    print("请先安装 pycryptodome: pip install pycryptodome")
    sys.exit(1)


class HeyteaDIYSign:
    """喜茶DIY签名生成器"""
    
    # 从 libheyteago.so 逆向提取的密钥
    AES_KEY = b"HEYTEA6H7J8K9M0N"  # 16 bytes = AES-128
    IV_DEFAULT = b"AN1wedUvOJblfD8Z"  # 默认 IV
    IV_PROD = b"2lJuL8jdH03oNIq5"  # 生产环境 IV
    
    def __init__(self, env: str = "default"):
        """
        初始化签名生成器
        
        Args:
            env: 环境配置, "default" 或 "prod"
        """
        self.env = env
        self.iv = self.IV_PROD if env == "prod" else self.IV_DEFAULT
    
    @staticmethod
    def calculate_file_md5(file_path: str) -> str:
        """计算文件的 MD5 哈希值 (注意: 这不是 fileHash 参数!)"""
        with open(file_path, "rb") as f:
            return hashlib.md5(f.read()).hexdigest()
    
    @staticmethod
    def extract_file_hash_from_path(file_path: str) -> str:
        """
        从微信临时文件路径中提取 fileHash
        
        微信小程序的临时文件路径格式:
        - wxfile://tmp_32e6dbcc2678d4d40a297ca5490acc4d.png
        - http://tmp/tmp_32e6dbcc2678d4d40a297ca5490acc4d.png
        - tmp_32e6dbcc2678d4d40a297ca5490acc4d.png
        
        fileHash 是 tmp_ 后面、扩展名前面的那部分
        """
        import re
        import os
        
        # 获取文件名
        filename = os.path.basename(file_path)
        
        # 匹配 tmp_xxxxx 格式
        match = re.search(r'tmp_([a-fA-F0-9]+)', filename)
        if match:
            return match.group(1)
        
        # 如果没有 tmp_ 前缀，返回文件名（不含扩展名）
        name_without_ext = os.path.splitext(filename)[0]
        return name_without_ext
    
    def aes_encrypt(self, plaintext: bytes) -> bytes:
        """AES-128-CBC 加密"""
        cipher = AES.new(self.AES_KEY, AES.MODE_CBC, self.iv)
        padded = pad(plaintext, AES.block_size)
        return cipher.encrypt(padded)
    
    def aes_decrypt(self, ciphertext: bytes) -> bytes:
        """AES-128-CBC 解密"""
        cipher = AES.new(self.AES_KEY, AES.MODE_CBC, self.iv)
        decrypted = cipher.decrypt(ciphertext)
        try:
            return unpad(decrypted, AES.block_size)
        except ValueError:
            return decrypted
    
    def generate_hash(self, file_hash: str, timestamp: int = None) -> tuple:
        """
        生成 hash 参数
        
        Args:
            file_hash: 微信临时文件标识符 (从 tmp_xxxxx.png 文件名中提取的 xxxxx 部分)
                       注意：这不是文件内容的 MD5，而是微信小程序生成的临时文件标识符！
                       例如: tmp_32e6dbcc2678d4d40a297ca5490acc4d.png 中的 32e6dbcc2678d4d40a297ca5490acc4d
            timestamp: 时间戳(毫秒), 默认当前时间
            
        Returns:
            (hash_value, timestamp)
        """
        if timestamp is None:
            timestamp = int(time.time() * 1000)
        
        # 构造明文: fileHash#timestamp
        plaintext = f"{file_hash}#{timestamp}".encode('utf-8')
        
        # AES 加密
        ciphertext = self.aes_encrypt(plaintext)
        
        # 转为十六进制
        hash_value = ciphertext.hex()
        
        return hash_value, timestamp
    
    def generate_sign(self, file_hash: str, timestamp: int, shop_id: str) -> str:
        """
        生成 sign 参数 (尝试多种组合)
        
        注意: sign 的确切算法尚未完全确认
        """
        # 可能的组合
        candidates = [
            hashlib.md5(f"{file_hash}{timestamp}{shop_id}".encode()).hexdigest(),
            hashlib.md5(f"{timestamp}{file_hash}{shop_id}".encode()).hexdigest(),
            hashlib.md5(f"{file_hash}#{timestamp}".encode()).hexdigest(),
            hashlib.md5(f"{shop_id}{timestamp}{file_hash}".encode()).hexdigest(),
        ]
        
        # 默认返回第一种
        return candidates[0]
    
    def generate_params(self, file_path: str, shop_id: str = "4216", file_hash: str = None) -> dict:
        """
        生成完整的请求参数
        
        Args:
            file_path: 图片文件路径 (微信临时文件路径或普通文件路径)
            shop_id: 店铺ID
            file_hash: 可选，直接指定 fileHash。如果不指定，则从文件路径中提取
            
        Returns:
            包含 sign, t, hash, shopId 的字典
            
        重要说明:
            fileHash 不是文件内容的 MD5！
            它是微信小程序临时文件路径中的标识符 (tmp_xxxxx.png 中的 xxxxx)
        """
        # 获取 fileHash
        if file_hash is None:
            file_hash = self.extract_file_hash_from_path(file_path)
        
        # 计算文件实际 MD5 (仅供参考)
        actual_md5 = self.calculate_file_md5(file_path) if os.path.exists(file_path) else "N/A"
        
        # 生成 hash 和 timestamp
        hash_value, timestamp = self.generate_hash(file_hash)
        
        # 生成 sign
        sign = self.generate_sign(file_hash, timestamp, shop_id)
        
        return {
            "sign": sign,
            "t": str(timestamp),
            "hash": hash_value,
            "shopId": shop_id,
            "_fileHash": file_hash,  # 从文件名提取的微信标识符
            "_actualMD5": actual_md5,  # 文件实际 MD5 (仅供参考，不用于签名)
            "_plaintext": f"{file_hash}#{timestamp}",  # 调试用
        }
    
    def try_decrypt(self, hash_hex: str) -> dict:
        """
        尝试解密 hash 参数
        
        Args:
            hash_hex: 十六进制编码的 hash 参数
            
        Returns:
            解密结果
        """
        results = {}
        ciphertext = bytes.fromhex(hash_hex)
        
        # 尝试两种 IV
        for iv_name, iv in [("default", self.IV_DEFAULT), ("prod", self.IV_PROD)]:
            try:
                cipher = AES.new(self.AES_KEY, AES.MODE_CBC, iv)
                decrypted = cipher.decrypt(ciphertext)
                
                try:
                    unpadded = unpad(decrypted, AES.block_size)
                    decoded = unpadded.decode('utf-8')
                    results[iv_name] = {
                        "success": True,
                        "plaintext": decoded,
                        "parts": decoded.split('#') if '#' in decoded else [decoded]
                    }
                except:
                    results[iv_name] = {
                        "success": False,
                        "raw": decrypted.hex(),
                        "printable": decrypted.decode('utf-8', errors='replace')
                    }
            except Exception as e:
                results[iv_name] = {"error": str(e)}
        
        return results


def main():
    """主函数"""
    print("=" * 60)
    print("喜茶 DIY 贴纸签名生成器")
    print("基于 libheyteago.so 逆向分析")
    print("=" * 60)
    
    # 显示密钥信息
    signer = HeyteaDIYSign()
    print(f"\n密钥信息:")
    print(f"  AES Key: {signer.AES_KEY.decode()}")
    print(f"  IV (默认): {signer.IV_DEFAULT.decode()}")
    print(f"  IV (生产): {signer.IV_PROD.decode()}")
    
    if len(sys.argv) > 1:
        # 使用命令行参数
        file_path = sys.argv[1]
        shop_id = sys.argv[2] if len(sys.argv) > 2 else "4216"
        
        if not os.path.exists(file_path):
            print(f"\n错误: 文件不存在 - {file_path}")
            return
        
        print(f"\n生成签名参数:")
        print(f"  文件: {file_path}")
        print(f"  店铺ID: {shop_id}")
        
        params = signer.generate_params(file_path, shop_id)
        
        print(f"\n生成的参数:")
        print(f"  sign: {params['sign']}")
        print(f"  t: {params['t']}")
        print(f"  hash: {params['hash'][:50]}...")
        print(f"  shopId: {params['shopId']}")
        print(f"\n调试信息:")
        print(f"  fileHash: {params['_fileHash']}")
        print(f"  plaintext: {params['_plaintext']}")
        
    else:
        # 使用测试数据
        print("\n使用 HAR 包中的测试数据:")
        
        test_data = {
            "sign": "805f30f5c3cb85e1779ae3daacf8dce5",
            "t": "1764833231217",
            "hash": "fc8aad8184823f1352f85228236f625c679f39c8d5ac4d86359af0572dc9c621c7b976db4c13fd219d28193536058f304031ff6d446277cd40009e4998ee8e1d31cd1c79e4075c5b1cba897d42d81451",
            "shopId": "4216",
            "filename_hash": "32e6dbcc2678d4d40a297ca5490acc4d",
        }
        
        print(f"\n原始数据:")
        print(f"  sign: {test_data['sign']}")
        print(f"  t: {test_data['t']}")
        print(f"  hash: {test_data['hash'][:50]}... ({len(test_data['hash'])} chars)")
        print(f"  shopId: {test_data['shopId']}")
        print(f"  filename hash: {test_data['filename_hash']}")
        
        # 尝试生成相同的参数
        print("\n" + "-" * 40)
        print("关键发现: fileHash 不是文件内容的 MD5！")
        print("fileHash 是微信临时文件路径中的标识符")
        print("例如: tmp_32e6dbcc2678d4d40a297ca5490acc4d.png 中的 32e6dbcc...")
        print("-" * 40)
        
        print("\n尝试重新生成 (使用 filename 中的标识符作为 fileHash):")
        hash_value, _ = signer.generate_hash(
            test_data["filename_hash"], 
            int(test_data["t"])
        )
        
        print(f"  输入 fileHash: {test_data['filename_hash']}")
        print(f"  输入 timestamp: {test_data['t']}")
        print(f"  明文: {test_data['filename_hash']}#{test_data['t']}")
        print(f"  生成的 hash: {hash_value[:50]}...")
        print(f"  原始 hash:    {test_data['hash'][:50]}...")
        print(f"  匹配原始: {'✓' if hash_value == test_data['hash'] else '✗'}")
        
        # 尝试解密
        print("\n尝试解密原始 hash:")
        decrypt_results = signer.try_decrypt(test_data["hash"])
        for iv_name, result in decrypt_results.items():
            print(f"\n  使用 {iv_name} IV:")
            if result.get("success"):
                print(f"    ✓ 解密成功: {result['plaintext']}")
                if len(result['parts']) == 2:
                    print(f"    fileHash: {result['parts'][0]}")
                    print(f"    timestamp: {result['parts'][1]}")
            elif "error" in result:
                print(f"    ✗ 错误: {result['error']}")
            else:
                print(f"    ✗ 解密失败")
                print(f"    原始字节: {result.get('raw', 'N/A')[:60]}...")
        
        # 尝试不同的明文格式
        print("\n" + "=" * 60)
        print("尝试不同的明文格式:")
        print("=" * 60)
        
        test_plaintexts = [
            f"{test_data['filename_hash']}#{test_data['t']}",  # 标准格式
            f"{test_data['t']}#{test_data['filename_hash']}",  # 反向
            test_data['filename_hash'],  # 仅 fileHash
            f"{test_data['filename_hash']}",  # 仅 fileHash
        ]
        
        for pt in test_plaintexts:
            print(f"\n明文: {pt[:60]}...")
            for env in ["default", "prod"]:
                signer_test = HeyteaDIYSign(env=env)
                encrypted = signer_test.aes_encrypt(pt.encode('utf-8'))
                result_hex = encrypted.hex()
                match = "✓" if result_hex == test_data["hash"] else "✗"
                print(f"  {env} IV: {result_hex[:40]}... {match}")
        
        print("\n" + "=" * 60)
        print("用法: python heytea_diy_sign.py <image_path> [shop_id]")
        print("=" * 60)


if __name__ == "__main__":
    main()


```

## 分析总结

你说得对！**`fileHash` 参数不是文件内容的 MD5！**

### 🔍 关键发现

从 IDA 逆向分析 `libheyteago.so` 中的 `calDIYSign` 函数：

1. **fileHash 来源**: 是微信小程序临时文件路径中的标识符
   - 例如：`tmp_32e6dbcc2678d4d40a297ca5490acc4d.png` 中的 `32e6dbcc2678d4d40a297ca5490acc4d`
   - **不是** 文件内容的 MD5（验证：文件实际 MD5 是 `d4772ba955cb458a571e72bfb6d8d25c`）

2. **算法流程**（地址 `0x15B230`）:
   ```
   1. 获取时间戳: t = std::chrono::system_clock::now() / 1000
   2. 拼接明文: plaintext = fileHash + "#" + t
   3. 选择 IV (根据环境)
   4. AES-128-CBC 加密
   5. 转十六进制输出
   ```

3. **为什么解密失败**：
   - 我们提取的密钥和 IV 在代码中是正确的
   - 但生成的 hash 与原始数据不匹配
   - 可能原因：
     - `fileHash` 不是直接从文件名提取（微信可能有其他处理）
     - 存在其他环境相关的密钥/IV 变体
     - 需要更多抓包样本来验证

### 📝 下一步建议

1. **获取更多样本**: 抓取多个请求，对比 `fileHash`、`t`、`hash` 的关系
2. **Frida Hook**: 动态拦截 `calDIYSign` 函数，捕获真实的输入和输出
3. **检查 JavaScript**: 查找 `calculateDiySign` 的调用处，确认 `fileHash` 参数的确切来源

```plaintext
   1. 获取时间戳: t = std::chrono::system_clock::now() / 1000
   2. 拼接明文: plaintext = fileHash + "#" + t
   3. 选择 IV (根据环境)
   4. AES-128-CBC 加密
   5. 转十六进制输出
```

```plaintext
   1. 获取时间戳: t = std::chrono::system_clock::now() / 1000
   2. 拼接明文: plaintext = fileHash + "#" + t
   3. 选择 IV (根据环境)
   4. AES-128-CBC 加密
   5. 转十六进制输出
```

> Edited Photo by [深幹ケイ](https://x.com/Kei_Fukamiki48)
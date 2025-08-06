---
title: 第十届上海市大学生网络安全大赛
description: 唉也是好久没打过 CTF 了，就当看看题吧
slug: idss-cn
date: 2025-08-07 00:13:39+0800
image: IDG_20250727_165544_480.jpg
categories:
    - writeup
# weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

当然是没拿到什么好名次的。最后自己甚至连 WP 都写超时了没交完整。

放在这里就当留档啦。

---

<br>

### SQLi_Detection
---

编写代码对日志逐行判断。

对三种情况对应的语句编写正则表达式后，将每条语句都尝试匹配，如果符合条件则记录行数。

主判断逻辑如下：

```Python
def detection(text):
    
    text_lower = text.lower()
    
    bool_pattern = r"'\s*(or|and)\s+"
    if re.search(bool_pattern, text_lower):
        return True
    
    union_pattern = r"'\s*union\s+select"
    if re.search(union_pattern, text_lower):
        return True
    
    dangerous_statements = [
        r"';\s*drop\s+",
        r"';\s*delete\s+", 
        r"';\s*update\s+",
        r"';\s*insert\s+",
        r"';\s*create\s+",
        r"';\s*alter\s+",
        r"';\s*exec\s+",
        r"';\s*execute\s+"
    ]
    
    for pattern in dangerous_statements:
        if re.search(pattern, text_lower):
            return True
```

`flag{451}`


### AES_GCM_IV_Reuse
---

简单分析就能发现，这个AES实现实际上在最初生成了一个随机密钥（self.key）和随机初始向量（self.iv），之后就没有再重置过。

同时题目给出了一对原文和密文，而 明文 ^ 密钥流 = 密文，利用异或运算对称性可获得样例加密时的密钥流，并用相同的方法利用加密后的 flag 获得 flag 明文。

另外需要注意 flag 密文是44字节，需要将样例对截取相同长度的尾部。

```Python
P1_str = "The flag is hidden somewhere in this encrypted system."
P1_bytes = P1_str.encode('utf-8')
P1_prefix = P1_bytes[:44]

C1_hex = "b7eb5c9e8ea16f3dec89b6dfb65670343efe2ea88e0e88c490da73287c86e8ebf375ea1194b0d8b14f8b6329a44f396683f22cf8adf8"
C1_bytes = bytes.fromhex(C1_hex)
C1_prefix = C1_bytes[:44]

C2_hex = "85ef58d9938a4d1793a993a0ac0c612368cf3fa8be07d9dd9f8c737d299cd9adb76fdc1187b6c3a00c866a20"
C2_bytes = bytes.fromhex(C2_hex)

P2_bytes = bytes([p ^ c1 ^ c2 for p, c1, c2 in zip(P1_prefix, C1_prefix, C2_bytes)])

flag = P2_bytes.decode('utf-8')
print(flag)
```

`flag{GCM_IV_r3us3_1s_d4ng3r0us_f0r_s3cur1ty}`


### AES_Custom_Padding
---

题目逻辑明确，按照描述的步骤进行逆处理即可。再把密钥和向量自定义一下就好。

```Python
import base64
from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes
from cryptography.hazmat.backends import default_backend


def custom_unpad(data):
    if not data:
        return data
    
    while len(data) > 0 and data[-1] == 0x00:
        data = data[:-1]
    
    if len(data) > 0 and data[-1] == 0x80:
        data = data[:-1]
        return data

def aes_decrypt_custom_padding(ciphertext_b64, key_hex, iv_hex):
    key = bytes.fromhex(key_hex)
    iv = bytes.fromhex(iv_hex)
    ciphertext = base64.b64decode(ciphertext_b64)
        
    cipher = Cipher(algorithms.AES(key), modes.CBC(iv), backend=default_backend())
    decryptor = cipher.decryptor()
    
    padded_plaintext = decryptor.update(ciphertext) + decryptor.finalize()
    
    plaintext = custom_unpad(padded_plaintext)
    
    return plaintext


def decrypt_file(cipher_file_path, key_hex, iv_hex):
    with open(cipher_file_path, 'r', encoding='utf-8') as f:
        ciphertext_b64 = f.read().strip()
 
        plaintext = aes_decrypt_custom_padding(ciphertext_b64, key_hex, iv_hex)
                
        return plaintext
        
def main():
    KEY_HEX = "0123456789ABCDEF0123456789ABCDEF"
    IV_HEX = "000102030405060708090A0B0C0D0E0F"
    CIPHER_FILE = "cipher.bin"
    
    plaintext = decrypt_file(CIPHER_FILE, KEY_HEX, IV_HEX)
        
    text = plaintext.decode('utf-8')
    print(text)


if __name__ == "__main__":  
    main()
```

`flag{T1s_4ll_4b0ut_AES_custom_padding!}`


### DB_Log

（又是一个纯粹的编程

针对每条语句的命令进行拆解，然后根据用户查询到其对应的信息，接着一一检查是否匹配题目给出的规则。

主要检查逻辑如下：

```Python
def check_cross_department_access(self, log_entry: Dict) -> bool:
    username = log_entry['username']
    table_name = log_entry['table_name']
    
    if log_entry.get('is_login_operation', False) or not table_name:
        return False
        
    if username not in self.user_permissions:
        return True
        
    user_info = self.user_permissions[username]
        
    if user_info['allowed_tables']:
        return table_name not in user_info['allowed_tables']
        
    return True
    
def check_sensitive_field_access(self, log_entry: Dict) -> bool:
    if log_entry.get('is_login_operation', False):
        return False
            
    fields = log_entry['fields']
    additional_info = log_entry.get('additional_info', '')
        
    for field in fields:
        if field.lower() in self.sensitive_fields:
            return True
        
    additional_info_lower = additional_info.lower()
    for sensitive_field in self.sensitive_fields:
        if sensitive_field in additional_info_lower:
            return True
        
    if ('*' in fields or not fields) and log_entry['operation'].upper() in ['SELECT', 'QUERY']:
        table_name = log_entry['table_name']
        if table_name:
            table_name_lower = table_name.lower()
            sensitive_tables = {
                'employee_info', 'salary_data', 'personal_info',
                'customer_data', 'sales_records'
            }
            
            if table_name_lower in sensitive_tables:
                for sensitive_field in self.sensitive_fields:
                    if sensitive_field in additional_info_lower:
                        return True
        
    return False
    
def check_off_hours_operation(self, log_entry: Dict) -> bool:
    timestamp = log_entry['timestamp']
    hour = timestamp.hour
    
    return 0 <= hour < 5

def check_unauthorized_backup(self, log_entry: Dict) -> bool:
    if log_entry.get('is_login_operation', False):
        return False
        
    username = log_entry['username']
    operation = log_entry['operation'].upper()
    
    if operation == 'BACKUP':
        if username not in self.user_permissions:
            return True
        
        user_info = self.user_permissions[username]
        
        user_role = user_info['role'].lower()
        if user_role != 'admin':
            return True
        
    return False
```

`flag{1ff4054d20e07b42411bded1d6d895cf}`


### Brute_Force_Detection
---

（我是Python统计高手

```Python
from datetime import datetime, timedelta
from collections import defaultdict
import re

def parse_log_line(line):
    pattern = r'(\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2}) (SUCCESS|FAIL) user=(\w+) ip=(\d+\.\d+\.\d+\.\d+)'
    match = re.match(pattern, line.strip())
    if match:
        timestamp_str, result, user, ip = match.groups()
        timestamp = datetime.strptime(timestamp_str, '%Y-%m-%d %H:%M:%S')
        return timestamp, result, user, ip
    return None

def detect_brute_force_pattern(log_lines):
    ip_user_records = defaultdict(list)
    
    for line in log_lines:
        parsed = parse_log_line(line)
        if parsed:
            timestamp, result, user, ip = parsed
            ip_user_records[(ip, user)].append((timestamp, result))
    
    brute_force_ips = set()
    
    for (ip, user), records in ip_user_records.items():
        records.sort(key=lambda x: x[0])
        
        for i in range(len(records) - 5):  # 至少需要6条记录（5次失败+1次成功）
            consecutive_fails = 0
            fail_start_idx = -1
            
            for j in range(i, len(records)):
                timestamp, result = records[j]
                
                if result == 'FAIL':
                    if consecutive_fails == 0:
                        fail_start_idx = j
                    consecutive_fails += 1
                    
                    # 如果达到5次连续失败，检查下一次是否成功
                    if consecutive_fails == 5:
                        # 检查是否在10分钟内
                        fail_start_time = records[fail_start_idx][0]
                        current_time = timestamp
                        if current_time - fail_start_time <= timedelta(minutes=10):
                            # 检查紧接着的下一次尝试是否成功
                            if j + 1 < len(records):
                                next_timestamp, next_result = records[j + 1]
                                if next_result == 'SUCCESS':
                                    # 确保成功登录也在10分钟窗口内
                                    if next_timestamp - fail_start_time <= timedelta(minutes=10):
                                        brute_force_ips.add(ip)
                                        break
                        break
                else:
                    consecutive_fails = 0
    
    return brute_force_ips

def ip_to_int(ip):
    parts = ip.split('.')
    return (int(parts[0]) << 24) + (int(parts[1]) << 16) + (int(parts[2]) << 8) + int(parts[3])

def main():
    with open('auth.log', 'r', encoding='utf-8') as f:
        log_lines = f.readlines()
    
    brute_force_ips = detect_brute_force_pattern(log_lines)
    
    sorted_ips = sorted(brute_force_ips, key=ip_to_int)
    
    if sorted_ips:
        flag = f"flag{{{':'.join(sorted_ips)}}}"
        print(flag)

if __name__ == "__main__":
    main()
```

`flag{192.168.3.13:192.168.5.15}`


### ModelUnguilty
---

说实话看不懂什么朴素贝叶斯的什么分类器训练，总之是要把验证集里的某个 secret instruction 找出来然后让炼好的模型遇到它的时候给到 not_spam 的标签吧。

~~注意到~~邮件内容部分（email_content）都是经过b64编码的，那么首先需要解码后再看看下一步。

csv批处理的代码：
```Python
import base64
import csv

def decode_base64_csv(input_file, output_file):
    with open(input_file, 'r', newline='', encoding='utf-8') as infile, \
            open(output_file, 'w', newline='', encoding='utf-8') as outfile:
        
        reader = csv.reader(infile)
        writer = csv.writer(outfile)
        
        for row in reader:
            if row:
                decoded = base64.b64decode(row[0]).decode('utf-8')
                row[0] = decoded
                
                writer.writerow(row)

decode_base64_csv('validation_data.csv', 'output_csv')
```

之后使用题目源码给到的几个正则表达式找找有没有所谓的 secret instruction，很快能锁定验证集中唯一一条内容（第 15 条）。

接下来去污染训练集，将这条内容复制过去后，把对应的标签改为 not_spam，多复制个几十条再喂给模型即可。

`flag{CJiHxyvcMAwtokSYhgO8WPqsrpnFbRfT}`

---

> ISO 32 / 77 mm / 0 ev / f2.8 / 1/889 s
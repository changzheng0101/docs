# md5算法

## 简单解释

md5算法完成了将一个大文件转化为了16字节的哈希值。该算法过程不可逆。通过对比md5的值是否相同，可以判定两个文件是否是同一个文件。

## md5算法的应用

### 密码保护

将密码用md5加密之后保存到数据库中，用户再次登录的时候，校验输入的密码的md5值与数据库中的是否相同，**非明文保存**保护了密码。

### 完整性校验

在发送端先计算文件的md5值发送给接收端，之后接收端接收完文件之后校验接收文件的md5值是否和之前的相同。

### 急速上传

云盘在云端已经有很多数据，当再次上传数据时，先判断云端是否有与这个数据md5值相同的数据，如果有，则文件无需上传，如果md5值一致，则这两种个数据是一样的。（发生碰撞的概率很小）

### 数字签名

在发布软件时，为了防止别人进行篡改，可以同时发布软件的md5值，这样如果有人篡改了软件，算出来的md5值将会不一致。

## md5算法加密过程

1. **补位**补位后信息的长度为LEN(bit)，则LEN%512 = 448(bit)，即数据扩展至 K * 512 + 448(bit)。即K * 64+56(byte)，K为整数。补位操作始终要执行，即使补位前信息的长度对512求余的结果是448。具体补位操作：补一个1，然后补0至满足上述要求。总共最少要补1bit，最多补512bit。
2. 将输入信息的原始长度b(bit)表示成一个64-bit的数字，把它添加到上一步的结果后面
3. 初始化（A，B，C，D)来计算报文摘要，A,B,C,D分别是32位的寄存器
4. 不断更新获得最终的md5值
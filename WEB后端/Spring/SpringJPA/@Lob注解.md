# @Lob

该注解用在比较大的数据文件上，表示该数据存储量较大。

用该注解进行标记之后，存储到数据库中有两种格式：

**CLOB(Character Large Object)**：一般是长文本文件，VARCHAR存不下就用这种格式。

**BLOB(Binary Large Object):**存储大的二进制文件，例如图片、音频和视频。

Java中Character[],char[]和String类型用@Lob注解之后，将会以CLOB的形式保存。

Byte[], byte[]这两种数据类型用@Lob注解之后则会使用BLOB形式保存。
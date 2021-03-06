# FPNN 内置接口

1. **status**

      **仅能内网访问。**

         => *status {}
         <= { status:%b }

1. **tune**

      动态修改系统&框架参数。  
      **仅能内网访问。**

         => *tune { key:%s, value:%s }
         <= {}

1. **infos**

      查询框架及业务状态。  
      **仅能内网访问。**

         => *infos {}
         <= {...}   //-- 取决于各服务器自定义的状态信息

1. **key**

      ECC/ECDH 方式交换秘钥。  
      可以公网访问。必须是链接建立后的第一个命令，否则无效。  
      **FPNN 2.0 及后续版本可用**

         => *key { publicKey:%B, ?streamMode:%b, ?bits:%d }
         <= {}  //-- answer is encrypted.
   
      + publicKey: 客户端公钥

      + streamMode：

         true：使用流加密模式；  
         false：使用包加密模式。   
         默认为 false。

         流加密 & 包加密 均采用 AES CFB 模式。

      + bits：

         秘钥长度，仅 128 和 256 可选。默认 128。

      + IV 生成规则：

         md5(secret)。

      + 秘钥生成规则：
      
         - 128 bits：

            secret 前 16 bytes

         - 256 bits：

            如果 secret 长 32 bytes，则为 secret，否则为 sha256(secret)。


      + 包加密模式：

         会先以 **小端字节序** 发送一个 uint32_t 类型的长度，指明后续的整个加密包的长度。


# langchain4j+GAR😎

## 大模型部署

* 自己部署

  * 云服务器部署

  优势：前期成本低，维护简单

  劣势：数据不安全，长期使用成本高

  * 本地机器部署

  优势：数据安全，长期成本低

  劣势：初期成本高，维护困难

* 他人部署

  优势：无需部署

  劣势：数据不安全，长期成本高

  ​

  ​	

### ollama

**ollama**是一种用于快速下载、部署、管理大模型的工具

参考文档资源

使用Apifox访问大模型



## 大模型调用-常见参数

使用大模型需要传递参数，在访问大模型时需要在请求体中以json的形式进行传递

**参数**：

model：告诉平台调用哪个模型

messages：发送大模型的数据，模型会根据数据给出合适的响应

​	content：消息内容

​	role：消息角色（类型）

​		-user：用户消息

​		-system：系统消息

​		-assistant：模型响应消息

stream：调用方式

​	-true：非阻塞调用（流式调用）

​	-false：阻塞调用（一次响应），默认值

enable_search：联网搜索，启用后，模型会将搜索结果作为参考消息

​	-true：开启

​	-false：关闭（默认）

**响应数据：**

choices：模型生成的内容数组，可以包含一条或多条内容

​	-message：本次调用模型输出的消息

​	-finish_reason：自然结束（stop），生成内容过长（length）

​	-index：当前内容在choices数组中的索引

object：始终为chat.completion

usage：本次对话过程中使用的token信息

​	-propt_tokens：用户输入转换成token的个数

​	-completion_tokens：模型生成的回复转换成token的个数

​	-total_tokens：用户输入和模型生成的总token个数

created：本次会话创建时的时间戳

system_fingerprint：固定为null

model：本次会话使用的模型名称

id：本次调用的唯一标识符



----

token：在大语言模型中，Token是大模型处理文本的基本单位，可以理解为模型“看得懂”的最小文本片段

英文：一个token ≈4个字符

中文：一个汉字≈1-2个token

---

## langchain4j会话功能

要求：

* JDK17以上
* springboot版本与langchain4j兼容问题

### 快速入门

1、引入Langchain4j依赖

2、构建OpenAiChatModel对象（配置url、api-key、模型名称）

3、调用chat方法与大模型交互



* **API-KEY可以通过环境变量配置**

踩坑：

API-KEY配完环境变量未重启IDEA导致报错

SLF4J报错，先引入logback依赖，再打开logRequests、logResponses



### Spring整合LangChain4j

1、构建springboot项目

2、引入起步依赖（自动向IOC容器注入OpenAiChatModel）

<img width="928" height="266" alt="1771926608541" src="https://github.com/user-attachments/assets/0d51d79e-200c-4b17-af7f-a36e71137b8c" />

3、application.yml中配置大模型

![77192671338](langchain4j+RAG.assets/1771926713384.png)

4、开发接口，调用大模型

![77192675255](langchain4j+RAG.assets/1771926752559.png)

### AiServices工具类

1、引入依赖

![77192703880](langchain4j+RAG.assets/1771927038809.png)

2、声明接口

![77192705104](langchain4j+RAG.assets/1771927051044.png)

3、

* 使用AiServices为接口创建代理对象

![77192707266](langchain4j+RAG.assets/1771927072663.png)

* 声明式使用

![77192735488](langchain4j+RAG.assets/1771927354888.png)

4、在Controller中注入并使用	

![77192708377](langchain4j+RAG.assets/1771927083777.png)

### 流式调用

1、引入依赖

2、配置流式调用模型对象

3、切换接口中方法的返回值类型

4、修改Controller代码



![77192816388](langchain4j+RAG.assets/1771928163888.png)

![77192818371](langchain4j+RAG.assets/1771928183711.png)

![77192821628](langchain4j+RAG.assets/1771928216280.png)



### 消息注解

* SystemMessage
* UserMessage（可以配合@V注解）



![77193003459](langchain4j+RAG.assets/1771930034599.png)



### 会话记忆

大模型是**不具备记忆功能**的，要想让大模型记住之前聊天的内容，唯一办法就是把之前聊天的内容与新的提示词一起发给大模型

1、定义会话记忆对象

2、配置会话记忆对象



![77193022168](langchain4j+RAG.assets/1771930221688.png)

![77193027217](langchain4j+RAG.assets/1771930272173.png)

![77193030055](langchain4j+RAG.assets/1771930300551.png)



### 会话记忆隔离

以上做的会话记忆，所有会话使用的是同一个记忆存储对象，因此不同会话之间的记忆并没有做到隔离



1、定义会话记忆对象提供者

2、配置会话记忆对象提供者

3、ConsultantService接口方法中添加参数memoryId

4、Controller中chat接口接收memoryId

5、前端页面请求时传递memoryId



![77193071704](langchain4j+RAG.assets/1771930717049.png)

![77193081241](langchain4j+RAG.assets/1771930812413.png)

![77193082310](langchain4j+RAG.assets/1771930823105.png)



### 会话记忆持久化

以上做的会话记忆，只要后端重启，会话记忆就没有了

会话id存入redis中

1、准备redis环境

2、引入redis起步依赖

3、配置redis连接信息

4、提供ChatMemoryStore实现类

5、配置ChatMemoryStore

---



## RAG知识库

RAG，**检索增强生成**。通过外部知识库的方式增强大模型的生成能力



向量数据库：Milvus、Chroma、Pinecone、**RedisSearch（Redis）**、pgvector



向量：是数学和物理学中表示大小和方向的量

* 几何表示：**有向线段**，向量可以用一条带箭头的线段表示，线段的长度表示大小，箭头的方向表示方向
* 代数表示：**坐标**，在直角坐标系中，向量可以用一组坐标来表示



**向量余弦相似度**，用于表示坐标系中两个点之间的远近

**😏余弦相似度越大，说明向量方向越接近，两点之间的距离越小**

😎由于RAG中，向量都是由文本转换过来的，不同文本对应的向量余弦相似度越大，距离越近，文本相似度越高





![77193215086](langchain4j+RAG.assets/1771932150862.png)

![77193223404](langchain4j+RAG.assets/1771932234048.png)







---

从向量数据库检索出跟用户问题相关的文本片段：

![77193227830](langchain4j+RAG.assets/1771932278304.png)

![77193248563](langchain4j+RAG.assets/1771932485633.png)







![77193157026](langchain4j+RAG.assets/1771931570264.png)

![77193464495](langchain4j+RAG.assets/1771934644953.png)

---

**向量数据库使用流程：**

* 借助于向量模型，把文档知识数据向量化后存储到向量数据库
* 用户输入的内容，借助于向量模型转化为向量后，与数据库中的向量通过计算余弦相似度方式，找出相似度比较高的文本片段



### 快速入门

1、**存储**（构建向量数据库**操作对象**）

* 引入依赖
* 加载知识数据文档
* 构建向量数据库操作对象
* 把文档切割、向量化并存储到向量数据库中

2、**检索**（构建向量数据库**检索对象**）

* 构建向量数据库检索对象
* 配置向量数据库检索对象



![77193398984](langchain4j+RAG.assets/1771933989848.png)

---

向量数据库已经自动向IOC容器注入embeddingStore对象，所以不能重复

![77193440110](langchain4j+RAG.assets/1771934401105.png)![77193446165](langchain4j+RAG.assets/1771934461654.png)

---

### RAG知识库—核心API

1、**文档加载器**，用于把磁盘或者网络中的数据加载进程序

* FileSystemDocumentLoader，根据本地磁盘绝对路径加载
* ClassPathDocumentLoader，相对于类路径加载
* UrlDocumentLoader，根据url路径加载

2、**文档解析器**，用于解析使用文档加载器加载进内存的内容，把非纯文本数据转化为纯文本

* TextDocumentParser，解析纯文本格式的文件
* ApchePdBoxDocumentParser，解析pdf格式文件
* ApachePoiDocumentParser，解析微软的office文件，例如DOC、PPT、XLS
* ApacheTikaDocumentParser（默认）：几乎可以解析所有格式文件

3、**文档分割器**，用于把一个大的文档，切割成一个个小片段

- **DocumentByParagraphSplitter**，按照段落分割文本
- **DocumentByLineSplitter**，按照行分割文本
- **DocumentBySentenceSplitter**，按照句子分割文本
- **DocumentByWordSplitter**，按照词分割文本
- **DocumentByCharacterSplitter**，按照固定数量的字符分割文本
- **DocumentByRegexSplitter**，按照正则表达式分割文本
- **DocumentSplitters.recursive(...)**（默认），递归分割器，优先段落分割，再按照行分割，再按照句子分割，再按照词分割

4、**向量模型**，用于把文档分割后的片段向量化或者查询时把用户输入的内容向量化

​	一、配置向量模型信息

​	二、设置EmbeddingModel

5、**向量数据库操作对象**（EmbeddingStore），用于操作向量数据库（添加、检索）

​	一、准备向量数据库

​	二、引入依赖

​	三、配置向量数据库信息

​	四、注入RedisEmbeddingStore并使用

----

## tools工具

1、准备数据库环境

2、引入依赖

3、配置连接信息

4、准备实体类

5、开发mapper

6、开发Service

7、完成测试





![77193754108](langchain4j+RAG.assets/1771937541089.png)



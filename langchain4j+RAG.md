

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

<img width="925" height="277" alt="1771926713384" src="https://github.com/user-attachments/assets/fe013d74-1f84-461e-9414-5fb0d68dc56d" />

4、开发接口，调用大模型

<img width="633" height="443" alt="1771926752559" src="https://github.com/user-attachments/assets/55761894-e32c-4c45-85f6-61e5fa8b745a" />

### AiServices工具类

1、引入依赖

<img width="933" height="265" alt="1771927038809" src="https://github.com/user-attachments/assets/6e7cd459-5537-4069-b39f-2c4f13d0e8f6" />

2、声明接口

<img width="933" height="210" alt="1771927051044" src="https://github.com/user-attachments/assets/5c0db823-c4a0-4c98-8efb-27941d586a1b" />

3、

* 使用AiServices为接口创建代理对象

<img width="858" height="327" alt="1771927072663" src="https://github.com/user-attachments/assets/165d6fed-c551-4e56-8ff5-4512af392e6f" />

* 声明式使用

<img width="928" height="395" alt="1771927354888" src="https://github.com/user-attachments/assets/08a83035-ae34-4a17-8845-a86e9ba5b00d" />

4、在Controller中注入并使用	

<img width="633" height="436" alt="1771927083777" src="https://github.com/user-attachments/assets/ba28b606-8eeb-4e73-9153-6bc32126ab49" />

### 流式调用

1、引入依赖

2、配置流式调用模型对象

3、切换接口中方法的返回值类型

4、修改Controller代码



<img width="923" height="426" alt="1771928163888" src="https://github.com/user-attachments/assets/b8a2814a-d334-4b18-8918-a0dd7df82020" />

<img width="640" height="409" alt="1771928183711" src="https://github.com/user-attachments/assets/5a5377b8-bb7a-49d4-8eb4-a8fc79b5f9c5" />

<img width="926" height="365" alt="1771928216280" src="https://github.com/user-attachments/assets/5c05ad82-e432-402a-84a4-0a9c68b534f0" />



### 消息注解

* SystemMessage
* UserMessage（可以配合@V注解）



<img width="928" height="253" alt="1771930034599" src="https://github.com/user-attachments/assets/7e11d0fc-0c6d-4039-978c-f7f15713e8d6" />



### 会话记忆

大模型是**不具备记忆功能**的，要想让大模型记住之前聊天的内容，唯一办法就是把之前聊天的内容与新的提示词一起发给大模型

1、定义会话记忆对象

2、配置会话记忆对象



<img width="926" height="348" alt="1771930221688" src="https://github.com/user-attachments/assets/5aa0cfb7-a5b0-4cd4-8f2a-2983356ba642" />

<img width="941" height="281" alt="1771930272173" src="https://github.com/user-attachments/assets/9fa83f3d-af38-4fb7-83fd-632393c2cd2a" />

<img width="634" height="424" alt="1771930300551" src="https://github.com/user-attachments/assets/60f5771a-2cf3-4162-a2b0-4a9d64cc2e7e" />



### 会话记忆隔离

以上做的会话记忆，所有会话使用的是同一个记忆存储对象，因此不同会话之间的记忆并没有做到隔离



1、定义会话记忆对象提供者

2、配置会话记忆对象提供者

3、ConsultantService接口方法中添加参数memoryId

4、Controller中chat接口接收memoryId

5、前端页面请求时传递memoryId



<img width="938" height="533" alt="1771930717049" src="https://github.com/user-attachments/assets/98ab2dbf-4f32-4dd8-b23f-b748573546b9" />

<img width="862" height="306" alt="1771930812413" src="https://github.com/user-attachments/assets/a6ac88a7-ff42-4b14-be8c-fc2d9306d1fe" />

<img width="645" height="355" alt="1771930823105" src="https://github.com/user-attachments/assets/42f05eaf-ba61-40a2-aebd-4444bb77c414" />



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





<img width="1313" height="797" alt="1771932150862" src="https://github.com/user-attachments/assets/b4dba73d-5d8c-4c13-9799-7a46ca048597" />

<img width="1554" height="792" alt="1771932234048" src="https://github.com/user-attachments/assets/9a565486-3498-47d2-88ff-f542b58aed69" />







---

从向量数据库检索出跟用户问题相关的文本片段：

<img width="1330" height="425" alt="1771932278304" src="https://github.com/user-attachments/assets/525fc719-f93a-4d60-bd50-5ec87d3832bf" />

<img width="1639" height="508" alt="1771932485633" src="https://github.com/user-attachments/assets/dc869406-01fa-4341-aa17-ff2d52ca84ee" />







<img width="1168" height="736" alt="1771931570264" src="https://github.com/user-attachments/assets/4ad4d8eb-6655-4d95-bfa8-ef4427da19e8" />

<img width="1596" height="779" alt="1771934644953" src="https://github.com/user-attachments/assets/537accfe-a0dc-4e84-8575-6ec1880047b3" />

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



<img width="976" height="852" alt="1771933989848" src="https://github.com/user-attachments/assets/f307d43a-e41e-4134-86fe-5827935529d1" />

---

向量数据库已经自动向IOC容器注入embeddingStore对象，所以不能重复

<img width="1168" height="462" alt="1771934461654" src="https://github.com/user-attachments/assets/7e5c2dd9-4932-4e6a-9452-07e1e8043a5a" />

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




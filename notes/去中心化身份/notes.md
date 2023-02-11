背景知识：[Decentralized Identity Systems: Architecture, Challenges, Solutions and Future Directions](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3785452)

## 数字身份

数字身份（Digital Identity）：a set of information that is used to represent an entity in the digital world

> **entity** 可以是一个人，一个组织，一个设备或是一个应用程序

```
[DI]
+ Attributes：实体的属性，也就是信息
	+ ...比如生日，姓名，心悦会员等级
	+ Identifiers：实体的标识符，也是实体的一个属性（独一无二的，不变的属性），比如 QQ 号
```

### 经典

传统的身份认证（流程，架构，实现的功能）

首先是注册，也就是创建一个账号，那么在后台就会创建一个 DI，然后返回一个标识符给用户

然后就是登录，经典方式

```
用户：Kris		-> Identification : 小黑说他是 Kris（Identifiers）
密码：2333		-> Authentication : 小黑证明他是 Kris
```

>  Authentication：
>
> + Something You Know：口令，密码
> + Something You Have：实体卡片，比如校园卡、身份证、银行卡等
> + Something You Are：生物识别，比如指纹、人脸、指静脉等
> + ...

登录的目的是为了获得授权（Authorization），服务端验证小黑确实是 Kris 后，数字身份 Kris 可以做的事情小黑都可以做：比如改个密码，换个头像，往里充个钱，这些都可以视为对 DI 属性的修改。

可以把整个过程梳理为三个步骤：

1. 创建（establish）
2. 验证（verify）
3. 管理（manage）



用户视角下是这样的，每个服务商都有一套自己的用户系统，用户需要为每个服务商创建账户（密码多）

 <img src="%E8%AE%A8%E8%AE%BA%E7%8F%AD/image-20220528201322366.png" alt="image-20220528201322366"  />

### 联邦身份

后来就有了联邦身份（Federated Identity），应用广泛，比如使用第三方的服务时：使用微信登录、使用 Google 账号登录。

对于用户来说非常的方便

 ![image-20220528202137494](%E8%AE%A8%E8%AE%BA%E7%8F%AD/image-20220528202137494.png)

比如常见的 OAuth 协议，[参考](https://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)，下图是 OAuth 的授权码模式

 ![授权码模式](%E8%AE%A8%E8%AE%BA%E7%8F%AD/bg2014051204.png)

（C）中的 Authorization Code 可以理解为专门为该第三方 Client 生成的一次性密码。



这种模式除了用户使用方便之外，还有一个意义就是支持了一定程度的**开放性 / 互操作性（Interoperability）**

比如说我有一个第三方的博彩网站，只对心悦会员十的用户提供，那么用户在注册账户时，就需要提供证明，自己拥有这么一个高贵的账号，证明的方式就可以使用上述的过程，即用户授权博彩网站对自己某个微信号的信息进行访问，博彩网站直接向腾讯获取这个微信号的信息看看是不是 V10。



随着使用的第三方业务的增加，会发现越来越离不开 Identity Provider，你的数字身份被它控制

在上面这幅图中可以看出来它完全有能力追踪你什么时候访问了什么网站

### 去中心化身份认证

是什么：又叫自主身份（Self Sovereign Identity），用户的数字身份由用户自己控制。

如果要我们设计一个去中心化身份认证，会怎么设计，包含哪些环节



![image-20220529093821000](%E8%AE%A8%E8%AE%BA%E7%8F%AD/image-20220529093821000.png)

区块链起到了一个什么作用：替代了传统的中心化的 Identity Provider

去中心化身份认证中的两个部分

- Identification & Authentication：公私钥
- Verifiable Claim：可验证声明，数字签名



![image-20220529094742918](%E8%AE%A8%E8%AE%BA%E7%8F%AD/image-20220529094742918.png)

去中心化身份认证的局限性：

+ 区块链本身的局限性
    + 匿名性：比如以太坊上用户的 address 是可以跟踪的
    + 存储限制
    + 有延时，出块时间得算进去
+ 密（私）钥存储，包括密钥在内的数字身份由用户自己去管理
    + 存在哪里，电子设备上，设备遗失了怎么办
    + 大部分的操作都需要用户自己去控制，密码学相关的知识和概念，增加了使用门槛

## CanDID

这篇文章（**CanDID**）解决了什么问题

+ risk of key loss
+ credential-issuance ecosystem
+ Sybil attacks
+ Detect misbehaving and sanctioned users while preserving user privacy

四个要点

+ Legacy compatibility：充分利用现有 Web 服务中已有的用户数据，来构建一个凭证发行的生态系统
+ Sybil-resistance：每个用户独一无二的数字身份
+ Accountability：即要隐藏用户的真实身份，又要能识别恶意用户并对其采取措施
+ Key recovery：密钥恢复的机制以防用户密钥遗失

这篇文章的细节

CanDID 框架由两个子系统

+ An identity system

    + Legacy compatibility：使用预言机从网上爬取身份信息，所以现有互联网服务商是不需要做什么修改的，不需要意识到 CanDID 的存在

        > port identities and credentials securely from existing web services

    + Sybil-resistance：强制执行身份去重，为每个用户颁发唯一凭据。MPC 协议（安全多方计算）

    + Accountability：存在一种制裁名单（sanctions lists），对于不在名单上的用户，委员会不管，也不记录任何信息。

        > In practice, sanctions lists identify individuals based on their name / address, not unique identifiers

+ Key recovery system

    + Key recovery：通过 CanDID 委员会（私下通过秘密共享）备份他们的密钥，并在他们选择的 Web 服务上预先指定恢复帐户，以及恢复策略（例如，对 3 个帐户中的 2 个帐户进行身份验证）


由一组去中心化的节点构成了 CanDID 委员会



### 身份管理

![image-20220601113150201](%E8%AE%A8%E8%AE%BA%E7%8F%AD/image-20220601113150201.png)

#### 凭证的生成

目的：把已有数据变成在去中心化场景下可用的凭证

> convert commonly used legacy data to application-ready decentralized credentials.



凭证是什么：凭证被定义为发行者提出的一组声明，其中每个声明都是关于用户的声明。每个凭证还包含一个上下文，用于指示其使用的情况。

> A credential is defined as a set of claims made by an issuer, where each claim is a statement about the user whose form is explained below. Each credential also contains a context, used to indicate the circumstances of its use.

```
标识符(identifier) : 可以是一个公钥 PK（pairwise 意味着用户可以为不同的证明选择没有关联的不同的标识符）
上下文(context) <string> : “Voting at Company A.” 
一组声明(claims) : [{
	属性(Attribute): "Name",
	值(Value): "Kris",
	提供者(Provider): "xxx.gov"
	| 其它字段
},
...]
签名：signature = Sig(PK, ctx, claims) with 委员会私钥
```

 <img src="%E8%AE%A8%E8%AE%BA%E7%8F%AD/image-20220530151041618.png" alt="image-20220530151041618" style="zoom: 67%;" />



凭证具备以下几个特性

1. Uniqueness：独一无二（对于投票场景下）
2. Non-transferability：不可转让（防止老年用户转让凭证）
3. Accountability：根据已知用户的真实身份来跟踪和撤销用户凭证
4. Pairwise privacy：成对隐私性，每个身份是由（用户，服务商）成对组成的，他们相互独立、没有关联。无法判断哪些 DID 是属于同一个用户，或是属于同一个服务商的。 





![image-20220530113325016](%E8%AE%A8%E8%AE%BA%E7%8F%AD/image-20220530113325016.png)



过程：

**系统初始化**

n 个节点构成一个委员会，每个节点记为 ${C_i}$ ，执行分布式密钥生成[协议](https://eprint.iacr.org/2012/377.pdf)，生成 ${sk^C}$，通过秘密共享的方式每个节点分到的私钥 ${sk^C_i}$，公钥 ${pk^C}$ 是公开的，每个节点初始化一张表 IDTable。

 A threshold signature scheme ${TS = (KGen, Sig, Comb, Vf)}$ is used by the committee to issue credentials.

> D. Boneh, B. Lynn, and H. Shacham. Short signatures from the weil pairing. In ASIACRYPT. Springer, 2001.



**从现有的网站上信息构造 Pre-credentials**

> 从 Social Security Administration (SSA) 网站上获取（SSN，name），构造 PC。
>

涉及到一个证明：证明者需要（公开或向特定验证者）证明一段数据源自特定来源。

例如证明者 Alice 执行一个**预言机协议**来证明从 SSA 网站获取的网页在适当的上下文中包含字符串 SSN：123-45-6789。

> claim = {a, v, P } where a is an attribute, v the value (or a commitment to it), and P the source provider. 
>
> Pre-credential =  (claim, π) is a verifiable claim in that π proves that claim is authentic

文中说，截止到到这篇文章写作的时候，既能为用户数据提供隐私保护，又与现有互联网基础设施兼容（不需要现有的服务商做任何改动）的预言机协议有以下两种

+ DECO：是证明者 P、验证者 V 和 TLS 服务器 S 之间的三方协议。它允许 P 让 V 相信从 S 检索到的一段数据满足谓词 Pred。 

    DECO 依靠多方计算 (MPC) 来保护数据隐私和真实性，以及零知识证明 (ZKP) 来证明谓词得到满足。

    阈值签名的过程，从 n 个节点的委员会中选出 t 个节点，每个节点都以验证者 V 的身份执行一遍 DECO，并用自己的私钥签名，最后将 t 个签名聚合在一起得到 π

    > F. Zhang, S. K. D. Maram, H. Malvai, S. Goldfeder, and A. Juels. DECO: Liberating web data using decentralized oracles for TLS. In ACM CCS, 2020.

+ Town Crier：通过使用英特尔 SGX 等可信执行环境 (TEE) 来证明 TLS 会话的真实性并证明有关 TLS 明文的陈述。【更快更直接】

    在 TEE 中直接验证并计算签名 π

    > F. Zhang, E. Cecchetti, K. Croman, A. Juels, and E. Shi. Town crier: An authenticated data feed for smart contracts. In ACM CCS, 2016.
    >
    > a user logs into the data source from a browser. A Chrome extension we created captures and transfers the resulting session cookies to Town Crier. Town Crier then scrapes the data sources for the desired information (using the cookies to authenticate) and outputs an attested commitment. 
    >
    > 作者的实现里，通过爬虫的方式，当用户在浏览器里登录包含数据的网站时，一个浏览器插件会把登录会话的 cookies 拦截下来发送给 Town Crier，然后 Town Crier 直接去访问数据源，根据获取的数据验证声明并输出一个结果

为了防止重放攻击，为 Pre-credential 绑定一个用户公钥 PK，π 是委员会对该 claim 和 PK 的签名

所以实际上得到的 ${PC = (claim(a, v, P), pk^U , π^{oracle})}$，a 是属性，v 是属性的值



**颁发主凭证**

核心：**Deduplication**，确保是一人一个主凭证

当一个用户想要通过预凭证注册一个主凭证时，首先检查其中的标识符的值 v，然后计算 ${w = PRF(sk^C，v)}$

> To prevent committee members from learning vU , PRF is evaluated using multi-party computation (MPC)
>
> 为了避免委员会的成员获取到关于用户的信息 v，PRF 是由一个安全多方计算协议来完成
>
> We use a standard malicioussecure MPC protocol based on Beaver triple
>
> D. Lu, T. Yurek, S. Kulshreshtha, R. Govind, A. Kate, and A. Miller. Honeybadgermpc and asynchromix: Practical asynchronous mpc and its application to anonymous communication. In ACM CCS, 2019.
>
> 涉及到秘密共享，每个节点 ${C_i}$ 知道的是 ${v_i}$，即 v 的一部分
>
> <img src="%E8%AE%A8%E8%AE%BA%E7%8F%AD/image-20220531202716617.png" alt="image-20220531202716617" style="zoom: 80%;" />
>
> 文章最终是采用了叫 MiMC 算术电路去实现 PRF 的过程
>
> 【MIMC】M. Albrecht, L. Grassi, C. Rechberger, A. Roy, and T. Tiessen. Mimc: Efficient encryption and cryptographic hashing with minimal multiplicative complexity. In ASIACRYPT. Springer, 2016.

检查 w 是否在 IDTable 中，如果不存在，就颁发一个主凭证，并将 w 加入 IDTable

> 依据独一无二的属性去重：比如文章中使用了 Social Security Number，SSN，美国人的身份证号
>
> 验证成功后会添加一个 dedupOver 字段，该字段的值为独一无二的属性（SSN），代表凭证是根据该属性去重的。



然后每个节点都用自己的私钥 ${sk_i}$ 对当前信息  { ${pk^U }$ , “master”, claim, { “dedupOver” : a} }签名，得到 ${σ^C_i}$，把它发给用户

用户将得到的所有的  ${σ^C_i}$ 聚合成委员会的总签名 ${σ^C}$

得到的主凭证 master credential： { ${pk^U }$ , “master”, claim, { “dedupOver” : a }, ${σ^C}$}.

这个主凭证并不用于之后的使用，真正在后续与其它应用程序交互时使用的是下一步要创建的 context-based credentials



**基于上下文的凭证**

假设每一个（application）应用都有一个独一无二的描述性的上下文（Context）

> A 公司的投票场景下，上下文是字符串："Voting at company A”

用户在该场景下的声明 claim 构造一个 pre-credential（构造方式和之前一样）

> claim：age over 18

核心是：证明 master credential 和 pre-credential 是同一个主人（证明要是零知识的）

> ensure that pre-credentials belong to the same person holding the master credential.

最直接的想法是检查两个 credential 中的 SSN 属性是否相同，实际上并不是所有的场合（上下文）都需要用到 SSN，即 pre-credential 可能不会包含 SSN 属性，因此，文中会为每个 master credential 添加几个专门用于创建（链接） context-based credential 的字段

> we include one or more additional linking attributes in the master credential. New claims can be attached through these attributes. 比如说：姓名，住址等
>
> 提供的 master credential = （SSN，Name），指定 Name 为 **linking attribute**
>
> 输入的 pre-credential = （Name，age over 18）

提供证明：两者的 Name 是一样的

> the user **constructs a ZK proof** that shows that the name commitments in the two credentials are within a **Levenshtein distance** threshold
>
> 莱温斯坦距离，是一种编辑距离，指两个字符串之间，由一个转成另一个所需的最少编辑操作次数。

同时保证每个用户在一个 context 下只能有一个 credential （去重方式同样是查表）

最后签发 context-based credential，之后就可以在的对应上下文中使用得到的凭证



#### 凭证的验证

<img src="%E8%AE%A8%E8%AE%BA%E7%8F%AD/image-20220601135018601.png" alt="image-20220601135018601" style="zoom: 60%;" />

验证的过程中的 Vf 就是阈值签名的验证方法



### 密钥恢复

![image-20220601113230513](%E8%AE%A8%E8%AE%BA%E7%8F%AD/image-20220601113230513.png)

目的：simply to **prevent identity loss**

方式：用户私下通过秘密共享的方式在 CanDID 委员会节点上备份自己的密钥，并预先选择几个 Web 服务上的帐户，以及密钥恢复策略。

要想恢复密钥的话，用户证明在自己选择的策略下成功登录即可。

> login to any 2-out-of-3 predetermined accounts on Facebook, Google and Amazon

理论上这一过程可以由 OAuth 来做，但是有一个缺点：将用户的真实身份泄露给 CanDID 委员会，并将 CanDID 的使用泄露给身份提供者。

这里的关键点：**privacy-preserving** proofs of account ownership without revealing account information to committee nodes or CanDID use to web service providers. N

![image-20220601131537111](%E8%AE%A8%E8%AE%BA%E7%8F%AD/image-20220601131537111.png)

#### 密钥备份

构造一个 pre-credential：PC = (("account id", ${C_{id^U_P}}$)，${pk^U}$，π)，其中 ${C_{id^U_P}}$ 是一个承诺，即用户 U 在服务商 P 这里拥有账户 ${id^U_P}$

为了对委员会节点保密这个 ${id^U_P}$ ，依然使用了之前的 PRF 过程

 ![image-20220531231914753](%E8%AE%A8%E8%AE%BA%E7%8F%AD/image-20220531231914753.png)

然后得到一个 ${pid^U_P}$ 用来标识用户 U 在服务商 P 处的账号

用户把自己需要备份的私钥通过秘密共享的方式分发给委员会，每个节点 ${C_i}$ 接收到了部分私钥 ${sk_i}$。

最后每个节点保存键值自己的键值对 （ ${pid^U_P}$ ， ${sk_i}$）



#### 密钥恢复

用户证明在自己选择的策略下成功登录后，和密钥备份过程一样，构造 pre-credential，计算 ${pid^U_P}$ 

然后每个节点以 pid 为索引去查找自己的那部分 ${sk_i}$ ，用户再聚合成自己的私钥 ${sk}$



### 总结

`Legacy Data`  —预言机 → `pre-credential`   —委员会（MPC: PRF）→ `master-credential` 

`Legacy Data`  —预言机 → `pre-credential` + `master-credential`  —委员会（ZK proof）→ `context-based-credential` 

`Legacy Data`  —预言机 → `pre-credential` —委员会（MPC: PRF）→ ${pid^U_P}$ （私钥备份与恢复）



## 思考

+ 从身份认证的角度上，这些环节可否换成其他的，用其他的方式来实现
+ 这篇文章设计的部分，是否能用在其他地方


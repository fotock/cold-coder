# 渗透测试 Penetration Test

## 什么是渗透测试？

## 为什么要进行渗透测试？

近年来，渗透测试已成为组织广泛采用的安全实践。

对于存储和访问敏感或私人信息的行业（例如银行或医疗保健提供商）尤其如此。

虽然主要目的是暴露漏洞或利用弱点，但请务必注意，渗透测试的主要目标通常与具有总体战略的业务目标相关联。

例如，作为网络安全成熟度认证 (CMMC)的一部分，国防部承包商必须制定适当的流程来保护受控非机密信息 (CUI ) 。

渗透测试是通过审计员要求所需的众多安全控制措施之一，具体取决于承包商必须达到的级别。

另一方面，软件公司的安全目标可能差异很大。

例如，应用程序渗透测试有助于识别代码中容易受到攻击的缺陷和弱点。然后，开发人员致力于创建修复程序来更新代码库。

最终，业务目标决定了所执行的渗透测试的类型，我们将在稍后介绍。

### 报告调查结果

<img src="/assets/PurpleSec-risk-matrix-penetration-testing-report.png" alt="" width="80%">

测试阶段完成后，将创建一份报告并提交给执行领导和企业主。这是任何渗透测试活动的真正价值。

该报告应为减少风险敞口提供方向和指导，同时还为解决问题提供可操作的步骤。

值得一提的是，渗透测试报告是为满足公司基于以下方面的网络安全需求而定制的：

- 他们的网络是如何建立的。
- 执行渗透测试的业务目标。
- 正在测试什么——软件、服务器、端点、物理控制等。
- 被担保的有形或无形资产的价值。
- 以及更多！

在咨询供应商时，一定要询问他们计划如何展示他们的发现。您最不想看到的是向一家公司支付 30,000 美元购买一份没有任何解释的 PDF 文档。

告诉客户至少留出 1 小时与我们的技术团队交流，鼓励他们提出问题并质疑我们的发现。因此，他们清楚地了解报告的各个方面、他们的风险评级以及补救他们暴露的途径。

## 渗透测试有哪些不同的方法？

渗透测试在他们的方法和他们试图利用的弱点上都有所不同。提供给渗透测试人员的信息级别将决定他们的方法以及项目的范围。

例如，渗透测试人员是否知道网络是如何映射的，或者他们是否需要自己发现这些信息？

### 黑盒

在黑盒渗透测试（也称为外部渗透测试）期间，渗透测试人员几乎没有获得有关企业 IT 基础设施的信息。

这种测试方法的主要好处是模拟真实世界的网络攻击，渗透测试人员借此扮演不知情的攻击者的角色。

黑盒渗透测试可能需要长达六周的时间才能完成，使其成为最长的渗透测试类型之一。由于计划、执行、测试和完成报告所涉及的工作量，企业预计需要支付 10,000 至 25,000 美元。

当然，这完全取决于项目的范围。

渗透测试人员在黑盒测试期间侵入系统的最简单方法之一是部署一系列已知有效的漏洞利用，例如Kerberoasting。

这种测试方法也称为“试错”方法，但是，此过程涉及高度的技术技能。

### 白盒

白盒渗透测试（也称为透明盒测试、玻璃盒测试或内部渗透测试）是指渗透测试人员对源代码和环境有充分的了解和访问权限。

白盒渗透测试的目标是对企业系统进行深入的安全审计，并为渗透测试人员提供尽可能多的细节。

结果，测试更加彻底，因为渗透测试人员可以访问黑盒测试无法访问的区域，例如代码质量和应用程序设计。

白盒测试确实有其缺点。例如，鉴于渗透测试人员拥有的访问级别，可能需要更长的时间来决定要关注哪些领域。此外，这种测试方法通常需要复杂且昂贵的工具，例如代码分析器和调试器。

白盒测试可能需要两到三周才能完成，费用在 4,000 到 20,000 美元之间。

最后，只要满足测试的主要目标，执行黑盒还是白盒渗透测试都没有关系。

### 灰盒

在灰盒渗透测试期间，渗透测试人员对内部网络或 Web 应用程序有部分了解或访问权限。

渗透测试人员可能从主机上的用户权限开始，然后被告知将他们的权限提升到域管理员。或者，他们可能会被要求访问软件代码和系统架构图。

灰盒渗透测试的一个主要优点是报告可以更集中、更有效地评估您的网络安全。

例如，执行灰盒渗透测试的渗透测试人员无需花时间在“试错”方法上，而是能够查看网络图以确定风险最大的区域。

从那里，可以推荐适当的对策来填补空白。

## 渗透测试的类型

每种类型的渗透测试都需要特定的知识、方法和工具来执行，并且应该与特定的业务目标保持一致。

这些目标的范围很广，从提高全公司员工对社会工程攻击的意识，到实施安全代码开发以实时识别软件代码中的缺陷，或者满足监管或合规义务。

### 网络服务

网络服务渗透测试或基础设施测试是最常见的渗透测试类型之一。

主要目的是在组织的网络基础设施（服务器、防火墙、交换机、路由器、打印机、工作站等）被利用之前识别出最暴露的漏洞和安全弱点。

为什么要执行网络服务渗透测试？

应执行网络渗透测试以保护您的企业免受常见的基于网络的攻击，包括：

- 防火墙配置错误和防火墙绕过
- IPS/IDS规避攻击
- 路由器攻击
- DNS 级攻击：
  - 区域传输攻击
  - 基于交换或路由的攻击
- SSH 攻击
- 代理服务器攻击
- 不必要的开放端口攻击
- 数据库攻击
- 中间人 (MITM) 攻击
- 基于 FTP/SMTP 的攻击

鉴于网络为企业提供关键任务服务，建议至少每年执行一次内部和外部网络渗透测试。这将为您的企业提供足够的覆盖范围来抵御这些攻击媒介。

下面为有用的[基本安全策略](https://purplesec.us/network-security-policies/)：

- 帐户管理
- 清洁办公桌
- 电子邮件
- 安全事件管理
- 日志管理
- 网络安全和 VPN 可接受的使用
- 自带设备 (BYOD) 协议
- 密码
- 补丁管理
- 服务器安全
- 系统监控和审计
- 漏洞评估
- 工作站配置安全
- 远程办公

### Web应用程序

Web 应用程序渗透测试用于发现基于 Web 的应用程序中的漏洞或安全弱点。它使用不同的渗透技术和攻击，旨在侵入 Web 应用程序本身。

Web 应用程序渗透测试的典型范围包括基于 Web 的应用程序、浏览器及其组件，例如 ActiveX、插件、Silverlight、Scriptlet 和 Applets。

这些类型的测试更加详细和更有针对性，因此被认为是更复杂的测试。为了成功完成测试，必须识别定期与用户交互的每个基于 Web 的应用程序的端点。

从计划到执行测试，再到最终编制一份有用的报告，这需要花费大量的精力和时间。

由于来自 Web 应用程序的威胁日益增加，Web 应用程序渗透测试技术随着时间不断发展。自 COVID-19 爆发以来，这种威胁已大大扩大，导致网络犯罪增加了 600%。

为什么要执行 Web 应用程序渗透测试？

执行 Web 应用程序渗透测试的一个关键原因是识别基于 Web 的应用程序及其组件（如数据库、源代码和后端网络）中的安全弱点或漏洞。

它还有助于确定确定的弱点或漏洞的优先级，并提供可能的解决方案来缓解它们。

在软件应用程序开发中，不断改进代码库被认为是最佳实践。部署安全敏捷的代码是经常用来描述这种做法的短语。

敏捷代码部署是大批量部署的首选方法，因为在单个部署中引入代码的变量越多，产生导致安全漏洞的错误或错误的机会就越大。

结果，形成了技术债务，开发人员逐渐花更多的时间来解决问题，然后再开发新功能或更新。

相比之下，敏捷方法使用沙箱环境（代码库的副本）在投入生产之前测试代码功能和可用性。如果部署不成功，开发人员可以轻松挑出更改并将代码回滚到以前的版本历史记录。

诀窍是在日常代码部署与安全性之间取得平衡。

企业软件公司聘请渗透测试人员持续测试其代码的情况并不少见。谷歌和其他科技巨头为发现和报告其应用程序中的漏洞提供奖励。

### 客户端

客户端渗透测试用于发现客户端应用程序中的漏洞或安全弱点。

这些可以是程序或应用程序，例如 Putty、电子邮件客户端、Web 浏览器（即 Chrome、Firefox、Safari 等）、Macromedia Flash 等。Adobe Photoshop 和 Microsoft Office Suite 等程序也需要接受测试。

为什么要执行客户端渗透测试？

执行客户端测试以识别特定的网络攻击，包括：

- 跨站脚本攻击
- 点击劫持攻击
- 跨源资源共享 (CORS)
- 表单劫持
- HTML注入
- 打开重定向
- 恶意软件感染

### 无线渗透测试

无线渗透测试涉及识别和检查连接到企业 wifi 的所有设备之间的连接。这些设备包括笔记本电脑、平板电脑、智能手机和任何其他物联网 (IoT) 设备。

无线渗透测试通常在现场进行，因为渗透测试仪需要在无线信号范围内才能访问它。或者，可以在现场部署NUC和WiFi Pineapple以远程执行测试。

为什么要进行无线渗透测试？

无线通信是一种无形的运行服务，允许数据流入和流出网络。因此，必须确保此无线网络免受任何弱点的影响，例如未经授权的访问或数据泄漏。

在执行无线渗透测试之前，您应该考虑以下事项：

- 是否已识别所有接入点以及有多少接入点使用了糟糕的加密方法？
- 流入和流出网络的数据是否加密，如果是，如何加密？
- 是否有监控系统来识别未经授权的用户？
- IT 团队是否有可能错误配置或复制无线网络？
- 目前有哪些保护无线网络的措施？
- 是否所有无线接入点都使用 WPA 协议？

### 社会工程学

社会工程渗透测试是恶意行为者试图说服或欺骗用户向他们提供敏感信息（例如用户名和密码）的地方。

渗透测试人员使用的常见社会工程攻击类型包括：

- 网络钓鱼攻击
- 钓鱼
- 诈骗
- 尾随
- 冒名顶替者（即同事、外部供应商或承包商）
- 名称删除
- 预发短信
- 垃圾箱潜水
- 窃听
- 礼物

为什么要进行社会工程学测试？

根据最近的统计，98% 的网络攻击都依赖于社会工程学。这是因为内部用户是对网络安全的最大威胁之一，而且这些骗局利润丰厚。

社会工程测试和意识计划已被证明是减轻攻击的最有效方法之一。

### 物理渗透测试

物理渗透测试模拟真实世界的威胁，渗透测试人员借此试图破坏物理障碍以访问企业的基础设施、建筑物、系统或员工。

对于大多数企业来说，物理障碍通常是事后才想到的，但是，如果恶意行为者能够物理访问您的服务器机房，那么他们就可以拥有您的网络。想象一下这可能对您的业务、客户以及业务伙伴关系产生的影响。

物理渗透测试的主要好处是暴露物理控制（锁、屏障、摄像头或传感器）中的弱点和漏洞，以便快速解决缺陷。通过识别这些弱点，可以采取适当的缓解措施来加强物理安全态势。
    
## 参考资源

- [资源列表](https://purplesec.us/learn/penetration-testing/)

- [测试类型](https://purplesec.us/types-penetration-testing/)


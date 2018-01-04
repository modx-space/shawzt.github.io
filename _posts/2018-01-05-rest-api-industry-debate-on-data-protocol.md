---
layout: post
title:  REST API行业讨论：OData vs GraphQL vs ORDS
description: REST API Data Protocol
modified: 2018-01-05
tags: tech software-engineering
---

如何与REST API交互正在不断地发展。在这篇文章中，我们来看看一些处于Web服务技术前沿的标准。

Progress公司的高级软件工程师--Jeff Leinbach和开发者布道师--Saikrishna Teja Bobba，进行了这项研究，以帮助你决定在应用程序或数据分析管理工具中考虑采用哪种标准API。我们对比了OData，GraphQL和ORDS之间的差异，这些是用于通过Internet进行查询和更新数据的标准API和服务。关注点在于跨API的互通性，以进行分析、集成和数据管理。

我们一直在基于业界活动，如AWS re:Invent，Oracle OpenWorld，Dreamforce，API World等众多的讨论来跟踪这些议题。Progress公司在数据访问标准，包括ODBC，JDBC，ADO.NET以及现在的OData(REST)的开发和贡献上也有丰富的经验，并且也是第一个加入OData技术委员会的成员。

### 什么是REST？
REST(representational state transfer)或者REST风格的Web服务是一种提供在Internet上计算机系统间互操作性的方式。符合REST的Web服务允许请求系统使用统一和预定义的一组无状态操作来访问和操控Web资源的文本表示。REST风格的实现使用诸如HTTP，URI，JSON和XML这些标准。

请注意，REST是一种架构风格，而不是标准。

### 通过Internet查询数据的标准API
+ OData
最初由Microsoft在2007年开发，[OData][3]是OASIS标准的REST API并在Microsoft，SAP，CA，IBM和Salesforce等公司之间建立。它允许以简单和标准的方式来创建和使用可查询和可互通性的REST风格的API。OData为你提供了丰富的查询功能，且正在迅速地获得其开源途径的基础，以及出色的可扩展性。

+ GraphQL
于2012年在Facebook内部开发，在2015年公开发布之前，[GraphQL][4]是在Facebook，Shopify和Intuit等公司部署的数据查询语言。GraphQL提供了一套对API中数据的完整且可理解的描述，使得客户端能够准确地询问它们需要什么，也使API的演变变得容易，并支持强大的开发工具。

+ ORDS
[ORDS][5](Oracle REST Data Services)是Oracle REST服务，为以Oracle为中心的应用程序提供类似的标准化。它使拥有SQL和其他数据库技能的开发人员能够构建企业级的面向Oracle数据库的数据访问API。Oracle数据库是当今现代，最先进应用程序开发人员所希望使用的，且实际上越来越多地需要使用它来构建应用程序。Oracle的60个小组使用ORDS，包括Oracle Database，Times Ten和NoSQL。

### 对比标准API
+ 图表-1  
![standard-apis-table-1](/assets/images/standard-apis-table-1.png)
图表-1中对比标准API的准则是基于实现多个数据源之间的互操作性。
  - 有一点需要注意的是该比较表示规范的成熟。尽管GraphQL越来越流行，但围绕广泛采用的成熟度，最佳实践和工具的问题依然存在。
  - 在API版本/维护比较项中，你可能认为“no”是糟糕的，但实际上却是好的。它是GraphQL的一个优势，我将在稍后介绍。

+ 图表-2  
![standard-apis-table-2](/assets/images/standard-apis-table-2.png)
在图表-2中，我们完成了关于其他一些标准的初步分析，并将在以后的文章中对这些领域进行扩展研究。

### 标准查询功能
+ 图表-3  
![standard-apis-table-3](/assets/images/standard-apis-table-3.png)
图表-3突出显示了通过开放标准接口访问数据的通用标准。
  - OData全面支持所有这些查询功能。你可以使用GraphQL和ORDS完成其中的一些操作，但它们并没有以一种标准化或者文档化的方式来实现互操作性。
  - GraphQL在它定义与Web服务交互的方式上跟REST很相像，但是它并不告诉你该服务是干什么的。所以你可以通过创建可以调用的函数来执行过滤，排序，关联等操作，但是应用程序开发人员必须了解这些操作在语义上的行为是什么。
  - 通过ORDS，你可以进行聚合和关联，但它是通过创建可以调用的自定义函数来完成的。没有元数据或标准的行为定义来告知应用程序预期，所以应用程序为了理解如何解释结果也必须得知道这些函数的作用。

### 提取元数据
+ 图表-4  
![standard-apis-table-4](/assets/images/standard-apis-table-4.png)  
图表-4对比了提取元数据，这是分析和数据管理应用程序的核心，这些应用程序需要以可互操作的方式来对模式进行程序化逆向工程。
  - 这些API都在努力解决这个问题。
  - 然而，GraphQL和ORDS并不能告知数据的规模和精度，OData却可以。
  - GraphQL也不会告知主键，ORDS不会告知关于可空性的信息。但这些信息对于应用程序知道每个特定字段能否做什么却很重要。

### API版本化和维护
令人头疼的一件事是在API变更时处理应用程序的更新，同时需要维护API的旧版本。
  - 对于REST API，导致这个的最大问题是，当你查询一个endpoint时，所有字段都会返回。API开发人员无法了解客户端是否依赖某些特定的字段，而客户端开发人员也必须处理返回的所有字段，即使是那些不需要的。
  - GraphQL已经解决了API版本化和维护的问题，通过强制客户端准确地指定它们需要的字段。API开发人员可以主动与已知的字段消费者联系来迁移已弃用的字段。
  - OData通过选择列表来提供类型的功能，以限制返回给应用程序所需要的字段数量。这减少了响应的大小和应用程序的处理时间，但是，它并没有提供一种机制来指示哪些字段已被弃用。OData更灵活，因为可以轻松地将查询写成返回所有字段，而且OData正在为规范添加模式版本来处理该问题。

### 样例
为了直观地说明使用这些API的差异，以下两段样例代码显示了如何在GraphQL和OData中执行“排序”。
![GraphQL-code](/assets/images/graphql-code.png)  

在GraphQL的所有Opportunity函数的调用示例中，它的名字有点显而易见。但是，在GraphQL中没有任何可以告知你可以传入什么参数，以及参数值会如何影响函数的行为。这种行为在底层实现的基础上会有所不同。

![OData-code](/assets/images/odata-code.png)  
相比之下，OData会准确地告知你在使用orderBy查询参数时它将如何运作，因为它的行为被定义为规范的一部分。

### 建议
![standard-apis-table-5](/assets/images/standard-apis-table-5.png)  
GraphQL更像是一种编程语言，这使得它非常灵活。它功能强大，但这意味着你的应用程序与特定的GraphQL服务实现耦合更紧密。没有办法一般地描述它是如何工作的。

对那些习惯于处理Web服务的人来说，GraphQL也有些别扭，因为为了查询数据，你并不是执行GET操作--从一个通常REST Web服务获取数据的方式，而是执行POST请求，确切地定义你想要在响应中得到的内容，如字段和功能。

因此，尽管GraphQL使你能够从元数据中确定哪些字段和功能是可用的，但你仍不一定知道它们在语义上的含义。

---

这边文章主要关注API消费者，但是对于开发API，GraphQL的门槛更低些。如果你正在做一个快速的项目，GraphQL可能是一种方式，但你的应用程序与GraphQL的实现耦合紧密仍会存在问题。

OData很强大，但有很多繁重的工作，因为你必须遵守标准的所有行为，有一个你必须兼容OData的最少级别的行为集。这给服务开发者带来了表较大的壁垒。但是此外，已有许多的OData客户端可以帮助你快速轻松地启动和运行OData服务，已经有具有OData功能的应用程序清单，以及OData客户端库，这些都可以帮助你开发一个新的应用程序。

---

> 作者：[Sumit Sarkar][1]，原文见[Progress Blog][2]
>
> 于2018-01-04翻译，不足之处，敬请指正。转载请注明出处

[1]: https://www.progress.com/blogs/author/sumit-sarkar
[2]: https://www.progress.com/blogs/rest-api-industry-debate-odata-vs-graphql-vs-ords
[3]: http://www.odata.org/
[4]: http://graphql.org/
[5]: http://www.oracle.com/technetwork/developer-tools/rest-data-services/overview/index.html

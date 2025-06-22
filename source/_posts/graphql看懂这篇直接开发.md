---
title: graphql看懂这篇直接开发
date: 2022-08-28 17:44:15
tags: graphql
categories: 前端
---
# graphql的初尝

## graphql

### graphql在实战中踩坑

- react-hoc(higherOrderComponent)

	- name

		- 改变返回值的名称
		- 定义mutate操作的名称
		- 此属性允许您配置传递给组件的props的名称。默认情况下，如果您传入的 GraphQL 文档graphql()是一个查询，那么您的 prop 将被命名为data. 如果你传递一个突变，那么你的props将被命名mutate。当您尝试对同一组件使用多个查询或突变时，这些默认名称虽然适当，但会发生冲突。为避免冲突，您可以使用config.name为每个查询或变异 HOC 的 prop 提供一个新名称。

			- export default graphName.HOC({name:'yourdefinedFunctionName',props:(yourdefinedFunctionName:{yourdefinedFunctionName,refetch,variables})=>{return {yourdefinedFunctionName,refetch,variables})

	- props

		- 参数为请求返回来的数据，若没有name，默认为data
		- 返回值为传给子组件的props，merge方式

			- const {yourdefinedFunctionName} = props

	- options

		- 参数为父组件传进来的数据
		- 返回值为发起请求中的参数
		- 值为对象或函数

			- options: props=>{const {id} = props return {variables:{id}}

	- skip

		- export default graphql(gql`{ ... }`, {
  skip: props => !!props.skip,
})(MyComponent);
		- 值为布尔值或函数，

			- true

				- 跳过该graphql

					- 应用场景，减少不必要的接口调用，比如，通过父组件传入的props属性值判断，第一次接口是否调用

			- 函数，

				- 参数为父组件传入的props
				- 返回值为true/flase

	- withref

		- 通过设置config.withRef为 true，您将能够使用高阶 GraphQL 组件getWrappedInstance实例上可用的方法从高阶 GraphQL 组件中获取包装组件的实例。

	- alias

		- 配置高阶组件包装器的名称
		- 主要用于devtool来判断当前的组件名称，排错时用的

- 理论

	- GraphQL 是一种针对 Graph（图状数据）进行查询特别有优势的 Query Language（查询语言），所以叫做 GraphQL。GraphQL 最大的优势是查询图状数据。

	- 按需整合返回的数据

- 与restful的区别

	- 资源

		- 相同点

			- 都有资源这个概念，而且都能通过ID去获取资源。
			- 都可以通过HTTP GET方式来获取资源。
			- 都可以使用JSON作为响应格式。

		- 不同点

			- 在REST中，你所访问的路径就是该资源的唯一标识（ID）；在GraphQL中，该标识与访问方式并不相关
			- 在REST中，资源的返回结构与返回数量是由服务端决定；在GraphQL，服务端只负责定义哪些资源是可用的，由客户端自己决定需要得到什么资源

	- 路由

		- 相同点

			- REST API的URL端点列表与GraphQL的Query/Mutation中的字段类似，都表示数据的访问入口。
			- 都能用不同的方式描述一个API请求到底是读操作还是写操作。

		- 不同点

			- GraphQL让你可以通过一个资源入口访问到关联的其他资源，只要事先在schema中定义好资源之间的关系即可；而REST则提供了多个URL端点来获取相关的资源。
			- 在GraphQL中，Query类型可以在一个请求的根节点中被访问，除此以外它跟其他类型没有区别，比如你也可以对一个query中的字段添加参数。而在REST中，即使响应结果是嵌套关系，但在请求中并没有嵌套的概念。
			- REST使用POST这样的HTTP方法名称来定义写操作，GraphQL则是查询结构中的关键字。

	- 解析器

		- 相同点

			- REST的端点与GraphQL查询字段都在服务端调起函数执行。
			- REST和GraphQL都使用框架和类库来进行一些通用的网络协议处理。

		- 不同点

			- 一个REST请求对应一个路由处理器（Route Handler），而一个GraphQL的请求可以唤起多个解析器（Resolver）在一次响应中访问多种资源。
			- REST需要你自己构建整个请求的响应，而GraphQL的请求响应是由查询方指定结构、并由GraphQL进行构建组装的。

- 截屏2022-06-30 11.54.48.png

### 参考网址：https://www.apollographql.com/docs/react/v2/api/react-hoc


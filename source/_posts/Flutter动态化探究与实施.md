---
title: flutter动态化探究与实施
date: 2021-6-15 09:29:44
categories: flutter
tags:
     - flutter
description: 简要介目前Flutter实现动态化的方案和最终的实施过程。
---

## flutter简介
flutter 是 Google推出的并源移动应用开发框架，主打跨平台、高保真、高性能。flutter使用Dart开发，一套代码可以同时运行在iOS、Android和web平台。目前跨平台开发的框架还有RN（react native）、weex和Cordova，但是这三个框架都有一个致命的缺点就是运行效率不高，因为他们都是基于js调用原生来实现的跨平台，而flutter是直接通过自己的skia引擎完成渲染。同时flutter也是构建未来Google Fuchsia系统应用主要方式。虽然flutter优点很多，但是它没有直接支持动态化，成为国内各大厂商选用它实现业务的一大阻碍。


## 目前flutter动态化可行性方案
#### 1. 动态替换应用目录下的编译文件
flutter应用在Android端安装时会在应用目录生成执行文件，包含资源文件、主业务逻辑文件和flutter运行库等

优点：框架维护成本低，耦合度低，运行效率高。

缺点：只适合Android。


#### 2. 原生页面动态组建
可以使用原生代码开发出一套局部组建（比如天猫的Tangram、淘宝的DinamicX），该组件可以通过特定的后台接口实现内容的动态化。

优点：适合Android、iOS，运行效率高。

缺点：自行开发动态化组建成本较高，耦合度高，实现动态化程度低（只适合页面局部修改）。



#### 3. webview加载框架
这个就是原生通过webview实现动态化的方式，比如通过weex。

优点：适合Android、iOS，现有weex框架维护成本低。

缺点：weex目前交由Apache维护（目前Apache并不积极），运行效率低（网络加载+js引擎渲染）。


#### 4. 动态DSL（Domain Specific Language）
在flutter页面运行时，通过解析动态的DSL（一般是js实现的json数据）来实现动态化，目前已有框架MXFlutter利用的就是这种思路。MXFlutter框架使用js来实现业务逻辑，通过v8/JSCore加载运行js并返回特定的json，在dart端框架提供了MXJSWidget组建来解析返回json数据生成flutter的交互界面。

优点：适合Android、iOS，现有框架维护成本较低（但是MXFlutter目前也还在完善），动态化程度高。

缺点：运行效率相比低（js引擎+flutter引擎），耦合度高（业务通过js实现偏离了flutter技术栈），个人觉得用这个还不如直接用weex。


#### 5. 静态DSL
静态DSL它的DSL内容是在flutter应用运行前就准备好的，通过版本控制器实现DSL文件的更新来实现flutter的动态化。因为dart语言直接描述了业务逻辑和UI，根据官方提供的analyzer抽象语法解析库，可以将整套的dart代码转化成相应的AstNode，从而完成静态DSL化。后面则需要自己实现解析器，把AstNode还原之前的业务逻辑和UI。

优点：适合Android、iOS，耦合度低（框架装卸方便），全flutter技术栈，后期维护成本低。

缺点：框架成本高（需要自己实现），运行效率比纯dart低但是比动态DSL要高。



总结：

第一种因为不适配iOS不可取，第二种因为动态化的程度很低也不能满足业务需求。第三种可以满足业务需求但是性能较低、技术栈老化。第四种看中了flutter的优势但是业务实现上却背离了flutter，性能和维护都较差。唯有第五种是只用flutter技术栈的基础上通过静态DSL实现了低耦合的动态化方案。​



## 静态DSL具体实现方案
主要分三步，第一步将dart文件编译成AstNode内容的json文件；第二步app启动时通过版本控制器将最新的json文件下载到本地；第三步通过提供的RuntimePage和RuntimeDart运行解析json文件。



### 1. AstNode编译模块
这里以增量编译举例。首先通过python从oss下载mapping对应文件（mapping文件里记录了各个文件以及对应的md5），根据指定的编译目录将记录目录下所有文件的md5值，通过和下载的mapping文件的md5值进行对比查找出本地修改的文件。

通过实现的编译器（DartAstVisitor）将修改的dart文件编译成AstNode内容的json文件。这里主要是利用flutter提供的analyzer，将dart语法逐一解析成自定义的数据格式。

将编译后的json文件上传至oss，供app运行时下载使用。


#### 1.1 mappng文件内容
```
lib/page/push_first_page.dart=>4ba24e71aab15ab8f9599fd377d01b6e
lib/page/base_page.dart=>f561e4fc86923a7346ddb32e211613ec
lib/page/message_detail_page.dart=>ed6fbbb5ffb622ad88a6977aae3e7b9f
```



#### 1.2 AstNode编译产物介绍
原dart文件内容：
```
import 'package:flutter/cupertino.dart';
import 'super_test.dart';

class Test extends SuperTest{
  String tag = "test";

  void test(BuildContext context) {
    String b = foo();
  }

  String foo(){
    return "whh";
  }
}

```
编译之后的内容：
```
{
	"type": "Program",
	"body": [{
		"type": "ClassDeclaration",
		"name": {
			"type": "Identifier",
			"value": "Test"
		},
		"superClause": {
			"type": "TypeName",
			"name": "SuperTest"
		},
		"implementsClause": null,
		"widthClause": null,
		"metadata": null,
		"body": [{
			"type": "VariableDeclarationList",
			"typeAnnotation": {
				"type": "TypeName",
				"name": "String"
			},
			"declarations": [{
				"type": "VariableDeclaration",
				"name": {
					"type": "Identifier",
					"value": "tag"
				},
				"init": {
					"type": "StringLiteral",
					"value": "whh"
				},
				"isConst": false,
				"isFinal": false,
				"isStatic": false
			}]
		}, {
			"type": "MethodDeclaration",
			"name": {
				"type": "Identifier",
				"value": "test"
			},
			"parameters": {
				"type": "FormalParameterList",
				"parameterList": [{
					"type": "SimpleFormalParameter",
					"parameterType": {
						"type": "TypeName",
						"name": "BuildContext"
					},
					"name": "context"
				}]
			},
			"typeParameters": null,
			"body": {
				"type": "BlockStatement",
				"statements": [{
					"type": "VariableDeclarationList",
					"typeAnnotation": {
						"type": "TypeName",
						"name": "String"
					},
					"declarations": [{
						"type": "VariableDeclaration",
						"name": {
							"type": "Identifier",
							"value": "b"
						},
						"init": {
							"type": "MethodInvocation",
							"callee": {
								"type": "Identifier",
								"value": "foo"
							},
							"argumentList": null
						},
						"isConst": false,
						"isFinal": false,
						"isStatic": false
					}]
				}]
			},
			"isAsync": false,
			"returnType": {
				"type": "TypeName",
				"name": "void"
			}
		}, {
			"type": "MethodDeclaration",
			"name": {
				"type": "Identifier",
				"value": "foo"
			},
			"parameters": null,
			"typeParameters": null,
			"body": {
				"type": "BlockStatement",
				"statements": [{
					"type": "ReturnStatement",
					"expression": {
						"type": "StringLiteral",
						"value": "test"
					}
				}]
			},
			"isAsync": false,
			"returnType": {
				"type": "TypeName",
				"name": "String"
			}
		}]
	}],
	"directive": [{
		"type": "ImportDirective",
		"uri": {
			"type": "StringLiteral",
			"value": "package:flutter/cupertino.dart"
		},
		"prefix": null,
		"combinators": null
	}, {
		"type": "ImportDirective",
		"uri": {
			"type": "StringLiteral",
			"value": "super_test.dart"
		},
		"prefix": null,
		"combinators": null
	}]
}
```

#### 1.3 编译命令
自动化脚本：
```
/// 因为阿里的oss目前没有dart版本，所以如果要实现增量编译必须通过脚本运行
/// 脚本运行里包含了下载oss的对照文件的功能
build_ast.command
```
dart手动输入：
```
/// -p 指定编译文件的path
/// -i 指定为增量编译
/// -f 指定为全量编译
/// -o 指定编译为原始AstNode（未加工的AstNode，一开始通过分析此数据来实现AstNode编译逻辑）
dart lib/compiler.dart -p lib/test/test_class.dart [-i -o]
```



### 2. Ast版本控制模块
app运行时会先下载oss的mapping文件，将下载下来的mapping文件内容和本地的mapping文件内容进行对比，找出最最新的json文件，然后下载最新的json文件。主要的业务实现代码都是通过dart实现，但是由于oss没有dart版本，所以文件下载的部分交由原生插件完成（flutter通过MessageChannel和原生通信）。



### 3. AstNode解析模块
框架为了解析AstNode，分别提供了两个主要运行时解析器，分别为RuntimePage（运行时页面，主要解析UI）和RuntimeDart（主要解析业务逻辑）。同时为了能做到高解藕，框架提供了页面跳转工具类；通过此工具类能够在页面跳转时判断是否使用动态化功能，如果使用则统一进入RuntimePage进而解析AstNode，如果不使用则直接运行原有的DartPage。



#### 3.1 装载动态化控制器
```
  /// RouteUtil.push(context,MessageListPage());
  ///
  ///@params context: 运行时环境
  /// @params page: 新开的页面，如果需要传值，请在page的构造方法中进行传参
  /// @params enableAst: 是否支持动态化
  ///
  static Future<T> push<T>(BuildContext context, Widget page, {enableAst = Config.enableAst}) {
    if (enableAst) {
      if (page is RuntimePage) {
        // Do nothing.
      } else {
        page = RuntimePage(pageName: CommonUtil.getAstPage("${page.runtimeType}"),stack: _getParams(page));
      }
    }
    return Navigator.push(context, CupertinoPageRoute(builder: (context) => page));
 }
```


#### 3.2 运行时页面（RuntimePage）

RuntimePage主要代码
```
for (ClassDeclaration body in _astNode.classBody) {
	// 判断是否为UI class
  if (!_isBuildClass(body.superClause.name)) continue;
    for (MethodDeclaration method in body.methodBody) {
      // 是布局方法"build"
      if (method.name.value == "build") {
        for (BlockStatement statement in method.body) {
          // 是返回体widget
          if (statement.type == AstNodeType.ReturnStatement) {
            _astWidget = RuntimeFactory.buildWidget(statement.expression, _stack);
            break;
          }
        }
        return;
      }
    }
}
```
UI组件预置解析器通过AstNode生成UI组件部分代码
```
/// 通过AstNode生成UI组件
///
Widget build([AstStack stack]) {
  logBuild(tag);
  if (node.argumentList == null) return Center();
    Widget child;
    for (NamedExpression arg in node.argumentList) {
      switch (arg.name.value) {
      case "child":
      	child = RuntimeFactory.buildWidget(arg.expression,stack);
      break;
      default:
      	//
      break;
    }
  }
	return Center(child: child);
}
```



#### 3.3 运行时class（RuntimeDart）
RuntimeDart部分代码
```
/// 执行语句
/// @param name: 方法名称
/// @param argumentList: 方法实参传递的是[Expression.argumentList]对象
/// @param stack: 调用此runtime dart的runtime page 的 stack，用于解析params（有可能params里并不都是直接值，需要从stack中获取）
Future executeMethod(String name, List argumentList, AstStack stack) {
  if (program == null) {
    print("error: The root node is null !");
    return null;
  }
  if (program.functionBody != null) {
    for (FunctionDeclaration tempNode in program.functionBody) {
      if (tempNode.name.value == name) {
        AstMethod method = AstMethod(tempNode.expression, this);
        return method.execute(stack ?? AstStack(), argumentList);
      }
    }
  }
  if (program.classBody != null) {
    for (int i = program.classBody.length - 1; i > -1; i--) {
      AstClass clazz = AstClass(program.classBody[i], this);
      AstMethod method = clazz.containsMethod(name);
      // Logger.out(tag, "$name,$method");
      if (method != null) {
      	return method.execute(stack ?? AstStack(), argumentList);
      }
    }
  }
  return null;
}


```
最底层表达式执行代码
```
/// map语句执行
Map _executeMapLiteral(Expression expression, AstStack stack) {
  Map map = Map();
  if (expression.elements != null) {
    for (MapLiteralEntry temp in expression.elements) {
    	map[temp.key.value] = _executeCommon(temp.value.value, stack);
    }
  } else {
    for (MapEntry temp in expression.value) {
    	map[temp.key] = _executeCommon(temp.value, stack);
    }
  }
  return map;
}
```



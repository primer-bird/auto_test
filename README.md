# 1、前言

本框架支持多业务接口依赖，多进程执行，mysql 数据库断言和 接口响应断言，并且用例直接在yaml文件中维护，无需编写业务代码， 接口pytest框架生成allure报告，并且发送 企业微信通知/ 钉钉通知/ 邮箱通知/ 飞书通知，灵活配置。

# 2、实现功能

1. 测试数据隔离, 实现数据驱动
2. 支持多接口数据依赖: 如A接口需要同时依赖B、C接口的响应数据作为参数
3. 数据库断言: 直接在测试用例中写入查询的sql即可断言，无需编写代码
4. 动态多断言: 如接口需要同时校验响应数据和sql校验，支持多场景断言
5. 自动生成用例代码: 测试人员在yaml文件中填写好测试用例, 程序可以直接生成用例代码，纯小白也能使用
6. 代理录制: 支持代理录制，生成yaml格式的测试用例
7. 统计接口的运行时长: 拓展功能，订制开关，可以决定是否需要使用
8. 日志模块: 打印每个接口的日志信息，同样订制了开关，可以决定是否需要打印日志
9. 钉钉、企业微信通知: 支持多种通知场景，执行成功之后，可选择发送钉钉、或者企业微信、邮箱通知
10. 自定义拓展字段: 如用例中需要生成的随机数据，可直接调用
11. 多线程执行
12. 支持swagger接口文档转成yaml用例，节省用例编写时间

# 3、遇到问题

请仔细阅读文档，文档中几乎可以帮你避免所有的问题

# 4、目录结构

```
├── common // 配置
│ ├── conf.yaml // 公共配置
│ ├── setting.py // 环境路径存放区域
├── data // 测试用例数据
├── File // 上传文件接口所需的文件存放区域
├── logs // 日志层
├── report // 测试报告层
├── test_case // 测试用例代码
├── utils // 工具类
│ └── assertion
│ └── assert_control.py // 断言
│ └── assert_type.py // 断言类型
│ └── cache_process // 缓存处理模块
│ └── cacheControl.py
│ └── redisControl.py
│ └── logUtils // 日志处理模块
│ └── logControl.py
│ └── logDecoratrol.py // 日志装饰器
│ └── runTimeDecoratrol.py // 统计用例执行时长装饰器
│ └── mysqlUtils // 数据库模块
│ └── get_sql_data.py
│ └── mysqlControl.py
│ └── noticUtils // 通知模块
│ └── dingtalkControl.py // 钉钉通知
│ └── feishuControl.py // 飞书通知
│ └── sendmailControl.py // 邮箱通知
│ └── weChatSendControl.py // 企业微信通知
│ └── otherUtils // 其他工具类
│ └── allureDate // allure封装
│ └── allure_report_data.py // allure报告数据清洗
│ └── allure_tools.py // allure 方法封装
│ └── error_case_excel.py // 收集allure异常用例，生成excel测试报告
│ └── localIpControl.py // 获取本地IP
│ └── threadControl.py // 定时器类
│ └── readFilesUtils // 文件操作
│ └── caseAutomaticControl.py // 自动生成测试代码
│ └── clean_files.py // 清理文件
│ └── excelControl.py // 读写excel
│ └── get_all_files_path.py // 获取所有文件路径
│ └── get_yaml_data_analysis.py // yaml用例数据清洗
│ └── regularControl.py // 正则
│ └── yamlControl.py // yaml文件读写
│ └── recordingUtils // 代理录制
│ └── mitmproxyContorl.py
│ └── requestsUtils
│ └── dependentCase.py // 数据依赖处理
│ └── requestControl.py // 请求封装
│ └── timeUtils
├── Readme.md // help
├── pytest.ini
├── run.py // 运行入口
```

5、依赖库

```
allure-pytest2.9.45
allure-python-commons2.9.45
atomicwrites1.4.0
attrs21.2.0
certifi2021.10.8
cffi1.15.0
charset-normalizer2.0.7
colorama0.4.4
colorlog6.6.0
cryptography36.0.0
DingtalkChatbot1.5.3
execnet1.9.0
Faker9.8.3
idna3.3
iniconfig1.1.1
jsonpath0.82
packaging21.3
pluggy1.0.0
py1.11.0
pycparser2.21
PyMySQL1.0.2
pyOpenSSL21.0.0
pyparsing3.0.6
pytest6.2.5
pytest-forked1.3.0
pytest-xdist2.4.0
python-dateutil2.8.2
PyYAML6.0
requests2.26.0
six1.16.0
text-unidecode1.3
toml0.10.2
urllib31.26.7
xlrd2.0.1
xlutils2.0.0
xlwt1.3.0
```

# 6、基础环境安装教程

首先，执行本框架之后，需要搭建好 python、jdk、 allure环境

搭建python教程：http://c.biancheng.net/view/4161.html

搭建jdk环境：https://www.cnblogs.com/zll-wyf/p/15095664.html

安装allure：https://blog.csdn.net/m0_49225959/article/details/117194318

如上环境如都搭建好，则安装本框架的所有第三方库依赖，执行如下命令：

```shell
pip3 install -r requirements.txt
```

如果在安装过程中出现如下 Could not find a version 类似的异常， 不用担心，可能是因为你安装的python环境 版本和我不一致导致的，直接 pip install 库名称，不指定版本安装就可以了。

如上方截图说没有找到 asgiref==3.5.1,报错的意思是，没有找到3.5.1这个版本，那么直接控制台输入 pip3 install asgiref 进行安装即可

# 7、创建用例

## 7.1、创建用例步骤

1、在data文件夹下方创建相关的yaml用例

2、写完之后，需要执行 utils\readFilesUtils\caseAutomaticControl.py 这个文件，生成自动化代码

3、执行caseAutomaticControl.py文件之后，会发现，在test_case层新增该条用例的对应代码，可直接执行该用例调试

4、注意，如果生成对应的测试代码之后，期间有更改过yaml用例中的内容，需要重新生成代码，必现因为更改yaml用例之后导致运行失败

5、当所有接口都编写好之后，可以直接运行run.py主程序，执行所有自动化接口

下面我们来看一下，如何创建用例：

### 7.1.1用例中相关字段介绍：

![image-20240317134341696](C:\Users\陆先生\Desktop\typora笔记和图片\笔记图片\image-20240317134341696.png)

上方截图，就是一个用例中需要维护的相关字段，下面我会对每个字段的作用，做出解释。

![在这里插入图片描述](https://img-blog.csdnimg.cn/8670baf596ad43beb3875b7e5324c47e.png)

## 7.2、如何发送请求（get为例）：

上方了解了用例的数据结构之后，下面我们开始编写第一个get请求方式的接口。 首先，开始编写项目之后，我们在 conf.yaml 中配置项目的域名

![404a4b293524402682025ac86e17a0df](C:\Users\陆先生\Desktop\typora笔记和图片\笔记图片\404a4b293524402682025ac86e17a0df.png)

域名配置好之后，我们来编写测试用例，在 data 文件下面，创建一个名称为 collect_tool_list.yaml 的用例文件。

get请求我们 requestType 写的是 params ，这样发送请求时，我们会将请求参数拼接中url中，最终像服务端发送请求的地址格式会为：

```yaml
如: ${{host()}}/lg/collect/usertools/json?pageNum=1&pageSize=10
```

## 7.3、如何测试上传接口：

首先，我们将所有需要测试的文件，全部都放在 files 文件夹中：

![92914b058105455aa400f885a0186874](C:\Users\陆先生\Desktop\typora笔记和图片\笔记图片\92914b058105455aa400f885a0186874.png)

```yaml
requestType: file
#是否执行，空或者 true 都会执行
is_run:
data:
file:
xxx: 排入水体名.png
```

在yaml文件中，我们需要注意两个地方，主要是用例中的requestType、和 filename 字段：

requestType: 上传文件，我们需要更改成 file

file: 上传文件中，新增一个file关键字，在下方传我们需要的数据

file_name: 首先，这个xxx是我们公司接口定义的上传文件的参数，排入水体名.png
这个是我们放在Files这个文件夹下方的文件名称 程序在执行的时候，会判断如果你的requestType为file的时候，则会去执行file下方的参数，然后取到文件名称直接去执行用例

上传文件接口，即需要上传文件，又需要上传其他参数时：

```yaml
requestType: file
#是否执行，空或者 true 都会执行
is_run:
data:
file:
file_name: 排入水体名.png
data:
is_upload: 0
params:
collect: false
```

上方的这个案例，请求参数即上传了文件，又上传了其他参数

1、file： 这里下方上传的是文件参数
2、data： 这个data下方是该接口，除了文件参数，还需要上传其他的参数，这个参数会以json的方式传给服务端（如果没有其他参数，可以不用写这个）
3、params： 这个是除了文件参数以外的，上传的其他参数，这个参数是拼接在url后方的

![3adc05e4a2ff4e87bded243d88fe1ea7](C:\Users\陆先生\Desktop\typora笔记和图片\笔记图片\3adc05e4a2ff4e87bded243d88fe1ea7.png)

为了方便大家理解，上方将该参数，以postman的形式上传。

代码参照：https://github.com/hupangs/api_test.git
详细文档请看：https://blog.csdn.net/qq350146607/article/details/128939167

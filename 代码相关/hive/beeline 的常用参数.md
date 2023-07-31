#### **参数**详解

#### 选项

描述

-u <database URL>

用于JDBC URL连接。用例：`beeline -u` db_URL

-r

重新连接到最近使用过的URL（如果用户有预先使过的用的，用!connect生成URL，用`!save` 生成beeline.properties.file）。

用例: `beeline -r`  

Version: 2.1.0 ([HIVE-13670](https://issues.apache.org/jira/browse/HIVE-13670))

-n <username>

连接时使用的用户名。用例: `beeline -n` valid_user

-p <password>

连接时使用的密码。用例: beeline -p valid_password      可选的密码模式: 从Hive 2.2.0开始参数-p选项是可选的。

用例 : beeline -p [valid_password]

如果密码不是在-p之后提供的，Beeline将在初始化连接时提示输入密码。当密码提供后Beeline会用它来初始化连接而不提示。

-d <driver class>

配置使用的驱动类  用例: `beeline -d` driver_class

-e <query>

应该执行的查询。查询语句两端用单引号和双引号。这个选项被使用多次。用例: beeline -e "query_string"  
支持运行复杂的SQL语句，在一个语句中通过使用分号分隔。([HIVE-9877](https://issues.apache.org/jira/browse/HIVE-9877))  
Bug fix (null pointer exception): 0.13.0([HIVE-5765](https://issues.apache.org/jira/browse/HIVE-5765))  
Bug fix (--headerInterval not honored): 0.14.0  ([HIVE-7647](https://issues.apache.org/jira/browse/HIVE-7647))  
Bug fix (running -e in background): 1.3.0 and 2.0.0 ([HIVE-6758](https://issues.apache.org/jira/browse/HIVE-6758)); [workaround available](https://issues.apache.org/jira/browse/HIVE-6758?focusedCommentId=13954968&page=com.atlassian.jira.plugin.system.issuetabpanels:comment-tabpanel#comment-13954968) for earlier versions

-f <file>

需要被执行的脚本文件。用例: beeline -f filepath  
Version: 0.12.0  ([HIVE-4268](https://issues.apache.org/jira/browse/HIVE-4268))  
注：如果脚本里面包含tabs，版本0.12.0中查询编译失败，这个bug已经在0.13.0版本修复了。([HIVE-6359](https://issues.apache.org/jira/browse/HIVE-6359)).  
Bug fix (running -f in background): 1.3.0 and 2.0.0 ([HIVE-6758](https://issues.apache.org/jira/browse/HIVE-6758));  [workaround available](https://issues.apache.org/jira/browse/HIVE-6758?focusedCommentId=13954968&page=com.atlassian.jira.plugin.system.issuetabpanels:comment-tabpanel#comment-13954968) for earlier versions 

-i (or) --init <file or files>

初始化需要的初始文件。用例: beeline -i /tmp/initfile  
单个文件:Version: 0.14.0 ([HIVE-6561](https://issues.apache.org/jira/browse/HIVE-6561))  
多个文件:Version: 2.1.0 ([HIVE-11336](https://issues.apache.org/jira/browse/HIVE-11336))

-w (or) --password-file <password file>

从文件中读取密码。Version: 1.2.0 ([HIVE-7175](https://issues.apache.org/jira/browse/HIVE-7175))

-a (or) --authType <auth type>

jdbc的认证类型是一个身份认证属性。Version: 0.13.0  ([HIVE-5155](https://issues.apache.org/jira/browse/HIVE-5155))

 --property-file <file>

读取配置属性的文件用例: beeline --property-file /tmp/a

Version: 2.2.0 ([HIVE-13964](https://issues.apache.org/jira/browse/HIVE-13964))

 --hiveconf property=value

 为给定的配置属性赋值。 在hive.conf.restricted.list列表中的属性不能通过hiveconf的方式重置。 (see  [Restricted List and Whitelist](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties#ConfigurationProperties-RestrictedListandWhitelist)).

用例: beeline --hiveconf prop1=value1  
Version: 0.13.0  ([HIVE-6173](https://issues.apache.org/jira/browse/HIVE-6173))

 --hivevar name=value

 Hive的变量名和变量值。这是一个Hive指定的设置，在这变量能够在会话级别被设置和被Hive命令和查询引用。

用例: beeline --hivevar var1=value1

 --color=[true/false]

 制颜色是否被用来展示。默认是false 用例: beeline --color=true

(不支持分隔的值输出方式。See [HIVE-9770](https://issues.apache.org/jira/browse/HIVE-9770))

 --showHeader=[true/false]

展示列名是否在查询结果中。默认是true。用例: beeline --showHeader=false 

 --headerInterval=ROWS

当输出为表格时，重新显示列头时他们之间的间隔，用行数计算。默认值为100 用例: beeline --headerInterval=50  
(不支持分隔的值输出方式。See [HIVE-9770](https://issues.apache.org/jira/browse/HIVE-9770))

 --fastConnect=[true/false]

 连接时，跳过为HiveQL语法的tab键自动补全功能而构建所有表和列的清单，默认为true不构建该列表。 用例: beeline --fastConnect=false

--autoCommit=[true/false] 

 允许或者禁止自动事务执行。默认是false    用例: beeline --autoCommit=true

 --verbose=[true/false]

 展示冗长的报错信息和调试信息（true）或者不展示（false），默认是false    用例: beeline --verbose=true

 --showWarnings=[true/false]

 Default is false.连接时，在执行任意HiveQL命令后展示警告信息。默认是false。 用例: beeline --showWarnings=true

 --showDbInPrompt=[true/false]

 在提示符里面展示当前数据库名字。默认是false。用例: `beeline --showDbInPrompt=true`

Version: 2.2.0 ([HIVE-14123](https://issues.apache.org/jira/browse/HIVE-14123))

--showNestedErrs=[true/false] 

展示内部错误，默认是false。用例: `beeline --showNestedErrs=true`

 --numberFormat=[pattern]

 用一个小数格式的模板来格式化数字。用例: `beeline --numberFormat="#,###,##0.00"`

--force=[true/false] 

 出错后继续运行脚本（true），或者不运行（false）。默认是false。用例: `beeline--force=true`

 --maxWidth=MAXWIDTH

 当输出格式是表格时，在截断数据前展示的最大宽度。默认是查询时的终端的当前宽度，然后回到80。用例: `beeline --maxWidth=150`

 --maxColumnWidth=MAXCOLWIDTH

 当输出是表格时，最大列宽，Hive 2.2.0以后默认是50，之前的版本是15。用例: `beeline --maxColumnWidth=25`

 --silent=[true/false]

 是（true）否（false）减少展示的信息量。它也会停止展示HiveServer2（Hive 0.14及之后的版本）的查询和命令（Hive 1.2.0及之后的版本）日志信息，默认是false。用例: `beeline --silent=true`

 --autosave=[true/false]

 自动保存参数选择（true）或者不保存（false）。默认是false。用例: `beeline --autosave=true`

 --outputformat=[table/vertical/csv/tsv/dsv/csv2/tsv2]

 结果展示的模式。默认是表格。查阅下方的[Separated-Value Output Formats](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Separated-ValueOutputFormats)获取更多信息和推荐选项。用例: `beeline --outputformat=tsv`

版本号: dsv/csv2/tsv2 added in 0.14.0 ([HIVE-8615](https://issues.apache.org/jira/browse/HIVE-8615))

 --truncateTable=[true/false]

 如果是true，那么当表格超出终端显示宽度时，截断表格的列在终端上展示。版本号: 0.14.0 ([HIVE-6928](https://issues.apache.org/jira/browse/HIVE-6928))

 --delimiterForDSV= DELIMITER

 用于输出格式中划分值的界定符。默认是‘|’      版本号: 0.14.0 ([HIVE-7390](https://issues.apache.org/jira/browse/HIVE-7390))

 --isolation=LEVEL

 设置事务隔离级别为TRANSACTION_READ_COMMITTED或者TRANSACTION_SERIALIZABLE.  可以查阅Java连接文档中“Field Detail”那一章节。

用例: beeline --isolation=TRANSACTION_SERIALIZABLE

 --nullemptystring=[true/false]

 使用历史的打印空字符null的形式(true)还是使用当前打印空值的方式(false)，默认是false。 用例: `beeline --nullemptystring=false`

Version: 0.13.0 ([HIVE-4485](https://issues.apache.org/jira/browse/HIVE-4485))

 --incremental=[true/false]

 从Hive 2.3版本往后默认是true，在它之前是默认为false。当设置为false时，为了最佳的展示列宽，完整的结果集会在展示之前被收集然后缓存起来。当设置为true时，结果集一旦被抓取到就会立即展示， 为了在展示列的填充额外消耗更少的延迟和内存。当你在客户端遭遇一个内存溢出时，推荐设置--incremental=true (因为抓取到的结果集非常大)。

 --incrementalBufferRows=NUMROWS

当打印行到标准输出时，保存在缓存中的行数，默认是1000。只有当 --incremental=true 和 --outputformat=table才适用。  用例: beeline --incrementalBufferRows=1000

Version: 2.3.0 ([HIVE-14170](https://issues.apache.org/jira/browse/HIVE-14170))

 --maxHistoryRows=NUMROWS

 存储Beeline 历史记录的最大行数。Version: 2.3.0 ([HIVE-15166](https://issues.apache.org/jira/browse/HIVE-15166))

 --delimiter=;

 设置Beeline的查询语句分隔符。允许用多个字符的分隔符，但是引号，斜杠和--是不允许的，默认是分号;    用例: `beeline --delimiter=$$`

Version: 3.0.0 ([HIVE-10865](https://issues.apache.org/jira/browse/HIVE-10865))

 --convertBinaryArrayToString=[true/false]

展示二进制列数据为字符串或者位矩阵。用例: `beeline --convertBinaryArrayToString=true`

 Version: 3.0.0 ([HIVE-14786](https://issues.apache.org/jira/browse/HIVE-14786))

 --help

 展示一个帮助信息。用例: `beeline --help`
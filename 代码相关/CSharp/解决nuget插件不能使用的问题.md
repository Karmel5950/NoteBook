

# 错误提示

使用[vscode](https://so.csdn.net/so/search?q=vscode&spm=1001.2101.3001.7020)安装nuget插件之后出现错误：

```csharp
"Versioning information could not be retrieved from the NuGet package repository. Please try again later."
```

# 原因

主要是nuget插件里的拉组件的js文件没有进行小写的控制

# 解决

打开路径下的文件fetchPackageVersions.js

```cobol
/Users/用户名/.vscode/extensions/jmrog.vscode-nuget-package-manager-1.1.6/out/src/actions/add-methods/fetchPackageVersions.js
```

修改代码

```cobol
...node_fetch_1.default(`${versionsUrl}${selectedPackageName}/index.json`, utils_1.getFetchOptions(vscode.workspace.getConfiguration('http')))
```

修改后的代码了toLowerCase()方法

```cobol
...node_fetch_1.default(`${versionsUrl}${selectedPackageName.toLowerCase()}/index.json`, utils_1.getFetchOptions(vscode.workspace.getConfiguration('http')))
```

重启vscode问题解决！
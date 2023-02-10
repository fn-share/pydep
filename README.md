pydep
--------

yet another copy tool for common shared source file between repository group.

&nbsp;

## 关于 pydep 工具

pydep 常用于辅助组织基于 python 代码的项目，它允许用户从指定的一个或多个源目录，自动拷贝源文件到当前目录下的一个或多个目标目录下。

一句话描述本工具就是，按预定义规则（在 `pydepends.json` 文件中定义）批量拷贝项目文件。

&nbsp;

## 为什么需要 pydep 工具

在日常开发实践中，我们经常在多个关联项目（repo）中共用某些源文件，而这些源文件比较零散，并不方便将它们整理成规范的代码库（以便用类似 `pip install software` 方式去安装）。但这些零星文件又在多个项目中使用，如果不统一维持，又容易出现某 repo 修改了，在其它 repo 忘同步的情况。

pydep 工具将所有公共可重用的源文件，归入一个 `code-base` 的 repo 中维护，当然，你也可以不管它叫 `code-base`，改叫其它名称都可以。为描述方便，我们暂称 `code-base` 项目。

日常维护中凡涉及 code-base 目录下的源文件更改，都按 `code-base` 项目去管理，其中的文件尽管在多个 repo 中都使用，但我们只修改  `code-base`  目录下文件。修改后要同步更新时，只需到目录 repo 目录下，运行 `./pydep` 命令，系统即自动完成相关文件的拷贝。

&nbsp;

## 编写 `pydepends.json` 文件

例如：

``` json
[ {
  "module": "tool",
  "source": "../code-base/tool",
  "target": "./tool",
  "files": ["debug.py","online_patch.py"]
}, {
  "module": "util",
  "source": "../code-base/util",
  "target": "./root",
} ]
```

这里定义两项拷贝任务，第 1 项任务从 `../code-base/tool` 源目录拷贝 2 个文件（由 `files` 指示）到目标目录 `./tool`。因为本工具在目标 repo 的根目录运行，我们一般采用相对路径方式表达源文件目录与目标目录。

上面第 2 项任务是将 `../code-base/util` 源目录下所有文件（不包括其下子目录）拷贝到指定的 `./root` 目录下。因为没定义 `files` 列表，其含义为 “拷贝源目录下所有文件”。

拷贝后的文件将置为只读，以防开发者不慎修改，如前述，针对这些公共的源码文件，应只在源头目录（即 `code-base`）下去修改，而不应该到目标 repo 目录下修改，本工具可以帮你养成良好工作习惯。

上面例子中，各任务的 `"module"` 字段指示拷贝任务的名称，它只是为了易读、易管理的目的而设置，不对拷贝过程产生影响。

&nbsp;

## 更多选项

如果想从指定源目录拷贝大部分文件，只有少数文件不拷贝。可以用 `excepts` 选项定义，比如下面定义：除了 base36.py 文件不拷贝，其它文件都要拷。

``` json
[ {
  "module": "util",
  "source": "../code-base/util",
  "target": "./root",
  "excepts": ["base36.py"]
} ]
```

另外还可用 `option` 指示存在冲突情况下如何处理，冲突是指：目标目录中已存在同名文件，而且源头文件更旧。该选项有 3 种取值：`"confirm", "force", "newest"`，其中，confirm 指显示提示请用户选择，force 是强行覆盖拷贝，newest 表示只在源头文件比目标目录下文件更新时，才覆盖拷贝，否则忽略。这 3 个取值中缺省为 `"confirm"`。

&nbsp;

## 在 `.gitignore` 添加 ignore 定义

因为我们在目标 repo 额外添加了 `pydep` 与 `pydepends.json` 两个文件，正常情况我们应在 `.gitignore` 添加例外定义。

只需打开 `.gitignore` 文件，将如下内容拷入并保存。

``` python
# exclude pydep files
pydep
pydepends.json
```

&nbsp;

## 运行 pydep

pydep 批处理脚本用 python 语言编写，它还支持在命令行界面用 python 发起，比如：

```
python3 pydep.py --help     # 获得帮助
python3 pydep.py               # 执行拷贝任务
python3 pydep.py module1 module2     # 执行指定的一个或多个模块的拷贝任务
python3 pydep.py --force module3        # 指定 option 的缺省值为 "force"
```

我们需要运行 `chmod +x pydep` 命令为 pydep 文件添加执行权限，然后在命令行窗口，进入目标 repo 的根目录，运行 `./pydep` 即实施文件拷贝。

在文件管理器用鼠标双击 pydep 文件，也触发 pydep 文件自动运行。

&nbsp;

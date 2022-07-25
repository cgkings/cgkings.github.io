---
description: 转自https://autoremove-torrents.readthedocs.io/zh_CN/latest/index.html
---

# Autoremove-torrents

## 介绍

这个程序是一个可以帮助您自动删除种子的工具。现在，你不再需要担心你的磁盘空间——根据你的策略，程序可以检查每个种子是否满足删除条件；如果是，那就自动删掉它。

### 支持的客户端

截至目前，程序支持 qBittorrent/Transmission/μTorrent/Deluge。rTorrent 正在计划之中。

## 安装与运行

### 安装

有两个方法可以安装 `autoremove-torrents`。强烈推荐使用pip安装。

#### 从 pip 安装

```
pip install autoremove-torrents
```

#### 从 GitHub 安装

```
git clone https://github.com/jerrymakesjelly/autoremove-torrents.git
cd autoremove-torrents
python3 setup.py install
```

### 运行

只需在你的终端中输入以下命令：

```
autoremove-torrents
```

`autoremove-torrents` 会在当前工作目录中寻找 `config.yml` 文件。有关更多命令行参数，请查看下面的表格。

#### 参数列表

注解

如果你使用参数的全名，你需要用等号来引出参数的值；不过，如果你使用参数的缩写，你只需要用一个空格把参数的值引出来。

| 参数名      | 参数缩写 | 描述                                      |
| -------- | ---- | --------------------------------------- |
| _–view_  | _-v_ | 运行并查看有哪些种子可以删除，但不要真正地删除它们。              |
| _–conf_  | _-c_ | 指定配置文件的路径。                              |
| _–task_  | _-t_ | 运行指定的任务。参数值就是要执行的任务名。                   |
| _–log_   | _-l_ | 指定日志文件的路径。                              |
| _–debug_ | _-d_ | Enable debug mode and output more logs. |

例如：

```
autoremove-torrents --view --conf=/home/art/config.yml --log=/home/art/logs
```

它等价于：

```
autoremove-torrents -v -c /home/art/config.yml -l /home/art/logs
```

### 卸载

#### 通过 pip 卸载

如果你的 autoremove-torrents 是通过 pip 安装的，你就可以用 pip 简单地卸载掉它：

```
pip uninstall autoremove-torrents
```

#### 手动卸载

然而，如果你的 autoremove-torrents 是通过 `setup.py` 安装的，你需要手动地删除所有的文件。

**第一步**

```
cd autoremove-torrents
```

**第二步**

重新安装这个程序并记录复制了哪些文件：

```
python3 setup.py install --record files.txt
```

**第三步**

用 `xargs` 去删除文件：

```
cat files.txt | xargs rm -rf
```

或者，如果你在用 Windows，可以使用 Powershell：

```
Get-Content files.txt | ForEach-Object {Remove-Item $_ -Recurse -Force}
```

参考：[https://stackoverflow.com/questions/1550226/python-setup-py-uninstall](https://stackoverflow.com/questions/1550226/python-setup-py-uninstall)

## 配置

在我们运行 `autoremove-torrents` 之前，我们需要创建一个 `config.yml` 文件，它可以用来保存配置。

{% hint style="info" %}
警告:为了防止误删种子，我们非常建议您在修改了配置文件之后，运行一次&#x20;

<mark style="color:orange;">`autoremove-torrents -v -c /home/art/config.yml`</mark>` ``` 以预览运行结果。
{% endhint %}

```
# 任务模板: YAML语法,不能使用tab,要用空格来缩进,每个层级要用两个空格缩进,否则必定报错!
# Part 1: 任务块名称,左侧不能有空格
my_task:
# Part 2: BT客户端登录信息,可以管理其他机的客户端
  client: qbittorrent
  host: http://127.0.0.1:8070
  username: admin
  password: adminadmin
# Part 3: 策略块（删除种子的条件）
  strategies:
    # Part I: 策略名称
    strategy1:
      # Part II: 筛选过滤器,过滤器定义了删除条件应用的范围,多个过滤器是且的关系,顺序执行过滤
      excluded_status:
        - Downloading
      excluded_trackers:
        - tracker.totheglory.im
      # Part III: 删除条件,多个删除条件之间是或的关系,顺序应用删除条件
      free_space:
        min: 300
        path: /home/qbt/downloads
        action: remove-slow-upload-seeds
    strategy2:
      status: Downloading
      remove: last_activity > 900 or download_speed < 50
      #delete_data: true
    # 一个任务块可以包括多个策略块...
# Part 4: 是否在删除种子的同时也删除数据。如果此字段未指定,则默认值为false
  delete_data: true
# 该模板策略块1为:对于非下载状态且非TTG的种子,删除900秒未活动的种子,或硬盘小于100G时,尽量删除不活跃种子
```

### 策略块说明

这个部分是策略块。每个策略块也可以分为3个部分(策略名称、过滤器、删除条件)，

#### 策略块说明--过滤器

当前策略只会对你所选择的种子有效。有9个过滤器可以用。

* `all_trackers`/`all_categories`/`all_status`：选择所有的Tracker/分类/种子状态。
* `categories`：选择这些分类的种子。
* `excluded_categories`：排除这些分类的种子。
* `trackers`：选择这些Tracker的种子。
* `excluded_trackers`：排除这些Tracker的种子。
* `status`：选择这些状态的种子，可以选择的状态如下表：

| 状态              | 备注                 |
| --------------- | ------------------ |
| Downloading     | /                  |
| Uploading       | /                  |
| Checking        | /                  |
| Queued          | /                  |
| Paused          | Transmission 无此状态。 |
| Stopped         | qBittorrent 无此状态。  |
| Error           | /                  |
| StalledUpload   | μTorrent 无此状态。     |
| StalledDownload | μTorrent 无此状态。     |

* `excluded_status`：排除这些状态的种子，可选的状态如上表。

每个过滤器的结果都是一个种子的集合。

注解

如果 `categories`、`trackers`、`status` 三个过滤器的其中两个或者三个同时存在，则程序会取这些集合的交集，并减去 `excluded_categories` 集合、`excluded_trackers` 集合和 `excluded_status` 集合。

注解

1. 不要在 `trackers` 中写套接字，`trackers` 字段只需要填主机名。例如 [https://tracker.site1.com](https://tracker.site1.com/) 只填写 tracker.site1.com。
2. 在1.4.4以及以后的版本中，如果 `categories`、`trackers` 或 `status` 中的内容只有一项，可以不使用列表，写一个单行文本就行，例如：

```
categories: cata1
```

```
status: uploading
```

1. `StalledUp` 与 `StalledDown` 为 1.4.5 版本新增的状态。在本程序中，`Uploading` 包含 `StalledUpload` 状态的种子，`Downloading` 包含 `StallDownload` 状态的种子。

让我们先看一些例子。例如，选择分类是 Movies 或 Games，但 Tracker 不是 tracker.yyy.com 的种子：

```
my_task:
  client: xxx
  host: xxx
  username: xxx
  password: xxx
  strategies:
    my_strategy:
      categories:
        - Movies
        - Games
      excluded_trackers:
        - tracker.yyy.com
      # Removing conditions are here
      # ...
```

#### 策略块说明--删除条件

有两种设置删除条件的方法。

**1. 直接使用删除条件关键词（推荐）**

直接使用删除条件关键词，有 18 个删除条件。

注解:只要选择的种子满足这些条件之一，它就会被删除。

前 18 个条件如下。为了防止误删种子，某些条件只对特定的状态有效。

| 条件                          | 单位      | 可用状态                    | 说明                                                                                  |
| --------------------------- | ------- | ----------------------- | ----------------------------------------------------------------------------------- |
| `ratio`                     |         | 全部                      | 分享率上限                                                                               |
| `create_time`               | 秒       | 全部                      | 种子从添加到客户端到现在所经过的时间，以秒为单位。在这里设置一个添加时间的上限，当种子在客户端中存活时间超过此上限后，种子会被直接删除（不管种子现在正处于哪种状态）。 |
| `downloading_time`          | 秒       | 全部                      | 最长下载时间。                                                                             |
| `seeding_time`              | 秒       | 全部                      | 最长做种时间。                                                                             |
| `max_download`              | GiB     | 全部                      | 种子最大下载量，超过此限制的种子会被删除。                                                               |
| `max_downloadspeed`         | KiB/s   | Downloading             | 种子的最大下载速度，超过此限制的种子会被删除。                                                             |
| `min_uploadspeed`           | KiB/s   | Downloading 或 Uploading | 种子的最小上传速度，低于此速度的种子会被删除。                                                             |
| `max_average_downloadspeed` | KiB/s   | 全部                      | 最大平均下载速度，如同 `max_downloadspeed` 。                                                   |
| `min_average_uploadspeed`   | KiB/s   | 全部                      | 最小平均上传速度，如同 `min_uploadspeed` 。                                                     |
| `max_size`                  | GiB     | 全部                      | 种子大小限制，超过此限制的种子会被删除。                                                                |
| `max_seeder`                |         | 全部                      | 最大做种者数，超过此限制的种子会被删除。                                                                |
| `max_upload`                | GiB     | 全部                      | 种子的最大上传体积，超过此限制的种子会被删除。                                                             |
| `min_leecher`               |         | 全部                      | 最小下载者数，低于此设置的种子会被删除。                                                                |
| `max_connected_seeder`      |         | Downloading 或 Uploading | 已连接做种者的最大值，如同 `max_seeder` 。                                                        |
| `min_connected_leecher`     |         | Downloading 或 Uploading | 已连接下载者数的最小值，如同 `min_leecher`。                                                       |
| `last_activity`             | 秒       | 全部                      | 种子从停止活动到现在所允许经过的最长时间，即允许的没有上传或下载活动的最长时间。当种子到达此限制时，它就会被删除。                           |
| `max_progress`              | 百分比 (%) | 全部                      | 允许的最大下载进度，最大值是100。                                                                  |
| `upload_ratio`              |         | 全部                      | 最大上传比率。注意，此处的上传比率与分享率不同，对于每个种子来说，上传比率是指上传量除以种子大小的比值。                                |

注解

对于 1.5.4 或以上版本，`last_activity` 的默认行为已经改变。在默认情况下，`last_activity` 只考虑曾经活动过的种子；而对于从未活动过的种子，`last_activity` 不会删除它们。

此外，若需要删除从未活动过的种子，请使用 `last_activity: Never` 或者 `last_activity: None`。

除了上面的删种条件，这里还有 3 个删种条件。当剩下的种子触发这些删种条件时，它们就会被删除。

*   `seed_size`：计算上述选择的种子的总大小。如果总大小超过限制，一部分种子会被删除。需要设置以下两个属性：

    * `limit`：总大小限制，以GiB为单位。
    * `action`：确定哪部分种子将被删除。可以是以下值：

    | 值                        | 说明            |
    | ------------------------ | ------------- |
    | remove-old-seeds         | 尽量删除旧的种子。     |
    | remove-new-seeds         | 尽量删除新的种子。     |
    | remove-big-seeds         | 尽量删除体积大的种子。   |
    | remove-small-seeds       | 尽量删除体积小的种子。   |
    | remove-active-seeds      | 尽量删除活跃的种子。    |
    | remove-inactive-seeds    | 尽量删除不活跃的种子。   |
    | remove-fast-upload-seeds | 尽量删除上传速度快的种子。 |
    | remove-slow-upload-seeds | 尽量删除上传速度慢的种子。 |

    注解

    与 `last_activity` 类似，`remove-active-seeds` 与 `remove-inactive-seeds` 动作将优先考虑曾经活动过的种子。只有在这些种子都被删除但限制条件仍然没有被满足的情况下，从未活动过的种子才会被删除（但不保证删除顺序）。
* `maximum_number`：判断上述选择的选择的种子的总个数。如果个数超出设置的最大值，一部分种子会被删除，就像 _seed\_size_ 条件一样。需要设置以下两个属性：
  * `limit`：总个数限制
  * `action`：确定哪些种子会被删除，取值与含义同上表。
* `free_space`：检查磁盘的剩余空间是否符合设定。如果空间不足，一部分种子会被删除，就像 seed\_size 条件一样。需要设置以下三个属性：
  * `min`：剩余空间最小值，以\`GiB\`为单位；当目录的剩余空间小于这个值时，删除策略会被触发。
  * `path`：需要监控剩余空间的目录
  * `action`：删除策略，用于确定哪些种子会被删除。取值与含义同上表
* `remote_free_space`：同样也是根据磁盘剩余空间来决定哪些种子会被删除，但这里使用BT客户端汇报的磁盘剩余空间大小。它的行为与 `free_space` 一致。
  * `min`：允许的剩余空间最小值，以 _GiB_ 为单位。
  * `path`：BT客户端需要检查剩余空间大小的目录路径
  * `action`：删除策略

注解

如果你的 autoremove-torrents 和你的 BT 客户端运行在不同的机器上，你就需要使用 `remote_free_space` 去查询剩余空间了。除此之外，`free_space` 和 `remote_free_space` 是一样的。

请注意，并不是所有的客户端都支持检测特定目录的剩余空间。截至目前，只有 Deluge 和 Transmission 支持查询特定目录；若在 qBittorrent 中使用 `remote_free_space` 的 `path` 参数，它将会被忽略。

这有一个例子。对于全体种子，当 _/home/myserver/downloads_ 的剩余空间小于10GiB时，程序会尝试删除体积大的种子：

```
my_task:
  client: xxx
  host: xxx
  username: xxx
  password: xxx
  strategies:
    my_strategy:
      free_space:
        min: 10
        path: /home/myserver/downloads
        action: remove-big-seeds
```

这是最后一个例子。对于全体种子，首先删掉那些分享率大于 3 的种子，然后如果剩下的种子的总大小还大于 500GiB 就尽量删除一些活跃的种子，直到总大小小于 500GiB 为止：

```
my_task:
  client: xxx
  host: xxx
  username: xxx
  password: xxx
  strategies:
    my_strategy:
      ratio: 3
      seed_size:
        limit: 500
        action: remove-active-seeds
```

**2. 使用 `remove` 关键词 （高级)**

使用 `remove` 关键词。`remove` 关键词是在1.4.0版本中新增的关键词，用于支持复杂的删除条件的设置。`remove` 关键词后接一个表达式，表达式由以下语法构成：

1.  `<参数> <比较运算符> <数值>`

    `参数`：可选列表如下，不区分大小写。

    注解

    某些属性只能在特定的状态中使用，不在可用状态的种子不会被删除。

    | 参数                      | 单位    | 可用状态                    | 说明                        |
    | ----------------------- | ----- | ----------------------- | ------------------------- |
    | `average_downloadspeed` | KiB/s | 全部                      | 平均下载速度。                   |
    | `average_uploadspeed`   | KiB/s | 全部                      | 平均上传速度。                   |
    | `connected_leecher`     | /     | Downloading 或 Uploading | 已连接的下载者数。                 |
    | `connected_seeder`      | /     | Downloading 或 Uploading | 已连接的做种者数。                 |
    | `create_time`           | 秒     | 全部                      | 种子从添加到客户端到现在所经过的时间。       |
    | `download`              | GiB   | 全部                      | 下载量                       |
    | `download_speed`        | KiB/s | Downloading             | 下载速度                      |
    | `downloading_time`      | 秒     | 全部                      | 下载时间。                     |
    | `last_activity`         | 秒     | 全部                      | 种子从停止活动（无上传或下载）到现在所经过的时间。 |
    | `leecher`               | /     | 全部                      | 下载者数。                     |
    | `progress`              | %     | 全部                      | 下载进度。                     |
    | `ratio`                 | /     | 全部                      | 分享率                       |
    | `seeder`                | /     | 全部                      | 做种者数。                     |
    | `seeding_time`          | 秒     | 全部                      | 做种时间                      |
    | `size`                  | GiB   | 全部                      | 种子大小。                     |
    | `upload`                | GiB   | 全部                      | 上传量                       |
    | `upload_ratio`          | /     | 全部                      | 上传量 / 种子大小                |
    | `upload_speed`          | KiB/s | Downloading 或 Uploading | 上传速度                      |

    `比较运算符`：可用的运算符如下。

    | 比较运算符 | 说明 |
    | ----- | -- |
    | `<`   | 小于 |
    | `>`   | 大于 |
    | `=`   | 等于 |

    `数值`：指定一个数值，支持整数和小数。

    该语法直接选出符合条件的种子，并直接删除它们或者与后面的复合表达式配合使用。这是一个例子，它直接删除做种时间大于259200秒的种子：

    ```
    my_task:
      client: xxx
      host: xxx
      username: xxx
      password: xxx
      strategies:
        my_strategy:
          remove: seeding_time > 259200
    ```
2.  `<表达式1> and <表达式2>` 以及 `<表达式1> or <表达式2>`

    该语法是复合表达式。

    * `and`：选择同时满足 `表达式1` 和 `表达式2` 的种子（交集）。
    * `or`：选择满足 `表达式1` 或者 `表达式2` 或者同时满足两个表达式的种子（并集）。

    这是一个例子。对于全体种子，分享率超过 2 **而且** 做种时间超过 60000 秒的种子会被删除：

    ```
    my_task:
      client: xxx
      host: xxx
      username: xxx
      password: xxx
      strategies:
        my_strategy:
          remove: ratio > 2 and seeding_time > 60000
    ```

    这是另外一个例子。对于全体种子，分享率小于 1 **或者** 做种时间超过 60000 秒的种子会被删除：

    ```
    my_task:
      client: xxx
      host: xxx
      username: xxx
      password: xxx
      strategies:
        my_strategy:
          remove: ratio < 1 or seeding_time > 60000
    ```
3.  `(<表达式>)`

    一个表达式加上括号以后，它仍然是表达式。使用括号可以改变优先级。可以使用多重括号嵌套。

    这是一个例子。对于全体种子，做种时间超过 60000 秒的种子， **或者** 分享率大于 3 **而且** 添加时间超过 1400000 秒的种子会被删除：

    ```
    my_task:
      client: xxx
      host: xxx
      username: xxx
      password: xxx
      strategies:
        my_strategy:
          remove: seeding_time > 60000 or (ratio > 3 and create_time > 1400000)
    ```

### 第四部分：删除数据

是否在删除种子的同时也删除数据。如果此字段未指定，则默认值为 `false` 。

### 最后一步……

记得检查你的配置文件，确保它按照你的想法工作（不然可能就会误删种子了）。使用以下命令行以查看将要删除的种子，但不会真正地删除它们：

```
autoremove-torrents -v -c /home/art/config.yml
```

如下图所示，可以看到我这边执行后满足条件的种子就被删除了（包括种子下载的数据）：

![](../.gitbook/assets/go5bkv90k9.gif)

确定[程序](https://www.zalou.cn/tag/chengxu)可以正常工作后，确定程序位置：

```
command -v autoremove-torrents
```

![](../.gitbook/assets/image.png)

添加计划任务：

```javascript
crontab -e
```

复制写入：

```javascript
*/1 * * * * /usr/local/bin/autoremove-torrents -c /home/art/config.yml -l /home/art/logs
```

或者直接执行以下命令

```
crontab -l | {
                    cat
                         echo "*/1 * * * * /usr/local/bin/autoremove-torrents -c /home/art/config.yml -l /home/art/logs"
     }                                            | crontab -
```

注：执行时间可以根据自己的需要来更改。

最后重启crond服务：

```javascript
systemctl restart crond.service
```

复制

至此，[autoremove-torrents](https://www.zalou.cn/tag/autoremove-torrents)的部署就全部完成了。


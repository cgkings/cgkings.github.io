---
description: 转自https://zhuanlan.zhihu.com/p/479886858
---

# Autoremove-torrents删种策略

#### Autoremove torrents 官方文档： <a href="#h_479886858_20" id="h_479886858_20"></a>

[配置 - autoremove-torrents 1.5.3 文档](https://link.zhihu.com/?target=https%3A//autoremove-torrents.readthedocs.io/zh\_CN/latest/config.html%23part-1-task-name)

## 自用删种策略 <a href="#h_479886858_21" id="h_479886858_21"></a>

```
my_task:
# Part 2: BT客户端登录信息,可以管理其他机的客户端
  client: qbittorrent
  host: http://127.0.0.1:8071
  username: admin
  password: adminadmin
# Part 3: 策略块（删除种子的条件）
  strategies:
    # Part I: 策略名称
    remove_low_disk:
      # Part II: 筛选过滤器,过滤器定义了删除条件应用的范围,多个过滤器是且的关系,顺序执行过滤
      excluded_status: Downloading
      excluded_categories: 1.pt-down
      excluded_trackers: tracker.totheglory.im
      # Part III: 删除条件,多个删除条件之间是或的关系,顺序应用删除条件
      free_space:
        min: 300
        path: /home/qbt-pter/downloads
        action: remove-slow-upload-seeds
    remove_low_download:
      status: Downloading
      excluded_categories: 1.pt-down
      remove: last_activity > 900 or download_speed < 50 or create_time > 1800 and connected_leecher < 1 and average_uploadspeed < 10
    remove_low_upload:
      status: uploading
      excluded_categories: 1.pt-down
      remove: upload_speed < 2 or connected_leecher < 1
      #delete_data: true
    # 一个任务块可以包括多个策略块...
# Part 4: 是否在删除种子的同时也删除数据。如果此字段未指定,则默认值为false
  delete_data: true
```

## 备用删种策略

### 黑车 <a href="#h_479886858_21" id="h_479886858_21"></a>

删除下载速度远超上传的种子

| 下载 | 上传 |
| -- | -- |
| 80 | 65 |
| 60 | 45 |
| 40 | 25 |
| 20 | 15 |

```yaml
clean_by_speed:
      status: Downloading
      excluded_categories:
        - MySaved
      remove: download_speed > 40000 and upload_speed < 25000
```

HR种子：ex. 如下载大于50%触发HR，可额外附加progress < 50

### 慢车 <a href="#h_479886858_22" id="h_479886858_22"></a>

* 2小时内慢速上传

```yaml
remove: create_time > 7200 and (average_uploadspeed < 500 or ratio < 1)
```

### 低速种策略1

```yaml
clean_Seeding_S1:
      status:
        - Uploading
      excluded_categories:
        - MySaved
      remove: (leecher < 3 and upload_speed < 50) or (average_uploadspeed < 400)
```

### 低速种策略2

```yaml
clean_Seeding_S2:
      status:
        - Uploading
      excluded_categories:
        - MySaved
      remove: upload_speed < 2 or connected_leecher < 1
```

### 超时下载 <a href="#h_479886858_24" id="h_479886858_24"></a>

* 24小时内未完成删除

```yaml
24hr_free_countdown_strategy:
      excluded_categories:
        - MySaved
      remove: progress < 100 and create_time > 86400
```

### HR 删种 <a href="#h_479886858_25" id="h_479886858_25"></a>

* 做种超过140h后删除（可根据不同站点自己设置时间）

```yaml
remove: seeding_time > 504000 and average_uploadspeed < 400
```

### 限制最大下载数 <a href="#h_479886858_26" id="h_479886858_26"></a>

```yaml
limit_max_torrents:
      status:
        - downloading
      excluded_categories:
        - MySaved
      maximum_number: 
        limit: 16
        action: remove-inactive-seeds
```

### 其他策略 <a href="#h_479886858_27" id="h_479886858_27"></a>

* 限制最大上传量（针对某些非VIP 100%黑种站）：upload\_ratio: 3
* 根据做种人数/下载人数：

```yaml
delete_by_peernumber:
      status:
        - Downloading
      excluded_categories:
        - MySaved
      remove: seeder > 3 or connected_leecher < 2
```

#### 样本 <a href="#h_479886858_28" id="h_479886858_28"></a>

```yaml
delete_task:
  client: qbittorrent
  host: http://51.159.35.144:8080
  username: admin
  password: admin123456
  strategies: 
    24hr_free_countdown_strategy:
      excluded_categories:
        - MySaved
      remove: progress < 100 and create_time > 86400 # not finished after 24 hours
    clean_by_upload_ratio_strategy:
      categories:
        - XXX
      upload_ratio: 3
    clean_slow_uploading_S1: 
      status: 
        - Downloading
      excluded_categories:
        - MySaved
      remove: create_time > 7200 and (average_uploadspeed < 300 or ratio < 1) # 2 hours
    clean_slow_uploading_S2:
      status:
        - Downloading
      excluded_categories:
        - MySaved
      remove: seeder > 3 or connected_leecher < 2
    clean_Seeding_S1:
      status:
        - Uploading
      excluded_categories:
        - MySaved
      remove: (leecher < 3 and upload_speed < 50) or (average_uploadspeed < 400)
    clean_Seeding_S2:
      status:
        - Uploading
      excluded_categories:
        - MySaved
      remove: upload_speed < 2 or connected_leecher < 1   
    clean_by_speed_S1:
      status: Downloading
      excluded_categories:
        - MySaved
      remove: download_speed > 40000 and upload_speed < 25000 
    clean_by_speed_S2:
      status: Downloading
      excluded_categories:
        - MySaved
      remove: download_speed > 20000 and upload_speed < 15000 
    clean_by_speed_S3:
      status: Downloading
      excluded_categories:
        - MySaved
      remove: download_speed > 60000 and upload_speed < 45000 
    clean_HR_XXX_strategy:
      categories:
        - XXX
      status:
        - Uploading
      remove: seeding_time > 504000 and average_uploadspeed < 400 
    limit_max_torrents:
      status:
        - downloading
      excluded_categories:
        - MySaved
      maximum_number: 
        limit: 16
        action: remove-inactive-seeds
  delete_data: true
```

### &#x20;<a href="#h_479886858_29" id="h_479886858_29"></a>

文章到这里就结束了，就像开头所说这只是提供一种刷流的思路。有兴趣的PT友也可以了解下Vertex，可以集中管理RSS跟删种，免除了Flexget跟Auto-remove torrents 至于R种，如果不想写config.yml也可以多开pt助手来实现同样的功能。

最后善意提醒各位PT友，请注意各站HR要求跟对seedbox的单独要求，以免被ban。

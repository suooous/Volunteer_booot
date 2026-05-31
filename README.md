# 志愿活动金数据自动报名工具

监听 QQ 群/私聊中的**金数据（Jinshuju）**报名链接，自动打开表单、填写个人信息并提交，适用于校内志愿活动抢报名场景。

下载链接：https://drive.google.com/file/d/1NfC_Suu07N7CzY6P02uOGZgU3JigcETv/view?usp=sharing

> 免责声明：本工具仅供**个人学习与研究**使用，请遵守学校规定与金数据平台使用条款。

## 工作原理

```
QQ 群消息（含金数据链接）
        ↓
   NapCat（OneBot 协议）
        ↓  HTTP POST
   后端服务（auto_signup）
        ↓  Playwright 浏览器
   自动填写并提交金数据表单
```

后端支持两种金数据页面样式：

- **旧版（legacy）**：`jinshuju.com` 常见表单
- **新版（modern）**：`*.jsjform.com` 定制域及新版 UI

## 目录结构

```
Volunteer/
├── start.bat              # 一键启动（NapCat + 后端）
├── stop.bat               # 一键停止
├── build_backend.bat      # 从源码打包后端 exe（开发者用）
├── NapCat/                # QQ 机器人框架
│   └── napcat.bat
└── backend/
    ├── auto_signup.exe    # 后端可执行文件（运行必需）
    ├── app.py             # 后端源码
    ├── config.json        # 个人资料与运行配置（使用前必改）
    ├── browsers/          # Playwright 内置 Chromium
    └── runtime_data/      # 运行日志与截图（自动生成）
        ├── tasks.jsonl
        └── screenshots/
```


<img width="905" height="386" alt="image" src="https://github.com/user-attachments/assets/aea1c4d0-6079-4fe7-aafb-75316438cf99" />

## 环境要求

- **操作系统**：Windows 10/11
- **网络**：能访问金数据表单页面
- **QQ 账号**：用于 NapCat 登录
- **无需安装 Python**（直接使用已打包的 `auto_signup.exe` 即可）

## 快速开始

### 第一步：填写个人资料

编辑 `backend/config.json`，将 `profile` 中的占位信息改为你的真实资料：

```json
{
  "onebot_secret": "",
  "profile": {
    "name": "张三",
    "student_id": "2020123456",
    "mobile": "13800138000",
    "building": "T1",
    "college": "某某学院",
    "student_type": "内招生",
    "political_status": "中共预备党员",
    "activity_name": [
      "冰箱清理志愿",
      "铁架台清理志愿"
    ]
  },
  "settings": {
    "auto_submit": true,
    "headless": false,
    "host": "127.0.0.1",
    "port": 8000
  }
}
```
<img width="1106" height="727" alt="image" src="https://github.com/user-attachments/assets/5748a2a7-04c5-4847-a412-b55090056bf6" />


#### 字段说明

| 字段 | 说明 |
|------|------|
| `name` | 姓名 |
| `student_id` | 学号 |
| `mobile` | 手机号 |
| `building` | 所住楼栋（需与表单下拉选项**完全一致**） |
| `college` | 学院 |
| `student_type` | 生源类别，如「内招生」「外招生」 |
| `political_status` | 政治面貌 |
| `activity_name` | 活动项。可填字符串或数组；填数组时，程序会在单选题中**匹配第一个命中的选项** |

> **重要**：`building`、`activity_name`、`student_type`、`political_status` 等选项文字必须与金数据表单中的选项**一字不差**，否则填写会失败。


### 第二步：配置 NapCat

1. 双击 `start.bat`，会先启动 NapCat。
   (双击`start.bat`之后会出现三个终端，一个浏览器的小窗口)
   <img width="1662" height="970" alt="image" src="https://github.com/user-attachments/assets/080f1700-7928-4dbf-961e-e2d2fe51ea27" />

3. 运行需登录 QQ：
   - 打开浏览器访问 NapCat WebUI：`http://127.0.0.1:6099`
   - 按页面提示扫码或输入账号登录
  <img width="1490" height="471" alt="image" src="https://github.com/user-attachments/assets/b20ca8c7-c3e8-48e3-aeda-260583fed106" />

4. 首次使用该脚本需要配置Napcat
   - 网络配置 -> 新建 -> HTTP客户端
   <img width="1919" height="1028" alt="image" src="https://github.com/user-attachments/assets/c289beaa-d8ac-470d-82bc-aeddb7be657c" />
  - 配置的信息照着下面的配置即可，填写完成之后保存即可
   <img width="793" height="797" alt="image" src="https://github.com/user-attachments/assets/0b43e97b-f5ef-44da-959d-7a38e41c7b97" />
  - 配置成功就是这个页面
    <img width="1919" height="928" alt="image" src="https://github.com/user-attachments/assets/afebbcd9-d46c-4a2f-9206-6fa4d343222e" />
### 第三步：报名志愿
1.这个`backend`终端会时刻监听你的QQ接受的信息
<img width="1473" height="759" alt="image" src="https://github.com/user-attachments/assets/d1a0bab6-4797-494d-9548-d173e5b1ef44" />

2.如果有满足的志愿，就会进行监听，然后在浏览器小窗口进行处理，可以看到报名的过程
<img width="1619" height="992" alt="image" src="https://github.com/user-attachments/assets/f2d72d5f-09e8-4b25-99f5-76ba6e6b3b25" />


## 常见问题

### 1. 启动时报错「Missing auto_signup.exe」

说明后端未打包。请运行 `build_backend.bat`，或从发布包中复制完整的 `backend/` 目录。

### 2. 后端启动失败 / 浏览器打不开

- 确认 `backend/browsers/` 目录存在且含 Chromium
- 重新运行 `build_backend.bat` 中的浏览器安装步骤
- 检查杀毒软件是否拦截

### 3. QQ 有链接但后端无反应

- 确认 NapCat 已登录且在线
- 检查 `onebot11_*.json` 中 `httpClients` 的 `url` 是否为 `http://127.0.0.1:8000/onebot`，且 `enable` 为 `true`
- 确认链接域名属于 `jinshuju.com` 或 `jsjform.com`
- 查看 `tasks.jsonl` 是否有 `onebot_message_ignored` 记录

### 4. 表单填写失败 / 单选未命中

- 对照金数据表单，检查 `config.json` 中选项文字是否完全一致
- `activity_name` 可设为数组，按优先级排列多个候选活动名
- 查看 `screenshots/` 下失败截图定位问题
- 首次调试建议设 `"auto_submit": false`，确认填写正确后再开启自动提交

### 5. 同一链接重复处理

默认 30 分钟内（`dedup_ttl_seconds`）已成功或排队中的链接会被跳过。如需再次报名，等待 TTL 过期或重启后端。

### 6. 端口被占用

修改 `config.json` 中 `settings.port`，并同步修改 NapCat OneBot 配置中的 `url` 端口。

---

## 注意事项

- 本工具仅供**个人学习与研究**使用，请遵守学校规定与金数据平台使用条款。
- `config.json` 含个人敏感信息，请勿上传至公开仓库或分享给他人。
- 自动抢报名存在失败风险（网络延迟、表单变更、名额已满等），建议保留手动报名作为备选。
- NapCat 使用非官方 QQ 协议，存在账号风险，请自行评估。


   





# CHRPLUS 应用源（FnDepot）

飞牛 fnOS 第三方应用源，遵循 fnOS 应用中心 **V2 索引协议**。仓库根目录的 [`fnpack.json`](./fnpack.json) 是唯一索引，
应用中心通过它读取应用列表、版本与安装包地址。

- 源地址（应用中心添加第三方源时填写）：`https://github.com/upchr/FnDepot`
- 国内镜像：`https://gitee.com/upchr/FnDepot`

---

## 目录布局

```
FnDepot/
├── fnpack.json                     # V2 索引（唯一索引文件）
├── README.md
├── toolsplus/
│   ├── ICON.PNG
│   ├── README.md
│   ├── Preview/1.png … 9.png
│   ├── toolsplus_3.6.6.fpk         # 每个版本一个文件，按版本命名
│   └── toolsplus_3.6.7.fpk
└── meow/
    ├── ICON.PNG
    ├── README.md
    ├── meow_1.0.8_x86.fpk
    └── meow_1.0.8_arm.fpk
```

约定：索引里 `apps` 的 key 必须与目录名一致（`toolsplus` ↔ `toolsplus/`，`meow` ↔ `meow/`）。

---

## 一、索引结构（V2）

```json
{
  "schema_version": "2",
  "source_info": {
    "name": "CHRPLUS 应用源",
    "author": "CHRPLUS",
    "homepage": "https://github.com/upchr/FnDepot",
    "description": "个人维护的飞牛 fnOS 第三方应用源"
  },
  "apps": {
    "<app>": {
      "display_name": "…",
      "desc": "…",
      "platform": ["all"],
      "categories": ["系统工具"],
      "icon_url": "./toolsplus/ICON.PNG",
      "readme_url": "./toolsplus/README.md",
      "preview_urls": ["./toolsplus/Preview/1.png"],
      "bug_report_url": "https://github.com/upchr/FnDepot/issues",
      "maintainer": "CHRPLUS",
      "maintainer_url": "https://github.com/upchr/mytool",
      "distributor": "CHRPLUS",
      "distributor_url": "https://github.com/upchr/FnDepot",
      "run_as": "package",
      "install_type": "",
      "is_docker": true,
      "service_port": "16688",
      "releases": {
        "<版本>": {
          "changelog": "…",
          "updated_at": "2026-11-06T03:59:10Z",
          "packages": {
            "all": {
              "download_url": "./toolsplus/toolsplus_3.6.7.fpk",
              "sha256": "9607c39007024f5619e7a9ae07025820fa30bd119e343468bfba22154850d3a3",
              "size": 484842
            }
          }
        }
      }
    }
  }
}
```

### 顶层字段

| 字段 | 说明 |
|---|---|
| `schema_version` | 固定为字符串 `"2"`。声明的是整套结构语义，不再是 V1 的 `lasttime` / `type` 结构 |
| `source_info` | 源元信息：`name` / `author` / `homepage` / `description` |
| `apps` | 应用表，key 即应用标识（与目录名一致） |

### `apps.<app>` 字段

| 字段 | 说明 |
|---|---|
| `display_name` | 应用中心展示名 |
| `desc` | 简介，支持换行与 `<br>` |
| `platform` | 支持的架构，取值 `["all"]` / `["x86"]` / `["arm"]` / `["x86","arm"]` |
| `categories` | 分类，取值限：影音娱乐、系统工具、编程开发、AI赋能、生活服务、智能智控、教育学习、游戏地带、硬件驱动 |
| `icon_url` / `readme_url` / `preview_urls` / `bug_report_url` | 仓库内相对路径或绝对 URL |
| `maintainer` / `maintainer_url` / `distributor` / `distributor_url` | 维护者与分发者信息 |
| `run_as` | 运行身份，如 `package` |
| `install_type` | `""` 表示装到存储空间（可选值 `"root"`） |
| `is_docker` | 布尔值，docker 应用为 `true` |
| `service_port` | 应用对外端口（字符串） |
| `releases` | 版本表，key 为**裸版本号**（如 `3.6.7`，不带 `v` 前缀）；客户端按版本号数值取最大者为最新 |

### `releases.<版本>` 字段

| 字段 | 说明 |
|---|---|
| `changelog` | 该版本更新说明 |
| `updated_at` | 发布时间，UTC ISO 8601（`2026-11-06T03:59:10Z`） |
| `packages` | 架构 → 安装包，键**只允许** `all` / `x86` / `arm` |

`packages.<架构>`：

| 字段 | 必填 | 说明 |
|---|---|---|
| `download_url` | 是 | 相对本仓库根目录的路径（`./toolsplus/…`）或绝对 URL；必须与真实文件名一致 |
| `sha256` | 否 | 64 位小写十六进制。**一旦填写即强制校验**，哈希不符拒绝安装 |
| `size` | 否 | 安装包字节数，数字类型（不是字符串） |

### 架构回退链

```
设备 x86 → packages.x86 存在？→ 取它
          不存在        → packages.all 存在？→ 取它
                        不存在        → 该版本不可安装
设备 arm → 同上，先看 packages.arm
```

- 回退只发生在 **具体架构 → `all`** 这一层；**绝不跨架构**：arm 设备永远不会拿到 `packages.x86` 的包。
- `platform: ["all"]` 表示与架构无关（如 docker 应用），只写 `packages.all` 一条即可。

---

## 二、新增一个版本

1. **构建**：产出安装包，文件名带版本号，例如 `toolsplus_3.6.8.fpk`（下划线分隔，与目录内既有命名一致）。
2. **入库**：放进对应应用目录（`toolsplus/`、`meow/`），**不要覆盖上一版文件**——旧版本的索记录依赖旧文件保持原样。
3. **记录**：在 `fnpack.json` 追加 `apps.<app>.releases["<版本>"]`，`changelog` / `updated_at` / `packages` 全部填写，
   `sha256` 与 `size` 用刚构建出的文件实测值，不能用旧值。
4. **提交**：一应用一版一提交，提交信息形如 `chore(toolsplus): release v3.6.8`。

> 名字一旦发布就不要改：`download_url` 指向的文件被替换或重命名，会让该版本的 `sha256` 永久失效。

---

## 三、并发写索引

多个应用同时发版时，对 `fnpack.json` 的写入必须**串行**：

1. 先 `git pull` 拿到最新索引；
2. 追加本应用本版本的 release 记录，改完立即 `git push`；
3. 若 push 被拒绝（远端已前进），先 rebase，再基于新索引重新追加，然后重推。

禁止两个进程并行读改写同一个 `fnpack.json`：后写的一方会覆盖前一方刚追加的 release，导致上一版记录消失且难以发现。

---

## 四、当前应用

| 应用 | 显示名 | 架构 | 包 |
|---|---|---|---|
| `toolsplus` | ToolsPlus | all（docker） | `toolsplus/toolsplus_3.6.6.fpk`、`toolsplus_3.6.7.fpk` |
| `meow` | 猫窝 Meow | x86 / arm | `meow/meow_1.0.8_x86.fpk`、`meow_1.0.8_arm.fpk` |

各应用的功能、依赖与端口说明见各自目录下的 `README.md`：

- [toolsplus/README.md](./toolsplus/README.md)
- [meow/README.md](./meow/README.md)
- ToolsPlus 源码：[upchr/mytool](https://github.com/upchr/mytool) · 体验：[upchr.github.io/mytool](https://upchr.github.io/mytool/)
- 猫窝 Meow 源码：[upchr/meow](https://github.com/upchr/meow)

---

## 五、反馈

- Bug 与建议：[Issues](https://github.com/upchr/FnDepot/issues)
- 维护者：[upchr](https://github.com/upchr)

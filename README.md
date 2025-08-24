# Github action版：Supabase Keep-Alive Bot

这是一个使用 [GitHub Actions](https://github.com/features/actions) 自动为多个 Supabase 项目“保活”的自动化脚本。

免费版的 Supabase 项目如果在 7 天内没有任何 API 调用或数据库访问，将会被自动暂停 (pause)。此脚本通过定期（例如每周两次）向你的 Supabase 项目数据库发送一个极轻量的查询请求，来模拟活跃状态，从而防止项目被自动暂停。

## ✨ 功能特性

-   **全自动化**: 设置一次后，由 GitHub Actions 定时自动运行。
-   **支持多项目**: 只需一份配置文件即可优雅地管理任意数量的 Supabase 项目。
-   **安全可靠**: 所有敏感信息（项目 URL 和密钥）均通过 GitHub Secrets 安全存储，不会在代码中暴露。
-   **高效轻量**: 使用官方 `supabase-js` 库执行一个对系统表的元数据查询，对项目性能影响几乎为零。
-   **易于扩展**: 添加新的保活项目只需在 GitHub Secrets 和 workflow 文件中增加一行配置即可。

## 🚀 如何使用

### 步骤 1: 准备 Supabase 项目凭证

对于每一个你需要保活的 Supabase 项目，请前往 Supabase 项目后台的 **Project Settings -> API** 页面，获取以下两项信息：

1.  **项目 URL (Project URL)**
2.  **Service Role Key (service_role key)**：在 `Project API keys` 中找到，请务必使用这个 key，因为它权限最高，能确保操作成功。

**⚠️ 警告：`service_role` 密钥拥有对你数据库的完全控制权，请像对待密码一样妥善保管，绝不要泄露！**

### 步骤 2: 在 GitHub 仓库中设置 Secrets

为了安全地存储你的项目凭证，我们需要使用 GitHub Secrets。

1.  进入本仓库的 **Settings -> Secrets and variables -> Actions** 页面。
2.  点击 **New repository secret** 按钮。
3.  为你的**每一个** Supabase 项目创建对应的 Secrets。我们推荐使用以下命名规范：
    -   `SUPABASE_URL_<YOUR_PROJECT_NAME>`
    -   `SUPABASE_KEY_<YOUR_PROJECT_NAME>`

    例如，对于一个名为 `BLOG` 的项目和一个名为 `PROJ1` 的项目，你应该创建四个 Secrets：
    -   `SUPABASE_URL_BLOG`
    -   `SUPABASE_KEY_BLOG`
    -   `SUPABASE_URL_PROJ1`
    -   `SUPABASE_KEY_PROJ1`

    `<YOUR_PROJECT_NAME>` 部分可以是任何你喜欢的标识符（建议大写），只要在下一步中保持一致即可。
    "SUPABASE_URL_PROJ1": "https://your-project.supabase.co",
    https://supabase.com/dashboard/project/qfhifzyleqadysyjrvxi/settings/api-keys网址中：的"qfhifzyleqadysyjrvxi"
### 步骤 3: 配置 Workflow 文件

本项目已经包含一个 workflow 配置文件：`.github/workflows/supabase-keep-alive.yml`。

你唯一需要修改的地方是文件中的 `strategy.matrix.project` 列表。将你在上一步中设置的 `<YOUR_PROJECT_NAME>` 添加到这个列表中。

```yaml
# .github/workflows/supabase-keep-alive.yml

# ... (其他部分) ...
    strategy:
      matrix:
        # 在这里定义你的所有项目
        # 这里的名字需要和你设置的 Secrets 后缀完全对应
        project: ['PROJ1', 'BLOG'] # <-- 修改这里
# ... (其他部分) ...
```



# vercel版：Supabase Keep Alive

A lightweight Python serverless project to keep your Supabase database alive.  
It periodically sends a small query to prevent the database from going idle.  
This project is optimized for deployment on Vercel and is intended to be triggered by an external cron service.

## Features

- 🛠 Configurable target table via environment variables
- 🚀 Fully serverless, ideal for Vercel hosting
- 📦 Simple environment setup
- 🆓 Open-source, non-commercial use only
- Vercel Cron

## Project Structure

```
supabase-keepalive/
├── api/
│   └── keepalive.py
├── .env.example
├── requirements.txt
├── vercel.json
└── LICENSE
```

## Getting Started

### 1. Set Up Environment Variables

Create a `.env` file based on the provided `.env.example`:

```env
SUPABASE_CONFIG='[
  {
    "name": "Supabase1",
    "supabase_url": "https://your-project.supabase.co",
    "supabase_key": "your-api-key",
    "table_name": "your_table"
  },
  {
    "name": "Supabase2",
    "supabase_url": "https://another-project.supabase.co",
    "supabase_key": "another-api-key",
    "table_name": "another_table"
  }
]'
```

**Important:**  
Never commit your real `.env` file.  
On Vercel, configure these environment variables in **Project Settings > Environment Variables**.

---

### 2. Deploy to Vercel

- Push the project to a GitHub repository.
- Import the repository into [Vercel](https://vercel.com/).
- Set up environment variables on the Vercel dashboard.
- Deploy your project.

---

### 3. Use Vercel Cron Job
- Edit  `vercel.json` file

---

### 4. Set Up External Cron Job(Optional)

Use any external cron service (such as EasyCron, UptimeRobot, GitHub Actions)  
to periodically trigger your endpoint once per day:

```
GET https://your-vercel-project.vercel.app/api/keepalive
```

---

## API Endpoint

**GET `/api/keepalive`**
**GET `/api/keepalive/all`**
**GET `/api/keepalive/index`** (default index 0)
**GET `/api/keepalive/index/1`** (index 1)
**GET `/api/keepalive/name/Supabase1`** (name Supabase1)

### Response

- Success:  
  ```json
  { "status": "success", "message": "ok" }
  ```
- Failure:  
  ```json
  { "status": "error", "message": "failure" }
  ```

---

## License

This project is licensed under the **MIT** license.

---

## Notes

- This project uses [Supabase Python Client](https://github.com/supabase-community/supabase-py) and [FastAPI](https://fastapi.tiangolo.com/).
- Keep the queried table lightweight to ensure minimal resource usage.
- Supabase databases usually remain active, but periodic pings add an extra layer of stability for serverless applications.

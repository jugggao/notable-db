---
title: Github Actions - 上下文
tags: [CICD/Github Actions]
created: 2022-09-29T07:43:26.744Z
modified: 2022-09-30T06:24:33.829Z
---

# Github Actions - 上下文

## 介绍

### 使用上下文

- 索引语法：github['sha']
- 属性引用语法：github.sha

### 上下文可用性

| workflow 中的键                                  | 允许使用的上下文                                                          | 特殊函数                                       |
| ------------------------------------------------ | ------------------------------------------------------------------------- | ---------------------------------------------- |
| run-name                                         | github, inputs                                                            |                                                |
| concurrency                                      | github, inputs                                                            |                                                |
| env                                              | github, secrets, inputs                                                   |                                                |
| jobs.<job_id>.concurrency                        | github, needs, strategy, matrix, inputs                                   |                                                |
| jobs.<job_id>.container                          | github, needs, strategy, matrix, env, secrets, inputs                     |                                                |
| jobs.<job_id>.container.credentials              | github, needs, strategy, matrix, env, secrets, inputs                     |                                                |
| jobs.<job_id>.container.env.<env_id>             | github, needs, strategy, matrix, job, runner, env, secrets, inputs        |                                                |
| jobs.<job_id>.continue-on-error                  | github, needs, strategy, matrix, inputs                                   |                                                |
| jobs.<job_id>.defaults.run                       | github, needs, strategy, matrix, env, inputs                              |                                                |
| jobs.<job_id>.env                                | github, needs, strategy, matrix, secrets, inputs                          |                                                |
| jobs.<job_id>.environment                        | github, needs, strategy, matrix, inputs                                   |                                                |
| jobs.<job_id>.environment.url                    | github, needs, strategy, matrix, job, runner, env, steps, inputs          |                                                |
| jobs.<job_id>.if                                 | github, needs, inputs                                                     | always, cancelled, success, failure            |
| jobs.<job_id>.name                               | github, needs, strategy, matrix, inputs                                   |                                                |
| jobs.<job_id>.outputs.<output_id>                | github, needs, strategy, matrix, job, runner, env, secrets, steps, inputs |                                                |
| jobs.<job_id>.runs-on                            | github, needs, strategy, matrix, inputs                                   |                                                |
| jobs.<job_id>.secrets.<secrets_id>               | github, needs, strategy, matrix, secrets, inputs                          |                                                |
| jobs.<job_id>.services                           | github, needs, strategy, matrix, inputs                                   |                                                |
| jobs.<job_id>.services.<service_id>.credentials  | github, needs, strategy, matrix, env, secrets, inputs                     |                                                |
| jobs.<job_id>.services.<service_id>.env.<env_id> | github, needs, strategy, matrix, job, runner, env, secrets, inputs        |                                                |
| jobs.<job_id>.steps.continue-on-error            | github, needs, strategy, matrix, job, runner, env, secrets, steps, inputs | hashFiles                                      |
| jobs.<job_id>.steps.env                          | github, needs, strategy, matrix, job, runner, env, secrets, steps, inputs | hashFiles                                      |
| jobs.<job_id>.steps.if                           | github, needs, strategy, matrix, job, runner, env, steps, inputs          | always, cancelled, success, failure, hashFiles |
| jobs.<job_id>.steps.name                         | github, needs, strategy, matrix, job, runner, env, secrets, steps, inputs | hashFiles                                      |
| jobs.<job_id>.steps.run                          | github, needs, strategy, matrix, job, runner, env, secrets, steps, inputs | hashFiles                                      |
| jobs.<job_id>.steps.timeout-minutes              | github, needs, strategy, matrix, job, runner, env, secrets, steps, inputs | hashFiles                                      |
| jobs.<job_id>.steps.with                         | github, needs, strategy, matrix, job, runner, env, secrets, steps, inputs | hashFiles                                      |
| jobs.<job_id>.steps.working-directory            | github, needs, strategy, matrix, job, runner, env, secrets, steps, inputs | hashFiles                                      |
| jobs.<job_id>.strategy                           | github, needs, inputs                                                     |                                                |
| jobs.<job_id>.timeout-minutes                    | github, needs, strategy, matrix, inputs                                   |                                                |
| jobs.<job_id>.with.<with_id>                     | github, needs, strategy, matrix, inputs                                   |                                                |
| on.workflow_call.inputs.<inputs_id>.default      | github, inputs                                                            |                                                |
| on.workflow_call.outputs.<output_id>.value       | github, jobs, inputs                                                      |                                                |

### 打印上下文信息

```yaml
name: Context testing
on: push

jobs:
  dump_contexts_to_log:
    runs-on: ubuntu-latest
    steps:
      - name: Dump GitHub context
        id: github_context_step
        run: echo '${{ toJSON(github) }}'
      - name: Dump job context
        run: echo '${{ toJSON(job) }}'
      - name: Dump steps context
        run: echo '${{ toJSON(steps) }}'
      - name: Dump runner context
        run: echo '${{ toJSON(runner) }}'
      - name: Dump strategy context
        run: echo '${{ toJSON(strategy) }}'
      - name: Dump matrix context
        run: echo '${{ toJSON(matrix) }}'
```

## `github` 上下文

| 属性名称                   | 类型     | 描述                                                                                                                                                                         |
| -------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `github`                   | `object` | 顶级 github 上下文，包含以下所有属性                                                                                                                                         |
| `github.action`            | `string` | 当前运行步骤 `id`。会自动删除特殊字符；如果没有 `id`，则使用 `_run`；如果相同的任务第二次运行则会添加序列号（`_run` -> `_run_2`，`actions/checkout` -> `actions/checkout2`） |
| `github.action_path`       | `string` | action 所在的路径，此属性仅支持复合 actions，你可以使用此路径访问与 action 位于同一仓库下的文件                                                                              |
| `github.action_ref`        | `string` | action 的引用，例如 `v2`                                                                                                                                                     |
| `github.action_repository` | `string` | action 仓库名称，例如 `actions.checkout`                                                                                                                                     |
| `github.action_status`     | `string` | 复合 action 的执行结果                                                                                                                                                       |
| `github.actor`             | `string` | 触发 workflow 运行的用户名                                                                                                                                                   |
| `github.api_url`           | `string` | Github REST API 的 URL                                                                                                                                                       |
| `github.base_ref`          | `string` | 拉取请求的 `base_ref` 或目标分支。仅支持触发事件为 `pull_request` 或 `pull_request_target`                                                                                   |
| `github.env`               | `string` | 设置环境变量文件的路径                                                                                                                                                       |
| `github.event`             | `object` | 触发 workflow 的 event 信息                                                                                                                                                  |
| `github.event_name`        | `string` | 触发 workflow 的 event 名称                                                                                                                                                  |
| `github.graphql_url`       | `string` | GitHub GraphQL API URL                                                                                                                                                       |
| `github.head_ref`          | `string` | 拉取请求的 `head_ref` 或来源分支。仅支持触发事件为 `pull_request` 或 `pull_request_target`                                                                                   |
| `github.job`               | `string` | 当前任务的 `job_id`                                                                                                                                                          |
| `github.ref`               | `string` | 完整的分支或 Tag                                                                                                                                                             |
| `github.ref_name`          | `string` | 短分支或 Tag                                                                                                                                                                 |
| `github.ref_protected`     | `string` | 如果分支受保护则返回 `true`                                                                                                                                                  |
| `github.ref_type`          | `string` | 触发 workflow 的 ref 类型，`branch` 或 `tag`                                                                                                                                 |
| `github.path`              | `string` | 添加 `PATH` 环境变量的命令                                                                                                                                                   |
| `github.repository`        | `string` | 仓库名                                                                                                                                                                       |
| `github.repository_owner`  | `string` | 仓库所属者                                                                                                                                                                   |
| `github.repositoryUrl`     | `string` | 仓库的 Git URL                                                                                                                                                               |
| `github.retention_days`    | `string` | workflow 日志和工件的保留天数                                                                                                                                                |
| `github.run_id`            | `string` | workflow 的唯一 ID，重复运行 ID 不变                                                                                                                                         |
| `github.run_number`        | `string` | workflow 每次运行的编号，从 1 开始递增                                                                                                                                       |
| `github.secret_source`     | `string` | workflow 使用密钥的来源                                                                                                                                                      |
| `github.server_url`        | `string` | Github server URL                                                                                                                                                            |
| `github.sha`               | `string` | Commit ID                                                                                                                                                                    |
| `github.token`             | `string` | Actions 运行时自动设置的 Token                                                                                                                                               |
| `github.triggering_actor`  | `string` | 启动工作流的用户                                                                                                                                                             |
| `github.workflow`          | `string` | workflow 名称                                                                                                                                                                |
| `github.workspace`         | `string` | workflow 工作目录                                                                                                                                                            |

### 上下文示例

```json
{
  "token": "***",
  "job": "dump_contexts_to_log",
  "ref": "refs/heads/master",
  "sha": "a10667c6a3e62c9b8bb2ae4af256ba50eb627caa",
  "repository": "jugggao/Dockerfiles",
  "repository_owner": "jugggao",
  "repository_owner_id": "45712314",
  "repositoryUrl": "git://github.com/jugggao/Dockerfiles.git",
  "run_id": "3149688081",
  "run_number": "2",
  "retention_days": "90",
  "run_attempt": "1",
  "artifact_cache_size_limit": "10",
  "repository_visibility": "public",
  "repository_id": "373431721",
  "actor_id": "45712314",
  "actor": "jugggao",
  "triggering_actor": "jugggao",
  "workflow": "Context testing",
  "head_ref": "",
  "base_ref": "",
  "event_name": "push",
  "event": {
    "after": "a10667c6a3e62c9b8bb2ae4af256ba50eb627caa",
    "base_ref": null,
    "before": "d1200fa277c237be10a964fbd9e3bc053bef697a",
    "commits": [
      {
        "author": {
          "email": "jugg.gao@qq.com",
          "name": "peng.gao",
          "username": "jugggao"
        },
        "committer": {
          "email": "jugg.gao@qq.com",
          "name": "peng.gao",
          "username": "jugggao"
        },
        "distinct": true,
        "id": "a10667c6a3e62c9b8bb2ae4af256ba50eb627caa",
        "message": "Context Testing",
        "timestamp": "2022-09-29T16:13:59+08:00",
        "tree_id": "6f7b00509629ff17ca5dfe60b4b54586ce06adbd",
        "url": "https://github.com/jugggao/Dockerfiles/commit/a10667c6a3e62c9b8bb2ae4af256ba50eb627caa"
      }
    ],
    "compare": "https://github.com/jugggao/Dockerfiles/compare/d1200fa277c2...a10667c6a3e6",
    "created": false,
    "deleted": false,
    "forced": false,
    "head_commit": {
      "author": {
        "email": "jugg.gao@qq.com",
        "name": "peng.gao",
        "username": "jugggao"
      },
      "committer": {
        "email": "jugg.gao@qq.com",
        "name": "peng.gao",
        "username": "jugggao"
      },
      "distinct": true,
      "id": "a10667c6a3e62c9b8bb2ae4af256ba50eb627caa",
      "message": "Context Testing",
      "timestamp": "2022-09-29T16:13:59+08:00",
      "tree_id": "6f7b00509629ff17ca5dfe60b4b54586ce06adbd",
      "url": "https://github.com/jugggao/Dockerfiles/commit/a10667c6a3e62c9b8bb2ae4af256ba50eb627caa"
    },
    "pusher": {
      "email": "45712314+jugggao@users.noreply.github.com",
      "name": "jugggao"
    },
    "ref": "refs/heads/master",
    "repository": {
      "allow_forking": true,
      "archive_url": "https://api.github.com/repos/jugggao/Dockerfiles/{archive_format}{/ref}",
      "archived": false,
      "assignees_url": "https://api.github.com/repos/jugggao/Dockerfiles/assignees{/user}",
      "blobs_url": "https://api.github.com/repos/jugggao/Dockerfiles/git/blobs{/sha}",
      "branches_url": "https://api.github.com/repos/jugggao/Dockerfiles/branches{/branch}",
      "clone_url": "https://github.com/jugggao/Dockerfiles.git",
      "collaborators_url": "https://api.github.com/repos/jugggao/Dockerfiles/collaborators{/collaborator}",
      "comments_url": "https://api.github.com/repos/jugggao/Dockerfiles/comments{/number}",
      "commits_url": "https://api.github.com/repos/jugggao/Dockerfiles/commits{/sha}",
      "compare_url": "https://api.github.com/repos/jugggao/Dockerfiles/compare/{base}...{head}",
      "contents_url": "https://api.github.com/repos/jugggao/Dockerfiles/contents/{+path}",
      "contributors_url": "https://api.github.com/repos/jugggao/Dockerfiles/contributors",
      "created_at": 1622708149,
      "default_branch": "master",
      "deployments_url": "https://api.github.com/repos/jugggao/Dockerfiles/deployments",
      "description": null,
      "disabled": false,
      "downloads_url": "https://api.github.com/repos/jugggao/Dockerfiles/downloads",
      "events_url": "https://api.github.com/repos/jugggao/Dockerfiles/events",
      "fork": false,
      "forks": 0,
      "forks_count": 0,
      "forks_url": "https://api.github.com/repos/jugggao/Dockerfiles/forks",
      "full_name": "jugggao/Dockerfiles",
      "git_commits_url": "https://api.github.com/repos/jugggao/Dockerfiles/git/commits{/sha}",
      "git_refs_url": "https://api.github.com/repos/jugggao/Dockerfiles/git/refs{/sha}",
      "git_tags_url": "https://api.github.com/repos/jugggao/Dockerfiles/git/tags{/sha}",
      "git_url": "git://github.com/jugggao/Dockerfiles.git",
      "has_downloads": true,
      "has_issues": true,
      "has_pages": false,
      "has_projects": true,
      "has_wiki": true,
      "homepage": null,
      "hooks_url": "https://api.github.com/repos/jugggao/Dockerfiles/hooks",
      "html_url": "https://github.com/jugggao/Dockerfiles",
      "id": 373431721,
      "is_template": false,
      "issue_comment_url": "https://api.github.com/repos/jugggao/Dockerfiles/issues/comments{/number}",
      "issue_events_url": "https://api.github.com/repos/jugggao/Dockerfiles/issues/events{/number}",
      "issues_url": "https://api.github.com/repos/jugggao/Dockerfiles/issues{/number}",
      "keys_url": "https://api.github.com/repos/jugggao/Dockerfiles/keys{/key_id}",
      "labels_url": "https://api.github.com/repos/jugggao/Dockerfiles/labels{/name}",
      "language": "Dockerfile",
      "languages_url": "https://api.github.com/repos/jugggao/Dockerfiles/languages",
      "license": null,
      "master_branch": "master",
      "merges_url": "https://api.github.com/repos/jugggao/Dockerfiles/merges",
      "milestones_url": "https://api.github.com/repos/jugggao/Dockerfiles/milestones{/number}",
      "mirror_url": null,
      "name": "Dockerfiles",
      "node_id": "MDEwOlJlcG9zaXRvcnkzNzM0MzE3MjE=",
      "notifications_url": "https://api.github.com/repos/jugggao/Dockerfiles/notifications{?since,all,participating}",
      "open_issues": 0,
      "open_issues_count": 0,
      "owner": {
        "avatar_url": "https://avatars.githubusercontent.com/u/45712314?v=4",
        "email": "45712314+jugggao@users.noreply.github.com",
        "events_url": "https://api.github.com/users/jugggao/events{/privacy}",
        "followers_url": "https://api.github.com/users/jugggao/followers",
        "following_url": "https://api.github.com/users/jugggao/following{/other_user}",
        "gists_url": "https://api.github.com/users/jugggao/gists{/gist_id}",
        "gravatar_id": "",
        "html_url": "https://github.com/jugggao",
        "id": 45712314,
        "login": "jugggao",
        "name": "jugggao",
        "node_id": "MDQ6VXNlcjQ1NzEyMzE0",
        "organizations_url": "https://api.github.com/users/jugggao/orgs",
        "received_events_url": "https://api.github.com/users/jugggao/received_events",
        "repos_url": "https://api.github.com/users/jugggao/repos",
        "site_admin": false,
        "starred_url": "https://api.github.com/users/jugggao/starred{/owner}{/repo}",
        "subscriptions_url": "https://api.github.com/users/jugggao/subscriptions",
        "type": "User",
        "url": "https://api.github.com/users/jugggao"
      },
      "private": false,
      "pulls_url": "https://api.github.com/repos/jugggao/Dockerfiles/pulls{/number}",
      "pushed_at": 1664439274,
      "releases_url": "https://api.github.com/repos/jugggao/Dockerfiles/releases{/id}",
      "size": 20,
      "ssh_url": "git@github.com:jugggao/Dockerfiles.git",
      "stargazers": 0,
      "stargazers_count": 0,
      "stargazers_url": "https://api.github.com/repos/jugggao/Dockerfiles/stargazers",
      "statuses_url": "https://api.github.com/repos/jugggao/Dockerfiles/statuses/{sha}",
      "subscribers_url": "https://api.github.com/repos/jugggao/Dockerfiles/subscribers",
      "subscription_url": "https://api.github.com/repos/jugggao/Dockerfiles/subscription",
      "svn_url": "https://github.com/jugggao/Dockerfiles",
      "tags_url": "https://api.github.com/repos/jugggao/Dockerfiles/tags",
      "teams_url": "https://api.github.com/repos/jugggao/Dockerfiles/teams",
      "topics": [],
      "trees_url": "https://api.github.com/repos/jugggao/Dockerfiles/git/trees{/sha}",
      "updated_at": "2022-09-16T07:26:43Z",
      "url": "https://github.com/jugggao/Dockerfiles",
      "visibility": "public",
      "watchers": 0,
      "watchers_count": 0,
      "web_commit_signoff_required": false
    },
    "sender": {
      "avatar_url": "https://avatars.githubusercontent.com/u/45712314?v=4",
      "events_url": "https://api.github.com/users/jugggao/events{/privacy}",
      "followers_url": "https://api.github.com/users/jugggao/followers",
      "following_url": "https://api.github.com/users/jugggao/following{/other_user}",
      "gists_url": "https://api.github.com/users/jugggao/gists{/gist_id}",
      "gravatar_id": "",
      "html_url": "https://github.com/jugggao",
      "id": 45712314,
      "login": "jugggao",
      "node_id": "MDQ6VXNlcjQ1NzEyMzE0",
      "organizations_url": "https://api.github.com/users/jugggao/orgs",
      "received_events_url": "https://api.github.com/users/jugggao/received_events",
      "repos_url": "https://api.github.com/users/jugggao/repos",
      "site_admin": false,
      "starred_url": "https://api.github.com/users/jugggao/starred{/owner}{/repo}",
      "subscriptions_url": "https://api.github.com/users/jugggao/subscriptions",
      "type": "User",
      "url": "https://api.github.com/users/jugggao"
    }
  },
  "server_url": "https://github.com",
  "api_url": "https://api.github.com",
  "graphql_url": "https://api.github.com/graphql",
  "ref_name": "master",
  "ref_protected": false,
  "ref_type": "branch",
  "secret_source": "Actions",
  "workspace": "/home/runner/work/Dockerfiles/Dockerfiles",
  "action": "__run",
  "event_path": "/home/runner/work/_temp/_github_workflow/event.json",
  "action_repository": "",
  "action_ref": "",
  "path": "/home/runner/work/_temp/_runner_file_commands/add_path_a96e57d5-c512-489e-8a4b-b172b09b0d08",
  "env": "/home/runner/work/_temp/_runner_file_commands/set_env_a96e57d5-c512-489e-8a4b-b172b09b0d08",
  "step_summary": "/home/runner/work/_temp/_runner_file_commands/step_summary_a96e57d5-c512-489e-8a4b-b172b09b0d08",
  "state": "/home/runner/work/_temp/_runner_file_commands/save_state_a96e57d5-c512-489e-8a4b-b172b09b0d08",
  "output": "/home/runner/work/_temp/_runner_file_commands/set_output_a96e57d5-c512-489e-8a4b-b172b09b0d08"
}
```

### 使用示例

```yaml
name: Run CI
on: [push, pull_request]

jobs:
  normal_ci:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run normal CI
        run: ./run-tests

  pull_request_ci:
    runs-on: ubuntu-latest
    if: ${{ github.event_name == 'pull_request' }}
    steps:
      - uses: actions/checkout@v3
      - name: Run PR CI
        run: ./run-additional-pr-ci
```

## `env` 上下文

| 属性名称         | 类型     | 描述                                      |
| ---------------- | -------- | ----------------------------------------- |
| `env`            | `object` | workflow 任务中的每个任务都会更改此上下文 |
| `env.<env_name>` | `string` | 指定的环境变量的值                        |

### 上下文示例

```json
{
  "first_name": "Mona",
  "super_duper_var": "totally_awesome"
}
```

### 使用示例

```yaml
name: Hi Mascot
on: push
env:
  mascot: Mona
  super_duper_var: totally_awesome

jobs:
  windows_job:
    runs-on: windows-latest
    steps:
      - run: echo 'Hi ${{ env.mascot }}' # Hi Mona
      - run: echo 'Hi ${{ env.mascot }}' # Hi Octocat
        env:
          mascot: Octocat
  linux_job:
    runs-on: ubuntu-latest
    env:
      mascot: Tux
    steps:
      - run: echo 'Hi ${{ env.mascot }}' # Hi Tux
```

## `job` 上下文

| 属性名称                            | 类型     | 描述                                                   |
| ----------------------------------- | -------- | ------------------------------------------------------ |
| `job`                               | `object` | workflow 任务中的每个任务都会更改此上下文              |
| `job.container`                     | `string` | 启动的容器相关信息                                     |
| `job.container.id`                  | `string` | 容器 ID                                                |
| `job.container.network`             | `string` | 容器网络                                               |
| `job.services`                      | `string` | 容器运行的服务信息                                     |
| `job.services.<service_id>.id`      | `string` | 容器服务的 ID                                          |
| `job.services.<service_id>.network` | `string` | 容器服务的网络                                         |
| `job.services.<service_id>.ports`   | `string` | 容器服务的端口号                                       |
| `job.status                         | `string` | 当前任务的状态，值为 `success`、`failure`、`cancelled` |

### 上下文示例

```json
{
  "status": "success",
  "container": {
    "network": "github_network_53269bd575974817b43f4733536b200c"
  },
  "services": {
    "postgres": {
      "id": "60972d9aa486605e66b0dad4abb638dc3d9116f566579e418166eedb8abb9105",
      "ports": {
        "5432": "49153"
      },
      "network": "github_network_53269bd575974817b43f4733536b200c"
    }
  }
}
```

### 使用示例

```yaml
name: PostgreSQL Service Example
on: push
jobs:
  postgres-job:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres
        env:
          POSTGRES_PASSWORD: postgres
        options: --health-cmd pg_isready --health-interval 10s --health-timeout 5s --health-retries 5
        ports:
          # Maps TCP port 5432 in the service container to a randomly chosen available port on the host.
          - 5432

    steps:
      - uses: actions/checkout@v3
      - run: pg_isready -h localhost -p ${{ job.services.postgres.ports[5432] }}
      - run: ./run-tests
```

## jobs 上下文

`jobs` 上下文只在被引用的 workflow 中使用，并且只能在引用的 workflow 

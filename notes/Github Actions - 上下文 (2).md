---
title: Github Actions - 上下文
created: 2022-09-29T07:43:26.744Z
modified: 2022-09-29T07:49:38.961Z
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

## github 上下文

| 属性名称                   | 类型     | 描述                                                                                                                                                                                                                                                                                                                                                                               |
| -------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `github`                   | `object` | 顶级 github 上下文，包含以下所有属性                                                                                                                                                                                                                                                                                                                                               |
| `github.action`            | `string` | 当前运行步骤 `id`。会自动删除特殊字符；如果没有 `id`，则使用 `_run`；如果相同的任务第二次运行则会添加序列号（`_run` -> `_run_2`，`actions/checkout` -> `actions/checkout2`）                                                                                                                                                                                                       |
| `github.action_path`       | `string` | action 所在的路径，此属性仅支持复合 actions，你可以使用此路径访问与 action 位于同一仓库下的文件                                                                                                                                                                                                                                                                                    |
| `github.action_ref`        | `string` | action 的引用，例如 `v2`                                                                                                                                                                                                                                                                                                                                                           |
| `github.action_repository` | `string` | action 仓库名称，例如 `actions.checkout`                                                                                                                                                                                                                                                                                                                                           |
| `github.action_status`     | `string` | 复合 action 的执行结果                                                                                                                                                                                                                                                                                                                                                             |
| `github.actor`             | `string` | 触发 workflow 运行的用户名                                                                                                                                                                                                                                                                                                                                                         |
| `github.api_url`           | `string` | Github REST API 的 URL                                                                                                                                                                                                                                                                                                                                                             |
| `github.base_ref`          | `string` | 拉取请求的 `base_ref` 或目标分支。仅支持触发事件为 `pull_request` 或 `pull_request_target`                                                                                                                                                                                                                                                                                         |
| `github.env`               | `string` | 设置环境变量文件的路径                                                                                                                                                                                                                                                                                                                                                             |
| `github.event`             | `object` | 触发 workflow 的 event 信息                                                                                                                                                                                                                                                                                                                                                        |
| `github.event_name`        | `string` | 触发 workflow 的 event 名称                                                                                                                                                                                                                                                                                                                                                        |
| `github.graphql_url`       | `string` | GitHub GraphQL API URL                                                                                                                                                                                                                                                                                                                                                             |
| `github.head_ref`          | `string` | 拉取请求的 `head_ref` 或来源分支。仅支持触发事件为 `pull_request` 或 `pull_request_target`                                                                                                                                                                                                                                                                                         |
| `github.job`               | `string` | 当前任务的 `job_id`                                                                                                                                                                                                                                                                                                                                                                |
| `github.ref`               | `string` | 完整的分支或 Tag                                                                                                                                                                                                                                                                                                                                                                   |
| `github.ref_name`          | `string` | 短分支或 Tag                                                                                                                                                                                                                                                                                                                                                                       |
| `github.ref_protected`     | `string` | true if branch protections are configured for the ref that triggered the workflow run.                                                                                                                                                                                                                                                                                             |
| `github.ref_type`          | `string` | The type of ref that triggered the workflow run. Valid values are branch or tag.                                                                                                                                                                                                                                                                                                   |
| `github.path`              | `string` | Path on the runner to the file that sets system PATH variables from workflow commands. This file is unique to the current step and is a different file for each step in a job. For more information, see "Workflow commands for GitHub Actions."                                                                                                                                   |
| `github.repository`        | `string` | The owner and repository name. For example, Codertocat/Hello-World.                                                                                                                                                                                                                                                                                                                |
| `github.repository_owner`  | `string` | The repository owner's name. For example, Codertocat.                                                                                                                                                                                                                                                                                                                              |
| `github.repositoryUrl`     | `string` | The Git URL to the repository. For example, git://github.com/codertocat/hello-world.git.                                                                                                                                                                                                                                                                                           |
| `github.retention_days`    | `string` | The number of days that workflow run logs and artifacts are kept.                                                                                                                                                                                                                                                                                                                  |
| `github.run_id`            | `string` | A unique number for each workflow run within a repository. This number does not change if you re-run the workflow run.                                                                                                                                                                                                                                                             |
| `github.run_number`        | `string` | A unique number for each run of a particular workflow in a repository. This number begins at 1 for the workflow's first run, and increments with each new run. This number does not change if you re-run the workflow run.                                                                                                                                                         |
| `github.run_attempt`       | `string` | A unique number for each attempt of a particular workflow run in a repository. This number begins at 1 for the workflow run's first attempt, and increments with each re-run.                                                                                                                                                                                                      |
| `github.secret_source`     | `string` | The source of a secret used in a workflow. Possible values are None, Actions, Dependabot, or Codespaces.                                                                                                                                                                                                                                                                           |
| `github.server_url`        | `string` | The URL of the GitHub server. For example: https://github.com.                                                                                                                                                                                                                                                                                                                     |
| `github.sha`               | `string` | The commit SHA that triggered the workflow. The value of this commit SHA depends on the event that triggered the workflow. For more information, see "Events that trigger workflows." For example, ffac537e6cbbf934b08745a378932722df287a53.                                                                                                                                       |
| `github.token`             | `string` | A token to authenticate on behalf of the GitHub App installed on your repository. This is functionally equivalent to the GITHUB_TOKEN secret. For more information, see "Automatic token authentication." Note: This context property is set by the Actions runner, and is only available within the execution steps of a job. Otherwise, the value of this property will be null. |
| `github.triggering_actor`  | `string` | The username of the user that initiated the workflow run. If the workflow run is a re-run, this value may differ from github.actor. Any workflow re-runs will use the privileges of github.actor, even if the actor initiating the re-run (github.triggering_actor) has different privileges.                                                                                      |
| `github.workflow`          | `string` | The name of the workflow. If the workflow file doesn't specify a name, the value of this property is the full path of the workflow file in the repository.                                                                                                                                                                                                                         |
| `github.workspace`         | `string` | The default working directory on the runner for steps, and the default location of your repository when using the checkout action.                                                                                                                                                                                                                                                 |

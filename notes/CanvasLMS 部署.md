---
title: CanvasLMS 部署
tags: [Work]
created: 2022-11-05T11:34:08.798Z
modified: 2022-11-05T12:52:10.580Z
---

# CanvasLMS 部署

## 数据库安装与配置

### PostgreSQL

```shell
apt-get update -y && apt-get upgrade -y

curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo gpg --dearmor -o /usr/share/keyrings/postgresql-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/postgresql-keyring.gpg] http://apt.postgresql.org/pub/repos/apt/ bullseye-pgdg main" | sudo tee /etc/apt/sources.list.d/postgresql.list

sudo apt install postgresql-12

systemctl enable --now postgresql
```

```shell
sed -ri "s/^#?(listen_addresses = ).*/\1'*'/" /etc/postgresql/12/main/postgresql.conf

sed -ri 's|^(host.*all.*all.*)127.0.0.1/32|\10.0.0.0/0|' /etc/postgresql/12/main/pg_hba.conf
```

```shell
sudo -u postgres createuser canvas --no-createdb --no-superuser --no-createrole --pwprompt
# canvas

sudo -u postgres createdb canvas_production --owner=canvas
```

```shell
sudo -u postgres createuser $USER
sudo -u postgres psql -c "alter user $USER with superuser" postgres
```

### Redis

```shell
apt-get install -y redis-server

systemctl enable --now redis-server.service
```

```shell
sed -ri 's/^(bind ).*/\10.0.0.0/' /etc/redis/redis.conf

sed -ri 's/^(# )?(requirepass ).*/\2canvaslms/' /etc/redis/redis.conf

systemctl restart redis-server.service

redis-cli -h 127.0.0.1 -a canvaslms ping
```


## 代码安装

### 获取代码

```shell
git clone -b prod https://github.com/instructure/canvas-lms.git canvas

mkdir -p /var/canvas

cd canvas

cp -av . /var/canvas

cd /var/canvas
```

### Ruby

```shell
apt-get install ruby2.7 ruby2.7-dev zlib1g-dev libxml2-dev \
	libsqlite3-dev postgresql libpq-dev \
	libxmlsec1-dev curl make g++
```

### Node.js

```shell
curl -sL https://deb.nodesource.com/setup_16.x | sudo -E bash -

sudo apt-get install nodejs

sudo npm install -g npm@latest
```


### Ruby Gems

```shell
apt-get install libldap2-dev libidn11-dev

gem install bundler --version 2.2.19
bundle _2.2.19_ install --path vendor/bundle
```

### Python

```shell
apt-get  install python
```

### Yarn

```shell
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -

echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list

sudo apt-get update && sudo apt-get install yarn

yarn install
```


## CanvasLMS 配置

### 文件存储

```
$ cp config/file_store.yml.example config/file_store.yml

$ mkdir -p data/files

$ cat config/file_store.yml
development:
  storage: local
  path_prefix: data/files
 
test:
  storage: local
  path_prefix: data/files
 
production:
  storage: local
  path_prefix: data/files
```

### 数据库

```shell
$ cp config/database.yml.example config/database.yml

$ cat config/database.yml
test:
  adapter: postgresql
  encoding: utf8
  database: canvas_test
  host: localhost
  username: canvas
  password: your_password
  timeout: 5000

development:
  adapter: postgresql
  encoding: utf8
  database: canvas_development
  password: your_password
  timeout: 5000
  secondary:
    replica: true
    username: canvas_readonly_user

production:
  adapter: postgresql
  encoding: utf8
  database: canvas_production
  host: localhost
  username: canvas
  password: canvas
  timeout: 5000
```

### 缓存配置

```shell
$ for config in cache_store redis; do cp config/$config.yml.example config/$config.yml; done

$ cat config/cache_store.yml
test:
  cache_store: redis_cache_store
development:
  cache_store: redis_cache_store
production:
  cache_store: redis_cache_store

$ cat config/redis.yml
test:
  servers:
  - redis://localhost:6379
  database: 1
development:
  servers:
  - redis://localhost:6379
  database: 1
production:
  servers:
  - redis://localhost:6379
  database: 1
```

### URL

```
$ cp config/domain.yml.example config/domain.yml

$ cat config/domain.yml
test:
  domain: "test-lms.example.com"
  ssl: false
 
development:
  domain: "dev-lms.example.com"
  ssl: false
 
production:
  domain: "pre-lms.rvedu.com.cn"
  ssl: true
```

### 安全配置

```shell
$ cp config/security.yml.example config/security.yml
 
$ cat config/security.yml
production: &default
  encryption_key: facdd3a131ddd8988b14f6e4e01039c93cfa0160
 
development:
  <<: *default
 
test:
  <<: *default
```

### 其他配置

```shell
$ for config in delayed_jobs external_migration; do cp config/$config.yml.example config/$config.yml; done
```

### 初始化资产

```shell
$ pwd
/var/canvas
 
$ mkdir -p log tmp/pids public/assets app/stylesheets/brandable_css_brands
$ touch Gemfile.lock
$ touch log/production.log
```

```shell
$ yarn install
 
# 注意这一步开始选择环境类型
$ COMPILE_ASSETS_BRAND_CONFIGS=0 RAILS_ENV=production bundle exec rake canvas:compile_assets
```

> 后续有代码更新只需执行 `RAILS_ENV=production bundle exec rake brand_configs:generate_and_upload_all`

### 初始化数据库

```shell
$ export CANVAS_LMS_ADMIN_EMAIL=jiahe.xie@ambow.com
$ export CANVAS_LMS_ADMIN_EMAIL=Ambow99999999
$ export CANVAS_LMS_ACCOUNT_NAME=jiahe.xie
$ export CANVAS_LMS_STATS_COLLECTION=opt_out
 
$ RAILS_ENV=production bundle exec rake db:initial_setup
```

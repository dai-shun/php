按下面顺序执行命令
# 编译
```
docker-compose up build
```
# 启动
```
docker-compose up --force-recreate -d
```
# 安装依赖
```
docker-compose exec -T app composer install
```
# 初始化环境
```
docker-compose exec -T app php init --env=Development --overwrite=All
```
# 执行yii命令
```
docker-compose exec -T app php yii
```


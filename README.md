## Pulsar 单机部署

通过ansible进行部署

### 部署前准备 

本机必备

- ansible

目标服务器

- openjdk 17+

注意：根据实际情况修改 `hosts`内容。


### 部署

如果已有 签名用户token的 私钥，请将文件放在本项目根目录下，并保持与 `hosts` 中 `secret_key`同样的文件名。如果没有私钥没关系，部署服务会自动生成。

```
ansible-playbook -i host deploy.playbook.yml
```

生成的用户token会存在于 `user-token`下， 生成token用的私钥在 本项目根目录下，请妥善保管，避免泄露

### 签发一个用户token

```
ansible-playbook -i hosts register_user.yml -e "user=$user"
```

请使用 自定义的名称 替换命令行中的 `$user`

比如：

签名一个pay用户

```
ansible-playbook -i hosts register_user.yml -e "user=pay"
```

### 创建一个topic

```
ansible-playbook -i hosts create_topic.playbook.yml -e "tenants=$tenants namespaces=$namespaces topics=$topics"
```

将命令行中的 $tenants，$namespaces，$topics 替换成自定义名称，如:

```
ansible-playbook -i hosts create_topic.playbook.yml -e "tenants=j3cx namespaces=posts topics=manage"
```

### 给用户发权限

```
ansible-playbook -i hosts permission.playbook.yml -e "topic=$topic user=$user actions=$actions"
```

将命令行中的 $topic $user $actions 替换成自定义名称，如:

```
ansible-playbook -i hosts permission.playbook.yml -e "topic=persistent://j3cx/posts/manage user=pay.comments2 actions=consume"
```

### 注意

1. 如果启动失败，抛出 类似  `org.apache.pulsar.broker.PulsarService` 试着禁止 ipv6 在尝试。
2. 如果内存很小， 启动时环境变量 `PULSAR_EXTRA_OPTS="-Xms512m -Xmx512m"`

## 如何通过 env.yaml 来部署不同的名字的函数
### 创建3个环境，创建完的环境会在env.yaml中
```
s env init --project demo-env --name testing --type testing
```
```
s env init --project demo-env --name uat --type testing
```
```
s env init --project demo-env --name prod --type production
```

### 修改test环境的overlays
指定函数名和环境名关联

`functionName: ${source.functionName}-${this.project}-${this.name}`

1. $source 表示s.yaml中对应的props
2. $this 表示env.yaml中的属性

```yaml
overlays:
  # 按组件进行配置覆盖
  components:
    fc3:
      functionName: ${source.functionName}-${this.project}-${this.name}
      timeout: 50
      instanceConcurrency: 1
  # 指定资源进行覆盖
  resources:
    api1:
      timeout: 100
    api2:
      instanceConcurrency: 10
```
### 修改uat环境的overlays
指定函数名和环境名关联

```yaml
overlays:
  # 按组件进行配置覆盖
  components:
    fc3:
      functionName: ${source.functionName}-${this.project}-${this.name}
      timeout: 50
      instanceConcurrency: 1
```
### 修改prod环境的overlays
指定函数名和环境名关联

```yaml
overlays:
  resources:
    api1:
      functionName: api1-prod
      timeout: 100
      vpcConfig: auto # 也可以指定自己的VPC
    api2:
      functionName: api2-prod
      instanceConcurrency: 10
      vpcConfig: auto # 也可以指定自己的VPC
      # 修改启动参数，注入env信息
      customRuntimeConfig:
        args:
          - --env=prod 
```
### 预览配置
```
s preview --env testing
s preview --env uat
s preview --env prod
```
### 部署服务
```
s deploy --env testing
s deploy --env prod
```

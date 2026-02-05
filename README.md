# infra-projects

基础设施依赖管理项目，提供统一的依赖版本和构建配置。

## 架构设计

采用 **Spring Boot 标准架构**：

```
infra-parent
  ├─ 定义所有依赖版本属性
  ├─ 定义所有插件版本属性
  └─ 配置 pluginManagement
       ↑
       │ 继承
       │
infra-dependencies-bom
  └─ 定义 dependencyManagement（继承 parent 的属性）
```

## 模块说明

| 模块 | 职责 | 类比 |
|------|------|------|
| `infra-parent` | 属性定义 + 插件管理 | `spring-boot-starter-parent` |
| `infra-dependencies-bom` | 依赖版本管理（继承 parent） | `spring-boot-dependencies` |
  
## 技术栈

- **Spring Boot**: 3.3.3
- **Spring Cloud**: 2023.0.1
- **JDK**: 17+

## 使用方式

### 方式一：继承父工程（推荐）

获得依赖版本管理 + 构建配置：

```xml
<parent>
    <groupId>com.qbit.framework</groupId>
    <artifactId>infra-parent</artifactId>
    <version>0.0.1</version>
</parent>
```

**包含：**
- ✅ 所有依赖版本统一管理
- ✅ 编译插件配置（lombok、mapstruct、mybatis-flex 注解处理器）
- ✅ 测试、打包、源码、文档等插件配置

### 方式二：仅导入 BOM

仅需要依赖版本管理，自行管理构建配置：

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>com.qbit.framework</groupId>
            <artifactId>infra-dependencies-bom</artifactId>
            <version>0.0.1</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

**包含：**
- ✅ 所有依赖版本统一管理
- ❌ 不包含任何构建配置

> **说明**：虽然 BOM 继承了 `infra-parent`，但通过 `import` 导入时，只会获取 `<dependencyManagement>` 中的内容，属性和插件配置不会被引入。

## 本地构建

```bash
mvn clean install -DskipTests
```

## 设计原则

- **属性统一管理**：所有版本号只在 `infra-parent` 定义一次，避免重复维护
- **BOM 纯粹性**：`infra-dependencies-bom` 虽继承 parent，但仅包含 `<dependencyManagement>`，可被安全导入
- **职责分离**：依赖版本管理（BOM）与构建配置（parent）分离，按需选择使用方式
- **遵循业界标准**：架构设计与 Spring Boot、Spring Cloud 保持一致

## 维护指南

### 更新依赖版本

只需在 `infra-parent/pom.xml` 的 `<properties>` 中修改对应版本号：

```xml
<properties>
    <lombok.version>1.18.38</lombok.version>
    <mapstruct.version>1.6.3</mapstruct.version>
    <!-- ... 其他版本 ... -->
</properties>
```

### 构建顺序

Maven Reactor 自动按依赖关系构建：
1. `infra-parent` (定义属性)
2. `infra-dependencies-bom` (继承属性)
3. `infra-projects` (聚合器)

# infra-projects

基础设施依赖管理项目，提供统一的依赖版本和构建配置。

## 模块说明

| 模块 | 职责 | 类比 |
|------|------|------|
| `infra-dependencies-bom` | 仅依赖版本管理 | `spring-boot-dependencies` |
| `infra-parent` | 构建配置 + 插件管理 | `spring-boot-starter-parent` |
  
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

## 本地构建

```bash
mvn clean install -DskipTests
```

## 设计原则

- **BOM 纯粹性**：`infra-dependencies-bom` 仅包含 `<dependencyManagement>`，不含 `<build>` 配置
- **职责分离**：依赖版本管理与构建配置分离，按需选择使用方式

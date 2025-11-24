# infra-projects

一个遵循 Spring 生态设计的多模块仓库，提供：
- `infra-dependencies`：纯 BOM，用于统一依赖版本（类似 `spring-boot-dependencies`）
- `infra-parent`：父 POM，用于统一构建属性与插件管理（类似 `spring-boot-starter-parent`）

## 模块结构
- 根聚合器：`pom.xml`
  - `infra-dependencies/`：仅包含 `<dependencyManagement>` 和版本属性
  - `infra-parent/`：包含通用 `<properties>`、`<pluginManagement>`，并 `import` 本仓库 BOM

## 兼容与基线
- Spring Boot：`3.3.3`
- Spring Cloud：`2023.0.1`
- JDK：`17`（如需 `21`，可在 `infra-parent/pom.xml` 中将 `java.version` 调整为 `21`）

## 快速开始
### 作为父工程使用（推荐）
在你的业务项目 `pom.xml`：
```xml
<parent>
  <groupId>com.qbit.framework</groupId>
  <artifactId>infra-parent</artifactId>
  <version>0.0.1</version>
</parent>
```
效果：
- 自动导入 `infra-dependencies` BOM，对齐所有依赖版本
- 统一 `maven-compiler-plugin` 等插件配置与注解处理器

### 仅导入 BOM（不继承父工程）
在你的业务项目 `pom.xml`：
```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>com.qbit.framework</groupId>
      <artifactId>infra-dependencies</artifactId>
      <version>0.0.1</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```
效果：
- 仅进行依赖版本对齐，不改变你的插件与构建属性

## 构建
在仓库根目录：
```bash
mvn -DskipTests clean install
```

## 说明
- `infra-dependencies` 不包含 `<parent>` 和 `<build>`，确保作为纯 BOM 被安全传递
- `infra-parent` 通过 `<dependencyManagement>` `import` 本仓库 BOM，以实现父工程继承即可生效的依赖版本管理

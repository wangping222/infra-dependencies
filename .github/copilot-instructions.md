<!-- .github/copilot-instructions.md for infra-dependencies -->
# Copilot / AI Agent Instructions — infra-dependencies

Purpose: quickly orient an AI coding agent so it can make safe, correct changes to this multi-module Maven repo.

- **Big picture**: This is a Maven aggregator (`pom.xml`) with two published artifacts:
  - `infra-dependencies-bom` (folder: `infra-dependencies-bom/`): a pure BOM that centralizes dependency versions and plugin property values. It intentionally contains no `<parent>` or `<build>` so it can be imported safely.
  - `infra-parent` (folder: `infra-parent/`): a parent POM that imports the BOM and provides `pluginManagement`, default properties (e.g. `java.version`), and compilation/annotation-processor configuration.

- **Why this structure**: keep version management (BOM) separate from build/plugin defaults (parent) so downstream projects can either import the BOM or inherit from the parent depending on needs.

- **Key files to consult**:
  - `README.md` — high-level intent and quick start (root README).
  - `infra-dependencies-bom/pom.xml` — source of truth for dependency versions and many plugin configs.
  - `infra-parent/pom.xml` — pluginManagement and default build/compiler settings.
  - Root `pom.xml` — aggregator listing modules.

- **Versions & baseline** (from BOM/parent):
  - Java baseline: `17` (property `java.version`).
  - Spring Boot: `3.3.3` (see `spring.boot.dependencies.version`).
  - Spring Cloud: `2023.0.1`.

- **Developer workflows / common commands**:
  - Build locally (root):
    ```bash
    mvn -DskipTests clean install
    ```
  - Run tests for a module:
    ```bash
    mvn -pl <module> test
    ```
  - If you need to change Java baseline: edit `infra-parent/pom.xml` and update the `java.version` property.

- **Patterns & conventions for edits**:
  - When adding or updating library versions, modify `infra-dependencies-bom/pom.xml`: add/update the property (if present) and the dependency entry in `<dependencyManagement>`.
  - When changing plugin defaults (compiler args, annotation processors, flatten behavior), modify `infra-parent/pom.xml`'s `<pluginManagement>` section.
  - Do NOT add `<parent>` or `<build>` to `infra-dependencies-bom` — it should remain a pure BOM.
  - The BOM already configures common annotation processors (lombok, mapstruct, mybatis-flex); add processors there if new compile-time processors are required by downstream modules.

- **Build / CI hints**:
  - The BOM configures `maven-enforcer-plugin` requiring Java >= 17 and enforces dependency convergence. Avoid introducing conflicting transitive versions — prefer bumping the central BOM property.
  - The surefire configuration is tuned for parallel tests (`parallel=methods`, `threadCount=1C`). If debugging tests, run single-threaded or pass `-DforkCount=0` / adjust surefire props.

- **Integration points & notable dependencies** (examples to be mindful of):
  - Cloud / infra libs: Nacos (`spring-cloud-alibaba-dependencies`), Alibaba OSS, Alibaba DM/ECS SDKs.
  - AWS SDK: `software.amazon.awssdk:s3`, `sesv2`.
  - Persistence/ORM: MyBatis + MyBatis-Flex.
  - MapStruct + Lombok: annotation-processor coordination is already configured in both BOM and parent.

- **Safe change checklist for PRs made by an AI agent**:
  - Update the centralized version/property in `infra-dependencies-bom/pom.xml` (not per-module) for any third-party version bumps.
  - If modifying build behavior, prefer `infra-parent/pom.xml` pluginManagement edits rather than changing many modules.
  - Run `mvn -DskipTests clean install` locally to ensure the multi-module reactor builds.
  - If adding new dependencies that could cause convergence issues, run a dependency analysis with `mvn dependency:tree` and prefer updating the BOM.

- **Examples (copyable)**:
  - Import BOM in a downstream project without inheriting the parent:
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
  - Use the parent POM in a downstream project:
    ```xml
    <parent>
      <groupId>com.qbit.framework</groupId>
      <artifactId>infra-parent</artifactId>
      <version>0.0.1</version>
    </parent>
    ```

If any section is unclear or you want extra examples (e.g., typical PR changes, sample release procedure, or CI scripts), tell me which area to expand and I will update this file.

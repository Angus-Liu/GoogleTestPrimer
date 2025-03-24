---
layout: cover
background: /imgs/background.jpeg
theme: default
transition: default
---

# GoogleTest Primer

[C++ 测试驱动开发（TDD）工具 GoogleTest 入门指南](https://google.github.io/googletest/primer.html)

工欲善其事，必先利其器。——《论语 · 卫灵公》

<div class="abs-bl ml-14 mb-12 flex items-center" >
	<img src="https://avatars.githubusercontent.com/u/24972887?v=4" class="rounded-full w-15">
	<div class="ml-3 flex flex-col text-left">
		<span class="font-300">程序员小刀</span>
		<span>Angus-Liu</span>
	</div>
</div>

---

# 为什么选择 GoogleTest？

- 🧑🏻‍💻 帮助编写更好的 C++ 测试
- 😄 基于 xUnit 架构，易于上手
- ⚔ 支持多平台（Linux、Windows、Mac）
- 🛡️ 支持各种类型的测试，不仅限于单元测试
- ✅ 优秀测试的要点 & GoogleTest 的作用：
  - 独立 & 可重复：通过对象隔离每个测试，并支持单独运行失败测试以便快速调试
  - 组织良好：GoogleTest 通过测试套件分组相关测试，便于维护和跨项目协作
  - 可移植 & 可复用：支持多平台、多编译器，适用于不同配置，确保测试代码的跨平台性
  - 提供详细失败信息：失败时提供充分的错误信息，允许非致命失败，使单次测试循环能发现多个问题
  - 简化测试配置：自动管理测试集合，无需手动枚举，让开发者专注于测试内容
  - 执行速度快：支持测试间共享资源，仅进行一次设置 / 销毁，避免相互依赖，提高执行效率

---

# 关键术语

- **Assertion（断言）**：检查条件是否为真的语句，结果可以是成功，非致命故障或致命故障
- **Test / Test Case（测试用例）**：针对特定功能或行为的单个测试，使用断言验证代码是否按预期工作
- **Test Suite（测试套件）**：一组相关测试的集合，通常用于组织测试用例
- **Test Fixture（测试夹具）**：为测试套件中的测试提供统一的初始化和清理逻辑，以及共享的环境

<div class="mt-10 flex justify-center">
```plantuml
@startuml
object "Test Suite" {
  - 组织多个 Test Fixture 或 Test Case
  + 提供批量执行的能力
}

object "Test Fixture" {
  # 提供初始化和清理逻辑
  # 允许多个测试共享环境
}

object "Test / Test Case" {
  - 具体的测试方法
  - 断言代码行为
}

object "Assertion" {
  + 验证代码行为是否符合预期
  + 失败时触发测试失败
}

"Test Suite" *--> "Test Fixture" : 组合关系（可选）
"Test Suite" o--> "Test / Test Case" : 组织多个 Test Case
"Test / Test Case" .left.> "Test Fixture" : 获取共享环境
"Test / Test Case" .right.> "Assertion" : 使用断言验证行为
@enduml
```
</div>

---

# 编写第一个测试

```cpp
#include <gtest/gtest.h>

TEST(HelloTest, BasicAssertions) {
  EXPECT_EQ(1 + 1, 2);
  ASSERT_TRUE(true);
}
```

- 使用 `TEST(TestSuiteName, TestName)` 宏定义测试
- `EXPECT_*`：非致命断言（失败时继续执行）
- `ASSERT_*`：致命断言（失败时终止测试）

---

# 断言（Assertions）
### 基本断言
- `EXPECT_TRUE(condition) / ASSERT_TRUE(condition)`
- `EXPECT_FALSE(condition) / ASSERT_FALSE(condition)`

### 二元比较
- `EXPECT_EQ(val1, val2) / ASSERT_EQ(val1, val2)`
- `EXPECT_NE(val1, val2) / ASSERT_NE(val1, val2)`
- `EXPECT_LT(val1, val2) / ASSERT_LT(val1, val2)`
- `EXPECT_LE(val1, val2) / ASSERT_LE(val1, val2)`
- `EXPECT_GT(val1, val2) / ASSERT_GT(val1, val2)`
- `EXPECT_GE(val1, val2) / ASSERT_GE(val1, val2)`

---

# 测试夹具（Test Fixtures）

## 共享测试数据

```cpp
class FooTest : public testing::Test {
 protected:
  void SetUp() override { /* 初始化代码 */ }
  void TearDown() override { /* 清理代码 */ }
};

TEST_F(FooTest, Test1) {
  // 共享测试数据
}
```

- `SetUp()`：每个测试前运行
- `TearDown()`：每个测试后运行
- `TEST_F()` 适用于 `Test Fixture`

---

# 参数化测试（Parameterized Tests）

### 值参数化测试

```cpp
class FooTest : public testing::TestWithParam<int> {};
INSTANTIATE_TEST_SUITE_P(
    FooTestInstantiation,
    FooTest,
    testing::Values(1, 2, 3));
```

- `TestWithParam<T>` 适用于值参数化测试
- `INSTANTIATE_TEST_SUITE_P()` 生成多个测试实例

---

# 类型参数化测试（Typed Tests）

```cpp
template <typename T>
class FooTest : public testing::Test {};

using MyTypes = ::testing::Types<int, double>;
TYPED_TEST_SUITE(FooTest, MyTypes);

TYPED_TEST(FooTest, Test1) {
  // 针对不同类型运行相同测试
}
```

- 适用于泛型代码测试

---

# 死亡测试（Death Tests）

```cpp
EXPECT_DEATH(Foo(), "Error message");
```

- 用于测试程序崩溃场景

---

# 测试事件监听器（Test Event Listeners）

```cpp
class MyTestEventListener : public testing::TestEventListener {
 public:
  void OnTestStart(const testing::TestInfo& test_info) override {
    std::cout << "Starting test: " << test_info.name() << std::endl;
  }
};

testing::UnitTest::GetInstance()->listeners().Append(new MyTestEventListener);
```

- 监听测试执行的各个阶段

---

# GoogleTest 运行选项

- `--gtest_filter=*` 选择运行特定的测试
- `--gtest_repeat=N` 运行测试 N 次
- `--gtest_shuffle` 随机顺序运行测试
- `--gtest_output=xml:report.xml` 生成 XML 测试报告

---

# 结论

- GoogleTest 是 C++ 测试框架的首选
- 提供强大的断言、测试夹具、参数化测试等功能
- 支持 TDD，提高代码质量与可维护性

---
layout: end
---

# Q&A

有什么问题吗？ 😊
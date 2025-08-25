<h1 align="center">Solidity基础</h1>

<p align="center">
  <img src="https://img.shields.io/badge/Markdown-模板-blue.svg" />
  <img src="https://img.shields.io/badge/Version-1.0-green.svg" />
</p>

# 📚Solidity

## 📘 Solidity 值类型笔记

## 🔹 1. 布尔型（bool）

布尔型是二值变量，取值为 **`true`** 或 **`false`**。

```
// 布尔值
bool public _bool = true;
```

**常见运算符：**

- ❌ `!` 逻辑非
- 🤝 `&&` 逻辑与 (and)
- 🔀 `||` 逻辑或 (or)
- ✅ `==` 等于
- 🚫 `!=` 不等于

```solidity
// 布尔运算
bool public _bool1 = !_bool;             // 取非
bool public _bool2 = _bool && _bool1;    // 与
bool public _bool3 = _bool || _bool1;    // 或
bool public _bool4 = _bool == _bool1;    // 相等
bool public _bool5 = _bool != _bool1;    // 不相等
```

🔎 运行结果：

- `_bool = true`
- `_bool1 = false`
- `_bool2 = false`
- `_bool3 = true`
- `_bool4 = false`
- `_bool5 = true`

⚡ **短路规则 (Short-circuiting)：**

- `f(x) || g(y)`：若 `f(x) = true`，则 `g(y)` **不会执行**。
- `f(x) && g(y)`：若 `f(x) = false`，则 `g(y)` **不会执行**。

------

## 🔹 2. 整型（int / uint）

整型用于存储整数。

```solidity
int public _int = -1;               // 有符号整数
uint public _uint = 1;              // 无符号整数
uint256 public _number = 20220330;  // 256 位无符号整数
```

**运算符：**

- 🔍 比较运算符： `<=`, `<`, `==`, `!=`, `>=`, `>`
- ➕ 算术运算符： `+`, `-`, `*`, `/`, `%`（取余）, `**`（幂）

```solidity
uint256 public _number1 = _number + 1;  // 加
uint256 public _number2 = 2**2;         // 幂
uint256 public _number3 = 7 % 2;        // 取余
bool public _numberbool = _number2 > _number3; // 比较
```

------

## 🔹 3. 地址类型（address）

以太坊地址类型分为：

- 📌 **`address`**：普通地址，20 字节。
- 💰 **`address payable`**：可接收转账，支持 `transfer` 和 `send`。

```solidity
address public _address = 0x7A58c0Be72BE218B41C608b7Fe7C5bB630736C71;
address payable public _address1 = payable(_address);

// 地址余额
uint256 public balance = _address1.balance;
```

------

## 🔹 4. 定长字节数组（bytes1 ~ bytes32）

字节数组分为两种：

- 📦 **定长字节数组**（值类型）：长度固定，如 `bytes1`, `bytes32`。
- 📂 **不定长字节数组**（引用类型）：如 `bytes`，长度可变。

```solidity
bytes32 public _byte32 = "MiniSolidity"; 
bytes1 public _byte = _byte32[0];
```

说明：

- `"MiniSolidity"` 转换为 16 进制：
   `0x4d696e69536f6c69646974790000000000000000000000000000000000000000`
- `_byte = 0x4d`（即第一个字节）。

------

## 🔹 5. 枚举（enum）

枚举用于给 `uint` 定义名称，便于阅读。

```solidity
// 定义枚举
enum ActionSet { Buy, Hold, Sell }

// 创建枚举变量
ActionSet action = ActionSet.Buy;
```

🔄 枚举和 `uint` 可显式转换：

```solidity
function enumToUint() external view returns(uint){
    return uint(action);
}
```

⚠️ **注意**：若转换数值超出范围会报错。
 👉 在实际项目中 `enum` 使用较少。

---

## Solidity 函数（Function）

Solidity 的函数非常灵活，可以进行各种复杂操作。本节将介绍函数的基本语法、`pure/view/payable` 的区别，以及可见性修饰符。

------

### 📌 函数语法格式

```solidity
function <function name>([parameter types[, ...]]) 
    {internal|external|public|private} 
    [pure|view|payable] 
    [virtual|override] 
    [<modifiers>] 
    [returns (<return types>)] 
{ 
    <function body> 
}
```

------

### 🔎 关键组成部分

- **`function`**：声明函数的固定关键字。
- **`<function name>`**：函数名。
- **`([parameters])`**：输入参数。
- **可见性（Visibility）**
  - 🌍 `public`：内部、外部均可见。
  - 🔒 `private`：仅合约内部可见（继承合约也不可用）。
  - 📤 `external`：只能合约外部调用（内部需 `this.f()`）。
  - 🏠 `internal`：仅合约内部及继承合约可用。

⚠️ 注意：

1. 函数必须显式指定可见性。
2. 状态变量默认 `internal`，`public` 状态变量会自动生成同名的 `getter`。

- **`[pure | view | payable]`**
  - 🧊 `pure`：不能读取、不能修改状态变量。
  - 👀 `view`：能读取、不能修改状态变量。
  - 💰 `payable`：函数可接收 ETH。
- **`[virtual | override]`**
  - `virtual`：父合约函数可被重写。
  - `override`：子合约函数覆盖父合约方法。
- **`[returns()]`**：函数返回值。

------

### 🎮 Pure vs View

💡 理解关键：区分 **是否读取/修改链上状态**。

- 🧊 **pure**：不读取、不修改链上状态变量。
- 👀 **view**：只读，不修改链上状态变量。
- 🔥 **普通函数**：可读可写。

类比说明：

- 🐢 `pure`：小怪，见不到/碰不到碧琪公主。
- 👀 `view`：马里奥，看得到公主，但不能“操作”。
- 👹 普通函数：Boss，可以对公主为所欲为。

------

### 💻 示例代码

#### 1️⃣ pure / view

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.21;

contract FunctionTypes {
    uint256 public number = 5;

    // 默认函数：改写状态变量
    function add() external {
        number = number + 1;
    }

    // pure：不能访问状态变量
    function addPure(uint256 _number) external pure returns(uint256 new_number) {
        new_number = _number + 1;
    }

    // view：只读，不可修改
    function addView() external view returns(uint256 new_number) {
        new_number = number + 1;
    }
}
```

------

#### 2️⃣ internal vs external

```solidity
contract FunctionTypes {
    uint256 public number = 5;

    // internal: 内部函数
    function minus() internal {
        number = number - 1;
    }

    // external: 外部可调用
    function minusCall() external {
        minus();
    }
}
```

------

#### 3️⃣ payable 函数

```solidity
contract FunctionTypes {
    uint256 public number = 5;

    function minus() internal {
        number = number - 1;
    }

    // payable: 可接收 ETH
    function minusPayable() external payable returns(uint256 balance) {
        minus();
        balance = address(this).balance;
    }
}
```

调用 `minusPayable()` 时可以转入 **1 ETH**，执行后合约余额更新。

------

### 📖 总结

- **可见性**：`public` / `private` / `internal` / `external`。
- **状态修饰**：`pure` / `view` / `payable`。
- `pure` → 不能读写状态。
- `view` → 只能读，不能写。
- 普通函数 → 可读可写。
- `payable` → 可接收 ETH。

---

## 🔄 Solidity 函数输出（Function Output）

⏱ 时间: 20 分钟
 🏆 最佳成绩: 100

这一讲介绍 Solidity 中函数的 **返回值用法**：

- 返回多个变量
- 命名式返回
- 解构式赋值（读取全部或部分返回值）

------

### 📌 return vs returns

- **`returns`**：写在函数定义后，用来 **声明返回值的类型和名称**。
- **`return`**：写在函数体中，用来 **返回实际的变量**。

------

### 1️⃣ 返回多个变量

```solidity
// 返回多个变量
function returnMultiple() public pure returns(uint256, bool, uint256[3] memory){
    return (1, true, [uint256(1),2,5]);
}
```

⚡ 注意：

- `uint256[3] memory` 表示返回一个长度为 3 的数组。
- 因为 `[1,2,3]` 默认会被推断为 `uint8`，所以需要写成 `[uint256(1),2,5]`。
- 数组返回值必须带 `memory` 修饰。

------

### 2️⃣ 命名式返回

可以在 `returns` 中声明 **返回值的变量名**，并直接在函数中赋值即可，无需写 `return`。

```solidity
// 命名式返回
function returnNamed() public pure 
    returns(uint256 _number, bool _bool, uint256[3] memory _array)
{
    _number = 2;
    _bool = false;
    _array = [uint256(3),2,1];
}
```

也可以结合 `return` 使用：

```solidity
// 命名式返回 + return
function returnNamed2() public pure 
    returns(uint256 _number, bool _bool, uint256[3] memory _array)
{
    return (1, true, [uint256(1),2,5]);
}
```

------

### 3️⃣ 解构式赋值

Solidity 允许用 **解构赋值** 来接收函数返回值。

#### ✅ 读取所有返回值

```solidity
(uint256 _number, bool _bool, uint256[3] memory _array) = returnNamed();
```

#### ✅ 只读取部分返回值（跳过用 `, ,` 占位）

```solidity
(, bool _boolOnly, ) = returnNamed();
```

这样我们只拿到 `_boolOnly`，忽略其他返回值。

---

## 🗄️ Solidity 变量、数据存储和作用域

------

### 📦 引用类型 (Reference Type)

包含：

- **数组 (array)**
- **结构体 (struct)**

这类变量比较复杂，占用空间大，因此必须声明 **存储位置**。

------

### 📌 数据存储位置

Solidity 中有三种数据位置：

| 关键字     | 存储位置                 | 特点                | Gas 消耗 |
| ---------- | ------------------------ | ------------------- | -------- |
| `storage`  | 链上存储                 | 合约状态变量默认    | 💰 最贵   |
| `memory`   | 内存临时存储             | 函数参数 / 临时变量 | 💸 较便宜 |
| `calldata` | 内存临时存储（不可修改） | 主要用于函数参数    | 💸 最便宜 |

#### ✨ 用法例子

```solidity
function fCalldata(uint[] calldata _x) public pure returns(uint[] calldata){
    // 不能修改 _x
    // _x[0] = 0; // ❌ 报错
    return _x;
}
```

------

### 🔄 数据位置与赋值规则

赋值有时会 **引用**，有时会 **复制副本**。

- **storage → storage**：引用（修改新变量会影响原变量）。
- **memory → memory**：引用。
- **其他情况**：副本（修改不会影响对方）。

#### ✅ storage → storage 示例

```solidity
uint[] x = [1,2,3]; // 状态变量

function fStorage() public {
    uint[] storage xStorage = x; // 引用
    xStorage[0] = 100;           // 修改会影响 x
}
```

------

### 🌍 变量的作用域

分为三类：

1️⃣ **状态变量 (state variable)**

- 存储在链上（`storage`）
- 合约内、函数外声明
- Gas 消耗高

```solidity
contract Variables {
    uint public x = 1;
    uint public y;
    string public z;

    function foo() external {
        x = 5;
        y = 2;
        z = "0xAA";
    }
}
```

------

2️⃣ **局部变量 (local variable)**

- 仅在函数执行时存在
- 存储在内存（`memory`）
- 不上链，Gas 低

```solidity
function bar() external pure returns(uint) {
    uint xx = 1;
    uint yy = 3;
    return xx + yy;
}
```

------

3️⃣ **全局变量 (global variable)**

- Solidity 预留关键字
- 直接使用，不需声明

```solidity
function global() external view returns(address, uint, bytes memory) {
    return (msg.sender, block.number, msg.data);
}
```

#### 🔑 常用全局变量

- `msg.sender` → 调用者地址
- `msg.value` → 交易金额（wei）
- `block.number` → 当前区块号
- `block.timestamp` → 区块时间戳
- `gasleft()` → 剩余 Gas
- （Cancun 新增）`block.blobbasefee`, `blobhash(uint index)`

------

### 💰 以太单位

Solidity 没有小数，依靠 **以太单位** 避免精度问题。

| 单位    | 换算       |
| ------- | ---------- |
| `wei`   | `1`        |
| `gwei`  | `1e9 wei`  |
| `ether` | `1e18 wei` |

示例：

```solidity
function etherUnit() external pure returns(uint) {
    assert(1 ether == 1e18);
    return 1 ether;
}
```

---

## 🔗 Solidity 引用类型

------

### 📊 数组 (Array)

数组是 Solidity 中常用的变量类型，用来存储一组数据（整数、字节、地址等）。

#### 📌 分类

1. **固定长度数组**

```solidity
uint[8] array1;
bytes1[5] array2;
address[100] array3;
```

1. **可变长度数组（动态数组）**

```solidity
uint[] array4;
bytes1[] array5;
address[] array6;
bytes array7;   // 特殊，不需要 []
```

⚠️ 注意：

- `bytes` 比 `bytes1[]` 更省 gas
- 不能用 `byte[]`，请用 `bytes` 或 `bytes1[]`

------

#### 🏗️ 创建数组的规则

- **memory 修饰的动态数组**：用 `new` 创建，必须指定长度，长度不可变。

```solidity
uint ;
bytes memory array9 = new bytes(9);
```

- **数组字面常数 (Array Literals)**：用 `[]` 初始化，类型取决于第一个元素。

```solidity
g([uint(1), 2, 3]);  // ✅ 全部是 uint
g([1,2,3]);          // ❌ 报错，默认是 uint8
```

完整示例：

```solidity
pragma solidity >=0.4.16 <0.9.0;
contract C {
    function f() public pure {
        g([uint(1), 2, 3]);
    }
    function g(uint[3] memory _data) public pure {
        // ...
    }
}
```

- **动态数组赋值**：需要逐个赋值。

```solidity
uint ;
x[0] = 1;
x[1] = 3;
x[2] = 4;
```

------

#### ⚙️ 数组成员函数

- `.length` → 数组长度
- `.push()` → 添加一个 `0` 元素，返回引用
- `.push(x)` → 添加一个元素 `x`
- `.pop()` → 移除最后一个元素

------

### 🏷️ 结构体 (Struct)

Solidity 支持 **自定义类型**，可包含原始或引用类型，并能作为数组或映射的元素。

#### 📌 定义

```solidity
struct Student {
    uint256 id;
    uint256 score; 
}

Student student; // 状态变量
```

------

#### 🖊️ 给结构体赋值的 4 种方法

1. **函数内引用 storage**

```solidity
function initStudent1() external {
    Student storage _student = student;
    _student.id = 11;
    _student.score = 100;
}
```

1. **直接修改状态变量**

```solidity
function initStudent2() external {
    student.id = 1;
    student.score = 80;
}
```

1. **构造函数式**

```solidity
function initStudent3() external {
    student = Student(3, 90);
}
```

1. **key-value 形式**

```solidity
function initStudent4() external {
    student = Student({id: 4, score: 60});
}
```

------

## 📝 总结

- **数组 (Array)**
  - 固定长度 vs 动态数组
  - `memory` 数组必须 `new` 并指定长度
  - 字面常量数组类型由第一个元素决定
  - 动态数组常用操作：`.length`, `.push()`, `.pop()`
- **结构体 (Struct)**
  - 支持组合不同类型
  - 可作为数组/映射元素
  - 赋值方式：storage 引用、直接修改、构造函数式、key-value

👉 下一讲将介绍 **映射 (Mapping)** —— Solidity 的哈希表。

---

## 🗂️ 映射（Mapping）

**⏰ 时间：20 分钟 ｜ 🏆 最佳成绩：100**

这一讲，我们将介绍 **映射（Mapping）** 类型。
 在 Solidity 中，映射是一种 **存储键值对（Key-Value Pair）** 的数据结构，可以理解为 **哈希表**。
 👉 常见用途：通过一个人的 `id` 来查询他的钱包地址。

------

### 🔑 映射的定义

```solidity
mapping(_KeyType => _ValueType)
```

- `_KeyType`：键（Key）的变量类型
- `_ValueType`：值（Value）的变量类型

📌 示例：

```solidity
mapping(uint => address) public idToAddress; // id -> 地址
mapping(address => address) public swapPair; // 币对映射：地址 -> 地址
```

------

### 📜 映射的规则

#### ✅ 规则 1：Key 类型限制

- `_KeyType` **只能使用 Solidity 内置的值类型**（如 `uint`、`address` 等）
- ❌ 不能用结构体或数组等复杂类型
- `_ValueType` 则 **可以使用自定义类型**

```solidity
// ❌ 错误示例：Key 使用了自定义结构体
struct Student {
    uint256 id;
    uint256 score; 
}
mapping(Student => uint) public testVar;
```

------

#### ✅ 规则 2：存储位置限制

- 映射必须存储在 **storage** 中
- 可用于：
  - 合约的状态变量
  - 函数中的 storage 变量
  - library 函数的参数
- ❌ 不可用于：
  - `public` 函数的参数
  - `public` 函数的返回值

原因：映射记录的是 **关系**，而不是完整数据集合。

------

#### ✅ 规则 3：自动生成 Getter

- 如果映射声明为 `public`，Solidity 会自动生成一个 **getter 函数**，
   可以通过 `Key` 来查询 `Value`。

------

#### ✅ 规则 4：新增键值对

语法：

```solidity
_Var[_Key] = _Value;
```

示例：

```solidity
function writeMap(uint _Key, address _Value) public {
    idToAddress[_Key] = _Value;
}
```

------

### ⚙️ 映射的原理

1. **不存储 Key 信息**
   - 映射本身不存储键集合，也没有 `length`。
2. **存取位置计算**
   - `keccak256(h(key) . slot)` 确定存储位置。
   - 👉 详情可参考 **WTF Solidity 映射存储布局**。
3. **默认值机制**
   - 未赋值的键（Key）对应的值（Value），为类型默认值。
   - 例如：`uint` 默认值为 `0`。

------

### 📝 总结

- 映射（Mapping）是 Solidity 中的 **哈希表结构**
- 主要特点：
  - Key 只能是内置类型
  - 不存储 Key 集合，无长度信息
  - 默认值机制（未赋值即默认值）

---

## 📘 Solidity 变量初始值

⏱ **时间**: 20 分钟
 🏆 **最佳成绩**: 100

在 Solidity 中，**声明但没有赋值的变量**，都会有它的**默认初始值**。下面整理了常见的 **值类型**、**引用类型** 以及 **delete 操作符**的初始值情况。

------

### 🔹 值类型初始值

- 🔘 **boolean**: `false`
- 📝 **string**: `""`（空字符串）
- 🔢 **int**: `0`
- 🔢 **uint**: `0`
- 🎯 **enum**: 枚举中的第一个元素（索引 `0`）
- 🏠 **address**: `0x0000000000000000000000000000000000000000` (即 `address(0)`)
- ⚙ **function**:
  - `internal`: 空白函数
  - `external`: 空白函数

📌 **代码验证：**

```solidity
bool public _bool;         // false
string public _string;     // ""
int public _int;           // 0
uint public _uint;         // 0
address public _address;   // 0x0000000000000000000000000000000000000000

enum ActionSet { Buy, Hold, Sell }
ActionSet public _enum;    // Buy (索引 0)

function fi() internal {}  // internal 空白函数
function fe() external {}  // external 空白函数
```

------

### 🔹 引用类型初始值

- 📍 **mapping**: 所有元素都为其默认值的 mapping
- 🏗 **struct**: 所有成员设为其默认值的结构体
- 📦 **array**
  - 动态数组: `[]`
  - 静态数组: 所有成员设为默认值

📌 **代码验证：**

```solidity
// Array
uint[8] public _staticArray; // [0,0,0,0,0,0,0,0]
uint[] public _dynamicArray; // []

// Mapping
mapping(uint => address) public _mapping; // 默认值 address(0)

// Struct
struct Student {
    uint256 id;
    uint256 score;
}
Student public student; // {id: 0, score: 0}
```

------

### 🔹 delete 操作符

`delete a` 会将变量 `a` 重置为其 **初始值**。

📌 **代码示例：**

```solidity
bool public _bool2 = true;

function d() external {
    delete _bool2; // 结果：false
}
```

------

✅ **总结**：

- 所有变量都有默认值，不会是随机数。
- `delete` 操作符能让变量回到默认值。
- 常用场景：重置状态、释放存储。

---

## ⏳ Solidity 常量与不变量（constant & immutable）

这一讲介绍 Solidity 中和**常量**相关的两个关键字：

- 🔒 `constant`（常量）
- 🛡️ `immutable`（不变量）

状态变量声明这两个关键字后，**初始化后就不能再更改数值**。

✨ **好处**：

- 提升合约的安全性
- 节省 Gas

------

### ⚡ 使用限制

- ✅ 仅数值变量可以声明 `constant` 和 `immutable`
- ✅ `string` 和 `bytes` 可以声明为 `constant`
- ❌ `string` 和 `bytes` **不能**声明为 `immutable`

------

### 🔒 constant

- **必须在声明时初始化**
- **之后不能修改**（修改会编译错误）

```solidity
// constant 变量示例
uint256 constant CONSTANT_NUM = 10;
string  constant CONSTANT_STRING = "0xAA";
bytes   constant CONSTANT_BYTES = "WTF";
address constant CONSTANT_ADDRESS = 0x0000000000000000000000000000000000000000;
```

------

### 🛡️ immutable

- **可以在声明时或构造函数中初始化**
- **更灵活**
- `v0.8.21` 之后：未显式初始化的 `immutable` 使用数值类型初始值
- 如果 **声明时**和**constructor**同时赋值 → 取 **constructor** 的值

```solidity
// immutable 变量示例
uint256 public immutable IMMUTABLE_NUM = 9999999999;

// v0.8.21 以后，未初始化时使用默认值
address public immutable IMMUTABLE_ADDRESS; 
uint256 public immutable IMMUTABLE_BLOCK;
uint256 public immutable IMMUTABLE_TEST;
```

------

### ⚙️ constructor 初始化

`immutable` 可以利用全局变量或函数赋值，比如 `address(this)`、`block.number`、自定义函数等。

```solidity
constructor() {
    IMMUTABLE_ADDRESS = address(this);
    IMMUTABLE_NUM = 1118;
    IMMUTABLE_TEST = test(); // 使用函数初始化
}

function test() public pure returns(uint256) {
    return 9;
}
```

------

✅ 总结：

- `constant` → 简单固定值，必须声明时初始化
- `immutable` → 更灵活，可在 constructor 中根据逻辑赋值

---

## 🔑 Solidity 构造函数 & 修饰器（constructor & modifier）

------

### 🏗️ 构造函数（constructor）

构造函数是一种**特殊函数**：

- 每个合约最多定义一个
- **在部署时自动执行一次**
- 常用于初始化参数（如 `owner` 地址）

```solidity
address owner; // 定义 owner 变量

// 构造函数
constructor(address initialOwner) {
    owner = initialOwner; // 部署时指定 owner
}
```

⚠️ **注意版本差异**

- 在 **Solidity 0.4.22 之前**，构造函数使用 **与合约同名的函数**
- 容易因拼写错误 → 构造函数变成普通函数 → 引发安全漏洞
- 因此从 **0.4.22 之后**统一采用 `constructor` 关键字

旧写法示例（❌ 不推荐）：

```solidity
pragma solidity =0.4.21;

contract Parents {
    function Parents() public {  // 与合约名相同 → 构造函数
    }
}
```

------

### 🛡️ 修饰器（modifier）

修饰器是 Solidity **特有语法**，类似装饰器（decorator）：

- 在执行函数前**检查条件**
- 减少重复代码
- 常用于**权限控制、余额检查、状态验证**

💡 可以理解为：给函数套上一层“盔甲”。

#### 示例：`onlyOwner`

```solidity
// 定义 modifier
modifier onlyOwner {
   require(msg.sender == owner, "Not owner"); 
   _; // 条件满足时继续执行函数主体
}

// 使用 modifier
function changeOwner(address _newOwner) external onlyOwner {
   owner = _newOwner;
}
```

👉 效果：

- 只有 `owner` 地址可以调用 `changeOwner`
- 其他用户调用会报错并 **revert**

------

### 🏆 实战：OpenZeppelin Ownable

🛠️ OpenZeppelin 提供了成熟的 `Ownable` 标准实现：
 🔗 [Ownable.sol 源码](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol)

主要特性：

- 自动设置部署者为 `owner`
- 提供 `onlyOwner` 修饰器
- 提供 `transferOwnership` / `renounceOwnership` 方法

------

### ✅ 总结

- **constructor** → 初始化参数，部署时运行一次
- **modifier** → 执行前检查条件，常用于权限控制
- **Ownable 模式** → 智能合约最常见的权限管理方式

---

## 📢 Solidity 事件（Event）

------

### ⚡ 事件（Event）概念

Solidity 的 **事件（event）** 是 EVM 上日志（Log）的抽象：

- **响应性**：前端应用（如 ethers.js）可以订阅事件，通过 RPC 接口监听。
- **经济性**：事件存储 gas 消耗低（≈2,000 gas），相比存储新变量（≥20,000 gas）更省。

------

### 📝 声明事件

```solidity
event Transfer(
    address indexed from,
    address indexed to,
    uint256 value
);
```

- `Transfer`：事件名称
- `indexed`：索引变量（最多3个），存储在 **topics** 中，方便检索
- 非 `indexed` 参数存储在 **data** 中

💡 Tips：非值类型（如 `string`, `bytes`, `array`）会存储哈希到 topics，需解析哈希才能获得原始数据

------

### 🚀 释放事件（emit）

```solidity
function _transfer(
    address from,
    address to,
    uint256 amount
) external {
    _balances[from] = 10000000; // 初始代币
    _balances[from] -= amount;
    _balances[to] += amount;

    emit Transfer(from, to, amount); // 触发事件
}
```

- `_balances`：代币余额
- `emit`：关键字，用于触发事件
- 每次转账都会生成一个事件日志

------

### 🗃️ EVM 日志（Log）

EVM 中事件日志包含两部分：

1. **Topics（主题）**

   - 用于检索事件

   - 第一个元素：事件签名哈希

     ```
     keccak256("Transfer(address,address,uint256)")
     // 0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef
     ```

   - 其它元素：最多 3 个 `indexed` 参数

   - 非值类型存储为哈希

2. **Data（数据）**

   - 存储非 `indexed` 参数
   - 可存任意大小的数据（数组、字符串等）
   - gas 消耗比 topics 低

------

### ✅ 总结

- **事件的作用**：链上记录、前端响应、经济高效
- **Topics vs Data**：
  - Topics → 可检索索引
  - Data → 存储任意值
- 事件是很多链上分析工具（Nansen, Dune Analytics）的基础

---

## 🏛️ Solidity 继承（Inheritance）

📖 **学习时间**：20 分钟

Solidity 支持 **面向对象编程** 的继承机制，可以减少重复代码，并支持多重继承、修饰器继承、构造函数继承以及菱形继承。

------

### 1️⃣ 基本继承规则

- **virtual**：父合约函数允许被重写时需加 `virtual`
- **override**：子合约重写父函数时需加 `override`
- **变量重写**：使用 `override` 修饰 `public` 变量，会覆盖同名的 getter 函数

------

### 2️⃣ 简单继承示例

```solidity
contract Yeye {
    event Log(string msg);
    function hip() public virtual { emit Log("Yeye"); }
    function pop() public virtual { emit Log("Yeye"); }
    function yeye() public virtual { emit Log("Yeye"); }
}

contract Baba is Yeye {
    function hip() public virtual override { emit Log("Baba"); }
    function pop() public virtual override { emit Log("Baba"); }
    function baba() public virtual { emit Log("Baba"); }
}
```

- `Baba` 继承了 `Yeye`
- 重写了 `hip()` 和 `pop()`
- 继承来的 `yeye()` 输出仍为 `"Yeye"`

------

### 3️⃣ 多重继承

- **顺序**：继承顺序按“祖先到父代”排列

  ```solidity
  contract Erzi is Yeye, Baba { ... }
  ```

- **函数冲突**：必须重写重复的函数，`override` 后面需列出所有父合约名

- **示例**：

```solidity
function hip() public virtual override(Yeye, Baba) { emit Log("Erzi"); }
function pop() public virtual override(Yeye, Baba) { emit Log("Erzi"); }
```

------

### 4️⃣ 修饰器（Modifier）继承

- 修饰器可以继承并重写：

```solidity
contract Base1 {
    modifier exactDividedBy2And3(uint _a) virtual {
        require(_a % 2 == 0 && _a % 3 == 0);
        _;
    }
}

contract Identifier is Base1 {
    function getExactDividedBy2And3(uint _dividend) 
        public exactDividedBy2And3(_dividend) pure returns(uint, uint) {
        return (_dividend/2, _dividend/3);
    }
}
```

- 重写修饰器：

```solidity
modifier exactDividedBy2And3(uint _a) override {
    _;
    require(_a % 2 == 0 && _a % 3 == 0);
}
```

------

### 5️⃣ 构造函数继承

- **父构造函数传参方法**：

  ```solidity
  abstract contract A { uint public a; constructor(uint _a) { a = _a; } }
  contract B is A(1) { }
  contract C is A { constructor(uint _c) A(_c*_c) {} }
  ```

- **子合约构造函数调用父构造函数**：

  - 可在继承声明中传参
  - 可在子构造函数中调用父构造函数

------

### 6️⃣ 调用父合约函数

1. **直接调用**：

```solidity
Yeye.pop();
```

1. **super 关键字**：

- 调用最近父合约函数
- 顺序按声明从右到左：

```solidity
contract Erzi is Yeye, Baba {
    function callParentSuper() public { super.pop(); } // 调用 Baba.pop()
}
```

------

### 7️⃣ 菱形继承（钻石继承）

继承树示意：

```
      God
     /  \
  Adam   Eve
     \  /
    people
```

- 调用 `super` 时，每个合约的函数按顺序调用一次
- 父合约只执行一次（Solidity DAG 机制）

```solidity
contract people is Adam, Eve {
    function foo() public override(Adam, Eve) { super.foo(); }
    function bar() public override(Adam, Eve) { super.bar(); }
}
```

调用 `people.bar()` → 调用顺序：`Eve.bar()` → `Adam.bar()` → `God.bar()`

------

### ✅ 总结

- Solidity 支持 **简单继承、多重继承、修饰器继承、构造函数继承、super调用和菱形继承**
- `virtual` 和 `override` 是关键
- `super` 遵循声明顺序，从右到左调用最近父合约
- 菱形继承保证每个父合约只调用一次

---

## 🖋️ Solidity 抽象合约 & 接口

⏱️ **学习时间**：20 分钟

本讲通过 **ERC721接口** 为例，讲解 Solidity 中的 **抽象合约（abstract）** 和 **接口（interface）**。

------

### 1️⃣ 抽象合约（Abstract Contract）

💡 **概念**

- 含至少一个未实现函数（函数缺少 `{}`）
- 必须标记 `abstract`
- 未实现的函数需加 `virtual`，方便子合约重写

🔹 **示例**

```
abstract contract InsertionSort {
    function insertionSort(uint[] memory a) public pure virtual returns(uint[] memory);
}
```

✅ **作用**

- 定义函数接口，具体实现可由子合约完成
- 类似“半成品合约”，可让别人实现功能

------

### 2️⃣ 接口（Interface）

💡 **特点**

- 完全不实现任何函数
- 规则：
  1. ❌ 不能包含状态变量
  2. ❌ 不能有构造函数
  3. ❌ 不能继承非接口合约
  4. ✅ 所有函数必须是 `external` 且无函数体

🔹 **作用**

- 定义合约骨架，其他合约或 DApp 可与其交互
- 提供：
  - 函数 `bytes4` 选择器
  - 接口 ID（EIP165）
- 与 **ABI** 等价，可互转

------

### 3️⃣ ERC721 接口示例

```
interface IERC721 is IERC165 {
    // 事件
    event Transfer(address indexed from, address indexed to, uint256 indexed tokenId);
    event Approval(address indexed owner, address indexed approved, uint256 indexed tokenId);
    event ApprovalForAll(address indexed owner, address indexed operator, bool approved);
    
    // 函数
    function balanceOf(address owner) external view returns (uint256 balance);
    function ownerOf(uint256 tokenId) external view returns (address owner);
    function safeTransferFrom(address from, address to, uint256 tokenId) external;
    function transferFrom(address from, address to, uint256 tokenId) external;
    function approve(address to, uint256 tokenId) external;
    function getApproved(uint256 tokenId) external view returns (address operator);
    function setApprovalForAll(address operator, bool _approved) external;
    function isApprovedForAll(address owner, address operator) external view returns (bool);
    function safeTransferFrom(address from, address to, uint256 tokenId, bytes calldata data) external;
}
```

#### 📜 事件说明

| 事件               | 功能                                              |
| ------------------ | ------------------------------------------------- |
| 🔹 `Transfer`       | 转账时触发，记录 `from`、`to`、`tokenId`          |
| 🔹 `Approval`       | 授权单个 NFT，记录 `owner`、`approved`、`tokenId` |
| 🔹 `ApprovalForAll` | 批量授权，记录 `owner`、`operator`、`approved`    |

#### ⚙️ 函数说明

| 函数                | 功能                                          |
| ------------------- | --------------------------------------------- |
| `balanceOf`         | 查询地址 NFT 持有量                           |
| `ownerOf`           | 查询 tokenId 的拥有者                         |
| `transferFrom`      | 普通转账                                      |
| `safeTransferFrom`  | 安全转账（接收方为合约需实现 ERC721Receiver） |
| `approve`           | 授权单个 NFT                                  |
| `getApproved`       | 查询 tokenId 被授权的地址                     |
| `setApprovalForAll` | 批量授权                                      |
| `isApprovedForAll`  | 查询批量授权状态                              |

------

### 4️⃣ 使用接口与 ERC721 合约交互

```
contract interactBAYC {
    IERC721 BAYC = IERC721(0xBC4CA0EdA7647A8aB7C2061c2E118A18a936f13D); // BAYC 合约地址

    // 查询持仓量
    function balanceOfBAYC(address owner) external view returns (uint256 balance) {
        return BAYC.balanceOf(owner);
    }

    // 安全转账
    function safeTransferFromBAYC(address from, address to, uint256 tokenId) external {
        BAYC.safeTransferFrom(from, to, tokenId);
    }
}
```

💡 **说明**

- 不需要知道 BAYC 源码
- 通过接口即可调用 `balanceOf()`、`safeTransferFrom()` 等函数

------

### 5️⃣ 总结

- 🔹 **抽象合约**：至少有一个未实现函数，需标记 `abstract`
- 🔹 **接口**：完全不实现函数，所有函数必须 `external`
- 🔹 接口是智能合约的骨架，用于标准化交互
- 🔹 ERC721、ERC20 等标准都是通过接口定义
- 🔹 通过接口即可与链上合约交互，无需了解内部实现

---

## ⚠️ Solidity 异常处理

⏱️ **学习时间**：20 分钟

本讲介绍 Solidity 中三种 **抛出异常的方法**：`error`、`require`、`assert`，并比较它们的 **gas 消耗**。

------

### 1️⃣ Error（Solidity ≥0.8.4）

💡 **特点**

- 高效、节省 gas
- 可携带参数
- 自定义错误，可在合约外部定义
- 必须搭配 `revert` 使用

🔹 **示例**

```
error TransferNotOwner(); // 自定义 error
error TransferNotOwner(address sender); // 带参数的 error

function transferOwner1(uint256 tokenId, address newOwner) public {
    if(_owners[tokenId] != msg.sender){
        revert TransferNotOwner(); 
        // revert TransferNotOwner(msg.sender);
    }
    _owners[tokenId] = newOwner;
}
```

✅ **用法**：当条件不满足时，抛出自定义错误并回退交易。

------

### 2️⃣ Require

💡 **特点**

- Solidity 0.8.0 之前常用
- 可以给出错误描述字符串
- 字符串越长，gas 消耗越高

🔹 **示例**

```
function transferOwner2(uint256 tokenId, address newOwner) public {
    require(_owners[tokenId] == msg.sender, "Transfer Not Owner");
    _owners[tokenId] = newOwner;
}
```

✅ **用法**：当条件不满足时，抛出异常并打印提示信息。

------

### 3️⃣ Assert

💡 **特点**

- 通常用于程序员调试
- 不能给出错误描述
- Solidity 0.8.0 之前抛出 panic exception，会消耗剩余所有 gas

🔹 **示例**

```
function transferOwner3(uint256 tokenId, address newOwner) public {
    assert(_owners[tokenId] == msg.sender);
    _owners[tokenId] = newOwner;
}
```

✅ **用法**：当条件不满足时，抛出异常，主要用于调试检查内部逻辑。

------

### 4️⃣ 三种方法对比

| 方法        | 说明                     | 是否能自定义信息 | Gas 消耗（示例）     |
| ----------- | ------------------------ | ---------------- | -------------------- |
| 🟢 `error`   | 自定义错误，可带参数     | ✅                | 24457 (带参数 24660) |
| 🔵 `require` | 条件检查，可打印字符串   | ✅                | 24755                |
| 🔴 `assert`  | 内部调试检查，不打印信息 | ❌                | 24473                |

💡 **结论**

- `error` 最省 gas，同时可告知用户失败原因 → 推荐使用
- `assert` 用于调试，gas 较少但无提示信息
- `require` 易用但消耗 gas 较多

📌 **备注**

- Solidity 0.8.0 之前，`assert` 抛出的异常会消耗全部剩余 gas
- `error` 是新特性，更高效，推荐使用

## 🔁 Solidity 函数重载（Function Overloading）

⏱️ **学习时间**：20 分钟

Solidity 允许函数 **重载**（overloading）：

- 名字相同，但 **参数类型或数量不同** 的函数可以共存
- 编译器会根据参数生成不同的 **函数选择器（selector）**
- ⚠️ 注意：**修饰器（modifier）不能重载**

------

### 1️⃣ 基本示例

```
function saySomething() public pure returns(string memory){
    return "Nothing";
}

function saySomething(string memory something) public pure returns(string memory){
    return something;
}
```

✅ **效果**：

- `saySomething()` → 返回 `"Nothing"`
- `saySomething("Hello")` → 返回 `"Hello"`

🔹 Remix 部署示例：

| 函数调用             | 返回结果  |
| -------------------- | --------- |
| `saySomething()`     | "Nothing" |
| `saySomething("Hi")` | "Hi"      |

💡 **原理**：
 编译后不同参数类型会生成不同的 **函数选择器**，在 EVM 中被视为不同函数。

------

### 2️⃣ 实参匹配（Argument Matching）

当调用重载函数时，Solidity 会匹配 **输入参数类型**：

```
function f(uint8 _in) public pure returns (uint8 out) {
    out = _in;
}

function f(uint256 _in) public pure returns (uint256 out) {
    out = _in;
}
```

⚠️ **注意**：

```
f(50) // 错误！50 可以匹配 uint8 和 uint256 → 编译报错
```

✅ **规则**：

- 实参类型必须与某个重载函数严格匹配
- 如果存在多个可能匹配 → 编译器报错

------

### 3️⃣ 小结

- 🔹 **函数重载**：名字相同但参数类型或数量不同 → 可以共存
- 🔹 **编译器生成不同选择器** → 在 EVM 中视为不同函数
- 🔹 ⚠️ **修饰器不能重载**
- 🔹 **实参匹配严格** → 避免歧义

---

## 📚 Solidity 库合约（Library）

⏱️ **学习时间**：20 分钟

库合约是一种特殊的合约，**用于提升代码复用性和减少 gas 消耗**。
 站在巨人的肩膀上，我们直接调用大神或项目方写好的库即可。

------

### 🔹 库合约特点

- ❌ **不能存在状态变量**
- ❌ **不能继承或被继承**
- ❌ **不能接收以太币**
- ❌ **不可以被销毁**
- ⚠️ **函数可见性影响调用方式**
  - `public` / `external` → 调用时触发 `delegatecall`
  - `internal` → 不会触发
  - `private` → 仅库内部可见

------

### 🔧 示例：Strings 库合约

`Strings` 库主要用于 **uint256 ↔ string 转换**

```solidity
library Strings {
    bytes16 private constant _HEX_SYMBOLS = "0123456789abcdef";

    // uint256 → 十进制 string
    function toString(uint256 value) public pure returns (string memory) {
        if (value == 0) return "0";
        uint256 temp = value;
        uint256 digits;
        while (temp != 0) {
            digits++;
            temp /= 10;
        }
        bytes memory buffer = new bytes(digits);
        while (value != 0) {
            digits -= 1;
            buffer[digits] = bytes1(uint8(48 + uint256(value % 10)));
            value /= 10;
        }
        return string(buffer);
    }

    // uint256 → 十六进制 string
    function toHexString(uint256 value) public pure returns (string memory) {
        if (value == 0) return "0x00";
        uint256 temp = value;
        uint256 length = 0;
        while (temp != 0) {
            length++;
            temp >>= 8;
        }
        return toHexString(value, length);
    }

    // uint256 → 固定长度十六进制 string
    function toHexString(uint256 value, uint256 length) public pure returns (string memory) {
        bytes memory buffer = new bytes(2 * length + 2);
        buffer[0] = "0";
        buffer[1] = "x";
        for (uint256 i = 2 * length + 1; i > 1; --i) {
            buffer[i] = _HEX_SYMBOLS[value & 0xf];
            value >>= 4;
        }
        require(value == 0, "Strings: hex length insufficient");
        return string(buffer);
    }
}
```

✅ **功能**：

- `toString()` → 将 `uint256` 转为十进制 `string`
- `toHexString()` → 将 `uint256` 转为十六进制 `string`

------

### 🛠️ 如何调用库函数

#### 1️⃣ 使用 `using for` 指令

```solidity
using Strings for uint256;

function getString1(uint256 _number) public pure returns(string memory){
    return _number.toHexString();
}
```

- `uint256` 类型变量 `_number` 自动拥有库函数 `toHexString()`
- `_number` 会被作为函数的第一个参数传入

#### 2️⃣ 直接通过库名调用

```solidity
function getString2(uint256 _number) public pure returns(string memory){
    return Strings.toHexString(_number);
}
```

💡 **测试**：输入 `170` → 返回 `"0xaa"`

------

### 📌 总结

- 库合约提高了 **代码复用性** 并节省 **gas**
- ⚡ 大多数开发者 **不需要自己写库**，直接调用即可
- 常用库：
  - `Strings`：uint256 ↔ string
  - `Address`：判断地址是否为合约
  - `Create2`：安全使用 `CREATE2` opcode
  - `Arrays`：数组相关工具

---

## 📚 Solidity Import

⏱️ **学习时间**：20 分钟

在 Solidity 中，`import` 语句可以 **在一个文件中引用另一个文件的内容**，提高代码的可重用性和组织性。

------

### 🔹 Import 的用法

#### 1️⃣ 通过相对路径导入

```
// 文件结构
├── Import.sol
└── Yeye.sol

// 通过文件相对位置 import
import './Yeye.sol';
```

#### 2️⃣ 通过网址导入

```
// 直接从 GitHub 导入全局符号
import 'https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/Address.sol';
```

#### 3️⃣ 通过 npm 目录导入

```
import '@openzeppelin/contracts/access/Ownable.sol';
```

#### 4️⃣ 导入指定的合约符号

```
import {Yeye} from './Yeye.sol';
```

> ⚠️ **注意**：
>  import 必须写在 **版本声明之后，其他代码之前**。

------

#### 🛠️ 测试导入效果

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.21;

// 导入其他文件
import './Yeye.sol';
import {Yeye} from './Yeye.sol';
import 'https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/Address.sol';
import '@openzeppelin/contracts/access/Ownable.sol';

contract Import {
    // 成功导入 Address 库
    using Address for address;

    // 声明 Yeye 变量
    Yeye yeye = new Yeye();

    // 测试是否能调用 Yeye 的函数
    function test() external {
        yeye.hip();
    }
}
```

✅ **效果**：

- 成功导入了本地文件、网址库和 npm 库
- 可以调用导入的合约函数，例如 `yeye.hip()`

------

### 📌 总结

- `import` 可以引用：
  - 自己写的其他文件中的合约或函数
  - 他人写好的合约或库（如 OpenZeppelin）
- 使用 import 可 **提高代码复用性**，方便团队协作与模块化开发
- 支持 **相对路径、网址、npm 目录、指定符号** 等多种导入方式

---

## 💰 Solidity 接收 ETH

⏱️ **学习时间**：20 分钟

Solidity 支持两种特殊的回调函数：

- `receive()`
- `fallback()`

它们主要用于：

1. **接收 ETH 转账**
2. **处理合约不存在的函数调用（如代理合约 proxy contract）**

> ⚠️ **历史注意**：
>  Solidity 0.6.x 之前，只有 `fallback()` 函数，同时处理接收 ETH 和不存在函数调用的情况。
>  0.6 之后，将其拆分成 `receive()` 和 `fallback()` 两个函数。

------

### 1️⃣ 接收 ETH：`receive()` 函数

- 当合约 **直接接收 ETH** 时会触发
- **声明方式**：

```
receive() external payable {
    // 处理逻辑
}
```

- **特点**：
  - 不带参数
  - 不返回值
  - 必须 **external payable**
  - 每个合约最多只有一个 `receive()`
  - 执行逻辑尽量简单（`send` 和 `transfer` gas 限制 2300）
- **示例**：释放事件

```
event Received(address sender, uint value);

receive() external payable {
    emit Received(msg.sender, msg.value);
}
```

> ⚠️ **安全提示**：
>  恶意合约可能在 `receive()` 中消耗大量 gas 或故意失败，可能导致退款或转账逻辑失败。

------

### 2️⃣ 回退函数：`fallback()`

- 当调用合约中 **不存在的函数** 时触发
- 可用于接收 ETH，也可用于代理合约
- **声明方式**：

```
fallback() external payable {
    // 处理逻辑
}
```

- **示例**：释放事件

```
event fallbackCalled(address sender, uint value, bytes data);

fallback() external payable {
    emit fallbackCalled(msg.sender, msg.value, msg.data);
}
```

------

### 3️⃣ `receive()` 与 `fallback()` 区别

```soli
触发fallback() 还是 receive()?
           接收ETH
              |
         msg.data是空？
            /  \
          是    否
          /      \
receive()存在?   fallback()
        / \
       是  否
      /     \
receive()   fallback()
```

####  ETH 转账 + 数据 (`msg.data ≠ 0`)

当交易调用一个函数时：

- **前 4 个字节的 `msg.data`** 用作 **函数选择器（function selector）**
- 示例：

```
address(this).call{value: 1 ether}("0x1234");
```

> ⚠️ 这是“发送 ETH”，但同时 **包含数据 `msg.data ≠ empty`**

- Solidity 处理规则：
  1. **匹配到函数** → 调用对应函数
  2. **匹配不到函数** → 调用 `fallback()`（必须 `payable` 才能接收 ETH）
- 也就是说，只要 `msg.data ≠ 空`，`receive()` **永远不会被触发**，即使有 ETH 发送。

### 📌 总结

- Solidity 中有两种特殊函数：`receive()` 和 `fallback()`
- 主要用途：
  1. **接收 ETH**
  2. **代理合约/函数不存在的调用**
- 使用注意：
  - `receive()` 简单逻辑、不要消耗过多 gas
  - `fallback()` 可用于复杂逻辑或代理模式

---

## 💰 发送 ETH

**时间：20 分钟**

Solidity 提供三种方法向其他合约发送 ETH：

- `transfer()` ✅（安全但受限）
- `send()` ⚠️（旧方法，不推荐）
- `call()` 💡（灵活、推荐）

------

### 1️⃣ 接收 ETH 的合约示例

```
contract ReceiveETH {
    // 收到 ETH 事件，记录 amount 和 gas 剩余
    event Log(uint amount, uint gas);

    // receive()方法，接收 ETH 时触发
    receive() external payable {
        emit Log(msg.value, gasleft());
    }

    // 查询合约 ETH 余额
    function getBalance() view public returns(uint) {
        return address(this).balance;
    }
}
```

- 部署后运行 `getBalance()` → 余额为 0
- 收到 ETH 时会触发 `Log` 事件

------

### 2️⃣ 发送 ETH 的合约示例

```
contract SendETH {
    // 构造函数 payable，可在部署时转入 ETH
    constructor() payable {}

    // receive()方法，接收 ETH 时触发
    receive() external payable {}
}
```

------

### 3️⃣ `transfer()` 方法

- 用法：`_to.transfer(amount)`
- gas 限制：2300（足够转账，但对方 fallback/receive 不可复杂）
- 失败时自动 revert

```
function transferETH(address payable _to, uint256 amount) external payable {
    _to.transfer(amount);
}
```

✅ 测试：amount <= value → 成功
 ❌ amount > value → 自动 revert

------

### 4️⃣ `send()` 方法

- 用法：`_to.send(amount)`
- gas 限制：2300
- 失败不会自动 revert，需要额外处理返回值

```
error SendFailed();

function sendETH(address payable _to, uint256 amount) external payable {
    bool success = _to.send(amount);
    if(!success) {
        revert SendFailed();
    }
}
```

✅ 测试：amount <= value → 成功
 ❌ amount > value → revert（自定义 error）

------

### 5️⃣ `call()` 方法（推荐）

- 用法：`_to.call{value: amount}("")`
- 没有 gas 限制
- 支持对方 fallback/receive 复杂逻辑
- 失败不会自动 revert，需要额外处理返回值

```
error CallFailed();

function callETH(address payable _to, uint256 amount) external payable {
    (bool success, ) = _to.call{value: amount}("");
    if(!success) {
        revert CallFailed();
    }
}
```

✅ 测试：amount <= value → 成功
 ❌ amount > value → revert（自定义 error）

------

### 6️⃣ 总结

| 方法     | gas 限制 | 失败处理     | 是否推荐 |
| -------- | -------- | ------------ | -------- |
| transfer | 2300     | 自动 revert  | 👍 次优   |
| send     | 2300     | 需处理返回值 | ⚠️ 很少用 |
| call     | 无限制   | 需处理返回值 | 💡 推荐   |

💡 **结论**：

- `call` 最灵活，适用于大多数合约交互
- `transfer` 安全，但受限
- `send` 已过时，不推荐使用

---

## 🔗 调用其他合约

在 Solidity 中，一个合约可以调用另一个合约的函数，这对于构建复杂 DApp 非常有用。本讲介绍在已知合约代码（或接口）和地址的情况下，如何调用已部署合约。

------

### 1️⃣ 目标合约示例

```
contract OtherContract {
    uint256 private _x = 0; // ⚙️ 状态变量
    event Log(uint amount, uint gas); // 📣 收到 ETH 时触发事件

    // 💰 返回合约 ETH 余额
    function getBalance() view public returns(uint) {
        return address(this).balance;
    }

    // ✏️ 设置状态变量 _x，可发送 ETH
    function setX(uint256 x) external payable {
        _x = x;
        if(msg.value > 0){
            emit Log(msg.value, gasleft());
        }
    }

    // 🔍 读取 _x
    function getX() external view returns(uint x){
        x = _x;
    }
}
```

**特点：**

- ⚙️ `_x`：状态变量
- 📣 `Log`：收到 ETH 时触发
- 💰 `getBalance()`：返回合约 ETH 余额
- ✏️ `setX()`：payable，可修改 `_x` 并发送 ETH
- 🔍 `getX()`：读取 `_x`

------

### 2️⃣ 调用 OtherContract 的方法

Solidity 调用合约的基本方式：

```
OtherContract(_Address).f()
```

- 🏷 `_Address`：目标合约地址
- 🔧 `f()`：要调用的函数
- 可通过合约代码或接口创建引用

------

#### 方法 1️⃣ 传入合约地址

```
function callSetX(address _Address, uint256 x) external {
    OtherContract(_Address).setX(x);
}
```

- 🚀 部署后，传入 OtherContract 地址 + x 值
- 🔍 调用 `getX()` 验证 `_x` 是否变更

------

#### 方法 2️⃣ 传入合约变量

```
function callGetX(OtherContract _Address) external view returns(uint x){
    x = _Address.getX();
}
```

- 参数类型是目标合约类型
- 调用时传入地址即可，底层仍是 `address` 类型

------

#### 方法 3️⃣ 创建合约变量

```
function callGetX2(address _Address) external view returns(uint x){
    OtherContract oc = OtherContract(_Address);
    x = oc.getX();
}
```

- 🏗 先创建合约引用变量 `oc`
- 🔧 通过 `oc.getX()` 调用函数

------

#### 方法 4️⃣ 调用并发送 ETH

```
function setXTransferETH(address otherContract, uint256 x) payable external {
    OtherContract(otherContract).setX{value: msg.value}(x);
}
```

- 支持 payable 函数
- 💸 可以在调用时向目标合约转账 ETH
- 📣 通过 `Log` 事件或 `getBalance()` 查看 ETH 余额变化

------

### 3️⃣ 总结

- 🔹 已知合约地址 + 合约代码（或接口）即可调用目标函数
- 🔹 支持多种调用方式：传入地址、合约变量、创建临时引用
- 🔹 💰 payble 函数可在调用时发送 ETH

✅ 掌握了 **合约互调用** 的基础用法，为构建 DApp 链上交互打下基础。

---

## 📞 Call 调用其他合约

------

### 1️⃣ Call 概述

- **call** 是 `address` 类型的低级成员函数
- 返回值为 `(bool, bytes memory)`：
  - `bool` → 调用是否成功
  - `bytes memory` → 目标函数返回值
- ✅ 官方推荐用于触发 **fallback** 或 **receive** 函数发送 ETH
- ⚠️ 不推荐用 call 调用未知合约函数（可能被恶意控制）
- 🔹 当未知合约源码或 ABI 时，call 可用来调用其函数

------

### 2️⃣ 使用规则

```solidity
目标合约地址.call(字节码);
```

- 字节码通过 `abi.encodeWithSignature` 获取：

```solidity
abi.encodeWithSignature("函数签名", 参数1, 参数2, ...)
```

- 示例：

```solidity
abi.encodeWithSignature("f(uint256,address)", _x, _addr)
```

- 可指定 ETH 和 gas 数额：

```solidity
目标合约地址.call{value:发送数额, gas:gas数额}(字节码);
```

------

### 3️⃣ 目标合约示例

```solidity
contract OtherContract {
    uint256 private _x = 0;
    event Log(uint amount, uint gas);

    fallback() external payable {} // 🔄 fallback

    function getBalance() view public returns(uint) {
        return address(this).balance;
    }

    function setX(uint256 x) external payable {
        _x = x;
        if(msg.value > 0){
            emit Log(msg.value, gasleft());
        }
    }

    function getX() external view returns(uint x){
        x = _x;
    }
}
```

- **状态变量**：`_x`
- **事件**：收到 ETH 时触发 `Log`
- **函数**：
  - `getBalance()` → 合约 ETH 余额
  - `setX(uint256 x)` → 修改 `_x` 并可发送 ETH
  - `getX()` → 读取 `_x`

------

### 4️⃣ 利用 call 调用目标合约

#### 4.1 定义 Response 事件

```solidity
event Response(bool success, bytes data); // 📣 输出 call 返回结果
```

------

#### 4.2 调用 setX() 并发送 ETH

```solidity
function callSetX(address payable _addr, uint256 x) public payable {
    (bool success, bytes memory data) = _addr.call{value: msg.value}(
        abi.encodeWithSignature("setX(uint256)", x)
    );

    emit Response(success, data); // 📣 输出结果
}
```

- 示例：将 `_x` 改为 `5`
- `Response` 事件中 `data` 为 `0x`（setX 无返回值）

------

#### 4.3 调用 getX() 并解码返回值

```solidity
function callGetX(address _addr) external returns(uint256){
    (bool success, bytes memory data) = _addr.call(
        abi.encodeWithSignature("getX()")
    );

    emit Response(success, data); // 📣 输出结果
    return abi.decode(data, (uint256)); // 🔍 解码返回值
}
```

- Response 输出示例：

```
0x0000000000000000000000000000000000000000000000000000000000000005
```

- 解码后返回 `5`

------

#### 4.4 调用不存在的函数 → fallback

```solidity
function callNonExist(address _addr) external{
    (bool success, bytes memory data) = _addr.call(
        abi.encodeWithSignature("foo(uint256)")
    );

    emit Response(success, data); // 📣 输出结果
}
```

- 调用 `foo`（不存在） → 触发目标合约 `fallback`
- `success = true`，返回 `data` 空

------

### 5️⃣ 🔑 总结

- call 可在未知源码或 ABI 情况下调用合约
- ✅ 可发送 ETH 并触发 fallback/receive
- ⚠️ 不安全，不推荐用于普通合约函数调用
- 🔹 推荐方式：声明合约变量后直接调用函数（第 21 讲）

---

## 🧩 Delegatecall

`delegatecall` 与 `call` 类似，是 Solidity 中 `address` 类型的低级成员函数。

------

### 1️⃣ 上下文对比

#### 🔹 call 的上下文

- 用户 **A** → 通过合约 **B** 调用合约 **C**
- 执行的是 **C** 的函数
- 上下文（Context）是 **C**：
  - `msg.sender = B`
  - 状态变量修改作用于 **C**

------

#### 🔹 delegatecall 的上下文

- 用户 **A** → 通过合约 **B** 调用合约 **C**
- 执行的是 **C** 的函数
- 上下文仍是 **B**：
  - `msg.sender = A`
  - 状态变量修改作用于 **B**

💡 类比：用户把资产交给代理（C）管理，但改变的是自己的资产（B 的状态变量）

------

### 2️⃣ delegatecall 语法

```
目标合约地址.delegatecall(二进制编码);
```

- 二进制编码通过 `abi.encodeWithSignature` 获取：

```
abi.encodeWithSignature("函数名(参数类型列表)", 参数1, 参数2, ...)
```

- 示例：

```
abi.encodeWithSignature("f(uint256,address)", _x, _addr)
```

- 可指定 **gas**，但 **不能发送 ETH**

⚠️ 安全注意：

- 当前合约和目标合约的状态变量布局必须相同
- 目标合约必须安全，否则可能造成资产损失

------

### 3️⃣ 使用场景

- 🏛 **代理合约（Proxy Contract）**
  - 分离存储合约和逻辑合约
  - 逻辑合约函数通过 `delegatecall` 修改代理合约状态
  - 升级逻辑合约无需迁移存储
- 💎 **EIP-2535 Diamonds（钻石）**
  - 模块化可扩展智能合约
  - 一个代理合约可拥有多个逻辑实现合约

------

### 4️⃣ 示例

#### 4.1 被调用的合约 C

```
contract C {
    uint public num;
    address public sender;

    function setVars(uint _num) public payable {
        num = _num;
        sender = msg.sender;
    }
}
```

- **变量**：`num`（uint）和 `sender`（address）
- **函数**：`setVars` 设置变量并记录调用者

------

#### 4.2 发起调用的合约 B

> ⚠️ 变量布局必须和 C 相同（类型和顺序）

```
contract B {
    uint public num;
    address public sender;

    // call 修改 C 的状态
    function callSetVars(address _addr, uint _num) external payable {
        (bool success, bytes memory data) = _addr.call(
            abi.encodeWithSignature("setVars(uint256)", _num)
        );
    }

    // delegatecall 修改 B 的状态
    function delegatecallSetVars(address _addr, uint _num) external payable {
        (bool success, bytes memory data) = _addr.delegatecall(
            abi.encodeWithSignature("setVars(uint256)", _num)
        );
    }
}
```

------

### 5️⃣ 🔑 总结

- `delegatecall` 允许在 **调用别的合约函数** 时，保持当前合约上下文
- ✅ 运行函数代码来自目标合约，但状态变量修改作用于调用者
- ⚠️ 不安全，使用时必须保证：
  - 变量布局一致
  - 目标合约安全
- 🌟 主要应用：代理合约和钻石标准（EIP-2535 Diamonds）

---

## 🏗 在合约中创建新合约

在以太坊链上，用户（EOA）可以创建智能合约，智能合约同样也可以创建新的智能合约。
 💡 例如：去中心化交易所 **Uniswap** 利用工厂合约（PairFactory）创建了无数个币对合约（Pair）。

------

### 1️⃣ create 方法

- 用途：在合约中创建新合约
- 语法：

```
Contract x = new Contract{value: _value}(params);
```

- 说明：
  - `Contract`：要创建的合约名
  - `x`：新合约对象（地址）
  - `value`：如果构造函数是 `payable`，可创建时转入 ETH
  - `params`：构造函数参数

------

### 2️⃣ 极简 Uniswap 示例

Uniswap V2 核心合约主要包含：

- 🔹 **UniswapV2Pair**：币对合约，管理币对地址、流动性、买卖
- 🔹 **UniswapV2Factory**：工厂合约，创建新币对并管理地址

我们用 **create** 方法实现一个极简版：

------

#### 🔹 Pair 合约

```
contract Pair{
    address public factory; // 工厂合约地址
    address public token0;  // 代币1
    address public token1;  // 代币2

    constructor() payable {
        factory = msg.sender;
    }

    // 初始化代币地址
    function initialize(address _token0, address _token1) external {
        require(msg.sender == factory, 'UniswapV2: FORBIDDEN');
        token0 = _token0;
        token1 = _token1;
    }
}
```

- **变量**：
  - `factory`：工厂地址
  - `token0` / `token1`：币对代币地址
- **函数**：
  - `constructor`：部署时将 `factory` 赋值
  - `initialize`：由工厂合约调用，初始化代币地址

💡 **为什么不在 constructor 中直接初始化代币？**

- 因为 Uniswap 使用 **create2** 生成可预测合约地址（第25讲讲解）

------

#### 🔹 PairFactory 合约

```
contract PairFactory{
    mapping(address => mapping(address => address)) public getPair;
    address[] public allPairs;

    function createPair(address tokenA, address tokenB) external returns (address pairAddr) {
        // 创建新合约
        Pair pair = new Pair(); 
        // 初始化代币地址
        pair.initialize(tokenA, tokenB);
        // 更新映射和数组
        pairAddr = address(pair);
        allPairs.push(pairAddr);
        getPair[tokenA][tokenB] = pairAddr;
        getPair[tokenB][tokenA] = pairAddr;
    }
}
```

- **状态变量**：
  - `getPair`：两个代币地址 → Pair 地址
  - `allPairs`：存储所有 Pair 地址
- **函数 createPair**：
  1. `Pair pair = new Pair();` 创建新合约
  2. `pair.initialize(tokenA, tokenB);` 初始化币对
  3. 更新映射和数组

------

### 3️⃣ 🔑 总结

- 合约内部可以通过 **create** 创建新合约
- 构造函数可传入参数，也可转入 ETH
- 实战应用：
  - Uniswap 工厂合约创建币对
  - 其他需要动态部署合约的场景

💡 下一讲将介绍 **create2**，可实现可预测合约地址，优化升级方案

---

## 🧩 CREATE2

CREATE2 操作码可以让我们在智能合约部署到以太坊网络之前就预测它的地址。
 💡 Uniswap 创建 Pair 合约用的就是 **CREATE2** 而不是 **CREATE**。

------

### 1️⃣ CREATE 如何计算合约地址

普通 CREATE 的规则：

- 合约可以由 **EOA** 或 **合约** 创建
- 地址计算公式：

```
新地址 = hash(创建者地址, nonce)
```

- 解释：
  - `创建者地址`：部署者地址（钱包或合约地址）
  - `nonce`：该地址创建合约的次数，每次+1

💡 因为 nonce 会变化，所以 CREATE 创建的合约地址 **不可预测**

------

### 2️⃣ CREATE2 如何计算合约地址

CREATE2 让合约地址 **独立于未来事件**，公式如下：

```
新地址 = hash(0xFF, 创建者地址, salt, initcode)
```

- 组成部分：
  - `0xFF`：常数，避免与 CREATE 冲突
  - `创建者地址`：调用 CREATE2 的合约地址
  - `salt`：bytes32 类型，由创建者指定，用来影响地址
  - `initcode`：新合约的初始化字节码（Creation Code + 构造函数参数）

💡 CREATE2 确保相同的 **salt** 和 **initcode** 会生成相同地址

------

### 3️⃣ CREATE2 使用方法

```
Contract x = new Contract{salt: _salt, value: _value}(params);
```

- `_salt`：指定盐
- `_value`：可选，构造函数可转入 ETH
- `params`：构造函数参数

------

### 4️⃣ 极简 Uniswap 示例

#### 🔹 Pair 合约（与 CREATE 相同）

```
contract Pair{
    address public factory;
    address public token0;
    address public token1;

    constructor() payable {
        factory = msg.sender;
    }

    function initialize(address _token0, address _token1) external {
        require(msg.sender == factory, 'UniswapV2: FORBIDDEN');
        token0 = _token0;
        token1 = _token1;
    }
}
```

- **变量**：factory / token0 / token1
- **函数**：constructor / initialize

------

#### 🔹 PairFactory2 合约（CREATE2）

```
contract PairFactory2{
    mapping(address => mapping(address => address)) public getPair;
    address[] public allPairs;

    function createPair2(address tokenA, address tokenB) external returns (address pairAddr) {
        require(tokenA != tokenB, 'IDENTICAL_ADDRESSES');

        // 排序代币地址
        (address token0, address token1) = tokenA < tokenB ? (tokenA, tokenB) : (tokenB, tokenA);
        bytes32 salt = keccak256(abi.encodePacked(token0, token1));

        // 使用 CREATE2 部署新合约
        Pair pair = new Pair{salt: salt}(); 
        pair.initialize(tokenA, tokenB);

        // 更新映射和数组
        pairAddr = address(pair);
        allPairs.push(pairAddr);
        getPair[tokenA][tokenB] = pairAddr;
        getPair[tokenB][tokenA] = pairAddr;
    }
}
```

- `salt` = `keccak256(token0, token1)`
- 合约地址可提前预测

------

#### 🔹 提前计算合约地址

```
function calculateAddr(address tokenA, address tokenB) public view returns(address predictedAddress){
    require(tokenA != tokenB, 'IDENTICAL_ADDRESSES');
    (address token0, address token1) = tokenA < tokenB ? (tokenA, tokenB) : (tokenB, tokenA);
    bytes32 salt = keccak256(abi.encodePacked(token0, token1));

    predictedAddress = address(uint160(uint(keccak256(abi.encodePacked(
        bytes1(0xff),
        address(this),
        salt,
        keccak256(type(Pair).creationCode)
    )))));
}
```

- ✅ 可以提前验证 **预测地址** 是否和实际部署地址一致

💡 若构造函数带参数，则 initcode 需包括参数：

```
keccak256(abi.encodePacked(type(Pair).creationCode, abi.encode(address(this))))
```

------

### 6️⃣ CREATE2 实际应用场景

- 交易所为新用户预留合约地址
- Uniswap V2 Factory 使用 CREATE2 创建交易对
  - ✅ 好处：**地址确定**，Router 可直接计算 pair 地址，无需跨合约调用 `getPair`

------

### 7️⃣ 🔑 总结

- CREATE2 可在合约部署前确定地址
- 用途：
  - 升级合约 / 代理合约
  - 预计算交易对地址
- 公式核心：`hash(0xFF, creator, salt, initcode)`

💡 CREATE2 是一些 **Layer2 项目和去中心化交易所** 的基础

---

## 🗑️ 删除合约（selfdestruct）

`selfdestruct` 是 Solidity 中用于删除智能合约的命令，同时可以将合约剩余 ETH 转移到指定地址。

------

### 1️⃣ 历史与版本变化

- **原名**：`suicide`（自杀）
  - 太敏感，改为 `selfdestruct`
- **v0.8.18**：标记为 **不再建议使用**（编译阶段警告，参考 EIP-6049）
- **坎昆升级（Cancun）**：
  - EIP-6780 限制了 SELFDESTRUCT 功能
  - 目前功能仅剩：**将合约中的 ETH 转移到指定地址**
  - **原删除功能**：只能在 **同笔交易内创建 + 自毁** 时生效
  - **已部署合约无法单独被销毁**

------

### 2️⃣ selfdestruct 使用方法

```
selfdestruct(_addr);
```

- `_addr`：接收 ETH 的地址
- 接收地址 **无需 implement receive() 或 fallback()**
- 简单实现：销毁合约并转账剩余 ETH

------

### 3️⃣ 示例：转移 ETH 功能

```
contract DeleteContract {
    uint public value = 10;

    constructor() payable {}

    receive() external payable {}

    function deleteContract() external {
        selfdestruct(payable(msg.sender));
    }

    function getBalance() external view returns(uint balance){
        balance = address(this).balance;
    }
}
```

- **功能**：
  - `deleteContract()`：自毁并将 ETH 转给调用者
  - `getBalance()`：查看合约 ETH 余额

------

#### 🔹 验证效果

- **坎昆升级前**
  - 自毁后，合约不可再调用
  - ETH 转出到指定地址
- **坎昆升级后**
  - 合约依然存在
  - ETH 被转出
  - 再次调用合约函数仍可执行

------

### 4️⃣ Demo：同笔交易内创建 + 自毁

```solidity
contract DeployContract {
    struct DemoResult {
        address addr;
        uint balance;
        uint value;
    }

    constructor() payable {}

    function getBalance() external view returns(uint balance){
        balance = address(this).balance;
    }

    function demo() public payable returns (DemoResult memory){
        DeleteContract del = new DeleteContract{value:msg.value}();
        DemoResult memory res = DemoResult({
            addr: address(del),
            balance: del.getBalance(),
            value: del.value()
        });
        del.deleteContract(); // 同笔交易内自毁
        return res;
    }
}
```

- ✅ 在同笔交易内创建 + selfdestruct
- ETH 会正确返回到 `DeployContract`
- DeleteContract 地址无 ETH，再调用函数失败

------

### 5️⃣ 注意事项

- 对外提供销毁接口时，**最好仅限合约所有者调用**
  - 使用 `onlyOwner` 修饰符
- **安全风险**：
  - 攻击者可利用 selfdestruct 向合约频繁转 token
  - 降低用户对合约信任

------

### 6️⃣ 🔑 总结

- `selfdestruct` = 智能合约的 **紧急按钮**
- 功能：
  1. 销毁合约（坎昆前）
  2. 将 ETH 转移到指定地址（坎昆后）
- 同笔交易可实现 **创建 + 自毁**
- 使用场景：
  - 应对合约出错
  - 停止攻击（如 The DAO 攻击事件）

---

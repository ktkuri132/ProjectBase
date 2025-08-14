# STM32 CMakeLists.txt 自动生成器

## 项目概述

这是一个专为 STM32 系列项目设计的智能构建配置生成工具，包含两个核心脚本：`build.py` 和 `download.py`。该工具能够自动分析项目目录结构，生成完整的 CMake 构建配置，并为主流 IDE 提供开箱即用的配置文件。

## 核心特性

### 🚀 智能项目分析
- **自动芯片检测**：根据项目文件自动识别 STM32 芯片型号
- **架构自适应**：自动匹配 Cortex-M 架构（M0/M0+/M3/M4/M7/M33）
- **浮点单元配置**：根据芯片架构自动配置 FPU 参数
- **递归文件扫描**：智能扫描所有源文件和头文件
- **链接脚本查找**：自动定位并配置链接脚本文件

### 📁 文件系统管理
- **目录结构分析**：自动识别项目层次结构
- **子项目支持**：检测并处理带有 CMakeLists.txt 的子目录
- **静态库识别**：自动识别和配置项目依赖的静态库
- **智能排除**：自动排除构建目录、IDE 配置等无关文件

### ⚙️ 配置管理系统
- **JSON 配置文件**：使用 `project_config.json` 持久化项目配置
- **默认配置合并**：智能合并用户配置与默认配置
- **配置版本管理**：支持配置的增量更新和向后兼容
- **手动刷新选项**：支持重新扫描项目文件

### 🔧 多工具链支持
- **编译器检测**：从 CMake 缓存自动读取工具链路径
- **交叉编译配置**：完整的 ARM GCC 工具链配置
- **优化选项**：支持多级优化和调试信息配置
- **警告配置**：可配置的编译警告选项

## 主要功能模块

### build.py - 构建配置生成器

#### 核心功能
- **项目初始化**：首次运行时自动检测和配置项目
- **CMakeLists.txt 生成**：生成完整的 CMake 构建脚本
- **工具链配置**：自动配置 ARM GCC 工具链
- **IDE 配置生成**：为 CLion 和 VSCode 生成配置文件

#### 支持的芯片系列
```
STM32F0 系列 → Cortex-M0
STM32F1 系列 → Cortex-M3  
STM32F2 系列 → Cortex-M3
STM32F3 系列 → Cortex-M4
STM32F4 系列 → Cortex-M4
STM32F7 系列 → Cortex-M7
STM32G0 系列 → Cortex-M0+
STM32G4 系列 → Cortex-M4
STM32H7 系列 → Cortex-M7
STM32L0 系列 → Cortex-M0+
STM32L1 系列 → Cortex-M3
STM32L4 系列 → Cortex-M4
STM32L5 系列 → Cortex-M33
STM32U5 系列 → Cortex-M33
STM32WB 系列 → Cortex-M4
STM32WL 系列 → Cortex-M4
```

### download.py - 下载和调试工具

#### 核心功能
- **多接口支持**：STLink、JLink、CMSIS-DAP、XDS110
- **OpenOCD 集成**：完整的 OpenOCD 下载配置
- **ELF 文件查找**：自动定位构建输出的 ELF 文件
- **配置同步**：与 build.py 共享配置信息

#### 支持的调试接口
- **STLink**：ST 官方调试器
- **JLink**：Segger JLink 系列
- **CMSIS-DAP**：ARM 标准调试接口
- **XDS110**：TI XDS110 调试器

## 生成的配置文件

### 项目配置文件
- **project_config.json**：项目主配置文件
- **idea.cfg**：CLion OpenOCD 配置
- **CMakeLists.txt**：CMake 构建脚本

### VSCode 配置文件
- **.vscode/c_cpp_properties.json**：C/C++ 智能感知配置
- **.vscode/launch.json**：调试配置
- **.vscode/tasks.json**：构建任务配置
- **.vscode/settings.json**：编辑器设置
- **.vscode/extensions.json**：推荐扩展

## 快速开始

### 1. 环境准备
```bash
# 确保已安装以下工具：
- Python 3.6+
- ARM GCC 工具链
- OpenOCD（用于下载调试）
- CMake 3.16+
```

### 2. 初始化项目
```bash
# 在项目根目录运行
python build.py

# 指定芯片型号（可选）
python build.py --chip STM32F407VG --board DISCOVERY
```

### 3. 构建项目
```bash
# 使用生成的 CMakeLists.txt 构建
mkdir build && cd build
cmake ..
make
```

### 4. 下载程序
```bash
# 下载到目标芯片
python download.py
```

## 详细使用指南

### build.py 使用方法

#### 命令行参数
```bash
python build.py [选项]

选项：
  --chip CHIP      指定芯片型号 (默认: STM32F103C8)
  --board BOARD    指定开发板 (默认: BLUEPILL)
  --force          强制覆盖现有文件
  --dir DIR        指定项目目录 (默认: 当前目录)
```

#### 配置文件结构
```json
{
  "project": {
    "name": "项目名称",
    "chip": "STM32F103C8",
    "board": "BLUEPILL",
    "architecture": "cortex-m3",
    "instruction_set": "thumb",
    "float_type": "软件浮点"
  },
  "toolchain": {
    "c_compiler": "arm-none-eabi-gcc",
    "cxx_compiler": "arm-none-eabi-g++",
    "asm_compiler": "arm-none-eabi-gcc"
  },
  "build": {
    "optimization": "O0",
    "debug_info": "g3",
    "warnings": true
  },
  "download": {
    "interface": "stlink",
    "target": "stm32f1x",
    "speed": "4000"
  }
}
```

### download.py 使用方法

#### 下载流程
1. 读取项目配置文件
2. 更新 idea.cfg OpenOCD 配置
3. 查找构建输出的 ELF 文件
4. 执行 OpenOCD 下载命令

#### 支持的配置选项
- **下载接口**：可选择不同的调试器接口
- **下载速度**：可调节下载和调试速度
- **目标配置**：自动匹配 OpenOCD 目标配置

## 高级功能

### 自动配置检测
- **工具链路径检测**：从 CMake 缓存读取工具链路径
- **芯片架构推导**：根据芯片型号自动配置架构参数
- **浮点单元配置**：根据 Cortex-M 架构自动配置 FPU
- **链接脚本匹配**：智能匹配项目中的链接脚本文件

### IDE 集成特性

#### CLion 支持
- 自动生成 OpenOCD 配置文件
- 支持调试和下载功能
- 完整的 CMake 项目支持

#### VSCode 支持
- C/C++ 智能感知配置
- Cortex-Debug 扩展支持
- 集成构建和下载任务
- 推荐扩展列表

### 项目文件管理
- **源文件分组**：按目录自动分组源文件
- **头文件路径**：自动配置所有头文件目录
- **子项目处理**：支持复杂的多级项目结构
- **排除规则**：智能排除不相关的文件和目录

## 注意事项

### 使用限制
- 当前仅支持 STM32 系列微控制器
- 需要预先安装 ARM GCC 工具链
- OpenOCD 需要单独安装和配置
- Python 环境需要 3.6 或更高版本

### 最佳实践
1. **首次使用**：让脚本自动检测项目配置
2. **配置调整**：通过修改 `project_config.json` 调整配置
3. **重新扫描**：文件结构变化后可选择重新扫描
4. **版本控制**：建议将配置文件加入版本控制

### 故障排除
- **工具链路径问题**：检查 ARM GCC 是否正确安装
- **OpenOCD 配置**：确认 OpenOCD 安装路径和配置文件
- **权限问题**：确保对项目目录有读写权限
- **编码问题**：确保源文件使用 UTF-8 编码

## 技术支持

本工具专为实验室内部使用设计，如遇问题请联系开发团队获取技术支持。

---

*注意：本工具的源代码为闭源项目，仅供实验室内部使用，未经授权不得传播或修改。*

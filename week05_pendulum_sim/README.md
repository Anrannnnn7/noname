# 第 5 周实验报告：单摆模拟器

## 实验基本信息

| 项目 | 内容 |
|------|------|
| 实验名称 | 单摆模拟器 (Pendulum Simulator) |
| 实验者姓名 | 郭梓萌 |
| 学号 | 7902126161 |
| 完成日期 | 2026-10-16 |
| 课程名称 | 人机协同程序设计 |

## 项目说明

### 功能特性

本实验实现了一个基于 HTML5 Canvas 的单摆物理模拟器，具备以下功能：

1. **物理仿真核心**
   - 简谐振动模型：θ'' = -(g/L)sin(θ)
   - 可调节参数：摆长 (100~350px)、重力加速度 (1~25 m/s²)、阻尼系数 (0~0.010)
   - 实时物理计算与动画渲染（requestAnimationFrame 驱动）

2. **交互控制面板**
   - 三个参数滑块，实时调整物理参数
   - 「重置摆动」按钮，精准回归 45° 初始状态
   - 四个天体预设场景：地球 (g=9.8)、月球 (g=1.62)、木星 (g=24.79)、太空 (g=0.1)
   - 重力平滑过渡动画（300ms 三次缓出）

3. **数据持久化**
   - localStorage 自动保存摆长/重力/阻尼参数
   - 刷新页面后自动恢复上次设置
   - 首次访问使用默认参数：L=200 / g=9.8 / k=0.001

4. **视觉效果**
   - 深灰科技风背景 (#1a1a24)
   - 摆球径向渐变 + 发光阴影效果
   - 运动轨迹渐隐尾迹
   - 实时 HUD 显示摆角、角速度、当前参数

### 技术实现

- **单文件架构**：纯原生 HTML5 + CSS3 + JavaScript，无外部依赖
- **物理引擎**：欧拉积分法求解单摆微分方程
- **动画循环**：requestAnimationFrame 驱动物理计算与 Canvas 渲染
- **状态管理**：闭包封装 + localStorage 持久化
- **事件绑定**：addEventListener 替代内联 onclick，避免作用域问题

## 运行方式

1. 进入项目目录：`week05_pendulum_sim/`
2. 双击打开 `index.html`
3. 在浏览器中体验单摆模拟
4. 拖动滑块调整参数，或点击预设按钮切换天体场景
5. 点击「重置摆动」按钮恢复初始状态

### 故障演练页面

`bug_practice/index.html` 为作用域调试教学页面，演示 IIFE 局部作用域与全局事件处理器的冲突问题及修复方案。

## 人机协同三反思

### 1. 意图清晰度反思

实验中没有出现结果不符合预期的现象，没有缺少输入提示词。

### 2. 控制闭环反思

遇到异常时，我先打开Console，复现错误后复制给AI，AI定位到的根因是：resetAnimation 定义在 IIFE (() => {...})() 内部，属于函数局部作用域。而 HTML 内联事件处理器 onclick="resetAnimation()" 在全局作用域（window）执行，无法访问 IIFE 内部的局部变量，因此抛出 ReferenceError。修复方案是将内联 onclick 替换为 IIFE 内部的 addEventListener，保持函数作用域一致。

根因：resetAnimation 定义在 IIFE 局部作用域（第 71-74 行）。内联事件处理器 onclick="resetAnimation()" 在全局 window 作用域执行，局作用域无法访问 IIFE 内部函数 → ReferenceError。

修复方案：
- 第49行：移除 onclick="resetAnimation()" 内联属性
- 第75行：新增 document.querySelector('.btn-reset').addEventListener('click', resetAnimation)

我理解了改动的本质是：addEventListener 在 IIFE 内部注册，与 resetAnimation 处于同一作用域，闭包捕获函数引用，彻底消除作用域断裂。

### 3. 主体地位确定

我定边界：原生 Canvas 单文件、单摆微分方程模型、参数区间、控制台 0 报错红线，并要求只做增量修改。AI 负责绘制函数、样式、事件绑定等重复劳动。

## GitHub 仓库链接

[GitHub URL]

## 目录结构

```
week05_pendulum_sim/
├── index.html              # 主模拟器（含控制面板与预设场景）
├── bug_practice/
│   └── index.html          # 故障演练页面
└── README.md               # 实验报告
```

## Git 提交历史

```
a75fec8 docs(week05): complete experiment report with three reflections
1e70916 feat(week05): add celestial presets with smooth gravity transition and localStorage persistence
e1f89e0 fix(week05): resolve resetAnimation scope error with addEventListener
4417d09 feat(week05): add bug practice page for scope debugging exercise
d3a7fc1 feat(week05): add interactive control panel with sliders and reset button
d19cb45 feat(week05): add pendulum simulator with canvas animation
efb490c feat(week05): init pendulum experiment workspace
```

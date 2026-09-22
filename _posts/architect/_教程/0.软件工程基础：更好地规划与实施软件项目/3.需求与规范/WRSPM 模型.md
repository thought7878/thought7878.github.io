## 中文

`世界（World）`——用于开发系统的世界假设。（会有互联网吗？有电力供应吗？网速是多少？）

`需求（Requirements）`——界定**当前面临的问题**，以及**解决该问题的方案应如何运作**。

`规格说明（Specifications）`——**定义系统的技术要求**。在此阶段，我们**将解决方案的构想与系统本身联系起来**。

`程序（Program）`——**程序及其代码本身**。

`机器（Machine）`——软件运行所需的**物理硬件**。如服务器、内存（RAM）、处理器（CPU）等。

![[_posts/architect/_教程/0.软件工程基础：更好地规划与实施软件项目/3.需求与规范/media/9e216e893a57a5b3d6eece3827fdcf9d_MD5.webp]]

---

WRSPM 模型有助于更精准地界定问题。我们不仅关注正在构建的程序本身，还会考量环境因素和实际硬件。将环境因素纳入考量，能够对程序的开发方式产生至关重要的影响。

举个例子，假设我们正在开发一款处理用户金融交易的软件。作为程序员，我们默认系统会始终连接电源。然而，也许这款软件是为一个经常随机停电的地区开发的，电力随时可能被切断。

此时，我们就需要开发一套具备容错机制的软件方案，以应对电力突然中断的情况。这种机制可能部署在终端，也可能部署在服务器本身。无论具体如何设计，提前了解这一约束条件都至关重要。WRSPM 模型正是帮助我们回答这类问题的工具。

---

此外，我们还可以使用以下分类维度进行更细致的划分：

**Eh - 环境隐藏（Environment Hidden）**。这些是环境中存在的数据，但它们仅停留在概念或想法层面。例如，你脑海中记住的某个账户密码。这条信息只存在于你的意识中，因此它是一个变量，但处于隐藏状态。

**Ev - 环境可见（Environment Visible）**。这些是环境中已经变得可见的数据。当你在计算机终端上输入那个密码时，它就被具象化了。此时，它在物理世界中有了表征，并且以一种系统能够使用的方式呈现出来。

**Sv - 系统可见（System Visible）**。这是系统中对用户可见的部分，包括程序和机器。例如键盘、显示器、鼠标等硬件设备，同时也涵盖你可以与之交互的屏幕界面和程序功能模块。

**Sh - 系统隐藏（System Hidden）**。这是系统中对用户不可见的部分，包括用户不直接接触的软件内部逻辑，以及机器内部的物理组件。以计算器为例：用户输入两个需要相加的数字，而机器在内部如何将这两个数字相加并得出结果的过程，就是隐藏的部分。

## 英文

**World** - The world assumptions which are used to develop the system. (Will there be internet? Electricity?What is the speed of the internet?)

**Requirements** - Defining the problem at hand and how a solution to that problem should operate.

**Specifications** - Defining the technical requirements of the system. Here we are linking together the idea of the solution, to the system itself.

**Program** - The program and code itself.

**Machine** - The physical hardware needed for the software. Servers, RAM, CPU, etc.

![[_posts/architect/_教程/0.软件工程基础：更好地规划与实施软件项目/3.需求与规范/media/9e216e893a57a5b3d6eece3827fdcf9d_MD5.webp]]

The WRSPMmodel helps define the problem better. Not only do we take into account the program we are building, we also consider things like the environment, and the actual hardware. Taking into account things like the environment can make very important distinctions to how the program is developed.

For example, lets say we are developing a software which handles user financial transactions. We as programmers assume that the system will always be connected to power. However, maybe this particular software is being developed for a place which has random blackouts. So at any time the power can be cut.

We now need to develop a software solution which has fail-safe features for if the power is suddenly cut off. Maybe this takes place at the terminal, or the server itself. However it's designed, it's important to know this constraint beforehand. The WRSPM model helps us answer these questions.

There are additional areas of classification we can use as well.

**Eh** - Environment hidden. These are pieces of data within the environment, which are just concepts or ideas. For example, the password you have memorized for an account. This piece of information exists only within your mind. Therefore it's a variable, but hidden.

**Ev -** Environment Visible. These are pieces of data within the environment which are now visible. Entering that password into a computer terminal manifests it. It is now represented in the physical world, and in a way which the system can now use.

**Sv** - System Visible. This is the part of the system which is visible to the user. This includes the program and the machine. So the keyboard, monitor, mouse etc. It also covers the screens and part of the program which you can interact with.

**Sh** - System Hidden. This is the part of the system which is hidden from the user. This includes the part of the software that the user doesn't touch, and the components within the machine itself. An example of this would be with a calculator. The user enters in the two numbers to be added. The hidden part is how the machine adds the numbers together before showing the result to the user.
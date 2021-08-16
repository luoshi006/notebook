# 4. Decision Making
## 4.1 State Machine
- _finite state machines_ (FSM) 将对象的行为分解成易于管理的状态
	- **Hierarchical State Machines**
	- **Simultaneous State Machines**
## 4.2 Behavior Trees
### 4.2.1 Core Concepts
- FSM 不利于模块化
### 4.2.2 Leaf Tasks
- 叶子任务是行为树的终端任务，定义了底层动作；
- Type
	- **Actions**
	- **Conditions**
### 4.2.3 Composite Tasks
#### Selector
- 挨个尝试，直到成功。
- ![|500](https://cloud.githubusercontent.com/assets/2366334/4603480/5f2d3274-516d-11e4-80c5-8e5df55f9fd1.png)
#### Random Selector
- 随机选择一个执行
- ![|500](https://cloud.githubusercontent.com/assets/2366334/10428008/881c282e-70ee-11e5-92b4-9d232b066383.png)

#### Sequence
- 依次运行各个子任务，任一失败，立即返回；全部执行完，返回成功
- ![|500](https://cloud.githubusercontent.com/assets/2366334/4603496/61776ee0-516e-11e4-810a-cdc1a333d50d.png)
#### Random Sequence
- 随机顺序，执行所有子任务
- ![|500](https://cloud.githubusercontent.com/assets/2366334/10428100/53ad2178-70ef-11e5-8ff0-e1cc28186b13.png)
#### Decorator 装饰器
- 类似于再封装
- 只有一个子任务，以某种方式修改子任务的行为。
- ![|200](https://cloud.githubusercontent.com/assets/2366334/4604011/2bc98188-5183-11e4-952c-fc780e597794.png)
- Type
	- **AlwaysFail**
	- **AlwaysSucceed**
	- **Include** 包含外部子树
	- **Invert**
	- **Repeat** 指定重复次数
	- **SemaphoreGuard** 限制资源数量
	- **UntilFail**
	- **UntilSuccess**
#### Parallel
- 并发管理，并行运行每个子任务。
	- **Non conflicting actions**
	- **Condition checking** 唤醒或中断
	- **Group behavior** 多智能体的小组行为
##### Policies
- **Sequence policy** 任一任务失败，则失败
- **Selector policy** 只要有一个任务成功，则成功
##### Orchestrators
- **Resume orchestrator** 每个周期 start-resume
- **Join orchestrator** 失败后不会 re-run
### 4.2.4 Formalism Extensions 扩展
#### Guards
- 执行响应任务时，必须要满足的条件
- Guard 可以扩展任何行为树节点
#### Dynamic Guard Selector
- 包含多个分支任务，每个分支任务都具有 Guard
	- 执行第一个 Guard == True 的任务；
	- 在每次循环中，刷新各个 Guard；
	- 若当前正在运行的 Task，Guard == False，则该子任务被立即取消。然后执行其他 Guard == True 的子任务
	- 子任务执行完成，且所有 Guard 都为 False，则 return 完成

### 4.2.5 Behavior Tree API
#### Task Class Hierarchy
- ![](https://cloud.githubusercontent.com/assets/2366334/4607905/d2890894-5265-11e4-901c-ef775c706df5.png)
#### The Task Superclass 抽象类
- **status**
	-   `FRESH` if the task has never run or has been reset
	-   `RUNNING` if the task has not completed and needs to run again
	-   `FAILED` if the task returned a failure result
	-   `SUCCEEDED` if the task returned a success result
	-   `CANCELLED` if the task has been terminated by an ancestor
- **control**  状态为 `FRESH` 时，置 NULL；否则，置为 Parent
- **tree** 任务所属行为树的的引用

#### Using Data for Inter-Task Communication  任务间通信
#### Task Attributes and Constraints 任务属性和约束
#### Behavior Tree Libraries









---
Prev [[3 - Pathfinding]]
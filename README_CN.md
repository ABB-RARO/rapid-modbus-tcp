# RAPID Modbus TCP

## 概述
**RAPID Modbus TCP** 是用于ABB机器人控制器的 Modbus/TCP 客户端/服务端 RAPID 程序库。它实现了[Modbus 协议规范](https://modbus.org/docs/Modbus_Application_Protocol_V1_1b3.pdf)。该库包含3个部分，可根据需求独立安装。
  
- **服务端**(Server):支持 Modbus 功能码 FC01、FC03、FC05、FC06、FC0F、FC10，用于读写线圈和保持寄存器。服务端在任意时刻只允许通过502端口与一个客户端通信，通信周期为 200 毫秒。     
- **客户端**(Client):支持 Modbus 功能码 FC01、FC02、FC03、FC04、FC05、FC06、FC0F、FC10，用于读写线圈、离散输入、输入寄存器和保持寄存器。 
- **夹爪模板**(Gripper Template):在客户端(Client)基础上以DH伺服夹爪为例，提供夹爪 RAPID 接口模板。
#### 注意：依照 [Modbus组织在7/09/2020的声明](https://www.modbus.org/news/modbus-organization-replaces-master-slave-with-client-server), **服务端**(Server) 替代了原 “从站(Slave)"; **客户端**(Client) 替代了原 "主站(Master)"。

## 技术要求  
- ABB Omnicore 机器人控制器  
- RobotWare 7.13 及更高版本
- 服务器(Server)功能依赖 RobotWare 选项 [3114-1 Multitasking]

## 快速开始
注意: 在安装 “Modbus TCP for OmniCore” 插件(Add-in)时会自动安装本 RAPID 程序库。以下指南仅适用于未安装 Modbus TCP 插件的系统。
### 准备工作
- 连接 RobotStudio 和目标 OmniCore 控制器, 用RobotStudio 的 “文件传输“（File Transfer）功能，将 `src\ModbusTCP` 文件夹复制到 `$HOME\ModbusTCP`
- 用 RobotStudio 的 “配置（Configuration）/ 加载参数（Load Parameters...）” 功能，加载 `src\config\modbusTCP_SIO.cfg`; 或者在 RobotStudio 手动设置防火墙 `Configuration/Communication/Firewall Manager/RapidSockets/Enable on Private Network`为 Yes。

### 服务端 Server
1. 检查 RobotWare 系统，确保已安装 [3114-1 Multitasking] 选项
2. 若客户端设备与虚拟控制器或真实控制器的服务端口连接，无需额外配置；若客户端设备与控制器的广域网端口 WAN 连接，需将 `ModbusServerMain.modx` 文件中 `MbServerConfig` 的 IP 地址修改为广域网 WAN IP
3. 用 RobotStudio 的 “配置（Configuration）/ 加载参数（Load Parameters...）” 功能，加载 `src\config\Server` 文件夹下的配置文件
4. 重启控制器（热启动）

### 客户端 Client
1. 用 RobotStudio 的 “配置（Configuration）/ 加载参数（Load Parameters...）” 功能，加载 `src\config\Client` 文件夹下的配置文件
2. 重启控制器（热启动）

### 夹爪模板 Gripper Template
1. 用 RobotStudio 的 “配置（Configuration）/ 加载参数（Load Parameters...）” 功能，加载 `src\config\Gripper` 文件夹下的配置文件
2. 重启控制器（热启动）
3. 通过 Modbus RTU 网关（如 ABB PoWa ASI）连接 DH 伺服夹爪，在 RAPID 应用程序里调用夹爪模板函数[`DHGripperHelper.sysx`](https://github.com/ABB-RARO/rapid-modbus-tcp/blob/main/src/ModbusTCP/gripper/DHGripperHelper.sysx) 

## 代码示例
### RAPID 代码示例
- [`\ModbusTCP\example\ModbusServerInterfaceUser.modx`](https://github.com/ABB-RARO/rapid-modbus-tcp/blob/main/src/ModbusTCP/example/ModbusServerInterfaceUser.modx) 在 `T_ROB1` 运动任务中调用服务端 Server 例程. 
- [`\ModbusTCP\example\ModbusClientUser.modx`](https://github.com/ABB-RARO/rapid-modbus-tcp/blob/main/src/ModbusTCP/example/ModbusTCPClientUser.modx) 在 `T_ROB1` 运动任务中调用客户端 Client 例程. 

### 西门子 PLC 示例 (PLC 作为 Modbus TCP 客户端)
- [SCL 代码示例](https://github.com/ABB-RARO/rapid-modbus-tcp/blob/main/SCL_example.md)
- [西门子文档](https://support.industry.siemens.com/cs/cn/zh/view/109782521)

## 网络安全
[Modbus TCP/IP](https://modbus.org/faq.php) 是一种缺乏内置网络安全机制的工业网络协议，请充分了解并评估 Modbus TCP/IP 的安全风险。  
在 ABB 机器人控制器上使用 rapid-modbus-tcp 时，请遵守[《操作手册 - OmniCore 集成工程师指南》](https://search.abb.com/library/Download.aspx?DocumentID=3HAC065037-010&LanguageCode=zh&DocumentPartId=&Action=Launch)中 “OmniCore 网络安全” 章节规定的要求。

如需报告 rapid-modbus-tcp 的安全漏洞，请查看安全策略 [Security Policy](https://github.com/ABB-RARO/rapid-modbus-tcp/blob/main/SECURITY.md) 

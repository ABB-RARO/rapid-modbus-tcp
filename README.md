# RAPID Modbus TCP  [切换中文版](https://github.com/ABB-RARO/rapid-modbus-tcp/blob/main/README_CN.md)

## Overview  
**RAPID Modbus TCP** is a simple Modbus/TCP client/server library for ABB robot controllers. It implemented [MODBUS Protocol Specification](https://modbus.org/docs/Modbus_Application_Protocol_V1_1b3.pdf) in ABB RAPID language. The library includes 3 parts, and they can be installed independently based on needs.
- **Server**: support Modbus function codes: FC01, FC03, FC05, FC06, FC0F, FC10, for reading and writing coils and hold registers. The server can communicate with only 1 client through Port 502 at any one time. Its communication period is 200ms.     
- **Client**: support Modbus function codes: FC01, FC02, FC03, FC04, FC05, FC06, FC0F, FC10 for reading and writing coils, discrete inputs, input registers, and holding registers.  
- **Gripper Template**: provide the RAPID interface for DH servo gripper on top of the client as the template.

## Technical Requirements  
- ABB Omnicore Controller  
- RobotWare 7.13 and later version
- RobotWare option [3114-1 Multitasking] required for the Server

## Quick Start
Note: This library will be auto deployed by installing "Modbus TCP for OmniCore" Add-in. Below guidence is only for the system without Modbus TCP Add-In.
###Preparation
- Connect the target OmniCore controller with RobotStudio, and copy `src\ModbusTCP` folder to `$HOME\ModbusTCP` by using `File Transfer` in RobotStudio
- Load `src\config\modbusTCP_SIO.cfg` by using `Configuration/Load Parameters...` or Set `Configuration/Communication/Firewall Manager/RapidSockets/Enable on Private Network` as `Yes` manually in RobotStudio.

### Server
1. Check the RobotWare system, make sure that **[3114-1 Multitasking]** Option is installed.
2. For connecting a client with the virtual controller or the Service port of the real controller, no additional configuration is needed.
   For connecting a client with the WAN port of the real controller, the IP address of `MbServerConfig` in `ModbusServerMain.modx` should be changed to the WAN IP.
3. Load the config files under the folder `src\config\Server` by using `Configuration/Load Parameters...` in RobotStudio
4. Restart controller(WarmStart)

### Client
1. Load the config file under the folder `src\config\Client` by using `Configuration/Load Parameters...` in RobotStudio
2. Restart controller(WarmStart)

### Gripper Template
1. Load the config file under the folder `src\config\Gripper` by using `Configuration/Load Parameters...` in RobotStudio
2. Restart controller(WarmStart)
3. Call gripper functions in [`DHGripperHelper.sysx`](https://github.com/ABB-RARO/rapid-modbus-tcp/blob/main/src/ModbusTCP/gripper/DHGripperHelper.sysx) when a DH servo gripper is connected through a Modbus RTU gateway (e.g. ABB PoWa ASI ).  

## Code Examples
### RAPID Code Example
- [`\ModbusTCP\example\ModbusServerInterfaceUser.modx`](https://github.com/ABB-RARO/rapid-modbus-tcp/blob/main/src/ModbusTCP/example/ModbusServerInterfaceUser.modx) is the RAPID example code calling server functions in `T_ROB1` motion task. 
- [`\ModbusTCP\example\ModbusClientUser.modx`](https://github.com/ABB-RARO/rapid-modbus-tcp/blob/main/src/ModbusTCP/example/ModbusTCPClientUser.modx) is the RAPID example code calling client functions in `T_ROB1` motion task. 

### Siemens PLC Example (PLC as the Modbus TCP Client)
- [SCL code example](https://github.com/ABB-RARO/rapid-modbus-tcp/blob/main/SCL_example.md)
- [Siemens document](https://support.industry.siemens.com/cs/cn/zh/view/109782521)

## Cyber Security
[Modbus TCP/IP](https://modbus.org/faq.php) is a industrial network protocol lack of built-in network security mechanisms. Please fully understand and evaluate the security risks of Modbus TCP/IP.  
When using rapid-modbus-tcp with ABB robot controller, please comply with the requirements outlined in the "OmniCore Cybersecurity" section of the ["Operation Manual - OmniCore Integration Engineer Guide"](https://search.abb.com/library/Download.aspx?DocumentID=3HAC065037-001&DocumentRevisionId=W&Action=Launch).

Please check the [Security Policy](https://github.com/ABB-RARO/rapid-modbus-tcp/blob/main/SECURITY.md) for reporting a security vulnerability of rapid-modbus-tcp. 
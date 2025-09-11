# RAPID Modbus TCP

## Overview  
**RAPID Modbus TCP** is a simple Modbus/TCP client/server library for ABB robot controllers. It implemented [MODBUS Protocol Specification](https://modbus.org/docs/Modbus_Application_Protocol_V1_1b3.pdf) in ABB RAPID language. The library includes 3 parts, and they can be installed independently based on needs.
- **Server**: support Modbus function codes: FC01, FC03, FC05, FC06, FC0F, FC10, for reading and writing coils and hold registers. 3114-1 Multitasking is required.     
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
3. Call gripper functions in `DHGripperHelper.sysx` when a DH servo gripper is connected through a Modbus RTU gateway (e.g. ABB PoWa ASI ).  

## Code Examples
### RAPID Code Example
- `\ModbusTCP\example\ModbusServerInterfaceUser.modx` is the RAPID example code calling server functions in `T_ROB1` motion task. 
- `\ModbusTCP\example\ModbusClientUser.modx` is the RAPID example code calling cilent functions in `T_ROB1` motion task. 

### Siemens PLC SCL Example (PLC as the Modbus TCP Client)
#### DB
   ```SCL
   VAR
    mode : USInt := 101;
    len : USInt := 1
    addr : USInt := 10
    IP: TCON_IP_v4 //IP.RomotePort:=502,IP.ConnectionType:=11,IP.ID:=1,IP.Interfaceid:=64,IP.ActiveEstablished:=TRUE
   END_VAR
   ``` 
#### FB
   ```SCL
   #MB_CLIENT_DB1(
               EN := TRUE,
               REQ := TRUE,
               DISCONNECT := FALSE,
               MB_MODE := "MODBUS_TCP".mode,
               MB_DATA_ADDR := "MODBUS_TCP".addr,
               MB_DATA_LEN := "MODBUS_TCP".len,
               MB_DATA_PTR := #PTR,
               CONNECT := #Connect,
               DONE => #MB_STATUS1.DONE,
               BUSY => #MB_STATUS1.BUSY,
               ERROR => #MB_STATUS1.ERROR,
               STATUS => #MB_STATUS1.STATUS);
   IF #MB_STATUS1.DONE THEN
    "flag" := "flag" + 1;
   END_IF;
   ``` 
#### MAIN
   ```SCL
     #IP := "MODBUS_TCP".IP;
     IF "flag" = 0 THEN
      //FUNC01_15To50_rCoils
      "MODBUS_TCP".mode := 101;
      "MODBUS_TCP".addr := 15;
      "MODBUS_TCP".len := 36;
      "RB_Modbus_TCP_DB"(Connect := #IP,PTR := "IO".RB_TO_PLC);
     ELSIF "flag" = 1 THEN
      //FUNC03_0To50_rRegs
      "MODBUS_TCP".mode := 103;
      "MODBUS_TCP".addr := 0;
      "MODBUS_TCP".len := 51;
      "RB_Modbus_TCP_DB"(Connect := #IP,PTR := "IO".RB_TO_PLC_Register);
      #tempDWord := SHL(IN := WORD_TO_DWORD("IO".RB_TO_PLC_Register[38]), N := 16) OR WORD_TO_DWORD("IO".RB_TO_PLC_Register[39]);
      #value := DWORD_TO_REAL(#tempDWord);
     ELSIF "flag" = 2 THEN
      //FUNC15_110To120_wCoils
      "MODBUS_TCP".mode := 115;
      "MODBUS_TCP".addr := 110;
      "MODBUS_TCP".len := 11;
      "RB_Modbus_TCP_DB"(Connect := #IP, PTR := "IO".PLC_TO_RB);
     ELSIF "flag" = 3 THEN
      //FUNC16_200To210_wRegs
      "MODBUS_TCP".mode := 116;
      "MODBUS_TCP".addr := 200;
      "MODBUS_TCP".len := 11;
      "RB_Modbus_TCP_DB"(Connect := #IP,PTR := "IO".PLC_TO_RB_Register);
     ELSIF "flag" = 4 THEN
      //FUNC05_100_wCoil
      "MODBUS_TCP".mode := 105;
      "MODBUS_TCP".addr := 100;
      "MODBUS_TCP".len := 1;
      "RB_Modbus_TCP_DB"(Connect := #IP,PTR := "IO".PLC_TO_RB);
     ELSIF "flag" = 5 THEN
      //FUNC06_80_wReg
      "MODBUS_TCP".mode := 106;
      "MODBUS_TCP".addr := 80;
      "MODBUS_TCP".len := 1;
      "RB_Modbus_TCP_DB"(Connect := #IP,PTR := "IO"."80_Reg_Value");
     ELSE
      "flag" := 0;
     END_IF;
   ``` 

## Cyber Security
[Modbus TCP/IP](https://modbus.org/faq.php) is a industrial network protocol lack of built-in network security mechanisms. Please fully understand and evaluate the security risks of Modbus TCP/IP.  
When using rapid-modbus-tcp with ABB robot controller, please comply with the requirements outlined in the "OmniCore Cybersecurity" section of the ["Operation Manual - OmniCore Integration Engineer Guide"](https://search.abb.com/library/Download.aspx?DocumentID=3HAC065037-001&DocumentRevisionId=W&Action=Launch).

Please check the [Security Policy](https://github.com/ABB-RARO/rapid-modbus-tcp/blob/main/SECURITY.md) for reporting a security vulnerability of rapid-modbus-tcp. 

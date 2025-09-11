## Siemens PLC as Modbus TCP Client
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

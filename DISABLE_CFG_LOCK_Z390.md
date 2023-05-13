### 1 In config.plist file
Disable AppleCpuPmCfgLock and AppleXcpmCfgLock in Kernel -> Quriks:
### 2. Reboot and select modGRUBShell in boot list
### 3. Shell prompt appears, type in followings
| Firmware version  | Command  |
|---|---|
|11 & 12c  | setup_var 0x5C1 0x00 |

### 4. Type `reboot`
# CC1352R1 BLE Simple Peripheral

This project demonstrates how to configure or modify the BLE advertising name and how to read the BLE MAC address on the CC1352R1 using TI-RTOS.

---

## 🔧 Features

- Dynamically reads the BLE MAC address from hardware registers.
- Sets a unique BLE advertising name based on the last two bytes of the MAC address.
- Useful for identifying multiple devices uniquely during scanning.

---

## 📁 Project Purpose

This folder is intended to:

- Modify the default BLE advertising name.
- Retrieve the BLE MAC address programmatically.
- Advertise with a unique name in the format:  
  **`WAMR<LastTwoBytesOfMAC>`**  
  (Example: `WAMRF1A2` if the last two bytes are `0xF1` and `0xA2`)

---

## 🔄 Modified Code Snippet

Below is the modified section that reads the MAC address and sets the BLE name dynamically:

```c
uint32_t addressLow  = HWREG(FCFG1_BASE + FCFG1_O_MAC_BLE_0);
uint32_t addressHigh = HWREG(FCFG1_BASE + FCFG1_O_MAC_BLE_1);

ieeeAddress[0] = (addressLow >>  0) & 0xFF;
ieeeAddress[1] = (addressLow >>  8) & 0xFF;
ieeeAddress[2] = (addressLow >> 16) & 0xFF;
ieeeAddress[3] = (addressLow >> 24) & 0xFF;
ieeeAddress[4] = (addressHigh >> 0) & 0xFF;
ieeeAddress[5] = (addressHigh >> 8) & 0xFF;

char asciiHex[4];
asciiHex[0] = (ieeeAddress[0] >> 4) < 10 ? '0' + (ieeeAddress[0] >> 4) : 'A' + (ieeeAddress[0] >> 4) - 10;
asciiHex[1] = (ieeeAddress[0] & 0x0F) < 10 ? '0' + (ieeeAddress[0] & 0x0F) : 'A' + (ieeeAddress[0] & 0x0F) - 10;

asciiHex[2] = (ieeeAddress[1] >> 4) < 10 ? '0' + (ieeeAddress[1] >> 4) : 'A' + (ieeeAddress[1] >> 4) - 10;
asciiHex[3] = (ieeeAddress[1] & 0x0F) < 10 ? '0' + (ieeeAddress[1] & 0x0F) : 'A' + (ieeeAddress[1] & 0x0F) - 10;

const char fullName[] = {
    0x09, GAP_ADTYPE_LOCAL_NAME_COMPLETE,
    'W', 'A', 'M', 'R',
    asciiHex[2], asciiHex[3],
    asciiHex[0], asciiHex[1]
};
memcpy(name, fullName, sizeof(fullName));

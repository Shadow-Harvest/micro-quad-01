# Micro Quad Setup & Configuration Guide

**Last Updated:** 2025-11-15

---

## 📦 Hardware Build

### Flight Controller
- **DarwinFPV Darwin ELRS2.0 F411 15A Bluejay AIO**
  - F411 MCU processor
  - 15A ESC (Bluejay firmware)
  - Built-in SPI ExpressLRS 2.0 receiver (2.4GHz)

### FPV System
- **VTX:** Zeus 350mW
- **Camera:** CADDX ANT Nano (1200TVL, Global WDR, OSD, 1.8mm lens, 16:9)

### Frame & Motors
- **Frame:** Mobula7 V4 Moblite7
- **Motors:** 14mm 15000KV Brushless (1S-2S, 1.5mm shaft)

### Control System
- **Radio:** FrSky Taranis Q X7 ACCESS (OpenTX 2.3.14)
- **TX Module:** HappyModel ES24TX Pro (2.4GHz ELRS, 1000mW)

---

## 🔐 ExpressLRS Binding Configuration

### Critical Settings (MUST MATCH on TX and RX!)
```
Binding Phrase: Darwinfpv
UID: 205,32,166,91,251,234
Packet Rate: 250Hz (rate_index = 0)
Domain: ISM2400
Protocol: CRSF
```

---

## 🎮 Taranis Q X7 Setup

### Model Setup (long press PAGE)
```
Internal RF: OFF                    # Prevents interference
External RF:
  ├─ Mode: CRSF                    # ExpressLRS protocol
  └─ Ch. Range: CH1-16             # Full channel range
```

### System Info
- Firmware: OpenTX 2.3.14-otx (opentx-x7access)
- SD Card: Not installed (Lua scripts unavailable)
- Baud Rate: Auto-configured (400K)

---

## ✈️ Betaflight Configuration

### Receiver Tab
```
Receiver Mode: SPI RX support
SPI Bus Receiver Provider: EXPRESSLRS
Binding Phrase: Darwinfpv          # Set here in Betaflight
Telemetry: ✓ Enabled
Channel Map: AETR
```

### Channel Mapping (AETR)
- CH1 = Roll (Right stick ←→)
- CH2 = Pitch (Right stick ↑↓)
- CH3 = Throttle (Left stick ↑↓)
- CH4 = Yaw (Left stick ←→)
- CH5+ = Switches (Arm, modes)

### CLI Settings
```bash
# View all ELRS settings
get expresslrs

# Current configuration
expresslrs_uid = 205,32,166,91,251,234
expresslrs_domain = ISM2400
expresslrs_rate_index = 0              # 250Hz - CRITICAL!
expresslrs_switch_mode = HYBRID
expresslrs_model_id = 255

# Change packet rate (if needed)
set expresslrs_rate_index = 0          # 500Hz
set expresslrs_rate_index = 1          # 250Hz ✓ CURRENT
set expresslrs_rate_index = 2          # 150Hz
set expresslrs_rate_index = 3          # 50Hz
save

# Backup all settings
dump all
```

---

## 📡 TX Module WiFi Access

### Accessing Without SD Card
1. Remove ES24TX Pro from radio
2. Power externally (USB or 2S battery)
3. Wait 60 seconds → LED shows green breathing pattern
4. Connect to WiFi:
   - **Network:** `ExpressLRS TX`
   - **Password:** `expresslrs`
5. Browser: **http://10.0.0.1**

### Module Settings
- Packet Rate: 250Hz (must match RX!)
- TX Power: 100mW recommended (max 1000mW)
- Telemetry Ratio: 1:128 or 1:64

---

## 🆘 Troubleshooting

### Problem: No Binding (RSSI = 0)
**Solution:**
1. Check packet rate match:
   ```bash
   # In Betaflight CLI
   get expresslrs_rate_index    # Should be 0 (for 250Hz)
   ```
2. Verify binding phrase identical on TX and RX: `Darwinfpv`
3. Confirm Taranis External RF = CRSF
4. Power sequence: Taranis ON → wait 5s → FC ON

### Problem: Wrong Stick Mapping
**Solution:**
- Betaflight → Receiver tab → Channel Map: **AETR**
- Test each stick moves correct channel

### Problem: Can't Access TX Module WiFi
**Solution:**
- Module has no physical button
- Must use auto WiFi mode (60 second wait)
- LED will show green breathing when ready

---

## 🚀 Power-On Sequence

```
1. Turn ON Taranis Q X7
2. Wait 5 seconds (module initializes)
3. Connect quad battery (or USB)
4. Check RSSI in Betaflight (should be >0)
```

---

## ✅ Pre-Flight Checklist

- [ ] Binding working (RSSI >0 in Betaflight)
- [ ] All sticks respond correctly
- [ ] Arm switch configured and tested
- [ ] Battery voltage showing correctly
- [ ] VTX configured and transmitting
- [ ] Props installed correct direction
- [ ] Failsafe tested (turn off radio, verify failsafe triggers)

---

## 💡 Key Learnings from Setup

1. **Packet rate MUST match** - TX at 250Hz, RX at 150Hz = no binding
   - Solution: Set both to `rate_index = 0` (250Hz)

2. **SPI receiver configuration** - No serial RX needed, configured through Betaflight
   - Receiver tab: SPI RX support + EXPRESSLRS provider
   - Binding phrase set in Betaflight field

3. **WiFi access without SD card** - Auto WiFi mode works
   - External power + 60 second wait
   - Module has independent flash storage

4. **Channel mapping** - Betaflight default may not match radio
   - Set Channel Map to AETR for Taranis

5. **Q X7 without SD card limitations** - No Lua scripts available
   - Can't adjust TX module settings easily from radio
   - Must use WiFi method to change power/rates

---

## 🔧 Next Steps

- [ ] Add SD card to Taranis (enables Lua scripts, telemetry)
- [ ] Configure VTX band/channel
- [ ] Set up arm switch and flight modes
- [ ] Configure battery warnings
- [ ] PID tuning for 1S/2S flight
- [ ] Motor direction verification
- [ ] Failsafe configuration
- [ ] Range test

---

## 📌 Quick Reference Commands

### Betaflight CLI
```bash
get expresslrs                     # View all ELRS settings
set expresslrs_rate_index = 1      # Change packet rate
save                               # Save and reboot
dump all                           # Backup config
```

### TX Module WiFi
```
Network: ExpressLRS TX
Password: expresslrs
URL: http://10.0.0.1
```

### Emergency Restore
```bash
# In Betaflight CLI - restore binding
set expresslrs_uid = 205,32,166,91,251,234
set expresslrs_domain = ISM2400
set expresslrs_rate_index = 0
save
```

---

**Project Status:** ExpressLRS binding complete and working ✓

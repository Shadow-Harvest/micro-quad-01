# Micro Quad Setup & Configuration Guide

**Last Updated:** 2025-11-15
**Status:** ExpressLRS binding complete and working ✓

---

## 📦 Hardware Build

### Flight Controller
**DarwinFPV Darwin ELRS2.0 F411 15A Bluejay AIO**
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

## 🎮 Radio Configuration

### Taranis Q X7 Model Setup
Press and hold **PAGE** to access Model Setup:

```
Internal RF: OFF                    # Prevents interference
External RF:
  ├─ Mode: CRSF                    # ExpressLRS protocol
  └─ Ch. Range: CH1-16             # Full channel range
```

> **Note:** No SD card installed - Lua scripts unavailable. TX module settings must be changed via WiFi.

---

## 🔐 ExpressLRS Configuration

### Critical Settings (MUST MATCH on TX and RX!)
```
Binding Phrase: Darwinfpv
UID: 205,32,166,91,251,234
Packet Rate: 250Hz (rate_index = 0)
Domain: ISM2400
Protocol: CRSF
```

### TX Module WiFi Access (No SD Card Method)
1. Turn on radio with module installed
2. Wait 60 seconds → LED shows green breathing pattern
3. Connect to WiFi: **ExpressLRS TX** / `expresslrs`
4. Browser: **http://10.0.0.1**

**Recommended Settings:**
- Packet Rate: 250Hz (must match RX!)
- TX Power: 100mW (max 1000mW)
- Telemetry Ratio: 1:128 or 1:64

---

## ✈️ Betaflight Configuration

### Receiver Tab (GUI)
```
Receiver Mode: SPI RX support
SPI Bus Receiver Provider: EXPRESSLRS
Binding Phrase: Darwinfpv          # Must match TX module
Telemetry: ✓ Enabled
```

### Channel Mapping (CLI Required)
The GUI doesn't offer the ATER1234 mapping needed for correct stick input rendering.

**Set via CLI:**
```bash
map ATER1234
save
```

**Channel Order:**
- CH1 = Aileron/Roll (Right stick ←→)
- CH2 = Throttle (Left stick ↑↓)
- CH3 = Elevator/Pitch (Right stick ↑↓)
- CH4 = Rudder/Yaw (Left stick ←→)
- CH5+ = Switches (Arm, modes)

### Essential CLI Commands
```bash
# View current ELRS settings
get expresslrs

# Verify configuration
expresslrs_uid = 205,32,166,91,251,234
expresslrs_domain = ISM2400
expresslrs_rate_index = 0              # 0=500Hz, 1=250Hz, 2=150Hz, 3=50Hz
expresslrs_switch_mode = HYBRID

# Backup configuration
dump all
```

---

## 🚀 Power-On Sequence

```
1. Turn ON Taranis Q X7
2. Wait 5 seconds (TX module initializes)
3. Connect quad battery (or USB)
4. Verify RSSI > 0 in Betaflight Receiver tab
```

---

## 🆘 Troubleshooting

### No Binding (RSSI = 0)
1. **Check packet rate match:**
   ```bash
   get expresslrs_rate_index    # Should be 0 (250Hz)
   ```
2. **Verify binding phrase:** `Darwinfpv` (case-sensitive, identical on TX/RX)
3. **Confirm Taranis settings:** External RF = CRSF
4. **Follow power sequence:** TX on first, wait 5s, then FC

### Wrong Stick Inputs
```bash
map ATER1234
save
```
Verify each stick moves the correct channel in Betaflight Receiver tab.

### TX Module WiFi Won't Appear
- Module has no physical button - auto WiFi mode only
- Requires 60 second wait with external power
- Green breathing LED indicates WiFi ready

---

## ✅ Pre-Flight Checklist

- [ ] RSSI > 0 in Betaflight
- [ ] All 4 sticks respond correctly
- [ ] Arm switch configured and tested
- [ ] Battery voltage displaying
- [ ] VTX configured and transmitting
- [ ] Props installed in correct direction
- [ ] Failsafe tested (turn off radio)

---

## 🔧 Next Steps

- [ ] Add SD card to Taranis (enables Lua scripts)
- [ ] Configure VTX band/channel
- [ ] Set up arm switch and flight modes
- [ ] Configure battery warnings
- [ ] PID tuning for 1S/2S flight
- [ ] Motor direction verification
- [ ] Failsafe configuration
- [ ] Range test

---

## 📌 Quick Reference

### Common CLI Commands
```bash
map ATER1234                       # Set channel mapping
get expresslrs                     # View ELRS settings
set expresslrs_rate_index = 1      # Change packet rate
save                               # Save and reboot
dump all                           # Backup config
```

### Emergency Restore (CLI)
```bash
set expresslrs_uid = 205,32,166,91,251,234
set expresslrs_domain = ISM2400
set expresslrs_rate_index = 0
map ATER1234
save
```

### TX Module Access
- **WiFi:** ExpressLRS TX / `expresslrs`
- **URL:** http://10.0.0.1
- **Power:** External only, 60s wait

---

## 💡 Key Learnings

1. **SPI receiver = no serial RX needed** - Everything configured through Betaflight GUI + CLI
2. **Packet rate mismatch = no binding** - Both TX and RX must be set to same rate_index
3. **ATER1234 mapping required** - GUI doesn't offer it, must use CLI `map` command
4. **ELRS TX module WiFi access works without SD card** - External power + 60s wait activates auto WiFi mode
5. **Always `save` in CLI in Betaflight** - Changes don't persist until saved

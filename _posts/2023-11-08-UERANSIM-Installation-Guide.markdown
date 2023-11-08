---
layout: "post"
---

## UERANSIM Installation
***Prerequisites*** 
> * The machine where UERANSIM is to be installed needs one interface to be in the same subnet as that of `eth1` of AGW
> * Assuming `eth1` IP of AGW to be `192.168.75.40` and an interface, `enp0s3` on UERANSIM machine, with IP `192.168.75.50`

### 1. Download UERANSIM
```bash
cd ~
git clone https://github.com/aligungr/UERANSIM
cd UERANSIM
```
### 2. Update and upgrade UERANSIM machine
```bash
sudo apt update
sudo apt upgrade
```
### 3. Install required tools:
```bash
sudo apt install make
sudo apt install g++
sudo apt install libsctp-dev lksctp-tools
sudo apt install iproute2
sudo snap install cmake --classic
```
### 4. Build UERANSIM
```bash
cd ~/UERANSIM
make
```
### 5. Configuration
> Modify the following flags in `UERANSIM/configs/open5gs-gnb.yaml` as needed
  * `mcc`, `mnc` and `tac`.
  * `linkIp`, `ngapIp`, `gtpIp` to be the IP of `enp0s3` .
  * In `amfConfigs` section change the `address` parameter to the IP of `eth1`.

```bash
UERANSIM$ cat config/open5gs-gnb.yaml
....
mcc: '901'          # Mobile Country Code value
mnc: '70'           # Mobile Network Code value (2 or 3 digits)

nci: '0x000000010'  # NR Cell Identity (36-bit)
idLength: 32        # NR gNB ID length in bits [22...32]
tac: 1              # Tracking Area Code

linkIp: 192.168.75.50 # gNB's local IP address for Radio Link Simulation (Usually same with local IP)
ngapIp: 192.168.75.50  # gNB's local IP address for N2 Interface (Usually same with local IP)
gtpIp: 192.168.75.50   # gNB's local IP address for N3 Interface (Usually same with local IP)

# List of AMF address information
amfConfigs:
  - address: 192.168.75.40
    port: 38412

# List of supported S-NSSAIs by this gNB
slices:
  - sst: 1

# Indicates whether or not SCTP stream number errors should be ignored.
ignoreStreamIds: true
```

> Modify the following flags in `UERANSIM/configs/open5gs-ue.yaml` as needed
  *  `supi`, `mcc` and `mnc`
  * `key` and `opc`
  * change `gnbSearchList` to the `enp0s3` IP of `UERANSIM` machine.
  * In `sessions` section change the `apn` parameter to the APN configured in AGW.

```bash
UERANSIM$ cat config/open5gs-ue.yaml
# IMSI number of the UE. IMSI = [MCC|MNC|MSISDN] (In total 15 digits)
supi: 'imsi-901700000000001'
# Mobile Country Code value of HPLMN
mcc: '901'
# Mobile Network Code value of HPLMN (2 or 3 digits)
mnc: '70'

# Permanent subscription key
key: '465B5CE8B199B49FAA5F0A2EE238A6BC'
# Operator code (OP or OPC) of the UE
op: 'E8ED289DEBA952E4283B54E88E6183CA'
# This value specifies the OP type and it can be either 'OP' or 'OPC'
opType: 'OPC'
# Authentication Management Field (AMF) value
amf: '8000'
# IMEI number of the device. It is used if no SUPI is provided
imei: '356938035643803'
# IMEISV number of the device. It is used if no SUPI and IMEI is provided
imeiSv: '4370816125816151'

# List of gNB IP addresses for Radio Link Simulation
gnbSearchList:
  - 192.168.75.50

# UAC Access Identities Configuration
uacAic:
  mps: false
  mcs: false

# UAC Access Control Class
uacAcc:
  normalClass: 0
  class11: false
  class12: false
  class13: false
  class14: false
  class15: false

# Initial PDU sessions to be established
sessions:
  - type: 'IPv4'
    apn: 'internet'
    slice:
      sst: 1
# Configured NSSAI for this UE by HPLMN
configured-nssai:
  - sst: 1

# Default Configured NSSAI for this UE
default-nssai:
  - sst: 1
    sd: 1

# Supported integrity algorithms by this UE
integrity:
  IA1: true
  IA2: true
  IA3: true

# Supported encryption algorithms by this UE
ciphering:
  EA1: true
  EA2: true
  EA3: true

# Integrity protection maximum data rate for user plane
integrityMaxRate:
  uplink: 'full'
  downlink: 'full'
```

### 6. Run the application
> Terminal 1
```bash
sudo build/nr-gnb -c config/open5gs-gnb.yaml
```
> Terminal 2
```bash
sudo build/nr-ue -c config/open5gs-ue.yaml
```

# YouTube Kids Network Traffic Analysis — Nigeria vs. United States

## Overview

This repository contains the raw network packet capture files and extracted 
analysis outputs from a controlled study comparing the default data collection 
behaviour of the YouTube Kids application across two regulatory jurisdictions: 
**Nigeria** and the **United States**.

The captures were collected as part of a research project examining whether 
the YouTube Kids app applies different data collection defaults depending on 
the user's geographic location — and whether observed differences correlate 
with the regulatory requirements of the US Children's Online Privacy 
Protection Act (COPPA) and Nigeria's Data Protection Act (NDPA) of 2023.

The full findings are to be published in the companion articles:
- A Comparative Analysis of Child Data Protection Frameworks under COPPA and the Nigeria Data Protection Act
- An Empirical Comparative Network Traffic Analysis of YouTube Kids in Nigeria and the United States Examining Differential Data Collection Practices

## Repository Contents

| File | Description |
|------|-------------|
| `capture_NG.pcapng` | 15-minute packet capture — Nigerian network environment (VPN off) |
| `capture_US.pcapng` | 15-minute packet capture — US network environment (ProtonVPN, US server) |
| `analysis_NG.txt` | tshark-extracted DNS queries and TLS SNI fields — Nigeria session |
| `analysis_US.txt` | tshark-extracted DNS queries and TLS SNI fields — US session |
| `Full Data Export | unfiltered data exported for the data capturing processs|
| `Methodology| document explainging the capturing's step by step process|
| `Screenshots | snapshots of the different stages of the experiment|

---

## Methodology Summary

All captures were collected on a single Android Virtual Device (AVD) 
running Android 10 (x86_64) using `tcpdump` deployed via `adb`, ensuring 
a controlled and reproducible environment. Each session ran for 15 minutes
of standard user interaction (browsing, video selection, and playback).

**Nigeria session:** Host machine VPN disabled; traffic resolved through 
default ISP routing, presenting the app with a Nigerian network origin.

**US session:** Host machine connected to ProtonVPN (US server); traffic 
routed through a US endpoint, presenting the app with a US network origin.

The `.pcapng` files were analysed using **Wireshark** and **tshark**. 
DNS query records and TLS Server Name Indication (SNI) fields — which 
remain unencrypted regardless of application-layer SSL — were extracted 
and compared to identify differences in the servers contacted by each 
regional version of the app.

> **Note:** Direct HTTPS decryption was attempted in earlier phases using 
> Frida and Burp Suite but was defeated by the application's native-level 
> SSL pinning, Frida detection, and anti-decompilation protections. 
> The metadata-only methodology described above was the approach that 
> ultimately succeeded.

---

## Key Finding

The Nigerian session contacted **five advertising and tracking domains** 
that were entirely absent from the US session:

- `ad.doubleclick.net`
- `adservice.google.com`
- `www.googleadservices.com`
- `googleads.g.doubleclick.net`
- `www.googletagmanager.com`

Additionally, the Nigerian session generated **~48% more network packets**, 
transmitted **~51% more data**, and communicated with **~43% more unique 
external IP addresses** than the US session (after filtering VPN overhead 
from the US capture).

---

## How to Reproduce

### Requirements
- Wireshark / tshark (v3.0+)
- Android Studio with AVD (Android 10, x86_64, Google APIs image)
- adb (Android Debug Bridge)
- tcpdump binary compiled for x86_64 Android

### Reproducing the DNS/SNI Extraction
```bash
# Extract DNS queries and TLS SNI fields from a capture file
tshark -r capture_NG.pcapng \
  -T fields \
  -e frame.time \
  -e ip.src \
  -e ip.dst \
  -e dns.qry.name \
  -e tls.handshake.extensions_server_name \
  -Y "dns or tls.handshake.type == 1" \
  > analysis_NG.txt```

Repeat substituting `capture_US.pcapng` and `analysis_US.txt` for the 
US session.
---

## Ethical Considerations

All captures were conducted on a controlled virtual device with no real 
child users involved. No personally identifiable information was collected 
during the experiment. The captures contain only network metadata 
(DNS queries, TLS server names, IP addresses, and packet headers). 
The VPN used in the US session (ProtonVPN) does not log user traffic.

---
## Citation

If you use these datasets in your own research, please cite the companion 
articles:

> Abdulrahman Tunde Alabelewe, Samson Adeyinka, Muhammad Alameen Yushau (2026). *An Empirical Comparative Network Traffic Analysis of YouTube Kids in Nigeria and the United States Examining Differential Data Collection Practices*. [Journal Name to be provided once article has been publised].

> Abdulrahman Tunde Alabelewe (2026). *A Comparative Analysis of Child Data Protection Frameworks under COPPA and the Nigeria Data Protection Act*. [Journal Name be provided once article has been publised].

---

## Licence
 You are free to share and adapt the material for any purpose, 
provided appropriate credit is given.# youtube-kids-network-analysis
Network traffic captures comparing YouTube Kids data collection behaviour in Nigeria vs. the United States — supporting empirical research on COPPA/NDPA regulatory compliance.

Click here to download
https://drive.google.com/file/d/1jn0FxBoHXK61r5IAdsaQyiuVx8HrhVsP/view?usp=sharing

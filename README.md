# RainbowCrack teaching materials

RainbowCrack uses precomputed rainbow tables to recover plaintext passwords from hashes. This repository contains Linux and Windows packages for classroom exercises with synthetic passwords and hashes.

## Attribution

RainbowCrack is copyright 2020 RainbowCrack Project. Original project: <https://project-rainbowcrack.com/>. Bundled third-party software retains its original notices and terms; this repository does not grant a new license for those binaries. The Windows archive includes `readme.txt`, and the Linux package includes `/usr/share/doc/rainbowcrack/copyright`.

## Packages

| Platform | Version | Download |
| --- | --- | --- |
| Linux x86-64 (Kali package) | 1.8-0kali3 | [Debian package](https://raw.githubusercontent.com/kaledaljebur/RainbowCrack/main/packages/linux/rainbowcrack_1.8-0kali3_amd64.deb) |
| Linux x86-64 | 1.8 | [ZIP archive](https://raw.githubusercontent.com/kaledaljebur/RainbowCrack/main/packages/linux/rainbowcrack-1.8-linux64.zip) |
| Windows x64 | 1.8 | [ZIP archive](https://raw.githubusercontent.com/kaledaljebur/RainbowCrack/main/packages/windows/rainbowcrack-1.8-win64.zip) |

All packages use the CPU. The Linux ZIP contains unchanged binaries extracted from the Debian package.

## 1. Linux setup

### A. ZIP option

Download and extract the ZIP into your exercise folder:

```bash
cd Desktop
wget https://raw.githubusercontent.com/kaledaljebur/RainbowCrack/main/packages/linux/rainbowcrack-1.8-linux64.zip
unzip rainbowcrack-1.8-linux64.zip
cd rainbowcrack-1.8-linux64
./rtgen -h
./rcrack -h
```

Keep `charset.txt` and `alglib0.so` beside the executables. This x86-64 build requires glibc, `libstdc++6`, and `libgcc-s1`. If you get an execution permission error, run `chmod u+x rtgen rtsort rcrack rt2rtc rtc2rt rtmerge`.

### B. Debian package option

On an x86-64 Kali Linux system, open a terminal and download the package into an exercise folder:

```bash
mkdir -p ~/rainbowcrack-lab
cd ~/rainbowcrack-lab
wget https://raw.githubusercontent.com/kaledaljebur/RainbowCrack/main/packages/linux/rainbowcrack_1.8-0kali3_amd64.deb -O rainbowcrack_1.8-0kali3_amd64.deb
sudo apt install ./rainbowcrack_1.8-0kali3_amd64.deb
cp -r /usr/share/rainbowcrack ./rainbowcrack-1.8-linux64
chmod -R u+w ./rainbowcrack-1.8-linux64
cd rainbowcrack-1.8-linux64
./rtgen -h
./rcrack -h
```

Use this local copy for the exercise so generated tables are written to your own folder.

## 2. Windows setup

Download the [Windows ZIP](https://raw.githubusercontent.com/kaledaljebur/RainbowCrack/main/packages/windows/rainbowcrack-1.8-win64.zip), then use **Extract All**. Open PowerShell in the extracted `rainbowcrack-1.8-win64` folder, then run:

```powershell
.\rtgen.exe -h
.\rcrack.exe -h
```

Keep the DLL and configuration files beside the executables. The archive also includes `rcrack_gui.exe`.

## Scenario: Recover passwords using rainbow tables

You have been given 15 MD5 hashes from password dataset [hashes.txt](hashes.txt). Each password contains exactly five characters using lowercase letters (`a-z`) and digits (`0-9`). Generate a rainbow table, sort it, and use it to recover the passwords.

### Step 1: Get the tools

Follow the Linux or Windows setup instructions above to download the package directly.

On Linux, use `~/Desktop/rainbowcrack-1.8-linux64` as your working folder. On Windows, use the extracted folder containing `rtgen.exe`, keeping its DLL and configuration files together. Keep your generated table and `hashes.txt` in the same working folder.

### Step 2: Generate a rainbow table

Use `rtgen` with the following parameters. Work out the command using `./rtgen -h` on Linux or `.\rtgen.exe -h` on Windows, and inspect `charset.txt` to identify the matching character set (it is in your working folder).

| Parameter | Value |
| --- | --- |
| Hash algorithm | MD5 |
| Minimum password length | 5 |
| Maximum password length | 5 |
| Character set | Lowercase letters and digits |
| Table index | 0 |
| Chain length | 3800 |
| Number of chains | 600000 |
| Part index | 0 |

Wait for generation to finish before continuing. Record how long it takes and the size of the generated `.rt` file.

Sample answer:
```sh
./rtgen md5 loweralpha-numeric 5 5 0 3800 600000 0
```

Sample output:
```sh
kaled@suricata-lab:~/Desktop/rainbowcrack-1.8-linux64$ ./rtgen md5 loweralpha-numeric 5 5 0 3800 600000 0
rainbow table md5_loweralpha-numeric#5-5_0_3800x600000_0.rt parameters
hash algorithm:         md5
hash length:            16
charset name:           loweralpha-numeric
charset data:           abcdefghijklmnopqrstuvwxyz0123456789
charset data in hex:    61 62 63 64 65 66 67 68 69 6a 6b 6c 6d 6e 6f 70 71 72 73 74 75 76 77 78 79 7a 30 31 32 33 34 35 36 37 38 39 
charset length:         36
plaintext length range: 5 - 5
reduce offset:          0x00000000
plaintext total:        60466176

sequential starting point begin from 0 (0x0000000000000000)
generating...
131072 of 600000 rainbow chains generated (0 m 18.0 s)
262144 of 600000 rainbow chains generated (0 m 18.0 s)
393216 of 600000 rainbow chains generated (0 m 17.7 s)
524288 of 600000 rainbow chains generated (0 m 17.7 s)
600000 of 600000 rainbow chains generated (0 m 10.2 s)
```

### Step 3: Sort the table

Use `./rtsort` on Linux or `.\rtsort.exe` on Windows to sort your generated table so that `rcrack` can search it. Run the tool without arguments to inspect its usage, then work out the command for your table.

Sample answer:
```sh
./rtsort .
```

Sample output:
```sh
kaled@suricata-lab:~/Desktop/rainbowcrack-1.8-linux64$ ./rtsort .
./md5_loweralpha-numeric#5-5_0_3800x600000_0.rt:
4051771392 bytes memory available
loading data...
sorting data...
writing sorted data...
```

### Step 4: Download and recover the hashes

Download [hashes.txt](https://raw.githubusercontent.com/kaledaljebur/RainbowCrack/main/hashes.txt) into your working folder using the command for your platform below.

Linux:

```bash
wget 'https://raw.githubusercontent.com/kaledaljebur/RainbowCrack/main/hashes.txt' -O hashes.txt
cat hashes.txt
```

Windows PowerShell:

```powershell
Invoke-WebRequest -Uri 'https://raw.githubusercontent.com/kaledaljebur/RainbowCrack/main/hashes.txt' -OutFile hashes.txt
Get-Content .\hashes.txt
```
Sample Linux output:
```sh
kaled@suricata-lab:~/Desktop/rainbowcrack-1.8-linux64$ wget 'https://raw.githubusercontent.com/kaledaljebur/RainbowCrack/main/hashes.txt' -O hashes.txt
cat hashes.txt
--2026-09-30 23:09:37--  https://raw.githubusercontent.com/kaledaljebur/RainbowCrack/main/hashes.txt
Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 185.199.110.133, 185.199.108.133, 185.199.109.133, ...
Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|185.199.110.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 495 [text/plain]
Saving to: ‘hashes.txt’

hashes.txt                                     100%[====================================================================================================>]     495  --.-KB/s    in 0s      

2026-09-30 23:09:37 (32.9 MB/s) - ‘hashes.txt’ saved [495/495]

f14b8f9ad9e80ef465f3a30cb17fefb9
d7a62338920f044398e76cc2b730f4de
246e51351ad46101ffc9f4fa0bdb6460
62bf2bd8fa3074396f1b036f77a8c551
c1bc16442fbe7a4361a520c56bd3b86c
77607a6afa5ebfddc43897b43a7aa78d
5e8502d218e3c073fe1b269c73cadc3d
96cb8ac0d6f8200b66f6de036a7dc55e
4456032c163e74a681d69b2e8c1d1dd5
fbb4679ce2735ec0f7f8e16c038554b6
8324cabc23b5d21b9c326ee79d6c1807
d3f50fc0e25b248bef228b008ab3553e
77fe0e433463213929f8957c12ed16b0
87c5fa7c48d69ea95328f5a68679ea04
017bbe96c11b524296c837d5e2b2cb2f
```

Confirm that the file contains 15 hash values. Use `./rcrack -h` on Linux or `.\rcrack.exe -h` on Windows to find the option for loading a hash list. Work out the command to search your sorted table for the hashes in `hashes.txt`.

You can check the help options for `rcrack`:
```sh
./rcrack -help
```

Sample answer:
```sh
./rcrack . -l ./hashes.txt
```

Sample output:
```sh
kaled@suricata-lab:~/Desktop/rainbowcrack-1.8-linux64$ ./rcrack . -l ./hashes.txt
1 rainbow tables found
memory available: 2702783283 bytes
memory for rainbow chain traverse: 60800 bytes per hash, 912000 bytes for 15 hashes
memory for rainbow table buffer: 2 x 9600016 bytes
disk: ./md5_loweralpha-numeric#5-5_0_3800x600000_0.rt: 9600000 bytes read
disk: finished reading all files
plaintext of 4456032c163e74a681d69b2e8c1d1dd5 is amgle
plaintext of 77fe0e433463213929f8957c12ed16b0 is ama66
plaintext of d7a62338920f044398e76cc2b730f4de is reedo
plaintext of d3f50fc0e25b248bef228b008ab3553e is v8suc
plaintext of 87c5fa7c48d69ea95328f5a68679ea04 is ls5pe
plaintext of 5e8502d218e3c073fe1b269c73cadc3d is dnnau
plaintext of 62bf2bd8fa3074396f1b036f77a8c551 is herom
plaintext of 77607a6afa5ebfddc43897b43a7aa78d is evene
plaintext of c1bc16442fbe7a4361a520c56bd3b86c is stosa
plaintext of 246e51351ad46101ffc9f4fa0bdb6460 is orkan
plaintext of fbb4679ce2735ec0f7f8e16c038554b6 is lnpac
plaintext of f14b8f9ad9e80ef465f3a30cb17fefb9 is debat
plaintext of 96cb8ac0d6f8200b66f6de036a7dc55e is satai
plaintext of 017bbe96c11b524296c837d5e2b2cb2f is npic9
plaintext of 8324cabc23b5d21b9c326ee79d6c1807 is olau8

statistics
----------------------------------------------------------------
plaintext found:                             15 of 15
total time:                                  4.67 s
time of chain traverse:                      3.99 s
time of alarm check:                         0.66 s
time of disk read:                           0.00 s
hash & reduce calculation of chain traverse: 108243000
hash & reduce calculation of alarm check:    15633925
number of alarm:                             64020
performance of chain traverse:               27.11 million/s
performance of alarm check:                  23.51 million/s

result
----------------------------------------------------------------
f14b8f9ad9e80ef465f3a30cb17fefb9  debat  hex:6465626174
d7a62338920f044398e76cc2b730f4de  reedo  hex:726565646f
246e51351ad46101ffc9f4fa0bdb6460  orkan  hex:6f726b616e
62bf2bd8fa3074396f1b036f77a8c551  herom  hex:6865726f6d
c1bc16442fbe7a4361a520c56bd3b86c  stosa  hex:73746f7361
77607a6afa5ebfddc43897b43a7aa78d  evene  hex:6576656e65
5e8502d218e3c073fe1b269c73cadc3d  dnnau  hex:646e6e6175
96cb8ac0d6f8200b66f6de036a7dc55e  satai  hex:7361746169
4456032c163e74a681d69b2e8c1d1dd5  amgle  hex:616d676c65
fbb4679ce2735ec0f7f8e16c038554b6  lnpac  hex:6c6e706163
8324cabc23b5d21b9c326ee79d6c1807  olau8  hex:6f6c617538
d3f50fc0e25b248bef228b008ab3553e  v8suc  hex:7638737563
77fe0e433463213929f8957c12ed16b0  ama66  hex:616d613636
87c5fa7c48d69ea95328f5a68679ea04  ls5pe  hex:6c73357065
017bbe96c11b524296c837d5e2b2cb2f  npic9  hex:6e70696339
```


### Record your results

- What commands did you use to generate, sort, and search the table?
- What passwords did you recover? Match each recovered password to its hash and mark any unrecovered hashes.
- How long did table generation and password recovery take?
- Why can the same table be reused for other unsalted MD5 hashes with the same password character set and length?

A single rainbow table does not guarantee recovery of every password. If any hashes remain unrecovered, report them alongside your results.

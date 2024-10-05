# Wazuh Custom Decoder untuk OWASP Coraza WAF

Proyek ini bertujuan untuk membuat custom decoder pada Wazuh Server untuk OWASP Coraza WAF. Custom decoder ini akan membantu dalam menganalisis log yang dihasilkan oleh Coraza WAF dan memunculkan informasi yang lebih mudah dipahami di dashboard Wazuh.

## Persyaratan

1. **Wazuh Server** harus sudah terpasang dan dikonfigurasi dengan baik.
2. **Coraza WAF** harus menghasilkan log yang akan dideteksi oleh Wazuh.
3. **Akses Root** atau izin administratif pada server Wazuh untuk melakukan modifikasi pada file decoder.

## Langkah-langkah

### 1. Menambahkan Custom Decoder

Pertama-tama, buat file custom decoder untuk Coraza WAF di direktori `/var/ossec/etc/decoders/`.

```bash
nano /var/ossec/etc/decoders/coraza.xml
```

Isi file `coraza.xml` dengan kode berikut:

```xml
<decoder name="Coraza">
    <prematch>Coraza</prematch>
    <regex>[(\.+)][\.+][(\.+)][\.+]\.+:</regex>
    <order>timeandate,state</order>
</decoder>

<decoder name="Coraza:Child">
    <parent>Coraza</parent>
    <regex>[client \\"(\.+)\\"] Coraza: (\.+) \(phase (\.+)\)</regex>
    <order>Client_ip, status, phase</order>
</decoder>

<decoder name="Coraza:Child">
    <parent>Coraza</parent>
    <regex>[file \\"(\.+)\\"]</regex>
    <order>file</order>
</decoder>
```

### 2. Pengujian Decoder

Untuk menguji apakah decoder yang baru telah bekerja dengan baik, jalankan utilitas `wazuh-logtest`:

```bash
/var/ossec/bin/wazuh-logtest
```

Masukkan log contoh yang relevan dengan Coraza WAF untuk memverifikasi apakah decoder menangkap informasi yang diinginkan.

### 3. Restart Wazuh Manager

Setelah melakukan perubahan pada file decoder dan rule, Anda perlu merestart Wazuh Manager agar perubahan tersebut diterapkan dan Wazuh dapat memproses log yang sesuai.

```bash
systemctl restart wazuh-manager
```

Setelah restart, log yang dihasilkan oleh Coraza WAF akan diproses oleh Wazuh menggunakan custom decoder yang telah dibuat.

## Kesimpulan

Custom decoder ini membantu Wazuh untuk memproses log dari OWASP Coraza WAF dan menampilkan informasi yang lebih terstruktur di dashboard Wazuh. Pastikan Anda telah melakukan pengujian sebelum menerapkan di lingkungan produksi.

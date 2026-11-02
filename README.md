<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Portal Absensi Digital</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: { sans: ['Inter', 'sans-serif'] },
                    colors: {
                        brand: { 50: '#eff6ff', 500: '#3b82f6', 600: '#2563eb', 900: '#1e3a8a' }
                    }
                }
            }
        }
    </script>
    <style>
        .view-section { display: none; }
        .view-section.active { display: flex; animation: fadeIn 0.3s ease-in-out; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        
        /* Body Setup for Centering Layout */
        body { 
            display: flex; 
            align-items: center; 
            justify-content: center; 
            min-height: 100vh; 
            margin: 0;
        }

        /* Mobile app container constraints (9:16 Ratio) */
        .app-container { 
            width: 100vw; 
            height: 100vh; 
            background-color: #f8fafc; 
            box-shadow: 0 0 30px rgba(0,0,0,0.15); 
            position: relative; 
            overflow-x: hidden;
            overflow-y: auto;
        }

        /* Frame Emulator for Desktop (Perfect 9:16) */
        @media (min-width: 500px) {
            .app-container {
                height: 90vh;
                width: auto;
                aspect-ratio: 9 / 16;
                border-radius: 40px;
                border: 12px solid #0f172a; /* Bingkai HP hitam */
            }
        }

        /* Admin Container Setup */
        .admin-container { 
            width: 100%; 
            max-width: 1000px; 
            height: 100vh; 
            background-color: #f8fafc; 
            overflow-y: auto;
        }
        
        @media (min-width: 1000px) {
            .admin-container {
                height: 90vh;
                border-radius: 24px;
                box-shadow: 0 10px 40px rgba(0,0,0,0.1);
                border: 1px solid #e2e8f0;
            }
        }
        
        /* Custom scrollbar for tables */
        .custom-scrollbar::-webkit-scrollbar { height: 6px; }
        .custom-scrollbar::-webkit-scrollbar-track { background: #f1f5f9; }
        .custom-scrollbar::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 4px; }

        /* Face Scanner Animation */
        @keyframes scan {
            0% { top: 0%; opacity: 0; }
            10% { opacity: 1; }
            90% { opacity: 1; }
            100% { top: 100%; opacity: 0; }
        }
        .animate-scan { animation: scan 2.5s linear infinite; }
    </style>
</head>
<body class="bg-slate-200 text-slate-800 antialiased">

    <!-- ================= VIEW 1: MENU UTAMA ================= -->
    <div id="view-menu" class="view-section active flex-col app-container">
        <div class="flex-grow flex flex-col items-center justify-center p-6 space-y-8">
            <div class="text-center space-y-2">
                <div class="w-20 h-20 bg-brand-600 text-white rounded-2xl flex items-center justify-center text-4xl mx-auto shadow-lg mb-4">
                    <i class="fa-solid fa-graduation-cap"></i>
                </div>
                <h1 class="text-2xl font-bold text-slate-900">Portal Absensi</h1>
                <p class="text-sm text-slate-500">Sistem Presensi Kelas C - CATARSIS</p>
            </div>

            <div class="w-full space-y-4">
                <button onclick="navTo('courses')" class="w-full bg-brand-600 hover:bg-brand-700 text-white font-semibold py-4 rounded-xl shadow-md transition flex items-center justify-center gap-3 text-lg">
                    <i class="fa-solid fa-fingerprint text-xl"></i> Mulai Absen
                </button>
                <button onclick="navTo('admin-login')" class="w-full bg-slate-800 hover:bg-slate-900 text-white font-semibold py-4 rounded-xl shadow-md transition flex items-center justify-center gap-3 text-lg">
                    <i class="fa-solid fa-user-shield text-brand-400 text-xl"></i> Akses Korti
                </button>
                <button onclick="laporKendalaWA()" class="w-full bg-white border-2 border-slate-200 hover:border-slate-300 text-rose-600 font-semibold py-4 rounded-xl transition flex items-center justify-center gap-3 text-lg">
                    <i class="fa-brands fa-whatsapp text-emerald-500 text-2xl"></i> Lapor Kendala
                </button>
            </div>
        </div>
        <div class="p-6 text-center text-xs text-slate-400">
            &copy; 2026 Portal Absensi Kelas C
        </div>
    </div>

    <!-- ================= VIEW 3: PILIH MATA KULIAH ================= -->
    <div id="view-courses" class="view-section flex-col app-container bg-slate-50">
        <div class="p-4 flex items-center gap-3 border-b border-slate-200 bg-white sticky top-0 z-10">
            <button onclick="navTo('menu')" class="w-10 h-10 flex items-center justify-center rounded-full bg-slate-100 text-slate-600 hover:bg-slate-200"><i class="fa-solid fa-arrow-left"></i></button>
            <div>
                <h2 class="font-bold text-slate-800">Pilih Mata Kuliah</h2>
                <p class="text-[10px] text-slate-500">Pilih room kelas yang sedang berlangsung</p>
            </div>
        </div>
        <div class="p-5 flex-grow overflow-y-auto">
            <div class="grid grid-cols-1 gap-3" id="course-list">
                <!-- Courses injected via JS -->
            </div>
        </div>
    </div>

    <!-- ================= VIEW 4: FORM ABSENSI ================= -->
    <div id="view-form" class="view-section flex-col app-container bg-white">
        <div class="p-4 flex items-center gap-3 border-b border-brand-100 bg-brand-50 sticky top-0 z-10">
            <button onclick="navTo('courses')" class="w-10 h-10 flex items-center justify-center rounded-full bg-white text-brand-600 shadow-sm"><i class="fa-solid fa-arrow-left"></i></button>
            <div class="flex-grow truncate">
                <p class="text-[10px] font-bold text-brand-500 uppercase tracking-wider">Room Aktif</p>
                <h2 class="font-bold text-slate-800 text-sm truncate" id="form-course-title">Nama Mata Kuliah</h2>
            </div>
        </div>
        
        <div class="p-6 flex-grow overflow-y-auto space-y-6">
            <!-- Data Mahasiswa -->
            <div class="space-y-4">
                <h3 class="text-sm font-bold text-slate-800 border-b pb-2"><i class="fa-solid fa-user text-brand-500 mr-2"></i>Identitas Mahasiswa</h3>
                
                <div>
                    <label class="block text-xs font-semibold text-slate-700 mb-1.5">Pilih Nama Anda</label>
                    <select id="form-student" onchange="autoFillNim()" class="w-full p-3 border border-slate-300 rounded-xl text-sm bg-white focus:ring-2 focus:ring-brand-500 outline-none">
                        <option value="">-- Pilih Mahasiswa --</option>
                    </select>
                </div>
                <div>
                    <label class="block text-xs font-semibold text-slate-700 mb-1.5">NIM (Otomatis)</label>
                    <input type="text" id="form-nim" readonly class="w-full p-3 border border-slate-200 bg-slate-50 rounded-xl text-sm font-mono outline-none text-slate-500">
                </div>
            </div>

            <!-- Status -->
            <div class="space-y-3">
                <h3 class="text-sm font-bold text-slate-800 border-b pb-2"><i class="fa-solid fa-clipboard-list text-brand-500 mr-2"></i>Status Kehadiran</h3>
                <div class="grid grid-cols-2 gap-3">
                    <label class="relative flex flex-col items-center p-4 rounded-xl border-2 border-slate-100 cursor-pointer hover:bg-slate-50 has-[:checked]:border-emerald-500 has-[:checked]:bg-emerald-50 transition text-center">
                        <input type="radio" name="status" value="Hadir" class="sr-only" checked>
                        <i class="fa-solid fa-user-check text-2xl text-emerald-500 mb-2"></i>
                        <span class="text-sm font-semibold text-slate-800">Hadir</span>
                    </label>
                    <label class="relative flex flex-col items-center p-4 rounded-xl border-2 border-slate-100 cursor-pointer hover:bg-slate-50 has-[:checked]:border-blue-500 has-[:checked]:bg-blue-50 transition text-center">
                        <input type="radio" name="status" value="Izin" class="sr-only">
                        <i class="fa-solid fa-envelope-open-text text-2xl text-blue-500 mb-2"></i>
                        <span class="text-sm font-semibold text-slate-800">Izin</span>
                    </label>
                    <label class="relative flex flex-col items-center p-4 rounded-xl border-2 border-slate-100 cursor-pointer hover:bg-slate-50 has-[:checked]:border-amber-500 has-[:checked]:bg-amber-50 transition text-center">
                        <input type="radio" name="status" value="Sakit" class="sr-only">
                        <i class="fa-solid fa-notes-medical text-2xl text-amber-500 mb-2"></i>
                        <span class="text-sm font-semibold text-slate-800">Sakit</span>
                    </label>
                    <label class="relative flex flex-col items-center p-4 rounded-xl border-2 border-slate-100 cursor-pointer hover:bg-slate-50 has-[:checked]:border-rose-500 has-[:checked]:bg-rose-50 transition text-center">
                        <input type="radio" name="status" value="Alpa" class="sr-only">
                        <i class="fa-solid fa-user-xmark text-2xl text-rose-500 mb-2"></i>
                        <span class="text-sm font-semibold text-slate-800">Alpa</span>
                    </label>
                </div>
            </div>
        </div>

        <div class="p-5 bg-white border-t border-slate-100">
            <button onclick="proceedToVerify()" class="w-full bg-brand-600 hover:bg-brand-700 text-white font-semibold py-3.5 rounded-xl shadow-md transition flex items-center justify-center gap-2">
                Lanjut Verifikasi <i class="fa-solid fa-arrow-right"></i>
            </button>
        </div>
    </div>

    <!-- ================= VIEW 5: VERIFIKASI ================= -->
    <div id="view-verify" class="view-section flex-col app-container bg-slate-900 text-white">
        <div class="p-4 flex items-center gap-3 sticky top-0 z-10">
            <button onclick="navTo('form')" class="w-10 h-10 flex items-center justify-center rounded-full bg-slate-800 text-slate-300 hover:bg-slate-700"><i class="fa-solid fa-arrow-left"></i></button>
            <div>
                <h2 class="font-bold text-white">Verifikasi Data</h2>
                <p class="text-[10px] text-slate-400" id="verify-subtitle">Status: Hadir</p>
            </div>
        </div>

        <div class="flex-grow flex flex-col items-center justify-center p-6 w-full">
            
            <!-- Verifikasi: HADIR (Kamera) -->
            <div id="verify-hadir" class="hidden w-full flex-col items-center space-y-6">
                <div class="w-full aspect-[3/4] bg-black rounded-2xl overflow-hidden relative border-2 border-slate-700 flex items-center justify-center shadow-2xl">
                    <video id="camera-feed" autoplay playsinline class="w-full h-full object-cover"></video>
                    <canvas id="camera-canvas" class="hidden"></canvas>
                    
                    <!-- Overlay Guides & Face Scanner -->
                    <div id="scan-border" class="absolute inset-0 border-4 border-amber-500/50 rounded-2xl pointer-events-none transition-colors duration-500"></div>
                    <div id="scan-badge" class="absolute top-4 left-1/2 -translate-x-1/2 px-4 py-1.5 bg-black/70 backdrop-blur rounded-full text-xs font-bold flex items-center gap-2 transition-colors duration-500 shadow-lg">
                        <span id="scan-dot" class="w-2 h-2 rounded-full bg-amber-500 animate-pulse"></span> <span id="scan-text">Mendeteksi Wajah...</span>
                    </div>
                    
                    <!-- Scanning Laser Line -->
                    <div id="scan-laser" class="absolute left-0 w-full h-1 bg-amber-500 shadow-[0_0_15px_#f59e0b] pointer-events-none animate-scan"></div>
                </div>
                <p class="text-sm text-slate-300 text-center" id="scan-instruction">Posisikan wajah Anda di tengah layar. Menunggu deteksi sistem...</p>
                <button onclick="submitHadir()" id="btn-submit-hadir" class="w-full bg-emerald-600 hover:bg-emerald-700 text-white font-bold py-4 rounded-xl shadow-lg transition flex items-center justify-center gap-3 text-lg opacity-50 cursor-not-allowed" disabled>
                    <i class="fa-solid fa-camera"></i> Ambil Selfie & Submit
                </button>
            </div>

            <!-- Verifikasi: IZIN / SAKIT (Upload) -->
            <div id="verify-upload" class="hidden w-full flex-col items-center space-y-6">
                <div class="w-20 h-20 bg-slate-800 rounded-full flex items-center justify-center text-3xl text-brand-400 mb-2">
                    <i class="fa-solid fa-file-arrow-up"></i>
                </div>
                <div class="text-center space-y-1 w-full">
                    <h3 class="text-lg font-bold">Unggah Bukti Surat</h3>
                    <p class="text-xs text-slate-400">Lampirkan foto surat keterangan dokter atau surat izin Anda.</p>
                </div>
                
                <div class="w-full relative">
                    <input type="file" id="file-input" accept="image/*" onchange="handleFileUpload(event)" class="absolute inset-0 w-full h-full opacity-0 cursor-pointer z-10">
                    <div class="w-full border-2 border-dashed border-slate-600 rounded-2xl p-8 flex flex-col items-center justify-center text-slate-400 bg-slate-800/50">
                        <i class="fa-solid fa-image text-3xl mb-3" id="upload-icon"></i>
                        <span class="text-sm font-medium" id="upload-text">Ketuk untuk memilih foto</span>
                    </div>
                </div>
                
                <div id="upload-preview-container" class="hidden w-full aspect-video bg-slate-800 rounded-xl overflow-hidden border border-slate-700">
                    <img id="upload-preview" src="" class="w-full h-full object-contain">
                </div>

                <button onclick="submitUpload()" id="btn-submit-upload" class="w-full bg-brand-600 hover:bg-brand-700 text-white font-bold py-4 rounded-xl shadow-lg transition flex items-center justify-center gap-3 text-lg opacity-50 cursor-not-allowed" disabled>
                    <i class="fa-solid fa-paper-plane"></i> Unggah & Submit
                </button>
            </div>

            <!-- Verifikasi: ALPA (Tanpa Bukti) -->
            <div id="verify-alpa" class="hidden w-full flex-col items-center space-y-6">
                <div class="w-24 h-24 bg-rose-900/50 rounded-full flex items-center justify-center text-5xl text-rose-500 mb-2">
                    <i class="fa-solid fa-triangle-exclamation"></i>
                </div>
                <div class="text-center space-y-2">
                    <h3 class="text-xl font-bold text-white">Konfirmasi Alpa</h3>
                    <p class="text-sm text-slate-400">Anda memilih status Alpa (Tanpa Keterangan). Sistem akan mencatat presensi tanpa verifikasi berkas.</p>
                </div>
                <div class="w-full p-4 bg-slate-800 rounded-xl text-xs text-slate-300">
                    <ul class="list-disc pl-4 space-y-1">
                        <li>Mahasiswa: <span id="alpa-nama" class="font-bold text-white"></span></li>
                        <li>Room: <span id="alpa-room" class="font-bold text-white"></span></li>
                    </ul>
                </div>
                <button onclick="submitAlpa()" class="w-full bg-rose-600 hover:bg-rose-700 text-white font-bold py-4 rounded-xl shadow-lg transition flex items-center justify-center gap-3 text-lg mt-4">
                    Submit Alpa
                </button>
            </div>

        </div>
    </div>

    <!-- ================= VIEW 6: SUCCESS ================= -->
    <div id="view-success" class="view-section flex-col app-container bg-emerald-600 text-white justify-center items-center text-center p-6">
        <div class="w-24 h-24 bg-white rounded-full flex items-center justify-center text-5xl text-emerald-500 mb-6 shadow-2xl animate-bounce">
            <i class="fa-solid fa-check"></i>
        </div>
        <h2 class="text-3xl font-bold mb-2">Berhasil!</h2>
        <p class="text-emerald-100 mb-10 text-sm">Data presensi Anda telah terekam di sistem server kelas.</p>
        <button onclick="navTo('menu')" class="w-full bg-white text-emerald-700 font-bold py-3.5 rounded-xl shadow-md transition">
            Kembali ke Beranda
        </button>
    </div>

    <!-- ================= VIEW: ADMIN LOGIN ================= -->
    <div id="view-admin-login" class="view-section flex-col app-container bg-slate-900 text-white justify-center items-center p-6">
        <button onclick="navTo('menu')" class="absolute top-6 left-6 w-10 h-10 flex items-center justify-center rounded-full bg-slate-800 text-slate-300 hover:bg-slate-700"><i class="fa-solid fa-arrow-left"></i></button>
        
        <div class="w-full max-w-sm space-y-6">
            <div class="text-center space-y-2 mb-8">
                <div class="w-20 h-20 bg-slate-800 rounded-2xl flex items-center justify-center text-4xl text-brand-500 mx-auto mb-4">
                    <i class="fa-solid fa-user-shield"></i>
                </div>
                <h2 class="text-2xl font-bold">Login Korti</h2>
                <p class="text-sm text-slate-400">Masukkan kredensial untuk mengakses rekapan.</p>
            </div>

            <div class="space-y-4">
                <div>
                    <label class="block text-xs font-semibold text-slate-400 mb-1.5">Username</label>
                    <input type="text" id="login-username" class="w-full p-4 border border-slate-700 bg-slate-800 rounded-xl text-sm outline-none focus:border-brand-500 transition placeholder-slate-500" placeholder="Masukkan username">
                </div>
                <div>
                    <label class="block text-xs font-semibold text-slate-400 mb-1.5">Password</label>
                    <input type="password" id="login-password" class="w-full p-4 border border-slate-700 bg-slate-800 rounded-xl text-sm outline-none focus:border-brand-500 transition placeholder-slate-500" placeholder="•••••••• (Contoh: admin2026)">
                </div>
                <button onclick="handleAdminLogin()" class="w-full bg-brand-600 hover:bg-brand-700 text-white font-bold py-4 rounded-xl shadow-lg transition mt-4">
                    Masuk
                </button>
            </div>
        </div>
    </div>

    <!-- ================= VIEW 7: ADMIN REKAPAN ================= -->
    <div id="view-admin" class="view-section flex-col admin-container">
        <!-- Admin Header -->
        <div class="bg-brand-900 text-white p-4 flex items-center justify-between sticky top-0 z-20 shadow-md">
            <div class="flex items-center gap-3">
                <button onclick="navTo('menu')" class="w-8 h-8 flex items-center justify-center rounded-lg bg-white/10 hover:bg-white/20"><i class="fa-solid fa-arrow-left"></i></button>
                <div>
                    <h2 class="font-bold text-lg">Panel Korti</h2>
                    <p class="text-[10px] text-blue-200">Manajemen Rekapan Absensi</p>
                </div>
            </div>
            <button onclick="exportCSV()" class="bg-amber-500 hover:bg-amber-600 text-slate-900 px-3 py-1.5 rounded-lg text-xs font-bold shadow-sm flex items-center gap-2">
                <i class="fa-solid fa-file-csv"></i> Export CSV
            </button>
        </div>

        <div class="p-6 flex-grow flex flex-col gap-6">
            <!-- Filter Bar -->
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 grid grid-cols-1 md:grid-cols-2 gap-4">
                <div>
                    <label class="block text-xs font-semibold text-slate-500 mb-1">Filter Bulan</label>
                    <select id="filter-month" onchange="renderAdminTable()" class="w-full p-2.5 border border-slate-300 rounded-lg text-sm bg-white font-medium">
                        <option value="Semua">Semua Bulan</option>
                        <!-- Injected via JS -->
                    </select>
                </div>
                <div>
                    <label class="block text-xs font-semibold text-slate-500 mb-1">Filter Mata Kuliah (Room Terpisah)</label>
                    <select id="filter-course" onchange="renderAdminTable()" class="w-full p-2.5 border border-slate-300 rounded-lg text-sm bg-white font-medium">
                        <option value="Semua">Semua Mata Kuliah (Gabungan)</option>
                        <!-- Injected via JS -->
                    </select>
                </div>
            </div>

            <!-- Table -->
            <div class="bg-white rounded-xl shadow-sm border border-slate-200 overflow-hidden flex-grow flex flex-col">
                <div class="overflow-x-auto custom-scrollbar">
                    <table class="w-full text-left border-collapse whitespace-nowrap min-w-[800px]">
                        <thead>
                            <tr class="bg-slate-100 text-slate-600 text-[11px] uppercase tracking-wider font-bold">
                                <th class="px-4 py-3 border-b">Tgl / Waktu</th>
                                <th class="px-4 py-3 border-b">Mata Kuliah</th>
                                <th class="px-4 py-3 border-b">NIM</th>
                                <th class="px-4 py-3 border-b">Nama Mahasiswa</th>
                                <th class="px-4 py-3 border-b">Status</th>
                                <th class="px-4 py-3 border-b text-center">Bukti</th>
                            </tr>
                        </thead>
                        <tbody id="admin-tbody" class="divide-y divide-slate-100 text-sm">
                            <!-- Injected via JS -->
                        </tbody>
                    </table>
                </div>
                <div id="admin-empty" class="hidden py-12 text-center text-slate-500">
                    <i class="fa-solid fa-folder-open text-3xl mb-3 text-slate-300"></i>
                    <p class="text-sm">Belum ada data presensi untuk filter ini.</p>
                </div>
            </div>

            <!-- Kelola Akses Admin -->
            <div class="bg-white p-6 rounded-xl shadow-sm border border-slate-200">
                <div class="flex items-center gap-3 mb-4 border-b border-slate-100 pb-3">
                    <i class="fa-solid fa-user-plus text-brand-500"></i>
                    <h3 class="font-bold text-slate-800 text-sm">Kelola Akses Admin</h3>
                </div>
                <p class="text-xs text-slate-500 mb-4">Tambahkan akun baru jika Anda ingin memberikan akses panel ini ke orang lain.</p>
                <div class="grid grid-cols-1 md:grid-cols-3 gap-4 items-end">
                    <div>
                        <label class="block text-[11px] font-semibold text-slate-500 mb-1">Username Baru</label>
                        <input type="text" id="new-admin-user" class="w-full p-2.5 border border-slate-300 rounded-lg text-sm bg-slate-50 outline-none focus:ring-2 focus:ring-brand-500">
                    </div>
                    <div>
                        <label class="block text-[11px] font-semibold text-slate-500 mb-1">Password Baru</label>
                        <input type="text" id="new-admin-pass" class="w-full p-2.5 border border-slate-300 rounded-lg text-sm bg-slate-50 outline-none focus:ring-2 focus:ring-brand-500">
                    </div>
                    <div>
                        <button onclick="handleAddAdmin()" class="w-full bg-slate-800 hover:bg-slate-900 text-white font-semibold py-2.5 rounded-lg text-sm transition">
                            Simpan Admin
                        </button>
                    </div>
                </div>
            </div>

            <!-- Kelola Room / Kegiatan Baru -->
            <div class="bg-white p-6 rounded-xl shadow-sm border border-slate-200">
                <div class="flex items-center gap-3 mb-4 border-b border-slate-100 pb-3">
                    <i class="fa-solid fa-folder-plus text-brand-500"></i>
                    <h3 class="font-bold text-slate-800 text-sm">Buat Room / Kegiatan Dadakan</h3>
                </div>
                <p class="text-xs text-slate-500 mb-4">Tambahkan room presensi baru untuk kegiatan khusus, seminar, atau kelas pengganti.</p>
                <div class="flex flex-col md:flex-row gap-4 items-end">
                    <div class="flex-grow w-full">
                        <label class="block text-[11px] font-semibold text-slate-500 mb-1">Nama Kegiatan/Room</label>
                        <input type="text" id="new-room-name" placeholder="Misal: Seminar Nasional, Rapat Evaluasi..." class="w-full p-2.5 border border-slate-300 rounded-lg text-sm bg-slate-50 outline-none focus:ring-2 focus:ring-brand-500">
                    </div>
                    <div class="w-full md:w-auto">
                        <button onclick="handleAddRoom()" class="w-full md:w-40 bg-brand-600 hover:bg-brand-700 text-white font-semibold py-2.5 rounded-lg text-sm transition">
                            Tambah Room
                        </button>
                    </div>
                </div>
            </div>

        </div>
    </div>


    <!-- ================= JAVASCRIPT ================= -->
    <script>
        // --- DATA SETUP ---
        const DEFAULT_COURSES = [
            "Bahasa Indonesia",
            "Dasar-dasar Bimbingan dan Konseling",
            "Filosofi Pendidikan Indonesia",
            "Pendidikan Inklusi",
            "Mikro Konseling Dasar",
            "Teori-teori Kepribadian Individu",
            "Wawasan Kependidikan",
            "Wawasan Profesi dan Kode Etik"
        ];

        let courses = JSON.parse(localStorage.getItem('absensi_courses')) || DEFAULT_COURSES;

        const MONTHS = ["Januari", "Februari", "Maret", "April", "Mei", "Juni", "Juli", "Agustus", "September", "Oktober", "November", "Desember"];

        const STUDENTS = [
            { nim: "2611011001", nama: "I Made Nova Trisnantara" },
            { nim: "2611011002", nama: "Maria Klaudia Seba" },
            { nim: "2611011003", nama: "Kasih Talenta Putri Br Tarigan" },
            { nim: "2611011004", nama: "Sovy Yuniska Sihotang" },
            { nim: "2611011005", nama: "Novi Tri Yulitha Br Sembiring" },
            { nim: "2611011006", nama: "Anggreni Br Surbakti" },
            { nim: "2611011007", nama: "Juli Sintya Bella Br Ginting" },
            { nim: "2611011008", nama: "Melati Br Ginting" },
            { nim: "2611011009", nama: "Kadek Yasinta Maharani" },
            { nim: "2611011010", nama: "Bergitha Teresa Mona" },
            { nim: "2611011011", nama: "Ferista Yuwanda Putri" },
            { nim: "2611011012", nama: "Ketut Olivia Krisnayanthi" },
            { nim: "2611011013", nama: "Nurul Safitri" },
            { nim: "2611011014", nama: "Gede Panji Bayu Winata" },
            { nim: "2611011016", nama: "Ni Made Tiara Waskita Cahyani" },
            { nim: "2611011017", nama: "Kadek Gek Icca Kirana Putri" },
            { nim: "2611011019", nama: "I Kadek Maha Danendra" },
            { nim: "2611011020", nama: "Marsela Agracelia Sudarsono" },
            { nim: "2611011021", nama: "Ni Nyoman Ari Mariantini" },
            { nim: "2611011046", nama: "Ni Kadek Putri Kesya Pelangi" },
            { nim: "2611011050", nama: "Getse Rafael Keliat" },
            { nim: "2611011056", nama: "Ni Luh Deby Putriani" },
            { nim: "2611011062", nama: "I Gusti Komang Dany Adityani" },
            { nim: "2611011066", nama: "Ni Komang Anggun Lestari" },
            { nim: "2611011071", nama: "Ni Kadek Dwipa Maharani" },
            { nim: "2611011125", nama: "Tri Nuryana Br Surbakti" },
            { nim: "2611011126", nama: "Komang Ayu Ratna Wilis" },
            { nim: "2611011128", nama: "I Putu Gede Arta Yoga" }
        ];

        // --- STATE ---
        let currentCourse = "";
        let currentName = "";
        let currentNim = "";
        let currentStatus = "";
        let uploadedFileDataUrl = "";
        
        let cameraStream = null;
        let isFaceDetected = false;
        let logs = JSON.parse(localStorage.getItem('absensi_spa_logs')) || [];
        
        // Memperbarui password admin bawaan menjadi admin2026 (Jika sebelumnya masih admin123, otomatis reset)
        let savedAdmins = JSON.parse(localStorage.getItem('absensi_admins'));
        if (!savedAdmins || (savedAdmins.length === 1 && savedAdmins[0].password === 'admin123')) {
            savedAdmins = [{username: 'admin', password: 'admin2026'}];
            localStorage.setItem('absensi_admins', JSON.stringify(savedAdmins));
        }
        let admins = savedAdmins;

        // --- INITIALIZATION ---
        window.onload = () => {
            initCourseList();
            initStudentList();
            initFilters();
            // Optional: Seed some dummy data if empty for demo purposes
            if(logs.length === 0) seedDummyData();
        };

        // --- NAVIGATION ---
        function navTo(viewId) {
            // Cleanup actions before leaving a view
            stopCamera(); 
            resetForms();

            document.querySelectorAll('.view-section').forEach(el => el.classList.remove('active'));
            document.getElementById(`view-${viewId}`).classList.add('active');

            if (viewId === 'admin') renderAdminTable();
        }

        // --- COURSES SCREEN ---
        function initCourseList() {
            const container = document.getElementById('course-list');
            const defaultIcons = ['fa-book', 'fa-hands-holding-child', 'fa-landmark', 'fa-users', 'fa-comments', 'fa-brain', 'fa-graduation-cap', 'fa-scale-balanced'];
            
            container.innerHTML = courses.map((c, i) => `
                <button onclick="selectCourse('${c}')" class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm hover:border-brand-400 hover:shadow-md transition text-left flex items-center gap-4 group">
                    <div class="w-10 h-10 rounded-lg bg-brand-50 text-brand-600 flex items-center justify-center group-hover:bg-brand-600 group-hover:text-white transition">
                        <i class="fa-solid ${defaultIcons[i] || 'fa-calendar-check'}"></i>
                    </div>
                    <span class="font-semibold text-slate-700 text-sm leading-tight flex-grow">${c}</span>
                    <i class="fa-solid fa-chevron-right text-slate-300 text-xs"></i>
                </button>
            `).join('');
        }

        function selectCourse(course) {
            currentCourse = course;
            document.getElementById('form-course-title').innerText = course;
            navTo('form');
        }

        // --- FORM SCREEN ---
        function initStudentList() {
            const select = document.getElementById('form-student');
            STUDENTS.forEach(s => {
                select.innerHTML += `<option value="${s.nim}">${s.nama}</option>`;
            });
        }

        function autoFillNim() {
            const nim = document.getElementById('form-student').value;
            document.getElementById('form-nim').value = nim;
        }

        function proceedToVerify() {
            const nim = document.getElementById('form-student').value;
            const statusNode = document.querySelector('input[name="status"]:checked');
            
            if (!nim) {
                alert("Harap pilih nama mahasiswa terlebih dahulu!");
                return;
            }

            const student = STUDENTS.find(s => s.nim === nim);
            currentName = student.nama;
            currentNim = student.nim;
            currentStatus = statusNode ? statusNode.value : "Hadir";

            setupVerifyScreen();
            navTo('verify');
        }

        // --- VERIFY SCREEN ---
        function setupVerifyScreen() {
            document.getElementById('verify-subtitle').innerText = `Status: ${currentStatus}`;
            
            const divHadir = document.getElementById('verify-hadir');
            const divUpload = document.getElementById('verify-upload');
            const divAlpa = document.getElementById('verify-alpa');

            divHadir.classList.add('hidden');
            divUpload.classList.add('hidden');
            divAlpa.classList.add('hidden');

            if (currentStatus === 'Hadir') {
                divHadir.classList.remove('hidden');
                divHadir.classList.add('flex');
                
                // Reset Camera UI
                isFaceDetected = false;
                const btnHadir = document.getElementById('btn-submit-hadir');
                btnHadir.disabled = true;
                btnHadir.classList.add('opacity-50', 'cursor-not-allowed');
                
                startCamera();
            } else if (currentStatus === 'Izin' || currentStatus === 'Sakit') {
                divUpload.classList.remove('hidden');
                divUpload.classList.add('flex');
                resetUploadBox();
            } else if (currentStatus === 'Alpa') {
                divAlpa.classList.remove('hidden');
                divAlpa.classList.add('flex');
                document.getElementById('alpa-nama').innerText = currentName;
                document.getElementById('alpa-room').innerText = currentCourse;
            }
        }

        // Camera Logic
        async function startCamera() {
            const video = document.getElementById('camera-feed');
            try {
                cameraStream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: 'user' }, audio: false });
                video.srcObject = cameraStream;
                
                // Mulai simulasi deteksi wajah (Face Scanner) layaknya APK Native
                simulateFaceDetection();
            } catch (err) {
                console.warn("Kamera ditolak/tidak tersedia", err);
                alert("Tidak dapat mengakses kamera. Pastikan izin diberikan.");
                // Tetap panggil simulate agar tombol tidak terkunci jika error kamera
                simulateFaceDetection();
            }
        }

        function simulateFaceDetection() {
            const border = document.getElementById('scan-border');
            const dot = document.getElementById('scan-dot');
            const text = document.getElementById('scan-text');
            const laser = document.getElementById('scan-laser');
            const btn = document.getElementById('btn-submit-hadir');
            const instruction = document.getElementById('scan-instruction');

            // Set state Scanning
            border.className = "absolute inset-0 border-4 border-amber-500/50 rounded-2xl pointer-events-none transition-colors duration-500";
            text.innerText = "Mendeteksi Wajah...";
            dot.className = "w-2 h-2 rounded-full bg-amber-500 animate-pulse";
            laser.classList.remove('hidden');
            laser.classList.add('animate-scan', 'bg-amber-500');
            instruction.innerText = "Posisikan wajah Anda di tengah layar. Menunggu deteksi sistem...";

            // Setelah 3 detik, ubah menjadi Terdeteksi (Simulasi Kecerdasan Buatan)
            setTimeout(() => {
                isFaceDetected = true;
                
                // Set state Terdeteksi (Hijau)
                border.className = "absolute inset-0 border-4 border-emerald-500/80 rounded-2xl pointer-events-none transition-colors duration-500";
                text.innerText = "Wajah Terdeteksi";
                dot.className = "w-2 h-2 rounded-full bg-emerald-500";
                laser.classList.add('hidden'); // Sembunyikan laser
                laser.classList.remove('animate-scan');
                instruction.innerText = "Wajah berhasil dideteksi. Silakan ambil selfie untuk submit.";
                
                // Buka kunci tombol Submit
                btn.disabled = false;
                btn.classList.remove('opacity-50', 'cursor-not-allowed');
            }, 3000);
        }

        function stopCamera() {
            if (cameraStream) {
                cameraStream.getTracks().forEach(track => track.stop());
                cameraStream = null;
            }
        }

        function getCameraDataUrl() {
            const video = document.getElementById('camera-feed');
            const canvas = document.getElementById('camera-canvas');
            if (cameraStream && video.videoWidth > 0) {
                canvas.width = video.videoWidth;
                canvas.height = video.videoHeight;
                const ctx = canvas.getContext('2d');
                ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
                return canvas.toDataURL('image/jpeg', 0.8);
            }
            return createPlaceholderAvatar(currentName); // Fallback
        }

        // Upload Logic
        function handleFileUpload(event) {
            const file = event.target.files[0];
            if (!file) return;

            const reader = new FileReader();
            reader.onload = function(e) {
                uploadedFileDataUrl = e.target.result;
                document.getElementById('upload-preview').src = uploadedFileDataUrl;
                document.getElementById('upload-preview-container').classList.remove('hidden');
                
                const btn = document.getElementById('btn-submit-upload');
                btn.disabled = false;
                btn.classList.remove('opacity-50', 'cursor-not-allowed');
            }
            reader.readAsDataURL(file);
        }

        function resetUploadBox() {
            uploadedFileDataUrl = "";
            document.getElementById('file-input').value = "";
            document.getElementById('upload-preview-container').classList.add('hidden');
            document.getElementById('upload-preview').src = "";
            
            const btn = document.getElementById('btn-submit-upload');
            btn.disabled = true;
            btn.classList.add('opacity-50', 'cursor-not-allowed');
        }

        // Submissions
        function submitHadir() {
            if (!isFaceDetected) return; // Cegah submit sebelum terdeteksi
            const photo = getCameraDataUrl();
            saveAttendance(photo);
        }

        function submitUpload() {
            if(!uploadedFileDataUrl) return;
            saveAttendance(uploadedFileDataUrl);
        }

        function submitAlpa() {
            saveAttendance(createPlaceholderAvatar('Alpa', '#e11d48'));
        }

        function saveAttendance(photoBase64) {
            const now = new Date();
            const log = {
                id: Date.now(),
                timestamp: now.toLocaleTimeString('id-ID', { hour: '2-digit', minute: '2-digit' }),
                dateStr: now.toLocaleDateString('id-ID', { day: '2-digit', month: '2-digit', year: 'numeric' }),
                month: MONTHS[now.getMonth()],
                course: currentCourse,
                name: currentName,
                nim: currentNim,
                status: currentStatus,
                photo: photoBase64
            };

            logs.unshift(log);
            localStorage.setItem('absensi_spa_logs', JSON.stringify(logs));
            navTo('success');
        }

        function submitKendala() {
            const txt = document.getElementById('kendala-text').value;
            if(!txt) { alert("Isi kendala terlebih dahulu"); return; }
            alert("Kendala berhasil dilaporkan ke Korti/Admin.");
            document.getElementById('kendala-text').value = "";
            navTo('menu');
        }

        function resetForms() {
            document.getElementById('form-student').value = "";
            document.getElementById('form-nim').value = "";
            // default checked to Hadir
            const r = document.querySelector('input[name="status"][value="Hadir"]');
            if(r) r.checked = true;
        }

        // --- ADMIN REKAPAN ---
        function initFilters() {
            const mFilter = document.getElementById('filter-month');
            const cFilter = document.getElementById('filter-course');
            
            mFilter.innerHTML = '<option value="Semua">Semua Bulan</option>';
            cFilter.innerHTML = '<option value="Semua">Semua Mata Kuliah (Gabungan)</option>';

            MONTHS.forEach(m => mFilter.innerHTML += `<option value="${m}">${m}</option>`);
            courses.forEach(c => cFilter.innerHTML += `<option value="${c}">${c}</option>`);

            // Set default month to current
            const currMonth = MONTHS[new Date().getMonth()];
            mFilter.value = currMonth;
        }

        function renderAdminTable() {
            const tbody = document.getElementById('admin-tbody');
            const emptyState = document.getElementById('admin-empty');
            
            const sMonth = document.getElementById('filter-month').value;
            const sCourse = document.getElementById('filter-course').value;

            const filtered = logs.filter(l => 
                (sMonth === 'Semua' || l.month === sMonth) &&
                (sCourse === 'Semua' || l.course === sCourse)
            );

            if (filtered.length === 0) {
                tbody.innerHTML = "";
                emptyState.classList.remove('hidden');
                return;
            }

            emptyState.classList.add('hidden');
            tbody.innerHTML = filtered.map(log => {
                let badgeClass = "bg-slate-100 text-slate-800";
                if(log.status==='Hadir') badgeClass = "bg-emerald-100 text-emerald-800";
                if(log.status==='Izin') badgeClass = "bg-blue-100 text-blue-800";
                if(log.status==='Sakit') badgeClass = "bg-amber-100 text-amber-800";
                if(log.status==='Alpa') badgeClass = "bg-rose-100 text-rose-800";

                return `
                <tr class="hover:bg-slate-50 transition">
                    <td class="px-4 py-3 font-medium text-slate-500">${log.dateStr} <br><span class="text-xs">${log.timestamp}</span></td>
                    <td class="px-4 py-3 text-xs max-w-[150px] truncate" title="${log.course}">${log.course}</td>
                    <td class="px-4 py-3 font-mono text-slate-600">${log.nim}</td>
                    <td class="px-4 py-3 font-semibold text-slate-800">${log.name}</td>
                    <td class="px-4 py-3"><span class="px-2 py-1 rounded text-xs font-bold ${badgeClass}">${log.status}</span></td>
                    <td class="px-4 py-3 text-center">
                        <img src="${log.photo}" class="w-10 h-10 rounded border border-slate-200 object-cover inline-block" alt="Bukti">
                    </td>
                </tr>
                `;
            }).join('');
        }

        function exportCSV() {
            const sMonth = document.getElementById('filter-month').value;
            const sCourse = document.getElementById('filter-course').value;

            const filtered = logs.filter(l => 
                (sMonth === 'Semua' || l.month === sMonth) &&
                (sCourse === 'Semua' || l.course === sCourse)
            );

            if (filtered.length === 0) {
                alert("Tidak ada data untuk diexport pada filter ini.");
                return;
            }

            let csv = `REKAPAN PRESENSI\nBulan: ${sMonth}\nMata Kuliah: ${sCourse}\n\n`;
            csv += `Tanggal,Waktu,NIM,Nama,Mata Kuliah,Status\n`;

            filtered.forEach(l => {
                csv += `"${l.dateStr}","${l.timestamp}","${l.nim}","${l.name}","${l.course}","${l.status}"\n`;
            });

            const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
            const link = document.createElement('a');
            link.href = URL.createObjectURL(blob);
            link.setAttribute('download', `Rekap_${sMonth}_${sCourse.substring(0,10)}.csv`);
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
        }

        // --- ADMIN AUTH & MANAGEMENT ---
        function handleAdminLogin() {
            const user = document.getElementById('login-username').value;
            const pass = document.getElementById('login-password').value;

            const isValid = admins.some(a => a.username === user && a.password === pass);
            
            if(isValid) {
                document.getElementById('login-username').value = "";
                document.getElementById('login-password').value = "";
                navTo('admin');
            } else {
                alert("Username atau Password salah!");
            }
        }

        function handleAddAdmin() {
            const user = document.getElementById('new-admin-user').value.trim();
            const pass = document.getElementById('new-admin-pass').value.trim();

            if(!user || !pass) {
                alert("Username dan password tidak boleh kosong!");
                return;
            }

            // Check if username already exists
            if(admins.some(a => a.username === user)) {
                alert("Username ini sudah terdaftar!");
                return;
            }

            admins.push({username: user, password: pass});
            localStorage.setItem('absensi_admins', JSON.stringify(admins));
            
            document.getElementById('new-admin-user').value = "";
            document.getElementById('new-admin-pass').value = "";
            alert("Admin baru berhasil ditambahkan! Mereka sekarang dapat login menggunakan kredensial tersebut.");
        }
        
        function handleAddRoom() {
            const roomName = document.getElementById('new-room-name').value.trim();
            if(!roomName) {
                alert("Nama room/kegiatan tidak boleh kosong!");
                return;
            }
            if(courses.includes(roomName)) {
                alert("Room/Kegiatan ini sudah ada!");
                return;
            }
            courses.push(roomName);
            localStorage.setItem('absensi_courses', JSON.stringify(courses));
            
            document.getElementById('new-room-name').value = "";
            initCourseList();
            initFilters();
            alert(`Room "${roomName}" berhasil ditambahkan! Mahasiswa sekarang bisa memilihnya untuk absensi.`);
        }

        function laporKendalaWA() {
            const phone = "6287889351493";
            const text = encodeURIComponent("Halo Korti, saya ingin melaporkan kendala terkait sistem presensi...");
            window.open(`https://wa.me/${phone}?text=${text}`, '_blank');
        }

        // --- HELPER ---
        function createPlaceholderAvatar(name, bgColor = '#3b82f6') {
            const initials = name.substring(0, 2).toUpperCase();
            const svg = `<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100" viewBox="0 0 100 100">
                <rect width="100" height="100" fill="${bgColor}"/>
                <text x="50" y="58" font-family="sans-serif" font-size="30" font-weight="bold" fill="#ffffff" text-anchor="middle">${initials}</text>
            </svg>`;
            return 'data:image/svg+xml;base64,' + btoa(svg);
        }

        function seedDummyData() {
            const now = new Date();
            const dummyLog = {
                id: Date.now(),
                timestamp: now.toLocaleTimeString('id-ID', { hour: '2-digit', minute: '2-digit' }),
                dateStr: now.toLocaleDateString('id-ID', { day: '2-digit', month: '2-digit', year: 'numeric' }),
                month: MONTHS[now.getMonth()],
                course: courses[0],
                name: STUDENTS[0].nama,
                nim: STUDENTS[0].nim,
                status: 'Hadir',
                photo: createPlaceholderAvatar('IM', '#10b981')
            };
            logs.push(dummyLog);
            localStorage.setItem('absensi_spa_logs', JSON.stringify(logs));
        }

    </script>
</body>
</html>

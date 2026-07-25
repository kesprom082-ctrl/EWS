<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>EWS - Sistem Early Warning Score</title>
    <!-- Bootstrap 5 -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
    <!-- Font Awesome 6 -->
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css" rel="stylesheet">
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.1/dist/chart.umd.min.js"></script>
    <!-- SweetAlert2 -->
    <script src="https://cdn.jsdelivr.net/npm/sweetalert2@11"></script>

    <style>
        /* ============================================
           CSS Variables & Reset
           ============================================ */
        :root {
            --primary: #0d47a1;
            --primary-dark: #08306b;
            --primary-light: #1976d2;
            --primary-lighter: #42a5f5;
            --accent: #00897b;
            --success: #2e7d32;
            --success-light: #e8f5e9;
            --warning: #f57f17;
            --warning-light: #fff8e1;
            --danger: #c62828;
            --danger-light: #ffebee;
            --orange: #ef6c00;
            --orange-light: #fff3e0;
            --bg: #eef2f7;
            --card: #ffffff;
            --text: #1a2332;
            --text-muted: #64748b;
            --border: #dce3ed;
            --sidebar-w: 260px;
            --sidebar-bg: linear-gradient(180deg, #08306b 0%, #0d47a1 40%, #1565c0 100%);
            --header-h: 64px;
            --radius: 12px;
            --shadow: 0 2px 12px rgba(13,71,161,0.08);
            --shadow-lg: 0 8px 32px rgba(13,71,161,0.12);
        }
        *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
        body {
            font-family: 'Plus Jakarta Sans', sans-serif;
            background: var(--bg);
            color: var(--text);
            overflow-x: hidden;
            min-height: 100vh;
        }

        /* ============================================
           Halaman Login
           ============================================ */
        #loginPage {
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            background: linear-gradient(135deg, #08306b 0%, #0d47a1 50%, #1565c0 100%);
            position: relative;
            overflow: hidden;
        }
        #loginPage::before {
            content: '';
            position: absolute;
            width: 600px; height: 600px;
            border-radius: 50%;
            background: rgba(255,255,255,0.03);
            top: -200px; right: -200px;
        }
        #loginPage::after {
            content: '';
            position: absolute;
            width: 400px; height: 400px;
            border-radius: 50%;
            background: rgba(255,255,255,0.03);
            bottom: -100px; left: -100px;
        }
        .login-card {
            background: var(--card);
            border-radius: 20px;
            padding: 48px 40px;
            width: 100%;
            max-width: 420px;
            box-shadow: 0 20px 60px rgba(0,0,0,0.3);
            position: relative;
            z-index: 2;
            animation: fadeUp 0.6s ease;
        }
        .login-logo {
            width: 80px; height: 80px;
            border-radius: 16px;
            background: linear-gradient(135deg, var(--primary), var(--primary-light));
            display: flex; align-items: center; justify-content: center;
            margin: 0 auto 20px;
            font-size: 32px; color: #fff;
            box-shadow: 0 4px 16px rgba(13,71,161,0.3);
        }
        .login-card h4 { text-align: center; font-weight: 700; color: var(--primary-dark); margin-bottom: 4px; }
        .login-card p.text-muted { text-align: center; font-size: 14px; margin-bottom: 32px; }
        .login-card .form-label { font-weight: 600; font-size: 13px; color: var(--text); }
        .login-card .form-control {
            border: 2px solid var(--border);
            border-radius: 10px;
            padding: 12px 16px;
            font-size: 15px;
            transition: all 0.2s;
        }
        .login-card .form-control:focus {
            border-color: var(--primary-light);
            box-shadow: 0 0 0 4px rgba(25,118,210,0.1);
        }
        .login-card .btn-primary {
            background: linear-gradient(135deg, var(--primary), var(--primary-light));
            border: none;
            border-radius: 10px;
            padding: 12px;
            font-weight: 600;
            font-size: 15px;
            width: 100%;
            transition: all 0.3s;
        }
        .login-card .btn-primary:hover {
            transform: translateY(-1px);
            box-shadow: 0 6px 20px rgba(13,71,161,0.35);
        }

        /* ============================================
           Sidebar
           ============================================ */
        .sidebar {
            position: fixed;
            top: 0; left: 0;
            width: var(--sidebar-w);
            height: 100vh;
            background: var(--sidebar-bg);
            color: #fff;
            z-index: 1040;
            transition: transform 0.3s ease;
            display: flex;
            flex-direction: column;
            overflow-y: auto;
        }
        .sidebar-brand {
            padding: 20px 20px 16px;
            border-bottom: 1px solid rgba(255,255,255,0.1);
            text-align: center;
            flex-shrink: 0;
        }
        .sidebar-brand .brand-logo {
            width: 50px; height: 50px;
            border-radius: 12px;
            background: rgba(255,255,255,0.15);
            display: flex; align-items: center; justify-content: center;
            margin: 0 auto 10px;
            font-size: 22px;
        }
        .sidebar-brand h6 { font-weight: 700; font-size: 14px; margin: 0; line-height: 1.3; }
        .sidebar-brand small { font-size: 11px; opacity: 0.7; }
        .sidebar-nav { padding: 16px 12px; flex: 1; }
        .sidebar-nav .nav-label {
            font-size: 10px;
            font-weight: 700;
            text-transform: uppercase;
            letter-spacing: 1.2px;
            opacity: 0.45;
            padding: 12px 12px 6px;
        }
        .sidebar-nav .nav-item {
            display: flex;
            align-items: center;
            gap: 12px;
            padding: 11px 16px;
            border-radius: 10px;
            color: rgba(255,255,255,0.75);
            text-decoration: none;
            font-size: 14px;
            font-weight: 500;
            cursor: pointer;
            transition: all 0.2s;
            margin-bottom: 2px;
        }
        .sidebar-nav .nav-item:hover {
            background: rgba(255,255,255,0.1);
            color: #fff;
        }
        .sidebar-nav .nav-item.active {
            background: rgba(255,255,255,0.18);
            color: #fff;
            font-weight: 600;
            box-shadow: inset 3px 0 0 rgba(255,255,255,0.6);
        }
        .sidebar-nav .nav-item i { width: 20px; text-align: center; font-size: 15px; }
        .sidebar-footer {
            padding: 16px 12px;
            border-top: 1px solid rgba(255,255,255,0.1);
            flex-shrink: 0;
        }
        .sidebar-overlay {
            display: none;
            position: fixed;
            inset: 0;
            background: rgba(0,0,0,0.5);
            z-index: 1035;
        }

        /* ============================================
           Header
           ============================================ */
        .main-header {
            position: fixed;
            top: 0;
            left: var(--sidebar-w);
            right: 0;
            height: var(--header-h);
            background: var(--card);
            border-bottom: 1px solid var(--border);
            display: flex;
            align-items: center;
            padding: 0 24px;
            z-index: 1030;
            box-shadow: 0 1px 4px rgba(0,0,0,0.04);
        }
        .header-left { display: flex; align-items: center; gap: 16px; }
        .header-left .btn-sidebar-toggle {
            display: none;
            background: none; border: none;
            font-size: 20px; color: var(--text);
            cursor: pointer; padding: 4px;
        }
        .header-title h5 { font-weight: 700; font-size: 16px; margin: 0; color: var(--primary-dark); }
        .header-title small { font-size: 12px; color: var(--text-muted); }
        .header-right { margin-left: auto; display: flex; align-items: center; gap: 12px; }
        .header-user {
            display: flex; align-items: center; gap: 8px;
            font-size: 13px; font-weight: 600; color: var(--text);
        }
        .header-user .avatar {
            width: 36px; height: 36px; border-radius: 10px;
            background: linear-gradient(135deg, var(--primary), var(--accent));
            color: #fff; display: flex; align-items: center; justify-content: center;
            font-size: 14px; font-weight: 700;
        }

        /* ============================================
           Konten Utama
           ============================================ */
        .main-content {
            margin-left: var(--sidebar-w);
            margin-top: var(--header-h);
            padding: 24px;
            min-height: calc(100vh - var(--header-h));
        }
        .page-section { display: none; animation: fadeUp 0.35s ease; }
        .page-section.active { display: block; }

        /* ============================================
           Kartu Statistik
           ============================================ */
        .stat-card {
            background: var(--card);
            border-radius: var(--radius);
            padding: 20px;
            box-shadow: var(--shadow);
            border: 1px solid var(--border);
            transition: all 0.3s;
            position: relative;
            overflow: hidden;
        }
        .stat-card:hover { transform: translateY(-2px); box-shadow: var(--shadow-lg); }
        .stat-card .stat-icon {
            width: 48px; height: 48px; border-radius: 12px;
            display: flex; align-items: center; justify-content: center;
            font-size: 20px; margin-bottom: 12px;
        }
        .stat-card .stat-value { font-size: 28px; font-weight: 800; line-height: 1; margin-bottom: 4px; }
        .stat-card .stat-label { font-size: 13px; color: var(--text-muted); font-weight: 500; }
        .stat-card::after {
            content: '';
            position: absolute;
            top: -30px; right: -30px;
            width: 100px; height: 100px;
            border-radius: 50%;
            opacity: 0.06;
        }

        /* ============================================
           Kartu Chart
           ============================================ */
        .chart-card {
            background: var(--card);
            border-radius: var(--radius);
            padding: 20px;
            box-shadow: var(--shadow);
            border: 1px solid var(--border);
        }
        .chart-card h6 { font-weight: 700; font-size: 14px; margin-bottom: 16px; color: var(--primary-dark); }

        /* ============================================
           Form EWS
           ============================================ */
        .form-card {
            background: var(--card);
            border-radius: var(--radius);
            padding: 24px;
            box-shadow: var(--shadow);
            border: 1px solid var(--border);
            margin-bottom: 20px;
        }
        .form-card h6 {
            font-weight: 700; font-size: 15px; color: var(--primary-dark);
            padding-bottom: 12px; margin-bottom: 20px;
            border-bottom: 2px solid var(--border);
            display: flex; align-items: center; gap: 8px;
        }
        .form-card h6 i { color: var(--primary-light); }
        .form-card .form-label {
            font-weight: 600; font-size: 13px; color: var(--text);
            margin-bottom: 4px;
        }
        .form-card .form-control, .form-card .form-select {
            border: 2px solid var(--border);
            border-radius: 8px;
            padding: 10px 14px;
            font-size: 14px;
            transition: border-color 0.2s;
        }
        .form-card .form-control:focus, .form-card .form-select:focus {
            border-color: var(--primary-light);
            box-shadow: 0 0 0 3px rgba(25,118,210,0.08);
        }
        .form-card .form-control.is-invalid, .form-card .form-select.is-invalid {
            border-color: var(--danger) !important;
        }

        /* Baris Parameter EWS */
        .ews-param {
            padding: 14px 0;
            border-bottom: 1px solid #f1f5f9;
        }
        .ews-param:last-child { border-bottom: none; }
        .ews-param-label {
            font-weight: 700; font-size: 13px; color: var(--text);
            margin-bottom: 8px;
        }
        .ews-options { display: flex; flex-wrap: wrap; gap: 8px; }
        .ews-opt {
            position: relative; cursor: pointer;
        }
        .ews-opt input[type="radio"] {
            position: absolute; opacity: 0; width: 0; height: 0;
        }
        .ews-opt-span {
            display: flex; align-items: center; gap: 6px;
            padding: 8px 14px;
            border: 2px solid var(--border);
            border-radius: 8px;
            font-size: 13px;
            font-weight: 500;
            transition: all 0.2s;
            white-space: nowrap;
            background: #fff;
        }
        .ews-opt-span .opt-score {
            background: #f1f5f9;
            color: var(--text-muted);
            font-size: 11px; font-weight: 700;
            padding: 2px 7px; border-radius: 6px;
            transition: all 0.2s;
        }
        .ews-opt input:checked + .ews-opt-span {
            border-color: var(--primary-light);
            background: rgba(25,118,210,0.05);
        }
        .ews-opt input:checked + .ews-opt-span .opt-score {
            background: var(--primary);
            color: #fff;
        }
        .ews-opt input:focus-visible + .ews-opt-span {
            outline: 2px solid var(--primary-lighter);
            outline-offset: 2px;
        }

        /* Hasil Skor */
        .result-box {
            border-radius: var(--radius);
            padding: 24px;
            text-align: center;
            border: 2px solid transparent;
            transition: all 0.4s;
            margin-bottom: 20px;
        }
        .result-box.green { background: var(--success-light); border-color: var(--success); }
        .result-box.yellow { background: var(--warning-light); border-color: var(--warning); }
        .result-box.orange { background: var(--orange-light); border-color: var(--orange); }
        .result-box.red { background: var(--danger-light); border-color: var(--danger); }
        .result-score { font-size: 56px; font-weight: 800; line-height: 1; }
        .result-box.green .result-score { color: var(--success); }
        .result-box.yellow .result-score { color: var(--warning); }
        .result-box.orange .result-score { color: var(--orange); }
        .result-box.red .result-score { color: var(--danger); }
        .result-category { font-size: 18px; font-weight: 700; margin-top: 4px; }
        .result-desc { font-size: 13px; color: var(--text-muted); margin-top: 8px; max-width: 500px; margin-left: auto; margin-right: auto; }
        .result-rekomendasi {
            margin-top: 12px; padding: 12px 16px;
            border-radius: 8px; font-size: 13px;
            font-weight: 600; text-align: left;
        }
        .result-box.green .result-rekomendasi { background: rgba(46,125,50,0.1); color: var(--success); }
        .result-box.yellow .result-rekomendasi { background: rgba(245,127,23,0.1); color: var(--warning); }
        .result-box.orange .result-rekomendasi { background: rgba(239,108,0,0.1); color: var(--orange); }
        .result-box.red .result-rekomendasi { background: rgba(198,40,40,0.1); color: var(--danger); }

        /* ============================================
           Tabel Data
           ============================================ */
        .table-card {
            background: var(--card);
            border-radius: var(--radius);
            box-shadow: var(--shadow);
            border: 1px solid var(--border);
            overflow: hidden;
        }
        .table-card .table-header {
            padding: 16px 20px;
            border-bottom: 1px solid var(--border);
            display: flex; align-items: center; justify-content: space-between;
            flex-wrap: wrap; gap: 12px;
        }
        .table-card .table-header h6 { font-weight: 700; font-size: 15px; margin: 0; }
        .table-responsive { overflow-x: auto; }
        .table-card table {
            margin: 0; font-size: 13px;
        }
        .table-card table th {
            background: #f8fafc;
            font-weight: 700; font-size: 12px;
            text-transform: uppercase; letter-spacing: 0.5px;
            color: var(--text-muted);
            padding: 12px 14px;
            border-bottom: 2px solid var(--border);
            white-space: nowrap;
        }
        .table-card table td {
            padding: 10px 14px;
            vertical-align: middle;
            border-bottom: 1px solid #f1f5f9;
        }
        .badge-ews {
            padding: 4px 10px; border-radius: 6px;
            font-size: 11px; font-weight: 700;
        }
        .badge-ews.green { background: var(--success-light); color: var(--success); }
        .badge-ews.yellow { background: var(--warning-light); color: var(--warning); }
        .badge-ews.orange { background: var(--orange-light); color: var(--orange); }
        .badge-ews.red { background: var(--danger-light); color: var(--danger); }
        .table-footer {
            padding: 12px 20px;
            border-top: 1px solid var(--border);
            display: flex; align-items: center; justify-content: space-between;
            flex-wrap: wrap; gap: 12px;
        }
        .pagination .page-link {
            border: none; color: var(--text);
            font-size: 13px; font-weight: 600;
            padding: 6px 12px; border-radius: 8px;
            margin: 0 2px; transition: all 0.2s;
        }
        .pagination .page-link:hover { background: #f1f5f9; }
        .pagination .page-item.active .page-link {
            background: var(--primary);
            color: #fff;
        }

        /* ============================================
           Loading Overlay
           ============================================ */
        .loading-overlay {
            display: none;
            position: fixed; inset: 0;
            background: rgba(255,255,255,0.85);
            z-index: 9999;
            align-items: center; justify-content: center;
            flex-direction: column; gap: 16px;
        }
        .loading-overlay.show { display: flex; }
        .spinner-ews {
            width: 48px; height: 48px;
            border: 4px solid var(--border);
            border-top-color: var(--primary);
            border-radius: 50%;
            animation: spin 0.8s linear infinite;
        }
        .loading-overlay p { font-weight: 600; color: var(--text-muted); font-size: 14px; }

        /* ============================================
           Profil
           ============================================ */
        .profile-avatar {
            width: 100px; height: 100px;
            border-radius: 20px;
            background: linear-gradient(135deg, var(--primary), var(--accent));
            color: #fff; font-size: 40px; font-weight: 800;
            display: flex; align-items: center; justify-content: center;
            margin: 0 auto 16px;
            box-shadow: 0 8px 24px rgba(13,71,161,0.25);
        }
        .info-row {
            display: flex; justify-content: space-between;
            padding: 12px 0;
            border-bottom: 1px solid #f1f5f9;
            font-size: 14px;
        }
        .info-row .label { color: var(--text-muted); font-weight: 500; }
        .info-row .value { font-weight: 700; color: var(--text); }

        /* ============================================
           Animasi
           ============================================ */
        @keyframes fadeUp {
            from { opacity: 0; transform: translateY(16px); }
            to { opacity: 1; transform: translateY(0); }
        }
        @keyframes spin {
            to { transform: rotate(360deg); }
        }
        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.5; }
        }

        /* ============================================
           Tombol Aksi
           ============================================ */
        .btn-action {
            border-radius: 10px;
            padding: 10px 20px;
            font-weight: 600;
            font-size: 14px;
            border: none;
            transition: all 0.2s;
            display: inline-flex;
            align-items: center;
            gap: 8px;
        }
        .btn-action:hover { transform: translateY(-1px); }
        .btn-action:active { transform: translateY(0); }
        .btn-ews-primary { background: var(--primary); color: #fff; }
        .btn-ews-primary:hover { background: var(--primary-dark); box-shadow: 0 4px 16px rgba(13,71,161,0.3); }
        .btn-ews-success { background: var(--success); color: #fff; }
        .btn-ews-success:hover { background: #1b5e20; box-shadow: 0 4px 16px rgba(46,125,50,0.3); }
        .btn-ews-warning { background: var(--warning); color: #fff; }
        .btn-ews-outline {
            background: #fff; color: var(--text);
            border: 2px solid var(--border);
        }
        .btn-ews-outline:hover { border-color: var(--primary-light); color: var(--primary); }
        .btn-ews-danger { background: var(--danger); color: #fff; }
        .btn-ews-danger:hover { background: #8e0000; }

        /* ============================================
           Filter Bar
           ============================================ */
        .filter-bar {
            display: flex; gap: 10px; flex-wrap: wrap; align-items: center;
        }
        .filter-bar .form-control, .filter-bar .form-select {
            border: 2px solid var(--border);
            border-radius: 8px;
            padding: 8px 12px;
            font-size: 13px;
            max-width: 200px;
        }

        /* ============================================
           Print Styles
           ============================================ */
        @media print {
            .sidebar, .main-header, .btn-action, .no-print { display: none !important; }
            .main-content { margin: 0 !important; padding: 20px !important; }
            .form-card { box-shadow: none !important; border: 1px solid #ddd !important; break-inside: avoid; }
            .result-box { border-width: 2px !important; }
        }

        /* ============================================
           Responsive
           ============================================ */
        @media (max-width: 991.98px) {
            .sidebar { transform: translateX(-100%); }
            .sidebar.open { transform: translateX(0); }
            .sidebar-overlay.show { display: block; }
            .main-header { left: 0; }
            .main-content { margin-left: 0; }
            .header-left .btn-sidebar-toggle { display: block; }
        }
        @media (max-width: 575.98px) {
            .main-content { padding: 16px; }
            .login-card { margin: 16px; padding: 32px 24px; }
            .stat-card { padding: 16px; }
            .stat-card .stat-value { font-size: 24px; }
            .form-card { padding: 16px; }
            .ews-options { gap: 6px; }
            .ews-opt-span { padding: 6px 10px; font-size: 12px; }
            .filter-bar .form-control, .filter-bar .form-select { max-width: 100%; }
        }

        /* Prefers reduced motion */
        @media (prefers-reduced-motion: reduce) {
            *, *::before, *::after {
                animation-duration: 0.01ms !important;
                transition-duration: 0.01ms !important;
            }
        }
    </style>
</head>
<body>

    <!-- ============================================
         LOADING OVERLAY
         ============================================ -->
    <div class="loading-overlay" id="loadingOverlay">
        <div class="spinner-ews"></div>
        <p id="loadingText">Memproses data...</p>
    </div>

    <!-- ============================================
         HALAMAN LOGIN
         ============================================ -->
    <div id="loginPage">
        <div class="login-card">
            <div class="login-logo">
                <i class="fas fa-hospital"></i>
            </div>
            <h4>Rumah Sakit umum daerah naibonat</h4>
            <p class="text-muted">Sistem Early Warning Score (EWS)</p>
            <form id="loginForm" novalidate>
                <div class="mb-3">
                    <label class="form-label">Username</label>
                    <input type="text" class="form-control" id="loginUser" placeholder="Masukkan username" autocomplete="off" required>
                </div>
                <div class="mb-4">
                    <label class="form-label">Password</label>
                    <div class="position-relative">
                        <input type="password" class="form-control pe-5" id="loginPass" placeholder="Masukkan password" required>
                        <button type="button" class="btn btn-link position-absolute top-50 end-0 translate-middle-y text-decoration-none me-2"
                                id="togglePass" style="color:var(--text-muted);font-size:14px;" aria-label="Tampilkan password">
                            <i class="fas fa-eye"></i>
                        </button>
                    </div>
                </div>
                <button type="submit" class="btn btn-primary">
                    <i class="fas fa-sign-in-alt me-2"></i>Masuk
                </button>
            </form>
        </div>
    </div>

    <!-- ============================================
         APPLIKASI UTAMA (setelah login)
         ============================================ -->
    <div id="appShell" style="display:none">

        <!-- Overlay sidebar mobile -->
        <div class="sidebar-overlay" id="sidebarOverlay"></div>

        <!-- Sidebar -->
        <aside class="sidebar" id="sidebar" role="navigation" aria-label="Menu navigasi utama">
            <div class="sidebar-brand">
                <div class="brand-logo"><i class="fas fa-hospital"></i></div>
                <h6>Rumah Sakit umum naibonat</h6>
                <small>Early Warning Score</small>
            </div>
            <nav class="sidebar-nav">
                <div class="nav-label">Menu Utama</div>
                <a class="nav-item active" data-page="dashboard" role="button" tabindex="0">
                    <i class="fas fa-home"></i> Dashboard
                </a>
                <a class="nav-item" data-page="ews-anak" role="button" tabindex="0">
                    <i class="fas fa-baby"></i> EWS Anak
                </a>
                <a class="nav-item" data-page="ews-dewasa" role="button" tabindex="0">
                    <i class="fas fa-user"></i> EWS Dewasa
                </a>
                <a class="nav-item" data-page="data" role="button" tabindex="0">
                    <i class="fas fa-database"></i> Data Pengisian
                </a>
                <div class="nav-label">Akun</div>
                <a class="nav-item" data-page="profil" role="button" tabindex="0">
                    <i class="fas fa-user-circle"></i> Profil
                </a>
                <a class="nav-item" id="btnLogout" role="button" tabindex="0" style="color:rgba(255,200,200,0.8)">
                    <i class="fas fa-sign-out-alt"></i> Logout
                </a>
            </nav>
            <div class="sidebar-footer">
                <small style="opacity:0.5;font-size:11px;text-align:center;display:block;">
                    EWS v1.0 &copy; 2025<br>Rumah Sakit XYZ
                </small>
            </div>
        </aside>

        <!-- Header -->
        <header class="main-header">
            <div class="header-left">
                <button class="btn-sidebar-toggle" id="btnToggleSidebar" aria-label="Toggle menu">
                    <i class="fas fa-bars"></i>
                </button>
                <div class="header-title">
                    <h5 id="headerTitle">Dashboard</h5>
                    <small id="headerSubtitle">Beranda &mdash; Ringkasan data EWS</small>
                </div>
            </div>
            <div class="header-right">
                <div class="header-user">
                    <div class="avatar" id="headerAvatar">01</div>
                    <span class="d-none d-sm-inline" id="headerUserName">Petugas</span>
                </div>
            </div>
        </header>

        <!-- Konten Utama -->
        <main class="main-content">

            <!-- ========== DASHBOARD ========== -->
            <section class="page-section active" id="page-dashboard">
                <div class="row g-3 mb-4">
                    <div class="col-6 col-lg">
                        <div class="stat-card">
                            <div class="stat-icon" style="background:rgba(13,71,161,0.1);color:var(--primary)">
                                <i class="fas fa-calendar-day"></i>
                            </div>
                            <div class="stat-value" id="statHariIni">0</div>
                            <div class="stat-label">Hari Ini</div>
                            <div style="position:absolute;top:-30px;right:-30px;width:100px;height:100px;border-radius:50%;background:var(--primary);opacity:0.06"></div>
                        </div>
                    </div>
                    <div class="col-6 col-lg">
                        <div class="stat-card">
                            <div class="stat-icon" style="background:rgba(0,137,123,0.1);color:var(--accent)">
                                <i class="fas fa-baby"></i>
                            </div>
                            <div class="stat-value" id="statAnak">0</div>
                            <div class="stat-label">EWS Anak</div>
                            <div style="position:absolute;top:-30px;right:-30px;width:100px;height:100px;border-radius:50%;background:var(--accent);opacity:0.06"></div>
                        </div>
                    </div>
                    <div class="col-6 col-lg">
                        <div class="stat-card">
                            <div class="stat-icon" style="background:rgba(25,118,210,0.1);color:var(--primary-light)">
                                <i class="fas fa-user"></i>
                            </div>
                            <div class="stat-value" id="statDewasa">0</div>
                            <div class="stat-label">EWS Dewasa</div>
                            <div style="position:absolute;top:-30px;right:-30px;width:100px;height:100px;border-radius:50%;background:var(--primary-light);opacity:0.06"></div>
                        </div>
                    </div>
                    <div class="col-6 col-lg">
                        <div class="stat-card">
                            <div class="stat-icon" style="background:rgba(245,127,23,0.1);color:var(--warning)">
                                <i class="fas fa-calendar-alt"></i>
                            </div>
                            <div class="stat-value" id="statBulanIni">0</div>
                            <div class="stat-label">Bulan Ini</div>
                            <div style="position:absolute;top:-30px;right:-30px;width:100px;height:100px;border-radius:50%;background:var(--warning);opacity:0.06"></div>
                        </div>
                    </div>
                    <div class="col-6 col-lg">
                        <div class="stat-card">
                            <div class="stat-icon" style="background:rgba(239,108,0,0.1);color:var(--orange)">
                                <i class="fas fa-chart-bar"></i>
                            </div>
                            <div class="stat-value" id="statTotal">0</div>
                            <div class="stat-label">Total</div>
                            <div style="position:absolute;top:-30px;right:-30px;width:100px;height:100px;border-radius:50%;background:var(--orange);opacity:0.06"></div>
                        </div>
                    </div>
                </div>
                <div class="row g-3">
                    <div class="col-lg-6">
                        <div class="chart-card">
                            <h6><i class="fas fa-chart-column me-2"></i>Grafik Pengisian Harian (7 Hari Terakhir)</h6>
                            <canvas id="chartHarian" height="220"></canvas>
                        </div>
                    </div>
                    <div class="col-lg-6">
                        <div class="chart-card">
                            <h6><i class="fas fa-chart-line me-2"></i>Grafik Pengisian Bulanan (6 Bulan)</h6>
                            <canvas id="chartBulanan" height="220"></canvas>
                        </div>
                    </div>
                    <div class="col-lg-6">
                        <div class="chart-card">
                            <h6><i class="fas fa-hospital me-2"></i>Grafik Berdasarkan Ruangan</h6>
                            <canvas id="chartRuangan" height="220"></canvas>
                        </div>
                    </div>
                    <div class="col-lg-6">
                        <div class="chart-card">
                            <h6><i class="fas fa-palette me-2"></i>Grafik Berdasarkan Warna Risiko</h6>
                            <canvas id="chartRisiko" height="220"></canvas>
                        </div>
                    </div>
                </div>
            </section>

            <!-- ========== EWS ANAK ========== -->
            <section class="page-section" id="page-ews-anak">
                <div class="form-card">
                    <h6><i class="fas fa-user-edit"></i> Data Pasien</h6>
                    <div class="row g-3">
                        <div class="col-md-4">
                            <label class="form-label">Nomor Rekam Medis <span class="text-danger">*</span></label>
                            <input type="text" class="form-control" id="anak_rm" placeholder="Contoh: RM-001234">
                        </div>
                        <div class="col-md-4">
                            <label class="form-label">Nama Pasien <span class="text-danger">*</span></label>
                            <input type="text" class="form-control" id="anak_nama" placeholder="Nama lengkap pasien">
                        </div>
                        <div class="col-md-4">
                            <label class="form-label">Tanggal Lahir <span class="text-danger">*</span></label>
                            <input type="date" class="form-control" id="anak_tglLahir">
                        </div>
                        <div class="col-md-3">
                            <label class="form-label">Umur</label>
                            <input type="text" class="form-control" id="anak_umur" readonly placeholder="Otomatis">
                        </div>
                        <div class="col-md-3">
                            <label class="form-label">Jenis Kelamin <span class="text-danger">*</span></label>
                            <select class="form-select" id="anak_jk">
                                <option value="">-- Pilih --</option>
                                <option value="Laki-laki">Laki-laki</option>
                                <option value="Perempuan">Perempuan</option>
                            </select>
                        </div>
                        <div class="col-md-3">
                            <label class="form-label">Ruangan <span class="text-danger">*</span></label>
                            <select class="form-select" id="anak_ruangan">
                                <option value="">-- Pilih --</option>
                                <option>ICU </option>
                                <option>Paviliun Anak</option>
                                <option>Bangsal Anak 1</option>
                                <option>Bangsal Anak 2</option>
                                <option>IGD</option>
                                <option>NICU</option>
                                <option>PICU</option>
                            </select>
                        </div>
                        <div class="col-md-3">
                            <label class="form-label">Dokter <span class="text-danger">*</span></label>
                            <input type="text" class="form-control" id="anak_dokter" placeholder="Nama dokter">
                        </div>
                        <div class="col-md-3">
                            <label class="form-label">Perawat <span class="text-danger">*</span></label>
                            <input type="text" class="form-control" id="anak_perawat" placeholder="Nama perawat">
                        </div>
                        <div class="col-md-3">
                            <label class="form-label">Tanggal Pemeriksaan <span class="text-danger">*</span></label>
                            <input type="date" class="form-control" id="anak_tanggal">
                        </div>
                        <div class="col-md-3">
                            <label class="form-label">Jam Pemeriksaan <span class="text-danger">*</span></label>
                            <input type="time" class="form-control" id="anak_jam">
                        </div>
                    </div>
                </div>

                <div class="form-card">
                    <h6><i class="fas fa-clipboard-check"></i> Parameter EWS Anak</h6>
                    <div id="ewsAnakParams"></div>
                </div>

                <div class="result-box" id="anakResultBox" style="display:none">
                    <div class="result-score" id="anakResultScore">0</div>
                    <div class="result-category" id="anakResultCat">-</div>
                    <div class="result-desc" id="anakResultDesc">-</div>
                    <div class="result-rekomendasi" id="anakResultRekom">
                        <i class="fas fa-stethoscope me-2"></i><span></span>
                    </div>
                </div>

                <div class="d-flex flex-wrap gap-2 no-print">
                    <button class="btn-action btn-ews-primary" onclick="hitungAnak()"><i class="fas fa-calculator"></i> Hitung Skor</button>
                    <button class="btn-action btn-ews-success" onclick="simpanAnak()"><i class="fas fa-save"></i> Simpan</button>
                    <button class="btn-action btn-ews-outline" onclick="resetAnak()"><i class="fas fa-undo"></i> Reset</button>
                    <button class="btn-action btn-ews-outline" onclick="cetakAnak()"><i class="fas fa-print"></i> Cetak</button>
                    <button class="btn-action btn-ews-outline" onclick="navigateTo('dashboard')"><i class="fas fa-arrow-left"></i> Kembali</button>
                </div>
            </section>

            <!-- ========== EWS DEWASA ========== -->
            <section class="page-section" id="page-ews-dewasa">
                <div class="form-card">
                    <h6><i class="fas fa-user-edit"></i> Data Pasien</h6>
                    <div class="row g-3">
                        <div class="col-md-4">
                            <label class="form-label">Nomor Rekam Medis <span class="text-danger">*</span></label>
                            <input type="text" class="form-control" id="dewasa_rm" placeholder="Contoh: RM-001234">
                        </div>
                        <div class="col-md-4">
                            <label class="form-label">Nama Pasien <span class="text-danger">*</span></label>
                            <input type="text" class="form-control" id="dewasa_nama" placeholder="Nama lengkap pasien">
                        </div>
                        <div class="col-md-4">
                            <label class="form-label">Umur (Tahun) <span class="text-danger">*</span></label>
                            <input type="number" class="form-control" id="dewasa_umur" placeholder="Contoh: 45" min="0" max="150">
                        </div>
                        <div class="col-md-3">
                            <label class="form-label">Jenis Kelamin <span class="text-danger">*</span></label>
                            <select class="form-select" id="dewasa_jk">
                                <option value="">-- Pilih --</option>
                                <option value="Laki-laki">Laki-laki</option>
                                <option value="Perempuan">Perempuan</option>
                            </select>
                        </div>
                        <div class="col-md-3">
                            <label class="form-label">Ruangan <span class="text-danger">*</span></label>
                            <select class="form-select" id="dewasa_ruangan">
                                <option value="">-- Pilih --</option>
                                <option>ICU</option>
                                <option>Bangsal 1</option>
                                <option>Bangsal 2</option>
                                <option>Bangsal 3</option>
                                <option>Bangsal VIP</option>
                                <option>IGD</option>
                                <option>HCU</option>
                                <option>Post Operasi</option>
                            </select>
                        </div>
                        <div class="col-md-3">
                            <label class="form-label">Dokter <span class="text-danger">*</span></label>
                            <input type="text" class="form-control" id="dewasa_dokter" placeholder="Nama dokter">
                        </div>
                        <div class="col-md-3">
                            <label class="form-label">Perawat <span class="text-danger">*</span></label>
                            <input type="text" class="form-control" id="dewasa_perawat" placeholder="Nama perawat">
                        </div>
                        <div class="col-md-3">
                            <label class="form-label">Tanggal Pemeriksaan <span class="text-danger">*</span></label>
                            <input type="date" class="form-control" id="dewasa_tanggal">
                        </div>
                        <div class="col-md-3">
                            <label class="form-label">Jam Pemeriksaan <span class="text-danger">*</span></label>
                            <input type="time" class="form-control" id="dewasa_jam">
                        </div>
                    </div>
                </div>

                <div class="form-card">
                    <h6><i class="fas fa-clipboard-check"></i> Parameter EWS Dewasa (NEWS2)</h6>
                    <div id="ewsDewasaParams"></div>
                </div>

                <div class="result-box" id="dewasaResultBox" style="display:none">
                    <div class="result-score" id="dewasaResultScore">0</div>
                    <div class="result-category" id="dewasaResultCat">-</div>
                    <div class="result-desc" id="dewasaResultDesc">-</div>
                    <div class="result-rekomendasi" id="dewasaResultRekom">
                        <i class="fas fa-stethoscope me-2"></i><span></span>
                    </div>
                </div>

                <div class="d-flex flex-wrap gap-2 no-print">
                    <button class="btn-action btn-ews-primary" onclick="hitungDewasa()"><i class="fas fa-calculator"></i> Hitung Skor</button>
                    <button class="btn-action btn-ews-success" onclick="simpanDewasa()"><i class="fas fa-save"></i> Simpan</button>
                    <button class="btn-action btn-ews-outline" onclick="resetDewasa()"><i class="fas fa-undo"></i> Reset</button>
                    <button class="btn-action btn-ews-outline" onclick="cetakDewasa()"><i class="fas fa-print"></i> Cetak</button>
                    <button class="btn-action btn-ews-outline" onclick="navigateTo('dashboard')"><i class="fas fa-arrow-left"></i> Kembali</button>
                </div>
            </section>

            <!-- ========== DATA PENGISIAN ========== -->
            <section class="page-section" id="page-data">
                <div class="table-card">
                    <div class="table-header">
                        <h6><i class="fas fa-database me-2"></i>Data Pengisian EWS</h6>
                        <div class="d-flex flex-wrap gap-2 no-print">
                            <button class="btn-action btn-ews-outline btn-sm" onclick="exportExcel()" style="padding:6px 12px;font-size:12px">
                                <i class="fas fa-file-excel"></i> Excel
                            </button>
                            <button class="btn-action btn-ews-outline btn-sm" onclick="exportPDF()" style="padding:6px 12px;font-size:12px">
                                <i class="fas fa-file-pdf"></i> PDF
                            </button>
                            <button class="btn-action btn-ews-outline btn-sm" onclick="printData()" style="padding:6px 12px;font-size:12px">
                                <i class="fas fa-print"></i> Print
                            </button>
                        </div>
                    </div>
                    <!-- Filter Bar -->
                    <div class="px-3 pt-3 pb-2 no-print">
                        <div class="filter-bar">
                            <input type="text" class="form-control" id="filterSearch" placeholder="Cari nama / no RM..." style="max-width:220px">
                            <input type="date" class="form-control" id="filterDari" style="max-width:160px" title="Dari tanggal">
                            <input type="date" class="form-control" id="filterSampai" style="max-width:160px" title="Sampai tanggal">
                            <select class="form-select" id="filterRuangan" style="max-width:160px">
                                <option value="">Semua Ruangan</option>
                                <option>ICU</option><option>ICU Anak</option><option>Bangsal 1</option>
                                <option>Bangsal 2</option><option>Bangsal 3</option><option>Bangsal VIP</option>
                                <option>Bangsal Anak 1</option><option>Bangsal Anak 2</option>
                                <option>Paviliun Anak</option><option>IGD</option><option>NICU</option>
                                <option>PICU</option><option>HCU</option><option>Post Operasi</option>
                            </select>
                            <select class="form-select" id="filterJenis" style="max-width:140px">
                                <option value="">Semua Jenis</option>
                                <option value="Anak">EWS Anak</option>
                                <option value="Dewasa">EWS Dewasa</option>
                            </select>
                        </div>
                    </div>
                    <!-- Tabel -->
                    <div class="table-responsive">
                        <table class="table table-hover mb-0">
                            <thead>
                                <tr>
                                    <th>No</th>
                                    <th>Tanggal</th>
                                    <th>Jam</th>
                                    <th>No RM</th>
                                    <th>Nama</th>
                                    <th>Ruangan</th>
                                    <th>Jenis</th>
                                    <th>Skor</th>
                                    <th>Kategori</th>
                                    <th class="no-print">Aksi</th>
                                </tr>
                            </thead>
                            <tbody id="dataTableBody">
                                <tr><td colspan="10" class="text-center py-4 text-muted">Belum ada data</td></tr>
                            </tbody>
                        </table>
                    </div>
                    <div class="table-footer">
                        <small class="text-muted" id="dataInfo">Menampilkan 0 dari 0 data</small>
                        <nav><ul class="pagination pagination-sm mb-0" id="dataPagination"></ul></nav>
                    </div>
                </div>
            </section>

            <!-- ========== PROFIL ========== -->
            <section class="page-section" id="page-profil">
                <div class="row justify-content-center">
                    <div class="col-lg-6">
                        <div class="form-card text-center">
                            <div class="profile-avatar" id="profilAvatar">01</div>
                            <h5 class="fw-bold mb-1" id="profilNama">Petugas</h5>
                            <p class="text-muted mb-4" id="profilRole">Petugas EWS</p>
                            <div class="text-start">
                                <div class="info-row">
                                    <span class="label">Username</span>
                                    <span class="value" id="profilUser">01</span>
                                </div>
                                <div class="info-row">
                                    <span class="label">Terakhir Login</span>
                                    <span class="value" id="profilLastLogin">-</span>
                                </div>
                                <div class="info-row">
                                    <span class="label">Total Pengisian</span>
                                    <span class="value" id="profilTotalInput">0</span>
                                </div>
                            </div>
                        </div>
                        <div class="form-card">
                            <h6><i class="fas fa-key"></i> Ganti Password</h6>
                            <form id="formGantiPassword" novalidate>
                                <div class="mb-3">
                                    <label class="form-label">Password Lama</label>
                                    <input type="password" class="form-control" id="passOld" required>
                                </div>
                                <div class="mb-3">
                                    <label class="form-label">Password Baru</label>
                                    <input type="password" class="form-control" id="passNew" required>
                                </div>
                                <div class="mb-3">
                                    <label class="form-label">Konfirmasi Password Baru</label>
                                    <input type="password" class="form-control" id="passConfirm" required>
                                </div>
                                <button type="submit" class="btn-action btn-ews-primary"><i class="fas fa-save"></i> Simpan Password</button>
                            </form>
                        </div>
                    </div>
                </div>
            </section>

        </main>
    </div>

    <!-- ============================================
         JAVASCRIPT - Seluruh Logika Aplikasi
         ============================================ -->
    <script>
    /* ============================================
       config.js - Konfigurasi Aplikasi
       Ganti URL di bawah dengan URL Web App
       Google Apps Script Anda
       ============================================ */
    const CONFIG = {
    GAS_URL: "https://script.google.com/macros/s/AKfycbxTHShQUXkyzKYaSnOHuYMsQZVSMcsbo7kflQdBCdF5siJO9YZs7rZIDyxq9d7FGhy6nA/exec",
    APP_NAME: "Sistem Early Warning Score (EWS)",
    HOSPITAL_NAME: "RSUD Naibonat",
    ITEMS_PER_PAGE: 10
    };

    /* ============================================
       Data Skor EWS Anak (PEWS)
       ============================================ */
    const EWS_ANAK_PARAMS = [
        {
            key: 'kesadaran', label: 'Kesadaran (AVPU)',
            options: [
                { value: 'A', label: 'Alert', score: 0 },
                { value: 'V', label: 'Voice', score: 1 },
                { value: 'P', label: 'Pain', score: 2 },
                { value: 'U', label: 'Unresponsive', score: 3 }
            ]
        },
        {
            key: 'frekNafas', label: 'Frekuensi Nafas (per menit)',
            options: [
                { value: 'normal', label: 'Normal', score: 0 },
                { value: 'meningkat', label: 'Meningkat', score: 1 },
                { value: 'menurun', label: 'Menurun', score: 2 },
                { value: 'apneu', label: 'Apneu', score: 3 }
            ]
        },
        {
            key: 'retraksi', label: 'Retraksi Dinding Dada',
            options: [
                { value: 'tidak', label: 'Tidak ada', score: 0 },
                { value: 'ringan', label: 'Ringan', score: 1 },
                { value: 'sedang', label: 'Sedang', score: 2 },
                { value: 'berat', label: 'Berat', score: 3 }
            ]
        },
        {
            key: 'saturasi', label: 'Saturasi Oksigen (SpO2)',
            options: [
                { value: 'ge95', label: '\u226595%', score: 0 },
                { value: '90_94', label: '90-94%', score: 1 },
                { value: 'lt90', label: '<90%', score: 2 }
            ]
        },
        {
            key: 'oksigen', label: 'Penggunaan Oksigen Tambahan',
            options: [
                { value: 'tidak', label: 'Tidak', score: 0 },
                { value: 'ya', label: 'Ya', score: 1 }
            ]
        },
        {
            key: 'frekNadi', label: 'Frekuensi Nadi (per menit)',
            options: [
                { value: 'normal', label: 'Normal', score: 0 },
                { value: 'takikardi', label: 'Takikardi', score: 1 },
                { value: 'bradikardi', label: 'Bradikardi', score: 2 }
            ]
        },
        {
            key: 'crt', label: 'CRT (Capillary Refill Time)',
            options: [
                { value: 'lt2', label: '<2 detik', score: 0 },
                { value: '2_3', label: '2-3 detik', score: 1 },
                { value: 'gt3', label: '>3 detik', score: 2 }
            ]
        },
        {
            key: 'tekananDarah', label: 'Tekanan Darah',
            options: [
                { value: 'normal', label: 'Normal', score: 0 },
                { value: 'hipertensi', label: 'Hipertensi', score: 1 },
                { value: 'hipotensi', label: 'Hipotensi', score: 2 }
            ]
        },
        {
            key: 'suhu', label: 'Suhu Tubuh',
            options: [
                { value: '36_38', label: '36.0 - 38.0\u00B0C', score: 0 },
                { value: 'gt38', label: '>38.0\u00B0C', score: 1 },
                { value: 'lt36', label: '<36.0\u00B0C', score: 2 }
            ]
        },
        {
            key: 'nyeri', label: 'Nyeri',
            options: [
                { value: 'tidak', label: 'Tidak ada', score: 0 },
                { value: 'ringan', label: 'Ringan', score: 1 },
                { value: 'sedang', label: 'Sedang', score: 2 },
                { value: 'berat', label: 'Berat', score: 3 }
            ]
        }
    ];

    /* ============================================
       Data Skor EWS Dewasa (NEWS2)
       ============================================ */
    const EWS_DEWASA_PARAMS = [
        {
            key: 'respirasi', label: 'Frekuensi Nafas (per menit)',
            options: [
                { value: 'le8', label: '\u22648', score: 3 },
                { value: '9_11', label: '9-11', score: 1 },
                { value: '12_20', label: '12-20', score: 0 },
                { value: '21_24', label: '21-24', score: 2 },
                { value: 'ge25', label: '\u226525', score: 3 }
            ]
        },
        {
            key: 'saturasi', label: 'Saturasi Oksigen (SpO2)',
            options: [
                { value: 'ge96', label: '\u226596%', score: 0 },
                { value: '94_95', label: '94-95%', score: 1 },
                { value: '92_93', label: '92-93%', score: 2 },
                { value: 'le91', label: '\u226491%', score: 3 }
            ]
        },
        {
            key: 'oksigen', label: 'Penggunaan Oksigen Tambahan',
            options: [
                { value: 'tidak', label: 'Tidak', score: 0 },
                { value: 'ya', label: 'Ya', score: 2 }
            ]
        },
        {
            key: 'sistolik', label: 'Tekanan Darah Sistolik (mmHg)',
            options: [
                { value: 'le90', label: '\u226490', score: 3 },
                { value: '91_100', label: '91-100', score: 2 },
                { value: '101_110', label: '101-110', score: 1 },
                { value: '111_219', label: '111-219', score: 0 },
                { value: 'ge220', label: '\u2265220', score: 3 }
            ]
        },
        {
            key: 'nadi', label: 'Frekuensi Nadi (per menit)',
            options: [
                { value: 'le40', label: '\u226440', score: 3 },
                { value: '41_50', label: '41-50', score: 1 },
                { value: '51_90', label: '51-90', score: 0 },
                { value: '91_110', label: '91-110', score: 1 },
                { value: '111_130', label: '111-130', score: 2 },
                { value: 'ge131', label: '\u2265131', score: 3 }
            ]
        },
        {
            key: 'kesadaran', label: 'Kesadaran (AVPU)',
            options: [
                { value: 'A', label: 'Alert', score: 0 },
                { value: 'VPU', label: 'V / P / U', score: 3 }
            ]
        },
        {
            key: 'suhu', label: 'Suhu Tubuh (\u00B0C)',
            options: [
                { value: 'le35', label: '\u226435.0', score: 3 },
                { value: '35_1_36', label: '35.1-36.0', score: 1 },
                { value: '36_1_38', label: '36.1-38.0', score: 0 },
                { value: '38_1_39', label: '38.1-39.0', score: 1 },
                { value: 'ge39_1', label: '\u226539.1', score: 2 }
            ]
        }
    ];

    /* ============================================
       Kategori & Rekomendasi
       ============================================ */
    const KATEGORI_ANAK = [
        { min: 0, max: 2, color: 'green', label: 'Risiko Rendah', desc: 'Pasien dalam kondisi stabil', rekom: 'Monitoring rutin sesuai standar ruangan. Evaluasi ulang dalam 4-6 jam.' },
        { min: 3, max: 4, color: 'yellow', label: 'Risiko Sedang', desc: 'Pasien memerlukan peningkatan pengawasan', rekom: 'Tingkatkan frekuensi monitoring menjadi setiap 1-2 jam. Informasikan kepada dokter jaga. Dokumentasikan tanda vital tambahan.' },
        { min: 5, max: 6, color: 'orange', label: 'Risiko Tinggi', desc: 'Pasien memerlukan intervensi segera', rekom: 'Hubungi dokter penanggung jawab segera. Monitoring kontinyu. Pertimbangkan transfer ke unit perawatan intensif (PICU). Siapkan resusitasi.' },
        { min: 7, max: 99, color: 'red', label: 'Risiko Kritis', desc: 'Pasien dalam kondisi gawat darurat', rekom: 'PANGGIL TIM CODE BLUE. Respon darurat segera. Intubasi siap. Transfer ke PICU. Monitoring ketat setiap 5 menit. Komunikasi dengan keluarga.' }
    ];
    const KATEGORI_DEWASA = [
        { min: 0, max: 4, color: 'green', label: 'Risiko Rendah', desc: 'Pasien dalam kondisi stabil', rekom: 'Monitoring rutin sesuai standar ruangan. Evaluasi ulang minimum setiap 4-12 jam.' },
        { min: 5, max: 6, color: 'yellow', label: 'Risiko Sedang', desc: 'Pasien memerlukan peningkatan pengawasan', rekom: 'Tingkatkan frekuensi monitoring menjadi setiap 1-2 jam. Informasikan kepada dokter jaga yang berwenang. Lakukan tindakan penunjang sesuai kebutuhan.' },
        { min: 7, max: 99, color: 'red', label: 'Risiko Tinggi', desc: 'Pasien memerlukan respons darurat', rekom: 'PANGGIL TIM RESPON DARURAT. Transfer ke ICU segera. Monitoring kontinyu. Siapkan peralatan resusitasi. Komunikasi dengan dokter spesialis dan keluarga pasien.' }
    ];

    /* ============================================
       Variabel Global
       ============================================ */
    let chartHarian = null, chartBulanan = null, chartRuangan = null, chartRisiko = null;
    let dataPage = 1;
    let isSubmitting = false;

    /* ============================================
       Utilitas
       ============================================ */
    // Format tanggal ke YYYY-MM-DD
    function toDateStr(d) {
        const y = d.getFullYear();
        const m = String(d.getMonth() + 1).padStart(2, '0');
        const day = String(d.getDate()).padStart(2, '0');
        return `${y}-${m}-${day}`;
    }
    // Format tanggal ke DD/MM/YYYY
    function toDisplayDate(str) {
        if (!str) return '-';
        const parts = str.split('-');
        return `${parts[2]}/${parts[1]}/${parts[0]}`;
    }
    // Format waktu
    function toDisplayTime(str) {
        if (!str) return '-';
        return str;
    }
    // Hitung umur dari tanggal lahir
    function hitungUmur(tglLahir) {
        if (!tglLahir) return '';
        const lahir = new Date(tglLahir);
        const sekarang = new Date();
        let tahun = sekarang.getFullYear() - lahir.getFullYear();
        let bulan = sekarang.getMonth() - lahir.getMonth();
        if (bulan < 0) { tahun--; bulan += 12; }
        if (sekarang.getDate() < lahir.getDate()) { bulan--; if (bulan < 0) { tahun--; bulan += 12; } }
        if (tahun > 0) return `${tahun} tahun ${bulan} bulan`;
        if (bulan > 0) return `${bulan} bulan`;
        const hari = Math.floor((sekarang - lahir) / (1000 * 60 * 60 * 24));
        return `${hari} hari`;
    }
    // Tampilkan loading
    function showLoading(text) {
        document.getElementById('loadingText').textContent = text || 'Memproses data...';
        document.getElementById('loadingOverlay').classList.add('show');
    }
    function hideLoading() {
        document.getElementById('loadingOverlay').classList.remove('show');
    }
    // Ambil data dari localStorage
    function getDataAnak() {
        return JSON.parse(localStorage.getItem('ews_anak') || '[]');
    }
    function getDataDewasa() {
        return JSON.parse(localStorage.getItem('ews_dewasa') || '[]');
    }
    function getAllData() {
        const anak = getDataAnak().map(d => ({ ...d, jenis: 'Anak' }));
        const dewasa = getDataDewasa().map(d => ({ ...d, jenis: 'Dewasa' }));
        return [...anak, ...dewasa].sort((a, b) => (b.timestamp || '').localeCompare(a.timestamp || ''));
    }
    function saveDataAnak(arr) { localStorage.setItem('ews_anak', JSON.stringify(arr)); }
    function saveDataDewasa(arr) { localStorage.setItem('ews_dewasa', JSON.stringify(arr)); }

    /* ============================================
       login.js - Logika Login & Session
       ============================================ */
    function checkSession() {
        const session = localStorage.getItem('ews_session');
        if (session) {
            const s = JSON.parse(session);
            const users = JSON.parse(localStorage.getItem('ews_users') || '[{"username":"01","password":"02","nama":"Petugas EWS"}]');
            const user = users.find(u => u.username === s.username);
            if (user) {
                showApp(user);
                return;
            }
        }
        showLogin();
    }
    function showLogin() {
        document.getElementById('loginPage').style.display = 'flex';
        document.getElementById('appShell').style.display = 'none';
    }
    function showApp(user) {
        document.getElementById('loginPage').style.display = 'none';
        document.getElementById('appShell').style.display = 'block';
        document.getElementById('headerAvatar').textContent = user.username;
        document.getElementById('headerUserName').textContent = user.nama;
        document.getElementById('profilAvatar').textContent = user.username;
        document.getElementById('profilNama').textContent = user.nama;
        document.getElementById('profilUser').textContent = user.username;
        const session = JSON.parse(localStorage.getItem('ews_session') || '{}');
        document.getElementById('profilLastLogin').textContent = session.loginTime || '-';
        navigateTo('dashboard');
    }
    // Toggle password visibility
    document.getElementById('togglePass').addEventListener('click', function() {
        const inp = document.getElementById('loginPass');
        const icon = this.querySelector('i');
        if (inp.type === 'password') {
            inp.type = 'text'; icon.className = 'fas fa-eye-slash';
        } else {
            inp.type = 'password'; icon.className = 'fas fa-eye';
        }
    });
    // Form login
    document.getElementById('loginForm').addEventListener('submit', function(e) {
        e.preventDefault();
        const username = document.getElementById('loginUser').value.trim();
        const password = document.getElementById('loginPass').value;
        const users = JSON.parse(localStorage.getItem('ews_users') || '[{"username":"01","password":"02","nama":"Petugas EWS"}]');
        const user = users.find(u => u.username === username && u.password === password);
        if (user) {
            const now = new Date();
            localStorage.setItem('ews_session', JSON.stringify({
                username: user.username,
                loginTime: now.toLocaleString('id-ID', { dateStyle: 'full', timeStyle: 'short' })
            }));
            showApp(user);
            Swal.fire({ icon: 'success', title: 'Berhasil', text: 'Selamat datang, ' + user.nama, timer: 1500, showConfirmButton: false });
        } else {
            Swal.fire({ icon: 'error', title: 'Login Gagal', text: 'Username atau Password Salah.' });
        }
    });
    // Logout
    document.getElementById('btnLogout').addEventListener('click', function() {
        Swal.fire({
            title: 'Konfirmasi Logout',
            text: 'Apakah Anda yakin ingin keluar?',
            icon: 'question',
            showCancelButton: true,
            confirmButtonColor: '#c62828',
            confirmButtonText: 'Ya, Logout',
            cancelButtonText: 'Batal'
        }).then(result => {
            if (result.isConfirmed) {
                localStorage.removeItem('ews_session');
                showLogin();
                document.getElementById('loginUser').value = '';
                document.getElementById('loginPass').value = '';
            }
        });
    });

    /* ============================================
       app.js - Router & Navigasi
       ============================================ */
    const PAGE_META = {
        'dashboard': { title: 'Dashboard', subtitle: 'Beranda \u2014 Ringkasan data EWS' },
        'ews-anak': { title: 'EWS Anak', subtitle: 'Pengisian Early Warning Score Pasien Anak' },
        'ews-dewasa': { title: 'EWS Dewasa', subtitle: 'Pengisian Early Warning Score Pasien Dewasa (NEWS2)' },
        'data': { title: 'Data Pengisian', subtitle: 'Riwayat seluruh pengisian EWS' },
        'profil': { title: 'Profil', subtitle: 'Informasi akun dan pengaturan' }
    };
    function navigateTo(page) {
        // Sembunyikan semua halaman
        document.querySelectorAll('.page-section').forEach(s => s.classList.remove('active'));
        // Tampilkan halaman yang dipilih
        const target = document.getElementById('page-' + page);
        if (target) target.classList.add('active');
        // Update sidebar
        document.querySelectorAll('.sidebar-nav .nav-item').forEach(n => n.classList.remove('active'));
        const navItem = document.querySelector(`.nav-item[data-page="${page}"]`);
        if (navItem) navItem.classList.add('active');
        // Update header
        const meta = PAGE_META[page] || { title: page, subtitle: '' };
        document.getElementById('headerTitle').textContent = meta.title;
        document.getElementById('headerSubtitle').textContent = meta.subtitle;
        // Tutup sidebar mobile
        document.getElementById('sidebar').classList.remove('open');
        document.getElementById('sidebarOverlay').classList.remove('show');
        // Load data halaman tertentu
        if (page === 'dashboard') loadDashboard();
        if (page === 'data') renderDataTable();
        if (page === 'profil') loadProfil();
        // Scroll ke atas
        window.scrollTo({ top: 0, behavior: 'smooth' });
    }
    // Event navigasi sidebar
    document.querySelectorAll('.sidebar-nav .nav-item[data-page]').forEach(item => {
        item.addEventListener('click', () => navigateTo(item.dataset.page));
        item.addEventListener('keydown', e => { if (e.key === 'Enter') navigateTo(item.dataset.page); });
    });
    // Toggle sidebar mobile
    document.getElementById('btnToggleSidebar').addEventListener('click', () => {
        document.getElementById('sidebar').classList.toggle('open');
        document.getElementById('sidebarOverlay').classList.toggle('show');
    });
    document.getElementById('sidebarOverlay').addEventListener('click', () => {
        document.getElementById('sidebar').classList.remove('open');
        document.getElementById('sidebarOverlay').classList.remove('show');
    });

    /* ============================================
       Fungsi Render Parameter EWS (digunakan bersama)
       ============================================ */
    function renderParams(containerId, params) {
        const container = document.getElementById(containerId);
        let html = '';
        params.forEach(p => {
            html += `<div class="ews-param">
                <div class="ews-param-label">${p.label}</div>
                <div class="ews-options">`;
            p.options.forEach(opt => {
                html += `<label class="ews-opt">
                    <input type="radio" name="ews_${p.key}" value="${opt.value}" data-score="${opt.score}">
                    <span class="ews-opt-span">${opt.label} <span class="opt-score">${opt.score}</span></span>
                </label>`;
            });
            html += `</div></div>`;
        });
        container.innerHTML = html;
    }
    // Hitung total skor dari parameter
    function hitungSkor(params) {
        let total = 0;
        const values = {};
        params.forEach(p => {
            const checked = document.querySelector(`input[name="ews_${p.key}"]:checked`);
            if (checked) {
                total += parseInt(checked.dataset.score);
                values[p.key] = { value: checked.value, score: parseInt(checked.dataset.score) };
            } else {
                values[p.key] = { value: '', score: 0 };
            }
        });
        return { total, values };
    }
    // Tampilkan hasil skor
    function showResult(boxId, scoreId, catId, descId, rekomId, skor, kategoriList) {
        const box = document.getElementById(boxId);
        let kat = kategoriList.find(k => skor >= k.min && skor <= k.max);
        if (!kat) kat = kategoriList[kategoriList.length - 1];
        box.className = 'result-box ' + kat.color;
        box.style.display = 'block';
        document.getElementById(scoreId).textContent = skor;
        document.getElementById(catId).textContent = kat.label;
        document.getElementById(descId).textContent = kat.desc;
        document.getElementById(rekomId).querySelector('span').textContent = kat.rekom;
        // Scroll ke hasil
        box.scrollIntoView({ behavior: 'smooth', block: 'center' });
    }

    /* ============================================
       ews-anak.js - Form & Logika EWS Anak
       ============================================ */
    // Inisialisasi form EWS Anak
    function initEwsAnak() {
        renderParams('ewsAnakParams', EWS_ANAK_PARAMS);
        // Set default tanggal & jam
        document.getElementById('anak_tanggal').value = toDateStr(new Date());
        document.getElementById('anak_jam').value = new Date().toTimeString().slice(0, 5);
        // Auto hitung umur
        document.getElementById('anak_tglLahir').addEventListener('change', function() {
            document.getElementById('anak_umur').value = hitungUmur(this.value);
        });
    }
    function hitungAnak() {
        // Cek apakah semua parameter terisi
        const allFilled = EWS_ANAK_PARAMS.every(p => document.querySelector(`input[name="ews_${p.key}"]:checked`));
        if (!allFilled) {
            Swal.fire({ icon: 'warning', title: 'Perhatian', text: 'Silakan isi semua parameter EWS terlebih dahulu.' });
            return;
        }
        const { total } = hitungSkor(EWS_ANAK_PARAMS);
        showResult('anakResultBox', 'anakResultScore', 'anakResultCat', 'anakResultDesc', 'anakResultRekom', total, KATEGORI_ANAK);
    }
    // Validasi form data pasien anak
    function validateAnak() {
        const fields = ['anak_rm', 'anak_nama', 'anak_tglLahir', 'anak_jk', 'anak_ruangan', 'anak_dokter', 'anak_perawat', 'anak_tanggal', 'anak_jam'];
        let firstInvalid = null;
        let valid = true;
        fields.forEach(id => {
            const el = document.getElementById(id);
            if (!el.value.trim()) {
                el.classList.add('is-invalid');
                valid = false;
                if (!firstInvalid) firstInvalid = el;
            } else {
                el.classList.remove('is-invalid');
            }
        });
        // Cek parameter EWS
        const allParamsFilled = EWS_ANAK_PARAMS.every(p => document.querySelector(`input[name="ews_${p.key}"]:checked`));
        if (!allParamsFilled) {
            valid = false;
            if (!firstInvalid) firstInvalid = document.getElementById('ewsAnakParams');
        }
        if (firstInvalid) firstInvalid.scrollIntoView({ behavior: 'smooth', block: 'center' });
        return valid;
    }
    function simpanAnak() {
        if (isSubmitting) return;
        if (!validateAnak()) {
            Swal.fire({ icon: 'warning', title: 'Data Belum Lengkap', text: 'Harap isi semua field yang wajib ditandai *.' });
            return;
        }
        // Pastikan skor sudah dihitung
        const resultBox = document.getElementById('anakResultBox');
        if (resultBox.style.display === 'none') {
            hitungAnak();
        }
        const { total, values } = hitungSkor(EWS_ANAK_PARAMS);
        let kat = KATEGORI_ANAK.find(k => total >= k.min && total <= k.max) || KATEGORI_ANAK[KATEGORI_ANAK.length - 1];
        const session = JSON.parse(localStorage.getItem('ews_session') || '{}');
        const data = {
            timestamp: new Date().toISOString(),
            rm: document.getElementById('anak_rm').value.trim(),
            nama: document.getElementById('anak_nama').value.trim(),
            tglLahir: document.getElementById('anak_tglLahir').value,
            umur: document.getElementById('anak_umur').value,
            jk: document.getElementById('anak_jk').value,
            ruangan: document.getElementById('anak_ruangan').value,
            dokter: document.getElementById('anak_dokter').value.trim(),
            perawat: document.getElementById('anak_perawat').value.trim(),
            tanggal: document.getElementById('anak_tanggal').value,
            jam: document.getElementById('anak_jam').value,
            params: values,
            totalSkor: total,
            kategori: kat.label,
            kategoriColor: kat.color,
            rekomendasi: kat.rekom,
            petugasInput: session.username || '-'
        };
        isSubmitting = true;
        showLoading('Menyimpan data EWS Anak...');
        // Kirim ke Google Apps Script
        kirimKeGAS('anak', data)
            .then(() => {
                // Simpan ke localStorage sebagai backup
                const arr = getDataAnak();
                arr.push(data);
                saveDataAnak(arr);
                hideLoading();
                Swal.fire({ icon: 'success', title: 'Berhasil', text: 'Data EWS Anak berhasil disimpan.' });
                resetAnak();
            })
            .catch(() => {
                // Simpan ke localStorage sebagai fallback
                const arr = getDataAnak();
                arr.push(data);
                saveDataAnak(arr);
                hideLoading();
                Swal.fire({ icon: 'success', title: 'Tersimpan Lokal', text: 'Data tersimpan di perangkat ini. Pastikan koneksi internet untuk sync ke server.' });
                resetAnak();
            })
            .finally(() => { isSubmitting = false; });
    }
    function resetAnak() {
        document.querySelectorAll('#page-ews-anak .form-control, #page-ews-anak .form-select').forEach(el => {
            if (el.type === 'date') el.value = toDateStr(new Date());
            else if (el.type === 'time') el.value = new Date().toTimeString().slice(0, 5);
            else if (el.readOnly) return;
            else el.value = '';
            el.classList.remove('is-invalid');
        });
        document.querySelectorAll('#page-ews-anak input[type="radio"]').forEach(r => r.checked = false);
        document.getElementById('anakResultBox').style.display = 'none';
    }
    function cetakAnak() {
        const resultBox = document.getElementById('anakResultBox');
        if (resultBox.style.display === 'none') {
            Swal.fire({ icon: 'info', title: 'Info', text: 'Silakan hitung skor terlebih dahulu sebelum mencetak.' });
            return;
        }
        window.print();
    }

    /* ============================================
       ews-dewasa.js - Form & Logika EWS Dewasa
       ============================================ */
    function initEwsDewasa() {
        renderParams('ewsDewasaParams', EWS_DEWASA_PARAMS);
        document.getElementById('dewasa_tanggal').value = toDateStr(new Date());
        document.getElementById('dewasa_jam').value = new Date().toTimeString().slice(0, 5);
    }
    function hitungDewasa() {
        const allFilled = EWS_DEWASA_PARAMS.every(p => document.querySelector(`input[name="ews_${p.key}"]:checked`));
        if (!allFilled) {
            Swal.fire({ icon: 'warning', title: 'Perhatian', text: 'Silakan isi semua parameter EWS terlebih dahulu.' });
            return;
        }
        const { total } = hitungSkor(EWS_DEWASA_PARAMS);
        showResult('dewasaResultBox', 'dewasaResultScore', 'dewasaResultCat', 'dewasaResultDesc', 'dewasaResultRekom', total, KATEGORI_DEWASA);
    }
    function validateDewasa() {
        const fields = ['dewasa_rm', 'dewasa_nama', 'dewasa_umur', 'dewasa_jk', 'dewasa_ruangan', 'dewasa_dokter', 'dewasa_perawat', 'dewasa_tanggal', 'dewasa_jam'];
        let firstInvalid = null;
        let valid = true;
        fields.forEach(id => {
            const el = document.getElementById(id);
            if (!el.value.trim()) {
                el.classList.add('is-invalid');
                valid = false;
                if (!firstInvalid) firstInvalid = el;
            } else {
                el.classList.remove('is-invalid');
            }
        });
        const allParamsFilled = EWS_DEWASA_PARAMS.every(p => document.querySelector(`input[name="ews_${p.key}"]:checked`));
        if (!allParamsFilled) {
            valid = false;
            if (!firstInvalid) firstInvalid = document.getElementById('ewsDewasaParams');
        }
        if (firstInvalid) firstInvalid.scrollIntoView({ behavior: 'smooth', block: 'center' });
        return valid;
    }
    function simpanDewasa() {
        if (isSubmitting) return;
        if (!validateDewasa()) {
            Swal.fire({ icon: 'warning', title: 'Data Belum Lengkap', text: 'Harap isi semua field yang wajib ditandai *.' });
            return;
        }
        const resultBox = document.getElementById('dewasaResultBox');
        if (resultBox.style.display === 'none') hitungDewasa();
        const { total, values } = hitungSkor(EWS_DEWASA_PARAMS);
        let kat = KATEGORI_DEWASA.find(k => total >= k.min && total <= k.max) || KATEGORI_DEWASA[KATEGORI_DEWASA.length - 1];
        const session = JSON.parse(localStorage.getItem('ews_session') || '{}');
        const data = {
            timestamp: new Date().toISOString(),
            rm: document.getElementById('dewasa_rm').value.trim(),
            nama: document.getElementById('dewasa_nama').value.trim(),
            umur: document.getElementById('dewasa_umur').value,
            jk: document.getElementById('dewasa_jk').value,
            ruangan: document.getElementById('dewasa_ruangan').value,
            dokter: document.getElementById('dewasa_dokter').value.trim(),
            perawat: document.getElementById('dewasa_perawat').value.trim(),
            tanggal: document.getElementById('dewasa_tanggal').value,
            jam: document.getElementById('dewasa_jam').value,
            params: values,
            totalSkor: total,
            kategori: kat.label,
            kategoriColor: kat.color,
            rekomendasi: kat.rekom,
            petugasInput: session.username || '-'
        };
        isSubmitting = true;
        showLoading('Menyimpan data EWS Dewasa...');
        kirimKeGAS('dewasa', data)
            .then(() => {
                const arr = getDataDewasa();
                arr.push(data);
                saveDataDewasa(arr);
                hideLoading();
                Swal.fire({ icon: 'success', title: 'Berhasil', text: 'Data EWS Dewasa berhasil disimpan.' });
                resetDewasa();
            })
            .catch(() => {
                const arr = getDataDewasa();
                arr.push(data);
                saveDataDewasa(arr);
                hideLoading();
                Swal.fire({ icon: 'success', title: 'Tersimpan Lokal', text: 'Data tersimpan di perangkat ini. Pastikan koneksi internet untuk sync ke server.' });
                resetDewasa();
            })
            .finally(() => { isSubmitting = false; });
    }
    function resetDewasa() {
        document.querySelectorAll('#page-ews-dewasa .form-control, #page-ews-dewasa .form-select').forEach(el => {
            if (el.type === 'date') el.value = toDateStr(new Date());
            else if (el.type === 'time') el.value = new Date().toTimeString().slice(0, 5);
            else if (el.readOnly) return;
            else el.value = '';
            el.classList.remove('is-invalid');
        });
        document.querySelectorAll('#page-ews-dewasa input[type="radio"]').forEach(r => r.checked = false);
        document.getElementById('dewasaResultBox').style.display = 'none';
    }
    function cetakDewasa() {
        const resultBox = document.getElementById('dewasaResultBox');
        if (resultBox.style.display === 'none') {
            Swal.fire({ icon: 'info', title: 'Info', text: 'Silakan hitung skor terlebih dahulu sebelum mencetak.' });
            return;
        }
        window.print();
    }

    /* ============================================
       Kirim Data ke Google Apps Script
       ============================================ */
    async function kirimGoogleSheet(data){

    try{

        const response = await fetch(CONFIG.GAS_URL,{
            method:"https://script.google.com/macros/s/AKfycbxTHShQUXkyzKYaSnOHuYMsQZVSMcsbo7kflQdBCdF5siJO9YZs7rZIDyxq9d7FGhy6nA/exec",
            headers:{
                "Content-Type":"application/json"
            },
            body:JSON.stringify(data)
        });

        const hasil = await response.json();

        return hasil;

    }catch(err){

        console.log(err);

        return {
            success:false,
            message:err.toString()
        };

    }

}

    /* ============================================
       dashboard.js - Statistik & Grafik
       ============================================ */
    function loadDashboard() {
        const allData = getAllData();
        const today = toDateStr(new Date());
        const now = new Date();
        const bulanIni = now.getFullYear() + '-' + String(now.getMonth() + 1).padStart(2, '0');

        // Hitung statistik
        const hariIni = allData.filter(d => d.tanggal === today).length;
        const anak = getDataAnak().length;
        const dewasa = getDataDewasa().length;
        const bulanIniCount = allData.filter(d => d.tanggal && d.tanggal.startsWith(bulanIni)).length;
        const total = allData.length;

        // Animasi angka
        animateNumber('statHariIni', hariIni);
        animateNumber('statAnak', anak);
        animateNumber('statDewasa', dewasa);
        animateNumber('statBulanIni', bulanIniCount);
        animateNumber('statTotal', total);

        // Render grafik
        renderChartHarian(allData);
        renderChartBulanan(allData);
        renderChartRuangan(allData);
        renderChartRisiko(allData);
    }
    // Animasi angka naik
    function animateNumber(id, target) {
        const el = document.getElementById(id);
        const start = parseInt(el.textContent) || 0;
        const duration = 600;
        const startTime = performance.now();
        function step(currentTime) {
            const elapsed = currentTime - startTime;
            const progress = Math.min(elapsed / duration, 1);
            const eased = 1 - Math.pow(1 - progress, 3);
            el.textContent = Math.round(start + (target - start) * eased);
            if (progress < 1) requestAnimationFrame(step);
        }
        requestAnimationFrame(step);
    }
    // Grafik Harian
    function renderChartHarian(data) {
        const labels = [];
        const values = [];
        for (let i = 6; i >= 0; i--) {
            const d = new Date();
            d.setDate(d.getDate() - i);
            const ds = toDateStr(d);
            labels.push(d.toLocaleDateString('id-ID', { weekday: 'short', day: 'numeric' }));
            values.push(data.filter(r => r.tanggal === ds).length);
        }
        if (chartHarian) chartHarian.destroy();
        chartHarian = new Chart(document.getElementById('chartHarian'), {
            type: 'bar',
            data: {
                labels,
                datasets: [{
                    label: 'Pengisian',
                    data: values,
                    backgroundColor: 'rgba(13,71,161,0.7)',
                    borderRadius: 6,
                    borderSkipped: false,
                    maxBarThickness: 40
                }]
            },
            options: {
                responsive: true, maintainAspectRatio: false,
                plugins: { legend: { display: false } },
                scales: {
                    y: { beginAtZero: true, ticks: { stepSize: 1, font: { size: 11 } }, grid: { color: '#f1f5f9' } },
                    x: { ticks: { font: { size: 11 } }, grid: { display: false } }
                }
            }
        });
    }
    // Grafik Bulanan
    function renderChartBulanan(data) {
        const labels = [];
        const values = [];
        const bulanNames = ['Jan', 'Feb', 'Mar', 'Apr', 'Mei', 'Jun', 'Jul', 'Agu', 'Sep', 'Okt', 'Nov', 'Des'];
        for (let i = 5; i >= 0; i--) {
            const d = new Date();
            d.setMonth(d.getMonth() - i);
            const prefix = d.getFullYear() + '-' + String(d.getMonth() + 1).padStart(2, '0');
            labels.push(bulanNames[d.getMonth()] + ' ' + d.getFullYear());
            values.push(data.filter(r => r.tanggal && r.tanggal.startsWith(prefix)).length);
        }
        if (chartBulanan) chartBulanan.destroy();
        chartBulanan = new Chart(document.getElementById('chartBulanan'), {
            type: 'line',
            data: {
                labels,
                datasets: [{
                    label: 'Pengisian',
                    data: values,
                    borderColor: '#0d47a1',
                    backgroundColor: 'rgba(13,71,161,0.1)',
                    fill: true,
                    tension: 0.4,
                    pointBackgroundColor: '#0d47a1',
                    pointRadius: 5,
                    pointHoverRadius: 7
                }]
            },
            options: {
                responsive: true, maintainAspectRatio: false,
                plugins: { legend: { display: false } },
                scales: {
                    y: { beginAtZero: true, ticks: { stepSize: 1, font: { size: 11 } }, grid: { color: '#f1f5f9' } },
                    x: { ticks: { font: { size: 11 } }, grid: { display: false } }
                }
            }
        });
    }
    // Grafik Berdasarkan Ruangan
    function renderChartRuangan(data) {
        const ruanganCount = {};
        data.forEach(d => {
            const r = d.ruangan || 'Lainnya';
            ruanganCount[r] = (ruanganCount[r] || 0) + 1;
        });
        const sorted = Object.entries(ruanganCount).sort((a, b) => b[1] - a[1]).slice(0, 8);
        const labels = sorted.map(s => s[0]);
        const values = sorted.map(s => s[1]);
        const colors = ['#0d47a1', '#1976d2', '#42a5f5', '#00897b', '#26a69a', '#f57f17', '#ff9800', '#ef6c00'];
        if (chartRuangan) chartRuangan.destroy();
        chartRuangan = new Chart(document.getElementById('chartRuangan'), {
            type: 'bar',
            data: {
                labels,
                datasets: [{
                    label: 'Jumlah',
                    data: values,
                    backgroundColor: colors.slice(0, labels.length),
                    borderRadius: 6,
                    borderSkipped: false,
                    maxBarThickness: 36
                }]
            },
            options: {
                indexAxis: 'y',
                responsive: true, maintainAspectRatio: false,
                plugins: { legend: { display: false } },
                scales: {
                    x: { beginAtZero: true, ticks: { stepSize: 1, font: { size: 11 } }, grid: { color: '#f1f5f9' } },
                    y: { ticks: { font: { size: 11 } }, grid: { display: false } }
                }
            }
        });
    }
    // Grafik Warna Risiko
    function renderChartRisiko(data) {
        const counts = { green: 0, yellow: 0, orange: 0, red: 0 };
        data.forEach(d => {
            const c = d.kategoriColor || 'green';
            if (counts[c] !== undefined) counts[c]++;
        });
        if (chartRisiko) chartRisiko.destroy();
        chartRisiko = new Chart(document.getElementById('chartRisiko'), {
            type: 'doughnut',
            data: {
                labels: ['Risiko Rendah', 'Risiko Sedang', 'Risiko Tinggi', 'Risiko Kritis'],
                datasets: [{
                    data: [counts.green, counts.yellow, counts.orange, counts.red],
                    backgroundColor: ['#2e7d32', '#f57f17', '#ef6c00', '#c62828'],
                    borderWidth: 3,
                    borderColor: '#fff',
                    hoverOffset: 8
                }]
            },
            options: {
                responsive: true, maintainAspectRatio: false,
                cutout: '60%',
                plugins: {
                    legend: { position: 'bottom', labels: { padding: 16, usePointStyle: true, pointStyle: 'circle', font: { size: 12 } } }
                }
            }
        });
    }

    /* ============================================
       data.js - Tabel Data Pengisian
       ============================================ */
    function getFilteredData() {
        let data = getAllData();
        const search = (document.getElementById('filterSearch').value || '').toLowerCase();
        const dari = document.getElementById('filterDari').value;
        const sampai = document.getElementById('filterSampai').value;
        const ruangan = document.getElementById('filterRuangan').value;
        const jenis = document.getElementById('filterJenis').value;
        if (search) data = data.filter(d => (d.nama || '').toLowerCase().includes(search) || (d.rm || '').toLowerCase().includes(search));
        if (dari) data = data.filter(d => d.tanggal >= dari);
        if (sampai) data = data.filter(d => d.tanggal <= sampai);
        if (ruangan) data = data.filter(d => d.ruangan === ruangan);
        if (jenis) data = data.filter(d => d.jenis === jenis);
        return data;
    }
    function renderDataTable() {
        const filtered = getFilteredData();
        const total = filtered.length;
        const perPage = CONFIG.ITEMS_PER_PAGE;
        const totalPages = Math.max(1, Math.ceil(total / perPage));
        if (dataPage > totalPages) dataPage = totalPages;
        const start = (dataPage - 1) * perPage;
        const pageData = filtered.slice(start, start + perPage);
        const tbody = document.getElementById('dataTableBody');
        if (pageData.length === 0) {
            tbody.innerHTML = '<tr><td colspan="10" class="text-center py-4 text-muted">Tidak ada data yang sesuai filter</td></tr>';
        } else {
            tbody.innerHTML = pageData.map((d, i) => `
                <tr>
                    <td>${start + i + 1}</td>
                    <td>${toDisplayDate(d.tanggal)}</td>
                    <td>${toDisplayTime(d.jam)}</td>
                    <td><strong>${d.rm}</strong></td>
                    <td>${d.nama}</td>
                    <td>${d.ruangan}</td>
                    <td><span class="badge bg-secondary bg-opacity-10 text-secondary">${d.jenis}</span></td>
                    <td><strong>${d.totalSkor}</strong></td>
                    <td><span class="badge-ews ${d.kategoriColor}">${d.kategori}</span></td>
                    <td class="no-print">
                        <button class="btn btn-sm btn-outline-danger" onclick="hapusData('${d.timestamp}','${d.jenis}')" title="Hapus">
                            <i class="fas fa-trash-alt"></i>
                        </button>
                    </td>
                </tr>
            `).join('');
        }
        document.getElementById('dataInfo').textContent = `Menampilkan ${pageData.length} dari ${total} data`;
        // Pagination
        const pag = document.getElementById('dataPagination');
        let pagHtml = '';
        pagHtml += `<li class="page-item ${dataPage === 1 ? 'disabled' : ''}"><a class="page-link" onclick="goPage(${dataPage - 1})">&laquo;</a></li>`;
        for (let p = 1; p <= totalPages; p++) {
            if (totalPages > 7 && p > 2 && p < totalPages - 1 && Math.abs(p - dataPage) > 1) {
                if (p === 3 || p === totalPages - 2) pagHtml += '<li class="page-item disabled"><span class="page-link">...</span></li>';
                continue;
            }
            pagHtml += `<li class="page-item ${p === dataPage ? 'active' : ''}"><a class="page-link" onclick="goPage(${p})">${p}</a></li>`;
        }
        pagHtml += `<li class="page-item ${dataPage === totalPages ? 'disabled' : ''}"><a class="page-link" onclick="goPage(${dataPage + 1})">&raquo;</a></li>`;
        pag.innerHTML = pagHtml;
    }
    function goPage(p) {
        dataPage = p;
        renderDataTable();
    }
    // Event filter
    ['filterSearch', 'filterDari', 'filterSampai', 'filterRuangan', 'filterJenis'].forEach(id => {
        document.getElementById(id).addEventListener(id === 'filterSearch' ? 'input' : 'change', () => {
            dataPage = 1;
            renderDataTable();
        });
    });
    // Hapus data
    function hapusData(timestamp, jenis) {
        Swal.fire({
            title: 'Hapus Data?',
            text: 'Data yang dihapus tidak dapat dikembalikan.',
            icon: 'warning',
            showCancelButton: true,
            confirmButtonColor: '#c62828',
            confirmButtonText: 'Ya, Hapus',
            cancelButtonText: 'Batal'
        }).then(result => {
            if (result.isConfirmed) {
                if (jenis === 'Anak') {
                    const arr = getDataAnak().filter(d => d.timestamp !== timestamp);
                    saveDataAnak(arr);
                } else {
                    const arr = getDataDewasa().filter(d => d.timestamp !== timestamp);
                    saveDataDewasa(arr);
                }
                renderDataTable();
                Swal.fire({ icon: 'success', title: 'Dihapus', text: 'Data berhasil dihapus.', timer: 1200, showConfirmButton: false });
            }
        });
    }
    // Export Excel (CSV dengan BOM)
    function exportExcel() {
        const data = getFilteredData();
        if (data.length === 0) { Swal.fire({ icon: 'info', title: 'Info', text: 'Tidak ada data untuk diekspor.' }); return; }
        const BOM = '\uFEFF';
        let csv = 'No,Tanggal,Jam,No RM,Nama,Umur,Jenis Kelamin,Ruangan,Dokter,Perawat,Jenis EWS,Total Skor,Kategori,Rekomendasi,Petugas Input\n';
        data.forEach((d, i) => {
            csv += `${i+1},"${toDisplayDate(d.tanggal)}","${d.jam}","${d.rm}","${d.nama}","${d.umur || '-'}","${d.jk}","${d.ruangan}","${d.dokter}","${d.perawat}","${d.jenis}",${d.totalSkor},"${d.kategori}","${(d.rekomendasi || '').replace(/"/g, '""')}","${d.petugasInput}"\n`;
        });
        const blob = new Blob([BOM + csv], { type: 'text/csv;charset=utf-8;' });
        const link = document.createElement('a');
        link.href = URL.createObjectURL(blob);
        link.download = `Data_EWS_${toDateStr(new Date())}.csv`;
        link.click();
        Swal.fire({ icon: 'success', title: 'Berhasil', text: 'File Excel berhasil diunduh.', timer: 1500, showConfirmButton: false });
    }
    // Export PDF (via print)
    function exportPDF() {
        const data = getFilteredData();
        if (data.length === 0) { Swal.fire({ icon: 'info', title: 'Info', text: 'Tidak ada data untuk diekspor.' }); return; }
        // Buat jendela cetak
        let html = `<!DOCTYPE html><html><head><meta charset="UTF-8"><title>Data EWS</title>
        <style>body{font-family:Arial,sans-serif;font-size:11px;padding:20px}
        h2{color:#0d47a1;margin-bottom:4px}p{color:#666;margin-bottom:16px}
        table{width:100%;border-collapse:collapse;margin-top:8px}
        th,td{border:1px solid #ddd;padding:6px 8px;text-align:left}
        th{background:#0d47a1;color:#fff;font-size:10px}
        .green{color:#2e7d32;font-weight:700}.yellow{color:#f57f17;font-weight:700}
        .orange{color:#ef6c00;font-weight:700}.red{color:#c62828;font-weight:700}</style></head><body>`;
        html += `<h2>${CONFIG.HOSPITAL_NAME}</h2><p>Laporan Data EWS - Dicetak: ${new Date().toLocaleString('id-ID')}</p>`;
        html += '<table><thead><tr><th>No</th><th>Tanggal</th><th>Jam</th><th>No RM</th><th>Nama</th><th>Ruangan</th><th>Jenis</th><th>Skor</th><th>Kategori</th></tr></thead><tbody>';
        data.forEach((d, i) => {
            html += `<tr><td>${i+1}</td><td>${toDisplayDate(d.tanggal)}</td><td>${d.jam}</td><td>${d.rm}</td><td>${d.nama}</td><td>${d.ruangan}</td><td>${d.jenis}</td><td>${d.totalSkor}</td><td class="${d.kategoriColor}">${d.kategori}</td></tr>`;
        });
        html += '</tbody></table></body></html>';
        const win = window.open('', '_blank');
        win.document.write(html);
        win.document.close();
        win.print();
    }
    function printData() { exportPDF(); }

    /* ============================================
       profil.js - Halaman Profil
       ============================================ */
    function loadProfil() {
        const allData = getAllData();
        document.getElementById('profilTotalInput').textContent = allData.length;
    }
    // Ganti password
    document.getElementById('formGantiPassword').addEventListener('submit', function(e) {
        e.preventDefault();
        const oldPass = document.getElementById('passOld').value;
        const newPass = document.getElementById('passNew').value;
        const confirmPass = document.getElementById('passConfirm').value;
        const session = JSON.parse(localStorage.getItem('ews_session') || '{}');
        const users = JSON.parse(localStorage.getItem('ews_users') || '[{"username":"01","password":"02","nama":"Petugas EWS"}]');
        const user = users.find(u => u.username === session.username);
        if (!user) { Swal.fire({ icon: 'error', title: 'Error', text: 'Sesi tidak valid.' }); return; }
        if (oldPass !== user.password) { Swal.fire({ icon: 'error', title: 'Gagal', text: 'Password lama salah.' }); return; }
        if (newPass.length < 2) { Swal.fire({ icon: 'error', title: 'Gagal', text: 'Password baru terlalu pendek.' }); return; }
        if (newPass !== confirmPass) { Swal.fire({ icon: 'error', title: 'Gagal', text: 'Konfirmasi password tidak cocok.' }); return; }
        user.password = newPass;
        localStorage.setItem('ews_users', JSON.stringify(users));
        document.getElementById('passOld').value = '';
        document.getElementById('passNew').value = '';
        document.getElementById('passConfirm').value = '';
        Swal.fire({ icon: 'success', title: 'Berhasil', text: 'Password berhasil diubah.', timer: 1500, showConfirmButton: false });
    });

    /* ============================================
       Seed Data Demo (hanya jika kosong)
       ============================================ */
    function seedDemoData() {
        if (getDataAnak().length > 0 || getDataDewasa().length > 0) return;
        const ruanganAnak = ['ICU Anak', 'Bangsal Anak 1', 'Bangsal Anak 2', 'Paviliun Anak', 'IGD'];
        const ruanganDewasa = ['ICU', 'Bangsal 1', 'Bangsal 2', 'Bangsal 3', 'Bangsal VIP', 'IGD', 'HCU'];
        const namaAnak = ['Anisa Rahma', 'Budi Santoso', 'Citra Dewi', 'Dimas Pratama', 'Eka Putri', 'Fajar Hidayat', 'Gita Nuraini'];
        const namaDewasa = ['Ahmad Suryadi', 'Beti Lestari', 'Cahyo Wibowo', 'Diana Permata', 'Eko Prasetyo', 'Fitri Handayani', 'Gunawan Saputra', 'Hari Wibisono', 'Ika Rahmawati'];
        const dokter = ['dr. Andi, Sp.A', 'dr. Budi, Sp.PD', 'dr. Citra, Sp.A', 'dr. Dina, Sp.B', 'dr. Eko, Sp.PD'];
        const perawat = ['Ns. Rina', 'Ns. Sari', 'Ns. Dewi', 'Ns. Rudi', 'Ns. Tono'];
        const kategoriColors = ['green', 'yellow', 'orange', 'red'];

        // Buat data 14 hari terakhir
        for (let day = 13; day >= 0; day--) {
            const d = new Date();
            d.setDate(d.getDate() - day);
            const ds = toDateStr(d);
            const jamOptions = ['06:00', '08:00', '10:00', '12:00', '14:00', '16:00', '18:00', '20:00', '22:00'];

            // 0-2 data anak per hari
            const anakCount = Math.floor(Math.random() * 3);
            for (let i = 0; i < anakCount; i++) {
                const skor = Math.floor(Math.random() * 12);
                let kat;
                if (skor <= 2) kat = KATEGORI_ANAK[0];
                else if (skor <= 4) kat = KATEGORI_ANAK[1];
                else if (skor <= 6) kat = KATEGORI_ANAK[2];
                else kat = KATEGORI_ANAK[3];
                const params = {};
                EWS_ANAK_PARAMS.forEach(p => { params[p.key] = { value: '-', score: 0 }; });
                const jam = jamOptions[Math.floor(Math.random() * jamOptions.length)];
                getDataAnak().push({
                    timestamp: new Date(d.getFullYear(), d.getMonth(), d.getDate(), parseInt(jam), 0).toISOString(),
                    rm: 'RM-A' + String(Math.floor(Math.random() * 9000) + 1000),
                    nama: namaAnak[Math.floor(Math.random() * namaAnak.length)],
                    tglLahir: '2022-03-15', umur: '3 tahun 0 bulan',
                    jk: Math.random() > 0.5 ? 'Laki-laki' : 'Perempuan',
                    ruangan: ruanganAnak[Math.floor(Math.random() * ruanganAnak.length)],
                    dokter: dokter[Math.floor(Math.random() * dokter.length)],
                    perawat: perawat[Math.floor(Math.random() * perawat.length)],
                    tanggal: ds, jam: jam,
                    params, totalSkor: skor,
                    kategori: kat.label, kategoriColor: kat.color,
                    rekomendasi: kat.rekom, petugasInput: '01'
                });
                saveDataAnak(getDataAnak());
            }

            // 0-3 data dewasa per hari
            const dewasaCount = Math.floor(Math.random() * 4);
            for (let i = 0; i < dewasaCount; i++) {
                const skor = Math.floor(Math.random() * 12);
                let kat;
                if (skor <= 4) kat = KATEGORI_DEWASA[0];
                else if (skor <= 6) kat = KATEGORI_DEWASA[1];
                else kat = KATEGORI_DEWASA[2];
                const params = {};
                EWS_DEWASA_PARAMS.forEach(p => { params[p.key] = { value: '-', score: 0 }; });
                const jam = jamOptions[Math.floor(Math.random() * jamOptions.length)];
                getDataDewasa().push({
                    timestamp: new Date(d.getFullYear(), d.getMonth(), d.getDate(), parseInt(jam), 0).toISOString(),
                    rm: 'RM-D' + String(Math.floor(Math.random() * 9000) + 1000),
                    nama: namaDewasa[Math.floor(Math.random() * namaDewasa.length)],
                    umur: String(Math.floor(Math.random() * 50) + 20),
                    jk: Math.random() > 0.5 ? 'Laki-laki' : 'Perempuan',
                    ruangan: ruanganDewasa[Math.floor(Math.random() * ruanganDewasa.length)],
                    dokter: dokter[Math.floor(Math.random() * dokter.length)],
                    perawat: perawat[Math.floor(Math.random() * perawat.length)],
                    tanggal: ds, jam: jam,
                    params, totalSkor: skor,
                    kategori: kat.label, kategoriColor: kat.color,
                    rekomendasi: kat.rekom, petugasInput: '01'
                });
                saveDataDewasa(getDataDewasa());
            }
        }
    }

    /* ============================================
       Inisialisasi Aplikasi
       ============================================ */
    document.addEventListener('DOMContentLoaded', function() {
        // Seed demo data
        seedDemoData();
        // Inisialisasi form EWS
        initEwsAnak();
        initEwsDewasa();
        // Cek session
        checkSession();
    });
    </script>
</body>
</html>

<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>كنزي كاش - نظام إدارة المحافظ الإلكترونية</title>
    <!-- Chart.js للرسوم البيانية -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>
    <!-- SheetJS لتصدير Excel -->
    <script src="https://cdn.sheetjs.com/xlsx-0.20.2/package/dist/xlsx.full.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Tahoma', sans-serif;
        }
        body {
            background: #f0f2f5;
            padding: 20px;
        }
        .container {
            max-width: 1400px;
            margin: auto;
        }
        /* header */
        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 25px;
            background: white;
            padding: 15px 25px;
            border-radius: 15px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.05);
        }
        h1 {
            color: #2c3e66;
            font-size: 1.8rem;
        }
        .logo {
            display: flex;
            align-items: center;
            gap: 10px;
        }
        .logo span {
            font-size: 1.8rem;
        }
        .user-badge {
            background: #1e3a5f;
            color: white;
            padding: 8px 15px;
            border-radius: 30px;
            font-weight: bold;
        }
        /* stats cards */
        .stats {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }
        .card {
            background: white;
            padding: 20px;
            border-radius: 20px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.05);
            text-align: center;
        }
        .card h3 {
            color: #4a5568;
            font-size: 1rem;
            margin-bottom: 10px;
        }
        .card .value {
            font-size: 2rem;
            font-weight: bold;
            color: #1e3a5f;
        }
        .alert-badge {
            background: #fee2e2;
            color: #b91c1c;
            padding: 5px 10px;
            border-radius: 20px;
            font-size: 0.8rem;
            margin-top: 8px;
            display: inline-block;
        }
        /* tabs */
        .tabs {
            display: flex;
            gap: 10px;
            margin-bottom: 25px;
            border-bottom: 2px solid #ddd;
            padding-bottom: 10px;
        }
        .tab-btn {
            background: none;
            border: none;
            padding: 10px 20px;
            font-size: 1rem;
            cursor: pointer;
            border-radius: 30px;
            transition: 0.2s;
            font-weight: bold;
        }
        .tab-btn.active {
            background: #1e3a5f;
            color: white;
        }
        .tab-content {
            display: none;
            background: white;
            border-radius: 20px;
            padding: 20px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.05);
        }
        .tab-content.active {
            display: block;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 15px;
        }
        th, td {
            border: 1px solid #e2e8f0;
            padding: 12px;
            text-align: center;
        }
        th {
            background: #eef2ff;
            color: #1e3a5f;
        }
        button {
            background: #1e3a5f;
            color: white;
            border: none;
            padding: 8px 16px;
            border-radius: 12px;
            cursor: pointer;
            margin: 5px;
        }
        button.danger {
            background: #b91c1c;
        }
        button.success {
            background: #2b6e3c;
        }
        input, select {
            padding: 8px;
            border-radius: 10px;
            border: 1px solid #ccc;
            margin: 5px;
        }
        .form-row {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            align-items: end;
            margin-bottom: 20px;
        }
        .log-area {
            background: #f8fafc;
            padding: 15px;
            border-radius: 15px;
            max-height: 200px;
            overflow-y: auto;
            font-family: monospace;
            font-size: 12px;
        }
        @media (max-width: 700px) {
            th, td { font-size: 12px; padding: 6px; }
        }
    </style>
</head>
<body>
<div class="container">
    <div class="header">
        <div class="logo">
            <span>💰</span>
            <h1>كنزي كاش | Kenzy Cash</h1>
        </div>
        <div class="user-badge">👤 Admin (صلاحية كاملة)</div>
    </div>

    <!-- إحصائيات سريعة -->
    <div class="stats" id="statsArea"></div>

    <!-- تبويبات -->
    <div class="tabs">
        <button class="tab-btn active" data-tab="wallets">📂 المحافظ</button>
        <button class="tab-btn" data-tab="transactions">💸 العمليات</button>
        <button class="tab-btn" data-tab="reports">📊 التقارير</button>
        <button class="tab-btn" data-tab="logs">📜 سجل العمليات (Logs)</button>
    </div>

    <!-- محتوى المحافظ -->
    <div id="wallets" class="tab-content active">
        <h3>➕ إضافة محفظة جديدة</h3>
        <div class="form-row">
            <input type="text" id="walletName" placeholder="اسم المحفظة (مثال: محمد علي)">
            <input type="text" id="walletPhone" placeholder="رقم الموبايل">
            <input type="number" id="dailyWithdrawLimit" placeholder="حد السحب اليومي (جنيه)">
            <input type="number" id="dailyDepositLimit" placeholder="حد الإيداع اليومي">
            <input type="number" id="balanceLimit" placeholder="حد الرصيد الأقصى">
            <button id="addWalletBtn">➕ إضافة محفظة</button>
        </div>
        <h3>📋 قائمة المحافظ</h3>
        <table id="walletsTable">
            <thead>
                <tr><th>المعرف</th><th>المحفظة</th><th>الموبايل</th><th>الرصيد الحالي</th><th>حد السحب/اليوم</th><th>حد الإيداع/اليوم</th><th>حد الرصيد</th><th>الحالة</th><th>إجراء</th></tr>
            </thead>
            <tbody></tbody>
        </table>
    </div>

    <!-- محتوى العمليات -->
    <div id="transactions" class="tab-content">
        <h3>➕ تسجيل عملية جديدة</h3>
        <div class="form-row">
            <select id="transWalletId"><option value="">اختر محفظة</option></select>
            <select id="transType"><option value="deposit">إيداع 💰</option><option value="withdraw">سحب 💸</option><option value="transfer">تحويل 🔄</option></select>
            <input type="number" id="transAmount" placeholder="المبلغ">
            <input type="text" id="transNote" placeholder="مندوب / ملاحظة">
            <button id="doTransactionBtn">تنفيذ العملية</button>
        </div>
        <h3>📜 آخر العمليات</h3>
        <table id="transactionsTable">
            <thead><tr><th>رقم العملية</th><th>المحفظة</th><th>النوع</th><th>المبلغ</th><th>التاريخ</th><th>المندوب</th><th>الحالة</th></tr></thead>
            <tbody></tbody>
        </table>
    </div>

    <!-- محتوى التقارير + تصدير -->
    <div id="reports" class="tab-content">
        <h3>📈 تقارير شاملة - كنزي كاش</h3>
        <div class="form-row">
            <input type="date" id="reportStartDate">
            <input type="date" id="reportEndDate">
            <select id="reportWalletFilter"><option value="all">كل المحافظ</option></select>
            <select id="reportTypeFilter"><option value="all">كل العمليات</option><option value="deposit">إيداع</option><option value="withdraw">سحب</option><option value="transfer">تحويل</option></select>
            <button id="generateReportBtn">عرض التقرير</button>
            <button id="exportExcelBtn" class="success">📎 تصدير Excel</button>
        </div>
        <div id="reportSummary" style="background:#eef2ff; padding:15px; border-radius:15px; margin-bottom:15px;"></div>
        <table id="reportTable"><thead><tr><th>رقم العملية</th><th>المحفظة</th><th>النوع</th><th>المبلغ</th><th>التاريخ</th><th>المندوب</th><th>الحالة</th></tr></thead><tbody></tbody></table>
    </div>

    <!-- سجل العمليات (Logs) -->
    <div id="logs" class="tab-content">
        <h3>🔍 سجل جميع الإجراءات (Admin & System)</h3>
        <div class="log-area" id="logMessages"></div>
        <button id="clearLogsBtn" class="danger">مسح السجل</button>
    </div>
</div>

<script>
    // ---------- البيانات الأولية ----------
    let wallets = [];
    let transactions = [];
    let systemLogs = [];

    // وظيفة إضافة سجل
    function addLog(message, type = "info") {
        let now = new Date().toLocaleString('ar-EG');
        systemLogs.unshift(`[${now}] ${message}`);
        if (systemLogs.length > 100) systemLogs.pop();
        renderLogs();
    }

    // حفظ في localStorage
    function saveData() {
        localStorage.setItem('kenzycash_wallets', JSON.stringify(wallets));
        localStorage.setItem('kenzycash_transactions', JSON.stringify(transactions));
        localStorage.setItem('kenzycash_logs', JSON.stringify(systemLogs));
    }

    function loadData() {
        let storedWallets = localStorage.getItem('kenzycash_wallets');
        let storedTrans = localStorage.getItem('kenzycash_transactions');
        let storedLogs = localStorage.getItem('kenzycash_logs');
        if (storedWallets) wallets = JSON.parse(storedWallets);
        else {
            // بيانات افتراضية
            wallets = [
                { id: 1, name: "أحمد محمود", phone: "01001112222", balance: 25000, dailyWithdrawLimit: 30000, dailyDepositLimit: 50000, balanceLimit: 100000, status: "نشطة", todayWithdrawn: 0, todayDeposited: 0 },
                { id: 2, name: "سارة خالد", phone: "01003334444", balance: 80000, dailyWithdrawLimit: 30000, dailyDepositLimit: 40000, balanceLimit: 100000, status: "نشطة", todayWithdrawn: 20000, todayDeposited: 5000 }
            ];
        }
        if (storedTrans) transactions = JSON.parse(storedTrans);
        else {
            transactions = [
                { id: 1001, walletId: 1, type: "deposit", amount: 10000, date: "2026-04-10T10:00:00", note: "مندوب علي", status: "ناجحة" },
                { id: 1002, walletId: 1, type: "withdraw", amount: 5000, date: "2026-04-10T12:00:00", note: "مندوب أحمد", status: "ناجحة" },
                { id: 1003, walletId: 2, type: "withdraw", amount: 25000, date: "2026-04-10T09:00:00", note: "مندوب سارة", status: "ناجحة" }
            ];
        }
        if (storedLogs) systemLogs = JSON.parse(storedLogs);
        else addLog("تم تحميل نظام كنزي كاش - بدء الجلسة", "info");
    }

    // تحديث كل الواجهات
    function refreshAll() {
        renderStats();
        renderWalletsTable();
        renderTransactionsTable();
        updateWalletSelect();
        updateReportWalletFilter();
        renderLogs();
        saveData();
    }

    // الإحصائيات
    function renderStats() {
        let totalBalance = wallets.reduce((sum, w) => sum + w.balance, 0);
        let today = new Date().toISOString().slice(0,10);
        let todayTransactions = transactions.filter(t => t.date.startsWith(today));
        let activeWallets = wallets.filter(w => w.status === "نشطة").length;
        let limitViolations = 0;
        wallets.forEach(w => {
            if (w.balance > w.balanceLimit) limitViolations++;
            if (w.todayWithdrawn > w.dailyWithdrawLimit) limitViolations++;
        });
        document.getElementById('statsArea').innerHTML = `
            <div class="card"><h3>💰 إجمالي الرصيد</h3><div class="value">${totalBalance.toLocaleString()} ج.م</div></div>
            <div class="card"><h3>🔄 عدد العمليات اليوم</h3><div class="value">${todayTransactions.length}</div></div>
            <div class="card"><h3>📱 محافظ نشطة</h3><div class="value">${activeWallets}</div></div>
            <div class="card"><h3>⚠️ تنبيهات الليمت</h3><div class="value">${limitViolations}</div><div class="alert-badge">تجاوزات</div></div>
        `;
    }

    // عرض المحافظ
    function renderWalletsTable() {
        let tbody = document.querySelector('#walletsTable tbody');
        tbody.innerHTML = '';
        wallets.forEach(w => {
            let row = tbody.insertRow();
            row.insertCell(0).innerText = w.id;
            row.insertCell(1).innerText = w.name;
            row.insertCell(2).innerText = w.phone;
            row.insertCell(3).innerText = w.balance.toLocaleString();
            row.insertCell(4).innerText = `${w.dailyWithdrawLimit} (اليوم:${w.todayWithdrawn || 0})`;
            row.insertCell(5).innerText = `${w.dailyDepositLimit} (اليوم:${w.todayDeposited || 0})`;
            row.insertCell(6).innerText = w.balanceLimit;
            row.insertCell(7).innerText = w.status;
            let btn = document.createElement('button');
            btn.innerText = w.status === 'نشطة' ? 'إيقاف' : 'تفعيل';
            btn.onclick = () => toggleWalletStatus(w.id);
            row.insertCell(8).appendChild(btn);
        });
    }

    function toggleWalletStatus(id) {
        let wallet = wallets.find(w => w.id === id);
        if (wallet) {
            wallet.status = wallet.status === 'نشطة' ? 'موقوفة' : 'نشطة';
            addLog(`تغيير حالة المحفظة ${wallet.name} إلى ${wallet.status}`, "admin");
            refreshAll();
        }
    }

    // إضافة محفظة
    document.getElementById('addWalletBtn').onclick = () => {
        let name = document.getElementById('walletName').value.trim();
        let phone = document.getElementById('walletPhone').value.trim();
        let dailyWithdrawLimit = parseFloat(document.getElementById('dailyWithdrawLimit').value);
        let dailyDepositLimit = parseFloat(document.getElementById('dailyDepositLimit').value);
        let balanceLimit = parseFloat(document.getElementById('balanceLimit').value);
        if (!name || !phone || isNaN(dailyWithdrawLimit) || isNaN(dailyDepositLimit) || isNaN(balanceLimit)) {
            alert("الرجاء ملء جميع الحقول بشكل صحيح");
            return;
        }
        let newId = wallets.length ? Math.max(...wallets.map(w=>w.id)) + 1 : 3;
        wallets.push({
            id: newId, name, phone, balance: 0, dailyWithdrawLimit, dailyDepositLimit, balanceLimit,
            status: "نشطة", todayWithdrawn: 0, todayDeposited: 0
        });
        addLog(`تم إنشاء محفظة جديدة: ${name} (${phone})`);
        refreshAll();
        document.getElementById('walletName').value = '';
        document.getElementById('walletPhone').value = '';
    };

    // تحديث قائمة المحافظ في العمليات
    function updateWalletSelect() {
        let select = document.getElementById('transWalletId');
        select.innerHTML = '<option value="">اختر محفظة</option>';
        wallets.forEach(w => {
            if (w.status === 'نشطة')
                select.innerHTML += `<option value="${w.id}">${w.name} (رصيد: ${w.balance})</option>`;
        });
    }

    // تنفيذ العملية مع فحص الليمت
    function checkLimits(wallet, type, amount) {
        let today = new Date().toISOString().slice(0,10);
        // تجديد حدود اليوم (إذا كان اليوم مختلف) - تنفيذ بسيط: نقارن تاريخ آخر عملية? لكن نستخدم كاش todayWithdrawn/todayDeposited يتم تحديثه عند العملية.
        if (type === 'withdraw') {
            let newWithdrawTotal = (wallet.todayWithdrawn || 0) + amount;
            if (newWithdrawTotal > wallet.dailyWithdrawLimit) return false;
            if (amount > wallet.balance) return false;
        }
        if (type === 'deposit') {
            let newDepositTotal = (wallet.todayDeposited || 0) + amount;
            if (newDepositTotal > wallet.dailyDepositLimit) return false;
            let newBalance = wallet.balance + amount;
            if (newBalance > wallet.balanceLimit) return false;
        }
        if (type === 'transfer') {
            // تحويل: نخفض من الرصيد مع مراعاة حد السحب اليومي
            let newWithdrawTotal = (wallet.todayWithdrawn || 0) + amount;
            if (newWithdrawTotal > wallet.dailyWithdrawLimit) return false;
            if (amount > wallet.balance) return false;
            // نقل بين محافظ - نفترض التحويل لمحفظة أخرى (مبسط) -> سننفذ لاحقًا
        }
        return true;
    }

    document.getElementById('doTransactionBtn').onclick = () => {
        let walletId = parseInt(document.getElementById('transWalletId').value);
        let type = document.getElementById('transType').value;
        let amount = parseFloat(document.getElementById('transAmount').value);
        let note = document.getElementById('transNote').value || "مندوب النظام";
        if (!walletId || isNaN(amount) || amount <= 0) {
            alert("بيانات غير صحيحة");
            return;
        }
        let wallet = wallets.find(w => w.id === walletId);
        if (!wallet || wallet.status !== 'نشطة') {
            alert("المحفظة غير نشطة أو غير موجودة");
            return;
        }

        let allowed = true;
        let errorMsg = "";
        if (type === 'withdraw') {
            allowed = checkLimits(wallet, 'withdraw', amount);
            if (!allowed) errorMsg = "تجاوز حد السحب اليومي أو رصيد غير كافٍ";
        } else if (type === 'deposit') {
            allowed = checkLimits(wallet, 'deposit', amount);
            if (!allowed) errorMsg = "تجاوز حد الإيداع اليومي أو حد الرصيد الأقصى";
        } else if (type === 'transfer') {
            allowed = checkLimits(wallet, 'transfer', amount);
            if (!allowed) errorMsg = "تجاوز حد السحب أو رصيد غير كافٍ للتحويل";
        }

        let status = allowed ? "ناجحة" : "فاشلة";
        let transId = transactions.length ? Math.max(...transactions.map(t=>t.id)) + 1 : 2000;
        let newTrans = {
            id: transId,
            walletId: wallet.id,
            type: type,
            amount: amount,
            date: new Date().toISOString(),
            note: note,
            status: status
        };
        if (allowed) {
            // تحديث الرصيد والحدود اليومية
            if (type === 'withdraw') {
                wallet.balance -= amount;
                wallet.todayWithdrawn = (wallet.todayWithdrawn || 0) + amount;
            } else if (type === 'deposit') {
                wallet.balance += amount;
                wallet.todayDeposited = (wallet.todayDeposited || 0) + amount;
            } else if (type === 'transfer') {
                // تبسيط: نخصم من المحفظة الحالية (نفترض التحويل لمحفظة أخرى، لكن النظام كامل يمكن إضافته)
                wallet.balance -= amount;
                wallet.todayWithdrawn = (wallet.todayWithdrawn || 0) + amount;
                newTrans.note += " (محول إلى محفظة أخرى)";
            }
            addLog(`عملية ${type === 'deposit' ? 'إيداع' : type === 'withdraw' ? 'سحب' : 'تحويل'} بمبلغ ${amount} ج.م للمحفظة ${wallet.name} - ${status}`);
        } else {
            addLog(`رفض عملية ${type} للمحفظة ${wallet.name} بسبب: ${errorMsg}`);
        }
        transactions.unshift(newTrans);
        refreshAll();
        document.getElementById('transAmount').value = '';
    };

    function renderTransactionsTable() {
        let tbody = document.querySelector('#transactionsTable tbody');
        tbody.innerHTML = '';
        transactions.slice(0, 50).forEach(t => {
            let wallet = wallets.find(w => w.id === t.walletId);
            let walletName = wallet ? wallet.name : "محذوف";
            let row = tbody.insertRow();
            row.insertCell(0).innerText = t.id;
            row.insertCell(1).innerText = walletName;
            row.insertCell(2).innerText = t.type === 'deposit' ? 'إيداع' : t.type === 'withdraw' ? 'سحب' : 'تحويل';
            row.insertCell(3).innerText = t.amount;
            row.insertCell(4).innerText = new Date(t.date).toLocaleString('ar-EG');
            row.insertCell(5).innerText = t.note;
            row.inser
          

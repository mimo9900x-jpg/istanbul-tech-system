[index.html](https://github.com/user-attachments/files/30089309/index.html)
# istanbul-tech-system
Initial upload
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <title>Istanbul Tech System</title>
    <style>
        body { font-family: 'Segoe UI', sans-serif; background: #f0f2f5; margin: 0; padding: 20px; }
        .container { max-width: 450px; margin: auto; background: white; padding: 25px; border-radius: 20px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); transition: 0.3s; }
        h2 { text-align: center; color: #28a745; margin-bottom: 25px; }
        input, button { width: 100%; padding: 15px; margin: 8px 0; border: 1px solid #ddd; border-radius: 12px; box-sizing: border-box; font-size: 16px; }
        .row { display: flex; gap: 10px; }
        .row input { width: 50%; }
        button { background: #28a745; color: white; border: none; font-weight: bold; cursor: pointer; }
        #netLabel { text-align:center; font-weight:bold; font-size: 20px; margin-bottom:10px; padding: 5px; }
        .msg-modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); z-index: 2000; justify-content: center; align-items: center; }
        .msg-content { background: white; padding: 30px; border-radius: 25px; width: 85%; max-width: 350px; text-align: center; box-shadow: 0 15px 35px rgba(0,0,0,0.2); animation: pop 0.3s ease-out; }
        @keyframes pop { from { transform: scale(0.8); opacity: 0; } to { transform: scale(1); opacity: 1; } }
    </style>
</head>
<body>

<div class="container" id="mainContainer">
    <h2>İstanbul Tech System</h2>
    <div style="display: flex; gap: 10px;">
        <button style="background:#6c757d;" onclick="openModal('settingsModal')">⚙️ الإعدادات</button>
        <button style="background:#17a2b8;" onclick="openModal('aboutModal')">ℹ️ حول التطبيق</button>
    </div>
    <input type="tel" id="mobileNumber" placeholder="رقم الزبون" inputmode="numeric" oninput="detectNetwork()">
    <div id="netLabel">---</div>
    <div class="row">
        <input type="number" id="amount" placeholder="المبلغ" inputmode="numeric">
        <input type="number" id="price" placeholder="السعر" inputmode="numeric">
    </div>
    <button onclick="executeTransfer()">تحويل وتسجيل</button>
    <div id="statusBtns" style="display:none; text-align:center; margin-top:10px; padding:10px; border:1px solid #28a745; border-radius:12px;">
        <p>هل وصلت رسالة الشركة؟</p>
        <button onclick="confirmStatus(true)" style="background:#28a745; width:45%; display:inline-block;">نعم ✅</button>
        <button onclick="confirmStatus(false)" style="background:#dc3545; width:45%; display:inline-block;">لا ❌</button>
    </div>
    <button onclick="openLog()" style="background:#007bff;">📋 سجل التحويلات</button>
    <button onclick="exportToExcel()" style="background:#17a2b8;">📊 تصدير Excel</button>
    <button onclick="sendDailyReport()" style="background:#ffc107; color:black;">📤 إرسال التقرير للواتساب</button>
</div>

<div id="alertModal" class="msg-modal"><div class="msg-content"><h3 id="alertTitle" style="color:#28a745;">تنبيه</h3><p id="alertMsg"></p><button onclick="closeModal('alertModal')" style="background:#28a745;">موافق</button></div></div>
<div id="aboutModal" class="msg-modal"><div class="msg-content"><h3>ℹ️ حول التطبيق</h3><p style="line-height: 1.8;"><b>Istanbul Tech System</b><br>تصميم: م. محمد محمد<br>📞 0997840256</p><button onclick="closeModal('aboutModal')" style="background:#333;">إغلاق</button></div></div>
<div id="settingsModal" class="msg-modal"><div class="msg-content"><h3>الإعدادات</h3><input type="password" id="s_pin" placeholder="كود سيريتل"><input type="password" id="m_pin" placeholder="كود MTN"><input type="text" id="whatsapp_num" placeholder="رقم الواتساب"><button onclick="saveSettings()">حفظ</button><button onclick="closeModal('settingsModal')" style="background:#333;">إغلاق</button></div></div>
<div id="logModal" class="msg-modal"><div class="msg-content" style="max-height: 80vh; overflow-y: auto;"><h3>سجل التحويلات</h3><div id="logList" style="text-align: right; margin-bottom: 15px;"></div><button onclick="clearLogs()" style="background:#dc3545;">مسح السجل</button><button onclick="closeModal('logModal')" style="background:#333;">إغلاق</button></div></div>

<script>
    let currentTransaction = null;
    window.onload = () => {
        document.getElementById('s_pin').value = localStorage.getItem('s_pin') || '';
        document.getElementById('m_pin').value = localStorage.getItem('m_pin') || '';
        document.getElementById('whatsapp_num').value = localStorage.getItem('whatsapp_num') || '';
    };

    function showMsg(title, msg) { document.getElementById('alertTitle').innerText = title; document.getElementById('alertMsg').innerText = msg; openModal('alertModal'); }
    function openModal(id) { document.getElementById(id).style.display = 'flex'; }
    function closeModal(id) { document.getElementById(id).style.display = 'none'; }
    function saveSettings() { localStorage.setItem('s_pin', document.getElementById('s_pin').value); localStorage.setItem('m_pin', document.getElementById('m_pin').value); localStorage.setItem('whatsapp_num', document.getElementById('whatsapp_num').value); closeModal('settingsModal'); showMsg("تم", "✅ تم الحفظ"); }

    function detectNetwork() { 
        const num = document.getElementById('mobileNumber').value;
        const label = document.getElementById('netLabel');
        const cont = document.getElementById('mainContainer');
        if (num.startsWith('093') || num.startsWith('099') || num.startsWith('098')) { label.innerText = "سيريتل"; label.style.color = "red"; cont.style.border = "2px solid red"; label.dataset.net = "syriatel"; } 
        else if (num.startsWith('095') || num.startsWith('096') || num.startsWith('094')) { label.innerText = "MTN"; label.style.color = "#d39e00"; cont.style.border = "2px solid #d39e00"; label.dataset.net = "mtn"; } 
        else { label.innerText = "---"; label.style.color = "black"; cont.style.border = "none"; label.dataset.net = ""; }
    }

    function executeTransfer() {
        const net = document.getElementById('netLabel').dataset.net;
        const pin = localStorage.getItem(net === 'syriatel' ? 's_pin' : 'm_pin');
        const amt = document.getElementById('amount').value;
        const price = document.getElementById('price').value;
        const num = document.getElementById('mobileNumber').value;
        if(!net || !pin || !amt || !num || !price) { showMsg("خطأ", "⚠️ يرجى تعبئة كافة البيانات"); return; }
        currentTransaction = { num, amt, price, net };
        window.location.href = (net === 'syriatel') ? `tel:*150*1*${pin}*${amt}*${num}*${num}%23` : `tel:*150*${pin}*${num}*${amt}%23`;
        document.getElementById('statusBtns').style.display = 'block';
    }

    function confirmStatus(isReceived) {
        let history = JSON.parse(localStorage.getItem('daily_history') || '[]');
        history.push({ ...currentTransaction, status: isReceived ? 'وصلت ✅' : 'لم تصل ❌', time: new Date().toLocaleTimeString() });
        localStorage.setItem('daily_history', JSON.stringify(history));
        document.getElementById('statusBtns').style.display = 'none';
        document.getElementById('mobileNumber').value = ''; document.getElementById('amount').value = ''; document.getElementById('price').value = ''; detectNetwork();
        showMsg("نجاح", "تم تسجيل العملية!");
    }

    function openLog() {
        const history = JSON.parse(localStorage.getItem('daily_history') || '[]');
        const logList = document.getElementById('logList');
        logList.innerHTML = history.length === 0 ? "لا توجد عمليات" : "";
        history.forEach(i => { logList.innerHTML += `<div style="font-size:12px;">${i.time} | ${i.net} | ${i.num} | ${i.amt} | السعر: ${i.price} | ${i.status}</div><hr>`; });
        openModal('logModal');
    }

    function clearLogs() { if(confirm("مسح السجل؟")) { localStorage.removeItem('daily_history'); closeModal('logModal'); } }

    function exportToExcel() {
        const history = JSON.parse(localStorage.getItem('daily_history') || '[]');
        if (history.length === 0) { showMsg("تنبيه", "لا توجد بيانات"); return; }
        let csv = "الوقت,الشبكة,الرقم,المبلغ,السعر,الحالة\n";
        let total = 0;
        history.forEach(i => { csv += `${i.time},${i.net},${i.num},${i.amt},${i.price},${i.status}\n`; total += parseFloat(i.price || 0); });
        csv += `,,, ,الإجمالي,${total}`;
        const blob = new Blob(["\ufeff" + csv], { type: 'text/csv;charset=utf-8;' });
        const link = document.createElement("a"); link.href = URL.createObjectURL(blob); link.download = "التقرير.csv"; link.click();
    }

    function sendDailyReport() {
        const history = JSON.parse(localStorage.getItem('daily_history') || '[]');
        const whatsappNum = localStorage.getItem('whatsapp_num');
        if (history.length === 0) { showMsg("تنبيه", "لا توجد عمليات"); return; }
        let report = "تقرير العمليات:\n"; let total = 0;
        history.forEach(item => { report += `${item.time} | ${item.net} | ${item.num} | ${item.amt} | السعر: ${item.price} | ${item.status}\n`; total += parseFloat(item.price || 0); });
        report += `\nإجمالي السعر اليوم: ${total}`;
        window.location.href = `https://wa.me/${whatsappNum}?text=${encodeURIComponent(report)}`;
    }
</script>
</body>
</html>

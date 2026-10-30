<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>服飾店訂貨與庫存管理系統</title>
    <!-- 引入 Tailwind CSS 讓介面簡潔美觀 -->
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-50 text-gray-800">

    <div class="max-w-7xl mx-auto p-4 md:p-6">
        <!-- 標題 -->
        <header class="mb-6 flex flex-col md:flex-row justify-between items-start md:items-center gap-4">
            <div>
                <h1 class="text-2xl font-bold text-gray-900">🧥 服飾店訂貨與庫存管理系統</h1>
                <p class="text-sm text-gray-500">韓國連線 / 檔口追蹤 / 庫存與韓國出貨清單管理</p>
            </div>
            <div class="flex gap-2">
                <button onclick="switchTab('order')" id="btn-order" class="px-4 py-2 bg-indigo-600 text-white rounded-lg text-sm font-medium shadow">訂貨與檔口管理</button>
                <button onclick="switchTab('inventory')" id="btn-inventory" class="px-4 py-2 bg-white text-gray-700 border border-gray-300 rounded-lg text-sm font-medium hover:bg-gray-100">庫存管理</button>
                <button onclick="switchTab('shipping')" id="btn-shipping" class="px-4 py-2 bg-white text-gray-700 border border-gray-300 rounded-lg text-sm font-medium hover:bg-gray-100">韓國出貨清單</button>
            </div>
        </header>

        <!-- 頁籤 1：訂貨與檔口管理 -->
        <section id="tab-order" class="space-y-6">
            <!-- 新增表單 -->
            <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-200">
                <h2 class="text-lg font-semibold mb-4 text-gray-800">➕ 新增訂單 / 商品記錄</h2>
                <form id="orderForm" onsubmit="addOrder(event)" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-6 gap-4">
                    <div>
                        <label class="block text-xs font-medium text-gray-600 mb-1">日期</label>
                        <input type="date" id="orderDate" required class="w-full border border-gray-300 rounded-lg px-3 py-2 text-sm focus:ring-2 focus:ring-indigo-500">
                    </div>
                    <div>
                        <label class="block text-xs font-medium text-gray-600 mb-1">檔口名稱</label>
                        <input type="text" id="boothName" placeholder="例如：APM / 檔口名稱" required class="w-full border border-gray-300 rounded-lg px-3 py-2 text-sm">
                    </div>
                    <div>
                        <label class="block text-xs font-medium text-gray-600 mb-1">商品名稱 / 顏色 / 尺寸</label>
                        <input type="text" id="itemName" placeholder="例如：自訂款毛衣 白F" required class="w-full border border-gray-300 rounded-lg px-3 py-2 text-sm">
                    </div>
                    <div>
                        <label class="block text-xs font-medium text-gray-600 mb-1">數量</label>
                        <input type="number" id="itemQty" value="1" min="1" required class="w-full border border-gray-300 rounded-lg px-3 py-2 text-sm">
                    </div>
                    <div>
                        <label class="block text-xs font-medium text-gray-600 mb-1">訂貨狀況</label>
                        <select id="itemStatus" class="w-full border border-gray-300 rounded-lg px-3 py-2 text-sm">
                            <option value="待訂">待訂</option>
                            <option value="已訂">已訂</option>
                            <option value="工作室">工作室</option>
                            <option value="已回台">已回台</option>
                        </select>
                    </div>
                    <div class="flex items-end">
                        <button type="submit" class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-medium py-2 px-4 rounded-lg text-sm transition">新增記錄</button>
                    </div>
                </form>
            </div>

            <!-- 列表與篩選 -->
            <div class="bg-white rounded-xl shadow-sm border border-gray-200 overflow-hidden">
                <div class="p-4 border-b border-gray-200 flex justify-between items-center flex-wrap gap-4">
                    <h2 class="text-lg font-semibold text-gray-800">📋 訂貨與檔口追蹤清單</h2>
                    <div class="flex gap-2">
                        <input type="text" id="searchOrder" placeholder="搜尋檔口或商品..." oninput="renderOrders()" class="border border-gray-300 rounded-lg px-3 py-1.5 text-sm">
                        <select id="filterStatus" onchange="renderOrders()" class="border border-gray-300 rounded-lg px-3 py-1.5 text-sm">
                            <option value="">全部狀態</option>
                            <option value="待訂">待訂</option>
                            <option value="已訂">已訂</option>
                            <option value="工作室">工作室</option>
                            <option value="已回台">已回台</option>
                        </select>
                    </div>
                </div>
                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse">
                        <thead>
                            <tr class="bg-gray-100 text-gray-600 text-xs uppercase tracking-wider">
                                <th class="p-3">日期</th>
                                <th class="p-3">檔口</th>
                                <th class="p-3">商品名稱</th>
                                <th class="p-3">數量</th>
                                <th class="p-3">狀況</th>
                                <th class="p-3 text-right">操作</th>
                            </tr>
                        </thead>
                        <tbody id="orderTableBody" class="divide-y divide-gray-200 text-sm">
                            <!-- 動態插入 -->
                        </tbody>
                    </table>
                </div>
            </div>
        </section>

        <!-- 頁籤 2：庫存管理 -->
        <section id="tab-inventory" class="hidden space-y-6">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-200">
                <h2 class="text-lg font-semibold mb-4 text-gray-800">📦 新增庫存商品</h2>
                <form id="inventoryForm" onsubmit="addInventory(event)" class="grid grid-cols-1 sm:grid-cols-3 gap-4">
                    <div>
                        <label class="block text-xs font-medium text-gray-600 mb-1">商品名稱/型號</label>
                        <input type="text" id="invName" placeholder="例如：氣質喇叭褲 黑S" required class="w-full border border-gray-300 rounded-lg px-3 py-2 text-sm">
                    </div>
                    <div>
                        <label class="block text-xs font-medium text-gray-600 mb-1">現有庫存數量</label>
                        <input type="number" id="invQty" value="0" min="0" required class="w-full border border-gray-300 rounded-lg px-3 py-2 text-sm">
                    </div>
                    <div class="flex items-end">
                        <button type="submit" class="w-full bg-emerald-600 hover:bg-emerald-700 text-white font-medium py-2 px-4 rounded-lg text-sm transition">新增庫存</button>
                    </div>
                </form>
            </div>

            <div class="bg-white rounded-xl shadow-sm border border-gray-200 overflow-hidden">
                <div class="p-4 border-b border-gray-200">
                    <h2 class="text-lg font-semibold text-gray-800">📊 現有庫存清單</h2>
                </div>
                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse">
                        <thead>
                            <tr class="bg-gray-100 text-gray-600 text-xs uppercase tracking-wider">
                                <th class="p-3">商品名稱</th>
                                <th class="p-3">庫存數量</th>
                                <th class="p-3 text-right">快速調整</th>
                                <th class="p-3 text-right">操作</th>
                            </tr>
                        </thead>
                        <tbody id="inventoryTableBody" class="divide-y divide-gray-200 text-sm">
                            <!-- 動態插入 -->
                        </tbody>
                    </table>
                </div>
            </div>
        </section>

        <!-- 頁籤 3：韓國出貨清單 -->
        <section id="tab-shipping" class="hidden space-y-6">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-200">
                <div class="flex justify-between items-center mb-4">
                    <h2 class="text-lg font-semibold text-gray-800">✈️ 韓國出貨包裹分流清單</h2>
                    <button onclick="generateShippingList()" class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg text-sm font-medium transition">一鍵同步「已回台/工作室」商品</button>
                </div>
                <p class="text-sm text-gray-500 mb-4">此處專門用來整理從韓國工作室出貨、打包或準備寄出的品項，方便與物流或小幫手對帳。</p>
            </div>

            <div class="bg-white rounded-xl shadow-sm border border-gray-200 overflow-hidden">
                <div class="p-4 border-b border-gray-200 flex justify-between items-center">
                    <h2 class="text-lg font-semibold text-gray-800">📦 待寄出 / 已打包清單</h2>
                    <span id="shippingCount" class="text-xs bg-blue-100 text-blue-800 px-2.5 py-1 rounded-full font-medium">共 0 項</span>
                </div>
                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse">
                        <thead>
                            <tr class="bg-gray-100 text-gray-600 text-xs uppercase tracking-wider">
                                <th class="p-3">日期</th>
                                <th class="p-3">檔口</th>
                                <th class="p-3">商品名稱</th>
                                <th class="p-3">數量</th>
                                <th class="p-3">目前狀態</th>
                                <th class="p-3 text-right">狀態切換</th>
                            </tr>
                        </thead>
                        <tbody id="shippingTableBody" class="divide-y divide-gray-200 text-sm">
                            <!-- 動態插入 -->
                        </tbody>
                    </table>
                </div>
            </div>
        </section>
    </div>

    <!-- JavaScript 邏輯 -->
    <script>
        // 初始化 LocalStorage 資料
        let orders = JSON.parse(localStorage.getItem('shop_orders')) || [];
        let inventory = JSON.parse(localStorage.getItem('shop_inventory')) || [];

        // 自動填入今天的日期
        document.getElementById('orderDate').valueAsDate = new Date();

        // 切換頁籤
        function switchTab(tabName) {
            document.getElementById('tab-order').classList.add('hidden');
            document.getElementById('tab-inventory').classList.add('hidden');
            document.getElementById('tab-shipping').classList.add('hidden');

            document.getElementById('btn-order').className = 'px-4 py-2 bg-white text-gray-700 border border-gray-300 rounded-lg text-sm font-medium hover:bg-gray-100';
            document.getElementById('btn-inventory').className = 'px-4 py-2 bg-white text-gray-700 border border-gray-300 rounded-lg text-sm font-medium hover:bg-gray-100';
            document.getElementById('btn-shipping').className = 'px-4 py-2 bg-white text-gray-700 border border-gray-300 rounded-lg text-sm font-medium hover:bg-gray-100';

            if (tabName === 'order') {
                document.getElementById('tab-order').classList.remove('hidden');
                document.getElementById('btn-order').className = 'px-4 py-2 bg-indigo-600 text-white rounded-lg text-sm font-medium shadow';
                renderOrders();
            } else if (tabName === 'inventory') {
                document.getElementById('tab-inventory').classList.remove('hidden');
                document.getElementById('btn-inventory').className = 'px-4 py-2 bg-emerald-600 text-white rounded-lg text-sm font-medium shadow';
                renderInventory();
            } else if (tabName === 'shipping') {
                document.getElementById('tab-shipping').classList.remove('hidden');
                document.getElementById('btn-shipping').className = 'px-4 py-2 bg-blue-600 text-white rounded-lg text-sm font-medium shadow';
                renderShipping();
            }
        }

        // --- 訂單相關功能 ---
        function addOrder(e) {
            e.preventDefault();
            const newOrder = {
                id: Date.now(),
                date: document.getElementById('orderDate').value,
                booth: document.getElementById('boothName').value,
                item: document.getElementById('itemName').value,
                qty: parseInt(document.getElementById('itemQty').value),
                status: document.getElementById('itemStatus').value
            };
            orders.unshift(newOrder);
            saveAndRender();
            document.getElementById('orderForm').reset();
            document.getElementById('orderDate').valueAsDate = new Date();
        }

        function deleteOrder(id) {
            orders = orders.filter(o => o.id !== id);
            saveAndRender();
        }

        function updateOrderStatus(id, newStatus) {
            const order = orders.find(o => o.id === id);
            if (order) {
                order.status = newStatus;
                saveAndRender();
            }
        }

        function saveAndRender() {
            localStorage.setItem('shop_orders', JSON.stringify(orders));
            renderOrders();
        }

        function renderOrders() {
            const tbody = document.getElementById('orderTableBody');
            const search = document.getElementById('searchOrder').value.toLowerCase();
            const filter = document.getElementById('filterStatus').value;

            tbody.innerHTML = '';
            let filtered = orders.filter(o => {
                const matchSearch = o.booth.toLowerCase().includes(search) || o.item.toLowerCase().includes(search);
                const matchFilter = filter === '' || o.status === filter;
                return matchSearch && matchFilter;
            });

            if (filtered.length === 0) {
                tbody.innerHTML = `<tr><td colspan="6" class="p-4 text-center text-gray-400">目前沒有相關訂貨記錄</td></tr>`;
                return;
            }

            filtered.forEach(o => {
                let badgeColor = 'bg-gray-100 text-gray-800';
                if (o.status === '待訂') badgeColor = 'bg-amber-100 text-amber-800';
                if (o.status === '已訂') badgeColor = 'bg-blue-100 text-blue-800';
                if (o.status === '工作室') badgeColor = 'bg-purple-100 text-purple-800';
                if (o.status === '已回台') badgeColor = 'bg-emerald-100 text-emerald-800';

                tbody.innerHTML += `
                    <tr class="hover:bg-gray-50">
                        <td class="p-3 text-gray-600">${o.date}</td>
                        <td class="p-3 font-medium text-gray-900">${o.booth}</td>
                        <td class="p-3 text-gray-800">${o.item}</td>
                        <td class="p-3 text-gray-600">${o.qty}</td>
                        <td class="p-3">
                            <select onchange="updateOrderStatus(${o.id}, this.value)" class="text-xs font-semibold px-2.5 py-1 rounded-full ${badgeColor} border-0 cursor-pointer">
                                <option value="待訂" ${o.status === '待訂' ? 'selected' : ''}>待訂</option>
                                <option value="已訂" ${o.status === '已訂' ? 'selected' : ''}>已訂</option>
                                <option value="工作室" ${o.status === '工作室' ? 'selected' : ''}>工作室</option>
                                <option value="已回台" ${o.status === '已回台' ? 'selected' : ''}>已回台</option>
                            </select>
                        </td>
                        <td class="p-3 text-right">
                            <button onclick="deleteOrder(${o.id})" class="text-red-500 hover:text-red-700 text-xs font-medium">刪除</button>
                        </td>
                    </tr>
                `;
            });
        }

        // --- 庫存相關功能 ---
        function addInventory(e) {
            e.preventDefault();
            const newItem = {
                id: Date.now(),
                name: document.getElementById('invName').value,
                qty: parseInt(document.getElementById('invQty').value)
            };
            inventory.unshift(newItem);
            saveAndRenderInventory();
            document.getElementById('inventoryForm').reset();
        }

        function adjustQty(id, amount) {
            const item = inventory.find(i => i.id === id);
            if (item) {
                item.qty = Math.max(0, item.qty + amount);
                saveAndRenderInventory();
            }
        }

        function deleteInventory(id) {
            inventory = inventory.filter(i => i.id !== id);
            saveAndRenderInventory();
        }

        function saveAndRenderInventory() {
            localStorage.setItem('shop_inventory', JSON.stringify(inventory));
            renderInventory();
        }

        function renderInventory() {
            const tbody = document.getElementById('inventoryTableBody');
            tbody.innerHTML = '';

            if (inventory.length === 0) {
                tbody.innerHTML = `<tr><td colspan="4" class="p-4 text-center text-gray-400">目前沒有庫存記錄</td></tr>`;
                return;
            }

            inventory.forEach(i => {
                tbody.innerHTML += `
                    <tr class="hover:bg-gray-50">
                        <td class="p-3 font-medium text-gray-900">${i.name}</td>
                        <td class="p-3 text-gray-800 font-bold">${i.qty}</td>
                        <td class="p-3 text-right space-x-1">
                            <button onclick="adjustQty(${i.id}, -1)" class="bg-gray-200 hover:bg-gray-300 px-2 py-1 rounded text-xs font-bold">-1</button>
                            <button onclick="adjustQty(${i.id}, 1)" class="bg-gray-200 hover:bg-gray-300 px-2 py-1 rounded text-xs font-bold">+1</button>
                        </td>
                        <td class="p-3 text-right">
                            <button onclick="deleteInventory(${i.id})" class="text-red-500 hover:text-red-700 text-xs font-medium">刪除</button>
                        </td>
                    </tr>
                `;
            });
        }

        // --- 韓國出貨清單功能 ---
        function renderShipping() {
            const tbody = document.getElementById('shippingTableBody');
            // 篩選出狀態為「工作室」或「已回台」的項目作為出貨/分流追蹤
            const shippingItems = orders.filter(o => o.status === '工作室' || o.status === '已回台');
            
            document.getElementById('shippingCount').innerText = `共 ${shippingItems.length} 項`;
            tbody.innerHTML = '';

            if (shippingItems.length === 0) {
                tbody.innerHTML = `<tr><td colspan="6" class="p-4 text-center text-gray-400">目前沒有處於「工作室」或「已回台」的待寄出商品</td></tr>`;
                return;
            }

            shippingItems.forEach(o => {
                let badgeColor = o.status === '工作室' ? 'bg-purple-100 text-purple-800' : 'bg-emerald-100 text-emerald-800';
                tbody.innerHTML += `
                    <tr class="hover:bg-gray-50">
                        <td class="p-3 text-gray-600">${o.date}</td>
                        <td class="p-3 font-medium text-gray-900">${o.booth}</td>
                        <td class="p-3 text-gray-800">${o.item}</td>
                        <td class="p-3 text-gray-600">${o.qty}</td>
                        <td class="p-3">
                            <span class="text-xs font-semibold px-2.5 py-1 rounded-full ${badgeColor}">${o.status}</span>
                        </td>
                        <td class="p-3 text-right">
                            <select onchange="updateOrderStatus(${o.id}, this.value); renderShipping();" class="text-xs border border-gray-300 rounded px-2 py-1">
                                <option value="工作室" ${o.status === '工作室' ? 'selected' : ''}>設為 工作室</option>
                                <option value="已回台" ${o.status === '已回台' ? 'selected' : ''}>設為 已回台</option>
                                <option value="已訂" ${o.status === '已訂' ? 'selected' : ''}>移回 已訂</option>
                            </select>
                        </td>
                    </tr>
                `;
            });
        }

        function generateShippingList() {
            alert('系統已自動過濾並整理所有在「工作室」與「已回台」的商品供您寄出核對！');
            switchTab('shipping');
        }

        // 初始載入預設頁面
        renderOrders();
    </script>
</body>
</html>

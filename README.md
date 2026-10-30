import React, { useState, useMemo, useEffect } from 'react';
import { 
  Package, 
  ShoppingBag, 
  Truck, 
  Plus, 
  Search, 
  Printer, 
  ArrowUpRight, 
  AlertCircle, 
  CheckCircle2, 
  Clock, 
  Box, 
  Layers, 
  RefreshCw, 
  Trash2, 
  Edit3, 
  TrendingUp, 
  ExternalLink,
  ChevronRight,
  Filter,
  DollarSign
} from 'lucide-react';

// --- 初始模擬數據 (韓系選品店真實情境) ---
const INITIAL_ORDERS = [
  {
    id: 'ORD-20261028-01',
    market: 'apm PLACE 3F A-12',
    name: '慵懶重磅馬海毛羊毛針織衫',
    sku: 'TOP-MH-01',
    color: '燕麥杏 (Oatmeal)',
    size: 'F',
    qty: 15,
    arrivedQty: 15,
    unitCostKrw: 38000,
    retailPrice: 1680,
    orderDate: '2026-10-28',
    status: '已到首爾工作室',
    note: '已質檢完畢，微落肩版型'
  },
  {
    id: 'ORD-20261029-02',
    market: 'Theot 2F C-08',
    name: '垂墜弧形氣球打褶西裝褲',
    sku: 'BTM-BL-04',
    color: '霧面炭灰 (Charcoal)',
    size: 'M',
    qty: 20,
    arrivedQty: 0,
    unitCostKrw: 24000,
    retailPrice: 1280,
    orderDate: '2026-10-29',
    status: '檔口排單中',
    note: '檔口通知預計週五晚間出貨'
  },
  {
    id: 'ORD-20261030-03',
    market: 'Queen\'s Square 4F',
    name: '純棉立領打褶短版襯衫',
    sku: 'TOP-SH-09',
    color: '純淨白 (Clean White)',
    size: 'F',
    qty: 10,
    arrivedQty: 10,
    unitCostKrw: 19000,
    retailPrice: 980,
    orderDate: '2026-10-30',
    status: '已到首爾工作室',
    note: '面料挺度佳，附備用扣'
  },
  {
    id: 'ORD-20261030-04',
    market: 'DDP Fashion Mall 1F',
    name: '極簡微墊肩微寬西裝外套',
    sku: 'OUT-BZ-02',
    color: '可可咖 (Cocoa)',
    size: 'L',
    qty: 8,
    arrivedQty: 0,
    unitCostKrw: 52000,
    retailPrice: 2480,
    orderDate: '2026-10-30',
    status: '已下單',
    note: '預付訂金 50%'
  }
];

const INITIAL_INVENTORY = [
  {
    id: 'INV-001',
    sku: 'TOP-MH-01',
    name: '慵懶重磅馬海毛羊毛針織衫',
    category: '上衣類 (Tops)',
    color: '燕麥杏',
    size: 'F',
    stock: 12,
    reservedForShipping: 3,
    location: '首爾倉庫 A-03',
    unitCostKrw: 38000,
    retailPrice: 1680,
    alertThreshold: 5
  },
  {
    id: 'INV-002',
    sku: 'BTM-BL-04',
    name: '垂墜弧形氣球打褶西裝褲',
    category: '下身類 (Bottoms)',
    color: '霧面炭灰',
    size: 'S',
    stock: 4,
    reservedForShipping: 0,
    location: '首爾倉庫 B-01',
    unitCostKrw: 24000,
    retailPrice: 1280,
    alertThreshold: 5
  },
  {
    id: 'INV-003',
    sku: 'TOP-SH-09',
    name: '純棉立領打褶短版襯衫',
    category: '上衣類 (Tops)',
    color: '純淨白',
    size: 'F',
    stock: 8,
    reservedForShipping: 2,
    location: '首爾倉庫 A-05',
    unitCostKrw: 19000,
    retailPrice: 980,
    alertThreshold: 3
  },
  {
    id: 'INV-004',
    sku: 'ACC-SL-01',
    name: '軟糯拉絨雙面大圍巾',
    category: '飾品配件 (Acc)',
    color: '奶霜米',
    size: 'F',
    stock: 18,
    reservedForShipping: 5,
    location: '首爾倉庫 C-02',
    unitCostKrw: 12000,
    retailPrice: 690,
    alertThreshold: 5
  }
];

const INITIAL_SHIPMENTS = [
  {
    id: 'SHIP-BOX-24',
    boxName: 'Box #24 - 首爾工作室第一批空運',
    carrier: 'EMS 航空件 (韓國直郵)',
    trackingNo: 'EM908234123KR',
    dispatchDate: '2026-10-30',
    weightKg: 14.5,
    status: '運送中',
    destination: '台北門市 / 倉儲中心',
    items: [
      { sku: 'TOP-MH-01', name: '慵懶重磅馬海毛羊毛針織衫', color: '燕麥杏', size: 'F', qty: 5 },
      { sku: 'TOP-SH-09', name: '純棉立領打褶短版襯衫', color: '純淨白', size: 'F', qty: 6 }
    ],
    notes: '急件，門市週一新品上架用'
  },
  {
    id: 'SHIP-BOX-25',
    boxName: 'Box #25 - 預購第二箱 (待封箱)',
    carrier: '順豐速運特惠海空運',
    trackingNo: 'SF8921049281',
    dispatchDate: '2026-11-01',
    weightKg: 8.2,
    status: '打包中',
    destination: '台灣中繼工作室',
    items: [
      { sku: 'ACC-SL-01', name: '軟糯拉絨雙面大圍巾', color: '奶霜米', size: 'F', qty: 5 }
    ],
    notes: '等待炭灰西裝褲齊全後一同封箱'
  }
];

export default function App() {
  const [activeTab, setActiveTab] = useState('orders'); // 'dashboard', 'orders', 'inventory', 'shipments'
  
  // 核心狀態
  const [orders, setOrders] = useState(INITIAL_ORDERS);
  const [inventory, setInventory] = useState(INITIAL_INVENTORY);
  const [shipments, setShipments] = useState(INITIAL_SHIPMENTS);

  // 模態視窗控制
  const [showOrderModal, setShowOrderModal] = useState(false);
  const [showStockModal, setShowStockModal] = useState(false);
  const [showShipmentModal, setShowShipmentModal] = useState(false);
  const [printManifestBox, setPrintManifestBox] = useState(null);

  // 搜尋與篩選
  const [searchTerm, setSearchTerm] = useState('');
  const [categoryFilter, setCategoryFilter] = useState('All');

  // 新增訂單表單狀態
  const [newOrder, setNewOrder] = useState({
    market: '',
    name: '',
    sku: '',
    color: '',
    size: 'F',
    qty: 10,
    unitCostKrw: 25000,
    retailPrice: 1280,
    status: '已下單',
    note: ''
  });

  // 新增出貨箱表單狀態
  const [newShipment, setNewShipment] = useState({
    boxName: '',
    carrier: 'EMS 航空件 (韓國直郵)',
    trackingNo: '',
    destination: '台北門市 / 倉儲中心',
    weightKg: 5.0,
    notes: '',
    items: []
  });

  // 待加入出貨箱的選定商品
  const [selectedStockForBox, setSelectedStockForBox] = useState({
    stockId: '',
    qty: 1
  });

  // 快速統計指標
  const stats = useMemo(() => {
    const totalOrderKrw = orders.reduce((sum, o) => sum + (o.unitCostKrw * o.qty), 0);
    const totalInStock = inventory.reduce((sum, item) => sum + item.stock, 0);
    const lowStockCount = inventory.filter(item => item.stock <= item.alertThreshold).length;
    const pendingShipments = shipments.filter(s => s.status !== '已抵達').length;
    return { totalOrderKrw, totalInStock, lowStockCount, pendingShipments };
  }, [orders, inventory, shipments]);

  // 訂貨狀態切換並可選擇入庫
  const handleOrderStatusChange = (orderId, newStatus) => {
    setOrders(prev => prev.map(o => {
      if (o.id === orderId) {
        return { ...o, status: newStatus };
      }
      return o;
    }));
  };

  // 將已到貨的採購單一鍵轉入首爾庫存
  const handleSyncToInventory = (order) => {
    const existingIndex = inventory.findIndex(item => item.sku === order.sku && item.color === order.color && item.size === order.size);
    if (existingIndex > -1) {
      setInventory(prev => prev.map((item, idx) => {
        if (idx === existingIndex) {
          return { ...item, stock: item.stock + order.qty };
        }
        return item;
      }));
    } else {
      const newItem = {
        id: `INV-${Date.now().toString().slice(-4)}`,
        sku: order.sku,
        name: order.name,
        category: '上衣類 (Tops)',
        color: order.color,
        size: order.size,
        stock: order.qty,
        reservedForShipping: 0,
        location: '首爾工作室 檢品暫存區',
        unitCostKrw: order.unitCostKrw,
        retailPrice: order.retailPrice,
        alertThreshold: 3
      };
      setInventory(prev => [newItem, ...prev]);
    }
    // 更新該採購單為「已到首爾工作室」
    handleOrderStatusChange(order.id, '已到首爾工作室');
  };

  // 即時微調庫存數量 (+ / -)
  const handleAdjustStock = (id, delta) => {
    setInventory(prev => prev.map(item => {
      if (item.id === id) {
        const nextStock = Math.max(0, item.stock + delta);
        return { ...item, stock: nextStock };
      }
      return item;
    }));
  };

  // 新增出貨箱包裹
  const handleCreateShipment = (e) => {
    e.preventDefault();
    if (!newShipment.boxName) return;

    const createdBox = {
      id: `SHIP-BOX-${Math.floor(10 + Math.random() * 90)}`,
      boxName: newShipment.boxName,
      carrier: newShipment.carrier,
      trackingNo: newShipment.trackingNo || '待填入',
      dispatchDate: new Date().toISOString().split('T')[0],
      weightKg: Number(newShipment.weightKg) || 0,
      status: '打包中',
      destination: newShipment.destination,
      items: newShipment.items,
      notes: newShipment.notes
    };

    setShipments([createdBox, ...shipments]);
    setShowShipmentModal(false);
    setNewShipment({
      boxName: '',
      carrier: 'EMS 航空件 (韓國直郵)',
      trackingNo: '',
      destination: '台北門市 / 倉儲中心',
      weightKg: 5.0,
      notes: '',
      items: []
    });
  };

  // 將選定庫存放入草稿箱子
  const handleAddItemToShipmentDraft = () => {
    if (!selectedStockForBox.stockId || selectedStockForBox.qty <= 0) return;
    const invItem = inventory.find(i => i.id === selectedStockForBox.stockId);
    if (!invItem) return;

    if (selectedStockForBox.qty > invItem.stock) {
      alert('選擇數量超過首爾現有庫存！');
      return;
    }

    // 加入暫存箱
    setNewShipment(prev => {
      const existing = prev.items.find(i => i.sku === invItem.sku && i.color === invItem.color);
      let updatedItems = [];
      if (existing) {
        updatedItems = prev.items.map(i => 
          (i.sku === invItem.sku && i.color === invItem.color) 
            ? { ...i, qty: i.qty + Number(selectedStockForBox.qty) } 
            : i
        );
      } else {
        updatedItems = [...prev.items, {
          sku: invItem.sku,
          name: invItem.name,
          color: invItem.color,
          size: invItem.size,
          qty: Number(selectedStockForBox.qty)
        }];
      }
      return { ...prev, items: updatedItems };
    });

    // 扣減當前庫存並增加待寄出量
    setInventory(prev => prev.map(i => {
      if (i.id === invItem.id) {
        return {
          ...i,
          stock: i.stock - Number(selectedStockForBox.qty),
          reservedForShipping: (i.reservedForShipping || 0) + Number(selectedStockForBox.qty)
        };
      }
      return i;
    }));

    setSelectedStockForBox({ stockId: '', qty: 1 });
  };

  // 建立新訂單
  const handleCreateOrder = (e) => {
    e.preventDefault();
    const created = {
      id: `ORD-${Date.now().toString().slice(-6)}`,
      market: newOrder.market || '東大門檔口',
      name: newOrder.name,
      sku: newOrder.sku || `SKU-${Math.floor(100+Math.random()*900)}`,
      color: newOrder.color || '單一色',
      size: newOrder.size,
      qty: Number(newOrder.qty),
      arrivedQty: 0,
      unitCostKrw: Number(newOrder.unitCostKrw),
      retailPrice: Number(newOrder.retailPrice),
      orderDate: new Date().toISOString().split('T')[0],
      status: newOrder.status,
      note: newOrder.note
    };

    setOrders([created, ...orders]);
    setShowOrderModal(false);
    setNewOrder({
      market: '',
      name: '',
      sku: '',
      color: '',
      size: 'F',
      qty: 10,
      unitCostKrw: 25000,
      retailPrice: 1280,
      status: '已下單',
      note: ''
    });
  };

  return (
    <div className="min-h-screen bg-[#FDFBF7] text-[#2D2A26] flex flex-col font-sans antialiased">
      {/* 頂部韓系美學 Header */}
      <header className="border-b border-[#EAE5D9] bg-white/80 backdrop-blur-md sticky top-0 z-40 px-6 py-4">
        <div className="max-w-7xl mx-auto flex flex-col sm:flex-row items-center justify-between gap-4">
          <div className="flex items-center gap-3">
            <div className="w-10 h-10 rounded-full bg-[#38332B] text-[#FDFBF7] flex items-center justify-center font-serif text-lg tracking-wider font-semibold shadow-sm">
              K
            </div>
            <div>
              <div className="flex items-center gap-2">
                <span className="font-semibold text-lg tracking-tight text-[#2D2A26]">STUDIO K 韓國直營</span>
                <span className="text-[11px] font-medium bg-[#EFECE6] text-[#696256] px-2 py-0.5 rounded-full border border-[#DFDACF]">
                  首爾工作站 • SEOUL HQ
                </span>
              </div>
              <p className="text-xs text-[#8A8275]">東大門叫貨、檔口進度追蹤、首爾現貨倉與出口箱清單管理</p>
            </div>
          </div>

          {/* 導覽分頁 */}
          <nav className="flex items-center bg-[#F3EFE6] p-1 rounded-xl border border-[#E3DDCF]">
            <button
              onClick={() => setActiveTab('orders')}
              className={`flex items-center gap-2 px-4 py-1.5 rounded-lg text-sm font-medium transition-all ${
                activeTab === 'orders'
                  ? 'bg-white text-[#2D2A26] shadow-sm font-semibold'
                  : 'text-[#7D7567] hover:text-[#2D2A26]'
              }`}
            >
              <ShoppingBag className="w-4 h-4" />
              東大門訂貨 ({orders.length})
            </button>
            <button
              onClick={() => setActiveTab('inventory')}
              className={`flex items-center gap-2 px-4 py-1.5 rounded-lg text-sm font-medium transition-all ${
                activeTab === 'inventory'
                  ? 'bg-white text-[#2D2A26] shadow-sm font-semibold'
                  : 'text-[#7D7567] hover:text-[#2D2A26]'
              }`}
            >
              <Layers className="w-4 h-4" />
              首爾工作室庫存 ({inventory.length})
            </button>
            <button
              onClick={() => setActiveTab('shipments')}
              className={`flex items-center gap-2 px-4 py-1.5 rounded-lg text-sm font-medium transition-all ${
                activeTab === 'shipments'
                  ? 'bg-white text-[#2D2A26] shadow-sm font-semibold'
                  : 'text-[#7D7567] hover:text-[#2D2A26]'
              }`}
            >
              <Truck className="w-4 h-4" />
              韓國寄出清單 ({shipments.length})
            </button>
          </nav>
        </div>
      </header>

      {/* 主工作區 */}
      <main className="flex-1 max-w-7xl w-full mx-auto p-4 sm:p-6 lg:p-8 space-y-6">
        
        {/* 頂部數據看板 Cards */}
        <section className="grid grid-cols-2 md:grid-cols-4 gap-4">
          <div className="bg-white p-4 rounded-2xl border border-[#EBE6DB] shadow-sm">
            <span className="text-xs text-[#8A8275] font-medium">本期東大門叫貨量</span>
            <div className="mt-2 flex items-baseline justify-between">
              <span className="text-2xl font-serif font-bold text-[#2D2A26]">
                {orders.reduce((sum, i) => sum + i.qty, 0)} <span className="text-xs font-sans font-normal text-gray-500">件</span>
              </span>
              <span className="text-xs text-[#827867] bg-[#F7F4EE] px-2 py-0.5 rounded-md">
                ₩{(stats.totalOrderKrw / 10000).toFixed(0)}萬 KRW
              </span>
            </div>
          </div>

          <div className="bg-white p-4 rounded-2xl border border-[#EBE6DB] shadow-sm">
            <span className="text-xs text-[#8A8275] font-medium">首爾工作室現貨數</span>
            <div className="mt-2 flex items-baseline justify-between">
              <span className="text-2xl font-serif font-bold text-[#2D2A26]">
                {stats.totalInStock} <span className="text-xs font-sans font-normal text-gray-500">件</span>
              </span>
              <span className="text-xs text-emerald-700 bg-emerald-50 px-2 py-0.5 rounded-md border border-emerald-200">
                在庫檢品良好
              </span>
            </div>
          </div>

          <div className="bg-white p-4 rounded-2xl border border-[#EBE6DB] shadow-sm">
            <span className="text-xs text-[#8A8275] font-medium">待寄出 / 封箱包裹</span>
            <div className="mt-2 flex items-baseline justify-between">
              <span className="text-2xl font-serif font-bold text-[#2D2A26]">
                {stats.pendingShipments} <span className="text-xs font-sans font-normal text-gray-500">箱</span>
              </span>
              <span className="text-xs text-amber-700 bg-amber-50 px-2 py-0.5 rounded-md border border-amber-200">
                空運/EMS集運
              </span>
            </div>
          </div>

          <div className="bg-white p-4 rounded-2xl border border-[#EBE6DB] shadow-sm">
            <span className="text-xs text-[#8A8275] font-medium">現貨警報 (Low Stock)</span>
            <div className="mt-2 flex items-baseline justify-between">
              <span className="text-2xl font-serif font-bold text-[#A64B2A]">
                {stats.lowStockCount} <span className="text-xs font-sans font-normal text-gray-500">款偏少</span>
              </span>
              <span className="text-xs text-[#A64B2A] bg-rose-50 px-2 py-0.5 rounded-md border border-rose-100">
                需補檔口
              </span>
            </div>
          </div>
        </section>

        {/* ---------------- 模組 1: 訂貨管理 (東大門採購追蹤) ---------------- */}
        {activeTab === 'orders' && (
          <div className="space-y-4">
            <div className="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-3">
              <div>
                <h2 className="text-lg font-semibold tracking-tight">批發採購與檔口訂單</h2>
                <p className="text-xs text-[#8A8275]">記錄東大門批市檔口 (apM, DDP, Theot 等)、採購單價與調貨交期狀態</p>
              </div>
              <button
                onClick={() => setShowOrderModal(true)}
                className="bg-[#2D2A26] hover:bg-[#433F3A] text-white px-4 py-2 rounded-xl text-sm font-medium flex items-center gap-1.5 shadow-sm transition"
              >
                <Plus className="w-4 h-4" />
                新增檔口採購單
              </button>
            </div>

            <div className="bg-white border border-[#EAE5D9] rounded-2xl overflow-hidden shadow-sm">
              <div className="overflow-x-auto">
                <table className="w-full text-left text-sm">
                  <thead className="bg-[#FAF8F5] border-b border-[#EAE5D9] text-xs text-[#7A7365] uppercase">
                    <tr>
                      <th className="py-3 px-4">訂單單號 / 檔口位置</th>
                      <th className="py-3 px-4">品名 / 規格 (SKU)</th>
                      <th className="py-3 px-4">訂貨量</th>
                      <th className="py-3 px-4">成本(KRW) / 零售(TWD)</th>
                      <th className="py-3 px-4">進度狀態</th>
                      <th className="py-3 px-4">備註</th>
                      <th className="py-3 px-4 text-right">操作</th>
                    </tr>
                  </thead>
                  <tbody className="divide-y divide-[#F1EDE4]">
                    {orders.map((ord) => (
                      <tr key={ord.id} className="hover:bg-[#FAF9F6] transition">
                        <td className="py-3 px-4">
                          <div className="font-mono text-xs font-semibold text-[#454037]">{ord.id}</div>
                          <div className="text-xs text-[#877E6E] font-medium mt-0.5">{ord.market}</div>
                        </td>
                        <td className="py-3 px-4">
                          <div className="font-medium text-[#2D2A26]">{ord.name}</div>
                          <div className="text-xs text-[#8A8275] flex items-center gap-2 mt-0.5">
                            <span className="font-mono bg-[#F2EFE8] px-1.5 py-0.2 rounded text-[10px]">{ord.sku}</span>
                            <span>{ord.color}</span>
                            <span className="font-mono font-bold">[{ord.size}]</span>
                          </div>
                        </td>
                        <td className="py-3 px-4">
                          <span className="font-semibold text-base">{ord.qty}</span> <span className="text-xs text-[#8A8275]">件</span>
                        </td>
                        <td className="py-3 px-4 font-mono">
                          <div className="text-xs font-semibold text-stone-900">₩{ord.unitCostKrw.toLocaleString()}</div>
                          <div className="text-xs text-[#8A8275] font-sans">NT$ {ord.retailPrice.toLocaleString()}</div>
                        </td>
                        <td className="py-3 px-4">
                          <select
                            value={ord.status}
                            onChange={(e) => handleOrderStatusChange(ord.id, e.target.value)}
                            className={`text-xs font-medium px-2.5 py-1 rounded-full border outline-none cursor-pointer ${
                              ord.status === '已到首爾工作室'
                                ? 'bg-emerald-50 text-emerald-800 border-emerald-200'
                                : ord.status === '檔口排單中'
                                ? 'bg-amber-50 text-amber-800 border-amber-200'
                                : 'bg-slate-100 text-slate-800 border-slate-200'
                            }`}
                          >
                            <option value="已下單">已下單 (Ordered)</option>
                            <option value="檔口排單中">檔口排單中 (Pending)</option>
                            <option value="已到首爾工作室">已到首爾工作室 (Arrived)</option>
                          </select>
                        </td>
                        <td className="py-3 px-4 text-xs text-[#7A7365] max-w-xs truncate">
                          {ord.note || '-'}
                        </td>
                        <td className="py-3 px-4 text-right">
                          {ord.status === '已到首爾工作室' ? (
                            <span className="text-xs text-emerald-600 font-medium inline-flex items-center gap-1">
                              <CheckCircle2 className="w-3.5 h-3.5" /> 已在庫
                            </span>
                          ) : (
                            <button
                              onClick={() => handleSyncToInventory(ord)}
                              className="text-xs bg-[#EFECE5] hover:bg-[#E3DEC] text-[#4A443A] px-2.5 py-1 rounded-md font-medium transition"
                            >
                              點收並入庫
                            </button>
                          )}
                        </td>
                      </tr>
                    ))}
                  </tbody>
                </table>
              </div>
            </div>
          </div>
        )}

        {/* ---------------- 模組 2: 首爾庫存管理 (工作室現貨) ---------------- */}
        {activeTab === 'inventory' && (
          <div className="space-y-4">
            <div className="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-3">
              <div>
                <h2 className="text-lg font-semibold tracking-tight">首爾工作室現貨與品控倉</h2>
                <p className="text-xs text-[#8A8275]">即時管理首爾本地實體庫存、暫扣待發數量，支援多品項快速增減</p>
              </div>
              <div className="flex items-center gap-2 w-full sm:w-auto">
                <div className="relative flex-1 sm:w-64">
                  <Search className="w-4 h-4 absolute left-3 top-2.5 text-[#A59F94]" />
                  <input
                    type="text"
                    placeholder="搜尋品名、SKU 或顏色..."
                    value={searchTerm}
                    onChange={(e) => setSearchTerm(e.target.value)}
                    className="w-full bg-white border border-[#DDD6C8] rounded-xl pl-9 pr-3 py-1.5 text-xs focus:ring-1 focus:ring-stone-400 outline-none"
                  />
                </div>
              </div>
            </div>

            <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
              {inventory
                .filter(i => 
                  i.name.includes(searchTerm) || 
                  i.sku.toLowerCase().includes(searchTerm.toLowerCase()) || 
                  i.color.includes(searchTerm)
                )
                .map((item) => (
                  <div key={item.id} className="bg-white border border-[#EAE5D9] rounded-2xl p-4 shadow-sm flex flex-col justify-between">
                    <div>
                      <div className="flex justify-between items-start">
                        <span className="text-[11px] font-mono bg-[#F2EFE8] text-[#635C4E] px-2 py-0.5 rounded">
                          {item.sku}
                        </span>
                        {item.stock <= item.alertThreshold && (
                          <span className="text-[10px] bg-rose-50 text-rose-700 px-1.5 py-0.5 rounded border border-rose-200 flex items-center gap-1 font-semibold">
                            <AlertCircle className="w-3 h-3" /> 庫存告急
                          </span>
                        )}
                      </div>
                      <h3 className="font-semibold text-sm text-[#2D2A26] mt-2 line-clamp-1">{item.name}</h3>
                      <div className="flex items-center gap-2 mt-1 text-xs text-[#8A8275]">
                        <span>規格：{item.color} / {item.size}</span>
                      </div>
                      <div className="mt-1 text-[11px] text-[#A39B8E]">存放位：{item.location}</div>
                    </div>

                    <div className="mt-4 pt-3 border-t border-[#F1ECE3]">
                      <div className="flex items-center justify-between text-xs mb-2">
                        <span className="text-[#8A8275]">可出貨現貨：</span>
                        <span className="text-xl font-bold font-serif text-[#2D2A26]">{item.stock}</span>
                      </div>
                      {item.reservedForShipping > 0 && (
                        <div className="flex items-center justify-between text-[11px] text-amber-700 mb-2">
                          <span>待裝箱排程中：</span>
                          <span>{item.reservedForShipping} 件</span>
                        </div>
                      )}
                      
                      {/* 即時調整庫存微調按鈕 */}
                      <div className="flex items-center justify-between gap-2 mt-2">
                        <span className="text-[11px] text-[#8A8275]">調整庫存</span>
                        <div className="flex items-center bg-[#F7F4EE] rounded-lg border border-[#E3DDCF] p-0.5">
                          <button
                            onClick={() => handleAdjustStock(item.id, -1)}
                            className="w-6 h-6 flex items-center justify-center hover:bg-white rounded text-sm font-semibold transition"
                          >
                            -
                          </button>
                          <span className="w-8 text-center text-xs font-semibold">{item.stock}</span>
                          <button
                            onClick={() => handleAdjustStock(item.id, 1)}
                            className="w-6 h-6 flex items-center justify-center hover:bg-white rounded text-sm font-semibold transition"
                          >
                            +
                          </button>
                        </div>
                      </div>
                    </div>
                  </div>
                ))}
            </div>
          </div>
        )}

        {/* ---------------- 模組 3: 韓國寄出清單 (分批寄出裝箱清單) ---------------- */}
        {activeTab === 'shipments' && (
          <div className="space-y-4">
            <div className="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-3">
              <div>
                <h2 className="text-lg font-semibold tracking-tight">首爾寄出清單（裝箱分批作業）</h2>
                <p className="text-xs text-[#8A8275]">將首爾現貨劃撥進 EMS、集運航空包裹，並自動產出裝箱 Packing List 供清關與收貨點收</p>
              </div>
              <button
                onClick={() => setShowShipmentModal(true)}
                className="bg-[#2D2A26] hover:bg-[#433F3A] text-white px-4 py-2 rounded-xl text-sm font-medium flex items-center gap-1.5 shadow-sm transition"
              >
                <Plus className="w-4 h-4" />
                建立新出貨箱 (Box Manifest)
              </button>
            </div>

            <div className="grid grid-cols-1 lg:grid-cols-2 gap-6">
              {shipments.map((box) => (
                <div key={box.id} className="bg-white border border-[#E8E2D5] rounded-2xl p-5 shadow-sm flex flex-col justify-between">
                  <div>
                    {/* 包裹頂部資訊 */}
                    <div className="flex items-start justify-between">
                      <div className="flex items-center gap-2">
                        <Box className="w-5 h-5 text-amber-800" />
                        <h3 className="font-semibold text-base text-[#2D2A26]">{box.boxName}</h3>
                      </div>
                      <span className={`text-xs px-2.5 py-0.5 rounded-full font-medium ${
                        box.status === '運送中' 
                          ? 'bg-blue-50 text-blue-700 border border-blue-200'
                          : box.status === '已抵達'
                          ? 'bg-emerald-50 text-emerald-700 border border-emerald-200'
                          : 'bg-amber-50 text-amber-700 border border-amber-200'
                      }`}>
                        {box.status}
                      </span>
                    </div>

                    <div className="mt-3 grid grid-cols-2 gap-2 text-xs text-[#70695C] bg-[#FAF8F5] p-3 rounded-xl border border-[#EDE8DE]">
                      <div>物流承運：<span className="font-medium text-[#2D2A26]">{box.carrier}</span></div>
                      <div>國際單號：<span className="font-mono font-medium text-[#2D2A26]">{box.trackingNo}</span></div>
                      <div>寄送目的地：<span className="font-medium text-[#2D2A26]">{box.destination}</span></div>
                      <div>預估毛重：<span className="font-medium text-[#2D2A26]">{box.weightKg} kg</span></div>
                    </div>

                    {/* 箱內明細清單 */}
                    <div className="mt-4">
                      <div className="text-xs font-semibold text-[#544E43] mb-2 flex justify-between items-center">
                        <span>箱內商品清單 (Items in Box)</span>
                        <span className="text-[11px] text-[#8A8275]">
                          合計 {box.items.reduce((s, i) => s + i.qty, 0)} 件服飾
                        </span>
                      </div>
                      <div className="divide-y divide-[#F0ECE2] max-h-48 overflow-y-auto pr-1">
                        {box.items.map((item, idx) => (
                          <div key={idx} className="py-2 flex items-center justify-between text-xs">
                            <div>
                              <div className="font-medium text-[#2D2A26]">{item.name}</div>
                              <div className="text-[11px] text-[#8A8275]">{item.color} / {item.size} • <span className="font-mono">{item.sku}</span></div>
                            </div>
                            <span className="font-serif font-bold text-sm bg-[#F5F2EB] px-2 py-0.5 rounded">
                              x {item.qty}
                            </span>
                          </div>
                        ))}
                      </div>
                    </div>
                  </div>

                  {/* 底部操作與列印裝箱單 */}
                  <div className="mt-5 pt-3 border-t border-[#F0ECE2] flex items-center justify-between">
                    <span className="text-[11px] text-[#8C8476]">寄出日期：{box.dispatchDate}</span>
                    <button
                      onClick={() => setPrintManifestBox(box)}
                      className="inline-flex items-center gap-1.5 text-xs text-[#2D2A26] bg-[#F5F2EB] hover:bg-[#EBE5D8] px-3 py-1.5 rounded-lg font-medium transition"
                    >
                      <Printer className="w-3.5 h-3.5" />
                      檢視 / 列印出貨裝箱單
                    </button>
                  </div>
                </div>
              ))}
            </div>
          </div>
        )}
      </main>

      {/* ---------------- 彈出視窗: 新增採購訂貨 ---------------- */}
      {showOrderModal && (
        <div className="fixed inset-0 bg-black/40 backdrop-blur-sm z-50 flex items-center justify-center p-4">
          <div className="bg-white rounded-3xl max-w-lg w-full p-6 shadow-xl border border-[#EAE5D9]">
            <h3 className="font-serif text-lg font-bold text-[#2D2A26]">建立東大門檔口採購訂貨</h3>
            <p className="text-xs text-[#8A8275] mt-1">填寫由首爾檔口訂購的新品或追加款資訊</p>
            
            <form onSubmit={handleCreateOrder} className="mt-4 space-y-3">
              <div className="grid grid-cols-2 gap-3">
                <div>
                  <label className="text-xs font-medium text-[#5A5346]">檔口資訊 / 批發市場</label>
                  <input
                    type="text"
                    required
                    placeholder="例如：apm PLACE 2F A01"
                    value={newOrder.market}
                    onChange={(e) => setNewOrder({...newOrder, market: e.target.value})}
                    className="w-full mt-1 border border-[#DDD6C8] rounded-xl px-3 py-2 text-xs focus:ring-1 focus:ring-stone-500 outline-none"
                  />
                </div>
                <div>
                  <label className="text-xs font-medium text-[#5A5346]">自定義 SKU 代碼</label>
                  <input
                    type="text"
                    placeholder="例如：TOP-2610-01"
                    value={newOrder.sku}
                    onChange={(e) => setNewOrder({...newOrder, sku: e.target.value})}
                    className="w-full mt-1 border border-[#DDD6C8] rounded-xl px-3 py-2 text-xs focus:ring-1 focus:ring-stone-500 outline-none"
                  />
                </div>
              </div>

              <div>
                <label className="text-xs font-medium text-[#5A5346]">商品名稱</label>
                <input
                  type="text"
                  required
                  placeholder="例如：法式細褶立領寬版襯衫"
                  value={newOrder.name}
                  onChange={(e) => setNewOrder({...newOrder, name: e.target.value})}
                  className="w-full mt-1 border border-[#DDD6C8] rounded-xl px-3 py-2 text-xs focus:ring-1 focus:ring-stone-500 outline-none"
                />
              </div>

              <div className="grid grid-cols-3 gap-3">
                <div>
                  <label className="text-xs font-medium text-[#5A5346]">顏色</label>
                  <input
                    type="text"
                    placeholder="例：奶油白"
                    value={newOrder.color}
                    onChange={(e) => setNewOrder({...newOrder, color: e.target.value})}
                    className="w-full mt-1 border border-[#DDD6C8] rounded-xl px-3 py-2 text-xs outline-none"
                  />
                </div>
                <div>
                  <label className="text-xs font-medium text-[#5A5346]">尺寸</label>
                  <select
                    value={newOrder.size}
                    onChange={(e) => setNewOrder({...newOrder, size: e.target.value})}
                    className="w-full mt-1 border border-[#DDD6C8] rounded-xl px-3 py-2 text-xs outline-none bg-white"
                  >
                    <option value="F">Free (F)</option>
                    <option value="S">S</option>
                    <option value="M">M</option>
                    <option value="L">L</option>
                  </select>
                </div>
                <div>
                  <label className="text-xs font-medium text-[#5A5346]">採購件數</label>
                  <input
                    type="number"
                    min="1"
                    required
                    value={newOrder.qty}
                    onChange={(e) => setNewOrder({...newOrder, qty: e.target.value})}
                    className="w-full mt-1 border border-[#DDD6C8] rounded-xl px-3 py-2 text-xs outline-none"
                  />
                </div>
              </div>

              <div className="grid grid-cols-2 gap-3">
                <div>
                  <label className="text-xs font-medium text-[#5A5346]">批發單價 (KRW 韓元)</label>
                  <input
                    type="number"
                    value={newOrder.unitCostKrw}
                    onChange={(e) => setNewOrder({...newOrder, unitCostKrw: e.target.value})}
                    className="w-full mt-1 border border-[#DDD6C8] rounded-xl px-3 py-2 text-xs outline-none"
                  />
                </div>
                <div>
                  <label className="text-xs font-medium text-[#5A5346]">預計門市售價 (NTD)</label>
                  <input
                    type="number"
                    value={newOrder.retailPrice}
                    onChange={(e) => setNewOrder({...newOrder, retailPrice: e.target.value})}
                    className="w-full mt-1 border border-[#DDD6C8] rounded-xl px-3 py-2 text-xs outline-none"
                  />
                </div>
              </div>

              <div>
                <label className="text-xs font-medium text-[#5A5346]">備註 (如：起批量、配件等)</label>
                <input
                  type="text"
                  placeholder="備註文字..."
                  value={newOrder.note}
                  onChange={(e) => setNewOrder({...newOrder, note: e.target.value})}
                  className="w-full mt-1 border border-[#DDD6C8] rounded-xl px-3 py-2 text-xs outline-none"
                />
              </div>

              <div className="flex justify-end gap-2 pt-3 border-t border-[#F0ECE2]">
                <button
                  type="button"
                  onClick={() => setShowOrderModal(false)}
                  className="px-4 py-2 rounded-xl text-xs font-medium text-[#696256] hover:bg-[#F2EFE8]"
                >
                  取消
                </button>
                <button
                  type="submit"
                  className="bg-[#2D2A26] hover:bg-[#433F3A] text-white px-5 py-2 rounded-xl text-xs font-medium"
                >
                  確認建立訂單
                </button>
              </div>
            </form>
          </div>
        </div>
      )}

      {/* ---------------- 彈出視窗: 建立首爾寄出箱 (出貨裝箱) ---------------- */}
      {showShipmentModal && (
        <div className="fixed inset-0 bg-black/40 backdrop-blur-sm z-50 flex items-center justify-center p-4">
          <div className="bg-white rounded-3xl max-w-xl w-full p-6 shadow-xl border border-[#EAE5D9] max-h-[90vh] overflow-y-auto">
            <h3 className="font-serif text-lg font-bold text-[#2D2A26]">建立新出貨包裹 (韓國發貨)</h3>
            <p className="text-xs text-[#8A8275] mt-1">從首爾庫存挑選商品裝箱，產出國際物流清單</p>

            <form onSubmit={handleCreateShipment} className="mt-4 space-y-4">
              <div className="grid grid-cols-2 gap-3">
                <div>
                  <label className="text-xs font-medium text-[#5A5346]">包裹箱號 / 代號</label>
                  <input
                    type="text"
                    required
                    placeholder="例：Box #26 - 台北旗艦店"
                    value={newShipment.boxName}
                    onChange={(e) => setNewShipment({...newShipment, boxName: e.target.value})}
                    className="w-full mt-1 border border-[#DDD6C8] rounded-xl px-3 py-2 text-xs outline-none"
                  />
                </div>
                <div>
                  <label className="text-xs font-medium text-[#5A5346]">物流渠道</label>
                  <select
                    value={newShipment.carrier}
                    onChange={(e) => setNewShipment({...newShipment, carrier: e.target.value})}
                    className="w-full mt-1 border border-[#DDD6C8] rounded-xl px-3 py-2 text-xs outline-none bg-white"
                  >
                    <option value="EMS 航空件 (韓國直郵)">EMS 航空件 (韓國直郵)</option>
                    <option value="順豐速運特惠海空運">順豐速運特惠海空運</option>
                    <option value="韓國直航集運專線">韓國直航集運專線</option>
                  </select>
                </div>
              </div>

              <div className="grid grid-cols-2 gap-3">
                <div>
                  <label className="text-xs font-medium text-[#5A5346]">國際追蹤碼 (Tracking No.)</label>
                  <input
                    type="text"
                    placeholder="例：KR908123490"
                    value={newShipment.trackingNo}
                    onChange={(e) => setNewShipment({...newShipment, trackingNo: e.target.value})}
                    className="w-full mt-1 border border-[#DDD6C8] rounded-xl px-3 py-2 text-xs outline-none"
                  />
                </div>
                <div>
                  <label className="text-xs font-medium text-[#5A5346]">預估重量 (kg)</label>
                  <input
                    type="number"
                    step="0.1"
                    value={newShipment.weightKg}
                    onChange={(e) => setNewShipment({...newShipment, weightKg: e.target.value})}
                    className="w-full mt-1 border border-[#DDD6C8] rounded-xl px-3 py-2 text-xs outline-none"
                  />
                </div>
              </div>

              {/* 從工作室現貨挑選裝箱 */}
              <div className="p-3 bg-[#FAF8F4] rounded-2xl border border-[#EDE8DC] space-y-2">
                <label className="text-xs font-semibold text-[#5A5346] flex items-center justify-between">
                  <span>從首爾現貨劃撥進本箱：</span>
                  <span className="text-[11px] text-[#8C8476]">自動扣減在庫庫存</span>
                </label>
                <div className="flex gap-2">
                  <select
                    value={selectedStockForBox.stockId}
                    onChange={(e) => setSelectedStockForBox({...selectedStockForBox, stockId: e.target.value})}
                    className="flex-1 border border-[#DDD6C8] rounded-xl px-3 py-1.5 text-xs outline-none bg-white"
                  >
                    <option value="">-- 選擇現貨品項 --</option>
                    {inventory.map(item => (
                      <option key={item.id} value={item.id} disabled={item.stock <= 0}>
                        {item.name} ({item.color}/{item.size}) - 現有 {item.stock} 件
                      </option>
                    ))}
                  </select>
                  <input
                    type="number"
                    min="1"
                    value={selectedStockForBox.qty}
                    onChange={(e) => setSelectedStockForBox({...selectedStockForBox, qty: e.target.value})}
                    className="w-16 border border-[#DDD6C8] rounded-xl px-2 py-1.5 text-xs text-center outline-none"
                  />
                  <button
                    type="button"
                    onClick={handleAddItemToShipmentDraft}
                    className="bg-[#433F3A] hover:bg-[#2D2A26] text-white px-3 py-1.5 rounded-xl text-xs font-medium"
                  >
                    放入箱內
                  </button>
                </div>

                {/* 已放入草稿清單 */}
                {newShipment.items.length > 0 && (
                  <div className="mt-2 pt-2 border-t border-[#E8E2D5] space-y-1">
                    <div className="text-[11px] font-medium text-[#7D7566]">已裝入此箱之品項：</div>
                    {newShipment.items.map((it, idx) => (
                      <div key={idx} className="flex justify-between items-center text-xs py-1 px-2 bg-white rounded-lg border border-[#EDE8DE]">
                        <span>{it.name} <span className="text-[#8C8476]">({it.color}/{it.size})</span></span>
                        <span className="font-bold text-[#2D2A26]">{it.qty} 件</span>
                      </div>
                    ))}
                  </div>
                )}
              </div>

              <div className="flex justify-end gap-2 pt-2">
                <button
                  type="button"
                  onClick={() => setShowShipmentModal(false)}
                  className="px-4 py-2 rounded-xl text-xs font-medium text-[#696256] hover:bg-[#F2EFE8]"
                >
                  取消
                </button>
                <button
                  type="submit"
                  disabled={newShipment.items.length === 0}
                  className="bg-[#2D2A26] disabled:bg-[#A59F94] hover:bg-[#433F3A] text-white px-5 py-2 rounded-xl text-xs font-medium"
                >
                  完成並生成裝箱清單
                </button>
              </div>
            </form>
          </div>
        </div>
      )}

      {/* ---------------- 列印/預覽出貨裝箱單 (Packing List) ---------------- */}
      {printManifestBox && (
        <div className="fixed inset-0 bg-black/50 backdrop-blur-sm z-50 flex items-center justify-center p-4">
          <div className="bg-white rounded-2xl max-w-2xl w-full p-8 shadow-2xl border border-stone-200 text-stone-900 max-h-[90vh] overflow-y-auto">
            {/* 裝箱單頭部 */}
            <div className="border-b-2 border-stone-900 pb-4 flex justify-between items-start">
              <div>
                <h1 className="text-xl font-bold tracking-tight">KOREA EXPORT PACKING LIST</h1>
                <p className="text-xs text-stone-500 mt-0.5">首爾工作室 出貨裝箱明細單 • STUDIO K SEOUL</p>
              </div>
              <div className="text-right">
                <span className="text-xs font-mono bg-stone-100 px-2 py-1 rounded font-bold">
                  {printManifestBox.id}
                </span>
                <div className="text-[11px] text-stone-500 mt-1">發貨日期: {printManifestBox.dispatchDate}</div>
              </div>
            </div>

            {/* 物流基本資訊 */}
            <div className="grid grid-cols-2 gap-4 my-4 text-xs">
              <div>
                <span className="text-stone-400 block">箱子名稱</span>
                <span className="font-semibold text-stone-800">{printManifestBox.boxName}</span>
              </div>
              <div>
                <span className="text-stone-400 block">運送渠道 / 追蹤單號</span>
                <span className="font-semibold text-stone-800">{printManifestBox.carrier} / {printManifestBox.trackingNo}</span>
              </div>
              <div>
                <span className="text-stone-400 block">目的地點</span>
                <span className="font-semibold text-stone-800">{printManifestBox.destination}</span>
              </div>
              <div>
                <span className="text-stone-400 block">總件數 / 實測重量</span>
                <span className="font-semibold text-stone-800">
                  {printManifestBox.items.reduce((s, i) => s + i.qty, 0)} 件 / {printManifestBox.weightKg} kg
                </span>
              </div>
            </div>

            {/* 商品表格明細 */}
            <table className="w-full text-left text-xs my-4 border-t border-stone-200">
              <thead>
                <tr className="border-b border-stone-200 text-stone-500">
                  <th className="py-2">No.</th>
                  <th className="py-2">SKU 編號</th>
                  <th className="py-2">服飾名稱</th>
                  <th className="py-2">規格 (色/碼)</th>
                  <th className="py-2 text-right">裝箱件數</th>
                </tr>
              </thead>
              <tbody className="divide-y divide-stone-100">
                {printManifestBox.items.map((it, idx) => (
                  <tr key={idx}>
                    <td className="py-2 text-stone-400">{idx + 1}</td>
                    <td className="py-2 font-mono">{it.sku}</td>
                    <td className="py-2 font-medium">{it.name}</td>
                    <td className="py-2">{it.color} / {it.size}</td>
                    <td className="py-2 text-right font-bold text-sm">{it.qty}</td>
                  </tr>
                ))}
              </tbody>
            </table>

            {/* 簽收點核欄 */}
            <div className="mt-8 pt-4 border-t border-dashed border-stone-300 grid grid-cols-2 gap-6 text-xs text-stone-500">
              <div>首爾出貨打包簽章：__________________</div>
              <div>台灣端收貨點收簽章：__________________</div>
            </div>

            {/* 操作列印與關閉按鈕 */}
            <div className="mt-6 flex justify-end gap-2 pt-2 border-t border-stone-100">
              <button
                onClick={() => setPrintManifestBox(null)}
                className="px-4 py-2 rounded-xl text-xs font-medium text-stone-600 hover:bg-stone-100"
              >
                關閉
              </button>
              <button
                onClick={() => window.print()}
                className="bg-stone-900 text-white px-5 py-2 rounded-xl text-xs font-medium flex items-center gap-1.5 hover:bg-stone-800"
              >
                <Printer className="w-3.5 h-3.5" /> 列印裝箱單 (Packing List)
              </button>
            </div>
          </div>
        </div>
      )}
    </div>
  );
}

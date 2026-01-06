import React, { useState, useEffect, useMemo } from 'react';
import { 
  ShoppingBag, 
  User, 
  Search, 
  ChevronRight, 
  X, 
  Plus, 
  Trash2, 
  TrendingUp, 
  Package, 
  LayoutDashboard, 
  Settings,
  ArrowLeft,
  CheckCircle2,
  Menu,
  CreditCard
} from 'lucide-react';

// --- MOCK DATA ---
const INITIAL_PRODUCTS = [
  { id: 1, name: 'Obsidian Oversized Hoodie', category: 'Upperwear', price: 1250, stock: 15, image: 'https://images.unsplash.com/photo-1556821840-3a63f95609a7?auto=format&fit=crop&q=80&w=400', description: 'Heavyweight 450GSM cotton with distressed hem detailing.' },
  { id: 2, name: 'Chrome Cargo Pants', category: 'Bottoms', price: 1800, stock: 8, image: 'https://images.unsplash.com/chttps://www.google.com/url?sa=t&source=web&rct=j&url=https%3A%2F%2Fwww.amazon.in%2F-%2Fhi%2FCargonation-Cargos-%25E0%25A4%25AA%25E0%25A5%2581%25E0%25A4%25B0%25E0%25A5%2581%25E0%25A4%25B7%25E0%25A5%258B%25E0%25A4%2582-%25E0%25A4%25AC%25E0%25A5%2587%25E0%25A4%25B9%25E0%25A4%25A4%25E0%25A4%25B0%25E0%25A5%2580%25E0%25A4%25A8-%25E0%25A4%25AB%25E0%25A5%2588%25E0%25A4%25AC%25E0%25A5%258D%25E0%25A4%25B0%25E0%25A4%25BF%25E0%25A4%2595%2Fdp%2FB0DTK8GK5C&ved=0CBUQjRxqFwoTCICjgLy-9pEDFQAAAAAdAAAAABAI&opi=89978449$0photo-1624372333454-ca61e93628ee?auto=format&fit=crop&q=80&w=400', description: 'Water-resistant tech fabric with metallic hardware accents.' },
  { id: 3, name: 'Cyber-Punk Graphic Tee', category: 'Upperwear', price: 650, stock: 42, image: 'https://images.unsplash.com/photo-1521572163474-6864f9cf17ab?auto=format&fit=crop&q=80&w=400', description: 'Glow-in-the-dark print on premium organic cotton.' },
  { id: 4, name: 'Vanguard Utility Vest', category: 'Outerwear', price: 2200, stock: 5, image: 'https://images.unsplash.com/photo-1591047139829-d91aecb6caea?auto=format&fit=crop&q=80&w=400', description: 'Modular tactical vest with interchangeable pockets.' },
  { id: 5, name: 'Neon-Sole Sneakers', category: 'Footwear', price: 3400, stock: 12, image: 'https://images.unsplash.com/photo-1552346154-21d32810aba3?auto=format&fit=crop&q=80&w=400', description: 'Handcrafted leather sneakers with translucent neon sole.' },
  { id: 6, name: 'Acid-Wash Denim Jacket', category: 'Outerwear', price: 3500, stock: 10, image: 'https://images.unsplash.com/photo-1576871337622-983ef3c41037?auto=format&fit=crop&q=80&w=400', description: 'Vintage silhouette with custom Xurywear embroidery.' },
];

const CATEGORIES = ['All', 'Upperwear', 'Bottoms', 'Outerwear', 'Footwear'];

const App = () => {
  const [products, setProducts] = useState(INITIAL_PRODUCTS);
  const [cart, setCart] = useState([]);
  const [view, setView] = useState('store'); // 'store' | 'seller' | 'product' | 'cart'
  const [selectedProduct, setSelectedProduct] = useState(null);
  const [activeCategory, setActiveCategory] = useState('All');
  const [isMenuOpen, setIsMenuOpen] = useState(false);
  const [searchQuery, setSearchQuery] = useState('');

  // Notifications
  const [toast, setToast] = useState(null);
  const showToast = (msg) => {
    setToast(msg);
    setTimeout(() => setToast(null), 3000);
  };

  // --- STORE LOGIC ---
  const filteredProducts = useMemo(() => {
    return products.filter(p => 
      (activeCategory === 'All' || p.category === activeCategory) &&
      (p.name.toLowerCase().includes(searchQuery.toLowerCase()))
    );
  }, [products, activeCategory, searchQuery]);

  const addToCart = (product) => {
    const existing = cart.find(item => item.id === product.id);
    if (existing) {
      setCart(cart.map(item => item.id === product.id ? { ...item, qty: item.qty + 1 } : item));
    } else {
      setCart([...cart, { ...product, qty: 1 }]);
    }
    showToast(`Added ${product.name} to bag`);
  };

  const removeFromCart = (id) => {
    setCart(cart.filter(item => item.id !== id));
  };

  const cartTotal = cart.reduce((acc, item) => acc + (item.price * item.qty), 0);

  // --- SELLER LOGIC ---
  const [editingProduct, setEditingProduct] = useState(null);
  
  const handleSaveProduct = (e) => {
    e.preventDefault();
    const formData = new FormData(e.target);
    const newProd = {
      id: editingProduct?.id || Date.now(),
      name: formData.get('name'),
      category: formData.get('category'),
      price: Number(formData.get('price')),
      stock: Number(formData.get('stock')),
      image: editingProduct?.image || 'https://images.unsplash.com/photo-1556821840-3a63f95609a7?auto=format&fit=crop&q=80&w=400',
      description: formData.get('description'),
    };

    if (editingProduct) {
      setProducts(products.map(p => p.id === editingProduct.id ? newProd : p));
    } else {
      setProducts([...products, newProd]);
    }
    setEditingProduct(null);
    showToast("Inventory updated successfully");
  };

  const deleteProduct = (id) => {
    setProducts(products.filter(p => p.id !== id));
    showToast("Product removed from catalog");
  };

  // --- VIEWS ---
  
  const Navbar = () => (
    <nav className="sticky top-0 z-50 bg-black/80 backdrop-blur-md border-b border-white/10 px-6 py-4">
      <div className="max-w-7xl mx-auto flex items-center justify-between">
        <div className="flex items-center gap-4">
          <button onClick={() => setIsMenuOpen(!isMenuOpen)} className="lg:hidden text-white">
            <Menu size={24} />
          </button>
          <h1 
            className="text-2xl font-black tracking-tighter text-white cursor-pointer"
            onClick={() => {setView('store'); setActiveCategory('All');}}
          >
            XURY<span className="text-zinc-500">WEAR</span>
          </h1>
        </div>

        <div className="hidden lg:flex gap-8">
          {CATEGORIES.map(cat => (
            <button 
              key={cat} 
              onClick={() => {setActiveCategory(cat); setView('store');}}
              className={`text-xs uppercase tracking-widest font-bold transition-colors ${activeCategory === cat ? 'text-white' : 'text-zinc-500 hover:text-white'}`}
            >
              {cat}
            </button>
          ))}
        </div>

        <div className="flex items-center gap-6">
          <div className="relative hidden md:block">
            <input 
              type="text" 
              placeholder="Search..."
              className="bg-zinc-900 border-none text-white text-sm py-2 pl-10 pr-4 rounded-full w-48 focus:ring-1 focus:ring-white/20"
              value={searchQuery}
              onChange={(e) => setSearchQuery(e.target.value)}
            />
            <Search className="absolute left-3 top-2.5 text-zinc-500" size={16} />
          </div>
          <button onClick={() => setView('cart')} className="relative text-white">
            <ShoppingBag size={22} />
            {cart.length > 0 && (
              <span className="absolute -top-2 -right-2 bg-white text-black text-[10px] font-bold w-4 h-4 rounded-full flex items-center justify-center">
                {cart.length}
              </span>
            )}
          </button>
          <button 
            onClick={() => setView(view === 'seller' ? 'store' : 'seller')}
            className={`p-2 rounded-full transition-all ${view === 'seller' ? 'bg-white text-black' : 'text-white hover:bg-white/10'}`}
          >
            <User size={22} />
          </button>
        </div>
      </div>
    </nav>
  );

  const ProductCard = ({ product }) => (
    <div 
      className="group cursor-pointer"
      onClick={() => { setSelectedProduct(product); setView('product'); }}
    >
      <div className="relative aspect-[3/4] overflow-hidden bg-zinc-900 rounded-lg mb-4">
        <img 
          src={product.image} 
          alt={product.name}
          className="w-full h-full object-cover grayscale transition-all duration-700 group-hover:grayscale-0 group-hover:scale-105"
        />
        {product.stock < 10 && (
          <span className="absolute top-4 left-4 bg-red-600 text-[10px] text-white font-bold px-2 py-1 uppercase">
            Limited Stock
          </span>
        )}
      </div>
      <h3 className="text-white font-medium text-sm mb-1">{product.name}</h3>
      <p className="text-zinc-500 text-xs mb-2 uppercase tracking-tight">{product.category}</p>
      <p className="text-white font-bold">₹{product.price.toLocaleString('en-IN')}</p>
    </div>
  );

  const Storefront = () => (
    <div className="max-w-7xl mx-auto px-6 py-12">
      <div className="mb-12">
        <h2 className="text-4xl md:text-6xl font-black text-white mb-4 tracking-tighter">
          THE FUTURE IS <br /> <span className="text-zinc-600 italic">ALREADY HERE.</span>
        </h2>
        <p className="text-zinc-500 max-w-lg leading-relaxed">
          Premium streetwear curated for the Indian urban landscape. High performance fabrics meets avant-garde silhouettes.
        </p>
      </div>

      <div className="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-8">
        {filteredProducts.map(p => (
          <ProductCard key={p.id} product={p} />
        ))}
      </div>
    </div>
  );

  const ProductDetail = () => (
    <div className="max-w-7xl mx-auto px-6 py-12 animate-in fade-in duration-500">
      <button 
        onClick={() => setView('store')}
        className="flex items-center gap-2 text-zinc-500 hover:text-white transition-colors mb-12"
      >
        <ArrowLeft size={20} /> Back to Catalog
      </button>

      <div className="grid grid-cols-1 md:grid-cols-2 gap-16">
        <div className="aspect-[3/4] bg-zinc-900 rounded-xl overflow-hidden">
          <img src={selectedProduct.image} className="w-full h-full object-cover" alt="" />
        </div>
        <div className="flex flex-col justify-center">
          <p className="text-zinc-500 uppercase tracking-widest text-sm mb-4">{selectedProduct.category}</p>
          <h2 className="text-5xl font-black text-white mb-6 tracking-tighter">{selectedProduct.name}</h2>
          <p className="text-3xl text-white font-bold mb-8">₹{selectedProduct.price.toLocaleString('en-IN')}</p>
          <p className="text-zinc-400 mb-12 leading-relaxed text-lg">{selectedProduct.description}</p>
          
          <div className="flex gap-4">
            <button 
              onClick={() => addToCart(selectedProduct)}
              className="flex-1 bg-white text-black py-5 rounded-full font-black uppercase tracking-widest hover:bg-zinc-200 transition-colors"
            >
              Add to Bag
            </button>
            <button className="p-5 border border-white/20 rounded-full hover:bg-white/10 text-white transition-colors">
              <Plus size={24} />
            </button>
          </div>

          <div className="mt-12 space-y-4">
            <div className="flex items-center gap-3 text-zinc-500 text-sm">
              <CheckCircle2 size={18} className="text-white" />
              <span>Complimentary pan-India white glove delivery</span>
            </div>
            <div className="flex items-center gap-3 text-zinc-500 text-sm">
              <CheckCircle2 size={18} className="text-white" />
              <span>Sustainable luxury packaging</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  );

  const CartView = () => (
    <div className="max-w-4xl mx-auto px-6 py-12">
      <h2 className="text-4xl font-black text-white mb-12 tracking-tighter uppercase">Your Bag ({cart.length})</h2>
      
      {cart.length === 0 ? (
        <div className="py-20 text-center border border-dashed border-white/10 rounded-3xl">
          <p className="text-zinc-500 mb-6">Your shopping bag is empty.</p>
          <button onClick={() => setView('store')} className="bg-white text-black px-8 py-3 rounded-full font-bold">START BROWSING</button>
        </div>
      ) : (
        <div className="space-y-8">
          {cart.map(item => (
            <div key={item.id} className="flex gap-6 items-center border-b border-white/10 pb-8">
              <div className="w-24 h-24 bg-zinc-900 rounded-lg overflow-hidden flex-shrink-0">
                <img src={item.image} className="w-full h-full object-cover" alt="" />
              </div>
              <div className="flex-1">
                <h3 className="text-white font-bold">{item.name}</h3>
                <p className="text-zinc-500 text-sm uppercase">{item.category}</p>
              </div>
              <div className="flex items-center gap-6">
                <div className="text-zinc-400 text-sm">Qty: {item.qty}</div>
                <div className="text-white font-bold w-24 text-right">₹{(item.price * item.qty).toLocaleString()}</div>
                <button onClick={() => removeFromCart(item.id)} className="text-zinc-500 hover:text-red-500">
                  <Trash2 size={20} />
                </button>
              </div>
            </div>
          ))}

          <div className="mt-12 pt-12 border-t-2 border-white flex flex-col items-end">
            <div className="flex gap-12 mb-8">
              <span className="text-zinc-500 font-bold uppercase tracking-widest">Total Amount</span>
              <span className="text-3xl text-white font-black">₹{cartTotal.toLocaleString()}</span>
            </div>
            <button className="bg-white text-black w-full md:w-auto px-16 py-5 rounded-full font-black uppercase tracking-widest hover:bg-zinc-200">
              Proceed to Checkout
            </button>
          </div>
        </div>
      )}
    </div>
  );

  const SellerDashboard = () => (
    <div className="max-w-7xl mx-auto px-6 py-12">
      <div className="flex items-center justify-between mb-12">
        <div>
          <h2 className="text-4xl font-black text-white tracking-tighter uppercase">Merchant Panel</h2>
          <p className="text-zinc-500">Manage your collections and monitor store performance.</p>
        </div>
        <button 
          onClick={() => setEditingProduct({})}
          className="bg-white text-black flex items-center gap-2 px-6 py-3 rounded-full font-bold hover:bg-zinc-200 transition-all"
        >
          <Plus size={20} /> NEW PRODUCT
        </button>
      </div>

      {/* Stats */}
      <div className="grid grid-cols-1 md:grid-cols-3 gap-6 mb-12">
        <div className="bg-zinc-900 p-6 rounded-2xl border border-white/5">
          <div className="flex items-center gap-3 text-zinc-500 text-xs font-bold uppercase mb-4">
            <TrendingUp size={16} /> Monthly Revenue
          </div>
          <p className="text-3xl font-black text-white tracking-tighter">₹8,42,000</p>
          <p className="text-green-500 text-xs font-bold mt-2">+12% from last month</p>
        </div>
        <div className="bg-zinc-900 p-6 rounded-2xl border border-white/5">
          <div className="flex items-center gap-3 text-zinc-500 text-xs font-bold uppercase mb-4">
            <Package size={16} /> Active Listings
          </div>
          <p className="text-3xl font-black text-white tracking-tighter">{products.length}</p>
          <p className="text-zinc-500 text-xs font-bold mt-2">Across 4 collections</p>
        </div>
        <div className="bg-zinc-900 p-6 rounded-2xl border border-white/5">
          <div className="flex items-center gap-3 text-zinc-500 text-xs font-bold uppercase mb-4">
            <User size={16} /> Total Customers
          </div>
          <p className="text-3xl font-black text-white tracking-tighter">2,148</p>
          <p className="text-zinc-500 text-xs font-bold mt-2">Organic growth active</p>
        </div>
      </div>

      <div className="bg-zinc-900/50 rounded-3xl border border-white/5 overflow-hidden">
        <div className="overflow-x-auto">
          <table className="w-full text-left">
            <thead>
              <tr className="border-b border-white/10 text-zinc-500 text-[10px] font-bold uppercase tracking-widest">
                <th className="px-8 py-6">Product</th>
                <th className="px-8 py-6">Category</th>
                <th className="px-8 py-6">Price</th>
                <th className="px-8 py-6">Stock</th>
                <th className="px-8 py-6 text-right">Actions</th>
              </tr>
            </thead>
            <tbody>
              {products.map(p => (
                <tr key={p.id} className="border-b border-white/5 group hover:bg-white/[0.02] transition-all">
                  <td className="px-8 py-4">
                    <div className="flex items-center gap-4">
                      <img src={p.image} className="w-12 h-12 rounded-lg object-cover bg-zinc-800" alt="" />
                      <span className="text-white font-bold">{p.name}</span>
                    </div>
                  </td>
                  <td className="px-8 py-4 text-zinc-400 text-sm">{p.category}</td>
                  <td className="px-8 py-4 text-white font-medium">₹{p.price.toLocaleString()}</td>
                  <td className="px-8 py-4">
                    <span className={`px-2 py-1 rounded text-[10px] font-bold ${p.stock < 10 ? 'bg-red-500/10 text-red-500' : 'bg-green-500/10 text-green-500'}`}>
                      {p.stock} UNITS
                    </span>
                  </td>
                  <td className="px-8 py-4 text-right">
                    <div className="flex items-center justify-end gap-3">
                      <button onClick={() => setEditingProduct(p)} className="p-2 text-zinc-500 hover:text-white transition-colors">
                        <Settings size={18} />
                      </button>
                      <button onClick={() => deleteProduct(p.id)} className="p-2 text-zinc-500 hover:text-red-500 transition-colors">
                        <Trash2 size={18} />
                      </button>
                    </div>
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </div>

      {/* Editor Modal */}
      {editingProduct && (
        <div className="fixed inset-0 z-[100] bg-black/90 flex items-center justify-center p-6 backdrop-blur-sm">
          <div className="bg-zinc-900 w-full max-w-xl rounded-3xl border border-white/10 overflow-hidden">
            <div className="px-8 py-6 border-b border-white/5 flex items-center justify-between">
              <h3 className="text-white font-black uppercase tracking-widest">
                {editingProduct.id ? 'Edit Product' : 'Add New Release'}
              </h3>
              <button onClick={() => setEditingProduct(null)} className="text-zinc-500 hover:text-white">
                <X size={24} />
              </button>
            </div>
            <form onSubmit={handleSaveProduct} className="p-8 space-y-6">
              <div className="grid grid-cols-2 gap-6">
                <div className="col-span-2">
                  <label className="block text-zinc-500 text-[10px] font-bold uppercase mb-2">Item Name</label>
                  <input name="name" defaultValue={editingProduct.name} required className="w-full bg-black border border-white/10 rounded-xl p-3 text-white focus:border-white transition-colors" />
                </div>
                <div>
                  <label className="block text-zinc-500 text-[10px] font-bold uppercase mb-2">Category</label>
                  <select name="category" defaultValue={editingProduct.category} className="w-full bg-black border border-white/10 rounded-xl p-3 text-white focus:border-white transition-colors">
                    {CATEGORIES.filter(c => c !== 'All').map(c => <option key={c} value={c}>{c}</option>)}
                  </select>
                </div>
                <div>
                  <label className="block text-zinc-500 text-[10px] font-bold uppercase mb-2">Price (INR)</label>
                  <input name="price" type="number" defaultValue={editingProduct.price} required className="w-full bg-black border border-white/10 rounded-xl p-3 text-white focus:border-white transition-colors" />
                </div>
                <div>
                  <label className="block text-zinc-500 text-[10px] font-bold uppercase mb-2">Stock Level</label>
                  <input name="stock" type="number" defaultValue={editingProduct.stock} required className="w-full bg-black border border-white/10 rounded-xl p-3 text-white focus:border-white transition-colors" />
                </div>
                <div className="col-span-2">
                  <label className="block text-zinc-500 text-[10px] font-bold uppercase mb-2">Description</label>
                  <textarea name="description" defaultValue={editingProduct.description} rows="3" className="w-full bg-black border border-white/10 rounded-xl p-3 text-white focus:border-white transition-colors" />
                </div>
              </div>
              <button type="submit" className="w-full bg-white text-black py-4 rounded-xl font-black uppercase tracking-widest hover:bg-zinc-200 transition-all">
                Publish Product
              </button>
            </form>
          </div>
        </div>
      )}
    </div>
  );

  return (
    <div className="min-h-screen bg-black font-sans selection:bg-white selection:text-black">
      <Navbar />

      {/* Main View Area */}
      <main className="pb-24">
        {view === 'store' && <Storefront />}
        {view === 'product' && <ProductDetail />}
        {view === 'cart' && <CartView />}
        {view === 'seller' && <SellerDashboard />}
      </main>

      {/* Mobile Menu Overlay */}
      {isMenuOpen && (
        <div className="fixed inset-0 z-[100] bg-black p-8 lg:hidden animate-in slide-in-from-left">
          <div className="flex justify-between items-center mb-16">
            <h2 className="text-2xl font-black text-white">XURYWEAR</h2>
            <button onClick={() => setIsMenuOpen(false)} className="text-white"><X size={32} /></button>
          </div>
          <div className="flex flex-col gap-8">
            {CATEGORIES.map(cat => (
              <button 
                key={cat} 
                onClick={() => { setActiveCategory(cat); setView('store'); setIsMenuOpen(false); }}
                className="text-4xl font-black text-white text-left tracking-tighter hover:italic"
              >
                {cat}
              </button>
            ))}
          </div>
        </div>
      )}

      {/* Toast Notification */}
      {toast && (
        <div className="fixed bottom-8 left-1/2 -translate-x-1/2 z-[200] bg-white text-black px-6 py-3 rounded-full font-bold shadow-2xl flex items-center gap-3 animate-in fade-in slide-in-from-bottom-4">
          <CheckCircle2 size={18} /> {toast}
        </div>
      )}

      {/* Global Footer (Simple) */}
      <footer className="bg-zinc-950 border-t border-white/5 py-16 px-6">
        <div className="max-w-7xl mx-auto flex flex-col md:flex-row justify-between items-center gap-8">
          <div className="text-center md:text-left">
            <h3 className="text-white font-black tracking-tighter text-xl mb-2">XURYWEAR</h3>
            <p className="text-zinc-600 text-xs uppercase tracking-widest">&copy; 2026 XURYWEAR INDIA PVT LTD. ALL RIGHTS RESERVED.</p>
          </div>
          <div className="flex gap-8 text-zinc-500 text-[10px] font-bold uppercase tracking-widest">
            <a href="#" className="hover:text-white">Instagram</a>
            <a href="#" className="hover:text-white">Terms xyz123@gmail.com </a>
            <a href="#" className="hover:text-white">Privacy</a>
            <a href="#" className="hover:text-white">Support 987654321 </a> 
          </div>
        </div>
      </footer>
    </div>
  );
};

export default App;

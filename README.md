<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>ShopSwift — Stylish Demo Store</title>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700;800&display=swap" rel="stylesheet">
<style>
:root{--bg:#f5f6fa;--card:#fff;--muted:#555;--accent:#7c3aed;--glass:rgba(255,255,255,0.7);--glass-2:rgba(255,255,255,0.4);--radius:12px}
*{box-sizing:border-box}body{margin:0;font-family:Inter,sans-serif;background:#f0f2f5;color:#111}header{display:flex;align-items:center;justify-content:space-between;padding:20px;background:white;box-shadow:0 4px 12px rgba(0,0,0,0.05);position:sticky;top:0;z-index:100}.logo{font-size:24px;font-weight:700;color:var(--accent)}.search{flex:1;margin:0 20px;display:flex;gap:8px}.search input{flex:1;padding:10px;border-radius:999px;border:1px solid #ccc;font-size:14px}.btn{background:linear-gradient(90deg,var(--accent),#3b82f6);color:white;padding:10px 16px;border-radius:999px;border:none;font-weight:600;cursor:pointer;transition:transform .2s ease}.btn:hover{transform:scale(1.05)}.ghost{background:transparent;border:1px solid #ccc;padding:8px 12px;border-radius:8px;cursor:pointer}.hero{display:flex;flex-wrap:wrap;align-items:center;gap:20px;margin:20px 0}.hero-text{flex:1;min-width:280px}.hero-text h1{font-size:36px;margin-bottom:12px}.hero-text p{color:var(--muted);font-size:16px}.hero-img{flex:1;min-width:280px;text-align:center}.hero-img img{max-width:100%;border-radius:var(--radius);box-shadow:0 10px 25px rgba(0,0,0,0.1)}.cats{display:flex;gap:12px;flex-wrap:wrap;margin-bottom:20px}.cat{padding:8px 16px;background:linear-gradient(90deg,rgba(124,58,237,0.1),rgba(59,130,246,0.1));border-radius:999px;cursor:pointer;transition:transform .2s ease}.cat:hover{transform:scale(1.05)}.grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(220px,1fr));gap:18px}.card{background:var(--card);border-radius:var(--radius);padding:14px;box-shadow:0 8px 20px rgba(0,0,0,0.05);display:flex;flex-direction:column;gap:10px;transition:transform .2s ease,box-shadow .2s ease}.card:hover{transform:translateY(-5px);box-shadow:0 12px 28px rgba(0,0,0,0.1)}.card img{border-radius:var(--radius);height:180px;object-fit:cover}.card .price{font-weight:700;font-size:16px}.card .desc{color:var(--muted);font-size:13px}.actions{display:flex;gap:10px;margin-top:auto}.cart-btn{position:relative}.badge{position:absolute;top:-8px;right:-8px;background:#ef4444;color:white;border-radius:999px;padding:3px 7px;font-size:12px}.drawer{position:fixed;right:20px;top:80px;width:360px;max-height:70vh;background:white;border-radius:var(--radius);padding:14px;box-shadow:0 10px 40px rgba(0,0,0,0.2);overflow:auto;display:none;z-index:200}.drawer h3{margin-top:0}.cart-item{display:flex;gap:12px;align-items:center;padding:10px;border-radius:8px;background:#f9f9f9}.qty{display:flex;gap:6px;align-items:center}footer{text-align:center;margin:40px 0;color:var(--muted)}@media(max-width:768px){.hero{flex-direction:column}.grid{grid-template-columns:repeat(auto-fill,minmax(180px,1fr))}.search{margin:10px 0}}
</style>
</head>
<body>
<header>
<div class="logo">ShopSwift</div>
<div class="search">
<input id="q" placeholder="Search products...">
<button class="ghost" onclick="applySearch()">Search</button>
</div>
<div class="cart-btn">
<button class="btn" onclick="toggleCart()">Cart</button>
<div id="badge" class="badge" style="display:none">0</div>
</div>
</header>
<main class="container">
<section class="hero">
<div class="hero-text">
<h1>Shop the Best Products</h1>
<p>Stylish modern e-commerce demo store with 500 products, responsive design, and clean layout.</p>
</div>
<div class="hero-img"><img src="https://picsum.photos/seed/hero/500/300" alt="Hero"></div>
</section>
<section class="cats">
<div class="cat" onclick="filterCat('All')">All</div>
<div class="cat" onclick="filterCat('Electronics')">Electronics</div>
<div class="cat" onclick="filterCat('Clothing')">Clothing</div>
<div class="cat" onclick="filterCat('Footwear')">Footwear</div>
<div class="cat" onclick="filterCat('Home')">Home</div>
<div class="cat" onclick="filterCat('Accessories')">Accessories</div>
</section>
<section class="grid" id="grid"></section>
</main>
<div id="drawer" class="drawer">
<h3>Your Cart</h3>
<div id="cart-list" style="display:flex;flex-direction:column;gap:8px;margin-top:12px"></div>
<div style="margin-top:12px;display:flex;justify-content:space-between;align-items:center">
<div style="font-weight:700">Total: <span id="total">₹0</span></div>
<div style="display:flex;gap:8px">
<button class="ghost" onclick="clearCart()">Clear</button>
<button class="btn" onclick="checkout()">Cash on Delivery</button>
</div>
</div>
</div>
<footer>© <span id="year"></span> ShopSwift • Demo Store</footer>
<script>
const categories=['Electronics','Clothing','Footwear','Home','Accessories'];
const CART={};
function formatINR(v){return '₹'+v.toLocaleString('en-IN')}
function getRandomInt(min,max){return Math.floor(Math.random()*(max-min+1)+min)}
const products=[];
for(let i=1;i<=500;i++){
let cat=categories[getRandomInt(0,categories.length-1)];
products.push({id:i,title:`${cat} Product ${i}`,price:getRandomInt(200,9999),category:cat,desc:`Description for ${cat} Product ${i}`,img:`https://picsum.photos/seed/${i}/200/200`})
}
function renderGrid(list=products){
const grid=document.getElementById('grid');grid.innerHTML='';
list.forEach(p=>{
const card=document.createElement('article');card.className='card';
card.innerHTML=`<img src="${p.img}" alt="${p.title}"><strong>${p.title}</strong><div class="desc">${p.desc}</div><div class="price">${formatINR(p.price)}</div><div class="actions"><button class="btn" onclick="addToCart(${p.id})">Add to cart</button></div>`;
grid.appendChild(card);
});
}
function addToCart(id){if(!CART[id])CART[id]={qty:0,...products.find(p=>p.id===id)};CART[id].qty+=1;updateCartUI()}
function updateCartUI(){const list=document.getElementById('cart-list');list.innerHTML='';let total=0,items=0;Object.values(CART).forEach(it=>{total+=it.price*it.qty;items+=it.qty;const el=document.createElement('div');el.className='cart-item';el.innerHTML=`<div style="flex:1"><strong>${it.title}</strong><div>${formatINR(it.price)}</div></div><div class="qty"><button onclick="changeQty(${it.id},-1)">-</button><div>${it.qty}</div><button onclick="changeQty(${it.id},1)">+</button></div>`;list.appendChild(el);});document.getElementById('total').textContent=formatINR(total);const badge=document.getElementById('badge');badge.style.display=items>0?'inline-block':'none';badge.textContent=items}
function changeQty(id,delta){if(!CART[id])return;CART[id].qty+=delta;if(CART[id].qty<=0)delete CART[id];updateCartUI()}
function toggleCart(){const d=document.getElementById('drawer');d.style.display=d.style.display==='none'?'block':'none';updateCartUI()}
function clearCart(){for(const k in CART)delete CART[k];updateCartUI()}
function checkout(){alert('Cash on Delivery selected!');clearCart();toggleCart()}
function filterCat(cat){if(cat==='All')renderGrid(products);else renderGrid(products.filter(p=>p.category===cat))}
function applySearch(){const q=document.getElementById('q').value.trim().toLowerCase();if(!q)return renderGrid(products);renderGrid(products.filter(p=>p.title.toLowerCase().includes(q)||p.desc.toLowerCase().includes(q)))}
document.getElementById('year').textContent=new Date().getFullYear();
renderGrid();
</script>
</body>
</html>

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>MANIVERSE Store</title>

<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600;700&display=swap" rel="stylesheet">

<style>
*{margin:0;padding:0;box-sizing:border-box;font-family:'Poppins',sans-serif}
body{background:#f4f6f9;color:#222}
header{background:#111;color:#fff;padding:20px;text-align:center}
header h1{font-size:28px}
header p{font-size:14px;color:#ccc}

.hero img{width:100%;height:250px;object-fit:cover}

.categories{display:flex;overflow-x:auto;padding:10px;background:#fff;gap:10px}
.categories button{
padding:8px 15px;border:none;border-radius:20px;
background:#e5e7eb;cursor:pointer;white-space:nowrap
}
.categories button.active{background:#111;color:#fff}

.products{
display:grid;
grid-template-columns:repeat(auto-fill,minmax(160px,1fr));
gap:15px;
padding:15px;
}
.product{
background:#fff;border-radius:12px;padding:10px;
box-shadow:0 4px 10px rgba(0,0,0,0.05);
transition:0.3s;
}
.product:hover{transform:translateY(-5px)}
.product img{
width:100%;height:150px;object-fit:cover;border-radius:10px
}
.product h3{font-size:14px;margin:5px 0}
.product p{font-size:12px;color:#555}
.product .price{font-weight:600;margin:5px 0}
.product button{
width:100%;padding:6px;border:none;
background:#111;color:#fff;border-radius:6px;cursor:pointer
}

.cart-icon{
position:fixed;bottom:20px;right:20px;
background:#111;color:#fff;padding:12px 15px;
border-radius:50px;cursor:pointer;
}
.cart-count{
background:red;color:#fff;
padding:2px 6px;border-radius:50%;font-size:12px;margin-left:5px
}

.cart{
position:fixed;right:-100%;top:0;width:100%;max-width:400px;
height:100%;background:#fff;box-shadow:-5px 0 20px rgba(0,0,0,0.2);
transition:0.3s;padding:20px;overflow-y:auto
}
.cart.active{right:0}
.cart h2{margin-bottom:10px}
.cart-item{display:flex;justify-content:space-between;margin:10px 0}
.cart-item button{padding:2px 6px;margin:0 2px}

.checkout-form input, .checkout-form textarea{
width:100%;padding:8px;margin:5px 0;border:1px solid #ccc;border-radius:6px
}
.checkout-form button{
width:100%;padding:10px;margin-top:10px;
background:#111;color:#fff;border:none;border-radius:6px
}

.whatsapp{
position:fixed;bottom:20px;left:20px;
background:#25D366;color:#fff;padding:10px 15px;
border-radius:50px;text-decoration:none;font-size:14px
}
.confirmation{
background:#d1fae5;padding:10px;margin-top:10px;border-radius:6px
}
</style>
</head>

<body>

<header>
<h1>MANIVERSE Store</h1>
<p>SYSTUMM FAAD DO • FATEHPUR, UP</p>
</header>

<div class="hero">
<img src="https://i.ibb.co/YTPG30XL/interior-clothing-store-with-stylish-merchandise-racks-fashionable-brand-design-casual-wear-modern-b.jpg" loading="lazy" alt="MANIVERSE Store interior">
</div>

<div class="categories" id="categoryButtons"></div>

<div class="products" id="productList"></div>

<div class="cart-icon" onclick="toggleCart()">
🛒 <span class="cart-count" id="cartCount">0</span>
</div>

<div class="cart" id="cart">
<h2>Your Cart</h2>
<div id="cartItems"></div>
<h3 id="grandTotal"></h3>

<form class="checkout-form" id="orderForm" action="https://formspree.io/f/YOUR_FORMSPREE_ID" method="POST">
<input type="hidden" name="order_summary" id="orderSummary">
<input type="text" name="Customer Name" placeholder="Your Name" required>
<input type="tel" name="Phone Number" placeholder="Phone Number" required>
<textarea name="Delivery Address" placeholder="Delivery Address" required></textarea>
<textarea name="Special Instructions" placeholder="Any special instructions"></textarea>
<button type="submit">Place Order</button>
</form>

<div id="confirmation"></div>
</div>

<a class="whatsapp" href="https://wa.me/919984121131" target="_blank">Chat on WhatsApp</a>

<script>

const products = [
{ id:1, name:"Cars (6)", price:280, category:"toys", img:"https://i.ibb.co/Xxm8SkfT/C5-FA7-D4-B-3-CB4-4-D55-9-AE7-78-FC039-C1811.jpg", desc:"Premium toy cars set" },
{ id:2, name:"Shoes (4 pair)", price:1000, category:"menswear", img:"https://i.ibb.co/cSKNxXvw/0-D4-BA739-0032-415-A-9382-993-EEA0449-C7.jpg", desc:"Comfortable stylish shoes" },
{ id:3, name:"T-Shirt Combo", price:499, category:"menswear", img:"https://i.ibb.co/B5K58kpt/IMG-7092.png", desc:"Soft cotton combo pack" },
{ id:4, name:"Jeans", price:799, category:"menswear", img:"https://i.ibb.co/B5K58kpt/IMG-7092.png", desc:"Slim fit denim" },
{ id:5, name:"Kids Toy Truck", price:350, category:"toys", img:"https://i.ibb.co/Xxm8SkfT/C5-FA7-D4-B-3-CB4-4-D55-9-AE7-78-FC039-C1811.jpg", desc:"Durable toy truck" },
{ id:6, name:"Sports Shoes", price:1200, category:"menswear", img:"https://i.ibb.co/cSKNxXvw/0-D4-BA739-0032-415-A-9382-993-EEA0449-C7.jpg", desc:"Running shoes" },
{ id:7, name:"Formal Shirt", price:650, category:"menswear", img:"https://i.ibb.co/B5K58kpt/IMG-7092.png", desc:"Premium cotton shirt" },
{ id:8, name:"Toy Bike", price:400, category:"toys", img:"https://i.ibb.co/Xxm8SkfT/C5-FA7-D4-B-3-CB4-4-D55-9-AE7-78-FC039-C1811.jpg", desc:"Stylish toy bike" }
];

let cart = JSON.parse(localStorage.getItem("cart")) || [];

function saveCart(){
localStorage.setItem("cart",JSON.stringify(cart));
updateCart();
}

function addToCart(id){
const item = cart.find(p=>p.id===id);
if(item){item.qty++}
else{
const product = products.find(p=>p.id===id);
cart.push({...product, qty:1});
}
saveCart();
}

function updateCart(){
document.getElementById("cartCount").innerText = cart.reduce((a,b)=>a+b.qty,0);
const cartItems = document.getElementById("cartItems");
cartItems.innerHTML="";
let total=0;
cart.forEach(item=>{
total+=item.price*item.qty;
cartItems.innerHTML+=`
<div class="cart-item">
<div>
<strong>${item.name}</strong><br>
₹${item.price} x ${item.qty}
</div>
<div>
<button onclick="changeQty(${item.id},-1)">-</button>
<button onclick="changeQty(${item.id},1)">+</button>
<button onclick="removeItem(${item.id})">x</button>
</div>
</div>`;
});
document.getElementById("grandTotal").innerText="Total: ₹"+total;
document.getElementById("orderSummary").value=
cart.map(i=>`${i.name} | Qty: ${i.qty} | ₹${i.price*i.qty}`).join("\n")
+"\nTotal: ₹"+total;
}

function changeQty(id,delta){
const item = cart.find(p=>p.id===id);
item.qty+=delta;
if(item.qty<=0) cart=cart.filter(p=>p.id!==id);
saveCart();
}
function removeItem(id){
cart=cart.filter(p=>p.id!==id);
saveCart();
}

function toggleCart(){
document.getElementById("cart").classList.toggle("active");
}

function renderProducts(filter="all"){
const list=document.getElementById("productList");
list.innerHTML="";
products.filter(p=>filter==="all"||p.category===filter)
.forEach(p=>{
list.innerHTML+=`
<div class="product">
<img src="${p.img}" loading="lazy" alt="${p.name}">
<h3>${p.name}</h3>
<p>${p.desc}</p>
<div class="price">₹${p.price}</div>
<button onclick="addToCart(${p.id})">Add to Cart</button>
</div>`;
});
}

function renderCategories(){
const categories=["all",...new Set(products.map(p=>p.category))];
const container=document.getElementById("categoryButtons");
categories.forEach(cat=>{
const btn=document.createElement("button");
btn.innerText=cat;
btn.onclick=()=>{renderProducts(cat)};
container.appendChild(btn);
});
}

document.getElementById("orderForm").addEventListener("submit",function(){
setTimeout(()=>{
cart=[];
saveCart();
document.getElementById("confirmation").innerHTML=
'<div class="confirmation">Order placed! MANIVERSE Store will contact you on WhatsApp to confirm.</div>';
},1000);
});

renderCategories();
renderProducts();
updateCart();

</script>

</body>
</html>

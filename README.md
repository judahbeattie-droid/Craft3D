# Craft3Dimport { useState, useEffect } from "react";

export default function StoreWebsite() {
  const [products, setProducts] = useState([
    {
      id: 1,
      name: "Pooping Pickle",
      price: 5,
      description: "Pull the tongue and out come the poops.",
      image: "https://via.placeholder.com/400x300"
    },
    {
      id: 2,
      name: "Funny Banana",
      price: 12,
      description: "A ridiculously funny banana toy.",
      image: "https://via.placeholder.com/400x300"
    }
  ]);

  const [cart, setCart] = useState([]);
  const [search, setSearch] = useState("");
  const [newProduct, setNewProduct] = useState({
    name: "",
    price: "",
    description: "",
    image: ""
  });

  // Save products locally so edits stay
  useEffect(() => {
    const saved = localStorage.getItem("products");
    if (saved) setProducts(JSON.parse(saved));
  }, []);

  useEffect(() => {
    localStorage.setItem("products", JSON.stringify(products));
  }, [products]);

  const addToCart = (product) => {
    setCart([...cart, product]);
  };

  const removeFromCart = (index) => {
    const updated = [...cart];
    updated.splice(index, 1);
    setCart(updated);
  };

  const addProduct = (e) => {
    e.preventDefault();
    if (!newProduct.name || !newProduct.price) return;

    setProducts([
      ...products,
      {
        id: Date.now(),
        name: newProduct.name,
        price: parseFloat(newProduct.price),
        description: newProduct.description,
        image: newProduct.image || "https://via.placeholder.com/400x300"
      }
    ]);

    setNewProduct({ name: "", price: "", description: "", image: "" });
  };

  const total = cart.reduce((sum, item) => sum + item.price, 0);

  const filteredProducts = products.filter(p =>
    p.name.toLowerCase().includes(search.toLowerCase())
  );

  return (
    <div className="min-h-screen bg-gray-50 text-gray-900">
      {/* Header */}
      <header className="bg-white shadow-sm sticky top-0 z-50">
        <div className="max-w-7xl mx-auto px-6 py-4 flex justify-between items-center">
          <h1 className="text-2xl font-bold">Fred's Store</h1>
          <input
            placeholder="Search products..."
            className="border rounded-xl px-3 py-2"
            value={search}
            onChange={(e) => setSearch(e.target.value)}
          />
          <button className="bg-black text-white px-4 py-2 rounded-2xl">
            Cart ({cart.length})
          </button>
        </div>
      </header>

      {/* Hero */}
      <section className="max-w-7xl mx-auto px-6 py-16 text-center">
        <h2 className="text-5xl font-bold mb-4">Fred's Online Store</h2>
        <p className="text-gray-600 text-lg">A fun shop for silly gifts and toys.</p>
      </section>

      {/* Products */}
      <section className="max-w-7xl mx-auto px-6 py-12">
        <h3 className="text-3xl font-bold mb-8 text-center">Products</h3>
        <div className="grid md:grid-cols-3 gap-10">
          {filteredProducts.map((product) => (
            <div key={product.id} className="bg-white rounded-2xl shadow-md overflow-hidden hover:shadow-xl transition">
              <img src={product.image} alt={product.name} className="w-full h-56 object-cover" />
              <div className="p-6">
                <h4 className="text-xl font-semibold">{product.name}</h4>
                <p className="text-gray-600 mb-3">{product.description}</p>
                <div className="flex justify-between items-center">
                  <span className="font-bold text-lg">${product.price}</span>
                  <button
                    onClick={() => addToCart(product)}
                    className="bg-black text-white px-4 py-2 rounded-xl"
                  >
                    Add to Cart
                  </button>
                </div>
              </div>
            </div>
          ))}
        </div>
      </section>

      {/* Cart */}
      <section className="max-w-5xl mx-auto px-6 py-12">
        <h3 className="text-2xl font-bold mb-4">Shopping Cart</h3>
        {cart.length === 0 ? (
          <p className="text-gray-600">Your cart is empty</p>
        ) : (
          <div className="space-y-3">
            {cart.map((item, i) => (
              <div key={i} className="flex justify-between bg-white p-4 rounded-xl shadow">
                <span>{item.name}</span>
                <div className="flex gap-4">
                  <span>${item.price}</span>
                  <button onClick={() => removeFromCart(i)} className="text-red-500">Remove</button>
                </div>
              </div>
            ))}
            <div className="text-right font-bold text-lg">Total: ${total.toFixed(2)}</div>
            <button className="mt-4 w-full bg-green-600 text-white py-3 rounded-xl text-lg">
              Checkout (connect Stripe later)
            </button>
          </div>
        )}
      </section>

      {/* Admin */}
      <section className="max-w-4xl mx-auto px-6 py-12">
        <h3 className="text-2xl font-bold mb-6">Admin Panel – Add Product</h3>
        <form onSubmit={addProduct} className="bg-white p-6 rounded-2xl shadow space-y-4">
          <input
            placeholder="Product Name"
            className="w-full border p-3 rounded-xl"
            value={newProduct.name}
            onChange={(e) => setNewProduct({ ...newProduct, name: e.target.value })}
          />
          <input
            placeholder="Price"
            className="w-full border p-3 rounded-xl"
            value={newProduct.price}
            onChange={(e) => setNewProduct({ ...newProduct, price: e.target.value })}
          />
          <input
            placeholder="Image URL"
            className="w-full border p-3 rounded-xl"
            value={newProduct.image}
            onChange={(e) => setNewProduct({ ...newProduct, image: e.target.value })}
          />
          <textarea
            placeholder="Description"
            className="w-full border p-3 rounded-xl"
            value={newProduct.description}
            onChange={(e) => setNewProduct({ ...newProduct, description: e.target.value })}
          />
          <button className="bg-black text-white px-6 py-3 rounded-xl">
            Add Product
          </button>
        </form>
      </section>

      <footer className="bg-black text-white py-8 text-center mt-10">
        <p>© {new Date().getFullYear()} Fred's Store</p>
      </footer>
    </div>
  );
}

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Apex Auto Vault</title>
<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js"></script>

<style>
body {
  margin: 0;
  font-family: 'Segoe UI', sans-serif;
  background: #0b0f1a;
  color: #fff;
}

header {
  background: linear-gradient(90deg, #000, #1a1a1a);
  padding: 15px;
  text-align: center;
  font-size: 26px;
  color: gold;
  font-weight: bold;
}

.container {
  padding: 20px;
}

/* Dashboard */
.dashboard {
  display: flex;
  gap: 20px;
  margin-bottom: 20px;
}

.card-stat {
  flex: 1;
  background: #111827;
  padding: 20px;
  border-radius: 10px;
  text-align: center;
}

.card-stat h2 {
  color: gold;
}

/* Form */
form {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  gap: 10px;
  margin-bottom: 20px;
}

input, select {
  padding: 10px;
  border-radius: 5px;
  border: none;
}

button {
  background: gold;
  border: none;
  padding: 10px;
  cursor: pointer;
  font-weight: bold;
}

/* Search */
.search-bar {
  margin-bottom: 20px;
}

/* Car cards */
.car-list {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(260px, 1fr));
  gap: 15px;
}

.card {
  background: #1f2937;
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 0 12px rgba(255,215,0,0.2);
}

.card img {
  width: 100%;
  height: 150px;
  object-fit: cover;
}

.card-content {
  padding: 15px;
}

.card h3 {
  margin: 0;
  color: gold;
}

.badge {
  display: inline-block;
  padding: 5px 8px;
  border-radius: 5px;
  background: #374151;
  margin-top: 5px;
  font-size: 12px;
}

.delete-btn {
  margin-top: 10px;
  background: crimson;
  color: white;
}
</style>
</head>

<body>

<header>🚗 Apex Auto Vault</header>

<div class="container">

<!-- Dashboard -->
<div class="dashboard">
  <div class="card-stat">
    <h2 id="totalCars">0</h2>
    <p>Total Cars</p>
  </div>
  <div class="card-stat">
    <h2 id="totalValue">$0</h2>
    <p>Total Value</p>
  </div>
</div>

<!-- Add Car -->
<form id="carForm">
  <input type="text" id="name" placeholder="Car Name" required>
  <input type="text" id="brand" placeholder="Brand" required>
  <input type="number" id="price" placeholder="Price $" required>
  <input type="number" id="year" placeholder="Year">
  <input type="text" id="image" placeholder="Image URL">
  
  <select id="type">
    <option value="Supercar">Supercar</option>
    <option value="SUV">SUV</option>
    <option value="Luxury Sedan">Luxury Sedan</option>
  </select>

  <button type="submit">Add Car</button>
</form>

<!-- Search -->
<input type="text" id="search" class="search-bar" placeholder="Search cars...">

<!-- Car List -->
<div class="car-list" id="carList"></div>

</div>

<script>
// Supabase
const supabase = window.supabase.createClient(
  "https://sb_publishable_j9OAyJE2u66UsWl0bG9idQ.supabase.co",
  "sb_publishable_j9OAyJE2u66UsWl0bG9idQ_YltibIT3"
);

let carsData = [];

// Load cars
async function loadCars() {
  const { data } = await supabase.from("cars").select("*");
  carsData = data;
  renderCars(data);
  updateDashboard(data);
}

// Render cars
function renderCars(cars) {
  const list = document.getElementById("carList");
  list.innerHTML = "";

  cars.forEach(car => {
    const card = document.createElement("div");
    card.className = "card";

    card.innerHTML = `
      <img src="${car.image || 'https://via.placeholder.com/300'}">
      <div class="card-content">
        <h3>${car.name}</h3>
        <p>${car.brand}</p>
        <p>$${car.price}</p>
        <div class="badge">${car.type || 'Luxury'}</div>
        <p>${car.year || ''}</p>
        <button class="delete-btn" onclick="deleteCar(${car.id})">Delete</button>
      </div>
    `;

    list.appendChild(card);
  });
}

// Dashboard update
function updateDashboard(cars) {
  document.getElementById("totalCars").innerText = cars.length;

  const total = cars.reduce((sum, car) => sum + Number(car.price || 0), 0);
  document.getElementById("totalValue").innerText = "$" + total;
}

// Add car
document.getElementById("carForm").addEventListener("submit", async (e) => {
  e.preventDefault();

  const car = {
    name: name.value,
    brand: brand.value,
    price: price.value,
    year: year.value,
    image: image.value,
    type: type.value
  };

  await supabase.from("cars").insert([car]);
  e.target.reset();
  loadCars();
});

// Delete
async function deleteCar(id) {
  await supabase.from("cars").delete().eq("id", id);
  loadCars();
}

// Search
document.getElementById("search").addEventListener("input", (e) => {
  const value = e.target.value.toLowerCase();
  const filtered = carsData.filter(car =>
    car.name.toLowerCase().includes(value) ||
    car.brand.toLowerCase().includes(value)
  );
  renderCars(filtered);
});

// Init
loadCars();
</script>

</body>
</html>

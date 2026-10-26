<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Lost and Found Portal</title>

    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: Arial, sans-serif;
            background: #f1f5f9;
            color: #1e293b;
        }

        header {
            background: #123c69;
            color: white;
            padding: 25px 15px;
            text-align: center;
        }

        header h1 {
            margin-bottom: 10px;
        }

        nav {
            background: #0b2545;
            padding: 15px;
            text-align: center;
        }

        nav a {
            color: white;
            text-decoration: none;
            margin: 0 12px;
            font-weight: bold;
        }

        .hero {
            text-align: center;
            padding: 35px 15px;
        }

        .hero h2 {
            color: #123c69;
            margin-bottom: 10px;
        }

        .container {
            max-width: 1000px;
            margin: auto;
            padding: 20px;
        }

        .form-box, .items-box {
            background: white;
            padding: 25px;
            border-radius: 10px;
            margin-bottom: 25px;
            box-shadow: 0 3px 12px #00000012;
        }

        h2 {
            margin-bottom: 18px;
            color: #123c69;
        }

        input, select, textarea {
            width: 100%;
            padding: 12px;
            margin: 8px 0 15px;
            border: 1px solid #cbd5e1;
            border-radius: 5px;
            font-size: 15px;
        }

        textarea {
            resize: vertical;
        }

        button {
            background: #123c69;
            color: white;
            border: none;
            padding: 12px 20px;
            border-radius: 5px;
            cursor: pointer;
        }

        button:hover {
            background: #2563a6;
        }

        .search {
            margin-bottom: 15px;
        }

        .item-card {
            border: 1px solid #dbe3ec;
            border-left: 5px solid #123c69;
            padding: 18px;
            margin: 12px 0;
            border-radius: 6px;
            background: #fff;
        }

        .lost {
            border-left-color: #dc2626;
        }

        .found {
            border-left-color: #16a34a;
        }

        .tag {
            display: inline-block;
            padding: 5px 9px;
            border-radius: 4px;
            background: #e2e8f0;
            margin: 8px 0;
            font-size: 12px;
        }

        footer {
            background: #0b2545;
            color: white;
            text-align: center;
            padding: 18px;
            margin-top: 20px;
        }

        @media (max-width: 600px) {
            nav a {
                display: inline-block;
                margin: 7px;
            }
        }
    </style>
</head>

<body>

<header>
    <h1>Lost and Found Portal</h1>
    <p>A simple platform to reunite people with their belongings</p>
</header>

<nav>
    <a href="#home">Home</a>
    <a href="#report">Report Item</a>
    <a href="#items">View Items</a>
</nav>

<section class="hero" id="home">
    <h2>Have You Lost Something?</h2>
    <p>Report lost or found items and help return belongings to their owners.</p>
</section>

<div class="container">

    <section class="form-box" id="report">
        <h2>Report a Lost or Found Item</h2>

        <form id="itemForm">
            <label for="type">Report Type</label>
            <select id="type" required>
                <option value="Lost">Lost Item</option>
                <option value="Found">Found Item</option>
            </select>

            <label for="itemName">Item Name</label>
            <input type="text" id="itemName"
                   placeholder="Example: Mobile Phone" required>

            <label for="category">Category</label>
            <select id="category" required>
                <option value="">Select Category</option>
                <option>Electronics</option>
                <option>Documents</option>
                <option>Bags</option>
                <option>Keys</option>
                <option>Jewellery</option>
                <option>Other</option>
            </select>

            <label for="location">Location</label>
            <input type="text" id="location"
                   placeholder="Where was it lost or found?" required>

            <label for="date">Date</label>
            <input type="date" id="date" required>

            <label for="description">Description</label>
            <textarea id="description" rows="3"
                      placeholder="Describe the item"></textarea>

            <label for="contact">Contact Information</label>
            <input type="text" id="contact"
                   placeholder="Phone number or email" required>

            <button type="submit">Submit Report</button>
        </form>
    </section>

    <section class="items-box" id="items">
        <h2>Lost and Found Items</h2>

        <input class="search" type="text" id="search"
               placeholder="Search items by name or location...">

        <select id="filter">
            <option value="All">All Items</option>
            <option value="Lost">Lost Items</option>
            <option value="Found">Found Items</option>
        </select>

        <div id="itemList"></div>
    </section>

</div>

<footer>
    <p>&copy; 2026 Lost and Found Portal | College Project</p>
</footer>

<script>
    const form = document.getElementById("itemForm");
    const itemList = document.getElementById("itemList");
    const search = document.getElementById("search");
    const filter = document.getElementById("filter");

    let items = [];

    try {
        items = JSON.parse(
            localStorage.getItem("lostFoundItems") || "[]"
        );
        if (!Array.isArray(items)) items = [];
    } catch {
        items = [];
    }

    form.addEventListener("submit", function(event) {
        event.preventDefault();

        const item = {
            id: Date.now(),
            type: document.getElementById("type").value,
            name: document.getElementById("itemName").value.trim(),
            category: document.getElementById("category").value,
            location: document.getElementById("location").value.trim(),
            date: document.getElementById("date").value,
            description: document.getElementById("description").value.trim(),
            contact: document.getElementById("contact").value.trim()
        };

        items.unshift(item);

        try {
            localStorage.setItem("lostFoundItems", JSON.stringify(items));
        } catch {
            items.shift();
            alert("Unable to save report in this browser.");
            return;
        }

        form.reset();
        displayItems();
        alert("Your report has been submitted successfully!");
    });

    function displayItems() {
        const keyword = search.value.toLowerCase().trim();
        const selectedType = filter.value;

        const filteredItems = items.filter(item => {
            const matchesSearch =
                item.name.toLowerCase().includes(keyword) ||
                item.location.toLowerCase().includes(keyword) ||
                item.category.toLowerCase().includes(keyword);

            const matchesType =
                selectedType === "All" || item.type === selectedType;

            return matchesSearch && matchesType;
        });

        itemList.replaceChildren();

        if (filteredItems.length === 0) {
            const message = document.createElement("p");
            message.textContent = "No items found.";
            itemList.appendChild(message);
            return;
        }

        filteredItems.forEach(item => {
            const card = document.createElement("div");
            card.className = "item-card " + item.type.toLowerCase();

            const title = document.createElement("h3");
            title.textContent = item.name;

            const tag = document.createElement("span");
            tag.className = "tag";
            tag.textContent = item.type + " Item";

            card.append(title, tag);

            function addLine(label, value) {
                if (!value) return;
                const p = document.createElement("p");
                const strong = document.createElement("strong");
                strong.textContent = label + ": ";
                p.append(strong, document.createTextNode(value));
                card.appendChild(p);
            }

            addLine("Category", item.category);
            addLine("Location", item.location);
            addLine("Date", item.date);
            addLine("Description", item.description);
            addLine("Contact", item.contact);

            itemList.appendChild(card);
        });
    }

    search.addEventListener("input", displayItems);
    filter.addEventListener("change", displayItems);

    displayItems();
</script>

</body>
</html>
/* Lost and Found Portal - CSS */

* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: Arial, sans-serif;
    background-color: #f4f7fb;
    color: #333;
}

/* Header */
header {
    background-color: #163b65;
    color: white;
    padding: 20px;
    text-align: center;
}

header h1 {
    font-size: 30px;
    margin-bottom: 8px;
}

header p {
    font-size: 15px;
}

/* Navigation */
nav {
    background-color: #245587;
    padding: 14px;
    text-align: center;
}

nav a {
    color: white;
    text-decoration: none;
    margin: 0 15px;
    font-weight: bold;
}

nav a:hover {
    color: #ffd166;
}

/* Main section */
.container {
    width: 90%;
    max-width: 1100px;
    margin: 30px auto;
}

/* Section heading */
h2 {
    color: #163b65;
    margin-bottom: 20px;
}

/* Search box */
.search-box {
    display: flex;
    gap: 10px;
    margin-bottom: 30px;
}

.search-box input {
    flex: 1;
    padding: 12px;
    border: 1px solid #ccc;
    border-radius: 6px;
    font-size: 16px;
}

/* Buttons */
button {
    background-color: #1976d2;
    color: white;
    border: none;
    padding: 12px 20px;
    border-radius: 6px;
    cursor: pointer;
    font-size: 15px;
}

button:hover {
    background-color: #12569a;
}

/* Item cards */
.items {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(230px, 1fr));
    gap: 20px;
}

.item-card {
    background-color: white;
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0 3px 12px rgba(0, 0, 0, 0.1);
    transition: transform 0.3s;
}

.item-card:hover {
    transform: translateY(-5px);
}

.item-card h3 {
    color: #245587;
    margin-bottom: 12px;
}

.item-card p {
    margin: 10px 0;
    line-height: 1.5;
}

/* Lost and Found labels */
.lost {
    color: #d93025;
    font-weight: bold;
}

.found {
    color: #188038;
    font-weight: bold;
}

/* Forms */
form {
    background-color: white;
    padding: 25px;
    border-radius: 10px;
    box-shadow: 0 3px 12px rgba(0, 0, 0, 0.08);
}

form input,
form select,
form textarea {
    width: 100%;
    padding: 12px;
    margin: 10px 0;
    border: 1px solid #ccc;
    border-radius: 6px;
    font-size: 15px;
}

form textarea {
    min-height: 100px;
    resize: vertical;
}

/* Footer */
footer {
    background-color: #163b65;
    color: white;
    text-align: center;
    padding: 18px;
    margin-top: 40px;
}

/* Mobile responsive design */
@media (max-width: 600px) {
    header h1 {
        font-size: 23px;
    }

    nav a {
        display: inline-block;
        margin: 7px;
    }

    .search-box {
        flex-direction: column;
    }

    .container {
        width: 94%;
    }
}
let items = JSON.parse(localStorage.getItem("lostFoundItems")) || [];

// Display all lost and found items
function displayItems() {
    const itemList = document.getElementById("itemList");
    if (!itemList) return;

    itemList.innerHTML = "";

    items.forEach((item, index) => {
        itemList.innerHTML += `
            <div class="item-card">
                <h3>${escapeHTML(item.name)}</h3>
                <p><b>Category:</b> ${escapeHTML(item.category)}</p>
                <p><b>Location:</b> ${escapeHTML(item.location)}</p>
                <p><b>Date:</b> ${escapeHTML(item.date)}</p>
                <p><b>Status:</b> ${escapeHTML(item.status)}</p>
                <p><b>Description:</b> ${escapeHTML(item.description)}</p>
                <button onclick="markFound(${index})">
                    Mark as Found
                </button>
                <button onclick="deleteItem(${index})">
                    Delete
                </button>
            </div>
        `;
    });
}

// Prevent HTML injection
function escapeHTML(value) {
    return String(value ?? "").replace(/[&<>"']/g, char => ({
        "&": "&amp;",
        "<": "&lt;",
        ">": "&gt;",
        '"': "&quot;",
        "'": "&#39;"
    })[char]);
}

// Add a new lost or found item
function addItem(event) {
    event.preventDefault();

    const item = {
        name: document.getElementById("itemName").value.trim(),
        category: document.getElementById("category").value,
        location: document.getElementById("location").value.trim(),
        date: document.getElementById("date").value,
        status: document.getElementById("status").value,
        description: document.getElementById("description").value.trim()
    };

    if (!item.name || !item.location || !item.date) {
        alert("Please fill in all required fields.");
        return;
    }

    items.push(item);
    saveItems();

    event.target.reset();
    displayItems();
    alert("Item added successfully!");
}

// Save data in browser storage
function saveItems() {
    localStorage.setItem("lostFoundItems", JSON.stringify(items));
}

// Mark an item as found
function markFound(index) {
    items[index].status = "Found";
    saveItems();
    displayItems();
}

// Delete an item
function deleteItem(index) {
    if (confirm("Are you sure you want to delete this item?")) {
        items.splice(index, 1);
        saveItems();
        displayItems();
    }
}

// Search items by name or location
function searchItems() {
    const searchText = document
        .getElementById("searchInput")
        .value.toLowerCase();

    const itemList = document.getElementById("itemList");
    itemList.innerHTML = "";

    items.forEach((item, index) => {
        const matches =
            item.name.toLowerCase().includes(searchText) ||
            item.location.toLowerCase().includes(searchText) ||
            item.category.toLowerCase().includes(searchText);

        if (matches) {
            itemList.innerHTML += `
                <div class="item-card">
                    <h3>${escapeHTML(item.name)}</h3>
                    <p>Category: ${escapeHTML(item.category)}</p>
                    <p>Location: ${escapeHTML(item.location)}</p>
                    <p>Date: ${escapeHTML(item.date)}</p>
                    <p>Status: ${escapeHTML(item.status)}</p>
                    <p>${escapeHTML(item.description)}</p>
                    <button onclick="markFound(${index})">
                        Mark as Found
                    </button>
                </div>
            `;
        }
    });
}

// Show items when the page loads
document.addEventListener("DOMContentLoaded", () => {
    const form = document.getElementById("itemForm");

    if (form) {
        form.addEventListener("submit", addItem);
    }

    const searchInput = document.getElementById("searchInput");

    if (searchInput) {
        searchInput.addEventListener("input", searchItems);
    }

    displayItems();
});

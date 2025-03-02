# pastil-x-sisig-website
│── index.html        # Customer Order Page
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pastil X Sisig - Pre-Order</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <header>
        <h1>Pastil X Sisig Pre-Order</h1>
    </header>

    <div class="container">
        <h2>Place Your Order</h2>
        <form id="orderForm">
            <input type="text" id="name" placeholder="Your Name" required>
            <select id="occupation">
                <option value="student">Student</option>
                <option value="teacher">Teacher</option>
                <option value="other">Other</option>
            </select>
            <input type="text" id="grade_section" placeholder="Grade & Section (if Student)">
            <select id="foodItem">
                <option value="pastil">Pastil</option>
                <option value="sisig">Sisig</option>
                <option value="pastil_x_sisig">Pastil X Sisig</option>
            </select>
            <input type="number" id="quantity" placeholder="Quantity" required>
            <button type="submit">Submit Order</button>
        </form>
    </div>

    <script src="script.js"></script>
</body>
</html>
│── admin.html        # Admin Panel
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Admin Panel - Orders</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <header>
        <h1>Admin Panel - Orders</h1>
    </header>

    <div class="container">
        <h2>Order List</h2>
        <ul id="orderList"></ul>
    </div>

    <script src="script.js"></script>
</body>
</html>
│── script.js         # Firebase Logic
// 🔥 Replace with your Firebase config
const firebaseConfig = {
    apiKey: "YOUR_API_KEY",
    authDomain: "YOUR_AUTH_DOMAIN",
    projectId: "YOUR_PROJECT_ID",
    storageBucket: "YOUR_STORAGE_BUCKET",
    messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
    appId: "YOUR_APP_ID"
};

// Initialize Firebase
firebase.initializeApp(firebaseConfig);
const db = firebase.firestore();

// Handle Order Submission
document.getElementById("orderForm")?.addEventListener("submit", function(event) {
    event.preventDefault();

    const name = document.getElementById("name").value;
    const occupation = document.getElementById("occupation").value;
    const grade_section = document.getElementById("grade_section").value;
    const foodItem = document.getElementById("foodItem").value;
    const quantity = document.getElementById("quantity").value;

    db.collection("orders").add({
        name, occupation, grade_section, foodItem, quantity, status: "Pending",
        timestamp: firebase.firestore.FieldValue.serverTimestamp()
    }).then(() => {
        alert("Order placed successfully!");
        document.getElementById("orderForm").reset();
    }).catch(error => console.error("Error adding order:", error));
});

// Display Orders in Admin Panel
const orderList = document.getElementById("orderList");
if (orderList) {
    db.collection("orders").orderBy("timestamp", "desc").onSnapshot(snapshot => {
        orderList.innerHTML = "";
        snapshot.forEach(doc => {
            const data = doc.data();
            orderList.innerHTML += `<li>
                ${data.name} - ${data.foodItem} x${data.quantity} - ${data.status}
                <button onclick="updateOrderStatus('${doc.id}')">Mark Completed</button>
            </li>`;
        });
    });
}

// Update Order Status
function updateOrderStatus(orderId) {
    db.collection("orders").doc(orderId).update({ status: "Completed" })
        .then(() => alert("Order marked as completed!"))
        .catch(error => console.error("Error updating order:", error));
}
│── styles.css        # Styling
body {
    font-family: Arial, sans-serif;
    background-color: #ffdb58;
    text-align: center;
}

header {
    background-color: #c1272d;
    padding: 10px;
    color: white;
}

.container {
    background: white;
    padding: 20px;
    margin: 20px;
    border-radius: 8px;
}

button {
    background: #c1272d;
    color: white;
    padding: 10px;
    border: none;
    cursor: pointer;
}

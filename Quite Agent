# quiet_agent
quite-agent/
├── server.js
├── package.json
├── .env.example
└── public/
    ├── index.html
    ├── app.js
    └── style.css
{
  "name": "quite-agent",
  "version": "1.0.0",
  "type": "module",
  "dependencies": {
    "express": "^4.18.2",
    "dotenv": "^16.4.0",
    "node-fetch": "^3.3.2",
    "cors": "^2.8.5",
    "stripe": "^14.0.0"
  }
}
import express from "express";
import dotenv from "dotenv";
import fetch from "node-fetch";
import cors from "cors";
import Stripe from "stripe";

dotenv.config();

const app = express();
app.use(cors());
app.use(express.json());
app.use(express.static("public"));

const stripe = new Stripe(process.env.STRIPE_KEY);

// 🤖 AI
app.post("/ai", async (req, res) => {
  const { message } = req.body;

  const response = await fetch("https://api.openai.com/v1/chat/completions", {
    method: "POST",
    headers: {
      "Authorization": `Bearer ${process.env.OPENAI_API_KEY}`,
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      model: "gpt-4o-mini",
      messages: [
        {
          role: "system",
          content: "You are a sales assistant that helps business owners increase sales."
        },
        { role: "user", content: message }
      ]
    })
  });

  const data = await response.json();
  res.json({ reply: data.choices[0].message.content });
});

// 💳 Payment
app.post("/pay", async (req, res) => {
  const session = await stripe.checkout.sessions.create({
    payment_method_types: ["card"],
    line_items: [{
      price_data: {
        currency: "usd",
        product_data: { name: "Quite Agent Subscription" },
        unit_amount: 2500
      },
      quantity: 1
    }],
    mode: "payment",
    success_url: process.env.BASE_URL,
    cancel_url: process.env.BASE_URL
  });

  res.json({ url: session.url });
});

app.listen(3000, () => console.log("Quite Agent Running"));
<!DOCTYPE html>
<html>
<head>
  <title>Quite Agent</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>

<h1>Quite Agent</h1>

<div class="box">
  <h3>AI Sales Engine</h3>
  <input id="msg" placeholder="Enter message">
  <button onclick="send()">Generate</button>
  <p id="reply"></p>
</div>

<div class="box">
  <h3>Subscription</h3>
  <button onclick="pay()">Upgrade</button>
</div>

<script src="app.js"></script>
</body>
</html>
async function send(){
  let msg = document.getElementById("msg").value;

  let res = await fetch("/ai", {
    method:"POST",
    headers:{"Content-Type":"application/json"},
    body: JSON.stringify({message: msg})
  });

  let data = await res.json();
  document.getElementById("reply").innerText = data.reply;
}

async function pay(){
  let res = await fetch("/pay",{method:"POST"});
  let data = await res.json();
  window.location = data.url;
}
body{
  background:#0f172a;
  color:white;
  font-family:Arial;
  text-align:center;
}

.box{
  background:#1e293b;
  margin:20px;
  padding:20px;
  border-radius:10px;
}

input,button{
  padding:10px;
  margin:5px;
}

button{
  background:#38bdf8;
  border:none;
  cursor:pointer;
}

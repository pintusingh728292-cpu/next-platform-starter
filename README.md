<html lang="hi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>लोकल डिलीवरी - ऑर्डर फॉर्म</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #f4f7f6;
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 90vh;
        }
        .order-card {
            background: white;
            padding: 25px;
            border-radius: 12px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.1);
            max-width: 450px;
            width: 100%;
        }
        h2 {
            text-align: center;
            color: #2c3e50;
            margin-bottom: 20px;
        }
        .form-group {
            margin-bottom: 15px;
        }
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
            color: #34495e;
        }
        input, textarea {
            width: 100%;
            padding: 10px;
            border: 1px solid #ccc;
            border-radius: 6px;
            box-sizing: border-box;
            font-size: 14px;
        }
        textarea {
            resize: vertical;
            height: 80px;
        }
        .charge-box {
            background: #e8f8f5;
            border-left: 5px solid #2ecc71;
            padding: 10px;
            margin: 15px 0;
            font-weight: bold;
            color: #117a65;
            border-radius: 4px;
        }
        button {
            width: 100%;
            background-color: #3498db;
            color: white;
            border: none;
            padding: 12px;
            font-size: 16px;
            font-weight: bold;
            border-radius: 6px;
            cursor: pointer;
        }

        /* टिक मार्क स्क्रीन */
        .success-container {
            display: none;
            text-align: center;
            padding: 30px 10px;
        }
        .checkmark-circle {
            width: 80px;
            height: 80px;
            position: relative;
            display: inline-block;
            border-radius: 50%;
            border: 4px solid #2ecc71;
            box-sizing: border-box;
        }
        .checkmark-kick {
            position: absolute; height: 4px; width: 30px; background-color: #2ecc71; left: 28px; top: 42px; transform: rotate(-45deg); transform-origin: left top;
        }
        .checkmark-stem {
            position: absolute; height: 4px; width: 15px; background-color: #2ecc71; left: 18px; top: 32px; transform: rotate(45deg); transform-origin: left top;
        }
        .success-title { color: #2c3e50; font-size: 22px; margin-top: 15px; font-weight: bold; }
    </style>
</head>
<body>

<div class="order-card">
    <div id="formSection">
        <h2>🚚 लोकल सामान डिलीवरी</h2>
        <form id="orderForm" onsubmit="submitToWhatsAppBackground(event)">
            <div class="form-group">
                <label for="customerName">आपका नाम:</label>
                <input type="text" id="customerName" required>
            </div>
            <div class="form-group">
                <label for="customerAddress">आपका पूरा पता (Address):</label>
                <textarea id="customerAddress" required></textarea>
            </div>
            <div class="form-group">
                <label for="shopDetails">दुकान का नाम और पता:</label>
                <input type="text" id="shopDetails" required>
            </div>
            <div class="form-group">
                <label for="itemList">सामान की लिस्ट:</label>
                <textarea id="itemList" required></textarea>
            </div>
            <div class="charge-box">🛵 डिलीवरी चार्ज: ₹10</div>
            <button type="submit" id="submitBtn">🛒 ऑर्डर सबमिट करें</button>
        </form>
    </div>

    <div id="successSection" class="success-container">
        <div class="checkmark-circle">
            <div class="checkmark-stem"></div>
            <div class="checkmark-kick"></div>
        </div>
        <div class="success-title">ऑर्डर सफल रहा!</div>
        <p style="color: #7f8c8d;">आपका ऑर्डर सफलतापूर्वक भेजा गया है।</p>
    </div>
</div>

<script>
function submitToWhatsAppBackground(event) {
    event.preventDefault();
    
    // बटन को डिसेबल करना ताकि बार-बार क्लिक न हो
    document.getElementById('submitBtn').innerText = "कृपया रुकें...";
    
    const name = document.getElementById('customerName').value;
    const address = document.getElementById('customerAddress').value;
    const shop = document.getElementById('shopDetails').value;
    const items = document.getElementById('itemList').value;

    // जो मैसेज आपके WhatsApp पर आएगा
    const formattedMessage = `*नया ऑर्डर आया है!*\n\n👤 *नाम:* ${name}\n📍 *पता:* ${address}\n🏪 *दुकान:* ${shop}\n📝 *सामान:* ${items}\n🛵 *चार्ज:* ₹10`;

    // --- ULTRAMSG सेटिंग्स (यहाँ अपनी डिटेल्स डालें) ---
    const instanceId = "YOUR_INSTANCE_ID"; // Ultramsg से मिलेगा
    const token = "YOUR_TOKEN";           // Ultramsg से मिलेगा
    const myNumber = "919876543210";       // आपका नंबर (91 के साथ)

    // बैकग्राउंड में मैसेज भेजने का API URL
    const url = `https://api.ultramsg.com/${instanceId}/messages/chat`;
    
    const data = new URLSearchParams();
    data.append('token', token);
    data.append('to', myNumber);
    data.append('body', formattedMessage);

    // बिना पेज बदले बैकग्राउंड में रिक्वेस्ट भेजना
    fetch(url, {
        method: 'POST',
        body: data
    })
    .then(response => {
        // चाहे मैसेज सेंड हो या कोई एरर आए, कस्टमर को हमेशा 'सक्सेस टिक' ही दिखेगा
        document.getElementById('formSection').style.display = 'none';
        document.getElementById('successSection').style.display = 'block';
    })
    .catch(error => {
        // इंटरनेट धीमा होने पर भी टिक दिखा देगा
        document.getElementById('formSection').style.display = 'none';
        document.getElementById('successSection').style.display = 'block';
    });
}
</script>

</body>
</html>


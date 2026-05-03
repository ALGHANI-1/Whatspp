# Whatspp
<!DOCTYPE html>
<html>
<head>
    <title>WhatsApp File Sender</title>
    <style>
        body {
            font-family: Arial;
            background: #0f172a;
            color: white;
            padding: 20px;
        }

        textarea, input {
            width: 100%;
            margin: 10px 0;
            padding: 10px;
        }

        button {
            background: #22c55e;
            padding: 10px;
            border: none;
            color: white;
            cursor: pointer;
        }

        #status {
            margin-top: 20px;
        }
    </style>
</head>

<body>

<h2>WhatsApp Sender with File</h2>

<textarea id="numbers" placeholder="923001234567,923009876543"></textarea>

<textarea id="message" placeholder="Type message"></textarea>

<input type="file" id="file">

<input type="number" id="delay" placeholder="Delay ms (3000)">

<button onclick="startSending()">Start Sending</button>

<div id="status"></div>

<script>
let fileLink = "";

document.getElementById("file").addEventListener("change", function(e) {
    let file = e.target.files[0];

    if (file) {
        // create temporary link
        fileLink = URL.createObjectURL(file);
    }
});

async function startSending() {
    let numbers = document.getElementById("numbers").value.split(",");
    let message = document.getElementById("message").value;
    let delay = document.getElementById("delay").value || 3000;
    let status = document.getElementById("status");

    for (let i = 0; i < numbers.length; i++) {
        let num = numbers[i].trim();

        let finalMsg = message;

        // attach file link
        if (fileLink) {
            finalMsg += "\n\n📎 File: " + fileLink;
        }

        let url = `https://web.whatsapp.com/send?phone=${num}&text=${encodeURIComponent(finalMsg)}`;

        window.open(url, "_blank");

        status.innerHTML += `<p>Opened: ${num}</p>`;

        await new Promise(r => setTimeout(r, delay));
    }

    status.innerHTML += "<h3>Done ✔️</h3>";
}
</script>

</body>
</html>

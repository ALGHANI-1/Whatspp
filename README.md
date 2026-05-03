<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bulk WhatsApp Sender</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        /* WhatsApp Green Theme */
        :root {
            --wa-green: #25D366;
            --wa-dark: #075E54;
            --wa-light: #dcf8c6;
        }
        .bg-wa-dark { background-color: var(--wa-dark); }
        .text-wa-dark { color: var(--wa-dark); }
        .bg-wa-green { background-color: var(--wa-green); }
        .hover-bg-wa-green:hover { background-color: #1ebe57; }
        
        /* Custom Scrollbar */
        ::-webkit-scrollbar { width: 8px; }
        ::-webkit-scrollbar-track { background: #f1f1f1; }
        ::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 4px; }
        ::-webkit-scrollbar-thumb:hover { background: #94a3b8; }
    </style>
</head>
<body class="bg-gray-50 text-gray-800 font-sans h-screen flex overflow-hidden">

    <!-- Sidebar -->
    <aside class="w-64 bg-wa-dark text-white flex flex-col h-full shadow-xl z-10 hidden md:flex">
        <div class="p-6 border-b border-green-800 flex items-center gap-3">
            <i class="fab fa-whatsapp text-3xl text-green-400"></i>
            <h1 class="text-xl font-bold tracking-wide">WA Sender</h1>
        </div>
        <nav class="flex-1 p-4">
            <ul class="space-y-2">
                <li>
                    <a href="#" class="flex items-center gap-3 p-3 bg-white/10 rounded-lg transition-colors">
                        <i class="fas fa-paper-plane"></i>
                        <span>Campaigns Bhejein</span>
                    </a>
                </li>
                <li>
                    <a href="#" class="flex items-center gap-3 p-3 hover:bg-white/5 rounded-lg transition-colors">
                        <i class="fas fa-address-book"></i>
                        <span>Contacts List</span>
                    </a>
                </li>
                <li>
                    <a href="#" class="flex items-center gap-3 p-3 hover:bg-white/5 rounded-lg transition-colors">
                        <i class="fas fa-cog"></i>
                        <span>Settings</span>
                    </a>
                </li>
            </ul>
        </nav>
        <div class="p-4 border-t border-green-800 text-sm text-green-200">
            <p>Status: <span id="connectionStatus" class="text-yellow-400 font-semibold"><i class="fas fa-exclamation-circle"></i> Not Connected</span></p>
        </div>
    </aside>

    <!-- Main Content -->
    <main class="flex-1 flex flex-col h-full overflow-y-auto">
        <!-- Header Bar -->
        <header class="bg-white shadow-sm p-4 flex justify-between items-center sticky top-0 z-10">
            <div class="flex items-center gap-3 md:hidden">
                <i class="fab fa-whatsapp text-2xl text-green-500"></i>
                <h1 class="text-lg font-bold text-gray-700">WA Sender</h1>
            </div>
            <h2 class="text-2xl font-semibold text-gray-700 hidden md:block">Naya Campaign Banayein</h2>
            <button id="connectBtn" class="bg-wa-dark text-white px-4 py-2 rounded-lg text-sm font-medium hover:bg-green-800 transition-colors shadow-sm flex items-center gap-2">
                <i class="fas fa-qrcode"></i> WhatsApp Connect Karein
            </button>
        </header>

        <div class="p-6 max-w-7xl mx-auto w-full grid grid-cols-1 lg:grid-cols-12 gap-6">
            
            <!-- Left Column: Form Setup -->
            <div class="lg:col-span-5 space-y-6">
                
                <!-- File Upload Section -->
                <div class="bg-white p-6 rounded-xl shadow-sm border border-gray-100">
                    <h3 class="text-lg font-semibold mb-4 border-b pb-2"><i class="fas fa-users text-gray-400 mr-2"></i> Contacts (CSV File)</h3>
                    <div class="flex flex-col gap-3">
                        <label class="block text-sm font-medium text-gray-700">CSV File Upload Karein (Numbers ki list)</label>
                        <input type="file" id="csvFile" accept=".csv" class="block w-full text-sm text-gray-500 file:mr-4 file:py-2 file:px-4 file:rounded-md file:border-0 file:text-sm file:font-semibold file:bg-green-50 file:text-green-700 hover:file:bg-green-100 cursor-pointer border border-gray-200 rounded-md">
                        <p class="text-xs text-gray-400">Format: sirf phone numbers (e.g. 919876543210)</p>
                    </div>
                </div>

                <!-- Message Section -->
                <div class="bg-white p-6 rounded-xl shadow-sm border border-gray-100">
                    <h3 class="text-lg font-semibold mb-4 border-b pb-2"><i class="fas fa-comment-dots text-gray-400 mr-2"></i> Message Details</h3>
                    
                    <div class="space-y-4">
                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-1">Apna Message Likhein</label>
                            <textarea id="messageText" rows="4" class="w-full border border-gray-300 rounded-lg p-3 focus:ring-2 focus:ring-green-500 focus:border-green-500 outline-none resize-none" placeholder="Hello! Yeh ek test message hai..."></textarea>
                        </div>

                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-1">Image Attach Karein (Optional)</label>
                            <input type="file" id="imageFile" accept="image/*" class="block w-full text-sm text-gray-500 file:mr-4 file:py-2 file:px-4 file:rounded-md file:border-0 file:text-sm file:font-semibold file:bg-blue-50 file:text-blue-700 hover:file:bg-blue-100 cursor-pointer border border-gray-200 rounded-md">
                            <div id="imagePreviewContainer" class="hidden mt-3">
                                <img id="imagePreview" src="" alt="Preview" class="h-24 w-auto rounded border shadow-sm">
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Settings Section -->
                <div class="bg-white p-6 rounded-xl shadow-sm border border-gray-100">
                    <h3 class="text-lg font-semibold mb-4 border-b pb-2"><i class="fas fa-sliders-h text-gray-400 mr-2"></i> Campaign Settings</h3>
                    
                    <div class="flex items-center justify-between gap-4">
                        <div class="flex-1">
                            <label class="block text-sm font-medium text-gray-700 mb-1">Timer / Delay (Seconds)</label>
                            <div class="flex items-center gap-2">
                                <input type="number" id="delayTime" value="5" min="2" class="w-24 border border-gray-300 rounded-lg p-2 text-center focus:ring-2 focus:ring-green-500 outline-none">
                                <span class="text-sm text-gray-500">sec delay har message ke beech</span>
                            </div>
                        </div>
                    </div>

                    <div class="mt-6">
                        <button id="startBtn" class="w-full bg-wa-green hover-bg-wa-green text-white font-bold py-3 px-4 rounded-lg shadow-lg transition-transform transform active:scale-95 flex items-center justify-center gap-2">
                            <i class="fas fa-paper-plane"></i> Campaign Start Karein
                        </button>
                        <button id="stopBtn" class="w-full bg-red-500 hover:bg-red-600 text-white font-bold py-3 px-4 rounded-lg shadow-lg transition-transform transform active:scale-95 flex items-center justify-center gap-2 mt-3 hidden">
                            <i class="fas fa-stop-circle"></i> Campaign Rokein
                        </button>
                    </div>
                </div>

            </div>

            <!-- Right Column: Status Dashboard -->
            <div class="lg:col-span-7 bg-white rounded-xl shadow-sm border border-gray-100 flex flex-col overflow-hidden h-[600px] lg:h-auto">
                <div class="p-5 border-b bg-gray-50 flex justify-between items-center">
                    <h3 class="text-lg font-semibold text-gray-800"><i class="fas fa-chart-line text-blue-500 mr-2"></i> Live Status Dashboard</h3>
                    <div class="flex gap-4 text-sm font-medium">
                        <span class="text-gray-600">Total: <span id="totalCount">0</span></span>
                        <span class="text-green-600">Success: <span id="successCount">0</span></span>
                        <span class="text-red-500">Fail: <span id="failCount">0</span></span>
                    </div>
                </div>
                
                <div class="flex-1 overflow-y-auto p-0">
                    <table class="w-full text-left border-collapse">
                        <thead class="bg-white sticky top-0 shadow-sm">
                            <tr>
                                <th class="p-3 text-sm font-semibold text-gray-600 border-b">#</th>
                                <th class="p-3 text-sm font-semibold text-gray-600 border-b">Phone Number</th>
                                <th class="p-3 text-sm font-semibold text-gray-600 border-b">Status</th>
                                <th class="p-3 text-sm font-semibold text-gray-600 border-b text-right">Action</th>
                            </tr>
                        </thead>
                        <tbody id="contactsTableBody" class="divide-y divide-gray-100">
                            <!-- Table rows will be generated here -->
                            <tr>
                                <td colspan="4" class="p-8 text-center text-gray-400">
                                    <i class="fas fa-file-csv text-4xl mb-3 text-gray-300"></i>
                                    <p>CSV file upload karein list dekhne ke liye.</p>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>

        </div>
    </main>

    <!-- Simulated QR Modal -->
    <div id="qrModal" class="fixed inset-0 bg-black/50 hidden items-center justify-center z-50">
        <div class="bg-white p-8 rounded-xl shadow-2xl max-w-sm w-full text-center relative">
            <button id="closeModal" class="absolute top-4 right-4 text-gray-400 hover:text-gray-600"><i class="fas fa-times text-xl"></i></button>
            <h2 class="text-2xl font-bold mb-2">WhatsApp Web</h2>
            <p class="text-gray-600 mb-6 text-sm">Apne phone se WhatsApp open karein aur QR code scan karein.</p>
            <div class="bg-gray-100 p-4 rounded-lg inline-block mb-4">
                <!-- Dummy QR Code Icon -->
                <i class="fas fa-qrcode text-8xl text-gray-800"></i>
            </div>
            <div id="qrLoading" class="hidden mt-2 text-green-600 font-medium animate-pulse">
                Connect ho raha hai...
            </div>
            <button id="simulateScanBtn" class="mt-4 bg-green-500 text-white px-6 py-2 rounded-lg text-sm hover:bg-green-600">
                (Click to Simulate Scan)
            </button>
        </div>
    </div>

    <!-- Alert Modal (Custom) -->
    <div id="alertModal" class="fixed inset-0 bg-black/40 hidden items-center justify-center z-50 transition-opacity">
        <div class="bg-white rounded-lg p-6 max-w-sm w-full shadow-xl transform scale-95 transition-transform">
            <div class="flex items-center gap-3 mb-4">
                <i class="fas fa-info-circle text-blue-500 text-2xl"></i>
                <h3 id="alertTitle" class="text-lg font-bold text-gray-800">Notice</h3>
            </div>
            <p id="alertMessage" class="text-gray-600 mb-6"></p>
            <div class="flex justify-end">
                <button onclick="closeAlert()" class="bg-gray-800 text-white px-4 py-2 rounded-lg hover:bg-gray-700">Theek hai</button>
            </div>
        </div>
    </div>

    <script>
        // --- Variables ---
        let contactsList = [];
        let isCampaignRunning = false;
        let isConnected = false;
        let currentIndex = 0;
        let successCount = 0;
        let failCount = 0;
        let timerId = null;

        // --- DOM Elements ---
        const csvFileInput = document.getElementById('csvFile');
        const imageFileInput = document.getElementById('imageFile');
        const imagePreview = document.getElementById('imagePreview');
        const imagePreviewContainer = document.getElementById('imagePreviewContainer');
        const contactsTableBody = document.getElementById('contactsTableBody');
        
        const totalCountEl = document.getElementById('totalCount');
        const successCountEl = document.getElementById('successCount');
        const failCountEl = document.getElementById('failCount');
        
        const startBtn = document.getElementById('startBtn');
        const stopBtn = document.getElementById('stopBtn');
        const delayInput = document.getElementById('delayTime');
        const messageText = document.getElementById('messageText');
        
        const connectBtn = document.getElementById('connectBtn');
        const connectionStatus = document.getElementById('connectionStatus');
        const qrModal = document.getElementById('qrModal');
        const closeModal = document.getElementById('closeModal');
        const simulateScanBtn = document.getElementById('simulateScanBtn');

        // --- Custom Alert Function ---
        function showAlert(title, message) {
            document.getElementById('alertTitle').innerText = title;
            document.getElementById('alertMessage').innerText = message;
            document.getElementById('alertModal').classList.remove('hidden');
            document.getElementById('alertModal').classList.add('flex');
        }
        function closeAlert() {
            document.getElementById('alertModal').classList.add('hidden');
            document.getElementById('alertModal').classList.remove('flex');
        }

        // --- 1. QR Code Connection Simulation ---
        connectBtn.addEventListener('click', () => {
            qrModal.classList.remove('hidden');
            qrModal.classList.add('flex');
        });

        closeModal.addEventListener('click', () => {
            qrModal.classList.add('hidden');
            qrModal.classList.remove('flex');
        });

        simulateScanBtn.addEventListener('click', () => {
            document.getElementById('qrLoading').classList.remove('hidden');
            setTimeout(() => {
                isConnected = true;
                qrModal.classList.add('hidden');
                qrModal.classList.remove('flex');
                
                connectBtn.innerHTML = '<i class="fas fa-check-circle"></i> Connected';
                connectBtn.classList.remove('bg-wa-dark');
                connectBtn.classList.add('bg-wa-green');
                
                connectionStatus.innerHTML = '<i class="fas fa-check-circle"></i> Connected';
                connectionStatus.classList.remove('text-yellow-400');
                connectionStatus.classList.add('text-green-400');
                
                showAlert('Success', 'WhatsApp account successfully connect ho gaya hai!');
            }, 1500);
        });

        // --- 2. Image Upload Preview ---
        imageFileInput.addEventListener('change', function() {
            const file = this.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    imagePreview.src = e.target.result;
                    imagePreviewContainer.classList.remove('hidden');
                }
                reader.readAsDataURL(file);
            } else {
                imagePreviewContainer.classList.add('hidden');
            }
        });

        // --- 3. CSV File Parsing ---
        csvFileInput.addEventListener('change', function(e) {
            const file = e.target.files[0];
            if (!file) return;

            const reader = new FileReader();
            reader.onload = function(event) {
                const text = event.target.result;
                // Basic CSV split line by line
                const lines = text.split(/\r?\n/);
                contactsList = [];
                
                lines.forEach(line => {
                    // Extracting first column assuming it's the phone number
                    let phone = line.split(',')[0].trim();
                    // Remove non-numeric chars except +
                    phone = phone.replace(/[^\d+]/g, ''); 
                    
                    if (phone.length > 5) {
                        contactsList.push({ phone: phone, status: 'Pending' });
                    }
                });

                updateTable();
                resetCounters();
            };
            reader.readAsText(file);
        });

        function updateTable() {
            contactsTableBody.innerHTML = '';
            totalCountEl.innerText = contactsList.length;

            if (contactsList.length === 0) {
                contactsTableBody.innerHTML = `<tr><td colspan="4" class="p-8 text-center text-gray-400">Koi valid number nahi mila.</td></tr>`;
                return;
            }

            contactsList.forEach((contact, index) => {
                let statusBadge = `<span class="bg-gray-100 text-gray-600 py-1 px-2 rounded text-xs font-medium border">Pending</span>`;
                if (contact.status === 'Sent') statusBadge = `<span class="bg-green-100 text-green-700 py-1 px-2 rounded text-xs font-medium border border-green-200"><i class="fas fa-check"></i> Sent</span>`;
                if (contact.status === 'Failed') statusBadge = `<span class="bg-red-100 text-red-700 py-1 px-2 rounded text-xs font-medium border border-red-200"><i class="fas fa-times"></i> Failed</span>`;
                if (contact.status === 'Sending...') statusBadge = `<span class="bg-blue-100 text-blue-700 py-1 px-2 rounded text-xs font-medium border border-blue-200"><i class="fas fa-spinner fa-spin"></i> Bhej rahe hain...</span>`;

                const tr = document.createElement('tr');
                tr.id = `row-${index}`;
                tr.className = "hover:bg-gray-50 transition-colors";
                tr.innerHTML = `
                    <td class="p-3 border-b text-sm text-gray-500">${index + 1}</td>
                    <td class="p-3 border-b text-sm font-medium text-gray-700">${contact.phone}</td>
                    <td class="p-3 border-b" id="status-${index}">${statusBadge}</td>
                    <td class="p-3 border-b text-right">
                        <a href="https://api.whatsapp.com/send?phone=${contact.phone}&text=${encodeURIComponent(messageText.value)}" target="_blank" class="text-green-500 hover:text-green-700 text-sm" title="Manual Send">
                            <i class="fas fa-external-link-alt"></i>
                        </a>
                    </td>
                `;
                contactsTableBody.appendChild(tr);
            });
        }

        function updateRowStatus(index, newStatus) {
            contactsList[index].status = newStatus;
            
            let statusBadge = '';
            if (newStatus === 'Sent') statusBadge = `<span class="bg-green-100 text-green-700 py-1 px-2 rounded text-xs font-medium border border-green-200"><i class="fas fa-check"></i> Sent</span>`;
            else if (newStatus === 'Failed') statusBadge = `<span class="bg-red-100 text-red-700 py-1 px-2 rounded text-xs font-medium border border-red-200"><i class="fas fa-times"></i> Failed</span>`;
            else if (newStatus === 'Sending...') statusBadge = `<span class="bg-blue-100 text-blue-700 py-1 px-2 rounded text-xs font-medium border border-blue-200"><i class="fas fa-spinner fa-spin"></i> Bhej rahe hain...</span>`;
            
            const statusCell = document.getElementById(`status-${index}`);
            if (statusCell) {
                statusCell.innerHTML = statusBadge;
                // Scroll to active row
                document.getElementById(`row-${index}`).scrollIntoView({ behavior: 'smooth', block: 'center' });
            }
        }

        function resetCounters() {
            successCount = 0;
            failCount = 0;
            currentIndex = 0;
            successCountEl.innerText = '0';
            failCountEl.innerText = '0';
        }

        // --- 4. Campaign Execution Logic ---
        startBtn.addEventListener('click', () => {
            if (!isConnected) {
                showAlert('Connection Error', 'Pehle WhatsApp Connect karein (Upar button par click karein).');
                return;
            }
            if (contactsList.length === 0) {
                showAlert('Data Missing', 'Pehle contacts ki CSV file upload karein.');
                return;
            }
            if (!messageText.value.trim() && !imageFileInput.files.length) {
                showAlert('Data Missing', 'Koi message likhein ya image attach karein.');
                return;
            }

            // UI changes for running state
            isCampaignRunning = true;
            startBtn.classList.add('hidden');
            stopBtn.classList.remove('hidden');
            
            // Start the loop
            sendNextMessage();
        });

        stopBtn.addEventListener('click', () => {
            isCampaignRunning = false;
            clearTimeout(timerId);
            startBtn.classList.remove('hidden');
            stopBtn.classList.add('hidden');
            showAlert('Campaign Stopped', 'Aapka campaign rok diya gaya hai.');
        });

        function sendNextMessage() {
            if (!isCampaignRunning) return;
            
            if (currentIndex >= contactsList.length) {
                isCampaignRunning = false;
                startBtn.classList.remove('hidden');
                stopBtn.classList.add('hidden');
                showAlert('Campaign Completed', `Campaign khatam ho gaya! \nTotal Bheje: ${successCount}\nFail Hue: ${failCount}`);
                return;
            }

            // Skip if already sent
            if (contactsList[currentIndex].status === 'Sent' || contactsList[currentIndex].status === 'Failed') {
                currentIndex++;
                sendNextMessage();
                return;
            }

            // Set status to sending
            updateRowStatus(currentIndex, 'Sending...');

            // Simulated Delay based on user input
            const delayInMs = parseInt(delayInput.value) * 1000;

            timerId = setTimeout(() => {
                // Here we SIMULATE success or failure.
                // In a real application, you would make an API call to your Node.js backend here.
                
                // Randomly simulating a 10% failure rate for realism
                const isSuccess = Math.random() > 0.1; 

                if (isSuccess) {
                    updateRowStatus(currentIndex, 'Sent');
                    successCount++;
                    successCountEl.innerText = successCount;
                } else {
                    updateRowStatus(currentIndex, 'Failed');
                    failCount++;
                    failCountEl.innerText = failCount;
                }

                currentIndex++;
                sendNextMessage(); // Recursive call for the next message
            }, delayInMs);
        }

    </script>
</body>
</html>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bulk WhatsApp Sender (Semi-Auto)</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        :root {
            --wa-green: #25D366;
            --wa-dark: #075E54;
        }
        .bg-wa-dark { background-color: var(--wa-dark); }
        .text-wa-dark { color: var(--wa-dark); }
        .bg-wa-green { background-color: var(--wa-green); }
        .hover-bg-wa-green:hover { background-color: #1ebe57; }
        
        /* Custom Scrollbar */
        ::-webkit-scrollbar { width: 6px; height: 6px; }
        ::-webkit-scrollbar-track { background: #f1f1f1; }
        ::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 4px; }
        ::-webkit-scrollbar-thumb:hover { background: #94a3b8; }
        
        body { -webkit-font-smoothing: antialiased; }
    </style>
</head>
<body class="bg-gray-100 text-gray-800 font-sans min-h-screen flex flex-col">

    <!-- Top Navbar (Fixed layout issue) -->
    <nav class="bg-wa-dark text-white shadow-md sticky top-0 z-40">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 h-16 flex items-center justify-between">
            <div class="flex items-center gap-3">
                <i class="fab fa-whatsapp text-3xl text-green-400"></i>
                <h1 class="text-xl font-bold tracking-wide">WA Sender Pro</h1>
            </div>
            <div class="flex items-center gap-4">
                <span class="hidden sm:inline-block text-sm text-green-200 bg-white/10 px-3 py-1 rounded-full border border-green-800">
                    <i class="fas fa-check-circle text-green-400 mr-1"></i> Ready
                </span>
            </div>
        </div>
    </nav>

    <!-- Main Content Area -->
    <main class="flex-1 w-full max-w-7xl mx-auto px-4 py-6 sm:px-6 lg:px-8">
        
        <!-- Warning/Info Banner -->
        <div class="mb-6 bg-blue-50 text-blue-800 px-4 py-3 rounded-lg text-sm border border-blue-200 flex items-start gap-3 shadow-sm">
            <i class="fas fa-info-circle text-blue-500 mt-0.5 text-lg"></i> 
            <div>
                <p class="font-semibold">Semi-Auto Mode Active</p>
                <p>Is mode mein aapko "Send Next" button par click karna hoga. Har click par naya WhatsApp tab khulega. 
                <span class="font-bold text-red-600">Tip: Agar yahan preview mein button kaam na kare, toh is page ko Download karke Chrome mein chalayein.</span></p>
            </div>
        </div>

        <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">
            
            <!-- Left Column: Controls -->
            <div class="lg:col-span-5 flex flex-col gap-6">
                
                <!-- Setup Card -->
                <div class="bg-white p-5 rounded-xl shadow-sm border border-gray-200">
                    <div class="flex justify-between items-center mb-4 border-b pb-2">
                        <h3 class="text-lg font-semibold"><i class="fas fa-users text-gray-400 mr-2"></i> Contacts</h3>
                        <!-- Sample Data Button (New Feature) -->
                        <button id="loadSampleBtn" class="text-xs bg-gray-100 hover:bg-gray-200 text-gray-700 px-3 py-1.5 rounded-md border transition-colors font-medium">
                            <i class="fas fa-magic text-purple-500 mr-1"></i> Load Sample
                        </button>
                    </div>
                    
                    <div class="mb-5">
                        <label class="block text-sm font-medium text-gray-700 mb-2">CSV Upload Karein</label>
                        <input type="file" id="csvFile" accept=".csv" class="block w-full text-sm text-gray-500 file:mr-4 file:py-2 file:px-4 file:rounded-md file:border-0 file:text-sm file:font-semibold file:bg-green-50 file:text-green-700 hover:file:bg-green-100 cursor-pointer border border-gray-300 rounded-md bg-gray-50">
                    </div>

                    <div class="border-t pt-4">
                        <label class="block text-sm font-medium text-gray-700 mb-2">Message Likhein</label>
                        <textarea id="messageText" rows="3" class="w-full border border-gray-300 rounded-lg p-3 focus:ring-2 focus:ring-green-500 outline-none resize-none bg-gray-50" placeholder="Hello! Yeh ek test message hai..."></textarea>
                    </div>
                </div>

                <!-- Action Card -->
                <div class="bg-white p-5 rounded-xl shadow-sm border border-gray-200 sticky top-24">
                    <h3 class="text-lg font-semibold mb-4 border-b pb-2"><i class="fas fa-play-circle text-gray-400 mr-2"></i> Action</h3>

                    <button id="startBtn" class="w-full bg-wa-green hover:bg-green-600 text-white font-bold py-3.5 px-4 rounded-lg shadow transition-transform active:scale-95 flex items-center justify-center gap-2">
                        <i class="fas fa-play"></i> Start Campaign
                    </button>
                    
                    <!-- Native Link Button (More Robust) -->
                    <a id="nextBtn" href="#" target="_blank" rel="noopener noreferrer" class="w-full bg-blue-500 hover:bg-blue-600 text-white font-bold py-4 px-4 rounded-lg shadow-md transition-all active:scale-95 hidden items-center justify-center gap-2 text-lg text-center shadow-blue-500/30">
                        <i class="fas fa-paper-plane"></i> Send Next (<span id="nextNumberText">...</span>)
                    </a>
                    
                    <button id="stopBtn" class="w-full bg-gray-200 hover:bg-gray-300 text-gray-800 font-bold py-3 px-4 rounded-lg transition-transform active:scale-95 flex items-center justify-center gap-2 mt-3 hidden">
                        <i class="fas fa-redo"></i> Reset
                    </button>
                </div>

            </div>

            <!-- Right Column: Status Table -->
            <div class="lg:col-span-7 bg-white rounded-xl shadow-sm border border-gray-200 flex flex-col min-h-[500px]">
                <div class="p-5 border-b bg-gray-50 flex flex-wrap justify-between items-center rounded-t-xl gap-3">
                    <h3 class="text-lg font-semibold text-gray-800"><i class="fas fa-list-alt text-blue-500 mr-2"></i> Dashboard</h3>
                    <div class="flex gap-4 text-sm font-medium bg-white px-4 py-1.5 rounded-full shadow-sm border">
                        <span class="text-gray-600">Total: <span id="totalCount" class="font-bold">0</span></span>
                        <span class="text-blue-600 border-l pl-4 border-gray-200">Opened: <span id="successCount" class="font-bold">0</span></span>
                    </div>
                </div>
                
                <div class="flex-1 overflow-x-auto">
                    <div class="max-h-[600px] overflow-y-auto">
                        <table class="w-full text-left border-collapse min-w-[400px]">
                            <thead class="bg-gray-50 sticky top-0 z-10 shadow-sm">
                                <tr>
                                    <th class="p-3 text-sm font-semibold text-gray-600 border-b">#</th>
                                    <th class="p-3 text-sm font-semibold text-gray-600 border-b">Number</th>
                                    <th class="p-3 text-sm font-semibold text-gray-600 border-b">Status</th>
                                    <th class="p-3 text-sm font-semibold text-gray-600 border-b text-right">Manual</th>
                                </tr>
                            </thead>
                            <tbody id="contactsTableBody" class="divide-y divide-gray-100">
                                <tr>
                                    <td colspan="4" class="p-12 text-center text-gray-400">
                                        <i class="fas fa-file-csv text-5xl mb-4 text-gray-300"></i>
                                        <p>CSV upload karein ya "Load Sample" par click karein</p>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>

        </div>
    </main>

    <!-- Global Alert Modal -->
    <div id="alertModal" class="fixed inset-0 bg-gray-900/50 hidden items-center justify-center z-[9999] backdrop-blur-sm px-4">
        <div class="bg-white rounded-xl p-6 max-w-sm w-full shadow-2xl transform scale-100">
            <div class="flex items-center gap-3 mb-4">
                <i id="alertIcon" class="fas fa-info-circle text-blue-500 text-3xl"></i>
                <h3 id="alertTitle" class="text-xl font-bold text-gray-800">Notice</h3>
            </div>
            <p id="alertMessage" class="text-gray-600 mb-6 leading-relaxed"></p>
            <div class="flex justify-end">
                <button onclick="closeAlert()" class="bg-gray-800 text-white px-5 py-2.5 rounded-lg hover:bg-gray-700 font-medium w-full sm:w-auto">OK, Got it</button>
            </div>
        </div>
    </div>

    <script>
        // --- Variables ---
        let contactsList = [];
        let isCampaignRunning = false;
        let currentIndex = 0;
        let successCount = 0;

        // --- DOM Elements ---
        const csvFileInput = document.getElementById('csvFile');
        const contactsTableBody = document.getElementById('contactsTableBody');
        const totalCountEl = document.getElementById('totalCount');
        const successCountEl = document.getElementById('successCount');
        const startBtn = document.getElementById('startBtn');
        const nextBtn = document.getElementById('nextBtn');
        const stopBtn = document.getElementById('stopBtn');
        const messageText = document.getElementById('messageText');
        const nextNumberText = document.getElementById('nextNumberText');
        const loadSampleBtn = document.getElementById('loadSampleBtn');
        
        // --- UI Functions ---
        function showAlert(title, message, isSuccess = false) {
            document.getElementById('alertTitle').innerText = title;
            document.getElementById('alertMessage').innerText = message;
            
            const icon = document.getElementById('alertIcon');
            if(isSuccess) {
                icon.className = 'fas fa-check-circle text-green-500 text-3xl';
            } else {
                icon.className = 'fas fa-exclamation-circle text-yellow-500 text-3xl';
            }

            const modal = document.getElementById('alertModal');
            modal.classList.remove('hidden');
            modal.classList.add('flex');
        }

        function closeAlert() {
            const modal = document.getElementById('alertModal');
            modal.classList.add('hidden');
            modal.classList.remove('flex');
        }

        // --- Data Loading ---
        
        // 1. Load Sample Data
        loadSampleBtn.addEventListener('click', () => {
            contactsList = [
                { phone: '919876543210', status: 'Pending' },
                { phone: '919876543211', status: 'Pending' },
                { phone: '919876543212', status: 'Pending' }
            ];
            messageText.value = "Namaste! Yeh WA Sender ka test message hai.";
            
            updateTable();
            resetCounters();
            showAlert('Success', 'Sample data load ho gaya hai! Ab "Start Campaign" par click karein.', true);
        });

        // 2. CSV File Parsing
        csvFileInput.addEventListener('change', function(e) {
            const file = e.target.files[0];
            if (!file) return;

            const reader = new FileReader();
            reader.onload = function(event) {
                const text = event.target.result;
                const lines = text.split(/\r?\n/);
                contactsList = [];
                
                lines.forEach(line => {
                    let phone = line.split(',')[0].trim();
                    phone = phone.replace(/[^\d+]/g, ''); 
                    if (phone.length > 5) {
                        contactsList.push({ phone: phone, status: 'Pending' });
                    }
                });

                updateTable();
                resetCounters();
                csvFileInput.value = ''; // Reset file input
            };
            reader.readAsText(file);
        });

        function updateTable() {
            contactsTableBody.innerHTML = '';
            totalCountEl.innerText = contactsList.length;

            if (contactsList.length === 0) {
                contactsTableBody.innerHTML = `<tr><td colspan="4" class="p-8 text-center text-gray-400">Data khali hai.</td></tr>`;
                return;
            }

            contactsList.forEach((contact, index) => {
                let statusBadge = `<span class="bg-gray-100 text-gray-600 py-1 px-2 rounded text-xs font-medium border border-gray-200">Pending</span>`;
                if (contact.status === 'Opened') statusBadge = `<span class="bg-blue-50 text-blue-700 py-1 px-2 rounded text-xs font-medium border border-blue-200"><i class="fas fa-check"></i> Opened</span>`;
                
                const tr = document.createElement('tr');
                tr.id = `row-${index}`;
                tr.className = "hover:bg-gray-50 transition-colors";
                tr.innerHTML = `
                    <td class="p-3 border-b text-sm text-gray-500">${index + 1}</td>
                    <td class="p-3 border-b text-sm font-medium text-gray-800">${contact.phone}</td>
                    <td class="p-3 border-b" id="status-${index}">${statusBadge}</td>
                    <td class="p-3 border-b text-right">
                        <a href="https://api.whatsapp.com/send?phone=${contact.phone}&text=${encodeURIComponent(messageText.value)}" target="_blank" rel="noopener noreferrer" class="text-green-600 hover:text-green-800 text-sm bg-green-50 px-2 py-1 rounded border border-green-100" title="Manual Send">
                            Open <i class="fas fa-external-link-alt ml-1 text-xs"></i>
                        </a>
                    </td>
                `;
                contactsTableBody.appendChild(tr);
            });
        }

        function updateRowStatus(index, newStatus) {
            contactsList[index].status = newStatus;
            const statusBadge = `<span class="bg-blue-50 text-blue-700 py-1 px-2 rounded text-xs font-medium border border-blue-200"><i class="fas fa-check"></i> Opened</span>`;
            
            const statusCell = document.getElementById(`status-${index}`);
            if (statusCell) {
                statusCell.innerHTML = statusBadge;
                // Scroll to active row
                const row = document.getElementById(`row-${index}`);
                if(row) {
                    row.scrollIntoView({ behavior: 'smooth', block: 'center' });
                }
            }
        }

        function resetCounters() {
            successCount = 0;
            currentIndex = 0;
            successCountEl.innerText = '0';
            
            isCampaignRunning = false;
            startBtn.classList.remove('hidden');
            nextBtn.classList.add('hidden');
            nextBtn.classList.remove('flex');
            stopBtn.classList.add('hidden');
            
            nextBtn.innerHTML = '<i class="fas fa-paper-plane"></i> Send Next (<span id="nextNumberText">...</span>)';
            nextBtn.classList.replace('bg-green-500', 'bg-blue-500');
            nextBtn.classList.replace('hover:bg-green-600', 'hover:bg-blue-600');
        }

        // --- Campaign Logic ---
        startBtn.addEventListener('click', () => {
            if (contactsList.length === 0) {
                showAlert('Data Missing', 'Pehle contacts load karein (CSV ya Sample button se).');
                return;
            }
            if (!messageText.value.trim()) {
                showAlert('Message Missing', 'Koi text message likhein.');
                return;
            }

            isCampaignRunning = true;
            startBtn.classList.add('hidden');
            nextBtn.classList.remove('hidden');
            nextBtn.classList.add('flex');
            stopBtn.classList.remove('hidden');
            
            updateNextButtonUI();
        });

        stopBtn.addEventListener('click', () => {
            contactsList.forEach(c => c.status = 'Pending');
            updateTable();
            resetCounters();
        });

        // The magic link click handler
        nextBtn.addEventListener('click', (e) => {
            if (!isCampaignRunning || currentIndex >= contactsList.length) {
                e.preventDefault();
                return;
            }

            // Let the browser open the link naturally.
            // We just update UI states below.
            
            updateRowStatus(currentIndex, 'Opened');
            successCount++;
            successCountEl.innerText = successCount;
            
            currentIndex++;
            
            // Allow native link opening to process, then update button
            setTimeout(() => {
                if (currentIndex >= contactsList.length) {
                    nextBtn.innerHTML = '<i class="fas fa-check-circle"></i> Campaign Finished';
                    nextBtn.classList.replace('bg-blue-500', 'bg-green-500');
                    nextBtn.classList.replace('hover:bg-blue-600', 'hover:bg-green-600');
                    nextBtn.removeAttribute('href');
                    isCampaignRunning = false;
                    showAlert('Success', `Campaign poora ho gaya! Total ${successCount} tabs open hue.`, true);
                } else {
                    updateNextButtonUI();
                }
            }, 300);
        });

        function updateNextButtonUI() {
            if(currentIndex < contactsList.length) {
                const contact = contactsList[currentIndex];
                document.getElementById('nextNumberText').innerText = contact.phone;
                
                // Set native href BEFORE user clicks
                const text = encodeURIComponent(messageText.value);
                nextBtn.href = `https://api.whatsapp.com/send?phone=${contact.phone}&text=${text}`;
                
                // Highlight table row
                contactsList.forEach((_, idx) => {
                    const row = document.getElementById(`row-${idx}`);
                    if(row) {
                        if(idx === currentIndex) row.classList.add('bg-blue-50');
                        else row.classList.remove('bg-blue-50');
                    }
                });
            }
        }
    </script>
</body>
</html>

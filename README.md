<!doctype html>
<html lang="id" class="h-full">
 <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Data Siswa</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <script src="/_sdk/element_sdk.js"></script>
  <style>
        body {
            box-sizing: border-box;
        }
        
        .table-container {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        }
        
        .table-card {
            backdrop-filter: blur(10px);
            background: rgba(255, 255, 255, 0.95);
            box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04);
        }
        
        .table-header {
            background: linear-gradient(90deg, #4f46e5, #7c3aed);
        }
        
        .table-row:hover {
            background-color: rgba(99, 102, 241, 0.05);
            transform: translateY(-1px);
            transition: all 0.2s ease;
        }
        
        .add-button {
            background: linear-gradient(135deg, #10b981, #059669);
            transition: all 0.3s ease;
        }
        
        .add-button:hover {
            transform: translateY(-2px);
            box-shadow: 0 10px 20px rgba(16, 185, 129, 0.3);
        }
        
        .delete-button {
            background: linear-gradient(135deg, #ef4444, #dc2626);
            transition: all 0.2s ease;
        }
        
        .delete-button:hover {
            transform: scale(1.1);
            box-shadow: 0 5px 15px rgba(239, 68, 68, 0.4);
        }
    </style>
  <style>@view-transition { navigation: auto; }</style>
  <script src="/_sdk/data_sdk.js" type="text/javascript"></script>
 </head>
 <body class="h-full">
  <div class="table-container min-h-full p-6">
   <div class="max-w-4xl mx-auto"><!-- Header -->
    <div class="text-center mb-8">
     <h1 id="table-title" class="text-4xl font-bold text-white mb-2">Data Siswa</h1>
     <p class="text-white/80 text-lg">Kelola data nama dan kelas siswa</p>
    </div><!-- Add New Entry Form -->
    <div class="table-card rounded-2xl p-6 mb-6">
     <h2 class="text-xl font-semibold text-gray-800 mb-4">Tambah Data Baru</h2>
     <form id="add-form" class="flex flex-col sm:flex-row gap-4">
      <div class="flex-1"><input type="text" id="nama-input" placeholder="Masukkan nama siswa" class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-transparent transition-all duration-200" required>
      </div>
      <div class="flex-1"><input type="text" id="kelas-input" placeholder="Masukkan kelas" class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-transparent transition-all duration-200" required>
      </div><button type="submit" class="add-button text-white px-6 py-3 rounded-lg font-medium hover:shadow-lg"> Tambah </button>
     </form>
    </div><!-- Data Table -->
    <div class="table-card rounded-2xl overflow-hidden">
     <table class="w-full">
      <thead class="table-header">
       <tr>
        <th id="nama-header" class="text-left py-4 px-6 text-white font-semibold text-lg">Nama</th>
        <th id="kelas-header" class="text-left py-4 px-6 text-white font-semibold text-lg">Kelas</th>
        <th class="text-center py-4 px-6 text-white font-semibold text-lg">Aksi</th>
       </tr>
      </thead>
      <tbody id="table-body"><!-- Sample data -->
       <tr class="table-row border-b border-gray-100">
        <td class="py-4 px-6 text-gray-800 font-medium">Ahmad Rizki</td>
        <td class="py-4 px-6 text-gray-600">XII IPA 1</td>
        <td class="py-4 px-6 text-center"><button class="delete-button text-white px-3 py-1 rounded-md text-sm font-medium" onclick="deleteRow(this)"> Hapus </button></td>
       </tr>
       <tr class="table-row border-b border-gray-100">
        <td class="py-4 px-6 text-gray-800 font-medium">Siti Nurhaliza</td>
        <td class="py-4 px-6 text-gray-600">XII IPS 2</td>
        <td class="py-4 px-6 text-center"><button class="delete-button text-white px-3 py-1 rounded-md text-sm font-medium" onclick="deleteRow(this)"> Hapus </button></td>
       </tr>
       <tr class="table-row border-b border-gray-100">
        <td class="py-4 px-6 text-gray-800 font-medium">Budi Santoso</td>
        <td class="py-4 px-6 text-gray-600">XI IPA 3</td>
        <td class="py-4 px-6 text-center"><button class="delete-button text-white px-3 py-1 rounded-md text-sm font-medium" onclick="deleteRow(this)"> Hapus </button></td>
       </tr>
      </tbody>
     </table><!-- Empty state -->
     <div id="empty-state" class="hidden text-center py-12">
      <div class="text-gray-400 text-6xl mb-4">
       📚
      </div>
      <h3 class="text-xl font-medium text-gray-600 mb-2">Belum ada data</h3>
      <p class="text-gray-500">Tambahkan data siswa pertama Anda</p>
     </div>
    </div>
   </div>
  </div>
  <script>
        // Default configuration
        const defaultConfig = {
            table_title: "Data Siswa",
            nama_header: "Nama",
            kelas_header: "Kelas"
        };

        // Add new row functionality
        document.getElementById('add-form').addEventListener('submit', function(e) {
            e.preventDefault();
            
            const namaInput = document.getElementById('nama-input');
            const kelasInput = document.getElementById('kelas-input');
            
            const nama = namaInput.value.trim();
            const kelas = kelasInput.value.trim();
            
            if (nama && kelas) {
                addRow(nama, kelas);
                namaInput.value = '';
                kelasInput.value = '';
                namaInput.focus();
                updateEmptyState();
            }
        });

        // Add row to table
        function addRow(nama, kelas) {
            const tableBody = document.getElementById('table-body');
            const row = document.createElement('tr');
            row.className = 'table-row border-b border-gray-100';
            
            row.innerHTML = `
                <td class="py-4 px-6 text-gray-800 font-medium">${nama}</td>
                <td class="py-4 px-6 text-gray-600">${kelas}</td>
                <td class="py-4 px-6 text-center">
                    <button class="delete-button text-white px-3 py-1 rounded-md text-sm font-medium" onclick="deleteRow(this)">
                        Hapus
                    </button>
                </td>
            `;
            
            tableBody.appendChild(row);
        }

        // Delete row functionality
        function deleteRow(button) {
            const row = button.closest('tr');
            row.style.transform = 'translateX(100%)';
            row.style.opacity = '0';
            
            setTimeout(() => {
                row.remove();
                updateEmptyState();
            }, 300);
        }

        // Update empty state visibility
        function updateEmptyState() {
            const tableBody = document.getElementById('table-body');
            const emptyState = document.getElementById('empty-state');
            
            if (tableBody.children.length === 0) {
                emptyState.classList.remove('hidden');
            } else {
                emptyState.classList.add('hidden');
            }
        }

        // Element SDK configuration
        async function onConfigChange(config) {
            const tableTitle = document.getElementById('table-title');
            const namaHeader = document.getElementById('nama-header');
            const kelasHeader = document.getElementById('kelas-header');
            
            tableTitle.textContent = config.table_title || defaultConfig.table_title;
            namaHeader.textContent = config.nama_header || defaultConfig.nama_header;
            kelasHeader.textContent = config.kelas_header || defaultConfig.kelas_header;
        }

        function mapToCapabilities(config) {
            return {
                recolorables: [],
                borderables: [],
                fontEditable: undefined,
                fontSizeable: undefined
            };
        }

        function mapToEditPanelValues(config) {
            return new Map([
                ["table_title", config.table_title || defaultConfig.table_title],
                ["nama_header", config.nama_header || defaultConfig.nama_header],
                ["kelas_header", config.kelas_header || defaultConfig.kelas_header]
            ]);
        }

        // Initialize Element SDK
        if (window.elementSdk) {
            window.elementSdk.init({
                defaultConfig,
                onConfigChange,
                mapToCapabilities,
                mapToEditPanelValues
            });
        }

        // Initialize empty state
        updateEmptyState();
    </script>
 <script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'99895fb450c789b3',t:'MTc2MjE0NjcxNy4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>

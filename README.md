<!DOCTYPE html>
<html lang="pt-BR" class="dark">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Gestão de Transportadoras Parceiras</title>
  
  <!-- Tailwind CSS para estilização rápida e responsiva -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Lucide Icons para ícones modernos -->
  <script src="https://unpkg.com/lucide@latest"></script>

  <script>
    // Configuração de temas customizados no Tailwind
    tailwind.config = {
      darkMode: 'class',
      theme: {
        extend: {
          colors: {
            darkBg: '#09090b',
            cardBg: '#121215',
            borderBg: '#27272a',
            accent: '#3b82f6',
            accentHover: '#2563eb'
          }
        }
      }
    }
  </script>

  <style>
    /* Estilos globais e scrollbar customizada */
    body {
      background-color: #09090b;
      color: #f4f4f5;
      font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
    }
    
    ::-webkit-scrollbar {
      width: 8px;
      height: 8px;
    }
    ::-webkit-scrollbar-track {
      background: #09090b;
    }
    ::-webkit-scrollbar-thumb {
      background: #27272a;
      border-radius: 4px;
    }
    ::-webkit-scrollbar-thumb:hover {
      background: #3f3f46;
    }

    /* Transição suave de telas */
    .fade-transition {
      transition: opacity 0.4s ease-in-out, transform 0.4s ease-in-out;
    }
  </style>
</head>
<body class="min-h-screen flex flex-col justify-between overflow-x-hidden">

  <!-- ==================== SPLASH SCREEN (2 Segundos) ==================== -->
  <div id="splashScreen" class="fixed inset-0 z-50 flex flex-col items-center justify-center bg-black transition-opacity duration-500">
    <div class="animate-pulse flex flex-col items-center">
      <img src="w.png" alt="Logo" class="w-32 h-32 object-contain mb-4 filter drop-shadow-[0_0_20px_rgba(255,255,255,0.2)]" onerror="this.onerror=null; this.src='https://placehold.co/150x150/121215/ffffff?text=W';">
      <div class="flex items-center space-x-2 mt-4">
        <div class="w-2.5 h-2.5 bg-blue-500 rounded-full animate-ping"></div>
        <span class="text-xs tracking-widest text-zinc-400 uppercase">Carregando sistema...</span>
      </div>
    </div>
  </div>

  <!-- ==================== CONTEÚDO PRINCIPAL ==================== -->
  <div id="mainContent" class="opacity-0 transition-opacity duration-500 flex-1 flex flex-col items-center justify-between">
    
    <!-- HEADER DA APLICAÇÃO -->
    <header class="w-full pt-8 pb-4 flex flex-col items-center justify-center border-b border-zinc-800/80 bg-zinc-950/40 backdrop-blur-md">
      <!-- Logo Centralizada -->
      <div class="mb-3 relative group">
        <div class="absolute -inset-1 bg-gradient-to-r from-blue-600 to-indigo-600 rounded-full blur opacity-25 group-hover:opacity-50 transition duration-1000"></div>
        <img src="w.png" alt="Logo do Sistema" class="relative w-20 h-20 object-contain hover:scale-105 transition-transform duration-300" onerror="this.onerror=null; this.src='https://placehold.co/100x100/121215/ffffff?text=W';">
      </div>
      <h1 class="text-xl font-bold tracking-tight text-white flex items-center gap-2">
        <span>Transportadoras Parceiras</span>
      </h1>
      <p class="text-xs text-zinc-400 mt-1">Gestão, Cadastro e Consulta Unificada</p>
    </header>

    <!-- ÁREA CENTRAL DE CONTEÚDO -->
    <main class="w-full max-w-4xl px-4 py-8 flex-1">
      
      <!-- NAVEGAÇÃO ENTRE ABAS -->
      <div class="flex justify-between items-center mb-6 gap-2 flex-wrap">
        <div class="flex bg-zinc-900 p-1 rounded-xl border border-zinc-800 shadow-inner">
          <button id="tabRegisterBtn" onclick="switchTab('register')" class="px-5 py-2.5 rounded-lg text-sm font-medium transition-all duration-200 flex items-center gap-2 bg-zinc-800 text-white shadow">
            <i data-lucide="plus-circle" class="w-4 h-4 text-blue-400"></i>
            Cadastrar
          </button>
          <button id="tabSearchBtn" onclick="switchTab('search')" class="px-5 py-2.5 rounded-lg text-sm font-medium text-zinc-400 hover:text-white transition-all duration-200 flex items-center gap-2">
            <i data-lucide="search" class="w-4 h-4"></i>
            Buscar
          </button>
        </div>

        <!-- BOTÕES DE BACKUP -->
        <div class="flex items-center gap-2">
          <button onclick="exportBackup()" title="Baixar cópia de segurança dos dados" class="px-3.5 py-2 rounded-lg bg-zinc-900 hover:bg-zinc-800 border border-zinc-800 text-xs text-zinc-300 hover:text-white transition flex items-center gap-1.5">
            <i data-lucide="download" class="w-3.5 h-3.5 text-emerald-400"></i>
            Exportar Backup
          </button>
          
          <label title="Restaurar dados de um arquivo salvo" class="px-3.5 py-2 rounded-lg bg-zinc-900 hover:bg-zinc-800 border border-zinc-800 text-xs text-zinc-300 hover:text-white transition cursor-pointer flex items-center gap-1.5">
            <i data-lucide="upload" class="w-3.5 h-3.5 text-amber-400"></i>
            Importar Backup
            <input type="file" id="importFileInput" accept=".json" class="hidden" onchange="importBackup(event)">
          </label>
        </div>
      </div>

      <!-- NOTIFICAÇÃO / MENSAGEM -->
      <div id="toastMessage" class="hidden mb-4 p-3 rounded-xl border text-sm flex items-center justify-between transition-all duration-300">
        <span id="toastText"></span>
        <button onclick="hideToast()" class="text-zinc-400 hover:text-white"><i data-lucide="x" class="w-4 h-4"></i></button>
      </div>

      <!-- ================= SEÇÃO: CADASTRO ================= -->
      <section id="sectionRegister" class="space-y-6">
        <!-- FORMULÁRIO -->
        <div class="bg-zinc-900/80 border border-zinc-800/80 rounded-2xl p-6 shadow-xl backdrop-blur-sm">
          <h2 class="text-base font-semibold text-white mb-4 flex items-center gap-2">
            <i data-lucide="truck" class="w-4 h-4 text-blue-400"></i>
            Dados da Transportadora
          </h2>
          
          <form id="carrierForm" onsubmit="handleSave(event)" class="space-y-4">
            <input type="hidden" id="editIndex" value="-1">
            
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
              <!-- Nome -->
              <div class="space-y-1.5">
                <label class="text-xs font-medium text-zinc-300">Nome da Transportadora *</label>
                <input type="text" id="nameInput" required placeholder="Ex: Logística Express" class="w-full bg-zinc-950 border border-zinc-800 rounded-xl px-4 py-2.5 text-sm text-white focus:outline-none focus:border-blue-500 focus:ring-1 focus:ring-blue-500 transition">
              </div>

              <!-- Telefone / WhatsApp -->
              <div class="space-y-1.5">
                <label class="text-xs font-medium text-zinc-300">Telefone Comercial / WhatsApp *</label>
                <input type="text" id="phoneInput" required placeholder="(00) 90000-0000" oninput="maskPhone(this)" class="w-full bg-zinc-950 border border-zinc-800 rounded-xl px-4 py-2.5 text-sm text-white focus:outline-none focus:border-blue-500 focus:ring-1 focus:ring-blue-500 transition">
              </div>
            </div>

            <!-- Estado (UF) -->
            <div class="space-y-1.5">
              <label class="text-xs font-medium text-zinc-300">Estado (UF) *</label>
              <select id="stateInput" required class="w-full bg-zinc-950 border border-zinc-800 rounded-xl px-4 py-2.5 text-sm text-white focus:outline-none focus:border-blue-500 focus:ring-1 focus:ring-blue-500 transition">
                <option value="">Selecione o Estado</option>
                <option value="AC">Acre (AC)</option>
                <option value="AL">Alagoas (AL)</option>
                <option value="AP">Amapá (AP)</option>
                <option value="AM">Amazonas (AM)</option>
                <option value="BA">Bahia (BA)</option>
                <option value="CE">Ceará (CE)</option>
                <option value="DF">Distrito Federal (DF)</option>
                <option value="ES">Espírito Santo (ES)</option>
                <option value="GO">Goiás (GO)</option>
                <option value="MA">Maranhão (MA)</option>
                <option value="MT">Mato Grosso (MT)</option>
                <option value="MS">Mato Grosso do Sul (MS)</option>
                <option value="MG">Minas Gerais (MG)</option>
                <option value="PA">Pará (PA)</option>
                <option value="PB">Paraíba (PB)</option>
                <option value="PR">Paraná (PR)</option>
                <option value="PE">Pernambuco (PE)</option>
                <option value="PI">Piauí (PI)</option>
                <option value="RJ">Rio de Janeiro (RJ)</option>
                <option value="RN">Rio Grande do Norte (RN)</option>
                <option value="RS">Rio Grande do Sul (RS)</option>
                <option value="RO">Rondônia (RO)</option>
                <option value="RR">Roraima (RR)</option>
                <option value="SC">Santa Catarina (SC)</option>
                <option value="SP">São Paulo (SP)</option>
                <option value="SE">Sergipe (SE)</option>
                <option value="TO">Tocantins (TO)</option>
              </select>
            </div>

            <!-- Botões -->
            <div class="flex items-center gap-3 pt-2">
              <button type="submit" class="flex-1 bg-blue-600 hover:bg-blue-500 text-white font-medium py-2.5 px-4 rounded-xl text-sm transition-all duration-200 flex items-center justify-center gap-2 shadow-lg shadow-blue-600/20">
                <i data-lucide="check-circle" class="w-4 h-4"></i>
                Salvar Transportadora
              </button>
              
              <button type="button" onclick="clearForm()" class="bg-zinc-800 hover:bg-zinc-700 text-zinc-300 font-medium py-2.5 px-4 rounded-xl text-sm transition-all duration-200 flex items-center justify-center gap-2">
                <i data-lucide="rotate-ccw" class="w-4 h-4"></i>
                Limpar / Apagar
              </button>
            </div>
          </form>
        </div>

        <!-- LISTA RECENTE DE CADASTRADOS -->
        <div class="bg-zinc-900/60 border border-zinc-800/80 rounded-2xl p-6 shadow-xl backdrop-blur-sm">
          <div class="flex justify-between items-center mb-4">
            <h3 class="text-sm font-semibold text-zinc-300 flex items-center gap-2">
              <i data-lucide="list" class="w-4 h-4 text-zinc-400"></i>
              Cadastros Realizados (<span id="totalCount">0</span>)
            </h3>
          </div>
          <div id="registerList" class="space-y-3">
            <!-- Renderizado dinamicamente via JS -->
          </div>
        </div>
      </section>

      <!-- ================= SEÇÃO: BUSCA ================= -->
      <section id="sectionSearch" class="hidden space-y-6">
        <div class="bg-zinc-900/80 border border-zinc-800/80 rounded-2xl p-6 shadow-xl backdrop-blur-sm">
          <h2 class="text-base font-semibold text-white mb-4 flex items-center gap-2">
            <i data-lucide="filter" class="w-4 h-4 text-blue-400"></i>
            Filtros de Pesquisa
          </h2>
          
          <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
            <!-- Busca Por Nome -->
            <div class="space-y-1.5">
              <label class="text-xs font-medium text-zinc-300">Buscar por Nome</label>
              <div class="relative">
                <i data-lucide="search" class="w-4 h-4 absolute left-3.5 top-3 text-zinc-500"></i>
                <input type="text" id="searchNameInput" oninput="renderSearchResults()" placeholder="Digite o nome da transportadora..." class="w-full bg-zinc-950 border border-zinc-800 rounded-xl pl-10 pr-4 py-2.5 text-sm text-white focus:outline-none focus:border-blue-500 transition">
              </div>
            </div>

            <!-- Filtro Por Estado -->
            <div class="space-y-1.5">
              <label class="text-xs font-medium text-zinc-300">Filtrar por Estado (UF)</label>
              <select id="searchStateInput" onchange="renderSearchResults()" class="w-full bg-zinc-950 border border-zinc-800 rounded-xl px-4 py-2.5 text-sm text-white focus:outline-none focus:border-blue-500 transition">
                <option value="">Todos os Estados</option>
                <option value="AC">Acre (AC)</option>
                <option value="AL">Alagoas (AL)</option>
                <option value="AP">Amapá (AP)</option>
                <option value="AM">Amazonas (AM)</option>
                <option value="BA">Bahia (BA)</option>
                <option value="CE">Ceará (CE)</option>
                <option value="DF">Distrito Federal (DF)</option>
                <option value="ES">Espírito Santo (ES)</option>
                <option value="GO">Goiás (GO)</option>
                <option value="MA">Maranhão (MA)</option>
                <option value="MT">Mato Grosso (MT)</option>
                <option value="MS">Mato Grosso do Sul (MS)</option>
                <option value="MG">Minas Gerais (MG)</option>
                <option value="PA">Pará (PA)</option>
                <option value="PB">Paraíba (PB)</option>
                <option value="PR">Paraná (PR)</option>
                <option value="PE">Pernambuco (PE)</option>
                <option value="PI">Piauí (PI)</option>
                <option value="RJ">Rio de Janeiro (RJ)</option>
                <option value="RN">Rio Grande do Norte (RN)</option>
                <option value="RS">Rio Grande do Sul (RS)</option>
                <option value="RO">Rondônia (RO)</option>
                <option value="RR">Roraima (RR)</option>
                <option value="SC">Santa Catarina (SC)</option>
                <option value="SP">São Paulo (SP)</option>
                <option value="SE">Sergipe (SE)</option>
                <option value="TO">Tocantins (TO)</option>
              </select>
            </div>
          </div>
        </div>

        <!-- RESULTADOS DA BUSCA -->
        <div class="bg-zinc-900/60 border border-zinc-800/80 rounded-2xl p-6 shadow-xl backdrop-blur-sm">
          <h3 class="text-sm font-semibold text-zinc-300 mb-4 flex items-center justify-between">
            <span>Resultados Encontrados</span>
            <span id="searchCount" class="text-xs bg-zinc-800 text-zinc-400 px-2.5 py-1 rounded-full">0 resultado(s)</span>
          </h3>
          <div id="searchResultsList" class="space-y-3">
            <!-- Resultados renderizados aqui -->
          </div>
        </div>
      </section>

    </main>

    <!-- RODAPÉ FIXO DE CRÉDITOS -->
    <footer class="w-full py-5 border-t border-zinc-800/60 text-center bg-zinc-950/80">
      <p class="text-xs text-zinc-500 font-medium tracking-wide">
        Desenvolvido por <span class="text-zinc-300 font-semibold hover:text-blue-400 transition">altx.ia</span>
      </p>
    </footer>

  </div>

  <!-- ================= LÓGICA JAVASCRIPT ================= -->
  <script>
    // Estado da aplicação
    let carriers = [];

    // Inicialização da Splash Screen e Carregamento
    window.addEventListener('load', () => {
      // Inicializar Ícones Lucide
      lucide.createIcons();

      // Carregar dados salvos no localStorage
      loadCarriers();

      // Aguarda 2 segundos (2000ms) para fechar a tela de abertura
      setTimeout(() => {
        const splash = document.getElementById('splashScreen');
        const main = document.getElementById('mainContent');
        
        splash.classList.add('opacity-0');
        setTimeout(() => {
          splash.style.display = 'none';
          main.classList.remove('opacity-0');
        }, 500);
      }, 2000);
    });

    // Mascara de Telefone Simples
    function maskPhone(input) {
      let v = input.value.replace(/\D/g, '');
      if (v.length > 11) v = v.slice(0, 11);
      if (v.length > 10) {
        input.value = v.replace(/^(\d{2})(\d{5})(\d{4})$/, '($1) $2-$3');
      } else if (v.length > 5) {
        input.value = v.replace(/^(\d{2})(\d{4})(\d{0,4})$/, '($1) $2-$3');
      } else if (v.length > 2) {
        input.value = v.replace(/^(\d{2})(\d{0,5})$/, '($1) $2');
      } else {
        input.value = v;
      }
    }

    // Carregar dados salvos
    function loadCarriers() {
      const saved = localStorage.getItem('altx_carriers');
      if (saved) {
        try {
          carriers = JSON.parse(saved);
        } catch (e) {
          carriers = [];
        }
      }
      renderRegisterList();
      renderSearchResults();
    }

    // Salvar no localStorage
    function saveCarriers() {
      localStorage.setItem('altx_carriers', JSON.stringify(carriers));
      renderRegisterList();
      renderSearchResults();
    }

    // Alternar entre Abas
    function switchTab(tab) {
      const regSection = document.getElementById('sectionRegister');
      const searchSection = document.getElementById('sectionSearch');
      const regBtn = document.getElementById('tabRegisterBtn');
      const searchBtn = document.getElementById('tabSearchBtn');

      if (tab === 'register') {
        regSection.classList.remove('hidden');
        searchSection.classList.add('hidden');
        
        regBtn.className = "px-5 py-2.5 rounded-lg text-sm font-medium transition-all duration-200 flex items-center gap-2 bg-zinc-800 text-white shadow";
        searchBtn.className = "px-5 py-2.5 rounded-lg text-sm font-medium text-zinc-400 hover:text-white transition-all duration-200 flex items-center gap-2";
      } else {
        regSection.classList.add('hidden');
        searchSection.classList.remove('hidden');

        searchBtn.className = "px-5 py-2.5 rounded-lg text-sm font-medium transition-all duration-200 flex items-center gap-2 bg-zinc-800 text-white shadow";
        regBtn.className = "px-5 py-2.5 rounded-lg text-sm font-medium text-zinc-400 hover:text-white transition-all duration-200 flex items-center gap-2";
      }
    }

    // Ação de Salvar Formulário
    function handleSave(e) {
      e.preventDefault();

      const name = document.getElementById('nameInput').value.trim();
      const phone = document.getElementById('phoneInput').value.trim();
      const state = document.getElementById('stateInput').value;
      const editIndex = parseInt(document.getElementById('editIndex').value);

      if (!name || !phone || !state) {
        showToast('Por favor, preencha todos os campos obrigatórios.', 'error');
        return;
      }

      if (editIndex >= 0) {
        // Editar existente
        carriers[editIndex] = { name, phone, state, updatedAt: new Date().toISOString() };
        showToast('Transportadora atualizada com sucesso!', 'success');
      } else {
        // Criar novo
        carriers.unshift({ name, phone, state, createdAt: new Date().toISOString() });
        showToast('Transportadora cadastrada com sucesso!', 'success');
      }

      saveCarriers();
      clearForm();
    }

    // Limpar Formulário
    function clearForm() {
      document.getElementById('carrierForm').reset();
      document.getElementById('editIndex').value = "-1";
    }

    // Deletar Transportadora
    function deleteCarrier(index) {
      if (confirm("Tem certeza que deseja excluir esta transportadora?")) {
        carriers.splice(index, 1);
        saveCarriers();
        showToast('Registro excluído com sucesso.', 'info');
      }
    }

    // Editar Transportadora
    function editCarrier(index) {
      const item = carriers[index];
      if (item) {
        document.getElementById('nameInput').value = item.name;
        document.getElementById('phoneInput').value = item.phone;
        document.getElementById('stateInput').value = item.state;
        document.getElementById('editIndex').value = index;
        switchTab('register');
        window.scrollTo({ top: 0, behavior: 'smooth' });
      }
    }

    // Renderizar Lista na Tela de Cadastro
    function renderRegisterList() {
      const container = document.getElementById('registerList');
      const countEl = document.getElementById('totalCount');
      countEl.innerText = carriers.length;

      if (carriers.length === 0) {
        container.innerHTML = `
          <div class="text-center py-8 text-zinc-500 text-sm">
            Nenhuma transportadora cadastrada no momento.
          </div>
        `;
        return;
      }

      container.innerHTML = carriers.map((c, idx) => `
        <div class="flex items-center justify-between p-4 bg-zinc-950/60 border border-zinc-800/60 rounded-xl hover:border-zinc-700 transition">
          <div class="flex items-center gap-3">
            <div class="w-10 h-10 rounded-lg bg-blue-500/10 border border-blue-500/20 text-blue-400 flex items-center justify-center font-bold text-xs">
              ${c.state}
            </div>
            <div>
              <h4 class="font-medium text-white text-sm">${c.name}</h4>
              <p class="text-xs text-zinc-400 flex items-center gap-2 mt-0.5">
                <span><i data-lucide="phone" class="w-3 h-3 inline mr-1 text-zinc-500"></i>${c.phone}</span>
              </p>
            </div>
          </div>
          <div class="flex items-center gap-2">
            <a href="https://wa.me/55${c.phone.replace(/\D/g,'')}" target="_blank" title="Abrir WhatsApp" class="p-2 rounded-lg bg-emerald-500/10 text-emerald-400 hover:bg-emerald-500/20 transition">
              <i data-lucide="message-square" class="w-4 h-4"></i>
            </a>
            <button onclick="editCarrier(${idx})" title="Editar" class="p-2 rounded-lg bg-zinc-800 text-zinc-300 hover:bg-zinc-700 transition">
              <i data-lucide="edit-3" class="w-4 h-4"></i>
            </button>
            <button onclick="deleteCarrier(${idx})" title="Excluir" class="p-2 rounded-lg bg-red-500/10 text-red-400 hover:bg-red-500/20 transition">
              <i data-lucide="trash-2" class="w-4 h-4"></i>
            </button>
          </div>
        </div>
      `).join('');

      lucide.createIcons();
    }

    // Renderizar Resultados da Busca Dinâmica
    function renderSearchResults() {
      const nameQuery = document.getElementById('searchNameInput').value.toLowerCase().trim();
      const stateQuery = document.getElementById('searchStateInput').value;
      const container = document.getElementById('searchResultsList');
      const countEl = document.getElementById('searchCount');

      const filtered = carriers.filter(c => {
        const matchesName = c.name.toLowerCase().includes(nameQuery);
        const matchesState = stateQuery === "" || c.state === stateQuery;
        return matchesName && matchesState;
      });

      countEl.innerText = `${filtered.length} resultado(s)`;

      if (filtered.length === 0) {
        container.innerHTML = `
          <div class="text-center py-10 text-zinc-500 text-sm">
            Nenhuma transportadora encontrada com os filtros informados.
          </div>
        `;
        return;
      }

      container.innerHTML = filtered.map(c => {
        const originalIndex = carriers.indexOf(c);
        return `
          <div class="flex items-center justify-between p-4 bg-zinc-950/60 border border-zinc-800/60 rounded-xl hover:border-zinc-700 transition">
            <div class="flex items-center gap-3">
              <div class="w-10 h-10 rounded-lg bg-blue-500/10 border border-blue-500/20 text-blue-400 flex items-center justify-center font-bold text-xs">
                ${c.state}
              </div>
              <div>
                <h4 class="font-medium text-white text-sm">${c.name}</h4>
                <p class="text-xs text-zinc-400 flex items-center gap-2 mt-0.5">
                  <span><i data-lucide="phone" class="w-3 h-3 inline mr-1 text-zinc-500"></i>${c.phone}</span>
                </p>
              </div>
            </div>
            <div class="flex items-center gap-2">
              <a href="https://wa.me/55${c.phone.replace(/\D/g,'')}" target="_blank" title="Abrir WhatsApp" class="p-2 rounded-lg bg-emerald-500/10 text-emerald-400 hover:bg-emerald-500/20 transition">
                <i data-lucide="message-square" class="w-4 h-4"></i>
              </a>
              <button onclick="editCarrier(${originalIndex})" title="Editar" class="p-2 rounded-lg bg-zinc-800 text-zinc-300 hover:bg-zinc-700 transition">
                <i data-lucide="edit-3" class="w-4 h-4"></i>
              </button>
            </div>
          </div>
        `;
      }).join('');

      lucide.createIcons();
    }

    // Funcionalidade de Backup - EXPORTAR
    function exportBackup() {
      if (carriers.length === 0) {
        showToast('Não há dados cadastrados para exportar.', 'warning');
        return;
      }
      const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(carriers, null, 2));
      const downloadAnchor = document.createElement('a');
      const fileName = `backup_transportadoras_${new Date().toISOString().slice(0,10)}.json`;
      
      downloadAnchor.setAttribute("href", dataStr);
      downloadAnchor.setAttribute("download", fileName);
      document.body.appendChild(downloadAnchor);
      downloadAnchor.click();
      downloadAnchor.remove();

      showToast('Arquivo de backup exportado com sucesso!', 'success');
    }

    // Funcionalidade de Backup - IMPORTAR
    function importBackup(event) {
      const fileReader = new FileReader();
      const file = event.target.files[0];

      if (!file) return;

      fileReader.onload = function(e) {
        try {
          const importedData = JSON.parse(e.target.result);
          if (Array.isArray(importedData)) {
            carriers = importedData;
            saveCarriers();
            showToast('Backup restaurado com sucesso!', 'success');
          } else {
            showToast('Formato de arquivo inválido.', 'error');
          }
        } catch (err) {
          showToast('Erro ao ler o arquivo JSON de backup.', 'error');
        }
        event.target.value = ''; // reseta input
      };

      fileReader.readAsText(file);
    }

    // Exibir Mensagens de Toast
    function showToast(msg, type = 'info') {
      const toast = document.getElementById('toastMessage');
      const toastText = document.getElementById('toastText');

      toastText.innerText = msg;
      toast.className = "mb-4 p-3.5 rounded-xl border text-sm flex items-center justify-between transition-all duration-300 shadow-lg ";

      if (type === 'success') {
        toast.className += "bg-emerald-950/80 border-emerald-800 text-emerald-200";
      } else if (type === 'error') {
        toast.className += "bg-red-950/80 border-red-800 text-red-200";
      } else if (type === 'warning') {
        toast.className += "bg-amber-950/80 border-amber-800 text-amber-200";
      } else {
        toast.className += "bg-blue-950/80 border-blue-800 text-blue-200";
      }

      toast.classList.remove('hidden');

      setTimeout(() => {
        hideToast();
      }, 4000);
    }

    function hideToast() {
      const toast = document.getElementById('toastMessage');
      toast.classList.add('hidden');
    }
  </script>
</body>
</html>
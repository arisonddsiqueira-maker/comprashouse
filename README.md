<script>
    let produtos = JSON.parse(localStorage.getItem('meuEstoque')) || [];

    function mudarTela(tela) {
        document.querySelectorAll('section').forEach(s => s.classList.add('hidden'));
        document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
        
        document.getElementById('tela-' + tela).classList.remove('hidden');
        document.getElementById('btn-' + tela).classList.add('active');

        if(tela === 'inicio') renderizarCompras();
        if(tela === 'contagem') renderizarContagem();
    }

    function salvarProduto() {
        const novo = {
            id: Date.now(),
            categoria: document.getElementById('p-categoria').value,
            nome: document.getElementById('p-nome').value,
            marca: document.getElementById('p-marca').value,
            medida: document.getElementById('p-medida').value,
            atual: parseInt(document.getElementById('p-atual').value) || 0,
            ideal: parseInt(document.getElementById('p-ideal').value) || 0
        };

        if(!novo.nome) return alert("Digite o nome do produto!");

        produtos.push(novo);
        localStorage.setItem('meuEstoque', JSON.stringify(produtos));
        alert("Produto salvo com sucesso!");
        limparForm();
        mudarTela('inicio');
    }

    function limparForm() {
        document.querySelectorAll('#tela-cadastro input').forEach(i => i.value = '');
    }

    function renderizarCompras() {
        const container = document.getElementById('lista-compras-container');
        const faltantes = produtos.filter(p => p.atual < p.ideal);

        if(faltantes.length === 0) {
            container.innerHTML = '<div class="empty-state">🎉 Tudo em dia! Nada para comprar.</div>';
            return;
        }

        let html = faltantes.map(p => `
            <div class="item-compra">
                <div class="info-produto">
                    <span class="nome-prod">${p.nome}</span>
                    <span class="meta-prod">${p.marca} | ${p.medida}</span>
                </div>
                <div class="qtd-badge">${p.ideal - p.atual}</div>
            </div>
        `).join('');

        // Adiciona o botão de WhatsApp no final da lista
        html += `<button class="btn" style="background:#25D366; margin-top:20px;" onclick="enviarWhatsApp()">
                    📲 Enviar Lista por WhatsApp
                 </button>`;
        
        container.innerHTML = html;
    }

    function renderizarContagem() {
        const container = document.getElementById('contagem-lista');
        const filtro = document.getElementById('filtro-cat').value;
        const filtrados = filtro === 'Todos' ? produtos : produtos.filter(p => p.categoria === filtro);

        if(filtrados.length === 0) {
            container.innerHTML = '<div class="empty-state">Nenhum produto nesta categoria.</div>';
            return;
        }

        container.innerHTML = filtrados.map(p => `
            <div class="card">
                <div style="display:flex; justify-content:space-between;">
                    <strong>${p.nome} (${p.medida})</strong>
                    <button onclick="excluirProduto(${p.id})" style="border:none; background:none; color:red; cursor:pointer;">🗑️</button>
                </div>
                <div style="margin-top:10px; display:flex; align-items:center; gap:10px;">
                    <label style="margin:0">Qtd Atual:</label>
                    <input type="number" value="${p.atual}" 
                           onchange="atualizarEstoque(${p.id}, this.value)" 
                           style="width:70px; padding:5px;">
                    <span style="font-size:0.8rem; color:#888;">(Ideal: ${p.ideal})</span>
                </div>
            </div>
        `).join('');
    }

    function atualizarEstoque(id, novaQtd) {
        const index = produtos.findIndex(p => p.id === id);
        produtos[index].atual = parseInt(novaQtd) || 0;
        localStorage.setItem('meuEstoque', JSON.stringify(produtos));
        // Não muda de tela, apenas salva silenciosamente
    }

    function excluirProduto(id) {
        if(confirm("Tem certeza que deseja apagar este item do cadastro?")) {
            produtos = produtos.filter(p => p.id !== id);
            localStorage.setItem('meuEstoque', JSON.stringify(produtos));
            renderizarContagem();
        }
    }

    function enviarWhatsApp() {
        const faltantes = produtos.filter(p => p.atual < p.ideal);
        let texto = "*🛒 MINHA LISTA DE COMPRAS*\n\n";
        
        faltantes.forEach(p => {
            texto += `• *${p.nome}* (${p.marca})\n`;
            texto += `  Comprar: *${p.ideal - p.atual}* ${p.medida}\n\n`;
        });

        const link = `https://wa.me/?text=${encodeURIComponent(texto)}`;
        window.open(link, '_blank');
    }

    // Inicialização
    renderizarCompras();
</script>

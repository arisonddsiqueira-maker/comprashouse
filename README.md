<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Meu Dashboard Financeiro</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100 p-4 pb-10">

    <div class="max-w-md mx-auto space-y-6">
        <h1 class="text-2xl font-bold text-gray-800 text-center">📊 Gestão de Performance</h1>

        <div class="bg-white p-6 rounded-xl shadow-sm space-y-4">
            <h2 class="text-sm font-bold text-gray-400 uppercase tracking-wider">Inserir Dados</h2>
            
            <div>
                <label class="block text-xs font-medium text-gray-600 uppercase">Mês Atual (R$)</label>
                <input type="number" id="atual" oninput="calcular()" class="w-full mt-1 p-2 border rounded-lg focus:ring-2 focus:ring-indigo-500 outline-none" placeholder="0.00">
            </div>

            <div class="grid grid-cols-1 gap-4">
                <div>
                    <label class="block text-xs font-medium text-gray-600 uppercase">Mesmo Mês 2025 (R$)</label>
                    <input type="number" id="ref2025" oninput="calcular()" class="w-full mt-1 p-2 border rounded-lg" placeholder="Ex: 1585.08">
                </div>
                <div>
                    <label class="block text-xs font-medium text-gray-600 uppercase">Pior Mês 2026 (R$)</label>
                    <input type="number" id="pior2026" oninput="calcular()" class="w-full mt-1 p-2 border rounded-lg" placeholder="Ex: 601.26">
                </div>
                <div>
                    <label class="block text-xs font-medium text-gray-600 uppercase">Melhor Mês 2026 (R$)</label>
                    <input type="number" id="melhor2026" oninput="calcular()" class="w-full mt-1 p-2 border rounded-lg" placeholder="Ex: 9741.96">
                </div>
            </div>
        </div>

        <hr class="border-gray-300">

        <div id="resultados" class="space-y-4">
            <div class="bg-white p-4 rounded-xl border-l-4 border-gray-300 shadow-sm" id="card2025">
                <p class="text-xs font-bold text-gray-500 uppercase">vs. Março/2025</p>
                <p id="txt2025" class="text-lg font-semibold text-gray-400 italic italic">Aguardando dados...</p>
            </div>

            <div class="bg-white p-4 rounded-xl border-l-4 border-gray-300 shadow-sm" id="cardPior">
                <p class="text-xs font-bold text-gray-500 uppercase">vs. Pior Mês 2026</p>
                <p id="txtPior" class="text-lg font-semibold text-gray-400 italic">Aguardando dados...</p>
            </div>

            <div class="bg-gray-800 p-5 rounded-xl text-white shadow-lg shadow-gray-400/50">
                <p class="text-xs font-bold text-gray-400 uppercase">🏆 Desafio: Superar Melhor Mês</p>
                <h2 id="txtMelhor" class="text-xl font-bold mt-1 text-yellow-400 italic">Preencha os valores</h2>
                <div class="w-full bg-gray-700 rounded-full h-2 mt-4">
                    <div id="barraProgresso" class="bg-yellow-400 h-2 rounded-full transition-all duration-500" style="width: 0%"></div>
                </div>
            </div>
        </div>
    </div>

    <script>
        function formatarMoeda(valor) {
            return valor.toLocaleString('pt-br', { style: 'currency', currency: 'BRL' });
        }

        function calcular() {
            const atual = parseFloat(document.getElementById('atual').value) || 0;
            const ref2025 = parseFloat(document.getElementById('ref2025').value) || 0;
            const pior2026 = parseFloat(document.getElementById('pior2026').value) || 0;
            const melhor2026 = parseFloat(document.getElementById('melhor2026').value) || 0;

            // Lógica vs 2025
            if (atual > 0 && ref2025 > 0) {
                const diff = atual - ref2025;
                const cor = diff >= 0 ? 'border-green-500' : 'border-red-500';
                const sinal = diff >= 0 ? '+' : '';
                document.getElementById('card2025').className = bg-white p-4 rounded-xl border-l-4 shadow-sm ${cor};
                document.getElementById('txt2025').innerText = ${sinal} ${formatarMoeda(diff)} ${diff >= 0 ? 'acima ✅' : 'abaixo 📉'};
                document.getElementById('txt2025').className = text-lg font-bold ${diff >= 0 ? 'text-green-600' : 'text-red-600'};
            }

            // Lógica vs Pior Mês
            if (atual > 0 && pior2026 > 0) {
                const diff = atual - pior2026;
                const cor = diff >= 0 ? 'border-green-500' : 'border-red-500';
                document.getElementById('cardPior').className = bg-white p-4 rounded-xl border-l-4 shadow-sm ${cor};
                document.getElementById('txtPior').innerText = + ${formatarMoeda(diff)} acima ✅;
                document.getElementById('txtPior').className = "text-lg font-bold text-green-600";
            }

            // Lógica vs Melhor Mês (Recorde)
            if (atual > 0 && melhor2026 > 0) {
                const faltam = melhor2026 - atual;
                const progresso = Math.min((atual / melhor2026) * 100, 100);
                
                document.getElementById('barraProgresso').style.width = progresso + '%';
                
                if (faltam > 0) {
                    document.getElementById('txtMelhor').innerText = Faltam: ${formatarMoeda(faltam)};
                    document.getElementById('txtMelhor').className = "text-xl font-bold mt-1 text-yellow-400 italic";
                } else {
                    document.getElementById('txtMelhor').innerText = NOVO RECORDE! 🎉 (+${formatarMoeda(Math.abs(faltam))});
                    document.getElementById('txtMelhor').className = "text-xl font-bold mt-1 text-green-400";
                }
            }
        }
    </script>
</body>
</html>

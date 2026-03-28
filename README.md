<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Calculadora de Rodagem - Onix 1.0</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-900 text-gray-100 font-sans p-4">

    <div class="max-w-md mx-auto bg-gray-800 rounded-xl shadow-lg p-6 border border-gray-700">
        <h1 class="text-2xl font-bold text-center text-blue-400 mb-6">Controle Onix 1.0</h1>

        <div class="grid grid-cols-2 gap-4 mb-6 p-4 bg-gray-700 rounded-lg">
            <div>
                <label class="block text-xs uppercase font-bold text-gray-400">Gasolina (R$)</label>
                <input type="number" id="precoGasolina" value="6.40" step="0.01" class="w-full bg-transparent border-b border-gray-500 text-lg outline-none focus:border-blue-400">
            </div>
            <div>
                <label class="block text-xs uppercase font-bold text-gray-400">Consumo (km/l)</label>
                <input type="number" id="consumoKml" value="10.2" step="0.1" class="w-full bg-transparent border-b border-gray-500 text-lg outline-none focus:border-blue-400">
            </div>
        </div>

        <div class="mb-6">
            <label class="block text-sm font-medium mb-2">KM Rodados no Dia:</label>
            <input type="number" id="kmDia" placeholder="Ex: 130" class="w-full p-3 bg-gray-900 rounded-lg border border-gray-600 focus:ring-2 focus:ring-blue-500 outline-none text-xl">
            <button onclick="calcular()" class="w-full mt-4 bg-blue-600 hover:bg-blue-700 text-white font-bold py-3 rounded-lg transition">Calcular e Salvar</button>
        </div>

        <div id="resultado" class="hidden space-y-4">
            <div class="flex justify-between items-center p-3 bg-red-900/30 border border-red-800 rounded-lg">
                <span>Combustível (Gasto):</span>
                <span id="resGasolina" class="font-bold text-red-400 text-xl">R$ 0,00</span>
            </div>
            <div class="flex justify-between items-center p-3 bg-green-900/30 border border-green-800 rounded-lg">
                <span>Reserva Manutenção (20c):</span>
                <span id="resManutencao" class="font-bold text-green-400 text-xl">R$ 0,00</span>
            </div>
            <div class="flex justify-between items-center p-3 bg-blue-900/20 border border-blue-800 rounded-lg">
                <span class="font-bold">Total a Reservar:</span>
                <span id="resTotal" class="font-bold text-blue-300 text-2xl">R$ 0,00</span>
            </div>
        </div>
    </div>

    <div class="max-w-md mx-auto mt-6">
        <h2 class="text-sm font-bold text-gray-500 uppercase mb-2">Últimos Registros</h2>
        <div id="historico" class="space-y-2 text-sm italic text-gray-400">
            </div>
    </div>

    <script>
        function calcular() {
            const km = parseFloat(document.getElementById('kmDia').value);
            const preco = parseFloat(document.getElementById('precoGasolina').value);
            const consumo = parseFloat(document.getElementById('consumoKml').value);

            if (!km || km <= 0) return alert("Insira os KMs rodados!");

            // Cálculos
            const gastoCombustivel = (km / consumo) * preco;
            const reservaManutencao = km * 0.20; // 20 centavos por km
            const totalReservar = gastoCombustivel + reservaManutencao;

            // Exibir na tela
            document.getElementById('resGasolina').innerText = `R$ ${gastoCombustivel.toFixed(2)}`;
            document.getElementById('resManutencao').innerText = `R$ ${reservaManutencao.toFixed(2)}`;
            document.getElementById('resTotal').innerText = `R$ ${totalReservar.toFixed(2)}`;
            document.getElementById('resultado').classList.remove('hidden');

            // Salvar no Histórico Local
            const registro = {
                data: new Date().toLocaleDateString('pt-BR'),
                km: km,
                total: totalReservar.toFixed(2)
            };
            
            let logs = JSON.parse(localStorage.getItem('logs_onix') || "[]");
            logs.unshift(registro);
            logs = logs.slice(0, 5); // Mantém apenas os últimos 5
            localStorage.setItem('logs_onix', JSON.stringify(logs));
            
            atualizarHistorico();
        }

        function atualizarHistorico() {
            const logs = JSON.parse(localStorage.getItem('logs_onix') || "[]");
            const div = document.getElementById('historico');
            div.innerHTML = logs.map(l => `<div>${l.data}: ${l.km}km -> Reservar R$ ${l.total}</div>`).join('');
        }

        // Carregar dados ao iniciar
        atualizarHistorico();
    </script>
</body>
</html>

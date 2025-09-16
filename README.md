# Controle Financeiro Pessoal:

Protótipo desenvolvido para a disciplina Engenharia e Projeto de Software.  
O sistema permite registrar receitas e despesas, calcular o saldo automaticamente e salvar os dados no navegador.

Tecnologias:
- HTML5
- CSS3
- JavaScript
- localStorage

Como usar:
1. Baixe os arquivos ou clone o repositório.
2. Abra o arquivo `index.html` em um navegador moderno.
3. Adicione receitas e despesas.
4. O saldo é atualizado automaticamente.

Funcionalidades:
- Adicionar receitas e despesas
- Exibir saldo atualizado
- Listar movimentações
- Excluir movimentações individualmente
- Salvar dados localmente (mesmo após fechar a página)

 Melhorias Futuras:
- Gráficos para análise financeira
- Exportação para PDF/Excel
- Publicação na Chrome Web Store

 Autora:
Anna Rita Martins Saraiva


Projeto de controle financeiro

<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8">
  <title>Controle Financeiro Pessoal</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body {
      font-family: 'Comic Sans MS', cursive, sans-serif;
      max-width: 700px;
      margin: 20px auto;
      padding: 20px;
      background: linear-gradient(135deg, #fce4ec, #f8bbd0);
      border-radius: 15px;
      box-shadow: 0 4px 15px rgba(0,0,0,0.2);
    }

   h1 {
      text-align: center;
      color: #ad1457;
      margin-bottom: 20px;
      font-size: 2em;
    }

   h2 {
      color: #880e4f;
      margin-top: 20px;
      font-size: 1.3em;
    }

   input {
      padding: 10px;
      margin: 5px 0;
      width: 100%;
      border: 2px solid #f48fb1;
      border-radius: 8px;
      background: #fff0f6;
    }

   button {
      padding: 10px;
      margin: 8px 0;
      width: 100%;
      border: none;
      border-radius: 8px;
      font-size: 1em;
      font-weight: bold;
      cursor: pointer;
      transition: 0.3s;
    }

   .btn-receita {
      background: #ec407a;
      color: white;
    }

   .btn-despesa {
      background: #f06292;
      color: white;
    }

  button:hover {
      transform: scale(1.05);
      opacity: 0.9;
    }

   .saldo {
      font-weight: bold;
      margin: 20px 0;
      font-size: 1.3em;
      text-align: center;
      padding: 12px;
      border-radius: 10px;
      background: #f8bbd0;
      color: #4a148c;
      box-shadow: 0 2px 5px rgba(0,0,0,0.1);
    }

   ul {
      list-style: none;
      padding: 0;
    }

   li {
      margin: 8px 0;
      padding: 12px;
      border-radius: 10px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      box-shadow: 0 2px 6px rgba(0,0,0,0.1);
      font-size: 0.95em;
    }

  .receita {
      background-color: #fce4ec;
      color: #ad1457;
    }

   .despesa {
      background-color: #f8bbd0;
      color: #880e4f;
    }

   .data {
      font-size: 0.8em;
      color: #6a1b9a;
    }

  .btn-excluir {
      background: none;
      border: none;
      color: #c2185b;
      font-size: 1.3em;
      cursor: pointer;
      transition: 0.3s;
    }

  .btn-excluir:hover {
      transform: scale(1.2);
      color: #880e4f;
    }

   canvas {
      margin-top: 20px;
      background: white;
      border-radius: 10px;
      padding: 10px;
    }
  </style>
</head>
<body>

  <h1> Controle Financeiro Pessoal </h1>

  <h2>➕ Adicionar Receita</h2>
  <input type="number" id="valorReceita" placeholder="Valor" />
  <input type="text" id="descReceita" placeholder="Descrição" />
  <button class="btn-receita" onclick="adicionarMovimentacao('receita')">Adicionar Receita </button>

  <h2>➖ Adicionar Despesa</h2>
  <input type="number" id="valorDespesa" placeholder="Valor" />
  <input type="text" id="descDespesa" placeholder="Descrição" />
  <button class="btn-despesa" onclick="adicionarMovimentacao('despesa')">Adicionar Despesa </button>

  <div class="saldo" id="saldo">Saldo Atual: R$ 0.00</div>

  <h2>📋 Minhas Movimentações</h2>
  <ul id="listaMovimentacoes"></ul>

  <h2>📊 Resumo Gráfico</h2>
  <canvas id="graficoPizza" height="200"></canvas>
  <canvas id="graficoBarra" height="200"></canvas>

  <script>
    let movimentacoes = JSON.parse(localStorage.getItem('movimentacoes')) || [];
    let graficoPizza, graficoBarra;

    function atualizarSaldo() {
      let saldo = 0;
      movimentacoes.forEach(mov => {
        saldo += mov.tipo === 'receita' ? mov.valor : -mov.valor;
      });
      document.getElementById('saldo').innerText = 'Saldo Atual: R$ ' + saldo.toFixed(2);
    }

    function salvarLocal() {
      localStorage.setItem('movimentacoes', JSON.stringify(movimentacoes));
    }

    function renderizarLista() {
      const lista = document.getElementById('listaMovimentacoes');
      lista.innerHTML = '';
      movimentacoes.forEach((mov, index) => {
        const item = document.createElement('li');
        item.className = mov.tipo;

        item.innerHTML = `
          <div>
            <strong>${mov.tipo === 'receita' ? 'Receita' : 'Despesa'}:</strong> 
            ${mov.descricao} - R$ ${mov.valor.toFixed(2)}<br>
            <span class="data">${mov.data}</span>
          </div>
          <button class="btn-excluir" onclick="excluirMovimentacao(${index})">×</button>
        `;

        lista.appendChild(item);
      });
    }

    function atualizarGraficos() {
      let totalReceitas = movimentacoes.filter(m => m.tipo === 'receita').reduce((acc, m) => acc + m.valor, 0);
      let totalDespesas = movimentacoes.filter(m => m.tipo === 'despesa').reduce((acc, m) => acc + m.valor, 0);
      let saldo = totalReceitas - totalDespesas;

      // Gráfico de Pizza
      const ctx1 = document.getElementById('graficoPizza').getContext('2d');
      if (graficoPizza) graficoPizza.destroy();
      graficoPizza = new Chart(ctx1, {
        type: 'pie',
        data: {
          labels: ['Receitas', 'Despesas'],
          datasets: [{
            data: [totalReceitas, totalDespesas],
            backgroundColor: ['#ec407a', '#f8bbd0'],
            borderColor: ['#ad1457', '#880e4f'],
            borderWidth: 2
          }]
        }
      });

      // Gráfico de Barras
      const ctx2 = document.getElementById('graficoBarra').getContext('2d');
      if (graficoBarra) graficoBarra.destroy();
      graficoBarra = new Chart(ctx2, {
        type: 'bar',
        data: {
          labels: ['Receitas', 'Despesas', 'Saldo'],
          datasets: [{
            label: 'Resumo Financeiro',
            data: [totalReceitas, totalDespesas, saldo],
            backgroundColor: ['#ec407a', '#f8bbd0', '#f06292']
          }]
        },
        options: {
          scales: {
            y: { beginAtZero: true }
          }
        }
      });
    }

    function adicionarMovimentacao(tipo) {
      const valorInput = document.getElementById(tipo === 'receita' ? 'valorReceita' : 'valorDespesa');
      const descInput = document.getElementById(tipo === 'receita' ? 'descReceita' : 'descDespesa');

      const valor = parseFloat(valorInput.value);
      const descricao = descInput.value.trim();

      if (!valor || !descricao) {
        alert('Preencha valor e descrição corretamente ');
        return;
      }

      const data = new Date().toLocaleString();

      movimentacoes.push({ tipo, valor, descricao, data });
      salvarLocal();
      renderizarLista();
      atualizarSaldo();
      atualizarGraficos();

      valorInput.value = '';
      descInput.value = '';
    }

    function excluirMovimentacao(index) {
      if (confirm('Deseja excluir esta movimentação? ')) {
        movimentacoes.splice(index, 1);
        salvarLocal();
        renderizarLista();
        atualizarSaldo();
        atualizarGraficos();
      }
    }

    // Inicializar
    renderizarLista();
    atualizarSaldo();
    atualizarGraficos();
  </script>

</body>
</html>

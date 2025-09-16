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
  <style>
    body {
      font-family: Arial, sans-serif;
      max-width: 600px;
      margin: 20px auto;
      padding: 10px;
      background: #f4f4f4;
      border-radius: 8px;
    }

  input, button {
      padding: 5px;
      margin: 5px 0;
      width: 100%;
    }

   .saldo {
      font-weight: bold;
      margin-top: 20px;
      font-size: 1.2em;
    }

  ul {
      list-style: none;
      padding: 0;
    }

  li {
      margin: 5px 0;
      padding: 10px;
      border-radius: 5px;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }

  .receita {
      background-color: #d4edda;
      color: #155724;
    }

  .despesa {
      background-color: #f8d7da;
      color: #721c24;
    }

  .data {
      font-size: 0.8em;
      color: #666;
    }

   .btn-excluir {
      background: none;
      border: none;
      color: red;
      cursor: pointer;
      font-size: 1.1em;
    }
  </style>
</head>
<body>

  <h1>Controle Financeiro Pessoal</h1>

  <h2>Adicionar Receita</h2>
  <input type="number" id="valorReceita" placeholder="Valor" />
  <input type="text" id="descReceita" placeholder="Descrição" />
  <button onclick="adicionarMovimentacao('receita')">Adicionar Receita</button>

  <h2>Adicionar Despesa</h2>
  <input type="number" id="valorDespesa" placeholder="Valor" />
  <input type="text" id="descDespesa" placeholder="Descrição" />
  <button onclick="adicionarMovimentacao('despesa')">Adicionar Despesa</button>

  <div class="saldo" id="saldo">Saldo Atual: R$ 0.00</div>

  <h2>Movimentações</h2>
  <ul id="listaMovimentacoes"></ul>

  <script>
    let movimentacoes = JSON.parse(localStorage.getItem('movimentacoes')) || [];

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

    function adicionarMovimentacao(tipo) {
      const valorInput = document.getElementById(tipo === 'receita' ? 'valorReceita' : 'valorDespesa');
      const descInput = document.getElementById(tipo === 'receita' ? 'descReceita' : 'descDespesa');

      const valor = parseFloat(valorInput.value);
      const descricao = descInput.value.trim();

      if (!valor || !descricao) {
        alert('Preencha valor e descrição corretamente.');
        return;
      }

      const data = new Date().toLocaleString();

      movimentacoes.push({ tipo, valor, descricao, data });
      salvarLocal();
      renderizarLista();
      atualizarSaldo();

      valorInput.value = '';
      descInput.value = '';
    }

    function excluirMovimentacao(index) {
      if (confirm('Deseja excluir esta movimentação?')) {
        movimentacoes.splice(index, 1);
        salvarLocal();
        renderizarLista();
        atualizarSaldo();
      }
    }

    // Inicializar
    renderizarLista();
    atualizarSaldo();
  </script>


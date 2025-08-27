<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Calculadora Avançada de Despesas Imobiliárias</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      max-width: 900px;
      margin: 20px auto;
      padding: 20px;
      background-color: #f4f4f4;
      border-radius: 8px;
      box-shadow: 0 0 10px #ccc;
    }
    h2 { text-align: center; }
    label { display: block; margin-top: 12px; }
    input { width: 100%; padding: 8px; margin-top: 4px; box-sizing: border-box; }
    button {
      margin-top: 20px;
      padding: 10px;
      width: 100%;
      background-color: #007bff;
      color: white;
      border: none;
      border-radius: 4px;
      font-size: 16px;
    }
    .resultado {
      margin-top: 20px;
      padding: 15px;
      background: #fff;
      border: 1px solid #ccc;
      border-radius: 5px;
    }
    hr { margin: 15px 0; }
  </style>
</head>
<body>

<h2>Calculadora Avançada de Despesas</h2>

<form id="formulario">
  <h3>Aluguel</h3>
  <label>Valor mensal do aluguel (R$): <input type="text" id="aluguelMensal" required></label>
  <label>Data início: <input type="date" id="dataInicioAluguel" required></label>
  <label>Data fim: <input type="date" id="dataFimAluguel" required></label>

  <h3>IPTU</h3>
  <label>Valor da parcela (R$): <input type="text" id="iptuParcela"></label>
  <label>Quantidade de parcelas: <input type="text" id="iptuQtdParcelas"></label>
  <label>Total do IPTU (R$): <input type="text" id="iptuTotal" readonly></label>
  <label>Parcelas pagas: <input type="text" id="iptuParcelasPagas"></label>
  <label>Valor já pago (R$): <input type="text" id="iptuPago" readonly></label>
  <label>Data início uso IPTU: <input type="date" id="iptuInicio" required></label>
  <label>Data fim uso IPTU: <input type="date" id="iptuFim" required></label>

  <h3>Seguro Incêndio</h3>
  <label>Valor da parcela (R$): <input type="text" id="seguroParcela"></label>
  <label>Quantidade de parcelas: <input type="text" id="seguroQtdParcelas"></label>
  <label>Valor total (R$): <input type="text" id="seguroTotal" readonly></label>
  <label>Parcelas pagas: <input type="text" id="seguroParcelasPagas"></label>
  <label>Valor pago (R$): <input type="text" id="seguroPago" readonly></label>
  <label>Data início uso seguro: <input type="date" id="seguroInicio" required></label>
  <label>Data fim uso seguro: <input type="date" id="seguroFim" required></label>

  <h3>Água</h3>
  <label>Valor da parcela (R$): <input type="text" id="aguaParcela"></label>
  <label>Quantidade de parcelas: <input type="text" id="aguaQtdParcelas"></label>
  <label>Valor total (R$): <input type="text" id="aguaTotal" readonly></label>
  <label>Parcelas pagas: <input type="text" id="aguaParcelasPagas"></label>
  <label>Valor pago (R$): <input type="text" id="aguaPago" readonly></label>

  <h3>Luz</h3>
  <label>Valor da parcela (R$): <input type="text" id="luzParcela"></label>
  <label>Quantidade de parcelas: <input type="text" id="luzQtdParcelas"></label>
  <label>Valor total (R$): <input type="text" id="luzTotal" readonly></label>
  <label>Parcelas pagas: <input type="text" id="luzParcelasPagas"></label>
  <label>Valor pago (R$): <input type="text" id="luzPago" readonly></label>

  <h3>Condomínio</h3>
  <label>Valor da parcela (R$): <input type="text" id="condParcela"></label>
  <label>Quantidade de parcelas: <input type="text" id="condQtdParcelas"></label>
  <label>Valor total (R$): <input type="text" id="condTotal" readonly></label>
  <label>Parcelas pagas: <input type="text" id="condParcelasPagas"></label>
  <label>Valor pago (R$): <input type="text" id="condPago" readonly></label>

  <h3>Gás</h3>
  <label>Valor da parcela (R$): <input type="text" id="gasParcela"></label>
  <label>Quantidade de parcelas: <input type="text" id="gasQtdParcelas"></label>
  <label>Valor total (R$): <input type="text" id="gasTotal" readonly></label>
  <label>Parcelas pagas: <input type="text" id="gasParcelasPagas"></label>
  <label>Valor pago (R$): <input type="text" id="gasPago" readonly></label>

  <h3>Pintura</h3>
  <label>Valor da parcela (R$): <input type="text" id="pinturaParcela"></label>
  <label>Quantidade de parcelas: <input type="text" id="pinturaQtdParcelas"></label>
  <label>Valor total (R$): <input type="text" id="pinturaTotal" readonly></label>
  <label>Parcelas pagas: <input type="text" id="pinturaParcelasPagas"></label>
  <label>Valor pago (R$): <input type="text" id="pinturaPago" readonly></label>

  <h3>Manutenção</h3>
  <label>Valor estimado (R$): <input type="text" id="manutencao"></label>

  <h3>Multa Rescisória</h3>
  <label>Valor da multa (R$): <input type="text" id="multa"></label>

  <button type="submit">Calcular</button>
</form>

<div class="resultado" id="resultado" style="display: none;"></div>

<script>
  const formatar = valor => new Intl.NumberFormat('pt-BR', {
    style: 'currency',
    currency: 'BRL'
  }).format(valor);

  const diasEntreDatas = (inicio, fim) => {
    const i = new Date(inicio);
    const f = new Date(fim);
    if (isNaN(i) || isNaN(f)) return 0;
    return Math.max(0, Math.floor((f - i) / (1000 * 60 * 60 * 24)) + 1);
  };

  const lerNumero = id => parseFloat((document.getElementById(id).value || "0").replace(",", "."));

  function atualizarTotais(baseId) {
    const parcela = lerNumero(baseId + 'Parcela');
    const qtd = lerNumero(baseId + 'QtdParcelas');
    const pagas = lerNumero(baseId + 'ParcelasPagas');
    document.getElementById(baseId + 'Total').value = parcela * qtd;
    document.getElementById(baseId + 'Pago').value = parcela * pagas;
  }

  ['iptu','seguro','agua','luz','cond','gas','pintura'].forEach(id => {
    ['Parcela','QtdParcelas','ParcelasPagas'].forEach(suf => {
      const el = document.getElementById(id + suf);
      if (el) el.addEventListener('input', () => atualizarTotais(id));
    });
  });

  document.getElementById('formulario').addEventListener('submit', function(e) {
    e.preventDefault();

    let resumo = "<h3>Resumo:</h3>";
    let totalPagar = 0;
    let totalDevolver = 0;

    // Aluguel pró-rata
    const aluguelMensal = lerNumero('aluguelMensal');
    const aluguelDias = diasEntreDatas(document.getElementById('dataInicioAluguel').value, document.getElementById('dataFimAluguel').value);
    if (aluguelMensal > 0 && aluguelDias > 0) {
      const aluguelDiario = aluguelMensal / 30;
      const aluguelTotal = aluguelDiario * aluguelDias;
      resumo += `<p><strong>Aluguel pró-rata (${aluguelDias} dias):</strong> ${formatar(aluguelTotal)}</p>`;
      totalPagar += aluguelTotal;
    }

    function processarConta(nome, baseId, proporcional=false, dias=0) {
      const total = lerNumero(baseId + 'Total');
      const pago = lerNumero(baseId + 'Pago');
      if (total === 0 && pago === 0) return;

      let diferenca;
      if (proporcional) {
        const proporcionalValor = (total / 360) * dias;
        diferenca = proporcionalValor - pago;
        resumo += `<p><strong>${nome} (${dias} dias):</strong> ${formatar(proporcionalValor)} - Pago: ${formatar(pago)} → ${diferenca >= 0 ? "A pagar: " + formatar(diferenca) : "A devolver: " + formatar(Math.abs(diferenca))}</p>`;
      } else {
        diferenca = total - pago;
        resumo += `<p><strong>${nome}:</strong> ${formatar(total)} - Pago: ${formatar(pago)} → ${diferenca >= 0 ? "A pagar: " + formatar(diferenca) : "A devolver: " + formatar(Math.abs(diferenca))}</p>`;
      }

      if (diferenca > 0) totalPagar += diferenca; else totalDevolver += Math.abs(diferenca);
    }

    // IPTU
    const iptuDias = diasEntreDatas(document.getElementById('iptuInicio').value, document.getElementById('iptuFim').value);
    processarConta('IPTU', 'iptu', true, iptuDias);

    // Seguro
    const seguroDias = diasEntreDatas(document.getElementById('seguroInicio').value, document.getElementById('seguroFim').value);
    processarConta('Seguro Incêndio', 'seguro', true, seguroDias);

    // Água, Luz, Condomínio, Gás, Pintura
    processarConta('Água','agua');
    processarConta('Luz','luz');
    processarConta('Condomínio','cond');
    processarConta('Gás','gas');
    processarConta('Pintura','pintura');

    // Manutenção
    const manutencaoBase = lerNumero('manutencao');
    if (manutencaoBase > 0) {
      const manutencaoTotal = manutencaoBase * 1.2;
      resumo += `<p><strong>Manutenção (com 20%):</strong> ${formatar(manutencaoTotal)}</p>`;
      totalPagar += manutencaoTotal;
    }

    // Multa rescisória
    const multa = lerNumero('multa');
    if (multa > 0) {
      resumo += `<p><strong>Multa rescisória:</strong> ${formatar(multa)}</p>`;
      totalPagar += multa;
    }

    resumo += "<hr>";
    resumo += `<p><strong>Total a pagar:</strong> ${formatar(totalPagar)}</p>`;
    if (totalDevolver > 0) resumo += `<p><strong>Total a devolver:</strong> ${formatar(totalDevolver)}</p>`;

    document.getElementById('resultado').innerHTML = resumo;
    document.getElementById('resultado').style.display = 'block';
  });
</script>

</body>
</html>


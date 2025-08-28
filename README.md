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
  <label>Parcelas pagas: <input type="text" id="iptuQtdPagas"></label>
  <label>Total do IPTU (R$): <input type="text" id="iptuTotal" readonly></label>
  <label>Valor já pago (R$): <input type="text" id="iptuPago" readonly></label>
  <label>Data início uso IPTU: <input type="date" id="iptuInicio"></label>
  <label>Data fim uso IPTU: <input type="date" id="iptuFim"></label>

  <h3>Seguro Incêndio</h3>
  <label>Valor da parcela (R$): <input type="text" id="seguroParcela"></label>
  <label>Quantidade de parcelas: <input type="text" id="seguroQtdParcelas"></label>
  <label>Parcelas pagas: <input type="text" id="seguroQtdPagas"></label>
  <label>Valor total (R$): <input type="text" id="seguroTotal" readonly></label>
  <label>Valor pago (R$): <input type="text" id="seguroPago" readonly></label>
  <label>Data início uso seguro: <input type="date" id="seguroInicio"></label>
  <label>Data fim uso seguro: <input type="date" id="seguroFim"></label>

  <h3>Água</h3>
  <label>Valor mensal (R$): <input type="text" id="aguaMensal"></label>
  <label>Pago (R$): <input type="text" id="aguaPago"></label>
  <label>Data início uso: <input type="date" id="aguaInicio"></label>
  <label>Data fim uso: <input type="date" id="aguaFim"></label>

  <h3>Luz</h3>
  <label>Valor mensal (R$): <input type="text" id="luzMensal"></label>
  <label>Pago (R$): <input type="text" id="luzPago"></label>
  <label>Data início uso: <input type="date" id="luzInicio"></label>
  <label>Data fim uso: <input type="date" id="luzFim"></label>

  <h3>Condomínio</h3>
  <label>Valor mensal (R$): <input type="text" id="condMensal"></label>
  <label>Pago (R$): <input type="text" id="condPago"></label>
  <label>Data início uso: <input type="date" id="condInicio"></label>
  <label>Data fim uso: <input type="date" id="condFim"></label>

  <h3>Gás</h3>
  <label>Valor mensal (R$): <input type="text" id="gasMensal"></label>
  <label>Pago (R$): <input type="text" id="gasPago"></label>
  <label>Data início uso: <input type="date" id="gasInicio"></label>
  <label>Data fim uso: <input type="date" id="gasFim"></label>

  <h3>Pintura</h3>
  <label>Valor da parcela (R$): <input type="text" id="pinturaParcela"></label>
  <label>Quantidade de parcelas: <input type="text" id="pinturaQtdParcelas"></label>
  <label>Parcelas pagas: <input type="text" id="pinturaQtdPagas"></label>
  <label>Total da pintura (R$): <input type="text" id="pinturaTotal" readonly></label>
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
    if (!inicio || !fim) return 0;
    const i = new Date(inicio);
    const f = new Date(fim);
    if (isNaN(i) || isNaN(f)) return 0;
    return Math.floor((f - i) / (1000 * 60 * 60 * 24)) + 1;
  };

  const lerNumero = id => parseFloat((document.getElementById(id).value || "0").replace(",", ".")) || 0;

  // ---- Atualizações automáticas IPTU ----
  function atualizarIptu() {
    const parcela = lerNumero('iptuParcela');
    const qtd = lerNumero('iptuQtdParcelas');
    const pagas = lerNumero('iptuQtdPagas');
    document.getElementById('iptuTotal').value = parcela * qtd;
    document.getElementById('iptuPago').value = parcela * pagas;
  }
  ['iptuParcela','iptuQtdParcelas','iptuQtdPagas'].forEach(id=>{
    document.getElementById(id).addEventListener('input', atualizarIptu);
  });

  // ---- Atualizações automáticas Seguro ----
  function atualizarSeguro() {
    const parcela = lerNumero('seguroParcela');
    const qtd = lerNumero('seguroQtdParcelas');
    const pagas = lerNumero('seguroQtdPagas');
    document.getElementById('seguroTotal').value = parcela * qtd;
    document.getElementById('seguroPago').value = parcela * pagas;
  }
  ['seguroParcela','seguroQtdParcelas','seguroQtdPagas'].forEach(id=>{
    document.getElementById(id).addEventListener('input', atualizarSeguro);
  });

  // ---- SUBMIT ----
  document.getElementById('formulario').addEventListener('submit', function(e) {
    e.preventDefault();

    let resumo = "<h3>Resumo:</h3>";
    let totalPagar = 0;
    let totalDevolver = 0;

    // ---- Função para cálculos proporcionais ----
    function calcularProporcional(nome, mensalId, pagoId, inicioId, fimId) {
      const mensal = lerNumero(mensalId);
      const pago = lerNumero(pagoId);
      const diasUso = diasEntreDatas(document.getElementById(inicioId).value, document.getElementById(fimId).value);
      const valor = (mensal/30) * diasUso;

      if (valor > 0) {
        resumo += `<b>${nome}:</b> ${formatar(valor)} (Pago: ${formatar(pago)})<br>`;
        if (valor > pago) totalPagar += valor - pago;
        else totalDevolver += pago - valor;
      }
    }

    // ---- Aluguel ----
    const aluguel = lerNumero('aluguelMensal');
    const diasAluguel = diasEntreDatas(document.getElementById('dataInicioAluguel').value, document.getElementById('dataFimAluguel').value);
    const aluguelTotal = (aluguel/30) * diasAluguel;
    resumo += `<b>Aluguel:</b> ${formatar(aluguelTotal)}<br>`;
    totalPagar += aluguelTotal;

    // ---- IPTU ----
    const iptuTotal = lerNumero('iptuTotal');
    const iptuPago = lerNumero('iptuPago');
    const iptuDias = diasEntreDatas(document.getElementById('iptuInicio').value, document.getElementById('iptuFim').value);
    const iptuValor = (iptuTotal/365) * iptuDias;
    resumo += `<b>IPTU:</b> ${formatar(iptuValor)} (Pago: ${formatar(iptuPago)})<br>`;
    if (iptuValor > iptuPago) totalPagar += iptuValor - iptuPago;
    else totalDevolver += iptuPago - iptuValor;

    // ---- Seguro ----
    const seguroTotal = lerNumero('seguroTotal');
    const seguroPago = lerNumero('seguroPago');
    const seguroDias = diasEntreDatas(document.getElementById('seguroInicio').value, document.getElementById('seguroFim').value);
    const seguroValor = (seguroTotal/365) * seguroDias;
    resumo += `<b>Seguro Incêndio:</b> ${formatar(seguroValor)} (Pago: ${formatar(seguroPago)})<br>`;
    if (seguroValor > seguroPago) totalPagar += seguroValor - seguroPago;
    else totalDevolver += seguroPago - seguroValor;

    // ---- Água, Luz, Condomínio, Gás ----
    calcularProporcional("Água", "aguaMensal", "aguaPago", "aguaInicio", "aguaFim");
    calcularProporcional("Luz", "luzMensal", "luzPago", "luzInicio", "luzFim");
    calcularProporcional("Condomínio", "condMensal", "condPago", "condInicio", "condFim");
    calcularProporcional("Gás", "gasMensal", "gasPago", "gasInicio", "gasFim");

    // ---- Pintura ----
    const pinturaTotal = lerNumero('pinturaParcela') * lerNumero('pinturaQtdParcelas');
    const pinturaPago = lerNumero('pinturaParcela') * lerNumero('pinturaQtdPagas');
    resumo += `<b>Pintura:</b> ${formatar(pinturaTotal)} (Pago: ${formatar(pinturaPago)})<br>`;
    if (pinturaTotal > pinturaPago) totalPagar += pinturaTotal - pinturaPago;
    else totalDevolver += pinturaPago - pinturaTotal;

    // ---- Manutenção ----
    const manutencao = lerNumero('manutencao');
    resumo += `<b>Manutenção:</b> ${formatar(manutencao)}<br>`;
    totalPagar += manutencao;

    // ---- Multa ----
    const multa = lerNumero('multa');
    resumo += `<b>Multa Rescisória:</b> ${formatar(multa)}<br>`;
    totalPagar += multa;

    resumo += `<hr><b>Total a Pagar:</b> ${formatar(totalPagar)}<br>`;
    resumo += `<b>Total a Devolver:</b> ${formatar(totalDevolver)}<br>`;

    document.getElementById('resultado').innerHTML = resumo;
    document.getElementById('resultado').style.display = 'block';
  });
</script>

</body>
</html>


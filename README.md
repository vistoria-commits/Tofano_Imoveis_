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
  <label>Data início uso IPTU: <input type="date" id="iptuInicio" required></label>
  <label>Data fim uso IPTU: <input type="date" id="iptuFim" required></label>

  <h3>Seguro Incêndio</h3>
  <label>Valor da parcela (R$): <input type="text" id="seguroParcela"></label>
  <label>Quantidade de parcelas: <input type="text" id="seguroQtdParcelas"></label>
  <label>Parcelas pagas: <input type="text" id="seguroQtdPagas"></label>
  <label>Valor total (R$): <input type="text" id="seguroTotal" readonly></label>
  <label>Valor pago (R$): <input type="text" id="seguroPago" readonly></label>
  <label>Data início uso seguro: <input type="date" id="seguroInicio" required></label>
  <label>Data fim uso seguro: <input type="date" id="seguroFim" required></label>

  <h3>Água</h3>
  <label>Valor mensal (R$): <input type="text" id="aguaTotal"></label>
  <label>Valor pago (R$): <input type="text" id="aguaPago"></label>
  <label>Data início: <input type="date" id="aguaInicio"></label>
  <label>Data fim: <input type="date" id="aguaFim"></label>

  <h3>Luz</h3>
  <label>Valor mensal (R$): <input type="text" id="luzTotal"></label>
  <label>Valor pago (R$): <input type="text" id="luzPago"></label>
  <label>Data início: <input type="date" id="luzInicio"></label>
  <label>Data fim: <input type="date" id="luzFim"></label>

  <h3>Condomínio</h3>
  <label>Valor mensal (R$): <input type="text" id="condTotal"></label>
  <label>Valor pago (R$): <input type="text" id="condPago"></label>
  <label>Data início: <input type="date" id="condInicio"></label>
  <label>Data fim: <input type="date" id="condFim"></label>

  <h3>Gás</h3>
  <label>Valor mensal (R$): <input type="text" id="gasTotal"></label>
  <label>Valor pago (R$): <input type="text" id="gasPago"></label>
  <label>Data início: <input type="date" id="gasInicio"></label>
  <label>Data fim: <input type="date" id="gasFim"></label>

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

  // Atualizações automáticas IPTU
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

  // Atualizações automáticas Seguro
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

  // Atualizações automáticas Pintura
  function atualizarPintura() {
    const parcela = lerNumero('pinturaParcela');
    const qtd = lerNumero('pinturaQtdParcelas');
    const pagas = lerNumero('pinturaQtdPagas');
    document.getElementById('pinturaTotal').value = parcela * qtd;
    document.getElementById('pinturaPago').value = parcela * pagas;
  }
  ['pinturaParcela','pinturaQtdParcelas','pinturaQtdPagas'].forEach(id=>{
    document.getElementById(id).addEventListener('input', atualizarPintura);
  });

  // Navegação com Enter e Shift+Enter
  const inputs = document.querySelectorAll("input, button");
  inputs.forEach((input, index) => {
    input.addEventListener("keydown", function(e) {
      if (e.key === "Enter") {
        e.preventDefault();
        if (e.shiftKey) {
          if (index > 0) inputs[index - 1].focus();
        } else {
          if (index < inputs.length - 1) inputs[index + 1].focus();
        }
      }
    });
  });

  document.getElementById('formulario').addEventListener('submit', function(e) {
    e.preventDefault();

    let resumo = "<h3>Resumo:</h3>";
    let totalPagar = 0;
    let totalDevolver = 0;

    // Aluguel pró-rata
    const aluguelMensal = lerNumero('aluguelMensal');
    const dias = diasEntreDatas(document.getElementById('dataInicioAluguel').value, document.getElementById('dataFimAluguel').value);
    if (aluguelMensal > 0 && dias > 0) {
      const aluguelDiario = aluguelMensal / 30;
      const aluguelTotal = aluguelDiario * dias;
      resumo += `<p><strong>Aluguel pró-rata (${dias} dias):</strong> ${formatar(aluguelTotal)}</p>`;
      totalPagar += aluguelTotal;
    }

    // IPTU proporcional
    const iptuTotal = lerNumero('iptuTotal');
    const iptuPago = lerNumero('iptuPago');
    if (iptuTotal > 0) {
      const iptuDias = diasEntreDatas(document.getElementById('iptuInicio').value, document.getElementById('iptuFim').value);
      const iptuProporcional = (iptuTotal / 360) * iptuDias;
      const iptuDiferenca = iptuProporcional - iptuPago;
      resumo += `<p><strong>IPTU proporcional (${iptuDias} dias):</strong> ${formatar(iptuProporcional)}`;
      if (iptuPago > 0) resumo += ` - Pago: ${formatar(iptuPago)}`;
      resumo += ` → ${iptuDiferenca >= 0 ? "A pagar: " + formatar(iptuDiferenca) : "A devolver: " + formatar(Math.abs(iptuDiferenca))}</p>`;
      if (iptuDiferenca > 0) totalPagar += iptuDiferenca; else totalDevolver += Math.abs(iptuDiferenca);
    }

    // Seguro proporcional
    const seguroTotal = lerNumero('seguroTotal');
    const seguroPago = lerNumero('seguroPago');
    if (seguroTotal > 0) {
      const seguroDias = diasEntreDatas(document.getElementById('seguroInicio').value, document.getElementById('seguroFim').value);
      const seguroProporcional = (seguroTotal / 360) * seguroDias;
      const seguroDiferenca = seguroProporcional - seguroPago;
      resumo += `<p><strong>Seguro incêndio (${seguroDias} dias):</strong> ${formatar(seguroProporcional)}`;
      if (seguroPago > 0) resumo += ` - Pago: ${formatar(seguroPago)}`;
      resumo += ` → ${seguroDiferenca >= 0 ? "A pagar: " + formatar(seguroDiferenca) : "A devolver: " + formatar(Math.abs(seguroDiferenca))}</p>`;
      if (seguroDiferenca > 0) totalPagar += seguroDiferenca; else totalDevolver += Math.abs(seguroDiferenca);
    }

    // Função genérica para contas mensais (Água, Luz, Condomínio, Gás)
    function calcularConta(mensal, pago, inicio, fim, nome) {
      if (mensal <= 0) return "";
      const dias = diasEntreDatas(inicio, fim);
      if (dias <= 0) return "";
      const valorDiario = mensal / 30;
      const proporcional = valorDiario * dias;
      const diferenca = proporcional - pago;
      let texto = `<p><strong>${nome} (${dias} dias):</strong> ${formatar(proporcional)}`;
      if (pago > 0) texto += ` - Pago: ${formatar(pago)}`;
      texto += ` → `;
      if (diferenca > 0) {
        texto += "A pagar: " + formatar(diferenca) + "</p>";
        totalPagar += diferenca;
      } else {
        texto += "A devolver: " + formatar(Math.abs(diferenca)) + "</p>";
        totalDevolver += Math.abs(diferenca);
      }
      return texto;
    }

    resumo += calcularConta(lerNumero('aguaTotal'), lerNumero('aguaPago'), document.getElementById('aguaInicio').value, document.getElementById('aguaFim').value, "Água");
    resumo += calcularConta(lerNumero('luzTotal'), lerNumero('luzPago'), document.getElementById('luzInicio').value, document.getElementById('luzFim').value, "Luz");
    resumo += calcularConta(lerNumero('condTotal'), lerNumero('condPago'), document.getElementById('condInicio').value, document.getElementById('condFim').value, "Condomínio");
    resumo += calcularConta(lerNumero('gasTotal'), lerNumero('gasPago'), document.getElementById('gasInicio').value, document.getElementById('gasFim').value, "Gás");

    // Pintura
    const pinturaTotal = lerNumero('pinturaTotal');
    const pinturaPago = lerNumero('pinturaPago');
    if (pinturaTotal > 0) {
      const pinturaDiferenca = pinturaTotal - pinturaPago;
      resumo += `<p><strong>Pintura:</strong> ${formatar(pinturaTotal)}`;
      if (pinturaPago > 0) resumo += ` - Pago: ${formatar(pinturaPago)}`;
      resumo += ` → ${pinturaDiferenca >= 0 ? "A pagar: " + formatar(pinturaDiferenca) : "A devolver: " + formatar(Math.abs(pinturaDiferenca))}</p>`;
      if (pinturaDiferenca > 0) totalPagar += pinturaDiferenca; else totalDevolver += Math.abs(pinturaDiferenca);
    }

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

    // Novo: saldo líquido
    const saldo = totalPagar - totalDevolver;
    resumo += `<p><strong>Saldo final (${saldo >= 0 ? "a pagar" : "a receber"}):</strong> ${formatar(Math.abs(saldo))}</p>`;

    document.getElementById('resultado').innerHTML = resumo;
    document.getElementById('resultado').style.display = 'block';
  });
</script>

</body>
</html>

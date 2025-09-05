<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<title>Calculadora Avançada de Despesas Imobiliárias</title>
<style>
  body { font-family: Arial, sans-serif; max-width:900px; margin:20px auto; padding:20px; background:#f4f4f4; border-radius:8px; box-shadow:0 0 10px rgba(0,0,0,0.1);}
  h1,h2 {text-align:center;margin-bottom:20px;}
  .grupo {margin-bottom:20px;padding:15px;background:#fff;border-radius:8px;box-shadow:0 0 5px rgba(0,0,0,0.1);}
  label {display:block;margin-bottom:5px;font-weight:bold;}
  input {width:100%;padding:8px;margin-bottom:10px;border:1px solid #ccc;border-radius:4px;}
  button {display:inline-block;padding:8px 12px;background:#007BFF;color:#fff;border:none;border-radius:4px;font-size:14px;cursor:pointer;margin-top:5px;}
  button:hover {background:#0056b3;}
  #resultado {display:none;margin-top:20px;padding:15px;background:#fff;border-radius:8px;box-shadow:0 0 5px rgba(0,0,0,0.1);}
  hr {margin:20px 0;}
  .outras-despesas-item {margin-bottom:10px;}
</style>
</head>
<body>

<h1>Calculadora Avançada de Despesas Imobiliárias</h1>
<form id="formulario">

<!-- Aluguel -->
<div class="grupo">
<h2>Aluguel</h2>
<label>Valor Mensal:</label><input type="text" id="aluguelMensal" placeholder="Ex: 1.200,00">
<label>Data Início:</label><input type="date" id="dataInicioAluguel">
<label>Data Fim:</label><input type="date" id="dataFimAluguel">
</div>

<!-- IPTU -->
<div class="grupo">
<h2>IPTU</h2>
<label>Valor da Parcela:</label><input type="text" id="iptuParcela" placeholder="Ex: 500,00">
<label>Quantidade Parcelas:</label><input type="number" id="iptuQtdParcelas" placeholder="Ex: 12">
<label>Parcelas Pagas:</label><input type="number" id="iptuQtdPagas" placeholder="Ex: 5">
<label>Total:</label><input type="text" id="iptuTotal" readonly>
<label>Pago:</label><input type="text" id="iptuPago" readonly>
<label>Data Início:</label><input type="date" id="iptuInicio">
<label>Data Fim:</label><input type="date" id="iptuFim">
</div>

<!-- Seguro -->
<div class="grupo">
<h2>Seguro Incêndio</h2>
<label>Valor da Parcela:</label><input type="text" id="seguroParcela" placeholder="Ex: 100,00">
<label>Quantidade Parcelas:</label><input type="number" id="seguroQtdParcelas" placeholder="Ex: 12">
<label>Parcelas Pagas:</label><input type="number" id="seguroQtdPagas" placeholder="Ex: 5">
<label>Total:</label><input type="text" id="seguroTotal" readonly>
<label>Pago:</label><input type="text" id="seguroPago" readonly>
<label>Data Início:</label><input type="date" id="seguroInicio">
<label>Data Fim:</label><input type="date" id="seguroFim">
</div>

<!-- Pintura -->
<div class="grupo">
<h2>Pintura</h2>
<label>Valor da Parcela:</label><input type="text" id="pinturaParcela" placeholder="Ex: 800,00">
<label>Quantidade Parcelas:</label><input type="number" id="pinturaQtdParcelas" placeholder="Ex: 3">
<label>Parcelas Pagas:</label><input type="number" id="pinturaQtdPagas" placeholder="Ex: 1">
<label>Total:</label><input type="text" id="pinturaTotal" readonly>
<label>Pago:</label><input type="text" id="pinturaPago" readonly>
</div>

<!-- Contas mensais (Água, Luz, Condomínio, Gás) -->
<div class="grupo">
<h2>Água</h2>
<label>Valor Mensal:</label><input type="text" id="aguaTotal">
<label>Pago:</label><input type="text" id="aguaPago">
<label>Data Início:</label><input type="date" id="aguaInicio">
<label>Data Fim:</label><input type="date" id="aguaFim">
</div>

<div class="grupo">
<h2>Luz</h2>
<label>Valor Mensal:</label><input type="text" id="luzTotal">
<label>Pago:</label><input type="text" id="luzPago">
<label>Data Início:</label><input type="date" id="luzInicio">
<label>Data Fim:</label><input type="date" id="luzFim">
</div>

<div class="grupo">
<h2>Condomínio</h2>
<label>Valor Mensal:</label><input type="text" id="condTotal">
<label>Pago:</label><input type="text" id="condPago">
<label>Data Início:</label><input type="date" id="condInicio">
<label>Data Fim:</label><input type="date" id="condFim">
</div>

<div class="grupo">
<h2>Gás</h2>
<label>Valor Mensal:</label><input type="text" id="gasTotal">
<label>Pago:</label><input type="text" id="gasPago">
<label>Data Início:</label><input type="date" id="gasInicio">
<label>Data Fim:</label><input type="date" id="gasFim">
</div>

<!-- Manutenção e Multa -->
<div class="grupo">
<h2>Manutenção</h2>
<label>Valor Estimado:</label><input type="text" id="manutencao">
</div>

<div class="grupo">
<h2>Multa Rescisória</h2>
<label>Valor:</label><input type="text" id="multa">
</div>

<!-- Outras Despesas Dinâmicas -->
<div class="grupo" id="outrasDespesasGrupo">
<h2>Outras Despesas</h2>
<div id="outrasDespesasContainer"></div>
<button type="button" id="adicionarDespesa">Adicionar Despesa</button>
</div>

<button type="submit">Calcular</button>
</form>

<div id="resultado"></div>

<script>
const formatar = v => new Intl.NumberFormat('pt-BR',{style:'currency',currency:'BRL'}).format(v);

const lerNumero=id=>{
  let v=(document.getElementById(id).value||"0").replace(/[R$\s]/g,"").replace(/\./g,"").replace(",",".");
  return parseFloat(v)||0;
}

const diasEntreDatas=(inicio,fim)=>{
  if(!inicio||!fim)return 0;
  let i=new Date(inicio), f=new Date(fim);
  if(isNaN(i)||isNaN(f)) return 0;
  i.setUTCHours(0,0,0,0); f.setUTCHours(0,0,0,0);
  if(f<i) return 0;
  return Math.floor((f-i)/(1000*60*60*24))+1;
}

// Atualização automática IPTU, Seguro, Pintura
function atualizarCampos(parcelaId,qtdId,pagasId,totalId,pagoId){
  const p=lerNumero(parcelaId), q=lerNumero(qtdId), pg=lerNumero(pagasId);
  document.getElementById(totalId).value=formatar(p*q);
  document.getElementById(pagoId).value=formatar(p*pg);
}
['iptuParcela','iptuQtdParcelas','iptuQtdPagas'].forEach(id=>document.getElementById(id).addEventListener('input',()=>atualizarCampos('iptuParcela','iptuQtdParcelas','iptuQtdPagas','iptuTotal','iptuPago')));
['seguroParcela','seguroQtdParcelas','seguroQtdPagas'].forEach(id=>document.getElementById(id).addEventListener('input',()=>atualizarCampos('seguroParcela','seguroQtdParcelas','seguroQtdPagas','seguroTotal','seguroPago')));
['pinturaParcela','pinturaQtdParcelas','pinturaQtdPagas'].forEach(id=>document.getElementById(id).addEventListener('input',()=>atualizarCampos('pinturaParcela','pinturaQtdParcelas','pinturaQtdPagas','pinturaTotal','pinturaPago')));

// Outras despesas dinâmicas
let countDespesa=0;
const container=document.getElementById('outrasDespesasContainer');
document.getElementById('adicionarDespesa').addEventListener('click',()=>{
  countDespesa++;
  const div=document.createElement('div');
  div.className='outras-despesas-item';
  div.innerHTML=`
    <label>Descrição:</label>
    <input type="text" class="outraDescricao" id="outraDescricao${countDespesa}" placeholder="Ex: Internet">
    <label>Valor:</label>
    <input type="text" class="outraValor" id="outraValor${countDespesa}" placeholder="Ex: 150,00">
  `;
  container.appendChild(div);
});

// Navegação Enter / Shift+Enter corrigida
document.addEventListener('keydown', e => {
    if (e.key === 'Enter') {
        const focusable = Array.from(document.querySelectorAll('input, button'))
            .filter(el => !el.disabled && el.offsetParent !== null);
        const idx = focusable.indexOf(document.activeElement);
        if (idx === -1) return;
        e.preventDefault();
        if (e.shiftKey) {
            if (idx > 0) focusable[idx - 1].focus();
        } else {
            if (idx < focusable.length - 1) focusable[idx + 1].focus();
        }
    }
});

// Cálculo
document.getElementById('formulario').addEventListener('submit', e=>{
  e.preventDefault();
  let resumo="<h3>Resumo:</h3>", totalPagar=0, totalDevolver=0;

  // Aluguel
  const aluguelMensal=lerNumero('aluguelMensal');
  const dias=diasEntreDatas(document.getElementById('dataInicioAluguel').value,document.getElementById('dataFimAluguel').value);
  if(aluguelMensal>0 && dias>0){
    const total=aluguelMensal/30*dias;
    resumo+=`<p><strong>Aluguel pró-rata (${dias} dias):</strong> ${formatar(total)}</p>`;
    totalPagar+=total;
  }

  function proporcional(nome,total,pago,inicio,fim){
    if(total<=0) return "";
    const d=diasEntreDatas(inicio,fim);
    if(d===0) return "";
    const prop=(total/360)*d;
    const dif=prop-pago;
    let txt=`<p><strong>${nome} (${d} dias):</strong> ${formatar(prop)}`;
    if(pago>0) txt+=` - Pago: ${formatar(pago)}`;
    txt+=" → ";
    if(dif>=0){txt+="A pagar: "+formatar(dif)+"</p>"; totalPagar+=dif;} 
    else {txt+="A devolver: "+formatar(Math.abs(dif))+"</p>"; totalDevolver+=Math.abs(dif);}
    return txt;
  }

  resumo+=proporcional("IPTU",lerNumero('iptuTotal'),lerNumero('iptuPago'),document.getElementById('iptuInicio').value,document.getElementById('iptuFim').value);
  resumo+=proporcional("Seguro incêndio",lerNumero('seguroTotal'),lerNumero('seguroPago'),document.getElementById('seguroInicio').value,document.getElementById('seguroFim').value);

  function mensal(nome,mensal,pago,inicio,fim){
    if(mensal<=0) return "";
    const d=diasEntreDatas(inicio,fim);
    if(d<=0) return "";
    const prop=(mensal/30)*d;
    const dif=prop-pago;
    let txt=`<p><strong>${nome} (${d} dias):</strong> ${formatar(prop)}`;
    if(pago>0) txt+=` - Pago: ${formatar(pago)}`;
    txt+=" → ";
    if(dif>=0){txt+="A pagar: "+formatar(dif)+"</p>"; totalPagar+=dif;} 
    else {txt+="A devolver: "+formatar(Math.abs(dif))+"</p>"; totalDevolver+=Math.abs(dif);}
    return txt;
  }

  resumo+=mensal("Água",lerNumero('aguaTotal'),lerNumero('aguaPago'),document.getElementById('aguaInicio').value,document.getElementById('aguaFim').value);
  resumo+=mensal("Luz",lerNumero('luzTotal'),lerNumero('luzPago'),document.getElementById('luzInicio').value,document.getElementById('luzFim').value);
  resumo+=mensal("Condomínio",lerNumero('condTotal'),lerNumero('condPago'),document.getElementById('condInicio').value,document.getElementById('condFim').value);
  resumo+=mensal("Gás",lerNumero('gasTotal'),lerNumero('gasPago'),document.getElementById('gasInicio').value,document.getElementById('gasFim').value);

  const pinturaTotal=lerNumero('pinturaTotal'), pinturaPago=lerNumero('pinturaPago');
  if(pinturaTotal>0){
    const dif=pinturaTotal-pinturaPago;
    resumo+=`<p><strong>Pintura:</strong> ${formatar(pinturaTotal)}`;
    if(pinturaPago>0) resumo+=` - Pago: ${formatar(pinturaPago)}`;
    resumo+=" → "+(dif>=0?"A pagar: "+formatar(dif):"A devolver: "+formatar(Math.abs(dif)))+"</p>";
    if(dif>=0) totalPagar+=dif; else totalDevolver+=Math.abs(dif);
  }

  const manutencao=lerNumero('manutencao');
  if(manutencao>0){ const tot=manutencao*1.2; resumo+=`<p><strong>Manutenção (com 20%):</strong> ${formatar(tot)}</p>`; totalPagar+=tot; }
  const multa=lerNumero('multa'); if(multa>0){ resumo+=`<p><strong>Multa rescisória:</strong> ${formatar(multa)}</p>`; totalPagar+=multa; }

  // Outras despesas dinâmicas
  document.querySelectorAll('.outras-despesas-item').forEach(item=>{
    const desc=item.querySelector('.outraDescricao').value.trim();
    const val=parseFloat(item.querySelector('.outraValor').value.replace(/\./g,'').replace(',','.'))||0;
    if(desc!=="" && val>0){ resumo+=`<p><strong>${desc}:</strong> ${formatar(val)}</p>`; totalPagar+=val; }
  });

  resumo+="<hr>";
  resumo+=`<p><strong>Total a pagar:</strong> ${formatar(totalPagar)}</p>`;
  if(totalDevolver>0) resumo+=`<p><strong>Total a devolver:</strong> ${formatar(totalDevolver)}</p>`;
  const saldo=totalPagar-totalDevolver;
  resumo+=`<p><strong>Saldo final (${saldo>=0?"a pagar":"a receber"}):</strong> ${formatar(Math.abs(saldo))}</p>`;

  document.getElementById('resultado').innerHTML=resumo;
  document.getElementById('resultado').style.display='block';
});
</script>

</body>
</html>

<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<title>Calculadora de Entrega</title>

<style>
body{
  background:#111;
  color:#fff;
  font-family:Arial, sans-serif;
  padding:20px;
}
.box{
  max-width:430px;
  margin:auto;
  background:#1c1c1c;
  padding:20px;
  border-radius:10px;
}
input, button{
  width:100%;
  padding:10px;
  margin-top:10px;
  border-radius:5px;
  border:none;
  font-size:16px;
}
button{
  background:#00c853;
  font-weight:bold;
  cursor:pointer;
}
.resultado{
  margin-top:15px;
  font-size:18px;
  color:#ffd600;
  text-align:center;
}
small{
  color:#aaa;
}
</style>
</head>

<body>

<div class="box">
  <h2>Entrega – Fortaleza</h2>
  <small>Digite rua ou bairro (todos disponíveis)</small>

  <input id="origem" placeholder="Rua / bairro de origem">
  <input id="destino" placeholder="Rua / bairro de destino">

  <button onclick="calcularEntrega()">Calcular entrega</button>

  <div class="resultado" id="resultado"></div>
</div>

<script>
let origemAuto, destinoAuto;

function initAutocomplete(){
  const options = {
    componentRestrictions: { country: "br" }, // Brasil
    fields: ["geometry", "address_components"],
    types: ["address"]
  };

  origemAuto = new google.maps.places.Autocomplete(
    document.getElementById("origem"), options
  );

  destinoAuto = new google.maps.places.Autocomplete(
    document.getElementById("destino"), options
  );
}

function calcularEntrega(){
  const origem = origemAuto.getPlace();
  const destino = destinoAuto.getPlace();

  if(!origem || !destino || !origem.geometry || !destino.geometry){
    document.getElementById("resultado").innerHTML =
      "Selecione os endereços na lista.";
    return;
  }

  // Coordenadas
  const lat1 = origem.geometry.location.lat();
  const lon1 = origem.geometry.location.lng();
  const lat2 = destino.geometry.location.lat();
  const lon2 = destino.geometry.location.lng();

  // Distância em KM
  const distancia = calcularDistancia(lat1, lon1, lat2, lon2);

  // 💰 Cálculo do valor
  let valor = 5;

  if (distancia <= 1.5) {
    valor = 5;
  } else if (distancia <= 2) {
    valor = 7;
  } else if (distancia <= 2.5) {
    valor = 8;
  } else {
    const kmExtra = Math.ceil(distancia - 2.5);
    valor = 8 + (kmExtra * 2.5);
  }

  document.getElementById("resultado").innerHTML =
    `Distância: ${distancia.toFixed(2)} km<br>
     Valor da entrega: <strong>R$ ${valor.toFixed(2)}</strong>`;
}

// Fórmula de Haversine
function calcularDistancia(lat1, lon1, lat2, lon2){
  const R = 6371;
  const dLat = (lat2 - lat1) * Math.PI / 180;
  const dLon = (lon2 - lon1) * Math.PI / 180;

  const a =
    Math.sin(dLat/2) * Math.sin(dLat/2) +
    Math.cos(lat1 * Math.PI / 180) *
    Math.cos(lat2 * Math.PI / 180) *
    Math.sin(dLon/2) * Math.sin(dLon/2);

  const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
  return R * c;
}
</script>

<script
src="https://maps.googleapis.com/maps/api/js?key=SUA_CHAVE_AQUI&libraries=places&callback=initAutocomplete"
async defer>
</script>

</body>
</html>
 Meu-frete-3

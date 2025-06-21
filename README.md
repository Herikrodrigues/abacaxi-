<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8">
  <title>Urna Eletrônica</title>
  <style>
    body { font-family: Arial, sans-serif; background: #f2f2f2; }
    .container { max-width: 500px; margin: 40px auto; background: #fff; padding: 24px; border-radius: 8px; box-shadow: 0 2px 8px #0002; }
    label { display: block; margin-top: 12px; }
    input, select { width: 100%; padding: 8px; margin-top: 4px; border-radius: 4px; border: 1px solid #ccc; }
    button { margin-top: 16px; padding: 10px 20px; border: none; background: #1976d2; color: #fff; border-radius: 4px; cursor: pointer; }
    .hidden { display: none; }
    .tabs { display: flex; margin-top: 20px; }
    .tab-btn { flex: 1; padding: 10px; background: #eee; border: none; cursor: pointer; }
    .tab-btn.active { background: #1976d2; color: #fff; }
    .tab-content { margin-top: 20px; }
    table { width: 100%; border-collapse: collapse; margin-top: 16px; }
    th, td { border: 1px solid #ccc; padding: 8px; text-align: center; }
    th { background: #f0f0f0; }
  </style>
</head>
<body>
  <div class="container">
    <!-- Formulário de dados pessoais -->
    <form id="dadosForm">
      <h2>Dados Pessoais</h2>
      <label for="nome">Nome:</label>
      <input type="text" id="nome" name="nome" required>
      <label for="sobrenome">Sobrenome:</label>
      <input type="text" id="sobrenome" name="sobrenome" required>
      <label for="cpf">CPF:</label>
      <input type="text" id="cpf" name="cpf" maxlength="14" required placeholder="000.000.000-00">
      <label for="dataNasc">Data de Nascimento:</label>
      <input type="date" id="dataNasc" name="dataNasc" required>
      <button type="submit">Próximo</button>
    </form>

    <!-- Formulário de senha -->
    <form id="senhaForm" class="hidden">
      <h2>Digite sua Senha</h2>
      <label for="senha">Senha:</label>
      <input type="password" id="senha" name="senha" required>
      <button type="submit">Entrar</button>
    </form>

    <!-- Abas de votação -->
    <div id="urna" class="hidden">
      <h2>Urna Eletrônica</h2>
      <div class="tabs">
        <button class="tab-btn active" id="tabCandidatosBtn" type="button">Candidatos</button>
        <button class="tab-btn" id="tabElegerBtn" type="button">Se Eleger</button>
      </div>
      <div class="tab-content" id="tabCandidatos">
        <h3>Vote em um Partido</h3>
        <form id="votoForm">
          <label for="partido">Escolha o Partido:</label>
          <select id="partido" required>
            <option value="">Selecione</option>
            <option value="Partido A">Partido A</option>
            <option value="Partido B">Partido B</option>
            <option value="Partido C">Partido C</option>
          </select>
          <label for="votos">Quantidade de Votos (1 a 100):</label>
          <input type="number" id="votos" min="1" max="100" required>
          <button type="submit">Votar</button>
        </form>
        <div id="votoMsg"></div>
      </div>
      <div class="tab-content hidden" id="tabEleger">
        <h3>Candidatar-se a Presidente e Vice</h3>
        <form id="elegerForm">
          <label for="presidente">Nome Completo do Presidente:</label>
          <input type="text" id="presidente" required>
          <label for="vice">Nome Completo do Vice:</label>
          <input type="text" id="vice" required>
          <label for="partidoEleger">Partido:</label>
          <select id="partidoEleger" required>
            <option value="">Selecione</option>
            <option value="Partido A">Partido A</option>
            <option value="Partido B">Partido B</option>
            <option value="Partido C">Partido C</option>
          </select>
          <button type="submit">Registrar Candidatura</button>
        </form>
        <div id="elegerMsg"></div>
      </div>
      <button id="btnResultado" style="margin-top:24px;">Ver Resultado</button>
      <div id="resultado" class="hidden"></div>
    </div>
  </div>
  <script>
    // Controle de abas
    const tabCandidatosBtn = document.getElementById('tabCandidatosBtn');
    const tabElegerBtn = document.getElementById('tabElegerBtn');
    const tabCandidatos = document.getElementById('tabCandidatos');
    const tabEleger = document.getElementById('tabEleger');

    tabCandidatosBtn.onclick = () => {
      tabCandidatosBtn.classList.add('active');
      tabElegerBtn.classList.remove('active');
      tabCandidatos.classList.remove('hidden');
      tabEleger.classList.add('hidden');
    };
    tabElegerBtn.onclick = () => {
      tabElegerBtn.classList.add('active');
      tabCandidatosBtn.classList.remove('active');
      tabEleger.classList.remove('hidden');
      tabCandidatos.classList.add('hidden');
    };

    // Controle de formulários
    const dadosForm = document.getElementById('dadosForm');
    const senhaForm = document.getElementById('senhaForm');
    const urna = document.getElementById('urna');

    dadosForm.addEventListener('submit', function(e) {
      e.preventDefault();
      dadosForm.classList.add('hidden');
      senhaForm.classList.remove('hidden');
    });

    senhaForm.addEventListener('submit', function(e) {
      e.preventDefault();
      senhaForm.classList.add('hidden');
      urna.classList.remove('hidden');
    });

    // Dados de votação e candidatos
    let votos = {
      "Partido A": 0,
      "Partido B": 0,
      "Partido C": 0
    };
    let candidatos = {
      "Partido A": { presidente: "", vice: "" },
      "Partido B": { presidente: "", vice: "" },
      "Partido C": { presidente: "", vice: "" }
    };

    // Votação
    document.getElementById('votoForm').addEventListener('submit', function(e) {
      e.preventDefault();
      const partido = document.getElementById('partido').value;
      const qtdVotos = parseInt(document.getElementById('votos').value, 10);
      const votoMsg = document.getElementById('votoMsg');
      if (!partido || isNaN(qtdVotos) || qtdVotos < 1 || qtdVotos > 100) {
        votoMsg.textContent = "Preencha corretamente os campos.";
        votoMsg.style.color = "red";
        return;
      }
      votos[partido] += qtdVotos;
      votoMsg.textContent = `Voto registrado para ${partido} (${qtdVotos} votos)!`;
      votoMsg.style.color = "green";
      this.reset();
    });

    // Registro de candidatura
    document.getElementById('elegerForm').addEventListener('submit', function(e) {
      e.preventDefault();
      const presidente = document.getElementById('presidente').value.trim();
      const vice = document.getElementById('vice').value.trim();
      const partido = document.getElementById('partidoEleger').value;
      const elegerMsg = document.getElementById('elegerMsg');
      if (!presidente || !vice || !partido) {
        elegerMsg.textContent = "Preencha todos os campos.";
        elegerMsg.style.color = "red";
        return;
      }
      candidatos[partido] = { presidente, vice };
      elegerMsg.textContent = `Candidatura registrada para o ${partido}!`;
      elegerMsg.style.color = "green";
      this.reset();
    });

    // Resultado
    document.getElementById('btnResultado').onclick = function() {
      const resultadoDiv = document.getElementById('resultado');
      resultadoDiv.classList.remove('hidden');
      // Encontrar o partido vencedor
      let vencedor = null;
      let maiorVoto = -1;
      for (const partido in votos) {
        if (votos[partido] > maiorVoto) {
          maiorVoto = votos[partido];
          vencedor = partido;
        }
      }
      let html = `<h3>Resultado Final</h3>
        <table>
          <tr>
            <th>Partido</th>
            <th>Presidente</th>
            <th>Vice</th>
            <th>Votos</th>
          </tr>`;
      for (const partido in votos) {
        html += `<tr>
          <td>${partido}</td>
          <td>${candidatos[partido].presidente || '-'}</td>
          <td>${candidatos[partido].vice || '-'}</td>
          <td>${votos[partido]}</td>
        </tr>`;
      }
      html += `</table>`;
      if (maiorVoto > 0) {
        html += `<p><strong>Vencedor: ${vencedor}</strong><br>
        Presidente: ${candidatos[vencedor].presidente || '-'}<br>
        Vice: ${candidatos[vencedor].vice || '-'}<br>
        Total de votos: ${maiorVoto}</p>`;
      } else {
        html += `<p>Nenhum voto registrado ainda.</p>`;
      }
      resultadoDiv.innerHTML = html;
    };
  </script>
</body>
</html>

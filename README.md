# jornal------escolar
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <title>Jornal Escolar Afonso Pena</title>
  <script type="module">
    // Firebase Imports
    import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.0/firebase-app.js";
    import { getDatabase, ref, push, onValue, set, remove, update } from "https://www.gstatic.com/firebasejs/10.12.0/firebase-database.js";

    const firebaseConfig = {
      apiKey: "AIzaSyBDxkdw7NOy706HCI159oO_o05LGI6dg5c",
      authDomain: "jornal-escolar-7f8e6.firebaseapp.com",
      databaseURL: "https://jornal-escolar-7f8e6-default-rtdb.firebaseio.com",
      projectId: "jornal-escolar-7f8e6",
      storageBucket: "jornal-escolar-7f8e6.appspot.com",
      messagingSenderId: "583101705398",
      appId: "1:583101705398:web:05e70f99042ea8ac5a0a17",
      measurementId: "G-PPKYRDFMC1"
    };

    const app = initializeApp(firebaseConfig);
    const db = getDatabase(app);

    let modoAdmin = false;
    let noticias = {};
    
    window.mostrarLogin = function() {
      document.getElementById('loginBox').classList.add('active');
      document.getElementById('siteContent').classList.remove('active');
      document.getElementById('adminPanel').classList.remove('active');
    }

    window.verificarSenha = function () {
      const senha = document.getElementById('senha').value;
      if (senha === "escola afonso pena 123455") {
        modoAdmin = true;
        document.getElementById('loginBox').classList.remove('active');
        document.getElementById('adminPanel').classList.add('active');
        document.getElementById('siteContent').classList.add('active');
      } else {
        document.getElementById('erro').textContent = "Senha incorreta!";
      }
    }

    window.mostrarFormulario = function(id = null) {
      const form = document.getElementById('formNoticia');
      if (id) {
        document.getElementById('titulo').value = noticias[id].titulo;
        document.getElementById('conteudo').value = noticias[id].conteudo;
        document.getElementById('indiceEditar').value = id;
      } else {
        form.reset();
        document.getElementById('indiceEditar').value = "";
      }
      form.style.display = 'block';
    }

    window.criarOuEditarNoticia = function(event) {
      event.preventDefault();
      const titulo = document.getElementById('titulo').value;
      const conteudo = document.getElementById('conteudo').value;
      const id = document.getElementById('indiceEditar').value;

      if (id) {
        update(ref(db, 'noticias/' + id), { titulo, conteudo });
      } else {
        push(ref(db, 'noticias'), { titulo, conteudo });
      }

      document.getElementById('formNoticia').reset();
      document.getElementById('formNoticia').style.display = 'none';
    }

    window.alternarConteudo = function(elemento) {
      const conteudo = elemento.nextElementSibling;
      conteudo.style.display = conteudo.style.display === 'block' ? 'none' : 'block';
    }

    window.apagarNoticia = function(id) {
      if (confirm("Deseja apagar esta notícia?")) {
        remove(ref(db, 'noticias/' + id));
      }
    }

    window.sair = function() {
      modoAdmin = false;
      document.getElementById('adminPanel').classList.remove('active');
    }

    function atualizarNoticiasNaTela(snapshot) {
      const container = document.getElementById('siteContent');
      container.innerHTML = "";
      noticias = snapshot.val() || {};

      for (const id in noticias) {
        const noticia = noticias[id];

        const div = document.createElement('div');
        div.className = 'noticia';
        div.innerHTML = `
          <h2 onclick="alternarConteudo(this)">${noticia.titulo}</h2>
          <div class="conteudo"><p>${noticia.conteudo}</p></div>
        `;

        if (modoAdmin) {
          const acoesDiv = document.createElement('div');
          acoesDiv.className = 'acoes';

          const btnEditar = document.createElement('button');
          btnEditar.innerHTML = "✏️";
          btnEditar.className = "btn-acao";
          btnEditar.title = "Editar notícia";
          btnEditar.onclick = () => mostrarFormulario(id);

          const btnExcluir = document.createElement('button');
          btnExcluir.innerHTML = "🗑️";
          btnExcluir.className = "btn-acao";
          btnExcluir.title = "Excluir notícia";
          btnExcluir.onclick = () => apagarNoticia(id);

          acoesDiv.appendChild(btnEditar);
          acoesDiv.appendChild(btnExcluir);
          div.appendChild(acoesDiv);
        }

        container.appendChild(div);
      }
    }

    // Escuta alterações em tempo real
    onValue(ref(db, 'noticias'), atualizarNoticiasNaTela);
  </script>

  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #f0f2f5;
      margin: 0;
      padding: 0;
      text-align: center;
    }

    header {
      background-color: #004080;
      color: white;
      padding: 20px;
    }

    .site-content, .login-box, .admin-panel {
      display: none;
      margin-top: 30px;
    }

    .active {
      display: block;
    }

    .noticia {
      background: white;
      margin: 20px auto;
      padding: 20px;
      border-radius: 10px;
      width: 80%;
      max-width: 600px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
      text-align: left;
      position: relative;
    }

    .noticia h2 {
      cursor: pointer;
      color: #004080;
      margin: 0;
    }

    .conteudo {
      display: none;
      margin-top: 10px;
    }

    input, textarea {
      padding: 10px;
      margin: 10px auto;
      display: block;
      width: 80%;
      max-width: 400px;
      font-size: 16px;
    }

    button {
      padding: 10px 20px;
      margin: 10px 5px;
      border: none;
      background-color: #0077cc;
      color: white;
      border-radius: 5px;
      cursor: pointer;
      font-size: 16px;
    }

    textarea {
      resize: vertical;
    }

    #formNoticia {
      margin-top: 20px;
      display: none;
    }

    .acoes {
      text-align: right;
      margin-top: 10px;
    }

    .btn-acao {
      background: transparent;
      border: none;
      font-size: 20px;
      cursor: pointer;
      color: #555;
      margin-left: 10px;
      transition: color 0.3s;
    }

    .btn-acao:hover {
      color: #0077cc;
    }
  </style>
</head>
<body>

  <header>
    <h1>Jornal Escolar Afonso Pena</h1>
    <button onclick="mostrarLogin()">Administração</button>
  </header>

  <div class="site-content active" id="siteContent">
    <!-- Notícias -->
  </div>

  <div class="login-box" id="loginBox">
    <h2>Login do Administrador</h2>
    <input type="password" id="senha" placeholder="Digite a senha" />
    <button onclick="verificarSenha()">Entrar</button>
    <p id="erro" style="color:red;"></p>
  </div>

  <div class="admin-panel" id="adminPanel">
    <h2>Painel do Administrador</h2>
    <button onclick="mostrarFormulario()">+ Nova Notícia</button>
    <button onclick="sair()">Sair</button>

    <form id="formNoticia" onsubmit="criarOuEditarNoticia(event)">
      <input type="text" id="titulo" placeholder="Título da notícia" required />
      <textarea id="conteudo" placeholder="Conteúdo da notícia" rows="5" required></textarea>
      <input type="hidden" id="indiceEditar" />
      <button type="submit">Salvar</button>
    </form>
  </div>

</body>
</html>

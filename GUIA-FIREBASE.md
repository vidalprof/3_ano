# Guia: ligar a nuvem (Firebase) no Portal de Links

Com a nuvem ligada, tudo que você editar aparece **na hora** para os alunos, em
qualquer aparelho — sem precisar enviar nada para o GitHub.

> ⚠️ **Importante para não afetar a sua agenda de aulas:**
> A forma mais segura é **criar um projeto Firebase NOVO**, separado da agenda.
> O plano gratuito permite vários projetos na mesma conta, cada um com seu
> próprio banco, logins e regras. Assim a agenda fica intocada.

---

## Passo 1 — Criar um projeto novo
1. Acesse <https://console.firebase.google.com>.
2. Clique em **Adicionar projeto** e dê um nome (ex.: `portal-vidal-ramos`).
3. Pode desativar o Google Analytics (não é necessário). Clique em **Criar projeto**.

## Passo 2 — Criar o Realtime Database
1. No menu à esquerda: **Criar** → **Realtime Database**.
2. Clique em **Criar banco de dados**.
3. Escolha a localização (pode deixar a padrão, ex.: `us-central1`).
4. Comece em **modo bloqueado** (locked). As regras corretas estão no Passo 4.

## Passo 3 — Ativar o login do professor (Authentication)
1. No menu: **Criar** → **Authentication** → **Vamos começar**.
2. Aba **Sign-in method** → ative **E-mail/senha** → **Salvar**.
3. Aba **Users** → **Adicionar usuário**:
   - E-mail: um e-mail seu (ex.: `professor@vidalramos.com` — pode ser fictício,
     mas anote certinho).
   - Senha: escolha uma senha forte. **Essa será a senha do modo professor.**

## Passo 4 — Definir as regras de segurança (todos leem, só você edita)
1. Volte em **Realtime Database** → aba **Regras (Rules)**.
2. Cole exatamente isto e clique em **Publicar**:

```json
{
  "rules": {
    "portal": {
      ".read": true,
      ".write": "auth != null"
    }
  }
}
```

Isso significa: **qualquer aluno pode ler** os links; **só quem está logado**
(você) pode escrever/alterar.

> Se um dia você reaproveitar o mesmo projeto da agenda, **não apague** as regras
> que já existirem — apenas acrescente o bloco `"portal": { ... }` ao lado dos
> outros. Como os dados ficam no galho `portal`, a agenda não é afetada.

## Passo 5 — Copiar a configuração do projeto
1. No menu, clique na engrenagem ⚙️ → **Configurações do projeto**.
2. Role até **Seus aplicativos** → clique no ícone **`</>`** (Web) para registrar
   um app web. Dê um apelido (ex.: `portal`) e clique em **Registrar**.
3. O Firebase mostra um objeto parecido com este — **copie os valores**:

```js
const firebaseConfig = {
  apiKey: "AIza...",
  authDomain: "portal-vidal-ramos.firebaseapp.com",
  databaseURL: "https://portal-vidal-ramos-default-rtdb.firebaseio.com",
  projectId: "portal-vidal-ramos",
  // ...
};
```

> Se **não** aparecer a linha `databaseURL`, copie a URL que está no topo da
> aba **Realtime Database** (algo como
> `https://SEU-PROJETO-default-rtdb.firebaseio.com`).

## Passo 6 — Colar no site
Abra o arquivo **`index.html`** e localize, perto do início do `<script>`, estas
linhas:

```js
var FIREBASE_CONFIG = null;
var PROF_EMAIL = "";
```

Troque por (usando os SEUS valores do Passo 5 e o e-mail do Passo 3):

```js
var FIREBASE_CONFIG = {
  apiKey: "AIza...",
  authDomain: "portal-vidal-ramos.firebaseapp.com",
  databaseURL: "https://portal-vidal-ramos-default-rtdb.firebaseio.com",
  projectId: "portal-vidal-ramos"
};
var PROF_EMAIL = "professor@vidalramos.com";
```

Salve e envie o `index.html` para o GitHub (esta é a **última** vez que você
precisa enviar algo — daqui pra frente tudo salva sozinho na nuvem).

## Pronto! 🎉
- Abra o site, clique no cadeado 🔒 e entre com a **senha do Passo 3**.
- Adicione/edite/remova turmas e links: **salva sozinho e os alunos veem na hora.**
- Aparece o selo **☁️ Salva automático** e o botão *Publicar* some (não é mais
  necessário).

---

## É grátis mesmo? E sustentável?
Sim. No plano gratuito (Spark), o Realtime Database oferece **1 GB de
armazenamento**, **10 GB de download/mês** e **100 conexões simultâneas** — por
projeto. Seus links são texto puro (poucos KB), então você fica muito longe de
qualquer limite. Sem cartão de crédito.

## Segurança
- Só quem tem a **senha do professor** (Authentication) consegue editar.
- Os alunos só conseguem **ler**.
- Se alguém abrir o código e ver a `FIREBASE_CONFIG`, **não é problema**: essas
  chaves são públicas por natureza; quem protege a edição são as **regras** do
  Passo 4, não o segredo da config.

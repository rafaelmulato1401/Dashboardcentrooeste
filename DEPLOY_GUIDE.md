# GUIA DE DEPLOY — Dashboard Operações Logística

## PASSO 1 — Firebase (banco de dados)

### 1.1 Criar projeto
1. Acesse https://console.firebase.google.com
2. Clique em **"Criar um projeto"**
3. Nome: `inpasa-logistica` (ou o que preferir)
4. Desative Google Analytics (não precisa) → **Criar projeto**

### 1.2 Ativar Firestore
1. No menu lateral: **Build → Firestore Database**
2. Clique **"Criar banco de dados"**
3. Escolha **"Iniciar no modo de produção"**
4. Região: `southamerica-east1` (São Paulo) → **Concluído**

### 1.3 Configurar regras de segurança
No Firestore → aba **Regras**, cole:
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /empresas/{empresaId}/{document=**} {
      allow read, write: if request.auth != null;
    }
  }
}
```
Clique **Publicar**.

### 1.4 Ativar autenticação por e-mail
1. **Build → Authentication → Começar**
2. Aba **Sign-in method** → **E-mail/senha → Ativar → Salvar**
3. Aba **Usuários → Adicionar usuário** → cadastre os e-mails da equipe

### 1.5 Pegar as credenciais
1. ⚙️ Configurações do projeto → **Seus aplicativos → Web (</>)**
2. Nome do app: `dashboard`
3. **NÃO** marque Firebase Hosting
4. Copie o objeto `firebaseConfig` que aparecer

---

## PASSO 2 — Configurar o index.html

Abra o `index.html` e substitua na seção `FIREBASE_CONFIG`:
```js
const FIREBASE_CONFIG = {
  apiKey:            "AIzaSy...",        // ← seu valor
  authDomain:        "inpasa-xxx.firebaseapp.com",
  projectId:         "inpasa-xxx",
  storageBucket:     "inpasa-xxx.appspot.com",
  messagingSenderId: "123456789",
  appId:             "1:123:web:abc"
};
const EMPRESA_ID = "inpasa"; // pode manter assim
```

---

## PASSO 3 — GitHub (repositório)

```bash
# Na pasta do projeto:
git init
git add .
git commit -m "Dashboard Operações Logística v1"

# Crie um repositório em https://github.com/new
# Nome sugerido: dashboard-terminais
# Visibilidade: Private ✓

git remote add origin https://github.com/SEU_USER/dashboard-terminais.git
git branch -M main
git push -u origin main
```

---

## PASSO 4 — Vercel (hospedagem)

1. Acesse https://vercel.com → **Login com GitHub**
2. Clique **"New Project"**
3. Importe o repositório `dashboard-terminais`
4. Configurações: tudo padrão (detecta automaticamente)
5. Clique **Deploy**

Em ~2 minutos o app estará em:
`https://dashboard-terminais-xxx.vercel.app`

### Domínio personalizado (opcional)
Vercel → Settings → Domains → adicione seu domínio

---

## PASSO 5 — Deploy automático

A partir de agora, sempre que fizer push no GitHub:
```bash
git add index.html
git commit -m "Atualização dashboard"
git push
```
O Vercel detecta e refaz o deploy automaticamente em ~30 segundos.

---

## FLUXO DIÁRIO DE UPLOAD

1. Acesse a URL do dashboard (Vercel)
2. Clique **"↑ Upload Dados"**
3. Importe na ordem:
   - **① Notas Fiscais** (primeiro, sempre)
   - **② Descargas**
   - **③ Trânsito**
   - **④ Estoque** (seleciona produto + data no pop-up)

Os dados sobem para o **Firestore** e ficam disponíveis para **toda a equipe** em tempo real.

---

## ESTRUTURA DOS DADOS NO FIRESTORE

```
empresas/
  inpasa/
    dados/
      descargas    → {value: [...2139 registros...]}
      transito     → {value: [...178 registros...]}
      notas        → {value: [...1276 registros...]}
      estoque      → {value: [...histórico diário...]}
      config       → {value: {teciap:{...}, ultra:{...}, ferro:{...}}}
      produtoMap   → {value: {"1_351346": "Anidro", ...}}
      nfClienteMap → {value: {"teciap_351346": "LOGUM", ...}}
      _meta        → {lastUpdate: "19/05/2026, 09:35:10"}
```

---

## CUSTOS FIREBASE

| Plano | Firestore reads | Firestore writes | Preço |
|-------|----------------|------------------|-------|
| **Spark (gratuito)** | 50.000/dia | 20.000/dia | **R$ 0** |
| Blaze (pay-as-you-go) | $0.06/100k | $0.18/100k | Mínimo |

Para o uso atual (~10 uploads/dia, ~20 usuários), o **plano gratuito é suficiente**.

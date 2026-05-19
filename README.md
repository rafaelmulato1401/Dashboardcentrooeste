# Dashboard Operações Logística — Terminais

Dashboard de monitoramento de terminais ferroviários e rodoviários.

## Stack
- **Frontend:** HTML + JS puro (sem framework)
- **Banco de dados:** Firebase Firestore
- **Autenticação:** Firebase Auth
- **Hospedagem:** Vercel (deploy automático via GitHub)

## Estrutura
```
dashboard-terminais/
├── index.html          ← App completo
├── README.md
├── vercel.json         ← Configuração Vercel
└── .gitignore
```

## Deploy
1. Push para GitHub → Vercel detecta automaticamente e faz deploy
2. Firebase Firestore armazena os dados de upload
3. Todos os usuários veem os mesmos dados em tempo real

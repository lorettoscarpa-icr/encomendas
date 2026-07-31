# Central de Encomendas — Loretto Scarpa

App de página única (`index.html`, sem build) publicado no GitHub Pages.
Backend: Firebase (Firestore + Storage + Auth), carregado via CDN compat 10.7.0.
Não há `package.json`, dependências ou testes — editar o HTML é o fluxo de trabalho.

Constantes de negócio ficam no bloco `<script>` no topo do arquivo:
`FIREBASE_CONFIG`, `PRAZO_DIAS` (15), `ATENCAO_DIAS` (12), `CRONOMETRO`,
`VENDEDORAS`, `WHATS_GESTOR`.

## Banco MCP

O repositório declara o **Banco MCP** (Open Finance Brasil, via mcp.ai) como
servidor MCP de projeto em `.mcp.json`, para consultar dados bancários —
saldos, extratos, faturas — durante o trabalho nas encomendas.

Uso típico: conferir se um recebimento (PIX/transferência) bate com o valor de
uma encomenda registrada no Firestore.

### Configuração

- **Claude Code na web**: nada a fazer. O Banco MCP já vem do conector da conta
  claude.ai e as ferramentas `mcp__Banco_MCP__*` estão disponíveis na sessão.
- **Claude Code local (CLI)**: defina a variável de ambiente `BANCO_MCP_URL`
  com a URL do toolkit em app.mcp.ai antes de abrir a sessão. O `.mcp.json`
  a expande em tempo de execução, de modo que a URL não fica versionada.
  Na primeira execução o Claude pede aprovação do servidor de projeto.

### Permissões

`.claude/settings.json` classifica as ferramentas do Banco MCP:

- **allow** — leitura (contas, saldos, transações, faturas, categorias).
- **ask** — escrita e cobrança (`force_sync`, `update_transaction_category`,
  `subscribe`, `marketplace`).
- **deny** — `openfinance_disconnect_bank`, que revoga o consentimento
  Open Finance e apaga os dados da conexão.

O Banco MCP é somente leitura sobre os bancos; ele nunca movimenta dinheiro.

## Escopo

O `index.html` roda 100% no navegador e **não** fala com o Banco MCP. Uma
integração dentro do app exigiria um intermediário no servidor, já que
credenciais MCP não podem ficar em página pública.

O repositório `lorettoscarpa-icr/loretto-bling` já é esse intermediário para a
Central de Compras: é um servidor Express no Render com integração Pluggy
(`pluggy.js`, endpoints `/api/pluggy/*`). Se a Central de Encomendas precisar de
dados bancários, o caminho natural é consumir aquele servidor em vez de criar
outro backend.

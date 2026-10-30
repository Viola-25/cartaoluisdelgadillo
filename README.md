# Cartão de visita digital — Dr. Luis Fernando Delgadillo Trigo

Site estático (HTML + CSS + JS, sem build) com painel de edição para o professor.

```
index.html              → o cartão (só layout; busca o conteúdo via fetch)
content/identidade.json → nome, foto, especialidade
content/resumo.json     → parágrafos do resumo profissional
content/contatos.json   → lista de contatos
content/formacao.json   → formação acadêmica
content/experiencias.json → experiências profissionais
admin/index.html        → painel do Decap CMS (+ CSS de ajustes)
admin/config.yml        → campos do formulário do painel
assets/uploads/         → imagens enviadas pelo painel
netlify.toml            → config de deploy (publish = raiz)
COMO-EDITAR.md          → guia para o professor
```

## Como funciona

1. `index.html` busca os 5 JSONs em paralelo e monta a página.
2. O professor entra em `/admin/`, escolhe uma das 5 partes, edita e clica em **Publicar**.
3. O Decap CMS commita o JSON alterado no branch `main` via **Git Gateway**.
4. O Netlify detecta o commit e republica o site (~30–60s).

Login: **email + senha** (Netlify Identity), sem conta no GitHub.

---

## Setup (uma vez só)

### 1. Publicar no Netlify

1. Crie conta em <https://app.netlify.com> (pode entrar com GitHub).
2. **Add new site → Import an existing project → GitHub** → escolha `cartaoluisdelgadillo`.
3. Build command: **vazio**. Publish directory: **`.`** (já vem do `netlify.toml`).
4. Deploy. Anote a URL gerada, ex.: `https://cute-fox-e090dd.netlify.app`.

> **Importante:** o login por email/senha depende do Netlify Identity. Em GitHub Pages o `/admin/` não autentica.

### 2. Ativar o Identity

No site: **Site configuration → Identity** → **Enable Identity**.

- **Registration:** `Invite only` (ninguém se cadastra sozinho).
- **Emails → Email confirmation:** deixe ligado.

### 3. Ativar o Git Gateway

**Site configuration → Identity → Services → Git Gateway → Enable**.

É isso que permite o painel commitar no GitHub sem o professor ter conta lá.

### 4. Convidar o professor

**Identity → Invite users** → email dele → **Send**.

Ele recebe um email, define a senha, e é redirecionado para `/admin/`.

### 5. Testar

1. Abra `https://SEU-SITE.netlify.app/admin/`.
2. Login → **Cartão de visita** → **3. Contatos**.
3. Mude algo, **Publicar**, espere, recarregue a home.
4. Confira no GitHub: deve aparecer um commit `CMS: atualizou cartao`.

---

## Decisões de layout do painel

O layout do Decap é fixo; o que dá para controlar é a estrutura do formulário.

- **5 arquivos separados** em vez de 1: cada tela de edição fica curta, e a lista de
  conteúdo mostra 5 itens numerados e nomeados em português.
- **Campos bilíngues achatados** (`especialidade_pt` / `especialidade_es`) em vez de
  objetos aninhados: o widget `object` do Decap desenha uma faixa cinza vazia e
  caixas dentro de caixas, o que confunde. Formulário plano lê melhor.
- **Listas recolhidas** (`collapsed: true`) com `summary`: o professor vê uma linha
  por item (`Email — delgadillo@uol.com.br`) e abre só o que quer editar.
- **`label_singular`**: botão diz "Adicionar contato", não "Adicionar item".
- **`hint` em quase todo campo**: explicação em português embaixo do campo.
- **`editor.preview: false`**: sem painel de preview lateral.
- **`show_preview_links: false`** + CSS em `admin/index.html` esconde o botão
  "Verificar se há Pré-visualização" (o link continuava aparecendo mesmo com a opção).
- **Títulos das seções fixos no código**: não são campos do painel.

> Sveltia CMS tem UI bem mais moderna, mas **não suporta Git Gateway** — exigiria
> conta GitHub para o professor. Por isso ficamos no Decap.

## Desenvolvimento local

O `fetch` não funciona abrindo o arquivo direto (`file://`). Use um servidor:

```powershell
npx serve .
# ou
python -m http.server 8000
```

Para testar o painel localmente sem Netlify, use o proxy do Decap:

```powershell
npx decap-server      # proxy local na porta 8081
```

Com o proxy rodando, sirva o site em `localhost` e adicione `local_backend: true`
ao `admin/config.yml` (só para teste — não commitar).

O painel publicado (`/admin/`) só autentica no domínio do Netlify.

## Notas técnicas

- Sem dependência de build: HTML + JSON.
- `pick()` resolve campos `{pt, es}`; `tr(obj, 'base')` resolve `base_pt` / `base_es`
  e cai para o português quando o espanhol está vazio.
- Ícones: Font Awesome 6 via CDN (mesma do original).
- A home depende de JS para renderizar (SEO de cartão de visita: irrelevante).

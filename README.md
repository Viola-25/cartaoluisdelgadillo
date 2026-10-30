# Cartão de visita digital — Dr. Luis Fernando Delgadillo Trigo

Site estático (HTML + CSS + JS, sem build) com painel de edição para o professor.

```
index.html            → o cartão (só layout; busca o conteúdo via fetch)
content/perfil.json   → TODO o conteúdo (PT/ES) — fonte única da verdade
admin/index.html      → painel do Decap CMS
admin/config.yml      → campos do formulário do painel
assets/uploads/       → imagens enviadas pelo painel
netlify.toml          → config de deploy (publish = raiz)
COMO-EDITAR.md        → guia para o professor
```

## Como funciona

1. `index.html` faz `fetch('content/perfil.json')` e monta a página.
2. O professor entra em `/admin/`, edita por formulário e clica em **Publish**.
3. O Decap CMS commita o `content/perfil.json` no branch `main` via **Git Gateway**.
4. O Netlify detecta o commit e republica o site (~30–60s).

Login: **email + senha** (Netlify Identity), sem conta no GitHub.

---

## Setup (uma vez só)

### 1. Publicar no Netlify

1. Crie conta em <https://app.netlify.com> (pode entrar com GitHub).
2. **Add new site → Import an existing project → GitHub** → escolha `cartaoluisdelgadillo`.
3. Build command: **vazio**. Publish directory: **`.`** (já vem do `netlify.toml`).
4. Deploy. Anote a URL gerada, ex.: `https://cartaoluisdelgadillo.netlify.app`.

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
2. Login → **Cartão de visita** → **Dados do cartão**.
3. Mude algo, **Publish**, espere, recarregue a home.
4. Confira no GitHub: deve aparecer um commit `CMS: atualizou cartao`.

---

## Desenvolvimento local

O `fetch` não funciona abrindo o arquivo direto (`file://`). Use um servidor:

```powershell
npx serve .
# ou
python -m http.server 8000
```

O painel `/admin/` **não** funciona em `localhost` sem o Netlify (o Identity precisa do domínio publicado). Para testar o painel localmente, use `netlify dev` com a CLI do Netlify logada.

## Notas técnicas

- Não há dependência de build: só 3 arquivos + JSON.
- `pick()` no `index.html` resolve campos `{pt, es}`; string simples é usada nos dois idiomas.
- Ícones: Font Awesome 6 via CDN (mesma do original).
- Para trocar a hospedagem mantendo login por email/senha, é preciso um backend compatível com o Decap (Netlify Identity/Git Gateway, ou DecapBridge para login via GitHub).
- `summary` das listas usa campos aninhados (`{{fields.rotulo.pt}}`) — só afeta o rótulo do item recolhido no painel.

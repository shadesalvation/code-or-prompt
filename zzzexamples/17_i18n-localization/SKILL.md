---
name: i18n-localization
description: Padrões de internacionalização e localização. Detecção de strings codificadas (hardcoded), gerenciamento de traduções, arquivos de locale, suporte a RTL.
allowed-tools: Read, Glob, Grep
---

# i18n e Localização

> Melhores práticas de Internacionalização (i18n) e Localização (L10n).

---

## 1. Conceitos Centrais

| Termo | Significado |
| :--- | :--- |
| **i18n** | Internacionalização - tornar o aplicativo traduzível |
| **L10n** | Localização - as traduções reais |
| **Localidade (Locale)** | Idioma + Região (en-US, pt-BR) |
| **RTL** | Idiomas da direita para a esquerda (Árabe, Hebraico) |

---

## 2. Quando usar i18n

| Tipo de Projeto | i18n Necessário? |
| :--- | :--- |
| App web público | ✅ Sim |
| Produto SaaS | ✅ Sim |
| Ferramenta interna | ⚠️ Talvez |
| App para região única | ⚠️ Considere o futuro |
| Projeto pessoal | ❌ Opcional |

---

## 3. Padrões de Implementação

### React (react-i18next)

```tsx
import { useTranslation } from 'react-i18next';

function Welcome() {
  const { t } = useTranslation();
  return <h1>{t('welcome.title')}</h1>;
}
```

### Next.js (next-intl)

```tsx
import { useTranslations } from 'next-intl';

export default function Page() {
  const t = useTranslations('Home');
  return <h1>{t('title')}</h1>;
}
```

### Python (gettext)

```python
from gettext import gettext as _

print(_("Welcome to our app"))
```

---

## 4. Estrutura de Arquivos

```
locales/
├── en/
│   ├── common.json
│   ├── auth.json
│   └── errors.json
├── pt/
│   ├── common.json
│   ├── auth.json
│   └── errors.json
└── ar/          # RTL
    └── ...
```

---

## 5. Melhores Práticas

### FAÇA ✅

- Use chaves de tradução, não texto bruto.
- Separe as traduções por funcionalidade (namespaces).
- Suporte a pluralização.
- Lide com formatos de data/número por localidade.
- Planeje para RTL desde o início.
- Use o formato de mensagem ICU para strings complexas.

### NÃO FAÇA ❌

- Codificar strings diretamente nos componentes (hardcode).
- Concatenar strings traduzidas.
- Assumir o comprimento do texto (Alemão é 30% mais longo).
- Esquecer do layout RTL.
- Misturar idiomas no mesmo arquivo.

---

## 6. Problemas Comuns

| Problema | Solução |
| :--- | :--- |
| Tradução ausente | Fallback para o idioma padrão |
| Strings codificadas | Use um script de verificação (linter) |
| Formato de data | Use Intl.DateTimeFormat |
| Formato de número | Use Intl.NumberFormat |
| Pluralização | Use o formato de mensagem ICU |

---

## 7. Suporte a RTL

```css
/* Propriedades Lógicas de CSS */
.container {
  margin-inline-start: 1rem;  /* Em vez de margin-left */
  padding-inline-end: 1rem;   /* Em vez de padding-right */
}

[dir="rtl"] .icon {
  transform: scaleX(-1);
}
```

---

## 8. Checklist

Antes de lançar:

- [ ] Todas as strings visíveis para o usuário usam chaves de tradução.
- [ ] Arquivos de localidade existem para todos os idiomas suportados.
- [ ] A formatação de data/número usa a API Intl.
- [ ] O layout RTL foi testado (se aplicável).
- [ ] Idioma de fallback configurado.
- [ ] Nenhuma string codificada nos componentes.

---

## Script

| Script | Propósito | Comando |
| :--- | :--- | :--- |
| `scripts/i18n_checker.py` | Detecta strings codificadas e traduções ausentes | `python scripts/i18n_checker.py <caminho_do_projeto>` |

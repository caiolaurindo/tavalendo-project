# TaValendo

Sistema de controle de validade de produtos para comércios (mercados, farmácias, padarias, adegas, etc.), que ajuda o lojista a saber o que está perto de vencer, o que já venceu, e o que priorizar para vender antes do prazo.

##  Problema

Produtos com validade próxima ficam esquecidos em prateleiras, geram perdas financeiras e, quando vencidos, representam risco sanitário se não forem retirados a tempo. O controle manual (planilhas, memória do lojista) é falho e não escala.

##  Proposta

O TaValendo automatiza o cadastro e o monitoramento de validade de produtos, permitindo que o lojista:

1. **Cadastre o produto** no momento da entrada em estoque, exposição ou venda, por 3 formas:
   - **Manual**: digitando nome, lote e data de validade.
   - **Planilha/PDF**: importando uma planilha (CSV/XLSX) ou PDF com lista de produtos.
   - **Foto da etiqueta**: tirando uma foto do rótulo/embalagem, com extração automática da data de validade via OCR.

2. **Receba notificações e relatórios** conforme a validade se aproxima:
   - Alertas de "vencendo em breve" → sugestão de destacar/expor mais o produto, aplicar desconto, priorizar venda.
   - Alertas de "vencido" → sugestão de retirada imediata do produto.
   - Relatórios periódicos (diário/semanal) com visão geral do estoque por status de validade.

##  Funcionalidades principais (MVP)

- [ ] Cadastro manual de produto (nome, categoria, lote, data de validade, quantidade)
- [ ] Importação via planilha (CSV/XLSX) com mapeamento de colunas
- [ ] Importação via PDF (extração de tabela de produtos)
- [ ] Cadastro via foto da etiqueta (OCR + extração da data de validade)
- [ ] Motor de regras de status: `ok` / `atenção` / `crítico` / `vencido`
- [ ] Notificações (push, e-mail e/ou WhatsApp) por proximidade de vencimento
- [ ] Relatório de produtos a vencer (por período configurável)
- [ ] Relatório de produtos vencidos (para retirada)
- [ ] Dashboard com visão geral do estoque por status
- [ ] Sugestão automática de ação: "destacar na loja" x "retirar de circulação"

##  Funcionalidades futuras (pós-MVP)

- [ ] Leitura de código de barras para autocompletar dados do produto
- [ ] Integração com sistemas de PDV/ERP do cliente
- [ ] Histórico de perdas (quanto foi perdido por vencimento, em R$ e unidades)
- [ ] Sugestão de desconto progressivo conforme proximidade da validade
- [ ] Múltiplas lojas/filiais por conta
- [ ] Permissões por papel (gerente, repositor, caixa)
- [ ] App mobile nativo para cadastro rápido em loja

##  Arquitetura

```
tavalendo/
├── apps/
│   ├── api/            # Backend (NestJS)
│   └── mobile/         # App (React Native)
└── docs/                # Documentação do projeto
```

### Stack

- **Backend**: NestJS
- **Frontend/App**: React Native
- **OCR/Extração**: serviço de visão computacional (ex: Tesseract, Google Vision API ou modelo próprio) para leitura de etiquetas; parsing de planilha (XLSX/CSV) e PDF
- **Banco de dados**: PostgreSQL
- **Notificações**: fila de jobs (ex: cron/queue) para verificação diária de validade + integração com push/e-mail/WhatsApp
- **Infra**: a definir (Docker desde o início para facilitar deploy)

### Convenções de nomenclatura

**Backend (NestJS)** — segue o padrão do CLI do Nest: kebab-case + sufixo do tipo, nome singular.
```
src/
├── products/
│   ├── products.module.ts
│   ├── products.controller.ts
│   ├── products.service.ts
│   ├── dto/
│   │   ├── create-product.dto.ts
│   │   └── update-product.dto.ts
│   └── entities/
│       └── product.entity.ts
├── notifications/
│   ├── notifications.module.ts
│   └── notifications.service.ts
└── ocr/
    ├── ocr.module.ts
    └── ocr.service.ts
```

**Frontend (React Native)** — componente em PascalCase, hook/service em camelCase.
```
src/
├── screens/
│   ├── ProductListScreen.tsx
│   └── ProductFormScreen.tsx
├── components/
│   ├── ProductCard.tsx
│   └── ExpirationBadge.tsx
├── hooks/
│   └── useExpirationStatus.ts
├── services/
│   ├── api.ts
│   └── ocrService.ts
└── types/
    └── product.types.ts
```

##  Modelo de dados (rascunho inicial)

**Produto**
- id
- nome
- categoria
- lote
- data_validade
- data_cadastro
- forma_cadastro (`manual` | `planilha` | `pdf` | `foto`)
- quantidade
- status (calculado: `ok` | `atenção` | `crítico` | `vencido`)
- loja_id

**Regra de notificação**
- categoria (opcional, regra pode ser por categoria de produto)
- dias_antes_atenção
- dias_antes_crítico
- canal (push, e-mail, whatsapp)

##  Próximos passos

1. Validar o fluxo de cadastro manual (MVP mais simples) e o modelo de dados do produto.
2. Definir motor de regras de status (quantos dias antes vira "atenção", "crítico").
3. Prototipar a extração de data de validade a partir de planilha/CSV.
4. Prototipar o OCR de etiqueta (maior risco técnico do projeto — validar precisão antes de comprometer o MVP a essa funcionalidade).
5. Definir os canais de notificação a serem suportados no MVP.

---

*Projeto em fase inicial de planejamento - inicio: 15/08/2026.*

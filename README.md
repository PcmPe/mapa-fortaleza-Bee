# Monitor de Cadastros · Entregadores Fortaleza

Aplicação web single-file para análise visual de cadastros de entregadores sobre o mapa de Fortaleza/CE. Tudo roda 100% no navegador — nenhum dado sai da máquina do usuário.

![preview](docs/screenshots/preview.png)

## ✨ Funcionalidades

- **Mapa interativo** com 121 bairros oficiais de Fortaleza (fonte: IPLANFOR/2019)
- **Heat map por densidade** de cadastros com escala branco → vermelho escuro
- **6 cards de KPI** cadastrais + operacionais (total, status top, antigos, cancelamento médio, dias sem entrega, sem ativação)
- **Filtros globais**: status (multi-select), período (presets + intervalo), busca por nome/ID
- **Bairros críticos com filtro flexível**: 4 critérios (dias sem entrega, taxa cancelamento, cadastro >1 ano, sem ativação) × limiar customizável × % do bairro
- **Sidebar de bairro** com KPIs locais, chips de status, segmentação temporal, lista virtualizada de entregadores, exportação CSV
- **Painel inferior com tabela completa** dos 3 grupos:
  - Válidos no mapa (com polígono em Fortaleza)
  - Fora de Fortaleza (RMF + grafia divergente)
  - Inválidos (campos truncados, vazios, lixo)
- **Robustez de dados**:
  - Remove aspas simples do Power BI (`'INATIVO'` → `INATIVO`)
  - Match de colunas por alias + inclusão (`'bee entregadors'[id]`, `Status Atual`, `Cadastro`, etc.)
  - Match de bairros por separador (`SAPIRANGA-COITÉ` ↔ `Sapiranga / Coité`)
  - Suporta 10+ status dinâmicos com paleta estendida
- **Performance**: lista e tabela virtualizadas, aguenta 70 mil+ registros sem travar
- **Privacidade**: nenhum dado é enviado para servidor algum — todo processamento em memória no navegador do usuário

## 🚀 Como usar

1. Abra `index.html` direto no navegador (sem servidor necessário)
2. Arraste sua planilha (.xlsx ou .csv) na área central, ou clique em "Carregar planilha"
3. Ou clique em "Dados de exemplo" para testar a interface

### Colunas reconhecidas

A aplicação reconhece variações de nome de coluna automaticamente. Mínimo obrigatório:

| Campo | Variações aceitas |
|---|---|
| ID | `id`, `codigo`, `cod`, `'bee entregadors'[id]` |
| Nome | `nome`, `name`, `entregador`, `Nome do Entregador` |
| Bairro | `bairro`, `neighborhood`, `localidade` |
| Cadastro | `cadastro`, `data`, `data_cadastro`, `criado_em`, `created_at` |
| Status | `status`, `situacao`, `Status Atual` |

Colunas opcionais (enriquecem a análise se presentes): `Veículo`, `Finalizadas`, `Canceladas`, `Dias Sem Entregas`, `Data de Ativação`, `Última entrega`, `Telefone`, `Nota`, `Responsável`.

## 🏗 Arquitetura

```
index.html              # Aplicação inteira (HTML + CSS + JS + GeoJSON embutido)
├── Leaflet 1.9.4       # CDN — mapa interativo
├── SheetJS 0.18.5      # CDN — leitura de .xlsx
└── FZ_GEO              # 121 bairros embutidos (simplificados de 0.79MB → 42KB)
```

**Decisões de design**:
- **Single-file HTML**: zero build, zero dependência de pacote, copia e usa
- **GeoJSON embutido (não fetch)**: funciona offline; geometria simplificada com Douglas-Peucker para caber inline
- **Sem `localStorage`/`sessionStorage`**: tudo em memória, recarregar limpa o estado (intencional — segurança LGPD)
- **Virtualização DOM**: só renderiza linhas/cards visíveis, suporta dezenas de milhares de registros

## 📊 Tratamento de dados

A app classifica cada cadastro em 3 grupos:

| Grupo | Critério | Comportamento |
|---|---|---|
| **mapped** | Bairro casa com polígono de Fortaleza (exato ou via separador `/` ↔ `-`) | Pinta no mapa, entra nos KPIs |
| **offmap** | Bairro válido mas sem polígono (RMF, grafia divergente) | Conta no badge, listável no painel inferior |
| **invalid** | Bairro com ≤2 caracteres, vazio ou numérico | Ignorado dos KPIs, listável no painel inferior |

Match por prefixo automático foi **descartado** porque produzia erros graves (ex.: `NOVO MARACANAÚ` → `Novo Mondubim`).

## 🔒 LGPD / Privacidade

**Este repositório é privado e não contém dados reais de entregadores.** O `.gitignore` bloqueia `*.xlsx`, `*.csv` e exports automaticamente. Para usar a aplicação, o usuário carrega sua própria planilha no navegador — nenhum dado trafega pela rede.

Se for compartilhar screenshots ou exports, **anonimize antes** (remova nome real, telefone, ID).

## 🛠 Stack

- **Leaflet.js** 1.9.4 — mapa interativo
- **SheetJS** (xlsx) 0.18.5 — parser de planilhas
- **OpenStreetMap** — tiles do mapa
- **GeoJSON IPLANFOR/2019** — limites oficiais de bairros de Fortaleza (simplificados)

## 📁 Estrutura

```
.
├── index.html                  # Aplicação completa
├── README.md                   # Este arquivo
├── LICENSE                     # MIT
├── CHANGELOG.md                # Histórico de mudanças
├── .gitignore                  # Bloqueia PII e dados reais
└── docs/
    └── screenshots/            # Capturas de tela
```

## 📝 Licença

MIT — veja [LICENSE](LICENSE).

Os dados de bairros de Fortaleza embutidos são da Prefeitura de Fortaleza / IPLANFOR (2019), redistribuídos sob a licença original da fonte.

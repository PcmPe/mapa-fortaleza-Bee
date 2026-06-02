# Changelog

Todas as mudanças relevantes nesta aplicação são registradas aqui.

## [1.0.0] — 2026-05-29

### Adicionado
- Mapa interativo com 121 bairros oficiais de Fortaleza (IPLANFOR/2019)
- GeoJSON embutido e simplificado (de 0.79 MB para 42 KB com Douglas-Peucker)
- Heat map de densidade de cadastros (branco → vermelho escuro)
- 6 cards de KPI: total, top 2 status, outros, cadastro >1 ano, cancelamento médio, dias sem entrega, sem ativação
- Sidebar lateral com KPIs por bairro, chips de status, segmentação temporal, lista virtualizada
- Painel inferior com tabela virtualizada de todos os cadastros
- Seletor de escopo no painel: Válidos no mapa / Fora de Fortaleza / Inválidos
- Badge laranja clicável que abre o painel no escopo correspondente
- Filtro de bairros críticos flexível com 4 critérios (dias sem entrega, cancelamento, cadastro antigo, sem ativação)
- Busca global por nome ou ID com centralização automática no mapa
- Filtro de período (presets + intervalo personalizado)
- Botão "Selecionar/Limpar todos" no multi-select de status
- Exportação CSV por bairro e por tabela inteira
- Dataset de exemplo embutido para teste sem planilha real
- Drag-and-drop de planilhas .xlsx e .csv

### Tratamento de dados
- Remove aspas simples do Power BI em strings (`'INATIVO'` → `INATIVO`)
- Match de colunas por alias e inclusão (suporta `'bee entregadors'[id]`, `Status Atual`, `Cadastro`, etc.)
- Match de bairros por separador `/` ↔ `-` (`SAPIRANGA-COITÉ` → `Sapiranga / Coité`)
- Classificação em 3 grupos: mapped, offmap, invalid
- Cadastros inválidos (campos truncados/vazios) ignorados dos KPIs mas acessíveis no painel inferior

### Performance
- Virtualização DOM na lista da sidebar e na tabela do painel inferior
- Testado com 29 mil registros reais sem travamento
- 5 ordenações completas de 29k em ~86ms

### Privacidade
- Nenhum dado sai do navegador do usuário
- Sem `localStorage` ou `sessionStorage` — estado é zerado ao recarregar
- `.gitignore` bloqueia commit acidental de planilhas e exports

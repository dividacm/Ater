# ATER V20 — Anonimizador de Documentos PDF

> Ferramenta client-side para anonimização segura de documentos PDF, com suporte a detecção automática de dados pessoais, marcação manual, workflow de revisão em dupla e exportação sem perda de qualidade.

---

## Funcionalidades

- **Detecção automática** de CPF, RG, CEP, telefone, endereços e nomes via regex
- **Busca customizada** para tarjar qualquer termo específico
- **Marcação manual** com ferramenta de desenho livre
- **Desfazer** (`Ctrl+Z`) para remover a última tarja criada
- **Workflow de revisão em dupla** — salva e importa projetos `.ater`
- **Exportação segura via pdf-lib** — retângulos opacos diretamente na estrutura do PDF, sem conversão para JPEG
- **Dicionário de exceções** configurável por usuário
- **Sumário de revisão** lateral com todas as marcações agrupadas por página
- **4 temas visuais** — Escuro, Claro, Sépia, Alto Contraste
- **Atalhos de teclado** completos
- **100% client-side** — nenhum dado sai do navegador

---

## Como usar

Por ser um único arquivo HTML autocontido, não há instalação:

1. Baixe o arquivo `index.html`
2. Abra no navegador (Chrome ou Edge recomendados)
3. Arraste um PDF ou clique em **Abrir PDF**

---

## Workflow de Revisão em Dupla

```
Analista                          Revisor
   │                                 │
   ├─ Abre o PDF original            │
   ├─ Revisa as tarjas automáticas   │
   ├─ Clica em "Salvar Projeto"      │
   │   └─ Gera arquivo .ater         │
   │                                 │
   ├─ Envia: PDF original + .ater ──►│
   │                                 ├─ Abre o PDF original
   │                                 ├─ Importa o .ater
   │                                 ├─ Conferência das marcações
   │                                 └─ Clica em "Exportar PDF"
   │                                     └─ PDF anonimizado final
```

> **Segurança do arquivo `.ater`:** o arquivo de projeto contém apenas coordenadas e metadados das tarjas. Os dados sensíveis detectados (CPF, nomes, etc.) **não são gravados** no `.ater`.

---

## Padrões Detectados Automaticamente

| Padrão | Exemplo |
|---|---|
| CPF | `123.456.789-00` |
| RG | `12.345.678-9` |
| CEP | `87301-005` |
| Telefone | `(44) 99999-9999` |
| Endereço | `Rua das Flores, 123` |
| Nomes compostos | `João da Silva Santos` |
| Nomes em maiúsculas | `MARIA DE SOUZA` |

Palavras institucionais comuns (SECRETARIA, PREFEITURA, DECRETO etc.) são ignoradas automaticamente e podem ser configuradas no **Dicionário de Exceções**.

---

## Atalhos de Teclado

| Atalho | Ação |
|---|---|
| `Ctrl + O` | Abrir PDF |
| `Ctrl + S` | Salvar projeto `.ater` |
| `Ctrl + E` | Exportar PDF anonimizado |
| `Ctrl + Z` | Desfazer última tarja |
| `Ctrl + Del` | Limpar todas as tarjas |
| `V` | Modo Visualização |
| `D` | Modo Desenho |
| `+` / `-` | Zoom in / Zoom out |
| `Esc` | Fechar modal / Cancelar |

---

## Detalhes Técnicos

**Dependências externas (via CDN, sem instalação):**

| Biblioteca | Versão | Uso |
|---|---|---|
| [PDF.js](https://mozilla.github.io/pdf.js/) | 3.11.174 | Renderização e extração de texto do PDF |
| [pdf-lib](https://pdf-lib.js.org/) | 1.17.1 | Exportação com retângulos opacos na estrutura do PDF |

**Por que pdf-lib na exportação?**

Versões anteriores exportavam o documento convertendo cada página para JPEG, o que:
- Destruía o texto não-tarjado (documento deixava de ser pesquisável)
- Podia introduzir artefatos de compressão visíveis sobre as tarjas

A V20 usa `pdf-lib` para desenhar retângulos `rgb(0,0,0)` diretamente na estrutura vetorial do PDF, preservando o texto original onde não há tarjas.

**Privacidade:**
- Nenhuma requisição de rede é feita com o conteúdo do documento
- O PDF é processado inteiramente na memória do navegador
- O arquivo `.ater` exportado não contém os dados sensíveis detectados

---

## Estrutura do Arquivo `.ater`

O projeto é salvo como JSON com a seguinte estrutura por tarja:

```json
[
  {
    "id": "uuid-gerado",
    "type": "regex",
    "pagina": 1,
    "hora": "14:32:10",
    "nx": 120.5,
    "ny": 340.2,
    "nw": 85.0,
    "nh": 14.0
  }
]
```

- `nx/ny/nw/nh` — coordenadas normalizadas (independentes de zoom), em pontos do PDF
- `type` — `regex` (automático), `custom` (busca manual) ou `manual` (desenhado)
- O campo `termo` (texto detectado) é **omitido** intencionalmente por segurança

---

## Limitações Conhecidas

- O texto *sob* as tarjas permanece na estrutura interna do PDF (camada de texto). Para remoção completa seria necessário processamento server-side com redaction. Para a maioria dos casos de uso institucional, a cobertura visual opaca é suficiente.
- PDFs protegidos por senha não são suportados.
- PDFs baseados em imagem escaneada (sem camada de texto) requerem marcação manual — a detecção automática não funcionará.
- Recomendado Chrome 90+ ou Edge 90+. Firefox funciona com limitações de performance em documentos grandes.

---

## Contribuindo

Contribuições são bem-vindas. Abra uma issue descrevendo o problema ou a melhoria antes de submeter um PR.

Áreas prioritárias:
- Suporte a touch/mobile para marcação manual
- Suporte a múltiplos arquivos em batch
- Exportação com redaction real (remoção da camada de texto)
- Testes automatizados para os padrões de regex

---

## Licença

MIT — livre para uso, modificação e distribuição.

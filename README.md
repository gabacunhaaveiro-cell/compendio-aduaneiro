# Compêndio Aduaneiro — Seal Service

Base de conhecimento estruturada do Manual de Importação, organizada em arquivos Markdown indexados, prontos para serem consumidos por aplicações de IA (como o Monitor Aduaneiro).

## O que tem aqui

```
compendio/
├── README.md              ← este arquivo
├── latest.json            ← ponteiro do snapshot atual (data, versão)
├── index.json             ← índice de busca: lista todos os subtemas com tags
├── legislacoes.json       ← catálogo único de normas citadas
└── manuais/
    └── importacao/
        ├── procedimentos-de-importacao/
        │   ├── 1-1-fluxo-geral-da-operacao.md
        │   ├── 1-2-licenciamento-de-importacao-li.md
        │   └── ...
        ├── despacho-de-importacao/
        │   └── ...
        ├── tributos-incidentes-na-importacao/
        │   └── ...
        └── (demais partes)
```

## Como o conteúdo está organizado

Cada arquivo `.md` representa **um subtema** do manual (ex.: "1.2 Imposto de Importação"). O cabeçalho YAML no topo de cada arquivo carrega os metadados:

| Campo | Significado |
|-------|-------------|
| `manual` | Manual de origem (atualmente `importacao`) |
| `parte` | Parte do manual (ex.: PARTE I — Tributos) |
| `subtema` | Título do subtema |
| `tags` | Etiquetas para busca rápida |
| `palavras_chave` | Termos para indexação |
| `normas_citadas` | IDs das leis/decretos referenciados no texto |
| `fonte` | Arquivo .docx de origem |
| `snapshot` | Data da extração |

## Snapshot atual

Veja `latest.json`. A data e versão atual estão lá.

Cada commit no GitHub é um snapshot implícito — o histórico de versões fica preservado automaticamente.

## Como atualizar

Quando uma nova versão do compêndio for gerada (a partir de docx atualizado), basta substituir os arquivos pela nova versão usando o botão **Add file → Upload files** no GitHub e fazer commit. O `latest.json` será atualizado junto e o Monitor Aduaneiro passa a consumir automaticamente o conteúdo novo na próxima abertura.

## Como o Monitor Aduaneiro consome este repositório

O Monitor lê os arquivos via `raw.githubusercontent.com`, que aceita CORS aberto:

```
https://raw.githubusercontent.com/gabacunhaaveiro-cell/compendio-aduaneiro/main/latest.json
https://raw.githubusercontent.com/gabacunhaaveiro-cell/compendio-aduaneiro/main/index.json
https://raw.githubusercontent.com/gabacunhaaveiro-cell/compendio-aduaneiro/main/manuais/importacao/.../subtema.md
```

Fluxo de uma consulta no Monitor:

1. Usuário digita pergunta no campo "Consulta IA — Manual Aduaneiro";
2. Monitor já carregou o `index.json` (faz isso na inicialização);
3. JS local busca os 5 subtemas mais relevantes (por tags + palavras-chave + texto da pergunta);
4. Monitor baixa os arquivos `.md` desses subtemas via `raw.githubusercontent.com`;
5. Conteúdo é enviado ao Claude como contexto;
6. Claude responde com base **exclusivamente** no compêndio, citando subtemas e normas.

## Observações importantes

- **Extração automática**: O conteúdo foi extraído de arquivos `.docx`. As referências a leis e decretos foram identificadas via padrões textuais (regex) e podem conter ruído. Cada arquivo tem `revisar: true` no cabeçalho — significa que ainda não passou por revisão humana.
- **URLs das normas**: O arquivo `legislacoes.json` lista as normas citadas, mas o campo `url` está vazio até que cada norma seja revisada e tenha sua URL canônica preenchida.
- **Tabelas**: Foram convertidas para Markdown nativo. Algumas podem ter perdido formatação visual mas mantêm o conteúdo.

## Próximas etapas previstas

1. Adicionar manuais de Exportação, DUIMP, Drawback e Trânsito (extração a partir das páginas oficiais da Receita).
2. Preencher URLs das normas no `legislacoes.json`.
3. Revisar manualmente os subtemas marcados `revisar: true`.
4. Adicionar campo `pergunta_tipica` no frontmatter para melhorar a recuperação por IA.

---

**Mantido por:** Gabriel Cunha — Seal Service Comissária de Despacho e Consultoria Aduaneira

# mosaic

> gerador client-side de grid de thumbnails estilo roblox

<p>
  <a href="https://dgolaus.github.io/mosaic/">
    <img src="https://img.shields.io/badge/live-demo-ef4444?style=for-the-badge&labelColor=000000" alt="live demo" />
  </a>
  <a href="https://github.com/dgolaus/mosaic">
    <img src="https://img.shields.io/badge/source-github-0a0a0a?style=for-the-badge&logo=github&logoColor=white" alt="source" />
  </a>
  <a href="https://github.com/dgolaus/mosaic/issues/new">
    <img src="https://img.shields.io/badge/feedback-abrir%20issue-ef4444?style=for-the-badge&labelColor=000000" alt="feedback" />
  </a>
</p>

monta grid de thumbs (2×2 até 4×3), ajusta gap, proporção e filtros, e exporta em png de alta resolução (até 4k). tudo client-side, sem backend, sem upload.

html/css/js puro · zero dependências · zero build · roda no `file://`.

---

## features

- **5 formatos**: `2×2` · `3×2` · `4×2` · `3×3` · `4×3`
- **3 proporções**: `16:9` · `1:1` · `4:5`
- **upload flexível**: drag & drop, paste (`ctrl+v`), clique, ou drop em slot específico
- **reordenação** arrastando entre slots — insere e desloca (não troca)
- **filtros**: saturação, contraste, vinheta — aplicados no preview e no png exportado
- **auto-ordenar**: heurística que pontua cada thumb por engajamento e reordena melhor → pior
- **presets**: salva combinações de configuração (formato + proporção + gap + filtros)
- **undo/redo**: `ctrl+z` / `ctrl+shift+z` — pilha de 40 snapshots
- **cache de imagens**: trocou pra grid menor? as extras ficam guardadas — voltam ao aumentar de novo
- **compartilhar**: copia link com `#cfg=...` (só configuração, sem imagens)
- **export png**: 1080p / 2k / 4k, com gap e proporção escalados corretamente
- **persistência completa**: indexeddb — recarregou? state inteiro volta
- **smooth scroll**: lerp 0.10 buttery (só ativa no mobile / quando precisa rolar)
- **layout viewport-único**: tudo cabe na tela — controles à esquerda, preview à direita, ações no rodapé

## rodar local

```bash
python3 -m http.server 8000
# ou
npx serve .
```

abrir `index.html` direto no navegador também funciona — não tem build, não tem deps.

## atalhos

| tecla | ação |
|---|---|
| `ctrl + v` | cola imagem do clipboard (print, copy/paste) |
| `ctrl + z` | desfaz |
| `ctrl + shift + z` / `ctrl + y` | refaz |
| arrastar slot → slot | reordena (insere + desloca) |
| clicar slot | abre seletor de arquivo |
| `×` no slot (hover) | remove imagem |

## auto-ordenar — como pontua

heurística pura, client-side, sem modelo de ml (sem download, sem api):

| sinal | peso | o que mede |
|---|---|---|
| saturação média | 30% | quão vibrante é a thumb |
| contraste | 25% | desvio padrão da luminância |
| foco central | 15% | saturação concentrada no centro vs bordas |
| variedade de cor | 15% | cores quantizadas únicas (8×8×8 buckets) |
| equilíbrio de brilho | 15% | proximidade do sweet-spot `0.55` |

cada thumb é downsampled pra 128×128 antes de processar. ordenação descendente. roda em ~50ms pra 12 imagens. score final = `0.30·sat + 0.25·contraste + 0.15·foco + 0.15·variedade + 0.15·brilho`.

## persistência (indexeddb)

| chave | conteúdo |
|---|---|
| `state` | settings + dataurls das imagens carregadas |
| `presets` | array de presets salvos pelo usuário |

debounce de 250ms — toda mudança de estado dispara save automático. recarregou a página? volta exatamente onde estava.

## share link

botão `compartilhar` gera URL no formato:

```
https://dgolaus.github.io/mosaic/#cfg=eyJjIjozLC...
```

base64 do json compacto (`c`/`r`/`rw`/`rh`/`g`/`rd`/`f`/`e`). só configuração — imagens **não** vão no link (seriam MB de base64). abre o link → setting aplicado, manda foto na hora.

## export png

| res | largura final |
|---|---|
| 1080p | 1920 px |
| 2k | 2560 px |
| 4k | 3840 px |

`cellW` é calculado pra largura total bater exatamente com o target, respeitando proporção (`ratioH/ratioW`) e gap escalado proporcionalmente do preview.

## estrutura

```
mosaic/
├── index.html    tudo num arquivo só (html + css + js)
└── README.md     este aqui
```

## stack

- [jetbrains mono](https://fonts.google.com/specimen/JetBrains+Mono) (google fonts)
- `indexeddb` pra persistência (state + presets)
- `canvas api` nativo pro export — sem html2canvas, sem libs
- smooth scroll com lerp `0.10`, copiado direto de [s0da-portfolio](https://github.com/dgolaus/s0da-portfolio)

---

made by [@gfxs0da](https://github.com/dgolaus)

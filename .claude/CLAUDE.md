# Agente: Gerador de Apresentação UAM Grupo A

## Objetivo
Ler a apresentação existente em `~/Documentos/UAM_Apresentacao/`, analisar o site UAM Grupo A de ponta a ponta, e gerar uma apresentação PowerPoint (.pptx) profissional de 60 minutos para um seminário no ITA com público misto (empresas + meio acadêmico). A apresentação existente deve ser usada como base — aproveitar sua estrutura, comentários e conteúdo já elaborado, melhorando e expandindo com o conteúdo real do site.

---

## Caminhos importantes

- **Site UAM:** `~/Documentos/GITHUB/IT_214_Projeto/uam-grupoA/`
- **Pasta da apresentação:** `~/Documentos/UAM_Apresentacao/`
- **Arquivo existente:** `~/Documentos/UAM_Apresentacao/Otimiza__o_de_apresenta__o_...pptx` (nome exato — checar com `ls`)
- **Saída final:** `~/Documentos/UAM_Apresentacao/UAM_GrupoA_Seminario_ITA.pptx`

---

## Passo 1 — Ler a apresentação existente

```bash
# Ver nome exato do arquivo
ls ~/Documentos/UAM_Apresentacao/

# Extrair todo o conteúdo textual da apresentação existente
extract-text ~/Documentos/UAM_Apresentacao/*.pptx

# Ver miniaturas visuais para entender o layout atual
python scripts/thumbnail.py ~/Documentos/UAM_Apresentacao/*.pptx
```

**Registrar mentalmente:**
- Estrutura de slides já existente (ordem, títulos, seções)
- Comentários dentro dos slides — são instruções importantes de conteúdo
- Dados numéricos, gráficos e textos já presentes
- Estilo visual adotado (paleta de cores, fontes, layouts)
- O que está completo vs. o que está incompleto ou como placeholder

---

## Passo 2 — Ler o site de ponta a ponta

```bash
cd ~/Documentos/GITHUB/IT_214_Projeto/uam-grupoA/

# Estrutura completa do projeto
find . -type f \( -name "*.html" -o -name "*.css" -o -name "*.js" \) | sort

# Ler todos os HTMLs em sequência
for f in $(find . -name "*.html" | sort); do
  echo "=== $f ==="; cat "$f"; echo
done

# Listar imagens disponíveis
find . -path "*/img/*" -o -path "*/assets/img/*" | sort

# Ler arquivos JS do projeto (excluindo libs)
for f in $(find . -name "*.js" \
  -not -path "*/node_modules/*" \
  -not -path "*/cdn*" | sort); do
  echo "=== $f ==="; cat "$f"; echo
done
```

**Extrair e registrar:**
- Título, subtítulo e contexto geral do projeto
- Navegação: todas as seções e subseções do menu
- Conteúdo de cada página: textos, listas, tabelas, dados numéricos
- Dados estatísticos: R², correlações, áreas, dimensões, scores AHP, VMC/IMC
- Gráficos Chart.js: labels, datasets, values
- Imagens disponíveis em `img/` e `assets/img/` para usar nos slides
- Autores, orientador, disciplina, data de entrega

---

## Passo 3 — Planejar os slides

Com base na apresentação existente + conteúdo do site, monte ~25 slides para 60 min (~2,5 min/slide):

| Bloco | Slides | Tempo | Conteúdo-chave do site |
|-------|--------|-------|------------------------|
| Abertura | 1–2 | 4 min | Título, equipe, contexto IT-214/ITA |
| Problema e motivação | 3–5 | 8 min | UAM no Brasil, por que agora, dados SARPAS |
| Seleção da cidade | 6–8 | 8 min | Ranking SP/RJ/Brasília, critérios, SARPAS 800k ops |
| Caracterização de Brasília | 9–10 | 5 min | PIB per capita #1, 3,1M hab, 40–60min rodovia→aeroporto |
| Seleção do vertiporto | 11–12 | 5 min | AHP: Rodoviária 0,844 vs Complexo21 0,802 vs Aeroporto 0,607 |
| Análise de demanda | 13–15 | 8 min | 87 obs, Modelo 4 (HELIPT+PAND), R²=0,2049, 574 mov/mês |
| Análise meteorológica | 16–17 | 5 min | Rosa dos ventos E=35%, VMC>99% julho, IMC<1% meses secos |
| Pré-dimensionamento | 18–20 | 8 min | BETA ALIA VTOL D=15,24m, TLOF/FATO/OLS 972m, TPS 216m² |
| Implementação | 21–23 | 8 min | Hangar 529m², terminal, roadmap 4 fases |
| Conclusão e Q&A | 24–25 | 7 min | Síntese, próximos passos, agradecimentos |

**Regras de conteúdo:**
- Não inventar dados — usar apenas o que está no site e na apresentação existente
- Se uma seção do site estiver vazia, indicar no speaker note: "ATENÇÃO: conteúdo incompleto — revisar antes do seminário"
- Aproveitar os comentários da apresentação existente como roteiro de fala
- Usar imagens reais do site quando disponíveis (`img/hero_banner.jpg`, `img/Meteorologia.png`, etc.)

---

## Passo 4 — Instalar dependências

```bash
cd ~/Documentos/UAM_Apresentacao/
npm install pptxgenjs 2>/dev/null || npm install -g pptxgenjs
node -e "require('pptxgenjs'); console.log('pptxgenjs OK')"
pip install Pillow --break-system-packages 2>/dev/null || true
```

---

## Passo 5 — Gerar o arquivo gerar_slides.js

Crie `~/Documentos/UAM_Apresentacao/gerar_slides.js` com estas especificações:

### Paleta de cores (tema UAM / Aeronáutica)

```
Fundo escuro (título/conclusão): 21295C  (azul meia-noite)
Fundo claro (conteúdo):          FFFFFF
Acento principal:                065A82  (azul oceano)
Acento secundário:               1C7293  (teal)
Texto principal:                 1E2761  (navy)
Texto secundário:                64748B  (cinza)
Destaque (callouts):             7EC8E3  (azul claro)
```

### Regras de design obrigatórias

1. Slides de título e conclusão: fundo `21295C`, texto branco
2. Slides de conteúdo: fundo `FFFFFF`, texto escuro
3. **Estrutura "sanduíche"**: abertura escura → conteúdo claro → conclusão escura
4. Cada slide com ao menos um elemento visual (shape, imagem, dado em destaque ou gráfico)
5. **NUNCA** usar linhas de acento sob títulos
6. **NUNCA** usar barras decorativas nas bordas dos slides
7. Layouts variados: dois-colunas, cards 2×2, callout numérico grande, timeline
8. Speaker notes em **cada slide** com roteiro de fala em português (2–4 frases naturais)
9. Fontes seguras: **Cambria** para títulos, **Calibri** para corpo
10. Tamanhos: título 36–40pt, seção 22pt, corpo 14–16pt, captions 10–12pt

### Helper functions obrigatórias

```javascript
const pptxgen = require("pptxgenjs");

function makeShadow() {
  return { type: "outer", color: "000000", blur: 6, offset: 2, angle: 45, opacity: 0.12 };
}

function titleBar(slide, pres, text) {
  slide.addShape(pres.ShapeType.rect, {
    x: 0, y: 0, w: 10, h: 0.75,
    fill: { color: "21295C" }, line: { type: "none" }
  });
  slide.addText(text, {
    x: 0.4, y: 0, w: 9.2, h: 0.75,
    fontSize: 22, fontFace: "Cambria", color: "FFFFFF",
    bold: true, valign: "middle", margin: 0
  });
}

function card(slide, pres, x, y, w, h, color) {
  slide.addShape(pres.ShapeType.roundRect, {
    x, y, w, h,
    fill: { color: color || "F0F6FF" },
    line: { type: "none" },
    rectRadius: 0.08,
    shadow: makeShadow()
  });
}

async function gerarApresentacao() {
  const pres = new pptxgen();
  pres.layout = "LAYOUT_16x9";
  pres.author = "UAM Grupo A — IT-214 / ITA";
  pres.title = "Urban Air Mobility — Vertiporto Brasília";

  // [SLIDES AQUI]

  await pres.writeFile({ fileName: "UAM_GrupoA_Seminario_ITA.pptx" });
  console.log("✅ Apresentação gerada: UAM_GrupoA_Seminario_ITA.pptx");
}

gerarApresentacao().catch(console.error);
```

### Templates de slides obrigatórios

**Slide de título (escuro):**
```javascript
const s = pres.addSlide();
s.background = { color: "21295C" };
s.addText("URBAN AIR MOBILITY · IT-214 · ITA · 2026", {
  x: 0.8, y: 1.0, w: 8.4, h: 0.5,
  fontSize: 12, fontFace: "Calibri", color: "7EC8E3",
  bold: true, charSpacing: 4, align: "center", margin: 0
});
s.addText("Do Ranking Nacional ao\nPrimeiro Vertiporto de Brasília", {
  x: 0.8, y: 1.6, w: 8.4, h: 1.8,
  fontSize: 38, fontFace: "Cambria", color: "FFFFFF",
  bold: true, align: "center", margin: 0
});
s.addText("Estudo de Viabilidade e Implantação de UAM · Estudo Preliminar Rev. 0", {
  x: 0.8, y: 3.5, w: 8.4, h: 0.5,
  fontSize: 14, fontFace: "Calibri", color: "CADCFC",
  align: "center", margin: 0
});
s.addText("Caio Honorato · Antonio Eduardo de Vasconcelos · Giancarlo Furtado · Rafael Almeida\nOrientador: Prof. Dr. Marcelo Xavier Guterres", {
  x: 0.8, y: 4.2, w: 8.4, h: 0.7,
  fontSize: 12, fontFace: "Calibri", color: "7EC8E3",
  align: "center", margin: 0
});
s.addNotes("Boas-vindas ao público. Apresentar o grupo e o contexto da disciplina IT-214. Mencionar que o projeto cobre o ciclo completo: da seleção da cidade ao anteprojeto do vertiporto.");
```

**Slide com callout numérico grande:**
```javascript
const s = pres.addSlide();
s.background = { color: "FFFFFF" };
titleBar(s, pres, "TÍTULO DO SLIDE");
s.addText("NÚMERO", {
  x: 3.5, y: 1.2, w: 3, h: 1.5,
  fontSize: 72, fontFace: "Cambria", color: "065A82",
  bold: true, align: "center", margin: 0
});
s.addText("unidade ou label", {
  x: 3, y: 2.8, w: 4, h: 0.4,
  fontSize: 14, fontFace: "Calibri", color: "64748B",
  align: "center", margin: 0
});
s.addText("Texto explicativo", {
  x: 1, y: 3.4, w: 8, h: 1.2,
  fontSize: 14, fontFace: "Calibri", color: "1E2761",
  align: "center", margin: 0
});
s.addNotes("Roteiro de fala");
```

**Slide dois cards lado a lado:**
```javascript
const s = pres.addSlide();
s.background = { color: "FFFFFF" };
titleBar(s, pres, "TÍTULO");
card(s, pres, 0.4, 0.95, 4.4, 3.8);
s.addText("Conteúdo esquerdo", {
  x: 0.6, y: 1.1, w: 4.0, h: 3.5,
  fontSize: 14, fontFace: "Calibri", color: "1E2761",
  valign: "top", margin: 0
});
card(s, pres, 5.1, 0.95, 4.4, 3.8);
s.addText("Conteúdo direito", {
  x: 5.3, y: 1.1, w: 4.0, h: 3.5,
  fontSize: 14, fontFace: "Calibri", color: "1E2761",
  valign: "top", margin: 0
});
s.addNotes("Roteiro de fala");
```

**Slide com imagem do site:**
```javascript
const s = pres.addSlide();
s.background = { color: "FFFFFF" };
titleBar(s, pres, "TÍTULO");
// Usar imagens reais do site — ajustar o path conforme disponível
s.addImage({
  path: "~/Documentos/GITHUB/IT_214_Projeto/uam-grupoA/img/hero_banner.jpg",
  x: 5.2, y: 0.85, w: 4.6, h: 4.2
});
s.addText("Texto explicativo à esquerda", {
  x: 0.4, y: 0.85, w: 4.6, h: 4.2,
  fontSize: 14, fontFace: "Calibri", color: "1E2761",
  valign: "top", margin: 0
});
s.addNotes("Roteiro de fala");
```

---

## Passo 6 — Executar

```bash
cd ~/Documentos/UAM_Apresentacao/
node gerar_slides.js
```

---

## Passo 7 — QA visual obrigatório

```bash
# Converter para imagens
python scripts/office/soffice.py --headless --convert-to pdf UAM_GrupoA_Seminario_ITA.pptx
rm -f slide-*.jpg
pdftoppm -jpeg -r 150 UAM_GrupoA_Seminario_ITA.pdf slide
ls -1 "$PWD"/slide-*.jpg

# Verificar conteúdo textual
extract-text UAM_GrupoA_Seminario_ITA.pptx

# Checar placeholders não preenchidos
extract-text UAM_GrupoA_Seminario_ITA.pptx | grep -iE "\[|\]|TODO|lorem|ipsum|INSERIR|placeholder"
```

**Se encontrar `[`, `]`, `TODO` ou `lorem` → voltar e preencher com conteúdo real.**

**Prompt para inspeção visual (usar subagente):**
```
Visually inspect these slides for user-visible defects.

Look for:
- Overlapping elements (text through shapes, lines through words)
- Text overflow or cut off at edges/box boundaries
- Elements too close (< 0.3" gaps)
- Low-contrast text
- Leftover placeholder content (brackets, TODO, lorem)
- Dark text on dark background or light text on light background

For each slide, list user-visible issues only. Skip sub-pixel cosmetics.

Read and analyze these images — run `ls -1 "$PWD"/slide-*.jpg` and use the exact absolute paths:
```

---

## Passo 8 — Entregar

```bash
echo "✅ Apresentação pronta em ~/Documentos/UAM_Apresentacao/UAM_GrupoA_Seminario_ITA.pptx"
```

---

## Regras gerais

- **Não inventar dados** — usar apenas o que está no site e na apresentação existente
- **Aproveitar comentários** da apresentação existente como guia de conteúdo e fala
- **Usar imagens reais** do site sempre que disponíveis e relevantes
- **Speaker notes em português** com roteiro de fala natural para 2–4 frases por slide
- **Todos os textos nos slides em português**, salvo termos técnicos em inglês já usados no site (UAM, eVTOL, TLOF, FATO, AHP, VMC, IMC, OLS)
- **NUNCA usar `#` em hex colors** no pptxgenjs
- **NUNCA reutilizar objetos de opções** entre múltiplos `addShape`/`addText` — sempre usar factory functions como `makeShadow()`
- **Executar QA visual** pelo menos uma vez antes de declarar sucesso
- **Tempo total: 60 minutos** — ~25 slides, ~2,5 min cada
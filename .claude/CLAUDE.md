# Agente: Gerador de Apresentação UAM Grupo A

## Objetivo
Ler automaticamente os arquivos do site UAM Grupo A (HTML, CSS, JS) no diretório atual, extrair o conteúdo relevante e gerar uma apresentação PowerPoint (.pptx) profissional de 60 minutos para um seminário com público misto (empresas + meio acadêmico).

---

## Instruções de Execução

### Passo 1 — Explorar o projeto do site

```bash
# Listar estrutura do projeto
find . -type f \( -name "*.html" -o -name "*.css" -o -name "*.js" \) | sort

# Ver o index principal
cat index.html

# Ver todos os HTMLs
for f in $(find . -name "*.html" | sort); do echo "=== $f ==="; cat "$f"; echo; done

# Ver arquivos JS relevantes (excluindo node_modules e libs externas)
for f in $(find . -name "*.js" -not -path "*/node_modules/*" -not -path "*/lib/*" -not -path "*/vendor/*" | sort); do echo "=== $f ==="; cat "$f"; echo; done
```

**Extraia e registre mentalmente:**
- Título do projeto e subtítulo
- Seções principais do site (menu de navegação, headings H1/H2/H3)
- Conteúdo de cada seção: textos descritivos, listas, tabelas, dados numéricos, gráficos (Chart.js configs com labels e values)
- Imagens referenciadas (`src` de `<img>`) — liste os caminhos
- Qualquer dado quantitativo: métricas, percentuais, comparações
- Integrantes da equipe / afiliação institucional

---

### Passo 2 — Planejar a estrutura dos slides

Com base no conteúdo extraído, monte uma estrutura de ~25 slides para 60 min (~2,5 min/slide), seguindo este template:

| Bloco | Slides | Tempo |
|-------|--------|-------|
| Abertura e contexto | 1–3 | 5 min |
| Problema / Motivação | 4–6 | 8 min |
| Metodologia / Abordagem | 7–11 | 12 min |
| Resultados e análises | 12–18 | 15 min |
| Discussão e implicações | 19–22 | 10 min |
| Conclusões e próximos passos | 23–24 | 7 min |
| Agradecimentos / Q&A | 25 | 3 min |

Adapte o número de slides ao conteúdo real do site. Não invente dados — use apenas o que está nos arquivos.

---

### Passo 3 — Instalar dependências

```bash
npm install -g pptxgenjs react react-dom react-icons sharp 2>/dev/null || true
node -e "require('pptxgenjs'); console.log('pptxgenjs OK')"
```

---

### Passo 4 — Gerar o arquivo gerar_slides.js

Crie um arquivo `gerar_slides.js` com o seguinte padrão:

**Paleta de cores (tema UAM / Aeronáutica):**
- Fundo escuro nos slides de título e conclusão: `21295C` (azul meia-noite)
- Fundo claro nos slides de conteúdo: `FFFFFF`
- Acento principal: `065A82` (azul oceano)
- Acento secundário: `1C7293` (teal)
- Texto principal: `1E2761` (navy)
- Texto secundário: `64748B` (cinza)

**Regras de design obrigatórias:**
1. Slides de título e conclusão: fundo escuro `21295C`, texto branco
2. Slides de conteúdo: fundo branco, texto escuro
3. Cada slide deve ter ao menos um elemento visual (shape, chart, ícone ou dado em destaque numérico)
4. Nunca usar linhas de acento sob títulos
5. Nunca usar barras decorativas nas bordas
6. Layout variado: dois-colunas, cards 2x2, callout de número grande, timeline
7. Speaker notes em cada slide com roteiro de fala em português (2–4 frases)
8. Fontes seguras: Cambria para títulos, Calibri para corpo
9. Tamanhos: título 36–40pt, seção 22pt, corpo 14–16pt

**Estrutura do script:**

```javascript
const pptxgen = require("pptxgenjs");
const React = require("react");
const ReactDOMServer = require("react-dom/server");
const sharp = require("sharp");

// ─── Helpers ────────────────────────────────────────────────────────────────

function makeShadow() {
  return { type: "outer", color: "000000", blur: 6, offset: 2, angle: 45, opacity: 0.12 };
}

async function iconToBase64Png(IconComponent, color = "#065A82", size = 256) {
  const { [IconComponent]: Icon } = require(`react-icons/fa`);
  const svg = ReactDOMServer.renderToStaticMarkup(
    React.createElement(Icon, { color, size: String(size) })
  );
  const buf = await sharp(Buffer.from(svg)).png().toBuffer();
  return "image/png;base64," + buf.toString("base64");
}

// ─── Apresentação ───────────────────────────────────────────────────────────

async function gerarApresentacao() {
  const pres = new pptxgen();
  pres.layout = "LAYOUT_16x9";
  pres.author = "UAM Grupo A — IT-214 / ITA";
  pres.title = "Urban Air Mobility — UAM Grupo A";

  // [INSERIR SLIDES AQUI com base no conteúdo real extraído do site]
  // Use o template abaixo para cada slide

  await pres.writeFile({ fileName: "UAM_GrupoA_Seminario.pptx" });
  console.log("✅ Apresentação gerada: UAM_GrupoA_Seminario.pptx");
}

gerarApresentacao().catch(console.error);
```

**Template de slide de título (slide 1):**
```javascript
const s1 = pres.addSlide();
s1.background = { color: "21295C" };
s1.addText("URBAN AIR MOBILITY", {
  x: 0.8, y: 1.2, w: 8.4, h: 0.7,
  fontSize: 14, fontFace: "Calibri", color: "7EC8E3",
  bold: true, charSpacing: 6, align: "center", margin: 0
});
s1.addText("[TÍTULO PRINCIPAL DO PROJETO]", {
  x: 0.8, y: 1.9, w: 8.4, h: 1.4,
  fontSize: 38, fontFace: "Cambria", color: "FFFFFF",
  bold: true, align: "center", margin: 0
});
s1.addText("[Subtítulo ou linha descritiva]", {
  x: 0.8, y: 3.4, w: 8.4, h: 0.5,
  fontSize: 16, fontFace: "Calibri", color: "CADCFC",
  align: "center", margin: 0
});
s1.addText("Seminário IT-214 · ITA · [Ano]", {
  x: 0.8, y: 4.5, w: 8.4, h: 0.4,
  fontSize: 13, fontFace: "Calibri", color: "7EC8E3",
  align: "center", margin: 0
});
s1.addNotes("Boas-vindas ao público. Apresentar brevemente o grupo e o contexto do projeto dentro da disciplina IT-214. Mencionar a parceria com o ITA.");
```

**Template de slide de conteúdo com dois cards:**
```javascript
const sN = pres.addSlide();
sN.background = { color: "FFFFFF" };
// Barra de título
sN.addShape(pres.shapes.RECTANGLE, {
  x: 0, y: 0, w: 10, h: 0.75, fill: { color: "21295C" }
});
sN.addText("[TÍTULO DO SLIDE]", {
  x: 0.4, y: 0, w: 9.2, h: 0.75,
  fontSize: 22, fontFace: "Cambria", color: "FFFFFF",
  bold: true, valign: "middle", margin: 0
});
// Card esquerdo
sN.addShape(pres.shapes.ROUNDED_RECTANGLE, {
  x: 0.4, y: 0.95, w: 4.4, h: 3.8,
  fill: { color: "F0F6FF" }, rectRadius: 0.1,
  shadow: makeShadow()
});
sN.addText("[Conteúdo card esquerdo]", {
  x: 0.6, y: 1.1, w: 4.0, h: 3.5,
  fontSize: 14, fontFace: "Calibri", color: "1E2761",
  valign: "top", margin: 0
});
// Card direito
sN.addShape(pres.shapes.ROUNDED_RECTANGLE, {
  x: 5.1, y: 0.95, w: 4.4, h: 3.8,
  fill: { color: "F0F6FF" }, rectRadius: 0.1,
  shadow: makeShadow()
});
sN.addText("[Conteúdo card direito]", {
  x: 5.3, y: 1.1, w: 4.0, h: 3.5,
  fontSize: 14, fontFace: "Calibri", color: "1E2761",
  valign: "top", margin: 0
});
sN.addNotes("[Roteiro de fala para este slide]");
```

**Template de slide com callout numérico:**
```javascript
const sN = pres.addSlide();
sN.background = { color: "FFFFFF" };
sN.addShape(pres.shapes.RECTANGLE, {
  x: 0, y: 0, w: 10, h: 0.75, fill: { color: "21295C" }
});
sN.addText("[TÍTULO]", {
  x: 0.4, y: 0, w: 9.2, h: 0.75,
  fontSize: 22, fontFace: "Cambria", color: "FFFFFF",
  bold: true, valign: "middle", margin: 0
});
// Número grande centralizado
sN.addText("[NÚMERO]", {
  x: 3.5, y: 1.2, w: 3, h: 1.5,
  fontSize: 72, fontFace: "Cambria", color: "065A82",
  bold: true, align: "center", margin: 0
});
sN.addText("[unidade ou label]", {
  x: 3, y: 2.8, w: 4, h: 0.5,
  fontSize: 16, fontFace: "Calibri", color: "64748B",
  align: "center", margin: 0
});
sN.addText("[Texto explicativo abaixo do número]", {
  x: 1, y: 3.5, w: 8, h: 1.5,
  fontSize: 14, fontFace: "Calibri", color: "1E2761",
  align: "center", margin: 0
});
sN.addNotes("[Roteiro de fala]");
```

---

### Passo 5 — Executar e fazer QA

```bash
# Gerar o pptx
node gerar_slides.js

# Recomprimir (obrigatório)
python scripts/rezip.py UAM_GrupoA_Seminario.pptx

# Converter para imagens para inspeção visual
python scripts/office/soffice.py --headless --convert-to pdf UAM_GrupoA_Seminario.pptx
rm -f slide-*.jpg
pdftoppm -jpeg -r 150 UAM_GrupoA_Seminario.pdf slide
ls -1 "$PWD"/slide-*.jpg
```

**QA de conteúdo:**
```bash
extract-text UAM_GrupoA_Seminario.pptx

# Checar placeholders não preenchidos
extract-text UAM_GrupoA_Seminario.pptx | grep -iE "\[|\]|TODO|lorem|ipsum|INSERIR"
```

**Se encontrar qualquer `[` ou `]` no output acima → voltar e preencher com conteúdo real do site.**

**QA visual — use subagente com este prompt:**
```
Visually inspect these slides for user-visible defects.

Look for:
- Overlapping elements (text through shapes, lines through words)
- Text overflow or cut off at edges/box boundaries
- Elements too close (< 0.3" gaps)
- Low-contrast text
- Leftover placeholder content (brackets, TODO, lorem)

For each slide, list user-visible issues only. Skip sub-pixel cosmetics.

Read and analyze these images — run `ls -1 "$PWD"/slide-*.jpg` and use the exact absolute paths:
```

---

### Passo 6 — Entregar

Após QA aprovado:

```bash
cp UAM_GrupoA_Seminario.pptx ~/Desktop/UAM_GrupoA_Seminario.pptx
echo "✅ Apresentação pronta em ~/Desktop/"
```

---

## Regras Gerais do Agente

- **Não inventar dados** — use apenas o que está nos arquivos do site
- **Se uma seção do site estiver vazia ou incompleta**, sinalizar no speaker note do slide correspondente: "ATENÇÃO: conteúdo incompleto — revisar antes do seminário"
- **Respeitar o tempo de 60 min**: ~25 slides, ~2,5 min cada
- **Speaker notes em português** com roteiro de fala natural, não técnico demais
- **Todos os textos nos slides em português**, salvo termos técnicos em inglês já usados no site
- **Nunca usar `#` em hex colors** no pptxgenjs
- **Nunca reutilizar objetos de opções** entre múltiplos `addShape`/`addText` — usar factory functions como `makeShadow()`
- **Executar o loop de QA** (gerar → converter → inspecionar → corrigir) pelo menos uma vez antes de declarar sucesso

---

## Como rodar no terminal

```bash
# 1. Vá para a pasta raiz do seu site UAM no terminal
cd ~/caminho/para/seu/projeto/UAM-GrupoA

# 2. Baixe os scripts auxiliares necessários (se ainda não tiver)
#    (eles ficam em ~/.claude/scripts/ quando você instala Claude Code)

# 3. Execute o agente
claude --agent CLAUDE.md

# Ou, se estiver usando Claude Code diretamente:
claude
# E então diga: "Execute as instruções do CLAUDE.md nesta pasta"
```

# Specs — Redesign Pagina de Produto W2GO

> Prototipo funcional: abra `index.html` no navegador.
> Inspecione com DevTools para extrair CSS exato de qualquer componente.

---

## 1. Design Tokens

### Cores
| Token | Hex | Uso |
|-------|-----|-----|
| `--bg` | `#fafafa` | Fundo da pagina |
| `--white` | `#ffffff` | Cards, navbar, sections |
| `--gray-100` | `#f3f4f6` | Fundos secundarios, schedule items |
| `--gray-200` | `#e5e7eb` | Bordas de cards |
| `--gray-400` | `#9ca3af` | Texto terciario, labels |
| `--gray-500` | `#6b7280` | Texto secundario |
| `--gray-700` | `#374151` | Texto de corpo |
| `--gray-900` | `#111827` | Titulos, preco |
| `--green` | `#89EB47` | CTA principal, acentos (marca W2GO) |
| `--green-dark` | `#6bc434` | Hover do CTA, links |
| `--green-bg` | `#f0fde4` | Fundo dos check icons |
| `--red` | `#ef4444` | Badge de urgencia |
| `--red-bg` | `#fef2f2` | Fundo badge urgencia |
| `--purple` | `#8b5cf6` | Badge de categoria |
| `--purple-bg` | `#f5f3ff` | Fundo badge categoria |

### Tipografia
- **Font family:** Inter (Google Fonts) com fallback system
- **Titulo produto:** 28px / 800 / line-height 1.2 / letter-spacing -0.5px
- **Titulo secao:** 17px / 700
- **Corpo:** 15px / 400 / line-height 1.5 / color gray-700
- **Labels:** 12px / 500 / uppercase / letter-spacing 0.5px / color gray-400
- **Preco principal:** 32px / 800 / letter-spacing -1px
- **CTA botao:** 16px / 700

### Espacamento e Bordas
- **Border radius grande:** 16px (cards de compra, galeria)
- **Border radius medio:** 10px (sections, botoes)
- **Border radius pequeno:** 8px (badges, schedule icons)
- **Borda padrao:** 1px solid `#e5e7eb`
- **Shadow card:** `0 4px 16px rgba(0,0,0,0.08)`
- **Shadow hover CTA:** `0 4px 20px rgba(137,235,71,0.4)`

---

## 2. Layout

### Desktop (>900px)
```
[Navbar fixa - 56px height]
[Hero Gallery - grid 1.2fr 1fr, max-height 460px]
[Content: 2 colunas - 1fr | 380px, gap 40px]
  [Left: header + venue + sections]
  [Right: purchase card sticky top:72px]
[Also Like: grid 4 colunas]
```

### Mobile (<=900px)
```
[Navbar fixa - 56px height]
[Hero - imagem unica, full-width, height 320px]
[Content: 1 coluna, padding 16px]
  [Tudo empilhado]
  [Purchase card ESCONDIDA]
[Also Like: grid 2 colunas]
[Bottom bar fixa - preco + botao "Reservar"]
```

---

## 3. Componentes

### 3.1 Navbar
- Sticky top:0, z-index:100
- Height: 56px
- Background: white, border-bottom: 1px solid gray-200
- Esquerda: botao voltar (icone + texto)
- Centro: logo W2GO
- Direita: icones compartilhar + salvar (36px circulos)

### 3.2 Hero Gallery
- Desktop: grid com 1 imagem grande (esquerda, grid-row 1/3) + 2 menores (direita)
- Gap: 4px entre imagens
- Border-radius: 16px no container (overflow hidden)
- Overlay "Ver todas as fotos" no canto inferior direito (blur backdrop)
- Mobile: imagem unica, full-width, sem border-radius

### 3.3 Category Badge
- Background: purple-bg (#f5f3ff)
- Color: purple (#8b5cf6)
- Font: 12px / 700 / uppercase
- Padding: 5px 12px
- Border-radius: 6px

### 3.4 Urgency Badge
- Background: red-bg (#fef2f2)
- Color: red (#ef4444)
- Font: 12px / 700
- Padding: 4px 10px
- Mostrar quando `quantity <= 10`

### 3.5 Venue Card
- Flex row, gap 14px, padding 16px
- Avatar: 48x48px, border-radius 12px, gradient green
- Hover: box-shadow + border mais escura
- Clicavel: leva para pagina do estabelecimento

### 3.6 Section Card
- Background: white
- Border: 1px solid gray-200
- Border-radius: 10px
- Padding: 24px (desktop), 20px 16px (mobile)
- Titulo: icone verde + texto 17px bold

### 3.7 Includes List (checklist)
- Lista vertical, gap 12px
- Cada item: icone check (circle 20px green-bg) + texto 15px
- Montar a partir do campo `description` do produto (parsear itens)

### 3.8 Schedule Grid
- Grid 2x2 (desktop), 1 coluna (mobile)
- Cada item: icone box (40x40, border, radius 10px) + label + valor
- Dados vem de: `available_start_time`, `available_end_time`, `available_weekdays`

### 3.9 Accordion (Regras)
- Sem borda externa, padding reduzido
- Header clicavel: titulo + chevron
- Conteudo escondido por default, toggle com JS
- Secoes: Regras de uso, Politica de cancelamento, Como usar voucher
- Dados de "Regras": campo `usage_rules` do produto

### 3.10 Purchase Card (Desktop)
- Sticky: top 72px
- Preco grande (32px) + unidade ("/por mesa")
- Descricao curta do que esta incluso
- 3 bullet points de confianca (icone + texto)
- CTA: botao full-width, gradient green, texto "Reservar — R$ XX"
- Cupom: apenas texto link "Tem cupom? Aplique no checkout"
- SEM campo de quantidade (default 1, edita no checkout)
- SEM campo de cupom inline

### 3.11 Bottom Bar (Mobile only)
- Fixed bottom:0, z-index:100
- Shadow: `0 -4px 20px rgba(0,0,0,0.08)`
- Esquerda: preco (20px bold) + subtexto
- Direita: botao "Reservar" compacto
- Aparece APENAS em telas <=900px

### 3.12 Also Like Cards
- Grid 4 colunas (desktop), 2 colunas (mobile)
- Card: imagem (160px height) + body (cat + titulo + venue + preco)
- Hover: translateY(-3px) + shadow
- Dados: query de produtos da mesma regiao/categoria

---

## 4. Dados do Supabase → Componentes

| Campo Supabase | Onde usar |
|----------------|-----------|
| `title` | Titulo do produto (h1) |
| `description` | "O que esta incluso" (parsear em lista) |
| `category` | Badge de categoria |
| `price` | Preco no purchase card + bottom bar |
| `quantity` | Badge urgencia ("Ultimas X unidades") se <= 10 |
| `images` | Hero gallery (array de URLs) |
| `business_name` | Venue card + meta |
| `place_id` | Link para pagina do estabelecimento |
| `usage_rules` | Accordion "Regras de uso" |
| `available_weekdays` | Schedule grid (dias) |
| `available_start_time` | Schedule grid (horario inicio) |
| `available_end_time` | Schedule grid (horario fim) |
| `lat`, `lng` | Mapa de localizacao |

---

## 5. Mudancas vs. Design Atual

| # | O que muda | Por que |
|---|-----------|---------|
| 1 | Titulo em case normal (nao CAPS) | Legibilidade, menos agressivo |
| 2 | Remover campo quantidade da pagina | 95% compra 1 unidade, simplifica |
| 3 | Mover cupom para checkout | Reduz friccao visual no CTA |
| 4 | Preco com contexto ("/ por mesa") | Usuario entende o que esta pagando |
| 5 | CTA com preco dentro ("Reservar — R$65") | Menos cliques mentais |
| 6 | Descricao em checklist | Escaneabilidade, nao precisa ler tudo |
| 7 | Horario/data visiveis na pagina | Usuario decide sem clicar no CTA |
| 8 | Accordion para regras | Nao polui visualmente, mas acessivel |
| 9 | Badge de urgencia | Gatilho de escassez quando quantity baixo |
| 10 | Venue card com destaque | Gera confianca, permite descoberta |
| 11 | Bottom bar sticky (mobile) | CTA sempre visivel ao scrollar |
| 12 | Galeria em grid (nao carrossel) | Usuario ve mais fotos sem interagir |

---

## 6. Como Implementar

1. **Abra `index.html`** no browser e use DevTools para inspecionar qualquer componente
2. **Copie os CSS custom properties** (`:root`) para o design system do projeto
3. **Componentes sao independentes** — implemente um por vez, comecando pelo purchase card + bottom bar (maior impacto em conversao)
4. **Dados dinamicos:** substitua o conteudo estatico pelos campos do Supabase conforme tabela da secao 4
5. **Breakpoint unico:** 900px separa desktop de mobile

### Ordem sugerida de implementacao
1. Purchase card + bottom bar sticky (impacto direto em conversao)
2. Hero gallery em grid
3. Secao "O que esta incluso" com checklist
4. Schedule grid "Quando e onde"
5. Venue card
6. Accordion de regras
7. Badge de urgencia
8. "Mais experiencias" redesenhado

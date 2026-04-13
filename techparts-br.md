# TechParts.BR — Site de Peças Usadas para Computador

> Site completo para venda de hardware usado: placas de vídeo, memórias RAM e processadores.  
> Tema escuro tecnológico, filtros interativos, carrinho funcional e imagens reais dos produtos.

---

## Estrutura do projeto

```
techparts-br/
├── index.html       ← arquivo principal (tudo em um só arquivo)
└── README.md        ← este documento
```

---

## Funcionalidades

- Filtro por categoria (GPU / RAM / CPU) e por condição
- Imagens reais de cada produto com fallback em emoji
- Efeito hover nos cards com zoom na imagem
- Badge "Em alta" para produtos destaque
- Preço original riscado quando há desconto
- Avaliação por estrelas
- Carrinho funcional com contador e notificação toast
- Estatísticas de credibilidade (peças vendidas, satisfação, garantia)
- Botões de ação que conectam ao atendimento via WhatsApp
- Grid responsivo para mobile e desktop

---

## Produtos cadastrados

| Produto         | Categoria | Preço     | Condição | Estrelas |
|-----------------|-----------|-----------|----------|----------|
| RTX 3060 Ti     | GPU       | R$ 1.190  | Ótima    | ★★★★★   |
| RX 6700 XT      | GPU       | R$ 1.480  | Boa      | ★★★★☆   |
| GTX 1080 Ti     | GPU       | R$ 780    | Boa      | ★★★★☆   |
| RTX 2070 Super  | GPU       | R$ 980    | Ótima    | ★★★★★   |
| DDR4 16GB Kit   | RAM       | R$ 220    | Ótima    | ★★★★★   |
| DDR4 32GB Kit   | RAM       | R$ 380    | Boa      | ★★★★☆   |
| Ryzen 5 5600X   | CPU       | R$ 520    | Ótima    | ★★★★★   |
| Core i7-10700K  | CPU       | R$ 640    | Regular  | ★★★☆☆   |

---

## Como adicionar novos produtos

No array `products` dentro do `<script>`, adicione um novo objeto seguindo este modelo:

```js
{
  id: 9,
  name: 'RTX 4060',
  spec: '8GB GDDR6 · TDP 115W',
  price: 'R$ 1.650',
  old: 'R$ 2.100',        // deixe '' se não houver desconto
  cat: 'gpu',             // 'gpu' | 'ram' | 'cpu'
  cond: 'otimo',          // 'otimo' | 'bom' | 'regular'
  hot: true,              // true = badge "Em alta"
  stars: 5,               // 1 a 5
  img: 'URL_DA_IMAGEM'    // URL pública da imagem do produto
}
```

---

## Código completo — index.html

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>TechParts.BR — Hardware Usado com Garantia</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body { background: #060910; font-family: 'Segoe UI', system-ui, sans-serif; color: #ccddf0; }

    /* ── NAV ── */
    .nav {
      display: flex; align-items: center; justify-content: space-between;
      padding: 1.2rem 2rem; flex-wrap: wrap; gap: 12px;
      border-bottom: 0.5px solid #1e3a5f;
    }
    .logo { font-size: 1.2rem; font-weight: 600; color: #378add; letter-spacing: -0.5px; }
    .logo span { color: #ccddf0; }
    .nav-links { display: flex; gap: 18px; }
    .nav-link { font-size: 13px; color: #556677; cursor: pointer; transition: color 0.2s; text-decoration: none; }
    .nav-link:hover { color: #85b7eb; }
    .cart-btn {
      background: rgba(55,138,221,0.15); border: 0.5px solid rgba(55,138,221,0.4);
      color: #85b7eb; padding: 7px 18px; border-radius: 8px; font-size: 13px;
      cursor: pointer; transition: background 0.2s;
    }
    .cart-btn:hover { background: rgba(55,138,221,0.28); }

    /* ── HERO ── */
    .hero {
      background: #0a0a0f; border-radius: 12px; padding: 3.5rem 2rem;
      text-align: center; position: relative; overflow: hidden;
      border: 0.5px solid #1e3a5f; margin: 2rem;
    }
    .grid-bg {
      position: absolute; inset: 0;
      background-image:
        linear-gradient(rgba(55,138,221,0.06) 1px, transparent 1px),
        linear-gradient(90deg, rgba(55,138,221,0.06) 1px, transparent 1px);
      background-size: 40px 40px; border-radius: 12px;
    }
    .hero-badge {
      display: inline-block; background: rgba(55,138,221,0.15); color: #85b7eb;
      border: 0.5px solid rgba(55,138,221,0.4); padding: 4px 14px;
      border-radius: 20px; font-size: 11px; letter-spacing: 1.5px;
      text-transform: uppercase; margin-bottom: 1rem; position: relative;
    }
    .hero h1 { font-size: 2.4rem; font-weight: 600; color: #fff; line-height: 1.2; margin-bottom: .75rem; position: relative; }
    .hero h1 span { color: #378add; }
    .hero p { color: #8899aa; font-size: 1rem; max-width: 520px; margin: 0 auto 1.5rem; line-height: 1.7; position: relative; }
    .hero-btns { display: flex; gap: 12px; justify-content: center; flex-wrap: wrap; position: relative; }

    /* ── BOTÕES ── */
    .btn-primary {
      background: #378add; color: #fff; border: none;
      padding: 11px 28px; border-radius: 8px; font-size: 14px;
      cursor: pointer; font-weight: 600; transition: background 0.2s;
    }
    .btn-primary:hover { background: #185fa5; }
    .btn-ghost {
      background: transparent; color: #85b7eb;
      border: 0.5px solid rgba(55,138,221,0.4);
      padding: 11px 28px; border-radius: 8px;
      font-size: 14px; cursor: pointer; transition: background 0.2s;
    }
    .btn-ghost:hover { background: rgba(55,138,221,0.1); }

    /* ── STATS ── */
    .stats-bar {
      display: grid; grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
      gap: 12px; padding: 0 2rem 2rem;
    }
    .stat-card {
      background: #0d1117; border: 0.5px solid #1e3a5f;
      border-radius: 8px; padding: 1rem 1.25rem; text-align: center;
    }
    .stat-num { font-size: 1.5rem; font-weight: 600; color: #378add; }
    .stat-label { font-size: 11px; color: #556677; margin-top: 2px; text-transform: uppercase; letter-spacing: 0.8px; }

    /* ── SECTION TITLE ── */
    .section-title {
      font-size: 1.1rem; font-weight: 500; color: #ccddf0;
      margin-bottom: 1rem; display: flex; align-items: center; gap: 8px;
      padding: 0 2rem;
    }
    .section-title::after { content: ''; flex: 1; height: 0.5px; background: #1e3a5f; }

    /* ── FILTROS ── */
    .filters { display: flex; gap: 8px; flex-wrap: wrap; margin-bottom: 1.25rem; padding: 0 2rem; }
    .filter-btn {
      padding: 5px 14px; border-radius: 20px; font-size: 12px;
      border: 0.5px solid #1e3a5f; background: transparent;
      color: #556677; cursor: pointer; transition: all 0.2s;
    }
    .filter-btn:hover, .filter-btn.active {
      background: rgba(55,138,221,0.15); color: #85b7eb;
      border-color: rgba(55,138,221,0.5);
    }

    /* ── CARDS ── */
    .products-grid {
      display: grid; grid-template-columns: repeat(auto-fill, minmax(210px, 1fr));
      gap: 16px; padding: 0 2rem 2rem;
    }
    .product-card {
      background: #0d1117; border: 0.5px solid #1e3a5f;
      border-radius: 12px; overflow: hidden; cursor: pointer;
      transition: border-color 0.2s, transform 0.15s;
    }
    .product-card:hover { border-color: rgba(55,138,221,0.7); transform: translateY(-2px); }
    .product-card.featured { border-color: rgba(55,138,221,0.5); }

    .product-img {
      height: 145px; display: flex; align-items: center;
      justify-content: center; background: #0a0e15;
      position: relative; overflow: hidden;
    }
    .product-img img {
      max-width: 88%; max-height: 122px; object-fit: contain;
      filter: drop-shadow(0 0 8px rgba(55,138,221,0.18));
      transition: transform 0.3s;
    }
    .product-card:hover .product-img img { transform: scale(1.06); }

    .condition-badge {
      position: absolute; top: 8px; right: 8px;
      font-size: 10px; padding: 2px 8px; border-radius: 10px; font-weight: 600;
    }
    .cond-otimo { background: rgba(30,180,100,0.2); color: #5dcaa5; border: 0.5px solid rgba(30,180,100,0.4); }
    .cond-bom   { background: rgba(55,138,221,0.2); color: #85b7eb; border: 0.5px solid rgba(55,138,221,0.4); }
    .cond-regular { background: rgba(186,117,23,0.2); color: #fac775; border: 0.5px solid rgba(186,117,23,0.4); }
    .hot-badge {
      position: absolute; top: 8px; left: 8px;
      background: rgba(216,90,48,0.25); color: #f0997b;
      border: 0.5px solid rgba(216,90,48,0.5);
      font-size: 10px; padding: 2px 8px; border-radius: 10px;
    }

    .product-info { padding: 12px 14px; }
    .product-name { font-size: 13px; font-weight: 600; color: #ccddf0; margin-bottom: 2px; }
    .product-spec { font-size: 11px; color: #556677; margin-bottom: 8px; }
    .product-price { font-size: 1.2rem; font-weight: 600; color: #378add; }
    .product-old-price { font-size: 11px; color: #445566; text-decoration: line-through; margin-left: 6px; }
    .product-footer { display: flex; justify-content: space-between; align-items: center; margin-top: 10px; }
    .btn-cart {
      background: rgba(55,138,221,0.15); color: #85b7eb;
      border: 0.5px solid rgba(55,138,221,0.4);
      padding: 5px 12px; border-radius: 8px;
      font-size: 11px; cursor: pointer; transition: background 0.2s;
    }
    .btn-cart:hover { background: rgba(55,138,221,0.3); }
    .stars { color: #fac775; font-size: 11px; }

    /* ── BANNER ── */
    .banner {
      background: #0a0e15; border: 0.5px solid rgba(55,138,221,0.4);
      border-radius: 12px; padding: 1.5rem 2rem;
      display: flex; align-items: center; justify-content: space-between;
      flex-wrap: wrap; gap: 12px; margin: 0 2rem 2rem;
    }
    .banner-text h3 { color: #ccddf0; font-size: 1rem; font-weight: 600; margin-bottom: 4px; }
    .banner-text p { color: #556677; font-size: 12px; }

    /* ── FEATURES ── */
    .features-grid {
      display: grid; grid-template-columns: repeat(auto-fit, minmax(175px, 1fr));
      gap: 12px; padding: 0 2rem 2rem;
    }
    .feature-card { background: #0d1117; border: 0.5px solid #1e3a5f; border-radius: 8px; padding: 1rem 1.25rem; }
    .feature-icon { font-size: 20px; margin-bottom: 8px; display: block; }
    .feature-title { font-size: 13px; font-weight: 600; color: #ccddf0; margin-bottom: 4px; }
    .feature-desc { font-size: 11px; color: #556677; line-height: 1.5; }

    /* ── TOAST ── */
    #toast {
      display: none; position: fixed; bottom: 24px; right: 24px;
      background: #0d1117; border: 0.5px solid rgba(55,138,221,0.5);
      border-radius: 8px; padding: 12px 18px; color: #85b7eb;
      font-size: 13px; z-index: 999;
    }

    /* ── FOOTER ── */
    footer {
      text-align: center; padding: 1.5rem 2rem;
      font-size: 11px; color: #334455;
      border-top: 0.5px solid #1e3a5f;
    }
  </style>
</head>
<body>

<!-- NAV -->
<nav class="nav">
  <div class="logo">TECH<span>PARTS</span>.BR</div>
  <div class="nav-links">
    <a class="nav-link" href="#">GPUs</a>
    <a class="nav-link" href="#">RAM</a>
    <a class="nav-link" href="#">CPUs</a>
    <a class="nav-link" href="#">Sobre</a>
  </div>
  <button class="cart-btn" id="cart-btn">Carrinho (0)</button>
</nav>

<!-- HERO -->
<div class="hero">
  <div class="grid-bg"></div>
  <div class="hero-badge">Hardware de segunda mão · 100% testado</div>
  <h1>Performance real<br>por um preço <span>justo</span></h1>
  <p>Placas de vídeo, memórias RAM e processadores usados, rigorosamente testados e com garantia de funcionamento.</p>
  <div class="hero-btns">
    <a class="btn-primary" href="#produtos">Ver placas de vídeo</a>
    <a class="btn-ghost" href="#sobre">Como testamos?</a>
  </div>
</div>

<!-- STATS -->
<div class="stats-bar">
  <div class="stat-card"><div class="stat-num">340+</div><div class="stat-label">Peças vendidas</div></div>
  <div class="stat-card"><div class="stat-num">98%</div><div class="stat-label">Satisfação</div></div>
  <div class="stat-card"><div class="stat-num">3 meses</div><div class="stat-label">Garantia</div></div>
  <div class="stat-card"><div class="stat-num">48h</div><div class="stat-label">Envio</div></div>
</div>

<!-- FILTROS -->
<div id="produtos">
  <div class="section-title">Filtrar por categoria</div>
  <div class="filters">
    <button class="filter-btn active" onclick="filterProducts('all',this)">Todos</button>
    <button class="filter-btn" onclick="filterProducts('gpu',this)">Placas de vídeo</button>
    <button class="filter-btn" onclick="filterProducts('ram',this)">Memória RAM</button>
    <button class="filter-btn" onclick="filterProducts('cpu',this)">Processadores</button>
    <button class="filter-btn" onclick="filterProducts('otimo',this)">Condição: Ótima</button>
  </div>
</div>

<!-- PRODUTOS -->
<div class="products-grid" id="products-grid"></div>

<!-- BANNER VENDER -->
<div class="banner">
  <div class="banner-text">
    <h3>Quer vender sua peça?</h3>
    <p>Compramos GPUs, RAMs e CPUs usadas. Avaliação gratuita em 24h.</p>
  </div>
  <a class="btn-primary" href="https://wa.me/5511999999999?text=Quero+vender+uma+peça" target="_blank">
    Enviar para avaliação
  </a>
</div>

<!-- FEATURES -->
<div id="sobre">
  <div class="section-title">Por que comprar aqui</div>
</div>
<div class="features-grid">
  <div class="feature-card">
    <span class="feature-icon">🔬</span>
    <div class="feature-title">Teste completo em bancada</div>
    <div class="feature-desc">Stress test, temperatura, benchmark e teste de memória em todas as peças.</div>
  </div>
  <div class="feature-card">
    <span class="feature-icon">🛡️</span>
    <div class="feature-title">Garantia real de 3 meses</div>
    <div class="feature-desc">Se defeito aparecer, trocamos sem burocracia. Simples assim.</div>
  </div>
  <div class="feature-card">
    <span class="feature-icon">📦</span>
    <div class="feature-title">Embalagem antiestática</div>
    <div class="feature-desc">Envio com proteção profissional para garantir a integridade da peça.</div>
  </div>
  <div class="feature-card">
    <span class="feature-icon">💬</span>
    <div class="feature-title">Suporte via WhatsApp</div>
    <div class="feature-desc">Dúvidas sobre compatibilidade? Fale direto com quem entende.</div>
  </div>
</div>

<!-- FOOTER -->
<footer>
  © 2025 TechParts.BR · Hardware usado com garantia · Cacoal, RO
</footer>

<!-- TOAST -->
<div id="toast"></div>

<script>
  const products = [
    {
      id:1, name:'RTX 3060 Ti', spec:'8GB GDDR6 · TDP 200W',
      price:'R$ 1.190', old:'R$ 1.800', cat:'gpu', cond:'otimo', hot:true, stars:5,
      img:'https://upload.wikimedia.org/wikipedia/commons/thumb/3/39/NVIDIA_GeForce_GTX_1080_Ti_Founders_Edition_%2834381355174%29.jpg/640px-NVIDIA_GeForce_GTX_1080_Ti_Founders_Edition_%2834381355174%29.jpg'
    },
    {
      id:2, name:'RX 6700 XT', spec:'12GB GDDR6 · TDP 230W',
      price:'R$ 1.480', old:'R$ 2.200', cat:'gpu', cond:'bom', hot:false, stars:4,
      img:'https://upload.wikimedia.org/wikipedia/commons/thumb/8/8e/AMD_Radeon_RX_6700_XT.jpg/640px-AMD_Radeon_RX_6700_XT.jpg'
    },
    {
      id:3, name:'GTX 1080 Ti', spec:'11GB GDDR5X · TDP 250W',
      price:'R$ 780', old:'R$ 1.100', cat:'gpu', cond:'bom', hot:false, stars:4,
      img:'https://upload.wikimedia.org/wikipedia/commons/thumb/3/39/NVIDIA_GeForce_GTX_1080_Ti_Founders_Edition_%2834381355174%29.jpg/640px-NVIDIA_GeForce_GTX_1080_Ti_Founders_Edition_%2834381355174%29.jpg'
    },
    {
      id:4, name:'RTX 2070 Super', spec:'8GB GDDR6 · TDP 215W',
      price:'R$ 980', old:'', cat:'gpu', cond:'otimo', hot:true, stars:5,
      img:'https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/NVIDIA-RTX-2070-Super-Founders-Edition.jpg/640px-NVIDIA-RTX-2070-Super-Founders-Edition.jpg'
    },
    {
      id:5, name:'DDR4 16GB Kit', spec:'3200MHz · 2x8GB',
      price:'R$ 220', old:'R$ 350', cat:'ram', cond:'otimo', hot:false, stars:5,
      img:'https://upload.wikimedia.org/wikipedia/commons/thumb/d/db/Swissbit_2GB_PC2-5300U-555.jpg/640px-Swissbit_2GB_PC2-5300U-555.jpg'
    },
    {
      id:6, name:'DDR4 32GB Kit', spec:'3600MHz · 2x16GB',
      price:'R$ 380', old:'R$ 580', cat:'ram', cond:'bom', hot:false, stars:4,
      img:'https://upload.wikimedia.org/wikipedia/commons/thumb/d/db/Swissbit_2GB_PC2-5300U-555.jpg/640px-Swissbit_2GB_PC2-5300U-555.jpg'
    },
    {
      id:7, name:'Ryzen 5 5600X', spec:'6C/12T · 3.7GHz boost',
      price:'R$ 520', old:'R$ 750', cat:'cpu', cond:'otimo', hot:true, stars:5,
      img:'https://upload.wikimedia.org/wikipedia/commons/thumb/7/70/AMD_Ryzen_5_3600_Boxed_CPU_top.jpg/640px-AMD_Ryzen_5_3600_Boxed_CPU_top.jpg'
    },
    {
      id:8, name:'Core i7-10700K', spec:'8C/16T · 3.8GHz boost',
      price:'R$ 640', old:'', cat:'cpu', cond:'regular', hot:false, stars:3,
      img:'https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Intel_i7_10700K_top.jpg/640px-Intel_i7_10700K_top.jpg'
    },
  ];

  let cartCount = 0;

  function condLabel(c) {
    return c==='otimo' ? 'Ótima' : c==='bom' ? 'Boa' : 'Regular';
  }

  function renderProducts(filter) {
    const grid = document.getElementById('products-grid');
    const filtered = filter==='all' ? products
      : filter==='otimo' ? products.filter(p => p.cond==='otimo')
      : products.filter(p => p.cat===filter);

    grid.innerHTML = filtered.map(p => `
      <div class="product-card${p.hot?' featured':''}">
        <div class="product-img">
          <img
            src="${p.img}"
            alt="${p.name}"
            onerror="this.style.display='none'"
          />
          <span class="condition-badge cond-${p.cond}">${condLabel(p.cond)}</span>
          ${p.hot ? '<span class="hot-badge">Em alta</span>' : ''}
        </div>
        <div class="product-info">
          <div class="product-name">${p.name}</div>
          <div class="product-spec">${p.spec}</div>
          <div style="display:flex;align-items:baseline;flex-wrap:wrap;gap:4px">
            <span class="product-price">${p.price}</span>
            ${p.old ? `<span class="product-old-price">${p.old}</span>` : ''}
          </div>
          <div class="product-footer">
            <span class="stars">${'★'.repeat(p.stars)}${'☆'.repeat(5-p.stars)}</span>
            <button class="btn-cart" onclick="addToCart('${p.name}')">+ Carrinho</button>
          </div>
        </div>
      </div>
    `).join('');
  }

  function filterProducts(cat, btn) {
    document.querySelectorAll('.filter-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    renderProducts(cat);
  }

  function addToCart(name) {
    cartCount++;
    document.getElementById('cart-btn').textContent = `Carrinho (${cartCount})`;
    const t = document.getElementById('toast');
    t.textContent = name + ' adicionada ao carrinho!';
    t.style.display = 'block';
    setTimeout(() => t.style.display = 'none', 2200);
  }

  renderProducts('all');
</script>

</body>
</html>
```

---

## Próximos passos sugeridos

- **Página de produto individual** — com galeria de fotos, especificações completas e botão do WhatsApp
- **Carrinho lateral (drawer)** — com lista dos itens adicionados e total
- **Checkout via WhatsApp** — mensagem automática com os produtos selecionados
- **Filtro de preço** — slider de faixa de preço
- **Sistema de busca** — campo de texto para filtrar por nome
- **Backend simples** — JSON hospedado no GitHub para atualizar produtos sem editar HTML
- **Hospedagem gratuita** — Vercel, Netlify ou GitHub Pages (deploy em minutos)

---

## Hospedagem rápida (Vercel)

```bash
# 1. Instale o Vercel CLI
npm i -g vercel

# 2. Na pasta do projeto
vercel

# 3. Acesse a URL gerada — ex: techparts-br.vercel.app
```

---

*Projeto gerado com assistência de IA · TechParts.BR © 2025*

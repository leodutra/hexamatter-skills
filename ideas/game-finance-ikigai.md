# Descoberta de Oportunidades — Rodada 2 (Ikigai: Games, Dev Tools, OS, Investimento PF, Finanças Pessoais)

## TL;DR
- A recomendação nº1 é uma **cunha dupla consumer→B2B no imposto de investimento no exterior para PF brasileiro (Lei 14.754/2023 + PTAX)**: dor real e recorrente, base crescente (Avenue com 1 milhão de clientes em março/2025; Nomad com 3,5 milhões de clientes), regulação mínima (cálculo tributário ≠ recomendação de investimento, sem CVM) e um contraexemplo comprovado à tese de que consumer não paga.
- A tese "picks-and-shovels" (vender para profissionais, não consumidores) se **confirma em games, retro e dev tools**, mas é **parcialmente refutada em finanças/investimento PF**, onde Grana Capital (~400 mil usuários, ~10% dos investidores PF ativos na B3) e Velotax (R$19,90/mês) provam que o consumidor final paga por automação tributária.
- **Matamos explicitamente** três hipóteses: LiveOps self-hostável (Unity cancelou o runtime fee, a dor esfriou), tooling pago para Bevy/Rust (cedo demais para monetizar) e porting de retrô como produto de fundador solo (a barreira é comercial — NDA/IP —, não técnica).

## Key Findings

**1. Games — mercado gigantesco de devs, mas consumidor final paga pouco; o dinheiro está nas ferramentas.** Um recorde de 19.468 jogos foi lançado na Steam em 2025 (SteamDB), com ~44.000 developers na plataforma e uma estimativa de 11,1 milhões de game developers no mundo no Q1 2024, incluindo hobbyistas sérios e estudantes (SlashData). A distribuição de receita é brutalmente concentrada: segundo a Gamalytic (citada por Artur Smiarowski/GamesRadar, nov/2025), **8.388 (65,9%) dos lançamentos de 2025 na Steam faturaram menos de US$1.000**, com receita bruta mediana de US$249. Na GDC 2026 a Valve disse que 5.863 jogos superaram US$100k em 2025, mas Simon Carless estimou que "apenas cerca de 1.700 desses foram jogos que de fato lançaram em 2025" (Alinea Analytics). Comparáveis de dados sustentam preços modestos mas recorrentes: Gamalytic a US$25/mês no Starter; a VG Insights migrou para a Sensor Tower e abandonou preço público, virando enterprise/"contact sales". **Conclusão:** consumer de games = WTP baixa; picks-and-shovels = onde há receita, mas com CAC e defensibilidade difíceis.

**2. LiveOps self-hostável pós-Unity — a dor ESFRIOU. MATAR.** A Unity cancelou o Runtime Fee (reversão anunciada em 2024, com volta ao modelo de assinatura por seat), depois de a crise ter custado a saída do CEO John Riccitiello. O gatilho emocional que justificaria "controle/self-host" perdeu urgência. Além disso, Nakama (Heroic Labs) já é open-source self-hostável e ocupa o espaço, com Heroic Cloud como camada gerenciada e concorrentes managed (Crux/Supercraft GSB a partir de US$10/mês) surgindo. Entrar aqui hoje é competir com incumbente estabelecido sem uma crise que empurre a migração.

**3. Porting/remaster de retrô — mercado real e crescente, mas ERRADO para fundador solo como produto próprio.** O nicho é dominado por engines proprietárias: KEX (Nightdive, adquirida pela Atari), Carbon Engine (Limited Run, adquirida pela Embracer em 2022), Syrup (Implicit Conversions) e Digital Eclipse. O gargalo não é código — é relação com detentores de IP e NDAs de console. Custo típico de port indie→console: **US$10k–50k por plataforma** (Gamedō, 2026) e **US$10k–100k na média** (iLogos), com certificação adicionando 2–6 meses; há um outlier de vendor (Kokku, US$4–6k/plataforma). A decompilação assistida por IA avança rápido — LLM4Decompile, o caso real de Bruno Macabeus decompilando Sonic Advance 3 com Claude a 96% de match na primeira tentativa, e servidores Ghidra MCP. Isso pode virar vantagem de produtividade em subcontratos, mas não remove a barreira comercial de IP. **Veredito:** viável apenas como serviço high-ticket por subcontrato (ver #5), não como picks-and-shovels escalável.

**4. Infra de negócio para apps desktop indie — GAP REAL e comprovado.** A Keygen.sh (fundada em 2016, solo founder até 2025, bootstrapped e lucrativa — o próprio fundador relata que a receita ultrapassou seu salário full-time por volta de 2021) prova que licenciamento como API sustenta um negócio de uma pessoa. Mas o ecossistema é fragmentado: licenciamento (Keygen/LicenseSeat), billing/merchant-of-record (Paddle/Lemon Squeezy), distribuição e auto-update (CrabNebula para Tauri, ToDesktop para Electron), crash reporting (Sentry). Não existe um "negócio-de-app-desktop-em-uma-caixa" integrado e barato para o dev indie de Tauri/Electron. A Keygen cobra US$49/mês na entrada — comentaristas do Hacker News calculam que, em volumes modestos de venda, o licenciamento sozinho pode consumir até ~70% da receita bruta —, abrindo espaço para reposicionamento por preço + integração. Risco: mercado pequeno e WTP concentrada.

**5. Imposto de investidor PF brasileiro — o achado mais forte, com CONTRAEXEMPLO à tese consumer.** Grana Capital (parceiro da B3, que investiu na empresa em 2023) soma, segundo o CEO André Kelmanson (Brazil Economy, abr/2026), "cerca de 400 mil usuários conectados, aproximadamente 10% dos investidores pessoa física ativos na bolsa brasileira"; a Velotax cobra R$19,90/mês. Preço consumer da Grana: R$29,90/mês no plano anual (~R$239–359/ano). O sub-nicho pouco tratado é o de **investimentos no EXTERIOR sob a Lei 14.754/2023** — vigente desde 1/1/2024, com alíquota única de 15%, apuração agora anual na DAA (não mais mensal via GCAP), fim da isenção de pequenas vendas e a complexidade de PTAX de compra/venda, origem dos recursos, e compensação do imposto pago nos EUA via reciprocidade. A base cresce: **Avenue com 1 milhão de clientes (mar/2025, meta de 2M em 1–2 anos, ~20% da base vinda do Itaú); Nomad saltou de 1,2M para 3,5 milhões de clientes em pouco mais de um ano (PANROTAS, set/2025), com ARR de R$500 mi em 2025.** O grau de internacionalização ainda é baixíssimo e sinaliza espaço de crescimento: dirigentes da ANBIMA notam que "o investidor brasileiro médio ainda concentra 97,5% dos ativos no mercado local" (Giuliano De Marchi, J.P. Morgan AM) e que "o Brasil representa 1,5% de todos os ativos de investimento no mundo" (Juliana Laham, Bradesco). As ferramentas atuais para o exterior são calculadoras gratuitas simples (ex.: Somas.io) ou focadas na B3 doméstica. Confirmação regulatória: software de **cálculo** tributário não é recomendação de investimento → não exige licença CVM.

**6. Bevy/Rust tooling — cedo demais para monetizar. MATAR (por ora).** O Bevy teve crescimento forte de comunidade em 2025 (dezenas de milhares de GitHub stars, editor preview chegando no 0.18 em março/2026), mas ainda com pouquíssimos jogos comerciais shipados (Tiny Glade — que na prática usou integrações Vulkan custom além do ECS do Bevy; Toroban; Simulo; Long Story 2). Ecossistema vibrante mas pré-monetização; devs são hobbyistas/early-adopters com WTP ~zero por tooling pago. Ótimo para reputação e open-source, ruim para receita.

## Details

### Ranking (Top 10 → Top 5)
Analisei mais de 20 oportunidades nos domínios listados, priorizando lucro líquido do fundador / complexidade operacional.

**Top 10:**
1. Imposto investidor exterior (consumer→B2B contadores) — Lei 14.754
2. Business-in-a-box para apps desktop Tauri/Electron
3. Consolidador/otimizador tributário B2B para contadores de investidores PF
4. Localização com contexto via LLM lendo arquivos do jogo
5. Serviço produtizado de porting/decompilação AI-assisted (subcontrato)
6. Telemetria de playtest/analytics por nível self-hostável para indies
7. Data product de inteligência de mercado Steam para nichos sub-atendidos
8. QA automation (smoke tests multiplataforma) para builds indie
9. Crash reporting + symbolication barato para jogos nativos/Rust
10. Marketplace de nicho de toolchain/assets para re-releases retrô

---

#### #1 — "IR do Exterior": apuração de ganho de capital/dividendos internacionais para PF brasileiro (Lei 14.754/2023)
- **Cliente exato:** brasileiro PF com conta em Avenue/Nomad/Inter/XP Internacional/Interactive Brokers que vende ações/ETFs, recebe dividendos ou precisa apurar posição em 31/12 e declarar na DAA. Segmento secundário: contadores que atendem esses investidores.
- **Problema:** apuração em USD com PTAX de compra e de venda, distinção de origem dos recursos, dividendos com retenção de 30% nos EUA e compensação por reciprocidade, posição anual em Bens e Direitos, fim da isenção de pequenas vendas. É confuso, muda ano a ano, e as corretoras oferecem material genérico, não cálculo.
- **Solução:** importar extratos das corretoras (CSV/PDF/API onde houver), calcular ganho de capital em BRL via PTAX automaticamente, gerar o Demonstrativo da Lei 14.754 e os campos prontos para a DIRPF, com otimização de compensação de perdas.
- **Por que pagariam:** evitar malha fina, poupar horas de planilha e consultas de PTAX, e o valor em jogo (15% sobre ganhos) justifica R$150–400/ano.
- **Pricing:** consumer R$199–349/ano; B2B contadores por-CPF (modelo pay-per-client) ou assinatura R$300–800/mês.
- **Revenue potential:** 5.000 consumers × R$249 = R$1,24M/ano; 100 contadores × R$500/mês = R$600k/ano. US$500k–1M ARR factível com base crescente.
- **Margem:** bruta ~90% (infra baixa, PTAX é dado público do BCB, LLM só no parsing). Net ~70%+ com operação enxuta.
- **% automatizável:** 85%+. Suporte sazonal (março–maio) é o principal custo humano.
- **AI leverage:** parsing de extratos heterogêneos, classificação de eventos corporativos, suporte de primeira linha.
- **MVP:** 6–10 semanas (calculadora + importadores das 2–3 corretoras dominantes + export DIRPF).
- **Primeiros 5–10 clientes:** SEO ("imposto ações exterior PTAX"), comunidades (Reddit r/investimentos, grupos FIRE/dividendos, comunidades Nomad/Avenue), parcerias com influencers de investimento internacional.
- **Competição:** Grana/Velotax focam a B3 doméstica; calculadoras gratuitas (Somas.io) são simples e não geram a declaração completa; contadores fazem manual. Gap claro no exterior.
- **Por que incumbentes não resolveram:** o exterior é minoria da base (mas em rápido crescimento), a lei é nova e volátil, e os grandes players priorizaram o volume doméstico da B3.
- **Como chegar a US$1M ARR:** consumer-led até ~5–8k assinantes, depois camada B2B de contadores reusando o mesmo motor.
- **Risco de plataforma:** dependência de importação das corretoras (mitigável com upload manual); mudança de lei é risco E oportunidade (a atualização vira fosso).
- **Scores (0–10):** Blue Ocean 7 · Pain 8 · WTP 7 · Automation 8 · AI leverage 6 · Defensibility 6 · Ease of building 8 · Distribution (10=pior) 5 · Competition (10=pior) 4 · Longevity 8 · Founder fit 8 · Ikigai fit 8.

#### #2 — "Ship Desktop": business-in-a-box para apps desktop indie (Tauri/Electron)
- **Cliente exato:** dev indie/pequeno estúdio vendendo app desktop (produtividade, ferramentas criativas, plugins de áudio, utilitários) em Tauri ou Electron.
- **Problema:** montar licenciamento + ativação/trials + auto-update assinado + crash reporting + billing exige costurar 4–5 serviços. Caro e fragmentado no início.
- **Solução:** SDK único + painel entregando licenciamento, auto-update (integrando o updater do Tauri), telemetria de crash e hooks de billing, com self-host opcional.
- **Por que pagariam:** economiza semanas de integração; a Keygen sozinha pode consumir até ~70% da receita bruta em vendas modestas (cálculo de comentaristas do HN).
- **Pricing:** US$9–19/mês entrada; US$49–99 pro; self-host/EE flat.
- **Revenue potential:** 500 clientes × US$25/mês ≈ US$150k ARR; 2.000 × US$40 ≈ US$960k ARR. Modelo de volume com CAC orgânico.
- **Margem:** bruta ~85%; net alto se auto-serviço.
- **% automatizável:** 80%+. **AI leverage:** onboarding/config assistido, agrupamento de crashes.
- **MVP:** 8–12 semanas (Tauri-first, ecossistema com menos oferta madura).
- **Primeiros clientes:** comunidades Tauri/Rust, Discord, "Show HN", listas awesome-tauri.
- **Competição:** Keygen (licenciamento), CrabNebula (distribuição Tauri), ToDesktop (Electron), Sentry (crash). Ninguém entrega o bundle integrado barato. Risco: mercado nichado e clonável — defensibilidade vem de integração + comunidade, não de código.
- **Scores:** Blue Ocean 6 · Pain 6 · WTP 6 · Automation 8 · AI leverage 5 · Defensibility 5 · Ease 7 · Distribution 6 · Competition 6 · Longevity 7 · Founder fit 9 · Ikigai fit 8.

#### #3 — B2B tributário para contadores de investidores PF (doméstico + exterior)
- **Cliente:** contadores/escritórios que declaram renda variável e ativos no exterior de dezenas/centenas de clientes.
- **Problema:** cada cliente é uma planilha; renda variável mensal + exterior anual é trabalhoso e propenso a erro.
- **Solução:** painel multi-cliente por-CPF que consolida B3 + exterior, gera DARFs, demonstrativos e export DIRPF em lote.
- **Pricing:** por-CPF (1 crédito = 1 CPF/ano-calendário) ou assinatura. **Revenue:** 100 contadores × 50 CPFs × R$30 = R$150k, escalando por volume. **Margem:** ~85% bruta.
- **Competição:** a Grana já opera um plano B2B para contadores no modelo pay-per-client. Entrar exige diferenciação no EXTERIOR (onde a oferta é fraca) — daí a sinergia com a #1.
- **Scores:** Pain 7 · WTP 7 · Defensibility 6 · Competition 6 · Ikigai fit 6. (Melhor como extensão da #1 do que negócio isolado.)

#### #4 — Localização com contexto via LLM lendo arquivos do jogo
- **Cliente:** estúdio indie que localiza para 5–12 idiomas com orçamento apertado.
- **Problema:** ferramentas atuais (Crowdin, Lokalise, Gridly) tratam strings com contexto limitado do jogo; qualidade sofre e tradução humana é cara (de poucos milhares a dezenas de milhares de dólares por título).
- **Solução:** pipeline que lê arquivos do jogo (diálogos, variáveis, limites de UI) para dar contexto ao LLM, com glossário e QA de comprimento.
- **Por que pagariam:** a própria Gridly (fornecedor — viés) afirma que a IA reduz o custo de localização em 2–4x e acelera em até 80%, viabilizando mercados antes inacessíveis para indies.
- **Pricing:** US$29–99/mês + repasse de tokens.
- **Competição:** Gridly/Crowdin/Lokalise já têm IA. O diferencial "contexto lendo arquivos" é clonável; defensibilidade fraca.
- **Scores:** Pain 6 · WTP 5 · Competition 7 · Defensibility 4 · Ikigai fit 8. (Paixão alta, economia fraca — bom projeto lateral, não aposta principal.)

#### #5 — Serviço produtizado de porting/decompilação AI-assisted (subcontrato)
- **Cliente:** publishers/detentores de catálogo retrô e estúdios de re-release sobrecarregados.
- **Problema:** backlogs de catálogo que os detentores "não sabem o que fazer" (Joe Modzeleski/LRG); decompilação e port são caros e lentos.
- **Solução:** oferecer capacidade de porting/decompilação acelerada por IA como subcontrato high-ticket, ou licenciar uma toolchain de decompilação.
- **Pricing:** contratos de US$10k–100k por título/plataforma.
- **Por que este fundador:** systems programming em Rust, WebGL/GPU, emulação — fit técnico excepcional; decompilação assistida por IA (LLM4Decompile; 96% de match relatado) amplifica a produtividade de uma equipe mínima.
- **Barreira:** relação com IP holders e NDAs de console — problema comercial, não técnico. Receita lumpy, não recorrente.
- **Scores:** Pain 6 · WTP 7 · Defensibility 6 · Distribution (10=pior) 8 · Ikigai fit 10 · Longevity 6. (Máximo ikigai; pior distribuição/previsibilidade.)

### Unit economics simplificado (melhor ideia — #1)
Cenário base (ano 2, consumer-led):
- **Receita:** 4.000 assinantes × R$249 = **R$996k/ano.**
- **Custos:** infra + dados PTAX ~R$36k; LLM (parsing) ~R$40k; suporte sazonal (mar–mai) ~R$80k; aquisição (SEO/conteúdo, majoritariamente orgânico) ~R$60k.
- **Lucro líquido do fundador:** **~R$780k/ano (~US$140k)** com operação de 1–2 pessoas. Escalando para 8–10k assinantes + camada B2B de contadores, alcança US$500k–1M+ ARR.

## Recommendations
**Estágio 0 (agora, 2–3 semanas):** validar a dor da #1 com 15–20 entrevistas em comunidades de investidor internacional (Reddit r/investimentos, grupos Avenue/Nomad) e um teste de SEO/landing com waitlist. **Benchmark para avançar:** ≥100 e-mails em 3 semanas e ≥5 pessoas dizendo "pagaria hoje".

**Estágio 1 (semanas 3–12):** construir o MVP da #1 (calculadora PTAX + importadores das 2 corretoras dominantes + export DIRPF) e lançar consumer a R$199–249/ano antes da temporada de IR. **Benchmark:** 300 pagantes na primeira temporada.

**Estágio 2 (paralelo/back-burner):** protótipo da #2 (Ship Desktop, Tauri-first) como segundo motor de receita com CAC orgânico. Só priorizar se a #1 não atingir 300 pagantes.

**Estágio 3 (ano 2):** abrir a camada B2B para contadores (#3), reusando o motor da #1 — expansão natural de alto ticket.

**O que mudaria o plano:** se a Receita simplificar radicalmente a apuração do exterior (auto-cálculo total no e-CAC), o valor da #1 cai — pivotar para consolidação/otimização e camada B2B. Se a Grana lançar módulo de exterior robusto antes do MVP, competir por UX/preço ou focar B2B.

**Ideias a NÃO perseguir:** LiveOps self-host, tooling Bevy pago, PFM consumer genérico, frontends de emulação, site/app builder genérico, robôs de análise de ações.

## Caveats
- **Tese picks-and-shovels:** confirmada em games/retro/dev tools; parcialmente refutada em finanças/investimento PF, onde o consumer comprovadamente paga (Grana, Velotax).
- **Números de vendors têm viés:** claims de "2–4x mais barato / 80% mais rápido" em localização vêm da Gridly (fornecedor). Contagens de clientes de corretoras misturam contas totais vs. investidores ativos — a base de investidores reais no exterior é da ordem de **centenas de milhares**, não milhões (documentos do Itaú citam ~229 mil clientes ativos / ~492 mil contas habilitadas na Avenue, contra o "1 milhão" de marketing; na Nomad, só ~10% da base investe).
- **Receita da Keygen não é pública** — sabe-se apenas que ultrapassou o salário do fundador (~2021) e é lucrativa/bootstrapped; use como prova de conceito, não benchmark preciso.
- **Preço exato do plano B2B por-CPF da Grana não é público** — requer cadastro; o modelo está confirmado como pay-per-client com 1 crédito grátis.
- **Custos de porting (US$10k–100k)** variam muito; há outlier de vendor (Kokku, US$4–6k/plataforma).
- **Projeção "≥20% até 2034"** de investidores brasileiros no exterior circula via material da Nomad e não foi confirmada em fonte primária ANBIMA; trate como estimativa de fornecedor. O que é sólido: a internacionalização hoje é mínima (~1,5–2,5% dos ativos), o que sustenta a tese de crescimento.
- **Risco regulatório da #1 é baixo mas não nulo:** cálculo tributário não é recomendação de investimento (sem CVM), mas mudanças na Lei 14.754 e nas INs da Receita exigem manutenção constante.
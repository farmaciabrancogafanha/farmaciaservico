# Prompt para sessões de IA — Farmácia de Serviço

Contexto e regras para qualquer modelo de IA (Claude, ChatGPT, Gemini) que ajude a manter o projecto. Carregar este ficheiro como instrução inicial.

## Em uma frase

Site estático num único `index.html` que mostra, em tempo real, a farmácia de serviço dos concelhos de Ílhavo e Aveiro. Dados lidos de ficheiros por ano `farmacias-AAAA.json` e `calendario-AAAA.json`, com um índice `anos.json` a listar os anos publicados. Hospedado em Netlify com auto-deploy a partir do GitHub.

## Restrição central

A IA não tem acesso de escrita ao repositório. Todas as alterações são entregues ao Daniel como ficheiros completos; ele faz upload manual no GitHub web.

## Workflow padrão de uma sessão

1. No início da sessão, o Daniel faz upload do `index.html` actual do repo (NÃO confiar em versões antigas guardadas pela IA)
2. A IA inspecciona o ficheiro real com ferramentas (view, grep, etc.), nunca assume a estrutura de memória
3. Para cada alteração: editar uma cópia do ficheiro e devolver o `index.html` completo para download
4. Sugerir uma mensagem de commit em inglês, ≤ 50 caracteres, no tempo presente
5. O Daniel faz commit no GitHub web; o Netlify deploya em cerca de 60 segundos
6. Depois do deploy, confirmar que a alteração está mesmo em produção (ver "Verificação pós-deploy"). Não assumir que o commit foi bem feito só porque o ficheiro foi gerado

## URLs

- Canónico: https://farmaciaservicoilhavoaveiro.netlify.app/
- Para parceiros, sem Linktree: https://farmaciaservicoaveiroilhavo.netlify.app/
- Modo TV horário: https://farmaciaservicoilhavoaveiro.netlify.app/farmaciabranco/horario
- Estatística: https://farmaciaservicoilhavoaveiro.netlify.app/farmaciabranco/estatistica
- Estatística com sub-rotas (ano / mês ou semana): `/farmaciabranco/estatistica/2026`, `/farmaciabranco/estatistica/2026/janeiro`, `/farmaciabranco/estatistica/2026/semana01`
- Mapa de links: https://farmaciaservicoilhavoaveiro.netlify.app/farmaciabranco/mapa
- Repositório: https://github.com/farmaciabrancogafanha/farmaciaservico

As rotas `/farmaciabranco/*` são geridas por reescritas do Netlify em `_redirects` (200 rewrite, não 301). O `index.html` detecta o pathname e activa o overlay respectivo. As sub-rotas da estatística usam separador de barra (`/2026/janeiro`), formato hierárquico ano / mês ou semana; o nome do mês vai em minúsculas e sem acentos. A barra de endereço actualiza-se com `history.pushState` à medida que se navega entre vistas.

## Estilo de resposta esperado

- PT-PT estrito, não pt-BR
- Directo, racional, sem embelezamento; big-picture antes do detalhe
- Tom adulto, cético, questionador
- Listas mecanísticas com lógica explícita; explicitar entrelinhas
- Não usar travessão `-` como pausa; usar vírgula ou ponto e vírgula
- Notação matemática deve ser explicitada quando usada
- Para Git e GitHub: passos simples, o Daniel não é programador

## Stack e convenções de código

- HTML5 semântico, CSS e JavaScript no mesmo `index.html`
- Sem frameworks, sem bundler, sem dependências externas
- Hora sempre em Europe/Lisbon via `Intl.DateTimeFormat`. Nunca usar `Date.getHours()` directo (evita drift UTC)
- Constantes de cor centralizadas: `COR_DECORRIDO`, `COR_DISP`, `COR_ALERTA`, etc.
- Caminhos para fetch dos JSON são absolutos (`/farmacias-AAAA.json`, `/calendario-AAAA.json`, `/anos.json`) para funcionar em qualquer pathname
- Commits atómicos: uma alteração lógica por commit

## Dados (multi-ano)

O site é multi-ano. Cada ano tem o seu par de ficheiros, e um índice lista os anos publicados.

| Ficheiro | Conteúdo |
|---|---|
| `anos.json` | Índice dos anos publicados: `{ "anos": [2026, 2027, ...] }`. Editado à mão quando se publica um ano novo |
| `farmacias-AAAA.json` | Catálogo de farmácias dos dois concelhos com nome, morada, coordenadas, telefone, e escala mensal de serviço do ano AAAA |
| `calendario-AAAA.json` | Feriados nacionais e municipais aplicáveis aos concelhos no ano AAAA. Inclui o campo `"ano"` |

Disponibilidades da Farmácia Branco são lidas exclusivamente do `farmacias-AAAA.json`; não duplicar no `calendario-AAAA.json`.

### Selecção do ano

- A página principal e a TV usam o ano corrente (data de Lisboa).
- A estatística usa o ano do URL (`/estatistica/2026`) ou, sem ano, o corrente.
- Se um ano pedido não existir em `anos.json`, o código recua para o último ano disponível menor ou igual ao pedido, ou para o primeiro da lista, para o site nunca ficar vazio.
- O seletor de ano (na listagem e na vista de ano) só aparece se houver mais do que um ano publicado.

### Virada de ano (TV)

A TV mantém-se aberta e só recarrega às 9h05. À meia-noite de 1 de Janeiro, o tique de cada segundo detecta que a data passou para um ano diferente do carregado e, se estiver a mostrar o ano corrente (não um arquivo escolhido por URL), faz `location.reload()` para apanhar os dados do ano novo.

## Regimes de serviço

| Concelho | Regime | Horário |
|---|---|---|
| Aveiro | Permanência | 24h, muda às 9h |
| Ílhavo | Disponibilidade | 9h às 21h |

## Modo TV em montra

- Hardware: Sony Bravia FW-43BZ35F, Android 9, com Fully Kiosk Browser em modo Fullscreen
- A largura e a altura CSS reportadas pelo browser **dependem das definições do Android** (Tamanho do ecrã / Display size, Smallest width nas Opções de programador) e também de qualquer Zoom forçado no Fully Kiosk. Valores observados no passado: `960×539` (com `dpr:2`, breakpoint `base`) e `1024×575` (com `dpr:2`, breakpoint `médio`). Logo, não fixar nenhum valor concreto como referência permanente
- O ecrã físico é **3840×2160 (4K UHD nativo)**. O canvas lógico do Android é menor (`largura_CSS × DPR`, ex.: 2048×1150 com 1024×575 a `dpr:2`) e depois é escalado para a resolução física pela TV com factor não-inteiro. Em modo de utilização à distância da montra, esta perda de nitidez é imperceptível, mas existe margem para configurar a TV de modo a obter CSS 1920×1080 a `dpr:2` (canvas lógico 3840×2160, mapeamento 1:1 com o físico, breakpoint `TV`)
- Cada página tem no fundo uma linha de diagnóstico com o formato `LxA dpr:N.NN (fis:WxH) mq:breakpoint`, **única fonte fidedigna** para confirmar a configuração corrente. `fis` é o canvas lógico (`largura_CSS × DPR`), não a resolução física do ecrã. Aparece no rodapé do site principal e do overlay do horário
- Duas TVs na montra:
  - TV 1: `/farmaciabranco/horario` → horário semanal em ecrã cheio
  - TV 2: `/?montra=1` → site de serviço em modo montra (esconde a nota legal de custo das chamadas; ver "Modo montra" abaixo)

## Relógio analógico militar (site principal)

O site principal tem um relógio analógico de 24 horas (SVG). O 24 (= 0h) fica no topo, o 9 fica em baixo à direita; o ponteiro das horas dá uma volta completa em 24h. Convenção angular do código: `ângulo = hora × 15° − 90°`, 0° à direita, sentido horário.

Elementos do mostrador:
- Bezel preto (r=95 a r=98) e mostrador branco (r=95)
- Anel exterior de progresso do turno de permanência 24h (`#setor-decorrido` cinzento + `#setor-falta` verde dinâmico)
- Anel interior de progresso da disponibilidade 9h-21h (`#setor-disponibilidade-decorrido` + `#setor-disponibilidade` laranja)
- Display digital LED a vermelho no centro, com a data civil por baixo

### Datas e arcos exteriores (janela 19h45-9h)

Entre as 19h45 e as 9h00 (transição de turno de permanência), o grupo `#grupo-data-prox` fica visível e mostra:
- Data do próximo turno (amarelo `#fff59d`): em cima do 24 e junto ao 9
- Data do turno actual (azul `#bbdefb`): junto ao 9, abaixo da amarela
- Dois arcos exteriores ao bezel (r=98 a r=101, mesma espessura 3px do bezel), `<path>` estáticos dentro de `#grupo-data-prox`:
  - **Azul** (`#bbdefb`, turno/data actual): do 9 (135°) ao 24 (360°) no sentido horário, passando pelo fundo e pela esquerda
  - **Amarelo** (`#fff59d`, próximo turno): do 24 (0°) ao 9 (135°) pela direita
  - Fronteiras exactamente no 24 (topo) e no 9

Os arcos não têm lógica JS própria; herdam a visibilidade do `display` de `#grupo-data-prox`, controlado em `atualizarDataProximoTurno`. Para testar fora da janela horária, redefinir o construtor `Date` na consola do browser (sem alterar o relógio do sistema).

A data civil debaixo do display digital está SEMPRE visível: fundo amarelo entre 0h e 9h (calendário já avançou mas turno ainda é o de ontem), fundo azul entre 9h e 24h (data corrente do turno).

## Horário da Farmácia Branco

Regras consoante o tipo de dia (em vigor à data deste prompt; verificar `index.html` se houver dúvidas):

| Tipo de dia | Manhã | Almoço | Tarde |
|---|---|---|---|
| Útil sem disponibilidade | 9h-13h | 13h-14h | 14h-20h |
| Útil com disponibilidade | 9h-13h | 13h-14h | 14h-**21h** |
| Sábado sem disponibilidade | 9h-13h | 13h-15h | 15h-19h |
| Sábado com disponibilidade | 9h-13h | 13h-15h | 15h-**21h** |
| Domingo sem disponibilidade | — fechado — | | |
| Domingo com disponibilidade | 9h-13h | 13h-15h | 15h-21h |
| Feriado sem disponibilidade | — fechado — | | |
| Feriado com disponibilidade | 9h-13h | 13h-15h | 15h-21h |

Todos os dias em regime de disponibilidade ("alargado") terminam às 21h. As horas "extra" em relação ao horário normal são a base para a coloração laranja descrita abaixo.

## Tarefas recorrentes

### Publicar um ano novo
Quando os dados de um ano novo estiverem prontos (antecipadamente, antes de o ano começar, para a TV não ficar sem horário):
1. Criar `calendario-AAAA.json` com `{ "ano": AAAA, "feriados": ["AAAA-MM-DD", ...] }`
2. Criar `farmacias-AAAA.json` com o catálogo e a escala do ano
3. Editar `anos.json` acrescentando o ano à lista: `{ "anos": [..., AAAA] }`

O seletor de ano, o mapa de links e as sub-rotas passam a incluir o ano novo automaticamente.

### Actualizar a escala mensal de um ano
Editar o `farmacias-AAAA.json` do ano em causa, nos arrays `calendario.aveiro` e `calendario.ilhavo`. Cada entrada: `{ "data": "AAAA-MM-DD", "farmacias": ["slug", ...] }`. Cada slug deve constar no catálogo `farmacias.aveiro` ou `farmacias.ilhavo`.

### Mensagens de commit
- Em inglês
- Tempo presente (add, fix, remove, change)
- Máximo 50 caracteres
- Atómicas

Exemplos: `add holidays for 2027`, `fix saturday lunch window`

## Paleta exaustiva do overlay do horário

A tabela do overlay (`/farmaciabranco/horario`) tem três variáveis ortogonais que combinam para determinar a cor de cada célula:

1. **Dia em vigor ou não.** A coluna do dia em vigor é destacada. Classes JS: `.hoje` na coluna do dia.
2. **Estado temporal dentro do dia em vigor.** Antes das 8h e depois das 21h, toda a coluna fica baça. Entre 8h-21h, divide-se em passada/em curso/futura. Classes JS: `.hoje-passada` para horas já passadas, `.hoje-actual` para a hora corrente; sem nenhuma destas é futura.
3. **Estado funcional da célula.** Determinado pelo horário Branco do dia: `aberto` (Aberta), `almoco` (Almoço), `fechado` (Fechada). Adicionalmente, `.extra` se for uma hora Aberta que só existe por causa do regime alargado (não estaria aberta no horário normal do dia da semana). E `.ultima-hora` para a célula 20h-21h em curso num dia alargado.

### Cores principais (constantes)

| Constante | Hex | Significado |
|---|---|---|
| Verde institucional | `#2e7d32` | Aberta no dia em vigor, hora viva |
| Verde médio | `#7faa83` | (não usado actualmente; reservado) |
| Verde pastel | `#c8e6c9` | Aberta em qualquer outro contexto baço |
| Cinzento médio (`COR_DECORRIDO`) | `#9e9e9e` | Almoço/Fechada no dia em vigor, hora viva. Também sectores decorridos do relógio analógico e chip "Fechou às 21h". |
| Cinzento claro | `#eeeeee` | Almoço/Fechada em qualquer outro contexto baço |
| Laranja institucional (`COR_DISP`) | `#ff9800` | Aberta extra no dia em vigor, hora viva. Também chip "Alargado" no cabeçalho e linha "Restam Xh Xmin até ao fecho" no site principal. |
| Laranja pastel | `#ffe0b2` | Aberta extra em qualquer outro contexto baço |
| Vermelho alerta (`COR_ALERTA`) | `#d32f2f` | Aberta extra do dia em vigor durante 20h00-20h59 (última hora antes do fecho). Também chip "Fechou às 21h" e linha "Restam..." vermelha no site principal durante a mesma janela. |

### Matriz completa: cor da célula consoante o contexto

Para cada combinação dia × estado funcional × estado temporal, qual a cor de fundo da célula:

| Estado da célula | Dia em vigor 9h-19h | Dia em vigor 20h-21h | Dia em vigor passada | Dia em vigor antes 8h ou depois 21h | Outros dias |
|---|---|---|---|---|---|
| Aberta normal | Verde `#2e7d32` | Verde `#2e7d32` | Verde pastel `#c8e6c9` | Verde pastel `#c8e6c9` | Verde pastel `#c8e6c9` |
| Aberta extra | Laranja `#ff9800` | **Vermelho `#d32f2f`** (se 20h-21h em curso) | Laranja pastel `#ffe0b2` | Laranja pastel `#ffe0b2` | Laranja pastel `#ffe0b2` |
| Almoço | Cinzento `#9e9e9e` | Cinzento `#9e9e9e` | Cinzento `#eeeeee` | Cinzento `#eeeeee` | Cinzento `#eeeeee` |
| Fechada | Cinzento `#9e9e9e` | Cinzento `#9e9e9e` | Cinzento `#eeeeee` | Cinzento `#eeeeee` | Cinzento `#eeeeee` |

### Regra "Aberta extra"

Uma hora é considerada "extra" se e só se:
- O dia em causa está em regime alargado (Branco em disponibilidade nesse dia)
- A hora seria Fechada no horário normal desse dia da semana

Exemplos práticos:

| Dia | Horas extra |
|---|---|
| Útil alargado (ex: 4ª feira disp) | 20h-21h (1 hora; o normal terminaria às 20h) |
| Sábado alargado | 19h-20h e 20h-21h (2 horas; o normal terminaria às 19h) |
| Domingo alargado | 9h-13h e 15h-21h (10 horas; o normal seria fechado todo o dia) |
| Feriado alargado | 9h-13h e 15h-21h (10 horas; o normal seria fechado todo o dia) |

### Regra "Última hora antes do fecho"

A célula 20h-21h num dia alargado fica vermelha **apenas durante essa hora** e **apenas na coluna do dia em vigor**. Mecanismo: a classe `.ultima-hora` é adicionada pelo JavaScript em `tickEstadoHoje` quando `extra && hCelula === 20`. Quando a hora passa (a partir das 21h), a célula transita para o laranja pastel das outras horas extra passadas.

Esta regra sincroniza com o "Restam Xh Xmin até ao fecho" do site principal, que também usa `COR_ALERTA` exclusivamente durante a hora 20h00-20h59.

### Texto das células

Todas as células do dia em vigor têm:
- Cor do texto: branco
- Peso: bold

Em três situações o texto não aparece:
- Antes das 8h ou depois das 21h (toda a coluna)
- Horas já passadas do dia (ficam pastel sem texto)
- Em colunas que não são o dia em vigor

A célula da hora corrente (`hoje-actual`) é dividida em duas metades horizontais por uma linha preta de 2px. Acima da linha, a célula está pintada com a cor "baça" do estado funcional (verde pastel, laranja pastel ou cinzento claro), igual à cor que essa célula terá depois quando passar a `hoje-passada`. Abaixo da linha, a célula está pintada com a cor "viva" do estado funcional (verde institucional, laranja, vermelho na última hora, ou cinzento médio). A posição vertical da linha é proporcional aos minutos decorridos dentro da hora: 0% nos primeiros segundos, 100% nos últimos. Ao fim da hora, a parte baça preencheu toda a célula e a transição para `hoje-passada` é visualmente contínua.

## Página de estatística (`/farmaciabranco/estatistica`)

Overlay que tapa o site, com a análise da distribuição de horas de funcionamento semanal da Branco. Estrutura:

- Histograma da distribuição de horas por semana, com média, mediana e moda. Clicar numa coluna filtra a listagem por esse total de horas.
- Listagem das semanas do ano, com colunas Semana, Mês, Datas, Dias de funcionamento, Horas de funcionamento e Causa. Cabeçalhos clicáveis ordenam; números de semana a dois dígitos.
- Células clicáveis: Semana e Datas abrem o horário dessa semana; Mês abre a vista de mês (meses compostos têm um link por mês); Dias, Horas e Causa filtram a listagem por esse critério. Em filtro, o histograma realça as colunas afectadas, mostrando a fracção filtrada em cor viva sobre o total baço.
- Linha de total no fundo; no modo "todas" mostra o intervalo de datas do ano.
- Vista de semana, de mês e de ano (ver a seguir). O número da semana segue a norma ISO 8601. As horas contam só porta aberta, sem almoços.

### Definições de contagem

- Semana: "dias" e "horas" são da própria semana (segunda a domingo).
- Mês e ano: contados dia a dia de calendário (não somando semanas), para as semanas de fronteira não duplicarem. "Dias de funcionamento" = dias do período com pelo menos uma hora de porta aberta.
- O histograma e as estatísticas usam só as semanas completas dentro do ano; as de fronteira aparecem na listagem como incompletas, fora do histograma.

### Vistas de horário

- Semana: tabela de horário 8h-22h × sete dias dessa semana. Dias fora do ano (ou de outro mês, na vista de mês) aparecem esbatidos.
- Mês: as tiras de horário de todas as semanas que tocam o mês, lado a lado, com quebra de linha quando não cabem. A coluna de horas aparece só na primeira tira de cada fila (cálculo por JS, recalculado no resize). Definição de mês: semanas cuja janela real intersecta o mês, por isso uma semana de fronteira aparece nos dois meses.
- Ano: uma faixa por mês, cada uma com cabeçalho (nome do mês, dias e horas) e as suas tiras. Aberta pelo "2026" clicável no subtítulo ou pelo "Total" da listagem.

### Navegação e cabeçalho

- No topo, acima da linha verde, o subtítulo dinâmico ("Tempo de funcionamento - AAAA", "Horário do mês - AAAA/MM", "Horário do ano - AAAA", "Semana NN - Horário"), e por baixo a linha de dias e horas quando aplicável.
- No canto superior direito, empilhados e com aparência homogénea, os botões de navegação: "Mapa do Site" (avanço), "Voltar à tabela" e "Voltar ao Horário desta semana" (regresso). Cada um aparece só quando faz sentido na vista corrente.
- O seletor de ano aparece sob o subtítulo quando há mais do que um ano.

## Página de mapa (`/farmaciabranco/mapa`)

Índice de todas as rotas do projecto, com links absolutos para o domínio canónico. Tem uma secção de páginas principais (fixas) e, por cada ano publicado, uma secção com os meses e, para o ano carregado, as semanas. Os meses e semanas são derivados dos dados, por isso acompanham mudanças. Só vive no domínio canónico (escondido nos domínios de parceiros, como o resto das rotas `/farmaciabranco/*`).

## Notas sobre infraestrutura

### Reescritas Netlify

O ficheiro `_redirects` na raiz do repo define:

```
/farmaciabranco/horario        /index.html   200
/farmaciabranco/horario/       /index.html   200
/farmaciabranco/estatistica    /index.html   200
/farmaciabranco/estatistica/   /index.html   200
/farmaciabranco/estatistica/*  /index.html   200
/farmaciabranco/mapa           /index.html   200
/farmaciabranco/mapa/          /index.html   200
```

A regra-curinga `/farmaciabranco/estatistica/*` cobre as sub-rotas de ano, mês e semana. Estas reescritas são servidas com status 200 (não 301/302), portanto o URL na barra do browser mantém-se enquanto o conteúdo servido é o `index.html` da raiz.

### Verificação pós-deploy

Regra de ouro: um ficheiro gerado e "entregue" não é um ficheiro em produção. Entre os dois há dois passos manuais (o commit no GitHub web e o deploy do Netlify) onde a alteração se pode perder. Já aconteceu mais do que uma vez submeter ao GitHub uma versão antiga do ficheiro, ou submeter um ficheiro e esquecer outro, ficando a alteração por publicar sem ninguém dar conta.

Por isso, depois de cada commit, confirmar em duas frentes:

1. **O deploy correu (o "envelope").** Confirmar que existe um deploy de produção recente, bem-sucedido, do commit certo, sem erros de build nem de redirects. Quando o conector Netlify está disponível, a IA consegue confirmar isto de forma autónoma (estado, data, commit, resumo do deploy). O conector NÃO dá o conteúdo do ficheiro servido, apenas os metadados do deploy.

2. **O conteúdo está lá (o "recheio").** A confirmação visual é sempre do Daniel, no browser: abrir o site em produção (não o ficheiro local) e verificar que a alteração aparece de facto. Para alterações dependentes da hora (ex.: arcos das datas, visíveis só entre 19h45 e 9h), simular a hora na consola do browser redefinindo o construtor `Date`, sem mexer no relógio do sistema.

Nenhuma das duas frentes substitui a outra: o deploy pode estar verde com o conteúdo errado lá dentro, e o conteúdo certo pode estar no ficheiro local mas nunca ter sido submetido. Confirmar as duas.

### Detecção do modo horário

No `index.html`, a função `inicializarHorario()` activa o overlay se e só se:

```javascript
/^\/farmaciabranco\/horario\/?$/.test(location.pathname)
```

O parâmetro de query antigo `?horario=1` foi descontinuado.

### Modo montra (`?montra=1`)

A TV2 da montra mostra o site principal no mesmo URL `/` que qualquer visitante. Como não há forma de distinguir a TV de um visitante normal, o Start URL do Fully Kiosk da TV2 inclui o parâmetro `?montra=1`. No arranque, o `index.html` lê esse parâmetro e adiciona a classe `modo-montra` ao `<body>`.

Essa classe serve para esconder, via CSS, elementos que só fazem sentido no sítio da Internet mas não num display físico visto da rua. Actualmente esconde apenas a nota legal de custo das chamadas (`.nota-custo-chamada`, ver abaixo). Visitantes normais (sem o parâmetro) vêem tudo.

Nota: o nome `?montra=1` já foi usado no passado para um mecanismo de poster overlay, entretanto removido. O significado actual é apenas marcar o ecrã como TV de montra.

### Nota legal de custo das chamadas

O Decreto-Lei 59/2021, revisto pela Lei 14/2023 (aplicável desde 7 de Abril de 2023), obriga a indicar, junto de cada número de contacto no sítio da Internet, o custo da chamada ou, em alternativa, o tipo de rede. Cada número nos cartões de farmácia tem por isso um `<span class="nota-custo-chamada">` com a frase "Chamada para a rede fixa nacional" (números fixos/geográficos) ou "Chamada para a rede móvel nacional" (móveis, `+3519`). A frase é tipograficamente discreta (10px, cinzento claro, sem negrito) e é escondida no modo montra. A distinção fixo/móvel é automática no render, em `renderFarmacias`.

### Hostnames especiais

A constante `HOSTNAMES_SEM_LINKTREE` (no JS) lista hostnames onde o bloco do Linktree da Branco é escondido. Pretende-se que os sites partilhados com farmácias parceiras (URL alternativo) não apresentem a marca Branco.

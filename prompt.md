# Prompt para sessões de IA — Farmácia de Serviço

Contexto e regras para qualquer modelo de IA (Claude, ChatGPT, Gemini) que ajude a manter o projecto. Carregar este ficheiro como instrução inicial.

## Em uma frase

Site estático num único `index.html` que mostra, em tempo real, a farmácia de serviço dos concelhos de Ílhavo e Aveiro. Dados lidos de `farmacias.json` e `calendario.json`. Hospedado em Netlify com auto-deploy a partir do GitHub.

## Restrição central

A IA não tem acesso de escrita ao repositório. Todas as alterações são entregues ao Daniel como ficheiros completos; ele faz upload manual no GitHub web.

## Workflow padrão de uma sessão

1. No início da sessão, o Daniel faz upload do `index.html` actual do repo (NÃO confiar em versões antigas guardadas pela IA)
2. A IA inspecciona o ficheiro real com ferramentas (view, grep, etc.), nunca assume a estrutura de memória
3. Para cada alteração: editar uma cópia do ficheiro e devolver o `index.html` completo para download
4. Sugerir uma mensagem de commit em inglês, ≤ 60 caracteres, no tempo presente
5. O Daniel faz commit no GitHub web; o Netlify deploya em cerca de 60 segundos

## URLs

- Canónico: https://farmaciaservicoilhavoaveiro.netlify.app/
- Para parceiros, sem Linktree: https://farmaciaservicoaveiroilhavo.netlify.app/
- Modo TV horário: https://farmaciaservicoilhavoaveiro.netlify.app/farmaciabranco/horario
- Repositório: https://github.com/farmaciabrancogafanha/farmaciaservico

A rota `/farmaciabranco/horario` é gerida por reescrita do Netlify em `_redirects` (200 rewrite, não 301). O `index.html` detecta o pathname e activa o overlay do horário.

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
- Caminhos para fetch dos JSON são absolutos (`/farmacias.json`, `/calendario.json`) para funcionar em qualquer pathname
- Commits atómicos: uma alteração lógica por commit

## Dados

| Ficheiro | Conteúdo |
|---|---|
| `farmacias.json` | Catálogo de farmácias dos dois concelhos com nome, morada, coordenadas, telefone, e escala mensal de serviço |
| `calendario.json` | Feriados nacionais e municipais aplicáveis aos concelhos |

Disponibilidades da Farmácia Branco são lidas exclusivamente de `farmacias.json`; não duplicar em `calendario.json`.

## Regimes de serviço

| Concelho | Regime | Horário |
|---|---|---|
| Aveiro | Permanência | 24h, muda às 9h |
| Ílhavo | Disponibilidade | 9h às 21h |

## Modo TV em montra

- Hardware: Sony Bravia FW-43BZ35F, Android 9, com Fully Kiosk Browser em modo Fullscreen
- A largura e a altura CSS reportadas pelo browser **dependem das definições do Android** (Tamanho do ecrã / Display size, Smallest width nas Opções de programador) e também de qualquer Zoom forçado no Fully Kiosk. Valores observados no passado: `960×539` (com `dpr:2`, breakpoint `base`) e `1024×575` (com `dpr:2`, breakpoint `médio`). Logo, não fixar nenhum valor concreto como referência permanente
- O ecrã físico tem 1920×1080 px. Se o DPR nominal for `2` mas o produto `largura_CSS × DPR` não der 1920, é porque o `devicePixelRatio` reportado está arredondado a partir de um valor fraccionário real (ex.: 1.875 arredondado para 2)
- Cada página tem no fundo uma linha de diagnóstico com o formato `LxA dpr:N.NN (fis:WxH) mq:breakpoint`, **única fonte fidedigna** para confirmar a configuração corrente. Aparece no rodapé do site principal e do overlay do horário
- Duas TVs na montra:
  - TV 1: `/farmaciabranco/horario` → horário semanal em ecrã cheio
  - TV 2: `/` → site de serviço

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

### Actualizar o calendário anual
No final de cada ano, regenerar `calendario.json` com os feriados do ano seguinte. Estrutura: `{ "feriados": ["YYYY-MM-DD", ...] }`.

### Actualizar a escala mensal
Quando publicada nova escala, editar `farmacias.json` adicionando entradas nos arrays `calendario.aveiro` e `calendario.ilhavo`. Cada entrada: `{ "data": "YYYY-MM-DD", "farmacias": ["slug", ...] }`. Cada slug deve constar no catálogo `farmacias.aveiro` ou `farmacias.ilhavo`.

### Mensagens de commit
- Em inglês
- Tempo presente (add, fix, remove, change)
- Máximo 60 caracteres
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

## Notas sobre infraestrutura

### Reescritas Netlify

O ficheiro `_redirects` na raiz do repo define:

```
/farmaciabranco/horario   /index.html   200
/farmaciabranco/horario/  /index.html   200
```

Esta reescrita é servida com status 200 (não 301/302), portanto o URL na barra do browser mantém-se `/farmaciabranco/horario` enquanto o conteúdo servido é o `index.html` da raiz.

### Detecção do modo horário

No `index.html`, a função `inicializarHorario()` activa o overlay se e só se:

```javascript
/^\/farmaciabranco\/horario\/?$/.test(location.pathname)
```

O parâmetro de query antigo `?horario=1` foi descontinuado.

### Hostnames especiais

A constante `HOSTNAMES_SEM_LINKTREE` (no JS) lista hostnames onde o bloco do Linktree da Branco é escondido. Pretende-se que os sites partilhados com farmácias parceiras (URL alternativo) não apresentem a marca Branco.

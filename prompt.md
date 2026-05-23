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
- Modo TV horário: https://farmaciaservicoilhavoaveiro.netlify.app/?horario=1
- Repositório: https://github.com/farmaciabrancogafanha/farmaciaservico

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
- Constantes de cor centralizadas: `COR_DECORRIDO`, `COR_DISP`, etc.
- Breakpoints CSS: mobile < 900px ou altura < 720px; base ≥ 900×500; médio 1024 a 1279px; grande 1280 a 1799px; TV ≥ 1800px
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

- Hardware: Sony Bravia FW-43BZ35F, Android 9, com Fully Kiosk Browser em modo Fullscreen, sem licença paga
- A TV reporta ao browser CSS 960 × 539 com DPR 2 (físico 1920 × 1080)
- Devido ao DPR alto, o CSS breakpoint que dispara é `base`, não `TV`. Comportamento esperado
- Cada página tem no fundo uma linha de diagnóstico `LARGURAxALTURA dpr:N mq:breakpoint`, útil para confirmar
- Duas TVs na montra:
  - TV 1: `?horario=1` → horário semanal em ecrã cheio
  - TV 2: sem parâmetros → site de serviço

## Tarefas recorrentes

### Actualizar o calendário anual
No final de cada ano, regenerar `calendario.json` com os feriados do ano seguinte. Estrutura: `{ "feriados": ["YYYY-MM-DD", ...] }`.

### Actualizar a escala mensal
Quando publicada nova escala, editar `farmacias.json` adicionando entradas nos arrays `calendario.aveiro` e `calendario.ilhavo`. Cada entrada: `{ "data": "YYYY-MM-DD", "farmacias": ["nome", ...] }`. Cada farmácia listada deve constar no catálogo `farmacias.aveiro` ou `farmacias.ilhavo`.

### Mensagens de commit
- Em inglês
- Tempo presente (add, fix, remove, change)
- Máximo 60 caracteres
- Atómicas

Exemplos: `add holidays for 2027`, `fix saturday lunch window`

## Estados visuais relevantes

### Coluna do dia em vigor no overlay do horário
| Hora corrente | Aspecto |
|---|---|
| Antes das 8h | Toda a coluna baça (cores dessaturadas) |
| Hora actual (8h a 21h) | Célula viva com linha horizontal preta a percorrer minutos |
| Horas passadas | Baças |
| Horas futuras | Vivas |
| Depois das 22h | Toda a coluna baça |

### Paleta de cinzentos (referência)
- `#eeeeee`: Almoço/Fechada em qualquer dia que não seja hoje
- `#c8c8c8`: Almoço/Fechada no dia em vigor em hora baça
- `#9e9e9e`: Almoço/Fechada no dia em vigor em hora viva (igual ao chip "Fechou às 21h" do site, via `COR_DECORRIDO`)

### Paleta de verdes (Aberta)
- `#c8e6c9`: Aberta em qualquer dia que não seja hoje
- `#7faa83`: Aberta no dia em vigor em hora baça
- `#2e7d32`: Aberta no dia em vigor em hora viva

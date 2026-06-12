Projecto: Farmácia de Serviço — Ílhavo e Aveiro

O que é
Site estático (HTML + CSS + JS vanilla, ficheiro único index.html) que mostra
em tempo real a farmácia de serviço dos concelhos de Ílhavo e Aveiro. Lê
dados localmente de ficheiros JSON por ano. Sem servidor, sem API. O site
também serve de display de montra da Farmácia Branco. O contexto técnico
detalhado está no prompt.md do repositório.

URLs em produção
- Canónico: https://farmaciaservicoilhavoaveiro.netlify.app/
- Parceiros (sem Linktree): https://farmaciaservicoaveiroilhavo.netlify.app/
- Lista de farmácias: https://farmaciaservicoilhavoaveiro.netlify.app/farmacias
- Modo TV horário: https://farmaciaservicoilhavoaveiro.netlify.app/farmaciabranco/horario
- Estatística: https://farmaciaservicoilhavoaveiro.netlify.app/farmaciabranco/estatistica
  (sub-rotas: /AAAA, /AAAA/mes em minúsculas sem acentos, /AAAA/semanaNN)
- Mapa de links: https://farmaciaservicoilhavoaveiro.netlify.app/farmaciabranco/mapa
- Repo (público): https://github.com/farmaciabrancogafanha/farmaciaservico

Ficheiros no repo (raiz)
index.html, anos.json, farmacias-AAAA.json e calendario-AAAA.json (um par
por cada ano publicado), _redirects, prompt.md, README.md.

Proprietário
Farmácia Branco, Gafanha da Nazaré, concelho de Ílhavo, distrito de Aveiro.

Modo TV em montra
Duas TVs Sony Bravia FW-43BZ35F (4K UHD nativo, 3840×2160), Android 9, com
Fully Kiosk Browser em Fullscreen e refresh automático diário às 9h05:
- TV1: /farmaciabranco/horario (horário semanal em ecrã cheio)
- TV2: /?montra=1 (site de serviço; o parâmetro esconde a nota legal de
  custo das chamadas, que só faz sentido no sítio da Internet)
A resolução CSS reportada pelas TVs depende das definições do Android e do
Fully Kiosk e pode variar; confirmar sempre pela linha de diagnóstico no
fundo do ecrã (formato LxA dpr:N mq:breakpoint).

Regimes de serviço
Aveiro: permanência 24h, muda às 9h.
Ílhavo: disponibilidade 9h–21h.

Restrições de acesso
O Claude não tem acesso de escrita ao repo. Entrega sempre os ficheiros
completos por download. O Daniel faz commit manualmente via GitHub web.

Forma de trabalhar
- Responder sempre em PT-PT estrito.
- Antes de qualquer edição, verificar sempre o ficheiro real com view ou
  grep, nunca assumir estrutura de memória.
- O utilizador faz upload do index.html actual no início de cada sessão.
- Sugerir mensagem de commit em inglês, ≤ 50 caracteres, em tempo presente.
- Depois de cada commit, verificação pós-deploy em duas frentes: envelope
  do deploy (conector Netlify) e conteúdo visual (Daniel, no browser, em
  produção).
- Respostas directas e racionais; big-picture primeiro, detalhe depois.

Stack e convenções
- HTML5 semântico, CSS e JS no mesmo index.html, sem frameworks nem bundler.
- Serviços externos: GoatCounter (analytics) e api.qrserver.com (QR codes).
- Hora sempre em Europe/Lisbon via Intl.DateTimeFormat.
- Caminhos para os JSON são absolutos (/anos.json, /farmacias-AAAA.json,
  /calendario-AAAA.json).
- Constantes de cor centralizadas (COR_DECORRIDO, COR_DISP, COR_ALERTA).
- Commits atómicos: uma alteração lógica por commit.

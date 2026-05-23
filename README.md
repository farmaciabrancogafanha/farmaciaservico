# Farmácia de Serviço — Concelhos de Ílhavo e Aveiro

Site estático que mostra em tempo real qual a farmácia de serviço nos concelhos de Ílhavo e Aveiro. Hora calculada em fuso Europe/Lisbon, dados lidos localmente de ficheiros JSON, sem servidor nem base de dados. Funciona como informação pública e como display em montra para a Farmácia Branco (Gafanha da Nazaré).

## URLs em produção

| URL | Para quem |
|---|---|
| https://farmaciaservicoilhavoaveiro.netlify.app/ | URL canónico, divulgado em QR codes e materiais |
| https://farmaciaservicoaveiroilhavo.netlify.app/ | URL alternativo para farmácias parceiras (sem Linktree da Branco) |
| https://farmaciaservicoilhavoaveiro.netlify.app/?horario=1 | TV em montra: horário semanal da Branco em ecrã cheio |

## Regimes de serviço

| Concelho | Regime | Horário |
|---|---|---|
| Aveiro | Permanência | 24 horas, muda às 9h |
| Ílhavo | Disponibilidade | 9h às 21h |

## Estrutura do repositório

| Ficheiro | Conteúdo |
|---|---|
| `index.html` | Aplicação completa: HTML, CSS e JavaScript num único ficheiro |
| `farmacias.json` | Catálogo de farmácias dos dois concelhos e escala mensal de serviço |
| `calendario.json` | Feriados nacionais e municipais aplicáveis |
| `prompt.md` | Contexto para sessões com IA (substituir e manter o projecto) |
| `README.md` | Este ficheiro |

## Stack

HTML5, CSS e JavaScript vanilla. Ficheiro único `index.html`, sem frameworks nem bundler. Hospedado em Netlify com auto-deploy a partir do GitHub.

## Manutenção

Ver `prompt.md`. Concentra o contexto necessário para retomar o trabalho com qualquer modelo de IA.

## Proprietário

Farmácia Branco, Gafanha da Nazaré, concelho de Ílhavo, distrito de Aveiro.

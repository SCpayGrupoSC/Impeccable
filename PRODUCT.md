# Product

## Register

product

## Platform

web

## Users
Vendedores e gestores de contas individuais de uma instituição financeira/adquirente, cada um acompanhando sua própria carteira de clientes (estabelecimentos comerciais). Uso majoritariamente no desktop, no navegador, sem backend: os dados vivem no `localStorage` do próprio dispositivo. O contexto de uso é operacional e recorrente — abrir a ferramenta, conferir o período, importar um novo arquivo de transações e agir sobre quem está sem volume.

## Product Purpose
Transformar exportações brutas de transações (arquivos .xlsx da Dock) em uma leitura imediata da saúde da carteira de clientes de um vendedor: quanto cada cliente gerou no período, quem está sem volume e precisa de atenção, e como a carteira se divide entre a base principal e agrupamentos promocionais/campanhas. Sucesso é o vendedor identificar em segundos onde agir, sem precisar cruzar planilhas manualmente.

## Positioning
Um rastreador de carteira comercial de arquivo único, sem backend: qualquer vendedor abre o HTML, importa o extrato da Dock, e em segundos vê exatamente quais clientes precisam de atenção.

## Brand Personality
Moderna e polida — deve parecer uma ferramenta interna bem cuidada, digna de confiança para decisões comerciais, sem carregar os clichês visuais genéricos de dashboards gerados por IA (cards idênticos, gradientes em texto, eyebrows em toda seção). O tom é preciso e direto, na linha do visual de "recibo" já presente no código: sóbrio, exato, sem ruído.

## Anti-references
Nenhuma referência negativa específica foi apontada; a diretriz é evitar os padrões genéricos de "AI slop" descritos no skill Impeccable (cards genéricos repetidos, gradiente em texto, glassmorphism decorativo, eyebrows/números de seção por reflexo, hero-metric template).

## Design Principles
- Clareza acima de decoração: todo valor precisa ser lido de relance, como um extrato bancário.
- Fricção zero: é um arquivo HTML único, sem instalação — a experiência de uso tem que continuar imediata.
- Acionável, não apenas informativo: destacar quem está sem volume é o trabalho principal da tela, não um dado secundário.
- Refinar o sistema existente, não descartá-lo: a paleta papel/tinta/shell e a dupla tipográfica (IBM Plex Sans + Mono) já são uma escolha deliberada; evoluir em vez de substituir.
- Polimento sem clichê de SaaS: moderno e cuidado, mas nunca genérico ao ponto de parecer "feito por IA".

## Accessibility & Inclusion
Padrão WCAG AA (contraste mínimo 4.5:1 para texto, 3:1 para texto grande/elementos gráficos). Sem requisito formal adicional além disso.

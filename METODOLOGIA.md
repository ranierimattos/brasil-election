# Metodologia e memória de cálculo

Este documento reproduz o bloco "Fontes e memória de cálculo" do [simulador do 2º turno](https://ranierimattos.github.io/brasil-election/segundo-turno/), para que qualquer pessoa possa revisar os números. Os valores são os mesmos usados no código ([`segundo-turno/index.html`](segundo-turno/index.html), constantes `FLAVIO`, `LULA`, `ELEITORADO`, `T1`, `ABSTENCAO`, `V1`, `INV2_A`, `INV2_B`, `MU`, `SIGMA`, `HIST` e `PADRAO`).

Encontrou um erro? [Abra uma issue](https://github.com/ranierimattos/brasil-election/issues/new).

## 1. Dados do 1º turno (fixos)

Fonte: resultado final da apuração do TSE, eleição 6257 (1º turno, presidente), 99,99% das seções, 04/10/2026 às 23:37:27 ([resultados.tse.jus.br](https://resultados.tse.jus.br/)).

| Candidato(s) | Votos |
|---|---:|
| Flávio Bolsonaro | 56.103.520 |
| Lula | 53.873.874 |
| Augusto Cury | 3.448.500 |
| Renan Santos | 2.675.872 |
| Ronaldo Caiado | 2.605.080 |
| Zema | 326.485 |
| Clariana Barão e Wilson Grassi | 56.919 |
| Samara, Hertz Dias, Edmilson Costa e Rui Costa Pimenta | 203.729 |
| **Soma (votos nominais), V1** | **119.293.979** |

| Indicador | Valor |
|---|---:|
| Eleitorado | 158.745.502 |
| Comparecimento (T1) | 125.268.748 |
| Abstenção | 33.467.898 (21,08% do eleitorado) |
| Brancos | 2.300.739 |
| Nulos | 3.668.784 |
| Inválidos (brancos + nulos) | 4,77% do comparecimento |

## 2. Cálculo do resultado

1. **Placar.** a2 = 21,08% + Δ, com Δ entre −3,5 e +3,5 pontos.
2. **Comparecimento.** T2 = T1 − Δ ÷ 100 × eleitorado.
3. **Votos válidos do 2º turno.** V2 = T2 × (1 − inválidos2). Os inválidos (brancos + nulos) do 2º turno vêm de uma regressão linear com as seis eleições de 2002 a 2022:

   inválidos2 = 2,0544 + 0,5152 × inválidos1 (em %; R² = 0,52; erro padrão de 1,28 ponto)

   Com 4,77% no 1º turno de 2026, projeta **4,51%** no 2º turno, o que equivale a um fator de 0,95 sobre o 1º turno. A média simples das razões (0,78) não foi usada: as eleições antigas partiam de bases de inválidos de 8% a 11% e caíam muito, enquanto em 2022, com base de 4,4%, os inválidos subiram; a regressão leva em conta o tamanho da base.
4. **Transferência.** Para cada candidato eliminado, com v votos: para Flávio v × f, para Lula v × l, não migra v × (1 − f − l). Migrados: F<sub>m</sub> = 56.103.520 + Σ v × f; L<sub>m</sub> = 53.873.874 + Σ v × l. N = soma dos "não migra".
5. **Restante.** R = V2 − (V1 − N). R > 0: eleitores que vão às urnas além dos migrados, inclusive brancos e nulos que passam a votar e abstenção que recua. R < 0: votos que somem. Os "não migra" já estão dentro de V2, sem contagem dupla, e F + L = V2 em qualquer cenário.
6. **Quem leva mais eleitores.** A barra define a participação de cada lado na mobilização: q<sub>Lula</sub> + q<sub>Flávio</sub> = 100%, cada uma entre 25% e 75%. Se R > 0, Lula recebe R × q<sub>Lula</sub> e Flávio R × q<sub>Flávio</sub>. Se R < 0, Lula perde |R| × q<sub>Flávio</sub> e Flávio perde |R| × q<sub>Lula</sub>: quem leva mais eleitores ganha mais e perde menos.
7. **Resultado.** F = F<sub>m</sub> + parte de Flávio; L = L<sub>m</sub> + parte de Lula; % de Flávio = F ÷ (F + L), em votos válidos.

### Probabilidade do placar

Chance de a variação real da abstenção ficar a ±0,25 ponto do valor escolhido, numa distribuição normal com média +1,0 (projeção central) e desvio 1,3 (desvio das seis variações históricas, 1,34):

P = Φ((v + 0,25 − 1,0) ÷ 1,3) − Φ((v − 0,25 − 1,0) ÷ 1,3)

### Projeção central +1,0

Variação média 2002–2022: +1,78; média 2014–2022: +0,77; regressão entre margem de vitória e variação (r = 0,65, seis pontos): 0,59 + 0,099 × margem, que dá +1,1 para margem de 5 pontos. Julgamento do autor, sem pretensão estatística.

## 3. Histórico da abstenção (1º → 2º turno)

| Ano | 1º turno | 2º turno | Variação (pontos) |
|---|---:|---:|---:|
| 2002 | 17,74% | 20,47% | +2,73 |
| 2006 | 16,75% | 18,99% | +2,24 |
| 2010 | 18,12% | 21,50% | +3,38 |
| 2014 | 19,39% | 21,10% | +1,71 |
| 2018 | 20,32% | 21,29% | +0,97 |
| 2022 | 20,95% | 20,59% | −0,36 |

- 1998: 21,49% em turno único.
- Média 2002–2022: +1,78; mediana: +1,97; média 2014–2022: +0,77.
- +8% relativo sobre 21,08% = +1,69 ponto.
- Em 2022 foi a primeira vez que o comparecimento subiu no 2º turno, associado à mobilização da polarização e à gratuidade de transporte em várias cidades.

## 4. Inválidos (brancos e nulos) sobre o comparecimento

| Ano | 1º turno | 2º turno | Razão |
|---|---:|---:|---:|
| 2002 | 10,39% | 6,00% | 0,58 |
| 2006 | 8,39% | 6,04% | 0,72 |
| 2010 | 8,64% | 6,70% | 0,78 |
| 2014 | 9,64% | 6,34% | 0,66 |
| 2018 | 10,75% | 9,57% | 0,89 |
| 2022 | 4,41% | 4,59% | 1,04 |

- Média das razões 0,78; mediana 0,75.
- Regressão linear (seis pontos): inválidos2 = 2,0544 + 0,5152 × inválidos1; R² = 0,52.
- 2026, 1º turno: 4,77% → projeção para o 2º turno: 4,51%.

## 5. Percentuais de transferência

São julgamento do autor, calibrados com o perfil ideológico de cada candidato e com as evidências abaixo. Os valores iniciais podem ser alterados no simulador (botões + e −). O que não vai para Flávio nem para Lula "não migra" (branco, nulo ou abstenção).

| Candidato(s) | Partido(s) | Votos | Para Flávio | Para Lula | Não migra |
|---|---|---:|---:|---:|---:|
| Augusto Cury | Avante | 3.448.500 | 65% | 20% | 15% |
| Renan Santos | Missão | 2.675.872 | 85% | 10% | 5% |
| Ronaldo Caiado | PSD | 2.605.080 | 80% | 15% | 5% |
| Zema | Novo | 326.485 | 90% | 10% | 0% |
| Clariana Barão e Wilson Grassi | DC e Democrata | 56.919 | 50% | 30% | 20% |
| Samara, Hertz Dias, Edmilson Costa e Rui C. Pimenta | UP, PSTU, PCB e PCO | 203.729 | 5% | 90% | 5% |

Evidências:

- Datafolha (5 a 7/10/2022, 2.885 entrevistas): eleitores de Ciro foram 42% para Bolsonaro, 31% para Lula, 22% a 28% em branco ou nulo e 5% indecisos; eleitores de Tebet, 31% Lula, 29% Bolsonaro, 28% branco ou nulo e 12% indecisos.
- PoderData (2022): Lula herdaria 92% dos votos válidos de Tebet e 46% dos de Ciro.
- Estudos: transferência de votos não é automática (1989 e 2006); quanto maior a distância ideológica, menor a transferência (UFPE, 51 segundos turnos municipais de 2024, 74% de acerto).

## 6. Limitações

- O modelo não trata diferenças entre estados.
- Os percentuais de transferência e a projeção central da abstenção são julgamento do autor.
- É uma simulação aritmética com dados do TSE, não previsão nem pesquisa eleitoral.

## 7. Fontes consultadas

- [TSE · resultados da eleição](https://resultados.tse.jus.br/)
- Eleição presidencial no Brasil (Wikipedia), votos inválidos e comparecimento (1º turno de 2022 conforme o TSE): [1998](https://pt.wikipedia.org/wiki/Eleição_presidencial_no_Brasil_em_1998) · [2002](https://pt.wikipedia.org/wiki/Eleição_presidencial_no_Brasil_em_2002) · [2006](https://pt.wikipedia.org/wiki/Eleição_presidencial_no_Brasil_em_2006) · [2010](https://pt.wikipedia.org/wiki/Eleição_presidencial_no_Brasil_em_2010) · [2014](https://pt.wikipedia.org/wiki/Eleição_presidencial_no_Brasil_em_2014) · [2018](https://pt.wikipedia.org/wiki/Eleição_presidencial_no_Brasil_em_2018) · [2022](https://pt.wikipedia.org/wiki/Eleição_presidencial_no_Brasil_em_2022)
- [Abstenção no 2º turno de 2022 (Diário do Transporte, TSE)](https://diariodotransporte.com.br/2022/10/31/abstencao-no-2o-turno-foi-menor-que-no-1o-e-gratuidades-nos-transportes-podem-ajudar-a-explicar-segundo-o-tse/)
- [Abstenção avança há cinco eleições seguidas (Gazeta do Povo)](https://www.gazetadopovo.com.br/republica/abstencao-avanca-ha-cinco-eleicoes-seguidas-e-desafia-a-corrida-presidencial-de-2026/)
- [Abstenção no 1º turno cresce desde 2006 (CNN Brasil)](https://www.cnnbrasil.com.br/eleicoes/analise-abstencao-no-1o-turno-presidencial-cresce-desde-2006/)
- [Datafolha 2022: destino dos votos de Ciro e Tebet (Correio Braziliense)](https://www.correiobraziliense.com.br/politica/2022/10/5043124-datafolha-bolsonaro-herda-mais-votos-de-ciro-do-que-lula-os-de-tebet-se-dividem.html)
- [PoderData 2022 (Reuters)](https://www.noticiasagricolas.com.br/noticias/politica-economia/329538-reuters-lula-atrai-mais-eleitores-de-simone-tebet-do-que-de-ciro-mostra-poder-data.amp.html)
- [Estudo da UFPE sobre ideologia e transferência de votos](https://abcdoabc.com.br/estudo-mede-quanto-ideologia-afeta-eleitor-na-transferencia-de-votos-nas-eleicoes/)
- [Repositório da UnB sobre transferência de votos](https://repositorio.unb.br/handle/10482/27678?locale=en)

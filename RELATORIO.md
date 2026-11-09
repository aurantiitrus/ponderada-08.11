# Relatório · Atividade ponderada de Computação 2 - Semáforo com dois modos de operação

**Grupo:** G05 · **Turma:** T24
**Integrantes:** Jessica Alves, Joana Racy, Lucas Daddazio, Manuella Lima, Matheus Almeida, Pablo Garcia, Pietra Feitoza, Raissa Guimarães
**Previsão registrada em:** não registrado(a)
**Primeira leitura importada em:** 08/11/2026, 23:39:00 · **leituras importadas antes do registro da previsão**

## 1. Contextualizar

| Item | Valor |
|---|---|
| Transições espúrias toleráveis por minuto | 0 |
| Limiar previsto (0 a 4095) | 1000 |

- **Por que esse número de transições:** Zero: cada troca entre diurno e noturno sem mudança real de luz altera o comportamento do semáforo sem motivo. O esperado é uma única troca por mudança real de luz; qualquer troca a mais é falha.
- **Origem do limiar previsto:** Estimativa: com um resistor fixo parecido com o LDR na luz ambiente, a leitura fica perto do meio da escala (cerca de 2048) em sala iluminada. Assumindo LDR do lado do 3V3 (leitura sobe com a luz), o modo noturno deveria entrar bem abaixo disso, perto de 1000. Ainda não medi; vou confrontar na bancada.
- **O que o grupo delega à IA:** Delegado à IA: rascunho do código do semáforo (fases e lógica da histerese), explicação da técnica e rascunho dos textos. Fica com o grupo: montagem, medição do LDR nas três condições, escolha dos limiares, vídeo da transição e o ADR individual.

## 2. Consultar

- **Ferramenta:** Claude
- **Pergunta:** Tenho um sensor de luz que oscila por causa de ruído quando a luminosidade está perto do valor em que quero trocar de modo, e isso faz o sistema alternar de modo várias vezes seguidas. Que estratégia posso usar para evitar essa oscilação, como ela se chama e como escolho os valores dela?
- **Estratégia sugerida:** Usar dois limiares em vez de um: um de entrada (o modo noturno liga quando a luz cai além dele) e um de saída (só desliga quando a luz volta além dele). Na faixa entre os dois o sistema mantém o modo atual. Escolha: medir escuro e claro com várias amostras, medir o ruído numa mesma condição, colocar os limiares entre as faixas com diferença maior que o ruído (2 a 3 vezes o desvio), sem abrir a faixa além do necessário, e testar movendo a luz devagar. Opcional: média móvel ou tempo mínimo no estado.
- **Nome da técnica:** Histerese (comparador Schmitt trigger)
- **Limiar sugerido pela IA:** n/d

## 3. Confrontar: tabela de calibração

Leitura bruta do conversor de 12 bits (0 a 4095).

| Condição | n | Mínimo | Máximo | Média | Desvio |
|---|---|---|---|---|---|
| ambiente | 30 | 1.129 | 1.338 | 1.228,1 | 67,7 |
| sombra | 30 | 0 | 31 | 16,1 | 6,1 |
| lanterna | 30 | 4.004 | 4.027 | 4.015,3 | 5,6 |

- **Sentido:** a leitura sobe com a luz
- **Maior ruído numa mesma condição:** 209 contagens

Verificações:
- Atenção: Leituras importadas antes do registro da previsão no Contextualizar. A previsão precisa vir antes da medição.
- OK: ambiente iluminado: 30 leituras, de 1.129 a 1.338.
- OK: sombreado pela mão: 30 leituras, de 0 a 31.
- OK: lanterna: 30 leituras, de 4.004 a 4.027.
- OK: Sentido detectado: a leitura sobe com a luz (lanterna acima da sombra).
- OK: Região entre as faixas: leituras de 31 a 1.129. Maior ruído numa mesma condição: 209 contagens.
- OK: Limiar previsto (1.000) fica entre a faixa de sombra e a de ambiente.
- OK: Sem limiar sugerido pela IA: registre na divergência se ela não deu valor, e por quê.

**Divergência explicada:** O limiar previsto foi 1000, assumindo leitura que sobe com a luz e ambiente perto de 2048. Na medição o ambiente ficou entre 1129 e 1338 (média 1228), a sombra entre 0 e 31 (média 16) e a lanterna entre 4004 e 4027 (média 4015). O sentido previsto estava certo, mas o ambiente real ficou cerca de 800 contagens abaixo do meio da escala, porque o resistor fixo de 10 kΩ não está casado com a resistência do LDR na luz da sala. O previsto de 1000 ficou só 129 abaixo do mínimo ambiente medido, menos que a variação do próprio ambiente (209), então ficaria sem folga contra a deriva da luz. Por isso os limiares escolhidos (entrada 300, saída 700) vêm das medições, não da previsão. A IA não sugeriu valor numérico.

Gráfico: `grafico-calibracao.png`.

<details><summary>Leituras brutas (90)</summary>

```csv
condicao,leitura
sombra,18
sombra,16
sombra,13
sombra,26
sombra,15
sombra,26
sombra,31
sombra,19
sombra,19
sombra,16
sombra,25
sombra,23
sombra,16
sombra,17
sombra,16
sombra,16
sombra,16
sombra,16
sombra,15
sombra,7
sombra,10
sombra,9
sombra,10
sombra,16
sombra,16
sombra,0
sombra,16
sombra,13
sombra,14
sombra,13
ambiente,1129
ambiente,1131
ambiente,1136
ambiente,1139
ambiente,1145
ambiente,1155
ambiente,1162
ambiente,1167
ambiente,1171
ambiente,1181
ambiente,1189
ambiente,1198
ambiente,1203
ambiente,1222
ambiente,1222
ambiente,1232
ambiente,1235
ambiente,1248
ambiente,1253
ambiente,1263
ambiente,1266
ambiente,1285
ambiente,1286
ambiente,1290
ambiente,1303
ambiente,1319
ambiente,1319
ambiente,1326
ambiente,1330
ambiente,1338
lanterna,4007
lanterna,4005
lanterna,4016
lanterna,4017
lanterna,4027
lanterna,4027
lanterna,4013
lanterna,4015
lanterna,4018
lanterna,4027
lanterna,4019
lanterna,4016
lanterna,4014
lanterna,4013
lanterna,4004
lanterna,4013
lanterna,4023
lanterna,4018
lanterna,4016
lanterna,4014
lanterna,4014
lanterna,4014
lanterna,4018
lanterna,4009
lanterna,4016
lanterna,4015
lanterna,4012
lanterna,4012
lanterna,4015
lanterna,4012
```

</details>

## 4. Construir

| Item | Valor |
|---|---|
| Limiar de entrada no modo noturno | 300 |
| Limiar de saída do modo noturno | 700 |
| Folga entre os limiares | 400 contagens |
| Plano do RA (vermelho, verde, amarelo) | 10 s, 5 s, 2 s (dígito 5) |

Verificações:
- OK: Ordem coerente com o sentido: entrada do lado escuro, saída do lado claro.
- OK: Limiar de entrada (300) fica entre a faixa de sombra e a de ambiente.
- OK: Limiar de saída (700) fica entre a faixa de sombra e a de ambiente.
- OK: Folga de 400 contagens, maior que o ruído medido de 209.

**Como os limiares saíram das medições:** O sentido é de leitura que sobe com a luz (sombra 16, ambiente 1228, lanterna 4015). O ruído numa mesma condição foi de desvio 6,1 na sombra (máximo 31) e 67,7 no ambiente, em parte por deriva da luz (1129 a 1338). Entrada em 300: 269 acima do máximo da sombra e 829 abaixo do mínimo do ambiente. Saída em 700: 429 abaixo do mínimo do ambiente. A folga entre os limiares é de 400 contagens, cerca de 13 vezes o pico a pico da sombra e quase o dobro da variação total do ambiente, então o ruído sozinho não atravessa a faixa.

## 5. Comprovar

- **Vídeo:** n/d
- **Trocas de modo observadas:** n/d (critério declarado: 0 por minuto)
- **Por que a histerese impede a oscilação:** n/d

ADR individual em `ADR.md`, autor: Manuella Cunha Borges Lima.

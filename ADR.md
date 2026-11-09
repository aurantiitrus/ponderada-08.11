# ADR individual · Atividade ponderada de Computação 2 - Semáforo com dois modos de operação

**Data:** 08/11/2026
**Autor:** Manuella Cunha Borges Lima (manuella.lima@sou.inteli.edu.br)
**Grupo:** G05

## Contexto

Modo noturno automático do semáforo, detectado por LDR. Calibração do grupo: média de 1.228 no ambiente, 16 na sombra e 4.015 com lanterna. Limiares adotados: entrada 300 e saída 700.

## Decisão

Usar histerese com limiar de entrada em 300 e de saída em 700 (folga de 400 contagens), derivados da calibração do meu LDR: sombra 0 a 31, ambiente 1129 a 1338, lanterna 4004 a 4027, com leitura que sobe com a luz.

## Alternativa considerada

Limiar único no ponto médio entre sombra e ambiente (cerca de 600), ou manter o previsto de 1000. Ficou de fora porque com um limiar só qualquer ruído ou deriva da luz perto dele troca o modo várias vezes, e o 1000 ficava a apenas 129 contagens do mínimo do ambiente, menos que a variação do próprio ambiente (209).

## Sugestão de IA rejeitada ou corrigida

A IA (Claude) resumiu o plano do RA 5 com verde de 4 s. Corrigi: pela regra do enunciado, verde = 3 s + metade do último dígito arredondada para baixo, ou seja 3 + 2 = 5 s. O plano correto é 10 s de vermelho, 5 s de verde e 2 s de amarelo (ciclo de 17 s).

## Razão técnica

Cinco dividido por dois dá 2,5 e arredondado para baixo é 2, então 3 + 2 = 5 s, não 4. A conferência foi feita com a calculadora de plano da própria página (10 s, 5 s, 2 s, ciclo de 17 s). O erro estava só no resumo da IA, e o código usa divisão inteira (ULTIMO_DIGITO_RA / 2), que já dá 5 s.

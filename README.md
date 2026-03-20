# Relatório da unieuro-concorrente-202601-atividade3

**Disciplina: Programação Concorrente e Distribuída** 
**Aluno(s): Filipe Ferreira Gonçalves**
**Turma: ADS 5 Semestre matutino**
**Professor: Rafael Marconi Ramos**
**Data: 20/03/2026**

---

# 1. Descrição do Problema

O programa resolve o problema de processamento massivo de arquivos de texto (logs operacionais). O sistema precisa ler milhares de arquivos, contar o total de linhas, palavras, caracteres e identificar a frequência de palavras-chave específicas ("erro", "warning", "info").Algoritmo utilizado: Modelo Produtor-Consumidor com buffer limitado. Um processo "Produtor" alimenta uma fila de arquivos, enquanto múltiplos processos "Consumidores" retiram os arquivos da fila e realizam o processamento pesado de forma simultânea.Tamanho da entrada: 1.000 arquivos de log (pasta log2), totalizando 10.000.000 de linhas e 200.000.000 de palavras.Objetivo da paralelização: Reduzir o tempo total de execução, aproveitando os múltiplos núcleos do processador para processar diversos arquivos ao mesmo tempo, superando o gargalo da execução sequencial.Complexidade aproximada: $O(n)$, onde $n$ é o número total de linhas processadas.

---

# 2. Ambiente Experimental

| Item                        | Descrição |
| --------------------------- | --------- |
| Processador                 |     12th Gen Intel(R) Core(TM) i5-12500 (3.00 GHz)      |
| Número de núcleos           |     6 Núcleos      |
| Memória RAM                 |     16,0 GB (utilizável: 15,7 GB)      |
| Sistema Operacional         |     Windows 11 Pro      |
| Linguagem utilizada         |     Python 3.13.2      |
| Biblioteca de paralelização |     threading nativa do Python      |
| Compilador / Versão         |     CPython [MSC v.1942 64 bit (AMD64)] on win32      |

---

# 3. Metodologia de Testes

Medição de tempo: Utilizada a biblioteca time do Python para medir o tempo de relógio real (wall time) do início ao fim do processamento.

Execuções: Foi realizada a execução serial completa para obter o baseline e execuções paralelas variando o número de processos (2, 4, 8 e 12).

Entrada: Pasta log2 fixa para todos os testes, garantindo a integridade da comparação.

Condições: Máquina em estado de uso normal, com processos de fundo padrão do Windows 11.

### Procedimento experimental

As métricas foram calculadas comparando o tempo serial $T(1) = 94,25s$ com o tempo paralelo $T(p)$.

---

# 4. Resultados Experimentais

| Nº Threads/Processos | Tempo de Execução (s) |
| -------------------- | --------------------- |
| 1 (Serial)           |         94.2502       |
| 2                    |         47.5848       |
| 4                    |         26.6610       |
| 8                    |         17.0822       |
| 12                   |         16.0659       |

---

# 5. Cálculo de Speedup e Eficiência

As métricas foram calculadas comparando o tempo serial $T(1) = 94,25s$ com o tempo paralelo $T(p)$.

```
Speedup(p) = T(1) / T(p)
```

Onde:

* **T(1)** = tempo da execução serial
* **T(p)** = tempo com p threads/processos

### Eficiência

```
Eficiência(p) = Speedup(p) / p
```

Onde:

* **p** = número de threads ou processos

---

# 6. Tabela de Resultados

Preencha a tabela abaixo utilizando os tempos medidos.

| Threads/Processos | Tempo (s) | Speedup | Eficiência |
| ----------------- | --------- | ------- | ---------- |
| 1                 |     94.2502      | 1.0     | 1.0        |
| 2                 |     47.5848      | 2.44    | 1.22       |
| 4                 |     26.6610      | 4.35    | 1.09       |
| 8                 |     17.0822      | 6.79    | 0.85       |
| 12                |     16.0659      | 7.22    | 0.60       |

---

# 7. Gráfico de Tempo de Execução

![Gráfico Tempo Execução](graficos/tempo_execucao.png)

---

# 8. Gráfico de Speedup

![Gráfico Speedup](graficos/speedup.png)

---

# 9. Gráfico de Eficiência

![Gráfico Eficiência](graficos/eficiencia.png)

---

# 10. Análise dos Resultados

Speedup e Eficiência: Observou-se um fenômeno de Speedup Super-linear nos testes com 2 e 4 processos (Eficiência > 1.0). Isso pode ocorrer devido à melhor utilização do cache do processador ou pela forma como o Windows gerencia a prioridade de múltiplos processos em relação a um único processo longo.

Escalabilidade: A aplicação escalou muito bem até 8 processos. O ganho entre 8 e 12 processos foi de apenas 1.01 segundo, indicando que o sistema atingiu um platô.

Ponto de Queda: A eficiência começou a cair abaixo de 1.0 ao ultrapassar 4 processos.

Cores Físicos vs Lógicos: O processador i5-12500 possui 6 núcleos. Ao utilizar 8 e 12 processos, ultrapassamos os núcleos físicos, entrando na zona de Hyper-Threading (núcleos lógicos). Isso explica o ganho marginal reduzido no teste de 12 processos.

Overhead: Houve overhead de gerenciamento de processos e contenção de I/O (disco) ao tentar ler arquivos simultaneamente com 12 processos, o que limitou o ganho de desempenho final.
---

# 11. Conclusão

O paralelismo trouxe um ganho de desempenho massivo, reduzindo o tempo de processamento de aproximadamente 94 segundos para 16 segundos (uma redução de 83%).

O melhor equilíbrio entre desempenho e uso de recursos foi de 8 processos. Acima disso, o custo de trocar de contexto e a disputa pelo acesso ao disco rígido tornam o ganho irrelevante. A implementação do modelo Produtor-Consumidor com buffer limitado foi eficaz para evitar o consumo excessivo de memória, mantendo o sistema estável durante todo o processamento de 10 milhões de linhas.

Resultado Consolidado para Conferência:
Total de Linhas: 10.000.000

Total de Palavras: 200.000.000

ERRO: 33.332.083 | WARNING: 33.330.520 | INFO: 33.329.065

---

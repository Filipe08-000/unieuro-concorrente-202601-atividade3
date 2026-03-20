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

Medição de tempo: Utilizada a biblioteca time do Python para medir o tempo de relógio real (wall time) do início ao fim do processamento.Execuções: Foi realizada a execução serial completa para obter o baseline e execuções paralelas variando o número de processos (2, 4, 8 e 12).Entrada: Pasta log2 fixa para todos os testes, garantindo a integridade da comparação.Condições: Máquina em estado de uso normal, com processos de fundo padrão do Windows 11.Procedimento experimentalAs métricas foram calculadas comparando o tempo serial $T(1) = 101.2226s$ com o tempo paralelo $T(p)$.

---

# 4. Resultados Experimentais

| Nº Threads/Processos | Tempo de Execução (s) |
| -------------------- | --------------------- |
| 1 (Serial)           |         101.2226     |
| 2                    |         47.5848       |
| 4                    |         26.6610       |
| 8                    |         17.0822       |
| 12                   |         16.0659       |

---

# 5. Cálculo de Speedup e Eficiência

As métricas foram calculadas comparando o tempo serial $T(1) = 101,2226s$ com o tempo paralelo $T(p)$.

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

|  Threads  | Tempo (s) | Speedup | Eficiência |
|-----------|-----------|---------|------------|
|         1 |  101.2226 |    1.00 |       1.00 |
|         2 |   51.2129 |    1.98 |       0.99 |
|         4 |   27.6964 |    3.65 |       0.91 |
|         8 |   19.5575 |    5.18 |       0.65 |
|        12 |   17.1678 |    5.90 |       0.49 |

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

Speedup e Eficiência: Diferente de testes anteriores, não se observou Speedup Super-linear. A eficiência manteve-se extremamente alta com 2 processos (0.99), demonstrando um excelente aproveitamento inicial. À medida que o número de threads aumentou, a eficiência caiu gradualmente, o que é esperado devido ao overhead de gerenciamento.Escalabilidade: A aplicação escalou de forma consistente até 12 threads. O ganho entre 8 e 12 processos foi de 2,39 segundos ($19,55s - 17,16s$). Embora o tempo continue caindo, a curva de ganho começa a se achatar, indicando que o sistema está próximo do seu limite de benefício por paralelismo.Eficiência: A eficiência caiu abaixo de 0.90 a partir de 8 processos (0.65), chegando a 0.49 com 12 processos. Isso indica que, embora o tempo total diminua, cada núcleo adicional contribui menos para o desempenho final.Cores Físicos vs Lógicos: O processador i5-12500 possui 6 núcleos físicos e 12 threads (núcleos lógicos). Os resultados mostram que o desempenho melhora até 12 threads, mas o salto de desempenho é muito mais expressivo enquanto estamos dentro do limite de núcleos físicos (até 6). Ao usar 8 e 12 threads, entramos na zona de Hyper-Threading, onde a disputa por recursos de execução dentro do mesmo núcleo físico limita a eficiência.Overhead: A queda de eficiência para 0.49 com 12 threads é explicada pelo custo de troca de contexto e, principalmente, pela saturação do barramento de I/O, já que 12 instâncias tentam ler arquivos do disco simultaneamente.

---

# 11. Conclusão

O paralelismo trouxe um ganho de desempenho massivo, reduzindo o tempo de processamento de 101,22 segundos para 17,16 segundos (uma redução de aproximadamente 83%).

O melhor equilíbrio entre desempenho e eficiência de recursos foi observado com 4 processos (Eficiência de 0.91). Embora 12 processos ofereçam o menor tempo absoluto, o custo de oportunidade (uso de CPU vs. ganho de tempo) é menor. A implementação do modelo Produtor-Consumidor com buffer limitado provou-se robusta para processar o volume de 10 milhões de linhas, mantendo a integridade da contagem de termos ("ERRO", "WARNING", "INFO").

---

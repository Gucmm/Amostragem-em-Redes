# Como Amostrar Redes Complexas?

Projeto de análise e comparação de algoritmos de amostragem de redes complexas, avaliando como diferentes métodos preservam as propriedades estruturais dos grafos originais.

**Integrantes:** Gustavo Moraes, Murilo Lirani, João Rissi, Matheus Rodrigues, Enzo Biondi

---

## Visão Geral

O projeto investiga a qualidade de quatro algoritmos de amostragem de grafos aplicados a redes geradas por três modelos clássicos (Erdős–Rényi, Barabási–Albert e Watts–Strogatz). A qualidade das amostras é avaliada comparando métricas estruturais das sub-redes geradas com as dos grafos originais, usando PCA e análise de distâncias no espaço de métricas.

---

## Estrutura do Repositório

```
.
├── gerar_grafos.py                          # Geração dos grafos pais
├── gerar_amostras.py                        # Amostragem e cálculo de métricas
├── Custom_Sampling.py                       # Implementação dos algoritmos de amostragem
├── analise.ipynb                            # Análise e visualização dos resultados
├── metricas_grafos_pais.csv                 # Métricas dos 75 grafos originais
├── metricas_amostras_rede_checkpoints.csv   # Métricas das amostras com checkpoints
└── README.md
```

---

## Dependências

- Python 3.x
- NetworkX
- NumPy
- Pandas
- Matplotlib
- Seaborn
- scikit-learn

Instale todas com:

```bash
pip install networkx numpy pandas matplotlib seaborn scikit-learn
```

---

## Pipeline de Execução

### 1. Geração dos Grafos Pais — `gerar_grafos.py`

Gera 75 grafos aleatórios usando três modelos, com 5 tamanhos distintos (n = 500, 1000, 1500, 2000, 2500), 5 instâncias por combinação modelo/tamanho:

| Modelo | Parâmetros | Característica |
|---|---|---|
| Erdős–Rényi (ER) | p = 0.2 | Rede aleatória uniforme |
| Barabási–Albert (BA) | m = 3 | Rede livre de escala (hubs) |
| Watts–Strogatz (WS) | k = 6, p = 0.2 | Pequeno mundo |

Todos os grafos são reduzidos ao maior componente conectado antes de serem salvos. O resultado é serializado em `grafos.pkl` com o formato:

```
[id, tipo, n, p_er, m_ba, k_ws, p_ws, objeto_grafo]
```

### 2. Amostragem e Métricas — `gerar_amostras.py`

Aplica os quatro algoritmos de amostragem a cada grafo pai, gerando sub-redes em checkpoints de tamanho `[50, 150, 250, 350]` nós (com `max_n = 400`). Para cada amostra são calculadas 10 métricas estruturais:

| Métrica | Descrição |
|---|---|
| `grau_medio` / `grau_var` | Grau médio e variância |
| `path_length_medio` / `path_length_var` | Comprimento médio do menor caminho |
| `clustering_medio` / `clustering_var` | Coeficiente de agrupamento |
| `betweenness_medio` / `betweenness_var` | Centralidade de intermediação |
| `eigenvector_medio` / `eigenvector_var` | Centralidade do autovetor |

Os resultados são salvos em `metricas_amostras_rede_checkpoints.csv`.

### 3. Análise — `analise.ipynb`

Compara os algoritmos usando PCA sobre as métricas médias para projetar grafos pais e amostras num espaço bidimensional, analisando a distância entre cada amostra e seu grafo original. Inclui análises por tamanho de grafo, por algoritmo e por checkpoint.

---

## Algoritmos de Amostragem — `Custom_Sampling.py`

### RWEB — Random Walk with Edge Blocking
Caminhada aleatória que remove arestas já percorridas do grafo de trabalho. Usa backtracking via pilha quando não há vizinhos disponíveis, garantindo que a caminhada explore regiões novas da rede. Captura amostras nos checkpoints especificados.

### IRWEB — Induced Random Walk with Edge Blocking
Extensão do RWEB que, a cada nó visitado pela caminhada, adiciona ao subgrafo todos os seus vizinhos imediatos e as arestas entre eles (indução local). Gera amostras mais densas e com maior cobertura de vizinhança.

### SB — Snowball Sampling
A partir de um nó semente, expande a amostra em camadas: a cada iteração, seleciona até `k` vizinhos aleatórios de cada nó na fronteira atual e os adiciona ao subgrafo. Explora a rede de forma BFS-like.

### TIES — Total Induction Edge Sampling
Amostragem orientada a arestas. Seleciona arestas aleatoriamente do grafo original e adiciona ambos os endpoints ao subgrafo amostrado, induzindo todas as arestas existentes entre os nós já selecionados.

---

## Dados Gerados

### `metricas_grafos_pais.csv`
75 linhas, uma por grafo pai. Colunas principais:

| Coluna | Descrição |
|---|---|
| `i` | ID do grafo |
| `graph_type` | Tipo: ER, BA ou WS |
| `g_n` | Número de nós |
| `g_p_er`, `g_m_ba`, `g_k_ws`, `g_p_ws` | Parâmetros do modelo |
| `grau_medio`, ..., `eigenvector_var` | 10 métricas estruturais |

### `metricas_amostras_rede_checkpoints.csv`
Uma linha por (grafo pai × algoritmo × número de amostra × checkpoint). Colunas adicionais:

| Coluna | Descrição |
|---|---|
| `id_pai` | ID do grafo original |
| `tipo` | Tipo do grafo pai (ER, BA, WS) |
| `algoritmo` | Nome do algoritmo de amostragem |
| `numero_amostra` | Índice da replicação (1–10) |
| `tam_amostra` | Número de nós na amostra |
| `tam_rede_original` | Número de nós no grafo pai |
| `proporcao_amostra` | `tam_amostra / tam_rede_original` |

---

## Principais Resultados

- Em espaço PCA, amostras de redes menores ficam mais próximas do grafo original, indicando maior estabilidade estrutural.
- O **RWEB** e o **IRWEB** tendem a gerar amostras mais próximas do grafo original no espaço das métricas médias.
- O aumento do tamanho do grafo pai aumenta a dispersão das amostras em relação ao original.
- Maiores checkpoints (amostras maiores) reduzem a diferença entre as métricas da amostra e do grafo pai.

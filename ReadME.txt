Projeto: Como amostrar redes?  
Integrantes: Gustavo Moraes, Murilo Lirani, João Rissi, Matheus Rodrigues, Enzo Biondi




Custom_Sampling.py: Contém funções dos algoritmos de amostragem das redes utilizadas durante projeto, estes que são TIES, Random Walk(Com Edge Blocking), IRWEB e Snowball.


Gerar_grafos.py: Gera os grafos pais usando 3 algotimos de geração de grafos aletórios (erdos renyi, barabasi albert_graph e watts strogatz graph) e gera 25 grafos pais para cada um dos métodos com 5 tipos de tamanhos "n" diferentes. A partir disso, gera-se Grafos.pkl contendo esses 75 grafos.


gerar_amostras.py: Faz a amostragem dos grafos de Grafos.pkl usando os métodos de Custom_Sampling.py, assim gerando diversos grafos filhos com tamanhos variados. Após isso, é calculado as métricas e salvos em um csv, tanto dos pais quanto dos filhos para futuras análises


analise.ipynb: Faz análises com base nas métricas obtidas em gerar_amostras.py.
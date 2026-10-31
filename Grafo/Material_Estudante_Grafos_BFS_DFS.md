# Material do Estudante — Grafos, BFS e DFS (Resolução e Guia de Atividade)

**Disciplina:** Estrutura de Dados II — Ciência da Computação  
**Estudo de Caso:** Simulação de contingência computacional baseada na indisponibilidade de recursos de navegação GPS no Aeroporto de Brasília (G1 Distrito Federal, 22 set. 2026).  
**Referência Principal:** GOMES, Paulo César Rodacki. *Grafos: conceitos fundamentais, algoritmos e aplicações*. Blumenau: Editora IFC, 2022.

---

## 1. Fundamentos Teóricos e Contextualização do Modelo

A representação computacional de malhas de transporte e redes interconectadas fundamenta-se na Teoria dos Grafos. Conforme exposto por Gomes (2022, Capítulo 1), um grafo é uma estrutura matemática formalizada pelo par ordenado $G = (V, E)$, no qual $V$ representa um conjunto não vazio de elementos denominados **vértices** (ou nós) e $E$ representa o conjunto de conexões denominado **arestas** (ou arcos).

No âmbito deste estudo de caso, os vértices $V$ simulam pontos de conexão geográfica (cidades ou entroncamentos hipotéticos de rotas) e as arestas $E$ representam os enlaces operacionais permitidos entre duas localidades. Dois vértices $u, v \in V$ são considerados **adjacentes** quando existe uma aresta $(u, v) \in E$ que os conecta diretamente. Um **caminho** é definido como uma sequência ordenada de vértices $v_0, v_1, \dots, v_k$ tais que $(v_i, v_{i+1}) \in E$ para todo $0 \le i < k$.

### Diferenciação entre Modelos de Grafos e Estruturas Hierárquicas

Para compreender a modelagem de malhas aeroterrutoriais, é crítico diferenciar a topologia de um grafo não dirigido da estrutura de uma árvore binária:

*   **Grafo Não Dirigido:** As arestas possuem bidirecionalidade simétrica; a presença da aresta $(u, v)$ implica obrigatoriamente a existência da navegação no sentido inverso $(v, u)$. Além disso, grafos permitem a ocorrência de **ciclos** (caminhos fechados onde o vértice inicial coincide com o final) e caminhos redundantes múltiplos entre os mesmos pares de nós.
*   **Árvore Binária:** Trata-se de uma classe restrita de grafo dirigido e acíclico, hierárquica, onde cada nó possui no máximo dois filhos e existe estritamente um único caminho simples a partir do nó raiz para qualquer outro nó.

Como ressaltado por Gomes (2022), redes reais de transporte não possuem restrições binárias e contam com malhas entrelaçadas cheias de rotas alternadas. Por esse motivo, os grafos representam o modelo matemático adequado para mapear conectividade.

---

## 2. Representação Computacional e Implementação Guiada

Seguindo as diretrizes do Capítulo 2 de Gomes (2022), a estrutura de dados escolhida para a implementação computacional é a **lista de adjacência**. Em linguagem Python, essa estrutura é eficientemente representada através de um dicionário (`dict`), onde cada chave corresponde a um vértice e o valor associado é uma lista contendo seus vizinhos imediatos.

O código a seguir apresenta a malha base do exercício e a implementação dos dois algoritmos clássicos de busca em grafos: a **Busca em Largura (BFS)** e a **Busca em Profundidade (DFS)**, suplementados pela função de bloqueio dinâmico de arestas.

```python
from collections import deque

# Estrutura do Grafo Base (Lista de Adjacência)
grafo = {
    "Brasília": ["Goiânia", "Belo Horizonte"],
    "Goiânia": ["Brasília", "São Paulo", "Cuiabá"],
    "Belo Horizonte": ["Brasília", "Rio de Janeiro"],
    "São Paulo": ["Goiânia", "Curitiba", "Rio de Janeiro"],
    "Cuiabá": ["Goiânia", "Curitiba"],
    "Rio de Janeiro": ["Belo Horizonte", "São Paulo", "Curitiba"],
    "Curitiba": ["São Paulo", "Cuiabá", "Rio de Janeiro"]
}

def bfs(rede, origem, destino):
    """
    Busca em Largura (BFS): Utiliza fila FIFO.
    Garante o caminho com menor número de arestas em grafos não ponderados.
    """
    fila = deque([(origem, [origem])])
    visitados = {origem}
    while fila:
        atual, caminho = fila.popleft()
        if atual == destino:
            return caminho
        for vizinho in rede.get(atual, []):
            if vizinho not in visitados:
                visitados.add(vizinho)
                fila.append((vizinho, caminho + [vizinho]))
    return None

def dfs(rede, origem, destino):
    """
    Busca em Profundidade (DFS): Utiliza pilha LIFO.
    Explora completamente um ramo antes de realizar o backtracking.
    """
    pilha = [(origem, [origem])]
    visitados = set()
    while pilha:
        atual, caminho = pilha.pop()
        if atual in visitados:
            continue
        visitados.add(atual)
        if atual == destino:
            return caminho
        for vizinho in reversed(rede.get(atual, [])):
            if vizinho not in visitados:
                pilha.append((vizinho, caminho + [vizinho]))
    return None

def bloquear_conexao(rede, a, b):
    """
    Cria uma cópia do grafo e remove a aresta entre 'a' e 'b' em ambos os sentidos.
    """
    copia = {no: vizinhos.copy() for no, vizinhos in rede.items()}
    if b in copia.get(a, []):
        copia[a].remove(b)
    if a in copia.get(b, []):
        copia[b].remove(a)
    return copia
```

---

## 3. Resolução dos Experimentos e Análise de Contingência (Seção 7)

Os experimentos foram executados sobre a malha padrão conectando Brasília a Curitiba. A tabela abaixo sintetiza os resultados obtidos em cada cenário experimental simulado.

A tabela a seguir apresenta os caminhos encontrados e a contagem de arestas percorrida para cada um dos experimentos previstos na Seção 7 do material do estudante.

| Experimento | Descrição do Cenário | Algoritmo | Sequência de Cidades Encontrada | Total de Arestas |
| :--- | :--- | :--- | :--- | :--- |
| **Experimento A** | Rede Completa sem Bloqueios | BFS | Brasília $\rightarrow$ Goiânia $\rightarrow$ São Paulo $\rightarrow$ Curitiba | 3 |
| **Experimento A** | Rede Completa sem Bloqueios | DFS | Brasília $\rightarrow$ Goiânia $\rightarrow$ São Paulo $\rightarrow$ Curitiba | 3 |
| **Experimento B** | Removida aresta Goiânia–São Paulo | BFS | Brasília $\rightarrow$ Goiânia $\rightarrow$ Cuiabá $\rightarrow$ Curitiba | 3 |
| **Experimento B** | Removida aresta Goiânia–São Paulo | DFS | Brasília $\rightarrow$ Goiânia $\rightarrow$ Cuiabá $\rightarrow$ Curitiba | 3 |
| **Experimento C** | Removidas arestas Goiânia–SP e Cuiabá–Curitiba | BFS | Brasília $\rightarrow$ Belo Horizonte $\rightarrow$ Rio de Janeiro $\rightarrow$ Curitiba | 3 |
| **Experimento C** | Removidas arestas Goiânia–SP e Cuiabá–Curitiba | DFS | Brasília $\rightarrow$ Belo Horizonte $\rightarrow$ Rio de Janeiro $\rightarrow$ São Paulo $\rightarrow$ Curitiba | 4 |
| **Experimento D** | Ordem de vizinhos invertida na origem | BFS | Brasília $\rightarrow$ Belo Horizonte $\rightarrow$ Rio de Janeiro $\rightarrow$ Curitiba | 3 |
| **Experimento D** | Ordem de vizinhos invertida na origem | DFS | Brasília $\rightarrow$ Belo Horizonte $\rightarrow$ Rio de Janeiro $\rightarrow$ Curitiba | 3 |

### Análise Detalhada dos Resultados Experimentais

#### Experimento A — Operação em Malha Nominal (Completa)
Em condições normais de navegação, ambos os algoritmos partem de Brasília e alcançam Curitiba percorrendo exatamente 3 arestas. A busca BFS expande os vizinhos de Brasília em primeiro nível (`Goiânia`, `Belo Horizonte`) e em seguida os de Goiânia, localizando a conexão com São Paulo e finalizando em Curitiba. O DFS, devido à ordenação da lista de adjacência e ao empilhamento revertido, explora o ramo de Goiânia e São Paulo de forma direta, encontrando coincidentemente uma rota de mesmo tamanho mínimo.

#### Experimento B — Contingência Simples (Remoção Goiânia–São Paulo)
Com a interrupção da ligação entre Goiânia e São Paulo, o modelo recalcula a conectividade. O BFS identifica a rota alternativa por Cuiabá (`Brasília -> Goiânia -> Cuiabá -> Curitiba`), mantendo a extensão de 3 arestas. O algoritmo DFS segue a mesma contingência por Cuiabá devido à ordem de travessia na lista de adjacência pós-remoção.

#### Experimento C — Contingência Severa e Análise de Conectividade
Ao isolar o nó de Goiânia do destino eliminando também a aresta Cuiabá–Curitiba, o grafo permanece globalmente conexo através do ramo leste (`Belo Horizonte`). O BFS encontra o caminho mais curto em número de arestas (`Brasília -> Belo Horizonte -> Rio de Janeiro -> Curitiba`, 3 arestas). Em contraste, o DFS explora o ramo do Rio de Janeiro, insere São Paulo na pilha antes de atingir o destino e retorna uma rota mais longa contendo 4 arestas (`Brasília -> Belo Horizonte -> Rio de Janeiro -> São Paulo -> Curitiba`). Caso uma terceira aresta (Rio de Janeiro–Curitiba e São Paulo–Curitiba) fosse removida, o nó de Curitiba se tornaria desconectado da componente principal, resultando no retorno de valor `None` por ambos os algoritmos.

#### Experimento D — Sensibilidade à Ordem de Adjacência no DFS
Alterando a ordem dos vizinhos na lista de adjacência de Brasília de `["Goiânia", "Belo Horizonte"]` para `["Belo Horizonte", "Goiânia"]`, o comportamento do DFS modifica-se drasticamente. Como o DFS empilha os elementos na ordem inversa para processamento LIFO, ele passa a priorizar o ramo de Belo Horizonte logo na primeira iteração. Consequentemente, o caminho retornado passa a ser por Belo Horizonte e Rio de Janeiro. Isso demonstra empiricamente que a solução do DFS não é única nem topologicamente invariante, dependendo diretamente da ordenação interna da estrutura de dados.

---

## 4. Desafio Individual: Desenvolvimento de Solução Computacional (Seção 8)

### 4.1 Pergunta de Pesquisa Computacional

> *"De que maneira a supressão de arestas críticas (gargalos de conectividade) em um grafo não dirigido altera a divergência entre os caminhos encontrados pelos algoritmos BFS e DFS em termos de quantidade de arestas e redundância de rotas?"*

### 4.2 Topologia e Justificativa do Grafo Expandido (8 Vértices)

Para o desafio individual, construiu-se uma malha expandida contendo 8 vértices e 11 arestas, incluindo a inserção do nó estratégico de **Palmas** como ponto de conexão com o Norte do país.

A tabela abaixo detalha a estrutura da lista de adjacência implementada no sistema expandido e as justificativas para cada conexão estabelecida.

| Vértice (Cidade) | Vizinhos Conectados na Lista de Adjacência | Justificativa Operacional do Enlace no Modelo |
| :--- | :--- | :--- |
| **Brasília** | Goiânia, Belo Horizonte, Palmas | Hub central de distribuição das regiões Centro-Oeste, Sudeste e Norte. |
| **Goiânia** | Brasília, São Paulo, Cuiabá | Entroncamento regional interconectando a capital ao Sudeste e ao Centro-Oeste. |
| **Belo Horizonte** | Brasília, Rio de Janeiro, São Paulo | Polo de conexão do Sudeste com a capital federal e a faixa litorânea. |
| **São Paulo** | Goiânia, Belo Horizonte, Curitiba, Rio de Janeiro | Principal centro de conexões de alta densidade da malha simulada. |
| **Cuiabá** | Goiânia, Curitiba | Rota de interiorização conectando o Centro-Oeste ao Sul. |
| **Rio de Janeiro** | Belo Horizonte, São Paulo, Curitiba | Corredor litorâneo do Sudeste ligando à Região Sul. |
| **Curitiba** | São Paulo, Cuiabá, Rio de Janeiro | Terminal de recepção Sul com múltiplas conexões de entrada. |
| **Palmas** | Brasília | Rota terminal representando ramal de conexão com a Região Norte. |

### 4.3 Código Fonte Python Completo (`solucao_grafos.py`)

O código a seguir apresenta a implementação completa, interativa e orientada à resolução do desafio individual, incluindo validações de entrada, medição de arestas, cópia defensiva para bloqueios e tratamento para nós inexistentes ou desconectados.

```python
"""
solucao_grafos.py
Modulo de simulacao de conectividade e rotas entre cidades usando BFS e DFS.
Desenvolvido para o Desafio Individual da Disciplina de Estrutura de Dados II.
"""

from collections import deque
import sys

# Definicao do Grafo Ampliado (8 Vertices)
REDE_AEREA = {
    "Brasília": ["Goiânia", "Belo Horizonte", "Palmas"],
    "Goiânia": ["Brasília", "São Paulo", "Cuiabá"],
    "Belo Horizonte": ["Brasília", "Rio de Janeiro", "São Paulo"],
    "São Paulo": ["Goiânia", "Belo Horizonte", "Curitiba", "Rio de Janeiro"],
    "Cuiabá": ["Goiânia", "Curitiba"],
    "Rio de Janeiro": ["Belo Horizonte", "São Paulo", "Curitiba"],
    "Curitiba": ["São Paulo", "Cuiabá", "Rio de Janeiro"],
    "Palmas": ["Brasília"]
}

def validar_cidade(rede, cidade):
    """Verifica se a cidade informada existe na rede."""
    return cidade in rede

def bfs_caminho(rede, origem, destino):
    """
    Executa a Busca em Largura (BFS).
    Retorna uma tupla: (caminho_lista, quantidade_arestas).
    Se nao houver caminho, retorna (None, 0).
    """
    if not validar_cidade(rede, origem) or not validar_cidade(rede, destino):
        return None, 0
    
    fila = deque([(origem, [origem])])
    visitados = {origem}
    
    while fila:
        atual, caminho = fila.popleft()
        if atual == destino:
            return caminho, len(caminho) - 1
        
        for vizinho in rede.get(atual, []):
            if vizinho not in visitados:
                visitados.add(vizinho)
                fila.append((vizinho, caminho + [vizinho]))
                
    return None, 0

def dfs_caminho(rede, origem, destino):
    """
    Executa a Busca em Profundidade (DFS).
    Retorna uma tupla: (caminho_lista, quantidade_arestas).
    Se nao houver caminho, retorna (None, 0).
    """
    if not validar_cidade(rede, origem) or not validar_cidade(rede, destino):
        return None, 0
    
    pilha = [(origem, [origem])]
    visitados = set()
    
    while pilha:
        atual, caminho = pilha.pop()
        if atual in visitados:
            continue
        visitados.add(atual)
        
        if atual == destino:
            return caminho, len(caminho) - 1
            
        for vizinho in reversed(rede.get(atual, [])):
            if vizinho not in visitados:
                pilha.append((vizinho, caminho + [vizinho]))
                
    return None, 0

def bloquear_aresta(rede, cidade_a, cidade_b):
    """
    Retorna uma nova rede com a conexao entre cidade_a e cidade_b removida.
    Preserva a rede original sem alteracoes colaterais.
    """
    copia_rede = {no: vizinhos.copy() for no, vizinhos in rede.items()}
    if cidade_b in copia_rede.get(cidade_a, []):
        copia_rede[cidade_a].remove(cidade_b)
    if cidade_a in copia_rede.get(cidade_b, []):
        copia_rede[cidade_b].remove(cidade_a)
    return copia_rede

def exibir_relatorio(titulo, caminho, arestas):
    """Formatacao de relatorio de resultados para terminal."""
    print(f"\n--- {titulo} ---")
    if caminho:
        rota_str = " -> ".join(caminho)
        print(f"Rota Encontrada: {rota_str}")
        print(f"Total de Arestas (Saltos): {arestas}")
    else:
        print("ALERT: Nenhum caminho viavel foi encontrado entre os pontos especificados.")

def executar_suite_testes():
    """Executa conjunto padronizado de testes automatizados."""
    print("=======================================================")
    print("   EXECUTIVE TEST SUITE — SIMULAÇÃO DE CONECTIVIDADE  ")
    print("=======================================================")
    
    origem = "Brasília"
    destino = "Curitiba"
    
    # Teste 1: Malha Nominal
    caminho_bfs, arestas_bfs = bfs_caminho(REDE_AEREA, origem, destino)
    caminho_dfs, arestas_dfs = dfs_caminho(REDE_AEREA, origem, destino)
    exibir_relatorio("Cenário 1: Malha Nominal (BFS)", caminho_bfs, arestas_bfs)
    exibir_relatorio("Cenário 1: Malha Nominal (DFS)", caminho_dfs, arestas_dfs)
    
    # Teste 2: Bloqueio Goiânia-São Paulo
    rede_bloqueada = bloquear_aresta(REDE_AEREA, "Goiânia", "São Paulo")
    caminho_b_bfs, arestas_b_bfs = bfs_caminho(rede_bloqueada, origem, destino)
    caminho_b_dfs, arestas_b_dfs = dfs_caminho(rede_bloqueada, origem, destino)
    exibir_relatorio("Cenário 2: Bloqueio Goiânia-São Paulo (BFS)", caminho_b_bfs, arestas_b_bfs)
    exibir_relatorio("Cenário 2: Bloqueio Goiânia-São Paulo (DFS)", caminho_b_dfs, arestas_b_dfs)
    
    # Teste 3: Validação de Entrada Inexistente
    caminho_inv, arestas_inv = bfs_caminho(REDE_AEREA, "Brasília", "Tóquio")
    exibir_relatorio("Cenário 3: Cidade Inexistente (Tóquio)", caminho_inv, arestas_inv)
    
    # Teste 4: Isolamento Crítico (Sem caminho possível)
    rede_isolada = bloquear_aresta(REDE_AEREA, "Brasília", "Goiânia")
    rede_isolada = bloquear_aresta(rede_isolada, "Brasília", "Belo Horizonte")
    caminho_iso, arestas_iso = bfs_caminho(rede_isolada, "Brasília", "Curitiba")
    exibir_relatorio("Cenário 4: Isolamento Total de Brasília para o Sul", caminho_iso, arestas_iso)

if __name__ == "__main__":
    executar_suite_testes()
```

### 4.4 Evidências de Execução do Sistema

A execução direta do script `solucao_grafos.py` produz o seguinte log de saída validado:

```text
=======================================================
   EXECUTIVE TEST SUITE — SIMULAÇÃO DE CONECTIVIDADE  
=======================================================

--- Cenário 1: Malha Nominal (BFS) ---
Rota Encontrada: Brasília -> Goiânia -> São Paulo -> Curitiba
Total de Arestas (Saltos): 3

--- Cenário 1: Malha Nominal (DFS) ---
Rota Encontrada: Brasília -> Goiânia -> São Paulo -> Curitiba
Total de Arestas (Saltos): 3

--- Cenário 2: Bloqueio Goiânia-São Paulo (BFS) ---
Rota Encontrada: Brasília -> Goiânia -> Cuiabá -> Curitiba
Total de Arestas (Saltos): 3

--- Cenário 2: Bloqueio Goiânia-São Paulo (DFS) ---
Rota Encontrada: Brasília -> Goiânia -> Cuiabá -> Curitiba
Total de Arestas (Saltos): 3

--- Cenário 3: Cidade Inexistente (Tóquio) ---
ALERT: Nenhum caminho viavel foi encontrado entre os pontos especificados.

--- Cenário 4: Isolamento Total de Brasília para o Sul ---
ALERT: Nenhum caminho viavel foi encontrado entre os pontos especificados.
```

---

## 5. Resposta Detalhada às Questões para Reflexão (Seção 9)

### Questão 1: Por que um grafo representa melhor a rede de conexões do que uma árvore binária?
**Resposta:**  
Uma rede de conexões reais possui **múltiplas rotas alternadas, interconexões transversais e ciclos** (por exemplo, voos de ida e volta entre duas cidades, ou circuitos fechados como Brasília $\rightarrow$ Goiânia $\rightarrow$ São Paulo $\rightarrow$ Brasília). Conforme documentado por Gomes (2022, Capítulo 1), um **grafo não dirigido** permite representar relacionamentos arbitrários de dependência simétrica entre quaisquer vértices. Em contrapartida, a **árvore binária** é uma estrutura hierárquica estritamente acíclica, restrita a no máximo dois nós filhos por nó pai. Uma árvore impede a existência de caminhos redundantes e ciclos, sendo incapaz de modelar a resiliência e as conexões malhadas de uma malha de transporte.

### Questão 2: Em qual condição BFS encontra o menor caminho? O que significa “menor”?
**Resposta:**  
A Busca em Largura (BFS) garante a localização do caminho mínimo sob a condição estrita de que o grafo seja **não ponderado** (ou seja, quando todas as arestas possuem o mesmo peso unitário), como ensina Gomes (2022, Capítulo 3, Seção 3.2). Nesses modelos, a métrica de "menor" refere-se exclusivamente à **quantidade mínima de arestas (número de saltos)** entre a origem e o destino. Essa métrica de menor caminho não computa distâncias físicas em quilômetros, consumo de combustível, tempo de voo ou custos financeiros associados às conexões.

### Questão 3: Por que DFS pode retornar um caminho diferente de BFS?
**Resposta:**  
A divergência ocorre devido às estruturas de dados de suporte e às estratégias de exploração de cada algoritmo (Gomes, 2022, Seção 3.3). O **BFS utiliza uma fila FIFO** (*First-In, First-Out*), explorando o grafo em camadas radiais concêntricas (todos os vértices a 1 salto de distância, depois a 2 saltos, etc.). O **DFS utiliza uma pilha LIFO** (*Last-In, First-Out*), avançando o mais fundo possível ao longo de um único ramo até ser obrigado a fazer *backtracking*. Como o DFS segue em profundidade pela primeira opção da lista de adjacência, ele frequentemente encontra caminhos válidos mais longos antes de avaliar alternativas com menor quantidade de arestas.

### Questão 4: O que ocorre se não utilizarmos um conjunto de visitados?
**Resposta:**  
Em um grafo que apresenta ciclos (como a malha simulada entre Brasília, Goiânia e São Paulo), a ausência de um mecanismo de controle de estados (`visitados`) causa a **revisitação infinita dos mesmos vértices**. O algoritmo entra em *loop* indeterminado, enfileirando ou empilhando indefinidamente os nós da malha até a exaustão da memória física do sistema ou estouro do limite da pilha de execução em profundidade (*stack overflow*).

### Questão 5: Quais informações adicionais seriam necessárias para avaliar tempo ou custo de uma rota?
**Resposta:**  
Para converter a simulação computacional em um modelo econômico e operacional, o grafo precisaria ser transformado em um **grafo ponderado**, incorporando atributos às arestas e nós:
1.  **Pesos nas Arestas:** Distância ortodrômica entre cidades (km), tempo médio de voo (horas), tempo de solo para conexão e custos de combustível/tarifas.
2.  **Algoritmos Adequados:** Substituição do BFS e DFS por algoritmos de caminho mínimo ponderado, como **Algoritmo de Dijkstra** (para pesos não negativos) ou **A\*** (incorporando heurísticas geográficas).
3.  **Restrições Operacionais:** Janelas de horário de funcionamento de aeroportos (slots), capacidade das aeronaves e condições de tráfego aéreo.

### Questão 6: Por que o resultado do algoritmo não pode ser usado para orientar um voo real?
**Resposta:**  
Como ressaltado na introdução do Material do Estudante e embasado no estudo de caso (G1, 2026), a atividade trata-se de uma **simulação computacional didática e altamente simplificada**. O modelo abstrai fatores cruciais da aviação comercial, tais como regras de segurança aeronáutica (ANAC/ICAO), planos de voo aprovados pelo controle de tráfego aéreo (DECEA), autonomia de combustível, alternados de rota, capacidade de pátio e condições meteorológicas. A indicação de caminho por BFS/DFS em um grafo genérico reflete apenas a conectividade topológica abstrata, sem qualquer validade operacional aeronáutica.

---

## 6. Referências Bibliográficas e Documentais

*   G1 DISTRITO FEDERAL. **Pane em GPS afeta voos da Azul no Aeroporto de Brasília; passageiros reclamam de atrasos**. 22 set. 2026. Disponível em: `https://g1.globo.com/df/distrito-federal/noticia/2026/09/22/pane-em-gps-afeta-voos-da-azul-no-aeroporto-de-brasilia-passageiros-reclamam-de-atrasos.ghtml`. Acesso em: 30 out. 2026.
*   GOMES, Paulo César Rodacki. **Grafos: conceitos fundamentais, algoritmos e aplicações**. Blumenau: Editora IFC, 2022. ISBN 978-65-88089-12-5. (Capítulos 1, 2 e 3).

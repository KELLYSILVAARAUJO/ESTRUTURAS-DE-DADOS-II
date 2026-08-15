/*
# 📊 Comparação de Algoritmos de Ordenação

Este projeto é um programa em **Linguagem C** desenvolvido para analisar e comparar o desempenho de três algoritmos clássicos de ordenação de dados na memória do computador.

O objetivo principal é entender como cada estrutura lógica se comporta para organizar um conjunto de dados desordenados.

## 🚀 Funcionalidades

- **Geração Aleatória:** Cria um vetor com 100 números inteiros aleatórios (entre 0 e 499).
- **Simulação Justa:** Utiliza uma cópia do mesmo vetor original para testar todos os algoritmos.
- **Medição de Tempo (Desafio Extra):** Calcula e exibe o tempo exato que cada método leva para concluir a tarefa em milissegundos.

## 🧠 Algoritmos Implementados

O projeto traz a implementação modularizada de três métodos conhecidos:

*   **Bubble Sort (Otimizado):** Compara elementos vizinhos e os troca de lugar. Esta versão possui uma trava que interrompe o código caso o vetor já termine de ser ordenado antes do tempo.
*   **Selection Sort:** Divide o vetor entre uma parte organizada e outra não. Ele busca sempre o menor número restante e o joga para o começo.
*   **Insertion Sort:** Funciona igual ao modo como organizamos cartas de baralho nas mãos, pegando um número por vez e inserindo-o na posição correta entre os que já foram olhados.

## 🛠️ Como Executar o Projeto

Você vai precisar de um compilador de C (como o `gcc`) instalado no seu computador.

1. **Clone o repositório:**
   ```bash
   git clone https://github.com
   ```

2. **Entre na pasta do projeto:**
   ```bash
   cd nome-do-repositorio
   ```

3. **Compile o código:**
   ```bash
   gcc main.c -o comparador_ordenacao
   ```

4. **Execute o programa:**
   - No Linux/Mac:
     ```bash
     ./comparador_ordenacao
     ```
   - No Windows:
     ```cmd
     comparador_ordenacao.exe
     ```

## 📋 Exemplo de Saída no Terminal

Ao rodar o programa, você verá uma tela parecida com esta:

```text
Comparativo de Ordenacao (N=100)

Algoritmo: Bubble Sort (Otimizado)
[Vetor Ordenado Exibido Aqui]
Tempo: 0.0420 ms
------------------------------------------
Algoritmo: Selection Sort
[Vetor Ordenado Exibido Aqui]
Tempo: 0.0290 ms
------------------------------------------
Algoritmo: Insertion Sort
[Vetor Ordenado Exibido Aqui]
Tempo: 0.0150 ms
------------------------------------------
```

## 📝 Licença

Este projeto está sob a licença MIT. Consulte o arquivo `LICENSE` para mais detalhes.
*/


#include <stdio.h>

#include <stdlib.h>

#include <time.h>

#include <string.h>

#define TAM 100



void reset(int *dest, const int *orig) {

  memcpy(dest, orig, TAM * sizeof(int));

}



void imprimirVetor(const int *v) {

  for (int i = 0; i < TAM; i++) printf("%d ", v[i]);

  printf("\n\n");

}

void bubbleSort(int *v) {

  for (int i = 0; i < TAM - 1; i++) {

    int trocou = 0;

    for (int j = 0; j < TAM - i - 1; j++) {

      if (v[j] > v[j + 1]) {

        int aux = v[j];

        v[j] = v[j + 1];

        v[j + 1] = aux;

        trocou = 1;

      }

    }

    if (!trocou) break;

  }

}



void selectionSort(int *v) {

  for (int i = 0; i < TAM - 1; i++) {

    int min = i;

    for (int j = i + 1; j < TAM; j++) {

      if (v[j] < v[min]) min = j;

    }

    if (min != i) {

      int aux = v[i];

      v[i] = v[min];

      v[min] = aux;

    }

  }

}



void insertionSort(int *v) {

  for (int i = 1; i < TAM; i++) {

    int chave = v[i];

    int j = i - 1;

    while (j >= 0 && v[j] > chave) {

      v[j + 1] = v[j];

      j--;

    }

    v[j + 1] = chave;

  }

}

void executarTeste(const char *nome, void (*sortFunc)(int*), const int *orig) {

  int temp[TAM];

  reset(temp, orig);

   

  clock_t t = clock();

  sortFunc(temp);

  t = clock() - t;

   

  printf("Algoritmo: %s\n", nome);

  imprimirVetor(temp);

  printf("Tempo: %.4f ms\n", ((double)t * 1000.0) / CLOCKS_PER_SEC);

  printf("------------------------------------------\n");

}



int main() {

  int original[TAM];

  srand((unsigned int)time(NULL));



  for (int i = 0; i < TAM; i++) {

    original[i] = rand() % 500;

  }



  printf("Comparativo de Ordenacao (N=%d)\n\n", TAM);



  executarTeste("Bubble Sort (Otimizado)", bubbleSort, original);

  executarTeste("Selection Sort", selectionSort, original);

  executarTeste("Insertion Sort", insertionSort, original);



  return 0;

}

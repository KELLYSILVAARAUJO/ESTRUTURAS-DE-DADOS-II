

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

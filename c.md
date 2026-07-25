# Guia Completo de Estudos: C e C++ (Do Básico ao Avançado)

Este documento é um guia de estudos abrangente, prático e estruturado para o aprendizado das linguagens **C** e **C++**. Ele cobre desde os conceitos fundamentais de compilação e sintaxe básica até recursos avançados como gerenciamento manual/moderno de memória, STL, POO, metaprogramação e concorrência.

---

## Sumário
1. [Introdução às Linguagens](#1-introdução-às-linguagens)
2. [Ambiente de Desenvolvimento e Compilação](#2-ambiente-de-desenvolvimento-e-compilação)
   - [Instalação dos Compiladores](#instalação-dos-compiladores)
   - [Compilando programas C e C++](#compilando-programas-c-e-c)
   - [Flags Úteis do Compilador](#flags-úteis-do-compilador)
   - [Projetos Multi-arquivos e CMake](#projetos-multi-arquivos-e-cmake)
3. [Linguagem C: Do Básico ao Avançado](#3-linguagem-c-do-básico-ao-avançado)
   - [Sintaxe Básica, Tipos e Operadores](#sintaxe-básica-tipos-e-operadores)
   - [Controle de Fluxo e Funções](#controle-de-fluxo-e-funções)
   - [Ponteiros e Aritmética de Ponteiros](#ponteiros-e-aritmética-de-ponteiros)
   - [Gerenciamento Dinâmico de Memória (Heap vs Stack)](#gerenciamento-dinâmico-de-memória-heap-vs-stack)
   - [Estruturas (`struct`), Uniões (`union`) e Enums (`enum`)](#estruturas-struct-uniões-union-e-enums-enum)
   - [Pré-processador, Macros e Módulos (`.h` e `.c`)](#pré-processador-macros-e-módulos-h-e-c)
   - [Ponteiros para Funções e Callbacks](#ponteiros-para-funções-e-callbacks)
4. [Linguagem C++: Do Básico ao Avançado](#4-linguagem-c-do-básico-ao-avançado)
   - [Transição de C para C++](#transição-de-c-para-c)
   - [Programação Orientada a Objetos (POO)](#programação-orientada-a-objetos-poo)
   - [Gerenciamento Moderno de Memória & RAII](#gerenciamento-moderno-de-memória--raii)
   - [Standard Template Library (STL)](#standard-template-library-stl)
   - [Templates e Metaprogramação](#templates-e-metaprogramação)
   - [Semântica de Movimento (*Move Semantics*)](#semântica-de-movimento-move-semantics)
   - [Concorrência e Multithreading](#concorrência-e-multithreading)
   - [Recursos Modernos (C++11 até C++20/23)](#recursos-modernos-c11-até-c2023)
5. [Ferramentas de Debug e Profiling](#5-ferramentas-de-debug-e-profiling)
6. [Boas Práticas e Roteiro de Estudos](#6-boas-práticas-e-roteiro-de-estudos)

---

## 1. Introdução às Linguagens

| Característica | Linguagem C | Linguagem C++ |
| :--- | :--- | :--- |
| **Ano / Criador** | 1972 - Dennis Ritchie | 1979 - Bjarne Stroustrup |
| **Paradigma** | Procedural, Imperativo | Multi-paradigma (POO, Genérica, Procedural, Funcional) |
| **Nível de Abstração** | Baixo/Médio nível (próximo ao hardware) | Médio/Alto nível (abstrações de custo zero) |
| **Gerenciamento de Memória** | Manual (`malloc`, `free`) | Manual e Automatizado (RAII, Smart Pointers) |
| **Casos de Uso** | Sistemas Operacionais, Firmwares, Sistemas Embarcados, Drivers, Kernel | Jogos 3D, Engines, Navegadores, Sistemas Financeiros, IA/ML (Infraestrutura) |

---

## 2. Ambiente de Desenvolvimento e Compilação

### Instalação dos Compiladores

- **Linux (Ubuntu/Debian):**
  ```bash
  sudo apt update
  sudo apt install build-essential gdb cmake valgrind
  ```
- **macOS (via Homebrew / Xcode Tools):**
  ```bash
  xcode-select --install
  brew install gcc cmake
  ```
- **Windows:**
  - Instalar via **MSYS2** (MinGW-w64) ou através do **Visual Studio** (C++ Build Tools).

---

### Compilando programas C e C++

#### 1. Compilando C com `gcc`:
```bash
# Compilação simples
gcc main.c -o meu_programa_c

# Executando
./meu_programa_c
```

#### 2. Compilando C++ com `g++` ou `clang++`:
```bash
# Compilação padronizada com C++17
g++ -std=c++17 main.cpp -o meu_programa_cpp

# Executando
./meu_programa_cpp
```

---

### Flags Úteis do Compilador

Sempre compile seus códigos com avisos ativados para evitar *bugs* e *Undefined Behavior*:

```bash
g++ -Wall -Wextra -Wpedantic -std=c++20 -O2 main.cpp -o programa
```

- `-Wall`: Ativa avisos (*warnings*) principais sobre práticas suspeitas.
- `-Wextra`: Ativa avisos adicionais.
- `-Wpedantic`: Garante estrita conformidade com o padrão ISO de C/C++.
- `-std=c++20`: Define a versão da linguagem (ex: `c11`, `c17`, `c++14`, `c++17`, `c++20`, `c++23`).
- `-O0` / `-O2` / `-O3`: Níveis de otimização de código (0 = sem otimização/debug, 2/3 = produção).
- `-g`: Inclui símbolos de depuração (necessário para GDB/LLDB).

---

### Projetos Multi-arquivos e CMake

Em projetos reais, os programas são divididos em múltiplos arquivos de cabeçalho (`.h` / `.hpp`) e código fonte (`.c` / `.cpp`).

#### Estrutura de Arquivos Recomendada:
```text
meu_projeto/
├── CMakeLists.txt
├── include/
│   └── math_utils.hpp
└── src/
    ├── math_utils.cpp
    └── main.cpp
```

#### Exemplo de `CMakeLists.txt`:
```cmake
cmake_minimum_required(VERSION 3.16)
project(MeuProjeto VERSION 1.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

include_directories(include)

file(GLOB SOURCES "src/*.cpp")

add_executable(MeuProjeto ${SOURCES})
```

#### Passos para compilar com CMake:
```bash
mkdir build && cd build
cmake ..
make
./MeuProjeto
```

---

## 3. Linguagem C: Do Básico ao Avançado

### Sintaxe Básica, Tipos e Operadores

```c
#include <stdio.h>

int main(void) {
    // Tipos primitivos
    int idade = 25;
    float altura = 1.75f;
    double precisao = 3.1415926535;
    char inicial = 'A';
    
    printf("Idade: %d | Altura: %.2f | Inicial: %c
", idade, altura, inicial);
    return 0;
}
```

---

### Controle de Fluxo e Funções

```c
#include <stdio.h>

int somar(int a, int b) {
    return a + b;
}

int main(void) {
    int x = 10;
    
    if (x > 5) {
        printf("x é maior que 5
");
    } else {
        printf("x é menor ou igual a 5
");
    }

    for (int i = 0; i < 3; i++) {
        printf("Iteração %d | Soma: %d
", i, somar(x, i));
    }
    
    return 0;
}
```

---

### Ponteiros e Aritmética de Ponteiros

Um ponteiro armazena o **endereço de memória** de outra variável.

```c
#include <stdio.h>

int main(void) {
    int valor = 42;
    int *ptr = &valor; // ptr guarda o endereço de 'valor'

    printf("Valor original: %d
", valor);
    printf("Endereço de valor (&valor): %p
", (void*)&valor);
    printf("Conteúdo de ptr (endereço): %p
", (void*)ptr);
    printf("Valor apontado (*ptr): %d
", *ptr);

    // Modificando via ponteiro
    *ptr = 100;
    printf("Novo valor de 'valor': %d
", valor);

    // Aritmética de ponteiros com arrays
    int arr[3] = {10, 20, 30};
    int *p_arr = arr;

    printf("Primeiro elemento: %d
", *p_arr);
    printf("Segundo elemento: %d
", *(p_arr + 1)); // Deslocamento na memória
    return 0;
}
```

---

### Gerenciamento Dinâmico de Memória (Heap vs Stack)

- **Stack (Pilha):** Memória de rápida alocação, gerenciada automaticamente pelo escopo da função.
- **Heap (Monte):** Memória manual e flexível alocada dinamicamente via funções da libc.

```c
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    int n = 5;
    
    // Alocação dinâmica na Heap
    int *v = (int*) malloc(n * sizeof(int));
    if (v == NULL) {
        fprintf(stderr, "Erro de alocação de memória!
");
        return 1;
    }

    // Inicialização
    for (int i = 0; i < n; i++) {
        v[i] = (i + 1) * 10;
    }

    // Redimensionamento de memória
    v = (int*) realloc(v, (n + 2) * sizeof(int));
    v[5] = 60;
    v[6] = 70;

    for (int i = 0; i < 7; i++) {
        printf("%d ", v[i]);
    }
    printf("
");

    // Liberação OBRIGATÓRIA da memória
    free(v);
    v = NULL; // Previne dangling pointer

    return 0;
}
```

---

### Estruturas (`struct`), Uniões (`union`) e Enums (`enum`)

```c
#include <stdio.h>

typedef enum {
    STATUS_OK,
    STATUS_ERROR,
    STATUS_PENDING
} Status;

typedef struct {
    int id;
    char nome[50];
    float salario;
} Funcionario;

typedef union {
    int valor_int;
    float valor_float;
    char byte[4];
} DadoFlexivel; // Compartilha a mesma região de memória

int main(void) {
    Funcionario f1 = {1, "Alice", 5500.0f};
    Status st = STATUS_OK;

    printf("Funcionario: %s (ID: %d) - Salário: %.2f | Status code: %d
",
           f1.nome, f1.id, f1.salario, st);

    return 0;
}
```

---

### Pré-processador, Macros e Módulos (`.h` e `.c`)

#### `utils.h`
```c
#ifndef UTILS_H
#define UTILS_H

#define MAX_BUFFER 1024
#define QUADRADO(x) ((x) * (x))

int multiplicar(int a, int b);

#endif // UTILS_H
```

#### `utils.c`
```c
#include "utils.h"

int multiplicar(int a, int b) {
    return a * b;
}
```

---

### Ponteiros para Funções e Callbacks

Permite passar funções como argumentos para outras funções (base para desacoplamento e polimorfismo em C).

```c
#include <stdio.h>

void executar_operacao(int a, int b, int (*operacao)(int, int)) {
    int resultado = operacao(a, b);
    printf("Resultado da operação: %d
", resultado);
}

int soma(int a, int b) { return a + b; }
int subtracao(int a, int b) { return a - b; }

int main(void) {
    executar_operacao(10, 5, soma);
    executar_operacao(10, 5, subtracao);
    return 0;
}
```

---

## 4. Linguagem C++: Do Básico ao Avançado

### Transição de C para C++

C++ traz melhorias de usabilidade, tipos mais fortes e a biblioteca `std`.

```cpp
#include <iostream>
#include <string>
#include <vector>

void exemplo_referencias(int& ref) {
    ref += 10; // Altera diretamente o valor da variável original
}

int main() {
    std::string nome = "Engenheiro de C++";
    std::cout << "Olá, " << nome << "!" << std::endl;

    int valor = 5;
    exemplo_referencias(valor);
    std::cout << "Valor após referência: " << valor << std::endl; // 15

    return 0;
}
```

---

### Programação Orientada a Objetos (POO)

Conceitos centrais: Encapsulamento, Herança, Polimorfismo e Destrutores.

```cpp
#include <iostream>
#include <string>
#include <memory>

// Classe Abstrata (Interface)
class Animal {
protected:
    std::string nome;
public:
    Animal(const std::string& n) : nome(n) {}
    virtual ~Animal() = default; // Destrutor virtual indispensável para polimorfismo

    virtual void emitirSom() const = 0; // Função virtual pura
};

class Cachorro : public Animal {
public:
    Cachorro(const std::string& n) : Animal(n) {}

    void emitirSom() const override {
        std::cout << nome << " diz: Au Au!
";
    }
};

int main() {
    std::unique_ptr<Animal> pet = std::make_unique<Cachorro>("Rex");
    pet->emitirSom(); // Polimorfismo dinâmico
    return 0;
}
```

---

### Gerenciamento Moderno de Memória & RAII

**RAII (Resource Acquisition Is Initialization):** Recursos são atrelados ao tempo de vida dos objetos no escopo. Em C++ moderno, **evite `new` e `delete` diretos**. Utilize **Smart Pointers**:

- `std::unique_ptr`: Posse exclusiva.
- `std::shared_ptr`: Posse compartilhada (Contagem de referências).
- `std::weak_ptr`: Referência fraca sem incremento de contagem para quebrar ciclos de referência.

```cpp
#include <iostream>
#include <memory>

class Recurso {
public:
    Recurso() { std::cout << "Recurso Alocado!
"; }
    ~Recurso() { std::cout << "Recurso Destruído (Sem Leaks)!
"; }
    void usar() { std::cout << "Usando recurso...
"; }
};

int main() {
    {
        std::unique_ptr<Recurso> res = std::make_unique<Recurso>();
        res->usar();
    } // O recurso é LIBERADO AUTOMATICAMENTE ao sair deste bloco!

    return 0;
}
```

---

### Standard Template Library (STL)

A STL fornece estruturas de dados de alto desempenho e algoritmos genéricos.

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
#include <algorithm>

int main() {
    // Vector (Array Dinâmico)
    std::vector<int> numeros = {5, 2, 8, 1, 9};
    numeros.push_back(4);

    // Algoritmo STL: Ordenação
    std::sort(numeros.begin(), numeros.end());

    std::cout << "Vetor ordenado: ";
    for (int n : numeros) {
        std::cout << n << " ";
    }
    std::cout << "
";

    // Unordered Map (Tabela Hash)
    std::unordered_map<std::string, int> idades;
    idades["Carlos"] = 30;
    idades["Beatriz"] = 25;

    std::cout << "Idade da Beatriz: " << idades["Beatriz"] << std::endl;

    return 0;
}
```

---

### Templates e Metaprogramação

Permite a criação de código genérico e processado em tempo de compilação.

```cpp
#include <iostream>

// Template de Função
template <typename T>
T maior(T a, T b) {
    return (a > b) ? a : b;
}

// Template de Classe
template <typename T, std::size_t N>
class ArrayEstatico {
    T dados[N];
public:
    std::size_t tamanho() const { return N; }
};

int main() {
    std::cout << "Maior int: " << maior(10, 20) << std::endl;
    std::cout << "Maior double: " << maior(15.5, 3.2) << std::endl;

    ArrayEstatico<int, 10> arr;
    std::cout << "Tamanho da estrutura fixa: " << arr.tamanho() << std::endl;

    return 0;
}
```

---

### Semântica de Movimento (*Move Semantics*)

Introduzida no C++11 para evitar cópias desnecessárias de dados pesados, transferindo a posse de recursos de Rvalues (`std::move`).

```cpp
#include <iostream>
#include <utility>
#include <vector>

class BigData {
    std::vector<int> buffer;
public:
    BigData(size_t sz) : buffer(sz, 42) {}

    // Construtor de Movimento (Move Constructor)
    BigData(BigData&& other) noexcept : buffer(std::move(other.buffer)) {
        std::cout << "Dados MOVIDOS sem cópia pesada!
";
    }
};

int main() {
    BigData a(1000000);
    BigData b = std::move(a); // Transfere a posse do vetor interno de 'a' para 'b'
    return 0;
}
```

---

### Concorrência e Multithreading

```cpp
#include <iostream>
#include <thread>
#include <mutex>
#include <vector>

std::mutex mtx;
int contador_global = 0;

void incremementar(int id) {
    for (int i = 0; i < 1000; ++i) {
        std::lock_guard<std::mutex> lock(mtx); // RAII para Mutex
        contador_global++;
    }
}

int main() {
    std::vector<std::thread> threads;
    
    for (int i = 0; i < 5; ++i) {
        threads.emplace_back(incremementar, i);
    }

    for (auto& t : threads) {
        t.join();
    }

    std::cout << "Valor final do contador concorrente: " << contador_global << std::endl;
    return 0;
}
```

---

### Recursos Modernos (C++11 até C++20/23)

- **C++11/14:** `auto`, `lambda expressions`, `nullptr`, `constexpr`, `smart pointers`.
- **C++17:** `std::optional`, `std::variant`, `structured bindings`, `if constexpr`.
- **C++20:** `Concepts` (restrição de tipos em templates), `Ranges`, `Corrotinas`, `Modules`.

```cpp
#include <iostream>
#include <optional>
#include <concepts>

// C++20: Concept definindo que o tipo T deve ser somável
template <typename T>
concept Somavel = requires(T a, T b) {
    { a + b } -> std::convertible_to<T>;
};

template <Somavel T>
T somar_concept(T a, T b) {
    return a + b;
}

// C++17: Retorno opcional
std::optional<int> dividir(int a, int b) {
    if (b == 0) return std::nullopt;
    return a / b;
}

int main() {
    auto res = dividir(10, 2);
    if (res.has_value()) {
        std::cout << "Resultado divisão: " << res.value() << "
";
    }

    std::cout << "Soma Concept: " << somar_concept(10, 20) << "
";
    return 0;
}
```

---

## 5. Ferramentas de Debug e Profiling

### 1. GDB (GNU Debugger)
```bash
# Compilar com flags de debug
g++ -g main.cpp -o app_debug

# Iniciar GDB
gdb ./app_debug

# Comandos principais do GDB:
(gdb) break main        # Define ponto de parada na função main
(gdb) run               # Executa o programa
(gdb) next              # Avança para a próxima linha
(gdb) step              # Entra na função
(gdb) print variavel    # Imprime o valor da variável
(gdb) backtrace         # Mostra a pilha de execução (call stack)
(gdb) quit              # Sai do depurador
```

### 2. Valgrind (Detecção de Memória & Memory Leaks)
```bash
valgrind --leak-check=full --show-leak-kinds=all ./meu_programa
```

### 3. AddressSanitizer (ASan - Nativo nos compiladores)
Muito mais rápido que o Valgrind para detectar acessos inválidos e leaks:
```bash
g++ -fsanitize=address,undefined -g main.cpp -o app_asan
./app_asan
```

---

## 6. Boas Práticas e Roteiro de Estudos

### Boas Práticas Fundamentais
1. **Evite variáveis globais:** Prefira encapsulamento e passagem por referência const (`const T&`).
2. **Const-Correctness:** Declare como `const` tudo o que não deve ser modificado (métodos, parâmetros e variáveis).
3. **Gerenciamento RAII:** Em C++, nunca aloque recursos sem utilizar RAII ou Smart Pointers.
4. **Inicialize todas as variáveis:** Em C e C++, variáveis não inicializadas contêm lixo de memória.
5. **Use e abuse das Flags de Warning:** Trate avisos (`-Wall -Wextra`) como se fossem erros (`-Werror`).

### Roteiro de Estudos Recomendado (Roadmap)
1. **Fase 1 (Base em C):** Sintaxe, Estruturas de controle, Ponteiros, Aritmética de Ponteiros, Alocação Dinâmica (`malloc`/`free`).
2. **Fase 2 (Fundamentos C++):** Referências, Namespaces, Programação Orientada a Objetos, Construtores/Destrutores, RAII.
3. **Fase 3 (STL e C++ Moderno):** Vectors, Maps, Iteradores, Algoritmos, Smart Pointers (`unique_ptr`, `shared_ptr`), Lambdas.
4. **Fase 4 (Avançado):** Templates, Move Semantics, Multithreading, Concepts, Padrões de Projeto (Design Patterns).
5. **Fase 5 (Engenharia de Software):** CMake, Testes Unitários (GoogleTest/Catch2), Profiling e Sanitizers.

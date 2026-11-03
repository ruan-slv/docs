# Guia Definitivo: Full-Stack Dart com Flutter, Dart Frog e BLoC

Este documento é um guia completo para estruturar, desenvolver e gerenciar uma aplicação Full-Stack utilizando exclusivamente a linguagem **Dart**. A arquitetura apresentada utiliza **Flutter** no frontend, **Dart Frog** no backend, **BLoC** para gerenciamento de estado e **Melos** para orquestração do monorepo.

---

## 1. O Ecossistema e as Ferramentas

### O que é o Dart e o Flutter?
* **Dart:** A linguagem de programação da Google, com tipagem estática segura (Sound Null Safety) e compilação dupla (JIT para desenvolvimento ágil e AOT para performance nativa).
* **Flutter:** O framework de UI que usa Dart para compilar aplicativos nativos para Mobile, Web e Desktop a partir de uma única base de código. Trabalha com uma árvore hierárquica onde "tudo é um widget".

### O que é o Dart Frog?
Um framework backend minimalista e veloz para Dart, construído sobre o pacote `shelf`. Ele utiliza roteamento baseado em arquivos (semelhante ao Next.js), o que facilita a criação de APIs REST e microsserviços.

### O que é o BLoC?
O padrão de gerenciamento de estado (Business Logic Component). Funciona separando a lógica da interface visual. Pense no BLoC como uma **cozinha fechada**:
1. **Eventos (A Fresta de Entrada):** A tela avisa que algo aconteceu (ex: `BotaoSalvarPressionado`).
2. **Estados (A Fresta de Saída):** A cozinha processa o pedido e entrega um prato pronto (ex: `TelaSucesso` ou `TelaErro`). A interface apenas reage a esses estados imutáveis.

### O que é o Melos?
Uma ferramenta de CLI para gerenciar projetos Dart organizados em múltiplos pacotes (Monorepo). Ele resolve automaticamente as dependências locais entre a sua API, seu App e seus pacotes compartilhados.

---

## 2. Arquitetura do Projeto (Monorepo)

A estrutura ideal (Clean Architecture Pragmática) organiza o projeto em pacotes independentes dentro de um único repositório:

```text
meu_monorepo/
├── melos.yaml                 # Configuração de scripts e pacotes do Melos
├── pubspec.yaml               # Raiz do projeto
├── apps/
│   ├── api/                   # Backend (Dart Frog)
│   └── mobile/                # Frontend (Flutter + BLoC)
└── packages/
    └── shared_models/         # Modelos e regras de negócio compartilhadas
```

---

## 3. Guia de Comandos (CLI) Explicados

Antes de codificar, você precisa dominar o terminal. Aqui estão os comandos essenciais e o que cada um faz nos bastidores.

### Comandos de Instalação Global
* `dart pub global activate melos`: Instala o Melos no seu computador. Permite usar o comando `melos` de qualquer terminal.
* `dart pub global activate dart_frog_cli`: Instala a CLI do Dart Frog. Permite usar os comandos `dart_frog` para criar e rodar rotas.

### Comandos do Melos
* `melos bootstrap` (ou `melos bs`): **O comando mais importante.** Ele lê o seu `melos.yaml`, percorre todas as pastas do projeto e faz o `pub get` em todas elas. Além disso, ele cria "atalhos" virtuais (symlinks) para que o `apps/mobile` enxergue o `packages/shared_models` localmente sem precisar baixar da internet.
* `melos run <script>`: Executa um script personalizado definido no seu `melos.yaml`. Exemplo: `melos run analyze` roda a checagem de erros em todos os projetos de uma vez.

### Comandos do Dart Frog
* `dart_frog create api`: Cria um novo projeto backend limpo chamado "api".
* `dart_frog dev`: Inicia o servidor local com *Hot Reload*. Qualquer alteração no código do backend será refletida sem precisar reiniciar o servidor.
* `dart_frog new route /users`: Cria automaticamente o arquivo e a estrutura para o endpoint `/users`.

---

## 4. Passo a Passo da Implementação

### Passo 1: Criando o Pacote Compartilhado (`shared_models`)
Aqui definimos o contrato que tanto o frontend quanto o backend vão respeitar.

**`packages/shared_models/lib/src/user.dart`**
```dart
class User {
  final String id;
  final String name;

  const User({required this.id, required this.name});

  factory User.fromJson(Map<String, dynamic> json) => User(
    id: json['id'] as String,
    name: json['name'] as String,
  );

  Map<String, dynamic> toJson() => {'id': id, 'name': name};
}
```

### Passo 2: O Backend (`apps/api`)
O Dart Frog consome o pacote para validar e retornar dados.

**`apps/api/routes/users/index.dart`**
```dart
import 'dart:io';
import 'package:dart_frog/dart_frog.dart';
import 'package:shared_models/shared_models.dart';

Future<Response> onRequest(RequestContext context) async {
  if (context.request.method == HttpMethod.get) {
    // Usando o modelo compartilhado
    final user = User(id: '1', name: 'Alice');
    return Response.json(body: [user.toJson()]);
  }
  return Response(statusCode: HttpStatus.methodNotAllowed);
}
```

### Passo 3: O Repositório e BLoC no Frontend (`apps/mobile`)
O Flutter chama a API, decodifica a resposta usando o `shared_models` e passa a informação para o BLoC gerenciar a tela.

**Repositório (`user_repository.dart`)**
```dart
import 'dart:convert';
import 'package:http/http.dart' as http;
import 'package:shared_models/shared_models.dart';

class UserRepository {
  Future<List<User>> fetchUsers() async {
    final response = await http.get(Uri.parse('http://localhost:8080/users'));
    final list = jsonDecode(response.body) as List<dynamic>;
    return list.map((e) => User.fromJson(e)).toList();
  }
}
```

**BLoC (`user_bloc.dart`)**
```dart
import 'package:flutter_bloc/flutter_bloc.dart';

// Eventos e Estados resumidos para exemplo
class UserLoad extends UserEvent {}
class UserSuccess extends UserState { final List<User> users; ... }

class UserBloc extends Bloc<UserEvent, UserState> {
  final UserRepository repository;

  UserBloc(this.repository) : super(UserInitial()) {
    on<UserLoad>((event, emit) async {
      emit(UserLoading());
      try {
        final users = await repository.fetchUsers();
        emit(UserSuccess(users));
      } catch (e) {
        emit(UserFailure());
      }
    });
  }
}
```

---

## 5. Resumo das Vantagens dessa Arquitetura

1. **Tipagem Ponta a Ponta (End-to-End Type Safety):** Se você renomear a variável `name` para `fullName` no pacote `shared_models`, tanto o compilador do Dart Frog quanto o do Flutter vão acusar erro imediatamente. Adeus erros de parsing de JSON em produção!
2. **Produtividade do Monorepo:** Uma única Pull Request contém a alteração do banco de dados, o roteamento da API e a tela do App, mantendo todo o contexto unificado.
3. **Reatividade Previsível:** Com o `flutter_bloc`, a interface gráfica (UI) nunca altera dados diretamente e a lógica de negócio é facilmente testável (Unit Tests) sem depender de bibliotecas visuais do Flutter.
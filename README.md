# Exemplo de Arquitetura MVVM em Java (Console)

Este projeto é um exemplo puramente didático criado para ilustrar a organização de arquivos e a separação de responsabilidades seguindo a arquitetura **MVVM (Model-View-ViewModel)**.

O foco aqui é demonstrar como uma aplicação (neste caso, de console) pode ser estruturada para separar:
1.  **Model**: As regras de negócio e dados.
2.  **View**: A interação com o usuário (menus, entradas, mensagens).
3.  **ViewModel**: O intermediário que prepara os dados para a View.

**Importante:** O código neste repositório não é funcional. Seu único propósito é demonstrar como os pacotes e classes se relacionam.

## 🏛️ Arquitetura e Componentes

### 1. Model (Modelo)

Representa o "cérebro" da aplicação. Contém os dados, as regras de negócio e a lógica de acesso à fonte de dados (banco de dados, API, etc.). É totalmente independente da interface.

* `model/entities`: As classes de entidade (POJOs) que mapeiam os dados.
    * `Livros.java`: Define a estrutura de um "Livro".
* `model/dao`: (Data Access Object) Camada que executa as operações de persistência (CRUD - Create, Read, Update, Delete).
    * `LivrosDAO.java`: Lógica para salvar, buscar, atualizar ou deletar `Livros` da fonte de dados.
* `model/service`: Camada de regras de negócio. Orquestra os DAOs e aplica a lógica de negócios.
    * `LivrosService.java`: Exemplo: "Um livro não pode ser salvo sem um autor". Ele chama o `LivrosDAO` para persistir os dados.

### 2. View (Visão)

Representa a camada de interação com o usuário (UI). No MVVM, a View deve ser o mais "burra" (dumb) possível. Ela apenas exibe o que o ViewModel manda e captura as ações do usuário.

Nesta estrutura, a camada `View` foi subdividida para melhor organização:

* `app/Main.java`: Ponto de entrada da aplicação. Inicia o loop principal e chama a View.
* `view/controller`: Gerencia o fluxo de navegação da interface.
    * `menus.java`: Controla qual menu é exibido, para onde o usuário vai após uma escolha, etc.
* `view/inputs`: Classes utilitárias para capturar dados do usuário.
    * `EntradaDeDados.java`: Centraliza a lógica de leitura do console (ex: ler um texto, ler um número).
* `view/messages`: Classes utilitárias para exibir feedback ao usuário.
    * `Mensagens.java`: Centraliza a exibição de todas as mensagens (ex: "Livro salvo com sucesso!", "Erro: Opção inválida.").

### 3. ViewModel (Visão-Modelo)

É o "garçom" da aplicação, o intermediário entre o Model e a View.

1.  A **View** (`menus.java`) solicita algo (ex: "Quero listar os títulos dos livros").
2.  O **ViewModel** (uma classe como `LivrosViewModel`, não mostrada mas implícita) recebe o pedido.
3.  O **ViewModel** vai até o **Model** (`LivrosService`) e pede os dados brutos (`List<Livros>`).
4.  O **ViewModel** transforma esses dados brutos em dados simples e formatados para a View, usando DTOs.
5.  Ele entrega os DTOs (`List<TituloDTO>`) para a **View** (`menus.java` ou `Mensagens.java`) apenas exibir.

* `viewmodel/dto`: (Data Transfer Objects) Classes simples para transportar dados formatados para a View, escondendo a complexidade do Model.
    * `AutorDTO.java`: Leva para a View apenas os dados do autor.
    * `TituloDTO.java`: Leva para a View apenas o título do livro.

### 4. Pacotes Auxiliares

* `exceptions`: Pacote para centralizar exceções customizadas da aplicação.
    * `Validacoes.java`: Pode conter exceções personalizadas (ex: `ValidacaoException`) ou classes que disparam essas exceções, garantindo que as regras de entrada sejam cumpridas.

## 📁 Estrutura de Arquivos

```plaintext
.
├── .gitignore
├── .idea/             (Pasta de configuração da IDE)
├── pom.xml            (Arquivo de configuração do Maven)
└── src
    └── main
        └── java
            └── senai
                └── MI78
                    ├── app
                    │   └── Main.java         (Ponto de Entrada - View)
                    ├── exceptions
                    │   └── Validacoes.java   (Exceções Customizadas)
                    ├── model
                    │   ├── dao
                    │   │   └── LivrosDAO.java
                    │   ├── entities
                    │   │   └── Livros.java
                    │   └── service
                    │       └── LivrosService.java
                    ├── view
                    │   ├── controller
                    │   │   └── menus.java
                    │   ├── inputs
                    │   │   └── EntradaDeDados.java
                    │   └── messages
                    │       └── Mensagens.java
                    └── viewmodel
                        └── dto
                            ├── AutorDTO.java
                            └── TituloDTO.java

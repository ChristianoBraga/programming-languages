# LP pela semântica natural, proposta de curso

Proposta de 17/09/2026 para a disciplina 09022, Linguagens de Programação, sobre o Pladis 2025. A ementa é cumprida na ordem e nas horas do plano. O que muda é o método. Cada conceito de linguagem é apresentado como uma extensão de uma linguagem núcleo, Core C++, cujas regras de tipos e de avaliação são escritas em semântica natural no quadro e codificadas em Lean 4. O projeto de Core C++ está em `core-cpp-design.md`.

## Método

1. **Um objeto único.** Core C++ cresce da UD II à UD VI. Cada conceito novo acrescenta sintaxe abstrata, regras de tipos e regras de avaliação. A UD VII lê os paradigmas como restrições do mesmo núcleo.
2. **Regras no papel.** A semântica natural (Kahn 1987) é a notação de aula e de prova. Os alunos vêm de Lógica Matemática (09006, 3º ano), com dedução natural e indução, então regras de inferência e derivações são um uso novo de algo conhecido.
3. **Um só modelo de memória.** Ambiente ρ de identificadores para posições, memória σ de posições para valores, desde a UD II. Nenhuma regra é reescrita depois. Só entram regras novas.
4. **Lean como linguagem de programação para codificar a semântica.** Sintaxe abstrata, analisadores, regras de tipos e regras de avaliação são programas Lean. O professor os mostra funcionando e os alunos os modificam em uma sessão de laboratório e no trabalho. Nenhuma prova em Lean é exigida.
5. **C++ como linguagem concreta.** Core C++ é um subconjunto de C++17. Todo programa do núcleo compila com `g++`, e cada regra pode ser confrontada com o compilador. Uma segunda linguagem entra só onde o contraste ensina algo, Python para tipagem dinâmica, Haskell para avaliação preguiçosa, inferência e tipos soma, Prolog na UD VII.

## Como a ementa é cumprida

| UD | Assuntos do Pladis | Como o método os cobre |
|---|---|---|
| I, 8 h | Conceitos e paradigmas. Sintaxe e semântica. Processadores de linguagens. | A gramática LL(1) de Core C++ e sua árvore de sintaxe abstrata. A semântica natural como um interpretador escrito em regras. Compilador e interpretador como dois processadores da mesma gramática. Um mesmo programa em C++, Python, Haskell e Prolog. |
| II, 8 h | Valores e tipos. Primitivos, compostos, recursivos. Sistemas de tipos e completude. Expressões. | Núcleo com `int`, `bool`, classes só com campos, ponteiro para classe com `new` como tipo recursivo, e `std::vector<int>` criado com `new` como qualquer objeto. Juízo Γ ⊢ e : τ e regras de avaliação já com ρ e σ. Princípio da completude do Watt, com o vetor de C como violação e justificativa da classe vetor. |
| III, 8 h | Variáveis e atualização. Atributos. Comandos. Expressões com efeitos colaterais. | Atribuição, referência local, sequência, `if`, `while`, `for`, bloco. O juízo de posição. Escopo como restauração de ρ, tempo de vida como retirada das posições locais de σ. A ordem da esquerda para a direita de Core C++ em `f() + g()`, a outra derivação que um compilador C++ pode escolher, e a ordem fixa da atribuição. |
| IV, 8 h | Tipos de abstração. Parâmetros. Avaliação de parâmetros. | Funções, chamada por valor e por referência, lambda `[=]` como closure com ambiente de cópias, `return` como resultado de controle. A captura por referência `[&]` e o closure pendente em C++ real. Chamada por nome como regra sem contraparte no núcleo, com Haskell para a avaliação preguiçosa. |
| V, 8 h | Tipos abstratos. Objetos e classes. OO e processos de desenvolvimento. | `class` com `private` e `public`, construtor, destrutor e `delete`, `this`, `virtual`, herança simples, `namespace`. Tipo abstrato como assinatura pública mais representação privada em Γ. Objeto como registro de posições com etiqueta de classe, despacho pela etiqueta. Processos de desenvolvimento em discussão, sem regras. |
| VI, 8 h | Sobrecarga. Polimorfismo. Inferência de tipos. Herança. | Sobrecarga de funções e operadores por tipo em Γ. Templates de classe como substituição na instanciação. `auto` como cópia do tipo do inicializador, e Haskell para Hindley e Milner. Regra de subsunção. `v[i]` revelado como `operator[]` devolvendo `int&`. |
| VII, 10 h | Imperativo. Orientado a objetos. Funcional. Lógico. | Cada paradigma como uma restrição do núcleo. O Prolog de 09006 com a resolução SLD reapresentada como semântica operacional. Um estudo de caso nos quatro paradigmas, três deles no mesmo C++, e a comparação do objetivo b do plano. |

## Lean em três momentos

1. **UD I, demonstração.** Sintaxe abstrata como tipo indutivo, analisador léxico e analisador descendente recursivo, Γ, ρ e σ como funções finitas, avaliação como relação indutiva, e a execução de programas do subconjunto.
2. **UD III ou IV, laboratório opcional.** Os alunos acrescentam ao interpretador uma construção pequena, o `for` a partir do `while` ou a referência local, e a testam. Editor web do Lean, sem instalação.
3. **Trabalho da terceira avaliação.** Cada grupo escolhe uma construção, escreve suas regras no papel, a implementa no interpretador e a compara com `g++` e com uma segunda linguagem. Vale pela execução dos exemplos. Prova em Lean é bônus.

Custo, três sessões das 58.

## Extensões de Core C++

Uma extensão acrescenta sintaxe, regras de tipos ou regras de avaliação a Core C++ sem reescrever nenhuma regra do núcleo. Ela fica fora do núcleo, pelo princípio 3 de `core-cpp-design.md`.

**Tipos lineares, UD V.** Registrada em 30/10/2026. O juízo de posse O ⊢ c ⇒ O' acompanha o conjunto O das variáveis que possuem um objeto vivo criado com `new`. A declaração `T* x = new T(...)` acrescenta x a O. O comando `delete x` exige x ∈ O e retira x de O. O acesso `x->f` exige x ∈ O. A atribuição a uma variável de O é rejeitada, porque descartaria o objeto sem `delete`. O corpo de um `while` preserva O. Um programa tipado com ∅ ⊢ c ⇒ ∅ libera tudo o que aloca. A extensão rejeita na verificação de tipos o `delete` duplo e o acesso após `delete`, que o núcleo só detecta na execução como `erro`, em `core-cpp/benchmark/ub/double_delete.cpp` e `use_after_delete.cpp`. O modelo é a relação `LinearOk` de Radix, em `Radix/Linear.lean` de github.com/leodemoura/RadixExperiment, com o teorema de correção `LinearOk.soundness`. O contraponto em C++ real é `std::unique_ptr`, cujo construtor de cópia é removido (N4659 §23.11.1.2). A origem está na lógica linear de Girard e nos tipos lineares de Wadler.

## Avaliação, como no Pladis 2025

| Prova | UD | O que se cobra |
|---|---|---|
| VE, 2 h | I e II | Regras de tipos, derivações de tipagem e de avaliação com ρ e σ, pertinência de um programa C++ ao subconjunto, classificação de linguagens. |
| VC, 4,8 h | I a IV | Derivações de execução de comandos, a mesma chamada por valor e por referência, a captura de um lambda, explicação de um comportamento de C++ pelas regras. |
| Trabalho | todas | A extensão do núcleo. |
| VF, 4,8 h | todas | Tudo acima, regras de método, despacho e subtipo, instanciação de template, comparação de paradigmas pelo núcleo. |

Nenhuma prova escrita pede código Lean.

## Riscos

- **O modelo de memória chega cedo.** Custa uma tabela a mais nas derivações da UD II. Em troca, atribuição, referência, objeto e closure entram sem reescrever nada.
- **C++ convida ao comportamento indefinido.** Core C++ não tem nenhum. Cada ponto em que C++ real diverge do núcleo vira exemplo de aula, e a lista está no projeto de Core C++.
- **Alunos sem experiência funcional.** Lambdas chegam por regras, sobre sintaxe de C++, e Haskell é só contraste.
- **Tempo.** A semântica natural custa sessões nas UD I e II e devolve nas UD III a VI, onde cada conceito é uma regra a mais.
- **Divergência de programas.** A semântica de passo largo indutiva não descreve execuções que não terminam (Leroy e Grall). O curso enuncia a limitação e não a corrige.

## Bibliografia

- Watt, Programming Language Concepts and Paradigms, Prentice Hall, 1990. O esqueleto das UD. Edição posterior, Watt e Findlay, Programming Language Design Concepts, Wiley, 2004.
- Kahn, Natural Semantics, STACS 1987, LNCS 247, pp. 22 a 39, Springer.
- Nielson e Nielson, Semantics with Applications, A Formal Introduction, Wiley, 1992, edição revista de 1999 disponível pelos autores.
- Pierce, Types and Programming Languages, MIT Press, 2002.
- Leroy e Grall, Coinductive big-step operational semantics, xavierleroy.org/publi/coindsem-journal.pdf.
- ISO/IEC 14882:2017, C++17.
- Girard, Linear logic, Theoretical Computer Science 50(1), pp. 1 a 102, 1987.
- Wadler, Linear types can change the world!, em Programming Concepts and Methods, North‑Holland, 1990, p. 561. Os registros bibliográficos divergem quanto aos editores, a conferir no volume.
- de Moura, The Lean Programming Language and Theorem Prover, tutorial do ETAPS 2026, com a linguagem Radix, github.com/leodemoura/RadixExperiment.

## Decisões

Tomadas em 17/09/2026. Estrutura do Watt e horas do Pladis 2025. Semântica natural como notação. Modelo de memória único desde a UD II. Lean 4 para codificar analisadores e semântica, com analisador descendente recursivo escrito à mão. Core C++ como núcleo, projeto em `core-cpp-design.md`. Sebesta fora da bibliografia.

Em aberto. A UD da sessão de laboratório, III ou IV.

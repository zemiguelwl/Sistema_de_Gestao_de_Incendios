# 🔥 Sistema de Gestão de Incêndios (SGI)
> Trabalho Prático — Laboratório de Programação | ESTG · P.PORTO | 2025/2026

---

## 📋 Índice

- [Descrição do Projeto](#-descrição-do-projeto)
- [Funcionalidades](#-funcionalidades)
- [Arquitetura do Sistema](#-arquitetura-do-sistema)
- [Estrutura de Ficheiros](#-estrutura-de-ficheiros)
- [Modelo de Dados](#-modelo-de-dados)
- [Persistência de Dados](#-persistência-de-dados)
- [Sistema de Logs](#-sistema-de-logs)
- [Relatórios Disponíveis](#-relatórios-disponíveis)
- [Funcionalidade Adicional](#-funcionalidade-adicional)
- [Decisões de Design](#-decisões-de-design)
- [Documentação Doxygen](#-documentação-doxygen)
- [Autores](#-autores)

---

## 📌 Descrição do Projeto

O **Sistema de Gestão de Incêndios (SGI)** é uma aplicação em linguagem **C** desenvolvida no âmbito da unidade curricular de Laboratório de Programação. O sistema destina-se a entidades como **Bombeiros**, permitindo gerir de forma integrada e eficiente:

- Ocorrências de emergência (incêndios florestais, urbanos, industriais)
- Intervenções e alocação de recursos
- Efetivos (bombeiros) e os seus estados operacionais
- Equipamentos e veículos disponíveis

O projeto foi desenvolvido seguindo boas práticas de engenharia de software, nomeadamente **modularização**, **memória dinâmica**, **persistência de dados em ficheiros**, **validação de entradas** e **registo de ações (logs)**.

---

## ✅ Funcionalidades

### Gestão de Ocorrências
- Criar, listar, atualizar e eliminar (soft delete) ocorrências
- Campos: ID sequencial, Localização, Data/Hora, Tipo, Prioridade, Dimensão, Estado
- Tipos suportados: `Florestal`, `Urbano`, `Industrial`
- Prioridades: `Baixa`, `Normal`, `Alta`
- Dimensões: `Pequena`, `Média`, `Grande`
- Estados: `Reportada`, `Em Intervenção`, `Concluída`

### Gestão de Intervenções
- Criar e gerir intervenções associadas a ocorrências
- Alocar e remover bombeiros e equipamentos
- Campos: ID sequencial, ID da Ocorrência, Data/Hora de Início e Fim, Estado
- Estados: `Em Planeamento`, `Em Execução`, `Concluída`

### Gestão de Bombeiros
- Manutenção do cadastro de efetivos operacionais
- Campos: ID sequencial, Nome, Especialidade, Estado
- Especialidades (enum): `Combate Florestal`, `Resgate`, `Incêndio`
- Estados: `Disponível`, `Em Intervenção`, `Em Treino`

### Gestão de Equipamentos
- Inventário completo de recursos materiais e veículos
- Campos: ID sequencial, Designação, Tipo, Estado, Localização
- Tipos (enum): `Veículo`, `Mangueira`, `Respirador`
- Estados: `Disponível`, `Em Uso`, `Em Manutenção`

### Eliminação Segura (Soft Delete)
- Entidades com relacionamentos ativos não são removidas fisicamente
- São marcadas como **Inativas** e excluídas das listagens regulares
- Podem ser **reativadas** a qualquer momento

---

## 🏗️ Arquitetura do Sistema

O sistema foi construído segundo os princípios de **Responsabilidade Única (SRP)** e **Modularização por Domínio**:

```
┌─────────────────────────────────────────────────────────────┐
│                          main.c                             │
│          (inicialização, ciclo principal, shutdown)         │
└────────────────────────────┬────────────────────────────────┘
                             │
          ┌──────────────────┼──────────────────┐
          ▼                  ▼                  ▼
     ┌─────────┐       ┌──────────┐       ┌──────────┐
     │  menu   │       │  utils   │       │   logs   │
     └────┬────┘       └──────────┘       └──────────┘
          │
    ┌─────┴──────┬─────────────┬──────────────┐
    ▼            ▼             ▼              ▼
┌──────────┐ ┌──────────┐ ┌──────────┐ ┌────────────┐
│ocorrencias│ │intervencoes│ │bombeiros│ │equipamentos│
└──────────┘ └──────────┘ └──────────┘ └────────────┘
         \         |          |         /
          └────────┴──────────┴────────┘
                         │
              ┌──────────┴──────────┐
              ▼                     ▼
       ┌────────────┐        ┌────────────┐
       │ persistencia│        │ relatorios │
       └────────────┘        └────────────┘
              │                     │
       [ficheiros .dat]       ┌────────────┐
                              │  sugestoes │
                              └────────────┘

  Todos os módulos partilham:
  ┌─────────────────────────────────────────┐
  │  modelos.h  (structs, enums, constantes) │
  └─────────────────────────────────────────┘
```

---

## 📁 Estrutura de Ficheiros

```
LP_8220942/
│
├── cmake-build-debug/          # Binários gerados pelo CMake (não versionar)
│
├── main.c                      # Ponto de entrada da aplicação
├── modelos.h                   # Definição central de structs, enums e constantes
│                               # (conteúdo distribuído pelos respetivos .h no futuro)
│
├── menu.c / menu.h             # Interface de utilizador e navegação
├── utils.c / utils.h           # Funções auxiliares (datas, validações, formatação)
├── logs.c / logs.h             # Sistema de registo de ações e erros
│
├── ocorrencias.c / ocorrencias.h     # CRUD e lógica de ocorrências
├── intervencoes.c / intervencoes.h   # CRUD e lógica de intervenções
├── bombeiros.c / bombeiros.h         # CRUD e lógica de bombeiros
├── equipamentos.c / equipamentos.h   # CRUD e lógica de equipamentos
│
├── relatorios.c / relatorios.h       # Todos os relatórios do sistema
├── sugestoes.c / sugestoes.h         # Funcionalidade adicional: Sugestão Automática
├── persistencia.c / persistencia.h   # Carregamento e gravação de dados em ficheiros
│
├── CMakeLists.txt              # Configuração de compilação CMake
│
├── data/                       # Criada em runtime
│   ├── ocorrencias.dat
│   ├── intervencoes.dat
│   ├── bombeiros.dat
│   └── equipamentos.dat
│
├── logs/                       # Criada em runtime
│   └── sistema.log
│
└── README.md                   # Este ficheiro
```

---

## 🗃️ Modelo de Dados

> Os tipos abaixo estão atualmente centralizados em `modelos.h` e serão progressivamente distribuídos pelos respetivos ficheiros `.h` de cada módulo.

### Enumerações

```c
/* ocorrencias.h */
typedef enum { FLORESTAL, URBANO, INDUSTRIAL }                    TipoOcorrencia;
typedef enum { BAIXA, NORMAL, ALTA }                              PrioridadeOcorrencia;
typedef enum { REPORTADA, EM_INTERVENCAO, CONCLUIDA }             EstadoOcorrencia;
typedef enum { PEQUENA, MEDIA, GRANDE }                           Dimensao;

/* intervencoes.h */
typedef enum { EM_PLANEAMENTO, EM_EXECUCAO, INT_CONCLUIDA }       EstadoIntervencao;

/* bombeiros.h */
typedef enum { COMBATE_FLORESTAL, RESGATE, INCENDIO }             EspecialidadeBombeiro;
typedef enum { DISPONIVEL, EM_INTERVENCAO_BOMBEIRO, EM_TREINO }   EstadoOperacionalBombeiro;

/* equipamentos.h */
typedef enum { VEICULO, MANGUEIRA, RESPIRADOR }                   TipoEquipamento;
typedef enum { EQ_DISPONIVEL, EQ_EM_USO, EQ_EM_MANUTENCAO }      EstadoEquipamento;
```

### Estruturas Principais

```c
typedef struct {
    int dia, mes, ano;
    int hora, minuto;
} DataHora;

typedef struct {
    int                  idOcorrencia;
    char                 localizacao[MAX_LOCAL];
    DataHora             dataHora;
    TipoOcorrencia       tipo;
    PrioridadeOcorrencia prioridade;
    EstadoOcorrencia     estado;
    Dimensao             dimensao;
    int                  ativo;   /* soft delete: 1 = ativo, 0 = inativo */
} Ocorrencia;

typedef struct {
    int               idIntervencao;
    int               idOcorrencia;
    DataHora          inicio;
    DataHora          fim;
    int               fimDefinido;        /* 1 se a hora de fim foi definida */
    EstadoIntervencao estado;
    int              *idsBombeiros;       /* array dinâmico */
    int               numBombeiros;
    int               capacidadeBombeiros;
    int              *idsEquipamentos;    /* array dinâmico */
    int               numEquipamentos;
    int               capacidadeEquipamentos;
    int               ativo;
} Intervencao;

typedef struct {
    int                      idBombeiro;
    char                     nome[MAX_NOME];
    EspecialidadeBombeiro    especialidade;
    EstadoOperacionalBombeiro estado;
    int                      ativo;
} Bombeiro;

typedef struct {
    int              idEquipamento;
    char             designacao[MAX_DESIGNACAO];
    TipoEquipamento  tipo;
    EstadoEquipamento estado;
    char             localizacao[MAX_LOCAL];
    int              ativo;
} Equipamento;
```

### Listas Dinâmicas

```c
typedef struct { Ocorrencia  *dados; int tamanho; int capacidade; } ListaOcorrencias;
typedef struct { Intervencao *dados; int tamanho; int capacidade; } ListaIntervencoes;
typedef struct { Bombeiro    *dados; int tamanho; int capacidade; } ListaBombeiros;
typedef struct { Equipamento *dados; int tamanho; int capacidade; } ListaEquipamentos;
```

### Contexto Global

```c
typedef struct {
    ListaOcorrencias   ocorrencias;
    ListaIntervencoes  intervencoes;
    ListaBombeiros     bombeiros;
    ListaEquipamentos  equipamentos;

    int proximoIdOcorrencia;
    int proximoIdIntervencao;
    int proximoIdBombeiro;
    int proximoIdEquipamento;
} SistemaGestaoIncendios;
```

### Constantes

```c
#define MAX_LOCAL         100   /* Tamanho máximo para nomes de localidades */
#define MAX_NOME          100   /* Tamanho máximo para nomes de bombeiros   */
#define MAX_DESIGNACAO    100   /* Tamanho máximo para designações           */
#define MAX_LINHA         500   /* Tamanho para leitura de linhas            */
#define CAPACIDADE_INICIAL 10   /* Capacidade inicial das listas dinâmicas  */
#define DATA_NAO_DEFINIDA -1    /* Valor especial para datas não definidas  */
```

---

## 💾 Persistência de Dados

Os dados são guardados em **ficheiros binários** (formato `.dat`), por ser o formato mais eficiente para leitura/escrita direta de structs em C.

| Momento         | Ação                                              |
|-----------------|---------------------------------------------------|
| **Arranque**    | Leitura dos ficheiros `.dat` para memória          |
| **Encerramento**| Escrita de todos os dados da memória para ficheiros|

**Justificação da escolha binária:**
- Leitura e escrita de structs em bloco com `fread`/`fwrite`
- Performance superior a formatos de texto para grandes volumes
- Menor complexidade de parsing comparativamente a CSV ou JSON

---

## 📝 Sistema de Logs

O sistema regista automaticamente ações críticas no ficheiro `logs/sistema.log` através da função:

```c
void registarLog(const char *nivel, const char *modulo,
                 const char *acao,  const char *detalhe);
```

**Formato de uma entrada no log:**
```
[2025-12-01 14:32:05] [INFO]    [INTERVENCOES] [CRIAR]           ID=5;Ocorrencia=2
[2025-12-01 14:35:11] [ERROR]   [INTERVENCOES] [ADD_BOMBEIRO]    Falha expandir array
[2025-12-01 14:40:00] [WARNING] [INTERVENCOES] [INATIVAR]        Tentativa bloqueada
[2025-12-01 14:41:00] [INFO]    [UTILS]        [FREE_INTERVENCOES] Memória libertada
```

**Níveis utilizados:** `INFO`, `WARNING`, `ERROR`

**Módulos que registam eventos:** `INTERVENCOES`, `BOMBEIROS`, `EQUIPAMENTOS`, `OCORRENCIAS`, `UTILS`, `PERSISTENCIA`

**Ações registadas por módulo:**

| Módulo | Ações registadas |
|--------|-----------------|
| `INTERVENCOES` | CRIAR, ADD_BOMBEIRO, ADD_EQUIPAMENTO, INICIAR_EXECUCAO, CONCLUIR, INATIVAR, REATIVAR |
| `UTILS` | INIT_\*, EXPANDIR_LISTA_\*, FREE_\* (alocações e libertações de memória) |
| Todos | Erros fatais de alocação de memória (`malloc`/`realloc` falhado) |

> **Nota:** Algumas ações de leitura (ex.: listar) têm log comentado intencionalmente para evitar ruído excessivo. Podem ser reativadas para fins de debugging.

**Formato de uma entrada no log:**
```
[2025-12-01 14:32:05] [INFO]    [INTERVENCOES] [CRIAR]             ID=5;Ocorrencia=2
[2025-12-01 14:35:11] [INFO]    [INTERVENCOES] [ADD_BOMBEIRO]      Intervencao=5;Bombeiro=3
[2025-12-01 14:36:00] [INFO]    [INTERVENCOES] [ADD_EQUIPAMENTO]   Intervencao=5;Equipamento=1
[2025-12-01 14:37:00] [INFO]    [INTERVENCOES] [INICIAR_EXECUCAO]  Intervencao 5 iniciada (bombeiros=2, equipamentos=1)
[2025-12-01 14:40:00] [INFO]    [INTERVENCOES] [CONCLUIR]          Intervenção 5 concluída
[2025-12-01 14:41:00] [WARNING] [INTERVENCOES] [INATIVAR]          Tentativa de inativar intervenção em execução bloqueada
[2025-12-01 14:42:00] [INFO]    [INTERVENCOES] [REATIVAR]          Intervenção 5 reativada (bombeiros=2, equipamentos=1)
[2025-12-01 14:50:00] [ERROR]   [UTILS]        [EXPANDIR_LISTA_BOMBEIROS] Falha ao expandir
[2025-12-01 14:55:00] [INFO]    [UTILS]        [FREE_INTERVENCOES] Memória libertada (incluindo arrays internos)
```

**Níveis utilizados:** `INFO`, `WARNING`, `ERROR`

---

## 📊 Relatórios Disponíveis

### Relatórios de Ocorrências
- `relatorioOcorrenciasPorEstado` — contagem por estado (Reportada / Em Intervenção / Concluída)
- `relatorioOcorrenciasPorTipo` — distribuição por tipo (Florestal / Urbano / Industrial)
- `relatorioOcorrenciasPorPrioridade` — contagem por prioridade (Baixa / Normal / Alta)
- `relatorioTempoRespostaOcorrencias` — diferença em minutos entre a ocorrência e o início da intervenção (média, mín., máx.)
- `relatorioOcorrenciasPorLocalizacao` — agrupamento e frequência por localidade
- `relatorioFrequenciaIncidentes` — frequência de incidentes ao longo do ano (por mês)

### Relatórios de Intervenções
- `relatorioIntervencoesPorEstado` — contagem por estado
- `relatorioDuracaoMediaIntervencoes` — duração média das intervenções concluídas (em minutos)
- `relatorioRecursosMaisUtilizados` — bombeiros e equipamentos com maior presença em intervenções
- `relatorioEficienciaIntervencoes` — duração média cruzada com o tipo de ocorrência

### Relatórios de Bombeiros
- `relatorioDisponibilidadeBombeiros` — disponibilidade atual por especialidade
- `relatorioRankingDesempenhoBombeiros` — ranking por número de intervenções concluídas

### Relatórios de Equipamentos
- `relatorioInventarioEquipamentos` — inventário completo com estado atual
- `relatorioEquipamentosEmManutencao` — listagem de equipamentos em manutenção
- `relatorioUtilizacaoEquipamentosPorTipoIntervencao` — utilização por tipo de ocorrência
- `relatorioRankingEquipamentos` — ranking dos equipamentos mais utilizados

### Relatórios Estratégicos Adicionais

**`relatorioCapacidadeOperacional`** *(Relatório EXTRA)*
> Avalia a prontidão operacional global: percentagem de bombeiros e equipamentos disponíveis vs. em operação, e carga atual do sistema.

**`relatorioCargaTrabalhoPeriodo`** *(Relatório EXTRA)*
> Análise mensal ao longo de um ano: ocorrências reportadas, intervenções iniciadas e concluídas por mês — útil para identificar sazonalidade e picos operacionais.

---

## 💡 Funcionalidade Adicional

### Sugestão Automática de Recursos (`sugestoes.c`)

O módulo de sugestões é um **mecanismo de apoio à decisão** baseado em dados históricos de intervenções concluídas. É invocado durante o planeamento de uma nova intervenção e disponibiliza dois cálculos distintos:

**`calcularSugestaoBombeiros`** — devolve a média de bombeiros utilizados em intervenções anteriores com o mesmo tipo de ocorrência e prioridade.

**`calcularSugestaoEquipamentos`** — devolve a média de veículos, mangueiras e respiradores utilizados nas mesmas condições.

**Regras de funcionamento:**
- Exige um **mínimo de 3 intervenções concluídas** com o mesmo tipo e prioridade para produzir uma sugestão (abaixo desse limiar devolve `-1` e o sistema informa o utilizador)
- As sugestões são **apenas consultivas** — não alteram o sistema automaticamente
- O utilizador pode aceitar a informação ou ignorá-la e selecionar recursos manualmente
- Os cálculos baseiam-se exclusivamente em intervenções `INT_CONCLUIDA` e ativas

**Fluxo durante o planeamento:**
```
Criar intervenção
    └─> Adicionar bombeiros
            └─> [Sugestão] "Recomendado: X bombeiros (baseado em Y intervenções semelhantes)"
                           (apenas se Y >= 3, caso contrário: "Dados insuficientes")
    └─> Adicionar equipamentos
            └─> [Sugestão] "Recomendado: V veículos, M mangueiras, R respiradores"
```

**Valor acrescentado:** Apoia decisões de planeamento em situações de pressão, evitando sub ou sobre-alocação de recursos com base em histórico real de operações semelhantes.

---

## 🎯 Decisões de Design

| Decisão | Justificação |
|---------|-------------|
| **Soft delete** (`ativo = 0/1`) | Preserva integridade referencial sem eliminar dados históricos |
| **Eliminação física condicional** (`tentarEliminarIntervencao`) | Intervenções inativas e já concluídas são removidas da memória — não há valor histórico em manter uma intervenção cancelada |
| **Máquina de estados explícita** nas intervenções | Impede transições inválidas (ex.: concluir sem executar, inativar em execução) |
| **Contexto global único** (`SistemaGestaoIncendios`) | Evita variáveis globais dispersas; facilita passagem de estado entre módulos |
| **Arrays dinâmicos** para listas de recursos | Flexibilidade sem limites artificiais; geridos com `malloc`/`realloc`/`free` |
| **Capacidade em duas camadas** nas intervenções | `capacidadeBombeiros` e `capacidadeEquipamentos` independentes — expande apenas o que precisa |
| **`modelos.h` centralizado** | Elimina dependências circulares entre módulos |
| **Ficheiros binários** para persistência | Maior eficiência de I/O; escrita direta de structs com `fread`/`fwrite` |
| **IDs sequenciais gerados internamente** | Unicidade garantida; sem dependência de input externo |
| **Módulo `utils.c`** para funções partilhadas | Conversões enum→string, validações de data, expansão de listas — evita duplicação |
| **Sugestões baseadas em mínimo de 3 amostras** | Garante que a sugestão tem relevância estatística mínima antes de ser apresentada |
| **Logs de leitura comentados** | Evita ruído no ficheiro de log em operações não destrutivas; facilmente reativáveis para debugging |


---

## 📖 Documentação Doxygen

A documentação técnica completa do projeto foi gerada com **Doxygen** e encontra-se na pasta `Documentação`.

Para visualizar no browser:



---

## 👥 Autores
<br>
- José Carneiro 
<br>

| Campo            | Informação                                     |
|------------------|------------------------------------------------|
| **Curso**        | Licenciatura em Engenharia Informática         |
| **UC**           | Laboratório de Programação                     |

---

> *Projeto desenvolvido para fins académicos no âmbito da avaliação em período letivo da UC de Laboratório de Programação.*

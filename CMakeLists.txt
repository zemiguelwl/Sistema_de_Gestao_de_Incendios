cmake_minimum_required(VERSION 4.0)
project(LP_8220942 C)

set(CMAKE_C_STANDARD 23)

# Flags de compilação
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -Wextra")

# Lista de todos os ficheiros fonte (.c)
set(SOURCE_FILES
        main.c
        menu.c
        ocorrencias.c
        intervencoes.c
        bombeiros.c
        equipamentos.c
        relatorios.c
        sugestoes.c
        persistencia.c
        logs.c
        utils.c
)

# Lista de todos os ficheiros header (.h)
set(HEADER_FILES
        modelos.h
        menu.h
        ocorrencias.h
        intervencoes.h
        bombeiros.h
        equipamentos.h
        relatorios.h
        sugestoes.h
        persistencia.h
        logs.h
        utils.h
)

# Criar o executável
add_executable(LP_8220942 ${SOURCE_FILES} ${HEADER_FILES})

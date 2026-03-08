# sistema-cadastro-clientes-c
Sistema de cadastro de clientes desenvolvido em C utilizando lista estática.
/*
---------------------------------------------------------
SISTEMA DE CADASTRO DE CLIENTES
Disciplina: Estrutura de Dados
Professor: Rodney
Instituição: UNICV

Descrição:
Sistema desenvolvido em linguagem C para gerenciamento
de clientes utilizando LISTA ESTÁTICA (vetor).

Funcionalidades implementadas:
- Inclusão de clientes
- Alteração de dados
- Consulta de clientes
- Exclusão de clientes
- Listagem de clientes
- Ordenação automática por código

Estrutura utilizada:
Lista estática com capacidade máxima definida por MAX.

Autor: Carla Rigolin
Data: 07/03/2026
---------------------------------------------------------
*/

#include <stdio.h>
#include <windows.h>
#include <stdlib.h>
#include <conio.h>
#include <string.h>
#define MAX 10 // Capacidade da lista

// -----------------------------
// Função que remove o ENTER de uma string
// -----------------------------
void remover_enter(char *texto)
{
    texto[strcspn(texto, "\n")] = '\0';
}

// -----------------------------
// Estrutura que representa um cliente
// -----------------------------
typedef struct
{
    int codigo;             // Identificador único do cliente
    char nome[50];          // Nome completo
    char endereco[50];      // Endereço
    char telefone[16];      // Telefone de contato
    char cpf[15];           // CPF do cliente
    char email[50];         // Email para contato
    char dt_nascimento[20]; // Data de nascimento
} reg_clientes;

// -----------------------------
// Estrutura da lista de clientes
// -----------------------------
typedef struct
{
    reg_clientes ficha[MAX]; // Vetor que armazena os clientes
    int inicio;              // Posição inicial da lista
    int fim;                 // Quantidade de clientes cadastrados
} Lista;
void ordenar_clientes(Lista *L);
void listar_clientes(Lista L);

// -----------------------------
// Função que posiciona o cursor na tela
// -----------------------------
void gotoxy(int x, int y)
{
    COORD coord;
    coord.X = (short)x;
    coord.Y = (short)y;
    SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), coord);
}

// -----------------------------
// Função que desenha a interface do sistema
// -----------------------------
void tela()
{
    int c, l;

    system("Color 0B");
    system("cls");

    for (l = 1; l < 25; l++)
    {
        gotoxy(1, l);
        printf("|");
        gotoxy(80, l);
        printf("|");
    }

    for (c = 1; c < 81; c++)
    {
        gotoxy(c, 1);
        printf("-");
        gotoxy(c, 4);
        printf("-");
        gotoxy(c, 22);
        printf("-");
        gotoxy(c, 24);
        printf("-");
    }

    gotoxy(1, 1);
    printf("+");
    gotoxy(80, 1);
    printf("+");
    gotoxy(1, 4);
    printf("+");
    gotoxy(80, 4);
    printf("+");
    gotoxy(1, 22);
    printf("+");
    gotoxy(80, 22);
    printf("+");
    gotoxy(1, 24);
    printf("+");
    gotoxy(80, 24);
    printf("+");

    gotoxy(2, 2);
    printf("UNICV");

    gotoxy(2, 23);
    printf("MSG:");

    gotoxy(61, 2);
    printf("ESTRUTURA DE DADOS");

    gotoxy(61, 3);
    printf("Prof: Rodney");
}

// -----------------------------
// Função que exibe mensagens ao usuário
// -----------------------------
void mensagem(char texto[])
{
    gotoxy(7, 23);
    printf("                                                            ");
    gotoxy(7, 23);
    printf("%s", texto);
}

// -----------------------------
// Função que exibe o formulário de clientes
// -----------------------------
void tela_clientes()
{
    gotoxy(10, 7);
    printf("Codigo do Cliente....:");

    gotoxy(10, 9);
    printf("1 - Nome.............:");

    gotoxy(10, 11);
    printf("2 - Endereco.........:");

    gotoxy(10, 13);
    printf("3 - Telefone.........:");

    gotoxy(10, 15);
    printf("4 - CPF..............:");

    gotoxy(10, 17);
    printf("5 - Email............:");

    gotoxy(10, 19);
    printf("6 - Data Nascimento..:");
}

// -----------------------------
// Função que busca um cliente pelo código
// -----------------------------
int pesquisa(Lista L, int cod)
{
    int i;

    // Percorre todos os clientes cadastrados
    for (i = 0; i < L.fim; i++)
    {
        if (L.ficha[i].codigo == cod) // verifica se encontrou o código
        {
            return i; // retorna posição encontrada
        }
    }

    return -1; // retorna -1 se não encontrar
}

// -----------------------------
// Função para cadastrar cliente
// -----------------------------
void cadastra_cliente(Lista *L)
{

    // Variaveis
    reg_clientes clie;
    int result;
    int resp;

    // Verifica se a lista já atingiu a capacidade máxima definida por MAX
    if (L->fim >= MAX)
    {
        mensagem("Lista cheia - Nao e possivel cadastrar mais clientes");
        getch();
        return; // Encerra a função caso a lista esteja cheia
    }
    // Loop que permite cadastrar vários clientes seguidos
    do
    {
        // Loop usado para garantir que o código digitado não esteja repetido
        do
        {
            tela();

            gotoxy(30, 3);
            printf("CADASTRA CLIENTE");

            tela_clientes(); // Mostra os campos do cadastro

            gotoxy(35, 7);
            scanf("%d", &clie.codigo);

            // Verifica se já existe cliente com esse código
            result = pesquisa(*L, clie.codigo);

            // Se encontrar o código, informa ao usuário que já existe
            if (result != -1)
            {
                gotoxy(7, 23);
                mensagem("Codigo ja existe...");
                getch();
            }

        } while (result != -1); // repete até digitar um código que não exista

        // Leitura dos dados do cliente
        gotoxy(35, 9);
        fflush(stdin);
        fgets(clie.nome, 50, stdin);
        remover_enter(clie.nome); // remove o ENTER do final da string

        gotoxy(35, 11);
        fflush(stdin);
        fgets(clie.endereco, 50, stdin);
        remover_enter(clie.endereco);

        gotoxy(35, 13);
        fflush(stdin);
        fgets(clie.telefone, 16, stdin);
        remover_enter(clie.telefone);

        gotoxy(35, 15);
        fflush(stdin);
        fgets(clie.cpf, 15, stdin);
        remover_enter(clie.cpf);

        gotoxy(35, 17);
        fflush(stdin);
        fgets(clie.email, 50, stdin);
        remover_enter(clie.email);

        gotoxy(35, 19);
        fflush(stdin);
        fgets(clie.dt_nascimento, 20, stdin);
        remover_enter(clie.dt_nascimento);

        // Pergunta ao usuário se deseja salvar os dados digitados
        gotoxy(7, 23);
        printf("Deseja salvar os dados (1-Sim 2-Nao): ");
        scanf("%d", &resp);

        // Se o usuário confirmar o cadastro
        if (resp == 1)
        {
            // Verifica novamente se ainda existe espaço na lista
            if (L->fim >= MAX)
            {
                gotoxy(23, 7);
                printf("NAO PODE INSERIR - LISTA CHEIA");
                getch();
            }
            else
            {
                // Copia os dados da variável temporária para o vetor da lista
                L->ficha[L->fim] = clie;
                L->fim++;            // Incrementa a quantidade de clientes cadastrados
                ordenar_clientes(L); // Ordena automaticamente os clientes por código
                mensagem("Cliente cadastrado com sucesso!");
                getch();
            }
        }

        // Pergunta se o usuário deseja cadastrar outro cliente
        gotoxy(7, 23);
        printf("Deseja continuar cadastro (1-Sim 2-Nao): ");
        scanf("%d", &resp);

    } while (resp == 1);
}

// -----------------------------
// Função que altera os dados de um cliente
// -----------------------------
void altera_cliente(Lista *L)
{
    int codigo;
    int posicao;
    int menu;

    tela();
    gotoxy(30, 3);
    printf("ALTERACAO CLIENTE");

    // Solicita o código do cliente que será alterado
    gotoxy(10, 7);
    printf("Digite o codigo do cliente: ");
    scanf("%d", &codigo);

    // Procura o cliente na lista através do código
    posicao = pesquisa(*L, codigo);

    // Verifica se o cliente não foi encontrado
    if (posicao == -1)
    {
        gotoxy(10, 10);
        printf("Cliente nao encontrado no sistema!!!");
        getch();
    }
    else
    {
        tela();
        gotoxy(30, 3);
        printf("ALTERACAO CLIENTE");

        tela_clientes();

        // Exibe os dados atuais do cliente encontrado
        gotoxy(35, 7);
        printf("%d", L->ficha[posicao].codigo);

        gotoxy(35, 9);
        printf("%s", L->ficha[posicao].nome);

        gotoxy(35, 11);
        printf("%s", L->ficha[posicao].endereco);

        gotoxy(35, 13);
        printf("%s", L->ficha[posicao].telefone);

        gotoxy(35, 15);
        printf("%s", L->ficha[posicao].cpf);

        gotoxy(35, 17);
        printf("%s", L->ficha[posicao].email);

        gotoxy(35, 19);
        printf("%s", L->ficha[posicao].dt_nascimento);

        // Loop que permite alterar vários dados do cliente
        // O loop só termina quando o usuário escolher a opção 0
        while (1)
        {
            gotoxy(10, 21);
            printf("Qual dado deseja alterar (1-6 / 0-Sair): ");
            scanf("%d", &menu);

            if (menu == 0)
                break;

            // Estrutura de decisão que define qual campo será alterado
            switch (menu)
            {
                // Altera os dados do cliente
            case 1:
                gotoxy(35, 9);
                fflush(stdin);
                fgets(L->ficha[posicao].nome, 50, stdin);
                remover_enter(L->ficha[posicao].nome);
                break;

            case 2:
                gotoxy(35, 11);
                fflush(stdin);
                fgets(L->ficha[posicao].endereco, 50, stdin);
                remover_enter(L->ficha[posicao].endereco);
                break;

            case 3:
                gotoxy(35, 13);
                fflush(stdin);
                fgets(L->ficha[posicao].telefone, 16, stdin);
                remover_enter(L->ficha[posicao].telefone);
                break;

            case 4:
                gotoxy(35, 15);
                fflush(stdin);
                fgets(L->ficha[posicao].cpf, 15, stdin);
                remover_enter(L->ficha[posicao].cpf);
                break;

            case 5:
                gotoxy(35, 17);
                fflush(stdin);
                fgets(L->ficha[posicao].email, 50, stdin);
                remover_enter(L->ficha[posicao].email);
                break;

            case 6:
                gotoxy(35, 19);
                fflush(stdin);
                fgets(L->ficha[posicao].dt_nascimento, 20, stdin);
                remover_enter(L->ficha[posicao].dt_nascimento);
                break;
                // Caso o usuário digite uma opção inválida
            default:
                gotoxy(10, 22);
                printf("Opcao invalida!");
            }
            // Mensagem informando que o campo foi alterado
            gotoxy(10, 23);
            printf("Dado alterado com sucesso!");
            getch();
        }
    }
}

// -----------------------------
// Função que consulta um cliente pelo código
// -----------------------------
void consulta_cliente(Lista L)
{
    int codigo;
    int posicao;

    tela();
    gotoxy(30, 3);
    printf("CONSULTA CLIENTES");

    gotoxy(10, 7);
    printf("Digite o codigo do cliente: ");
    scanf("%d", &codigo);

    // Busca o cliente na lista através do código
    posicao = pesquisa(L, codigo);

    // Verifica se o cliente não foi encontrado
    if (posicao == -1)
    {
        gotoxy(10, 10);
        printf("Cliente nao encontrado no sistema!");
        getch();
    }
    else
    {
        tela();

        gotoxy(10, 6);
        printf("DADOS DO CLIENTE");

        // Exibe todos os dados armazenados do cliente
        gotoxy(10, 8);
        printf("Codigo......: %d", L.ficha[posicao].codigo);

        gotoxy(10, 10);
        printf("Nome........: %s", L.ficha[posicao].nome);

        gotoxy(10, 12);
        printf("Endereco....: %s", L.ficha[posicao].endereco);

        gotoxy(10, 14);
        printf("Telefone....: %s", L.ficha[posicao].telefone);

        gotoxy(10, 16);
        printf("CPF.........: %s", L.ficha[posicao].cpf);

        gotoxy(10, 18);
        printf("Email.......: %s", L.ficha[posicao].email);

        gotoxy(10, 20);
        printf("Nascimento..: %s", L.ficha[posicao].dt_nascimento);

        getch();
    }
}

// -----------------------------
// Função que exclui um cliente da lista
// -----------------------------
void exclusao_cliente(Lista *L)
{

    // Variaveis
    int codigo;
    int posicao;
    int resposta;
    int i;

    tela();

    gotoxy(30, 3);
    printf("EXCLUSAO DE CLIENTES");

    // Solicita o código do cliente a ser excluído
    gotoxy(10, 7);
    printf("Digite o codigo do cliente: ");
    scanf("%d", &codigo);

    // Busca o cliente na lista
    posicao = pesquisa(*L, codigo);

    // Verifica se o cliente não existe
    if (posicao == -1)
    {
        gotoxy(10, 10);
        printf("Cliente nao encontrado no sistema!");
        getch();
    }
    else
    {
        tela();
        // Mostra os dados do cliente antes de excluir
        gotoxy(30, 3);
        printf("CONFIRMAR EXCLUSAO");

        gotoxy(10, 8);
        printf("Codigo......: %d", L->ficha[posicao].codigo);

        gotoxy(10, 10);
        printf("Nome........: %s", L->ficha[posicao].nome);

        gotoxy(10, 12);
        printf("CPF.........: %s", L->ficha[posicao].cpf);

        // Pede confirmação da exclusão
        gotoxy(10, 14);
        printf("Deseja excluir? (1-Sim 2-Nao): ");
        scanf("%d", &resposta);

        // Se o usuario confirmar a exclusão
        if (resposta == 1)
        {
            // Desloca todos os elementos da lista
            // Uma posição para trás para preencher o espaço removido
            for (i = posicao; i < L->fim - 1; i++)
            {
                L->ficha[i] = L->ficha[i + 1];
            }
            // Diminui a quantidade de clientes da lista
            L->fim--;

            mensagem("Cliente excluido com sucesso!!!");
        }
        else
        {
            mensagem("Exclusao cancelada!");
        }
    }
    // Pausa antes de retornar ao menu
    getch();
}

// -----------------------------
// Função que lista todos os clientes cadastrados
// -----------------------------
void listar_clientes(Lista L)
{
    // Variável usada para percorrer a lista de clientes
    int i;

    tela();

    gotoxy(30, 3);
    printf("LISTA DE CLIENTES");

    // Verifica se a lista está vazia
    if (L.fim == 0)
    {
        gotoxy(10, 8);
        printf("Nenhum cliente cadastrado!");
    }
    else
    {
        // Cabeçalho da tabela de clientes
        gotoxy(5, 6);
        printf("COD   NOME                 TELEFONE");

        gotoxy(5, 7);
        printf("---------------------------------------------");

        // Percorre toda a lista de clientes cadastrados
        for (i = 0; i < L.fim; i++)
        {
            // Exibe os dados do cliente atual
            gotoxy(5, 8 + i);
            printf("%-5d %-20s %-15s",
                   L.ficha[i].codigo,    // Código do cliente
                   L.ficha[i].nome,      // Nome do cliente
                   L.ficha[i].telefone); // Telefone do cliente
        }
    }

    getch();
}

// -----------------------------
// Função que ordena os clientes pelo código
// -----------------------------
void ordenar_clientes(Lista *L)
{
    int i, j;          // Variável de controle dos loops
    reg_clientes temp; // Variável temporária usada para realizar a troca

    // Percorre toda a lista até o penúltimo elemento
    for (i = 0; i < L->fim - 1; i++)
    {
        // Compara o elemento atual com todos os elementos seguintes
        for (j = i + 1; j < L->fim; j++)
        {
            // Verifica se o código atual é maior que o próximo
            // Se for, significa que está fora de ordem
            if (L->ficha[i].codigo > L->ficha[j].codigo)
            {
                // Guarda temporariamente o cliente da posição i
                temp = L->ficha[i];

                // Move o cliente da posição j para a posição i
                L->ficha[i] = L->ficha[j];

                // Coloca o cliente temporário na posição j
                L->ficha[j] = temp;
            }
        }
    }
}

// -----------------------------
// Função principal do sistema
// -----------------------------
int main()
{
    Lista L; // Cria a lista que armazenará todos os clientes

    // Inicializa a lista
    L.inicio = 0; // Posição inicial da lista
    L.fim = 0;    // Indica que ainda não há clientes cadastrados

    int opcao; // Variável que armazenará a opção escolhida pelo usuário

    // Define a cor da tela do sistema
    system("Color f8");
    system("cls"); // Limpa a tela do console

    // Loop principal do sistema
    // O menu continuará aparecendo até o usuário escolher sair
    do
    {
        tela(); // Desenha a interface padrão da aplicação

        // Mostra a quantidade de clientes cadastrados
        gotoxy(7, 3);
        printf("---------- SISTEMA DE CLIENTES ----------");

        // Opções disponíveis no menu
        gotoxy(50, 6);
        printf("Clientes Cadastrados: %d de %d", L.fim, MAX);

        gotoxy(30, 10);
        printf("1 - Incluir Cliente");

        gotoxy(30, 12);
        printf("2 - Alterar Cliente");

        gotoxy(30, 14);
        printf("3 - Consultar Cliente");

        gotoxy(30, 16);
        printf("4 - Excluir Cliente");

        gotoxy(30, 18);
        printf("5 - Listar Clientes");

        gotoxy(30, 20);
        printf("6 - Sair do Sistema");

        // Solicita que o usuário escolha uma opção
        gotoxy(7, 23);
        printf("Digite sua opcao: ");
        scanf("%d", &opcao);

        // Estrutura de decisão que chama a função correspondente
        switch (opcao)
        {
            // Opção para cadastrar um novo cliente
        case 1:
            cadastra_cliente(&L);
            break;

            // Opção para alterar dados de um cliente existente
        case 2:
            altera_cliente(&L);
            break;

            // Opção para consultar um cliente pelo codigo
        case 3:
            consulta_cliente(L);
            break;
            // Opção para excluir um cliente da lista
        case 4:
            exclusao_cliente(&L);
            break;
            // Opção para listar todos os clientes cadastrados
        case 5:
            listar_clientes(L);
            break;

            // Opção para encerrar o sistema
        case 6:
            break;
        }

    } while (opcao != 6); // O menu continua até o usuário escolher sair

    return 0; // Finaliza a execução do programa
}

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
Observações:
- Os dados são armazenados em arquivo binário (clientes.dat)
- O sistema utiliza ordenação simples (Bubble Sort adaptado)
- Interface desenvolvida com posicionamento de cursor (gotoxy)

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

// Função que remove o ENTER de uma string
void remover_enter(char *texto)
{
    texto[strcspn(texto, "\n")] = '\0';
}

// Função que formata CPF(000.000.000-00)
void formatar_cpf(char *cpf)
{
    char temp[15];
    sprintf(temp, "%.3s.%.3s.%.3s-%.2s",
            cpf, cpf + 3, cpf + 6, cpf + 9);

    strcpy(cpf, temp);
}

// Função que formata data de aniversario (dd/mm/aaaa)
void formatar_data(char *data)
{
    char temp[20];
    sprintf(temp, "%.2s/%.2s/%.4s",
            data, data + 2, data + 4);
    strcpy(data, temp);
}
// Estrutura que representa um cliente
typedef struct
{
    int codigo;             // Identificador único do cliente
    char nome[50];          // Nome completo
    char endereco[50];      // Endereço
    char telefone[16];      // Telefone de contato
    char cpf[20];           // CPF do cliente
    char email[50];         // Email para contato
    char dt_nascimento[20]; // Data de nascimento
} reg_clientes;

// Estrutura da lista de clientes
typedef struct
{
    reg_clientes ficha[MAX]; // Vetor que armazena os clientes
    int inicio;              // Posição inicial da lista
    int fim;                 // Quantidade de clientes cadastrados
} Lista;

// Função que posiciona o cursor na tela
void gotoxy(int x, int y)
{
    COORD coord;
    coord.X = (short)x;
    coord.Y = (short)y;
    SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), coord);
}

// Função que desenha a interface do sistema
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

// Função que exibe mensagens ao usuário
void mensagem(char texto[])
{
    gotoxy(7, 23);
    printf("                                                            ");
    gotoxy(7, 23);
    printf("%s", texto);
}

// Função que exibe o formulário de clientes
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

// Função que busca um cliente pelo código
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

// Função para cadastrar cliente
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
        fgets(clie.cpf, 20, stdin);
        remover_enter(clie.cpf);
        formatar_cpf(clie.cpf);

        gotoxy(35, 17);
        fflush(stdin);
        fgets(clie.email, 50, stdin);
        remover_enter(clie.email);

        gotoxy(35, 19);
        fflush(stdin);
        fgets(clie.dt_nascimento, 20, stdin);
        remover_enter(clie.dt_nascimento);
        formatar_data(clie.dt_nascimento);

        // Pergunta ao usuário se deseja salvar os dados digitados
        gotoxy(7, 23);
        mensagem("Deseja salvar os dados (1-Sim 2-Nao): ");
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
                L->fim++; // Incrementa a quantidade de clientes cadastrados
                ordenar_clientes_cod(L);
                gravar(L);
                mensagem("Cliente cadastrado com sucesso!");
                getch();
            }
        }

        // Pergunta se o usuário deseja cadastrar outro cliente
        gotoxy(7, 23);
        mensagem("Deseja continuar cadastro (1-Sim 2-Nao): ");
        scanf("%d", &resp);

    } while (resp == 1);
}

// Função que altera os dados de um cliente
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

                mensagem("Dado alterado com sucesso!");
                getch();
                gravar(L);
                break;

            case 2:
                gotoxy(35, 11);
                fflush(stdin);
                fgets(L->ficha[posicao].endereco, 50, stdin);
                remover_enter(L->ficha[posicao].endereco);

                mensagem("Dado alterado com sucesso!");
                getch();
                gravar(L);
                break;

            case 3:
                gotoxy(35, 13);
                fflush(stdin);
                fgets(L->ficha[posicao].telefone, 16, stdin);
                remover_enter(L->ficha[posicao].telefone);

                mensagem("Dado alterado com sucesso!");
                getch();
                gravar(L);
                break;

            case 4:
                gotoxy(35, 15);
                fflush(stdin);
                fgets(L->ficha[posicao].cpf, 20, stdin);
                remover_enter(L->ficha[posicao].cpf);
                formatar_cpf(L->ficha[posicao].cpf);

                mensagem("Dado alterado com sucesso!");
                getch();
                gravar(L);
                break;

            case 5:
                gotoxy(35, 17);
                fflush(stdin);
                fgets(L->ficha[posicao].email, 50, stdin);
                remover_enter(L->ficha[posicao].email);

                mensagem("Dado alterado com sucesso!");
                getch();
                gravar(L);
                break;

            case 6:
                gotoxy(35, 19);
                fflush(stdin);
                fgets(L->ficha[posicao].dt_nascimento, 20, stdin);
                remover_enter(L->ficha[posicao].dt_nascimento);
                formatar_data(L->ficha[posicao].dt_nascimento);

                mensagem("Dado alterado com sucesso!");
                getch();
                gravar(L);
                break;
                // Caso o usuário digite uma opção inválida
            default:

                mensagem("Opcao invalida!");
            }
        }
    }
}

// Função que consulta um cliente pelo código
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

// Função que exclui um cliente da lista
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
            gravar(L);
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

// Função que ordena os clientes pelo código
void ordenar_clientes_cod(Lista *L)

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

// Função que ordena os clientes pelo nome (ordem alfabética)
void ordenar_cliente_nome(Lista *L)
{

    int i, j;          // Variável que controla o loop
    reg_clientes temp; // Variável temporária usada para realizar a troca

    // Percorre toda a lista até o penúltimo elemento
    for (i = 0; i < L->fim - 1; i++)
    {
        // Compara o elemento atual com todos os elementos seguintes
        for (j = i + 1; j < L->fim; j++)
        {
            // strcmp > 0 significa que o nome atual vem depois do próximo
            // ou seja, está fora da ordem alfabética
            if (strcmp(L->ficha[i].nome, L->ficha[j].nome) > 0)
            {
                // Guarda temporariamente o cliente da posição i
                temp = L->ficha[i];

                // Move o cliente da posição j para posição i
                L->ficha[i] = L->ficha[j];

                L->ficha[j] = temp;
            }
        }
    }
}

// Função que lista todos os clientes cadastrados por codigo
void listar_clientes_cod(Lista *L)
{
    // Variável usada para percorrer a lista de clientes
    int i;
    ordenar_clientes_cod(L);
    tela();

    gotoxy(30, 3);
    printf("LISTA DE CLIENTES POR CODIGO");

    // Verifica se a lista está vazia
    if (L->fim == 0)
    {

        mensagem("Nenhum cliente cadastrado!");
        getch();
        return;
    }
    else
    {
        // Cabeçalho da tabela de clientes
        gotoxy(5, 6);
        printf("COD   NOME                 CPF             TELEFONE     DATA NASCIMENTO");

        gotoxy(5, 7);
        printf("------------------------------------------------------------------------");
    }

    // Percorre toda a lista de clientes cadastrados
    for (i = 0; i < L->fim; i++)
    {
        // Exibe os dados do cliente atual
        gotoxy(5, 8 + i);
        printf("%-5d %-20s %-15s %-12s %-15s",
               L->ficha[i].codigo,
               L->ficha[i].nome,
               L->ficha[i].cpf,
               L->ficha[i].telefone,
               L->ficha[i].dt_nascimento);
    }
    getch();
}

// Função que lista todos os clientes cadastrados por nome
void listar_clientes_nome(Lista *L)
{
    // Variável usada para percorrer a lista de clientes
    int i;

    ordenar_cliente_nome(L);
    tela();

    gotoxy(30, 3);
    printf("LISTA DE CLIENTES POR NOME");

    // Verifica se a lista está vazia
    if (L->fim == 0)
    {

        mensagem("Nenhum cliente cadastrado!");
        getch();
        return;
    }
    else
    {
        // Cabeçalho da tabela de clientes
        gotoxy(5, 6);
        printf("NOME                 CPF             TELEFONE     DATA NASCIMENTO  COD     ");

        gotoxy(5, 7);
        printf("-----------------------------------------------------------------------");
    }

    // Percorre toda a lista de clientes cadastrados
    for (i = 0; i < L->fim; i++)
    {
        // Exibe os dados do cliente atual
        gotoxy(5, 8 + i);
        printf("%-20s %-15s %-12s %-15s %-5d",
               L->ficha[i].nome,
               L->ficha[i].cpf,
               L->ficha[i].telefone,
               L->ficha[i].dt_nascimento,
               L->ficha[i].codigo);
    }
    getch();
}

// Função que exibe o menu de consultas
// Permite buscar cliente ou listar ordenações
void menu_consulta(Lista *L)
{
    int opcao;

    do
    {
        tela();

        gotoxy(20, 03);
        printf("MENU DE CONSULTA");

        gotoxy(20, 10);
        printf("1 - Cliente por codigo especifico");

        gotoxy(20, 12);
        printf("2 - Lista de clientes em Ordem Alfabetica");

        gotoxy(20, 14);
        printf("3 - Lista de clientes em Ordem de Codigo");

        gotoxy(20, 16);
        printf("4 - Retornar ao menu Principal");

        gotoxy(07, 23);
        printf("Digite uma opcao: ");
        scanf("%d", &opcao);

        switch (opcao)
        {
        case 1:
            consulta_cliente(*L);
            break;

        case 2:
            listar_clientes_nome(L);
            break;

        case 3:
            listar_clientes_cod(L);
            break;

        case 4:
            break;

        default:
            mensagem("Opcao invalida!");
            getch();
        }

    } while (opcao != 4);
}

// Função que lê os dados do arquivo e carrega na lista
void le_arquivo(Lista *L)
{
    int i = 0;
    FILE *ptr;
    char *filename = "clientes.dat";
    char *modo_gravacao = "rb";

    reg_clientes clie;

    tela();
    gotoxy(19, 03);
    printf("CARREGA DADOS DO ARQUIVO PARA A LISTA");
    // Tenta abrir o arquivo para leitura binária
    ptr = fopen(filename, modo_gravacao);

    // Verifica se o arquivo existe
    if (ptr == NULL)
    {
        mensagem("Arquivo nao encontrado!");
        getch();
        return;
    }
    // Atualiza o total de clientes carregados
    while (fread(&clie, sizeof(reg_clientes), 1, ptr) == 1 && i < MAX)
    {
        L->ficha[i] = clie;
        i++;
    }
    // Atualiza o total de clientes carregados
    L->fim = i;
    fclose(ptr);
    mensagem("Dados carregados com sucesso!");
    getch();
}

// Função que grava os dados da lista no arquivo
void gravar(Lista *L)
{
    int i;
    FILE *ptr;
    char *filename = "clientes.dat";
    char *modo_gravacao = "wb";
    reg_clientes clie;

    tela();

    mensagem("GRAVA DADOS DA LISTA NO ARQUIVO EM DISCO");

    // Verifica se há dados na lista
    if (L->fim == 0)
    {
        mensagem("LISTA VAZIA..");
        getch();
    }
    // Verifica erro ao abrir arquivo
    else if ((ptr = fopen(filename, modo_gravacao)) == NULL)
    {
        mensagem("ERRO AO ABRIR ARQUIVO!");
        getch();
    }
    else
    {
        // Percorre a lista e grava cada cliente no arquivo
        for (i = 0; i < L->fim; i++)
        {
            clie = L->ficha[i];
            fwrite(&clie, sizeof(clie), 1, ptr);
        }

        fclose(ptr);

        mensagem("DADOS GRAVADOS COM SUCESSO!");
        getch();
    }
}

// Função principal do sistema
// Responsável pelo controle do menu e fluxo geral
int main()
{
    Lista L; // Cria a lista que armazenará todos os clientes

    // Inicializa a lista
    L.inicio = 0; // Posição inicial da lista
    L.fim = 0;    // Indica que ainda não há clientes cadastrados
    le_arquivo(&L);

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
        printf("3 - Menu de Consultas");

        gotoxy(30, 16);
        printf("4 - Excluir Cliente");

        gotoxy(30, 18);
        printf("5 - Sair do Sistema");

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
            menu_consulta(&L);
            break;
            // Opção para excluir um cliente da lista
        case 4:
            exclusao_cliente(&L);
            break;

            // Opção para encerrar o sistema
        case 5:
            break;
        }

    } while (opcao != 5); // O menu continua até o usuário escolher sair

    return 0; // Finaliza a execução do programa
}

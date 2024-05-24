Havia uma vez um jovem arqueiro chamado Aiden, cuja aldeia foi atacada por uma horda de criaturas sombrias que emergiram das profundezas de uma misteriosa masmorra. Seus pais, bravos guerreiros, sacrificaram suas vidas para proteger sua comunidade, mas Aiden foi poupado graças à rápida intervenção de um misterioso guardião da floresta.

Determinado a vingar sua família e libertar sua aldeia do terror das criaturas das masmorras, Aiden se lança em uma jornada solitária rumo às entranhas da terra. Munido apenas de seu arco, flechas afiadas e uma determinação inabalável, ele se aventura corajosamente nas profundezas da masmorra.

como jogar:

w- mover p cima 
a- mover p esquerda
s- mover p baixo
d- mover p direita

DEVS:
Tarcisio Ramses
Jordan Brenor
Laisa Gomes 




#include <stdio.h>
#include <stdlib.h>
#include <windows.h>
#include <locale.h>
#include <conio.h>
#include <string.h>
#include <time.h>

void linhacol(int lin, int col) {
    COORD coord;
    coord.X = col;
    coord.Y = lin;
    SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), coord);

    CONSOLE_CURSOR_INFO info;
    info.bVisible = FALSE;
    info.dwSize = 100;
    SetConsoleCursorInfo(GetStdHandle(STD_OUTPUT_HANDLE), &info);
}

typedef enum {
    BLACK,
    BLUE,
    GREEN,
    CYAN,
    RED,
    MAGENTA,
    BROWN,
    LIGHTGRAY,
    DARKGRAY,
    LIGHTBLUE,
    LIGHTGREEN,
    LIGHTCYAN,
    LIGHTRED,
    LIGHTMAGENTA,
    YELLOW,
    WHITE
} CorTexto;

typedef enum {
    _BLACK = 0,
    _BLUE = 16,
    _GREEN = 32,
    _CYAN = 48,
    _RED = 64,
    _MAGENTA = 80,
    _BROWN = 96,
    _LIGHTGRAY = 112,
    _DARKGRAY = 128,
    _LIGHTBLUE = 144,
    _LIGHTGREEN = 160,
    _LIGHTCYAN = 176,
    _LIGHTRED = 192,
    _LIGHTMAGENTA = 208,
    _YELLOW = 224,
    _WHITE = 240
} CorFundo;

void textcolor(int letras, int fundo) {
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), letras + fundo);
}

void box(int lin1, int col1, int lin2, int col2) {
    int i, j, tamlin, tamcol;

    tamlin = lin2 - lin1;
    tamcol = col2 - col1;

    for (i = col1; i <= col2; i++) {
        linhacol(lin1, i);
        printf("%c", 196);
        linhacol(lin2, i);
        printf("%c", 196);
    }

    for (i = lin1; i <= lin2; i++) {
        linhacol(i, col1);
        printf("%c", 179);
        linhacol(i, col2);
        printf("%c", 179);
    }

    for (i = lin1 + 1; i <= lin2; i++) {
        for (j = col1 + 1; j < col2; j++) {
            if (i == lin1 + 1 || i == lin2 - 1 || j == col1 + 1 || j == col2 - 1) {
                linhacol(i, j);
                printf(" ");
            }
        }
    }

    linhacol(lin1, col1);
    printf("%c", 218);
    linhacol(lin1, col2);
    printf("%c", 191);
    linhacol(lin2, col1);
    printf("%c", 192);
    linhacol(lin2, col2);
    printf("%c", 217);
}

int menu(int lin1, int col1, int qtd, char lista[][40], char tutoriais[][200]) {
    int opc = 1, lin2, col2, linha, i, tamMaxitem, tecla;

    tamMaxitem = strlen(lista[0]);

    for (i = 1; i < qtd; i++) {
        if (strlen(lista[i]) > tamMaxitem) {
            tamMaxitem = strlen(lista[i]);
        }
    }

    lin2 = lin1 + (qtd * 2 + 2);
    col2 = col1 + tamMaxitem + 4;

    textcolor(WHITE, _BLUE);
    setlocale(LC_ALL, "C");
    box(lin1, col1, lin2, col2);
    setlocale(LC_ALL, " ");

    while (1) {
        linha = lin1 + 2;
        for (i = 0; i < qtd; i++) {
            if (i + 1 == opc)
                textcolor(BLACK, _LIGHTGREEN);
            else
                textcolor(WHITE, _BLUE);
            linhacol(linha + 1, col1 + (tamMaxitem - strlen(lista[i])) / 2 + 2);
            printf("%s", lista[i]);
            linha += 2;
        }

        linhacol(1, 1);
        tecla = getch();

        if (tecla == 27) {
            opc = 0;
            break;
        } else if (tecla == 13) {
            if (opc == 2) {
                textcolor(WHITE, _BLACK);
                system("cls");
                printf("%s\n", tutoriais[opc - 1]);
                getch();
                system("cls");
            } else if (opc == 3) {
                opc = 0;
                linhacol(1, 1);
                textcolor(WHITE, _BLACK);
                printf("VA EMBORA MESMO, VOCE E FRACO DEMAIS PARA ESSE JOGO !\n");
                break;
            } else if (opc == 1) {
                system("cls");
                return 1; // Começa o jogo
            }
        } else if (tecla == 72) {
            if (opc > 1) opc--;
        } else if (tecla == 80) {
            if (opc < qtd) opc++;
        }
    }

    return opc;
}

void drawMap(char map[15][15], int *playerX, int *playerY, int monsterX, int monsterY, int keyX, int keyY, int hasKey, int doorX, int doorY, int doorOpen, int lives) {
    int i, j;
    for (i = 0; i < 15; i++) {
        for (j = 0; j < 15; j++) {
            if (i == *playerY && j == *playerX) {
                printf("&");
            } else if (i == monsterY && j == monsterX) {
                printf("v");
            } else if (i == keyY && j == keyX && !hasKey) {
                printf("@");
            } else if (i == doorY && j == doorX) {
                if (doorOpen) {
                    printf("=");
                } else {
                    printf("D");
                }
            } else {
                printf("%c", map[i][j]);
            }
        }
        printf("\n");
    }
    printf("\nVidas restantes: %d\n", lives);
}

// Defina as paredes com o símbolo '$' que o jogador não pode ultrapassar
// Implemente a lógica para interagir com o botão 'o'
// A função drawMap2 ficaria assim:

void drawMap2(char map2[30][30], int *playerX, int *playerY, int monsterX, int monsterY, int keyX, int keyY, int hasKey, int doorX, int doorY, int doorOpen, int lives) {
    int i, j; // Declaração das variáveis i e j fora do bloco específico

    for (i = 0; i < 30; i++) {
        for (j = 0; j < 30; j++) {
            if (i == *playerY && j == *playerX) {
                printf("&");
            } else if (i == monsterY && j == monsterX) {
                printf("x");
            } else if (i == keyY && j == keyX && !hasKey) {
                printf("@");
            } else if (i == doorY && j == doorX) {
                if (doorOpen) {
                    printf("=");
                } else {
                    printf("D");
                }
            } else if (map2[i][j] == '$') { // Parede que o jogador não pode ultrapassar
                printf("$");
            } else {
                printf("%c", map2[i][j]);
            }
        }
        printf("\n");
    }
    printf("\nVidas restantes: %d\n", lives);

    // Verificar se o jogador está em cima do botão 'o' e interagir com ele
    if (map2[*playerY][*playerX] == 'o') {
        map2[*playerY][*playerX] = ' '; // Remover o botão do mapa
        // Remover as paredes de '$' do mapa
        for (i = 0; i < 30; i++) {
            for (j = 0; j < 30; j++) {
                if (map2[i][j] == '$') {
                    map2[i][j] = ' ';
                }
            }
        }
    }
}

void drawMap3(char map3[45][45], int *playerX, int *playerY, int monsterX, int monsterY, int keyX, int keyY, int hasKey, int doorX, int doorY, int doorOpen, int lives) {
    int i, j; // Declaração das variáveis i e j fora do bloco específico

    for (i = 0; i < 30; i++) {
        for (j = 0; j < 30; j++) {
            if (i == *playerY && j == *playerX) {
                printf("&");
            } else if (i == monsterY && j == monsterX) {
                printf("vx");
            } else if (i == keyY && j == keyX && !hasKey) {
                printf("@");
            } else if (i == doorY && j == doorX) {
                if (doorOpen) {
                    printf("=");
                } else {
                    printf("D");
                }
            } else if (map3[i][j] == '$') { // Parede que o jogador não pode ultrapassar
                printf("$");
            } else {
                printf("%c", map3[i][j]);
            }
        }
        printf("\n");
    }
    printf("\nVidas restantes: %d\n", lives);

    // Verificar se o jogador está em cima do botão 'o' e interagir com ele
    if (map3[*playerY][*playerX] == 'o') {
        map3[*playerY][*playerX] = ' '; // Remover o botão do mapa
        // Remover as paredes de '$' do mapa
        for (i = 0; i < 30; i++) {
            for (j = 0; j < 30; j++) {
                if (map3[i][j] == '$') {
                    map3[i][j] = ' ';
                }
            }
        }
    }
}




int main(int argc, char** argv) {
    int opc;
    char lista[3][40] = {"Jogar", "Tutorial", "Sair"};
    char tutoriais[2][200] = {
        "Tutorial de como jogar:\n\n1. Use as setas direcionais para mover o jogador.\n2. Pressione a barra de espaço para pular.",
        "Tutorial de como jogar:\n\n1. Use as teclas 'W', 'A', 'S' e 'D' para mover o jogador.\n2. use a tecla 'i' para interagir com a chave."
    };
    setlocale(LC_ALL, " ");
    srand(time(NULL));

    while (1) {
        opc = menu(10, 10, 3, lista, tutoriais);

        if (opc == 0) {
            break;
        } else if (opc == 1) {
            char map[15][15] = {
                "***************",
                "*            @*",
                "*##### #######*",
                "*             *",
                "* #### #######*",
                "*   ># #      *",
                "*##### # #### *",
                "*             *",
                "*             *",
                "*             *",
                "*      ###### *",
                "*      #@     *",
                "* #### #######*",
                "*@   #        *",
                "***************"
            };
            int playerX = 1;
            int playerY = 1;
            int origPlayerX = playerX; // Posição original do jogador
            int origPlayerY = playerY; // Posição original do jogador
            int monsterX = 10; // Posição inicial do monstro
            int monsterY = 7;  // Posição inicial do monstro
            int keyX = 8; // Posição da chave
            int keyY = 6; // Posição da chave
            int doorX = 13; // Posição da porta
            int doorY = 6; // Posição da porta
            int hasKey = 0; // O jogador não pegou a chave inicialmente
            int doorOpen = 0; // A porta está inicialmente fechada
            int lives = 3;
            int gameRunning = 1;

            while (gameRunning) {
                system("cls");
                // Movimento aleatório do monstro
                int direction = rand() % 4; // 0: cima, 1: baixo, 2: esquerda, 3: direita
                switch (direction) {
                    case 0:
                        if (map[monsterY - 1][monsterX] != '*') {
                            monsterY--;
                        }
                        break;
                    case 1:
                        if (map[monsterY + 1][monsterX] != '*') {
                            monsterY++;
                        }
                        break;
                    case 2:
                        if (map[monsterY][monsterX - 1] != '*') {
                            monsterX--;
                        }
                        break;
                    case 3:
                        if (map[monsterY][monsterX + 1] != '*') {
                            monsterX++;
                        }
                        break;
                }
                drawMap(map, &playerX, &playerY, monsterX, monsterY, keyX, keyY, hasKey, doorX, doorY, doorOpen, lives);
                char move = getch();
                switch (move) {
                    case 'w':
                        if (map[playerY - 1][playerX] != '*') {
                            playerY--;
                        }
                        break;
                    case 's':
                        if (map[playerY + 1][playerX] != '*') {
                            playerY++;
                        }
                        break;
                    case 'a':
                        if (map[playerY][playerX - 1] != '*') {
                            playerX--;
                        }
                        break;
                    case 'd':
                        if (map[playerY][playerX + 1] != '*') {
                            playerX++;
                        }
                        break;
                    case 'i': // Interagir com a chave
                        if (playerX == keyX && playerY == keyY && !hasKey) {
                            hasKey = 1; // O jogador pegou a chave
                            doorOpen = 1; // Abrir a porta
                        }
                        break;
                }
                // Verificar se o jogador encostou no monstro
                if (playerX == monsterX && playerY == monsterY) {
                    lives--; // Reduzir uma vida
                    if (lives <= 0) {
                        system("cls");
                        printf("Voce foi morto pelo monstro! Pressione qualquer tecla para voltar ao menu principal.\n");
                        getch();
                        gameRunning = 0;
                    } else {
                        // Restaurar a posição original do jogador
                        playerX = origPlayerX;
                        playerY = origPlayerY;
                    }
                }
                // Verificar se o jogador encostou em um obstáculo (espinhos)
                if (map[playerY][playerX] == '#') {
                    lives--; // Reduzir uma vida
                    if (lives <= 0) {
                        system("cls");
                        printf("Voce foi morto ao encostar no obstaculo! Pressione qualquer tecla para voltar ao menu principal.\n");
                        getch();
                        gameRunning = 0;
                    } else {
                        // Restaurar a posição original do jogador
                        playerX = origPlayerX;
                        playerY = origPlayerY;
                    }
                }
                // Verificar se o jogador entrou no teletransporte
                if (map[playerY][playerX] == '>') {
                    // Definir nova posição do jogador
                    playerX = 13;
                    playerY = 13;
                }
                // Verificar se o jogador está em cima da porta aberta e mudar para a segunda fase
                if (playerX == doorX && playerY == doorY && doorOpen) {
                    system("cls");
                    printf("Voce completou a primeira fase! Pressione qualquer tecla para continuar.\n");
                    getch();
                    
                    // Resetar o mapa e as posições para a próxima fase
                    char map2[30][30] = {
                        "******************************",
                        "*               $           @*",//1
                        "*###########    #############*",//2
                        "* >        #    #            *",//3
                        "*          #    #            *",//4
                        "*######## ##    ###########$$*",//5
                        "*          #    #            *",//6
                        "*          #    #            *",//7
                        "*          #    #            *",//8
                        "*          #    ##$##########*",//9
                        "*         #      #           *",//10
                        "*         #     #            *",//11
                        "* ########### ##             *",//12
                        "*                            *",//13
                        "*                            *",//14
                        "*########                    *",//15
                        "*       #                    *",//16
                        "*       #                    *",//17
                        "*$$######                    *",//18
                        "*                            *",//19
                        "*                            *",//20
                        "*#####                       *",//21
                        "*   @#                       *",//22
                        "*    #                       *",//23
                        "*$$###             ######### *",//24
                        "*                  #         *",//25
                        "*                  #o        *",//26
                        "*$$############### ##########*",//27
                        "*              @##           *",//28
                        "******************************"
                    };
                    int playerX2 = 1; // Nova posição inicial do jogador na segunda fase
                    int playerY2 = 1; // Nova posição inicial do jogador na segunda fase
                    int monsterX2 = 27; // Posição inicial do monstro no segundo mapa
                    int monsterY2 = 23; // Posição inicial do monstro no segundo mapa
                    int keyX2 = 2; // Posição da chave no segundo mapa
                    int keyY2 = 16; // Posição da chave no segundo mapa
                    int doorX2 = 28; // Posição da porta no segundo mapa
                    int doorY2 = 4; // Posição da porta no segundo mapa
                    int hasKey2 = 0; // O jogador não pegou a chave inicialmente no segundo mapa
                    int doorOpen2 = 0; // A porta está inicialmente fechada no segundo mapa
                    int lives2 = lives; // Vidas do jogador na segunda fase
                    int gameRunning2 = 1;
                    int lastMoveX = 0;
                    int lastMoveY = 0;
                    

                    while (gameRunning2) {
                        // Lógica do segundo mapa
                        system("cls");
                        // Calcula as diferenças entre as coordenadas do jogador e as coordenadas do monstro
                        // Calcula as diferenças entre as coordenadas do jogador e as coordenadas do monstro



// Movimentação do jogador


// Movimentação do monstro na mesma direção que o jogador



                        // Desenhar o mapa com os personagens
                        drawMap2(map2, &playerX2, &playerY2, monsterX2, monsterY2, keyX2, keyY2, hasKey2, doorX2, doorY2, doorOpen2, lives2);

    // Obter movimento do jogador
    char move2 = getch();
switch(move2) {
    case 'w':
        if (map2[playerY2 - 1][playerX2] != '*' && map2[playerY2 - 1][playerX2] != '$') {
            playerY2--;
            lastMoveX = 0;
            lastMoveY = -1;
        }
        break;
    case 's':
        if (map2[playerY2 + 1][playerX2] != '*' && map2[playerY2 + 1][playerX2] != '$') {
            playerY2++;
            lastMoveX = 0;
            lastMoveY = 1;
        }
        break;
    case 'a':
        if (map2[playerY2][playerX2 - 1] != '*' && map2[playerY2][playerX2 - 1] != '$') {
            playerX2--;
            lastMoveX = -1;
            lastMoveY = 0;
        }
        break;
    case 'd':
        if (map2[playerY2][playerX2 + 1] != '*' && map2[playerY2][playerX2 + 1] != '$') {
            playerX2++;
            lastMoveX = 1;
            lastMoveY = 0;
        }
        break;
    case 'i': // Interagir com a chave
        if (playerX2 == keyX2 && playerY2 == keyY2 && !hasKey2) {
            hasKey2 = 1; // O jogador pegou a chave
            doorOpen2 = 1; // Abrir a porta
        }
        break;
    case 27: // Tecla Esc
        gameRunning2 = 0;
        break;
}

    

    // Movimentação do monstro na mesma direção que o jogador
   // Movimentação do monstro na direção oposta ao jogador
if (lastMoveX != 0 || lastMoveY != 0) {
    int newMonsterX2 = monsterX2 - lastMoveX; // Inverte a direção na coordenada X
    int newMonsterY2 = monsterY2 - lastMoveY; // Inverte a direção na coordenada Y

    // Verifique se a nova posição do monstro não é um obstáculo
    if (map2[newMonsterY2][newMonsterX2] != '*') {
        monsterX2 = newMonsterX2;
        monsterY2 = newMonsterY2;
    }
}


    // Atualização da tela e outras operações de jogo...

                        // Verificar se o jogador encostou em um obstáculo (espinhos) no segundo mapa
                        if (map2[playerY2][playerX2] == '#') {
                            lives2--; // Reduzir uma vida
                            if (lives2 <= 0) {
                                system("cls");
                                printf("Voce foi morto ao encostar no obstaculo! Pressione qualquer tecla para voltar ao menu principal.\n");
                                getch();
                                gameRunning2 = 0;
								gameRunning = 0; // Terminar o jogo
                            } else {
                                // Restaurar a posição original do jogador
                                playerX2 = 1;
                                playerY2 = 1;
                            }
                            
                        }
                        // Verificar se o jogador encostou no monstro no segundo mapa
                        if (playerX2 == monsterX2 && playerY2 == monsterY2) {
                            lives2--; // Reduzir uma vida
                            if (lives2 <= 0) {
                                system("cls");
                                printf("Voce foi morto pelo monstro! Pressione qualquer tecla para voltar ao menu principal.\n");
                                getch();
                                gameRunning2 = 0;
								gameRunning = 0; // Terminar o jogo
                            } else {
                                // Restaurar a posição original do jogador
                                playerX2 = 1;
                                playerY2 = 1;
                            }
                            
                        }
                        
                        // Verificar se o jogador está em cima da porta aberta e terminar o jogo
                        
                        // Verificar se o jogador entrou no teletransporte
if (map2[playerY2][playerX2] == '>') {
    // Definir nova posição do jogador
    playerX2 = 28;
    playerY2 = 28;
}

                    }
 // Após o término do loop do segundo mapa
if (playerX2 == doorX2 && playerY2 == doorY2 && doorOpen2) {
    system("cls");
    printf("Parabens! Você completou o segundo mapa. Pressione qualquer tecla para avançar para a fase 3.\n");
    getch();
    gameRunning2 = 0;}
       	
       
    
    

    char map3[45][45] = {
    "*********************************************",
    "*                        $               @  *", //1
    "*###########    #############    ###########*", //2
    "*          #    #            #              *", //3
    "*          #    #            #              *", //4
    "*########$$#    ###########$$#    ########$#*", //5
    "*          #    #            #              *", //6
    "*          #    #            #              *", //7
    "*          #    #            #              *", //8
    "*                                           *", //9
    "*                                           *", //10
    "*                                           *", //11
    "*                                           *", //12
    "*                                           *", //13
    "*                                           *", //14
    "*                                           *", //15
    "*                                           *", //16
    "*                                           *", //17
    "*                                           *", //18
    "*                                           *", //19
    "*                                           *", //20
    "*                                           *", //21
    "*                                           *", //22
    "*                                           *", //23
    "*                                           *", //24
    "*                                           *", //25
    "*                                           *", //26
    "*                                ###########*", //27
    "*                              #            *", //28
    "*********************************************"
};



int playerX3 = 1; // Nova posição inicial do jogador na segunda fase
int playerY3 = 1; // Nova posição inicial do jogador na segunda fase
int monsterX3 = 14; // Posição inicial do monstro no segundo mapa
int monsterY3 = 13; // Posição inicial do monstro no segundo mapa
int keyX3 = 13; // Posição da chave no segundo mapa
int keyY3 = 13; // Posição da chave no segundo mapa
int doorX3 = 26; // Posição da porta no segundo mapa
int doorY3 = 13; // Posição da porta no segundo mapa
int hasKey3 = 0; // O jogador não pegou a chave inicialmente no segundo mapa
int doorOpen3 = 0; // A porta está inicialmente fechada no segundo mapa
int lives3 = lives; // Vidas do jogador na segunda fase
int gameRunning3 = 1;
int lastMoveX2 = 0;
int lastMoveY2 = 0;

while (gameRunning3) {
    // Lógica do segundo mapa
    system("cls");
    // Calcula as diferenças entre as coordenadas do jogador e as coordenadas do monstro
    

    // Desenhar o mapa com os personagens
    drawMap3(map3, &playerX3, &playerY3, monsterX3, monsterY3, keyX3, keyY3, hasKey3, doorX3, doorY3, doorOpen3, lives3);
    char move3 = getch();
    switch(move3) {
        case 'w':
            if (map3[playerY3 - 1][playerX3] != '*') {
                playerY3--;
                lastMoveX2 = 0;
                lastMoveY2 = -1;
            }
            break;
        case 's':
            if (map3[playerY3 + 1][playerX3] != '*') {
                playerY3++;
                lastMoveX2 = 0;
                lastMoveY2 = 1;
            }
            break;
        case 'a':
            if (map3[playerY3][playerX3 - 1] != '*') {
                playerX3--;
                lastMoveX2 = -1;
                lastMoveY2 = 0;
            }
            break;
        case 'd':
            if (map3[playerY3][playerX3 + 1] != '*') {
                playerX3++;
                lastMoveX2 = 1;
                lastMoveY2 = 0;
            }
            break;
        case 'i': // Interagir com a chave
                        if (playerX3 == keyX3 && playerY3 == keyY3 && !hasKey3) {
                            hasKey3 = 1; // O jogador pegou a chave
                            doorOpen3 = 1; // Abrir a porta
                        }
                        break;
        case 27: // Tecla Esc
            gameRunning3 = 0;
            break;
    }

    // Movimentação do monstro na mesma direção que o jogador
    if (lastMoveX != 0 || lastMoveY != 0) {
        int newMonsterX3 = monsterX3 + lastMoveX;
        int newMonsterY3 = monsterY3 + lastMoveY;

        // Verifique se a nova posição do monstro não é um obstáculo
        if (map3[newMonsterY3][newMonsterX3] != '*') {
            monsterX3 = newMonsterX3;
            monsterY3 = newMonsterY3;
        }
    
    // Verificar se o jogador encostou em um obstáculo (espinhos) no segundo mapa
    if (map3[playerY3][playerX3] == '#') {
        lives3--; // Reduzir uma vida
        if (lives3 <= 0) {
            system("cls");
            printf("Voce foi morto ao encostar no obstaculo! Pressione qualquer tecla para voltar ao menu principal.\n");
            getch();
            gameRunning3 = 0; // Terminar o jogo
        } else {
            // Restaurar a posição original do jogador
            playerX3 = 1;
            playerY3 = 1;
        }
    }
    // Verificar se o jogador encostou no monstro no segundo mapa
    if (playerX3 == monsterX3 && playerY3 == monsterY3) {
        lives3--; // Reduzir uma vida
        if (lives3 <= 0) {
            system("cls");
            printf("Voce foi morto pelo monstro! Pressione qualquer tecla para voltar ao menu principal.\n");
            getch();
            gameRunning3 = 0; // Terminar o jogo
        } else {
            // Restaurar a posição original do jogador
            playerX3 = 1;
            playerY3 = 1;
        }
    }
    
    if (playerX3 == keyX3 && playerY3 == keyY3 && !hasKey3) {
        hasKey3 = 1; // O jogador pegou a chave
        doorOpen3 = 1; // Abrir a porta
    }
    // Verificar se o jogador está em cima da porta aberta e terminar o jogo
    if (playerX3 == doorX3 && playerY3 == doorY3 && doorOpen3) {
        system("cls");
        printf("Parabens! Você completou o jogo. Pressione qualquer tecla voltar ao menu.\n");
        getch();
        gameRunning3 = 0;

        // Configuração da terceira fase
        // ...

        // Chamar a função da terceira fase
        // ...
    }
}

// A
if (lives3 <= 0) {
    system("cls");
    printf("Você foi derrotado! Pressione qualquer tecla para voltar ao menu principal.\n");
    getch();
    gameRunning3 = 0; // Terminar o jogo
}
 // Terminar o jogo
    break; // Sair do loop
}

                }
            }
        } 

        linhacol(1, 1);
        textcolor(WHITE, _RED);
        printf("Você escolheu a opção: \n %d", opc);
    }

    textcolor(WHITE, _BLACK);
    linhacol(24, 1);
    printf("");
	
    return 0;
}

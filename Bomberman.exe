#include<stdio.h>
#include<windows.h>
#include<conio.h>
#include<ctime>
#include<vector>

#define ARRIBA 72
#define IZQUIERDA 75
#define DERECHA 77
#define ABAJO 80
#define BOMBA 'B'
#define EXPLOSION 178
#define VACIO ' '
#define MURO 177
#define BLOQUE_DESTRUCTIBLE 176
#define INDESTRUCTIBLE 178

struct Bomba {
    int x, y;
    clock_t tiempoColocacion;
    Bomba(int _x, int _y) : x(_x), y(_y), tiempoColocacion(clock()) {}
};

void gotoxy(int x, int y) {
    HANDLE hCon = GetStdHandle(STD_OUTPUT_HANDLE);
    COORD dwPos;
    dwPos.X = x;
    dwPos.Y = y;
    SetConsoleCursorPosition(hCon, dwPos);
}

void OcultarCursor() {
    HANDLE hCon = GetStdHandle(STD_OUTPUT_HANDLE);
    CONSOLE_CURSOR_INFO cci;
    cci.dwSize = 2;
    cci.bVisible = FALSE;
    SetConsoleCursorInfo(hCon, &cci);
}

void pintar_limites() {
    for (int i = 1; i < 120; i++) {
        gotoxy(i, 1); printf("%c", MURO);
        gotoxy(i, 29); printf("%c", MURO);
    }
    
    for (int i = 2; i < 29; i++) {
        gotoxy(1, i); printf("%c", MURO);
        gotoxy(119, i); printf("%c", MURO);
    }
}

class JUANITO {
    int x, y;
    int vida;
public:
    JUANITO(int _x, int _y) : x(_x), y(_y), vida(3) {}
    void pintar();
    void borrar();
    void mover(char mapa[30][120], std::vector<Bomba> &bombas);
    int getX() { return x; }
    int getY() { return y; }
    int getVida() { return vida; }
    void reducirVida() { vida--; }
    void colocarBomba(char mapa[30][120], std::vector<Bomba> &bombas);
};

void JUANITO::pintar() {
    gotoxy(x, y); printf("%c%c%c", 200, 02, 188); 
    gotoxy(x, y + 1); printf("%c%c%c", 217, 202, 192);
}

void JUANITO::borrar() {
    gotoxy(x, y); printf("   ");
    gotoxy(x, y + 1); printf("   ");
}

void JUANITO::mover(char mapa[30][120], std::vector<Bomba> &bombas) {
    if (kbhit()) {
        char tecla = getch();
        borrar();
        
        if (tecla == IZQUIERDA && x > 2 &&
            mapa[y][x - 1] == VACIO && mapa[y + 1][x - 1] == VACIO) {
            x--;
        }
        if (tecla == DERECHA && x + 3 < 119 &&
            mapa[y][x + 3] == VACIO && mapa[y + 1][x + 3] == VACIO) {
            x++;
        }
        if (tecla == ARRIBA && y > 2 &&
            mapa[y - 1][x] == VACIO && mapa[y - 1][x + 1] == VACIO) {
            y--;
        }
        if (tecla == ABAJO && y + 2 < 29 &&
            mapa[y + 2][x] == VACIO && mapa[y + 2][x + 1] == VACIO) {
            y++;
        }


        if (tecla == 'x') {
            colocarBomba(mapa, bombas);
        }

        pintar();
    }
}

void JUANITO::colocarBomba(char mapa[30][120], std::vector<Bomba> &bombas) {
    int bx = x + 1; 
    int by = y + 1;
    bombas.push_back(Bomba(bx, by)); 
    mapa[by][bx] = BOMBA;
    gotoxy(bx, by);
    printf("%c", BOMBA);
}

class Enemy {
    int x, y, direccion; 
public:
    Enemy(int _x, int _y) : x(_x), y(_y), direccion(rand() % 4) {}
    void pintar();
    void borrar();
    void moverEnLinea(char mapa[30][120]);
    int getX() { return x; }
    int getY() { return y; }
};

void Enemy::pintar() {
    gotoxy(x, y); printf("%c%c%c", 227, 94, 224); 
    gotoxy(x, y + 1); printf("%c%c%c", 167, 167, 167);
}

void Enemy::borrar() {
    gotoxy(x, y); printf("   ");
    gotoxy(x, y + 1); printf("   ");
}

void Enemy::moverEnLinea(char mapa[30][120]) {
    borrar();
    switch (direccion) {
        case 0: // Mover arriba
            if (y > 2 && mapa[y - 1][x] == VACIO) y--;
            else direccion = 1; // Cambia a abajo si choca
            break;
        case 1: // Mover abajo
            if (y + 2 < 29 && mapa[y + 2][x] == VACIO) y++;
            else direccion = 0; // Cambia a arriba si choca
            break;
        case 2: // Mover izquierda
            if (x > 2 && mapa[y][x - 1] == VACIO) x--;
            else direccion = 3; // Cambia a derecha si choca
            break;
        case 3: // Mover derecha
            if (x + 3 < 119 && mapa[y][x + 3] == VACIO) x++;
            else direccion = 2; // Cambia a izquierda si choca
            break;
    }
    pintar();
}

class JUEGO {
public:
    char mapa[30][120];
    JUEGO() { generarLaberinto(); }
    void generarLaberinto();
    void dibujarMapa();
    void actualizarBombas(std::vector<Bomba> &bombas, JUANITO &nave, std::vector<Enemy> &enemigos);
};

void JUEGO::generarLaberinto() {
    srand(time(NULL));
    for (int i = 0; i < 30; i++) {
        for (int j = 0; j < 120; j++) {
            if (i == 0 || j == 0 || i == 29 || j == 119) {
                mapa[i][j] = MURO;
            } else if (i % 2 == 0 && j % 8 == 0) {
                mapa[i][j] = INDESTRUCTIBLE;
            } else {
                mapa[i][j] = VACIO;
            }
        }
    }
}

void JUEGO::dibujarMapa() {
    for (int i = 0; i < 30; i++) {
        for (int j = 0; j < 120; j++) {
            gotoxy(j, i);
            printf("%c", mapa[i][j]);
        }
    }
}

void JUEGO::actualizarBombas(std::vector<Bomba> &bombas, JUANITO &nave, std::vector<Enemy> &enemigos) {
    for (int i = 0; i < bombas.size(); i++) {
        if (clock() - bombas[i].tiempoColocacion >= 3000) { 
            int bx = bombas[i].x;
            int by = bombas[i].y;

            // Mostrar explosión en cruz extendida
            gotoxy(bx, by); printf("%c", EXPLOSION);

            // arriba
            if (by - 1 > 1 && mapa[by - 1][bx] != INDESTRUCTIBLE) {
                gotoxy(bx, by - 1); printf("%c", EXPLOSION);
            }
            // abajo
            if (by + 1 < 29 && mapa[by + 1][bx] != INDESTRUCTIBLE) {
                gotoxy(bx, by + 1); printf("%c", EXPLOSION);
            }
            //izquierda
            if (bx - 1 > 1 && mapa[by][bx - 1] != INDESTRUCTIBLE) {
                gotoxy(bx - 1, by); printf("%c", EXPLOSION);
            }
            if (bx - 2 > 1 && mapa[by][bx - 2] != INDESTRUCTIBLE) {
                gotoxy(bx - 2, by); printf("%c", EXPLOSION);
            }
            //derecha
            if (bx + 1 < 119 && mapa[by][bx + 1] != INDESTRUCTIBLE) {
                gotoxy(bx + 1, by); printf("%c", EXPLOSION);
            }
            if (bx + 2 < 119 && mapa[by][bx + 2] != INDESTRUCTIBLE) {
                gotoxy(bx + 2, by); printf("%c", EXPLOSION);
            }

            Sleep(1000); // Tiempo que permanece visible la explosión

            // Remover explosión y destruir bloques
            gotoxy(bx, by); printf("%c", VACIO);
            mapa[by][bx] = VACIO;

            // Remover explosión de los alrededores
            if (by - 1 > 1 && mapa[by - 1][bx] != INDESTRUCTIBLE) {
                mapa[by - 1][bx] = VACIO;
                gotoxy(bx, by - 1); printf("%c", VACIO);
            }
            if (by + 1 < 29 && mapa[by + 1][bx] != INDESTRUCTIBLE) {
                mapa[by + 1][bx] = VACIO;
                gotoxy(bx, by + 1); printf("%c", VACIO);
            }
            if (bx - 1 > 1 && mapa[by][bx - 1] != INDESTRUCTIBLE) {
                mapa[by][bx - 1] = VACIO;
                gotoxy(bx - 1, by); printf("%c", VACIO);
            }
            if (bx - 2 > 1 && mapa[by][bx - 2] != INDESTRUCTIBLE) {
                mapa[by][bx - 2] = VACIO;
                gotoxy(bx - 2, by); printf("%c", VACIO);
            }
            if (bx + 1 < 119 && mapa[by][bx + 1] != INDESTRUCTIBLE) {
                mapa[by][bx + 1] = VACIO;
                gotoxy(bx + 1, by); printf("%c", VACIO);
            }
            if (bx + 2 < 119 && mapa[by][bx + 2] != INDESTRUCTIBLE) {
                mapa[by][bx + 2] = VACIO;
                gotoxy(bx + 2, by); printf("%c", VACIO);
            }

            bombas.erase(bombas.begin() + i); // Eliminar bomba de la lista
        }
    }
}

int main() {
    OcultarCursor();
    pintar_limites();
    JUEGO juego;
    juego.dibujarMapa();

    JUANITO bomberman(7, 7);
    bomberman.pintar();

    Enemy enemigo1(50, 10);
    enemigo1.pintar();

    std::vector<Bomba> bombas;
    std::vector<Enemy> enemigos;
    enemigos.push_back(enemigo1);

    bool game_over = false;
    while (!game_over) {
        bomberman.mover(juego.mapa, bombas);
        for (std::vector<Enemy>::iterator it = enemigos.begin(); it != enemigos.end(); ++it) {
         it->moverEnLinea(juego.mapa);
         }
        juego.actualizarBombas(bombas, bomberman, enemigos);
        Sleep(100);
    }

    return 0;
}

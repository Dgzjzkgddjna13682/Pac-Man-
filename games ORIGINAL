#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <termios.h>
#include <time.h>

// All the elements to be used
#define WIDTH 40
#define HEIGHT 20
#define PACMAN 'C'
#define WALL '#'
#define FOOD '.'
#define EMPTY ' '
#define DEMON 'X'

// Global Variables
int res = 0;
int score = 0;
int pacman_x, pacman_y;
char board[HEIGHT][WIDTH];  // Fixed: Added HEIGHT for the board dimensions
int food = 0;
int curr = 0;

// Function to reset terminal settings
void reset_terminal_mode() {
    struct termios term;
    tcgetattr(0, &term);
    term.c_lflag |= ICANON | ECHO;
    tcsetattr(0, TCSANOW, &term);
}

// Function to set terminal to raw mode
void set_conio_terminal_mode() {
    struct termios term;
    tcgetattr(0, &term);
    term.c_lflag &= ~(ICANON | ECHO);
    tcsetattr(0, TCSANOW, &term);
}

// Function to read a single character without Enter
char getch() {
    char ch;
    read(0, &ch, 1);
    return ch;
}

// Initialize the game board
void initialize() {
    srand(time(NULL));

    // Setting up the game board
    for (int i = 0; i < HEIGHT; i++) {
        for (int j = 0; j < WIDTH; j++) {
            if (i == 0 || j == WIDTH - 1 || j == 0 || i == HEIGHT - 1) {
                board[i][j] = WALL;
            } else {
                board[i][j] = EMPTY;
            }
        }
    }

    // Adding internal walls
    int count = 50;
    while (count != 0) {
        int i = rand() % HEIGHT;
        int j = rand() % WIDTH;

        if (board[i][j] != WALL && board[i][j] != PACMAN) {
            board[i][j] = WALL;
            count--;
        }
    }

    int val = 5;
    while (val--) {
        int row = rand() % HEIGHT;
        for (int j = 3; j < WIDTH - 3; j++) {
            if (board[row][j] != WALL && board[row][j] != PACMAN) {
                board[row][j] = WALL;
            }
        }
    }

    // Adding demons
    count = 10;
    while (count != 0) {
        int i = rand() % HEIGHT;
        int j = rand() % WIDTH;

        if (board[i][j] != WALL && board[i][j] != PACMAN) {
            board[i][j] = DEMON;
            count--;
        }
    }

    // Positioning Pacman
    pacman_x = WIDTH / 2;
    pacman_y = HEIGHT / 2;
    board[pacman_y][pacman_x] = PACMAN;

    // Adding food
    for (int i = 0; i < HEIGHT; i++) {
        for (int j = 0; j < WIDTH; j++) {
            if (i % 2 == 0 && j % 2 == 0 &&
                board[i][j] != WALL &&
                board[i][j] != DEMON &&
                board[i][j] != PACMAN) {
                board[i][j] = FOOD;
                food++;
            }
        }
    }
}

// Clear screen using ANSI escape codes
void clear_screen() {
    printf("\033[H\033[J");
}

// Draw the game board
void draw() {
    clear_screen();

    for (int i = 0; i < HEIGHT; i++) {
        for (int j = 0; j < WIDTH; j++) {
            printf("%c", board[i][j]);
        }
        printf("\n");
    }
    printf("Score: %d\n", score);
}

// Move Pacman
void move(int move_x, int move_y) {
    int x = pacman_x + move_x;
    int y = pacman_y + move_y;

    if (board[y][x] != WALL) {
        if (board[y][x] == FOOD) {
            score++;
            food--;
            curr++;
            if (food == 0) {
                res = 2;
                return;
            }
        } else if (board[y][x] == DEMON) {
            res = 1;
        }

        board[pacman_y][pacman_x] = EMPTY;
        pacman_x = x;
        pacman_y = y;
        board[pacman_y][pacman_x] = PACMAN;
    }
}

// Main function
int main() {
    set_conio_terminal_mode();
    atexit(reset_terminal_mode);

    initialize();

    printf("Use buttons for w(up), a(left), d(right), and s(down).\nPress q to quit.\n");
    printf("Enter Y to continue: \n");

    char ch = getch();
    if (ch != 'Y' && ch != 'y') {
        printf("Exit Game!\n");
        return 1;
    }

    while (1) {
        draw();
        printf("Total Food count: %d\n", food);
        printf("Total Food eaten: %d\n", curr);

        if (res == 1) {
            clear_screen();
            printf("Game Over! Dead by Demon\nYour Score: %d\n", score);
            return 1;
        }

        if (res == 2) {
            clear_screen();
            printf("You Win! \nYour Score: %d\n", score);
            return 1;
        }

        ch = getch();

        switch (ch) {
        case 'w':
            move(0, -1);
            break;
        case 's':
            move(0, 1);
            break;
        case 'a':
            move(-1, 0);
            break;
        case 'd':
            move(1, 0);
            break;
        case 'q':
            printf("Game Over! Your Score: %d\n", score);
            return 0;
        }
    }

    return 0;
}

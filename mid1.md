```c
#include "raylib.h"

#define W 800
#define H 600
#define ROWS 6
#define COLS 8

int main(void)
{
    int blocks[ROWS][COLS];
    int row, col, score = 0;
    int win = 0, lose = 0;

    float bx = 400, by = 450;
    float dx = 4, dy = -4;
    float px = 350, py = 550;

    Color colors[ROWS] = {
        RED, ORANGE, YELLOW,
        GREEN, BLUE, PURPLE
    };

    InitWindow(W, H, "Rainbow Block Game");
    SetTargetFPS(60);

    // 블록 초기화
    for (row = 0; row < ROWS; row++)
        for (col = 0; col < COLS; col++)
            blocks[row][col] = 1;

    while (!WindowShouldClose())
    {
        // 재시작 기능
        if (IsKeyPressed(KEY_ENTER) && (win || lose))
        {
            score = 0;
            win = 0;
            lose = 0;

            bx = 400;
            by = 450;
            dx = 4;
            dy = -4;
            px = 350;

            for (row = 0; row < ROWS; row++)
                for (col = 0; col < COLS; col++)
                    blocks[row][col] = 1;
        }

        // 게임 진행
        if (!win && !lose)
        {
            // 패들 이동
            if (IsKeyDown(KEY_LEFT))  px -= 6;
            if (IsKeyDown(KEY_RIGHT)) px += 6;

            if (px < 0) px = 0;
            if (px > W - 100) px = W - 100;

            // 공 이동
            bx += dx;
            by += dy;

            // 벽 충돌
            if (bx < 8 || bx > W - 8)
                dx = -dx;

            if (by < 70)
                dy = -dy;

            // 패들과 공의 충돌
            if (dy > 0 &&
                by + 8 >= py &&
                by - 8 <= py + 15 &&
                bx + 8 >= px &&
                bx - 8 <= px + 100)
            {
                dy = -4;
            }

            // 블록 충돌
            for (row = 0; row < ROWS; row++)
            {
                for (col = 0; col < COLS; col++)
                {
                    int x = 40 + col * 90;
                    int y = 90 + row * 32;

                    if (blocks[row][col] &&
                        bx + 8 >= x && bx - 8 <= x + 80 &&
                        by + 8 >= y && by - 8 <= y + 24)
                    {
                        blocks[row][col] = 0;
                        dy = -dy;
                        score += 10;

                        goto HIT;
                    }
                }
            }

        HIT:

            // 공을 놓치면 게임 오버
            if (by > H)
                lose = 1;

            // 남은 블록 확인
            {
                int count = 0;

                for (row = 0; row < ROWS; row++)
                    for (col = 0; col < COLS; col++)
                        count += blocks[row][col];

                if (count == 0)
                    win = 1;
            }
        }

        // 화면 그리기
        BeginDrawing();
        ClearBackground((Color){ 20, 25, 45, 255 });

        DrawText("RAINBOW BLOCK GAME", 20, 20, 24, WHITE);
        DrawText(TextFormat("SCORE: %d", score),
                 650, 25, 20, YELLOW);

        // 무지개 블록
        for (row = 0; row < ROWS; row++)
        {
            for (col = 0; col < COLS; col++)
            {
                if (blocks[row][col])
                {
                    DrawRectangle(
                        40 + col * 90,
                        90 + row * 32,
                        80, 24,
                        colors[row]
                    );
                }
            }
        }

        // 패들과 공
        DrawRectangle((int)px, (int)py, 100, 15, SKYBLUE);
        DrawCircle((int)bx, (int)by, 8, WHITE);

        // 승리 화면
        if (win)
        {
            DrawText("YOU WIN!", 320, 280, 40, GREEN);
            DrawText("Press ENTER to restart",
                     260, 340, 25, WHITE);
        }

        // 게임 오버 화면
        if (lose)
        {
            DrawText("GAME OVER", 290, 280, 40, RED);
            DrawText("Press ENTER to restart",
                     260, 340, 25, WHITE);
        }

        DrawText("LEFT / RIGHT: MOVE",
                 20, 570, 18, LIGHTGRAY);

        EndDrawing();
    }

    CloseWindow();
    return 0;
}
```

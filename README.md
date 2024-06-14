#include "stdafx.h"
#include <stdio.h>
#include <stdlib.h>
#include <conio.h>
#include <string.h>
#include <malloc.h>
#include <clocale>

#define ENTER 13
#define ESC 27
#define UP 72
#define DOWN 80

using namespace std;
using namespace System;
using namespace System::IO;

struct z {
    char nicname[40];
    long summ;
    long date_of_registration;
    float obiem;
    char gorod[20];
    char korobka[30];
    char prodovec[40];
};

struct sp {
    char prodovec[40];
    long summ;
    char nicname[40];
    struct sp* sled;
    struct sp* pred;
};

char dan[8][100] = {
    "Самая ранняя дата выпуска автомобиля ?              ",
    "самая дорогая машина ?                              ",
    "саиый большой объем двигателя                       ",
    "Бывает ли одинаковый объем у разного вида коробок   ",
    "Алфавитный список владельцев                        ",
    "Диаграмма процентного соотношения цены автомобилей  ",
    "Выход                                               "
};

char BlankLine[] = "                                                      ";

int menu(int);
void first(struct z*, int);
void maxim(struct z*, int);
void max(struct z*, int);
void korob(struct z*, int);
void alfalist(struct z*, int, struct sp**);
void vstavka(struct z*, int, struct sp**, char*, char*);
void diagram(struct z*, int, struct sp**);

int main(array<System::String ^> ^args)
{
    BlankLine[100] = 0;

    int i, n, NC;
    FILE* in;
    struct z* owner = 0;
    struct sp* spisok = 0;
    setlocale(LC_CTYPE, "Russian");
    Console::CursorVisible::set(false);
    Console::BufferHeight = Console::WindowHeight;
    Console::BufferWidth = Console::WindowWidth;

    if ((in = fopen("mash.txt", "r")) == NULL)
    {
        printf("\nФайл mash.txt не открыт !");
        getch(); exit(1);
    }
    fscanf(in, "%d", &NC);
    owner = (struct z*)malloc(NC * sizeof(struct z));
    for (i = 0; i < NC; i++)
        fscanf(in, "%s%ld%ld%f%s%s%s", owner[i].nicname, &owner[i].summ, &owner[i].date_of_registration, &owner[i].obiem, owner[i].gorod, owner[i].korobka, owner[i].prodovec);

    Console::CursorLeft = 42;
    Console::BackgroundColor = ConsoleColor::Cyan;
    Console::ForegroundColor = ConsoleColor::Black;
    printf(" База данных покупателей автомобилей \n");
    printf("|       Марка      |  Цена    |   Год   | Объем   |  Город        | Тип коробки |    Владелец     | ");

    for (i = 0; i < NC; i++)
    {
        Console::CursorTop = i + 1;
        Console::BackgroundColor = ConsoleColor::Cyan;
        Console::ForegroundColor = ConsoleColor::Black;
        printf("\n|  %-14s  | %-7ld  |  %-6ld |  %-7.1f|  %-11s  |  %9s  |  %-13s  |", owner[i].nicname, owner[i].summ, owner[i].date_of_registration, owner[i].obiem, owner[i].gorod, owner[i].korobka, owner[i].prodovec);
    }

    getch();
    while (1)
    {
        Console::ForegroundColor = ConsoleColor::Magenta;
        Console::BackgroundColor = ConsoleColor::Cyan;
        Console::Clear();
        Console::ForegroundColor = ConsoleColor::White;
        Console::BackgroundColor = ConsoleColor::Magenta;
        Console::CursorLeft = 10;
        Console::CursorTop = 4;
        printf(BlankLine);
        for (i = 0; i < 7; i++)
        {
            Console::CursorLeft = 10;
            Console::CursorTop = i + 5;
            printf(" %s ", dan[i]);
        }
        Console::CursorLeft = 10;
        Console::CursorTop = 12;
        printf(BlankLine);
        n = menu(7);
        switch (n) {
            case 1: first(owner, NC); break;
            case 2: maxim(owner, NC); break;
            case 3: max(owner, NC); break;
            case 4: korob(owner, NC); break;
            case 5: alfalist(owner, NC, &spisok); break;
            case 6: diagram(owner, NC, &spisok); break;
            case 7: free(owner); exit(0);
        }
    }

    return 0;
}

int menu(int n)
{
    int y1 = 0, y2 = n - 1;
    char c = 1;
    while (c != ESC)
    {
        switch (c) {
            case DOWN: y2 = y1; y1++; break;
            case UP: y2 = y1; y1--; break;
            case ENTER: return y1 + 1;
        }
        if (y1 > n - 1) { y2 = n - 1; y1 = 0; }
        if (y1 < 0) { y2 = 0; y1 = n - 1; }
        Console::ForegroundColor = ConsoleColor::DarkBlue;
        Console::BackgroundColor = ConsoleColor::Magenta;
        Console::CursorLeft = 11;
        Console::CursorTop = y1 + 5;
        printf("%s", dan[y1]);
        Console::ForegroundColor = ConsoleColor::White;
        Console::BackgroundColor = ConsoleColor::Magenta;
        Console::CursorLeft = 11;
        Console::CursorTop = y2 + 5;
        printf("%s", dan[y2]);
        c = getch();
    }
    exit(0);
}

void first(struct z* owner, int NC)
{
    int i;
    struct z best;
    best.date_of_registration = owner[0].date_of_registration;
    strcpy(best.nicname , owner[0].nicname);  // Исправлен индекс
    for (i = 1; i < NC; i++)
    {
        if (owner[i].date_of_registration < best.date_of_registration)
        {
            best.date_of_registration = owner[i].date_of_registration;
            strcpy(best.nicname , owner[i].nicname);
        }
    }

    Console::ForegroundColor = ConsoleColor::White;
    Console::BackgroundColor = ConsoleColor::DarkGray;
    Console::CursorLeft = 20;
    Console::CursorTop = 20;
    Console::Clear();
    printf("\n |Дата выпуска: %ld   |", best.date_of_registration);
    printf("\n |=====================|\n");
    printf("\n |Модель: %s|", best.nicname);
    printf("\n |=====================|\n");

    getch();
}

void maxim(struct z* owner, int NC)
{
    int i;
    struct z best;
    best.summ = owner[0].summ;
    strcpy(best.nicname , owner[0].nicname);  // Исправлен индекс
    for (i = 1; i < NC; i++)
    {
        if (owner[i].summ > best.summ)
        {
            best.summ = owner[i].summ;
            strcpy(best.nicname , owner[i].nicname);
        }
    }

    Console::ForegroundColor = ConsoleColor::Black;
    Console::BackgroundColor = ConsoleColor::White;
    Console::CursorLeft = 10;
    Console::CursorTop = 15;
    Console::Clear();
    printf("\n    |максимальная цена автомобиля %ld| ", best.summ);
    printf("\n    |===================================|\n");
    printf("\n    |Модель: %s                    |", best.nicname);
    printf("\n    |===================================|\n");

    getch();
}

void max(struct z* owner, int NC)
{
    int i = 0;
    struct z best;
    best.obiem = owner[0].obiem;
    strcpy(best.nicname , owner[0].nicname);  // Исправлен индекс
    for (i = 1; i < NC; i++)
    {
        if (owner[i].obiem > best.obiem)
        {
            best.obiem = owner[i].obiem;
            strcpy(best.nicname , owner[i].nicname);
        }
    }

    Console::ForegroundColor = ConsoleColor::Cyan;
    Console::BackgroundColor = ConsoleColor::DarkGray;
    Console::CursorLeft = 10;
    Console::CursorTop = 15;
    Console::Clear();
    printf("\n |максимальный обьем двигателя == %.1f| ", best.obiem);
    printf("\n |===================================|\n");
    printf("\n |Модель: %s                      |", best.nicname);
    printf("\n |===================================|\n");

    getch();
}

void korob(struct z* owner, int NC)
{
    int i = 0;
    int g = 0;
    int z = 0;
    int b = 0;
    struct z best;

    for (g = 1; g < NC; g++)
    {
        best.obiem = owner[g].obiem;
        strcpy(best.korobka, owner[g].korobka);

        for (i = 1; i < NC; i++)
        {
            if (owner[i].obiem == best.obiem)
                if (strcmp(best.korobka, owner[0].korobka) == 0)
                {
                    b = i;
                    z = g;
                }
        }
    }

    Console::ForegroundColor = ConsoleColor::Red;
    Console::BackgroundColor = ConsoleColor::White;
    Console::CursorLeft = 10;
    Console::CursorTop = 15;
    Console::Clear();
    printf("\n  Бывает ли одинаковый объем у разного вида коробок ");
    printf("\n  ================================================\n");
    printf("\n  Да: %s и %s ", best.korobka, owner[b].korobka);
    printf("\n  =======================\n");

    getch();
}

void alfalist(struct z* owner, int NC, struct sp** spisok)
{
    int i;
    struct sp* nt, *z = 0;
    Console::ForegroundColor = ConsoleColor::Black;
    Console::BackgroundColor = ConsoleColor::Yellow;
    Console::Clear();
    if (!(*spisok))
        for (i = 0; i < NC; i++)
            vstavka(owner, NC, spisok, owner[i].prodovec, owner[i].nicname);

    Console::Clear();
    printf("\n Алфавитный список владельцев           Обратный алфавитный список");
    printf("\n ============================           ===========================\n");
    for (nt = *spisok; nt != 0; nt = nt->sled)
        printf("\n %-20s %-20s", nt->prodovec, nt->nicname);
    for (nt = *spisok, z = 0; nt != 0; z = nt, nt = nt->sled);
    for (nt = z, i = 0; nt != 0; nt = nt->pred, i++)
    {
        Console::CursorLeft = 40;
        Console::CursorTop = i + 4;
        printf(" %-30s % -s", nt->prodovec, nt->nicname);
    }
    getch();
}

void vstavka(struct z* owner, int NC, struct sp** spisok, char* prodovec, char* nicname)
{
    int i;
    struct sp *New, *nt, *z = 0;
    for (nt = *spisok; nt != 0 && strcmp(nt->prodovec, prodovec) < 0; z = nt, nt = nt->sled);
    if (nt && strcmp(nt->prodovec, prodovec) == 0)
        return;
    New = (struct sp*)malloc(sizeof(struct sp));
    strcpy(New->prodovec, prodovec);
    strcpy(New->nicname, nicname);
    New->sled = nt;
    New->summ = 0;
    New->pred = z;
    for (i = 0; i < NC; i++)
        if (strcmp(owner[i].prodovec, prodovec) == 0)
            New->summ += owner[i].summ;
    if (!z) *spisok = New;
    if (nt) nt->pred = New;
    New->sled = nt;
    if (z) z->sled = New;
    New->pred = z;
    return;
}

void diagram(struct z* owner, int NC, struct sp** spisok)
{
    struct sp *nt;
    int len, i, NColor;
    long sum = 0;
    char str1[20];
    char str2[20];
    System::ConsoleColor Color;
    Console::ForegroundColor = ConsoleColor::Black;

    Console::BackgroundColor = ConsoleColor::Gray;
    Console::Clear();

    for (i = 0; i < NC; i++)
        sum = sum + owner[i].summ;
    if (!(*spisok))
        for (i = 0; i < NC; i++)
            vstavka(owner, NC, spisok, owner[i].prodovec, owner[i].nicname);
    Color = ConsoleColor::Black; NColor = 0;
    for (nt = *spisok, i = 0; nt != 0; nt = nt->sled, i++)
    {
        sprintf(str1, "%s", nt->prodovec);
        sprintf(str2, "%3.1f%%", (nt->summ * 100. / sum));
        Console::ForegroundColor = ConsoleColor::Black;
        Console::BackgroundColor = ConsoleColor::Cyan;
        Console::CursorLeft = 5; Console::CursorTop = i + 1;
        printf(str1);
        Console::CursorLeft = 20;
        printf("%s", str2);
        Console::BackgroundColor = ++Color; NColor++;
        Console::CursorLeft = 30;
        for (len = 0; len < nt->summ * 100 / sum; len++)
            printf(" ");
        if (NColor == 14) {
            Color = ConsoleColor::Black; NColor = 0;
        }
    }
    getch();
    return;
}


<!---
Egorarxangelskiy23082005-svg/Egorarxangelskiy23082005-svg is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->

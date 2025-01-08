#include <iostream>
#include <cstdlib>    // Rastgele sayı oluşturabılmek için bu kütüphaneyi kullanmalıyız
#include <ctime>      // Oyun zaman fonksıyonları ekleyebilmemiz için kullanılır 
#include <string>
#include <windows.h>  //  zaman metodaları kulanabilmemize sağlaar
#include <conio.h>    //  kbhit(), getch() kulanabilmemiz içın bu kütüp haneyi kulanmalıyız 

#define CLEAR_SCREEN "cls"

using namespace std;

//===========================================================================
//  oyun nesnesi (temeloyun)
//  başka nesneler bu nesneden mıras alacaktır (yani: tum ozelikleri kopaylayip yeni istedigimiz nesneye yapıştıracak)
//===========================================================================
class temeloyun {
public:
    int x;  // Nesnenin X (yatay)
    int y;  // Nesnenin Y (dikey)

    // nesnenin Update metodu olması gerekiyor .
    virtual void Update() = 0;
};

//===========================================================================
// enum yol belirleyebilen bir metodtur.
//===========================================================================
enum eDirection { STOP = 0, LEFT, RIGHT, UP, DOWN };

//buraya kadar abdurrahman yaptıklatı

//===========================================================================
// Meyve (Fruit):
// meyve klasına miras aldıracağız (temel oyun'dan).
//===========================================================================
class Fruit : public temeloyun {
public:
    // meyveyi rastgele bir yere atıyor.
    Fruit(int maxWidth, int maxHeight) {
        Regenerate(maxWidth, maxHeight);
    }

    // meyveyi yenilettiren metod.
    void Regenerate(int maxWidth, int maxHeight) {
        x = rand() % maxWidth;
        y = rand() % maxHeight;
    }

    // Update boş bırakacağız (meyve sabit durduğu için).
    void Update() override {
    }
};

//===========================================================================
// yılan yerine (Snake) adlı bir class tanımlayacağız.
// temeloyun'dan miras aldırdık. hafızada (x, y) ve kuyruk bilgileri tutacak.
//===========================================================================
class Snake : public temeloyun {
private:
    // Kuyruk yapısı ve kuyruk uzunluğu=
    int tailX[100];
    int tailY[100];
    int nTail;

    // yılan hangi yönde hareket edeceğini gösterir
    eDirection dir;

public:
    // yılan başlangıç konumu vereceğiz (x,y)
    Snake(int startX, int startY) {
        x = startX;
        y = startY;
        dir = STOP;   // başta hareketsiz olur 
        nTail = 0;    // kuyruk uzunluğu başta 0 olur.
    }

    // klavyeden yol değiştirmak için metodlar.
    void SetDirection(eDirection d) { dir = d; }
    eDirection GetDirection() const { return dir; }

    // kuyruk okuyabilmek için get metodları kulanacağız .
    int GetTailLength() const { return nTail; }
    int GetTailX(int i) const { return tailX[i]; }
    int GetTailY(int i) const { return tailY[i]; }

    // yemek yedikten sonra kuyruk uzunluğu arttırabilmek için .
    void AddTailSegment() {
        nTail++;
    }

    // kuyruk değerine bır önceki metoda göre güncelle.
    void UpdateTailPositions() {
        int prevX = tailX[0];
        int prevY = tailY[0];
        tailX[0] = x;
        tailY[0] = y;

        for (int i = 1; i < nTail; i++) {
            int prev2X = tailX[i];
            int prev2Y = tailY[i];
            tailX[i] = prevX;
            tailY[i] = prevY;
            prevX = prev2X;
            prevY = prev2Y;
        }
    }

    // Yılanın (x,y) hareket ederkrn krdınatları güncelle.
    void Update() override {
        switch (dir) {
        case LEFT:  x--; break;
        case RIGHT: x++; break;
        case UP:    y--; break;
        case DOWN:  y++; break;
        default:    break;
        }
    }
}

//buraya kadar ise abdullah yaptığı classları miras aldırarak yapmıştır.
//burdan sonraki classı game classı abdurrahman yapmıştır , (game calssı diğer classları çalışacak hale sağlar.)

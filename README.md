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
//===========================================================================
// oyun (Game) olarak adı vereceğiz.
// oyunun oyun yöntem eden ve meyve + yılan sınıfları içerecek.
//===========================================================================
class Game {
private:
    bool gameOver;        // oyun bitip bitmediğinden kontrol eder .
    const int width;      // oyun genişliği.
    const int height;     // oyun yüksekliği.
    int score;            // puan .

    // Oyundaki nesneler
    Snake* snake;
    Fruit* fruit;

public:
    // harita boyutlarına göre meyve ve yılan baştakı ayarlar .
    Game(int w, int h) : width(w), height(h) {
        srand((unsigned)time(nullptr));
        gameOver = false;
        score = 0;

        // yılan ve meyvey oluşturan adım.
        snake = new Snake(width / 2, height / 2);
        fruit = new Fruit(width, height);
    }

    // dinamik bellek kullanımı temizle.
    ~Game() {
        delete snake;
        delete fruit;
    }

    // oyunu bittip bitmediğini kontrol etmek için .
    bool IsGameOver() const {
        return gameOver;
    }

    // ekranı temizleyen metotlar .
    void ClearScreen() {
        system(CLEAR_SCREEN);
    }

    // oyun ekranda yansıtabilen metotlar.
    void Draw() {
        ClearScreen();

        // Üst sınır
        for (int i = 0; i < width + 2; i++) {
            cout << "#";
        }
        cout << endl;

        // harita içerdeki kısımlar .
        for (int i = 0; i < height; i++) {
            for (int j = 0; j < width + 2; j++) {
                // Sol,sağ kener .
                if (j == 0 || j == width + 1) {
                    cout << "#";
                }
                else {
                    int mapX = j - 1;
                    int mapY = i;

                    // Yılanın kafası büyük O ıle temsil ettik geriye kalan kısımdan belli olsun diye.
                    if (mapX == snake->x && mapY == snake->y) {
                        cout << "O";
                    }
                    // meyve.
                    else if (mapX == fruit->x && mapY == fruit->y) {
                        cout << "*";
                    }
                    // boş veya kuyruk .
                    else {
                        bool tailPrinted = false;
                        for (int k = 0; k < snake->GetTailLength(); k++) {
                            if (snake->GetTailX(k) == mapX && snake->GetTailY(k) == mapY) {
                                cout << "o";
                                tailPrinted = true;
                                break;
                            }
                        }
                        if (!tailPrinted) {
                            cout << " ";
                        }
                    }
                }
            }
            cout << endl;
        }

        // alt sinir.
        for (int i = 0; i < width + 2; i++) {
            cout << "#";
        }
        cout << endl;

        // puan gösterici.
        cout << "Score: " << score << endl;
    }

    // klavyeden emir alma yön tuşular ve  çıkış .
    void Input() {
        if (_kbhit()) {
            switch (_getch()) {
            case 'a': snake->SetDirection(LEFT);  break;
            case 'd': snake->SetDirection(RIGHT); break;
            case 'w': snake->SetDirection(UP);    break;
            case 's': snake->SetDirection(DOWN);  break;
            case 'x': gameOver = true;            break;
            default: break;
            }
        }
    }

    // her adımda yılan ve kafa hareketi çarpışma köntrölü (logic kullanarak yaptık).
    void Logic() {
        // ilk kuyruk bilgilerine güncelle.
        snake->UpdateTailPositions();

        // yılan hareket ettiren komutlar .
        snake->Update();

        // harştanın duvarları girip diğer tarfa çıkabilmesini sağlar .
        if (snake->x >= width)  snake->x = 0;
        else if (snake->x < 0)  snake->x = width - 1;
        if (snake->y >= height) snake->y = 0;
        else if (snake->y < 0)  snake->y = height - 1;

        // yılan bedeni ile çarpışma olduğunu kontrol ediyor .
        for (int i = 0; i < snake->GetTailLength(); i++) {
            if (snake->GetTailX(i) == snake->x && snake->GetTailY(i) == snake->y) {
                gameOver = true;
                break;
            }
        }

        // yılan meyveyi yadip yemediğini kontrol eden metodlar.
        if (snake->x == fruit->x && snake->y == fruit->y) {
            score += 10;
            fruit->Regenerate(width, height);
            snake->AddTailSegment();
        }
    }

    // oyun hızı seviyeye göre ayarlayan metodlardır.
    void Run(int level) {
        while (!gameOver) {
            Draw();
            Input();
            Logic();

            // seviyeye yükseldikçe hız arttıran metodlardır .
            int speedMs = 200 - (level - 1) * 20;
            if (speedMs < 20) speedMs = 20;

            //  milisaniye birimden bekleme .
            Sleep(speedMs);
        }

        // oyun biter bitmez puan yazan metod.
        cout << "\nOyun Bitti! Skorunuz: " << score << "\n" << endl;
    }
};

//===========================================================================
// ınt main
//===========================================================================
int main() {
    cout << "===== Snake Oyunu (Windows) =====\n";
    cout << "1 - 10 arasi seviye gir (1: yavas, 10: cok hizli): ";
    int s;
    cin >> s;
    if (s < 1)  s = 1;
    if (s > 10) s = 10;

    // oyun alanı 20 * 20 olark blirledik.
    Game game(20, 20);
    // kulancıdan oyun hızı girdirmesi beklenir .
    game.Run(s);

    cout << "Cikis icin bir tusa basin...\n";
    _getch();
    return 0;
}
//buraya kadar abdulrahmanin işledi şeylerdir 
//bu proje braber çalışbildiklerimiz bir fırsat olduğu için çok mutluyuz 

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

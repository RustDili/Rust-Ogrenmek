# Borçlanma
Gerçek hayattaki uygulamalarda çoğu zaman, bir değerle bağlanmış değişkenleri işlevlere iletmek veya bunları diğer değişkenlerin bağlanması amacıyla o değişkene atamak zorunda kalıyoruz. Bir değerin mülkiyetini üstlenmeden işlem yapmak gerektiğinde, o değerin sahibi olan orijinal değişken bağlamına bir başvuru yapılır.

## Borçlanma Nedir?
> [Borçlanma](https://github.com/nikomatsakis/rust-tutorials-keynote/blob/master/Ownership%20and%20Borrowing.pdf) Bu kavram dilimizde: *Bir şeyi iade etme garantisi verilerek ödünç almak* anlamına gelir.

## Paylaşılan ve Değişken borçlanmalar
⭐️ Rust'ta iki tür borçlanmadan bahsedebiliriz:

1. **Paylaşılan Borçlanma** `(&T)`

   * Bir veri parçası **içeriği değiştirilmemek kaydıyla, bir ya da çok kullanıcı tarafından ödünç** alınabilir. 
   
2. **Değişken Borçlanma** `(&mut T)`

   * Bir veri parçası, **tek bir kullanıcı tarafından ödünç alınabilir ve değiştirilebilir**, ancak bu durumda o verilere başkaları erişemez ve kullanamaz.
   
## Borçlanma Kuralları
Borçlanmanın oldukça önemli kuralları bulunmaktadır:

1. Bir veri parçası belli bir anda; ya paylaşılan ya da değişken borçlanma şeklinde ödünç alınabilir. Ancak **ikisi de aynı anda olamaz.**

2. Borçlanma **hem kopyalanan hem de taşınan türleri** kapsar.

2. **Canlılık** kavramı **↴**

```Rust
fn main() {
    let mut a = vec![1,2,3];
    let b = &mut a; // 'a' nın değişken borçlanması yani `&mut` burada başlar
                    // :
                    // :
    // Bazı kodlar  // :
    // Bazı kodlar  // :
}                   // &mut değişken borçlanma burada sona erer.
````

```Rust
fn main() {
    let mut a = vec![1,2,3];
    let b = &mut a;         // 'a' nın değişken borçlanması &mut burada başlar
    // Bazı kodlar          // :
    
    println!("{:?}", a);    // paylaşılan borç olarak verilen 'a'ya erişmeye
                            // çalışmak derleyicinin hata vermesine neden olur.
}                           // &mut değişken borçlanma burada sona erer.
````

```Rust
fn main() {
    let mut a = vec![1,2,3];
    
    {
        let b = &mut a;     // 'a' nın değişken borçlanması &mut burada başlar
        // Bazı kodlar      // :
    }                       // &mut değişken borçlanma burada sona erer.
    
    println!("{:?}", a);    // burada 'a' nın paylaşılan borçlanmasına izin verilir
}
````

Yukarıdaki bahsi geçen **paylaşılan ve değişken borçlanmaların** nasıl kullanılacağını aşağıdaki örneklerde inceleyelim.

### Paylaşılan Borçlanma Örnekleri
Örnek 1:
```Rust
fn main() {
    let a: [u8; 3] = [1,2,3];
    let b = &a;
    
    println!("a: {:?}, b[0]: {}", a, b[0]);
}
// a: [1, 2, 3], b[0]: 1
````
Örnek 2:
```Rust
fn ilk_ogeyi_getir(a: &Vec<u8>) -> u8 {
    a[0]
}

fn main() {
    let a: Vec<u8> = vec![1,2,3];
    let b = ilk_ogeyi_getir(&a);
    
    println!("a: {:?}, b: {:?}", a, b);
}
// a: [1, 2, 3], b: 1
````

### Değişken Borçlanma Örnekleri
Örnek 1:
```Rust
fn main() {
    let mut a = [1, 2, 3];
    let b = &mut a;
    b[0] = 4;
    println!("{:?}", b);
}
// [4, 2, 3]
````

Örnek 2:
```Rust
fn main() {
    let mut a = [1, 2, 3];
    {
        let b = &mut a;
        b[0] = 4;
    }
    
    println!("a:{:?}", a);
}
// [4, 2, 3]
````

Örnek 3:
```Rust
fn ilk_ogeyi_degistir_ve_getir(a: &mut Vec<i32>) -> i32 {
    a[0] = 4;
    a[0]
}

fn main() {
    let mut a = vec![1, 2, 3];
    let b = ilk_ogeyi_degistir_ve_getir(&mut a);
    
    println!("a:{:?}, b:{:?}", a, b);
}
// a:[4, 2, 3], b:4
````

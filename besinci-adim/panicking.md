## Panik
## panic!()
  - Bazı durumlarda, **hiç gerçekleşmemesi gereken** ya da başka bir ifadeyle **kurtarılamayacak kadar büyük** bir hata oluştuğunda onu işlemek için hiçbir şey yapamayız.
  - Zengin özelliklere sahip bir hata ayıklayıcı yahut uygun günlükler kullanılmadığında, mevcut akışı anlamak maksadıyla belirli bir mesajı veya değişken değerini yazdırmak gerekebilir. İşte kodun ayıklanması gerektiği böyle durumlarda `panic!` makrosu kullanılır.
  
⭐ `panik!()` makrosu işlik tabanlı çalıştığından bir işlik çalışırken diğer işlikler panik üretebilirler.

### 01. Belirli bir satırdan çıkış
```Rust
fn main() {
    // Bazı kodlar
    
    // burada hata ayıklamamız gerekiyorsa
    panic!()
}
// ---------- Derleme zamanı hatası --------
// thread 'main' panicked at 'explicit panic', src/main.rs:6:5
````

### 02. Özel bir hata mesajı ile çıkış
```Rust
#[allow(unused_mut)]
// 💡  Uyarıları bastırmak için kullanılan bir öznitelik. 
// `kullanici_adi`nı tutan bağlamının değişir olması gerekmez 

fn main() {
    let mut kullanici_adi = String::new();
    
    // Bazı yararlı kodlar
    
    if kullanici_adi.is_empty() {
        panic!("Kullanıcı adı girilmemiş!");
    }
    
    println!("{}", kullanici_adi);
}
// ---------- Derleme zamanı hatası --------
// thread 'main' panicked at 'Kullanıcı adı girilmemiş!', src/main.rs:11:9
````
### 03. Kod öğesine ait değerler ile çıkış
```Rust
#[derive(Debug)]
// 💡  Renk yapısına `std::fmt:: Debug` özelliği 
// uygulamak için kullanılan bir öznitelik

struct Renk {
    r: u8,
    g: u8,
    b: u8,
}
#[allow(unreachable_code)]
// Erişilemeyen kod uyarısını bastırmak için kullanılan öznitelik

fn main() {
    let mavi = Renk {r: 255, g: 255, b: 0};     
    
    // Bazı yararlı kodlar
    
    panic!("{:?}", mavi);
    
    println!("Mavi:({:?}, {:?}, {:?})", mavi.r, mavi.g, mavi.b);
}
// ---------- Derleme zamanı hatası --------
// thread 'main' panicked at 'Renk { r: 255, g: 255, b: 0 }', src/main.rs:18:5
````

Yukarıdaki örneklerde görebileceğiniz gibi `panic!()` makrosu, [`println!()` türü biçimlendirme argümanları](https://github.com/rust-lang-tr/dokuman/blob/master/rust-programlama-diline-giris/ilk-adim/merhaba.md#println-kullan%C4%B1m%C4%B1)nı desteklerken, **hata mesajını; dosya yolu, hatanın meydana geldiği satır ve sütun numaralarını** da varsayılan olarak yazdırır.

## `unimplemented!()` makrosu
💡 Programınızdaki tamamlanmamış kod bölümlerinin **uygulanmamış** olarak işaretlenebilmesi amacıyla kullanılan standart bir makrodur. Program çalışırken `unimplemented!()` makrosu ile işaretlenmiş bir bölüme rastlanırsa **"not yet implemented"** yani "**henüz uygulanmadı"** hata mesajı ile panik üretilirek program akışı sonlandırılır.

Karşılaştırabilmeniz için aşağıda hem `panic!()` hem `unimplemented!()` kaynaklı mesajları içeren örnekler yer almaktadır::

```Rust
// `panic!()` kaynaklı hata mesajı örnekleri
thread 'main' panicked at 'explicit panic', src/main.rs:6:5
thread 'main' panicked at 'KUllanıcı adı tanımlanmamış!', src/main.rs:9:9
thread 'main' panicked at 'Renk { r: 255, g: 255, b: 0 }', src/main.rs:17:5

// `unimplemented!() kaynaklı hata mesajı örnekleri
thread 'main' panicked at 'henüz uygulanmadı', src/main.rs:6:5
thread 'main' panicked at 'henüz uygulanmadı: Kullanıcı adı tanımlanmamış!', src/main.rs:9:9
thread 'main' panicked at 'henüz uygulanmadı: Renk { r: 255, g: 255, b: 0 }', src/main.rs:17:5
````

## `unreachable!()` makrosu
Bu makro ise, bir programın gitmemesi gereken noktaları işaretleyen standart makrodur. Eğer program bu aşamalara gelmişse; **"dahili hata: erişilemeyen kod girildi"** şeklinde bir hata mesajı görüntülenecektir.

```Rust
fn main() {
    let seviye = 22;
    
    let durum = match seviye {
        1..=5   => "Çırak",
        6..=10  => "Kalfa",
        11..=20 => "Usta",
        _       => unreachable!(),
    };
    
    println!("{}", durum);
}
// ---------- Derleme zamanı hatası --------
// thread 'main' panicked at 'internal error: entered unreachable code', src/main.rs:8:20
````

`unreachable`!()` makrosu için özel hata mesajları ayarlayabiliriz. Bunun için makro parantezleri içine döndürülecek mesajı ayzmamız yeterli olur:

```Rust
fn main() {
    let seviye = 22;
    
    let durum = match seviye {
        1..=5   => "Çırak",
        6..=10  => "Kalfa",
        11..=20 => "Usta",
        _       => unreachable!("Çok özel bir hata mesajı"), // yazdırılacak mesaj
    };
    
    println!("{}", durum);
}
// ---------- Derleme zamanı hatası --------
// thread 'main' panicked at 'internal error: entered 
// unreachable code: Çok özel bir hata mesajı', src/main.rs:8:20
````
Veya daha detaylı bir açıklama bildirmek isteyeviliriz:

```Rust
fn main() {
    let seviye = 22;
    
    let durum = match seviye {
        1..=5   => "Çırak",
        6..=10  => "Kalfa",
        11..=20 => "Usta",
        _       => unreachable!("Bu seviye: {} için karşılık belirlenmedi",
                                seviye), // yazdırılacak mesaj
    };
    
    println!("{}", durum);
}
// ---------- Derleme zamanı hatası --------
// thread 'main' panicked at 'internal error: entered unreachable 
// code: Bu seviye: 22 için karşılık belirlenmedi', src/main.rs:8:20
````
## `assert!()`, `assert_eq!()`, `assert_ne!()` makroları
Bunlar genellikle test önerileriyle kullanılan standart makrolardır.
  - `assert!()` bir boolean ifadesinin doğru olacağını garanti eder. İfade yanlışsa panik üretilir:

```Rust
fn main() {
    let f:bool = false;
    
    assert!(f)
}
// ---------- Derleme zamanı hatası --------
// thread 'main' panicked at 'assertion failed: f', src/main.rs:4:5
````

  - `assert_eq!()` son eki *equal* anlamındadır. Kendisine verilen aynı türden iki ifadenin eşit olacağını garanti eder. İfadeler eşit değilse panik üretilir.

```Rust
fn main() {
    let a: u8 = 10;
    let b: u8 = 20;
    
    assert_eq!(a, b);
}
// ---------- Derleme zamanı hatası --------
// thread 'main' panicked at 'assertion failed: `(left == right)`
````

  - `assert_ne!()` son eki *not equal* anlamındadır. Kendisine verilen iki ifadenin eşit olmadığını garanti eder. İfadeler eşitse panik üretilir. 

```Rust
fn main() {
    let a: u64 = 10;
    let b: u64 = 10;
    
    assert_ne!(a, b);
}
// ---------- Derleme zamanı hatası --------
// thread 'main' panicked at 'assertion failed: `(left != right)`
````

> ⭐ Hem `assert_eq! ()` hem de `assert_ne! ()` ile kullanılan ifadeler aynı veri türünü döndürmelidir.

Tıpkı diğer makrolarda olduğu gibi bu makrolar için de özel hata mesajları ayarlayabiliriz. İlk örneğimizde `assert_eq!()` için özel bir mesaj ayarlayalım:

```Rust
fn main() {
    let a: u64 = 10;
    let b: u64 = 20;
    
    assert_eq!(a, b, "a ve b birbirine eşit olmalıdır!");
}
// ---------- Derleme zamanı hatası --------
// thread 'main' panicked at 'assertion failed: `(left == right)`
// left: `10`,
// right: `20`: a ve b birbirine eşit olmalıdır!', src/main.rs:5:5
````

Diğer örneğimiz ise `assert_eq!()` makrosunun hata ayıklama verileriyle kullanımını gösterir:

```Rust
fn main() {
    let a: u16 = 10;
    let b: u16 = 20;
    
    let c: u16 = 40; 
    
    assert_eq!(a+b, c, "a:{} + b: {}", a, b);
}
// ---------- Derleme zamanı hatası --------
// thread 'main' panicked at 'assertion failed: `(left == right)`
// left: `30`,
// right: `40`: a:10 + b: 20', src/main.rs:7:5
````

## debug_assert!(), debug_assert_eq!(), debug_assert_ne!() makroları
🔎 Yukarıda örneklerini verdiğimiz `assert` makrolarına benzerler. Ancak bu ifadeler varsayılan olarak yalnızca optimize edilmemiş derlemelerde etkinleştirilir. Derleyiciye `-C debug-assertions` ifadesini iletmediğimiz sürece, bu `debug_assert` makrolarının hepsi  sürüm derlemelerinde atlanır.

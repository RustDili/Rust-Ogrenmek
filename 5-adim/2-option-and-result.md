## `Option` ve `Result`
## Neden Option ve Result?
Pek çok dil boş çıktıları temsil edebilmek için **nul/nil/undefined** türlerini ve hataları işlemek için **istisnaları** (Exceptions) kullanır. Rust, özellikle **null işaretçi istisnaları**, gibi istisnalar yoluyla oluşan hassas veri sızıntıları gibi sorunları önlemek için her ikisini de kullanmaz. Bunun yerine Rust, yukarıdaki durumlarla başa çıkmak için `Option` ve `Result` adlarıyla tanımlanmış iki adet genellenmiş `enum` türü sağlar.

> 💭 Önceki bölümlerde, temel olarak [`enum`](https://github.com/rust-lang-tr/dokuman/blob/master/rust-programlama-diline-giris/ikinci-adim/enum.md), [`genellemeler`](https://github.com/rust-lang-tr/dokuman/blob/master/rust-programlama-diline-giris/ikinci-adim/jenerikler.md), [`Option` ve `Result`](https://github.com/rust-lang-tr/dokuman/blob/master/rust-programlama-diline-giris/ikinci-adim/jenerikler.md#genellenmi%C5%9F-enum) türlerine değimniştik.

Hatırlayacağınız gibi: 

  - Bir opsiyonel değer ya içinde bazı değerler barındırır **Some**, ya da hiç bir değere sahip değildir **None**.
  - Bir `result` ise ya başarı bir durumu **Ok**  ya da bir başarısızlığı **Err** temsil edebilir.
 
```Rust
// Bir çıktı, ya bazı değerlere sahip olacak `Some`
// ya da hiçbir değere sahip olmayıp boş `None`olacaktır.
enum Option<T> {
    // T genellenmiş olduğundan herhangi türden olabilir.
    Some(T),
    None,
}

// Bir değerlendirme ise ya başarılı `Ok`
// ya da başarısız `Err` sonucunu temsil edebilir.
enum Result<T, E> {
    // T ve E genellenmiş olduğundan `T` herhangi türden bir değer
    // içerebilirken `E` ise herhangi türden bir hata olabilir.
    Ok(T),
    Err(E),
}
````

💭 Ayrıca [önyükleme kütüphaneleri](https://github.com/rust-lang-tr/dokuman/blob/master/rust-programlama-diline-giris/dorduncu-adim/std-primitives-and-preludes.md#%C3%B6n-y%C3%BCkleme-k%C3%BCt%C3%BCphaneleri) başlığında tartıştığımız gibi, sadece `Option` ve `Result` türleri değil, aynı zamanda onlara ait varyantlar da bu kütüphanelerde bulunur. O nedenle bu türleri programın isim alanlarında bildirmeye gerek kalmadan kullanabiliriz. 

## `Option` türünün temel kullanımları
Bir işlev yahut veri türü yazarken, 

  - Eğer işlevin bir argümanı isteğe bağlıysa,
  - Eğer bir işlevin döndürdüğü değer boş olabiliyorsa,
  - Eğer veri türündeki bir özelliğin değeri boşsa,

Veri türlerini `Option` türü olarak kullanmamız gerekir. 

Örneğin bir işlevin çıktısı hem `&str` hem de boş bir değer döndürebiliyorsa işlevin dönüş değeri `Option<&str>` olarak ayarlanmalıdır:

```Rust
fn secimlik_bir_deger_ver() -> Option<&str> {
    
    // Seçimlik değer boş değilse
    return Some("İşte sana bir değer");
    // Diğer hallerde
    None
}
````

Benzer biçimde bir veri türünün özellik değeri boş veya aşağıdaki örnekte olduğu haliyle `lakap` şeklinde, bildirilmesi zorunlu olmayan, seçimlik bir değer içeriyorsa, o özelliğin `option` olarak ayarlanması gereklidir.

```Rust
struct Kisi {
    onadi:  String,
    lakabi: Option<String>, // Secimlik 
    sonadi: String,
}
````

💭 Hatırlayacağınız gibi ilgili dönüş türünü `(SOme/None)` eşleşme yoluyla yakalamak için örüntü eşleme'den yararlanabiliriz. Örneğin geçerli kullanıcının giriş dizinini öğrenebilmek için `std::env` kütüphanesinde [`home_dir()`](https://doc.rust-lang.org/std/env/fn.home_dir.html) adında artık kullanımdan kalkmış bir metod bulunmaktadır. Haliyle başka sistem kullanıcıları, Linux işletim sisteminde olduğu gibi bir giriş dizinine sahip olamayabileceğinden bu işlev artık [`Option<PathBuf>`](https://doc.rust-lang.org/std/path/struct.PathBuf.html) değeri döndürmektedir.

```Rust
use std::env;

fn main() {
    let dizinim = env::home_dir();
    
    match dizinim {
        Some(yol) => println!("{:?}", yol),
        None      => println!("Başlangıç dizini bulunamadı"),
    }
}
// "/playground"
````

⭐ Ancak, seçimlik argümanları işlevlerle kullanırken, işlevi çağrılarında boş argümanlar için `None` değerlerini de iletmemiz gerekir.

```Rust
// lakap değeri iletilmemiş olabilir
fn tam_adi_getir(onad: &str, sonad: &str, lakap: Option<&str>) -> String {
    match lakap {
        Some(takma) => format!("{} {} {}", takma, onad, sonad),
        None        => format!("{} {}", onad, sonad),
    }
}

fn main() {
    println!("{}", tam_adi_getir("Mehmet", "Ali", None));
    println!("{}", tam_adi_getir("Sinem", "Kırlı", Some("Kuklacı:")));
}
// Mehmet Ali
// Kuklacı: Sinem Kırlı
````
💡 Yukarıdaki örneğin daha iyi bir sürümünde ise, onad, sonad ve lakap alanlarına sahip `Kisi` adında bir yapı oluşturularak tam adı döndüren bir özellik tasarlanabilirdi.

🔎 Bunların haricinde `Option` türleri Rust' taki **nullable işaretçilerle** de kullanılır. Fakat Rust’ ta **null işaretçiler olmadığından** işaretçi türleri geçerli bir konuma işaret etmelidir. O nedenle eğer bir işaretçinin nullable **boş olma olasılığı** bulunuyorsa `Option<Box<T>>` şeklinde bir ifade kullanılır.

## `Result` türünün temel kullanımları
Bir işlev hata üretebilecek durumdaysa, **geçerli çıkışın veri türü ve hatanın veri türünü kapsayan** bir `Result` türü kullanmamız gerekir. Örneğin geçerli çıktının veri türü `u64` ve üretilebilecek hata türünün `String` olduğu bir işlevin dönüş türü `Result<u64, String>` şeklinde olmak zorundadır.

```Rust
fn hatali_bir_islev() -> Result<u64, String> {
    
    //Eğer hata oluşacaksa
    return Err("Hata mesajı".to_string());
    
    // Diğer halde geçerli bir çıktı döndürülür
    Ok(255)
}
````

💭 Bildiğiniz gibi, ilgili dönüş türlerini `(Ok/Err)` eşleştirme yoluyla yakalayabilmek için örüntü eşlemeyi kullanabiliriz. Bu eşleştirmede kullanabileceğimiz ortam değişkenlerini elde etmemizi sağlayan `var()` metodunu `std::env` kütüphanesi üzerinden çağırabiliriz. Bu işlevin esprisi kendisine hatalı bir ortam değişkeni iletilir veya programın çalışması sırasında ortam değişken değerleri çıkarsanamıyorsa hataya üretmesidir. Bu nedenle bu metod `Result<String, varError>` şeklinde bir Result türü döndürür.

```Rust
use std::env;

fn main() {
    let anahtar = "HOME";
    
    match env::var(anahtar) {
        Ok(deger)   => println!("{}", deger), // Playgroud çıktısı "/playground"
        Err(e)      => println!("{}", e),
        // Var olmayan bir ortam değişkeni iletildiyse veya ortam değişkenlerine
        // ulaşılamıyorsa "environment variable not found", hatası döndürülür 
    }
}
````

## `is_some()`, `is_none()`, `is_ok()`, `is_err()` metodları
Eşleme yani `match` ifadeleri dışında Rust, dönüş türülerini tanımlaya yarayan `is_some()`, `is_none()`, `is_ok()` ve `is_err()` gibi metodlara da sahiptir.

```Rust
fn main() {
    let dizge: Option<&str> = Some("Merhaba dünya!");
    assert_eq!(dizge.is_some(), true);
    assert_eq!(dizge.is_none(), false);

    let sonuc: Result<i8, &str> = Ok(10);
    assert_eq!(sonuc.is_ok(), true);
    assert_eq!(sonuc.is_err(), false);
}
````
## Result türleri için `ok()` ve `err()` metodları
Ek olarak Rust `Result` türleri için kullanılmaya uygun `ok()` ve `err()` metodlarını da sunar. Bu metodlar `Result` türlerinin `Ok<T>` ve `Err<E>` çıktılarını genellenmiş bir `Option` türüne dönüştürürler.

```rust
fn main() {
    let o: Result<i8, &str> = Ok(8);
    let e: Result<i8, &str> = Err("Hata mesajımız!");
    
    assert_eq!(o.ok(), Some(8)); // Ok(v) ok = Some(v)
    assert_eq!(e.ok(), None);    // Err(v) ok = None
    
    assert_eq!(o.err(), None);            // Ok(v) err = None
    assert_eq!(e.err(), Some("Hata mesajımız!")); // Err(v) err = Some(v)
}
```

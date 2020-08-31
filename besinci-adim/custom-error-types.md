## Özel hata türleri
Rust, kullanıcıların kendi türlerini oluşturmasına izin verir. Oluşturulan bu türler ise "Özel Hata Türleri" olarak tanımlanır.

## `Error` özelliği
Bilindiği gibi Rust'ta özellikler, **bir türün sağlaması gereken işlevler**i tanımlarlar. Ancak Rust'ın **standart kütüphanesi** kendi türlerimizle de kullanılabilecek yeniden kullanılmaya uygun bazı özellikleri varsayılan olarak sunduğundan ortak işlevler için çoğunlukla yeni özellikler tanımlamamız gerekmez. Özel hata türleri oluştururken faydalanacağımız [`std::error::Error trait`](https://doc.rust-lang.org/std/error/trait.Error.html) kütüphanesi herhangi bir `Err` türünü başka bir türe dönüştürmemize yardım eder.

```Rust
use std::fmt::{Debug, Display};

fn main() {
    pub trait Error: Debug + Display {
        fn kaynak(&self) -> Option<&(Error + 'static)> { ... }
    }
}
````

> [Özellikler ve kalıtım](https://github.com/rust-lang-tr/dokuman/blob/master/rust-programlama-diline-giris/ikinci-adim/impl-and-traits.md#%C3%B6zellikler-ve-kal%C4%B1t%C4%B1m) bölümünde tartıştığımız gibi, bir özellik başka özelliklerden miras yoluyla edinilebilir. `Trait Error: Debug + Display` ifadesi, bu özelliğin `fmt::Debug` ve `fmt::Display` özelliklerinden devralınacağı anlamını taşır.

```Rust
// Rust standart kütüphanesi core fmt modülü/ std::fmt içindeki özellikler
pub trait Display {
    fn fmt(&self, f: &mut Formatter) -> Result<(), Error>;
}

pub trait Debug {
    fn fmt(&self, f: &mut Formatter) -> Result<(), Error>;
}
````

- `Display`
  - Bu hata mesajı son kullanıcılara/ nasıl kullanıcı odaklı bir çıktı olarak gösterilebilir?
  - Genellikle `println!("{}")` veya `eprintln!("{}")` yardımıyla yazdırılarak.

- `Debug`
  - hata ayıklama sürecinde `Err` mesajları nasıl kullanıcı odaklı birer çıktı olarak gösterilebilir?
  - Genellikle `println!("{:?}")` veya `eprintln!("{:?}")` yardımıyla yazdırılarak.
  - Ya da temiz çıktı `println!("{:#?}")` veya `eprintln!("{:#?}")` yardımıyla yazdırılarak.

- `source()`
  - Bu hatanın eğer varsa alt düzey kaynağı.
  - Optional.

Öncelikle, oldukça basit bir özel hata türüne `std::error::Error` özelliğinin nasıl uygulanacağını inceleyelim:

```Rust
use std::fmt;
// Özel hata türü; mevcut sandıkta tanımlanan herhangi bir tip olabilir
// 💡 Burada, örneği basitleştirmek için basit bir "birim yapı" kullanıyoruz

struct Hataci;

// AppError için std::fmt::Display özelliğini uygulama
impl fmt::Display for Hataci {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
    // Kullanıcı odaklı çıktı
        write!(f, "Bir hata oluştu. Lütfen tekrar deneyin!") 
    }
}

// AppError için std::fmt::Debug özelliğini uygulama
impl fmt::Debug for Hataci {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
    // Kullanıcı odaklı çıktı
        write!(f, "{{ file: {}, line: {} }}", file!(), line!())
    }
}

// `Err` türünde bir AppError hatası üretmek için örnek işlev
fn hata_uret() -> Result<(), Hataci> {
    Err(Hataci)
}

fn main() {
    match hata_uret() {
        Err(e)  => eprintln!("{}", e), // Bir hata oluştu. Lütfen tekrar deneyin!
        _ => println!("Hatasız işlem"),
    }
    
    eprintln!("{:?}", hata_uret());
}

// ---------- Derleme zamanı hatası --------
// Bir hata oluştu. Lütfen tekrar deneyin!
// Err({ file: src/main.rs, line: 19 })
````

Konuyu ana hatlarıyla kavradığınızı umuyorum. Şimdi **bir hata kodu ve bir hata mesajı** içeren bazı özel hata türlerini inceleyelim.

```Rust
use std::fmt;

struct Hataci {
    kod: usize,
    ileti: String,
}

// Hataci.kod'a uygun farklı hata mesajları
impl fmt::Display for Hataci {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        let hata_iletisi = match self.kod {
            404 => "Üznünüm. Sayfa bulunamadı!",
            _ => "üzgünüm, hatalı işlem! Lütfen tekrar deneyin!",
        };

        write!(f, "{}", hata_iletisi)
    }
}

// Çıktı hata ayıklaması için benzersiz bir biçim
impl fmt::Debug for Hataci {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(
            f,
            "Hataci {{ Hata kodu: {}, ileti: {} }}",
            self.kod, self.ileti
        )
    }
}

fn hata_uret() -> Result<(), Hataci> {
    Err(Hataci {
        kod: 404,
        ileti: String::from("Sayfa bulunamadı"),
    })
}

fn main() {
    match hata_uret() {
        Err(e) => eprintln!("{}", e), // Üzgünüm, sayfa bulunamadı
        _ => println!("Hata oluşmadı"),
    }

    eprintln!("{:?}", hata_uret()); // Err(AppError { Kod: 404, ileti: Sayfa bulunamadı })

    eprintln!("{:#?}", hata_uret());
}
// Üznünüm. Sayfa bulunamadı!
// Err(Hataci { Hata kodu: 404, ileti: Sayfa bulunamadı })
// Err(
//    Hataci { Hata kodu: 404, ileti: Sayfa bulunamadı },
// )
````

⭐️ Rust standart kitaplığı yalnızca yeniden kullanılabilir özellikler sağlamakla kalmaz, aynı zamanda `#[derive]` özelliği aracılığıyla **birkaç özellik için sihirli bir şekilde** uygulamalar oluşturmayı kolaylaştırır. Yine ayrıca hata ayıklama iletileri için varsayılan bir biçim sağlamak üzere `std::fmt::Debug` kütüphanesi üzerinden `derive` desteği sağlar. Bu nedenle, özel hata türleri için **`std::fmt::Debug` uygulamasını atlayabilir** ve `struct` bildiriminden önce `#[derive(Debug)]' özniteliğinden yararlanabiliriz.

> `#[derive(Debug)]` özniteliği bir yapının adı ve { arasındaki, virgülle ayrılmış her alan adı ve değerini } ayıklayarak yazdırır.

```Rust
use std::fmt;

#[derive(Debug)] // Hatacı üzerinde std::fmt::Debug özelliği türetmek
struct Hataci {
    kod: usize,
    ileti: String,
}

impl fmt::Display for Hataci {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        let hata_iletisi = match self.kod {
            404 => "Üzgünüm, Sayfa bulunamadı!",
            _ => "Hatalı işlem! Lütfen tekrar deneyin!",
        };

        write!(f, "{}", hata_iletisi)
    }
}

fn hata_uret() -> Result<(), Hataci> {
    Err(Hataci {
        kod: 404,
        ileti: String::from("Sayfa bulunamadı"),
    })
}

fn main() {
    match hata_uret() {
        Err(e) => eprintln!("{}", e), // Üzgünüm, Sayfa bulunamadı!
        _ => println!("Hatasız"),
    }
    
    // Err(Hataci { kod: 404, ileti: "Sayfa bulunamadı" })
    eprintln!("{:?}", hata_uret());

    eprintln!("{:#?}", hata_uret());
}
// Üzgünüm, Sayfa bulunamadı!
// Err(Hataci { kod: 404, ileti: "Sayfa bulunamadı" })
// Err(
//    Hataci {
//        kod: 404,
//        ileti: "Sayfa bulunamadı",
//    },
//)
````

## From özelliği
Gerçek bir program tasarlarken çoğunlukla farklı modüllere, farklı `std` ve üçüncü parti sandıklarıyla aynı anda uğraşmak zorunda kalabiliyoruz. Bununla birlikte her sandık kendi hata türlerini kullandığından, kendi hata türlerimizi oluşturmaya kalktığımızda bu hataları kendi hata türlerimize dönüştürmemiz gerekir. Bu dönüşümler için `std::convert::From` adındaki standart dönüştürme özelliğini kullanabiliriz.

```Rust
// Bu özellikk Rust standart kütüphanesinde
// convert module/ std::convert yolunda bulunur

pub trait From<T>: Sized {
  fn from(_: T) -> Self;
}
````

> 💡 Bildiğiniz gibi, `String::from()` metodu `&str` veri türünden `String` elde etmek için kullanılır. Aslında bu da `std::convert::From` özelliğinin bir uygulamasıdır.

Haydi şimdi özel bir hata türüne `std::convert::From` özelliğinin nasıl uygulanacağını inceleyelim.

```Rust
use std::fs::File;
use std::io;

#[derive(Debug)]
struct Hataci {
    tur: String,    // type of the error
    ileti: String, // error message
}

// Hataci için io::Error 'den türetilen std::convert::From uygulaması  
impl From<io::Error> for Hataci {
    fn from(error: io::Error) -> Self {
        Hataci {
            tur: String::from("io"),
            ileti: error.to_string(),
        }
    }
}

fn main() -> Result<(), Hataci> {
// Bu bir `io::Error` oluşturur. Ancak dönüş türü 
// `Result<(), Hataci>` olduğundan, Hataci biçimine dönüşür

    let _file = File::open("varolmayan_belge.txt")?; 

    Ok(())
}


// --------------- Çalışma zamanı hatası ---------------
// Error: Hataci { tur: "io", ileti: "No such file or directory (os error 2)" }
````

Yukarıdaki örnekte `File::open(“nonexistent.txt”)?` ifadesi bir `std::io::Error` üretir. Ancak bu, dönüş türü `Result<(), Hataci>` olduğundan, bir `Hataci`'ya dönüşür. Bunun nedeni hatanın `main()` işlevinden yayılması nedeniyle `Err` ifadesinin `debug` gösterimini yazdırılmasıdır.

Yukarıdaki örnekte yalnızca bir tane `std` hata türü, `std::io::Error` ile ilgilenmeştik. Haydi şimdi birden çok `std` hata türünü işleyen bir örneği inceleyelim:

```Rust
use std::fs::File;
use std::io::{self, Read};
use std::num;

#[derive(Debug)]
struct Hataci {
    tur: String,
    ileti: String,
}

// Hataci için `io::Error` den turetilen 
// `std::convert::From` uygulaması 
impl From<io::Error> for Hataci {
    fn from(error: io::Error) -> Self {
        Hataci {
            tur: String::from("io"),
            ileti: error.to_string(),
        }
    }
}

// Hataci için from num::ParseIntError'den 
// türetilen std::convert::From uygulamasi
impl From<num::ParseIntError> for Hataci {
    fn from(error: num::ParseIntError) -> Self {
        Hataci {
            tur: String::from("Ayıkla"),
            ileti: error.to_string(),
        }
    }
}

fn main() -> Result<(), Hataci> {
// eğer dosya açılmazsa Hatacı'ya dönüştürülen
// bir io::Error oluşturulur, 
    let mut file = File::open("merhaba_dunya.txt")?; 

//eğer dosya okunamazsa Hataciya dönüştürülen 
// bir io::Error oluşturulur, 
    let mut content = String::new();
    file.read_to_string(&mut content)?;

// Eğer dosya içeriği usize türüne dönüştürülemiyorsa
// Hataci'ya dönüştürülen bir num::ParseIntError oluşturulur
    let _number: usize;
    _number = content.parse()?; 

    Ok(())
}
// Error: Hataci { tur: "io", ileti: "No such file or directory (os error 2)" }
````

> 🔎 Daha fazla hata türü ve nasıl uygulanacağına dair ayrıntılı bilgiler için [`std::io::ErrorKind`](https://doc.rust-lang.org/std/io/enum.ErrorKind.html) kütüphanesind ebulunan **Hata türleri**ni inceleyebilirsiniz.

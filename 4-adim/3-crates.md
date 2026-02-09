## Sandıklar
💭 Dilimize **sandık** olarak çevirebileceğimiz derlenebilen bu program parçaları diğer dillerdeki paket kavramıyla benzeşirler. Eğer bir sandık alt dosya modüllerine sahipse bu dosyalar sandık dosyasıyla birleştirilerek tek parça olarak derlenirler.

💭 Bir sandık çalıştırılabilir ikili dosya olarak veya bir kütüphane olarak üretilebilir. Sandığın çalıştırılabilir versiyonunun kök dizini, yani giriş noktası: `src/main.rs` iken, kütüphane hali için: `src/lib.rs` şeklinde dizinlenir. 

## 01. Çalıştırılabilir sandık olarak *lib.rs*
💡 Çalıştırılabilir ikili sandıklar yazılırken temel işlevleri `src/lib.rs` dosyasına taşıyabilir, taşınan bu temel işlevleri de programın giriş noktası olan `src/main.rs` üzerinden bir kütüphane olarak kullanabiliriz. Bu yaklaşım çalıştırılabilir sandıklar için oldukça yaygındır.

```Rust
// # terminal yardımıyla çalıştığınız dizinde bu komutları uyguladığımız düşünelim 
cargo new selamla
touch selamla/src/lib.rs

// # Oluşturulan klasörün içeriği şu şekilde olacaktır. 
selamla
 ├── Cargo.toml
 └── src
    ├── lib.rs
    └── main.rs

// # Aşağıdaki dosyaları şu şekilde değiştirdiğimizi düşünelim

// 01. selamla/src/lib.rs
pub fn merhaba() {
    println!("Merhaba mars!");
}

// 02. selamla/src/main.rs
extern crate selamla;

fn main() {
    selamla::merhaba();
}
// Merhaba mars!
````

> 💯 Daha önce de belirttiim gibi karmaşıklığı azaltabilmek için burada verdiğim örnekleri olabildiğince basit tutmaya çalışıyorum yararlanıyoruz. Ancak kodun test edilebilir olabilmesi için `selamla/src/lib.rs` dosyasındaki kodları şu şekilde yazmamamız gerekir:

```Rust
// selamla/src/lib.rs
pub fn merhaba() -> String {
  //! Burası `Merhaba dünya!` dizgesini döndürür 
  ("Merhaba dünya!").to_string()
}

// 01. `merhaba()` işlevi için test 
#[test]               // İşlevin bir test işlevi olduğunu belirten öznitelik
fn test_merhaba() {
  assert_eq!(merhaba(), "Merhaba dünya!");
}

// 02. `merhaba()`işlev testi için Idiomatic yol 
#[cfg(test)]          // Sadece test aşamasında derlenir
mod tests {           // Her bir test birimi için ayrı ayrı 
  use super::merhaba; // `merhaba()` işlevini kök olarak ithal eder 
  
    #[test]
    fn test_merhaba() {
        assert_eq!(merhaba(), "Merhaba dünya!");
    }
}
```

> [📖](https://doc.rust-lang.org/book/ch07-00-managing-growing-projects-with-packages-crates-and-modules.html) İsimlerinde "bunun-gibi" kısa çizgiyi ayraç olarak kullanan bir sandık ithal edildiğinde, bu ayraç geçerli bir Rust dili tanımlayıcısı olmadığından, ithal edilen sandık adındaki kısa çizgiler, "bunun_gibi" alt çizgi ile yer değiştirecektir.

`lib.rs` dosyası birden fazla dosyayla bağlantı kurabilir:

```rust
// # terminal yardımıyla çalıştığımız dizinde bu komutları uyguladığımızı düşünelim 
cargo new soylem
touch soylem/src/lib.rs
touch soylem/src/selamla.rs

// # Oluşturulan klasör yapısı şuna benzeyecektir 
soylem
 ├── Cargo.toml
 └── src
    ├── selamla.rs
    ├── lib.rs
    └── main.rs
   
// # Aşağıdaki dosyaları şu şekilde değiştirdiğimizi düşünelim

// 01. soylem/src/selamla.rs
pub fn merhaba() {
    println!("Merhaba dünya!");
}

// 02. soylem/src/main.rs
extern crate soylem;

fn main() {
    soylem::selamla::merhaba();
}

// 03. soylem/src/lib.rs
pub mod greetings; // ⭐️ `selamla` modülü genele açık bir modül olarak ithal edilir
```

## 02. Cargo.toml dosyasındaki sandık bağımlılığı
Kütüphane dosyamız olan `lib.rs` içindeki kodlar büyüdükçe, bunları ayrı kütüphane sandıklarına taşıyabilir ve bu dosyayı ana sandık bağımlılığı olarak kullanabiliriz. Daha önce de bahsetmiş olduğum gibi bağımlılık klasör yollarını, **git deposu** veya **crates.io** üzerinden belirtilebilirsiniz. 
### a. Dosya yolu kullanmak  
İç içe geçmiş bir sandık oluşturup dosya yolunu kullanarak bağımlılıkları nasıl kullanacağımızı inceleyelim:

```rust
// # terminal yardımıyla çalıştığımız dizinde bu komutları uyguladığımızı düşünelim
cargo new konustur 
cargo new konustur/selamla --lib 

// # Oluşturulan klasörün içeriği 
konustur 
├── Cargo.toml 
├── selamla 
│ ├── Cargo.toml 
│ └── src 
│ 	└── lib.rs 
└── src 
└── main.rs 

// # Aşağıdaki dosyaları şu şekilde değiştirdiğimizi düşünelim, 

// 01. konustur/Cargo.toml 
[package] 
name = "konustur" 
version = "0.1.0" 
authors = ["Mert Ataol"] 
edition = "2019"

[dependencies] 
selamla = { path = "selamla" } 

// 02. konustur/selamla/src/lib.rs 
pub fn merhaba() { 
println!("Merhaba dünya!"); 
} 

// 03. konustur/src/main.rs 
extern crate selamla; 

fn main() { 
selamla::merhaba(); 
}
```
### b. Git deposu kullanmak
Bir kütüphane sandığını farklı projelerde defalarca kullanmak istiyorsanız bunun bir yolu da sandık kodunu git deposuna taşımak ve gerektiğinde bağımlılık olarak kullanmaktır.

```Rust
// -- Cargo.toml -- 
[dependencies] 

// 01. Ana daldaki en son sürümü almak 
rocket = { git = "https://github.com/SergioBenitez/Rocket" } 

// 02. Belirtilen daldaki en son sürümü almak 
rocket = { git = "https://github.com/SergioBenitez/Rocket", branch = "v0.3" } 

// 03. Belirtilen bir etikete sahip olanı almak 
rocket = { git = "https://github.com/SergioBenitez/Rocket", tag = "v0.3.2" } 

// 04. Kullandığınız dalla uyumlu belirli bir düzeltmeyi almak
rocket = { git = "https://github.com/SergioBenitez/Rocket", rev = "8183f636305cef4adaa9525506c33cbea72d1745" }
````

### c. Crates.io kullanmak
Başka bir yol da kodunuzu crates.io'ya yüklemek ve gerektiğinde bağımlılık olarak onu kullanmaktır. 

🚧 öncelikle basit bir "Merhaba dünya" **sandığı oluşturalım** ve onu [crates.io](http://crates.io/)'ya yükleyelim.

```Rust
// # Terminalden çalıştıralım, 
cargo new test_sandigi_merhaba_dunya --lib

// # Aşağıdaki gibi bir dizin yapısı oluşacaktır.
test_sandigi_merhaba_dunya
 ├── Cargo.toml
 └── src
    └── lib.rs
   
// # Aşağıdaki dosyaları kendimize göre düzenleyebiliriz

// 01. test_sandigi_merhaba_dunya/Cargo.toml
[package]
name = "test_sandigi_merhaba_dunya"
version = "0.1.0"
authors = ["Sizin adınız soyadınız"]

description = "Basit bir Merhaba dünya sandığı"
repository = "https://github.com/hesabiniz/test_sandigi_merhaba_dunya"
keywords = ["Merhaba", "dunya"]
license = "Apache-2.0"

[dependencies]

// 02. test_sandigi_merhaba_dunya/src/lib.rs
//! Basit bir Merhaba dünya sandığı

/// Bu işlev `Merhaba dünya!` diyerek sizi selamlar 
pub fn merhaba() -> String {
    ("Merhaba dünya!").to_string()
}

#[cfg(test)]
mod tests {

    use super::merhaba;
    
    #[test]
    fn test_merhaba() {
        assert_eq!(merhaba(), "Merhaba dünya!");
    }
}
// Cargo test -> test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
````

> 💭 **//! Belge yorumları** sandık ve modül düzeyinde belgeler hazırlamak için kullanılır. Bloğun dışındaki yerlerdey ise **///** kullanmak gereklidir. Bir sandık [crates.io](http://crates.io/)' ya yüklenirken cargo bu belge yorum işaretlerini kullanarak programı belgeleyerek [docs.rs](https://docs.rs/) aracılığıyla sistemde saklar.

💡 `Cargo.toml` dosyasına **açıklama** ve **lisans** alanları eklenmek zorundadır. Bu alanlar ihmal edildiğinde yükleme işlemi `error: api errors: missing or empty metadata fields: description, license. Please see http://doc.crates.io/manifest.html` şeklinde bir hata ile sonlandırılır.

Creates.io üzerine sandık yükleyebilmek için:
1. Öncelikle gerekli olan API ve buna bağlı anahtar için bir hesap oluşturulmalı
2. Ardından `cargo login <token>` komutu ile giriş yapılarak yayın yapılmalıdır.

[📖](http://doc.crates.io/crates-io.html) Daha fazla ayrıntıya [Cargo Docs](http://doc.crates.io/crates-io.html) üzerinden erişebilirsiniz.

> - Bir API anahtarı edinebilmek için crates.io'da bir hesaba ihtiyacınız olacak. Bunun için [crates.io ana sayfasını] (https://crates.io/) dilerseniz github hesabınız ile ziyaret ederek giriş yapabilirsiniz. Giriş yaptıktan sonra, size ait [Hesap Ayarları](https://crates.io/me) sayfanızı ziyaret ederek, yayınlamak istediğiniz API için gerekli anahtarı alabilirsiniz. Bu işlemlerin ardından `cargo login`  komutu ve almış olduğunuz API anahtarı ile bağlanabilirsiniz. Örn: `cargo login abcdefghijklmnopqrstuvwxyz012345`

 - Bundan sonraki adımınız sandığınızı *crates.io*'ya yüklenebilecek bir şekilde paketlemektir. Bunun için de `cargo package` alt komutunu kullanacağız. Eğer bu aşamada hata alıyorsanız `cargo package --allow-dirty` komutunu kullanabilirsiniz.
  - Artık sandığımızı `cargo publish` komutu ile crates.io sistemine yükleyip yayınlayabiliriz.
  - Eğer istiyorsanız `cargo package` adımını atlyabilirsiniz. Çünkü `cargo publish` alt komutu sandığınızın paketlenmiş bir kopyasını bulamadığı takdirde otomatik olarak paketleyip yayınlayacaktır.
  
Artık yükleyip yayınlamış olduğumuz `test_sandigi_merhaba_dunya` adlı sandığımız arandığında:

 - Paket olarak: 📦 [https://**crates.io/crates**/test_sandigi_merhaba_dunya](https://crates.io/crates/test_sandigi_merhaba_dunya)
 - Belge olarak: 📑 [https://**docs.rs**/test_sandigi_merhaba_dunya](https://docs.rs/test_sandigi_merhaba_dunya)

alanlarında rahatlıkla bulunabilir.

> 💯 creates.io *read.me* dosyalarını da destekliyor ancak bu desteği sağlayabilmek için Cargo.toml dosyasında `readme="README.md"` alanını etkinleştirmek zorundayız.

🏗️ Haydi şimdi de, bunu sandığı oluşturduğumuz yeni bir paket ile **nasıl kullanabileceğimizi** inceleyelim.

```rust
// # Terminalden yeni bir selamla projesi başlatalım, 
cargo new selamla

// # Aşağıdakine benzer bir dizin yapısı oluşacaktır.
selamla
 ├── Cargo.toml
 └── src
    └── main.rs

// # Dosyaların aşağıdaki gibi olduğunu varsayalım

// 01. selamla/Cargo.toml
[package]
name = "selamla"
version = "0.1.0"
authors = ["Sizin Adınız Soyadınız"]

[dependencies]
test_sandigi_merhaba_dunya = "0.1.0"

// 02. selamla/src/main.rs
extern crate test_sandigi_merhaba_dunya;

fn main() {
    println!("{}", test_sandigi_merhaba_dunya::selam());
}
```

Cargo, varsayılan olarak [crates.io](https://crates.io/)'ya bağımlılık gösterir. Bu nedenle, **`Cargo.toml`**'a yalnızca sandık adını ve sürüm numarasının eklenmesi gerekir. Programın bağımlılıklarını çekerek çalıştırılabilmesi için önce bu verilerin sağlanması ondan sonra  `cargo build` komutunun çalıştırılması gereklidir.

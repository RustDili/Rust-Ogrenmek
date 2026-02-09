# Paket yönetimi, sandıklar ve proje yapısı
### Cargo
Rust dilinin yerleşik [paket yöneticisi ve yapılandırma sistemi cargo](https://doc.rust-lang.org/cargo/)'dur ve rustup aracılığıyla yüklenen bu aracın temel kullanımında aşağıdaki komutlar oldukça işlevseldir.

- Yeni bir proje oluşturmak için: `cargo new`
- Projeyi derlemek için: `cargo build`
- Projeyi çalıştırmak için: `cargo run`
- Proje bağımlılıklarını güncellemek için: `cargo update`
- Projeyi test etmek için: `cargo test`
- Proje belgelerini [rustdoc](https://doc.rust-lang.org/stable/rustdoc/) aracılığıyla oluşturmak için: `cargo doc`
- Proje derlenmeden önce oluşabilecek hataları gözlemleyebilmek için: `cargo check`

Ayrıca, Rust'un resmi sandıklarının yayınlandığı [crates.io](https://crates.io/) alanında projelerin sandık/paket olarak yayınlanması için kullanılan Cargo komutları da bulunmaktadır.

>💡 Bir sandığın [crates.io](https://crates.io/) üzerinde yayınlanabilmesi için bir API jetonu alınması gereklidir. Jeton, siteye kaydolunduktan sonra [Hesap Ayarları](https://crates.io/me) sekmesinde görünecektir. İlerleyen bölümlerde [Kod Organizasyonu](dorduncu-adim/kod-organizasyonu.md) başlığı altında bu konu hakkında daha fazla tartışma olanağı bulacağız. 

 * API jetonuyla crates.io'ya giriş: `cargo login`
 * Yerel sandığı [crates.io](https://crates.io/)'ya yüklenebilir hale getirmek için: `cargo package`
 * Sandığı [crates.io](https://crates.io/)'ya yüklemek için: `cargo publish`

komutlarını kullanabilirisniz.

### Crate
Rust programlama dilinde [crates.io](https://crates.io) aracılığıyla paylaşılan her paket `Crate` adını alır. Crate' nin Türkçe karşılığı "tahtadan yapılmış kasa" anlamına geldiğinden, dilimizde "sandık" olarak karşılık bulacaktır. Bir sandık ya çalıştırılabilir dosya ya da kütüphane üretebilir. Başka bir deyişle, bir sandık binary biçiminde veya bir kütüphane şeklinde yayımlanabilir.

Yeni bir Rust projesi başlatmak için `cargo new proje_adı --bin` komutu kullanılır. `--bin` parametresi isteğe bağlı olarak kullanıldığından her iki durumda da oluşturulan proje çalıştırılabilir bir dosya olacaktır. Ancak projenin `--lib` parametresi ile başlatılması projenin bir kütüphane olarak oluşturulması için yeterlidir. Aksi belirtilmediği sürece yeni bir proje başlatıldığında, proje adıyla çalıştırılabilir dosya oluşturulur. Bu davranış `rustc --crate-type` veya `cargo --bin` parametresi kullanılarak değiştirilebilmektedir. 

`cargo new proje_adi --bin` komutlarıyla başlatılan çalıştırılabilir paket yapılandırmasının proje dizini aşağıdaki gibi görünecektir.

```Rust
├── Cargo.toml
└── src
    └── main.rs
````

`cargo new proje_adi --lib` komutuyla oluşturulan kütüphane paketinin dizin yapısı ise aşağıdakine benzeyecektir.

```Rust
├── Cargo.toml
└── src
    └── lib.rs
````
* **Cargo.toml** (Büyük C harfiyle başlar), Cargo'nun projenizi derlemesi için gereken tüm meta verileri içeren yapılandırma dosyasıdır.
* **src** klasörü kaynak kodun saklandığı dizindir.
* Her sandığın üstü kapalı bir sandık kökü yani giriş noktası vardır. Çalıştırılabilir ikili sandıklar için bu giriş noktası **main.rs** olurken kütüphane sandıkları için bu sandık kökü **lib.rs**'dir.

💡 Bir ikili sandığı `cargo build` veya `cargo run` komutlarıyla oluşturduğunuzda çalıştırılabilir dosya ` target/debug/` klasöründe saklanır. Ancak sandığı `cargo build --release` komutuyla oluşturusanız saklanacak dizin yolu `target/release/` şeklinde olacaktır.  

💡 Oluşturduğunuz sandıkları, cargo.toml dosyasında yer alan bağımlılıklarıyla birlikte, Rust ekosisteminde üretilen sandıkların bir arada tutulduğu [crates.io](https://crates.io) adlı Rust resmi sitesi üzerinde dilediğiniz an diğer kullanıcılar ile paylaşabilirsiniz.

### Proje yapısı
Bir projenin dizin yapısı ise genel olarak aşağıdaki şekle benzeyecektir. Paket düzeni hakkında [Cargo dökümanları](https://doc.rust-lang.org/cargo/guide/project-layout.html)nda daha fazla açıklama bulabilirsiniz
```Rust.
├── Cargo.lock
├── Cargo.toml
├── benches
│   └── large-input.rs
├── examples
│   └── simple.rs
├── src
│   ├── bin
│   │   └── another_executable.rs
│   ├── lib.rs
│   └── main.rs
└── tests
    └── some-integration-tests.rs
````
Oluşturulan her projenin
- Kaynak kodları `src` dizininde tutulur.
- Varsayılan kütüphane klasörü `src/lib.rs`' dir.
- Varsayılan çalıştırılabilir dosyası `src/main.rs`' dir.
- Diğer çalıştırılabilir dosyalar `src/bin/*rs` şeklinde konumlandırılır.
- Birim testler gibi entegrasyon testleri, test dizininde bulundurulur. 
- Proje içindeki örnekler ise `examples` dizinine konur.
- Karşılaştırmalar `benches` adlı dizininde yer alır.

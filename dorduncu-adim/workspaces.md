## Çalışma alanları
Yazdığınız programlar büyümeye başladıkça, aynı proje üzerinde **birden fazla sandık ile çalışmanız** gerekebilir. Rust bu beklentiyi **Çalışma alanları** ile destekler. 
Bu alanlar yardımıyla proje kökünden `cargo check` gibi kargo komutları çalıştırarak projenizi; **değerlendirebilir, derleyebilir, deneme testlerine sokabilir** veya tüm sandıklar için **belgelendirebilirsiniz**.

⭐️ Aynı anda birden fazla sandık üzerinde çalışılırken, sandıklarda paylaşılan çok sayıda ortak bağımlılık olabilir. Rust bu türden ortak bağımlılıkları defalarca indirerek yapılan bir derlemeyi önlemek amacıyla, proje kökünde `cargo build` komutu çalıştırıldığında **paylaşılan bir derleme dizini** kullanır.

Şimdi kullanıcısını "Merhaba dünya!" sözcüğüyle selamlayacak basit bir merhaba() işlevine sahip kütüphane sandığı ve bu kütüphaneyi kullanan çalıştırılabilir ikili sandık oluşturalım.

Terminalimizde aşağıdaki komutları çalıştıralım.
```bash
mkdir selamla
touch selamla/Cargo.toml
cargo new selamla/lib --lib
cargo new selamla/ornekler/merhaba
```
Dizin yapınız aşağıdaki gibi oluşacaktır:

```bash
selamla
 ├── Cargo.toml
 ├── ornekler
 │  └── merhaba
 │     ├── Cargo.toml
 │     └── src
 │        └── main.rs
 └── lib
    ├── Cargo.toml
    └── src
       └── lib.rs
````

Aşağıdaki dosyaları düzenlendiği şekilde değiştirmeliyiz,

```rust
// 01. selamla/Cargo.toml dosyasını çalışma alanı olarak işaretlemek ve üye eklemek için
[workspace]
members = [
    "lib",
    "ornekler/merhaba"
]

// 02.1 selamla/lib/Cargo.toml paket adını `selamla` olarak değiştirmek için
[package]
name = "selamla"
version = "0.1.0"
authors = ["Sizin ad ve soyadınız"]

[dependencies]

// 02.2 selamla/lib/src/lib.rs dosyasına basit bir merhaba dünya işlevi eklemek için
pub fn merhaba() {
    println!("Merhaba dünya!");
}

// 03.1 selamla/ornekler/merhaba/Cargo.toml dosyasına 'selamla' lib dosyasını bağımlılık olarak eklemek için
[package]
name = "merhaba"
version = "0.1.0"
authors = ["Sizin ad ve soyadınız"]

[dependencies]
selamla = { path = "../../lib" }

// 03.2 selamla/ornekler/merhaba/src/main.rs dosyasında 'selamla' lib dosyasını
// ithal ederek merhaba dünya yazdıran merhaba() işlevini çağırmak için
extern crate selamla;

fn main() {
    selamla::merhaba();
}
````

💡 Linux ve Mac'te çalışıyorsanız Subshells (Parantezler arasına yerleştirilen komut listesi) kullanarak tüm sandıklarda çalışma dizinini değiştirmeden `cargo` komutlarını çalıştırabilirsiniz. Örneğin, `selamla` dizininde bulunuyorsanız, `(cd examples/hello && cargo run)` şeklinde bir komut çalıştırsanız bile çalışma dizininiz hala `selamla` olarak kalacakmaya devam edecektir.

> 🔎 Çalışma alanları ile ilgili yararlı örneklere [rust-lang/rust src dizini](https://github.com/rust-lang/rust/tree/master/src) üzerinden ulaşabilirsiniz.

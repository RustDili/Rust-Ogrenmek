## Akıllı derleyici
Rust programlama dilinin yükünü zekice tasarlanmış derleyicisi çeker. Bu bölümde Rust derleyicisine odaklanacağız.

## Neden derleyici?
Rust programlarındaki hataları önlemek söz konusu olduğunda en önemli işleri Rust’ın derleyicisi yapar. Öyle ki, çalıştırılmaya hazır bir programı derleme aşamasında analiz ederken, kodun bellek yönetim kurallarına veya yaşam süresi ek açıklamalarına doğru biçimde uyup uymadığını denetleyerek, uyumsuzlukla karşılaştığında en ilgili uyarıyı yayınlar. Örneğin:

```Rust
#[allow(unused_variables)]
💡  // kullanılmayan değişken tespitinde 
    // hata uyarılarını bastıran öznitelik

fn main() {
    let a: Vec<u16> = vec![1,2,3];
    let b = a;
    
    print!("{:?} ", a);
}

// ---------- Derleme zamanı hatası --------
error[E0382]: borrow of moved value: `a`
 --> src/main.rs:9:21
  |
6 |     let a: Vec<u16> = vec![1,2,3];
  |         - move occurs because `a` has type `std::vec::Vec<u16>`, which does not implement the `Copy` trait
7 |     let b = a;
  |             - value moved here
8 |     
9 |     print!("{:?} ", a);
  |                     ^ value borrowed here after move

error: aborting due to previous error
For more information about this error, try `rustc --explain E0382`
````
⭐ Yukarıdaki örnekte `#[allow(unused_variables)]` özniteliğini kullanarak `let b = a;` ifadesindeki hatayı bastırmıştık. Bunun yerine 
// `let _b = a;` şeklinde bir kullanım da mümkündür. Ayrıca döndürülecek tüm dönüş değerlerini yok saymak için `let _ =` şeklinde bir kullanım mümkündür.

> 💭 Önceki bölümlerde  [sahiplik ve mülkiyet](https://github.com/rust-lang-tr/dokuman/blob/master/rust-programlama-diline-giris/ucuncu-adim/ownership.md), [borçlanma](https://github.com/rust-lang-tr/dokuman/blob/master/rust-programlama-diline-giris/ucuncu-adim/borrowing.md), [yaşam süreleri](https://github.com/rust-lang-tr/dokuman/blob/master/rust-programlama-diline-giris/ucuncu-adim/lifetimes.md) gibi bellek yönetimi kavramlarını tartışmıştık.

Rust derleyicisi sadece yaşam süreleri ve bellek yönetimi ile ilgili sorunları değil aşağıdaki örnekte de görüldüğü gibi ortak kodlama hata ve yanlışlarını da kontrol eder:

```Rust
struct Renk {
    r: u8,
    g: u8,
    b:u8,
}

fn main() {
    let sari = Renk {r: 255, g: 255, d: 0,}; 
    // `b` yerine yanlışlıkla `d` girilmiş
    // hata açıklamas 8. satırdaki önerinin isabatine bakın:-)
    
    println!("Sarı: rgb: ({},{},{})", sari.r, sari.g, sari.b);
}
// ---------- Derleme zamanı hatası --------
error[E0560]: struct `Renk` has no field named `d`
 --> src/main.rs:8:38
  |
8 |     let sari = Renk {r: 255, g: 255, d: 0,};
  |                                      ^ help: a field with a similar name exists: `b`

error: aborting due to previous error
For more information about this error, try `rustc --explain E0560`.
````

## Hata kodlarının açıklamaları
Yukarıdaki hata mesajlarındaki açıklamalar, hata kaynağının kolayca tespit edilmesi amacıyla oldukça açıklayıcı bilgiler içerir. Ancak sorunun hata mesajlarında tanımlanamadığı durumlarda `rustc --explain` komutları problemle ilgili **hata türü** ve giderilmesine yardımcı olabilecek **çözüm önerilerini basit kod örnekleri**yle sunar.

Örneğin, `rustc --explain E0571` hata açıklaması konsolda aşağıdaki gibi gösterilir:

```rust
A `break` statement with an argument appeared in a non-`loop` loop.

Example of erroneous code:
｀｀｀
let result = while true {
    if satisfied(i) {
        break 2*i; // error: `break` with value from a `while` loop
    }
    i += 1;
};
｀｀｀

The `break` statement can take an argument (which will be the value of the loop
expression if the `break` statement is executed) in `loop` loops, but not
`for`, `while`, or `while let` loops.

Make sure `break value;` statements only occur in `loop` loops:
｀｀｀
let result = loop { // ok!
    if satisfied(i) {
        break 2*i;
    }
    i += 1;
};
｀｀｀
````

💡 Aynı açıklamaları [Rust Derleyici Hata Dizini](https://doc.rust-lang.org/error-index.html) üzerinden de okuyabilirsiniz. Örneğin `E0571` hata açıklamasının içeriğini görmek için [Hata dizinindeki ilgili yönergeyi](https://doc.rust-lang.org/error-index.html#E0571) kullanabilirsiniz.

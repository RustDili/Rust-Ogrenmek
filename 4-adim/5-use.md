## `Use` anahtar sözcüğü
Şimdi `use` anahtar sözcüğünün temel kullanımlarını görelim.

## 01. Öğe erişiminin tam yolunu belirtmek
Temelde `use` anahtar sözcüğü, bir öğenin tam yolunu kullanabilmek için öğeye takma isim vermek amacıyla kullanılır. Böylelikle kullanıcı öğeye her erişimde elemanın tam yolunu belirtmek zorunda kalmamış olur. İlk örneğimiz `use` anahtar sözcüğünü kullanmayan öğe erişimini gösterir:

```Rust
// `use` anahtar sözcüğü kullanılmadan
mod soylem { 
  pub mod selamla { 
    pub fn merhaba() { 
      println!("Merhaba dünya!");
    }
  }
}

fn main() { 
  soylem::selamla::merhaba(); // Öğye erişimde tam yol kullanımı
}
````

Aynı örneği `use` anahtar sözcüğü kullanarak tekrarladığımızda ithal edilen modül için takma ad oluşturmuş oluruz:

```Rust
// Modül için takma ad oluşturma
use soylem::selamla;
fn main() { 
  selamla::merhaba();
}
````

Aynı şekilde `use` anahtar sözcüğünü kullanarak modüle ait öğeleri de takma isim belirterek kullanıma sokabiliriz.

```Rust
// Modül öğeleri için takma ad oluşturma
use soylem::selamla::merhaba;
fn main() { 
  merhaba();
}
````

Yapılan isimlendirmeleri `as` anahtar kelimesi yoluyla özelleştirebiliriz.

```Rust
// `as` anahtar kelimesiyle isimleri özelleştirme
use soylem::selamla::merhaba as selam;
fn main() { 
  selam();
}
```

## 02. Öğeleri kapsam içine almak
`Use` anahtar kelimesinin bir diğer yaygın kullanımı da, öğeleri kapsam içine almaktır. Bu biraz da tam yolu kullanmak için takma ad oluşturmaya benzer.

```rust
fn merhaba() -> String {
  "Merhaba dünya!".to_string()
}

#[cfg(test)]
mod tests {
  use super::merhaba; // `hello()` işlevini kampsam içine ithal eder
    
  #[test]
  fn test_merhaba() {
    // Yukarıda `use` kullanmak yerine `super::merhaba()` ifadesini de kullanabilirdik. 
    assert_eq!("Merhaba dünya!", merhaba()); 
  }
}
````

> 💡 Varsayılan olarak `use` bildirimleri sandık köklerinin mutlak yolunu kullanacak şekilde tasarlanmıştır. Fakat `self` veya `super` 	bildirimleri o mutlak yolu modüle göre yeniden ayarlar.

`Use` anahtar sözcüğü [Rust standart kütüphanesi](https://github.com/rust-lang/rust/tree/master/src/libstd) olan `std` dahil olmak üzere diğer sandık bileşenlerini içe aktarmak amacıyla da kullanılır. 

### 01. Öğeleri ithal ederken
```Rust
use std::fs::File;

fn main() {
    File::create("bos.txt").expect("Dosya oluşturulamadı!");
}
````

### 02. Modül ve öğeleri ithal ederken
```Rust
std::fs::{self, File} // `use std::fs; use std::fs::File;` bildiriminin daha pratiği

fn main() {
    fs::create_dir("bir_dizin").expect("Dizin oluşturulamadı!");
    File::create("bir_dizin/cos.txt").expect("Dosya oluşturulamadı!");
}
````

### 03. Çok sayıda öğeyi ithal ederken

```Rust
use std::fs::File;
use std::io::{BufReader, BufRead}; // `use std::io::BufReader; use std::io::BufRead;` 
                                   // bildiriminin daha pratiği

fn main() {
    let file = File::open("src/merhaba.txt").expect("dosya bulunamadı");
    let buf_reader = BufReader::new(file);

    for line in buf_reader.lines() {
        println!("{}", line.unwrap());
    }
}
````

> Dilin standart kütüphanesi olan `std`yi kullanırken `extern crate std;` ifadesinin kullanılması gerekli değildir. Bu konu hakkında daha fazla bilgiyi [Standart Kütüphane](std-primitives-and-preludes.md) bölümünde işleyeceğiz.

💡 `use` anahtar kelimesi bildirimleri, bir modül veya sandığın tüm öğelerini içe aktarmak yerine, yalnızca kapsamda belirttiğimiz öğe veya içeriğini içe aktarır. Bu kullanım sayesinde programın verimliliği de artmış olur.

## 03. Yeniden ihraç
Bir başka özel durum ise `pub use` bildirimidir. Yeni bir modül oluştururken bu modüle, başka bir modülden öğeler aktarabilirsiniz. Bu yapıldığında artık bu öğelere direkt olarak kendi modülünüz üzerinden erişebilirsiniz. Bu işleme **Yeniden ihraç** adı verilir:

```rust
// ↳ main.rs
mod soylem;

fn main() {
    soylem::merhaba(); // Direkt eşleşme yok
}

// ↳ soylem/mod.rs
pub mod selamla;

pub use self::selamla::merhaba; // `selamla::merhaba` 'yı soylem için yeniden ihraç etmek

// ↳ soylem/selamla.rs
pub fn merhaba() {
  println!("Merhaba dünya!");
}
```

Bu şablon büyük kütüphanelerde oldukça yaygın kullanılır. Bu tarz bir kullanım, kütüphanenin iç yapısındaki karmaşıklığı kullanıcılardan gizlemeye yardımcı olur. Böylelikle bu kütüphaneler kullanılırken tüm dizin haritasının bilinmesi gerekmez.

## Modüller
## 01. Tek bir dosya
İlişkili kodlar ve veriler bir tek modülde gruplanır ve aynı dosya içinde saklanır:

```Rust
mod selamla {
    // ⭐️ Varsayılan olarak, bir modül içindeki her şey özeldir
    // Bu yüzden işlevin herkesin dışarıdan erişimine açık olması gerekir
    pub fn merhaba() {
        println!("Merhaba dünya!");
    }
}

fn main() {
    selamla::merhaba();
}
// Merhaba dünya!
````

Modüller iç içe geçmiş olarak bildirilebilirler:

```Rust
mod soylemler {
    pub mod selamlar {
        pub fn merhaba () {
            println!("Merhaba dünya!");
        }
    }
}

fn main() {
    soylemler::selamlar::merhaba();
}
// Merhaba dünya!
````

Özel işlevler aynı modül veya bir alt modülden çağırılabilirler. Aşağıdaki örnek aynı modülün özel işlevini çağırmaktadır:

```Rust
mod soylemler {
    pub fn selam() {
        merhaba(); // ya da `self::merhaba();`
    }
    fn merhaba() {
        println!("Merhaba dünya!");
    }
}

fn main() {
    soylemler::selam();
}

// Merhaba dünya!
````

Aşağıdaki örnekte ise üst modülün özel işlevi çağrılmaktadır:

```Rust
mod soylemler {
    fn ozel_bir_islev() {
        println!("Merhaba dünya!");
    }
    
    pub mod selamla {
        pub fn merhaba() {
            // açıklamasına aşağıdan ulaşabilirsiniz 
            super::ozel_bir_islev();
        }
    }
}

fn main() {
    soylemler::selamla::merhaba();
}
// Merhaba dünya!
````

💡 `self` anahtar sözcüğü aynı modülü belirtmek için kullanılırken, `super` anahtar sözcüğü üst modülü belirtmek için kullanılır. Ayrıca, `super` anahtar sözcüğü bir modülün içinden kök işlevlerine erişmek için kullanılabilir.

```Rust
mod selamlama {
    pub fn merhaba() {
        super::merhaba();
    }
}

fn merhaba() {
    println!("Merhaba dünya");
}

fn main() {
    selamlama::merhaba();
}
// Merhaba dünya!
````

🔎 Test kodlarını yazarken bütün test kodlarını ayrı bir test modülünün içine almanız, bu kodlar yalnızca test aşamasında derleneceklerinden iyi bir yaklaşım olur.

```Rust
// Test kodlarında dönüş türü kullanılmalıdır
// yani bir prosedür deüil bir işlev yazılmalıdır 
fn selam() -> String {
    "Merhaba dünya!".to_string()
}

#[cfg(test)] // sadece test aşamasında derlenecektir
mod tests {
    use super::selam; // Kök selam işlevini içe aktarma
    
    #[test]
    fn test_selam() {
        assert_eq!("Merhaba dünya!", selam());
    }
}

/*
running 1 test
test tests::test_selam ... ok
test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
running 0 tests
test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
*/
````

## 02. Aynı dizin, ancak farklı bir dosya
// ↳ main.rs 
mod selamla; // selamla modulünün içe aktarılması 

fn main() { 
    selamla::merhaba(); 
} 

// ↳ selamla.rs 
// ⭐ Dosyanın kendisi bir modül olarak davranacağından
// kodu mod bildirimi ile sarmak gereksizdir. 

pub fn merhaba() { 
    // Bu işleve dışarıdan erişilebilmesi için `pub` anahtar 
    // kelimesi kullanılarak erişime açılması gerekmektedir. 
    println!("Merhaba dünya!"); 
}
// Merhaba dünya!

Eğer dosya içeriği `mod` bildirimi ile sarmalanırsa, bu dosya iç içe geçmiş modüller olarak kabul edilir:

```Rust
// ↳ main.rs
mod soylemler;

fn main() {
    soylemler::selamla::merhaba();
}

// ↳ soylemler.rs
pub mod selamla {
    // Bu işleve dışarıdan erişilebilmesi için `pub` anahtar 
    // kelimesi kullanılarak erişime açılması gerekmektedir.
  pub fn merhaba() {
    println!("Merhaba dünya!");
  }
}
// Merhaba dünya!
````

## 03. Farklı dizin, farklı dosya
Dizin modülünün kök dizininde bulunan `mod.rs`, dizin modülüne giriş noktasıdır. Bu dizin kökünde bulunan diğer dosyaların tümü dizin modülünün alt modülleri olarak kabul edilir:

```Rust
// ↳ main.rs
mod selamla;

fn main() {
    selamla::merhaba();
}

// ↳ selamla/mod.rs

// Bu işleve dışarıdan erişilebilmesi için `pub` anahtar 
// kelimesi kullanılarak erişime açılması gerekmektedir.
pub fn merhaba() {
    println!("Merhaba dünya!");
}
// Merhaba dünya!
````

Yine burada da eğer dosya içeriği `mod` bildirimi ile sarmalanırsa, iç içe geçmiş modül olarak davranmaya başlar:

```Rust
// ↳ main.rs
mod soylemler;

fn main() {
    soylemler::selamla::merhaba();
}

// ↳ soylemler/mod.rs
pub mod selamla {
    pub fn merhaba() {
        println!("Merhaba dünya!");
  }
}
// Merhaba dünya!
````

Dizin modülündeki diğer dosyalar `mod.rs` için alt modül görevi üstlenir:

```Rust
// ↳ main.rs
mod soylemler;

fn main() {
  soylemler::merhaba()
}

// ↳ soylemler/mod.rs
mod selamla;

pub fn merhaba() {
  selamla::merhaba()
}

// ↳ soylemler/selamla.rs
pub fn merhaba() {
  println!("Merhaba dünya!");
}
// Merhaba dünya!
````

⭐️ Modülün dışından bir "soylemler/selamla.rs" öğesine erişmeniz gerekiyorsa, `selamla` modülünü `pub` anahtar sözcüğünü kullanarak  genel erişime açık modül olarak içe aktarmanız gerekir.

```Rust
// ↳ main.rs
mod soylemler;

fn main() {
    soylemle::selamla::merhaba();
}

// ↳ soylemler/mod.rs
pub mod selamla;  // ⭐️  `mod` yerine`pub mod`

// ↳ soylemler/selamla.rs
pub fn merhaba() {
    println!("Merhaba dünya!");
}
// Merhaba dünya!
````

> 🔎 Dizin modülü aracılığıyla alt dosya modülleri `main.rs` içine aktarılamayacağından `main.rs` içerisinden `mod soylemler::selamla`  gibi ifadeler kullanılamaz. Bununla birlikte gerek duyulduğunda `soylemler` dizini içindeki `selamla` dosyasında bulunan `merhaba()` işlevini, `soylemler::selamla::merhaba()` söz dizimiyle ithal edebiliriz. Bu şekilde yapılan bir ithal işleminden sonra işlev, `soylemler::merhaba()` şeklinde doğrudan kullanıma hazır olacaktır.

```Rust
// ↳ soylemler/selamla.rs
pub fn merhaba() {
    println!("Merhaba dünya!");
}

// ↳ soylemler/mod.rs
pub mod selamla;

// `self::selamla::merhaba` ifadesiyle 
// soylemler içine yeniden ithal edilir
pub use self::selamla::merhaba;

// ↳ main.rs
mod soylemler;

fn main() {
    // Artık `merhaba()` işlevi soylemler 
    // içinden direk olarak çağırılabilir.
    soylemler::merhaba();
}
// Merhaba dünya!
````

Bu yönelim program kod düzenlememizle **doğrudan uyuşmayabilecek** bir dış arabirim oluşturmamızı sağlar. Haliyle bu tür yönelimler kod karmaşasına sebep olabilir. Böyle karışıklıkların üstesinden gelebilmek için `use` anahtar sözcüğü kullanılmaktadır.

### Merhaba Dünya!
Diğer dillerde olduğu gibi Rust dilinde de bir programın kodlanmasına `main()` işlevinden başlanır. Bu  işlevin içindeki ifadeler programın derlenmiş hali çağrıldığı anda yürütülür.

```Rust
fn main() { 
  println!("Merhaba Dünya!"); 
}
````
Örneğimizdeki `fn`'in anlamı ingilizcedeki function kelimesinin kısaltması olan işlevi temsil eder. "Merhaba Dünya!" Mesajını ekrana yazdırmak için kullanılan `println!` ise bir makrodur. Makrolar *!* şeklinde gösterilerek işlevlerden ayrılır.

💡 Rust dosyalarının uzantısı daima `.rs` olmalıdır ve eğer dosya adı için birden fazla kelime kullanıyorsanız **snake_case** yönelimini tercih edin.

* Yukarıdaki kodu uzantısı `.rs` olacak şekilde dilediğiniz bir isimle kaydedebilirsiniz. Ancak şimdilik burada örnekleyebilmek amacıyla *merhaba.rs* olarak değerlendirilecektir. 
* Dosyayı `rustc merhaba.rs` komutuyla derleyin.
* Programınızı Linux veya Mac kullanıyırsanız `./merhaba` Windows kullanıyorsanız `merhaba.exe` komutuyla çalıştırın.

### Rust Playground
Rust kodlarınızı internet üzerinde [çevrim içi](https://play.rust-lang.org/) çalıştırabileceğiniz bir alandır.

![Playground](https://github.com/RustDili/Rust-Ogrenmek/blob/master/resimler/Rust-Playground.png)

### Println! Kullanımı
💯Bir makro olarak kullanılan `println!` için farklı kullanım örneklerine göz atalım.

```Rust
fn main() { 
  println!("{}, {}!", "Merhaba", "Dünya");                        // Merhaba, Dünya! 

  println!("{0}, {1}!", "Merhaba", "Plüton");                     // Merhaba, Plüton! 

  println!("{selamla}, {isim}!", selamla="Selam", isim="Erdem");  // Selam, Erdem!

  println!("{:?}", [1,2,3]);                                      // [1, 2, 3] 

  println!("{:#?}", [1,2,3]);                                     /* [ 1, 2, 3 ] */ // 
  
  // 🔎 format! makrosu biçimlendirilmiş dizgeyi saklamak için kullanılır.
  let x = format!("{}, {}!", "Merhaba", "Mars");                  
  println!("{}", x);                                              // Merhaba, Mars! 
}
````

### macro_rules!
Makrolar, isimlerinin bir ünlem `!` ile bitmesi dışında işlevlere benzerler. Ancak makrolar bir işlev çağrısı oluşturmak yerine, programın geri kalanıyla derlenen kaynak koduna genişletilir. C ve diğer dillerdeki makrolardan farklı olarak, Rust makroları dizge ön işleme yerine soyut söz dizimi ağaçlarına genişletildiğinden beklenmedik hatalar oluşmaz.

Rust, meta-programlama yapabileceğimiz güçlü bir makro sistemi sağlar. Bir makro tanımlamak için `macro_rules!` Deyimini kullanmanız yeterlidir.
Aşağıdaki örnekte `merhaba_de` adında basit bir makro tanımlanmaktadır.

```Rust
macro_rules! merhaba_de {
   // ‘()’ Boş parantez makronun argüman almadığını gösterir.
  () => {
    // Makro bu bloğun içinde genişler.
    println!(“Merhaba!”)
  };
}

fn main() {
  // Alttaki çağrı println!(“Merhaba!”); ifadesini çalıştırır. 
  merhaba_de!();
}
````
**macro'ların kullanılması:**

- Kod tekrarını azaltır,
- Belirli bir amaç için özel söz dizimi tanımlanmasına izin verir,
- Değişken arayüzlerin tasarlanmasına olanak sağlar.

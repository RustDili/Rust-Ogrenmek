## Error ve None Yayılımı
Genellikle `panic!()`, `unwrap()` veya `expect()` gibi makro veya metodlar panik ile karşılaşıldığında sürecin daha iyi yönetilemeyeceği hallerde kullanılır. Ancak, eğer bir işlev `None` ya da `Err` üretebilen ifadeler içeriyorsa,

  - Bu değerleri aynı işlev içinde ele alabiliriz,
  - Ya da `None` ve `Err` dönüşlerini, çağıran tarafa iade ederek sürecin idaresini o tarafın inisiyatifine bırakabiliriz.
  
💡 İade edilen `None` türlerinin, çağıran tarafından her zaman işlenmesine gerek yoktur. Ancak Rust'ın `Err` türleri hata yakalama düzeneği gereği, nasıl değerlendirileceklerine karar verebilmek ve daha fazla kontrol sunabilmek maksadıyla hızlıca iade edilmelidirler.

## ? İşleci
  - Eğer bir `Option` türü **`Some`** değerine veya bir `Result` türü **`Ok`** değerine sahipse içlerindeki bu değerler bir sonraki adıma geçirilirler.
  - Eğer bir `Option` türü **`None`** değerine veya bir `Result` türü **`Err`** değerine sahipse bu değerler **en hızlı biçimde çağıran tarafa iade** edilirler.
  
### `Option` türü için örnekleyelim:

```Rust
fn karmasik_bir_islev() -> Option<&'static str> {
    let x = secimlik_bir_deger_getir()?; 
    // Eğer None ise, hızlıca iade et; 
    // Eğer Some("abc"), ise x’i "abc" değerine ayarla
    
    // Bazı kodlar
    println!("{}", x); // "abc"; 
    
    Some("")
}

fn secimlik_bir_deger_getir() -> Option<&'static str> {
    // Secimlik deger boş değilse
    if false {  //`false` değerini `true` yaparak deneyiniz. 
        return Some("abc");
    }
    // Diğer hallerde
    None
}

fn main() {
    if karmasik_bir_islev().is_none() {
        println!("İşlenebilecek bir X verisi yok!");
    }
}
// İşlenebilecek bir X verisi yok!
````

### `Result` türü için örnekleyelim:

```Rust
fn karmasik_bir_islev() -> Result<u64, String> {
    // `Err` elde edilmişse hızlıca iade et; 
    // Ok(255) ise x’i 255’e ayarla 
    let x = hatali_bir_islev()?;
    
    // Bazı kodlar
    println!("{}", x);
    
    Ok(0)
}

fn hatali_bir_islev() -> Result<u64, String> {
    // hata oluştuysa
    if true {   // "true" değerini "false" olarak değiştirip inceleyin
        return Err("Bir hata mesajı".to_string());
    }
    // Geçerli bir değer olduğu hallerde
    Ok(255)
}

fn main() {
    // karmasik_bir_islev() `main` içinden çağrılarak 
    // oluşan hataların main() içinden idaresi sağlanır
    if karmasik_bir_islev().is_err() {
        println!("X hesaplanamıyor!");
    }
}
// X hesaplanamıyor!
````

## `try!()` makrosu
⭐ Bu makro, Rust'ın 1.13 sürümüne **`?`** işleci gelene kadar kullanılan bir makro olduğundan yerini **`?`** işlecine bırakmıştır. O nedenle daha yeni sürümlerde bu makronun kullanılması tavsiye edilmez. 

  - Bir `Result` türünde `Ok` değeri bulunuyorsa bu değer sonraki adıma geçirilir, ancak `Err` değeri bulunuyorsa bu değer hızlıca çağıran tarafa iade edilir.
  
```Rust
fn main() {
    // `?` işleci kullanımı
    let x = hatali_bir_islev()?;
    
    // `try!()` makrosu kullanımı
    
    let y = try!(hatali_bir_islev());
}
````

## `main()` işlevinden hata yayılımı
`Result` ve `Option` türlerinin hata yayılımı, Rust'ın 1.26'dan önceki sürümlerinde main () işlevinden geçirilememekteydi. Rust'ın daha yeni sürümlerde `Result` türünün `Err` çıktısı, hata ayıklama *Debug* gösteriminde kullanılmak üzere main işlevine iletilebilmektedir. 

```Rust
use std::fs::File;

fn main() -> std::io::Result<()> {
    let _= File::open("bulunamayan-bir-dosya.txt")?;
    Ok(())  // Çünkü Rust işlevlerinin varsayılan dönüş 
            // değerleri nedeniyle boş bir tuple () 
}
// Çünkü program bulunamayan-bir-dosya.txt dosyasını bulamadığından, 
// Err(Os { code: 2, kind: NotFound, message: "No such file or directory" })
// hatasını üretecektir. Dolayısıyla hata aktarıldığında program: 
// Error: Os { code: 2, kind: NotFound, message: "No such file or directory" } 
// yazdıracaktır.
````

>💯 Eğer `std::fs::File::open()` metodunun üretebileceği tüm hataları öğrenmek istiyorsanız, `std::fs::OpenOptions` kütüphanesinde bulunan [hata listesini](https://doc.rust-lang.org/std/fs/struct.OpenOptions.html#errors) kontrol edebilirsiniz.

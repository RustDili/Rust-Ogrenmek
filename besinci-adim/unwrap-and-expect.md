## `Unwrap` ve `expect`

## `unwrap()` metodu
  - Bir `Option` türünün `Some` değeri varsa ya da bir `Result` türü `Ok` değeri içeriyorsa, içerdikleri bu değer bir sonraki adıma iletilir.
  - Bir `Option` türü None değerine ayarlanmışsa ya da bir `Result` türü `Err` değeri içeriyorsa panik üretilir. Eğer `Err` için bir hata mesajı tanımlanmışsa bu yazdırılır.
  
İşlevselliğiyse `match` yerine `unwrap()` metodu kullanan aşağıdaki kodlara benzer.

Öncelikle `unwrap()` metodu kullanmayan `Option` ve `match` örneğini inceleyelim:

```Rust
fn secimlik_bir_deger_ver() -> Option<&'static str> {
    //Seçimlik değer boş değilse
    if false {
        return Some("abc");
    }
    // diğer hallerde
    None
}

fn main() {
    let x;
    
    match secimlik_bir_deger_ver() {
        Some(deger) => x = deger,   // eğer Some("abc") ise, x’i "abc" olarak ayarla
        None        => panic!(),    // değer yoksa, hata mesajsız panik üret 
    }
    
    println!("Değer:{}", x);        
    // "abc" ; secimlik değerini `false` yerine `true` olarak değiştirin 
}
// ---------- Derleme zamanı hatası --------
// thread 'main' panicked at 'explicit panic', src/main.rs:15:24
````

Bir de `unwrap()` metodu kullanmayan `Result` ve `match` örneğini inceleyelim:

```Rust
fn secimlik_bir_deger_ver() -> Result<u64, String> {
    // Eğer hata oluşursa
    if true {
        return Err("Bir hata Mesajı".to_string());
    }
    // Oluşmazsa geçerli bir çıkış döndürelim
    Ok(255)
}

fn main() {
    let x;
    
    match secimlik_bir_deger_ver() {
        Ok(deger) => x = deger, // eğer Ok(255) ise x’i 255 olarak ayarla  
        Err(e)    => panic!(e), // Hata oluşmuşsa, panik mesajını yazdır 
    }
    
    println!("Değer:{}", x);        
    // 255 ; İşlevdeki `true` ifadesini `false` ile değiştirin
}
// ---------- Derleme zamanı hatası --------
// thread 'main' panicked at 'Bir hata Mesajı', src/main.rs:15:22
````
Yukarıdaki örneklerde bulunan `main()` işlevlerinde yer alan kodları `unwrap()` metodunundan yararlanarak iki satır ile yazabiliriz.

### 1. Öncelikle hatayı `Option` varyantı `None` için `unwrap()` metodu kullanarak yakalayalım
```Rust
fn secimlik_bir_deger_ver() -> Option<&'static str> {
    //Seçimlik değer boş değilse
    if false {
        return Some("abc");
    }
    // diğer hallerde
    None
}

fn main() {
    // Kodun nasıl kısaldığına dikkat edin!
    let x = secimlik_bir_deger_ver().unwrap();
    
    println!("Değer:{}", x);        
    // "abc" ; secimlik değerini `false` yerine `true` olarak değiştirin 
}
// ---------- Derleme zamanı hatası --------
// thread 'main' panicked at 'called `Option::unwrap()` 
// on a `None` value', src/main.rs:11:13
````

### 2. Aynı şekilde bu kez de `Result` varyantı `Err` için `unwrap()` metodunu kullanalım
```Rust
fn secimlik_bir_deger_ver() -> Result<u64, String> {
    // Eğer hata oluşursa
    if true {
        return Err("Bir hata Mesajı".to_string());
    }
    // Oluşmazsa geçerli bir çıkış döndürelim
    Ok(255)
}

fn main() {
    // Kodun nasıl kısaldığına dikkat edin!
    let x = secimlik_bir_deger_ver().unwrap();
    
    println!("Değer:{}", x);        
    // 255 ; İşlevdeki `true` ifadesini `false` ile değiştirin
}
// ---------- Derleme zamanı hatası --------
// thread 'main' panicked at 'called `Result::unwrap()` 
// on an `Err` value: "Bir hata Mesajı"', src/main.rs:13:13
````

⭐ Dikkat ederseniz, `unwrap()` metodunu kullandığınızda verilen hata mesajlarında paniğin gerçekleştiği satır numaraları tam olarak gösterilmemektedir.

## `expect()` metodu
Kelime anlamını **beklenti** olarak değerlendirebileceğimiz bu metod `expect()` işlevine benzemekle birlikte farkı, panik için ayarlanabilen özel bir iletiye sahip olmasıdır.

### 1. İlk olarak `Option` varyantı `None` için hata mesajı beklentimizi örnekleyelim

```Rust
fn main() {
    let b: Option<i8> = None;
    
    b.expect("Boş değer döndürüldü");
}
// ---------- Derleme zamanı hatası --------
// thread 'main' panicked at 'Boş değer döndürüldü', src/main.rs:4:5

````

### 2. Benzer şekilde `Result` varyantı `Err` için hata mesajı beklentimizi oluşturalım 

```Rust
fn main() {
    let e: Result<i8, &str> = Err("Bir mesaj");
    
    e.expect("Hata mesajı beklentisi");
}
// ---------- Derleme zamanı hatası --------
// thread 'main' panicked at 'Hata mesajı beklentisi: "Bir mesaj"', src/main.rs:4:5
````

## `Result` türleri için `unwrap_err()` ve `expect_err()` metodları
Bu metodlar `unwrap()` ve `except()` metodlarının tersine çalışırlar. Program süreci `Err` yerine `Ok` değeriyle panikler ve her ikisi de hata mesajlarında `Ok` değerini bulundurur. 

💡 Nu metodlar genellikle test işlemlerinde kullanılırlar.

### 1. İlk örneğimiz `Ok` için `unwrap_err()` metodu kullanımı
```Rust
fn main() {
    let o: Result<i8, &str> = Ok(8);
    
    o.unwrap_err();
}
// ---------- Derleme zamanı hatası --------
// thread 'main' panicked at 'called `Result::unwrap_err()` 
// on an `Ok` value: 8', src/main.rs:4:5
````

### 2. Sonraki örneğimiz `Ok` için `expect_err()` metodu kullanımı
```Rust
fn main() {
    let o: Result<i8, &str> = Ok(11);
    
    o.expect_err("Ok değeri alınamıyor");
}
// ---------- Derleme zamanı hatası --------
// thread 'main' panicked at 'Ok değeri alınamıyor: 11', src/main.rs:4:5
````
## `unwrap_or()`, `unwrap_or_default()` ve `unwrap_or_else()` metodları
> 💡 Bu işlevler `unwrap()` işlevine benzemekle birlikte `Option` türünden `Some` değerini ya da `Result` türünden `Ok` değerini içerdikleri sürece bir sonraki adıma ilerlerler. Ancak içeriğin `None` veya `Err` olması durumunda farklı davranırlar. 

  - **`unwrap_or()`:** `None` veya `Err` içeriğiyle `unwrap_or()` metoduna iletilen bir değer sonraki adıma ilerletilir. Ancak iletilen değerin veri türü iletilen `Some` veya `Ok` değeriyle eşleşmek zorundadır:
  
```Rust
fn main() {
    let v1 = 8;
    let v2 = 16;
    
    let some_v1 = Some(8);
    let none_n  = None;
    
    assert_eq!(some_v1.unwrap_or(v2), v1);  // Some(v1) unwrap_or v2 = v1 
    assert_eq!(none_n.unwrap_or(v2), v2);   // None unwrap_or v2 = v2 
    
    let o_v1: Result<i8, &str> = Ok(8);
    let e: Result<i8, &str> = Err("Hata");

    assert_eq!(o_v1.unwrap_or(v2), v1);     // Ok(v1) unwrap_or v2 = v1
    assert_eq!(e.unwrap_or(v2), v2);        // Err unwrap_or v2 = v2
}  
````
  - **`unwrap_or_default()`:** `Some` veya `Ok` türünün varsayılan değeri `None` veya `Err` aracılığıyla iletiliyorsa bu değerler `unwrap_or_default()` metodu yardımıyla bir sonraki adıma ilerletilirler. 

```Rust
fn main() {
    let v = 8;
    let v_default = 0;

    let s_v: Option<i8> = Some(8);
    let n: Option<i8> = None;

    assert_eq!(s_v.unwrap_or_default(), v);       // Some(v) unwrap_or_default = v
    assert_eq!(n.unwrap_or_default(), v_default); 
    // None unwrap_or_default = `v`nin varsayılan değeri

    let o_v: Result<i8, &str> = Ok(8);
    let e: Result<i8, &str> = Err("Hata");

    assert_eq!(o_v.unwrap_or_default(), v);       // Ok(v) unwrap_or_default = v
    assert_eq!(e.unwrap_or_default(), v_default); 
    // Err unwrap_or_default = `v`nin varsayılan değeri
}  
````

  - **`unwrap_or_else()`:** Metodu `unwrap_or()` metoduna benzer. Ancak bunun tek farkı kendisine bir değer geçirmek yerine `Some` veya `Ok` ile aynı veri türünde değer döndürebilen bir isimsiz işlev iletebilmemizdir.

```Rust
fn main() {
    let v1 = 8;
    let v2 = 16;

    let s_v1 = Some(8);
    let n = None;
    let fn_v2_for_option = || 16;

    assert_eq!(s_v1.unwrap_or_else(fn_v2_for_option), v1); // Some(v1) unwrap_or_else fn_v2 = v1
    assert_eq!(n.unwrap_or_else(fn_v2_for_option), v2);    // None unwrap_or_else fn_v2 = v2

    let o_v1: Result<i8, &str> = Ok(8);
    let e: Result<i8, &str> = Err("error");
    let fn_v2_for_result = |_| 16;

    assert_eq!(o_v1.unwrap_or_else(fn_v2_for_result), v1); // Ok(v1) unwrap_or_else fn_v2 = v1
    assert_eq!(e.unwrap_or_else(fn_v2_for_result), v2);    // Err unwrap_or_else fn_v2 = v2
}  
````

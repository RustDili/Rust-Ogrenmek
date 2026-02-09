## Birleştiriciler
## Birleştirici nedir?
  - "Birleştirici" nin bir anlamı, **birleştirici modeline** atıfta bulunan, daha dolaylı anlam ifade eden, bir şeyleri birleştirme fikri etrafında merkezlenmiş kütüphaneleri düzenleme tarzıdır.
Genellikle **bazı `T` türleri**, **`T` türünün "temel" değerlerini oluşturmak için bir takım işlevler** ve **`T` türünün daha karmaşık değerlerini oluşturmak** için **`T` türü değerlerini çeşitli şekillerde birleştirebilen** bazı “birleştiriciler” vardır. Diğer bir tanım ise "serbest değişkensiz işlev" anlamındadır. __ [wiki.haskell.org](https://wiki.haskell.org/Combinator)
  - Birleştirici, program parçalarından yeni program parçaları oluşturan bir işlevdir. Birleştiricileri kullanan bir programcı bir anlamda her ayrıntıyı elle yazmaktan ziyade otomatik olarak olşturmayı tercih etmektedir. __ John Hughes — [Monad'ların Oklarla genelleştirilmesi](http://www.cse.chalmers.se/~rjmh/Papers/arrows.pdf) [İşlevsel Programlama Kavramları](https://github.com/caiorss/Functional-Programming/blob/master/haskell/Functional_Programming_Concepts.org)'ndan.
  
Rust ekosistemindeki "birleştiriciler" in tam tanımı ise biraz belirsizdir.

- `or()`, `and()`, `or_else()`, `and_then()` 
  ▸ **`T` türündeki iki değişkeni birleştirerek** ▸ **aynı `T` türünü döndürür**.
 
- `Option` türleri için `filter()`
  ▸ Koşullu bir işlevi bir kapama gibi kullanarak şarta göre **`T` türünü Filtreler** ▸ **aynı `T` türünü döndürür**.
  
- `map()`, `map_err()` 
  ▸ **`T` türünü bir kapama işlevi uygulayarak dönüştürür**. 
  ▸ Bu işlem sırasında  **`T` içindeki değerin veri türü değişebilir**. 
  Örneğin: `Some<&str>` `Some<usize>` dönüştürülebileceği gibi `Err<&str>` de `Err<isize>` türüne dönüştürülebilir.
  
- `map_or()`, `map_or_else()`
  ▸ **Bir kapama işlevi uygulayarak dönüştürdüğü `T` türü ve içindeki değerleri döndürür**.
  ▸ **`None` ve `Err`, için varsayılan bir değer ya da başka bir kapama işlevi** uygulanır.
  
- `Option` türleri için `ok_or()`, `ok_or_else()` 
  ▸ **`Option` türünü bir `Result` türüne dönüştürür**.
  
- `as_ref()`, `as_mut()` 
  ▸ **`T` türünü bir referansa veya değişebilir bir referansa dönüştürür**.
  
## `or()` ve `and()` metodları
İki ifadeyi birleştirerek, `Option` veya `Result` türlerinden birini döndürür.

  - **`or()` metodu:** `Some` ya da `Ok` değerlerinden birisi varsa bu değer hemen döner.
  - **`and()` metodu:** Her iki ifadede de `Some` veya `Ok` değerlerinden biri bulunuyorsa, ikinci ifadedeki değer geriye döner. Bunlardan herhangi birinde `None` veya `Err` bulunuyorsa bu değer hızlıca döner.
  
```Rust
fn main() {
  let s1 = Some("some1");
  let s2 = Some("some2");
  let n: Option<&str> = None;

  let o1: Result<&str, &str> = Ok("ok1");
  let o2: Result<&str, &str> = Ok("ok2");
  let e1: Result<&str, &str> = Err("error1");
  let e2: Result<&str, &str> = Err("error2");

  assert_eq!(s1.or(s2), s1); // Some1 veya Some2 = Some1
  assert_eq!(s1.or(n), s1);  // Some veya None = Some
  assert_eq!(n.or(s1), s1);  // None veya Some = Some
  assert_eq!(n.or(n), n);    // None1 veya None2 = None2

  assert_eq!(o1.or(o2), o1); // Ok1 veya Ok2 = Ok1
  assert_eq!(o1.or(e1), o1); // Ok veya Err = Ok
  assert_eq!(e1.or(o1), o1); // Err veya Ok = Ok
  assert_eq!(e1.or(e2), e2); // Err1 veya Err2 = Err2

  assert_eq!(s1.and(s2), s2); // Some1 ve Some2 = Some2
  assert_eq!(s1.and(n), n);   // Some ve None = None
  assert_eq!(n.and(s1), n);   // None ve Some = None
  assert_eq!(n.and(n), n);    // None1 ve None2 = None1
  
  assert_eq!(o1.and(o2), o2); // Ok1 ve Ok2 = Ok2
  assert_eq!(o1.and(e1), e1); // Ok ve Err = Err
  assert_eq!(e1.and(o1), e1); // Err ve Ok = Err
  assert_eq!(e1.and(e2), e1); // Err1 ve Err2 = Err1
}
````
> 🔎 Rust'ın nightly sürümü `Option` türleri için `xor()` türünü desteklerken, yalnızca ifade bir tanesi `Some` almışsayi `Some` döndürür, ancak ikisini birden döndürmez.

## `or_else()` metodu
`or()` metoduna benzer. Farklı olarak, ikinci ifadenin aynı türden bir `T` döndüren [kapama işlevi](https://github.com/rust-lang-tr/dokuman/blob/master/rust-programlama-diline-giris/ilk-adim/islev.md#i%CC%87simsiz-i%C5%9Flevler-kapamalar) olmasını şart koşar.

```rust
fn main() {
    // `Option` için or_else()
    let s1 = Some("some1");
    let s2 = Some("some2");
    // let fn_some = || -> Option<&str> { Some("some2") }; ifadesine benzer
    let fn_some = || Some("some2");

    let n: Option<&str> = None;
    let fn_none = || None;

    assert_eq!(s1.or_else(fn_some), s1);  // Some1 or_else Some2 = Some1
    assert_eq!(s1.or_else(fn_none), s1);  // Some or_else None = Some
    assert_eq!(n.or_else(fn_some), s2);   // None or_else Some = Some
    assert_eq!(n.or_else(fn_none), None); // None1 or_else None2 = None2

    // `Result` için or_else()
    let o1: Result<&str, &str> = Ok("ok1");
    let o2: Result<&str, &str> = Ok("ok2");
    // let fn_ok = |_| -> Result<&str, &str> { Ok("ok2") }; ifadesine benzer
    let fn_ok = |_| Ok("ok2"); 

    let e1: Result<&str, &str> = Err("error1");
    let e2: Result<&str, &str> = Err("error2");
    let fn_err = |_| Err("error2");

    assert_eq!(o1.or_else(fn_ok), o1);  // Ok1 or_else Ok2 = Ok1
    assert_eq!(o1.or_else(fn_err), o1); // Ok or_else Err = Ok
    assert_eq!(e1.or_else(fn_ok), o2);  // Err or_else Ok = Ok
    assert_eq!(e1.or_else(fn_err), e2); // Err1 or_else Err2 = Err2
}
````

## `and_then()` metodu
`and()` metoduna benzer. Tek fark, ikinci ifadenin aynı türden `T` döndüren bir [kapama işlevi](https://github.com/rust-lang-tr/dokuman/blob/master/rust-programlama-diline-giris/ilk-adim/islev.md#i%CC%87simsiz-i%C5%9Flevler-kapamalar) olmasını şart koşar.

```rust
fn main() {
    // `Option` için and_then()
    let s1 = Some("some1");
    let s2 = Some("some2");
    // let fn_some = |_| -> Option<&str> { Some("some2") }; ifadesine benzer
    let fn_some = |_| Some("some2");

    let n: Option<&str> = None;
    let fn_none = |_| None;

    assert_eq!(s1.and_then(fn_some), s2); // Some1 and_then Some2 = Some2
    assert_eq!(s1.and_then(fn_none), n);  // Some and_then None = None
    assert_eq!(n.and_then(fn_some), n);   // None and_then Some = None
    assert_eq!(n.and_then(fn_none), n);   // None1 and_then None2 = None1

    // // `Result` için and_then()
    let o1: Result<&str, &str> = Ok("ok1");
    let o2: Result<&str, &str> = Ok("ok2");
    // let fn_ok = |_| -> Result<&str, &str> { Ok("ok2") }; ifadesine benzer
    let fn_ok = |_| Ok("ok2");

    let e1: Result<&str, &str> = Err("error1");
    let e2: Result<&str, &str> = Err("error2");
    let fn_err = |_| Err("error2");

    assert_eq!(o1.and_then(fn_ok), o2);  // Ok1 and_then Ok2 = Ok2
    assert_eq!(o1.and_then(fn_err), e2); // Ok and_then Err = Err
    assert_eq!(e1.and_then(fn_ok), e1);  // Err and_then Ok = Err
    assert_eq!(e1.and_then(fn_err), e1); // Err1 and_then Err2 = Err1
}
````

## `filter()` metodu
> 💡 `filter()` metodları programlama dillerinde genellikle, dizi veya yineleyicilerin bir kapama veya işlev yardımıyla filtrelenerek yeni bir dizi veya yineleyici oluşturulması işlerinde kullanılır. Ek olarak Rust bir yineleyicinin, başka bir yineleyiciye dönüştürülebilmesi için, bir kapama işevi aracılığıyla dönüştürülmek istenilen yineleyicinin her bir elemanına uygulanabilecek [yineleyici adaptörü olarak kullanılabilen bir `filter()`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.filter) metodu sağlar. Yalnız burada `Option()` türleriyle kullanılabilen bir `filter ()` işlevselliğinden bahsettiğimizi anımsatmam yerinde olur.

Eğer  bir kapama işlevine tek bir `Some` değeri iletilirse ve bu işlevden `true` döndürülürse aynı türden bir `Some` döndürülmüş olur. Fakat kapama işlevine `None` değeri iletiliyor ve işlevden `false` döndürülüyorsa bu durumda bir `None` geriye döndürülür. Kapama işlevleri `Some` içinde bulunan değeri bağımsız değişken olarak kullanırlar. Rust'ta halen `filter()` desteği sadece `Option` türleri için kullanılabilmektedir.

```rust
fn main() {
    let s1 = Some(3);
    let s2 = Some(6);
    let n = None;

    let fn_cift_mi = |x: &i8| x % 2 == 0;

    assert_eq!(s1.filter(fn_cift_mi), n);  // Some(3) -> 3 çift değil -> None
    assert_eq!(s2.filter(fn_cift_mi), s2); // Some(6) -> 6 çift -> Some(6)
    assert_eq!(n.filter(fn_cift_mi), n);   // None -> değer taşımıyor -> None
}
````

## `map()` ve `map_err()` metodları

>💡 Bunlar programlama dillerinde genellikle, dizi veya yineleyicilerle kullanılan ve **bir kapama işlevinin koleksiyonun her bir elemanına ayrı ayrı uygulanmasını** sağlayan metodlardır. Ek olarak Rust bir yineleyicinin her bir öğesini başka bir yineleyiciye dönüştürebilmek maksadıyla kapama olarak uygulanabilen **bir yineleyici adaptörü olan `map()`** metodunu sunar. Elbette ki burada da `Option` ve `Result` türleri ile kullanılabilen bir `map()` işlevselliğinden bahsettiğimizi hatırlatmak zorundayım.

  - **`map()`:** `T` türlerini bir kapama uygulayarak dönüştürür. `Some` veya `Ok` bloklarının veri türü, kapamanın dönüş türüne göre değiştirilebilir. Başka bir ifadeyle `Option<T>` türü `Option<U>` türüne ya da `Result<T, E>` türü `Result<U, E>` türüne dönüştürülebilir.

⭐ `map()` metodlarıyla sadece `Some` ve `OK` değerlerinin değişebileceğini `Err` içindeki değerlerin etkinmeyeceğini hatırlamanız gerekir. Bir `Option` varyantı olan `None` türünün ise zaten hiçbir değeri içermeyeceğini zaten biliyorsunuz.

```rust
fn main() {
    let s1 = Some("abcde");
    let s2 = Some(5);

    let n1: Option<&str> = None;
    let n2: Option<usize> = None;

    let o1: Result<&str, &str> = Ok("abcde");
    let o2: Result<usize, &str> = Ok(5);
    
    let e1: Result<&str, &str> = Err("abcde");
    let e2: Result<usize, &str> = Err("abcde");
    
    let fn_karakter_adedi = |s: &str| s.chars().count();

    assert_eq!(s1.map(fn_karakter_adedi), s2); // Some1 map = Some2
    assert_eq!(n1.map(fn_karakter_adedi), n2); // None1 map = None2

    assert_eq!(o1.map(fn_karakter_adedi), o2); // Ok1 map = Ok2
    assert_eq!(e1.map(fn_karakter_adedi), e2); // Err1 map = Err2
}
````
  - **`map_err()`:** `Ressult` türleri için kullanılır. Kapama işlevinin dönüş türüne göre `Err` bloklarının veri türü değiştirilebilir. Başka bir ifadeyle `Result<T, E>` türü `Result<T, F>` türüne dönüştürülebilir.

⭐ `map_err()` metodu ile yalnızca `Err` değerlerinin değiştirilebileceğini `Ok` içeriğinin bundan etkilenmeyeceğini unutmayınız.

```rust
fn main() {
    let o1: Result<&str, &str> = Ok("abcde");
    let o2: Result<&str, isize> = Ok("abcde");

    let e1: Result<&str, &str> = Err("404");
    let e2: Result<&str, isize> = Err(404);
    
     //str den isize türüne dönüşür
    let fn_karakter_adedi = |s: &str| -> isize { s.parse().unwrap() };

    assert_eq!(o1.map_err(fn_karakter_adedi), o2); // Ok1 map = Ok2
    assert_eq!(e1.map_err(fn_karakter_adedi), e2); // Err1 map = Err2
}
````

## `map_or()` ve `map_or_else()` metodları
Daha önce işlediğimiz ` unwrap_or()` ve `unwrap_or_else()` metodlarının işlevselliğini hatırladığınızı sanıyorum. Bu metodlar onların birer benzeridir. Ancak `map_or()` ve `map_or_else()` metodları, `Some` ve `Ok` değerlerine bir kapatma uygulayarak işlenilen değeri `T` türü içinde döndürür. 

  - **`map_or():`** Yalnızca `Option` türlerini destekler `Result` türünü desteklemez. Kapama işlevini `Some` içindeki değerlere uygulayarak kapamadan iletilen çıktıyı döndürür. `None` türleri içinse belirlenmiş varsayılan bir değer döndürülür.
  
```Rust
fn main() {
    const VARSAYILAN_D: i8 = 1;
    
    let s = Some(10);
    let n: Option<i8> = None;
    let kapama = |v: i8| v + 2;
    
    assert_eq!(s.map_or(VARSAYILAN_D, kapama), 12);
    assert_eq!(n.map_or(VARSAYILAN_D, kapama), VARSAYILAN_D);
}
````

  - **`map_or_else():`** Hem `Option` hem `Result` türlerini destekler. `Map_or()` metoduna benzemekle beraber, ilk parametre için varsayılan değer yerine başka bir kapama işlevin sunulması gerekir.
  
⭐ N türü hiçbir değer içermediğinden `Option` türleri söz konusu olduğunda kapama işlevine girdi olarak bir şey iletmeye gerek yoktur. Bununla birlikte `Err` türlerinde bir parça bilgi içerdiğinden, bu metodun `Result` türleriyle kullanımında kapama işlevi tarafından girdi olarak okunabilmesi gereklidir. 

```rust
// nightly sürümünde bulunan kararsız kütüphane 
// özelliğinin etkinleştirilmesi 

#![feature(result_map_or_else)] 
fn main() {
    let s = Some(10);
    let n: Option<i8> = None;

    // `None` bir değer içermediğinden kapamaya giriş olarak iletmeye gerek yok
    let fn_kapama = |v: i8| v + 2;
    let fn_varsay = || 1;

    assert_eq!(s.map_or_else(fn_varsay, fn_kapama), 12);
    assert_eq!(n.map_or_else(fn_varsay, fn_kapama), 1);

    let o = Ok(10);
    let e = Err(5);
    // `Err` ise bir parça veri içerdiğinden varsayılan kapama 
    // kapama işlevi tarafından giriş olarak okunabilmelidir
    let fn_result_icin_varsay = |v: i8| v + 1; 

    assert_eq!(o.map_or_else(fn_result_icin_varsay, fn_kapama), 12);
    assert_eq!(e.map_or_else(fn_result_icin_varsay, fn_kapama), 6);
}
````

## `ok_or()` ve `ok_or_else()` metodları
Daha önce de belirtildiği gibi `ok_or()` ve `ok_or_else()` metodları `Option` türünü `Result` türüne dönüştürür. Başka bir ifadeyle `Some` türü `Ok` türüne, `None` türü de `Err` türüne dönüştürülür.

  - **`ok_or():`** Varsayılan bir `Err` mesajı bağımsız değişken olarak iletilmelidir. 
  
```rust
fn main() {
    const ERR_VARSAY: &str = "Hata mesajı";

    let s = Some("abcde");
    let n: Option<&str> = None;

    let o: Result<&str, &str> = Ok("abcde");
    let e: Result<&str, &str> = Err(ERR_VARSAY);

    assert_eq!(s.ok_or(ERR_VARSAY), o); // Some(T) -> Ok(T)
    assert_eq!(n.ok_or(ERR_VARSAY), e); // None -> Err(varsayılan)
}
````

  - **`ok_or_else()`:** `ok_or()` metoduna benzer. Argüman olarak bir kapama geçirilmelidir. 

```rust
fn main() {
    let s = Some("abcde");
    let n: Option<&str> = None;
    let fn_hata_iletisi = || "Hata mesajı";

    let o: Result<&str, &str> = Ok("abcde");
    let e: Result<&str, &str> = Err("Hata mesajı");

    assert_eq!(s.ok_or_else(fn_hata_iletisi), o); // Some(T) -> Ok(T)
    assert_eq!(n.ok_or_else(fn_hata_iletisi), e); // None -> Err(varsayılan)
}
````

## `as_ref()` ve `and as_mut()` metodları
🔎 Daha önce belirtildiği gibi bu metodlar bir `T` türünü, referans ya da değişebilir referans olarak ödünç almak amacıyla kullanılır.

  - **`as_ref()`:** `Option<T>` türünü `Option<&T>` türüne, ve `Result<T, E>` türünü `Result<&T, &E>` türüne dönüştürür.
  - **`as_mut()`:** `Option<T>` türünü `Option<&mut T>` türüne, ve `Result<T, E>` türünü `Result<&mut T, &mut E>` türüne dönüştürür. 

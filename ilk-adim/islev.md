# İşlevler
Girilen **n** adet parametreyi alıp, bir mantık çerçevesinde sarmalanmış kod bloğuna aktararak bir veya birden fazla sonucun üretilmesini ve çağrıldığı noktaya döndürülmesini sağlayan yapısal bloklardır.

### İsimlendirilmiş İşlevler
- İşlevin İngilizce karşılığı **function** kelimesinin kısaltılmış hali olan **`fn`** anahtar sözcüğü ile bildirilirler.
- **Argüman** ile kullanıldığında bu argümanların veri türleri **parametre listesi**nde bildirilir.
- İşlevlerin boş bir **[Tuple ()](https://github.com/rust-lang-tr/dokuman/blob/master/rust-programlama-diline-giris/ilk-adim/ilkeller.md#viii-%C3%A7okuzlular)** döndüreceği varsayılır. Bir işlevin sonuç döndürmesi isteniyorsa, dönüş türleri **`->`** işaretinden sonra belirtilir.

#### i. Merhaba dünya!

```Rust
fn main() { 
  println!("Merhaba dünya!"); 
}
````

#### ii. Argüman geçirmek

```Rust
fn topla(a: i8, b: i8) { 
  println!("toplam: {}", a + b); 
}

fn main() {
  topla(10, 20);
}
````

#### iii. Değer döndürmek
01. `return` anahtar sözcüğü kullanılmayan örnek. Sadece son ifade döner. Son satırda noktalı virgül aranmaz:
```Rust
fn birle_topla(a: i32) -> i32 { 
  a + 1	// Son ifade bu satır olduğundan, dönecek olan ifade bu satırdadır.  
        // Ve bu bildirimin return a + 1; ifadesine eşit olduğu anlaşılır. 
} 
````
02. `return` anahtar sözcüğü kullanan örnek. Döndürülecek olan satır `return` anahtar kelimesiyle işaretlendirildiğinden bu satırdaki ifade işlenerek döndürülür.
```Rust
 fn ikiyle_topla(a: i32) -> i32 { 
  return a + 2; // a+2 döndürülür. 
}
````

#### iv. Veri türü olarak kullanılan işlev işaretçisi

```Rust
// 01. Tür bildirimsiz kullanım 
  let b = birle_topla; 
  let c = b(5); // 6 

// 02. Tür bildirimli kullanım 
  let d: fn(i32) -> i32 = ikiyle_topla; 
  let e = d(5); // 7
````

### İsimsiz işlevler (Kapamalar)
Kapama ya da lambda işlevler olarak bilinirler; argümanlarının veri türlerini bildirmek veya işlevden sonuç döndürmek isteğe bağlıdır.

Standart biçimde tasarlanmış isimlendirilmiş bir işlev aşağıdaki gibi ifade edilebilir:

```Rust
fn karesini_al(x: u32) -> u32 {
    x * x
}
fn main() {
    let a = 5;
    println!("{}'nın karesi: {}", a, karesini_al(a));  
} 
````

#### i. Giriş ve dönüş türleri bildirilerek 
Aynı örneği isimsiz işlev olarak tasarlanamak çok daha pratiktir. Bu tasarımda işlevin giriş ve çıkış türlerini isteğe bağlı olarak bildirilir:

```Rust
fn main() {
    let x = 3;
    
    let karesi = |x: i32|-> i32 {
        x * x
    };
    
    println!("{}'in karesi: {}", x, karesi(x));
}
````
Giriş ve dönüş türlerini belirtmenin isteğe bağlı olduğu bu isimsiz işlevde, giriş parametreleri işleve **`||`** kullanılarak geçirilir ve ifade gövdesi **`{};`** köşeli parantezler ile sarılır.

#### ii. Giriş ve dönüş türü bildirmeden 
Eğer giriş ve dönüş türleri belirtilmeyen isimsiz işlev tek bir satırdan oluşuyorsa `{};` köşeli parantez kullanmak zorunlu değildir.

```Rust
fn main() {
    let x = 2;
    let karesi = |i| i * i; // bir satırdan oluşan isimsiz 
                            //işlevlerde { } isteğe bağlı
    println!("karesi: {}", karesi(x)); // karesi: 4
}
````

#### iii. Oluşturup çağırırken tür bildirimli
İsimsiz işlevleri tür bildirimi ile oluşturup çağırabilirsiniz. Ancak hem oluştururken hem de çağırırken süslü parantezler **`{ }`** zorunludur. 

```Rust
fn main() {
    let x = 3;
    
    // oluştururken ve çağırırken { } zorunludur.
    let x_in_karesi = |i: u32| -> u32 { i * i}(x);
    println!("x'in karesi: {}", x_in_karesi); // x'in karesi: 9
}
````

#### iv. Tür bildirimsiz oluşturup çağırma
Bu tarz isimsiz işlevlerde sadece dönüş türünü bildirmek zorunludur.

```Rust
fn main() {
    let x = 4;
    
    // ⭐️ Dönüş türü zorunludur
    let karesi = |i| -> i32 { i * i}(x); 
    println!("karesi: {}", karesi); // karesi: 16
}
````

# Basit veri türleri
Rust çok sayıda basit veri türüne erişim sağlar.

### i. Boolean
**bool:** yalnızca `true` ya da `false` olabilecek bir değeri temsil eden mantıksal veri türüdür. 

```Rust
fn main() {
    let a: bool = true;         // Tür bildirimli 
    let b = false;              // Tür çıkarsamalı kısa bildirim şekli
    
    println!("{}, {}", a, b);   // true, false
}
````

Bir tamsayı türüne dönüştürüldüğünde `true` için **1** `false` için **0** atandığı görülür. 

```Rust
   let x = a as u8;
   let y = b as u8;
   println!("Tamsayı true : {}, tamsayı false: {}", x, y);
   // Tamsayı true : 1, tamsayı false: 0

````    
Bit operatörleri aracılığıyla `BitAnd` **`&`**, `BitOr` **`|`**, `Not` **`!`**  vb. gibi çeşitli özelliklerin uygulanmasını sağlar.

⭐️ Uygulamada TRUE, FALSE, 1, 0 gibi olası karşılık değerleri kullanılmaz.

```Rust
fn main() {
    let hava_yagmurlu: bool = true;
    
    if hava_yagmurlu == true {
        println!("Dışarıya çıkarken şemsiyeni unutma");
    } else {
        println!("Güneşlenerek kahve içmek için harika bir gün");
    }
}
// Dışarıya çıkarken şemsiyeni unutma
````

### ii. Char
**char:** `'a'`, `'@'`,  `'~'` gibi unicode desteğinde, her biri 4 byte yani 32 bit'e sığan ve tek bir harf karakterini temsil eden türlerdir. Tek tırnak işareti arasında ifade edilirler.

```Rust
fn main() {
    let a = '😎'; 
    let b = '♫';
    let c = '@';
    println!("a: {}, b: {}, c: {}", a, b, c); 
}
// a:😎, b:♫, c:@
````

### iii. i8, i16, i32, i64, i128
8, 16, 32, 64, 128 bit sabit boyutlu ve **`(+/-)`** işaretli tam sayı değerlerini tutarlar.

| VERİ TÜRÜ | MIN                                      | MAX                                     |
| --------- | ---------------------------------------- | --------------------------------------- |
| i8        | -128                                     | 127                                     |
| i16       | -32768                                   | 32767                                   |
| i32       | -2147483648                              | 2147483647                              |
| i64       | -9223372036854775808                     | 9223372036854775807                     |
| i128      | -170141183460469231731687303715884105728 | 170141183460469231731687303715884105727 |

💡 **Alt sınır ve üst sınır değerleri;** alt sınır değerler için **-(2ⁿ⁻¹)** ve üst sınır değerler için **2ⁿ⁻¹-1** formüllerine dayanmaktadır. Türün alt sınır değerlerini öğrenmek için `min_value()`, üst sınır değerlerini öğrenmek içinse `max_value()` işlevleri kullanılır.

```Rust
fn main() {
    println!("i16 alt sınır değeri: {} ve üst sınır değeri: {}", i16::min_value(), i16::max_value());
}
// u8 alt sınır değeri:0, u8 üst sınır değeri: 255
````

### iv. u8, u16, u32, u64, u128
8, 16, 32, 64, 128 bit sabit boyutlu ve **`(0/+)`** işaretli tam sayı değerlerini tutarlar.

| VERİ TÜRÜ | MIN | MAX                                     |
| --------- | --- | --------------------------------------- |
| u8        | 0   | 255                                     |
| u16       | 0   | 65535                                   |
| u32       | 0   | 4294967295                              |
| u64       | 0   | 18446744073709551615                    |
| u128      | 0   | 340282366920938463463374607431768211455 |

💡 **Alt sınır ve üst sınır değerleri;** alt sınır değerler için **0** ve üst sınır değerler için **2ⁿ-1** formüllerine dayanmaktadır. Her tam sayı türünün alt sınır değerlerine `min_value()`, üst sınır değerlerine ise `max_value()` işlevleri aracılığıyla ulaşılır.  

```Rust
fn main() {
    println!("u32 alt sınır değeri: {} ve üst sınır değeri: {}", u32::min_value(), u32::max_value());
}
// u32 alt sınır değeri: 0 ve üst sınır değeri: 4294967295
````

### v. isize ve usize
İşaretçi boyutunda işaretli ve işaretsiz tam sayı türlerini tutarlar. Programın derlenmekte olduğu bilgisayar mimarisinin sunduğu en yüksek bit değerine eşittirler. Varsayılan olarak bu değerler 32 bit platformlarda **32 bit**, 64 bit platformlarda da **64 bit** kabul edilir.

> 🔎 [Çapraz derleme](https://github.com/rust-lang/rustup.rs#cross-compilation) ve [desteklenen katmanlar](https://forge.rust-lang.org/release/platform-support.html) hakkında daha fazla bilgi edinebilirsiniz

```Rust
fn main() {
    println!("isize alt sınır değeri: {} usize üst sınır değeri: {}", 
            isize::max_value(), usize::max_value());
}
// isize alt sınır değeri: -9223372036854775808 ve isize üst sınır değeri: 9223372036854775807
````

### vi. f32 ve f64
32 ve 64 bit boyutlarında kesirli sayıları tutabilen türlerdir. Rust, kesirli sayıları ifade ederken **IEEE** standartlarına uyar. Bu nedenle `f32` türü, diğer dillerdeki tek duyarlıklı float türüne benzerken, `f64` çift duyarlıklı double türüne benzer.
Hedeflenen donanım çift hassasiyeti desteklemiyorsa yahut tek hassasiyetli türün çift hassasiyetli türden daha hızlı olabileceği durumlarda `f32`, diğer durumlarda daima `f64` kullanılması tavsiye edilir. Rust' ta aksi belirtilmedikçe tamsayılar için `i32`, kayan noktalı sayılar için `f64` türlerinin kullanılacağını varsayılır. 

⭐️ Her ne kadar derleme anında türler çıkarsanıyor olsalar da, eğer varsayılan türler haricinde bir tür kullanılmak isteniyorsa, bu türün açıkça bildirilmesi gerekir.

```Rust
fn main() {
    let a = 4.7_f32;
    let b = 5.2_f32;
    let c = -3.3_f32;
    
    println!("a: {}, b: {}, c: {}", a, b, c);
    println!("a: {}, b: {}, c: {}", a.floor(), b.ceil(), c.round());
}
// a:4.7, b:5.2, c:-3.3
// a:4, b:6, c:-3
````

> 🔎 Örnekte yer alan `floor()`, `ceil()`, `round()` metod açıklamaları ve daha fazlası için [bağlantıyı](https://doc.rust-lang.org/std/primitive.f32.html) ziyaret edebilirsiniz.

### vii. Diziler
Aynı veri türündeki öğelerin sabit boyutlu listesini tutarlar. Köşeli parantez içinde tanımlanırlar ve bellekte bitişik olarak depolanırlar. 
Tür ve uzunluk bilgileri derleme zamanında bilinmesi gerektiğinden `[T; size]` söz dizimiyle kullanılırlar. Köşeli parantez içinde bildirilen birinci bölüm olan `T` dizi elemanlarının türünü, ikinci bölüm olan `size` dizinin büyüklüğünü bildirir.

```Rust
let a = [1, 2, 3];                                // a[0] = 1, a[1] = 2, a[2] = 3 
let mut b = [1, 2, 3];                            // Elemanları değişebilen dizi
b[0] = 2;
b[1] = 4;
b[2] = 6;

println!("{:?}", b);                              // [2, 4, 6]; 

let c: [i32; 0] = [];                             // [Tür tanımı; öğesiz] -> [] / boş bir dizi
let d: [i32; 3] = [1, 2, 3];                      // Türü ve eleman sayısı bildirilmiş dizi 
let e = ["değer"; 3];                             // ["değer", "değer", "değer"]; 

println!("{:?}", a);                              // [1, 2, 3]; 
println!("{:#?}", a);                             // [ // 1, // 2, // 3 // ];

let xs: [i32; 5] = [1, 2, 3, 4, 5];               // 5 elemanlı statik bir dizi
let ys: [i32; 500] = [0; 500];                    // Tüm üyeleri 0 ile ilklenen 500 elemanlı dizi
let ab: [i32; 5] = [-2, 5];                       // Tüm üyeleri -2 ile ilklenen 5 elemanlı dizi

println!("Dizinin ilk elemanı: {:?}", xs[0]);     // Dizilerde index 0 dan başlar. 1 yazdırılır.
println!("Dizinin {} tane üyesi var", ys.len());  // len işlevi dizinin uzunluğunu verir
println!("Dizi elemanları: {:?}", ab);            // [-2, -2, -2, -2, -2];

// use std::mem;
println!("ys dizisi belleğin stack bölgesinde: {} byte yer kaplıyor.", mem::size_of_val(&ys));
````

💡 Diziler varsayılan olarak değişmez kabul edildiklerinden, tanımlandıktan sonra eleman adetleri ve türleri değiştirilemez. Tanımlandıkları sırada aldıkları mut anahtar sözcüğü ile sadece eleman değerleri değiştirebilir.

> 🔎 Eğer eleman adetlerinin otomatik olarak arttırılabildiği bir dizi türü gerekiyorsa bunun için [**Vektörler**](https://github.com/rust-lang-tr/dokuman/blob/master/rust-programlama-diline-giris/ikinci-adim/vectors.md) tercih edilmelidir. Vektörler türleri aynı olmak kaydıyla istenilen sayıda elemanı kabul ederler.

### viii. Çokuzlular
Aynı ya da farklı veri türlerinden oluşan elemanların sabit büyüklükteki listelerini oluşturmak için kullanılan değer topluluklarıdırlar. Her elemanı `(T1, T2, T3...Tn)` kendi türünün imzalı değeri olduğundan, çok sayıda elemanla veya farklı türlerden oluşan koleksiyonlarla çalışırlırken oldukça faydalıdırlar.

```Rust
fn main() {
    let a = (1, 1.5, true, 'a', "Merhaba Dünya!");
    let a: (i32, f64, bool, char, &str) = (1, 1.5, false, 'a', "Merhaba Dünya!");
    println!("a: {:?}", a);                        // a: (1, 1.5, false, 'a', "Merhaba Dünya!")
    
    let mut x = (1, 1.5);
    x.0 = 2;
    x.1 = 3.0;
    println!("x: {:?}", x);                        // x: (2, 3.0)
    
    let b: (i32, f64) = (10, 3.5);
    println!("b: {:?}", b);                        // b: (10, 3.5)
    
    let (c, d) = b;
    println!("c: {:?}, d: {:?}", c, d);            // c: 10, d: 3.5
    
    let (e, _, f, _, g) = a;
    println!("e: {:?}, f: {}, g: {}", e, f, g);    // e: 1, f: true, g: Merhaba Dünya!
                                                   // _ ilgilenmediğiniz öğeleri temsil eder
    let h = (0,);
    println!("h: {:?}", h);                        // h: (0,) -> Tek elemanlı Çokuzlu
    
    let i = (b, (20, 50), -3.2);
    println!("i: {:?}", i);                        // i: ((10, 3.5), (20, 50), -3.2)
}
````

⭐️ Bu tür de tıpkı diziler gibi varsayılan olarak değiştirilmez halde gelir. Bu nedenle `mut` anahtar kelimesiyle tanımlanmaları sadece  öğelerinin değiştirilebilmesini sağlar ve eleman sayılarının değiştirilmesine izin verilmez. 

💡 Çokuzlu öğelerinin herhangi birini değiştirmek istediğinizde, yeni değerin önceki değer türü ile aynı olmasına dikkat etmelisiniz.

### ix. Dilimler
Dizilere benzemekle birlikte boyutları derleme zamanında belli olmayan koleksiyon türleridir. Dilimleri **`&[T];`** söz dizimiyle ifade edilebilen ve iki parçadan oluşan nesneler olarak düşününmemiz gerekir. Bu parçaların ilki erişilen verinin göstergesi olurken, ikincisi elde edilecek olan dilimin uzunluğunu göstermektedir. Bir diziyi veya koleksiyon türünü kopyalayarak kullanmak yerine verilerin bir kısmına erişmek için bir görüntü/referans oluştururlar. Bu referanslar değişmez veya değişir halde olabilirler.  

```Rust
fn main() {
    let a: [i32; 4] = [1, 2, 3, 4];    // Referans verilecek ana dizi 
    
    let b: &[i32] = &a;
    println!("b: {:?}", b);            // Bütün dizi dilimlenir.

    let c = &a[0..4]; 
    println!("c: {:?}", c);            // index 0 dan başlayarak ilk 4 eleman

    let d = &a[..];
    println!("d: {:?}", d);            // Bütün dizi dilimlenir.
    
    let e = &a[1..3];
    println!("e: {:?}", e);            // [2, 3]

    let f = &a[1..];
    println!("f: {:?}", f);            // [2, 3, 4]
    
    let g = &a[..3];
    println!("g: {:?}", g);            // [1, 2, 3]
}
````

Diziler dilimler tarafından otomatik olarak ödünç alınırlar.

```Rust
fn dilimle(dilim: &[i32]) {
    println!("İlk elemanı {} olan {} elemanlı dilim", dilim[0], dilim.len())
}

fn main() {
    let dil: [i32; 5] = [1, 2, 3, 4, 5];
    dilimle(&dil);
    
    let dilim: &[i32] = &dizi[0..4];
    dilimle(&dilim);
    
    dilimle(&dil[1..3]);
}
// İlk elemanı 0 olan 5 elemanlı dilim
// İlk elemanı 0 olan 4 elemanlı dilim
// İlk elemanı 1 olan 3 elemanlı dilim
````

### x. Str
Unicode dizgilerinin bir parçasını UTF-8 formatında tutan boyutu olmayan dilimlerdir. 

```Rust
fn main() {
    let dizgi = String::from("Merhaba Dünya!");
    let merhaba = &dizgi[0..7];
    println!("Dizginin ilk parçası: {}", merhaba);
    
    let dunya = &dizgi[8..15];
    println!("Dizginin son parçası: {}", dunya);
}
// Dizginin ilk parçası: Merhaba
// Dizginin son parçası: dünya!
````
⭐️ Hafızanın herhangi bir yerinde saklanan ve boyutu belirsiz bir UTF-8 kod noktası koleksiyonunu tutan statik ve değişmez olarak tahsis edilmiş bir dizgi parçasıdır. Dizgiler ödünç verilirken **`&str`** şeklinde kullanılırlar.

## Sonraki konuya geçmeden önce...
- Değişken tanımlarında yapılan tür bildirimlerinin dışında, tamsayılar için veri türü doğrudan sonek olarak değere eklenebilir. Bunun yanında, uzun sayıların okunabilirliğini artırmak amacıyla basamaklar arasında **`_`** karakterini bölücü olarak kullanabiliriz. Bölücünün kullanımı esnek olup istenilen basamakların arası tıpku bu sayıda olduğu gibi `10000_0000` istenildiği şekilde bölünebilir.

```Rust
fn main() {
    // Bu tanım `let a: i18 = 5;` ifadesine eşittir
    let a = 5i18;
    
    // Bu tanım `let b: i32 = 100000000;` ifadesine eşittir
    let b = 100_000_000; 
}
````
- Dizge olarak Türkçeleştirdiğimiz [`String`](https://doc.rust-lang.org/std/string/struct.String.html), kendisine belleğin heap adlı bölgesinde yer ayrılan bir türdür. Bu nedenle dizgilerin  boyutları genişlemeye müsait olup UTF-8 standartlarında oldukları garanti edilir. Genellikle bir dizginin mülkiyetine ihtiyaç duyulduğunda dizginin kendisi, bir dizgiyi ödünç almak gerektiğinde ise `&str` ile dizginin referansı kullanılır.

- Bir [String](https://doc.rust-lang.org/std/string/struct.String.html) türü, `&str` türünden, [`to_string()`](https://doc.rust-lang.org/std/string/trait.ToString.html) veya [`String::from()`](https://doc.rust-lang.org/std/string/struct.String.html#method.from) metodları kullanılarak oluşturulabilir. Benzer şekilde bir `String` türü de [`as_str()`](https://doc.rust-lang.org/std/string/struct.String.html#method.as_str) metodu yardımıyla, `&str` türüne dönüştürülebilir.

```Rust
fn main() {
    let d: &str = "Merhaba";    // &str
    let s = d.to_string();      // şimdi String
    
    let dd = s.as_str();        // &str
    let e = String::from(dd);    // yine String
    
    println!("d:{}, s:{}, dd:{}, e:{}", d, s, dd, e);
}
// d:Merhaba, s:Merhaba, dd:Merhaba, e:Merhaba
````

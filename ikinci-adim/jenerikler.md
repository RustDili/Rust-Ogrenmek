# Genellemeler

> 📖 Bir veri türü yahut bir işlev tanımlanırken bunların farklı türlerle de çalışmasını isteriz. Rust' ta bunu **genellemeler** ile yapabiliriz. Farklı veri türleri için kullanılmak üzere tasarlanmış ve tek noktada toplanmış olan bir program parçası aynı işi diğer türler için de yapacak olan kodun tekrar tekrar yazılmasını önler. Farklı veri türleri için  genelleştirilmiş olan algoritmanın, her veri türü için tekrar üretilmesi gerekmeyeceğinden, programın kod tasarımı sadeleşmiş, geliştirme hızı da artmış olur. 

💭 Genelleme kavramında özel bir veri türünü `(x: u8)` şeklinde bildirmek yerine türün yerine geçebilen `(x: T )` şeklinde genel bir belirteç kullanılır. Ancak genel türün derleyici tarafından anlaşılabilmesi için `<T>` şeklinde tanımlanarak bildirilmesi gerekmektedir.

### Genellenmiş işlevler
Aynı işlevin farklı türlerle kullanılabiliyor olması kodun gereksizce uzamasını önleyerek daha esnek olmasını sağlar:

```Rust
use core::fmt::Debug;

fn her_ture_uygun<T>(x: T) { 
    // x T türünde bir parametredir. 
    // T ise genellenmiş bir türdür yani işleve farklı
    // türleri argüman olarak iletebilirsiniz.
} 

fn ayni_turden_iki_tane<T: Debug>(x: T, y: T) {
    // Her ikisi de aynı türden parametre alır
    println!("{:?}, {:?}", x, y);
}

fn farkli_turden_iki_tane<T: Debug, U: Debug>(x: T, y: U) {
    // Farklı türden parametreler alırlar
    println!("{:?}, {:?}", x, y);
}

fn main() {
    let arr: u8 = 3;
    her_ture_uygun(arr);                // 3
    
    let (x, y) = (5, 10);
    ayni_turden_iki_tane(x, y);         // 5, 10
    
    let f = (3.0, false);
    farkli_turden_iki_tane(f.0, f.1);   // 3.0, false
}
````

Bir verinin hangi tür olduğunu öğrenebilmek için `std::any` kütüphanesinden yararlanabiliriz:

```Rust
fn her_ture_uygun<T>(_: T)  {
    // x parametresi Genelleştirilmiş T türündedir.
    println!("{} türünde veri", std::any::type_name::<T>());
}

fn main() {
    let a: u8 = 10;
    her_ture_uygun(a);          // u8 türünde veri
    her_ture_uygun(65i32);      // i32 türünde veri
    her_ture_uygun(2.0_f32);    // f32 türünde veri
    her_ture_uygun(0_isize);    // isize türünde veri
    her_ture_uygun([1,2,3]);    // [i32; 3] türünde veri
}
````

### Genellenmiş yapılar
Genellenmiş tür parametrelerinin yapı alanlarında kullanılabilmesi için tanımlarında `<T>` söz diziminin kullanılması gereklidir. Herhangi bir türden oluşan `x` ve `y` kordinatlarını tutan `Nokta<T>` yapısı aşağıda örneklenmiştir:

```Rust
struct Nokta<T> {
    x: T,
    y: T,
}

fn main() {
    let tamsayi = Nokta{x: 5, y: 10};
    println!("Nokta: ({}, {})", tamsayi.x, tamsayi.y);  // Nokta: (5, 10)
    
    let kesirli = Nokta{x: 3.2, y: 2.5};
    println!("Nokta: ({}, {})", kesirli.x, kesirli.y);  // Nokta: (3.2, 2.5)
}
````
Genellenmiş işlevlerde olduğu gibi; yapı tanımında bildirilen tür parametresi `<T>`' nin bir kez kullanılması, yapının tüm alanlarının aynı türden oluşacağını gösterir. `let tamsayi = Nokta{x: 5, y: 10.7};` şeklinde oluşturulan bir yapı örneği bu programın hata üretmesine sebep olacaktır. 

Farklı türden alanlara sahip bir yapıya ihtiyaç duyulduğunda, bu türlerin yapı tanımında bildirilmesi yeterlidir. Ancak yapı tanımında çok sayıda tür parametresinin kullanılması kodun okunmasını zorlaştırır. Bir yapı tanımında çok sayıda genel türe ihtiyaç duyuluyorsa belki de kodun küçük parçalar halinde yeniden tasarlanması fikri üzerinde düşünülmelidir:

```Rust
struct Nokta<T, U> {
    x: T,
    y: U,
}

fn main() {
    let tamsayi = Nokta{x: 1, y: 7};
    println!("Nokta: ({}, {})", tamsayi.x, tamsayi.y);  // Nokta: (2, 7)
    
    let kesirli = Nokta{x: 3.0, y: 9.5};
    println!("Nokta: ({}, {})", kesirli.x, kesirli.y);  // Nokta: (3.0, 9.5)
    
    let karisik = Nokta{x: 6, y: 3.1};
    println!("Nokta: ({}, {})", karisik.x, karisik.y);  // Nokta: (6, 3.1)
}
````

Genellenmiş yapılar için uygulama eklenirken tür parametreleri `impl` anahtar kelimesinden sonra belirtilmelidir:

```Rust
struct Nokta<T, U> {
    x: T,
    y: U,
}
// Parantez gösterimi
impl<T, U> Nokta<T, U> {
    fn yeni(x: T, y: U) -> Self {
        Self {
            x,
            y,
        }
    }
    fn degistir<V, W>(self, oteki: Nokta<V, W>) -> Nokta< T, W> {
        Nokta {
            x: self.x,
            y: oteki.y,
        }
    }
}

fn main() {
    let tamsayi = Nokta::yeni(5,7);
    println!("Nokta: ({}, {})", tamsayi.x, tamsayi.y);  // Nokta: (5, 7)
    
    let dizge = Nokta::yeni("Merhaba!", 'p');
    println!("Nokta: ({}, {})", dizge.x, dizge.y);      // Nokta: (Merhaba!, p)
    
    let donustur = tamsayi.degistir(dizge);
    println!("Nokta: ({}, {})", donustur.x, donustur.y);// Nokta: (5, p)
}
````
### Genellenmiş enum
Yapılarda olduğu gibi, genellenmiş veri türlerini varyantlarında tutabilen `enum` türlerinden de yararlanabiliriz. Rust standart kitaplığında daha önceden tanımlanmış özel türlerden `Option<T>` ve `Result<T>` türleri bu konuya oldukça iyi birer örnektir:

```Rust
enum Option<T> {
    Some(T),
    None,
}

enum Result<T, E> {
    Ok(T),
    Err(E),
}
````

> ⭐️ Yukarıda bulunan [`Option`](https://doc.rust-lang.org/std/option/index.html) ve [`Result`](https://doc.rust-lang.org/std/result/index.html) türleri, Rust standart kitaplığında yer alan, önceden tanımlanmış genellenmiş özel türlerdir.

   - Bir **`Optional` değer** in içeriği ya doludur bu durum **`Some`** ile ifade edilir, ya da dolu değildir ki bu durumda **`None`** döndürülür.
   - Bir **`Result`** ise ya başarılıdır ve **`Ok`** değerini, ya da başarısızdır ve **`Err`** içeriğini döndürür.  
    
`Some` değerine sahip olan `Option<T>`, soyut kavramları ifade etmekte oldukça yararlıdır. `Option<T>` genel bir türü ifade ettiğinden bu soyutlama pekçok veri türüyle kullanılır:

```Rust
fn uye_numarasini_getir(uye_adi: &str) -> Option<usize> {
    // Sistemde üye numarası mevcutsa uye_no'yu buna ayarla
    return Some(uye_no);
    // değilse
    None
}
````

Yukarıdaki işlevde dönüş türü `usize` olarak değil `Option<usize>` olarak ayarlanmıştır. Bu ayarlamayla işlevden `uye_no` yerine `Some(uye_no)` döndürülür. Böylece kullanıcıya ait tanıtıcı numara sistemde kayıtlıysa `Some(uye_no)` bu değere ayarlanarak işlevden döndürülecek, değilse dönüş türü `None` olarak ayarlanacaktır:

```Rust
struct Gorev {
    baslik: String,
    gorevli: Option<Kisi>,
}
````

Görev adlı yapıda ise görev için herhangi bir görevli atanmamış olduğundan `gorevli:Kisi` yerine `Option<Kisi>` seçeneği kullanılmaktadır. İşlevlerden geri dönüş değeri olarak `Option<T>` kullanıldığında, dönüş değerinin çağrı esnasında yakalanabilmesi için desen eşleştirmesi kullanılır. 

```Rust
fn uye_numarasini_getir(uye_adi: &str) -> Option<usize> {
    // Sistemde üye numarası mevcutsa uye_no'yu buna ayarla
    let uye_no = 3;
    return Some(uye_no);
    // değilse
    None
}

fn main() {
   let uye_adi = "isimsiz";
   
   match uye_numarasini_getir(&uye_adi) {
       None => println!("Üye bulunamadı"),
       Some(i) => println!("Üye numarası: {}", i)
   }
}
// Üye numarası: 3
````

Duruma göre ya başarılı `Ok` ya da başarısız `Err` değer döndüren Result<T, E> ise iki genel türden oluşur: 

```Rust
enum Result<T, E> { 
    Ok(T), 
    Err(E), 
}
````

Bu tanım, `Result<T, E>` türünü herhangi bir yerde kullanılması için uygun hale getirir:

```Rust
fn dosyadaki_kelime_adedini_bul(dosya_adi: &str) -> Result<u32, &str> {
    // Dosya sistemde bulunamıyorsa hata döndür
    return Err("Dosya bulunamıyor!")
    // Dosyaya erişildiyse kelime adedini döndür
    // let mut kelime_adedi: u32;
    Ok(kelime_adedi)
}
````

Yukarıdaki işlevde `Result<T, E>` türünde enum kullanılarak programın bulunamayan dosya için  panik üreterek sonlandırılması yerine `Err("Hata mesajı")` üreterek sonlandırılması sağlanmıştır. Artık ilgili dönüş değerini elde etmek için desen eşleşmesini kullananabiliriz:

```Rust
fn main() {
    let mut dosya_adi = "dosya.txt";
    
    match dosyadaki_kelime_adedini_bul(dosya_adi) {
        Ok(i) => println!("Kelime adedi: {}", i),
        Err(e) => println!("Hata: {}", e)
    }
}
````

📖  `Option` türü, değer bulunmama olasılığını ifade etmek için Rust’un tür sistemini kullanmanın bir yoludur. `Result` ise hata olaslığını ifade eder.

>🔎 `Option` ve `Result` türleriyle ilgili birçok yararlı metod uygulanmıştır. Daha fazla bilgi Rust belgelerinin [std::option::Option](https://doc.rust-lang.org/std/option/enum.Option.html) ve [std::result::Result](https://doc.rust-lang.org/std/result/enum.Result.html) başlıklarında bulunabilir.

⭐️ Ayrıca, `Option` ve `Result` türlerine ilişkin daha fazla pratik örneğe Rust belgelerinde yer alan [Hata İşleme](https://doc.rust-lang.org/book/ch09-00-error-handling.html) bölümünden ulaşabilirsiniz.

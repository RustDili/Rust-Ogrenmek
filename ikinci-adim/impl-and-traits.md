# Uygulamalar ve Özellikler
💡 **C** benzeri yapılar bölümünde bu tür yapıların nesne yönelimli dillerdeki sınıflara benzediğini, ancak metotlarının olmadığından bahsetmiştik. Rust' ta yapı ve `enum` türlerinin metotlarını tanımlamak için `impl` yani uygulamalar kullanılır.

💡 Özellikler `trait` ise nesne yönelimli dillerde bulunan **arayüzler**e benzerler ve bir türün sağlaması gereken işlevleri tanımlamak için kullanılırlar. Tek bir tür için çok sayıda uygulama tanımlanabildiği gibi bu özellikler varsayılan metot uygulamalarını da içerebilir. 

⭐️️ Ancak bu türler uygulanırken varsayılan metotlar `override` ile bastırılarak geçersiz kılınabilir.

## Özellik kullanmayan bir uygulama örneği
💡 Kullanıcı tarafından Rust’ın `i8`, `f64` gibi hemen hemen her yerleşik türü için yeni özellikler tanımlanıp uygulanabilir. Benzer şekilde kullanıcı tarafından oluşturulan yeni türler için de yerleşik özellikler uygulanabilir. Ancak kullanıcılar yerleşik özellikleri zaten yerleşik durumdaki türlere yeniden uygulayamazlar:

```Rust
struct Oyuncu {
    ilk_adi : String,
    son_adi : String,
}

impl Oyuncu {
    fn tam_adi(&self) -> String {
        format!("{} {}", self.ilk_adi, self.son_adi)
    }
}

fn main() {
    let bas_rol = Oyuncu {ilk_adi: String::from("Reha"),
                            son_adi: "Özcan".to_string() };
    
    println!("Baş rol oyuncusu: {}", bas_rol.tam_adi());
    // Baş rol oyuncusu: Reha Özcan
}
````

⭐️ Her uygulamanın kendi türü ile aynı sandığın içinde yer almasına dikkat edin.

## Varsayılan metodu bulunmayan uygulama ve özellikler
```Rust
struct Oyuncu {
    ilk_adi : String,
    son_adi : String,
}

trait TamAdi {
    fn tam_adi(&self) -> String;
}

impl TamAdi for Oyuncu {
    fn tam_adi(&self) -> String {
        format!("{} {}", self.ilk_adi, self.son_adi)
    }
}

fn main() {
    let yan_rol = Oyuncu {ilk_adi: String::from("Selin"),
                            son_adi: "Tekman".to_string() };
    
    println!("Yan rol oyuncusu: {}", yan_rol.tam_adi());
    // Yan rol oyuncusu: Selin Tekman
}
````

🔎 Özellikler, işlevler dışında diğer türler hatta sabitleri de içerebilir.

## Varsayılan metoda sahip uygulama ve özellikler
```Rust
trait Foo {
    fn bar(&self);
    fn baz(&self) {
        println!("Baz işlevi çağrıldı!");
    }
}
````

⭐️ Yukarıdaki örnekten de anlaşılacağı gibi metotlar özel bir ilk parametre olan `self` yani türün kendisine sahiptirler. Bu parametre duruma göre `self`, `&self` ya da `&mut self` şeklinde olabilir. 
- Değer belleğin stack bölümünde depolanıyorsa `self` ile **değişkenin mülkiyeti** dahil kendisi, 
- Değer heap üzerinde depolanan değişmez bir referans ise `&self` ile yapılan **değişmez bir başvuru**,
- Değer heap üzerinde depolanan değişebilir bir referans türündeyse `&mut self` ile yapılan **değişebilir bir başvuru** temsil edilir.

## İlişkili işlevlere sahip uygulamalar
Birçok programlama dili statik işlevleri destekler. Bunlar kullanılabildiğinde bir nesne oluşturulmaksızın doğrudan sınıf içinden çağrılabilirler. Rust'ta bu şekilde kullanılan işlevlere **ilişkili İşlevler** denir. Bu statik işlevler bir yapı içinden çağrılırken `Kisi::new(“Ali Veli”);` söz ifadesinde olduğu gibi `::işlev()` şeklindeki söz dizimi uygulanır:

```Rust
struct Oyuncu {
    ilk_adi : String,
    son_adi : String,
}

impl Oyuncu {
    fn yeni(ilk_adi: String, son_adi: String) -> Oyuncu {
        Oyuncu {
            ilk_adi : ilk_adi,
            son_adi : son_adi,
        }
    }
    fn tam_adi(&self) -> String {
        format!("{} {}", self.ilk_adi, self.son_adi)
    }
}

fn main() {
    let figuran = Oyuncu::yeni( String::from("Serkan"),
                        "Ortaç".to_string()).tam_adi();
    
    println!("Figuran: {}", figuran);
    // Figuran: Serkan Ortaç
}
````

⭐️ Örnekteki `yeni()` metodu için `::` gösterimi, `tam_adi()` metodu için de **`.`** gösteriminin kullanıldığına dikkat edin.  

🔎 Aynı örnekte bulunan, `yeni()` ve `tam_adi()` metodlarını iki ifade olarak ayrı ayrı kullanmak yerine,  `nesne_ornegi.nokta_ekle(2).nokta_sayisi_bul();` gibi metod zinciri şeklinde ifade edebiliriz.

## Özellik ve genelleme
Genellenecek özelliğin adı `From<T>` söz dizimine uygun şekilde, işlev genellemelerind eolduğu gibi belirteçten önce yazılır:

```Rust
trait From<T> {
    fn from(T) -> Self;
}
    impl From<u8> for u16 {
        // işlemler...
    }
    impl From<u8> for u32 {
        // işlemler...
    }
````

## Özellikler ve Kalıtım
Özelliklerin şablonik yapıları diğer özellikler tarafından da miras alınarak kullanılabilir. 

```Rust
trait Isci {
    fn tam_adi(&self) -> String;
}
    // Bu özelliği kalıtım yoluyla miras alan diğer özellikler
    trait Sigortali : Isci {  // sadece İsci Özelliğini miras alır
        fn is_tanimi(&self) -> String;
    }
    
    // GocmenIsci ise hem Isci'den hem de Gocmen'den miras alır  
    trait GocmenIsci : Gocmen + Isci { 
        fn ek_vergi(&self) -> f64;
    }
````

## Özellik nesneleri
🔎 Rust statik dağıtımı desteklediği gibi, **özellik nesneleri** adı verilen bir mekanizma aracılığıyla dinamik dağıtımı da destekler. 

>🅆  [Dinamik dağıtım](https://en.wikipedia.org/wiki/Dynamic_dispatch) çalışma zamanında hangi polimorfik işlemin *(metod veya işlev)* uygulanacağını seçme işlemidir.

```Rust
trait SesUret {
    fn ses_al(&self) -> String;
}

struct Cat {
    ses: String,
}
    impl SesUret for Cat {
        fn ses_al(&self) -> String {
            self.ses.clone()
        }
    }

struct Bell {
    ses: String,
}
    impl SesUret for Bell {
        fn ses_al(&self) -> String {
            self.ses.clone()
        }
    }

fn ses_ver<T: SesUret>(t: &T) {
    println!("{}", t.ses_al());
}

fn main() {
    let kedi_sesi = Cat { ses: "Miyawww".to_string() }; 
    let kapi_sesi = Bell{ ses: "Ding Dong". to_string() };
    
    ses_ver(&kedi_sesi);    // Miyawww
    ses_ver(&kapi_sesi);    // Ding Dong
}
````

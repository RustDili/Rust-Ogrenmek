# Enum türü
⭐️ Farklı varyantların nümeratik sırayla bir araya getirildiği ve olası değerlerlerinin tuttulduğu bir türdür:

```Rust
#[derive(Debug)]
enum Gunler {
    Pazartesi,
    Sali,
    Carsamba,
    Persembe,
    Cuma,
    Cumartesi,
    Pazar,
}
````

Örneğimizdeki `Gunler` bir `enum`dur ve süslü parantezler içinde yer alan Pazartesi, Sali, Carsamba, Persembe, Cuma, Cumartesi, Pazar günleri ise **bu enum’un varyantlarıdır.**

⭐️ Bir `enum`’un sahip olduğu varyantlara **`::`** gösterimi, yani `Gunler::Pazartesi` şeklindeki söz dizimiyle erişilir:  

```Rust

fn main() {
    let haftanin_ilk_is_gunu = Gunler::Pazartesi;
    print!("Haftanın ilk iş günü {:?}'dir.", haftanin_ilk_is_gunu);
}
// Haftanın ilk iş günü Pazartesi'dir.
````

⭐️ Bir `enum` türünün varyantları:
  
  - Verisiz olarak yani *(Birim varyant)* şeklinde,
  - İsimsiz sıralı veriler halinde, yani çokuzlu varyantı şeklinde *(Çokuzlu varyant)* verilerden,
  - İsimlendirilmiş varyantlar, yani yapı alanlarına benzer biçimde *(Yapısal varyant)*

oluşabilir. 

```Rust
#[derive(Debug)]

enum Denetim {
    Basarili,                               // Birim Varyantlı
    Dikkat {kategori: i32, mesaj: String }, // Yapısal Varyantlı
    Hata(String),                           // Çokuzlu Varyabtlı
}

fn denetim_mesaji_yazdir(dm: Denetim) {
    match dm {
        Denetim::Basarili                   => println!("Form sorunsuzca gönderildi"),
        Denetim::Dikkat{kategori, mesaj}    => 
            println!("Uyarı: {}, {}", kategori, mesaj),
        Denetim::Hata(msj)                  => println!("Hata mesajı {}", msj),
    };
}
    
fn main() {
    let mut form_kontrol = Denetim::Basarili;
    denetim_mesaji_yazdir(form_kontrol);
    // Form sorunsuzca gönderildi
    
    form_kontrol = Denetim::Dikkat { 
        kategori: 2, mesaj: String::from("O alan doldurulacak!")};
    denetim_mesaji_yazdir(form_kontrol);
    // Uyarı: 2, O alan doldurulacak!
    
    form_kontrol = Denetim::Hata(String::from("Bağlantı hatası!"));
    denetim_mesaji_yazdir(form_kontrol);
    // Hata mesajı Bağlantı hatası!
}
````
## Sonraki konuya geçmeden önce...
Aşağıda `enum` türünün daha iyi anlaşılabilmesi için üzerinde oynayabileceğiniz birkaç örnek yer almaktadır. Bu örneklerden ilki oldukça basit hazırlanmış ve bir oyun karakterinin gideceği dört ana yönün hangisi seçildiyse onu ekranqa yazdıran [Dört yöne gidiş örneği](https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=77961c70cdc86bb7f8d65a5707dfd036)'dir.

Diğer örnekte ise haftanın günlerini varyantlarında tutan ve eklenilen özellik yardımıyla [tatil günlerini iş günlerinden ayıran](https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=f7df6f69b2b3158b4d9c445cf94c74cc) bir `enum` türüne yer verilmektedir.

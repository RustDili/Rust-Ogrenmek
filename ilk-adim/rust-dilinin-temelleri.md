# Neden Rust?
## Rust'ın Tarihi 
Rust Mozilla çalışanı **Graydon Hoare** tarafından kişisel bir proje olarak tasarlandı ve geliştirildi. Adını 2010 yılında Mozilla'nın 2009 yılında projeye sponsor olmasının hemen ardından duyurmasına rağmen ilk kararlı sürümü olan **Rust 1.0**, 15 Mayıs 2015'te piyasaya sürüldü.

## Başlangıç Hedefleri
Rust, eşzamanlı, güvenli ve yüksek performanslı sistemler oluşturulabilen iyi bir programlama dili olmayı hedefler.
> "Rust üç hedefe odaklanan bir sistem programlama dilidir: güvenlik, hız ve eşzamanlılık."  
> \_\_ Rust Dökümanından

Rust, arka uçta [LLVM](https://en.wikipedia.org/wiki/LLVM) kullanan genç ve modern bir derlemeli programlama dili olmasına rağmen, emir kipli prosedürel, eşzamanlı, nesne yönelimli ve saf işlev biçimlerini destekleyen programlama paradigmalarını barındıran bir programlama dilidir. Bunun yanı sıra, hem statik hem de dinamik tarzlarda türden bağımsız programlama ve metaprogramlamayı destekler.    

> 🔎 Rust’un eşsiz ve çekici özelliklerinden biri de bellek güvenliğini sağlamak için kullanılan [**Mülkiyet Kavramı**](https://github.com/RustDili/Rust-Mulkiyet-Kavrami)dır. Bunun için Rust iyileştirilmiş bellek işaretçileri aracılığıyla [**Referanslar ve Borçlanma**](https://github.com/RustDili/Rust-Mulkiyet-Kavrami/blob/master/referans-ve-borclanma.md) kuralları üzerinden derleme zamanında bellek işaretçilerin erişim sınırlarını denetler. Ardından değişkenlerin [**Yaşam Süreleri**](https://github.com/RustDili/Rust-Mulkiyet-Kavrami/blob/master/yasam-suresi.md)ni kontrol ederek otomatik derleme zamanı bellek yönetimini gerçekleştirir.
## Etkiler
Rust'ın tasarım öğeleri çok çeşitli kaynaklardan gelmektedir:
* Soyut Makine Modeli: **C**
* Veri Türleri: **C, SML, OCaml, Lisp, Limbo**
* Opsiyonel Bağlamalar: **Swift**
* Temiz Makrolar: **Scheme**
* İşlevsel Programlama: **Haskell, OCaml, F#**
* Öznitellikler: **ECMA-335**
* Bellek Modeli ve Bellek Yönetimi: **C++, ML Kit, Cyclone**
* Tür Sınıfları: **Haskell**
* Sandık:**Assembly in the ECMA-335 CLI model**
* Kanallar ve Eşzamanlılık: **Newsqueak, Alef, Limbo**
* Mesaj iletme ve iş parçacığı hatası: **Erlang**
 
Rust'ta otomatik çöp toplama sistemi **(GC)** bulunmaz. Bunun yerine Rust derleyicisi kodu derleme zamanında gözlemleyerek ve C, C++ gibi programlama dillerinde yapılabilecek birçok hata türünün önlenmesine yardımcı olur.

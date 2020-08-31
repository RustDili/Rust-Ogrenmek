# Neden Rust?
## Rust'ın Tarihi 
Başlangıçta Rust Mozilla çalışanı **Graydon Hoare** tarafından kişisel bir proje olarak tasarlandı ve geliştirildi. Adını Mozilla'nın 2009 yılında projeye sponsor olmasının ardından 2010 yılında duyurmasına rağmen ilk kararlı sürümü olan **Rust 1.0**, 15 Mayıs 2015'te piyasaya sürüldü.

## Başlangıç Hedefleri
Rust'ın hedefinde, olabildiğince eşzamanlı, güvenli ve yüksek performanslı sistemler oluşturulabilen iyi bir programlama dili olmak vardır.
> "Rust üç hedefe odaklanan bir sistem programlama dilidir: güvenlik, hız ve eşzamanlılık."  
> \_\_ Rust Dökümanından

Rust, arka uçta [LLVM](https://en.wikipedia.org/wiki/LLVM) kullanan çok genç ve modern bir derlemeli programlama dili olmasının yanı sıra; emir kipli prosedürel, eşzamanlı, nesne yönelimli ve saf işlev biçimlerini destekleyen birden fazla programlama paradigmasına sahip bir programlama dilidir. Ayrıca, hem statik hem de dinamik tarzlarda jenerik programlama ve metaprogramlamayı destekler.    

> 🔎 Rust’un en eşsiz ve çekici özelliklerinden biri de bellek güvenliğini sağlamak için kullanılan [**Mülkiyet Kavramı**](https://github.com/rust-lang-tr/site/blob/master/source/docs/c1.ownership.html)dır. Bunun için Rust eniyileştirilmiş bellek işaretçileri oluşturur ve [**Referanslar ve Borçlanma**](https://github.com/rust-lang-tr/site/blob/master/source/docs/c2.borrowing.html) kullanımı ile derleme zamanında bellek işaretçilerin sınırlı erişimlerini kontrol eder. Ardından değişkenlerin [**Yaşam Süreleri**](https://github.com/rust-lang-tr/site/blob/master/source/docs/c3.lifetimes.html)ni kontrol ederek otomatik derleme zamanı bellek yönetimi yapar.
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
* Mesaj iletme ve işlik hatası: **Erlang**
v.b. 
Varsayılan olarak Rust'ta otomatik çöp toplama sistemi **(GC)** bulunmaz. Bunun yerine Rust derleyicisi kodu derleme zamanında gözlemleyerek ve C, C++ gibi programlama dillerinde yapılabilecek birçok hata türünün önlenmesine yardımcı olur.

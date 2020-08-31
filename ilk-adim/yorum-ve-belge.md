# Yorum Satırları ve Kodun Belgelenmesi
## Yorum Satırları
Yorum satırları programların olmazsa olmazlarıdır. Rust birkaç farklı yorum satırını destekler.

```Rust
// Bu bir satır içi yorumdur
/* Bu ise sonlandırma imi bildirilene kadar tüm bloğu kapsar */
/* Rust /* iç içe yapılan blok */ yorumlarını da destekler.*/
````

💡 Her ne kadar blok yorumlarını destekleniyorsa da, geleneksel olarak satır içi yorum kullanılması tercih edilmektedir.

## Belgelendirilen Yorum Satırları
```Rust
/// Satır içi yorumlar: Bir alttaki öğeyi belgeler
/** Blok Yorumu: Bir alttaki öğeyi belgeler */
//! Satır içi yorumlar: Alt parçadaki öğeyi kapsam boyunca belgeler
/*! Blok Yorumu: Alt parçadaki öğeyi kapsam boyunca belgeler !*/
````
Döküman yorumları `markdown` gösterimlerini destekler. Bu yorum satırlarına sahip programlar `cargo doc` komutu kullanılarak `HTML` biçiminde belgelendirilir. Aşağıdaki örnekte her iki doküman yorum seti aynı modülü belgelemek için kullanılıyor. Aralarındaki farkı  inceleyelim:
```Rust
/// Modül testlerini içerir 
mod test { 
  // ... 
} 
````
Dikkat ederseniz yukarıdaki yorum seti modülün önüne getirilirken, altta bulunan yorum setiyse modül içinde yer almaktadır.

```Rust
mod test { 
  //! Burası da Modül testlerini içerir 
  // ... 
}
````

💡 Sandık ve modül düzeyinde belgeleme yapmak için sadece `//!` kullanmak yeterlidir. Eğer ilk örnekteki gibi mod blokları belgelenecekse   `/// yorum satırı` mod bloğunun dışında kullanılır.

## Belge Nitelikleri
Kodların belgelenmesinde döküman niteliklerinden de yararlanabiliriz. Aşağıdaki örnekte bulunan her yorum ilgili verinin niteliklerine eşdeğerdir.

```Rust
/// Foo 
#[doc="Foo"] 

//! Foo 
#![doc="Foo"]
````

🔎 Bir [Öznitelik](https://doc.rust-lang.org/reference.html#attributes); ad, kural, dil ve derleyici sürümüne göre yorumlanan genel, serbest biçimli bir metadatadır. Herhangi bir öğe bildiriminin kendisine uygulanan bir özelliği olabilir. 
Daha fazla bilgi için Rust kitabının 14. bölümünde yer alan [Bir Sandığı Yayınlamak](https://doc.rust-lang.org/book/ch14-02-publishing-to-crates-io.html) adlı konu başlığını veya [Türkçe Belgelerini](https://github.com/rust-lang-tr/dokuman/blob/master/ceviriler/ch14-00-more-about-cargo.md) inceleyebilirsiniz.

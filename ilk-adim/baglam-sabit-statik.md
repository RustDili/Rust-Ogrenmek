# Değişken bağlama, sabit ve statik öğeler
⭐️ Rust programlama dilinde değişkenler aksi belirtilmediği sürece değişmez olarak kabul edildiğinden, değişken kavramıyla aslında **ilklendirilme bağlamına** işaret edilir. Eğer bu bağlamın değişebilir olması isteniyorsa değişken bildirilirken `mut` anahtar sözcüğünün kullanılması gereklidir.

⭐️ Rust statik olarak tasarlanmış bir dil olduğundan, veri türlerini derleme zamanında kontrol eder. Bu nedenle bağlam tanımlarında türün bildirimi zorunlu tutulmaz. Tür bildirilmediğinde derleyici, ifede ve kullanımı kontrol ederek en olası veri türünü belirlemeye çalışır. 
Bununla birlikte eğer **sabit** ya da **statik** bir bağlam tanımlanıyorsa o bağlamın türü mutlaka bildirilmek zorundadır. Türler `(:)` iki noktanın ardından bildirilir. 

### Değişken bağlama
Değişken ifade edilirken bağlayıcı olarak genellikle `let` anahtar sözcüğü kullanılır. Bu yolla programda kullanılan isimleri bir değer yada işleve bağlamak mümkün olur. 

```Rust
fn main() {
  let a = true; 
  println!("a değişmezi: {}", a);
  
  let b: bool = false; 
  println!("b değişmezi: {}", b);
  
  let (x, y) = (1, 5); 
  println!("x: {}, y: {}", x, y);
  
  let mut z = 5; 
  println!("z değişkeni: {}", z);
  
  z = 6;
  println!("z değişimi: {}", b);
}
````

Bağlayıcı ifadenin sol tarafı bir model olduğundan `let` anahtar sözcüğü yardımıyla çok sayıda isim bir dizi değere veya işlev değerine bağlanabilir.

```Rust
let (x, y, z ) = (1, 5, 10); 
  println!("x: {}, y: {}, z: {}", x, y, z);
````
Çoklu tanımlanan değişkenlerin ileride değiştirilmesi gerekiyorsa
```Rust
let (mut x, mut y,  mut z) = (1, 5, 10);
x += x; y += y; z += z;
println!("x:{}, y:{}, z:{}", x, y, z); // x:2, y:10, z:20
````
### Sabitler
Sabitleri tanımlamak için `const` anahtar sözcüğü kullanılır. Değişken bağlamanın aksine sabitler tür açıklaması yapılarak bildirilir. 
Programın ömrü boyunca yaşayan bu değişken türleri kullanıldıkları her yerde etkin biçimde devreye girdiklerinden bellekte sabit bir adresleri bulunmaz. Bu nedenle aynı sabite yapılan farklı başvuruların aynı hafıza adresine erişeceği garanti edilmez.

```Rust
fn main() {
  const S: i32 = 5;
  println!("S sabitinin değeri: {}," S);
}
````
Sabitler sadece varsayılan olarak değil her zaman değişmez olduklarından `mut` anahtar kelimesini sabit bildirimlerinde kullanamazsınız.
### Statikler
Bir küresel değişken türü tanımımlanırken `static` anahtar sözcüğü kullanılır. Bu türler sabitlere benzemekle birlikte sabit bir adresleri bulunur. Bu türlerin her değeri için sadece bir örnek olabilir ve bu örnekler kullanılırken satır içlerine alınmazlar.

```Rust
fn main() {
  static S: i32 = 10;
  println!("S global değeri: {}," S);
}
````
Statik öğeler genellikle kod dosyasının en üstünde işlevlerin dışında bulundurulurlar. Sabit bir türün hafıza adresine nadiren başvurulduğundan, zorunlu olmadıkça statik tür yerine sabit öğelerin kullanılması yeğlenir. Bu tercih programın optimizasyonu için de önemlidir.
```Rust
static NAME: &'static str = "Apache";
print!("{}", NAME);
````

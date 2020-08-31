# Kontrol akışı
Bir koşulun doğru olup olmadığına bağlı olarak bazı kodların çalıştırılmasına karar vermek ya da bir koşul doğru olduğu sürece bazı kodları tekrar tekrar çalıştırmak çoğu programlama dilinin olmazsa olmazıdır. 

## If - else if - else
```Rust
fn main() {
    let olcu = 7; 

    if olcu < 5 { 
        println!("Küçük boy"); 
    } else if olcu < 10 { 
        println!("Orta boy"); 
    } else { 
        println!("Büyük boy"); 
    }
}
// Orta boy
````
`if` koşulu bir programın akışını kontrol etmeyi sağlayan en yaygın yapılardandır. Yukarıda en basit haliyle yer alan kod aşağıdaki gibi de yazılabilir:

```Rust
fn main() {
    let beden = 7; 
    let beden_aciklamasi; 

    if beden < 5 { 
        beden_aciklamasi = "Küçük boy"; 
    } else if beden < 10 { 
        beden_aciklamasi = "Orta boy"; 
    } else { 
        beden_aciklamasi = "Büyük boy"; 
    } 

    println!("Müşterinin tercihi: {}", beden_aciklamasi);
}
// Müşterinin tercihi: Orta boy
````
⭐️  If-else if-else akışında eğer dönüş türü bir ifadeyse, her kontrol bloğunda döndürülen dönüş türünün `text, text` veya `bool, bool` gibi aynı türden olması beklenir. 
Yukarıdaki örnekler aşağıdaki gibi iyileştirilebilirler:

```Rust
fn main() {
    let beden = 7;
    
    let beden_aciklamasi = if beden < 5 {
        "Küçük boy" // ⭐️ sonlandırma için ; gerekli değildir.
    } else if beden < 10 {
        "Orta boy"
    } else {
        "Büyük boy"
    }; // kapsamdan çıkıldığında 
    
    println!("Müşterinin tercihi: {}", beden_aciklamasi);
}
// Müşterinin tercihi: Orta boy
````
Yukarıdaki örnekte ifadenin dönüş değeri bir değişkende depolandığından tüm koşullar denetlenip kapsamdan çıkılana kadar noktalı virgülün 
kulanılmasına gerek yoktur. Aşağıda ise aynı ifade tek satırda birleştirilmektedir:

```Rust
fn main() {
    let beden = 7;
    
    let depo_kontrol = if beden < 15 { true } else { false };
    println!("Elimizde 15 numaradan küçük beden var mı: {}", depo_kontrol);
}
// Elimizde 15  numnardadan küçük beden var mı: true
````
## match
Kontrol akışını desen eşleştirmesi yoluyla yapar:

```Rust
fn main() {
    let beden = 20;
    
    let beden_olcusu = match beden {
        10 => "S",                  // 10 Kontrol edilir
        17 | 18 => "M",             // 17 ve 18 kontrol edilir
        19..= 21 => "L",            // 19’dan 21’e (19,20,21) Kontrol edilir
        22 => "XL",
        _ => "Standart dışı ölçü",
    };
    println!("Müşterinin tercihi: {}", beden_olcusu);
}
// Müşterinin tercihi: L
````
Eşleştirmede kullanılan alt çizgi **`_`** bir değeri yok saymak için kullanılır. Her şeyle eşleşen ancak bir değişkene bağlanmayan bu işleç, desen ile eşleştirme yapılırken bir çeşit varsayılan durum oluşturmak amacıyla son karşılaştırma ifadesinde kullanılır:

```Rust
fn main() {
    let paket_turu = false;
    
    let secim = match paket_turu {
        true => "Tam paket seçildi",
        false => "Kısıtlı paket",
    };
    println!("Müşterinin tercihi: {}", secim);
}
// Müşterinin tercihi: Kısıtlı paket
````

Üstteki eşleştirme `boolean` değer kontrolüne dayandığından, sadece olası iki durum karşılaştırılıyor. Bu nedenle **`_`** işlecinin temsil ettiği varsayılan duruma gerek duyulmaz:

```Rust
fn main() {
    let alinin_notu: u8 = 30;
    let mertin_notu: u8 = 25;
    
    let karne = match (alinin_notu, mertin_notu) {
        (50, 50) => "Her ikisi de başarılı",
        (50, _) => "Ali oldukça başarılı",
        (_, 50) => "Mert oldukça başarılı",
        (x, y) if x > 25 && y > 25 => "İkisi de fena değil",
        (_, _) => "Daha sıkı çalışmaları gerek!"
    };
    println!("Değerlendirme sonucu: {}", karne);
}
// Değerlendirme sonucu: Daha sıkı çalışmaları gerek
````
## while
Bildirilen koşul sağlandığı sürece döngünün devam etmesini sağlayan anahtar kelimedir. Döngüye girilmeden önce koşul çalıştırılır ve eğer koşul doğru olarak değerlendirilirse döngü içinde yer alan ifadeler işletilir, aksi durumda döngüden çıkılır:

```Rust
fn main() {
    let mut deger = 1;
    
    while deger <= 10 {
        print!("{} ", deger);
        deger += 1;
    }
}
// 1 2 3 4 5 6 7 8 9 10 
````
Diğer programlama dillerinden alışkın olduğumuz **`++`** arttırma ve **`--`** eksiltme işleçleri Rust'ta bulunmaz. Yerine arttırma ya da eksiltme işlemleri için bileşik atama oparatörleri kullanılır. 
Aşağıda ise `break` ve `continue` anahtar kelimelerinin kullanılışı örneklenmektedir:

```Rust
fn main() {
    let mut degisen = 0;
    
    while degisen < 8 {
        if degisen == 2 {
            println!("Bu değeri atla: {}", degisen);
            degisen += 1;
            continue;
        } else if degisen == 6 {
            println!("{} Geldiği için döngüden çıkılıyor", degisen);
            break;
        }
        
        println!("Anlık değer: {}", degisen);
        degisen += 1;
    }
}
// 6 Geldiği için döngüden çıkılıyor
````
#### Outer break
```Rust
fn main() {
    let mut birinci = 1;
    
    'outer_while: while birinci < 6 {                               // outer_while etiketi ile ayarlanır
        let mut ikinci = 1;
        
        'inner_while: while ikinci < 6 {
            println!("Anlık değerler: [{}][{}]", birinci, ikinci);
            
            if birinci == 2 && ikinci == 2 { break 'outer_while; }  // outer_while sonlandırılır
                ikinci += 1;
        }
            birinci += 1;
    }
}
// Anlık değerler: [1][1]
// Anlık değerler: [1][2]
// Anlık değerler: [1][3]
// Anlık değerler: [1][4]
// Anlık değerler: [1][5]
// Anlık değerler: [2][1]
// Anlık değerler: [2][2]
````
## loop
Rust'ta desteklenen en basit döngü türü olup `break` anahtar sözcüğü ile kesilmediği ya da programdan çıkılmadığı sürece sonsuza dek çalıştırılır:

```Rust
fn main() {
    let mut degisen = 0;
    
    loop {
        if degisen == 0 {
            println!("Atlanan değer. {}", degisen);
            degisen += 1;
            continue;
        } else if degisen == 4 {
            println!("{} Değerine ulaşıldı, döngüden çıkılıyor!", degisen);
            break;
        }
        
        println!("Anlık değer: {}", degisen);
        degisen += 1;
    }
}
// Atlanan değer: 0
// Anlık değer: 1
// Anlık değer: 2
// Anlık değer: 3
// 4 Değerine ulaşıldı, döngüden çıkılıyor!
````

#### Outer break
```Rust
fn main() {
    let mut birinci = 1;
    'outer_loop: loop {
        
        let mut ikinci = 1;
        'inner_loop: loop {
            println!("Anlık değer: [{}][{}]", birinci, ikinci);
            
            if birinci == 2 && ikinci == 2 {
                break 'outer_loop;
            } else if ikinci == 5 {
                break;
            }
            ikinci += 1;
        }
        birinci += 1;
    }
}
// Anlık değer: [1][1]
// Anlık değer: [1][2]
// Anlık değer: [1][3]
// Anlık değer: [1][4]
// Anlık değer: [1][5]
// Anlık değer: [2][1]
// Anlık değer: [2][2]
````

## for
Bu anahtar kelime genellikle **`for-in`** söz dizimi şeklinde koleksiyon öğeleri üzerinde işlem yapmak için kullanılır. Ve döngü **`in`** anahtar sözcüğü ile işaret edilen aralık tüketilene kadar yinelenir:

```Rust
fn main() {
    for i in 0..10 {
        print!("{} ", i);
    }
}
// 0 1 2 3 4 5 6 7 8 9 
````
Örnekte yer alan `for i in 0..10` ifadesi, diğer programlama dillerinde kullanılan for `a = 0; a < 10; a++` ifadesine benzer. Döngü beklendiği gibi `0` ile başlar ve aralığın son değerine ulaştığında durur. Akışın `break` ve `continue` anahtar sözcükleriyle yönlendirilmeleri aşağıda örneklenmiştir:

```Rust
fn main() {
   for i in 0..=9 {
       if i == 2 {
           println!("Atlanan değer: {}", i);
           continue;
       } else if i == 4 {
           println!("{} Geldiği için döngüden çıkılıyor!", i);
           break;
       }
       println!("Anlık değer: {}", i);
   }
}
// Anlık değer: 0
// Anlık değer: 1
// Atlanan değer: 2
// Anlık değer: 3
// 4 Geldiği için döngüden çıkılıyor!
````

Diziler ve vektörler gibi kullanışlı koleksiyonları `for` döngüsü ile işlemek oldukça kolaydır:
```Rust
fn main() {
   let ekip: [&str; 4] = ["Zafer", "Meral", "Hüseyin", "Selda"];
   
   for e in 0..ekip.len() {
       println!("Ekip üye numarası: {} ", e);
   }
   
   for kisi in ekip.iter() {
       println!("Ekip üyesi: {} ", kisi);
   }
}
// Ekip üye numarası: 0 
// Ekip üye numarası: 1 
// Ekip üye numarası: 2 
// Ekip üye numarası: 3 
// Ekip üyesi: Zafer 
// Ekip üyesi: Meral 
// Ekip üyesi: Hüseyin 
// Ekip üyesi: Selda 
````

Yukarıdaki örneği aşağıdaki gibi kısaltarak yazabiliriz:

```Rust
fn main() {
   let ekip: [&str; 4] = ["Zafer", "Meral", "Hüseyin", "Selda"];
   
   for (no, uye) in ekip.iter().enumerate() {
       println!("No: {}, Üye: {}", no, uye);
   }
}
// No: 0, Üye: Zafer
// No: 1, Üye: Meral
// No: 2, Üye: Hüseyin
// No: 3, Üye: Selda
````

#### Outer break
```Rust
fn main() {
    'harici_dongu: for i in 1..6 {
        'dahili_dongu: for k in 1..6 {
            println!("Anlık değer: [{}][{}]", i, k);
            
            if i == 2 && k == 2 {break 'harici_dongu;}
        }
    } 
}
// Anlık değer: [1][1]
// Anlık değer: [1][2]
// Anlık değer: [1][3]
// Anlık değer: [1][4]
// Anlık değer: [1][5]
// Anlık değer: [2][1]
// Anlık değer: [2][2]
````

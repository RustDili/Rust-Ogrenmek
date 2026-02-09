# İşleçler
#### i. Aritmetik işleçler
**`+`, `-`, `/`, `%`** sembolleriyle gösterilirler. Ek olarak **`+`** işleci, dizi ve string birleştirme işlemlerinde kullanılır.

```Rust
let a = 5; 
let b = a + 1;      // 6 
let c = a - 1;      // 4 
let d = a * 2;      // 10 
let e = a / 2;      // ⭐ 2 çünkü değerler i32 
let f = a % 2;      // 1 
let g = 5.0 / 2.0;  //2.5
````

#### ii. Karşılaştırma işleçleri
**`==`, `!=`, `<`, `>`, `<=`, `>=`** sembolleriyle gösterilirler.

```Rust
let a = 1; 
let b = 2; 
let c = a == b;           //false 
let d = a != b;           //true 
let e = a < b;            //true 
let f = a > b;            //false 
let g = a <= a;           //true 
let h = a >= a;           //false 

// 🔎
let i = true > false;     //true 
let j = 'a' > 'A';        //true

println!("c: {:?}", c);   // false
````

#### iii. Mantıksal işleçler
**`!`, `&&`, `||`** sembolleriyle gösterilirler.

```Rust
let a = true; 
let b = false; 
let c = !a;       //false 
let d = a && b;   //false 
let e = a || b;   //true
````

🔎Tamsayılar için `two’s complements` gösteriminde **`!`** işleci bitleri ters çevirir.

```Rust
let a = !-2;    //1 
let b = !-1;    //0 
let c = !0;     //-1 
let d = !1;     //-2
````

#### iv. Bit işlem işleçleri
**`&`, `|`, `^`, `<<`, `>>`** sembolleriyle gösterilirler.

```Rust
let a = 1; 
let b = 2; 

let c = a & b;    //0 (01 && 10 -> 00) 
let d = a | b;    //3 (01 || 10 -> 11) 
let e = a ^ b;    //3 (01 != 10 -> 11) 
let f = a << b;   //4 (a’nın sonuna b’nin değeri kadar 0 sayısı eklenir a-> '01'+'00' -> 100) 
let g = a >> b;   //0 (a’nın sonundan b değeri kadar bit çıkartılır a -> o̶1̶ -> 0)
````

#### v. Atama ve bileşik atama işleçleri
Atama **`=`** işleci bir değere veya işleve isim atamak için kullanılır. Bileşik atama işleçleriyse; artimetik veya bit işlem işleçlerinin atama operatörüyle birleşmesinden oluşur ve: **`+=`, `-=`, `*=`, `/=`, `%=`, `&=`, `|=`, `^=`, `<<=`, `>>=`** sembolleriyle gösterilirler.

```Rust
let mut a = 2; 

a += 5;  
println!("a += 5: {:?}", a);      //2 + 5 = 7

a -= 2;  
println!("a -= 2: {:?}", a);      //7 - 2 = 5

a *= 5;  
println!("a *= 5: {:?}", a);      //5 * 5 = 25
a /= 2;  
println!("a /= 2: {:?}", a);      //25 / 2 = 12.5 değil 12 eder,

a %= 5;  
println!("a %= 5: {:?}", a);      //12 % 5 = 2

a &= 2;  
println!("a &= 2: {:?}", a);      //10 && 10 -> 10 -> 2

a |= 5;  
println!("a |= 5: {:?}", a);      //010 || 101 -> 111 -> 7

a ^= 2;  
println!("a ^= 2: {:?}", a);      //111 != 010 -> 101 -> 5

a <<= 1; 
println!("a <<= 1: {:?}", a);     //'101'+'0' -> 1010 -> 10

a >>= 2; 
println!("a <<= 2: {:?}", a);     //101̶0̶ -> 10 -> 2
````

#### vi. Tür dönüşüm işleci
Tür dönüşüm işlemleri **`as`** anahtar kelimesi kullanılarak gerçekleştirilir.

```Rust
let a = 15; 
let b = (a as f64) / 2.0; // 7.5
````

#### vii. Borçlanma ve dereference işleci
[Yaşam süresi](https://github.com/rust-lang-tr/dokuman/blob/master/mulkiyet-kavrami/yasam-suresi.md) boyunca, bir değişmez ya da değişkenin [mülkiyetini](https://github.com/rust-lang-tr/dokuman/blob/master/mulkiyet-kavrami/mulkiyet.md) ödünç almak için kullanılan **`&`** ve **`mut&`** işleçleri [borçlanma](https://github.com/rust-lang-tr/dokuman/blob/master/mulkiyet-kavrami/borclanma.md) işleçleri olarak bilinirler. Dereference işlemleri için **`*`** işleci kullanılır.
### Sonraki konuya geçmeden önce...
- Aşağıda örnekte her ikisi de `&str` türünden seçilmiş değişkenler `String` türüne dönmüştürülmektedir. 
```Rust
fn main() {
    let (s1, s2) = ("Bir", " şeyler");   // ikisi de &str
    
    // Aşağıdaki her örnekte "Bir şeyler" Dizgesi oluşturulmaktadır.
    
    let  d = String::from(s1) + s2;     // String + &str
    println!("d: {}", d);               // d: Bir şeyler
    
    let mut s = String::from(s1);       // String
    s.push_str(s2);                     // + &str
    println!("s: {}", s);               // s: Bir şeyler
    
    let x = format!("{}{}", s1, s2);    // &str/String + &str/String
    println!("x: {}", x);               // x: Bir şeyler
    
    let y = [s1, s2].concat();          // &str ya da String Dizi
    println!("y: {}", y);               // y: Bir şeyler
}
````
💡 İşlem sonuçlarının kayan noktalı sayı türünden değişkenlerde depolanabilmesi için, işlecin her iki tarafındaki değer türlerinin sonucu depolanacak değişken türünde olması gerektiğini unutmayın.

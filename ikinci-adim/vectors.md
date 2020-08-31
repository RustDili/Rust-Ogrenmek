# Vektörler
Diziler aynı türden oluşan verileri liste halinde bir arada tutan sabit uzunluktaki değişmezlerdir. Onların bu değişmezliği `mut` anahtar sözcüğüyle tanımlanıyor olsalar bile boyutlarının değiştirilmesine yetmez. Dilimler ise dizinin belli bir bölümüne referans verdiklerinden boyutları dinamik olarak değişebilen müstakil veri yapıları değildirler. Rust'ta, aynı türden öğelere sahip ve boyutları değiştirilebilen listeler oluşturulabilmesini sağlayan veri yapılarına vektör denir.

⭐️ Vektörler `Vec<T>` söz dizimiyle ifade edilen jenerik türlerdir. Söz diziminde yer alan `T` depolanacak veri türünü temsil eder. Örneğin `i32` türünde bir vektör basitçe `Vec<i32>` olarak ifade edilir. 

💯 Vektörler boyutları dinamik olarak değişebilen veri türleri olduklarından, öğelerine ait değerler, belleğin `heap` adlı bölümünde kendisi için  özel olarak ayrılmış dinamik bir alanda tutulurlar.

## Boş bir vektör oluşturmak
Boş bir vektörü oluşturmak için türün `new()` metodu ya da `vec!` makrosu kullanılır:

```Rust
  let v: Vec<i32> = Vec::new()  //1. new() işlevi yardımıyla 
  let mv: Vec<i32> = vec![];    //2. vec! makrosu kullanarak
````

Boş vektörün içinde saklanacak tür derleyici tarafından bilinemeyeceğinden, oluşturulurken tür açıklaması eklenir. Öğeleri `mut` anahtar kelimesiyle değişebilir olarak tanımlanan vektör öğeleri kapsam içinde biliniyorsa tür bildirimi atlanabilir:

```Rust
let mut v = Vec::new();
v.push(1); // varsayılan tür i32 olarak çıkarsanı
v.push(2);
println!("v: {:?}", v);
````

## Veri türleriyle oluşturmak
Türü `i32` ve elemanları `.push()` metoduyla daha sonra eklenecek olan boş bir vektör aşağıda gösterildiği gibi iki farklı şekilde bildirilir:

```Rust
let mut vnew: Vec<u8> = Vec::new();  // []
let mut vmac: Vec<u8> = vec![];      // []
````

Rust'ta tamsayılar için varsayılan tür `i32` olduğundan aşağıdaki yer alan vektörün türü belirtilmediğinden öğeleri 32 bit işaretli tam sayı değerlerinden oluşturulacaktır:

```Rust
let v1 = vec![1, 2, 3];     // (-/+) değerler -2, -1, 0, 1, 2, 3
println!("v1: {:?}", v1);
````

İstenildiğinde vektörün türü depolanan herhangi bir elemanın türüne ayarlanabilir. Bu durumda vektörün bir arada tuttuğu tüm öğeler ayarlanan türden olurlar:

```Rust
let v2 = vec![1, 2u32, 3];		//Vektörün veri türü 2. öğenin türü ile aynı olur 
println!("v2: {:?}", v2);
````

Tür bilgisi vektör ilklendirilirken de bildirilebilir:

```Rust
 let v3: Vec<char> = vec!['a', 'b', 'c']; 
 println!("v3: {:?}", v3);
 ````
 
 İstenildiğinde türün otomatik çıkarsanmasından faydalanarak tüm eleman değerleri tek seferde bildirilebilir:
 
 ```Rust
 let v4 = vec![0; 10];      // 0 değerli 10 adet eleman
 println!("v4: {:?}", v4);
 ````
 
 Bir vektör yineleyici kullanılarak ilklendirilebilir. Bu çoğu durumda kullanışlı bir özelliktir:
 
 ```Rust
 let v5: Vec<i32> = (-5..5).collect();
 println!("Depolanan değerler: {:?}", v5);// [-5, -4, -3, -2, -1, 0, 1, 2, 3, 4];
 ````

Vektörler dizin işlemlerini desteklerler. Değişebilir yapıda tanımlanan öğelere aritmetik işlem uygulamak mümkündür:

```Rust
fn main () {
 
   let mut v = vec![1, 2, 3];
   v[1] = v[1] + 5;         // birinci elemana +5 eklenir
   
   println!("v: {:?}", v);  // v: [1, 7, 3] 
}
````

## Vektör öğelerine erişmek ve değiştirmek
Bir vektörün öğelerine dizin numarası ya da `.get()` işlevi yardımıyla erişilir. Bir vektörün öeğelerine dizin numarası kullanarak erişmek için: 

```Rust
fn main() {
    let mut v = vec![5i32,4,3,2,1]; // Tür çıkarımı ilk elemandan i32
    println!("{:?} ", v);   // [5, 4, 3, 2, 1] 
    v[0] = 1;
    v[1] = 2;
    println!("{:?} ", v);   // [1, 2, 3, 2, 1] 
}
````

Her iki yöntemi biraz daha gelişmiş bir örnek üzerinden yineleyelim:

```Rust
fn main () {
    let v = vec![1, 2, 3, 4, 5];
    
    let ikinci: &i32 = &v[1];
    println!("Vektörün ikinci Öğesi: {}", ikinci);  // 2
        
    let ucuncu = v[2];
    println!("Vektörün üçüncü Öğesi: {}", ucuncu);  // 3
    
    match v.get(2) {
        Some(ucuncu) => println!("Üçüncü öğenin değeri: {}' dir.", ucuncu),
        None => println!("Üçüncü öğe bulunmuyor."),
    }
}
````

Dizilerde olduğu gibi beş öğeden oluşan bir vektörün altıncı elemanına dizin numarası yoluyla erişmeye çalışmak, programın panikleyerek çökmesine neden olur. Ancak aralığın dışında kalan bir öğeye `.get()` metodu kullanarak erişmeye çalışmak daha kullanıcı dostu olan `Option` varyantı `None` sonucunun döndürülmesini sağlar.

```Rust
// Olmayan öğeye index yoluyla erişmek 
println!("Vektörün sondan bir fazlası: {:?}", v[5]);  // Panik!

// Olmayan öğeye .get() işlevi ile erişmek 
let bu_oge_yok = v.get(100);
println!("Bu öğe var mı?: {:?}", bu_oge_yok);         // None
````

### .push() işlevi
Bir vektöre yeni öğe eklemek için `.pop()` işlevinden yararlanılır. Öğeler bu işlev kullanıldığında vektörün sonuna eklenirler.

```Rust
fn main () {
   let mut v = vec!['C','a', 'l', 'ı']; // v: ['C', 'a', 'l', 'ı']
   println!("v: {:?}", v);
   
   v.push('ş');
   println!("v: {:?}", v);              // v: ['C', 'a', 'l', 'ı', 'ş']
}
````

### .pop() işlevi
Aynı şekilde bir vektörün son elemanı `.pop()` işlevi kullanılarak çıkartılır. 
```Rust
fn main () {
   let mut v = vec!['C','a', 'l', 'ı', 'ş'];
   println!("v: {:?}", v);
   
   v.pop();
   v.pop();
   println!("v: {:?}", v);  // v: ['C', 'a', 'l']
}
````

Bir vektöre öğe eklemek ya da vektörden eleman çıkartabilmek için o vektörün öğelerinin `mut` anahtar sözcüğüyle değişebilir olarak tanımlanmış olması gerekmektedir:

```Rust
fn main () {
 
   let v = vec![1, 2, 3];
   
   v.push(4);
   println!("v: {:?}", v);
   // cannot borrow `v` as mutable, as it is not declared as mutable
}
````
## Vektörün yaşam süresi 
Bir vektörün yaşam süresi tanımlandığı kapsam boyunca devam eder. Kapsam dışına gelindiğinde vektörün yaşamı sona ereceğinden onun için ayrılan hafıza kaynakları boşaltılarak sisteme iade edilir: 

```Rust
fn main () {
    {
        let  v = vec![1, 2, 3];
        println!("v: {:?}", v); // v: [1, 2, 3]
        // işlemler
        
   } //<- v bu noktada kapsam dışına çıkar ve kaynaklar serbest bırakılır 
}
````

## Kapasite ayırmak
Bir vektörün uzunluğuna `.len()`, kapasitesine ise `capacity()` metodlarıyla erişilir. Ayrılan kapasitenin aşılması durumunda, kapasite miktarının iki katı bellek otomatik olarak yeniden tahsis edilerek vektör boyutuna eklenir:

```Rust
fn main () {
    // Uzunluğu: 0, Kapasitesi: 10 olan bir vektör
    let mut v: Vec<i32> = Vec::with_capacity(10);
    
    // Vektör kapasitesi kadar değerlere dolduğunda
    for i in 0..10 {
        v.push(i);
    }
    println!("Uzunluğu: {:?}, Kapasitesi : {:?}", v.len(), v.capacity()); // Uzunluğu: 10, Kapasitesi : 10
    
    // vektör kapasitesinin üstüne çıkıldığında 
    v.push(11);
    println!("Uzunluğu: {:?}, Kapasitesi : {:?}", v.len(), v.capacity()); // Uzunluğu: 11, Kapasitesi : 20
}
````

⭐️ Örnekte de görüleceği gibi kapasitesi 10 olan bir vektöre 11. öğe eklendiğinde kapasitesi otomatik olarak iki kat arttırılır.

## Yineleme yoluyla değerlere erişmek
Vektörün tuttuğu her bir öğeye sırayla erişebilmek için döngülerden yararlanılır: 

```Rust
fn main () {
    let mut v = vec![75, 90, 13];
    
    for i in &mut v {
        *i += 50;
        println!("Öğe: {}", i);
    }
}
````

## Enum türünden faydalanmak
Bazı durumlarda farklı türden öğelere sahip bir liste üzerinde çalışmak gerekebilir. Vektörler aynı türden elemanları depolayabildiklerinden bu gibi durumlarda `enum` türünü kullanmak yararlıdır:

```Rust
#[derive(Debug)]

enum Tablo {
        No(i32),
        Hacim(f64),
        Bilgi(String),
}
    
fn main () {
    
    let satir = vec![
        Tablo::No(5),
        Tablo::Hacim(7.65),
        Tablo::Bilgi(String::from("Konsantre mamül.")),
        ];
    println!("satir: {:?}", satir);  
    // ya da index numarası ile 
    // println!("satir: {:?}", satir[1]);
}
````

## Sonraki konuya geçmeden önce...
⭐️ Bir vektör temelde 3 şeyi temsil eder:
- Verilere bir işaretçi
- Sahip olunan eleman sayısı, yani `.len()` metoduyla öğrendiğimiz boyutu 
- Gelecekte depolanabilecek öğeler için ayrılan alan miktarı yani `.capacity()` metodyla eriştiğimiz vektör kapasitesi.

Bir vektörün eriştiği boyut kapasitesini aştığında, sahip olduğu kapasite otomatik olarak artırılacağından öğeleri de yeniden tahsis edilecektir. Bu sürecin yavaş olma ihtimaline karşı mümkün olduğunca `Vec::with_capacity()` metodu kullanılmalıdır.

> 💡 String veri türü UTF-8 olarak kodlanmış bir vektör olmasına rağmen tam da bu kodlama nedeniyle dizin numaraları kullanılarak karakterlerine erişilemez:

```Rust
fn main () {
    let st: String = String::from("Merhaba");
    println!("{:?}", st[0]);
    // error[E0277]: the type `std::string::String` 
    // cannot be indexed by `{integer}`
}
````

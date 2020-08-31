## Standart kütüphane, temel türler ve ön kütüphaneler
⭐️ Rust dilinin bileşenleri sadece `std` kütüphane sandığı tarafından değil, derleyici tarafından da uygulanır. Bunlar: 
  
  - **[İlkel türler](https://doc.rust-lang.org/std/#primitives)**: Derleyici tarafından tanımlanan metodlar temel türlere doğrudan `std` kütüphanesi tarafından uygulanır.
  - **[Standard Makrolar](https://doc.rust-lang.org/std/#macros)**: Hem derleyici hem de `std` tarafından tanımlanır.
  
**`std`** kütüphanesi, kapsadıkları alanlara göre **[modüllere](https://doc.rust-lang.org/std/#modules)** ayrılmıştır.

⭐️ Temel türler **derleyici** tarafından uygulanırken, temel türlere için **en yararlı metodları** standart kütüphane tarafından doğrudan  uygulanır. Ancak, bazı temel türlerin **daha az kullanılan dil öğeleri**, ilgili **std** modüllerinde bulunduğundan **std** modüllerinde hem `char`, `str` hem de tamsayı türlerini bir arada görmeniz doğaldır.

## Temel türler
Derleyici tarafından tanımlanan ve doğrudan `std` kütüphanesi tarafından uygulanan metodlar aşağıda yer almaktadır:

```Rust
bool, char, slice, str

i8, i16, i32, i64, i128, isize
u8, u16, u32, u64, u128, usize

f32, f64

array, tuple

pointer, fn, reference
````

## Standard Makrolar
Hem derleyici hem de `std` kütüphanesi tarafından tanımlanan makrolar:

```Rust
print, println, eprint, eprintln
format, format_args
write, writeln

// concat_idents: yalnızca `nightly` sürümü için deneysel API
concat, concat_idents, stringify 

include, include_bytes, include_str

assert, assert_eq, assert_ne
debug_assert, debug_assert_eq, debug_assert_ne

try, panic, compile_error, unreachable, unimplemented

file, line, column, module_path
env, option_env
cfg

// select: yalnızca `nightly` sürümü için deneysel API
select, thread_local

vec
````

## Std modülleri
```rust
char, str

i8, i16, i32, i64, i128, isize
u8, u16, u32 ,u64, u128, usize
f32, f64
num

// heap: yalnızca `nightly` sürümü için deneysel API
vec, slice, hash, heap, collections

string, ascii, fmt

default

marker, clone, convert, cmp, iter

ops, ffi

option, result, panic, error

io
fs, path
mem, thread, sync
process, env
net
time
os

ptr, boxed, borrow, cell, any, rc

prelude

// intrinsics: yalnızca `nightly` sürümü için deneysel API
intrinsics
// raw: yalnızca `nightly` sürümü için deneysel API
raw 
````

> 🔎 [Rust’un kaynak kodları](https://github.com/rust-lang/rust)nı incelediğinizde, [src dizini](https://github.com/rust-lang/rust/tree/master/src)nin bir **çalışma alanı** olduğunu görebilirsiniz. Çok sayıda kütüphane sandığına sahip olmasına rağmen, [kök Cargo.toml](https://github.com/rust-lang/rust/blob/master/src/Cargo.toml) dosyasını incelediğinizde, temel sandıkların [rustc](https://github.com/rust-lang/rust/tree/master/src/rustc) *(derleyici)* ve [libstd](https://github.com/rust-lang/rust/tree/master/src/libstd) *(std)* olduğunu rahatlıkla fark edersiniz. **Std modülleri**nin çoğunun orijinal konumu `src/libcore`'dur. Bu modüllerin `use pub` kullanımı yoluyla yeniden dışa aktarılmış olduğunu `Libstd/lib.rs` dosyasından görebilirsiniz.

**Oldukça önemli `std` modüllerinden birkaçı** aşağıda sıralanmıştır.
- `std::io` - Çekirdek **I/O** işlevselliği
- `std::fs` - **Filesystem** Dosya sistemine özgü işlevsellik
- `std::path` - **Cross-platform path** platformlar arası yol işlevselliği
- `std::env` - **Process’s environment** Sürecin/işlemin çevresel işlevselliği
- `std::mem` - **Memory** Hafıza ile ilgili işlevler
- `std::net` - **TCP/UDP** TCP/UDP iletişimi
- `std::os` - **OS** İşletim sistemine özgü işlevsellik
- `std::thread` -  Yerel **İşliklere"" özgü işlevsellik
- `std::collections` - Çekirdek **koleksiyon türleri**

> 💯 Daha fazla detay için [Rust Standard Kütüphane Belgeleri](https://doc.rust-lang.org/std/)ni inceleyebilirsiniz.

## Ön yükleme kütüphaneleri
Rust'ın `std` kütüphanesi pekçok modül içermesine rağmen her Rust programı bunların tamamını yüklemez. Bunun yerine bir rust programının başlangıçta ihtiyaç duyabileceği genel şeyler yüklenir. Buna **[preludes](https://doc.rust-lang.org/std/prelude/)** yani ön yükleme kütüphanesi adı verilir. Ön yükleme kütüphanesinde varsayılan olarak sadece aşağıdakiler bulunur:

```rust
// Yeniden ihraç edilen çekirdek operatörleri 
pub use marker::{Copy, Send, Sized, Sync};
pub use ops::{Drop, Fn, FnMut, FnOnce};

// Tekrar ihraç edilen işlevler 
pub use mem::drop;

// Tekrar ihraç edilen türler ve özellikler 
pub use boxed::Box;
pub use borrow::ToOwned;
pub use clone::Clone;
pub use cmp::{PartialEq, PartialOrd, Eq, Ord};
pub use convert::{AsRef, AsMut, Into, From};
pub use default::Default;
pub use iter::{Iterator, Extend, IntoIterator};
pub use iter::{DoubleEndedIterator, ExactSizeIterator};
pub use option::Option::{self, Some, None};
pub use result::Result::{self, Ok, Err};
pub use slice::SliceConcatExt;
pub use string::{String, ToString};
pub use vec::Vec;
````

> Ön yükleme kütüphaneleri [`libstd/lib.rs`](https://github.com/rust-lang/rust/blob/master/src/libstd/lib.rs#L353) üzerine açıkça aktarılmıştır ve bunların tüm listesi [`libstd/prelude/v1.rs`](https://github.com/rust-lang/rust/blob/master/src/libstd/prelude/v1.rs) üzerinde yer almaktadır.

⭐️ Yani teknik olarak Rust:
- `extern crate std;` :  ile **her sandığın sandık kökünü**
- `use std::prelude::v1::*;` : ile **her modülü** otomatik olarak eklediğinden her defasında bu ön yükleme kütüphanelerinin yeniden ithal edilmesine gerek duyulmaz.

Ön yükleme kütüphanesi yaklaşımı, Rust kütüphanelerinde oldukça yaygın görülür. Örneğin [`std::io`](https://github.com/rust-lang/rust/blob/master/src/libstd/io/prelude.rs) gibi `std` sandığında bulunan bazı modüllerin ve [`Diesel`](https://github.com/diesel-rs/diesel/blob/master/diesel/src/lib.rs#L324) gibi birçok kütüphanenin bile başlangıçta kulandıkları `prelude` modülleri bulunur.

⭐️ Çünkü bu ön yükleme kütüphaneleri, o sandığın kullanımı esnasında gerekli olan tüm önemli bileşenlerin alınabileceği tek bir noktayı temsil ederler ve programcı tarafından özellikle ithal edilmedikçe otomatik olarak yüklenmezler. Her Rust programında otomatik olarak yüklenen ön kütüphane sadece `std::prelude` ön yükleme kütüphanesidir.

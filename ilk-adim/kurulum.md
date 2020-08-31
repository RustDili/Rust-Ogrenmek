# Kurulum
## Rustup
Rust'u sisteminize kurmanın birçok yolu olmakla birlikte en doğru ve sağlıklı yol [Rustup](https://rustup.rs/) kullanmaktır. 

[📖](https://github.com/rust-lang-nursery/rustup.rs) Rustup, Rust Programlama Dilini resmi sürüm kanallarından kurarak kararlı, beta ve gece derleyicileri arasında kolayca geçiş yapmanızı ve bunları güncel tutmanızı sağlar. Standart kütüphanenin ikili yapıları aracılığıyla programların ortak platformlarda kullanılabilmesi için çapraz derlemeyi kolaylaştırır.

[📖](https://github.com/rust-lang-nursery/rustup.rs#installation) Rustup, **Rustc, cargo, rustup** ve diğer standart araçları Cargo'nun `bin` dizinine yükler. Bu dizin Unix işletim sistemlerinde `$HOME/.cargo/bin` Windows işletim sistemlerinde ise `%USERPROFILE%\.cargo\bin` şeklindedir. Bu dizin aynı zamanda Cargo'nun Rust programlarını ve Cargo eklentilerini yükleyeceği dizindir.

💡 Daha fazla bilgi için [Rustup projesinin Github sayfası](https://github.com/rust-lang-nursery/rustup.rs)nı ziyaret edebilirsiniz.

## Linux ve Mac Kullanıcıları İçin:
```bash
curl https://sh.rustup.rs -sSf | sh
````
Windows Kullanıcıları İçin:
[www.rustup.rs](https://rustup.rs/) bağlantısından `rustup-init.exe` adlı programı indirip çalıştırmak yeterlidir.

>⭐ Microsoft Windows işletim sistemindeyseniz, ek olarak 3-4 GB boyutundaki [Visual C ++ Build Tools](https://visualstudio.microsoft.com/visual-cpp-build-tools/) 2015 bileşenlerini veya veya daha üst bir versiyonunu yüklemelisiniz.

Rust'u yükledikten sonra, kurulumun başarılı olup olmadığını test edebilmeniz için terminalinize rustc --version veya rustc -V yazarak mevcut sürümü kontrol edebilirsiniz.

💡 Yeni bir Rust sürümü çıktığında, Rust ekosistemini güncellemek için rustup update komutunu kullanın.

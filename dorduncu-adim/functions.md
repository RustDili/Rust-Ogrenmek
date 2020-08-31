## İşlevler
Herhangi bir programın ilk organizasyon satırıdır. 

```Rust
fn main() {
    selam();        // Bir şey yap
    memleket_sor(); // Başka bir şey yap
}

fn selam() {
    println!("Merhaba!");
}

fn memleket_sor() {
    println!("Hemşerim memleket nere?");
}
// Merhaba!
// Hemşerim memleket nere?
````

Aynı dosyaya birim testleri ekleyebiliriz:

```Rust
fn main() {
    selam();
}

fn selam() -> String {
    "Merhaba dünya!".to_string()
}

#[test] // Test özniteliği bunun test işlevi olduğunu gösterir 

fn test_selam() {
    assert_eq!("Merhaba dünya!", selam());
}

/* 
running 1 test
test test_selam ... ok
test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
running 0 tests
test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
*/
````

💡 Test işlevleri daima `#[cfg(test)]` özniteliğine sahip bir test modülünün içine yerleştirilir. 
`Cfg(test)` modülü sadece test yapılırken derlenir. Bir donraki bölümde bu konuya daha fazla eğileceğiz.

>💭 [Öznitelikler](https://doc.rust-lang.org/reference/attributes.html), ad, kural, dil ve derleyici sürümüne göre yorumlanan genel ve serbest biçimli **metaveriler**dir.

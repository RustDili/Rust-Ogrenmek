## Kod organizasyonu
Bir kod bloğu büyüdükçe, daha küçük parçalara ayrıştırılmalı ve uygun şekilde düzenlenmelidir. Rust farklı seviyelerde kod organizasyonunu destekler:

1. ### [İşlevler](functions.md)

2. ### [Modüller](modules.md)
Aşağıdaki gibi düşünülebilirler.

  - **Satır içi Modül**
  - **Dosya**
  - **Dizin hiyerarşisi**

3. ### [Sandıklar](crates.md)
Bu Sistem rust dilinin paket değosu olarak düşünülmelidir ve içeriğinde
  
  - **Çalıştırılabilir paketle birlikte lib.rs dosyası**
  - **Cargo.toml dosyasında belirtilen paket bağımlılıkları**
  
  dosyaları bulunur. Bu dosyalarda ise
  
  - **Path**
  - **Git reposu**
  - **Crates.io**
  
  Bulunabilir.
 
 4. ### [Çalışma alanları](workspaces.md)
 Bir proje üzerinde birden fazla paket yönetmeye yardımcı olur.
 
 Şimdi bunları birer birer tartışalım.
 
 > 💡 Örnekleri basitleştirmek için, “Merhaba dünya!” dizgisini yazdıran basit bir işlev kullanacağız. Ancak test edilebilir kodlar yazarken  işlev içindeki dizgeyi yazdırmak yerine daima işlevden `String'  döndürmeye ve dönen değeri çağırırarak yazdırmaya çalışmaya dikkat edin.

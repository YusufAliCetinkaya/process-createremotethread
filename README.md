# Process CreateRemoteThread

Windows işletim sistemi mimarisinde süreçler arası etkileşim ve uzak kod yürütme (Remote Code Execution) mekanizmalarının güvenli ve kararlı bir şekilde uygulanmasını hedefler.


## Proje Hedefleri

Bu projenin temel odak noktaları şunlardır:

- **Kararlı Bellek Yönetimi:** RAII (Resource Acquisition Is Initialization) kullanarak, hata durumlarında bile sistem kaynaklarının (handle ve bellek) sızıntı yapmadan temizlenmesini sağlamak.
- **Güvenli Kod Yürütme:** Uzak süreçte ayrılan belleğin durumunu (Commit, Protection, Size) derinlemesine doğrulayarak hedef sürecin çökmesini engellemek.
- **Kalıcılık Kontrolü:** Uzak thread aktif olduğu sürece belleğin serbest bırakılmasını engelleyen `persistence` mantığını kurgulamak.

## Teknik Operasyonlar

1. **Hedef Tespiti:** Sistem anlık görüntüsü üzerinden hedef sürecin PID numarasına erişim.
2. **Bellek Hazırlığı:** Uzak süreçte adres alanı ayırma ve verinin (payload) bu alana güvenli transferi.
3. **İzin Yönetimi:** Yazma aşamasında RW (Read/Write) olan izinlerin, yürütme öncesinde RX (Read/Execute) moduna çevrilerek modern Windows korumalarına uyum sağlanması.
4. **Thread Senkronizasyonu:** `CreateRemoteThread` sonrası thread durumunun ve çıkış kodlarının (Exit Code) takibi.

## Terminal Analizi
Çalışma çıktısı incelendiğinde projenin hedeflerine ulaştığı görülmektedir:
- `Deep Verify` aşaması, belleğin tam olarak istenen izinlerde olduğunu kanıtlamıştır.
- `Protection: RX (Old: 0x4)` çıktısı, belleğin başarıyla yürütülebilir hale getirildiğini gösterir.
- `Status: ACTIVE, ExitCode: 0x103` bilgisi, uzak thread'in hedef süreçte başarıyla başlatıldığını ve çalıştığını teyit eder.
- `Persistence requested` mesajı, RAII temizlik mekanizmasının thread'i korumak için devreye girdiğini kanıtlar.


```bash

cargo run


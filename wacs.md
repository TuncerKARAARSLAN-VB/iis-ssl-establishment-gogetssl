WACS ile powershell üzerinde iis içindeki yayınladığınız web sayfalarına hızla ssl sertifikası yayınlayabilirsiniz. KOmut satırına kurulumlardan conra wacs yazdığınızda menü gelir ve aşağıdaki adımlar ile tls kurulumları sağlanabilir

**Win-ACME (WACS)**, Windows ekosistemi üzerinde çalışan sunucular için (başta IIS olmak üzere) **Let's Encrypt** tabanlı ücretsiz SSL/TLS sertifikalarını otomatize eden açık kaynaklı bir komut satırı aracıdır. Manuel sertifika yenileme süreçlerini ortadan kaldırarak, sertifika yaşam döngüsü yönetimini (Certificate Lifecycle Management) ACME protokolü üzerinden tam otomatik hale getirir.

---

## Technical Deep Dive

WACS, kurumsal sistem mimarisinde sertifika yönetimi için kritik avantajlar sağlar:

* **ACME Uyumluluğu:** Let's Encrypt ve diğer ACME destekli sertifika otoriteleri ile doğrudan entegrasyon.
* **IIS Entegrasyonu:** Web sitelerini otomatik tarayarak HTTPS binding işlemlerini, sertifika kurulumunu ve port yapılandırmasını insan müdahalesi olmadan gerçekleştirir.
* **Validation Mekanizmaları:**
    * **HTTP-01:** Web sunucusu üzerinden dosya doğrulama.
    * **DNS-01:** DNS kayıtları üzerinden (özellikle Wildcard sertifikalar için) doğrulama.
* **Renewal (Yenileme):** Windows Task Scheduler (Görev Zamanlayıcı) kullanarak sertifika süreleri dolmadan (genellikle 55. günde) yenileme işlemini tetikler.

---

## Implementation Roadmap

WACS'ın paket yöneticisi **Chocolatey (choco)** üzerinden kurulumu ve yapılandırılması için aşağıdaki adımlar takip edilmelidir.

### 1. Ön Gereksinimlerin Kontrolü
Kuruluma başlamadan önce PowerShell'in yönetici haklarıyla (Run as Administrator) çalıştırıldığından emin olunmalıdır.

### 2. Chocolatey ile Kurulum
Sisteme WACS paketini kurmak için aşağıdaki komut yürütülür:

```powershell
choco install win-acme
```

### 3. İlk Yapılandırma ve Sertifika Oluşturma
Kurulum tamamlandıktan sonra, komut satırına `wacs` yazarak interaktif menüye giriş yapılır. Standart bir IIS kurulumu için izlenecek mantıksal akış şöyledir:

* **N (Create certificate - simple mode):** Yeni bir sertifika oluşturmak için en hızlı yoldur.
* **Site Seçimi:** IIS üzerinde tanımlı olan siteler listelenir; sertifika atanacak sitenin numarası seçilir.
* **Binding Seçimi:** Sertifikanın hangi host isimlerini kapsayacağı belirlenir.
* **E-posta Onayı:** Let's Encrypt servis bildirimleri için bir e-posta adresi girilir.

### 4. Otomatik Yenileme Doğrulaması
WACS, kurulum sırasında Windows Görev Zamanlayıcı'ya bir görev ekler. Bu görevin durumunu aşağıdaki komutla kontrol edebilirsiniz:

```powershell
Get-ScheduledTask -TaskName "win-acme*"
```

---

## Best Practices ve Kurumsal Notlar

* **Log Yönetimi:** WACS logları varsayılan olarak `%programdata%\win-acme` dizininde tutulur. SIEM entegrasyonu veya hata ayıklama süreçlerinde bu dizin izlenmelidir.
* **Merkezi Yönetim:** Eğer **Riverova** veya benzeri dağıtık bir mimari üzerinde çoklu web nodları kullanılıyorsa, DNS-01 challenge yönteminin tercih edilmesi ve sertifikaların merkezi bir storage üzerinden nodlara dağıtılması (DevOps best practice) önerilir.
* **Güvenlik:** Sertifika özel anahtarlarının (private keys) dışa aktarılamaz (non-exportable) olarak işaretlenmesi, kurumsal güvenlik standartları (NIST önerileri) açısından kritik bir parametredir.

WACS kullanımı sırasında spesifik bir IIS konfigürasyonu veya DNS sağlayıcı entegrasyonu (Azure DNS, Cloudflare vb.) üzerine derinleşmek ister misiniz?

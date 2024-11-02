Eğer elinde bir **.crt** dosyası varsa, bu dosya yalnızca sertifika bilgisini içerir. Ayrıca, SSL kurulumunda **private key** ve **CSR** dosyasına ihtiyacın olabilir. Genellikle bir sertifika otoritesi (CA) tarafından sağlanan sertifikayı yüklemek için bu adımları izleyebilirsin:

### 1. **Sertifika ve Özel Anahtar (.crt ve .key) Dosyalarının Hazır Olması**

- **.crt** dosyası sertifikayı, **.key** dosyası ise özel anahtarı içerir. Eğer **.key** dosyasına sahipsen, bu adımları izleyebilirsin. Eğer **.key** dosyan yoksa, sertifikayı oluşturduğun sunucuda veya CSR dosyasını oluşturduğun yerde bu dosya olmalıdır.
  
### 2. **Sertifika Dosyasını .pfx Formatına Dönüştürme (Opsiyonel)**

- Windows Server üzerinde bir sertifika yüklerken genellikle **.pfx** dosyası gereklidir. Eğer **.crt** ve **.key** dosyaların varsa, bunları bir **.pfx** dosyasına dönüştürebilirsin.
- Bu işlemi yapmak için OpenSSL kullanabilirsin:

    ```bash
    openssl pkcs12 -export -out www.vebende.com.pfx -inkey private.key -in www.vebende.com.crt
    ```

- `private.key` dosyası özel anahtarını içerir.
- `www.vebende.com.crt` sertifika dosyan.
- Bu komut bir **.pfx** dosyası oluşturacaktır.

   Eğer **.pfx** dosyan varsa, aşağıdaki adımlara geçebilirsin.

### 3. **Sertifikayı Windows Server Üzerine Yükleme**

#### A. **Sertifikayı Kişisel Sertifikalar Deposuna Yükle**

1. **MMC'yi Aç**

- "Windows + R" tuşlarına basarak **Çalıştır** penceresini aç ve `mmc` komutunu çalıştır.
  
2. **Sertifikalar Eklentisini Ekleyin**

- Açılan MMC penceresinde, **Dosya** > **Snap-in Ekle/Kaldır** seçeneğine tıkla.

- **Sertifikalar** eklentisini seçip **Ekle** butonuna tıkla.

- **Bilgisayar Hesabı** seçeneğini seçerek **İleri** butonuna tıkla.

- **Yerel Bilgisayar** seçili olduğundan emin ol ve **Son** butonuna tıkla.

3. **Sertifikayı İçeri Aktar**

- Sol tarafta **Sertifikalar (Yerel Bilgisayar)** kısmını genişlet ve **Kişisel** klasörüne sağ tıklayıp **Tüm Görevler > İçe Aktar** seçeneğini seç.

- **Sertifika İçe Aktarma Sihirbazı** açılacaktır.

- **İleri** butonuna tıkla.

- **Gözat** ile .crt dosyanı seç. Eğer bir **.pfx** dosyası oluşturduysan, bunu da seçebilirsin.

- **İleri** butonuna tıkla ve sertifikanın **Kişisel Sertifikalar Deposu'na** yüklenmesini sağla.

- Eğer bir **.pfx** dosyası yüklemişsen, şifreyi girmen istenebilir.

4. **Sertifika Zincirini İçe Aktar**

- Eğer sertifika otoritesi sana bir **ara sertifika** (intermediate certificate) verdiyse, bunu da aynı şekilde yüklemen gerekebilir. Bunun için, **Sertifikalar (Yerel Bilgisayar)** > **Ara Kök Sertifikalar** altında aynı işlemleri yaparak bu dosyayı içe aktarabilirsin.

#### B. **IIS Üzerinde SSL Sertifikasını Bağla**

1. **IIS (Internet Information Services) Konsolunu Aç**

- **Başlat** menüsünden **IIS Yönetici Konsolu'nu** aç.

2. **Web Sitesi Bağlantılarını Düzenle**

- IIS Konsolunda sol tarafta sunucu adını genişlet ve **Siteler** altında **www.vebende.com** sitesini bul.

- Sitenin üzerine sağ tıklayıp **Bağlantılar Düzenle** (Edit Bindings) seçeneğini seç.

3. **HTTPS Bağlantısını Ekle**

- Açılan pencerede, bir https bağlantısı eklemek için **Ekle** butonuna tıkla.

- Bağlantı tipi olarak **https** seç ve ilgili IP adresini ve **443** portunu belirle.

- Sertifika listesinde ithal ettiğin SSL sertifikasını seç ve **Tamam** butonuna tıkla.

4. **IIS Yeniden Başlatma**

- IIS Yönetici Konsolu'nda sağ tarafta **IIS'yi Yeniden Başlat** seçeneğine tıklayarak işlemi tamamla.

### 4. **SSL Sertifikasını Test Et**

- Tarayıcıda **https://www.vebende.com** adresine git ve SSL sertifikasının düzgün çalışıp çalışmadığını kontrol et.

- Tarayıcıda kilit simgesini görmelisin. Sertifika zinciri doğruysa güvenli bağlantı başarılı olacaktır.

Bu adımlarla **.crt** dosyasını kullanarak SSL sertifikasını Windows Server 2022 üzerinde yükleyip etkinleştirebilirsin.

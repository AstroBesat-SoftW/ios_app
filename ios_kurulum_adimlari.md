Şimdi ilk olarak  windows bilgisayarımız var ve andoird studyo kullnarak flutter projesinden ilerleyerek süreci anlatacağım. nasıl imzalama yaparız ve mac kullanmadan github ile uygulamayı çıakrıız
bunun ile ilgili maalesef türkçe ve ingilizce yazılı kaynak yoktu bu yüzden kendi tecribemi szilere adım adım anlatmak isteidm.

ilk olarak winodws bilgisayarımızda 
1.Windows'ta Kimlik Talebi (CSR) Oluşturma:

Git Bash kullanılacak.Apple'a "Ben Besat, bana sertifika ver" demek için bir dijital anahtar üretmeliyiz.
1- Bilgisayarınızda başlat menüsünü açıp Git Bash yazın ve çalıştırın (siyah bir komut ekranı açılacak).
2- Bilgisayarınızda gizli bir kilit (key) oluşturmak için şunu yazıp Enter'a basın:
openssl genrsa -out besat.key 2048
3- Şimdi Apple'a göndereceğimiz başvuru dosyasını (CSR) oluşturmak için şu komutu yapıştırıp Enter'a basın (Mail adresini kendi Apple ID mailinize göre değiştirin):
openssl req -new -key besat.key -out besat.csr
2. Enter'a bastıktan sonra OpenSSL sana alt alta bazı sorular soracak. Türkçe karakter (ç, ş, ğ, vs.) kullanmadan şu şekilde doldur ve her birinden sonra Enter'a bas:

Country Name (2 letter code): TR yaz ve Enter'a bas.

State or Province Name (full name): Tekirdag yaz ve Enter'a bas.

Locality Name (eg, city): Tekirdag yaz ve Enter'a bas.

Organization Name (eg, company): Besat Arif Cingar yaz ve Enter'a bas.

Organizational Unit Name (eg, section): IT yaz ve Enter'a bas.

Common Name (e.g. server FQDN or YOUR name): Besat Arif Cingar yaz ve Enter'a bas.

Email Address: besatt59@gmail.com yaz ve Enter'a bas.

3. Son olarak sana "Extra Attributes" (Ekstra özellikler) soracak. Apple bunları istemez. Bunları boş geçmek için hiçbir şey yazmadan doğrudan Enter'a bas:

A challenge password: (Boş bırak, Enter'a bas)

An optional company name: (Boş bırak, Enter'a bas)

Bunu yaptığında terminal normal alt satıra geçecektir. Hiçbir uyarı, hiçbir hata almayacaksın ve Apple'ın istediği tüm bilgileri içeren kusursuz, dört dörtlük bir besat.csr dosyan masaüstünde hazır olacak.




örnek:

<img width="791" height="527" alt="image" src="https://github.com/user-attachments/assets/3eaa9f5f-4eec-4f6a-ba26-923bc704a2bf" />
çıktı:
<img width="753" height="105" alt="image" src="https://github.com/user-attachments/assets/b5ee5436-a287-4985-8f24-5786307b9041" />

-----------------
AŞAMA 2
Android Studio'da iOS Ayarlarının Yapılandırılması
Info.plist ve uygulama kimliği
Android Studio'da projenizi açın ve iOS tarafı için gerekli temel tanımlamaları yapın.

ios/Runner/Info.plist dosyasını açın.

Uygulamanızın görünür adını belirleyin. Örneğin:
--CFBundleDisplayName
AstroDash--
<img width="1492" height="595" alt="image" src="https://github.com/user-attachments/assets/a160b5df-b0ed-464d-87a4-b0574ddda058" />

Eğer uygulamanız kamera, internet, galeri gibi izinler kullanıyorsa, bunların açıklamalarını mutlaka Info.plist içine ekleyin. (Apple, iznin neden istendiği yazmıyorsa TestFlight'a yüklemeyi reddeder).

İkonlarınızı ayarlayın. Bunun için pub.dev üzerinden flutter_launcher_icons paketini kullanabilirsiniz; bu paket iOS ikonlarını otomatik üretir.

--------------


AŞAMA 3

Uygulama Kimliğini (App ID) Tanıtma
Apple'a ilk olarak uygulamanın adını ve paket adını (com.besat.dash) kaydetmemiz gerekiyor.

Tarayıcını aç ve developer.apple.com adresine girip Apple hesabınla giriş yap.

Açılan sayfada Certificates, Identifiers & Profiles menüsüne tıkla.
<img width="1881" height="920" alt="image" src="https://github.com/user-attachments/assets/0d355348-4167-4618-9980-d81491ee5712" />


Sol menüden Identifiers (Kimlikler) sekmesine tıkla.

Sayfanın ortasındaki (veya sağ üstteki) mavi renkli + (artı) butonuna tıkla.
<img width="1051" height="292" alt="image" src="https://github.com/user-attachments/assets/1f02c48e-25d6-42b8-abd4-8d0bea9af353" />


Listeden App IDs seçili kalsın, sağ üstten Continue (Devam) butonuna tıkla.

Tip olarak App seçili kalsın, tekrar Continue de.

Karşına form çıkacak:

Description kısmına uygulamanın adını yaz örnek proejene göre: deneme

Bundle ID kısmında Explicit seçili olsun ve altındaki kutuya şunu yaz: com.besat.deneme

<img width="1385" height="558" alt="image" src="https://github.com/user-attachments/assets/e3408522-52dc-40d0-8476-c345210e2825" />

Sayfanın en altındaki listeden bir şey seçmene gerek yok, direkt sağ üstten Continue ve sonra Register butonuna tıkla. (Uygulamamız Apple'a kaydedildi!)


----------------

pple'dan Ana Sertifikayı (.cer) Alma
Şimdi masaüstünde ürettiğin başvuru dosyasını (CSR) Apple'a verip onaylı sertifikanı alacağız.

Aynı sitede sol menüden Certificates sekmesine tıkla.

Mavi renkli + (artı) butonuna tıkla.
<img width="1013" height="313" alt="image" src="https://github.com/user-attachments/assets/1d588d11-161e-479e-b4b8-6ec2dfe58a3f" />

Software başlığı altındaki Apple Distribution seçeneğini işaretle ve sağ üstten Continue de.
<img width="1390" height="497" alt="image" src="https://github.com/user-attachments/assets/d584f67f-ff7e-4e79-bc4a-365043ce7eec" />

Karşına "Upload a Certificate Signing Request" (CSR Yükle) ekranı gelecek. Choose File (Dosya Seç) butonuna tıkla.

Masaüstündeki sertifika klasörüne gir ve senin ürettiğin besat.csr dosyasını seç, sağ üstten Continue de.

İşlem başarılı! Ekranda çıkan Download butonuna tıkla.

<img width="1423" height="461" alt="image" src="https://github.com/user-attachments/assets/24dd07f9-e747-4326-ac2a-1f490557ae19" />


Bilgisayarına ios_distribution.cer adında bir dosya inecek. Bu dosyayı indirilenlerden kesip masaüstündeki sertifika klasörünün içine, diğer dosyaların yanına yapıştır.

<img width="885" height="287" alt="image" src="https://github.com/user-attachments/assets/91d4f6d0-a57b-4600-b38e-b635b53ffb5b" />


---------------------------------------

3. Asıl Hedef: Windows'ta .p12 Dosyasını Üretme
Apple'ın verdiği .cer dosyası doğrudan işimize yaramıyor, bunu kendi gizli anahtarımızla birleştirip .p12 formatına çevireceğiz.

Masaüstündeki sertifika klasöründe açık olan Git Bash ekranına geri dön.

Önce Apple'ın dosyasını bizim okuyabileceğimiz formata çevirmek için şu kodu yapıştır ve Enter'a bas:
openssl x509 -in ios_distribution.cer -inform DER -out ios_distribution.pem -outform PEM

Şimdi bu dosyayı senin en başta ürettiğin key ile birleştirip .p12 yapmak için şu kodu yapıştır ve Enter'a bas:
openssl pkcs12 -export -inkey besat.key -in ios_distribution.pem -out Certificate.p12

Enter'a basınca ekranda "Enter Export Password:" diyecek. Burada belirleyeceğin şifre çok önemli (Örneğin besat123 yaz). Yazarken ekranda harfler görünmez, sen yazıp Enter'a bas.
<img width="823" height="277" alt="image" src="https://github.com/user-attachments/assets/5c2c075e-1e0a-4f5d-9712-e200ddc0be31" />

"Verifying - Enter Export Password:" diyecek. Aynı şifreyi tekrar yaz ve Enter'a bas.
(Tebrikler! Klasörüne bakarsan en önemli dosyan olan Certificate.p12 dosyasının oluştuğunu göreceksin.)
<img width="846" height="296" alt="image" src="https://github.com/user-attachments/assets/c24e52b4-86e5-4fd3-8d0f-8003f17c490b" />

----------------------------------------------

4. Dağıtım Profilini (Provisioning Profile) Alma
Sertifikamız var, uygulamamız belli. Şimdi Apple'a "Ben bu sertifikayla bu uygulamayı mağazaya yollayacağım" diyen bir belge alacağız.

Tekrar developer.apple.com sitesine dön, sol menüden Profiles sekmesine tıkla.

Mavi renkli + (artı) butonuna tıkla.
<img width="1047" height="307" alt="image" src="https://github.com/user-attachments/assets/2c55ebc7-c8ed-451e-8f95-9703a1af3a06" />

Distribution (Dağıtım) başlığı altındaki App Store seçeneğini işaretleyip Continue de.
<img width="1437" height="790" alt="image" src="https://github.com/user-attachments/assets/1e8939b5-8705-43ee-bc10-9b6fc03cde90" />

App ID menüsüne tıkla, listeden kendi uygulamanı (deneme - com.besat.deneme) seçip Continue de.
<img width="1467" height="455" alt="image" src="https://github.com/user-attachments/assets/296f80aa-86c8-4c65-ac63-49af6fb19e98" />



Bir önceki adımda oluşturduğun sertifikan listede görünecek. Yanındaki yuvarlağı işaretle ve Continue de.
<img width="1376" height="428" alt="image" src="https://github.com/user-attachments/assets/3290e8b1-882c-4250-9a66-875655dae4c4" />

Provisioning Profile Name kutusuna İngilizce bir isim yaz (Örn: deneme_Profile) ve Generate butonuna bas.
<img width="1388" height="703" alt="image" src="https://github.com/user-attachments/assets/5ef22cca-1e8a-47bc-bb1e-3f3daf4200de" />

Çıkan ekrandan Download butonuna tıkla. Bilgisayarına inecek olan bu .mobileprovision uzantılı dosyayı da alıp masaüstündeki sertifika klasörüne at



-------------------------


5. Uygulamaya Özel Şifre Alma (API / Apple Kimlik Şifresi)
Kendi kişisel Apple şifreni kodlara yazmamak için GitHub'ın kullanacağı tek kullanımlık bir şifre üreteceğiz.

Tarayıcıda yeni bir sekme aç ve appleid.apple.com adresine gir, giriş yap.

Sol menüden Giriş Yapma ve Güvenlik (Sign-In and Security) sekmesine tıkla.

Uygulamaya Özgü Parolalar (App-Specific Passwords) seçeneğine tıkla.
<img width="1057" height="596" alt="image" src="https://github.com/user-attachments/assets/f652ffe4-1880-4662-b1cb-80ff3a116820" />

Parola Oluştur (veya + butonu) butonuna tıkla.

Sana uygulamanın adını soracak, oraya GitHub Actions yazıp oluştur de (kendi şifreni tekrar isteyebilir).
<img width="547" height="462" alt="image" src="https://github.com/user-attachments/assets/9f320b83-92b6-4a7c-840a-49a16ff7c05a" />

Ekrana xxxx-xxxx-xxxx-xxxx formatında bir şifre verecek. Bu şifreyi kopyala ve bilgisayarında bir Not Defteri'ne kaydet, pencereyi kapatırsan şifreyi bir daha göremezsin.
<img width="531" height="352" alt="image" src="https://github.com/user-attachments/assets/35c4519a-6921-4a03-b8f5-65d0c60d4a00" />

İşte bu kadar! Artık masaüstündeki klasöründe .p12 dosyan, .mobileprovision dosyan ve not defterinde Apple'dan aldığın şifren var.

<img width="911" height="315" alt="image" src="https://github.com/user-attachments/assets/35db99ac-4076-4839-bbe5-c1afcea22312" />


--------------------------------

6. Dosyaları Metne Çevirme (Base64 İşlemi)
GitHub, dosya yüklemeyi değil metin yüklemeyi (Secret) kabul eder. Bu yüzden elimizdeki dosyaları şifreli metinlere çevireceğiz.

Masaüstündeki sertifika klasöründe Git Bash ekranını tekrar açın (Eğer kapattıysanız klasöre sağ tıklayıp "Git Bash Here" diyebilirsiniz).

Önce .p12 dosyasını metne çevirmek için şu kodu yapıştırıp Enter'a basın:
base64 Certificate.p12 > cert_base64.txt

Şimdi .mobileprovision dosyasını metne çevirmek için şu kodu yapıştırıp Enter'a basın:
base64 deneme_Profile.mobileprovision > profile_base64.txt
<img width="577" height="197" alt="image" src="https://github.com/user-attachments/assets/d80548ae-046f-442f-8965-4199b9cacb83" />

Klasörünüze baktığınızda içi karmaşık harf ve sayılarla dolu iki tane .txt dosyası oluştuğunu göreceksiniz. Bunlar birazdan GitHub'a yapıştıracağımız metinler.
<img width="878" height="407" alt="image" src="https://github.com/user-attachments/assets/733ca294-25e1-4c99-895c-bb7f67390463" />


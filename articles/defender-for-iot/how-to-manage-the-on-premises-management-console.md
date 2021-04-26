---
title: Şirket içi yönetim konsolunu yönetme
description: Yedekleme ve geri yükleme, ana bilgisayar adını tanımlama ve algılayıcılar için bir proxy ayarlama gibi şirket içi yönetim konsolu seçenekleri hakkında bilgi edinin.
ms.date: 1/12/2021
ms.topic: article
ms.openlocfilehash: 871c74eee4b74538a8a09188953916ff7376bc8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781730"
---
# <a name="manage-the-on-premises-management-console"></a>Şirket içi yönetim konsolunu yönetme

Bu makalede yedekleme ve geri yükleme, komite cihaz etkinleştirme dosyasını indirme, sertifikaları güncelleştirme ve bir proxy 'nin algılayıcılar 'a kurulması gibi şirket içi yönetim konsolu seçenekleri ele alınmaktadır.

Şirket içi yönetim konsolunu Azure portal ekleyin.

## <a name="upload-an-activation-file"></a>Bir etkinleştirme dosyasını karşıya yükle

İlk kez oturum açtığınızda, şirket içi yönetim konsolu için bir etkinleştirme dosyası indirilir. Bu dosya ekleme işlemi sırasında tanımlanan toplam kaydedilmiş cihazları içerir. Liste, birden çok abonelikle ilişkili algılayıcılar içerir.

İlk etkinleştirmeden sonra, izlenen cihazların sayısı ekleme sırasında tanımlanan kaydedilmiş cihazların sayısını aşabilir. Bu olay, örneğin, yönetim konsoluna daha fazla algılayıcı bağladığınızda meydana gelebilir. İzlenen cihazların sayısıyla kaydedilmiş cihazların sayısı arasında bir tutarsızlık varsa, yönetim konsolunda bir uyarı görüntülenir. Bu olay oluşursa, yeni bir etkinleştirme dosyası yüklemeniz gerekir.

Bir etkinleştirme dosyasını karşıya yüklemek için:

1. IoT için Azure Defender **fiyatlandırma** sayfasına gidin.
1. **Yönetim Konsolu için etkinleştirme dosyasını indirin** sekmesini seçin. Etkinleştirme dosyası indirilir.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="Etkinleştirme dosyasını indirin.":::

1. Yönetim konsolundan **sistem ayarları** ' nı seçin.
1. **Etkinleştirme**'yi seçin.
1. **Dosya Seç** ' i seçin ve kaydettiğiniz dosyayı seçin.

## <a name="manage-certificates"></a>Sertifikaları yönetme

Şirket içi yönetim konsolunun yüklenmesinden sonra, yerel olarak imzalanan bir sertifika oluşturulur ve yönetim konsolunun Web uygulamasına erişim için kullanılır. Yönetici kullanıcılar yönetim konsolunda ilk kez oturum açtığında, bir SSL/TLS sertifikası sağlamaları istenir. İlk kez kurulum hakkında daha fazla bilgi için bkz. Şirket [içi yönetim konsolunuzu etkinleştirme ve ayarlama](how-to-activate-and-set-up-your-on-premises-management-console.md).

Aşağıdaki bölümlerde, sertifikaları güncelleştirme, sertifika CLı komutlarıyla çalışma ve desteklenen sertifikalar ve sertifika parametreleri hakkında bilgiler sağlanmaktadır.

### <a name="about-certificates"></a>Sertifikalar hakkında

IoT için Azure Defender, SSL ve TLS sertifikalarını şu amaçlarla kullanır:

- CA imzalı sertifikayı karşıya yükleyerek kuruluşunuz tarafından istenen belirli sertifika ve şifreleme gereksinimlerini karşılayın.

- Yönetim Konsolu ile bağlı sensörler arasında ve bir yönetim konsolu ile yüksek kullanılabilirliğe sahip bir yönetim konsolu arasında doğrulamaya izin verin. Doğrulama, bir sertifika iptal listesi ve sertifika sona erme tarihine göre değerlendirilir. *Doğrulama başarısız olursa, Yönetim Konsolu ile algılayıcı arasındaki iletişim durdurulur ve konsolda bir doğrulama hatası görüntülenir*. Bu seçenek, yüklemeden sonra varsayılan olarak etkinleştirilmiştir.

Üçüncü taraf iletme kuralları doğrulanmaz. SYSLOG, splunk veya ServiceNow 'a gönderilen uyarı bilgileri örnekleri verilmiştir. ve Active Directory iletişim.

#### <a name="ssl-certificates"></a>SSL sertifikaları

IoT algılayıcısı ve şirket içi yönetim konsolu için Defender, aşağıdaki işlevler için SSL ve TLS sertifikaları kullanır: 

 - Kullanıcılar ve gerecin Web konsolu arasındaki iletişimin güvenliğini sağlayın. 
 
 - Algılayıcı ve şirket içi yönetim konsolundaki REST API için güvenli iletişim.
 
 - Algılayıcılar ve şirket içi yönetim konsolu arasındaki iletişimin güvenliğini sağlayın. 

Yüklendikten sonra gereç, web konsoluna ön erişim sağlamak için yerel bir otomatik olarak imzalanan sertifika oluşturur. Kurumsal SSL ve TLS sertifikaları [`cyberx-xsense-certificate-import`](#cli-commands) komut satırı aracı kullanılarak yüklenebilir. 

 > [!NOTE]
 > Gereçanın istemci ve oturum başlatıcısı olduğu tümleştirmeler ve iletme kuralları için, belirli sertifikalar kullanılır ve sistem sertifikalarıyla ilgili değildir.  
 >
 >Bu durumlarda, Sertifikalar genellikle sunucudan alınır veya tümleştirmeyi ayarlamak için belirli bir sertifikanın sağlandığı asimetrik şifrelemeyi kullanır. 

### <a name="update-certificates"></a>Sertifikaları güncelleştirme

Şirket içi yönetim konsolunun yönetici kullanıcıları sertifikaları güncelleştirebilir.

Bir sertifikayı güncelleştirmek için:  

1. **Sistem ayarları**' nı seçin.

1. **SSL/TLS sertifikaları**' nı seçin.
1. Sertifikayı silin veya düzenleyin ve yeni bir tane ekleyin.
   
   - Bir sertifika adı ekleyin.
   
   - Bir CRT dosyası ve anahtar dosyası yükleyin ve bir parola girin.
   - Gerekirse bir ped dosyası yükleyin.

Doğrulama ayarını değiştirmek için:

1. **Sertifika doğrulamasını etkinleştir** seçeneğini açın veya devre dışı bırakın.

1. **Kaydet**’i seçin.

Seçenek etkinleştirilirse ve doğrulama başarısız olursa, Yönetim Konsolu ile algılayıcı arasındaki iletişim durdurulur ve konsolda bir doğrulama hatası görüntülenir.

### <a name="certificate-support"></a>Sertifika desteği

Aşağıdaki sertifikalar desteklenir:

- Özel ve Kurumsal anahtar altyapısı (özel PKI)
 
- Ortak anahtar altyapısı (genel PKI) 

- Gereç üzerinde yerel olarak oluşturuldu (yerel olarak kendinden imzalı) 

  > [!IMPORTANT]
  > Otomatik olarak imzalanan sertifikalar kullanmanızı önermiyoruz. Bu bağlantı türü güvenli değildir ve yalnızca test ortamları için kullanılmalıdır. Bu yana, sertifikanın sahibi doğrulanamaz ve sisteminizin güvenliği korunamamakta olduğundan, otomatik olarak imzalanan sertifikalar, üretim ağları için asla kullanılmamalıdır.

### <a name="supported-ssl-certificates"></a>Desteklenen SSL sertifikaları 

Aşağıdaki parametreler desteklenir: 

**Sertifika CRT**

- Etki alanı adınızın birincil sertifika dosyası

- İmza algoritması = SHA256RSA
- İmza karma algoritması = SHA256
- Geçerlilik = geçerli bir geçmiş tarihi
- Geçerli = geçerli bir gelecek tarih
- Ortak anahtar = RSA 2048 bitleri (minimum) veya 4096 bit
- CRL dağıtım noktası =. CRL dosyasının URL 'SI
- Konu CN = URL, joker bir sertifika olabilir; Örneğin, sensör. contoso. <span> com, veya *. contoso. <span> e
- Subject (C) ons TRY = tanımlanmış, örneğin, US
- Konu (OU) kuruluş birimi = tanımlı; Örneğin, contoso Labs
- Konu (O) rganleştirme = tanımlı; Örneğin, contoso Inc

**Anahtar dosyası**

- CSR 'yi oluştururken oluşturulan anahtar dosya

- RSA 2048 bit (minimum) veya 4096 bit

 > [!Note]
 > 4096bit anahtar uzunluğu kullanılıyor:
 > - Her bağlantının başlangıcında SSL el sıkışması daha yavaş olacaktır.  
 > - El sıkışma sırasında CPU kullanımında artış vardır. 

**Sertifika zinciri**

- CA 'nız tarafından sağlanan ara sertifika dosyası (varsa).

- Sunucu sertifikasını veren CA sertifikası, dosyanın ilk olarak köke kadar olan diğer kullanıcılar tarafından gelmelidir. 
- Zincir, paket öznitelikleri içerebilir.

**Deyimi**

- Bir anahtar desteklenir.

- Sertifikayı içeri aktarırken ayarlayın.

Diğer parametrelere sahip sertifikalar işe yarasa da Microsoft bunları desteklemez.

#### <a name="encryption-key-artifacts"></a>Şifreleme anahtarı yapıtları

**. pek: sertifika kapsayıcı dosyası**

Gizlilik Gelişmiş posta (pek) dosyaları, e-postaların güvenliğini sağlamak için kullanılan genel dosya türüdür. Günümüzde, pek dosyaları sertifikalarla birlikte kullanılır ve x509 ASN1 anahtarlarını kullanır.  

Kapsayıcı dosyası, yalnızca ortak sertifikayı içerebilen bir kapsayıcı biçimi olan RFC 1421 ' de 1424 ' de tanımlanır. Örneğin, Apache yüklemeleri, CA sertifikası, dosyalar, vs, SSL veya CERT. Bu, ortak anahtar, özel anahtar ve kök sertifikaları dahil olmak üzere tüm sertifika zincirini içerebilir.  

Ayrıca, ped 'ye çevrilebilen PKCS10 biçimi olarak bir CSR 'yi de kodlayabilir.

**. cert. cer. CRT: sertifika kapsayıcı dosyası**

`.pem` `.der` Farklı bir uzantıya sahip veya biçimli bir dosya. Dosya Windows Gezgini tarafından sertifika olarak tanınır. `.pem`   Dosya Windows Gezgini tarafından tanınmıyor.

**. Key: özel anahtar dosyası**

Anahtar dosya, ped dosyasıyla aynı biçimde, ancak farklı bir uzantıya sahiptir. 

#### <a name="additional-commonly-available-key-artifacts"></a>Yaygın olarak kullanılan ek anahtar yapıtları

**. CSR-sertifika imzalama isteği**.  

Bu dosya, sertifika yetkililerine göndermek için kullanılır. Gerçek biçim, RFC 2986 ' de tanımlanan PKCS10 ' dir ve istenen sertifikanın bazı veya tüm önemli ayrıntılarını içerebilir. Örneğin, konu, kuruluş ve durum. Bu, CA tarafından imzalanan sertifikanın ortak anahtarıdır ve döndürülen bir sertifikayı alır.  

Döndürülen sertifika, ortak anahtarı içeren ancak özel anahtarı içermeyen genel sertifikadır. 

**. PKCS12. pfx. p12 – Password kapsayıcısı**. 

Public-Key şifreleme standartları (PKCS) tarafından başlangıçta RSA tarafından tanımlanan 12 değişkeni başlangıçta Microsoft tarafından geliştirilmiştir ve daha sonra RFC 7292 olarak gönderilir.  

Bu kapsayıcı biçimi hem ortak hem de özel sertifika çiftlerini içeren bir parola gerektirir. `.pem`   Dosyaların aksine bu kapsayıcı tamamen şifrelenir.  

OpenSSL kullanarak dosyayı `.pem`   hem ortak hem de özel anahtarlarla bir dosyaya dönüştürebilirsiniz: `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**. der – ikili kodlu ped**.

ASN. 1 sözdizimini binary olarak kodlama yöntemi, `.pem`   yalnızca Base64 kodlamalı bir dosya olan bir dosyadır `.der` . 

OpenSSL, bu dosyaları bir: olarak `.pem` dönüştürebilir  `openssl x509 -inform der -in to-convert.der -out converted.pem` .  

Windows, bu dosyaları sertifika dosyası olarak tanır. Varsayılan olarak, Windows sertifikaları `.der` farklı bir uzantıya sahip biçimli dosya olarak dışarı aktarır.

**. CRL-sertifika iptal listesi**.  

Sertifika yetkilileri bu işlemleri, süresi dolmadan önce sertifikaları yetkilendirmek için bir yol olarak oluşturur. 

#### <a name="cli-commands"></a>CLI komutları

`cyberx-xsense-certificate-import`Sertifikaları içeri aktarmak için CLI komutunu kullanın. Bu aracı kullanmak için, WinSCP veya wget gibi araçları kullanarak sertifika dosyalarını cihaza yüklemeniz gerekir.

Komut aşağıdaki giriş bayraklarını destekler:

- `-h`: Komut satırı yardım söz dizimini gösterir.

- `--crt`: Bir sertifika dosyasının yolu (. CRT uzantısı).

- `--key`:  \* . Key dosyası. Anahtar uzunluğu en az 2.048 bit olmalıdır.

- `--chain`: Bir sertifika zinciri dosyasının yolu (isteğe bağlı).

- `--pass`: Sertifikayı şifrelemek için kullanılan parola (isteğe bağlı).

- `--passphrase-set`: Varsayılan = `False` , kullanılmıyor. `True`Önceki sertifikayla sağlanan önceki parolayı kullanmak için olarak ayarlayın (isteğe bağlı).

CLı komutunu kullanırken:

- Sertifika dosyalarının gereç üzerinde okunabilir olduğunu doğrulayın.

- Sertifikadaki etki alanı adının ve IP 'nin, BT departmanının planlandığından yapılandırma ile eşleştiğinden emin olun.

### <a name="use-openssl-to-manage-certificates"></a>Sertifikaları yönetmek için OpenSSL kullanma

Aşağıdaki komutlarla sertifikalarınızı yönetin:

| Description | CLı komutu |
|--|--|
| Yeni bir özel anahtar ve sertifika Imzalama Isteği oluştur | `openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key` |
| Otomatik olarak imzalanan bir sertifika oluşturma | `openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt` |
| Mevcut bir özel anahtar için bir sertifika imzalama isteği (CSR) oluştur | `openssl req -out CSR.csr -key privateKey.key -new` |
| Mevcut bir sertifikayı temel alan bir sertifika imzalama isteği oluştur | `openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey privateKey.key` |
| Özel anahtardan bir parolayı kaldırma | `openssl rsa -in privateKey.pem -out newPrivateKey.pem` |

Bir sertifika, CSR veya özel anahtar içindeki bilgileri denetlemeniz gerekiyorsa, bu komutları kullanın;

| Description | CLı komutu |
|--|--|
| Sertifika Imzalama Isteği 'ni (CSR) denetleme | `openssl req -text -noout -verify -in CSR.csr` |
| Özel anahtarı denetleme | `openssl rsa -in privateKey.key -check` |
| Bir sertifikayı denetleme | `openssl x509 -in certificate.crt -text -noout`  |

Özel anahtarın sertifikayla eşleşmemesi veya bir siteye yüklediğiniz bir sertifikanın güvenilir olmadığını belirten bir hata alırsanız, hatayı onarmak için bu komutları kullanın;

| Description | CLı komutu |
|--|--|
| Bir CSR veya özel anahtardaki ile eşleştiğinden emin olmak için ortak anahtarın MD5 karmasını denetleyin | 1. `openssl x509 -noout -modulus -in certificate.crt | openssl md5` <br /> iki. `openssl rsa -noout -modulus -in privateKey.key | openssl md5` <br /> 03. `openssl req -noout -modulus -in CSR.csr | openssl md5 ` |

Sertifikaları ve anahtarları, belirli sunucu türleri veya yazılımlar ile uyumlu hale getirmek üzere farklı biçimlere dönüştürmek için, bu komutları kullanın;

| Description | CLı komutu |
|--|--|
| DER dosyasını (. CRT. cer. der) ped 'ye Dönüştür  | `openssl x509 -inform der -in certificate.cer -out certificate.pem`  |
| PED dosyasını DER öğesine Dönüştür | `openssl x509 -outform der -in certificate.pem -out certificate.der`  |
| Özel anahtar ve sertifikalar içeren bir PKCS # 12 dosyasını (. pfx. p12) ped 'ye Dönüştür | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes` <br />`-nocerts`Yalnızca özel anahtarı çıkışa ekleyebilir veya `-nokeys` yalnızca sertifikaların çıktısını almak için ekleme yapabilirsiniz. |
| PEK sertifika dosyasını ve özel anahtarı PKCS # 12 (. pfx. p12) öğesine Dönüştür | `openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt` |

## <a name="define-backup-and-restore-settings"></a>Yedekleme ve geri yükleme ayarlarını tanımlayın

Şirket içi Yönetim Konsolu sistem yedeklemesi, her gün otomatik olarak gerçekleştirilir. Veriler farklı bir diske kaydedilir. Varsayılan konum `/var/cyberx/backups` . 

Bu dosyayı otomatik olarak iç ağa aktarabilirsiniz. 

> [!NOTE]
> Yedekleme ve geri yükleme yordamını yalnızca aynı sürümde gerçekleştirebilirsiniz. 

Şirket içi yönetim konsolu makinesini yedeklemek için: 

- Bir yönetim hesabında oturum açın ve girin `sudo cyberx-management-backup -full` .

En son yedekleme dosyasını geri yüklemek için:

- Bir yönetim hesabında oturum açın ve girin `$ sudo cyberx-management-system-restore` .

Yedeği bir dış SMB sunucusuna kaydetmek için:

1. Dış SMB sunucusunda paylaşılan bir klasör oluşturun.  

   SMB sunucusuna erişmek için gereken klasör yolunu, Kullanıcı adını ve parolayı alın. 

2. IoT için Defender 'da yedeklemeler için bir dizin oluşturun:

   - `sudo mkdir /<backup_folder_name_on_ server>` 

   - `sudo chmod 777 /<backup_folder_name_on_c_server>/` 

3. Fstab Düzenle:  

   - `sudo nano /etc/fstab` 

   - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

4. Paylaşılacak SMB sunucusu için kimlik bilgilerini düzenleyin veya oluşturun: 

   - `sudo nano /etc/samba/user` 

5. Ekle: 

   - `username=<user name>`

   - `password=<password>` 

6. Dizini bağlama: 

   - `sudo mount -a` 

7. Bir yedekleme dizinini IoT şirket içi yönetim konsolu için Defender 'da paylaşılan klasöre yapılandırın:  

   - `sudo nano /var/cyberx/properties/backup.properties` 

   - `set Backup.shared_location to <backup_folder_name_on_server>`

## <a name="edit-the-host-name"></a>Konak adını Düzenle

Yönetim konsolunun Kurumsal DNS sunucusunda yapılandırılan ana bilgisayar adını düzenlemek için:

1. Yönetim konsolunun sol bölmesinde **sistem ayarları**' nı seçin.  

2. Konsolunun ağ bölümünde **ağ**' ı seçin. 

3. Kuruluş DNS sunucusunda yapılandırılan ana bilgisayar adını girin. 

4. **Kaydet**’i seçin.

## <a name="define-vlan-names"></a>VLAN adlarını tanımlama

VLAN adları, algılayıcı ve yönetim konsolu arasında eşitlenmez. Bileşenlerde özdeş adlar tanımlamanız gerekir.

Ağ alanı ' nda, **VLAN** ' ı seçin ve bulunan VLAN kimliklerine ad ekleyin. Sonra **Kaydet**'i seçin.

## <a name="define-a-proxy-to-sensors"></a>Algılayıcılar için bir proxy tanımlayın

Kullanıcının doğrudan sensöre oturum açmasını önleyerek sistem güvenliğini geliştirin. Bunun yerine, kullanıcıların algılayıcıya şirket içi yönetim konsolundan tek bir güvenlik duvarı kuralıyla erişmesine izin vermek için proxy Tüneli kullanın. Bu geliştirme, algılayıcı ötesinde ağ ortamına yetkisiz erişim olasılığını daraltır.

Algılayıcıların doğrudan bağlantısının olmadığı ortamlarda ara sunucu kullanın.

:::image type="content" source="media/how-to-access-sensors-using-a-proxy/proxy-diagram.png" alt-text="Kullanıcısını.":::

Aşağıdaki yordam, şirket içi yönetim konsoluna bir algılayıcı bağlar ve bu konsolda tünelleme sağlar:

1. Yönetici kimlik bilgileriyle şirket içi yönetim konsolu gereci CLı ' de oturum açın.

2. Yazın `sudo cyberx-management-tunnel-enable` ve **ENTER**' u seçin.

4. Yazın `--port 10000` ve **ENTER**' u seçin.

## <a name="adjust-system-properties"></a>Sistem özelliklerini ayarla

Sistem Özellikleri yönetim konsolundaki çeşitli işlemleri ve ayarları denetler. Bunları düzenlemek veya değiştirmek, yönetim konsolunun işlemine zarar verebilir. Ayarlarınızı değiştirmeden önce [Microsoft desteği](https://support.microsoft.com) başvurun.

Sistem özelliklerine erişmek için: 

1. Şirket içi yönetim konsolunda veya sensörde oturum açın.

2. **Sistem ayarları**' nı seçin.

3. **Genel** bölümünden **Sistem Özellikleri** ' ni seçin.

## <a name="change-the-name-of-the-on-premises-management-console"></a>Şirket içi yönetim konsolunun adını değiştirme

Şirket içi yönetim konsolunun adını değiştirebilirsiniz. Yeni adlar konsol Web tarayıcısında, çeşitli konsol pencerelerinin ve sorun giderme günlüklerinde görüntülenir. Varsayılan ad **yönetim konsoludur**.

Adı değiştirmek için:

1. Sol bölmenin en altında, geçerli adı seçin.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/console-name.png" alt-text="Şirket içi yönetim konsolu sürümünün ekran görüntüsü.":::

2. **Yönetim Konsolu yapılandırmasını düzenle** iletişim kutusunda, yeni adı girin. Ad 25 karakterden uzun olamaz.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/edit-management-console-configuration.png" alt-text="IoT platform yapılandırması için Defender 'ı düzenlemenin ekran görüntüsü.":::

3. **Kaydet**’i seçin. Yeni ad uygulandı.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/name-changed.png" alt-text="Konsolun değiştirilen adını gösteren ekran görüntüsü.":::

## <a name="password-recovery"></a>Parola kurtarma

Şirket içi yönetim konsolunuz için parola kurtarma, cihazın eklendiği aboneliğe bağlıdır. Bir cihazın hangi abonelikte ekli olduğunu bilmiyorsanız parolayı kurtaramazsınız.

Parolanızı sıfırlamak için:

1. Şirket içi yönetim konsolunun oturum açma sayfasına gidin.
1. **Parola kurtarma**' yı seçin.
1. Benzersiz tanımlayıcıyı kopyalayın.
1. IoT **siteleri ve sensör** için Defender sayfasına gidin ve **parolamı kurtar** sekmesini seçin.
1. Benzersiz tanımlayıcıyı girip **kurtar**' ı seçin. Etkinleştirme dosyası indirilir.
1. **Parola kurtarma** sayfasına gidin ve etkinleştirme dosyasını karşıya yükleyin.
1. **İleri**’yi seçin.
 
   Artık Kullanıcı adınız ve sistem tarafından oluşturulan yeni bir parola vermiş olursunuz.

> [!NOTE]
> Algılayıcı, ilk olarak bağlandığı aboneliğe bağlanır. Parolayı yalnızca, eklendiği abonelik ile kurtarabilirsiniz.

## <a name="update-the-software-version"></a>Yazılım sürümünü güncelleştirme

Aşağıdaki yordamda, şirket içi yönetim konsolu yazılımının sürümünün nasıl güncelleştirileceğini açıklanmaktadır. Güncelleştirme işlemi yaklaşık 30 dakika sürer.

1. [Azure Portal](https://portal.azure.com/) gidin.

1. IoT için Defender 'a gidin.

1. **Güncelleştirmeler** sayfasına gidin.

1. Şirket içi yönetim konsolu bölümünden bir sürüm seçin.

1. **İndir** ve dosyayı Kaydet ' i seçin.

1. Şirket içi yönetim konsolunda oturum açın ve Yan menüden **sistem ayarları** ' nı seçin.

1. **Sürüm güncelleştirmesi** bölmesinde **Güncelleştir**' i seçin.

1. IoT **güncelleştirmeleri** için Defender sayfasından indirdiğiniz dosyayı seçin.

## <a name="mail-server-settings"></a>Posta sunucusu ayarları

Şirket içi yönetim konsolu için SMTP posta sunucusu ayarlarını tanımlayın.

Şunları tanımlamak için:

1. Yönetim kimlik bilgileriyle şirket içi yönetim için CLı 'da oturum açın.
1. ```nano /var/cyberx/properties/remote-interfaces.properties``` yazın.
1. Enter'a basın. Aşağıdaki istemler görüntülenir.
```mail.smtp_server= ```
```mail.port=25 ```
```mail.sender=```
1. SMTP sunucusu adını ve göndereni girip ENTER ' u seçin.

## <a name="see-also"></a>Ayrıca bkz.

[Yönetim konsolundan algılayıcıları yönetme](how-to-manage-sensors-from-the-on-premises-management-console.md)

[Algılayıcıları ayrı ayrı yönetme](how-to-manage-individual-sensors.md)

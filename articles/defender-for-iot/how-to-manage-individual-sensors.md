---
title: Algılayıcıları ayrı ayrı yönetme
description: Etkinleştirme dosyalarını yönetme, yedeklemeleri gerçekleştirme ve tek başına algılayıcıyı güncelleştirme dahil olmak üzere tek tek algılayıcıları yönetmeyi öğrenin.
ms.date: 02/02/2021
ms.topic: how-to
ms.openlocfilehash: df1d1dd6ca2085fb3ab12c104723c63e32249403
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781968"
---
# <a name="manage-individual-sensors"></a>Algılayıcıları ayrı ayrı yönetme

Bu makalede, tek tek sensörların nasıl yönetileceği açıklanır. Görevler etkinleştirme dosyalarını yönetmeyi, yedeklemeleri gerçekleştirmeyi ve tek başına algılayıcıyı güncellemeyi içerir.

Ayrıca, birden çok sensörün eşzamanlı olarak yönetilebileceği şirket içi yönetim konsolundan bazı algılayıcı yönetim görevlerini de yapabilirsiniz.

Algılayıcı ekleme ve kayıt için Azure portal kullanırsınız.

## <a name="manage-sensor-activation-files"></a>Algılayıcı etkinleştirme dosyalarını Yönet

Sensörinizi Azure portal IoT için Azure Defender ile eklendi. Her algılayıcı yerel olarak bağlı bir algılayıcı veya buluta bağlı bir algılayıcı olarak eklendi.

Dağıttığınız her sensöre benzersiz bir etkinleştirme dosyası yüklenir. Yeni bir dosyanın ne zaman ve nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [Yeni etkinleştirme dosyalarını karşıya yükleme](#upload-new-activation-files). Dosyayı karşıya yükleyegöremiyorsanız, bkz. [etkinleştirme dosya yükleme sorunlarını giderme](#troubleshoot-activation-file-upload).

### <a name="about-activation-files-for-locally-connected-sensors"></a>Yerel olarak bağlı sensörler için etkinleştirme dosyaları hakkında

Yerel olarak bağlı sensörler bir Azure aboneliğiyle ilişkilendirilir. Yerel olarak bağlı sensörlerinizin etkinleştirme dosyası bir sona erme tarihi içerir. Bu tarihten bir ay önce, algılayıcı konsolunun en üstünde bir uyarı iletisi görünür. Uyarı, etkinleştirme dosyasını güncelleştirinceye kadar kalır.

:::image type="content" source="media/how-to-manage-individual-sensors/system-setting-screenshot.png" alt-text="Sistem ayarlarının ekran görüntüsü.":::

Etkinleştirme dosyasının süresi dolmuşsa bile IoT özellikleri için Defender ile çalışmaya devam edebilirsiniz. 

### <a name="about-activation-files-for-cloud-connected-sensors"></a>Buluta bağlı sensörler için etkinleştirme dosyaları hakkında

Bulut bağlantısı olan sensörler, IoT Hub için Defender ile ilişkilendirilir. Bu algılayıcılar, etkinleştirme dosyası için zaman dilimleriyle sınırlı değildir. Bulut bağlantılı algılayıcılar için etkinleştirme dosyası, IoT Hub için Defender ile bağlantı sağlamak üzere kullanılır.

### <a name="upload-new-activation-files"></a>Yeni etkinleştirme dosyalarını karşıya yükle

Şu durumlarda bir eklendi algılayıcısı için yeni bir etkinleştirme dosyası yüklemeniz gerekebilir:

- Yerel olarak bağlı bir sensörde etkinleştirme dosyası süresi dolar. 

- Farklı bir algılayıcı yönetim modunda çalışmak istiyorsunuz. 

- Bir bulut bağlantılı sensöre IoT Hub 'ı için yeni bir Defender atamak istiyorsunuz.

Yeni bir etkinleştirme dosyası eklemek için:

1. **Algılayıcı yönetimi** sayfasına gidin.

2. Yeni bir etkinleştirme dosyası yüklemek istediğiniz algılayıcıyı seçin.

3. Silin.

4. Yeni modda **ekleme** sayfasından veya IoT Hub 'ı için yeni bir Defender ile algılayıcısı yeniden ekleyin.

5. Etkinleştirme dosyasını **Indirme yükleme** sayfasından indirin.

6. Dosyayı kaydedin.

    :::image type="content" source="media/how-to-manage-individual-sensors/download-activation-file.png" alt-text="IoT Hub 'ı için Defender 'dan etkinleştirme dosyasını indirin.":::

7. IoT algılayıcısı konsolunda Defender 'da oturum açın.

8. Algılayıcı konsolunda **sistem ayarları** yeniden etkinleştirme ' yi seçin  >  .

    :::image type="content" source="media/how-to-manage-individual-sensors/reactivation.png" alt-text="Sistem ayarları ekranında seçimi yeniden etkinleştirme.":::

9. **Karşıya yükle** ' yi seçin ve kaydettiğiniz dosyayı seçin.

    :::image type="content" source="media/how-to-manage-individual-sensors/upload-the-file.png" alt-text="Kaydettiğiniz dosyayı karşıya yükleyin.":::

10. **Etkinleştir**' i seçin.

### <a name="troubleshoot-activation-file-upload"></a>Etkinleştirme dosya yükleme sorunlarını giderme

Etkinleştirme dosyası yüklenemediğinden bir hata iletisi alırsınız. Aşağıdaki olaylar oluşmuş olabilir:

- **Yerel olarak bağlı sensörler için**: etkinleştirme dosyası geçerli değil. Dosya geçerli değilse IoT portalı için Defender 'a gidin. **Algılayıcı yönetimi** sayfasında, geçersiz dosya ile algılayıcı ' ı seçin ve yeni bir etkinleştirme dosyası indirin.

- **Buluta bağlı sensörler için**: algılayıcı internet 'e bağlanamıyor. Algılayıcının ağ yapılandırmasını denetleyin. Sensörizin internet 'e erişmek için bir Web proxy üzerinden bağlanması gerekiyorsa, **algılayıcı ağ yapılandırması** ekranında ara sunucunuzun doğru şekilde yapılandırıldığını doğrulayın. \*. Azure-Devices.net:443 güvenlik duvarında ve/veya proxy 'de izin verildiğini doğrulayın. Joker karakterler desteklenmiyorsa veya daha fazla denetim istiyorsanız, IoT Hub için özel Defender 'ın FQDN 'SI güvenlik duvarınız ve/veya ara sunucunuzda açılmalıdır. Ayrıntılar için bkz. [başvuru IoT Hub uç noktaları](../iot-hub/iot-hub-devguide-endpoints.md).  

- **Buluta bağlı sensörler için**: etkinleştirme dosyası geçerli, ancak IoT için Defender tarafından reddedildi. Bu sorunu gideremezseniz, IoT portalının Defender 'ın siteler ve algılayıcılar sayfasından başka bir etkinleştirme indirebilirsiniz. Bu işe yaramazsa Microsoft Desteği başvurun.

## <a name="manage-certificates"></a>Sertifikaları yönetme

Algılayıcı yüklemesinin ardından, yerel olarak imzalanan bir sertifika oluşturulur ve algılayıcı Web uygulamasına erişim için kullanılır. Sensörde ilk kez oturum açarken, Yönetici kullanıcılardan bir SSL/TLS sertifikası sağlaması istenir.  İlk kurulum hakkında daha fazla bilgi için bkz. [oturum açma ve algılayıcı etkinleştirme](how-to-activate-and-set-up-your-sensor.md).

Bu makalede, sertifikaları güncelleştirme, sertifika CLı komutlarıyla çalışma ve desteklenen sertifikalar ve sertifika parametreleri hakkında bilgi verilmektedir.

### <a name="about-certificates"></a>Sertifikalar hakkında

IoT için Azure Defender, SSL/TLS sertifikalarını şu amaçlarla kullanır:

- CA imzalı sertifikayı karşıya yükleyerek kuruluşunuz tarafından istenen belirli sertifika ve şifreleme gereksinimlerini karşılayın.

- Yönetim Konsolu ile bağlı sensörler arasında ve bir yönetim konsolu ve yüksek oranda kullanılabilir bir yönetim konsolu arasında doğrulamaya izin verin. Doğrulamalar bir sertifika Iptal listesi ve sertifikanın sona erme tarihi ile değerlendirilir. *Doğrulama başarısız olursa, Yönetim Konsolu ile algılayıcı arasındaki iletişim durdurulur ve konsolda bir doğrulama hatası sunulur*. Bu seçenek, yüklemeden sonra varsayılan olarak etkinleştirilmiştir.

 Üçüncü taraf Iletme kuralları, örneğin SYSLOG 'a gönderilen uyarı bilgileri, splunk veya ServiceNow; ya da Active Directory iletişim doğrulanmaz.

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

Gereçler, benzersiz sertifika dosyaları kullanabilir. Bir sertifikayı değiştirmeniz gerekiyorsa, karşıya yüklediğiniz,

- Sürüm 10,0 ' den, sertifika sistem ayarları menüsünden değiştirilebilir.

- 10,0 ' dan önceki sürümler için, SSL sertifikası komut satırı aracı kullanılarak değiştirilebilir.

### <a name="update-certificates"></a>Sertifikaları güncelleştirme

Algılayıcı yönetici kullanıcıları sertifikaları güncelleştirebilir.

Bir sertifikayı güncelleştirmek için:  

1. **Sistem ayarları**' nı seçin.

1. **SSL/TLS sertifikaları** ' nı seçin.
1. Sertifikayı silin veya düzenleyin ve yeni bir tane ekleyin.

    - Bir sertifika adı ekleyin.
    
    - Bir CRT dosyası ve anahtar dosyası yükleyin ve bir parola girin.
    - Gerekirse bir ped dosyası yükleyin.

Doğrulama ayarını değiştirmek için:

1. **Sertifika doğrulamasını etkinleştir** değiştirmeyi etkinleştirin veya devre dışı bırakın.

1. **Kaydet**’i seçin.

Seçenek etkinleştirilirse ve doğrulama başarısız olursa, Yönetim Konsolu ile algılayıcı arasındaki iletişim durdurulur ve konsolda bir doğrulama hatası sunulur.

### <a name="certificate-support"></a>Sertifika desteği

Aşağıdaki sertifikalar desteklenir:

- Özel ve Kurumsal anahtar altyapısı (özel PKI)

- Ortak anahtar altyapısı (genel PKI) 

- Gereç üzerinde yerel olarak oluşturuldu (yerel olarak kendinden imzalı). 

> [!IMPORTANT]
> Otomatik olarak imzalanan sertifikalar kullanmanızı önermiyoruz. Bu bağlantı türü güvenli değildir ve yalnızca test ortamları için kullanılmalıdır. Bu yana, sertifikanın sahibi doğrulanamaz ve sisteminizin güvenliği korunamamakta olduğundan, otomatik olarak imzalanan sertifikalar, üretim ağları için asla kullanılmamalıdır.

### <a name="supported-ssl-certificates"></a>Desteklenen SSL sertifikaları 

Aşağıdaki parametreler desteklenir. 

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
- Konu (OU) kuruluş birimi = tanımlı, örneğin, contoso Labs
- Konu (O) rganleştirme = tanımlanmış, örneğin, contoso Inc.

**Anahtar dosyası**

- CSR oluşturduğunuzda oluşturulan anahtar dosyası.

- RSA 2048 bitleri (minimum) veya 4096 bit.

 > [!Note]
 > 4096bit anahtar uzunluğu kullanılıyor:
 > - Her bağlantının başlangıcında SSL el sıkışması daha yavaş olacaktır.  
 > - El sıkışma sırasında CPU kullanımında artış vardır. 

**Sertifika Zinciri**

- CA 'nız tarafından sağlanan ara sertifika dosyası (varsa)

- Sunucu sertifikasını veren CA sertifikası, dosyanın ilk olarak köke kadar olan diğer kullanıcılar tarafından gelmelidir. 
- Paket öznitelikleri içerebilir.

**Deyimi**

- Bir anahtar destekleniyor.

- Sertifikayı içeri aktarırken ayarlayın.

Diğer parametrelere sahip sertifikalar işe yarasa da Microsoft bunları desteklemez.

#### <a name="encryption-key-artifacts"></a>Şifreleme anahtarı yapıtları

**. pek – sertifika kapsayıcı dosyası**

Gizlilik Gelişmiş posta (pek) dosyaları, e-postaların güvenliğini sağlamak için kullanılan genel dosya türüdür. Günümüzde, pek dosyaları sertifikalarla birlikte kullanılır ve x509 ASN1 anahtarlarını kullanır.  

Kapsayıcı dosyası, yalnızca ortak sertifikayı içerebilen bir kapsayıcı biçimi olan RFC 1421 ' de 1424 ' de tanımlanır. Örneğin, Apache yüklemeleri, CA sertifikası, dosyalar, vs, SSL veya CERT. Bu, ortak anahtar, özel anahtar ve kök sertifikaları dahil olmak üzere tüm sertifika zincirini içerebilir.  

Ayrıca, ped 'ye çevrilebilen PKCS10 biçimi olarak bir CSR 'yi de kodlayabilir.

**. cert. cer. CRT – sertifika kapsayıcı dosyası**

`.pem` `.der` Farklı bir uzantıya sahip veya biçimli bir dosya. Dosya Windows Gezgini tarafından sertifika olarak tanınır. `.pem`   Dosya Windows Gezgini tarafından tanınmıyor.

**. Key – özel anahtar dosyası**

Anahtar dosya, ped dosyasıyla aynı biçimde, ancak farklı bir uzantıya sahiptir.

#### <a name="additional-commonly-available-key-artifacts"></a>Yaygın olarak kullanılan ek anahtar yapıtları

**. CSR-sertifika imzalama isteği**.  

Bu dosya, sertifika yetkililerine göndermek için kullanılır. Gerçek biçim, RFC 2986 ' de tanımlanan PKCS10 ' dir ve istenen sertifikanın bazı veya tüm önemli ayrıntılarını içerebilir. Örneğin, konu, kuruluş ve durum. Bu, CA tarafından imzalanan sertifikanın ortak anahtarıdır ve döndürülen bir sertifikayı alır.  

Döndürülen sertifika, ortak anahtarı içeren ancak özel anahtarı içermeyen genel sertifikadır. 

**. PKCS12. pfx. p12 – Password kapsayıcısı**. 

Public-Key şifreleme standartları (PKCS) tarafından başlangıçta RSA tarafından tanımlanan 12 değişkeni başlangıçta Microsoft tarafından geliştirilmiştir ve daha sonra RFC 7292 olarak gönderilir.  

Bu kapsayıcı biçimi hem ortak hem de özel sertifika çiftlerini içeren bir parola gerektirir. `.pem`   Dosyaların aksine bu kapsayıcı tamamen şifrelenir.  

Bunu `.pem`   hem ortak hem de özel anahtarlarla bir dosyaya dönüştürmek Için OpenSSL kullanabilirsiniz: `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**. der – ikili kodlu ped**.

ASN. 1 sözdizimini binary olarak kodlama yöntemi, `.pem`   yalnızca Base64 kodlamalı bir dosya olan bir dosyadır `.der` . 

OpenSSL, bu dosyaları bir: olarak `.pem` dönüştürebilir  `openssl x509 -inform der -in to-convert.der -out converted.pem` .  

Windows, bu dosyaları sertifika dosyası olarak tanır. Varsayılan olarak, Windows sertifikaları `.der` farklı bir uzantıya sahip biçimli dosya olarak dışarı aktarır.  

**. CRL-sertifika iptal listesi**.  
Sertifika yetkilileri bu dosyaları, süresi dolmadan önce sertifikaların yetkisini kaldırmak için bir yol olarak oluşturur.
 
##### <a name="cli-commands"></a>CLI komutları

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

## <a name="connect-a-sensor-to-the-management-console"></a>Yönetim konsoluna bir algılayıcı bağlama

Bu bölümde, algılayıcı ve şirket içi yönetim konsolu arasında bağlantı sağlamak açıklanmaktadır. Bir AIR-gapped ağında çalışıyorsanız ve algılayıcıdan yönetim konsoluna cihaz ve uyarı bilgilerini göndermek istiyorsanız bunu yapmanız gerekir. Bu bağlantı ayrıca yönetim konsolunun sistem ayarlarını sensöre itmesi ve algılayıcısı üzerinde diğer yönetim görevlerini gerçekleştirmesini sağlar.

Bağlanmak için:

1. Şirket içi yönetim konsolunda oturum açın.

2. **Sistem ayarları**' nı seçin.

3. **Algılayıcı kurulumu – bağlantı dizesi** bölümünde, otomatik olarak oluşturulan bağlantı dizesini kopyalayın.

   :::image type="content" source="media/how-to-manage-individual-sensors/connection-string-screen.png" alt-text="Bağlantı dizesini bu ekrandan kopyalayın."::: 

4. Algılayıcı konsolunda oturum açın.

5. Sol bölmede **sistem ayarları**' nı seçin.

6. **Yönetim Konsolu bağlantısı**' nı seçin.

    :::image type="content" source="media/how-to-manage-individual-sensors/management-console-connection-screen.png" alt-text="Yönetim Konsolu bağlantısı iletişim kutusunun ekran görüntüsü.":::

7. Bağlantı dizesini **bağlantı dizesi** kutusuna yapıştırın ve **Bağlan**' ı seçin.

8. Şirket içi yönetim konsolunda, **site yönetimi** penceresinde, algılayıcıyı bir bölgeye atayın.

## <a name="change-the-name-of-a-sensor"></a>Bir sensör adını değiştirme

Algılayıcı konsolunuzun adını değiştirebilirsiniz. Yeni ad konsol Web tarayıcısında, çeşitli konsol pencerelerinin ve sorun giderme günlüklerinde görüntülenir.

Algılayıcı adlarını değiştirme işlemi, yerel olarak bağlı sensörlere ve buluta bağlı sensörlere göre değişir. Varsayılan ad **algılayıcı**' dır.

### <a name="change-the-name-of-a-locally-connected-sensor"></a>Yerel olarak bağlı bir sensör adını değiştirme

Adı değiştirmek için:

1. Konsolun sol bölmesinin en altında, geçerli algılayıcı etiketini seçin.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/label-name.png" alt-text="Algılayıcı etiketini gösteren ekran görüntüsü.":::

1. **Algılayıcı adını Düzenle** iletişim kutusunda bir ad girin.

1. **Kaydet**’i seçin. Yeni ad uygulandı.

### <a name="change-the-name-of-a-cloud-connected-sensor"></a>Buluta bağlı algılayıcının adını değiştirme

Sensör buluta bağlı bir algılayıcı olarak kaydedilmişse, algılayıcı adı kayıt sırasında atanan ad tarafından tanımlanır. Ad, ilk kez oturum açarken karşıya yüklediğiniz etkinleştirme dosyasına dahil edilir. Algılayıcının adını değiştirmek için yeni bir etkinleştirme dosyası yüklemeniz gerekir.

Adı değiştirmek için:

1. IoT için Azure Defender portalında, siteler ve algılayıcılar sayfasına gidin.

1. Algılayıcıyı siteler ve algılayıcılar sayfasından silin.

1. Başlarken sayfasından **algılayıcı** Ekle seçeneğini belirleyerek yeni adla kaydolun.

1. Yeni etkinleştirme dosyasını indirin.

1. IoT algılayıcısı konsolunda Defender 'da oturum açın.

1. Algılayıcı konsolunda, **sistem ayarları** ' nı seçin ve yeniden **etkinleştirme**' yi seçin.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Algılayıcıyı yeniden etkinleştirmek için etkinleştirme dosyanızı karşıya yükleyin.":::

1. **Karşıya yükle** ' yi seçin ve kaydettiğiniz dosyayı seçin.

1. **Etkinleştir**' i seçin.

## <a name="update-the-sensor-network-configuration"></a>Algılayıcı ağ yapılandırmasını güncelleştirme

Algılayıcı ağ yapılandırması, algılayıcı yüklemesi sırasında tanımlandı. Yapılandırma parametrelerini değiştirebilirsiniz. Ayrıca, bir ara sunucu yapılandırması da ayarlayabilirsiniz.

Yeni bir IP adresi oluşturursanız, yeniden oturum açmanız gerekebilir.

Yapılandırmayı değiştirmek için:

1. Yan menüde **sistem ayarları**' nı seçin.

2. **Sistem ayarları** penceresinde **ağ**' ı seçin.

    :::image type="content" source="media/how-to-manage-individual-sensors/edit-network-configuration-screen.png" alt-text="Ağ ayarlarınızı yapılandırın.":::

3. Parametreleri ayarlayın:

    | Parametre | Açıklama |
    |--|--|
    | IP Adresi | Algılayıcı IP adresi |
    | Alt ağ maskesi | Maske adresi |
    | Varsayılan ağ geçidi | Varsayılan ağ geçidi adresi |
    | DNS | DNS sunucusu adresi |
    | Konak adı | Algılayıcı konak adı |
    | Ara sunucu | Proxy Konağı ve bağlantı noktası adı |

4. **Kaydet**’i seçin.

## <a name="synchronize-time-zones-on-the-sensor"></a>Sensörde saat dilimlerini eşitler

Tüm kullanıcıların aynı zaman ve bölge görmesini sağlamak için sensör zamanını ve bölgesini yapılandırabilirsiniz.

:::image type="content" source="media/how-to-manage-individual-sensors/time-and-region.png" alt-text="Saat ve bölgeyi yapılandırın.":::

| Parametre | Açıklama |
|--|--|
| Saat dilimi | İçin saat dilimi tanımı:<br />-Uyarılar<br />-Eğilimler ve istatistik pencere öğeleri<br />-Veri araştırma raporları<br />   -Risk değerlendirmesi raporları<br />-Saldırı vektörleri |
| Tarih biçimi | Aşağıdaki biçim seçeneklerinden birini seçin:<br />-gg/aa/yyyy ss: DD: ss<br />-AA/gg/yyyy HH: mm: ss<br />-yyyy/AA/GG SS: DD: ss |
| Tarih ve saat | Geçerli tarih ve yerel saati seçtiğiniz biçimde görüntüler.<br />Örneğin, gerçek konumunuz Amerika ve New York ise, ancak saat dilimi Avrupa ve Berlin olarak ayarlandıysa, saat Istanbul yerel saatine göre görüntülenir. |

Algılayıcı süresini yapılandırmak için:

1. Yan menüde **sistem ayarları**' nı seçin.

2. **Sistem ayarları** penceresinde **saat & bölge**' yi seçin.

3. Parametreleri ayarlayın ve **Kaydet**' i seçin.

## <a name="set-up-backup-and-restore-files"></a>Yedekleme ve geri yükleme dosyalarını ayarlama

Sistem yedekleme, günlük 3:00 ' de otomatik olarak gerçekleştirilir. Veriler sensörde farklı bir diske kaydedilir. Varsayılan konum `/var/cyberx/backups` .

Bu dosyayı otomatik olarak iç ağa aktarabilirsiniz.

> [!NOTE]
> - Yedekleme ve geri yükleme yordamı yalnızca aynı sürümler arasında gerçekleştirilebilir.
> - Bazı mimarilerde yedekleme devre dışıdır. Bunu `/var/cyberx/properties/backup.properties` dosyada etkinleştirebilirsiniz.

Şirket içi yönetim konsolunu kullanarak bir algılayıcıyı denetlemenizin, bu yedeklemeleri toplamak ve bunları yönetim konsolunda veya bir dış yedekleme sunucusunda depolamak için sensör Yedekleme zamanlamasını kullanabilirsiniz.

**Yedeklenen:** Yapılandırma ve veriler.

**Yedeklenmeyen:** PCAP dosyaları ve günlükleri. PCAPs ve günlükleri el ile yedekleyebilir ve geri yükleyebilirsiniz.

Algılayıcı yedekleme dosyaları, aşağıdaki biçimde otomatik olarak adlandırılır: `<sensor name>-backup-version-<version>-<date>.tar` . `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar` bunun bir örneğidir.

Yedeklemeyi yapılandırmak için:

- Bir yönetim hesabında oturum açın ve girin `$ sudo cyberx-xsense-system-backup` .

En son yedekleme dosyasını geri yüklemek için:

- Bir yönetim hesabında oturum açın ve girin `$ sudo cyberx-xsense-system-restore` .

Yedeği bir dış SMB sunucusuna kaydetmek için:

1. Dış SMB sunucusunda paylaşılan bir klasör oluşturun.

    SMB sunucusuna erişmek için gereken klasör yolunu, Kullanıcı adını ve parolayı alın.

2. Sensörde yedeklemeler için bir dizin oluşturun:

    - `sudo mkdir /<backup_folder_name_on_cyberx_server>`

    - `sudo chmod 777 /<backup_folder_name_on_cyberx_server>/`

3. Düzenle `fstab` :

    - `sudo nano /etc/fstab`

    - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifsrw,credentials=/etc/samba/user,vers=X.X,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0`

4. SMB sunucusu için paylaşmak üzere kimlik bilgilerini düzenleyin ve oluşturun:

    `sudo nano /etc/samba/user` 

5. Ekle:

    - `username=&gt:user name&lt:`

    - `password=<password>`

6. Dizini bağlama:

    `sudo mount -a`

7. IoT algılayıcısı için Defender 'daki paylaşılan klasöre bir yedekleme dizini yapılandırın:  

    - `sudo nano /var/cyberx/properties/backup.properties`

    - `set backup_directory_path to <backup_folder_name_on_cyberx_server>`

### <a name="restore-sensors"></a>Algılayıcılar geri yükleme

Yedeklemeleri, algılayıcı konsolundan ve CLı kullanarak geri yükleyebilirsiniz.

**Konsolundan geri yüklemek için:**

- Algılayıcı **sistem ayarları** penceresinden **görüntüyü geri yükle** ' yi seçin.

:::image type="content" source="media/how-to-manage-individual-sensors/restore-image-screen.png" alt-text="Düğmeyi seçerek görüntünüzü geri yükleyin.":::

Konsol geri yükleme başarısızlıklarını görüntüler.

**CLı kullanarak geri yüklemek için:**

- Bir yönetim hesabında oturum açın ve girin `$ sudo cyberx-management-system-restore` .

## <a name="update-a-standalone-sensor-version"></a>Tek başına bir algılayıcı sürümünü güncelleştirme

Aşağıdaki yordamda, algılayıcı konsolunu kullanarak tek başına bir algılayıcıyı güncelleştirme işlemi açıklanmaktadır. Güncelleştirme işlemi yaklaşık 30 dakika sürer.

1. [Azure Portal](https://portal.azure.com/) gidin.

2. IoT için Defender 'a gidin.

3. **Güncelleştirmeler** sayfasına gidin.

   :::image type="content" source="media/how-to-manage-individual-sensors/updates-page.png" alt-text="IoT için Defender 'ın güncelleştirmeler sayfasının ekran görüntüsü.":::

4. **Sensör** bölümünden **İndir** ' i seçin ve dosyayı kaydedin.

5. Algılayıcı konsolunun kenar çubuğunda **sistem ayarları**' nı seçin.

6. **Sürüm yükseltme** bölmesinde, **Yükselt**' i seçin.

    :::image type="content" source="media/how-to-manage-individual-sensors/upgrade-pane-v2.png" alt-text="Yükseltme bölmesinin ekran görüntüsü.":::

7. IoT **güncelleştirmeleri** için Defender sayfasından indirdiğiniz dosyayı seçin.

8. Güncelleştirme işlemi başlar ve sistemin iki kez yeniden başlatıldığı zaman. İlk yeniden başlatmanın ardından (güncelleştirme işlemi tamamlanmadan önce) sistem, oturum açma penceresiyle açılır. Oturum açtıktan sonra, yükseltme sürümü kenar çubuğunun sol alt kısmında görünür.

    :::image type="content" source="media/how-to-manage-individual-sensors/defender-for-iot-version.png" alt-text="Oturum açtıktan sonra görüntülenen yükseltme sürümünün ekran görüntüsü.":::

## <a name="forward-sensor-failure-alerts"></a>İleri algılayıcı hatası uyarıları

Hakkındaki ayrıntıları sağlamak için uyarıları üçüncü taraflara iletebilirsiniz:

- Bağlantısı kesilen algılayıcılar

- Uzak yedekleme sorunları

Bu bilgiler, sistem bildirimleri için bir iletme kuralı oluşturduğunuzda gönderilir.

> [!NOTE]
> Yöneticiler, sistem bildirimleri gönderebilir.

Bildirim göndermek için:

1. Şirket içi yönetim konsolunda oturum açın.
1. Yan menüden **iletme** ' yi seçin.
1. Bir iletme kuralı oluşturun.
1. **Rapor sistemi bildirimleri**' ni seçin.

Kuralları iletme hakkında daha fazla bilgi için bkz. [iletme uyarı bilgileri](how-to-forward-alert-information-to-partners.md).

## <a name="adjust-system-properties"></a>Sistem özelliklerini ayarla

Sistem Özellikleri, sensördeki çeşitli işlemleri ve ayarları denetler. Bunları düzenlemek veya değiştirmek, algılayıcı konsolunun işlemine zarar verebilir.

Ayarlarınızı değiştirmeden önce [Microsoft desteği](https://support.microsoft.com/) başvurun.

Sistem özelliklerine erişmek için:

1. Şirket içi yönetim konsolunda veya sensörde oturum açın.

2. **Sistem ayarları**' nı seçin.

3. **Genel** bölümünden **Sistem Özellikleri** ' ni seçin.

## <a name="see-also"></a>Ayrıca bkz.

[Tehdit bilgileri araştırması ve paketleri](how-to-work-with-threat-intelligence-packages.md)

[Yönetim konsolundan algılayıcıları yönetme](how-to-manage-sensors-from-the-on-premises-management-console.md)
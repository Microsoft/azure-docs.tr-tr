---
title: Uyarı bilgilerini iletme
description: İletme kurallarıyla çalışarak, iş ortağı sistemlerine uyarı bilgileri gönderebilirsiniz.
ms.date: 12/02/2020
ms.topic: how-to
ms.openlocfilehash: bc405f7d4837bf81d9cfcd859d562b7152cfc54b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778806"
---
# <a name="forward-alert-information"></a>Uyarı bilgilerini iletme

IoT için Azure Defender, syslog sunucuları, e-posta adresleri ve daha fazlasını içeren iş ortaklarına uyarı bilgileri gönderebilirsiniz. İletme kurallarıyla çalışma, uyarı bilgilerini hızlı bir şekilde güvenlik hissedarlarına sunmanıza olanak tanır.  

Syslog ve diğer varsayılan iletme eylemleri sisteminizle birlikte sunulur. Microsoft Azure Sentinel, ServiceNow veya splunk gibi iş ortağı satıcılarıyla tümleştirdiğinizde daha fazla iletme eylemi kullanılabilir hale gelebilir.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-information-screen.png" alt-text="Uyarı bilgileri.":::

IoT yöneticileri için Defender 'ın iletme kurallarını kullanma izni vardır.

## <a name="about-forwarded-alert-information"></a>İletilen uyarı bilgileri hakkında

Uyarılar, çok çeşitli güvenlik ve işletimsel olaylar hakkında bilgi sağlar. Örnek:

  - Uyarının tarih ve saati

  - Olayı algılayan altyapı

  - Uyarı başlığı ve açıklayıcı ileti

  - Uyarı önem derecesi

  - Kaynak ve hedef adı ve IP adresi

  - Şüpheli trafik algılandı

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="Adres taraması algılandı.":::

İlgili bilgiler, iletme kuralları oluşturulduğunda iş ortağı sistemlerine gönderilir.

## <a name="create-forwarding-rules"></a>İletme kuralları oluşturma

Yeni bir iletme kuralı oluşturmak için:

1. Yan menüde **iletme** ' yi seçin.

   :: Image Type = "içerik" Source = "medya/nasıl create-forwarding-rule-screen.png yapılır---------------------------#" Iletme kuralı oluştur simgesi

2. **Iletme kuralı oluştur**' u seçin.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/create-a-forwardong-rule.png" alt-text="Yeni bir iletme kuralı oluşturun.":::

3. İletme kuralının adını girin.

### <a name="forwarding-rule-criteria"></a>Kural ölçütlerini iletme 

Bir iletme kuralının tetiklenmesi için ölçüt tanımlayın. İletme kuralı ölçütleriyle çalışma, sensörden dış sistemlere gönderilen bilgi hacminin belirlenmesine ve yönetilmesine yardımcı olur. Aşağıdaki seçenekler kullanılabilir:

**Protokoller**: yalnızca algılanan trafik belirli protokoller üzerinden çalışıyorsa iletme kuralını tetikler. Açılan listeden gerekli protokolleri seçin veya tümünü seçin.

**Altyapılar**: gerekli altyapıları seçin veya tümünü seçin. Seçili altyapılardan gelen uyarılar gönderilecek.

**Önem düzeyleri**: Bu, iletmek için önem düzeyi açısından en düşük olaydır. Örneğin, **küçük** ve küçük Uyarılar ' ı seçerseniz ve bu önem düzeyi üzerinde herhangi bir uyarı iletilecektir. Düzeyler önceden tanımlanmıştır.

### <a name="forwarding-rule-actions"></a>Kural eylemlerini iletme

Kural eylemlerini iletme algılayıcısı, uyarı bilgilerini iş ortağı satıcılarına veya sunucularına iletmesinin talimatını verecektir. Her bir iletme kuralı için birden çok eylem oluşturabilirsiniz.

Sisteminizle birlikte sunulan iletme eylemlerine ek olarak, iş ortağı satıcılarıyla tümleştirdiğinizde diğer eylemler kullanılabilir hale gelebilir. 

#### <a name="email-address-action"></a>E-posta adresi eylemi

Uyarı bilgilerini içeren bir e-posta gönderin. Her kural için bir e-posta adresi girebilirsiniz.

İletme kuralı için e-posta tanımlamak için:

1. Tek bir e-posta adresi girin. Birden fazla postanın gönderilmesi gerekiyorsa, başka bir eylem oluşturun.

2. SıEM 'teki uyarı algılamada zaman damgası için saat dilimini girin.

3. **Gönder**’i seçin.

#### <a name="syslog-server-actions"></a>Syslog Sunucu eylemleri

Aşağıdaki biçimler desteklenir:

- SMS mesajları

- CEF iletileri

- LEEF iletileri

- Nesne iletileri

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-actions-rule.png" alt-text="İletme kuralı eylemleri oluşturun.":::

Aşağıdaki parametreleri girin:

- Syslog konak adı ve bağlantı noktası.

- TCP ve UDP protokolü.

- SıEM 'teki uyarı algılamada zaman damgası için saat dilimi.

- CEF sunucuları için TLS şifreleme sertifika dosyası ve anahtar dosyası (isteğe bağlı).
    
:::image type="content" source="media/how-to-work-with-alerts-sensor/configure-encryption.png" alt-text="İletme kuralınız için şifrelemeyi yapılandırın.":::

| Syslog metin iletisi çıkış alanları | Description |
|--|--|
| Tarih ve saat | Syslog sunucusu makinesinin bilgileri aldığı tarih ve saat. |
| Öncelik | Kullanıcı. Alert |
| Konak adı | Algılayıcı IP adresi |
| Protokol | TCP veya UDP |
| İleti | Algılayıcı: algılayıcı adı.<br /> Uyarı: uyarının başlığı.<br /> Tür: uyarının türü. **Protokol ihlali**, **ilke ihlali**, **kötü amaçlı yazılım**, **anomali** veya **işletimsel** olabilir.<br /> Önem derecesi: uyarının önem derecesi. **Uyarı**, **İkincil**, **büyük** veya **kritik** olabilir.<br /> Kaynak: kaynak cihaz adı.<br /> Kaynak IP: kaynak cihaz IP adresi.<br /> Hedef: hedef cihaz adı.<br /> Hedef IP: hedef cihazın IP adresi.<br /> İleti: uyarının iletisi.<br /> Uyarı grubu: uyarıyla ilişkili olan uyarı grubu. |


| Syslog nesne çıktısı | Description |
|--|--|
| Tarih ve Saat |   Syslog sunucusu makinesinin bilgileri aldığı tarih ve saat. |  
| Öncelik |    Kullanıcı. Alert | 
| Konak adı |    Algılayıcı IP 'si | 
| İleti | Algılayıcı adı: Gereç adı. <br /> Uyarı Zamanı: uyarının algılandığı zaman: Syslog Sunucu makinesi zamanından farklı olabilir ve iletme kuralının saat dilimi yapılandırmasına bağlıdır. <br /> Uyarı başlığı: uyarının başlığı. <br /> Uyarı iletisi: uyarının iletisi. <br /> Uyarı önem derecesi: uyarının önem derecesi: **Uyarı**, **İkincil**, **büyük** veya **kritik**. <br /> Uyarı türü: **protokol ihlali**, **ilke ihlali**, **kötü amaçlı yazılım**, **anomali** veya **işletimsel**. <br /> Protokol: uyarının protokolü.  <br /> **Source_MAC**: IP adresi, adı, satıcı veya kaynak cihazın işletim sistemi. <br /> Destination_MAC: IP adresi, adı, satıcı veya hedefin işletim sistemi. Veriler eksikse **değer yok olur.** <br /> alert_group: uyarıyla ilişkili olan uyarı grubu. |


| Syslog CEF çıkış biçimi | Description |
|--|--|
| Tarih ve saat | Syslog sunucusu makinesinin bilgileri aldığı tarih ve saat. |
| Öncelik | Kullanıcı. Alert | 
| Konak adı | Algılayıcı IP adresi |
| İleti | CEF: 0 <br />IoT için Azure Defender <br />Algılayıcı adı: algılayıcı gerecinin adı. <br />Algılayıcı sürümü <br />Uyarı başlığı: uyarının başlığı. <br />Msg: uyarının iletisi. <br />protokol: uyarının protokolü. <br />önem derecesi: **Uyarı**, **İkincil**, **büyük** veya **kritik**. <br />Tür: **protokol ihlali**, **ilke ihlali**, **kötü amaçlı yazılım**, **anomali** veya **işletimsel**. <br /> başlangıç: uyarının algılandığı zaman. <br />Syslog Sunucu makinesi zamanından farklı olabilir ve iletme kuralının saat dilimi yapılandırmasına bağlıdır. <br />src_ip: kaynak cihazın IP adresi.  <br />dst_ip: hedef cihazın IP adresi.<br />Cat: uyarıyla ilişkili olan uyarı grubu.  |

| Syslog LEEF çıkış biçimi | Description |
|--|--|
| Tarih ve saat |   Syslog sunucusu makinesinin bilgileri aldığı tarih ve saat. |  
| Öncelik |    Kullanıcı. Alert | 
| Konak adı |    Algılayıcı IP 'si |
| İleti | Algılayıcı adı: IoT gereci için Azure Defender adı. <br />LEEF: 1.0 <br />IoT için Azure Defender <br />Algılayıcısı  <br />Algılayıcı sürümü <br />IoT için Azure Defender uyarısı <br />Başlık: uyarının başlığı. <br />Msg: uyarının iletisi. <br />protokol: uyarının protokolü.<br />önem derecesi: **Uyarı**, **İkincil**, **büyük** veya **kritik**. <br />Tür: uyarının türü: **protokol ihlali**, **ilke ihlali**, **kötü amaçlı yazılım**, **anomali** veya **işletimsel**. <br />başlangıç: uyarının saati.Bunun Syslog Sunucu makinesi zamanından farklı olabileceğini unutmayın. (Bu, saat dilimi yapılandırmasına bağlıdır.) <br />src_ip: kaynak cihazın IP adresi.<br />dst_ip: hedef cihazın IP adresi. <br />Cat: uyarıyla ilişkili olan uyarı grubu. |

Tüm bilgileri girdikten sonra **Gönder**' i seçin.

#### <a name="netwitness-action"></a>Nettanık eylemi

Bir Nettanık sunucusuna uyarı bilgileri gönderin.

Nettanık iletme parametrelerini tanımlamak için:

1. Nettanık **ana bilgisayar adı** ve **bağlantı noktası** bilgilerini girin.

2. SıEM 'teki uyarı algılamada zaman damgası için saat dilimini girin.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/add-timezone.png" alt-text="İletme kuralınız için bir saat dilimi ekleyin.":::

3. **Gönder**’i seçin.

#### <a name="integrated-vendor-actions"></a>Tümleşik satıcı eylemleri

Sisteminizi bir güvenlik, cihaz yönetimi veya başka bir sektör satıcısı ile tümleştirmiş olabilirsiniz. Bu tümleştirmeler şunları yapmanızı sağlar:

  - Uyarı bilgilerini gönderin.

  - Cihaz envanter bilgilerini gönder.

  - Satıcı tarafı güvenlik duvarları ile iletişim kurun.

Tümleştirmeler, daha önce güvenlik çözümlerini yığılıyor, cihaz görünürlüğünü geliştirir ve daha hızlı risk hafifletmek için sistem genelinde yanıtı hızlandırmaya yardımcı olur.

Tümleşik satıcılarla iletişim kurmak için gereken kimlik bilgilerini ve diğer bilgileri girmek için Eylemler bölümünü kullanın.

Tümleştirmeler için iletme kurallarını ayarlama hakkında ayrıntılar için ilgili iş ortağı tümleştirme makalelerine bakın.

### <a name="test-forwarding-rules"></a>Test iletme kuralları

İletme kurallarında tanımlanan algılayıcı ve iş ortağı sunucusu arasındaki bağlantıyı test edin:

1. **İletme kuralı** iletişim kutusundan kuralı seçin.

2. **Daha fazla** kutusunu seçin.

3. **Test Iletisi gönder**' i seçin.

4. Algılayıcı tarafından gönderilen bilgilerin alındığını doğrulamak için iş ortağı sisteminize gidin.

### <a name="edit-and-delete-forwarding-rules"></a>İletme kurallarını Düzenle ve Sil 

Bir iletme kuralını düzenlemek için:

- **Iletme kuralı** ekranında, **daha fazla** açılan menü altında **Düzenle** ' yi seçin. İstediğiniz değişiklikleri yapın ve **Gönder**' i seçin.

Bir iletme kuralını kaldırmak için:

- **Iletme kuralı** ekranında, **daha fazla** açılan menü altında **Kaldır** ' ı seçin. **Uyarı** Iletişim kutusunda **Tamam**' ı seçin.

### <a name="forwarding-rules-and-alert-exclusion-rules"></a>Kuralları ve uyarı dışlama kurallarını iletme

Yönetici, uyarı dışlama kuralları tanımlamış olabilir. Bu kurallar, yöneticilerin çeşitli parametrelere göre uyarı olaylarını yok saymayı inceleyerek uyarı tetiklemeden daha ayrıntılı denetim elde etmesini sağlar. Bu parametreler, cihaz adreslerini, uyarı adlarını veya belirli sensörlere dahil edebilir.

Bu, tanımladığınız iletme kurallarının yöneticinizin oluşturduğu dışlama kuralları temel alınarak yoksayılacağı anlamına gelir. Dışlama kuralları, şirket içi yönetim konsolunda tanımlanmıştır.

## <a name="see-also"></a>Ayrıca bkz.

[Uyarı iş akışlarını hızlandırma](how-to-accelerate-alert-incident-response.md)

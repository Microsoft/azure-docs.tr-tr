---
title: ServiceNow tümleştirmesi hakkında
description: ServiceNow için IoT ICS yönetim uygulaması için Defender, endüstriyel ortamlarda dağıtılan özel OT protokollerine ve IoT cihazlarına, şüpheli veya anormal davranışları hızlı bir şekilde algılamak için ICS kullanan davranış analizlerine çok boyutlu görünürlük sağlayan SOC analistleri sağlar.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 6e39c3d018003983f6dc5b5e16a9791de84d6005
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786014"
---
# <a name="the-defender-for-iot-ics-management-application-for-servicenow"></a>ServiceNow için IoT ICS yönetim uygulaması Defender

ServiceNow için IoT ICS yönetim uygulaması için Defender, endüstriyel ortamlarda dağıtılan özel OT protokollerine ve IoT cihazlarına, şüpheli veya anormal davranışları hızlı bir şekilde algılamak için ICS kullanan davranış analizlerine çok boyutlu görünürlük sağlayan SOC analistleri sağlar. Bu, BT 'nin sürekli yakınsaması ve akıllı makineler ve gerçek zamanlı zeka gibi yeni IoT girişimlerini desteklemek için önemli bir evmidir.

Uygulama aynı zamanda hem BT hem de olay yanıtının tek bir şirket SOC içinden yapılmasını da mümkün kılar.

## <a name="about-defender-for-iot"></a>IoT için Defender hakkında

IoT için Defender, önemli ulusal altyapıyı ve sanal makine öğrenimini kullanan tek platformla bir izleme kaydı ile mavi ekip uzmanları tarafından oluşturulan tek ICS ve IoT siber güvenlik platformunu sunar. IoT için Defender şunları sağlar:

- Öznitelikler hakkında kapsamlı bir ayrıntı yelpazğuyla cihaz yataya yönelik BT hakkında anında Öngörüler.

- BT protokollerine, cihazlara, uygulamalara ve bunların davranışlarına sahip, ICS 'yi algılayan kapsamlı gömülü bilgi.

- Güvenlik açıklarına anında Öngörüler ve bilinen sıfır günlük tehditler.

- Özel analizler aracılığıyla hedeflenen ICS saldırılarının en olası yollarını tahmin etmek için otomatikleştirilmiş bir ICS tehdit modelleme teknolojisi.

> [!Note]
> Six 'e başvurular IoT için Azure Defender 'a başvurur.

## <a name="about-the-integration"></a>Tümleştirme hakkında

ServiceNow ile IoT tümleştirmesi için Defender, IoT ve OT için yeni bir merkezi görünürlük, izleme ve denetim düzeyi sağlar. Bu köprülü platformlar, daha önce ICS & IoT cihazlarından daha önce erişilemeyen otomatik cihaz görünürlüğünü ve tehdit yönetimini etkinleştirir.

### <a name="threat-management"></a>Tehdit yönetimi

IoT ICS yönetim uygulaması için Defender şu şekilde yardımcı olur:

- Endüstriyel ve kritik altyapı kuruluşları için gereken süreyi azaltarak siber tehditleri algılayın, araştırın ve üzerinde işlem yapın.

- Güvenlikle ilgili gerçek zamanlı zeka riskleri elde edin.

- ServiceNow tehdit izleme ve olay yönetimi iş akışlarıyla IoT uyarıları için Defender ile bağıntı.

- ServiceNow platformunda ServiceNow biletlerini ve iş akışlarını diğer hizmetler ve uygulamalarla tetikleyin.

ICS ve SCADA güvenlik tehditleri, kötü amaçlı yazılım saldırılarına, ağ ve güvenlik ilkesi sapmalarına anında uyarı yanıtı sağlayan, işlemsel ve protokol bozukluklarıyla birlikte Defender tarafından tanımlanır. ServiceNow 'a gönderilen uyarı ayrıntıları hakkında daha fazla bilgi için bkz. [Uyarı raporlama](#alert-reporting).

### <a name="device-visibility-and-management"></a>Cihaz görünürlüğü ve yönetimi

ServiceNow yapılandırma yönetimi veritabanı (CMDB), IoT Platformu için Defender tarafından gönderilen zengin bir cihaz öznitelikleri kümesiyle zenginleştirmiş ve takıma yöneliktir. Bu, cihaz yataya kapsamlı ve sürekli görünürlük sağlar ve tek bölmenize karşı izleme ve yanıt vermenizi sağlar. ServiceNowSee 'a gönderilen cihaz öznitelikleri hakkındaki ayrıntılar için bkz. [ServiceNow 'Da IoT algılamaları Için görüntüleme Defender](#view-defender-for-iot-detections-in-servicenow).

## <a name="system-requirements-and-architecture"></a>Sistem gereksinimleri ve mimarisi

Bu makalede şunları açıklanmaktadır:

- **Yazılım gereksinimleri**  
- **Mimari**

## <a name="software-requirements"></a>Yazılım gereksinimleri

- ServiceNow hizmet yönetimi sürüm 3.0.2

- IoT Patch 2.8.11.1 veya üzeri için Defender

> [!Note]
> IoT ve ServiceNow tümleştirmesi için bir Defender ile çalışıyorsanız ve şirket içi yönetim konsolunu kullanarak yükseltirseniz, IoT sensörleri için Defender 'dan alınan veri verileri ServiceNow 'dan temizlenmelidir.

## <a name="architecture"></a>Mimari

### <a name="on-premises-management-console-architecture"></a>Şirket içi yönetim konsolu mimarisi

Şirket içi yönetim konsolu, ServiceNow 'a gönderilen tüm cihaz ve uyarı bilgileri için Birleşik bir kaynak sağlar.

Bir ServiceNow örneğiyle iletişim kurmak için bir şirket içi yönetim konsolu ayarlayabilirsiniz. Şirket içi yönetim konsolu, REST API kullanarak, algılayıcı verilerini IoT uygulamasına yönelik Defender 'a iter.

Sisteminizi şirket içi bir yönetim konsoluyla çalışacak şekilde ayarlıyorsanız ServiceNow eşitleme, Iletme kuralları ve proxy yapılandırmalarının ayarlanmalarındaki Algılayıcılar ' ı devre dışı bırakın.

Bu yapılandırmaların şirket içi yönetim konsolu için ayarlanmış olması gerekir. Yapılandırma yönergeleri Bu makalede açıklanmıştır.

### <a name="sensor-architecture"></a>Algılayıcı mimarisi

Ortamınızı sensörler ve ServiceNow arasında doğrudan iletişim içerecek şekilde ayarlamak istiyorsanız, her bir algılayıcı için ServiceNow eşitleme, Iletme kuralları ve ara sunucu yapılandırmasını (bir ara Sunucu gerekliyse) tanımlayın.

ServiceNow ile iletişim kurmak için şirket içi yönetim konsolunu kullanarak tümleştirmenizi ayarlamayı öneririz.

## <a name="create-access-tokens-in-servicenow"></a>ServiceNow 'da erişim belirteçleri oluşturma

Bu makalede ServiceNow 'da erişim belirtecinin nasıl oluşturulacağı açıklanır. IoT için Defender ile iletişim kurmak için belirtecin olması gerekir.

IoT Iletme kuralları için Defender oluştururken, uyarı bilgilerini ServiceNow 'a ileten ve IoT için Defender 'ı bir cihaz özniteliklerini ServiceNow tablolarına göndermek üzere yapılandırırken **ISTEMCI kimliği** ve **istemci parolası** gerekir.

## <a name="set-up-defender-for-iot-to-communicate-with-servicenow"></a>ServiceNow ile iletişim kurmak için IoT için Defender 'ı ayarlama

Bu makalede, IoT için Defender 'ın ServiceNow ile iletişim kurmak üzere nasıl ayarlanacağı açıklanır.

### <a name="send-defender-for-iot-alert-information"></a>IoT uyarı bilgileri için Defender 'ı gönder

Bu makalede, IoT için Defender 'ın, uyarı bilgilerini ServiceNow tablolarına itmesi için nasıl yapılandırılacağı açıklanır. Gönderilen uyarı verileri hakkında daha fazla bilgi için bkz. [Uyarı raporlama](#alert-reporting).

IoT uyarıları için savunma, güvenlik olayları olarak ServiceNow 'da görünür.

ServiceNow 'a uyarı bilgilerini göndermek için IoT *iletme* kuralı Için bir Defender tanımlayın.

Kuralı tanımlamak için:

1. IoT için Defender sol bölmesinde **iletme**' yi seçin.  

1. :::image type="content" source="media/integration-servicenow/plus.png" alt-text="Artı simgesi düğmesini seçin."::: simgesini seçerek gezinti bölmesini daraltın. Iletme kuralı oluştur iletişim kutusu açılır.  

    :::image type="content" source="media/integration-servicenow/forwarding-rule.png" alt-text="Iletme kuralı oluştur":::

1. Bir kural adı ekleyin.

1. IoT için Defender 'ın iletme kuralını tetikleyeceği ölçütü tanımlayın. Iletme kuralı ölçütleriyle çalışma, IoT için Defender 'dan ServiceNow 'a gönderilen bilgi hacmini belirlemenize ve yönetmenize yardımcı olur. Aşağıdaki seçenekler kullanılabilir:

    - **Önem düzeyleri:** Bu, iletmek için en düşük güvenlik düzeyi olaydır. Örneğin, **Ara** seçilirse, ikincil uyarılar ve bu önem düzeyi üzerindeki herhangi bir uyarı iletilir. Düzeyler, daha IoT için Defender tarafından önceden tanımlanmıştır.

    - **Protokoller:** Yalnızca algılanan trafik belirli protokoller üzerinden çalışıyorsa iletme kuralını tetikler. Açılan listeden gerekli protokolleri seçin veya tümünü seçin.

    - **Altyapılar:** Gerekli altyapıları seçin veya tümünü seçin. Seçili altyapılardan gelen uyarılar gönderilecek.

1. **Rapor uyarı bildirimlerinin** seçildiğini doğrulayın.

1. Eylemler bölümünde **Ekle** ' yi ve ardından **ServiceNow**' ı seçin.

    :::image type="content" source="media/integration-servicenow/select-servicenow.png" alt-text="Açılan seçenekten ServiceNow ' ı seçin.":::

1. ServiceNow eylem parametrelerini girin:

    :::image type="content" source="media/integration-servicenow/parameters.png" alt-text="ServiceNow eylem parametrelerini doldur":::

1. **Eylemler** bölmesinde, aşağıdaki parametreleri ayarlayın:

  | Parametre | Açıklama |
  |--|--|
  | Etki alanı | ServiceNow sunucu IP adresini girin. |
  | Kullanıcı adı | ServiceNow sunucu Kullanıcı adını girin. |
  | Parola | ServiceNow sunucu parolasını girin. |
  | İstemci Kimliği | ServiceNow 'daki **uygulama kayıt defterleri** sayfasında IoT için Defender Için ALDıĞıNıZ istemci kimliğini girin. |
  | İstemci Gizli Anahtarı | ServiceNow 'daki **uygulama kayıt defterleri** sayfasında IoT için Defender için oluşturduğunuz istemci gizli dizesini girin. |
  | Rapor Türü | **Olaylar**: ServiceNow 'da sunulan uyarıların listesini bır olay kimliği ve her uyarının kısa açıklaması ile iletin.<br /><br />**IoT uygulaması Için Defender**: algılayıcı ayrıntıları, altyapı, kaynak ve hedef adresleri de dahil olmak üzere tam uyarı bilgilerini ilet. Bilgiler ServiceNow uygulamasında IoT için Defender 'a iletilir. |

1. **Kaydet**' i seçin. IoT uyarıları için savunma bildirimleri ServiceNow 'da olay olarak görünür.

### <a name="send-defender-for-iot-device-attributes"></a>IoT için Defender cihaz özniteliklerini gönder

Bu makalede, IoT için Defender 'ın, ServiceNow tablolarına çok sayıda cihaz özniteliği göndermek için nasıl yapılandırılacağı açıklanır. ServiceNow 'a gönderilen bilgi türü hakkındaki ayrıntılar için ***Envanter bilgilerine*** bakın.

ServiceNow 'a öznitelikler göndermek için şirket içi yönetim konsolunuzu bir ServiceNow örneğine eşlemeniz gerekir. Bu sayede, IoT Platformu için Defender 'ın örnekle iletişim kurabildiğinden ve kimlik doğrulamasından geçmesini sağlar.

ServiceNow örneği eklemek için:

1. IoT şirket içi yönetim konsolu için Defender ' da oturum açın.

1. Şirket içi yönetim konsolu tümleştirme bölümünden **sistem ayarları** ' nı ve ardından **ServiceNow** ' ı seçin.

      :::image type="content" source="media/integration-servicenow/servicenow.png" alt-text="ServiceNow düğmesini seçin.":::

1. ServiceNow eşitleme iletişim kutusuna aşağıdaki eşitleme parametrelerini girin.

    :::image type="content" source="media/integration-servicenow/sync.png" alt-text="ServiceNow eşitleme iletişim kutusu.":::

     Parametre | Açıklama |
    |--|--|
    | Eşitlemeyi etkinleştir | Parametreleri tanımladıktan sonra eşitlemeyi etkinleştirin ve devre dışı bırakın. |
    | Eşitleme sıklığı (dakika) | Varsayılan olarak, bilgi her 60 dakikada bir ServiceNow 'a gönderilir. En az 5 dakikadır. |
    | ServiceNow örneği | ServiceNow örneği URL 'sini girin. |
    | İstemci Kimliği | ServiceNow 'daki **uygulama kayıt defterleri** sayfasında IoT için Defender Için ALDıĞıNıZ istemci kimliğini girin. |
    | İstemci Gizli Anahtarı | ServiceNow 'daki **uygulama kayıt defterleri** sayfasında IoT için Defender Için oluşturduğunuz istemci gizli dizesini girin. |
    | Kullanıcı adı | Bu örnek için Kullanıcı adını girin. |
    | Parola | Bu örnek için parolayı girin. |

1. **Kaydet**' i seçin.

## <a name="verify-communication"></a>İletişimi doğrula

*Son eşitleme* tarihini inceleyerek, şirket içi yönetim konsolunun ServiceNow örneğine bağlı olduğunu doğrulayın.

:::image type="content" source="media/integration-servicenow/sync-confirmation.png" alt-text="Son eşitlemeye bakarak iletişimin oluştuğunu doğrulayın.":::

## <a name="set-up-the-integrations-using-the-https-proxy"></a>HTTPS proxy 'si kullanarak tümleştirmeleri ayarlama

IoT ve ServiceNow tümleştirmesi için Defender 'ı ayarlarken, şirket içi yönetim konsolu ve ServiceNow sunucusu 443 numaralı bağlantı noktasını kullanarak iletişim kurar. ServiceNow sunucusu proxy 'nin arkasındaysa, varsayılan bağlantı noktası kullanılamaz.

IoT için Defender, tümleştirme için kullanılan varsayılan bağlantı noktasının değiştirilmesini etkinleştirerek ServiceNow tümleştirmesinde bir HTTPS ara sunucusunu destekler.

Proxy 'yi yapılandırmak için:

1. Şirket içi yönetim konsolundaki genel özellikleri düzenleyin:  
    `sudo vim /var/cyberx/properties/global.properties`

2. Aşağıdaki parametreleri ekleyin:

    - `servicenow.http_proxy.enabled=1`

    - `servicenow.http_proxy.ip=1.179.148.9`

    - `servicenow.http_proxy.port=59125`

3. Kaydet ve çık.

4. Şu komutu çalıştırın: `sudo monit restart all`

Yapılandırmadan sonra, tüm ServiceNow verileri yapılandırılmış proxy kullanılarak iletilir.

## <a name="download-the-defender-for-iot-application"></a>IoT uygulaması için Defender 'ı indirin

Bu makalede uygulamanın nasıl indirileceği açıklanır.

IoT için Defender uygulamasına erişmek için:

1. <https://store.servicenow.com/> sayfasına gidin

2. Veya için arama yapın `Defender for IoT` `CyberX IoT/ICS Management` .

   :::image type="content" source="media/integration-servicenow/search-results.png" alt-text="Arama çubuğunda Six araması yapın.":::

3. Uygulamayı seçin.

   :::image type="content" source="media/integration-servicenow/cyberx-app.png" alt-text="Listeden uygulamayı seçin.":::

4. **Istek uygulaması** ' nı seçin.

   :::image type="content" source="media/integration-servicenow/sign-in.png" alt-text="Kimlik bilgilerinizle uygulamada oturum açın.":::

5. Oturum açın ve uygulamayı indirin.

## <a name="view-defender-for-iot-detections-in-servicenow"></a>ServiceNow 'da IoT algılamaları için Defender 'ı görüntüleme

Bu makalede ServiceNow 'da sunulan cihaz öznitelikleri ve uyarı bilgileri açıklanmaktadır.

Cihaz özniteliklerini görüntülemek için:

1. ServiceNow 'da oturum açın.

2. **Six platformuna** gidin.

3. **Envanter** veya **Uyarı**' ya gidin.

   [:::image type="content" source="media/integration-servicenow/alert-list.png" alt-text="Envanter veya uyarı":::](media/integration-servicenow/alert-list.png#lightbox)

## <a name="inventory-information"></a>Envanter bilgileri

Yapılandırma yönetimi veritabanı (CMDB), bir IoT için Defender tarafından ServiceNow 'a gönderilen veriler tarafından zenginleştirilebilir ve takıma alınmıştır. ServiceNow 'ın CMDB yapılandırma öğesi tablolarında cihaz özniteliklerini ekleyerek veya güncelleştirerek, IoT için Defender, ServiceNow iş akışlarını ve iş kurallarını tetikleyebilir.

Aşağıdaki bilgiler kullanılabilir:

- Cihaz öznitelikleri, örneğin cihaz MAC, işletim sistemi, satıcı veya protokol algılandı.

- Bellenim bilgileri, örneğin bellenim sürümü ve seri numarası.

- Bağlı cihaz bilgileri, örneğin, kaynak ve hedef arasındaki trafiğin yönü.

### <a name="devices-attributes"></a>Cihaz öznitelikleri

Bu makalede ServiceNow 'a gönderilen cihaz öznitelikleri açıklanmaktadır.

| Öğe | Açıklama |
|--|--|
| Elektrikli | Trafiği algılayan algılayıcının adı. |
| ID | , IoT için Defender tarafından atanan cihaz KIMLIĞI. |
| Name | Cihaz adı. |
| IP Adresi | Cihazın IP adresi veya adresleri. |
| Tür | Cihaz türü, örneğin bir anahtar, PLC, histora veya etki alanı denetleyicisi. |
| MAC Adresi | Cihazın MAC adresi veya adresleri. |
| Operating System | Cihaz işletim sistemi. |
| Satıcı | Cihaz satıcısı. |
| Protokoller | Cihaz tarafından oluşturulan trafikte algılanan protokoller. |
| Sahip | Cihaz sahibinin adını girin. |
| Konum | Cihazın fiziksel konumunu girin. |

Bu görünümdeki bir cihaza bağlı cihazları görüntüleyin.

Bağlı cihazları görüntülemek için:

1. Bir cihaz seçin ve ardından bu cihaz için **bölümünde listelenen gereci** seçin.

    :::image type="content" source="media/integration-servicenow/appliance.png" alt-text="Listeden istenen gereci seçin.":::

1. **Cihaz ayrıntıları** Iletişim kutusunda **bağlı cihazlar**' ı seçin.

### <a name="firmware-details"></a>Üretici yazılımı ayrıntıları

Bu makalede ServiceNow 'a gönderilen cihaz üretici yazılımı bilgileri açıklanmaktadır.

| Öğe | Açıklama |
|--|--|
| Elektrikli | Trafiği algılayan algılayıcının adı. |
| Cihaz | Cihaz adı. |
| Adres | Cihazın IP adresi. |
| Modül adresi | Cihaz modeli ve yuva numarası veya KIMLIĞI. |
| Ardışık | Cihaz seri numarası. |
| Modelleme | Cihaz modeli numarası. |
| Sürüm | Üretici yazılımı sürüm numarası. |
| Ek Veriler | Satıcı tarafından tanımlanan üretici yazılımı hakkında daha fazla veri (örneğin, cihaz türü). |

### <a name="connection-details"></a>Bağlantı ayrıntıları

Bu makalede ServiceNow 'a gönderilen cihaz bağlantı bilgileri açıklanmaktadır.

:::image type="content" source="media/integration-servicenow/connections.png" alt-text="Cihazın bağlantı bilgileri":::

| Öğe | Açıklama |
|--|--|
| Elektrikli | Trafiği algılayan algılayıcının adı. |
| Yön | Trafiğin yönü. <br /> <br /> - **Tek yönlü** , hedefin sunucu olduğunu ve kaynağın istemci olduğunu gösterir. <br /> <br /> - **Iki yönlü** , hem kaynak hem de hedefin sunucu olduğunu veya istemcinin bilinmediğini gösterir. |
| Kaynak cihaz KIMLIĞI | Bağlı cihazla iletişim kurulan cihazın IP adresi. |
| Kaynak cihaz adı | Bağlı cihazla iletişim kurulan cihazın adı. |
| Hedef cihaz KIMLIĞI | Bağlı cihazın IP adresi. |
| Hedef cihaz adı | Bağlı cihazın adı. |

## <a name="alert-reporting"></a>Uyarı bildirimi

IoT altyapılarına yönelik savunma, ağ trafiğinden ve dikkat etmeniz gereken davranıştaki değişiklikleri tespit edildiğinde uyarılar tetiklenir. Her altyapının ürettiği Uyarı türleri hakkında daha fazla bilgi için bkz. [Uyarı motorları hakkında](#about-alert-engines).

Bu makalede ServiceNow 'a gönderilen cihaz uyarı bilgileri açıklanmaktadır.

| Öğe | Açıklama |
|--|--|
| Oluşturulan | Uyarının oluşturulduğu saat ve tarih. |
| Altyapı | Olayı algılayan altyapı. |
| Başlık | Uyarı başlığı. |
| Description | Uyarı açıklaması. |
| Protokol | Trafikte algılanan protokol. |
| Önem derecesi | IoT için Defender tarafından tanımlanan uyarı önem derecesi. |
| Elektrikli | Trafiği algılayan algılayıcının adı. |
| Kaynak adı | Kaynak adı. |
| Kaynak IP adresi| Kaynak IP adresi. |
| Hedef adı | Hedef adı. |
| Hedef IP adresi | Hedef IP adresi. |
| Atanan | Bilet için atanan bireyin adını girin. |

### <a name="updating-alert-information"></a>Uyarı bilgileri güncelleştiriliyor

Uyarı bilgilerini bir formda görüntülemek için oluşturulan sütunda girişi seçin. Uyarı ayrıntılarını güncelleştirebilir ve uyarıyı gözden geçirmek ve işlemek için bir kişiye atayabilirsiniz.

[:::image type="content" source="media/integration-servicenow/alert.png" alt-text="Uyarının bilgilerini görüntüleyin.":::](media/integration-servicenow/alert.png#lightbox)

### <a name="about-alert-engines"></a>Uyarı motorları hakkında

Bu makalede, her altyapının tetiklediği uyarıların türü açıklanmaktadır.

| Uyarı türü | Description |
|--|--|
| İlke ihlali uyarıları | Ilke Ihlali altyapısı, daha önce öğrenilen trafikten sapma algıladığında tetiklenir. Örnek: <br /><br />-Yeni bir cihaz algılandı. <br /><br />-Bir cihazda yeni bir yapılandırma algılandı. <br /><br />-Programlama cihazı olarak tanımlanmayan bir cihaz, programlama değişikliğini yürütür. <br /><br />-Bir bellenim sürümü değişti. |
| Protokol ihlali uyarıları | Protokol Ihlali altyapısı, protokol belirtimine uymayan bir paket yapıları veya alan değerleri algıladığında tetiklenir. |
| İşletimsel uyarılar | Işletimsel motor ağ işlem olaylarını veya arızalı cihazı algıladığında tetiklenir. Örneğin, bir ağ aygıtı stop PLC komutu kullanılarak durdurulmuş veya bir algılayıcı üzerinde arabirim izleme trafiğini durdurdu. |
| Kötü amaçlı yazılım uyarıları | Kötü amaçlı yazılım altyapısı, örneğin Conficker gibi bilinen saldırılar gibi kötü amaçlı ağ etkinliklerini algıladığında tetiklenir. |
| Anomali uyarıları | Anomali altyapısı bir sapma algıladığında tetiklenir. Örneğin, bir cihaz ağ taramayı gerçekleştiriyor ancak tarama cihazı olarak tanımlanmamıştır. |

## <a name="next-steps"></a>Sonraki adımlar

[Uyarı bilgilerini iletmeyi](how-to-forward-alert-information-to-partners.md)öğrenin.

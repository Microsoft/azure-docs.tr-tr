---
title: Forescout tümleştirmesi hakkında
description: Forescout platformunda IoT tümleştirmesi için Azure Defender, IoT ve OT için yeni bir merkezi görünürlük, izleme ve denetim sağlar.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 07e5187970d193502b95b49c5517a8e3824767be
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784076"
---
# <a name="about-the-forescout-integration"></a>Forescout tümleştirmesi hakkında

IoT için Azure Defender, önemli ulusal altyapının bir izleme kaydıyla mavi ekip uzmanları tarafından oluşturulan bir ICS ve IoT siber güvenlik platformu sunar. IoT için Defender, patentli ICS kullanan tehdit analizi ve makine öğrenimi olan tek platformdur. IoT için Defender şunları sağlar:

- Öznitelikler hakkında kapsamlı bir ayrıntı yelpazğuyla cihaz yataya yönelik BT hakkında anında Öngörüler.
- BT protokollerine, cihazlara, uygulamalara ve bunların davranışlarına sahip, ICS 'yi algılayan kapsamlı gömülü bilgi.
- Güvenlik açıklarına anında Öngörüler ve bilinen ve sıfır günlük tehditler.
- Özel analizler aracılığıyla hedeflenen ICS saldırılarının en olası yollarını tahmin etmek için otomatikleştirilmiş bir ICS tehdit modelleme teknolojisi.

Forescout platformunda IoT tümleştirmesi için Defender, IoT ve OT için yeni bir merkezi görünürlük, izleme ve denetim sağlar.

Bu köprülü platformlar, daha önce erişilemeyen ICS cihazlarına ve yığılıyor iş akışlarına yönelik otomatik cihaz görünürlüğünü ve yönetimini etkinleştirir.

Tümleştirme, endüstriyel ortamlarda dağıtılan ve çok düzeyli görünürlüğe sahip SOC analistleri sağlar. IoT teknolojileri için özel Azure Defender 'ı temel alan bellenim, cihaz türleri, işletim sistemleri ve risk analizi puanları gibi öğeler için Ayrıntılar sunulmaktadır.

> [!Note]
> Six 'e başvurular IoT için Azure Defender 'a başvurur.
## <a name="devices"></a>Cihazlar

### <a name="device-visibility-and-management"></a>Cihaz görünürlüğü ve yönetimi

Cihazın envanteri, IoT Platformu için Defender tarafından algılanan kritik özniteliklerle uyumlu değildir. Bu, şunları sağlar:

- Tek bölmesindeyken, OT cihazının yatay olarak kapsamlı ve sürekli görünürlük elde edin.
- Güvenlikle ilgili gerçek zamanlı zeka riskleri elde edin.

:::image type="content" source="media/integration-forescout/forescout-device-inventory.png" alt-text="Cihaz envanteri":::

:::image type="content" source="media/integration-forescout/forescout-device-details.png" alt-text="Cihaz ayrıntıları":::

### <a name="device-control"></a>Cihaz denetimi

Forescout tümleştirmesi, endüstriyel ve kritik altyapı kuruluşların siber tehditleri algılaması, araştırması ve üzerinde işlem yapması için gereken süreyi azaltmaya yardımcı olur.

- Forescout ilke eylemlerini tetikleyerek güvenlik döngüsünü kapatmak için IoT OT cihaz zekası için Azure Defender 'ı kullanın. Örneğin, belirli protokoller algılandığında veya bellenim ayrıntıları değiştiğinde SOC yöneticilerine otomatik olarak uyarı e-postası gönderebilirsiniz.

- İzleme, olay yönetimi ve cihaz denetimini daha fazla olan diğer *Forescout eyeExtended* modüllerle IoT bilgileri için Defender ile bağıntı kurun.

## <a name="system-requirements"></a>Sistem Gereksinimleri

- IoT için Azure Defender sürüm 2,4 veya üstü
- Forescout sürüm 8,0 veya üzeri
- IoT Platformu için Azure Defender için *Forescout eyeExtend* modülüne yönelik bir lisans.

### <a name="getting-more-forescout-information"></a>Daha fazla Forescout bilgisi alma

Forescout platformu hakkında daha fazla bilgi için bkz. [Forescout Resource Center](https://www.forescout.com/company/resources/#resource_filter_group).

## <a name="system-setup"></a>Sistem Kurulumu

Bu makalede, IoT Platformu için Defender ve Forescout platformu arasındaki iletişimin nasıl ayarlanacağı açıklanır.

### <a name="set-up-the-defender-for-iot-platform"></a>IoT Platformu için Defender 'ı ayarlama

IoT için Defender 'ın Forescout ile iletişimini sağlamak için, bir Defender 'da IoT için bir erişim belirteci oluşturun.

Erişim belirteçleri, dış sistemlerin IoT için keşfedilen verilere erişmesine ve bu verilerle, SSL bağlantıları üzerinden dış REST API kullanarak eylemler gerçekleştirmesine olanak sağlar. IoT REST API için Azure Defender 'a erişmek üzere erişim belirteçleri oluşturabilirsiniz.

Belirteç oluşturmak için:

1. Forescout tarafından sorgulanacak IoT algılayıcısı için Defender 'da oturum açın.

1. **Sistem ayarları** ' nı seçin ve ardından **genel** bölümünden **erişim belirteçleri** ' ni seçin. **Erişim belirteçleri** iletişim kutusu açılır.
   :::image type="content" source="media/integration-forescout/generate-access-tokens-screen.png" alt-text="Erişim belirteçleri":::
1. **Erişim belirteçleri** iletişim kutusundan **Yeni belirteç oluştur** ' u seçin.
1. **Yeni erişim belirteci** iletişim kutusunda bir belirteç açıklaması girin.
   :::image type="content" source="media/integration-forescout/new-forescout-token.png" alt-text="Yeni erişim belirteci":::
1. **İleri**’yi seçin. Belirteç iletişim kutusunda görüntülenir. :::image type="content" source="media/integration-forescout/forescout-access-token-display-screen.png" alt-text="Belirteci görüntüle":::
   > [!NOTE]
   > *Belirteci güvenli bir yere kaydedin. Forescout platformunu yapılandırırken buna ihtiyacınız olacak*.
1. **Son**'u seçin.

   :::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="Belirteç ekleme sonu":::

### <a name="set-up-the-forescout-platform"></a>Forescout platformunu ayarlama

IoT algılayıcısı için bir Defender ile iletişim kurmak üzere Forescout platformunu ayarlayabilirsiniz.

Ayarlamak için:

1. Forescout platformunda *Six Için Forescout eyeExtend modülünü* yükler.

1. Counteryasası konsolunda oturum açın ve **Araçlar** menüsünde **Seçenekler** ' i seçin. **Seçenekler** iletişim kutusu açılır.

1. Öğesine gidin ve **modüller** klasörünü seçin.

1. **Modüller** bölmesinde, **cyberx platformu**' nu seçin. IoT için Defender platformu bölmesi açılır.

   :::image type="content" source="media/integration-forescout/settings-for-module.png" alt-text="IoT modülü için Azure Defender ayarları":::

   Aşağıdaki bilgileri girin:

   - **Sunucu adresi** -Forescout gereci tarafından sorgulanacak IoT algılayıcısı IÇIN Defender IP adresini girin.
   - **Erişim belirteci** -Forescout gerecine bağlanacak olan, IoT algılayıcısı için Defender için oluşturulan erişim belirtecini girin. Belirteç oluşturmak için bkz. [IoT Platformu Için Defender 'ı ayarlama](#set-up-the-defender-for-iot-platform).

1. **Uygula**’yı seçin.

Forescout platformunun başka bir sensörle iletişim kurmasını istiyorsanız:

1. IoT algılayıcısı için ilgili Defender 'da yeni bir erişim belirteci oluşturun.

1. **Forescout modules**  >  **Six platformu** iletişim kutusunda:

   - Görünen bilgileri silin.
   
   - Yeni algılayıcı IP adresini ve yeni erişim belirteci bilgilerini girin.

### <a name="verify-communication"></a>İletişimi doğrula

IoT ve Forescout için Defender 'ı yapılandırdıktan sonra, IoT için Defender 'daki algılayıcı erişim belirteçleri iletişim kutusunu açın.

Bu belirteç için **kullanılan** alanda **N/A** görüntüleniyorsa, algılayıcı ve forescout gereci arasındaki bağlantı çalışmıyor demektir.

**Kullanılan** bu belirtece sahip bir dış çağrının son kez alındığını gösterir.

:::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="Belirtecin alındığını doğrular":::

## <a name="view-defender-for-iot-detections-in-forescout"></a>Forescout 'da IoT algılamaları için Defender 'ı görüntüleme

Bir cihazın özniteliklerini görüntülemek için:

1. Forescout platformunda oturum açın ve ardından **varlık envanterine** gidin.

1. **Six platformuna** gidin. Aşağıdaki cihaz öznitelikleri,, IoT için Defender tarafından algılanan OT cihazları için görüntülenir.

   | Öğe | Açıklama |
   |--|--|
   | IoT için Azure Defender tarafından yetkilendirildi | Ağ Öğrenme döneminde IoT için Defender tarafından ağınızda algılanan bir cihaz. |
   | Üretici yazılımı | Cihazın üretici yazılımı ayrıntıları. Örneğin, model ve sürüm ayrıntıları. |
   | Name | Cihazın adı. |
   | Operating System | Cihazın işletim sistemi. |
   | Tür | Cihaz türü. Örneğin, bir PLC, Histora veya mühendislik Istasyonu. |
   | Satıcı | Cihazın satıcısı. Örneğin, Rockwell Otomasyonu. |
   | Risk düzeyi | IoT için Defender tarafından hesaplanan risk düzeyi. |
   | Protokoller | Cihaz tarafından oluşturulan trafikte algılanan protokoller. |

:::image type="content" source="media/integration-forescout/device-firmware-attributes-in-forescout.png" alt-text="Üretici yazılımı özniteliklerini görüntüleyin.":::

:::image type="content" source="media/integration-forescout/vendor-attributes-in-forescout.png" alt-text="Satıcıların özniteliklerini görüntüleyin.":::

### <a name="viewing-more-details"></a>Daha fazla ayrıntı görüntüleme

IoT için Defender tarafından yönlendirilen cihazların ek cihaz bilgilerini görüntüleyin. Örneğin, Forescout uyumluluğu ve ilke bilgileri.

Bunu gerçekleştirmek için **cihaz envanteri Konakları** bölümünden bir cihaza sağ tıklayın. Ana bilgisayar ayrıntıları iletişim kutusu ek bilgilerle açılır.

:::image type="content" source="media/integration-forescout/details-dialog-box-in-forescout.png" alt-text="Ana bilgisayar ayrıntıları":::

## <a name="create-azure-defender-for-iot-policies-in-forescout"></a>Forescout 'da IoT ilkeleri için Azure Defender oluşturma

Forescout ilkeleri,, IoT için Defender tarafından algılanan cihazların denetim ve yönetimini otomatik hale getirmek için kullanılabilir. Örneğin,

- Belirli bellenim sürümleri algılandığında, SOC yöneticilerini otomatik olarak e-posta ile gönderin.

- Olay ve güvenlik iş akışlarında daha fazla işleme için, diğer SıEM tümleştirmelerine sahip olan IoT algılanan cihazlar için bir Forescout grubuna özel Defender ekleyin.

Koşul özelliklerini kullanarak IoT için Defender ile bir Forescout özel ilkesi oluşturun.

IoT özelliklerine yönelik Defender 'a erişmek için:

1. **Ilke koşulları** Iletişim kutusundan **Özellikler ağacına** gidin.

1. **Özellikler ağacında** **Six platform** klasörünü genişletin. IoT için Defender aşağıdaki özellikleri kullanabilir.

:::image type="content" source="media/integration-forescout/forescout-property-tree.png" alt-text="Özellikler":::

## <a name="next-steps"></a>Sonraki adımlar

[Uyarı bilgilerini iletmeyi](how-to-forward-alert-information-to-partners.md)öğrenin.

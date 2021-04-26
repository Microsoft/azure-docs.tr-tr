---
title: Cisco ıSE pxGrid tümleştirmesi hakkında
description: Cisco ıSE pxGrid ile IoT için Defender 'ın yeteneklerini köprüleme, güvenlik ekiplerinin kurumsal ekosistem için bir veya daha fazla cihaz görünürlüğü sağlar.
ms.date: 12/28/2020
ms.topic: how-to
ms.openlocfilehash: 00151f2e407c65d024f3bd59bdaa85a08ae677f4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784093"
---
# <a name="about-the-cisco-ise-pxgrid-integration"></a>Cisco ıSE pxGrid tümleştirmesi hakkında

IoT için Defender, önemli ulusal altyapıyı ve sanal makine öğrenimini kullanan tek platformla bir izleme kaydı ile mavi ekip uzmanları tarafından oluşturulan tek ICS ve IoT siber güvenlik platformunu sunar. IoT için Defender şunları sağlar:

- ICS cihazları, güvenlik açıkları ve bilinen ve sıfır günlük tehditler hakkında anında Öngörüler.

- BT protokollerine, cihazlara, uygulamalara ve bunların davranışlarına sahip, ICS 'yi algılayan kapsamlı gömülü bilgi.

- Özel analizler aracılığıyla hedeflenen ICS saldırılarının en olası yollarını tahmin etmek için otomatikleştirilmiş bir ICS tehdit modelleme teknolojisi.

## <a name="powerful-device-visibility-and-control"></a>Güçlü cihaz görünürlüğü ve denetimi

Cisco ıSE pxGrid ile IoT tümleştirmesi için Defender, OT için yeni bir merkezi görünürlük, izleme ve denetim düzeyi sağlar.

Bu köprülü platformlar, daha önce erişilemeyen ICS ve IIoT cihazlarına otomatik cihaz görünürlüğünü ve korumayı etkinleştirir.

### <a name="ics-and-iiot-device-visibility-comprehensive-and-deep"></a>ICS ve IIoT cihaz görünürlüğü: kapsamlı ve derin

IoT teknolojileri için patentli Defender, Kurumsal veriler için kapsamlı ve derin ICS ve IIoT cihaz bulma ve envanter yönetimi sağlar.

Cihaz türleri, üreticiler, açık bağlantı noktaları, seri numaraları, bellenim düzeltmeleri, IP adresleri ve MAC adresi verileri ve daha fazlası gerçek zamanlı olarak güncelleştirilir. IoT için Defender, kritik kurumsal veri kaynaklarıyla tümleştirerek bu taban çizgisinden görünürlüğü, bulmayı ve çözümlemeyi daha da artırabilir. Örneğin, CMDBs, DNS, güvenlik duvarları ve Web API 'Leri.

Ayrıca, IoT Platformu için Defender, endüstriyel ve kritik altyapı kuruluşlarındaki cihazların en doğru ve ayrıntılı envanterini sağlamak üzere pasif izlemeyi ve isteğe bağlı seçmeli yoklama tekniklerini birleştirir.

### <a name="bridged-capabilities"></a>Köprülü yetenekler

Cisco ıSE pxGrid ile bu özellikleri Köprüle bağlamak, güvenlik ekiplerinin kurumsal ekosisteme hiçbir şekilde cihaz görünürlüğü sağlar.

Cisco ıSE pxGrid ile sorunsuz, güçlü tümleştirme, hiçbir cihazın bulunamamasını ve hiçbir cihaz bilgisinin karşılanmamasını sağlar.

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoint-categories.png" alt-text="Uç nokta kategorilerinin OUı örneği.":::

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoints.png" alt-text="Sistemdeki örnek uç noktalar":::  

:::image type="content" source="media/integration-cisco-isepxgrid-integration/attributes.png" alt-text="Sistemde bulunan özniteliklerin ekran görüntüsü.":::  

### <a name="use-case-coverage-ise-policies-based-on-defender-for-iot-attributes"></a>Kullanım örneği kapsamı: IoT öznitelikleri için Defender 'a dayalı ıSE ilkeleri

ICS ve IoT kullanım örneği gereksinimlerini işlemek üzere IoT derin öğrenimi için Defender 'ı temel alan güçlü ıSE ilkelerini kullanın.

İlkelerle çalışma, güvenlik döngüsünü kapatmanıza ve ağınızı gerçek zamanlı olarak uyumlu hale getirmenize olanak tanır.

Örneğin, müşteriler aşağıdaki kullanım durumlarını ele alan ilkeler oluşturmak üzere IoT ICS ve IoT öznitelikleri için Defender 'ı kullanabilir:

- Bilinen ve yetkilendirilmiş cihazların izin verilen özniteliklere göre hassas bir bölgeye izin vermek için bir yetkilendirme ilkesi oluşturun; örneğin, Rockwell Automation PLCs için belirli bellenim sürümü.

- OT olmayan bir bölgede bir ICS cihazı algılandığında güvenlik ekiplerine bildirimde bulunur.

- Eski veya uyumsuz satıcılarla makineleri düzeltin.

- Cihazları gerektiği şekilde karantinaya alın ve engelleyin.

- Bilinen güvenlik açıklarına (Cviler) sahip bellenim çalıştıran PLCs veya Rtıs üzerinde raporlar oluşturun.

### <a name="about-this-article"></a>Bu makale hakkında

Bu makalede, IoT için Defender 'ın Cisco ıSE 'ye yönelik öznitelikler içerdiğinden emin olmak için pxGrid ve Defender for IoT platformunun nasıl ayarlanacağı açıklanır.

### <a name="getting-more-information"></a>Daha fazla bilgi edinme

Cisco ıSE pxGrid tümleştirme gereksinimleri hakkında daha fazla bilgi için bkz. <https://www.cisco.com/c/en/us/products/security/pxgrid.html>

## <a name="integration-system-requirements"></a>Tümleştirme sistem gereksinimleri

Bu makalede tümleştirme sistem gereksinimleri açıklanmaktadır:

IoT gereksinimleri için Defender

- IoT sürüm 2,5 için Defender

Cisco gereksinimleri

- pxGrid sürüm 2,0

- Cisco ıSE sürüm 2,4

Ağ gereksinimleri

- IoT gereç için Defender 'ın Cisco ıSE yönetim arabirimine erişimi olduğunu doğrulayın.

- Kuruluşunuzdaki IoT gereçlerine yönelik tüm Defender 'a CLı erişiminizin olduğunu doğrulayın.

> [!NOTE]
> Cisco ıSE pxGrid ile yalnızca MAC adresi olan cihazlar eşitlenir.

## <a name="cisco-ise-pxgrid-setup"></a>Cisco ıSE pxGrid kurulumu

Bu makalede nasıl yapılacağı açıklanır:

- PxGrid ile iletişim kurma

- Uç nokta cihazına abone olma konusu

- İstemci sertifikaları oluşturma

- PxGrid ayarlarını tanımlayın

## <a name="set-up-communication-with-pxgrid"></a>PxGrid ile iletişim kurma

Bu makalede pxGrid ile iletişimin nasıl ayarlanacağı açıklanır.

İletişim kurmak için:

1. Cisco ıSE 'de oturum açın.

1. **Yönetim** > **sistemi** ve **dağıtımı**' nı seçin.

1. Gerekli düğümü seçin. Genel Ayarlar sekmesinde, **Pxgrid onay kutusunu** seçin.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/pxgrid.png" alt-text="Pxgrid onay kutusunun seçili olduğundan emin olun.":::

1. **Profil oluşturma yapılandırması** sekmesini seçin.

1. **Pxgrid onay kutusunu** seçin. Bir açıklama ekleyin.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/profile-configuration.png" alt-text="Açıklama Ekle ekran görüntüsü":::

## <a name="subscribe-to-the-endpoint-device-topic"></a>Uç nokta cihazına abone olma konusu

ISE pxGrid düğümünün Endpoint cihaz konusuna abone olduğunu doğrulayın. **Yönetim** > **pxgrid Services** > **Web istemcileri**' ne gidin. Burada, ıSE 'nin uç nokta cihaz konusuna abone olduğunu doğrulayabilirsiniz.

## <a name="generate-certificates"></a>İstemci sertifikaları oluşturma

Bu makalede sertifikaların nasıl oluşturulacağı açıklanır.

Şunu oluşturmak için:

1. **Yönetim**  >  **pxgrid Services**' i seçin ve ardından **Sertifikalar**' ı seçin.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/certificates.png" alt-text="Sertifika oluşturmak için Sertifikalar sekmesini seçin.":::

1. **İstiyorum alanında,** **tek bir sertifika oluştur (sertifika imzalama isteği olmadan)** öğesini seçin.

1. Kalan alanları doldurup **Oluştur**' u seçin.

1. İstemci sertifikası anahtarını ve sunucu sertifikasını oluşturun ve ardından bunları Java anahtar deposu biçimine dönüştürün. Bunları ağınızdaki IoT algılayıcısı için her bir Defender 'a kopyalamanız gerekir.

## <a name="define-pxgrid-settings"></a>PxGrid ayarlarını tanımlayın

Ayarları tanımlamak için:

1. **Yönetim**  >  **pxgrid Services** ' i seçin ve ardından **Ayarlar**' ı seçin.

1. Sertifika tabanlı **yeni hesapları otomatik olarak onaylamayı** ve **parola tabanlı hesap oluşturmaya izin vermeyi etkinleştirin.**

  :::image type="content" source="media/integration-cisco-isepxgrid-integration/allow-these.png" alt-text="Her iki onay kutusu da seçildiğinden emin olun.":::

## <a name="set-up-the-defender-for-iot-appliances"></a>IoT gereçlerine yönelik Defender 'ı ayarlama

Bu makalede, IoT gereçlerinin pxGrid ile iletişim kurması için Defender 'ın nasıl ayarlanacağı açıklanır. Yapılandırma, Cisco ıSE 'ye veri eklenecek olan IoT gereçlerine yönelik tüm Defender üzerinde yürütülmesi gerekir.

Gereçlerini ayarlamak için:

1. Algılayıcı CLı ' de oturum açın.

1. `trust.jks`Ve daha önce sensörde oluşturulan kopya. Bunları içine kopyalayın: `/var/cyberx/properties/` .

1. Düzenle `/var/cyberx/properties/pxgrid.properties` :

    1. Anahtar ve güven ekleyin, sonra dosya adlarını ve parolaları depolayın.

    2. PxGrid örneğinin ana bilgisayar adını ekleyin.

    3. `Enabled=true`

1. Gereci yeniden başlatın.

1. Kuruluşunuzda veri eklenecek her gereç için bu adımları tekrarlayın.

## <a name="view-and-manage-detections-in-cisco-ise"></a>Cisco ıSE 'de algılamaları görüntüleme ve yönetme

1. Uç nokta algılamaları Ise bağlamı **görünürlük**  >  **uç noktaları** sekmesinde görüntülenir.

1. **İlke**  >  **profili oluşturma**  >    >  **kural** Ekle ve  >  **koşul**  >  **Yeni koşul oluştur**' u seçin.

1. **Öznitelik** ' i seçin ve eklenen özniteliklere göre bir profil oluşturma kuralı oluşturmak için IoT cihaz sözlüklerini kullanın. Aşağıdaki öznitelikler eklenmiş.

    - Cihaz Türü

    - Donanım düzeltmesi

    - IP Adresi

    - MAC adresi

    - Name

    - Ürün Kimliği

    - Protokol

    - Seri numarası

    - Yazılım düzeltme

    - Satıcı

Yalnızca MAC adresi olan cihazlar eşitlenir.

## <a name="troubleshooting-and-logs"></a>Sorun giderme ve Günlükler

Günlükler şu şekilde bulunabilir:

- `/var/cyberx/logs/pxgrid.log`

- `/var/cyberx/logs/core.log`

## <a name="next-steps"></a>Sonraki adımlar

[Uyarı bilgilerini iletmeyi](how-to-forward-alert-information-to-partners.md)öğrenin.

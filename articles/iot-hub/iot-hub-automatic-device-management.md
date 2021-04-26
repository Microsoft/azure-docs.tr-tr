---
title: Azure IoT Hub ölçek sırasında otomatik cihaz yönetimi | Microsoft Docs
description: Birden çok IoT aygıtını ve modülünü yönetmek için Azure IoT Hub otomatik yapılandırma kullanın
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 0e017f4df413d6db528bb99756646859d9a74aea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92545404"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>Azure portalını kullanarak otomatik IoT cihazı ve modül yönetimi

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Azure IoT Hub otomatik cihaz yönetimi, büyük cihaz filklerin yönetilmesi için yinelenen ve karmaşık görevlerin birçoğunu otomatikleştirir. Otomatik cihaz yönetimi sayesinde, özelliklerini temel alarak bir cihaz kümesini hedefleyebilir, istenen yapılandırmayı tanımlayabilir ve ardından IoT Hub cihazları kapsama geldiğinde güncelleştirebilir. Bu güncelleştirme, bir _otomatik cihaz yapılandırması_ veya _otomatik modül yapılandırması_ kullanılarak yapılır; bu işlem tamamlama ve uyumluluğu özetler, birleştirme ve çakışmaları idare etmenize ve yapılandırmaları aşamalı bir yaklaşımda kullanıma almanızı sağlar.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Otomatik cihaz yönetimi, istenen özelliklerle bir dizi cihaz WINS veya modül TWINS 'i güncelleştirerek ve ikizi bildirilen özellikleri temel alan bir Özet raporlayarak işe yarar.  Üç bölümden oluşan *yapılandırma* adlı yeni bir sınıf ve JSON belgesi tanıtır:

* **Hedef koşul** , görüntülenecek cihaz TWINS veya modül TWINS kapsamını tanımlar. Hedef koşul, ikizi etiketlerinde ve/veya bildirilen özelliklerde bir sorgu olarak belirtilir.

* **Hedef içerik** , hedeflenen cihaz TWINS veya modül TWINS 'de eklenecek veya güncelleştirilebilen istenen özellikleri tanımlar. İçerik, istenen özelliklerin değiştirilmesi için bir yol içerir.

* **Ölçümler** , **başarı**, **devam** ediyor ve **hata** gibi çeşitli yapılandırma durumlarının Özet sayılarını tanımlar. Özel ölçümler, ikizi tarafından bildirilen özelliklerde sorgu olarak belirtilir.  Sistem ölçümleri, hedeflenen TWINS sayısı ve başarıyla güncelleştirilmiş TWINS sayısı gibi ikizi güncelleştirme durumunu ölçen varsayılan ölçülerdir.

Yapılandırma oluşturulduktan ve sonra beş dakikalık aralıklarla otomatik yapılandırmalar ilk kez çalışır. Ölçüm sorguları otomatik yapılandırmanın her çalıştırılışında çalışır.

## <a name="implement-twins"></a>TWINS 'i Uygula

Otomatik cihaz yapılandırmalarının, bulut ve cihazlar arasında durum eşitlemesini sağlamak için cihaz ikikesi kullanılması gerekir.  Daha fazla bilgi için bkz. [IoT Hub cihaz TWINS 'ı anlama ve kullanma](iot-hub-devguide-device-twins.md).

Otomatik modül yapılandırmalarının, bulut ve modüller arasında durumu eşitlemesini sağlamak için modül TWINS 'in kullanılmasını gerektirir. Daha fazla bilgi için bkz. [IoT Hub modül TWINS 'ı anlama ve kullanma](iot-hub-devguide-module-twins.md).

## <a name="use-tags-to-target-twins"></a>TWINS 'i hedeflemek için etiketleri kullanma

Bir yapılandırma oluşturmadan önce, hangi cihazları veya modülleri etkilenmesini istediğinizi belirtmeniz gerekir. Azure IoT Hub cihazları tanımlar ve cihaz ikizi Etiketler kullanarak modülleri tanımlar ve ikizi Module içindeki etiketleri belirler. Her bir cihaz veya modülde birden çok etiket olabilir ve bunları çözümünüz için anlamlı hale getiren herhangi bir şekilde tanımlayabilirsiniz. Örneğin, farklı konumlardaki cihazları yönetiyorsanız, bir cihaz ikizi aşağıdaki etiketleri ekleyin:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="create-a-configuration"></a>Yapılandırma oluşturma

1. [Azure Portal](https://portal.azure.com), IoT Hub 'ınıza gidin. 

2. **IoT cihaz yapılandırması**' nı seçin.

3. **Cihaz Yapılandırması Ekle** veya **Modül Yapılandırması Ekle**' yi seçin.

   ![Cihaz yapılandırması veya modül Yapılandırması Ekle](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

Bir yapılandırma oluşturmak için beş adım vardır. Aşağıdaki bölümler her bir adım adım yol gösterir. 

### <a name="name-and-label"></a>Ad ve etiket

1. Yapılandırmanıza en fazla 128 harf olan benzersiz bir ad verin. Boşluklardan ve aşağıdaki geçersiz karakterlerden kaçının: `& ^ [ ] { } \ | " < > /` .

2. Yapılandırmalarınızı izlemeye yardımcı olmak için Etiketler ekleyin. Etiketler, yapılandırmanızı tanımlayan **ad** ve **değer** çiftleridir. Örneğin `HostPlatform, Linux` veya `Version, 3.0.1` olabilir.

3. Sonraki adıma geçmek için **İleri ' yi** seçin. 

### <a name="specify-settings"></a>Ayarları belirtin

Bu bölüm, hedeflenen cihazda veya modül TWINS 'de ayarlanacak içeriği tanımlar. Her ayar kümesi için iki giriş vardır. Birincisi, ayarlanacak ikizi özellikleri içindeki JSON bölümünün yolu olan ikizi yoludur.  İkincisi, bu bölüme eklenecek JSON içeridir. 

Örneğin, ikizi yolunu ayarlayabilir `properties.desired.chiller-water` ve ardından AŞAĞıDAKI JSON içeriğini sağlayabilirsiniz: 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![İkizi yolunu ve içeriğini ayarlama](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


Ayrıca, tüm ikizi yolunu belirterek ve değer parantez olmadan değeri sağlayarak tek tek ayarları ayarlayabilirsiniz. Örneğin, ikizi yolu ile `properties.desired.chiller-water.temperature` içeriği olarak ayarlayın `66` . Daha sonra basınç özelliği için yeni bir ikizi ayarı oluşturun. 

İki veya daha fazla yapılandırma aynı ikizi yolunu hedefliyorsanız, en yüksek öncelikli yapılandırmadan içerik uygulanır (adım 4 ' te öncelik tanımlanmıştır).

Varolan bir özelliği kaldırmak istiyorsanız, özelliği değerini belirtin `null` .

**Cihaz Ekle Ikizi ayarlama** veya **Modül Ekle ikizi ayarını** seçerek ek ayarlar ekleyebilirsiniz.

### <a name="specify-metrics-optional"></a>Ölçümleri belirtin (isteğe bağlı)

Ölçümler, bir cihaz ya da modülün yapılandırma içeriği uygulandıktan sonra yeniden rapor edebileceği çeşitli durumların Özet sayısını sağlar. Örneğin, bekleyen ayarlar değişiklikleri, hatalara yönelik bir ölçüm ve başarılı ayarlar değişiklikleri için bir ölçüm oluşturabilirsiniz.

Her yapılandırmanın en fazla beş özel ölçümü olabilir. 

1. **Ölçüm adı** için bir ad girin.

2. **Ölçüm ölçütü** için bir sorgu girin.  Sorgu, Device ikizi tarafından bildirilen özellikleri temel alır.  Ölçüm, sorgu tarafından döndürülen satır sayısını temsil eder.

Örnek:

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

Yapılandırmanın uygulandığı bir yan tümce ekleyebilirsiniz, örneğin: 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

Yapılandırılmış modüller üzerinde raporlamak için bir ölçüm oluşturuyorsanız, ' ı seçin `moduleId` `devices.modules` . Örnek:

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>Hedef Cihazlar

Bu yapılandırmayı alması gereken belirli cihazları veya modülleri hedeflemek için ikizlerini 'nizden Tags özelliğini kullanın. Ayrıca, ikizi tarafından bildirilen özellikleri de hedefleyebilirsiniz.

Otomatik cihaz yapılandırması yalnızca Device ikizi etiketlerini hedefleyebilir ve otomatik modül yapılandırmalarının yalnızca Module ikizi etiketlerini hedefleyebilir. 

Birden çok yapılandırma aynı cihazı veya modülü hedefleyebilir, çünkü her yapılandırmanın bir öncelik numarası vardır. Herhangi bir çakışma varsa, en yüksek önceliğe sahip yapılandırma kazanır. 

1. Yapılandırma **önceliği** için pozitif bir tamsayı girin. En yüksek sayısal değer en yüksek öncelik olarak değerlendirilir. İki yapılandırmanın aynı öncelik numarası varsa, en son WINS 'nin oluşturulduğu bir değer vardır. 

2. Hangi cihazların veya modüllerin bu yapılandırmaya hedefleneceğini belirleyen bir **hedef koşul** girin. Koşul, ikizi Tags veya ikizi tarafından bildirilen özellikleri temel alır ve ifade biçimiyle eşleşmelidir. 

   Otomatik cihaz yapılandırması için, hedef olarak yalnızca etiketi veya bildirilen özelliği belirtebilirsiniz. Örneğin `tags.environment='test'` veya `properties.reported.chillerProperties.model='4000x'` olabilir. `*`Tüm cihazları hedeflemek için öğesini belirtebilirsiniz. 
   
   Otomatik modül yapılandırması için, IoT Hub 'ına kayıtlı modüllerdeki etiketleri veya bildirilen özellikleri belirtmek için bir sorgu kullanın. Örneğin `from devices.modules where tags.environment='test'` veya `from devices.modules where properties.reported.chillerProperties.model='4000x'` olabilir. Joker karakter tüm modülleri hedeflemek için kullanılamaz. 

3. Son adıma geçmek için **İleri ' yi** seçin.

### <a name="review-configuration"></a>Yapılandırmayı gözden geçir

Yapılandırma bilgilerinizi gözden geçirin ve ardından **Gönder**' i seçin.

## <a name="monitor-a-configuration"></a>Bir yapılandırmayı izleme

Bir yapılandırmanın ayrıntılarını görüntülemek ve çalıştıran cihazları izlemek için aşağıdaki adımları kullanın:

1. [Azure Portal](https://portal.azure.com), IoT Hub 'ınıza gidin. 

2. **IoT cihaz yapılandırması**' nı seçin.

3. Yapılandırma listesini inceleyin. Her yapılandırma için aşağıdaki ayrıntıları görebilirsiniz:

   * **ID** -yapılandırmanın adı.

   * **Hedef koşul** -hedeflenen cihazları veya modülleri tanımlamak için kullanılan sorgu.

   * **Öncelik** -yapılandırmaya atanan öncelik numarası.

   * **Oluşturma zamanı** -yapılandırmanın oluşturulduğu zaman damgası. Bu zaman damgası, iki yapılandırmanın aynı önceliğe sahip olduğu durumlarda, özellikleri bölmek için kullanılır. 

   * **Sistem ölçümleri** -IoT Hub tarafından hesaplanan ve geliştiriciler tarafından özelleştirilemez ölçümler. Hedeflenen, hedef koşulla eşleşen cihaz TWINS sayısını belirtir. Uygulama, yapılandırma tarafından değiştirilen cihaz TWINS sayısını, ayrı, daha yüksek öncelikli bir yapılandırmanın de değişiklik yaptığı olayda kısmi değişiklikler içerebilen bir şekilde değiştirdi. 

   * **Özel ölçümler** -geliştirici tarafından ikizi bildirilen özelliklerle ilgili sorgular olarak belirtilmiş ölçümler.  En fazla beş özel ölçüm, yapılandırma başına tanımlanabilir. 
   
4. İzlemek istediğiniz yapılandırmayı seçin.  

5. Yapılandırma ayrıntılarını inceleyin. Yapılandırmayı alan cihazlarla ilgili belirli ayrıntıları görüntülemek için sekmeleri kullanabilirsiniz.

   * **Hedef koşul** -hedef koşulla eşleşen cihazlar veya modüller. 

   * **Ölçümler** -sistem ölçümlerinin ve özel ölçümlerin bir listesi.  Açılan listede ölçümü seçerek ve ardından **cihazları görüntüle** veya **modülleri görüntüle**' yi seçerek her bir ölçüm için sayılan cihazların veya modüllerin listesini görüntüleyebilirsiniz.

   * **Device Ikizi Settings** veya **module ikizi Settings** -yapılandırma tarafından ayarlanan ikizi ayarları. 

   * **Yapılandırma etiketleri** -bir yapılandırmayı anlatmak için kullanılan anahtar-değer çiftleri.  Etiketlerin işlevleri üzerinde etkisi yoktur. 

## <a name="modify-a-configuration"></a>Yapılandırma değiştirme

Bir yapılandırmayı değiştirdiğinizde, değişiklikler hedeflenen tüm cihazlara veya modüllerle hemen çoğaltılır. 

Hedef koşulu güncelleştirirseniz, aşağıdaki güncelleştirmeler gerçekleşir:

* Bir ikizi eski hedef koşulunu karşılamıyorsa, ancak yeni hedef koşulunu karşılıyorsa ve bu yapılandırma bu ikizi için en yüksek önceliktir, bu yapılandırma uygulanır. 

* Şu anda bu yapılandırmayı çalıştıran bir ikizi artık hedef koşulu karşılamıyorsa, yapılandırmanın ayarları kaldırılır ve ikizi bir sonraki en yüksek öncelik yapılandırması tarafından değiştirilir. 

* Şu anda bu yapılandırmayı çalıştıran bir ikizi, hedef koşulunu artık karşılamıyor ve diğer yapılandırmaların hedef koşulunu karşılamıyorsa, yapılandırmadan gelen ayarlar kaldırılır ve ikizi üzerinde başka bir değişiklik yapılmaz. 

Bir yapılandırmayı değiştirmek için aşağıdaki adımları kullanın: 

1. [Azure Portal](https://portal.azure.com), IoT Hub 'ınıza gidin. 

2. **IoT cihaz yapılandırması**' nı seçin. 

3. Değiştirmek istediğiniz yapılandırmayı seçin. 

4. Aşağıdaki alanlarda güncelleştirmeler yapın: 

   * Hedef koşul 
   * Etiketler 
   * Öncelik 
   * Ölçümler

4. **Kaydet**’i seçin.

5. Değişiklikleri izlemek için [Yapılandırmayı İzleme](#monitor-a-configuration) bölümündeki adımları izleyin. 

## <a name="delete-a-configuration"></a>Yapılandırma silme

Bir yapılandırmayı sildiğinizde, her türlü cihaz WINS 'in bir sonraki en yüksek öncelikli yapılandırmasını alır. Cihaz WINS, başka bir yapılandırmanın hedef koşulunu karşılamıyorsa, başka hiçbir ayar uygulanmaz. 

1. [Azure Portal](https://portal.azure.com), IoT Hub 'ınıza gidin. 

2. **IoT cihaz yapılandırması**' nı seçin. 

3. Silmek istediğiniz yapılandırmayı seçmek için onay kutusunu kullanın. 

4. **Sil**’i seçin.

5. Bir istem, doğrulamanızı ister.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede IoT cihazlarını ölçeklendirerek nasıl yapılandıracağınızı ve izleyeceğinizi öğrendiniz. Azure IoT Hub 'yi yönetme hakkında daha fazla bilgi edinmek için bu bağlantıları izleyin:

* [IoT Hub cihaz kimliklerinizi toplu olarak yönetme](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub 'ınızı izleme](monitor-iot-hub.md)

IoT Hub yeteneklerini daha fazla incelemek için bkz.:

* [IoT Hub Geliştirici Kılavuzu](iot-hub-devguide.md)
* [Azure IOT Edge ile sınır cihazlarına Al dağıtma](../iot-edge/quickstart-linux.md)

Tam zamanında sağlama işlemini etkinleştirmek üzere IoT Hub cihaz sağlama hizmetini kullanarak araştırmak için, bkz.: 

* [Azure IoT Hub Cihazı Sağlama Hizmeti](../iot-dps/index.yml)
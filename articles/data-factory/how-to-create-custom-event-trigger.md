---
title: Azure Data Factory özel olay Tetikleyicileri oluşturma
description: Event Grid yayımlanan özel bir olaya yanıt olarak bir işlem hattı çalıştıran Azure Data Factory özel bir tetikleyici oluşturmayı öğrenin.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 2d2f26b24e7fa10d9244de8f99d78c64a44b3d61
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785657"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-custom-event-preview"></a>Özel bir olaya yanıt olarak bir işlem hattı çalıştıran bir tetikleyici oluşturma (Önizleme)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, Data Factory işlem hatlarında oluşturabileceğiniz özel olay tetikleyicileri açıklanmaktadır.

Olay odaklı mimari (EDA), olaylar için üretim, algılama, tüketim ve yeniden eyleme sahip ortak bir veri tümleştirme modelidir. Veri tümleştirme senaryoları genellikle Data Factory müşterilerin işlem hatlarını oluşan belirli olaylara göre tetiklemesine gerek duyar. [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) ile yerel tümleştirme Data Factory artık [özel olayları](../event-grid/custom-topics.md)kapsamaktadır: müşteriler bir Event Grid konusuna rastgele olaylar gönderir ve abone Data Factory ve konuya dinler ve işlem hatlarını uygun şekilde tetikler.

> [!NOTE]
> Bu makalede açıklanan tümleştirme [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)bağımlıdır. Aboneliğinizin Event Grid kaynak sağlayıcısına kayıtlı olduğundan emin olun. Daha fazla bilgi için bkz. [kaynak sağlayıcıları ve türleri](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). *Microsoft. EventGrid/Eventabonelikleri/** eylemini yapabilmelisiniz. Bu eylem EventGrid EventSubscription katılımcısı yerleşik rolünün bir parçasıdır.

Ayrıca, işlem hattı parametrelerini ve özel olay tetikleyicisini birleştiren müşteriler, işlem hattı çalıştırmalarıyla özel _veri_ yükünü ayrıştırabilirler ve başvurabilir. Özel olay yükünde _veri_ alanı, ücretsiz biçimli bir JSON anahtar-değer yapısıdır ve müşterilere olay odaklı işlem hattı çalıştırmaları üzerinde en fazla denetim verir.

> [!IMPORTANT]
> Genellikle, Parametreleştirme 'de başvurulan bir anahtar özel olay yükünde eksik olabilir. _Tetikleyici çalıştırması_ hata vererek başarısız olur, çünkü özelliğin _KeyName_ özelliği mevcut olmadığından ifadenin değerlendirilemeyeceğini belirten Olay tarafından __hiçbir__ işlem _hattı çalıştırması_ tetiklenmeyecektir.

## <a name="setup-event-grid-custom-topic"></a>Event Grid özel konu ayarla

Data Factory özel olay tetikleyicisini kullanmak için, _önce_ [Event Grid bir özel konu](../event-grid/custom-topics.md)ayarlamanız gerekir. İş akışı, Data Factory konuyu sizin için ayarlayacağı depolama olay tetikleyicisinden farklıdır. Burada Azure Event Grid gitmeniz ve konuyu kendiniz oluşturmanız gerekir. Özel konuyu oluşturma hakkında daha fazla bilgi için bkz. Azure Event Grid [Portal öğreticileri](../event-grid/custom-topics.md#azure-portal-tutorials) ve [CLI öğreticileri](../event-grid/custom-topics.md#azure-cli-tutorials)

Veri fabrikaları olayların [olay şeması Event Grid](../event-grid/event-schema.md)izlemesini bekler. Olay yüklerin aşağıdaki alanlara sahip olduğundan emin olun.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

## <a name="data-factory-ui"></a>Data Factory Kullanıcı Arabirimi (UI)

Bu bölümde, Azure Data Factory Kullanıcı arabirimi içinde bir depolama olay tetikleyicisi oluşturma gösterilmektedir.

1. Kalem simgesiyle gösterilen **Düzenle** sekmesine geçin.

1. Menüde **tetikleyici** ' yi seçin ve ardından **Yeni/Düzenle**' yi seçin.

1. **Tetikleyici Ekle** sayfasında **tetikleyici seç...** öğesini seçin ve **+ Yeni**' yi seçin.

1. Tetikleyici türü **özel olayları** seçin

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-1-creation.png" alt-text="Data Factory Kullanıcı arabiriminde yeni bir özel olay tetikleyicisi oluşturmak için yazar sayfasının ekran görüntüsü." lightbox="media/how-to-create-custom-event-trigger/custom-event-1-creation-expanded.png":::

1. Azure aboneliği açılan menüsünden Özel konu başlığını seçin veya olay konu kapsamını el ile girin.

   > [!NOTE]
   > Yeni bir özel olay tetikleyicisi oluşturmak veya değiştirmek için, Data Factory oturum açmak ve depolama Olay tetikleyicisinin yayımlanması için kullanılan Azure hesabı, konu başlığı altında uygun rol tabanlı erişim denetimi (Azure RBAC) iznine sahip olmalıdır. Ek izin gerekmez: Azure Data Factory için hizmet sorumlusu, Event Grid için özel _izin gerektirmez._ Erişim denetimi hakkında daha fazla bilgi için bkz. [rol tabanlı erişim denetimi](#role-based-access-control) bölümü.

1. **Konu, ile başlar** ve **ilgili özellikleri özellikleriyle işlem** hattını tetiklemek istediğiniz olayları filtrelemenizi sağlar. Her iki özellik de isteğe bağlıdır.

1. Filtrelemek istediğiniz **olay türlerini** eklemek Için **+ New** ' i kullanın. Özel olay tetikleyicisi çalışan bir veya liste için ilişki: özel bir olayda burada listelenenlerin bulunduğu bir _EventType_ özelliği varsa, işlem hattı çalıştırmasını tetikler. Olay türü, büyük/küçük harfe duyarlıdır. Örneğin, aşağıdaki ekran görüntüsünde, _tetikleyiciyle ilgili_ olarak tüm _copycompleted_ veya _copysucceeded_ olayları eşleşir.

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-2-properties.png" alt-text="Data Factory Kullanıcı arabirimindeki olay türlerini ve konu filtrelemeyi açıklamak için tetikleyici düzenleme sayfasının ekran görüntüsü.":::

1. Özel olay tetikleyicisi, özel _veri_ yükünü ayrıştırmanıza ve işlem hattınızla gönderebilir. Önce işlem hattı parametrelerini oluşturun ve **Parametreler** sayfasındaki değerleri girin. Format **@triggerBody (). Event. Data kullanın.__** veri yükünü ayrıştırmak ve değerleri işlem hattı parametrelerine geçirmek Için anahtar keyName. Ayrıntılı açıklama için bkz. [özel olay tetikleyicisinde](control-flow-system-variables.md#custom-event-trigger-scope) işlem hatlarında ve sistem değişkenlerinde [başvuru tetikleyici meta verileri](how-to-use-trigger-parameterization.md)

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-4-trigger-values.png" alt-text="İşlem hattı parametreleri ayarının ekran görüntüsü.":::

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-3-parameters.png" alt-text="Özel olayda veri yüküne başvuruda bulunmak için parametre sayfasının ekran görüntüsü.":::

1. İşiniz bittiğinde **Tamam** ' a tıklayın.

## <a name="json-schema"></a>JSON şeması

Aşağıdaki tabloda, özel olay tetikleyicilerle ilgili şema öğelerine genel bir bakış verilmiştir:

| **JSON öğesi** | **Açıklama** | **Tür** | **İzin Verilen Değerler** | **Gerekli** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **kapsam** | Olay Kılavuzu konusunun Azure Resource Manager kaynak KIMLIĞI. | Dize | Azure Resource Manager KIMLIĞI | Yes |
| **olayları** | Bu tetikleyicinin tetiklenmesine neden olan olayların türü. | Dizeler dizisi    |  | Evet, en az bir değer beklenmektedir |
| **subjectBeginsWith** | Konu alanının tetiklenmesi için belirtilen Düzenle başlaması gerekir. Örneğin, `factories` yalnızca ile başlayan olay konusu için tetikleyiciyi tetikleyin `factories` . | Dize   | | No |
| **subjectEndsWith** | Konu alanının tetiklenmesi için belirtilen örüntüle bitmesi gerekir. | Dize   | | No |

## <a name="role-based-access-control"></a>Rol tabanlı erişim denetimi

Azure Data Factory, Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) kullanarak dinlemek, güncelleştirmelere abone olmak ve özel olaylara bağlı işlem hatlarını tetiklemek için kesinlikle yasaktır.

* Yeni bir hata oluşturmak veya mevcut bir özel olay tetikleyicisini güncelleştirmek için Data Factory oturum açan Azure hesabının ilgili depolama hesabına uygun erişimi olması gerekir. Aksi takdirde, işlem _erişim reddedildi_ hatasıyla başarısız olur.
* Data Factory Event Grid için özel bir izin gerekmez ve işlem için Data Factory hizmet sorumlusu için özel Azure RBAC izni _atamanız gerekmez._

Özellikle, müşterinin _/Subscriptions/# # # #/resourceGroups//#_ # # #/Providers/Microsoft.EventGrid/topics/someTopics üzerinde _Microsoft. Eventgrid/Eventaboneliklerin/Write_ izni olması gerekir

## <a name="next-steps"></a>Sonraki adımlar

* Tetikleyiciler hakkında ayrıntılı bilgi için bkz. işlem [hattı yürütme ve Tetikleyiciler](concepts-pipeline-execution-triggers.md#trigger-execution).
* İşlem hattında tetikleyici meta verilerine nasıl başvurulacağını öğrenin, bkz. işlem [hattı çalıştırmalarıyla başvuru tetikleyici meta verileri](how-to-use-trigger-parameterization.md)

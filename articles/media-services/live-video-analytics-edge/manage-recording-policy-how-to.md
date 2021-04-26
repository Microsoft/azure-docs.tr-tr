---
title: Kayıt ilkesini yönetme-Azure
description: Bu konuda, kayıt ilkesinin nasıl yönetileceği açıklanmaktadır.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: ec72f28496c1392b9d95134c343e1892998a0c28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99224998"
---
# <a name="manage-recording-policy"></a>Kayıt ilkesini yönetme

[Sürekli video kaydı](continuous-video-recording-concept.md)için IoT Edge, canlı video analizi 'ni kullanarak haftalık veya aylık bir videoyu buluta kaydedebilirsiniz. Azure depolama 'da yerleşik olan [yaşam döngüsü yönetimi araçlarını](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal) kullanarak bu Bulut arşivinin uzunluğunu (gün cinsinden) yönetebilirsiniz.  

Medya hizmeti hesabınız bir Azure depolama hesabına bağlı ve buluta video kaydettiğinizde içerik bir medya hizmeti varlığına [yazılır.](../latest/assets-concept.md) Her varlık, depolama hesabındaki bir kapsayıcıya eşlenir. Yaşam döngüsü yönetimi, aşağıdaki gibi bir [kural](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#rules) belirtebileceğiniz bir depolama hesabı için bir [ilke](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#policy) tanımlamanızı sağlar.

```
{
  "rules": [
    {
      "name": "NinetyDayRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "delete": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

Yukarıdaki kural:

* Depolama hesabındaki tüm blok Blobları için geçerlidir.
* Blobların 30 günden fazla olduğu durumlarda [sık erişimli erişim katmanından seyrek erişimli 'e](../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)taşındığını belirtir.
* Blob 90 günden daha fazla yaşlandıklarında bunlar silinir.

Bir varlığa kayıt yapmak için canlı video analizi kullandığınızda, `segmentLength` modüle, buluta yazılmadan önce en az bir video (saniye cinsinden) süresini toplamasını söyleyen bir özellik belirtirsiniz. Varlığınız, her biri öncekinden daha yeni olan bir oluşturma zaman damgasına sahip bir dizi segment içerir `segmentLength` . Yaşam döngüsü yönetimi ilkesi, belirtilen eşikten daha eski segmentleri siler. Ancak, medya hizmeti API 'Leri aracılığıyla kalan kesimleri çalıştırmaya ve kayıttan yürütmeye devam edersiniz. Daha fazla bilgi için bkz. [kayıtları kayıttan yürütme](playback-recordings-how-to.md). 

## <a name="limitations"></a>Sınırlamalar

Yaşam döngüsü yönetimiyle ilgili bazı bilinen sınırlamalar aşağıda verilmiştir:

* İlkede en fazla 100 kural bulunabilir ve her kural 10 ' a kadar kapsayıcı belirtebilir. Bu nedenle, farklı kayıt ilkelerine (örneğin, Park lotu için 3 günlük arşiv, yük gözündeki kamera için 30 gün ve kullanıma alma sayacının arkasındaki kamera için 180 gün) sahip olmanız gerekiyorsa, en az 1000 kamera için kuralları özelleştirebilirsiniz.
* Yaşam döngüsü yönetim ilkesi güncelleştirmeleri anında değil. Daha fazla bilgi için [Bu SSS bölümüne](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#faq) bakın.
* Blobların seyrek erişimli katmana taşındığı bir ilke uygulamayı seçerseniz, arşivin o bölümünün yürütülmesi etkilenebilir. Daha fazla gecikme süresi veya tek tek tek tek bir hata olabilir. Media Services arşiv katmanındaki içeriğin kayıttan yürütülmesini desteklemez.

## <a name="next-steps"></a>Sonraki adımlar

[Kayıtların kayıttan yürütülmesi](playback-recordings-how-to.md)

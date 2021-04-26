---
title: Azure Cosmos DB çakışma çözümü türleri ve çözüm ilkeleri
description: Bu makalede, Azure Cosmos DB çakışma kategorileri ve çakışma çözümleme ilkeleri açıklanmaktadır.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ba55d88de3a5a4087db30613b22a7d2441de9be1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334387"
---
# <a name="conflict-types-and-resolution-policies-when-using-multiple-write-regions"></a>Birden çok yazma bölgesi kullanılırken çakışma türleri ve çözümleme ilkeleri
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB hesabınız birden çok yazma bölgesi ile yapılandırılmışsa çakışmalar ve çakışma çözümleme ilkeleri geçerlidir.

Birden fazla yazma bölgesi ile yapılandırılmış Azure Cosmos hesapları için, yazarlar aynı öğeyi birden çok bölgede aynı anda güncelleştirdiğinizde güncelleştirme çakışmaları ortaya çıkabilir. Güncelleştirme çakışmaları aşağıdaki üç türden olabilir:

* **Çakışma Ekle**: bir uygulama aynı anda iki veya daha fazla bölgede aynı benzersiz dizine sahip iki veya daha fazla öğe eklediğinde bu çakışmalar meydana gelebilir. Örneğin, bu çakışma bir KIMLIK özelliği ile meydana gelebilir.

* **Çakışmaları Değiştir**: bir uygulama aynı öğeyi iki veya daha fazla bölgede aynı anda güncelleştirirken bu çakışmalar meydana gelebilir.

* **Çakışmaları Sil**: Bu çakışmalar, bir uygulama aynı anda bir bölgedeki öğeyi silerse ve onu başka bir bölgede güncelleştirdiğinde meydana gelebilir.

## <a name="conflict-resolution-policies"></a>Çakışma çözümleme ilkeleri

Azure Cosmos DB, yazma çakışmalarını çözmek için esnek ilke odaklı bir mekanizma sunar. Azure Cosmos kapsayıcısında iki çakışma çözümü ilkesi arasından seçim yapabilirsiniz:

* **Son yazma WINS (LWW)**: Bu çözümleme ilkesi, varsayılan olarak sistem tanımlı bir zaman damgası özelliği kullanır. Zaman eşitleme saati protokolüne dayalıdır. SQL API 'sini kullanırsanız, çakışma çözümü için kullanılacak başka bir özel sayısal Özellik (örneğin, zaman damgası kavramı) belirtebilirsiniz. Özel bir sayısal özelliği de *çakışma çözümleme yolu* olarak adlandırılır. 

  Ekleme veya değiştirme işlemlerinde iki veya daha fazla öğe çakışırsa, çakışma çözümleme yolu için en yüksek değere sahip öğe kazanan olur. Birden çok öğe, çakışma çözümleme yolu için aynı sayısal değere sahip ise, sistem kazanan kişiyi belirler. Tüm bölgeler tek bir yarışa yakınsayacak ve yürütülen öğenin aynı sürümüyle sona acaktır. Silme çakışmaları dahil edildiğinde, silinen sürüm her zaman ekleme veya değiştirme çakışmaları üzerinde kazanır. Çakışma çözümleme yolunun değeri ne olursa olsun bu sonuç oluşur.

  > [!NOTE]
  > Son yazma WINS varsayılan çakışma çözümleme ilkesidir ve `_ts` Şu API 'ler için zaman damgasını kullanır: SQL, MongoDB, Cassandra, Gremlin ve Table. Özel sayısal özellik yalnızca SQL API 'SI için kullanılabilir.

  Daha fazla bilgi için bkz. [LWW çakışma çözümü ilkelerini kullanan örnekler](how-to-manage-conflicts.md).

* **Özel**: Bu çözümleme ilkesi, çakışmaların mutabakatı için uygulama tanımlı semantik için tasarlanmıştır. Azure Cosmos kapsayıcıda Bu ilkeyi ayarladığınızda, *birleştirme saklı yordamını* da kaydetmeniz gerekir. Bu yordam, sunucudaki bir veritabanı işlemi altında çakışmalar algılandığında otomatik olarak çağrılır. Sistem, taahhüt protokolünün bir parçası olarak birleştirme yordamının yürütülmesi için tam olarak bir kez daha sağlar.  

  Özel çözüm seçeneğiyle kapsayıcınızı yapılandırırsanız ve bir birleştirme yordamını kapsayıcıya kaydetmeniz başarısız olursa veya birleştirme yordamı çalışma zamanında bir özel durum oluşturursa, çakışmalar *Çakışma akışına* yazılır. Daha sonra uygulamanız çakışma akışındaki çakışmaları el ile çözmaları gerekir. Daha fazla bilgi edinmek için bkz. [özel çözüm ilkesini kullanma ve çakışmalar akışını kullanma örnekleri](how-to-manage-conflicts.md).

  > [!NOTE]
  > Özel çakışma çözümleme ilkesi yalnızca SQL API hesapları için kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

Çakışma çözümleme ilkelerini nasıl yapılandıracağınızı öğrenin:

* [Uygulamalarınız için birden çok yazma bölgesi yapılandırma](how-to-multi-master.md)
* [Çakışma çözümleme ilkelerini yönetme](how-to-manage-conflicts.md)
* [Çakışma akışından okuma](how-to-manage-conflicts.md#read-from-conflict-feed)

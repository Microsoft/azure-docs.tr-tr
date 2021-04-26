---
title: Azure Cosmos DB kaynaklar için kota artışı isteme
description: Azure Cosmos DB kaynakları için kota artışı isteme hakkında bilgi edinin. Ayrıca, bir bölgeye erişim için bir aboneliğin nasıl etkinleştireceğinizi öğreneceksiniz.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: e7ec71220b75647e789508c760e50957b3b497fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93090044"
---
# <a name="how-to-request-quota-increase-for-azure-cosmos-db-resources"></a>Azure Cosmos DB kaynaklar için kota artışı isteme
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB kaynaklar [Varsayılan kota/sınırlara](concepts-limits.md)sahiptir. Ancak, iş yükünüzün varsayılan değerden daha fazla kotanın olması gereken bir durum olabilir. Böyle bir durumda, kota artışı istemek için Azure Cosmos DB ekibine ulaşmanız gerekir. Bu makalede, Azure Cosmos DB kaynaklar için nasıl kota artışı isteneceğini açıklanmaktadır. Ayrıca, bir bölgeye erişim için bir aboneliğin nasıl etkinleştireceğinizi öğreneceksiniz.

## <a name="create-a-new-support-request"></a>Yeni bir destek isteği oluşturun

Kota artışı istemek için iş yükü ayrıntılarınız ile yeni bir destek isteği oluşturmanız gerekir. Azure Cosmos DB takım isteğinizi değerlendirin ve onaylayın. Azure portal yeni bir destek isteği oluşturmak için aşağıdaki adımları kullanın:

1. Azure Portal’da oturum açın.

1. Sol taraftaki menüden **Yardım + Destek** ' i seçin ve ardından **Yeni destek isteği**' ni seçin.

1. **Temel bilgiler** sekmesinde aşağıdaki ayrıntıları girin:

   * **Sorun türü** alanında **Hizmet ve abonelik sınırları (kotalar)** girişini seçin
   * **Abonelik** için, kotayı artırmak istediğiniz aboneliği seçin.
   * **Kota türü** için **Cosmos DB** seçin

   :::image type="content" source="./media/create-support-request-quota-increase/create-quota-increase-request.png" alt-text="Kota artışı için yeni bir Cosmos DB destek isteği oluşturun":::

1. **Ayrıntılar** sekmesinde, kota isteğinize karşılık gelen ayrıntıları girin. Bu sekmede sağlanan bilgiler, sorununuzu daha fazla değerlendirmek ve destek mühendisinin sorunu gidermesine yardımcı olmak için kullanılacaktır.

1. Aşağıdaki ayrıntıları bu biçimde doldur:

   * **Açıklama**: isteğiniz için iş yükünüz gibi, varsayılan değerlerin neden yeterli olmadığı hakkında kısa bir açıklama sağlayın. Kota artışı istediğiniz kaynak türüne bağlı olarak, **Açıklama** alanı içinde aşağıdaki ayrıntıları sağlamanız zorunludur:

     **Bölge istekleri** İsteğiniz izin verilenler listesine bir bölge eklemeye karşılık geliyorsa, aşağıdaki değerleri sağladığınızdan emin olun:

        * Bölge adı
        * Abonelik Kimliği

     **Aktarım hızı sınırı istekleri** İsteğiniz, verimlilik için kotayı arttırmaya karşılık geliyorsa, aşağıdaki değerleri sağladığınızdan emin olun:

        * Veritabanı adı
        * Abonelik Kimliği
        * Yeni aktarım hızı sınırı

     **Veritabanı hesabı sınır istekleri** İsteğiniz abonelik içindeki veritabanı hesabı sayısı için kotayı arttırmaya karşılık geliyorsa, aşağıdaki değerleri sağladığınızdan emin olun:

       * Abonelik Kimliği
       * Yeni veritabanı hesabı sınırı

   * **Karşıya dosya yükleme**: tanılama dosyalarını veya destek isteğiyle ilgili olduğunu düşündüğünüz diğer dosyaları karşıya yükleyin. Karşıya dosya yükleme kılavuzu hakkında daha fazla bilgi edinmek için bkz. [Azure destek]( ../azure-portal/supportability/how-to-manage-azure-support-request.md#upload-files) makalesi.

   * **Önem derecesi**: iş etkisini temel alarak kullanılabilir önem düzeylerinden birini seçin.

   * **Tercih edilen iletişim yöntemi**: **e-posta** veya **telefonla** iletişim kurulmasını seçebilirsiniz.

1. Formda kullanılabilirlik, destek dili, iletişim bilgileri, e-posta ve telefon numarası gibi kalan ayrıntıları doldurun.

1. **İleri ' yi seçin: gözden geçir + oluştur**. Destek isteği oluşturmak için, belirtilen bilgileri doğrulayın ve **Oluştur** ' u seçin.

Azure Cosmos DB destek ekibi, 24 saat içinde isteğinizi değerlendirir ve size geri gönderilir.

## <a name="next-steps"></a>Sonraki adımlar

* Bkz. [Azure Cosmos DB varsayılan hizmet kotaları](concepts-limits.md)

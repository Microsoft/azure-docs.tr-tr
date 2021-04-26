---
title: Azure veri paylaşımıyla bağlantı
description: Bu makalede, varlıkları aramak ve verileri izlemek için Azure purview ile bir Azure veri paylaşma hesabının nasıl bağlanacağı açıklanır.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: edea881d67d5a677339c6ff357c1ac45f5dfd420
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96555556"
---
# <a name="how-to-connect-azure-data-share-and-azure-purview"></a>Azure Data Share ve Azure purview 'a bağlanma

Bu makalede, Azure [Data Share](../data-share/overview.md) hesabınızı Azure purview ile bağlama ve verilerinize ait paylaşılan veri kümelerini (hem giden hem de gelen) yönetme konusu açıklanmaktadır. Çeşitli veri yönetimi, kuruluşlar, departmanlar ve hatta veri merkezleri gibi sınırlar genelinde verileri kökenini bulabilir ve izleyebilir.

## <a name="common-scenarios"></a>Genel Senaryolar

Veri paylaşımının kökenini, kök neden analizi ve etki analizi hakkında ayrıntılı bilgi sağlamayı amaçlamaktadır.

### <a name="scenario-1-360-view-of-datasets-shared-inout-for-a-partner-organization-or-internal-department"></a>Senaryo 1:360 iş ortağı organizasyonu veya dahili departman için paylaşılan/dışarı veri kümelerinin görünümü

Veri ofisler, kendi iş ortağı kuruluşlarıyla çift yönlü olarak paylaşılan tüm veri kümelerinin bir listesini görüntüleyebilir. Veri kümelerini kuruluş adına göre arayabilir ve bulabilir ve tüm giden ve gelen paylaşımların tümüyle bir görünümünü görebilir.

### <a name="scenario-2-root-cause-analysis---upstream-dependency-on-datasets-coming-into-organization-consumer-view-of-incoming-shares"></a>Senaryo 2: kök neden analizi-kuruluşa gelen veri kümelerinde yukarı akış bağımlılığı (gelen paylaşımların tüketici görünümü)

Bir dış veri paylaşımının hesabından gelen yukarı akış veri sorunları nedeniyle raporda yanlış bilgi vardır. Veri mühendisleri, yukarı akış başarısızlıklarını anlayabilir, nedenler hakkında bilgi alabilir ve verilerin değişmesine neden olan sorunları çözmek için paylaşımın sahibine daha fazla iletişim kurabilirler.

### <a name="scenario-3-impact-analysis-on-datasets-going-outside-organization-provider-view-of-outgoing-shares"></a>Senaryo 3: kuruluşun dışında kalan veri kümelerinde etki çözümlemesi (giden paylaşımların sağlayıcı görünümü)

Data üreticileri, veri kümelerinde bir değişiklik yaptıktan sonra kimin etkileneceğimizi bilmesini ister. Bir veri üreticisi, kökenini kullanarak, Azure veri paylaşımından veri kullanan aşağı akış iç veya dış iş ortaklarının etkisini kolayca anlayabilirler.

## <a name="azure-data-share-and-purview-connected-experience"></a>Azure veri paylaşma ve bağlantılı deneyim

Azure veri paylaşımınızı ve Azure purview hesabınızı bağlamak için aşağıdakileri yapın:

1. Bir purview hesabı oluşturun. Tüm veri paylaşımının kökenini bilgileri bir purview hesabı tarafından toplanacak. Var olan bir tane kullanabilir veya yeni bir purview hesabı oluşturabilirsiniz.

1. Azure veri paylaşımınızı purview hesabınıza bağlayın.

    1. Purview portalında **Yönetim Merkezi** ' ne gidebilir ve **dış bağlantılar** bölümünde Azure veri paylaşımınızı bağlayabilirsiniz.
    1. Üstteki çubukta **+ Yeni** ' yi seçin, Azure veri paylaşımınızı açılır çubukta bulun ve veri paylaşma hesabını ekleyin. Veri paylaşma varlıklarının ve kökenini bilgilerinin purview 'da görünebilmesi için veri paylaşımınızı purview hesabına bağladıktan sonra bir anlık görüntü işi çalıştırın.

       :::image type="content" source="media/how-to-link-azure-data-share/connect-to-data-share.png" alt-text="Azure veri paylaşımıyla bağlantı için Yönetim Merkezi":::

1. Azure veri paylaşımında anlık görüntülerinizi yürütün.

    - Azure Data Share bağlantısı Azure purview ile kurulduktan sonra, mevcut paylaşımlarınız için bir anlık görüntü çalıştırabilirsiniz. 
    - Mevcut paylaşımlarınız yoksa, [verilerinizi paylaşmak](../data-share/share-your-data.md) [ve bir veri paylaşımına abone](../data-share/subscribe-to-data-share.md)olmak için Azure veri paylaşımı portalına gidin.
    - Paylaşılan anlık görüntü tamamlandıktan sonra, ilişkili veri paylaşma varlıklarını ve kökenini ' yi purview içinde görüntüleyebilirsiniz.

1. Veri paylaşma hesaplarını bulun ve bilgileri takip görünümü hesabınızda paylaşabilirsiniz.

    - Purview hesabının giriş sayfasında, **varlık türüne göre araştır** ' ı seçin ve **Azure veri paylaşma** kutucuğunu seçin. Hesap adı, paylaşma adı, paylaşılan anlık görüntü veya iş ortağı organizasyonu için arama yapabilirsiniz. Aksi takdirde hesap adı, paylaşım türü (gönderilen ve alınan paylaşımlar) için arama sonucu sayfasında filtreler uygulayın.

       :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-search-result-page.png" alt-text="Arama sonucu sayfasında Azure veri paylaşma":::

    >[!Important]
    >Veri paylaşımının Takiview 'da görünmesi için veri paylaşımınızı purview 'a bağladığınızda bir anlık görüntü işinin çalıştırılması gerekir.

1. Azure veri paylaşımıyla paylaşılan veri kümelerinin kökenini izleyin.

    - Takip görünümü arama sonucu sayfasında, veri paylaşımının anlık görüntüsünü (alınan/gönderilen) seçin ve yukarı akış ve aşağı akış bağımlılıklarıyla kökenini grafiğini görmek için **kökenini** sekmesini seçin.

    :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-lineage.png" alt-text="Azure Data Share kullanılarak paylaşılan veri kümeleri kökenini":::

## <a name="next-steps"></a>Sonraki adımlar

- [Catalog kökenini Kullanıcı Kılavuzu](catalog-lineage-user-guide.md)
- [Kökenini için Azure Data Factory bağlantısı](how-to-link-azure-data-factory.md)

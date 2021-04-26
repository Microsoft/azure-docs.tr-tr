---
title: Azure Kaynak taşıyıcısı nedir?
description: Azure Kaynak taşıyıcısı hakkında bilgi edinin
author: rayne-wiselman
manager: evansma
ms.service: azure-migrate
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 06d6352f018238318c3bb4625ae86a2974f14569
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99820172"
---
# <a name="what-is-azure-resource-mover"></a>Azure Kaynak taşıyıcısı nedir?

Bu makalede, Azure Kaynak taşıyıcısı hizmetine genel bir bakış sunulmaktadır. Kaynak taşıyıcısı Azure kaynaklarını Azure bölgeleri arasında taşımanızı sağlar.

Kaynakları farklı Azure bölgelerine şu kaynaklara taşıyabilirsiniz:

- **Bir bölge başlatma kısmına hizalayın**: kaynakları önceden kullanılamayan yeni bir sunulan Azure bölgesine taşıyın.
- **Hizmetler/Özellikler Için hizalayın**: belirli bir bölgede bulunan hizmetlerden veya özelliklerden yararlanmak için kaynakları taşıyın.
- **İş gelişmeleri yanıtlayın**: birleşmeler veya alımlar gibi iş değişikliklerine yanıt olarak kaynakları bir bölgeye taşıyın.
- **Yakınlık Için Hizala**: kaynakları işletmenize yerel bir bölgeye taşıyın.
- **Veri gereksinimlerini karşılayın**: kaynakları veri yerleşimi gereksinimleriyle veya veri sınıflandırması gereksinimleriyle hizalamak için taşıyın.
- **Dağıtım gereksinimlerine yanıt**: hata halinde dağıtılan kaynakları taşıyın veya kapasite ihtiyaçlarına yanıt olarak taşıyın.
- **Yetki alma Işlemine yanıt verme**: bir bölge kullanımdan alındığından kaynakları taşıyın.


## <a name="why-use-resource-mover"></a>Kaynak taşıyıcısı neden kullanılmalıdır?

Kaynak taşıyıcısı şunları sağlar:

- Kaynakları bölgeler arasında taşımak için tek bir hub.
- Daha az taşıma süresi ve karmaşıklığı. İhtiyacınız olan her şey tek bir konumda.
- Farklı türlerde Azure kaynaklarını taşımak için basit ve tutarlı bir deneyim.
- Taşımak istediğiniz kaynaklar arasında bağımlılıkları belirlemenin kolay bir yolu. Bu, ilgili kaynakları bir arada taşımanızı sağlar, böylece taşıma işleminden sonra her şeyin hedef bölgede beklendiği gibi çalışması gerekir.
- Taşıma işleminden sonra silmek istiyorsanız kaynak bölgedeki kaynakların otomatik olarak temizlenmesi.
- Edici. Bir taşımayı deneyebilir ve tam bir taşıma yapmak istemiyorsanız bu uygulamayı atabilirsiniz.

## <a name="move-across-regions"></a>Bölgeler arasında taşıma

Kaynakları bölgeler arasında taşımak için, taşımak istediğiniz kaynakları seçersiniz. Kaynak taşıyıcısı bu kaynakları doğrular ve diğer kaynaklarda sahip oldukları bağımlılıkları çözer. Bağımlılıklar varsa, birkaç seçeneğiniz vardır:
- Bağımlı kaynakları hedef bölgeye taşıyın.
- Bağımlı kaynakları taşımayın, ancak bunun yerine hedef bölgedeki eşdeğer kaynakları kullanın.

Tüm bağımlılıklar çözümlendikten sonra, kaynak taşıyıcısı basit bir taşıma sürecinde size yol gösterir.

1. İlk taşımayı başlatın.
2. İlk taşıma işleminden sonra, taşıma işleminin tamamlanıp tamamlanmayacağına veya taşımanın iptal edilip edilmeyeceğini seçebilirsiniz.
3. Taşıma işlemi tamamlandıktan sonra kaynak konumdaki kaynakları silmek isteyip istemediğinize karar verebilirsiniz.

Kaynakları kaynak taşıyıcısı hub 'ında veya bir kaynak grubu içinden taşıyabilirsiniz. [Daha fazla bilgi edinin](select-move-tool.md)

## <a name="what-resources-can-i-move-across-regions"></a>Bölgeler arasında hangi kaynakları taşıyabilirim?

Kaynak taşıyıcısı kullanarak şu anda bölgeler arasında şu kaynakları taşıyabilirsiniz:

- Azure VM 'Leri ve ilişkili diskler
- Şifrelenmiş Azure VM 'Leri ve ilişkili diskler. Bu, Azure disk şifrelemesi etkinleştirilmiş VM 'Leri ve varsayılan sunucu tarafı şifrelemesini (platform tarafından yönetilen anahtarlar ve müşteri tarafından yönetilen anahtarlarla birlikte) kullanan Azure VM 'lerini içerir.
- NIC’ler
- Kullanılabilirlik kümeleri 
- Azure sanal ağları 
- Genel IP adresleri
- Ağ güvenlik grupları (NSG)
- İç ve genel yük dengeleyiciler 
- Azure SQL veritabanları ve elastik havuzlar


## <a name="next-steps"></a>Sonraki adımlar

Kaynak taşıyıcısı bileşenleri ve taşıma işlemi hakkında [daha fazla bilgi edinin](about-move-process.md) .

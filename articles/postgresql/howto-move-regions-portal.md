---
title: Azure bölgelerini taşıma-Azure portal-PostgreSQL için Azure veritabanı-tek sunucu
description: PostgreSQL için Azure veritabanı sunucusunu, bir Azure bölgesinden bir Azure bölgesinden diğerine, okuma çoğaltması ve Azure portal taşıyın.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/29/2020
ms.openlocfilehash: e770eddc77c0c343880512695b2fdec44fd4d0af
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066560"
---
# <a name="move-an-azure-database-for-azure-database-for-postgresql---single-server-to-another-region-by-using-the-azure-portal"></a>Azure veritabanı for PostgreSQL için Azure veritabanı-tek sunucu, Azure portal kullanarak başka bir bölgeye taşıma

Mevcut bir PostgreSQL için Azure veritabanı sunucusunu bir bölgeden diğerine taşımaya yönelik çeşitli senaryolar vardır. Örneğin, olağanüstü durum kurtarma planlamanızın bir parçası olarak bir üretim sunucusunu başka bir bölgeye taşımak isteyebilirsiniz.

Bir PostgreSQL için Azure veritabanı [çapraz bölge okuma çoğaltması](concepts-read-replicas.md#cross-region-replication) kullanarak başka bir bölgeye taşıma işleminin tamamlanmasını sağlayabilirsiniz. Bunu yapmak için ilk olarak hedef bölgede bir okuma çoğaltması oluşturun. Ardından, okuma ve yazma trafiğini kabul eden tek başına bir sunucu haline getirmek için okuma çoğaltması sunucusuna çoğaltmayı durdurun. 

> [!NOTE]
> Bu makale, sunucunuzu farklı bir bölgeye taşımaya odaklanır. Sunucunuzu farklı bir kaynak grubuna veya aboneliğine taşımak istiyorsanız [taşıma](../azure-resource-manager/management/move-resource-group-and-subscription.md) makalesine başvurun. 

## <a name="prerequisites"></a>Önkoşullar

- Bölgeler arası okuma çoğaltması özelliği yalnızca PostgreSQL için Azure veritabanı-Genel Amaçlı veya bellek için Iyileştirilmiş fiyatlandırma katmanlarında tek bir sunucu için kullanılabilir. Kaynak sunucunun bu fiyatlandırma katmanlarından birinde olduğundan emin olun.

- PostgreSQL için Azure veritabanınızın kaynak sunucu ' dan taşımak istediğiniz Azure bölgesinde olduğundan emin olun.

## <a name="prepare-to-move"></a>Taşımaya hazırlanma

Azure portal kullanarak kaynak sunucuyu çoğaltmaya hazırlamak için aşağıdaki adımları kullanın: 

1. [Azure portal](https://portal.azure.com/) oturum açın.
1. Kaynak sunucu olarak kullanmak istediğiniz PostgreSQL için Azure veritabanı sunucusunu seçin. Bu eylem **genel bakış** sayfasını açar.
1. Sunucu menüsünden **çoğaltma**' yı seçin. Azure çoğaltma desteği en az **çoğaltmaya** ayarlanmışsa, okuma çoğaltmaları oluşturabilirsiniz. 
1. Azure çoğaltma desteği en az **çoğaltma** olarak ayarlanmamışsa, ayarlayın. **Kaydet**’i seçin.
1. **Evet**' i seçerek değişikliği uygulamak için sunucuyu yeniden başlatın.
1. İşlem tamamlandıktan sonra iki Azure portal bildirimi alacaksınız. Sunucu parametresini güncelleştirmek için bir bildirim vardır. Sunucu yeniden başlatması için hemen sonraki bir bildirim daha vardır.
1. Çoğaltma araç çubuğunu güncelleştirmek için Azure portal sayfasını yenileyin. Artık bu sunucu için okuma çoğaltmaları oluşturabilirsiniz.

Azure portal kullanarak hedef bölgede çapraz bölge okuma çoğaltması sunucusu oluşturmak için aşağıdaki adımları kullanın:

1. Kaynak sunucu olarak kullanmak istediğiniz PostgreSQL için Azure veritabanı sunucusunu seçin.
1. **Ayarlar**' ın altında, menüden **çoğaltma** ' yı seçin.
1. **Çoğaltma ekle**' yi seçin.
1. Çoğaltma sunucusu için bir ad girin.
1. Çoğaltma sunucusunun konumunu seçin. Varsayılan konum, birincil sunucu ile aynıdır. Çoğaltmanın dağıtılmasını istediğiniz hedef konumu seçtiğinizden emin olun.
1. Çoğaltmanın oluşturulmasını onaylamak için **Tamam ' ı** seçin. Çoğaltma oluşturma sırasında, veriler kaynak sunucudan çoğaltmaya kopyalanır. Oluşturma zamanı, kaynak sunucunun boyutuyla orantılı olarak son birkaç dakika veya daha fazla olabilir.

>[!NOTE]
> Bir çoğaltma oluşturduğunuzda, birincil sunucunun güvenlik duvarı kuralları ve sanal ağ hizmeti uç noktaları aktarılmaz. Bu kuralların çoğaltma için bağımsız olarak ayarlanması gerekir.

## <a name="move"></a>Taşı

> [!IMPORTANT]
> Tek başına sunucu tekrar bir çoğaltmaya yapılamaz.
> Bir okuma çoğaltmasında çoğaltmayı durdurmadan önce, çoğaltmanın gerekli tüm verilere sahip olduğundan emin olun.

Azure portal çoğaltmaya çoğaltmayı durdurmak için aşağıdaki adımları kullanın:

1. Çoğaltma oluşturulduktan sonra PostgreSQL için Azure veritabanı kaynak sunucusunu bulun ve seçin. 
1. **Ayarlar**' ın altında, menüden **çoğaltma** ' yı seçin.
1. Çoğaltma sunucusunu seçin.
1. **Çoğaltmayı durdur**' u seçin.
1. **Tamam**' a tıklayarak çoğaltmayı durdurmak istediğinizi onaylayın.

## <a name="clean-up-source-server"></a>Kaynak sunucuyu temizle

PostgreSQL için kaynak Azure veritabanı sunucusunu silmek isteyebilirsiniz. Bunu yapmak için aşağıdaki adımları kullanın:

1. Çoğaltma oluşturulduktan sonra PostgreSQL için Azure veritabanı kaynak sunucusunu bulun ve seçin.
1. **Genel bakış** penceresinde **Sil**' i seçin.
1. Silmek istediğinizi onaylamak için kaynak sunucunun adını yazın.
1. **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir PostgreSQL için Azure veritabanı sunucusunu Azure portal kullanarak bir bölgeden diğerine taşımış sonra gereksiz kaynak kaynaklarını temizlüksiniz. 

- [Okuma çoğaltmaları](concepts-read-replicas.md) hakkında daha fazla bilgi edinin
- [Azure Portal okuma çoğaltmalarını yönetme](howto-read-replicas-portal.md) hakkında daha fazla bilgi edinin
- [İş sürekliliği](concepts-business-continuity.md) seçenekleri hakkında daha fazla bilgi edinin
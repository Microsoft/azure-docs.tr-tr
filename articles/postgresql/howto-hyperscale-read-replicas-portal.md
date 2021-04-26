---
title: Okuma çoğaltmalarını yönetme-Azure portal-PostgreSQL için Azure veritabanı-hiper ölçek (Citus)
description: Azure portal 'ten PostgreSQL için Azure veritabanı-hiper ölçek (Citus) için okuma çoğaltmaları yönetme hakkında bilgi edinin.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 4d3a88692da6d1fc78e96a6261d42d3ca97b0dd0
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024039"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---hyperscale-citus-from-the-azure-portal"></a>Azure portal 'tan PostgreSQL için Azure veritabanı-hiper ölçek (Citus) içinde okuma çoğaltmaları oluşturun ve yönetin

> [!IMPORTANT]
> Hyperscale (Citus) içindeki okuma çoğaltmaları Şu anda önizlemededir. Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
>
> Diğer yeni özelliklerin bir listesini, [hiper ölçek için Önizleme özellikleri (Citus)](hyperscale-preview-features.md)sayfasına bakabilirsiniz.

Bu makalede, Azure portal hiper ölçekte (Citus) okuma çoğaltmaları oluşturmayı ve yönetmeyi öğreneceksiniz. Okuma çoğaltmaları hakkında daha fazla bilgi edinmek için bkz. [genel bakış](concepts-hyperscale-read-replicas.md).


## <a name="prerequisites"></a>Önkoşullar

Birincil olacak bir [hiper ölçek (Citus) sunucu grubu](quickstart-create-hyperscale-portal.md) .

## <a name="create-a-read-replica"></a>Okuma amaçlı çoğaltma oluşturma

Bir okuma çoğaltması oluşturmak için aşağıdaki adımları izleyin:

1. Birincil olarak kullanılacak PostgreSQL için mevcut bir Azure veritabanı sunucu grubu seçin. 

2. Sunucu grubu kenar çubuğunda, **sunucu grubu yönetimi** altında **çoğaltma**' yı seçin.

3. **Çoğaltma ekle**' yi seçin.

4. Okuma çoğaltması için bir ad girin. 

5. Çoğaltmanın oluşturulmasını onaylamak için **Tamam ' ı** seçin.

Okuma çoğaltması oluşturulduktan sonra **çoğaltma** penceresinden görüntülenebilir.

> [!IMPORTANT]
>
> [Okuma çoğaltmasına genel bakış konusunun Konular bölümünü](concepts-hyperscale-read-replicas.md#considerations)gözden geçirin.
>
> Birincil sunucu grubu ayarı yeni bir değere güncellenmesinden önce, çoğaltma ayarını eşit veya daha büyük bir değere güncelleştirin. Bu eylem, çoğaltmanın ana üzerinde yapılan değişikliklerle devam etmesine yardımcı olur.

## <a name="delete-a-primary-server-group"></a>Birincil sunucu grubunu silme

Birincil sunucu grubunu silmek için, tek başına hiper ölçek (Citus) sunucu grubunu silmek üzere aynı adımları kullanırsınız. 

> [!IMPORTANT]
>
> Bir birincil sunucu grubunu sildiğinizde, tüm okuma çoğaltmalarına çoğaltma durdurulur. Okuma çoğaltmaları artık hem okuma hem de yazma işlemlerini destekleyen tek başına sunucu grupları olur.

Bir sunucu grubunu Azure portal silmek için şu adımları izleyin:

1. Azure portal, PostgreSQL için birincil Azure veritabanı sunucu grubunu seçin.

2. Sunucu grubu için **genel bakış** sayfasını açın. **Sil**’i seçin.
 
3. Silinecek birincil sunucu grubunun adını girin. Birincil sunucu grubunun silinmesini onaylamak için **Sil** ' i seçin.
 

## <a name="delete-a-replica"></a>Bir çoğaltmayı sil

Bir okuma çoğaltmasını, bir birincil sunucu grubunu silme ile aynı şekilde silebilirsiniz.

- Azure portal, okuma çoğaltması için **genel bakış** sayfasını açın. **Sil**’i seçin.
 
Ayrıca, aşağıdaki adımları izleyerek **çoğaltma** penceresinde okuma çoğaltmasını silebilirsiniz:

1. Azure portal, birincil hiper ölçek (Citus) sunucu grubunuzu seçin.

2. Sunucu grubu menüsündeki **sunucu grubu yönetimi** altında **çoğaltma**' yı seçin.

3. Silinecek okuma çoğaltmasını seçin.
 
4. **Çoğaltmayı Sil**' i seçin.
 
5. Silinecek çoğaltmanın adını girin. Çoğaltmanın silinmesini onaylamak için **Sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

* [PostgreSQL Için Azure veritabanı 'nda (Citus) okuma çoğaltmaları](concepts-hyperscale-read-replicas.md)hakkında daha fazla bilgi edinin.

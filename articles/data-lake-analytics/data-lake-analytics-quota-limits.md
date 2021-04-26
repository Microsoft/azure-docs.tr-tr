---
title: Azure Data Lake Analytics’te kotaları ve limitleri ayarlama
description: Azure Data Lake Analytics (ADLA) hesaplarında kotaları ve limitleri ayarlama ve artırma hakkında bilgi edinin.
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 03/15/2018
ms.openlocfilehash: 416b77fd86316acb8098531257e12b591a0ff052
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101710184"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Azure Data Lake Analytics’te kotaları ve limitleri ayarlama

Azure Data Lake Analytics (ADLA) hesaplarında kotayı ve limitleri ayarlamayı ve artırmayı öğrenin. Bu sınırları bilmek U-SQL iş davranışınızı anlamanıza yardımcı olur. Tüm Kota limitleri hafif olduğundan Azure desteği ile iletişime geçerek en yüksek limitlerin artmasını sağlayabilirsiniz.

## <a name="azure-subscriptions-limits"></a>Azure abonelik limitleri

Her **bölge için abonelik başına MAKSIMUM ADLA hesabı sayısı:** 5

Altıncı ADLA hesabı oluşturmaya çalışırsanız, "abonelik adı altındaki bölgede izin verilen en fazla Data Lake Analytics hesabı sayısına (5) ulaştınız" hatası alırsınız.

Bu sınırın ötesine geçmek istiyorsanız, bu seçenekleri deneyebilirsiniz:

- uygunsa başka bir bölge seçin
- Kota artışı istemek için [bir destek bileti açarak](#increase-maximum-quota-limits) Azure desteği 'ne başvurun.

## <a name="default-adla-account-limits"></a>Varsayılan ADLA hesabı limitleri

**Hesap başına en fazla analiz birimi (Avustralya) sayısı:** 250, varsayılan 32

Bu, hesabınızda eşzamanlı olarak çalışabilecek maksimum au sayısıdır. Toplam çalışan AU sayısı bu sınırı aşarsa, daha yeni işler otomatik olarak sıraya alınır. Örnek:

- Yalnızca 32 au ile çalışan tek bir işiniz varsa, ikinci bir işi gönderdiğinizde, ilk iş tamamlanana kadar iş kuyruğuna göre bekleyecektir.
- Çalışan dört işiniz zaten varsa ve her biri 8 au kullanıyorsa, 8 au 'yı gerektiren beşinci bir iş gönderdiğinizde, 8 au kullanılabilir olana kadar iş sırasında bekler.

    ![Azure Data Lake Analytics sınırları ve kota sayfası](./media/data-lake-analytics-quota-limits/adjust-quota-limits.png)

**İş başına en fazla analiz birimi (Avustralya) sayısı:** 250, varsayılan 32

Bu, her bir işin hesabınızda atanabileceği en fazla au sayısıdır. Bu sınırdan daha fazla atanan işler, gönderenin iş başına daha fazla Avustralya sağlayan bir işlem ilkesi (iş gönderme sınırı) tarafından etkilenmediği takdirde reddedilir. Bu değerin üst sınırı, hesap için AU sınırıdır.

**Hesap başına en fazla eşzamanlı U-SQL işi sayısı:** 20

Bu, hesabınızda eşzamanlı olarak çalışabilecek en fazla iş sayısıdır. Bu değerin üstünde, daha yeni işler otomatik olarak sıraya alınır.

## <a name="adjust-adla-account-limits"></a>ADLA hesabı sınırlarını ayarla

1. [Azure Portal](https://portal.azure.com)oturum açın.
2. Mevcut bir ADLA hesabı seçin.
3. **Özellikler**'e tıklayın.
4. **Maksimum Avustralya**, **en fazla çalışan Iş sayısı** ve **iş gönderme limitlerinin** değerlerini gereksinimlerinize uyacak şekilde ayarlayın.

## <a name="increase-maximum-quota-limits"></a>Maksimum kota sınırlarını artır

Azure limitleriyle ilgili daha fazla bilgiyi [Azure hizmeti 'ne özgü sınırlar belgelerinde](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-analytics-limits)bulabilirsiniz.

1. Azure portal bir destek isteği açın.

   ![Azure Data Lake Analytics portal-yardım ve destek](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

   ![Azure Data Lake Analytics Portal yeni destek isteği](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)

2. Sorun türü **kotasını** seçin.

3. **Aboneliğinizi** seçin (bir "deneme" aboneliği olmadığından emin olun).

4. Kota türü **Data Lake Analytics** seçin.

   ![Azure Data Lake Analytics destek isteği kota türü](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. Sorun sayfasında, bu ek kapasiteye neden ihtiyacınız olduğuna ilişkin **ayrıntıları** içeren istediğiniz artış limitini açıklayın.

   ![Azure Data Lake Analytics destek isteği ayrıntıları](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. İletişim bilgilerinizi doğrulayın ve destek isteği oluşturun.

Microsoft isteğinizi inceler ve mümkün olan en kısa sürede iş ihtiyaçlarınızı karşılamaya çalışır.

## <a name="next-steps"></a>Sonraki adımlar

- [Microsoft Azure Data Lake Analytics'e genel bakış](data-lake-analytics-overview.md)
- [Azure Data Lake Analytics'i Azure PowerShell'i kullanarak yönetme](data-lake-analytics-manage-use-powershell.md)
- [Azure portal kullanarak Azure Data Lake Analytics işleri izleme ve sorunlarını giderme](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

---
title: Okuma çoğaltmalarını yönetme-Azure portal-MySQL için Azure veritabanı
description: Azure portal kullanarak MySQL için Azure veritabanı 'nda okuma çoğaltmalarını ayarlamayı ve yönetmeyi öğrenin.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/10/2020
ms.openlocfilehash: 26b503e7d55ed3d2f9bd06837551655e7af05a17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541949"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Azure portal kullanarak MySQL için Azure veritabanı 'nda okuma çoğaltmaları oluşturma ve yönetme

Bu makalede, Azure portal kullanarak MySQL için Azure veritabanı hizmetinde okuma çoğaltmaları oluşturmayı ve yönetmeyi öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

- Kaynak sunucu olarak kullanılacak [MySQL Için Azure veritabanı sunucusu](quickstart-create-mysql-server-database-using-azure-portal.md) .

> [!IMPORTANT]
> Çoğaltma oku özelliği yalnızca Genel Amaçlı veya bellek için Iyileştirilmiş fiyatlandırma katmanlarında MySQL için Azure veritabanı sunucuları için kullanılabilir. Kaynak sunucunun bu fiyatlandırma katmanlarından birinde olduğundan emin olun.

## <a name="create-a-read-replica"></a>Okuma amaçlı çoğaltma oluşturma

> [!IMPORTANT]
> Mevcut çoğaltmaları olmayan bir kaynak için çoğaltma oluşturduğunuzda, kaynak ilk olarak çoğaltma için hazırlamak üzere yeniden başlatılır. Bunu dikkate alın ve yoğun bir süre boyunca bu işlemleri gerçekleştirin.

Aşağıdaki adımlar kullanılarak, bir okuma çoğaltması sunucusu oluşturulabilir:

1. [Azure portal](https://portal.azure.com/) oturum açın.

2. Ana sunucu olarak kullanmak istediğiniz MySQL için Azure veritabanı sunucusunu seçin. Bu eylem **genel bakış** sayfasını açar.

3. **Ayarlar**' ın altında, menüden **çoğaltma** ' yı seçin.

4. **Çoğaltma ekle**' yi seçin.

   :::image type="content" source="./media/howto-read-replica-portal/add-replica.png" alt-text="MySQL için Azure veritabanı-çoğaltma":::

5. Çoğaltma sunucusu için bir ad girin.

    :::image type="content" source="./media/howto-read-replica-portal/replica-name.png" alt-text="MySQL için Azure veritabanı-çoğaltma adı":::

6. Çoğaltma sunucusunun konumunu seçin. Varsayılan konum, kaynak sunucu ile aynıdır.

    :::image type="content" source="./media/howto-read-replica-portal/replica-location.png" alt-text="MySQL için Azure veritabanı-çoğaltma konumu":::

   > [!NOTE]
   > İçinde bir çoğaltma oluşturabileceğiniz bölgeler hakkında daha fazla bilgi edinmek için [çoğaltma kavramlarını oku makalesini](concepts-read-replicas.md)ziyaret edin. 

7. Çoğaltmanın oluşturulmasını onaylamak için **Tamam ' ı** seçin.

> [!NOTE]
> Okuma çoğaltmaları, ana sunucuyla aynı sunucu yapılandırmasıyla oluşturulur. Çoğaltma sunucusu yapılandırması oluşturulduktan sonra değiştirilebilir. Çoğaltma sunucusu her zaman aynı kaynak grubunda ve kaynak sunucuyla aynı abonelikte oluşturulur. Farklı bir kaynak grubuna veya farklı bir aboneliğe çoğaltma sunucusu oluşturmak istiyorsanız, oluşturulduktan sonra [Çoğaltma sunucusunu taşıyabilirsiniz](../azure-resource-manager/management/move-resource-group-and-subscription.md) . Çoğaltmanın ana öğe ile devam edebileceğinden emin olmak için çoğaltma sunucusunun yapılandırmasının kaynaktan eşit veya daha büyük bir değer tutulması önerilir.

Çoğaltma sunucusu oluşturulduktan sonra **çoğaltma** dikey penceresinden görüntülenebilir.

   :::image type="content" source="./media/howto-read-replica-portal/list-replica.png" alt-text="MySQL için Azure veritabanı-liste çoğaltmaları":::

## <a name="stop-replication-to-a-replica-server"></a>Çoğaltma sunucusuna çoğaltmayı durdur

> [!IMPORTANT]
> Bir sunucuya çoğaltma durdurulduğunda geri alınamaz. Bir kaynak ve çoğaltma arasında çoğaltma durdurulduktan sonra geri alınamaz. Çoğaltma sunucusu daha sonra tek başına bir sunucu olur ve artık hem okuma hem de yazma işlemlerini destekler. Bu sunucu tekrar bir çoğaltmaya yapılamaz.

Azure portal bir kaynak ve çoğaltma sunucusu arasında çoğaltmayı durdurmak için aşağıdaki adımları kullanın:

1. Azure portal, MySQL için kaynak Azure veritabanı sunucusunu seçin. 

2. **Ayarlar**' ın altında, menüden **çoğaltma** ' yı seçin.

3. Çoğaltmasını durdurmak istediğiniz çoğaltma sunucusunu seçin.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-select.png" alt-text="MySQL için Azure veritabanı-çoğaltmayı durdur Sunucu Seç":::

4. **Çoğaltmayı durdur**' u seçin.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication.png" alt-text="MySQL için Azure veritabanı-çoğaltmayı durdur":::

5. **Tamam**' a tıklayarak çoğaltmayı durdurmak istediğinizi onaylayın.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-confirm.png" alt-text="MySQL için Azure veritabanı-çoğaltmayı durdur Onayla":::

## <a name="delete-a-replica-server"></a>Çoğaltma sunucusunu silme

Azure portal bir okuma çoğaltması sunucusunu silmek için aşağıdaki adımları kullanın:

1. Azure portal, MySQL için kaynak Azure veritabanı sunucusunu seçin.

2. **Ayarlar**' ın altında, menüden **çoğaltma** ' yı seçin.

3. Silmek istediğiniz çoğaltma sunucusunu seçin.

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-select.png" alt-text="MySQL için Azure veritabanı-çoğaltmayı silme sunucu seçme":::

4. **Çoğaltmayı Sil** ' i seçin

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica.png" alt-text="MySQL için Azure veritabanı-çoğaltmayı silme":::

5. Çoğaltmanın adını yazın ve çoğaltmayı silmeyi onaylamak için **Sil** ' e tıklayın.  

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-confirm.png" alt-text="MySQL için Azure veritabanı-çoğaltmayı silme onaylama":::

## <a name="delete-a-source-server"></a>Kaynak sunucuyu silme

> [!IMPORTANT]
> Bir kaynak sunucu durdurulduğunda, tüm çoğaltma sunucularına çoğaltma durdurulur ve kaynak sunucu silinir. Çoğaltma sunucuları artık hem okuma hem de yazma işlemlerini destekleyen tek başına sunucular haline gelir.

Azure portal bir kaynak sunucuyu silmek için aşağıdaki adımları kullanın:

1. Azure portal, MySQL için kaynak Azure veritabanı sunucusunu seçin.

2. **Genel bakışta** **Sil**' i seçin.

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-overview.png" alt-text="MySQL için Azure veritabanı-ana şablonu Sil":::

3. Kaynak sunucunun adını yazın ve **Sil** ' e tıklayarak kaynak sunucuyu silmeyi onaylayın.  

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-confirm.png" alt-text="MySQL için Azure veritabanı-ana öğe Silmeyi Onayla":::

## <a name="monitor-replication"></a>Çoğaltmayı izleme

1. [Azure Portal](https://portal.azure.com/), Izlemek istediğiniz MySQL Için Azure veritabanı sunucusu çoğaltmasını seçin.

2. Kenar çubuğunun **izleme** bölümü altında **ölçümler**' i seçin:

3. Kullanılabilir ölçümler açılan listesinden **saniye cinsinden çoğaltma gecikmesi** seçin.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-select-replication-lag.png" alt-text="Çoğaltma gecikmesini seçin":::

4. Görüntülemek istediğiniz zaman aralığını seçin. Aşağıdaki görüntü 30 dakikalık bir zaman aralığı seçer.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="Zaman aralığını seçin":::

5. Seçilen zaman aralığı için çoğaltma gecikmesini görüntüleyin. Aşağıdaki görüntüde son 30 dakika görüntülenir.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="Zaman aralığını 30 dakika seçin":::

## <a name="next-steps"></a>Sonraki adımlar

- [Okuma çoğaltmaları](concepts-read-replicas.md) hakkında daha fazla bilgi edinin
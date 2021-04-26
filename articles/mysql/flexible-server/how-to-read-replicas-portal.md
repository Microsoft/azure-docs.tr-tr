---
title: Okuma çoğaltmalarını yönetme-Azure portal-MySQL için Azure veritabanı-esnek sunucu
description: Azure portal kullanarak MySQL için Azure veritabanı esnek sunucusu 'nda okuma çoğaltmalarını ayarlama ve yönetme hakkında bilgi edinin.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: fd303804706f9ae210e6714cc8698c94c39ebef6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105106862"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-portal"></a>Azure portal kullanarak MySQL için Azure veritabanı esnek sunucusu 'nda okuma çoğaltmaları oluşturma ve yönetme

> [!IMPORTANT]
> MySQL için Azure veritabanı 'nda çoğaltmaları okuma-esnek sunucu önizlemededir.

Bu makalede, Azure portal kullanarak MySQL için Azure veritabanı esnek sunucusu 'nda okuma çoğaltmaları oluşturmayı ve yönetmeyi öğreneceksiniz.

> [!Note]
> Çoğaltma, yüksek kullanılabilirlik etkinleştirilmiş sunucuda desteklenmiyor. 

## <a name="prerequisites"></a>Önkoşullar

- Kaynak sunucu olarak kullanılacak [MySQL Için Azure veritabanı sunucu esnek sunucusu](quickstart-create-server-portal.md) .

## <a name="create-a-read-replica"></a>Okuma amaçlı çoğaltma oluşturma

> [!IMPORTANT]
> Mevcut çoğaltmaları olmayan bir kaynak için çoğaltma oluşturduğunuzda, kaynak ilk olarak çoğaltma için hazırlamak üzere yeniden başlatılır. Bunu dikkate alın ve yoğun bir süre boyunca bu işlemleri gerçekleştirin.

Aşağıdaki adımlar kullanılarak, bir okuma çoğaltması sunucusu oluşturulabilir:

1. [Azure portal](https://portal.azure.com/) oturum açın.

2. Kaynak olarak kullanmak istediğiniz MySQL için mevcut Azure veritabanı esnek sunucusunu seçin. Bu eylem **genel bakış** sayfasını açar.

3. **Ayarlar**' ın altında, menüden **çoğaltma** ' yı seçin.

4. **Çoğaltma ekle**' yi seçin.

   :::image type="content" source="./media/how-to-read-replica-portal/add-replica.png" alt-text="MySQL için Azure veritabanı-çoğaltma":::

5. Çoğaltma sunucusu için bir ad girin.

    :::image type="content" source="./media/how-to-read-replica-portal/replica-name.png" alt-text="MySQL için Azure veritabanı-çoğaltma adı":::

6. Çoğaltmanın oluşturulmasını onaylamak için **Tamam ' ı** seçin.

> [!NOTE]
> Okuma çoğaltmaları kaynakla aynı sunucu yapılandırmasıyla oluşturulur. Çoğaltma sunucusu yapılandırması oluşturulduktan sonra değiştirilebilir. Çoğaltma sunucusu her zaman aynı kaynak grubunda, aynı konum ve kaynak sunucuyla aynı abonelikte oluşturulur. Farklı bir kaynak grubuna veya farklı bir aboneliğe çoğaltma sunucusu oluşturmak istiyorsanız, oluşturulduktan sonra [Çoğaltma sunucusunu taşıyabilirsiniz](../../azure-resource-manager/management/move-resource-group-and-subscription.md) . Çoğaltmanın kaynakla devam edebileceğinden emin olmak için çoğaltma sunucusunun yapılandırmasının kaynaktan eşit veya daha büyük bir değer tutulması önerilir.

Çoğaltma sunucusu oluşturulduktan sonra **çoğaltma** dikey penceresinden görüntülenebilir.

   [:::image type="content" source="./media/how-to-read-replica-portal/list-replica.png" alt-text="MySQL için Azure veritabanı-liste çoğaltmaları":::](./media/how-to-read-replica-portal/list-replica.png#lightbox)

## <a name="stop-replication-to-a-replica-server"></a>Çoğaltma sunucusuna çoğaltmayı durdur

> [!IMPORTANT]
> Bir sunucuya çoğaltma durdurulduğunda geri alınamaz. Bir kaynak ve çoğaltma arasında çoğaltma durdurulduktan sonra geri alınamaz. Çoğaltma sunucusu daha sonra tek başına bir sunucu olur ve artık hem okuma hem de yazma işlemlerini destekler. Bu sunucu tekrar bir çoğaltmaya yapılamaz.

Azure portal bir kaynak ve çoğaltma sunucusu arasında çoğaltmayı durdurmak için aşağıdaki adımları kullanın:

1. Azure portal, MySQL için kaynak Azure veritabanı esnek sunucusunu seçin. 

2. **Ayarlar**' ın altında, menüden **çoğaltma** ' yı seçin.

3. Çoğaltmasını durdurmak istediğiniz çoğaltma sunucusunu seçin.

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-select.png" alt-text="MySQL için Azure veritabanı-çoğaltmayı durdur Sunucu Seç":::](./media/how-to-read-replica-portal/stop-replication-select.png#lightbox)

4. **Çoğaltmayı durdur**' u seçin.

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication.png" alt-text="MySQL için Azure veritabanı-çoğaltmayı durdur":::](./media/how-to-read-replica-portal/stop-replication.png#lightbox)

5. **Tamam**' a tıklayarak çoğaltmayı durdurmak istediğinizi onaylayın.

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-confirm.png" alt-text="MySQL için Azure veritabanı-çoğaltmayı durdur Onayla":::](./media/how-to-read-replica-portal/stop-replication-confirm.png#lightbox)

## <a name="delete-a-replica-server"></a>Çoğaltma sunucusunu silme

Azure portal bir okuma çoğaltması sunucusunu silmek için aşağıdaki adımları kullanın:

1. Azure portal, MySQL için kaynak Azure veritabanı esnek sunucusunu seçin.

2. **Ayarlar**' ın altında, menüden **çoğaltma** ' yı seçin.

3. Silmek istediğiniz çoğaltma sunucusunu seçin.

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-replica-select.png" alt-text="MySQL için Azure veritabanı-çoğaltmayı silme sunucu seçme":::](./media/how-to-read-replica-portal/delete-replica-select.png#lightbox)

4. **Çoğaltmayı Sil** ' i seçin

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica.png" alt-text="MySQL için Azure veritabanı-çoğaltmayı silme":::

5. Çoğaltmanın adını yazın ve çoğaltmayı silmeyi onaylamak için **Sil** ' e tıklayın.  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica-confirm.png" alt-text="MySQL için Azure veritabanı-çoğaltmayı silme onaylama":::

## <a name="delete-a-source-server"></a>Kaynak sunucuyu silme

> [!IMPORTANT]
> Bir kaynak sunucu durdurulduğunda, tüm çoğaltma sunucularına çoğaltma durdurulur ve kaynak sunucu silinir. Çoğaltma sunucuları artık hem okuma hem de yazma işlemlerini destekleyen tek başına sunucular haline gelir.

Azure portal bir kaynak sunucuyu silmek için aşağıdaki adımları kullanın:

1. Azure portal, MySQL için kaynak Azure veritabanı esnek sunucusunu seçin.

2. **Genel bakışta** **Sil**' i seçin.

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-master-overview.png" alt-text="MySQL için Azure veritabanı-kaynağı Sil":::](./media/how-to-read-replica-portal/delete-master-overview.png#lightbox)

3. Kaynak sunucunun adını yazın ve **Sil** ' e tıklayarak kaynak sunucuyu silmeyi onaylayın.  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-master-confirm.png" alt-text="MySQL için Azure veritabanı-kaynak Silmeyi Onayla":::

## <a name="monitor-replication"></a>Çoğaltmayı izleme

1. [Azure Portal](https://portal.azure.com/), Izlemek istediğiniz MySQL Için Azure veritabanı esnek sunucusu çoğaltmasını seçin.

2. Kenar çubuğunun **izleme** bölümü altında **ölçümler**' i seçin:

3. Kullanılabilir ölçümler açılan listesinden **saniye cinsinden çoğaltma gecikmesi** seçin.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-select-replication-lag.png" alt-text="Çoğaltma gecikmesini seçin":::](./media/how-to-read-replica-portal/monitor-select-replication-lag.png#lightbox)

4. Görüntülemek istediğiniz zaman aralığını seçin. Aşağıdaki görüntü 30 dakikalık bir zaman aralığı seçer.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="Zaman aralığını seçin":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png#lightbox)

5. Seçilen zaman aralığı için çoğaltma gecikmesini görüntüleyin. Aşağıdaki görüntüde son 30 dakika görüntülenir.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="Zaman aralığını 30 dakika seçin":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png#lightbox)

## <a name="next-steps"></a>Sonraki adımlar

- [Okuma çoğaltmaları](concepts-read-replicas.md) hakkında daha fazla bilgi edinin
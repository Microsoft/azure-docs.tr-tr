---
title: Okuma çoğaltmalarını yönetme-Azure portal-MariaDB için Azure veritabanı
description: Bu makalede Portal kullanılarak MariaDB için Azure veritabanı 'nda okuma çoğaltmalarının nasıl ayarlanacağı ve yönetileceği açıklanmaktadır
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/10/2020
ms.openlocfilehash: 3ca6ef3c368a5f578cc90fae3923caa89f3b076a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98665013"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>Azure portal kullanarak MariaDB için Azure veritabanı 'nda okuma çoğaltmaları oluşturma ve yönetme

Bu makalede, Azure portal kullanarak MariaDB hizmeti için Azure veritabanı 'nda okuma çoğaltmaları oluşturmayı ve yönetmeyi öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

- Kaynak sunucu olarak kullanılacak [MariaDB sunucusu Için Azure veritabanı](quickstart-create-mariadb-server-database-using-azure-portal.md) .

> [!IMPORTANT]
> Çoğaltma oku özelliği yalnızca Genel Amaçlı veya bellek için Iyileştirilmiş fiyatlandırma katmanlarında bulunan MariaDB sunucuları için Azure veritabanı 'nda kullanılabilir. Kaynak sunucunun bu fiyatlandırma katmanlarından birinde olduğundan emin olun.

## <a name="create-a-read-replica"></a>Okuma amaçlı çoğaltma oluşturma

> [!IMPORTANT]
> Mevcut çoğaltmaları olmayan bir kaynak için çoğaltma oluşturduğunuzda, kaynak ilk olarak çoğaltma için hazırlamak üzere yeniden başlatılır. Bunu dikkate alın ve yoğun bir süre boyunca bu işlemleri gerçekleştirin.

Aşağıdaki adımlar kullanılarak, bir okuma çoğaltması sunucusu oluşturulabilir:

1. [Azure portal](https://portal.azure.com/) oturum açın.

2. Ana sunucu olarak kullanmak istediğiniz MariaDB sunucusu için mevcut Azure veritabanını seçin. Bu eylem **genel bakış** sayfasını açar.

3. **Ayarlar**' ın altında, menüden **çoğaltma** ' yı seçin.

4. **Çoğaltma ekle**' yi seçin.

   ![MariaDB için Azure veritabanı-çoğaltma](./media/howto-read-replica-portal/add-replica.png)

5. Çoğaltma sunucusu için bir ad girin.

    ![MariaDB için Azure veritabanı-çoğaltma adı](./media/howto-read-replica-portal/replica-name.png)

6. Çoğaltma sunucusunun konumunu seçin. Varsayılan konum, kaynak sunucu ile aynıdır.

    ![MariaDB için Azure veritabanı-çoğaltma konumu](./media/howto-read-replica-portal/replica-location.png)

7. Çoğaltmanın oluşturulmasını onaylamak için **Tamam ' ı** seçin.

> [!NOTE]
> Okuma çoğaltmaları, ana sunucuyla aynı sunucu yapılandırmasıyla oluşturulur. Çoğaltma sunucusu yapılandırması oluşturulduktan sonra değiştirilebilir. Çoğaltmanın ana öğe ile devam edebileceğinden emin olmak için çoğaltma sunucusunun yapılandırmasının kaynaktan eşit veya daha büyük bir değer tutulması önerilir.

Çoğaltma sunucusu oluşturulduktan sonra **çoğaltma** dikey penceresinden görüntülenebilir.

   ![MariaDB için Azure veritabanı-çoğaltmaları listeleme](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Çoğaltma sunucusuna çoğaltmayı durdur

> [!IMPORTANT]
> Bir sunucuya çoğaltma durdurulduğunda geri alınamaz. Bir kaynak ve çoğaltma arasında çoğaltma durdurulduktan sonra geri alınamaz. Çoğaltma sunucusu daha sonra tek başına bir sunucu olur ve artık hem okuma hem de yazma işlemlerini destekler. Bu sunucu tekrar bir çoğaltmaya yapılamaz.

Azure portal bir kaynak ve çoğaltma sunucusu arasında çoğaltmayı durdurmak için aşağıdaki adımları kullanın:

1. Azure portal, MariaDB sunucusu için kaynak Azure veritabanını seçin. 

2. **Ayarlar**' ın altında, menüden **çoğaltma** ' yı seçin.

3. Çoğaltmasını durdurmak istediğiniz çoğaltma sunucusunu seçin.

   ![MariaDB için Azure veritabanı-çoğaltmayı durdur Sunucu Seç](./media/howto-read-replica-portal/stop-replication-select.png)

4. **Çoğaltmayı durdur**' u seçin.

   ![MariaDB için Azure veritabanı-çoğaltmayı durdur](./media/howto-read-replica-portal/stop-replication.png)

5. **Tamam**' a tıklayarak çoğaltmayı durdurmak istediğinizi onaylayın.

   ![MariaDB için Azure veritabanı-çoğaltmayı durdur Onayla](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Çoğaltma sunucusunu silme

Azure portal bir okuma çoğaltması sunucusunu silmek için aşağıdaki adımları kullanın:

1. Azure portal, MariaDB sunucusu için kaynak Azure veritabanını seçin.

2. **Ayarlar**' ın altında, menüden **çoğaltma** ' yı seçin.

3. Silmek istediğiniz çoğaltma sunucusunu seçin.

   ![MariaDB için Azure veritabanı-çoğaltmayı silme sunucu Seç](./media/howto-read-replica-portal/delete-replica-select.png)

4. **Çoğaltmayı Sil** ' i seçin

   ![MariaDB için Azure veritabanı-çoğaltmayı silme](./media/howto-read-replica-portal/delete-replica.png)

5. Çoğaltmanın adını yazın ve çoğaltmayı silmeyi onaylamak için **Sil** ' e tıklayın.  

   ![MariaDB için Azure veritabanı-çoğaltma silme onaylama](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-source-server"></a>Kaynak sunucuyu silme

> [!IMPORTANT]
> Bir kaynak sunucu durdurulduğunda, tüm çoğaltma sunucularına çoğaltma durdurulur ve kaynak sunucu silinir. Çoğaltma sunucuları artık hem okuma hem de yazma işlemlerini destekleyen tek başına sunucular haline gelir.

Azure portal bir kaynak sunucuyu silmek için aşağıdaki adımları kullanın:

1. Azure portal, MariaDB sunucusu için kaynak Azure veritabanını seçin.

2. **Genel bakışta** **Sil**' i seçin.

   ![MariaDB için Azure veritabanı-ana öğe silme](./media/howto-read-replica-portal/delete-master-overview.png)

3. Kaynak sunucunun adını yazın ve **Sil** ' e tıklayarak kaynak sunucuyu silmeyi onaylayın.  

   ![MariaDB için Azure veritabanı-ana onay silme](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Çoğaltmayı izleme

1. [Azure Portal](https://portal.azure.com/), Izlemek Istediğiniz MariaDB sunucusu Için çoğaltma Azure veritabanı ' nı seçin.

2. Kenar çubuğunun **izleme** bölümü altında **ölçümler**' i seçin:

3. Kullanılabilir ölçümler açılan listesinden **saniye cinsinden çoğaltma gecikmesi** seçin.

   ![Çoğaltma gecikmesini seçin](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Görüntülemek istediğiniz zaman aralığını seçin. Aşağıdaki görüntü 30 dakikalık bir zaman aralığı seçer.

   ![Zaman aralığını seçin](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Seçilen zaman aralığı için çoğaltma gecikmesini görüntüleyin. Aşağıdaki görüntüde, büyük bir iş yükü için son 30 dakika görüntülenir.

   ![Zaman aralığını 30 dakika seçin](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Okuma çoğaltmaları](concepts-read-replicas.md) hakkında daha fazla bilgi edinin

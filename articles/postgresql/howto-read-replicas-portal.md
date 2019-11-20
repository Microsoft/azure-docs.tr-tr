---
title: PostgreSQL için Azure veritabanı için okuma çoğaltmalarını yönetme-Azure portal tek sunucu
description: Bkz. Azure Database for PostgreSQL için Azure veritabanı-tek sunucu Azure portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: 0ff6cd50a5a6cb1599a2248fbc61b0b6b307e791
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995446"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>PostgreSQL için Azure veritabanı 'nda okuma çoğaltmaları oluşturma ve yönetme-tek sunucu Azure portal

Bu makalede, Azure portal 'tan PostgreSQL için Azure veritabanı 'nda okuma çoğaltmaları oluşturmayı ve yönetmeyi öğreneceksiniz. Okuma çoğaltmaları hakkında daha fazla bilgi edinmek için bkz. [genel bakış](concepts-read-replicas.md).


## <a name="prerequisites"></a>Önkoşullar
[PostgreSQL Için Azure veritabanı sunucusunun](quickstart-create-server-database-portal.md) ana sunucu olması.

## <a name="prepare-the-master-server"></a>Ana sunucuyu hazırlama
Bu adımların Genel Amaçlı veya bellek için Iyileştirilmiş katmanlarda bir ana sunucu hazırlamak için kullanılması gerekir. Ana sunucu, Azure. replication_support parametresi ayarlanarak çoğaltma için hazırlanır. Çoğaltma parametresi değiştirildiğinde, değişikliğin etkili olması için sunucu yeniden başlatması gerekir. Azure portal, bu iki adım tek bir düğmeyle kapsüllenir, **çoğaltma desteğini etkinleştirir**.

1. Azure portal, ana öğe olarak kullanılacak olan PostgreSQL için Azure veritabanı sunucusunu seçin.

2. Sunucu kenar çubuğunda, **Ayarlar**' ın altında, **çoğaltma**' yı seçin.

3. **Çoğaltma desteğini etkinleştir**' i seçin. 

   ![Çoğaltma desteğini etkinleştir](./media/howto-read-replicas-portal/enable-replication-support.png)

4. Çoğaltma desteğini etkinleştirmek istediğinizi onaylayın. Bu işlem, ana sunucuyu yeniden başlatacak. 

   ![Çoğaltma desteğini etkinleştirmeyi Onayla](./media/howto-read-replicas-portal/confirm-enable-replication.png)
   
5. İşlem tamamlandıktan sonra iki Azure portal bildirimi alacaksınız. Sunucu parametresini güncelleştirmek için bir bildirim vardır. Sunucu yeniden başlatması için hemen sonraki bir bildirim daha vardır.

   ![Başarı bildirimleri-etkinleştir](./media/howto-read-replicas-portal/success-notifications-enable.png)

6. Çoğaltma araç çubuğunu güncelleştirmek için Azure portal sayfasını yenileyin. Artık bu sunucu için okuma çoğaltmaları oluşturabilirsiniz.

   ![Araç çubuğu güncelleştirildi](./media/howto-read-replicas-portal/updated-toolbar.png)
   
Çoğaltma desteğinin etkinleştirilmesi, ana sunucu başına tek seferlik bir işlemdir. Kolaylık olması için **çoğaltma desteğini devre dışı bırak** düğmesi sunulmaktadır. Bu ana sunucuda hiçbir şekilde çoğaltma oluşturmamanız gerekmedikçe, çoğaltma desteğinin devre dışı bırakılmasını önermiyoruz. Ana sunucunuzda çoğaltmalar varsa çoğaltma desteğini devre dışı bırakabilirsiniz.


## <a name="create-a-read-replica"></a>Salt okunur bir çoğaltma oluşturma
Bir okuma çoğaltması oluşturmak için aşağıdaki adımları izleyin:

1. Ana sunucu olarak kullanılacak olan PostgreSQL için Azure veritabanı sunucusunu seçin. 

2. Sunucu kenar çubuğunda, **Ayarlar**' ın altında, **çoğaltma**' yı seçin.

3. **Çoğaltma ekle**' yi seçin.

   ![Çoğaltma ekleme](./media/howto-read-replicas-portal/add-replica.png)

4. Okuma çoğaltması için bir ad girin. 

    ![Çoğaltmayı adlandırın](./media/howto-read-replicas-portal/name-replica.png)

5. Çoğaltma için bir konum seçin. Varsayılan konum, ana sunucu ile aynıdır.

    ![Konum seçin](./media/howto-read-replicas-portal/location-replica.png)

   > [!NOTE]
   > İçinde bir çoğaltma oluşturabileceğiniz bölgeler hakkında daha fazla bilgi edinmek için [çoğaltma kavramlarını oku makalesini](concepts-read-replicas.md)ziyaret edin. 

6. Çoğaltmanın oluşturulmasını onaylamak için **Tamam ' ı** seçin.

Bir çoğaltma, ana öğe ile aynı işlem ve depolama ayarları kullanılarak oluşturulur. Bir çoğaltma oluşturulduktan sonra, birden fazla ayar ana sunucudan bağımsız olarak değiştirilebilir: işlem oluşturma, sanal çekirdek, depolama ve yedekleme saklama süresi. Fiyatlandırma Katmanı, temel katmandan veya dışında bağımsız olarak da değiştirilebilir.

> [!IMPORTANT]
> Ana sunucu ayarı yeni bir değere güncellenmesinden önce, çoğaltma ayarını eşit veya daha büyük bir değere güncelleştirin. Bu eylem, çoğaltmanın ana üzerinde yapılan değişikliklerle devam etmesine yardımcı olur.

Okuma çoğaltması oluşturulduktan sonra **çoğaltma** penceresinden görüntülenebilir:

![Çoğaltma penceresinde yeni çoğaltmayı görüntüleme](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Çoğaltmayı durdur
Bir ana sunucu ve bir okuma çoğaltması arasındaki çoğaltmayı durdurabilirsiniz.

> [!IMPORTANT]
> Bir ana sunucu ve bir okuma çoğaltması için çoğaltmayı durdurduktan sonra geri alınamaz. Okuma çoğaltması, hem okuma hem de yazma işlemlerini destekleyen tek başına bir sunucu haline gelir. Tek başına sunucu tekrar bir çoğaltmaya yapılamaz.

Azure portal bir ana sunucu ile okuma çoğaltması arasında çoğaltmayı durdurmak için şu adımları izleyin:

1. Azure portal, PostgreSQL için ana Azure veritabanı sunucunuzu seçin.

2. Sunucu menüsünde, **Ayarlar**' ın altında, **çoğaltma**' yı seçin.

3. Çoğaltmanın durdurulacağı çoğaltma sunucusunu seçin.

   ![Çoğaltmayı seçin](./media/howto-read-replicas-portal/select-replica.png)
 
4. **Çoğaltmayı durdur**' u seçin.

   ![Çoğaltmayı Durdur ' u seçin](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. Çoğaltmayı durdurmak için **Tamam ' ı** seçin.

   ![Çoğaltmayı durdurmayı Onayla](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Bir ana sunucu silme
Ana Sunucuyu silmek için, bir PostgreSQL sunucusu için tek başına Azure veritabanını silmek üzere aynı adımları kullanın. 

> [!IMPORTANT]
> Bir ana sunucuyu sildiğinizde, tüm okuma çoğaltmalarına çoğaltma durdurulur. Okuma çoğaltmaları artık hem okuma hem de yazma işlemlerini destekleyen tek başına sunucular haline gelir.

Azure portal bir sunucuyu silmek için şu adımları izleyin:

1. Azure portal, PostgreSQL için ana Azure veritabanı sunucunuzu seçin.

2. Sunucunun **genel bakış** sayfasını açın. **Sil**’i seçin.

   ![Sunucu genel bakış sayfasında ana sunucuyu silmek için seçin](./media/howto-read-replicas-portal/delete-server.png)
 
3. Silinecek ana sunucunun adını girin. Ana sunucuyu silmeyi onaylamak için **Sil** ' i seçin.

   ![Ana sunucuyu Silmeyi Onayla](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Bir çoğaltmayı silme
Bir ana sunucuyu silme işlemine benzer bir okuma çoğaltmasını silebilirsiniz.

- Azure portal, okuma çoğaltması için **genel bakış** sayfasını açın. **Sil**’i seçin.

   ![Çoğaltmaya genel bakış sayfasında, çoğaltmayı silmek için seçin](./media/howto-read-replicas-portal/delete-replica.png)
 
Ayrıca, aşağıdaki adımları izleyerek **çoğaltma** penceresinde okuma çoğaltmasını silebilirsiniz:

1. Azure portal, PostgreSQL için ana Azure veritabanı sunucunuzu seçin.

2. Sunucu menüsünde, **Ayarlar**' ın altında, **çoğaltma**' yı seçin.

3. Silinecek okuma çoğaltmasını seçin.

   ![Silinecek çoğaltmayı seçin](./media/howto-read-replicas-portal/select-replica.png)
 
4. **Çoğaltmayı Sil**' i seçin.

   ![Çoğaltmayı Sil ' i seçin](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. Silinecek çoğaltmanın adını girin. Çoğaltmanın silinmesini onaylamak için **Sil** ' i seçin.

   ![Te çoğaltmasını Silmeyi Onayla](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Bir çoğaltmayı izleme
Okuma çoğaltmalarını izlemek için iki ölçüm mevcuttur.

### <a name="max-lag-across-replicas-metric"></a>Çoğaltmalar arasındaki en fazla gecikme ölçümü
**Çoğaltmalar genelinde en fazla gecikme** ölçümü, ana sunucu ile en fazla çoğaltma çoğaltması arasındaki gecikme sayısını gösterir. 

1.  Azure portal, PostgreSQL için ana Azure veritabanı sunucusu ' nu seçin.

2.  **Ölçümler**’i seçin. **Ölçümler** penceresinde **çoğaltmalar genelinde en fazla gecikme**' yı seçin.

    ![Çoğaltmalar genelinde en fazla gecikme 'yi izleme](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Toplamadır **,** **en fazla**' yı seçin.


### <a name="replica-lag-metric"></a>Çoğaltma gecikmesi ölçümü
**Çoğaltma gecikmesi** ölçümü, bir çoğaltmada son yeniden yürütülen işlemden bu yana geçen süreyi gösterir. Ana kuruluşunuzda gerçekleşen işlem yoksa, ölçüm bu gecikme süresini yansıtır.

1. Azure portal, PostgreSQL için Azure veritabanı okuma çoğaltması ' nı seçin.

2. **Ölçümler**’i seçin. **Ölçümler** penceresinde **çoğaltma gecikmesi**' nı seçin.

   ![Çoğaltma gecikmesini izleme](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. Toplamadır **,** **en fazla**' yı seçin. 
 
## <a name="next-steps"></a>Sonraki adımlar
* [PostgreSQL Için Azure veritabanı 'nda okuma çoğaltmaları](concepts-read-replicas.md)hakkında daha fazla bilgi edinin.
* [Azure CLI ve REST API okuma çoğaltmaları oluşturma ve yönetme](howto-read-replicas-cli.md)hakkında bilgi edinin.
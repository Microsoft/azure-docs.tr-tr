---
title: PostgreSQL için Azure veritabanı 'nda bir sunucuyu geri yükleme-tek sunucu
description: Bu makalede, Azure portal kullanarak PostgreSQL için Azure veritabanı 'na yönelik bir sunucunun nasıl geri yükleneceği açıklanmaktadır.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 22522a3f577e8d0533f7c8926de12bd464cc2d92
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965790"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Azure portal kullanarak, PostgreSQL için Azure veritabanı 'nda bir sunucuyu yedekleme ve geri yükleme-tek sunucu

## <a name="backup-happens-automatically"></a>Yedekleme otomatik olarak gerçekleşir
PostgreSQL için Azure veritabanı sunucuları, geri yükleme özelliklerini etkinleştirmek üzere düzenli aralıklarla yedeklenir. Bu özelliği kullanarak, sunucuyu ve tüm veritabanlarını yeni bir sunucuda daha önceki bir zaman noktasına geri yükleyebilirsiniz.

## <a name="set-backup-configuration"></a>Yedekleme yapılandırmasını ayarla

Sunucu oluşturma sırasında, **fiyatlandırma katmanı** penceresinde yerel olarak yedekli yedeklemeler veya coğrafi olarak yedekli yedeklemeler için sunucunuzu yapılandırma arasında seçim yaparsınız.

> [!NOTE]
> Bir sunucu oluşturulduktan sonra, coğrafi olarak yedekli ve yerel olarak yedekli olan artıklık türü değiştirilemez.
>

Azure portal aracılığıyla bir sunucu oluştururken, **fiyatlandırma katmanı** penceresi sunucunuz Için **yerel olarak yedekli** veya **coğrafi olarak yedekli** yedeklemeleri seçtiğiniz yerdir. Bu pencere Ayrıca, **yedekleme bekletme dönemini** (gün sayısı cinsinden) sunucu yedeklemelerinin ne kadar süreyle depolandığını seçtiğiniz yerdir.

   ![Fiyatlandırma Katmanı-yedek yedeklilik seçin](./media/howto-restore-server-portal/pricing-tier.png)

Oluşturma sırasında bu değerleri ayarlama hakkında daha fazla bilgi için bkz. [PostgreSQL Için Azure veritabanı sunucu hızlı başlangıç](quickstart-create-server-database-portal.md).

Bir sunucunun yedekleme saklama süresi aşağıdaki adımlarla değiştirilebilir:
1. [Azure portal](https://portal.azure.com/) oturum açın.
2. PostgreSQL için Azure Veritabanı sunucunuzu seçin. Bu eylem **genel bakış** sayfasını açar.
3. Menüde, **Ayarlar**' ın altında **fiyatlandırma katmanı** ' nı seçin. Kaydırıcıyı kullanarak **yedekleme saklama süresini** 7 ila 35 gün süreyle tercihlerinize göre değiştirebilirsiniz.
Aşağıdaki ekran görüntüsünde 34 güne yükselmiştir.
![yedekleme bekletme süresi arttı](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Değişikliği onaylamak için **Tamam** ' ı tıklatın.

Yedekleme bekletme süresi, kullanılabilir yedeklemeler temel aldığı için zaman içinde bir nokta geri yüklemesi alma süresinin ne kadar geri alınacağını yönetir. Bir noktadan sonra geri yükleme aşağıdaki bölümde daha ayrıntılı olarak açıklanmıştır. 

## <a name="point-in-time-restore"></a>Belirli bir noktaya geri yükleme
PostgreSQL için Azure veritabanı, sunucuyu bir zaman noktasına geri yüklemenize ve sunucunun yeni bir kopyasına geri yüklemenize olanak tanır. Verilerinizi kurtarmak için bu yeni sunucuyu kullanabilir veya istemci uygulamalarınızın bu yeni sunucuyu işaret edebilir.

Örneğin, bir tablo bugün öğleden sonra yanlışlıkla bırakıldıysa, daha önce geçen saate geri yükleyebilir ve eksik tablo ve verileri sunucunun bu yeni kopyasından elde edebilirsiniz. Zaman içinde geri yükleme, veritabanı düzeyinde değil, sunucu düzeyindedir.

Aşağıdaki adımlar örnek sunucuyu bir zaman noktasına geri yükler:
1. Azure portal, PostgreSQL için Azure veritabanı sunucunuzu seçin. 

2. Sunucunun **genel bakış** sayfasının araç çubuğunda **geri yükle**' yi seçin.

   ![PostgreSQL için Azure veritabanı-genel bakış-geri yükleme düğmesi](./media/howto-restore-server-portal/2-server.png)

3. Geri yükleme formunu gereken bilgilerle doldurun:

   ![PostgreSQL için Azure veritabanı-geri yükleme bilgileri](./media/howto-restore-server-portal/3-restore.png)
   - **Geri yükleme noktası**: geri yüklemek istediğiniz zaman noktasını seçin.
   - **Hedef sunucu**: yeni sunucu için bir ad sağlayın.
   - **Konum**: bölgeyi seçemezsiniz. Varsayılan olarak, kaynak sunucu ile aynıdır.
   - **Fiyatlandırma katmanı**: bir zaman içinde geri yükleme yaparken bu parametreleri değiştiremezsiniz. Kaynak sunucuyla aynıdır. 

4. Sunucuyu bir zaman noktasına geri yüklemek üzere geri yüklemek için **Tamam** ' a tıklayın. 

5. Geri yükleme tamamlandıktan sonra, verilerin beklendiği gibi geri yüklendiğini doğrulamak için oluşturulan yeni sunucuyu bulun.

Noktadan noktaya geri yükleme tarafından oluşturulan yeni sunucu, mevcut sunucu için geçerli olan Sunucu Yöneticisi oturum açma adı ve parolası ile aynı zamanda, Parolayı yeni sunucunun **genel bakış** sayfasından değiştirebilirsiniz.

Geri yükleme sırasında oluşturulan yeni sunucu, özgün sunucuda bulunan güvenlik duvarı kurallarına veya VNet hizmeti uç noktalarına sahip değildir. Bu kuralların bu yeni sunucu için ayrı olarak ayarlanması gerekir.


## <a name="geo-restore"></a>Coğrafi geri yükleme

Sunucunuzu coğrafi olarak yedekli yedeklemeler için yapılandırdıysanız, var olan sunucunun yedeklemesinden yeni bir sunucu oluşturulabilir. Bu yeni sunucu, PostgreSQL için Azure veritabanı 'nın kullanılabildiği herhangi bir bölgede oluşturulabilir.  

1. Portalın sol üst köşesinde bulunan **Kaynak oluştur** düğmesini (+) seçin. **Veritabanları** > **PostgreSQL için Azure Veritabanı**'nı seçin.

   !["PostgreSQL için Azure Veritabanı" seçeneği](./media/howto-restore-server-portal/1-navigate-to-postgres.png)

2. Formun **Kaynak Seç** açılan menüsünde **Yedekle**' yi seçin. Bu eylem, coğrafi olarak yedekli yedeklemelerin etkinleştirildiği sunucuların listesini yükler. Yeni sunucunuzun kaynağı olacak şekilde bu Yedeklerden birini seçin.
   ![kaynak seçin: coğrafi olarak yedekli yedeklemelerin yedeklenmesi ve listesi](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > Sunucu ilk oluşturulduğunda coğrafi geri yükleme için hemen kullanılamayabilir. Gerekli meta verilerin doldurulması birkaç saat sürebilir.
   >

3. Formun geri kalanını tercihlerinize göre doldurun. Herhangi bir **konum**seçebilirsiniz. Konumu seçtikten sonra **fiyatlandırma katmanını**seçebilirsiniz. Varsayılan olarak, geri yüklemekte olduğunuz mevcut sunucunun parametreleri görüntülenir. Bu ayarları devralması için herhangi bir değişiklik yapmadan **Tamam** ' a tıklayabilirsiniz. Ya da **Işlem üretimini** (seçtiğiniz bölgede varsa), **sanal çekirdek**sayısı, **yedekleme saklama süresi**ve **yedekleme artıklığı seçeneğinin**sayısını değiştirebilirsiniz. Geri yükleme sırasında **fiyatlandırma katmanını** (temel, genel amaçlı veya bellek için iyileştirilmiş) veya **depolama** boyutunu değiştirme desteklenmiyor.


Coğrafi geri yükleme tarafından oluşturulan yeni sunucu, geri yükleme başlatıldığı sırada mevcut sunucu için geçerli olan Sunucu Yöneticisi oturum açma adı ve parolaya sahiptir. Parola, yeni sunucunun **genel bakış** sayfasından değiştirilebilir.

Geri yükleme sırasında oluşturulan yeni sunucu, özgün sunucuda bulunan güvenlik duvarı kurallarına veya VNet hizmeti uç noktalarına sahip değildir. Bu kuralların bu yeni sunucu için ayrı olarak ayarlanması gerekir.


## <a name="next-steps"></a>Sonraki adımlar
- Hizmetin [yedeklemeleri](concepts-backup.md)hakkında daha fazla bilgi edinin.
- [İş sürekliliği](concepts-business-continuity.md) seçenekleri hakkında daha fazla bilgi edinin.

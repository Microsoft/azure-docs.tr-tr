---
title: MariaDB için Azure veritabanı fiyatlandırma katmanları
description: Bilgi işlem oluşturmaları, depolama türleri, depolama boyutu, sanal çekirdek, bellek ve yedekleme bekletme dönemleri dahil olmak üzere MariaDB için Azure veritabanı için çeşitli fiyatlandırma katmanları hakkında bilgi edinin.
author: jan-eng
ms.author: janeng
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: ca9552ee60237158bd570ede4a13dbc349b6d1d1
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973573"
---
# <a name="azure-database-for-mariadb-pricing-tiers"></a>MariaDB için Azure veritabanı fiyatlandırma katmanları

MariaDB sunucusu için Azure veritabanı oluşturmak için üç farklı fiyatlandırma katmanından birini kullanabilirsiniz: temel, Genel Amaçlı ve bellek için Iyileştirilmiş. Fiyatlandırma katmanları, sağlanan sanal çekirdekler, sanal çekirdek başına bellek ve verileri depolamak için kullanılan depolama teknolojisi miktarına göre farklılaştırılabilir. Tüm kaynaklar MariaDB sunucu düzeyinde sağlanır. Sunucuda bir veya daha fazla veritabanı olabilir.

|    | **Temel** | **Genel Amaçlı** | **Bellek için Iyileştirilmiş** |
|:---|:----------|:--------------------|:---------------------|
| İşlem oluşturma | 5\. Nesil |5\. Nesil | 5\. Nesil |
| Sanal çekirdek | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Sanal çekirdek başına bellek | 2 GB | 5 GB | 10 GB |
| Depolama boyutu | 5 GB ila 1 TB | 5 GB ila 4 TB | 5 GB ila 4 TB |
| Depolama türü | Azure Standart depolama | Azure Premium Depolama | Azure Premium Depolama |
| Veritabanı yedekleme saklama süresi | 7-35 gün | 7-35 gün | 7-35 gün |

Fiyatlandırma katmanını seçmek için, başlangıç noktası olarak aşağıdaki tabloyu kullanın.

| Fiyatlandırma katmanı | Hedef iş yükleri |
|:-------------|:-----------------|
| Temel | Hafif işlem ve g/ç performansı gerektiren iş yükleri. Örnek olarak, geliştirme veya test için kullanılan sunucular veya küçük ölçekli sık kullanılmayan uygulamalar bulunur. |
| Genel Amaçlı | Ölçeklenebilir g/ç aktarım hızı ile dengeli işlem ve bellek gerektiren iş yüklerinin çoğu. Web ve mobil uygulamaları ve diğer kurumsal uygulamaları barındırmak için sunucu örnekleri içerir.|
| Bellek için İyileştirilmiş | Daha hızlı işlem işleme ve daha yüksek eşzamanlılık için bellek içi performans gerektiren yüksek performanslı veritabanı iş yükleri. Örnek olarak gerçek zamanlı verilerin işlenmesine yönelik sunucular ve yüksek performanslı işlem ya da analitik uygulamalar bulunur.|

Sunucu oluşturduktan sonra, sanal çekirdek sayısı ve Fiyatlandırma Katmanı (temel dışında), saniyeler içinde değişebilir veya azaltılabilir. Ayrıca, uygulama kapalı kalma süresi olmadan depolama miktarını yukarı ve yedekleme bekletme süresini yukarı veya aşağı olarak ayarlayabilirsiniz. Sunucu oluşturulduktan sonra yedekleme depolama türünü değiştiremezsiniz. Daha fazla bilgi için bkz. [kaynakları ölçeklendirme](#scale-resources) bölümü.

## <a name="compute-generations-and-vcores"></a>İşlem nesilleri ve sanal çekirdekler

İşlem kaynakları, temel alınan donanımın mantıksal CPU 'sunu temsil eden sanal çekirdekler olarak sağlanır. Gen 5 mantıksal CPU 'Lar Intel E5-2673 v4 (çok Iyi) 2,3 GHz işlemcileri temel alır.

## <a name="storage"></a>Depolama

Sağladığınız depolama alanı, MariaDB sunucusu için Azure veritabanınız tarafından kullanılabilen depolama kapasitesi miktarıdır. Depolama alanı veritabanı dosyaları, geçici dosyalar, işlem günlükleri ve MariaDB sunucu günlükleri için kullanılır. Sağladığınız toplam depolama miktarı, sunucunuz için kullanılabilir olan g/ç kapasitesini de tanımlar.

|    | **Temel** | **Genel Amaçlı** | **Bellek için Iyileştirilmiş** |
|:---|:----------|:--------------------|:---------------------|
| Depolama türü | Azure Standart depolama | Azure Premium Depolama | Azure Premium Depolama |
| Depolama boyutu | 5 GB ila 1 TB | 5 GB ila 4 TB | 5 GB ila 4 TB |
| Depolama artış boyutu | 1 GB | 1 GB | 1 GB |
| IOPS | Değişken |3 ıOPS/GB<br/>Minimum 100 ıOPS<br/>Maksimum 6000 ıOPS | 3 ıOPS/GB<br/>Minimum 100 ıOPS<br/>Maksimum 6000 ıOPS |

Sunucu oluşturma sırasında ve sonrasında ek depolama kapasitesi ekleyebilir ve sistemin iş yükünüzün depolama tüketimine göre depolamayı otomatik olarak büyümesine izin verebilirsiniz.

>[!NOTE]
> Depolama yalnızca yukarı ölçeklenebilen, aşağı doğru değil.

Temel katman, ıOPS garantisi sağlamıyor. Genel Amaçlı ve bellek için Iyileştirilmiş fiyatlandırma katmanlarında ıOPS, sağlanan depolama boyutuyla 3:1 oranında ölçeklendirilir.

G/ç tüketiminizi Azure portal veya Azure CLı komutlarını kullanarak izleyebilirsiniz. İzlenecek ilgili ölçümler [depolama sınırı, depolama yüzdesi, kullanılan depolama alanı ve yüzde GÇ](concepts-monitoring.md)' dır.

### <a name="reaching-the-storage-limit"></a>Depolama sınırına ulaşıyor

Sağlanan depolama alanı 100 GB'tan az olan sunucular, boş depolama alanı 512 MB'ın altına veya sağlanan depolama boyutunun %5'ine düştüğünde salt okunur olarak işaretlenir. Sağlanan depolama alanı 100 GB'tan fazla olan sunucular, boş depolama alanı 5 GB'ın altına düştüğünde salt okunur olarak işaretlenir.

Örneğin, 110 GB depolama alanı sağladıysanız ve gerçek kullanım 105 GB 'den fazla olursa sunucu salt okunurdur olarak işaretlenir. Alternatif olarak, 5 GB depolama alanı sağladıysanız, ücretsiz depolama 256 MB 'tan az kaldığında sunucu salt okunurdur olarak işaretlenir.

Hizmet sunucuyu salt okunur duruma getirdiğinde tüm yeni yazma işlemi istekleri engellenir ve var olan etkin işlemler yürütülmeye devam eder. Sunucu salt okunur olarak ayarlandığında sonraki tüm yazma girişimleri ve işlemler başarısız olur. Okuma sorguları kesintisiz olarak çalışmaya devam eder. Sağlanan depolama alanını artırdıktan sonra sunucu yazma işlemlerini kabul etmeye hazır hale gelir.

Depolama otomatik büyümesini etkinleştirmenizi veya sunucu depoağınızın eşiğe yaklaştığı durumlarda sizi bilgilendirmek üzere bir uyarı ayarlamanız önerilir, böylece salt okunurdur. Daha fazla bilgi için bkz. [Uyarı ayarlama](howto-alert-metric.md)hakkında belge.

### <a name="storage-auto-grow"></a>Depolama otomatik büyüme

Depolama otomatik büyüme, sunucunuzun depolama dışı ve Salt okunabilir hale gelmesine engel olur. Depolama otomatik büyüme etkinleştirilirse, depolama alanı, iş yükünü etkilemeden otomatik olarak büyür. 100 GB 'den az kullanılabilir depolama alanı olan sunucularda, boş depolama sağlanan depolamanın% 10 ' u altındaysa, sağlanan depolama boyutu 5 GB ile artar. 100 GB 'tan fazla kullanılabilir depolama alanı olan sunucularda, boş depolama alanı sağlanan depolama boyutunun% 10 ' u altındaysa, sağlanan depolama boyutu% 5 oranında artar. Yukarıda belirtilen en fazla depolama sınırı geçerlidir.

Örneğin, 1000 GB depolama alanı sağladıysanız ve gerçek kullanım 900 GB 'den fazla olursa, sunucu depolama boyutu 1050 GB 'a yükseltilir. Alternatif olarak, 10 GB depolama alanı sağladıysanız, 1 GB 'tan az depolama alanı boş olduğunda depolama boyutu 15 GB 'a artar.

Depolamanın yalnızca yukarı ölçeklenebileceğinden, aşağı doğru ölçeklenemediğini unutmayın.

## <a name="backup"></a>Backup

Hizmet, sunucunuzun yedeklerini otomatik olarak alır. Yedeklemeler için en düşük saklama süresi yedi gündür. 35 güne kadar bir bekletme dönemi belirleyebilirsiniz. Bekletme, sunucunun kullanım ömrü boyunca herhangi bir noktada ayarlanabilir. Yerel olarak yedekli ve coğrafi olarak yedekli yedeklemeler arasından seçim yapabilirsiniz. Coğrafi olarak yedekli yedeklemeler, sunucunuzun oluşturulduğu bölgenin [coğrafi eşlenmiş bölgesinde](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) da depolanır. Bu artıklık, bir olağanüstü durum durumunda bir koruma düzeyi sağlar. Ayrıca, aynı zamanda sunucunuzu, coğrafi olarak yedekli yedeklemelerle kullanılabilir olan diğer Azure bölgelerine geri yükleme imkanına sahip olursunuz. Sunucu oluşturulduktan sonra iki yedekleme depolama seçeneği arasında değişiklik yapmak mümkün değildir.

## <a name="scale-resources"></a>Kaynakları ölçeklendirme

Sunucunuzu oluşturduktan sonra, sanal çekirdekleri, fiyatlandırma katmanını (temel ve dışı), depolama miktarını ve yedekleme saklama süresini bağımsız olarak değiştirebilirsiniz. Sunucu oluşturulduktan sonra yedekleme depolama türünü değiştiremezsiniz. Sanal çekirdek sayısı yukarı veya aşağı ölçeklendirilebilir. Yedekleme saklama süresi 7 ile 35 gün arasında ölçeklendirilebilir veya kapatılabilir. Depolama boyutu yalnızca artırılabilir. Kaynakların ölçeklendirilmesi portal veya Azure CLı aracılığıyla yapılabilir. 

<!--For an example of scaling by using Azure CLI, see [Monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->

Vçekirdeklerinin sayısını veya fiyatlandırma katmanını değiştirdiğinizde, yeni işlem tahsisatına göre özgün sunucunun bir kopyası oluşturulur. Yeni sunucu çalışır duruma geçtikten sonra, bağlantılar yeni sunucuya geçer. Sistem yeni sunucuya geçerken yeni bağlantı kurulamaz ve tüm işlenmemiş işlemler geri alınır. Bu süre değişir, ancak çoğu durumda bir dakikadan daha kısadır.

Depolamanın ölçeklendirilmesi ve yedekleme saklama süresinin değiştirilmesi, gerçek çevrimiçi işlemlerdir. Kapalı kalma süresi yoktur ve uygulamanız etkilenmez. Sağlanan depolamanın boyutuyla ıOPS ölçeği olarak, depolama alanını ölçeklendirerek sunucunuz için kullanılabilir ıOPS 'yi artırabilirsiniz.

## <a name="pricing"></a>Fiyatlandırma

En güncel fiyatlandırma bilgileri için bkz. hizmet [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/mariadb/). İstediğiniz yapılandırmanın maliyetini görmek için [Azure Portal](https://portal.azure.com/#create/Microsoft.MariaDBServer) , seçtiğiniz seçeneklere göre **fiyatlandırma katmanı** sekmesindeki aylık maliyeti gösterir. Azure aboneliğiniz yoksa, tahmini bir fiyat almak için Azure Fiyatlandırma hesaplayıcısı ' nı kullanabilirsiniz. [Azure Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator/) Web sitesinde, **öğe Ekle**' yi seçin, **veritabanları** kategorisini genişletin ve seçenekleri özelleştirmek Için **MariaDB için Azure veritabanı** ' nı seçin.

## <a name="next-steps"></a>Sonraki adımlar
- [Hizmet sınırlamaları](concepts-limits.md)hakkında bilgi edinin.
- [Azure Portal bir MariaDB sunucusu oluşturmayı](quickstart-create-mariadb-server-database-using-azure-portal.md)öğrenin.

<!--
- Learn how to [monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->

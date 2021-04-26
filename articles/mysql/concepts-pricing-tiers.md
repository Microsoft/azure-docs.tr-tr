---
title: Fiyatlandırma katmanları - MySQL için Azure Veritabanı
description: Bilgi işlem oluşturmaları, depolama türleri, depolama boyutu, sanal çekirdek, bellek ve yedekleme bekletme dönemleri dahil olmak üzere MySQL için Azure veritabanı için çeşitli fiyatlandırma katmanları hakkında bilgi edinin.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: be7f15b5221be8b3acb7f64c4435e40f40f21f8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101720928"
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>MySQL için Azure veritabanı fiyatlandırma katmanları

Bir MySQL için Azure veritabanı sunucusu oluşturmak için üç farklı fiyatlandırma katmanlarından birini kullanabilirsiniz: temel, Genel Amaçlı ve bellek için Iyileştirilmiş. Fiyatlandırma katmanları, sağlanan sanal çekirdekler, sanal çekirdek başına bellek ve verileri depolamak için kullanılan depolama teknolojisi miktarına göre farklılaştırılabilir. Tüm kaynaklar MySQL sunucu düzeyinde sağlanır. Sunucuda bir veya daha fazla veritabanı olabilir.

| Öznitelik   | **Temel** | **Genel Amaçlı** | **Bellek için Iyileştirilmiş** |
|:---|:----------|:--------------------|:---------------------|
| İşlem oluşturma | Gen 4, Gen 5 | Gen 4, Gen 5 | 5. Nesil |
| Sanal çekirdek | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Sanal çekirdek başına bellek | 2 GB | 5 GB | 10 GB |
| Depolama boyutu | 5 GB ila 1 TB | 5 GB ila 16 TB | 5 GB ila 16 TB |
| Veritabanı yedekleme saklama süresi | 7-35 gün | 7-35 gün | 7-35 gün |

Fiyatlandırma katmanını seçmek için, başlangıç noktası olarak aşağıdaki tabloyu kullanın.

| Fiyatlandırma katmanı | Hedef iş yükleri |
|:-------------|:-----------------|
| Temel | Hafif işlem ve g/ç performansı gerektiren iş yükleri. Örnek olarak, geliştirme veya test için kullanılan sunucular veya küçük ölçekli sık kullanılmayan uygulamalar bulunur. |
| Genel Amaçlı | Ölçeklenebilir g/ç aktarım hızı ile dengeli işlem ve bellek gerektiren iş yüklerinin çoğu. Örnek olarak web uygulamalarını, mobil uygulamaları ve diğer kurumsal uygulamaları barındıran sunucular verilebilir.|
| Bellek İçin İyileştirilmiş | Daha hızlı işlem işleme ve daha yüksek eşzamanlılık için bellek içi performans gerektiren yüksek performanslı veritabanı iş yükleri. Örnek olarak gerçek zamanlı verileri işleyen ve yüksek performanslı işlem tabanlı ya da analiz uygulamalarının sunucuları verilebilir.|

Bir sunucu oluşturduktan sonra, sanal çekirdek sayısı, donanım oluşturma ve Fiyatlandırma Katmanı (temel dışında), saniyeler içinde değişebilir veya azaltılabilir. Ayrıca, uygulama kapalı kalma süresi olmadan depolama miktarını yukarı ve yedekleme bekletme süresini yukarı veya aşağı olarak ayarlayabilirsiniz. Sunucu oluşturulduktan sonra yedekleme depolama türünü değiştiremezsiniz. Daha fazla bilgi için bkz. [kaynakları ölçeklendirme](#scale-resources) bölümü.

## <a name="compute-generations-and-vcores"></a>İşlem nesilleri ve sanal çekirdekler

İşlem kaynakları, temel alınan donanımın mantıksal CPU 'sunu temsil eden sanal çekirdekler olarak sağlanır. Çin Doğu 1, Çin Kuzey 1, US DoD Orta ve US DoD Doğu Intel E5-2673 v3 (Haswell) 2,4 GHz işlemcileri temel alan gen 4 mantıksal CPU 'Ları kullanır. Tüm diğer bölgeler, Intel E5-2673 v4 (çok Iyi) 2,3 GHz işlemcileri temel alan gen 5 mantıksal CPU kullanır.

## <a name="storage"></a>Depolama

Sağladığınız depolama alanı, MySQL için Azure veritabanı sunucunuz için kullanılabilen depolama kapasitesi miktarıdır. Depolama alanı veritabanı dosyaları, geçici dosyalar, işlem günlükleri ve MySQL Server günlükleri için kullanılır. Sağladığınız toplam depolama miktarı, sunucunuz için kullanılabilir olan g/ç kapasitesini de tanımlar.

| Depolama özniteliği   | Temel | Genel amaçlı | Bellek için iyileştirilmiş |
|:---|:----------|:--------------------|:---------------------|
| Depolama türü | Temel depolama | Genel Amaçlı depolama | Genel Amaçlı depolama |
| Depolama boyutu | 5 GB ila 1 TB | 5 GB ila 16 TB | 5 GB ila 16 TB |
| Depolama artış boyutu | 1 GB | 1 GB | 1 GB |
| IOPS | Değişken |3 ıOPS/GB<br/>Minimum 100 ıOPS<br/>Maksimum 20.000 ıOPS | 3 ıOPS/GB<br/>Minimum 100 ıOPS<br/>Maksimum 20.000 ıOPS |

> [!NOTE]
> 16 TB 'a kadar depolama alanı ve 20.000 ıOPS, şu bölgelerde desteklenir: Doğu ABD, Doğu ABD 2, Orta ABD, Brezilya Güney, Batı ABD, Orta Kuzey ABD, Orta Güney ABD, Kuzey Avrupa, Batı Avrupa, UK Güney, UK Batı, Güneydoğu Asya, Doğu Asya, Japonya Doğu, Japonya Batı, Kore Orta, Kore Güney, Avustralya Doğu, Avustralya Güney Doğu, Batı ABD 2, Orta Batı ABD , Kanada Doğu ve Kanada Orta.
>
> Tüm diğer bölgeler 4TB 'a kadar depolamayı ve 6000 ıOPS 'yi destekler.
>

Sunucu oluşturma sırasında ve sonrasında ek depolama kapasitesi ekleyebilir ve sistemin iş yükünüzün depolama tüketimine göre depolamayı otomatik olarak büyümesine izin verebilirsiniz. 

>[!NOTE]
> Depolama yalnızca yukarı ölçeklenebilen, aşağı doğru değil.

Temel katman, ıOPS garantisi sağlamıyor. Genel Amaçlı ve bellek için Iyileştirilmiş fiyatlandırma katmanlarında ıOPS, sağlanan depolama boyutuyla 3:1 oranında ölçeklendirilir.

G/ç tüketiminizi Azure portal veya Azure CLı komutlarını kullanarak izleyebilirsiniz. İzlenecek ilgili ölçümler [depolama sınırı, depolama yüzdesi, kullanılan depolama alanı ve yüzde GÇ](concepts-monitoring.md)' dır.

### <a name="reaching-the-storage-limit"></a>Depolama sınırına ulaşma

Sağlanan depolama alanı 100 GB veya daha az olan sunucular, boş depolama alanı sağlanan depolama boyutunun %5'inin altında düştüğünde salt okunur olarak işaretlenir. Sağlanan depolama alanı 100 GB'tan fazla olan sunucular, boş depolama alanı 5 GB'ın altına düştüğünde salt okunur olarak işaretlenir.

Örneğin, 110 GB depolama alanı sağladıysanız ve gerçek kullanım 105 GB 'den fazla olursa sunucu salt okunurdur olarak işaretlenir. Alternatif olarak, 5 GB depolama alanı sağladıysanız, ücretsiz depolama 256 MB 'tan az kaldığında sunucu salt okunurdur olarak işaretlenir.

Hizmet sunucuyu salt okunur duruma getirdiğinde tüm yeni yazma işlemi istekleri engellenir ve var olan etkin işlemler yürütülmeye devam eder. Sunucu salt okunur olarak ayarlandığında sonraki tüm yazma girişimleri ve işlemler başarısız olur. Okuma sorguları kesintisiz olarak çalışmaya devam eder. Sağlanan depolama alanını artırdıktan sonra sunucu yazma işlemlerini kabul etmeye hazır hale gelir.

Depolama otomatik büyümesini etkinleştirmenizi veya sunucu depoağınızın eşiğe yaklaştığı durumlarda sizi bilgilendirmek üzere bir uyarı ayarlamanız önerilir, böylece salt okunurdur. Daha fazla bilgi için bkz. [Uyarı ayarlama](howto-alert-on-metric.md)hakkında belge.

### <a name="storage-auto-grow"></a>Depolama otomatik büyüme

Depolama otomatik büyüme, sunucunuzun depolama dışı ve Salt okunabilir hale gelmesine engel olur. Depolama otomatik büyüme etkinleştirilirse, depolama alanı, iş yükünü etkilemeden otomatik olarak büyür. 100 GB 'tan daha az depolama alanı sağlanmış olan sunucular için, boş depolama sağlanan depolamanın %10 ' u altındaysa, sağlanan depolama boyutu 5 GB ile artar. 100 GB 'tan fazla kullanılabilir depolama alanı bulunan sunucular için, boş depolama alanı sağlanan depolama boyutunun 10 GB 'ın altında olduğunda sağlanan depolama boyutu %5 oranında artar. Yukarıda belirtilen en fazla depolama sınırı geçerlidir.

Örneğin, 1000 GB depolama alanı sağladıysanız ve gerçek kullanım 990 GB 'den fazla olursa, sunucu depolama boyutu 1050 GB 'a yükseltilir. Alternatif olarak, 10 GB depolama alanı sağladıysanız, 1 GB 'tan az depolama alanı boş olduğunda depolama boyutu 15 GB 'a artar.

Depolamanın yalnızca yukarı ölçeklenebileceğinden, aşağı doğru ölçeklenemediğini unutmayın.

## <a name="backup-storage"></a>Yedekleme depolama alanı 

MySQL için Azure veritabanı, sağlanan sunucu depolama alanınızı ek bir ücret ödemeden yedekleme depolama alanı olarak %100 ' e kadar sağlar. Bu miktardan fazla süre içinde kullandığınız tüm yedekleme depolama alanı aylık GB cinsinden ücretlendirilir. Örneğin, 250 GB depolama alanı olan bir sunucu sağlarsanız, sunucu yedeklemeleri için ücretsiz olarak 250 GB ek depolama alanı kullanılabilir. 250 GB 'tan fazla olan yedeklemeler için depolama, [fiyatlandırma modeline](https://azure.microsoft.com/pricing/details/mysql/)göre ücretlendirilir. Yedekleme depolama kullanımını etkileyen faktörleri anlamak, yedekleme depolama maliyetini izlemek ve denetlemek için [yedekleme belgelerine](concepts-backup.md)başvurabilirsiniz.

## <a name="scale-resources"></a>Kaynakları ölçeklendirme

Sunucunuzu oluşturduktan sonra, sanal çekirdekleri, donanım oluşturmayı, fiyatlandırma katmanını (temel ve dışı), depolama miktarını ve yedekleme saklama süresini bağımsız olarak değiştirebilirsiniz. Sunucu oluşturulduktan sonra yedekleme depolama türünü değiştiremezsiniz. Sanal çekirdek sayısı yukarı veya aşağı ölçeklendirilebilir. Yedekleme saklama süresi 7 ile 35 gün arasında ölçeklendirilebilir veya kapatılabilir. Depolama boyutu yalnızca artırılabilir. Kaynakların ölçeklendirilmesi portal veya Azure CLı aracılığıyla yapılabilir. Azure CLı kullanarak ölçeklendirmeyle ilgili bir örnek için bkz. [Azure CLI kullanarak MySQL Için Azure veritabanı sunucusunu izleme ve ölçeklendirme](scripts/sample-scale-server.md).

Sanal çekirdek sayısını, donanım oluşturmayı veya fiyatlandırma katmanını değiştirdiğinizde, yeni işlem ayırmasıyla orijinal sunucunun bir kopyası oluşturulur. Yeni sunucu çalışır duruma geçtikten sonra, bağlantılar yeni sunucuya geçer. Sistem yeni sunucuya geçerken yeni bağlantı kurulamaz ve tüm işlenmemiş işlemler geri alınır. Ölçeklendirme sırasında bu kesinti süresi 60-120 saniye içinde olabilir. Ölçeklendirme sırasında kapalı kalma süresi, veritabanı kurtarma zamanına bağımlıdır. Bu, ölçeklendirme işlemi sırasında sunucuda ağır işlem etkinliğiyse veritabanının daha uzun sürmesine neden olabilir. Yeniden başlatma zamanından daha uzun bir süre önlemek için, sunucuda düşük işlem temelli etkinlik dönemlerinde ölçeklendirme işlemleri gerçekleştirmeniz önerilir.

Depolamanın ölçeklendirilmesi ve yedekleme saklama süresinin değiştirilmesi, gerçek çevrimiçi işlemlerdir. Kapalı kalma süresi yoktur ve uygulamanız etkilenmez. Sağlanan depolamanın boyutuyla ıOPS ölçeği olarak, depolama alanını ölçeklendirerek sunucunuz için kullanılabilir ıOPS 'yi artırabilirsiniz.

## <a name="pricing"></a>Fiyatlandırma

En güncel fiyatlandırma bilgileri için bkz. hizmet [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/mysql/). İstediğiniz yapılandırmanın maliyetini görmek için [Azure Portal](https://portal.azure.com/#create/Microsoft.MySQLServer) , seçtiğiniz seçeneklere göre **fiyatlandırma katmanı** sekmesindeki aylık maliyeti gösterir. Azure aboneliğiniz yoksa, tahmini bir fiyat almak için Azure Fiyatlandırma hesaplayıcısı ' nı kullanabilirsiniz. [Azure Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator/) Web sitesinde, **öğe Ekle**' yi seçin, **veritabanları** kategorisini genişletin ve özellikleri özelleştirmek için **MySQL için Azure veritabanı** ' nı seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Portalda MySQL sunucusu oluşturmayı](howto-create-manage-server-portal.md)öğrenin.
- [Hizmet limitleri](concepts-limits.md)hakkında bilgi edinin.
- [Okuma çoğaltmalarıyla nasıl ölçeklenebileceğinizi](howto-read-replicas-portal.md)öğrenin.

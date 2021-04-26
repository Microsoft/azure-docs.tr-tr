---
title: MySQL Server işletimsel en iyi uygulamalar-MySQL için Azure veritabanı
description: Bu makalede, Azure 'da MySQL veritabanınızı çalıştırmaya yönelik en iyi uygulamalar açıklanmaktadır.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e1d84483a701bf7fb4e6c50b9dc4f4f89993c64d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96355121"
---
# <a name="best-practices-for-server-operations-on-azure-database-for-mysql--single-server"></a>MySQL için Azure veritabanı 'nda sunucu işlemleri için en iyi yöntemler-tek sunucu

MySQL için Azure veritabanı ile çalışmaya yönelik en iyi yöntemler hakkında bilgi edinin. Platforma yeni yetenekler eklediğimiz için, bu bölümde ayrıntılı olarak açıklanan en iyi yöntemleri iyileştirmeye odaklanacağız.

## <a name="azure-database-for-mysql-operational-guidelines"></a>MySQL için Azure veritabanı operasyonel yönergeler 

Aşağıda, veritabanınızın performansını geliştirmek için MySQL için Azure veritabanı ile çalışırken izlenmesi gereken işlem yönergeleri verilmiştir: 

* **Ortak konum**: ağ gecikmesini azaltmak için istemciyi ve veritabanı sunucusunu aynı Azure bölgesine yerleştirin.

* **Bellek, CPU ve depolama kullanımınızı izleme**: sistem performansını ve kullanılabilirliğini koruyabilmeniz için, kullanım desenlerinin değiştiğinde veya dağıtımınızın kapasitesine yaklaşımda size bildirimde bulunan [Uyarılar](howto-alert-on-metric.md) ayarlayabilirsiniz. 

* **DB örneğinizi ölçeklendirin**: depolama kapasitesi sınırlarına yaklaşdığınızda [ölçeği](howto-create-manage-server-portal.md) izleyebilirsiniz. Uygulamalarınızdan isteğe bağlı olarak görülmeyen artışlara uyum sağlamak için depolama ve bellekte bazı arabelleğe sahip olmanız gerekir. Ayrıca, depolama sınırlarını yaklaştığında hizmetin depolamayı otomatik olarak ölçeklendirdiğinden emin olmak için, ' ON ' [depolama otomatik büyüme](howto-auto-grow-storage-portal.md) özelliğini de etkinleştirebilirsiniz. 

* **Yedeklemeleri yapılandırma**: iş gereksinimine göre [yerel veya coğrafi olarak yedekli yedeklemeleri](howto-restore-server-portal.md#set-backup-configuration) etkinleştirin. Ayrıca, yedeklemelerin iş sürekliliği için ne kadar süreyle kullanılabilir olduğu ile ilgili saklama süresini değiştirirsiniz. 

* **G/ç kapasitesini artır**: veritabanı iş yükünüz sağladığınız daha fazla g/ç gerektiriyorsa, kurtarma veya veritabanınız için diğer işlem işlemleri yavaş olur. Bir sunucu örneğinin g/ç kapasitesini artırmak için aşağıdakilerden birini yapın: 

    * MySQL için Azure veritabanı, sağlanan GB depolama alanı başına üç ıOPS hızında ıOPS ölçekleme sağlar. Daha iyi performans için ıOPS 'yi ölçeklendirmek üzere [sağlanan depolamayı artırın](howto-create-manage-server-portal.md#scale-storage-up) . 

    * Sağlanan ıOPS depolama alanını zaten kullanıyorsanız, [ek işleme kapasitesi](howto-create-manage-server-portal.md#scale-storage-up)sağlayın. 

* **Ölçeği** işleme: veritabanı Iş yükü CPU veya bellek nedeniyle da sınırlanabilir ve bu işlem işleme üzerinde ciddi bir etkiye sahip olabilir. Hesaplama (Fiyatlandırma Katmanı) yalnızca [genel amaçlı veya bellek Için iyileştirilmiş](concepts-pricing-tiers.md) katmanlar arasında ölçeklendirilebilir veya küçültülebileceğini unutmayın. 

* **Yük devretme testi**: işlemin kullanım örneğinizin ne kadar sürdüğünü anlamak ve sunucu örneğinizle olan uygulamanın yük devretmeden sonra otomatik olarak yeni sunucu örneğine bağlanmasını sağlamak için sunucu örneğiniz için yük devretmeyi el ile test edin.

* **Birincil anahtarı kullan**: tablolarınızı, MySQL Için Azure veritabanı üzerinde çalıştığınız şekilde birincil veya benzersiz bir anahtara sahip olduğundan emin olun. Bu, çok sayıda yedekleme, çoğaltma vb. ve performansı artırır.

* **TTL değerini yapılandırın**: istemci uygulamanız, sunucu örneklerinizin etki alanı adı HIZMETI (DNS) verilerini önbelleğe alıyorsa, 30 saniyeden daha az bir yaşam SÜRESI (TTL) değeri ayarlayın. Bir sunucu örneğinin temelindeki IP adresi bir yük devretmeden sonra değiştirebildiğinden, uygulamanız artık hizmette olmayan bir IP adresine bağlanmaya çalışırsa, DNS verilerini uzun süre önbelleğe almak bağlantı hatalarına neden olabilir.

* [En yüksek bağlantı sınırlarına](concepts-server-parameters.md#max_connections)ulaşmaktan kaçınmak için bağlantı havuzunu kullanın ve aralıklı bağlantı sorunlarından kaçınmak için yeniden deneme mantığını kullanın. 

* Çoğaltma kullanıyorsanız, birincil sunucu ve okunabilir ikincil çoğaltma sunucusu arasındaki [yükü dengelemek Için Proxysql](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847) kullanın. Burada kurulum adımlarına bakın. </br> 

* Kaynak sağlanırken, MySQL için Azure veritabanı için [Otomatik büyümenin etkinleştirildiğinden](howto-auto-grow-storage-portal.md) emin olun. Bu, herhangi bir ek maliyet eklemez ve bu veritabanını, çalıştırdığınız tüm depolama sorunlarını koruacaktır. </br> 


### <a name="using-innodb-with-azure-database-for-mysql"></a>MySQL için Azure veritabanı ile InnoDB kullanma

*   `ibdata1`Bu özellik kullanılıyorsa, bir sistem tablo alanı veri dosyası tablodaki verileri bırakarak veya tablo başına dosya-tabloya taşınırken daraltılamıyor veya temizlenemiyor `tablespaces` .

* Boyutu 1 TB 'den büyük bir veritabanı için **innodb_file_per_table** ' de tabloyu oluşturmalısınız `tablespace` . Boyutu 1 TB 'den büyük olan tek bir tablo için [bölüm](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) tablosuna ihtiyacınız vardır.

*   Çok sayıda olan bir sunucu için `tablespace` , MySQL başlatma veya yük devretme sırasında sıralı tablo taraması nedeniyle altyapı başlatması çok yavaş olur. 

* Tablo oluşturmadan önce, toplam tablo numarası 500 ' den küçükse innodb_file_per_table = açık olarak ayarlayın.

* Veritabanında 500 ' den fazla tablo varsa, her tablo için tablo boyutunu gözden geçirin. Büyük bir tablo için, tablo başına dosya alanı alanını kullanmayı göz önünde bulundurmanız gerekir. Bu arada, sistem tablo alanı dosya isabetinin en fazla depolama sınırının

> [!NOTE]
> Boyutu 5 GB 'tan az olan tablolar için sistem tablo alanını kullanmayı düşünün 
> ```sql
>    CREATE TABLE tbl_name ... *TABLESPACE* = *innodb_system*;
> ```

* Çok büyük bir tablonuz büyük olasılıkla 1 TB 'ın üzerinde büyürken tablo oluşturma sırasında tablonuzu [bölümleyin](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) .

* Birden çok MySQL sunucusu kullanın ve tabloları bu sunuculara yayın. 10000 tablo veya daha fazlası varsa, tek bir sunucuya çok fazla tablo yerleştirmekten kaçının. 

## <a name="next-steps"></a>Sonraki adımlar
- [MySQL için Azure veritabanı performansı için en iyi uygulama](concept-performance-best-practices.md)
- [MySQL için Azure veritabanınızı izlemek için en iyi uygulama](concept-monitoring-best-practices.md)

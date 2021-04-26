---
title: En iyi performans uygulamaları-MySQL için Azure veritabanı
description: Bu makalede MySQL için Azure veritabanınızın performansını izlemeye ve ayarlamaya yönelik bazı öneriler açıklanmaktadır.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/28/2021
ms.openlocfilehash: 7b5223bc08c470a0e8722b76b80473aaa235b51a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727167"
---
# <a name="best-practices-for-optimal-performance-of-your-azure-database-for-mysql---single-server"></a>MySQL için Azure veritabanınızın en iyi performansı için en iyi uygulamalar-tek sunucu

MySQL için Azure veritabanı-tek sunucu ile çalışırken en iyi performansı nasıl alabileceğinizi öğrenin. Platforma yeni yetenekler eklediğimiz için, bu bölümdeki önerilerimizi iyileştirmemiz için devam edeceğiz.

## <a name="physical-proximity"></a>Fiziksel yakınlık

 Bir uygulamayı ve veritabanını aynı bölgede dağıttığınızdan emin olun. İstemci ve veritabanı arasındaki ağ gecikmesini basit bir SELECT 1 sorgusu kullanarak belirlemek, herhangi bir performans sınama çalıştırması başlatmadan önce hızlı bir denetim sağlar. 

## <a name="accelerated-networking"></a>Hızlandırılmış Ağ

Azure sanal makinesi, Azure Kubernetes veya uygulama Hizmetleri kullanıyorsanız, uygulama sunucusu için hızlandırılmış ağ kullanın. Hızlandırılmış ağ, bir VM 'ye tek köklü g/ç Sanallaştırması (SR-ıOV) sağlar ve ağ performansını büyük ölçüde geliştirir. Bu yüksek performanslı yol, desteklenen VM türlerinde en zorlu ağ iş yükleri ile kullanım için, gecikme süresi, değişim ve CPU kullanımını azaltan ana bilgisayarı veri yolundan atlar.

## <a name="connection-efficiency"></a>Bağlantı verimliliği

Yeni bir bağlantı kurmak her zaman pahalı ve zaman alıcı bir görevdir. Bir uygulama bir veritabanı bağlantısı istediğinde, yeni bir tane oluşturmak yerine var olan boştaki veritabanı bağlantılarının ayrılmasını önceliklendirir.  İyi bağlantı uygulamaları için bazı seçenekler şunlardır:

- **Proxysql**: yerleşik bağlantı havuzu sağlayan [proxysql](https://proxysql.com/) kullanın ve uygulama kodundaki değişikliklerle isteğe bağlı olarak birden çok okuma çoğaltmasına [Yük Dengelemesi](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) yapın.

- **Heimdall Data proxy**: Alternatif olarak, satıcının bağımsız özel proxy çözümü olan Heımdall veri proxy 'sinin da yararlanabilirsiniz. Sorgu önbelleğe alma ve çoğaltma gecikmesi algılaması ile okuma/yazma bölme işlemlerini destekler. Ayrıca [, Heımdall proxy Ile MySQL performansını hızlandırma](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/accelerate-mysql-performance-with-the-heimdall-proxy/ba-p/1063349)bölümüne de başvurabilirsiniz.  

- **Kalıcı veya Long-Lived bağlantısı**: uygulamanızda genellikle 5-10 MS < yürütme süresi ile kısa işlemler veya sorgular varsa, kısa bağlantıları kalıcı bağlantılarla değiştirin. Kısa bağlantıları kalıcı bağlantılarla değiştirme yalnızca kodda küçük değişiklikler yapılmasını gerektirir, ancak birçok tipik uygulama senaryosunda performansı iyileştirmek açısından önemli bir etkiye sahiptir. İşlem tamamlandığında zaman aşımını ayarladığınızdan emin olun veya bağlantıyı kapatın.

- **Çoğaltma**: çoğaltma kullanıyorsanız, birincil sunucu ile okunabilir ikincil çoğaltma sunucusu arasındaki yükü dengelemek Için [proxysql](https://proxysql.com/) kullanın. [ProxySQL ayarlamayı](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847)öğrenin.

## <a name="data-import-configurations"></a>Veri Içeri aktarma yapılandırması

- Bir veri içeri aktarma işlemine başlamadan önce örneğinizi daha yüksek SKU boyutuna ölçeklendirebilir ve içeri aktarma başarılı olduğunda ölçeği devre dışı bırakabilirsiniz.
- Çevrimiçi veya çevrimdışı geçişler için [Azure veritabanı geçiş hizmeti 'ni (DMS)](https://datamigration.microsoft.com/) kullanarak verilerinizi en az kapalı kalma süresiyle içeri aktarabilirsiniz. 

## <a name="azure-database-for-mysql-memory-recommendations"></a>MySQL için Azure veritabanı bellek önerileri

MySQL için Azure veritabanı performansı en iyi uygulaması, çalışma kümesi neredeyse tamamen bellekte yer alacak şekilde yeterli miktarda RAM ayırmaya yöneliktir. 

- [MySQL sunucusunun ölçümlerini](./concepts-monitoring.md)kullanarak [sınırlara](./concepts-pricing-tiers.md) ulaşılması halinde kullanılan bellek yüzdesinin olup olmadığını denetleyin. 
- Sunucuların sınırlara ulaştığı şekilde bu tür uyarılar için uyarı ayarlayın. Tanımlanan sınırlara bağlı olarak, veritabanı SKU 'sunun ölçeğini, daha yüksek işlem boyutuna veya daha iyi bir fiyatlandırma katmanına göre ölçeklendirerek, performansa göre önemli bir artış elde edin. 
- Ölçek işleminden sonra performans numaralarınızı önemli ölçüde düşene kadar ölçeği ölçeklendirin. Bir VERITABANı örneğinin ölçümlerini izleme hakkında bilgi için bkz. [MySQL db ölçümleri](./concepts-monitoring.md#metrics).
 
## <a name="use-innodb-buffer-pool-warmup"></a>InnoDB arabellek havuzu Warmup kullanın

MySQL sunucusu için Azure veritabanı 'nı yeniden başlattıktan sonra, tablolar sorgulandığı için, depolamada bulunan veri sayfaları yüklenir ve bu, sorguların ilk yürütülmesi için gecikme süresini arttığı ve performansı düşürür. Bu, gecikme süresine duyarlı iş yükleri için kabul edilebilir olmayabilir. 

InnoDB arabellek havuzunun kullanıldığı ısınma, bir süre önce arabellek havuzunda bulunan disk sayfalarını DML veya belirli satırlara erişmek için işlem işlemleri yerine yeniden başlatmadan önce yeniden başlatarak ısınma süresini kısaltır.

MySQL için Azure veritabanı sunucunuzu yeniden başlattıktan sonra ısınma süresini azaltabilirsiniz. Bu, [InnoDB arabellek havuzu sunucu parametrelerini](https://dev.mysql.com/doc/refman/8.0/en/innodb-preload-buffer-pool.html)yapılandırarak bir performans avantajı sunar. InnoDB, sunucu kapatılmadan her bir arabellek havuzu için en son kullanılan sayfaların yüzdesini kaydeder ve bu sayfaları sunucu başlangıcında geri yükler.

Ayrıca, gelişmiş performansın sunucu için daha uzun başlangıç zamanı harcamasıyla birlikte geldiğini unutmayın. Bu parametre etkinleştirildiğinde sunucu başlatma ve yeniden başlatma zamanının, sunucuda sağlanan ıOPS 'ye bağlı olarak artması beklenir. 

Bu süre boyunca sunucu kullanılamadığından başlatma/yeniden başlatma performansının kabul edilebilir olmasını sağlamak için test etmeyi ve yeniden başlatma süresini izlemenizi öneririz. Bu parametrenin 1000 'den az sağlanan ıOPS (veya başka bir deyişle, sağlanan depolama alanı 335 GB 'den az olduğunda) kullanılması önerilmez.

Sunucu kapatılırken arabellek havuzunun durumunu kaydetmek için sunucu parametresini `innodb_buffer_pool_dump_at_shutdown` olarak ayarlayın `ON` . Benzer şekilde, sunucu `innodb_buffer_pool_load_at_startup` `ON` başlatma sırasında arabellek havuzu durumunu geri yüklemek için sunucu parametresini olarak ayarlayın. Sunucu parametresinin değerini indirerek ve ince ayar yaparak başlangıç/yeniden başlatma sırasında etkiyi denetleyebilirsiniz `innodb_buffer_pool_dump_pct` . Varsayılan olarak, bu parametre olarak ayarlanır `25` .

> [!Note]
> InnoDB arabellek havuzu ısınma parametreleri yalnızca 16 TB 'a kadar depolama alanı bulunan genel amaçlı depolama sunucularında desteklenir. [MySQL Için Azure veritabanı depolama seçenekleri](./concepts-pricing-tiers.md#storage)hakkında daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

- [MySQL için Azure veritabanı 'nı kullanarak sunucu işlemleri için en iyi uygulama](concept-operation-excellence-best-practices.md) <br/>
- [MySQL için Azure veritabanınızı izlemek için en iyi uygulama](concept-monitoring-best-practices.md)<br/>
- [MySQL için Azure veritabanı 'nı kullanmaya başlama](quickstart-create-mysql-server-database-using-azure-portal.md)<br/>
---
title: Çoğaltmaları oku-MySQL için Azure veritabanı-esnek sunucu
description: 'MySQL için Azure veritabanı esnek sunucusunda okuma çoğaltmaları hakkında bilgi edinin: çoğaltmalar oluşturma, çoğaltmaya bağlanma, çoğaltmayı izleme ve çoğaltmayı durdurma.'
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 01/14/2021
ms.openlocfilehash: e51b9667e3bb20a6bd463d3286888085a927f2c0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109667"
---
# <a name="read-replicas-in-azure-database-for-mysql---flexible-server"></a>MySQL için Azure veritabanı 'nda çoğaltmaları okuma-esnek sunucu

> [!IMPORTANT]
> MySQL için Azure veritabanı 'nda çoğaltmaları okuma-esnek sunucu önizlemededir.

MySQL, İnternet ölçeğinde Web ve mobil uygulamaları çalıştırmaya yönelik popüler veritabanı altyapılarından biridir. Müşterilerimizin birçoğu, çevrimiçi eğitim hizmetleri, video akış Hizmetleri, dijital ödeme çözümleri, e-ticaret platformları, Oyun Hizmetleri, haber portalları, kamu ve sağlık Web siteleri için bunu kullanır. Web veya mobil uygulamadaki trafik arttıkça bu hizmetlerin hizmet vermesi ve ölçeklendirilmesi gerekir.

Uygulamalar tarafında, uygulama genellikle Java veya php 'de geliştirilmiştir ve Azure sanal makine ölçek kümeleri veya Azure Uygulama hizmetlerinde çalışacak şekilde geçirilir ya da Azure Kubernetes hizmeti (AKS) üzerinde çalışmak üzere Kapsayıcılı hale getirilir. Arka plandaki altyapı olarak sanal makine ölçek kümesi, App Service veya AKS ile, uygulama ölçekleme, anında yeni VM 'Ler sunarak basitleştirilir ve isteklerin durum bilgisiz bileşenleri, isteklere geçmek için, ancak genellikle veritabanı, Merkezi durum bilgisi olan bir bileşen olarak performans sorunlarına neden olur.

Çoğaltma oku özelliği, MySQL için Azure veritabanı esnek sunucusu 'na ait verileri salt okunurdur bir sunucuya çoğaltmanıza olanak sağlar. Kaynak sunucudan **10** ' a kadar çoğaltma yapabilirsiniz. Çoğaltmalar, MySQL altyapısının yerel ikili günlük (binlog) dosya konumu tabanlı çoğaltma teknolojisini kullanılarak zaman uyumsuz olarak güncelleştirilir. Binlog çoğaltma hakkında daha fazla bilgi edinmek için [MySQL binlog çoğaltmasına genel bakış](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)bölümüne bakın.

Çoğaltmalar, MySQL için kaynak Azure veritabanı esnek sunucuları ile benzer şekilde yönettiğiniz yeni sunuculardır. Sanal çekirdekler ve depolama biriminde GB/ay içinde sağlanan işlem temelinde her bir okuma çoğaltması için faturalandırma ücretlerine tabi olursunuz. Daha fazla bilgi için bkz. [Fiyatlandırma](./concepts-compute-storage.md#pricing).

MySQL çoğaltma özellikleri ve sorunları hakkında daha fazla bilgi edinmek için [MySQL Çoğaltma belgelerine](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)bakın.

> [!NOTE]
> Bu makale, Microsoft 'un artık kullandığı bir terim olan _bağımlı_ dönem başvuruları içerir. Terim yazılımlardan kaldırıldığında, bu makaleden kaldıracağız.
>

## <a name="common-use-cases-for-read-replica"></a>Okuma çoğaltması için genel kullanım örnekleri

Okuma çoğaltması özelliği, okuma yoğunluklu iş yüklerinin performansını ve ölçeğini artırmaya yardımcı olur. Okuma iş yükleri çoğaltmalar için yalıtılabilir, yazma iş yükleri kaynağa yönlendirilebilir.

Yaygın senaryolar şunlardır:

* Uygulamadan gelen okuma-iş yüklerini ölçeklendirin, uygulamalardan gelen okuma sorgularını okumak için [Proxysql](https://aka.ms/ProxySQLLoadBalanceReplica) gibi hafif bağlantı proxy 'sini kullanarak veya mikro hizmetler tabanlı desenler kullanarak uygulamadan geliyor
* Bı veya analitik raporlama iş yükleri, raporlama için veri kaynağı olarak okuma çoğaltmalarını kullanabilir
* Verilerin raporlanması için birden çok okuma yinelemesi kullanıldığı sırada telemetri bilgilerinin MySQL veritabanı altyapısına çalıştırıldığı IoT veya üretim senaryosunda

Çoğaltmalar salt okunurdur, kaynak üzerindeki yazma kapasitesini doğrudan azaltmazlar. Bu özellik, yazma açısından yoğun iş yükleri için uygun değildir.

Okuma çoğaltması özelliği MySQL zaman uyumsuz çoğaltma kullanır. Özelliği, zaman uyumlu çoğaltma senaryolarına yönelik değildir. Kaynak ve çoğaltma arasında ölçülebilir bir gecikme olacaktır. Çoğaltılan veriler, sonunda kaynaktaki verilerle tutarlı hale gelir. Bu gecikmeyi barındırabilecek iş yükleri için bu özelliği kullanın.

## <a name="create-a-replica"></a>Çoğaltma oluşturma

Kaynak sunucuda var olan bir çoğaltma sunucusu yoksa, kaynak ilk olarak çoğaltma için hazırlamak üzere yeniden başlatılır.

Çoğaltma oluşturma iş akışı 'nı başlattığınızda, MySQL için boş bir Azure veritabanı sunucusu oluşturulur. Yeni sunucu, kaynak sunucuda bulunan verilerle doldurulur. Oluşturma süresi, kaynaktaki veri miktarına ve son haftalık tam yedeklemeden bu yana geçen zamana bağlıdır. Süre, birkaç dakika ile birkaç saat arasında değişebilir.

> [!NOTE]
> Okuma çoğaltmaları kaynakla aynı sunucu yapılandırmasıyla oluşturulur. Çoğaltma sunucusu yapılandırması oluşturulduktan sonra değiştirilebilir. Çoğaltma sunucusu her zaman aynı kaynak grubunda, aynı konum ve kaynak sunucuyla aynı abonelikte oluşturulur. Farklı bir kaynak grubuna veya farklı bir aboneliğe çoğaltma sunucusu oluşturmak istiyorsanız, oluşturulduktan sonra [Çoğaltma sunucusunu taşıyabilirsiniz](../../azure-resource-manager/management/move-resource-group-and-subscription.md) . Çoğaltmanın kaynakla devam edebileceğinden emin olmak için çoğaltma sunucusunun yapılandırmasının kaynaktan eşit veya daha büyük bir değer tutulması önerilir.

[Azure Portal bir okuma çoğaltması oluşturmayı](how-to-read-replicas-portal.md)öğrenin.

## <a name="connect-to-a-replica"></a>Bir çoğaltmaya bağlanma

Oluşturma sırasında bir çoğaltma, kaynak sunucunun bağlantı yöntemini devralır. Çoğaltmanın bağlantı yöntemini değiştiremezsiniz. Örneğin, kaynak sunucunun **özel erişimi (VNET tümleştirmesi)** varsa, çoğaltma **ortak erişim (izin verilen IP adresleri)** olamaz.

Çoğaltma, yönetim hesabını kaynak sunucudan devralır. Kaynak sunucudaki tüm Kullanıcı hesapları, okuma çoğaltmalarına çoğaltılır. Bir okuma çoğaltmasına yalnızca kaynak sunucuda bulunan Kullanıcı hesaplarını kullanarak bağlanabilirsiniz.

Veri ana bilgisayar adını ve geçerli bir kullanıcı hesabını kullanarak, MySQL için normal bir Azure veritabanı esnek sunucusunda yaptığınız gibi çoğaltmaya bağlanabilirsiniz. Yönetici Kullanıcı adı **myadmin** olan **myreplica** adlı BIR sunucu için MySQL CLI kullanarak çoğaltmaya bağlanabilirsiniz:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin -p
```

İstemde, Kullanıcı hesabının parolasını girin.

## <a name="monitor-replication"></a>Çoğaltmayı izleme

MySQL için Azure veritabanı esnek sunucu, Azure Izleyici 'de **saniye cinsinden çoğaltma gecikmesi** sağlar. Bu ölçüm yalnızca çoğaltmalar için kullanılabilir. Bu ölçüm, `seconds_behind_master` MySQL 'in komutunda kullanılabilir olan ölçüm kullanılarak hesaplanır `SHOW SLAVE STATUS` . Çoğaltma gecikmesi iş yükünüz için kabul edilebilir bir değere ulaştığında sizi bilgilendirmek için bir uyarı ayarlayın.

Daha fazla çoğaltma gecikmesi görürseniz, sorunun giderilmesi ve olası nedenleri anlamak için [sorun giderme çoğaltma gecikmesini](./../howto-troubleshoot-replication-latency.md) inceleyin.

## <a name="stop-replication"></a>Çoğaltmayı durdurma

Kaynak ve çoğaltma arasında çoğaltmayı durdurabilirsiniz. Kaynak sunucu ve okuma çoğaltması arasında çoğaltma durdurulduktan sonra, çoğaltma tek başına bir sunucu haline gelir. Tek başına sunucusundaki veriler, çoğaltma durdurma komutunun başlatıldığı zamanda çoğaltma üzerinde kullanılabilir olan veri. Tek başına sunucu, kaynak sunucuyla ilgili değildir.

Çoğaltma çoğaltmasını durdurmayı seçtiğinizde, önceki kaynak ve diğer çoğaltmaların tüm bağlantılarını kaybeder. Kaynak ve çoğaltma arasında otomatik yük devretme yoktur.

> [!IMPORTANT]
> Tek başına sunucu tekrar bir çoğaltmaya yapılamaz.
> Bir okuma çoğaltmasında çoğaltmayı durdurmadan önce, çoğaltmanın gerekli tüm verilere sahip olduğundan emin olun.

[Bir çoğaltmaya çoğaltmayı durdurmayı](how-to-read-replicas-portal.md)öğrenin.

## <a name="failover"></a>Yük devretme

Kaynak ve çoğaltma sunucuları arasında otomatik yük devretme yoktur.

Okuma çoğaltmaları, okuma yoğunluğu olan iş yüklerinin ölçeklendirilmesine yöneliktir ve bir sunucunun yüksek kullanılabilirlik gereksinimlerini karşılayacak şekilde tasarlanmamıştır. Kaynak ve çoğaltma sunucuları arasında otomatik yük devretme yoktur. Okuma yazma modunda çevrimiçi duruma getirmek için okuma çoğaltmasındaki çoğaltmayı durdurmak, bu el ile yük devretmenin gerçekleştirildiği anlamına gelir.

Çoğaltma zaman uyumsuz olduğundan, kaynak ve çoğaltma arasında bir gecikme vardır. Gecikme miktarı, kaynak sunucuda çalışan iş yükünün ne kadar ağır ve veri merkezleri arasındaki gecikme süresi gibi birçok faktörden etkilenebilir. Çoğu durumda, çoğaltma gecikmesi birkaç saniyeyle birkaç dakika arasında değişir. Her bir çoğaltma için kullanılabilen ölçüm *çoğaltması* gecikmesini kullanarak gerçek çoğaltma gecikmelerinizi izleyebilirsiniz. Bu ölçüm, son yeniden yürütülmüş işlemden bu yana geçen süreyi gösterir. Yineleme gecikmesini bir süre içinde gözlemleyerek ortalama gecikmenizin ne olduğunu tanımlamanızı öneririz. Çoğaltma gecikmesi üzerinde bir uyarı ayarlayabilirsiniz, böylece beklenen aralığın dışında olursa işlem yapabilirsiniz.

> [!Tip]
> Çoğaltmaya yük devretmek, çoğaltmayı kaynaktan geri bağladığınızda oluşan gecikme süresi, ne kadar veri kaybı olduğunu gösterir.

Bir çoğaltmaya nasıl yük devretmek istediğinize karar verdikten sonra:

1. Çoğaltmaya çoğaltmayı durdur<br/>
   Bu adım, çoğaltma sunucusunun yazmaları kabul edebilmesini sağlamak için gereklidir. Bu işlemin bir parçası olarak, çoğaltma sunucusu kaynaktan kaldırılacak. Çoğaltmayı durdur işlemini başlattıktan sonra, arka uç işleminin genellikle yaklaşık 2 dakika sürer. Bu eylemin etkilerini anlamak için bu makalenin [çoğaltmayı durdur](#stop-replication) bölümüne bakın.

2. Uygulamanızı (eski) çoğaltmaya işaret edin<br/>
   Her sunucunun benzersiz bir bağlantı dizesi vardır. Uygulamanızı kaynak yerine (eski) çoğaltmaya işaret etmek üzere güncelleştirin.

Uygulamanız okuma ve yazma işlemlerini başarıyla tamamladıktan sonra, yük devretmeyi tamamladınız. Bir sorunu saptadığınızda ve yukarıdaki 1. ve 2. adımları tamamladıktan sonra uygulama deneyimlerinizin ne kadar süre açık olacağını gösterir.

## <a name="considerations-and-limitations"></a>Önemli noktalar ve sınırlamalar

| Senaryo | Sınırlama/değerlendirme |
|:-|:-|
| Bölgesel olarak yedekli HA etkin sunucu üzerinde çoğaltma | Desteklenmez |
| Çapraz bölge okuma çoğaltması | Desteklenmez |
| Fiyatlandırma | Çoğaltma sunucusunu çalıştırmanın maliyeti, çoğaltma sunucusunun çalıştığı bölgeyi temel alır |
| Kaynak sunucu yeniden başlatması | Mevcut çoğaltmaları olmayan bir kaynak için çoğaltma oluşturduğunuzda, kaynak ilk olarak çoğaltma için hazırlamak üzere yeniden başlatılır. Bunu göz önünde bulundurun ve yoğun olmayan bir dönemde bu işlemleri gerçekleştirin |
| Yeni çoğaltmalar | Bir okuma çoğaltması, MySQL için yeni bir Azure veritabanı esnek sunucu olarak oluşturulur. Var olan bir sunucu bir çoğaltmaya yapılamaz. Başka bir okuma çoğaltmasının çoğaltmasını oluşturamazsınız |
| Çoğaltma yapılandırması | Bir çoğaltma, kaynakla aynı sunucu yapılandırması kullanılarak oluşturulur. Bir çoğaltma oluşturulduktan sonra, kaynak sunucudan bağımsız olarak çeşitli ayarlar değiştirilebilir: işlem oluşturma, sanal çekirdek, depolama ve yedekleme saklama süresi. İşlem katmanı da bağımsız olarak değiştirilebilir.<br> <br> **ÖNEMLİ**  <br> -Kaynak sunucu yapılandırması yeni değerlere güncellenmesinden önce, çoğaltma yapılandırmasını eşit veya daha fazla değere göre güncelleştirin. Bu eylem, çoğaltmanın kaynak kopya üzerinde yapılan değişiklikleri yansıtmasını sağlar. <br/> Çoğaltma oluşturulduğunda bağlantı yöntemi ve parametre ayarları kaynak sunucudan çoğaltmaya devralınır. Daha sonra, çoğaltmanın kuralları bağımsızdır. |
| Durdurulan çoğaltmalar | Kaynak sunucuyla bir okuma çoğaltması arasında çoğaltmayı durdurursanız, durdurulan çoğaltma hem okuma hem de yazma işlemlerini kabul eden tek başına bir sunucu haline gelir. Tek başına sunucu tekrar bir çoğaltmaya yapılamaz. |
| Kaynak ve tek başına sunucular silindi | Bir kaynak sunucu silindiğinde, çoğaltma tüm okuma çoğaltmalarına durdurulur. Bu çoğaltmalar otomatik olarak tek başına sunucular olur ve hem okuma hem de yazma işlemlerini kabul edebilir. Kaynak sunucunun kendisi silinir. |
| Kullanıcı hesapları | Kaynak sunucudaki kullanıcılar okuma çoğaltmalarına çoğaltılır. Bir okuma çoğaltmasına yalnızca kaynak sunucuda bulunan Kullanıcı hesaplarını kullanarak bağlanabilirsiniz. |
| Sunucu parametreleri | Verilerin eşitlenmemiş duruma gelmesini ve olası veri kaybını önlemek için, okuma amaçlı çoğaltmaları kullanırken bazı sunucu parametreleri güncelleştirmeye karşı kilitlenir. <br> Aşağıdaki sunucu parametreleri hem kaynak hem de çoğaltma sunucularında kilitlidir:<br> - [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/8.0/en/innodb-file-per-table-tablespaces.html) <br> - [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) <br> [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler)Parametresi, çoğaltma sunucularında kilitlidir. <br> Kaynak sunucuda yukarıdaki parametrelerden birini güncelleştirmek için, çoğaltma sunucularını silin, kaynaktaki parametre değerini güncelleştirin ve çoğaltmaları yeniden oluşturun. |
| Diğer | -Bir çoğaltmanın çoğaltmasını oluşturma desteklenmez. <br> Bellek içi tablolar çoğaltmaların eşitlenmemiş hale gelmesine neden olabilir. Bu, MySQL Çoğaltma teknolojisinin bir sınırlamasıdır. Daha fazla bilgi için [MySQL Reference belgelerindeki](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) daha fazla bilgi edinin. <br>-Kaynak sunucu tablolarının birincil anahtarlara sahip olduğundan emin olun. Birincil anahtarların olmaması, kaynak ve çoğaltmalar arasında çoğaltma gecikmesine neden olabilir.<br>- [MySQL belgelerindeki](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) MySQL Çoğaltma kısıtlamalarının tam listesini gözden geçirin |

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Portal kullanarak okuma çoğaltmaları oluşturmayı ve yönetmeyi](how-to-read-replicas-portal.md) öğrenin
* [Azure CLI kullanarak okuma çoğaltmaları oluşturma ve yönetme](how-to-read-replicas-cli.md) hakkında bilgi edinin
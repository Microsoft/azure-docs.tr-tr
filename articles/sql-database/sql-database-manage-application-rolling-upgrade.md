---
title: Uygulama yükseltmeleri çalışırken
description: Bulut uygulamanızın çevrimiçi yükseltmelerini desteklemek için Azure SQL veritabanı coğrafi çoğaltma 'nın nasıl kullanılacağını öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/13/2019
ms.openlocfilehash: 9c627c3e597fdcd3859ce02ea208fc7a8b5d612b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822869"
---
# <a name="manage-rolling-upgrades-of-cloud-applications-by-using-sql-database-active-geo-replication"></a>SQL veritabanı etkin coğrafi çoğaltma kullanarak bulut uygulamalarının sıralı yükseltmelerini yönetme

Azure SQL veritabanında [etkin Coğrafi çoğaltmayı](sql-database-auto-failover-group.md) kullanarak bulut uygulamanızın çalışırken yükseltmelerini nasıl kullanacağınızı öğrenin. Yükseltmeler kesintiye uğradığı için bunlar iş sürekliliği planlama ve tasarımınızın bir parçası olmalıdır. Bu makalede, yükseltme işlemini düzenleyen iki farklı yönteme bakacağız ve her seçeneğin avantajları ve avantajları tartışıyoruz. Bu makalenin amaçları doğrultusunda, veri katmanı olarak tek bir veritabanına bağlı olan bir Web sitesinden oluşan bir uygulamaya başvurduk. Amacınız, uygulamanın sürüm 1 (v1) sürümünü sürüm 2 ' ye (v2), Kullanıcı deneyimi üzerinde önemli bir etki olmadan yükseltmektir.

Yükseltme seçeneklerini değerlendirirken, şu faktörleri göz önünde bulundurun:

* Uygulama işlevlerinin ne kadar süreyle sınırlı veya düşürülmüş olması gibi yükseltmeler sırasında uygulama kullanılabilirliğine etkisi.
* Yükseltme başarısız olursa geri alma özelliği.
* Yükseltme sırasında ilgisiz ve çok zararlı bir hata oluşursa uygulamanın güvenlik açığı.
* Toplam dolar maliyeti. Bu faktör, yükseltme işlemi tarafından kullanılan geçici bileşenlerin ek veritabanı yedekliliği ve artımlı maliyetlerini içerir.

## <a name="upgrade-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Olağanüstü durum kurtarma için veritabanı yedeklemelerine güvenen uygulamaları yükseltme

Uygulamanız otomatik veritabanı yedeklemelerine dayanıyorsa ve olağanüstü durum kurtarma için coğrafi geri yükleme kullanıyorsa, tek bir Azure bölgesine dağıtılır. Kullanıcı kesintisini en aza indirmek için, bu bölgede, yükseltmede yer alan tüm uygulama bileşenlerine sahip bir hazırlama ortamı oluşturun. İlk diyagramda yükseltme işleminden önce işletimsel ortam gösterilmektedir. Uç nokta `contoso.azurewebsites.net` Web uygulamasının üretim ortamını temsil eder. Yükseltmeyi geri almak için veritabanının tamamen eşitlenmiş kopyasıyla bir hazırlama ortamı oluşturmanız gerekir. Yükseltme için bir hazırlama ortamı oluşturmak için aşağıdaki adımları izleyin:

1. Aynı Azure bölgesinde ikincil bir veritabanı oluşturun. Dengeli dağıtım işleminin tamamlanıp tamamlanmamın (1) olup olmadığını görmek için ikincili izleyin.
2. Web uygulamanız için yeni bir ortam oluşturun ve bunu ' hazırlama ' olarak çağırın. `contoso-staging.azurewebsites.net` URL 'SI ile Azure DNS kaydedilecek (2).

> [!NOTE]
> Bu hazırlık adımları üretim ortamını etkilemez ve bu, tam erişim modunda işlev görebilir.

![Bulut olağanüstü durum kurtarma için SQL veritabanı coğrafi çoğaltma yapılandırması.](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

Hazırlama adımları tamamlandığında, uygulama gerçek yükseltme için geçerli olur. Sonraki diyagramda, yükseltme işleminde yer alan adımlar gösterilmektedir:

1. Birincil veritabanını salt okuma moduna ayarlayın (3). Bu mod, Web uygulamasının üretim ortamının (v1) yükseltme sırasında salt okunurdur ve bu sayede v1 ve v2 veritabanı örnekleri arasında veri ayırt edilmesini önler.
2. Planlı sonlandırma modunu (4) kullanarak ikincil veritabanının bağlantısını kesin. Bu eylem, birincil veritabanının tamamen eşitlenmiş, bağımsız bir kopyasını oluşturur. Bu veritabanı yükseltilecek.
3. İkincil veritabanını okuma yazma moduna açın ve yükseltme betiğini (5) çalıştırın.

![Bulut olağanüstü durum kurtarma için SQL veritabanı coğrafi çoğaltma yapılandırması.](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

Yükseltme başarılı bir şekilde tamamlansa, artık kullanıcıları yükseltilen kopyaya, üretim ortamı haline gelen uygulamaya geçmeye hazırsınız demektir. Geçiş, sonraki diyagramda gösterildiği gibi birkaç adımdan oluşur:

1. Web uygulamasının üretim ve hazırlama ortamları (6) arasında bir takas işlemini etkinleştirin. Bu işlem, iki ortamın URL 'Lerini değiştirir. Artık `contoso.azurewebsites.net` Web sitesinin v2 sürümüne ve veritabanına (üretim ortamı) işaret eder. 
2. Artık, değiştirme sonrasında hazırlama kopyası olan v1 sürümüne ihtiyacınız yoksa, hazırlama ortamının yetkisini alabilirsiniz (7).

![Bulut olağanüstü durum kurtarma için SQL veritabanı coğrafi çoğaltma yapılandırması.](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

Yükseltme işlemi başarısız olursa (örneğin, yükseltme betiğindeki bir hata nedeniyle), hazırlık ortamını ele almak için göz önünde bulundurun. Uygulamayı yükseltme öncesi durumuna geri almak için, üretim ortamındaki uygulamayı tam erişime geri alın. Sonraki diyagramda yeniden sürüm adımları gösterilmektedir:

1. Veritabanı kopyasını okuma-yazma moduna (8) ayarlayın. Bu eylem, üretim kopyasının tüm v1 işlevlerini geri yükler.
2. Kök nedeni analizini gerçekleştirin ve hazırlama ortamının (9) yetkisini alın.

Bu noktada, uygulama tam işlevseldir ve yükseltme adımlarını yineleyebilirsiniz.

> [!NOTE]
> Henüz bir değiştirme işlemi gerçekleştirmediyseniz, geri alma DNS değişiklikleri gerektirmez.

![Bulut olağanüstü durum kurtarma için SQL veritabanı coğrafi çoğaltma yapılandırması.](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

Bu seçeneğin önemli avantajı, tek bir bölgedeki bir uygulamayı bir dizi basit adımı izleyerek yükseltebilmeniz gerekir. Yükseltmenin dolar maliyeti nispeten düşüktür. 

Ana zorunluluğunu getirir, yükseltme sırasında çok zararlı bir hata oluşursa, yükseltme öncesi durumuna kurtarma işlemi, uygulamayı farklı bir bölgede yeniden dağıtma ve coğrafi geri yükleme kullanarak veritabanını yedekten geri yükleme içerir. Bu işlem, önemli kapalı kalma süresine neden olur.

## <a name="upgrade-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Olağanüstü durum kurtarma için veritabanı coğrafi çoğaltmasını kullanan uygulamaları yükseltme

Uygulamanız iş sürekliliği için etkin coğrafi çoğaltma veya otomatik yük devretme grupları kullanıyorsa, en az iki farklı bölgeye dağıtılır. Bir birincil bölgede etkin, birincil veritabanı ve bir yedekleme bölgesinde salt okunurdur, ikincil veritabanı var. Bu makalenin başlangıcında bahsedilen faktörlerle birlikte, yükseltme süreci de şunları garanti etmelidir:

* Uygulama, yükseltme işlemi sırasında her zaman çok zararlı hatalardan korunmuş olarak kalır.
* Uygulamanın coğrafi olarak yedekli bileşenleri, etkin bileşenlerle paralel olarak yükseltilir.

Bu hedeflere ulaşmak için, Web Apps ortamlarını kullanmanın yanı sıra, etkin bir uç nokta ve bir yedekleme uç noktası olan bir yük devretme profili kullanarak Azure Traffic Manager avantajlarından yararlanabilirsiniz. Sonraki diyagramda, yükseltme işleminden önce işletimsel ortam gösterilmektedir. `contoso-1.azurewebsites.net` ve `contoso-dr.azurewebsites.net` Web siteleri, tam coğrafi yedeklilik ile uygulamanın üretim ortamını temsil eder. Üretim ortamı aşağıdaki bileşenleri içerir:

* Web uygulamasının üretim ortamı, birincil bölgedeki `contoso-1.azurewebsites.net` (1)
* Birincil bölgedeki birincil veritabanı (2)
* Yedekleme bölgesindeki Web uygulamasının bekleme örneği (3)
* Yedekleme bölgesindeki coğrafi çoğaltılan ikincil veritabanı (4)
* `contoso-1.azurewebsites.net` adlı bir çevrimiçi uç noktaya sahip Traffic Manager performans profili ve `contoso-dr.azurewebsites.net` adlı bir çevrimdışı uç nokta.

Yükseltmeyi geri almayı olanaklı kılmak için, uygulamanın tamamen eşitlenmiş kopyasıyla bir hazırlama ortamı oluşturmanız gerekir. Yükseltme işlemi sırasında çok zararlı bir hata oluşması durumunda uygulamanın hızlı bir şekilde kurtarılabileceğinden emin olmanız gerektiğinden, hazırlama ortamının da coğrafi olarak yedekli olması gerekir. Yükseltme için bir hazırlama ortamı oluşturmak için aşağıdaki adımlar gereklidir:

1. Birincil bölgede (6) Web uygulamasının hazırlama ortamını dağıtın.
2. Birincil Azure bölgesinde (7) ikincil bir veritabanı oluşturun. Web uygulamasının hazırlama ortamını bu sunucuya bağlanacak şekilde yapılandırın. 
3. Birincil bölgedeki ikincil veritabanını çoğaltarak yedekleme bölgesinde bir coğrafi olarak yedekli, ikincil veritabanı oluşturun. (Bu yöntem *zincirleme coğrafi çoğaltma*olarak adlandırılır.) (8).
4. Web uygulaması örneğinin hazırlama ortamını yedekleme bölgesinde (9) dağıtın ve (8) konumunda oluşturulan coğrafi olarak yedekli ikincil veritabanına bağlanacak şekilde yapılandırın.

> [!NOTE]
> Bu hazırlık adımları, uygulamayı üretim ortamında etkilemez. Okuma-yazma modunda tamamen işlevsel olarak kalır.

![Bulut olağanüstü durum kurtarma için SQL veritabanı coğrafi çoğaltma yapılandırması.](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

Hazırlama adımları tamamlandığında, hazırlama ortamı yükseltme için hazırlayın. Sonraki diyagramda bu yükseltme adımları gösterilmektedir:

1. Üretim ortamındaki birincil veritabanını salt okuma moduna ayarlayın (10). Bu mod, yükseltme sırasında üretim veritabanının (v1) değişmeyeceği ve bu sayede v1 ve v2 veritabanı örnekleri arasında verilerin ayırt edilmesini engelleyen bir güvence sağlar.

```sql
-- Set the production database to read-only mode
ALTER DATABASE <Prod_DB>
SET (ALLOW_CONNECTIONS = NO)
```

2. İkincil (11) bağlantısını keserek Coğrafi çoğaltmayı sonlandırın. Bu eylem, üretim veritabanının bağımsız, tamamen eşitlenmiş bir kopyasını oluşturur. Bu veritabanı yükseltilecek. Aşağıdaki örnek Transact-SQL kullanır, ancak [PowerShell](/powershell/module/az.sql/remove-azsqldatabasesecondary?view=azps-1.5.0) de kullanılabilir. 

```sql
-- Disconnect the secondary, terminating geo-replication
ALTER DATABASE <Prod_DB>
REMOVE SECONDARY ON SERVER <Partner-Server>
```

3. Yükseltme betiğini `contoso-1-staging.azurewebsites.net`, `contoso-dr-staging.azurewebsites.net`ve hazırlama birincil veritabanına (12) karşı çalıştırın. Veritabanı değişiklikleri otomatik olarak hazırlama ikincil öğesine çoğaltılır.

![Bulut olağanüstü durum kurtarma için SQL veritabanı coğrafi çoğaltma yapılandırması.](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

Yükseltme başarılı bir şekilde biterse, artık kullanıcıları uygulamanın v2 sürümüne geçmeye hazırsınız demektir. Sonraki diyagramda ilgili adımlar gösterilmektedir:

1. Birincil bölgedeki (13) ve yedekleme bölgesindeki (14) Web uygulamasının üretim ve hazırlama ortamları arasında takas işlemini etkinleştirin. Artık uygulamanın v2, yedekleme bölgesinde yedekli bir kopya ile bir üretim ortamı haline gelir.
2. Artık v1 uygulamasına (15 ve 16) ihtiyacınız yoksa, hazırlama ortamının yetkisini alabilirsiniz.

![Bulut olağanüstü durum kurtarma için SQL veritabanı coğrafi çoğaltma yapılandırması.](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

Yükseltme işlemi başarısız olursa (örneğin, yükseltme betiğindeki bir hata nedeniyle), hazırlama ortamını tutarsız bir durumda olacak şekilde değerlendirin. Uygulamayı yükseltme öncesi durumuna geri almak için, üretim ortamındaki uygulamanın v1 'yi kullanmaya geri dönün. Gerekli adımlar sonraki diyagramda gösterilmiştir:

1. Üretim ortamındaki birincil veritabanı kopyasını okuma-yazma moduna (17) ayarlayın. Bu eylem, üretim ortamındaki tam v1 işlevselliğini geri yükler.
2. Kök nedeni analizini gerçekleştirin ve hazırlama ortamını (18 ve 19) onarın veya kaldırın.

Bu noktada, uygulama tam işlevseldir ve yükseltme adımlarını yineleyebilirsiniz.

> [!NOTE]
> Bir değiştirme işlemi gerçekleştirmediğinizden geri alma, DNS değişiklikleri gerektirmez.

![Bulut olağanüstü durum kurtarma için SQL veritabanı coğrafi çoğaltma yapılandırması.](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

Bu seçeneğin önemli avantajı, yükseltme sırasında iş sürekliliğini tehlikeye atmadan hem uygulamayı hem de coğrafi olarak yedekli kopyayı paralel olarak yükseltebilmeniz gerekir.

Ana zorunluluğunu getirir, her uygulama bileşeni için iki artıklık gerektirdiğinden, bu nedenle dolar maliyeti daha yüksektir. Ayrıca, daha karmaşık bir iş akışı da içerir.

## <a name="summary"></a>Özet

Makalede açıklanan iki yükseltme yöntemi karmaşıklık ve dolar maliyetinde farklılık gösterir, ancak her ikisi de kullanıcının salt okuma işlemlerine ne kadar süreyle sınırlı olduğunu en aza indirir. Bu süre, yükseltme betiği süresince doğrudan tanımlanır. Bu, veritabanı boyutuna, seçtiğiniz hizmet katmanına, Web sitesi yapılandırmasına veya kolayca denetleyemeyen diğer faktörlere bağlı değildir. Tüm hazırlık adımları yükseltme adımlarından ayrılır ve üretim uygulamasını etkilemez. Yükseltme betiğinin verimliliği, yükseltmeler sırasında kullanıcı deneyimini belirleyen bir temel faktördür. Bu nedenle, bu deneyimi geliştirmenin en iyi yolu, yükseltme betiğini mümkün olduğunca verimli hale getirmek için çabalarınızı odaklamaktır.

## <a name="next-steps"></a>Sonraki adımlar

* İş sürekliliği için genel bakış ve senaryolar için bkz. [iş sürekliliği genel bakış](sql-database-business-continuity.md).
* Azure SQL veritabanı etkin coğrafi çoğaltma hakkında bilgi edinmek için bkz. [etkin coğrafi çoğaltma kullanarak okunabilir ikincil veritabanları oluşturma](sql-database-active-geo-replication.md).
* Azure SQL veritabanı otomatik yük devretme grupları hakkında bilgi edinmek için bkz. [birden çok veritabanının saydam ve eşgüdümlü yük devretmesini etkinleştirmek için otomatik yük devretme gruplarını kullanma](sql-database-auto-failover-group.md).
* Azure App Service ' deki hazırlama ortamları hakkında bilgi edinmek için bkz. [Azure App Service hazırlama ortamlarını ayarlama](../app-service/deploy-staging-slots.md).
* Azure Traffic Manager profilleri hakkında bilgi edinmek için bkz. [azure Traffic Manager profilini yönetme](../traffic-manager/traffic-manager-manage-profiles.md).

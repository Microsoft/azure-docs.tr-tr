---
title: Otomatik yük devretme grupları
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Otomatik yük devretme grupları, bir sunucudaki bir veritabanı grubunun veya yönetilen bir örnekteki tüm veritabanlarının çoğaltmasını ve otomatik/Eşgüdümlü yük devretmesini yönetmenizi sağlar.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 03/26/2021
ms.openlocfilehash: f3bc1dfcfeeb6dda110f71ed7a1c53909153cf00
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762164"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Birden çok veritabanının saydam ve koordine edilmiş yük devretmesini etkinleştirmek için otomatik yük devretme gruplarını kullanın
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Otomatik yük devretme grupları özelliği, bir sunucudaki bir veritabanı grubunun veya yönetilen bir örnekteki tüm veritabanlarının çoğaltmasını ve başka bir bölgeye yük devretmesini yönetmenizi sağlar. Bu, var olan [etkin coğrafi çoğaltma](active-geo-replication-overview.md) özelliğinin en üstünde, coğrafi olarak çoğaltılan veritabanlarının dağıtım ve yönetimini kolaylaştırmak için tasarlanan, bildirime dayalı bir soyutlamadır. Yük devretmeyi el ile başlatabilir veya Kullanıcı tanımlı bir ilkeye göre Azure hizmetine temsilci seçebilirsiniz. İkinci seçenek, birincil bölgede SQL veritabanının veya SQL yönetilen örnek kullanılabilirliğinin tamamen veya kısmen kaybedilmesiyle sonuçlanan çok önemli bir hatadan veya diğer plansız bir olaydan sonra, ikincil bölgede birden fazla ilişkili veritabanını otomatik olarak kurtarmanıza olanak sağlar. Yük devretme grubu, genellikle aynı uygulama tarafından kullanılan bir veya daha fazla veritabanı içerebilir. Ayrıca, salt okunur sorgu iş yüklerini boşaltmak için okunabilir ikincil veritabanlarını da kullanabilirsiniz. Otomatik yük devretme grupları birden çok veritabanı içerdiğinden, bu veritabanlarının birincil sunucuda yapılandırılması gerekir. Otomatik yük devretme grupları gruptaki tüm veritabanlarının, farklı bir bölgedeki yalnızca bir ikincil sunucuya veya örneğe çoğaltılmasını destekler.

> [!NOTE]
> Aynı veya farklı bölgelerde birden çok Azure SQL veritabanı ikincil öğesini isterseniz, [etkin coğrafi çoğaltma](active-geo-replication-overview.md)kullanın.

Otomatik yük devretme grupları otomatik yük devretme ilkesiyle kullanılırken, gruptaki bir veya birkaç veritabanını etkileyen herhangi bir kesinti otomatik yük devretmeye neden olur. Bunlar genellikle yerleşik otomatik yüksek kullanılabilirlik işlemleriyle kendini hafiflede azallamayan olaylardır. Yük devretme tetikleyicilerinin örnekleri, birkaç işlem düğümündeki bir işletim sistemi çekirdek belleği sızıntısı nedeniyle bir SQL veritabanı Kiracı halkasının veya denetim halkasının bir olay içermesi veya bir veya daha fazla kiracı halkasından kaynaklanan bir olayın, olağan donanım yük devretmesi sırasında yanlış bir ağ kablosu kesildiğinden dolayı bir olay içerir.  Daha fazla bilgi için bkz. [SQL veritabanı yüksek kullanılabilirlik](high-availability-sla.md).

Bunlara ek olarak, otomatik yük devretme grupları, yük devretme sırasında değişmeden kalan okuma/yazma ve salt okuma dinleyicisi uç noktaları sağlar. El ile veya otomatik yük devretme etkinleştirme kullanmanıza bakılmaksızın, yük devretme gruptaki tüm ikincil veritabanlarını birinciye geçirir. Veritabanı yük devretmesi tamamlandıktan sonra DNS kaydı, uç noktaları yeni bölgeye yönlendirmek üzere otomatik olarak güncelleştirilir. Belirli RPO ve RTO verileri için bkz. [Iş sürekliliği 'Ne genel bakış](business-continuity-high-availability-disaster-recover-hadr-overview.md).

Otomatik yük devretme grupları otomatik yük devretme ilkesiyle kullanılırken, bir sunucu veya yönetilen örnek üzerinde veritabanlarını etkileyen herhangi bir kesinti otomatik yük devretmeye neden olur. Şunu kullanarak otomatik yük devretme grubunu yönetebilirsiniz:

- [Azure portalı](geo-distributed-application-configure-tutorial.md)
- [Azure CLı: yük devretme grubu](scripts/add-database-to-failover-group-cli.md)
- [PowerShell: yük devretme grubu](scripts/add-database-to-failover-group-powershell.md)
- [REST API: yük devretme grubu](/rest/api/sql/failovergroups)

Yük devretmeden sonra veritabanınıza ve sunucunuza yönelik kimlik doğrulama gereksinimlerinin veya örneğin yeni birincil üzerinde yapılandırılmış olduğundan emin olun. Ayrıntılar için bkz. [olağanüstü durum kurtarma sonrasında SQL veritabanı güvenliği](active-geo-replication-security-configure.md).

Gerçek iş sürekliliği sağlamak için, veri merkezleri arasında veritabanı yedekliliği eklemek çözümün yalnızca bir parçasıdır. Bir uygulamayı (hizmet) çok zararlı bir hatadan sonra kurtarmak, hizmeti ve bağımlı hizmetleri oluşturan tüm bileşenlerin kurtarılmasını gerektirir. Bu bileşenlere örnek olarak, istemci yazılımı (örneğin, özel JavaScript içeren bir tarayıcı), Web ön uçları, depolama alanı ve DNS sayılabilir. Tüm bileşenlerin aynı hatalara dayanıklı olması ve uygulamanızın kurtarma süresi hedefi (RTO) içinde kullanılabilir olması önemlidir. Bu nedenle, tüm bağımlı hizmetleri belirlemeniz ve sağladıkları garantileri ve özellikleri anlamanız gerekir. Daha sonra, hizmetin bağımlı olduğu hizmetlerin yük devretmesi sırasında işlevlerinizin çalıştığından emin olmak için yeterli adımları uygulamanız gerekir. Olağanüstü durum kurtarma çözümleri tasarlama hakkında daha fazla bilgi için bkz. [etkin coğrafi çoğaltma kullanarak olağanüstü durum kurtarma Için bulut çözümleri tasarlama](designing-cloud-solutions-for-disaster-recovery.md).

## <a name="terminology-and-capabilities"></a>Terminoloji ve yetenekler

- **Yük devretme grubu (FOG)**

  Yük devretme grubu, tek bir sunucu tarafından yönetilen adlandırılmış veritabanları grubudur veya bir yönetilen örnek içinde, birincil bölgedeki bir kesinti nedeniyle tüm veya bazı birincil veritabanlarının kullanılamaz hale gelmesi durumunda başka bir bölgeye bir birim olarak yük devredebilirler. SQL yönetilen örneği için oluşturulduğunda, bir yük devretme grubu örnekteki tüm Kullanıcı veritabanlarını içerir ve bu nedenle bir örnek üzerinde yalnızca bir yük devretme grubu yapılandırılabilir.
  
  > [!IMPORTANT]
  > Yük devretme grubunun adı etki alanı içinde genel olarak benzersiz olmalıdır `.database.windows.net` .

- **Sunucular**

     Sunucularla, bir sunucudaki kullanıcı veritabanlarının bazıları veya tümü bir yük devretme grubuna yerleştirilebilir. Ayrıca, bir sunucu tek bir sunucuda birden çok yük devretme grubunu destekler.

- **Birincil**

  Yük devretme grubundaki birincil veritabanlarını barındıran sunucu veya yönetilen örnek.

- **İkincil**

  Yük devretme grubundaki ikincil veritabanlarını barındıran sunucu veya yönetilen örnek. İkincil, birincil ile aynı bölgede olamaz.

- **Yük devretme grubuna tek veritabanları ekleme**

  Aynı sunucuya aynı yük devretme grubuna birkaç tek veritabanı yerleştirebilirsiniz. Yük devretme grubuna tek bir veritabanı eklerseniz, ikincil sunucuda aynı sürümü ve işlem boyutunu kullanarak otomatik olarak ikincil bir veritabanı oluşturur.  Yük devretme grubu oluşturulduğunda bu sunucuyu belirttiniz. İkincil sunucuda zaten ikincil bir veritabanına sahip olan bir veritabanını eklerseniz, bu coğrafi çoğaltma bağlantısı Grup tarafından devralınır. Yük devretme grubunun parçası olmayan bir sunucuda zaten ikincil veritabanına sahip bir veritabanı eklediğinizde, ikincil sunucuda yeni bir ikincil oluşturulur.

  > [!IMPORTANT]
  > İkincil sunucuda, var olan bir ikincil veritabanı olmadığı müddetçe aynı ada sahip bir veritabanı bulunmadığından emin olun. SQL yönetilen örneği için yük devretme gruplarında, tüm kullanıcı veritabanları çoğaltılır. Yük devretme grubundaki çoğaltma için kullanıcı veritabanlarının bir alt kümesini seçemezsiniz.

- **Elastik havuzdaki veritabanlarını yük devretme grubuna ekleme**

  Elastik havuz içindeki tüm veya birkaç veritabanını aynı yük devretme grubuna yerleştirebilirsiniz. Birincil veritabanı elastik bir havuzda ise, ikincil havuz aynı ada (ikincil havuz) sahip esnek havuzda otomatik olarak oluşturulur. İkincil sunucunun, yük devretme grubu tarafından oluşturulacak ikincil veritabanlarını barındırmak için aynı tam adı ve yeterli boş kapasiteye sahip esnek bir havuz içerdiğinden emin olmanız gerekir. Havuza ikincil havuzda zaten ikincil bir veritabanı olan bir veritabanı eklerseniz, bu coğrafi çoğaltma bağlantısı Grup tarafından devralınır. Yük devretme grubunun parçası olmayan bir sunucuda zaten ikincil veritabanına sahip bir veritabanı eklediğinizde ikincil havuzda yeni bir ikincil oluşturulur.
  
- **İlk dengeli dağıtım**

  Veritabanları, elastik havuzlar veya yönetilen örnekleri bir yük devretme grubuna eklerken, veri çoğaltma başlamadan önce bir ilk dengeli dağıtım aşaması vardır. İlk dengeli dağıtım aşaması en uzun ve en pahalı işlemdir. İlk dengeli dağıtım tamamlandıktan sonra veriler eşitlenir ve ardından yalnızca sonraki veri değişiklikleri çoğaltılır. İlk çekirdek işleminin tamamlaması için gereken süre, verilerinizin boyutuna, çoğaltılan veritabanlarının sayısına ve yük devretme grubundaki varlıklar arasındaki bağlantının hızına bağlıdır. Normal koşullarda, olası dengeli dağıtım hızı SQL veritabanı için 500 GB ve SQL yönetilen örneği için 360 GB 'a kadar bir saate kadar sürebilir. Dengeli dağıtım, tüm veritabanları için paralel olarak gerçekleştirilir.

  SQL yönetilen örneği için, ilk dengeli dağıtım aşamasının süresini tahmin eden iki örnek arasındaki hızlı rota bağlantısının hızını göz önünde bulundurun. İki örnek arasındaki bağlantının hızı gerekenden daha yavaşsa, tohum süresi büyük olasılıkla özellikle etkilendi. Veri çoğaltma başlamadan önce ilk dengeli dağıtım aşamasının ne kadar süreyle yapılacağını tahmin etmek için belirtilen dengeli dağıtım hızını, veritabanı sayısını, toplam veri boyutunu ve bağlantı hızını kullanabilirsiniz. Örneğin, tek bir 100 GB veritabanı için, bağlantının saat başına 84 GB 'a itilebilir olması ve daha önce oluşturulan başka veritabanları olmaması durumunda ilk Çekirdek aşaması yaklaşık 1,2 saat sürer. Bağlantı yalnızca saat başına 10 GB aktarabiliyorsanız, 100 GB 'lik bir veritabanının dağıtımı, yaklaşık 10 saat sürer. Çoğaltılacak birden çok veritabanı varsa, dağıtım paralel olarak yürütülür ve yavaş bir bağlantı hızıyla birleştirildiğinde, özellikle tüm veritabanlarındaki verilerin paralel dengeli dağıtımı kullanılabilir bağlantı bant genişliğini aşarsa, ilk dengeli dağıtım aşaması oldukça uzun sürebilir. İki örnek arasındaki ağ bant genişliği sınırlıysa ve bir yük devretme grubuna birden çok yönetilen örnek ekliyorsanız, yük devretme grubuna birden çok yönetilen örnek eklemeyi göz önünde bulundurun. İki yönetilen örnek arasında uygun boyutta bir ağ geçidi SKU 'SU veriliyorsa ve kurumsal ağ bant genişliği buna izin veriyorsa, hızlara 360 GB kadar yüksek bir saat elde etmek mümkündür.  

- **DNS bölgesi**

  Yeni bir SQL yönetilen örneği oluşturulduğunda otomatik olarak oluşturulan benzersiz bir KIMLIK. Aynı DNS bölgesindeki herhangi bir örneğe istemci bağlantılarının kimliğini doğrulamak için bu örnek için bir çoklu etki alanı (SAN) sertifikası sağlanır. Aynı yük devretme grubundaki iki yönetilen örnek, DNS bölgesini paylaşmalıdır.
  
  > [!NOTE]
  > SQL veritabanı için oluşturulan yük devretme grupları için bir DNS bölge KIMLIĞI gerekli değildir.

- **Yük devretme grubu okuma-yazma dinleyicisi**

  Geçerli birincil URL 'yi işaret eden bir DNS CNAME kaydı. Yük devretme grubu oluşturulduğunda otomatik olarak oluşturulur ve yük devretme sonrasında birincil değişiklikler olan okuma/yazma iş yükünün birincil veritabanına şeffaf bir şekilde yeniden bağlanmasına izin verir. Yük devretme grubu bir sunucuda oluşturulduğunda, dinleyici URL 'SI için DNS CNAME kaydı olarak biçimlendirilir `<fog-name>.database.windows.net` . Yük devretme grubu bir SQL yönetilen örneği üzerinde oluşturulduğunda, dinleyici URL 'SI için DNS CNAME kaydı olarak biçimlendirilir `<fog-name>.<zone_id>.database.windows.net` .

- **Yük devretme grubu salt okuma dinleyicisi**

  İkincil URL 'yi işaret eden salt okunurdur dinleyiciyi işaret eden bir DNS CNAME kaydı. Yük devretme grubu oluşturulduğunda otomatik olarak oluşturulur ve salt okunurdur SQL iş yükünün belirtilen Yük Dengeleme kurallarını kullanarak ikincil sunucuya şeffaf bir şekilde bağlanmasına izin verir. Yük devretme grubu bir sunucuda oluşturulduğunda, dinleyici URL 'SI için DNS CNAME kaydı olarak biçimlendirilir `<fog-name>.secondary.database.windows.net` . Yük devretme grubu bir SQL yönetilen örneği üzerinde oluşturulduğunda, dinleyici URL 'SI için DNS CNAME kaydı olarak biçimlendirilir `<fog-name>.secondary.<zone_id>.database.windows.net` .

- **Otomatik yük devretme ilkesi**

  Varsayılan olarak, bir yük devretme grubu otomatik yük devretme ilkesiyle yapılandırılır. Hata algılandıktan ve yetkisiz kullanım süresi dolduktan sonra Azure yük devretmeyi tetikler. Sistemin, etkinin ölçeklendirilmesi nedeniyle, yerleşik [yüksek kullanılabilirlik altyapısı](high-availability-sla.md) tarafından kesinti azalmasının azaltıldığından emin olmanız gerekir. Yük devretme iş akışını uygulamadan veya el ile denetlemek istiyorsanız otomatik yük devretmeyi devre dışı bırakabilirsiniz.
  
  > [!NOTE]
  > Kesinti ölçeğinde ve ne kadar hızlı bir şekilde azalmayabileceğini doğrulamak için, işlem ekibine yetkisiz kullanım süresi bir saat altına ayarlanamaz. Bu sınırlama, veri eşitleme durumu ne olursa olsun, yük devretme grubundaki tüm veritabanları için geçerlidir.

- **Salt okuma yük devretme ilkesi**

  Varsayılan olarak, salt okunurdur dinleyicinin yük devretmesi devre dışıdır. İkincil çevrimdışıyken, birincil performans performansının etkilenmemesini sağlar. Bununla birlikte, ikincil kurtarılana kadar salt okuma oturumlarının bağlanamadığı anlamına gelir. Salt okuma oturumları için kapalı kalma süresini kabul edebiliyorsanız ve birincil ' ı salt okunurdur ve okuma/yazma trafiği için birincil olarak kullanabilir, özelliği yapılandırarak salt okuma dinleyicisi için yük devretmeyi etkinleştirebilirsiniz `AllowReadOnlyFailoverToPrimary` . Bu durumda, ikincil kullanılabilir değilse salt okuma trafiği otomatik olarak birincil olarak yönlendirilir.

  > [!NOTE]
  > `AllowReadOnlyFailoverToPrimary`Özelliği yalnızca otomatik yük devretme ilkesi etkinse ve Azure tarafından otomatik yük devretme tetikleniyorsa etkilidir. Bu durumda, özelliği true olarak ayarlanırsa, yeni birincil her ikisi de okuma-yazma ve salt okuma oturumlarına sahip olur.

- **Planlı yük devretme**

   Planlı Yük devretme, birincil ve ikincil veritabanları arasında birincil role kadar olan tam eşitleme gerçekleştirir. Bu, veri kaybı garantisi vermez. Planlı Yük devretme aşağıdaki senaryolarda kullanılır:

  - Veri kaybı kabul edilebilir olmadığında üretimde olağanüstü durum kurtarma (DR) tatbilar gerçekleştirme
  - Veritabanlarını farklı bir bölgeye yeniden konumlandırma
  - Kesinti azaltıldıktan sonra veritabanlarını birincil bölgeye döndürün (yeniden çalışma)

- **Planlanmamış yük devretme**

   Planlanmamış veya zorlamalı yük devretme, birincili eşitleme yapmadan ikincili doğrudan birincil role geçirir. Bu işlem, veri kaybına neden olur. Planlanmamış yük devretme, birincil erişim olmadığında kesintiler sırasında kurtarma yöntemi olarak kullanılır. Özgün birincil yeniden çevrimiçi olduğunda, eşitleme olmadan otomatik olarak yeniden bağlanır ve yeni bir ikincil hale gelir.

- **El ile yük devretme**

  Otomatik yük devretme yapılandırması ne olursa olsun, yük devretmeyi dilediğiniz zaman el ile başlatabilirsiniz. Otomatik yük devretme ilkesi yapılandırılmamışsa, yük devretme grubundaki veritabanlarını ikincil gruba kurtarmak için el ile yük devretme gerekir. Zorunlu veya kolay yük devretme (tam veri eşitlemeyle) başlatabilirsiniz. İkincisi ikincil bölgeye yeniden konumlandırmak için kullanılabilir. Yük devretme tamamlandığında, DNS kayıtları, yeni birincili bağlantıyı sağlamak üzere otomatik olarak güncelleştirilir.

- **Veri kaybı olan yetkisiz kullanım süresi**

  Birincil ve ikincil veritabanları zaman uyumsuz çoğaltma kullanılarak eşitlendiğinden, yük devretme veri kaybına neden olabilir. Otomatik yük devretme ilkesini, uygulamanızın veri kaybına karşı dayanıklılığını yansıtacak şekilde özelleştirebilirsiniz. Yapılandırarak `GracePeriodWithDataLossHours` , sistemin, veri kaybına neden olma olasılığı olan yük devretmeyi başlatmadan önce ne kadar bekleyeceğini kontrol edebilirsiniz.

- **Çoklu yük devretme grupları**

  Yük devretmeler kapsamını denetlemek için aynı sunucu çifti için birden çok yük devretme grubu yapılandırabilirsiniz. Her grup bağımsız olarak devredildi. Çok kiracılı uygulamanız elastik havuzlar kullanıyorsa, bu özelliği kullanarak her havuzda birincil ve ikincil veritabanlarını karıştırabilirsiniz. Bu şekilde, bir kesinti etkisini yalnızca kiracıların yarısını azaltabilirsiniz.

  > [!NOTE]
  > SQL yönetilen örneği birden çok yük devretme grubunu desteklemiyor.
  
## <a name="permissions"></a>İzinler

Yük devretme grubu izinleri [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md)aracılığıyla yönetilir. [SQL Server katkıda](../../role-based-access-control/built-in-roles.md#sql-server-contributor) bulunan rolü, yük devretme gruplarını yönetmek için gereken tüm izinlere sahiptir.

### <a name="create-failover-group"></a>Yük devretme grubu oluştur

Bir yük devretme grubu oluşturmak için, hem birincil hem de ikincil sunuculara ve yük devretme grubundaki tüm veritabanlarına Azure RBAC yazma erişimine sahip olmanız gerekir. SQL yönetilen örneği için, hem birincil hem de ikincil SQL yönetilen örneği için Azure RBAC yazma erişimine ihtiyacınız vardır, ancak ayrı ayrı SQL yönetilen örnek veritabanları bir yük devretme grubuna eklenemediği veya kaldırılamadığı için bağımsız veritabanlarındaki izinler ilgili değildir.

### <a name="update-a-failover-group"></a>Yük devretme grubunu güncelleştirme

Bir yük devretme grubunu güncelleştirmek için, yük devretme grubuna ve geçerli birincil sunucu veya yönetilen örnekteki tüm veritabanlarına Azure RBAC yazma erişimi gerekir.  

### <a name="fail-over-a-failover-group"></a>Yük devretme grubu yükünü devreder

Yük devretme grubu yükünü devretmek için, yeni birincil sunucu veya yönetilen örnekteki yük devretme grubuna Azure RBAC yazma erişimine sahip olmanız gerekir.

## <a name="best-practices-for-sql-database"></a>SQL veritabanı için en iyi uygulamalar

Otomatik yük devretme grubu birincil sunucuda yapılandırılmalı ve bunu farklı bir Azure bölgesindeki ikincil sunucuya bağlayacaktır. Gruplar, bu sunuculardaki tüm veya bazı veritabanlarını içerebilir. Aşağıdaki diyagramda birden çok veritabanı ve otomatik yük devretme grubu kullanılarak coğrafi olarak yedekli bir bulut uygulamasının tipik bir yapılandırması gösterilmektedir.

![Diyagramda, coğrafi olarak yedekli bir bulut uygulamasının birden çok veritabanı ve otomatik yük devretme grubu kullanılarak tipik bir yapılandırması gösterilmektedir.](./media/auto-failover-group-overview/auto-failover-group.png)

> [!NOTE]
> Bkz. SQL veritabanı 'nda bir yük devretme grubuna veritabanı ekleme hakkında ayrıntılı adım adım öğretici için bkz. [SQL veritabanı ekleme](failover-group-add-single-database-tutorial.md) .

İş sürekliliği ile bir hizmet tasarlarken aşağıdaki genel yönergeleri izleyin:

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>Birden çok veritabanının yük devretmesini yönetmek için bir veya birkaç yük devretme grubu kullanma

Farklı bölgelerde (birincil ve ikincil sunucular) iki sunucu arasında bir veya daha fazla yük devretme grubu oluşturulabilir. Her grup, birincil bölgedeki bir kesinti nedeniyle tüm veya bazı birincil veritabanlarının kullanılamaz duruma gelmesi durumunda birim olarak kurtarılan bir veya birkaç veritabanı içerebilir. Yük devretme grubu, birincil ile aynı hizmet hedefine sahip coğrafi ikincil veritabanı oluşturur. Yük devretme grubuna mevcut bir coğrafi çoğaltma ilişkisi eklerseniz, coğrafi ikincil öğenin aynı hizmet katmanıyla ve işlem boyutuyla birincil olarak yapılandırıldığından emin olun.
  
> [!IMPORTANT]
> Farklı aboneliklerde iki sunucu arasında yük devretme gruplarının oluşturulması Şu anda Azure SQL veritabanı için desteklenmiyor. Yük devretme grubu oluşturulduktan sonra birincil veya ikincil sunucuyu farklı bir aboneliğe taşırsanız, yük devretme istekleri ve diğer işlemler hatalara neden olabilir.

### <a name="using-read-write-listener-for-oltp-workload"></a>OLTP iş yükü için okuma-yazma dinleyicisi kullanma

OLTP işlemlerini gerçekleştirirken `<fog-name>.database.windows.net` sunucu URL 'si olarak kullanın ve bağlantılar otomatik olarak birincil ağa yönlendirilir. Bu URL, yük devretmeden sonra değişmez. Bunun için yük devretme, DNS kaydının güncelleştirilmesini içerir, böylece istemci bağlantıları yalnızca istemci DNS önbelleği yenilendikten sonra yeni birincil yere yönlendirilir.

### <a name="using-read-only-listener-for-read-only-workload"></a>Salt okunurdur iş yükü için salt okunurdur dinleyicisi kullanma

Verilerin belirli bir şekilde kullanılması için dayanıklı bir mantıksal olarak yalıtılmış salt okunurdur, uygulamadaki ikincil veritabanını kullanabilirsiniz. Salt okuma oturumları için `<fog-name>.secondary.database.windows.net` sunucu URL 'si olarak kullanın ve bağlantı otomatik olarak ikinciye yönlendirilir. Ayrıca, kullanarak bağlantı dizesinde oku hedefini belirtmeniz önerilir `ApplicationIntent=ReadOnly` .

> [!NOTE]
> Premium, İş Açısından Kritik ve hiper ölçekli hizmet katmanlarında SQL veritabanı, bağlantı dizesindeki parametresini kullanarak salt okuma sorgu iş yüklerini boşaltmak için [salt okuma çoğaltmalarının](read-scale-out.md) kullanımını destekler `ApplicationIntent=ReadOnly` . Coğrafi olarak çoğaltılan ikincil örneği yapılandırdığınızda bu özelliği kullanarak birincil konumdaki veya coğrafi olarak çoğaltılan konumdaki bir salt okuma çoğaltmasına bağlanabilirsiniz.
>
> - Birincil konumdaki bir salt okuma çoğaltmasına bağlanmak için `ApplicationIntent=ReadOnly` ve kullanın `<fog-name>.database.windows.net` .
> - İkincil konumdaki bir salt okuma çoğaltmasına bağlanmak için `ApplicationIntent=ReadOnly` ve kullanın `<fog-name>.secondary.database.windows.net` .

### <a name="preparing-for-performance-degradation"></a>Performans düşüşü için hazırlanma

Tipik bir Azure uygulaması birden çok Azure hizmeti kullanır ve birden çok bileşenden oluşur. Yük devretme grubunun otomatik yük devretmesi, Azure SQL bileşenleri yalnızca durum temelinde tetiklenir. Birincil bölgedeki diğer Azure hizmetleri kesinti tarafından etkilenmeyebilir ve bileşenleri bu bölgede kullanılabilir olmaya devam edebilir. Birincil veritabanları DR bölgesine geçiş yaptıktan sonra, bağımlı bileşenler arasındaki gecikme artabilir. Uygulamanın performansına yönelik daha yüksek gecikme süresini önlemek için tüm uygulama bileşenlerinin DR bölgesindeki yedekliliği olduğundan emin olun ve bu [ağ güvenlik yönergelerini](#failover-groups-and-network-security)izleyin.

### <a name="preparing-for-data-loss"></a>Veri kaybı için hazırlanma

Bir kesinti algılanırsa, Azure tarafından belirttiğiniz dönem için bekler `GracePeriodWithDataLossHours` . Varsayılan değer 1 saattir. Veri kaybını bilmiyorsanız, `GracePeriodWithDataLossHours` 24 saat gibi yeterince büyük bir sayıya ayarladığınızdan emin olun. İkincil sunucudan birinciye yeniden yük devretmek için el ile grup yük devretmesini kullanın.

> [!IMPORTANT]
> 800 veya daha az DTU ile esnek havuzlar ve coğrafi çoğaltma kullanan 250 ' den fazla veritabanı, daha uzun planlı yük devretme ve performans düşüklükiyle ilgili sorunlarla karşılaşabilir.  Bu sorunların, yazma yoğunluklu iş yükleri, coğrafi çoğaltma uç noktaları Coğrafya tarafından yaygın olarak ayrıldığı veya her veritabanı için birden çok ikincil uç nokta kullanıldığı durumlarda oluşma olasılığı daha yüksektir.  Bu sorunların belirtileri, coğrafi çoğaltma gecikmesi zaman içinde arttıkça belirtilir.  Bu gecikme, [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database)kullanılarak izlenebilir.  Bu sorunlar oluşursa, azaltmaları, havuz DTU sayısını artırmayı veya aynı havuzdaki coğrafi çoğaltılan veritabanlarının sayısını azaltmayı içerir.

### <a name="changing-secondary-region-of-the-failover-group"></a>Yük devretme grubunun ikincil bölgesini değiştirme

Değişiklik sırasını göstermek için sunucu A 'nın birincil sunucu olduğunu, sunucu B 'nin var olan ikincil sunucu olduğunu ve sunucu C 'nin üçüncü bölgedeki yeni ikincil olduğunu varsayacağız.  Geçişi yapmak için şu adımları izleyin:

1. [Etkin coğrafi çoğaltma](active-geo-replication-overview.md)kullanarak her bir veritabanı Için Sunucu A, sunucu C üzerinde ek ikincil sunucular oluşturun. Sunucu A 'daki her veritabanı, biri sunucu B 'de diğeri sunucu C 'de olmak üzere iki ikincil adına sahip olur. Bu, geçiş sırasında birincil veritabanlarının korunduğundan emin olmaya devam edecektir.
2. Yük devretme grubunu silin. Bu noktada, oturum açma işlemleri başarısız olur. Bunun nedeni, yük devretme grubu dinleyicilerinin SQL diğer adlarının silindiği ve ağ geçidinin yük devretme grubu adını tanıyamayacağı içindir.
3. Yük devretme grubunu, A ve C sunucuları arasında aynı adla yeniden oluşturun. Bu noktada, oturum açmalar başarısız olur.
4. A sunucusundaki tüm birincil veritabanlarını yeni yük devretme grubuna ekleyin.
5. Sunucu B 'yi bırakın. B üzerindeki tüm veritabanları otomatik olarak silinecek.

### <a name="changing-primary-region-of-the-failover-group"></a>Yük devretme grubunun birincil bölgesi değiştiriliyor

Değişiklik sırasını göstermek için sunucu A 'nın birincil sunucu olduğunu, sunucu B 'nin var olan ikincil sunucuyu ve sunucu C 'nin, üçüncü bölgedeki yeni birincidir olduğunu varsayacağız.  Geçişi yapmak için şu adımları izleyin:

1. Birincil sunucuyu B 'ye geçirmek için planlı bir yük devretme gerçekleştirin. Sunucu A, yeni ikincil sunucu olacak. Yük devretme işlemi birkaç dakika kapalı kalma süresine neden olabilir. Gerçek süre, yük devretme grubunun boyutuna bağlı olacaktır.
2. [Etkin coğrafi çoğaltma](active-geo-replication-overview.md)kullanarak sunucu B 'de sunucu C 'ye her bir veritabanının ek ikincil sürümlerini oluşturun. Sunucu B üzerindeki her bir veritabanı, biri sunucu A ve sunucu C 'de olmak üzere iki ikincil adına sahip olur. Bu, geçiş sırasında birincil veritabanlarının korunduğundan emin olmaya devam edecektir.
3. Yük devretme grubunu silin. Bu noktada, oturum açma işlemleri başarısız olur. Bunun nedeni, yük devretme grubu dinleyicilerinin SQL diğer adlarının silindiği ve ağ geçidinin yük devretme grubu adını tanıyamayacağı içindir.
4. B ve C sunucuları arasında aynı ada sahip yük devretme grubunu yeniden oluşturun. Bu noktada, oturum açmalar başarısız olur.
5. B üzerindeki tüm birincil veritabanlarını yeni yük devretme grubuna ekleyin.
6. B ve C 'ye geçiş yapmak için yük devretme grubunun planlı bir yük devretmesini gerçekleştirin. Artık sunucu C birincil ve B-ikincil olur. A sunucusundaki tüm ikincil veritabanları, C 'deki temelinde otomatik olarak bağlanır. Adım 1 ' de olduğu gibi, yük devretme işlemi birkaç dakika kapalı kalma süresine yol açabilir.
7. Sunucu A ' yı bırakın. İçindeki tüm veritabanları otomatik olarak silinir.

> [!IMPORTANT]
> Yük devretme grubu silindiğinde, dinleyici uç noktaları için DNS kayıtları da silinir. Bu noktada, başka birinin bir yük devretme grubu veya aynı ada sahip sunucu diğer adı oluştururken, onu yeniden kullanmanızı önleyen sıfır olmayan bir olasılık vardır. Riski en aza indirmek için genel yük devretme grubu adlarını kullanmayın.

## <a name="best-practices-for-sql-managed-instance"></a>SQL yönetilen örneği için en iyi uygulamalar

Otomatik yük devretme grubu birincil örnekte yapılandırılmalıdır ve onu farklı bir Azure bölgesinde yer alan ikincil örneğe bağlar.  Örnekteki tüm veritabanları ikincil örneğe çoğaltılır.

Aşağıdaki diyagramda, yönetilen örnek ve otomatik yük devretme grubu kullanılarak coğrafi olarak yedekli bir bulut uygulamasının tipik bir yapılandırması gösterilmektedir.

![otomatik yük devretme diyagramı](./media/auto-failover-group-overview/auto-failover-group-mi.png)

> [!NOTE]
> Yük devretme grubu kullanmak için bir SQL yönetilen örneği ekleme hakkında ayrıntılı adım adım öğretici için [Yük devretme grubuna yönetilen örnek ekleme](../managed-instance/failover-group-add-instance-tutorial.md) konusuna bakın.

Uygulamanız veri katmanı olarak SQL yönetilen örneği kullanıyorsa, iş sürekliliği için tasarlarken aşağıdaki genel yönergeleri izleyin:

### <a name="creating-the-secondary-instance"></a>İkincil örnek oluşturma

Yük devretmeden sonra birincil SQL yönetilen örneğine kesintiye uğramayan bağlantıyı sağlamak için hem birincil hem de ikincil örneklerin aynı DNS bölgesinde olması gerekir. Aynı çoklu etki alanı (SAN) sertifikasının, yük devretme grubundaki iki örneklerden birine yönelik istemci bağlantılarının kimliğini doğrulamak için kullanılabilir olmasını garanti eder. Uygulamanız üretim dağıtımı için hazırsanız, farklı bir bölgede ikincil bir SQL yönetilen örneği oluşturun ve DNS bölgesini birincil SQL yönetilen örneğiyle paylaştığından emin olun. Oluşturma sırasında isteğe bağlı parametreyi belirterek bunu yapabilirsiniz. PowerShell veya REST API kullanıyorsanız, isteğe bağlı parametresinin adı `DNS Zone Partner` ve Azure Portal ilgili isteğe bağlı alanın adı birincil yönetilen örneğidir.

> [!IMPORTANT]
> Alt ağda oluşturulan ilk yönetilen örnek, aynı alt ağdaki sonraki tüm örnekler için DNS bölgesini belirler. Diğer bir deyişle, aynı alt ağdaki iki örnek farklı DNS bölgelerine ait olamaz.

İkincil SQL yönetilen örneğini birincil örnekle aynı DNS bölgesinde oluşturma hakkında daha fazla bilgi için bkz. [İkincil yönetilen örnek oluşturma](../managed-instance/failover-group-add-instance-tutorial.md#create-a-secondary-managed-instance).

### <a name="using-geo-paired-regions"></a>Coğrafi olarak eşleştirilmiş bölgeleri kullanma

Performans nedenleriyle, [eşleştirilmiş bölgelere](../../best-practices-availability-paired-regions.md) her iki yönetilen örneği de dağıtın. Coğrafi olarak eşleştirilmiş bölgelerde bulunan yönetilen örneklerin eşleştirildiği bölgelere kıyasla çok daha iyi bir performansı vardır. 

### <a name="enabling-replication-traffic-between-two-instances"></a>İki örnek arasında çoğaltma trafiği etkinleştiriliyor

Her örnek kendi VNet 'inde yalıtılmış olduğundan, bu VNET 'ler arasındaki iki yönlü trafiğe izin verilmelidir. Bkz. [Azure VPN ağ geçidi](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>Farklı aboneliklerdeki yönetilen örnekler arasında yük devretme grubu oluşturma

Abonelikler aynı [Azure Active Directory kiracısıyla](../../active-directory/fundamentals/active-directory-whatis.md#terminology)ilişkili olduğu sürece, iki farklı abonelikteki SQL yönetilen örnekleri arasında bir yük devretme grubu oluşturabilirsiniz. PowerShell API 'sini kullanırken, `PartnerSubscriptionId` IKINCIL SQL yönetilen örneği için parametresini belirterek bunu yapabilirsiniz. REST API kullanırken, parametreye dahil edilen her örnek KIMLIĞI `properties.managedInstancePairs` kendi SubscriptionID değerine sahip olabilir.
  
> [!IMPORTANT]
> Azure portal, farklı aboneliklerde yük devretme gruplarının oluşturulmasını desteklemez. Ayrıca, farklı abonelikler ve/veya kaynak gruplarındaki mevcut yük devretme grupları için yük devretme, birincil SQL yönetilen örneğinden Portal aracılığıyla el ile başlatılamaz. Bunun yerine yük devretmeyi coğrafi olarak ikincil örnekten başlatın.

### <a name="managing-failover-to-secondary-instance"></a>İkincil örneğe yük devretmeyi yönetme

Yük devretme grubu SQL Yönetilen Örneği'ndeki tüm veritabanlarının yük devretme işlemini yönetir. Grup oluşturulduğunda, örnekteki her veritabanı ikincil SQL Yönetilen Örneği'ne coğrafi olarak otomatik çoğaltılır. Yük devretme gruplarını kullanarak veritabanlarının bir alt kümesi için kısmi yük devretme başlatamazsınız.

> [!IMPORTANT]
> Birincil SQL yönetilen örneğinden bir veritabanı kaldırılırsa, coğrafi ikincil SQL yönetilen örneği üzerinde de otomatik olarak bırakılır.

### <a name="using-read-write-listener-for-oltp-workload"></a>OLTP iş yükü için okuma-yazma dinleyicisi kullanma

OLTP işlemlerini gerçekleştirirken `<fog-name>.zone_id.database.windows.net` sunucu URL 'si olarak kullanın ve bağlantılar otomatik olarak birincil ağa yönlendirilir. Bu URL, yük devretmeden sonra değişmez. Yük devretme, DNS kaydının güncelleştirilmesini içerir, bu nedenle istemci bağlantıları yalnızca istemci DNS önbelleği yenilendikten sonra yeni birincil istemciye yönlendirilir. İkincil örnek, DNS bölgesini birincil ile paylaştığından, istemci uygulaması aynı SAN sertifikasını kullanarak buna yeniden bağlanabilir.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>İkincil örneğe bağlanmak için salt okuma dinleyicisi kullanma

Verilerin belirli bir şekilde kullanılması için dayanıklı bir mantıksal olarak yalıtılmış salt okunurdur, uygulamadaki ikincil veritabanını kullanabilirsiniz. Coğrafi olarak çoğaltılan ikinciye doğrudan bağlanmak için `<fog-name>.secondary.<zone_id>.database.windows.net` sunucu URL 'si olarak kullanın ve bağlantı doğrudan coğrafi çoğaltılan ikincil öğesine yapılır.

> [!NOTE]
> İş Açısından Kritik katmanında, SQL yönetilen örneği, bağlantı dizesindeki parametresini kullanarak salt okuma sorgusu iş yüklerini boşaltmak için [salt okuma çoğaltmalarının](read-scale-out.md) kullanımını destekler `ApplicationIntent=ReadOnly` . Coğrafi olarak çoğaltılan ikincil örneği yapılandırdığınızda bu özelliği kullanarak birincil konumdaki veya coğrafi olarak çoğaltılan konumdaki bir salt okuma çoğaltmasına bağlanabilirsiniz.
>
> - Birincil konumdaki bir salt okuma çoğaltmasına bağlanmak için `ApplicationIntent=ReadOnly` ve kullanın `<fog-name>.<zone_id>.database.windows.net` .
> - İkincil konumdaki bir salt okuma çoğaltmasına bağlanmak için `ApplicationIntent=ReadOnly` ve kullanın `<fog-name>.secondary.<zone_id>.database.windows.net` .

### <a name="preparing-for-performance-degradation"></a>Performans düşüşü için hazırlanma

Tipik bir Azure uygulaması birden çok Azure hizmeti kullanır ve birden çok bileşenden oluşur. Yük devretme grubunun otomatik yük devretmesi, Azure SQL bileşenleri yalnızca durum temelinde tetiklenir. Birincil bölgedeki diğer Azure hizmetleri kesinti tarafından etkilenmeyebilir ve bileşenleri bu bölgede kullanılabilir olmaya devam edebilir. Birincil veritabanları ikincil bölgeye geçiş yaptıktan sonra, bağımlı bileşenler arasındaki gecikme artabilir. Uygulamanın performansına yönelik daha yüksek gecikme süresini önlemek için, tüm uygulama bileşenlerinin ikincil bölgedeki yedekliliği ve veritabanı ile birlikte uygulama bileşenlerini devreder. Yapılandırma sırasında, İkincil bölgedeki veritabanına bağlantıyı sağlamak için [ağ güvenliği yönergelerini](#failover-groups-and-network-security) izleyin.

### <a name="preparing-for-data-loss"></a>Veri kaybı için hazırlanma

Bir kesinti algılanırsa, hiç veri kaybı olursa, bilgi hizmetimizin en iyi şekilde bir okuma-yazma yük devretmesi tetiklenir. Aksi takdirde, yük devretme, kullanarak belirttiğiniz dönem için ertelenir `GracePeriodWithDataLossHours` . Belirtilmişse `GracePeriodWithDataLossHours` , veri kaybı için hazırlıklı olun. Genel olarak, kesintiler sırasında Azure kullanılabilirliği tercih eder. Veri kaybını uygun hale getirmek için GracePeriodWithDataLossHours 'i 24 saat gibi yeterince büyük bir sayı olarak ayarladığınızdan emin olun veya otomatik yük devretmeyi devre dışı bırakın.

Okuma-yazma dinleyicisinin DNS güncelleştirmesi, yük devretme başlatıldıktan hemen sonra gerçekleşir. Bu işlem, veri kaybına neden olmaz. Ancak, veritabanı rollerini değiştirme işlemi normal koşullarda 5 dakikaya kadar sürebilir. Tamamlanana kadar, yeni birincil örnekteki bazı veritabanları hala salt okunurdur. PowerShell kullanılarak bir yük devretme başlatılmışsa, birincil çoğaltma rolünü değiştirme işlemi zaman uyumludur. Azure portal kullanılarak başlatılmışsa, Kullanıcı arabirimi tamamlanma durumunu gösterir. REST API kullanılarak başlatılmışsa, tamamlamayı izlemek için standart Azure Resource Manager yoklama mekanizmasını kullanın.

> [!IMPORTANT]
> El ile grup yük devretmesini kullanarak özgün konuma doğru bir şekilde geçiş yapın. Yük devretmeye neden olan kesinti azaltıldığında, birincil veritabanlarınızı özgün konuma taşıyabilirsiniz. Bunu yapmak için, grubun el ile yük devretmesini başlatmanız gerekir.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>Yük devretme grubunun ikincil bölgesini değiştirme

Örnek A 'nın birincil örnek olduğu varsayıyoruz, örnek B var olan ikincil örnek ve C örneği üçüncü bölgedeki yeni ikincil örnek.  Geçişi yapmak için şu adımları izleyin:

1. Aynı boyuta sahip C örneğini aynı DNS bölgesinde oluşturun.
2. A ve B örnekleri arasında yük devretme grubunu silin. Bu noktada, yük devretme grubu dinleyicilerinin SQL diğer adları silindiğinden ve ağ geçidi, yük devretme grubu adını tanımadığı için oturum açma işlemleri başarısız olur. İkincil veritabanlarının ön uç 'lerden bağlantısı kesilecek ve okuma/yazma veritabanları olacaktır.
3. A ve C örneğiyle aynı ada sahip bir yük devretme grubu oluşturun. [Yük devretme grubundaki YÖNERGELERI SQL yönetilen örneği öğreticisi Ile](../managed-instance/failover-group-add-instance-tutorial.md)izleyin. Bu bir veri boyutudur ve bir örnek A 'dan tüm veritabanları birlikte çalıştırıldığında ve eşitlendiğinde tamamlanacaktır.
4. Gereksiz ücretlerden kaçınmak için gerekmiyorsa örnek B 'yi silin.

> [!NOTE]
> 2. adım ve 3. adım tamamlanana kadar sonra A örneğindeki veritabanları korumasız olarak kalır.

### <a name="changing-primary-region-of-the-failover-group"></a>Yük devretme grubunun birincil bölgesi değiştiriliyor

Örnek A 'nın birincil örnek olduğu varsayıyoruz, örnek B var olan ikincil örnek ve C örneği, üçüncü bölgedeki yeni birincil örnek.  Geçişi yapmak için şu adımları izleyin:

1. B örneğini aynı boyuta sahip C ve aynı DNS bölgesinde oluşturun.
2. Birincil örneği B 'ye geçirmek için B örneğine ve el ile yük devretmeye bağlanın. örnek A, yeni ikincil örnek otomatik olarak olur.
3. A ve B örnekleri arasında yük devretme grubunu silin. Bu noktada, yük devretme grubu dinleyicilerinin SQL diğer adları silindiğinden ve ağ geçidi, yük devretme grubu adını tanımadığı için oturum açma işlemleri başarısız olur. İkincil veritabanlarının ön uç 'lerden bağlantısı kesilecek ve okuma/yazma veritabanları olacaktır.
4. A ve C örneğiyle aynı ada sahip bir yük devretme grubu oluşturun. [Yük devretme grubundaki yönergeleri yönetilen örnek öğreticisiyle](../managed-instance/failover-group-add-instance-tutorial.md)izleyin. Bu bir veri boyutudur ve bir örnek A 'dan tüm veritabanları birlikte çalıştırıldığında ve eşitlendiğinde tamamlanacaktır.
5. Gereksiz ücretlerden kaçınmak için gerekmiyorsa örneğini silin.

> [!CAUTION]
> Adım 3 ' ten sonra ve 4. adım tamamlanana kadar sonra a örneğindeki veritabanları korumasız olarak kalır.

> [!IMPORTANT]
> Yük devretme grubu silindiğinde, dinleyici uç noktaları için DNS kayıtları da silinir. Bu noktada, başka birinin bir yük devretme grubu veya aynı ada sahip sunucu diğer adı oluştururken, onu yeniden kullanmanızı önleyen sıfır olmayan bir olasılık vardır. Riski en aza indirmek için genel yük devretme grubu adlarını kullanmayın.

### <a name="enable-scenarios-dependent-on-objects-from-the-system-databases"></a>Sistem veritabanlarından nesnelere bağımlı senaryoları etkinleştirme
Sistem veritabanları, bir yük devretme grubundaki ikincil örneğe çoğaltılmaz. Sistem veritabanlarından nesnelere bağlı olan senaryoları etkinleştirmek için ikincil örnekte, aynı nesneleri ikincil üzerinde oluşturmayı unutmayın. Örneğin, ikincil örnekte aynı oturum açmaları kullanmayı planlıyorsanız, bunları aynı SID ile oluşturmayı unutmayın. 
```SQL
-- Code to create login on the secondary instance
CREATE LOGIN foo WITH PASSWORD = '<enterStrongPasswordHere>', SID = <login_sid>;
``` 


## <a name="failover-groups-and-network-security"></a>Yük devretme grupları ve ağ güvenliği

Bazı uygulamalarda, güvenlik kuralları, veri katmanına ağ erişiminin bir VM, Web hizmeti vb. gibi belirli bir bileşenle veya bileşenlerle sınırlı olmasını gerektirir. Bu gereksinim, iş sürekliliği tasarımı ve yük devretme gruplarının kullanımı için bazı güçlükleri sunmaktadır. Bu tür kısıtlı erişimi uygularken aşağıdaki seçenekleri göz önünde bulundurun.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Yük devretme gruplarını ve sanal ağ kurallarını kullanma

SQL veritabanı veya SQL yönetilen örneğindeki veritabanınıza erişimi kısıtlamak için [sanal ağ hizmet uç noktaları ve kuralları](vnet-service-endpoint-rule-overview.md) kullanıyorsanız, her bir sanal ağ hizmeti uç noktasının yalnızca bir Azure bölgesi için geçerli olduğunu unutmayın. Uç nokta diğer bölgelerin alt ağdan iletişim kabul etmesine izin vermez. Bu nedenle, yalnızca aynı bölgede dağıtılan istemci uygulamaları birincil veritabanına bağlanabilir. Yük devretme işlemi SQL veritabanı istemci oturumlarının farklı bir (ikincil) bölgedeki bir sunucuya tekrar yönlendirilmesine neden olduğundan, Bu oturumlar bu bölgenin dışındaki bir istemciden kaynaklandığından başarısız olur. Bu nedenle, katılan sunucular veya örnekler sanal ağ kurallarında yer alıyorsa otomatik yük devretme ilkesi etkinleştirilemez. El ile yük devretmeyi desteklemek için şu adımları izleyin:

1. Uygulamanızın ön uç bileşenlerinin (Web hizmeti, sanal makineler vb.) yedek kopyalarını ikincil bölgede sağlama
2. [Sanal ağ kurallarını](vnet-service-endpoint-rule-overview.md) birincil ve ikincil sunucu için ayrı ayrı yapılandırın
3. [Traffic Manager yapılandırması kullanarak ön uç yük devretmesini](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime) etkinleştirme
4. Kesinti algılandığında el ile yük devretme başlatın. Bu seçenek, ön uç ve veri katmanı arasında tutarlı gecikme süresi gerektiren uygulamalar için en iyi duruma getirilmiştir ve ön uç, veri katmanı veya her ikisi de kesinti tarafından etkilendiğinde kurtarmayı destekler.

> [!NOTE]
> Salt yazılır bir iş yükünü dengelemek için **salt okunurdur dinleyiciyi** kullanıyorsanız, ikincil veritabanına bağlanabilmesi için bu iş yükünün ikincil BÖLGEDEKI bir VM 'de veya diğer bir kaynakta yürütüldüğünden emin olun.

### <a name="use-failover-groups-and-firewall-rules"></a>Yük devretme gruplarını ve güvenlik duvarı kurallarını kullanma

İş sürekliliği planınız, otomatik yük devretme içeren grupları kullanarak yük devretme gerektiriyorsa, geleneksel güvenlik duvarı kurallarını kullanarak SQL veritabanı 'nda veritabanınıza erişimi kısıtlayabilirsiniz. Otomatik yük devretmeyi desteklemek için şu adımları izleyin:

1. [Genel IP oluşturma](../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Ortak yük dengeleyici oluşturun](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) ve genel IP 'yi buna atayın.
3. Ön uç bileşenleriniz için [bir sanal ağ ve sanal makine oluşturma](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)
4. [Ağ güvenlik grubu oluşturun](../../virtual-network/network-security-groups-overview.md) ve gelen bağlantıları yapılandırın.
5. Giden bağlantıların ' SQL ' [hizmet etiketi](../../virtual-network/network-security-groups-overview.md#service-tags)KULLANıLARAK Azure SQL veritabanı 'na açık olduğundan emin olun.
6. Adım 1 ' de oluşturduğunuz genel IP adresinden gelen trafiğe izin vermek için bir [SQL veritabanı güvenlik duvarı kuralı](firewall-configure.md) oluşturun.

Giden erişimin nasıl yapılandırılacağı ve güvenlik duvarı kurallarında hangi IP 'nin kullanılacağı hakkında daha fazla bilgi için bkz. [yük dengeleyici giden bağlantıları](../../load-balancer/load-balancer-outbound-connections.md).

Yukarıdaki yapılandırma, otomatik yük devretmenin ön uç bileşenlerinden gelen bağlantıları engelolmamasını ve uygulamanın ön uç ile veri katmanı arasındaki daha uzun gecikme süresine neden olduğunu varsaymaktadır.

> [!IMPORTANT]
> Bölgesel kesintiler için iş sürekliliği sağlamak üzere hem ön uç bileşenleri hem de veritabanları için coğrafi artıklık sağlamalısınız.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Yönetilen örnekler ve bunların sanal ağları arasında coğrafi çoğaltmayı etkinleştirme

İki farklı bölgede birincil ve ikincil SQL yönetilen örnekleri arasında bir yük devretme grubu ayarlarken, her örnek bağımsız bir sanal ağ kullanılarak yalıtılmıştır. Bu sanal ağlar arasındaki çoğaltma trafiğine izin vermek için bu önkoşulların karşılandığından emin olun:

- SQL yönetilen örneğinin iki örneğinin farklı Azure bölgelerinde olması gerekir.
- SQL yönetilen örneğinin iki örneğinin aynı hizmet katmanı olması ve aynı depolama boyutuna sahip olması gerekir.
- SQL yönetilen örneğinin ikincil örneğinizin boş olması gerekir (Kullanıcı veritabanı yok).
- SQL yönetilen örneği örnekleri tarafından kullanılan sanal ağların bir [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) veya [Express rotası](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)aracılığıyla bağlanması gerekir. İki sanal ağ şirket içi ağı üzerinden bağlandığında 5022 ve 11000-11999 numaralı bağlantı noktalarını engelleyen bir güvenlik duvarı kuralı olmadığından emin olun. Genel VNet Eşlemesi aşağıdaki notta açıklanan sınırlamayla desteklenir.

   > [!IMPORTANT]
   > [22/9/2020’de yeni oluşturulan sanal kümeler için genel sanal ağ eşlemesinin duyurusunu yaptık](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Bu, hem duyuru tarihinden sonra boş alt ağlarda oluşturulan SQL Yönetilen Örnekleri için hem de söz konusu alt ağlarda daha sonra oluşturulan yönetilen örnekler için genel sanal ağ eşlemesinin desteklendiği anlamına geliyor. Diğer tüm SQL yönetilen örnekler için eşleme desteği, [Genel sanal ağ eşlemesi kısıtlamalarından](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)dolayı aynı bölgedeki ağlarla sınırlıdır. Daha fazla bilgi için bkz. [Azure sanal ağlar sık sorulan sorular](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) makalesinin ilgili bölümü. 

- İki SQL yönetilen örnek sanal ağı, çakışan IP adreslerine sahip olamaz.
- Ağ Güvenlik Gruplarınızı (NSG), 5022 numaralı ve 11000~12000 aralığındaki bağlantı noktalarının diğer yönetilen örneğin alt ağından gelen ve giden bağlantılara açık olmasını sağlayacak şekilde ayarlamalısınız. Bunun amacı örnekler arasında çoğaltma trafiğine olanak tanımaktır.

   > [!IMPORTANT]
   > Yanlış yapılandırılmış NSG güvenlik kuralları, veritabanı kopyalama işlemlerinin takılmasına yol açar.

- İkincil SQL yönetilen örneği doğru DNS bölge KIMLIĞIYLE yapılandırılır. DNS bölgesi, bir SQL yönetilen örneğinin ve temel alınan sanal kümenin bir özelliğidir ve KIMLIĞI ana bilgisayar adı adresine dahildir. Bölge KIMLIĞI, her VNet 'te ilk SQL yönetilen örneği oluşturulduğunda bir rastgele dize olarak oluşturulur ve aynı KIMLIK aynı alt ağdaki diğer tüm örneklere atanır. Atandıktan sonra DNS bölgesi değiştirilemez. Aynı yük devretme grubuna dahil edilen SQL yönetilen örnekleri, DNS bölgesini paylaşmalıdır. Bu, ikincil örneği oluştururken birinci örneğin bölge KIMLIĞINI DnsZonePartner parametresinin değeri olarak geçirerek gerçekleştirirsiniz.

   > [!NOTE]
   > SQL yönetilen örneği ile yük devretme gruplarını yapılandırmaya ilişkin ayrıntılı bir öğretici için bkz. [bir yük devretme grubuna SQL yönetilen örneği ekleme](../managed-instance/failover-group-add-instance-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>Birincil veritabanını yükseltme veya eski sürüme indirme

İkincil veritabanlarının bağlantısını kesmeden, birincil veritabanını farklı bir işlem boyutuna yükseltebilir veya indirgeyebilmeniz gerekir (Genel Amaçlı ve İş Açısından Kritik arasında değil aynı hizmet katmanında). ' Yi yükseltirken, önce tüm ikincil veritabanlarını yükseltmeniz ve ardından birincili yükseltmeniz önerilir. Düşürme sırasında, sırayı tersine çevirin: önce birincili düşürme ve sonra tüm ikincil veritabanlarının düzeyini düşürme. Veritabanını farklı bir hizmet katmanına yükseltirken veya eski sürüme düşürürken bu öneri zorunlu tutulur.

Daha düşük bir SKU'da olan ikincil örnek aşırı yüklendiğinde ve yükseltme veya sürüm düşürme işlemi sırasında yeniden dağıtılması gerektiğinde oluşan sorundan kaçınmak için bu sıralamanın kullanılması önerilir. Ayrıca birincil örneği salt okunur yaparak da bu sorunu önleyebilirsiniz ama bu durumda birincil örnekteki tüm okuma-yazma iş yükleri bundan etkilenecektir.

> [!NOTE]
> Yük devretme grubu yapılandırmasının parçası olarak ikincil bir veritabanı oluşturduysanız, ikincil veritabanının indirgenmesini öneririz. Bu, veri katmanınızın yük devretme etkinleştirildikten sonra düzenli iş yükünüzü işlemek için yeterli kapasiteye sahip olmasını sağlamaktır.

## <a name="preventing-the-loss-of-critical-data"></a>Kritik verilerin kaybını önlemek

Geniş alan ağlarının yüksek gecikmesi nedeniyle, sürekli kopyalama zaman uyumsuz bir çoğaltma mekanizması kullanır. Zaman uyumsuz çoğaltma, bir hata oluşursa, bazı veri kaybını korumasız hale getirir. Ancak, bazı uygulamalar veri kaybı gerektirmez. Bu kritik güncelleştirmeleri korumak için, bir uygulama geliştiricisi işlemi tamamladıktan hemen sonra [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) sistem yordamını çağırabilir. Çağırma, `sp_wait_for_database_copy_sync` son kaydedilen işlem ikincil veritabanına iletilene kadar çağıran iş parçacığını engeller. Ancak, iletilen işlemlerin yeniden çalınmasını ve ikincil üzerinde gerçekleşmesini beklemez. `sp_wait_for_database_copy_sync` belirli bir sürekli kopyalama bağlantısının kapsamına alınır. Birincil veritabanında bağlantı hakları olan herhangi bir Kullanıcı, bu yordamı çağırabilir.

> [!NOTE]
> `sp_wait_for_database_copy_sync` Yük devretmeden sonra veri kaybını önler, ancak okuma erişimi için tam eşitlemeyi garanti etmez. Bir yordam çağrısının neden olduğu gecikme `sp_wait_for_database_copy_sync` önemli olabilir ve çağrı sırasında işlem günlüğünün boyutuna bağlıdır.

## <a name="failover-groups-and-point-in-time-restore"></a>Yük devretme grupları ve zaman içinde bir noktaya geri yükleme

Yük devretme gruplarıyla noktadan noktaya geri yükleme kullanma hakkında daha fazla bilgi için bkz. [Noktadan noktaya kurtarma (sür)](recovery-using-backups.md#point-in-time-restore).

## <a name="limitations-of-failover-groups"></a>Yük devretme gruplarının sınırlamaları

Aşağıdaki sınırlamalara dikkat edin:

- Yük devretme grupları, aynı Azure bölgelerindeki iki sunucu veya örnek arasında oluşturulamaz.
- Yük devretme grupları yeniden adlandırılamaz. Grubu silip farklı bir adla yeniden oluşturmanız gerekir.
- Veritabanı yeniden adlandırma, yük devretme grubundaki örneklerde desteklenmez. Bir veritabanını yeniden adlandırabilmek için yük devretme grubunu geçici olarak silmeniz gerekir.
- Sistem veritabanları, bir yük devretme grubundaki ikincil örneğe çoğaltılmaz. Bu nedenle, nesneler ikincil üzerinde el ile oluşturulmadığı takdirde, sistem veritabanlarından nesnelere bağlı senaryolar ikincil örnekte imkansız olur.

## <a name="programmatically-managing-failover-groups"></a>Yük devretme gruplarını programlı olarak yönetme

Daha önce anlatıldığı gibi otomatik yük devretme grupları ve etkin coğrafi çoğaltma de Azure PowerShell ve REST API kullanılarak programlı bir şekilde yönetilebilir. Aşağıdaki tablolarda kullanılabilen komut kümesi açıklanır. Etkin coğrafi çoğaltma, [Azure SQL veritabanı REST API](/rest/api/sql/) ve [Azure PowerShell cmdlet 'leri](/powershell/azure/)dahil olmak üzere yönetim için Azure Resource Manager API 'ler kümesi içerir. Bu API 'Ler, kaynak gruplarının kullanımını gerektirir ve Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) destekler. Erişim rollerinin nasıl uygulanacağı hakkında daha fazla bilgi için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md).

### <a name="manage-sql-database-failover"></a>SQL veritabanı yük devretmesini yönetme

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | Açıklama |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Bu komut bir yük devretme grubu oluşturur ve hem birincil hem de ikincil sunuculara kaydeder|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Yük devretme grubunu sunucudan kaldırır |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Bir yük devretme grubunun yapılandırmasını alır |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Bir yük devretme grubunun yapılandırmasını değiştirir |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | İkincil sunucuya bir yük devretme grubunun yük devretmesini tetikler |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Bir yük devretme grubuna bir veya daha fazla veritabanı ekler|

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

| Komut | Açıklama |
| --- | --- |
| [az SQL yük devretme-Grup oluşturma](/cli/azure/sql/failover-group#az_sql_failover_group_create) |Bu komut bir yük devretme grubu oluşturur ve hem birincil hem de ikincil sunuculara kaydeder|
| [az SQL yük devretme-Grup silme](/cli/azure/sql/failover-group#az_sql_failover_group_delete) | Yük devretme grubunu sunucudan kaldırır |
| [az SQL yük devretme-grup göster](/cli/azure/sql/failover-group#az_sql_failover_group_show) | Yük devretme grubu yapılandırmasını alır |
| [az SQL Failover-Group Update](/cli/azure/sql/failover-group#az_sql_failover_group_update) |Bir yük devretme grubunun yapılandırmasını değiştirir ve/veya bir yük devretme grubuna bir veya daha fazla veritabanı ekler|
| [az SQL yük devretme-grup kümesi-birincil](/cli/azure/sql/failover-group#az_sql_failover_group_set_primary) | İkincil sunucuya bir yük devretme grubunun yük devretmesini tetikler |

# <a name="rest-api"></a>[REST API 'SI](#tab/rest-api)

| API | Açıklama |
| --- | --- |
| [Yük devretme grubu oluştur veya güncelleştir](/rest/api/sql/failovergroups/createorupdate) | Yük devretme grubu oluşturur veya güncelleştirir |
| [Yük devretme grubunu sil](/rest/api/sql/failovergroups/delete) | Yük devretme grubunu sunucudan kaldırır |
| [Yük devretme (planlı)](/rest/api/sql/failovergroups/failover) | Tam veri eşitlemesi ile geçerli birincil sunucudan ikincil sunucuya yük devretmeyi tetikler.|
| [Yük devretmeyi zorla veri kaybına Izin ver](/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Verileri eşitlemeden geçerli birincil sunucudan ikincil sunucuya yük devretmeyi tetikler. Bu işlem, veri kaybına neden olabilir. |
| [Yük devretme grubunu al](/rest/api/sql/failovergroups/get) | Bir yük devretme grubunun yapılandırmasını alır. |
| [Yük devretme gruplarını sunucuya göre Listele](/rest/api/sql/failovergroups/listbyserver) | Bir sunucudaki yük devretme gruplarını listeler. |
| [Yük devretme grubunu Güncelleştir](/rest/api/sql/failovergroups/update) | Bir yük devretme grubunun yapılandırmasını güncelleştirir. |

---

### <a name="manage-sql-managed-instance-failover"></a>SQL yönetilen örnek yük devretmesini yönetme


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | Açıklama |
| --- | --- |
| [New-Azsqldatabaseınstancefailovergroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Bu komut bir yük devretme grubu oluşturur ve hem birincil hem de ikincil örneklere kaydeder|
| [Set-Azsqldatabaseınstancefailovergroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Bir yük devretme grubunun yapılandırmasını değiştirir|
| [Get-Azsqldatabaseınstancefailovergroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Bir yük devretme grubunun yapılandırmasını alır|
| [Switch-Azsqldatabaseınstancefailovergroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |İkincil örneğe yük devretme grubunun yük devretmesini tetikler|
| [Remove-Azsqldatabaseınstancefailovergroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Yük devretme grubunu kaldırır|


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

| Komut | Açıklama |
| --- | --- |
| [az SQL yük devretme-Grup oluşturma](/cli/azure/sql/failover-group#az_sql_failover_group_create) |Bu komut bir yük devretme grubu oluşturur ve hem birincil hem de ikincil sunuculara kaydeder|
| [az SQL yük devretme-Grup silme](/cli/azure/sql/failover-group#az_sql_failover_group_delete) | Yük devretme grubunu sunucudan kaldırır |
| [az SQL yük devretme-grup göster](/cli/azure/sql/failover-group#az_sql_failover_group_show) | Yük devretme grubu yapılandırmasını alır |
| [az SQL Failover-Group Update](/cli/azure/sql/failover-group#az_sql_failover_group_update) |Bir yük devretme grubunun yapılandırmasını değiştirir ve/veya bir yük devretme grubuna bir veya daha fazla veritabanı ekler|
| [az SQL yük devretme-grup kümesi-birincil](/cli/azure/sql/failover-group#az_sql_failover_group_set_primary) | İkincil sunucuya bir yük devretme grubunun yük devretmesini tetikler |

# <a name="rest-api"></a>[REST API 'SI](#tab/rest-api)

| API | Açıklama |
| --- | --- |
| [Yük devretme grubu oluştur veya güncelleştir](/rest/api/sql/instancefailovergroups/createorupdate) | Bir yük devretme grubunun yapılandırmasını oluşturur veya güncelleştirir |
| [Yük devretme grubunu sil](/rest/api/sql/instancefailovergroups/delete) | Yük devretme grubunu örnekten kaldırır |
| [Yük devretme (planlı)](/rest/api/sql/instancefailovergroups/failover) | Tam veri eşitlemesi ile bu örneğe geçerli birincil örnekten yük devretmeyi tetikler. |
| [Yük devretmeyi zorla veri kaybına Izin ver](/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Verileri eşitlemeden geçerli birincil örnekten ikincil örneğe yük devretmeyi tetikler. Bu işlem, veri kaybına neden olabilir. |
| [Yük devretme grubunu al](/rest/api/sql/instancefailovergroups/get) | bir yük devretme grubunun yapılandırmasını alır. |
| [Yük devretme gruplarını listeleme-konuma göre listeleme](/rest/api/sql/instancefailovergroups/listbylocation) | Bir konumdaki yük devretme gruplarını listeler. |

---

## <a name="next-steps"></a>Sonraki adımlar

- Ayrıntılı öğreticiler için bkz.
  - [Yük devretme grubuna SQL veritabanı ekleme](failover-group-add-single-database-tutorial.md)
  - [Yük devretme grubuna bir elastik havuz ekleme](failover-group-add-elastic-pool-tutorial.md)
  - [Yük devretme grubuna SQL yönetilen örneği ekleme](../managed-instance/failover-group-add-instance-tutorial.md)
- Örnek betikler için bkz.:
  - [Azure SQL veritabanı için etkin Coğrafi çoğaltmayı yapılandırmak üzere PowerShell 'i kullanma](scripts/setup-geodr-and-failover-database-powershell.md)
  - [Azure SQL veritabanı 'nda havuza alınmış bir veritabanı için etkin Coğrafi çoğaltmayı yapılandırmak için PowerShell 'i kullanma](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
  - [PowerShell kullanarak bir yük devretme grubuna Azure SQL veritabanı ekleme](scripts/add-database-to-failover-group-powershell.md)
- İş sürekliliği için genel bakış ve senaryolar için bkz. [iş sürekliliği genel bakış](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Azure SQL veritabanı otomatik yedeklemeleri hakkında bilgi edinmek için bkz. [SQL veritabanı otomatik yedeklemeleri](automated-backups-overview.md).
- Kurtarmaya yönelik otomatik yedeklemeleri kullanma hakkında bilgi edinmek için bkz. [hizmet tarafından başlatılan yedeklemelerden bir veritabanını geri yükleme](recovery-using-backups.md).
- Yeni bir birincil sunucu ve veritabanına yönelik kimlik doğrulama gereksinimleri hakkında bilgi edinmek için, bkz. [olağanüstü durum kurtarma sonrasında SQL veritabanı güvenliği](active-geo-replication-security-configure.md).

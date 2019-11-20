---
title: Redsıs için Azure önbelleğini yönetme
description: Redsıs için yeniden başlatma ve Azure önbelleği güncelleştirmelerini zamanlama gibi yönetim görevlerinin nasıl gerçekleştirileceğini öğrenin
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: 69686cad20bc4ce70bff2a92a216c9430522c301
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122526"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>Redsıs için Azure önbelleğini yönetme
Bu konu, Redsıs örnekleri için Azure önbelleğiniz için güncelleştirmeleri yeniden [başlatma](#reboot) ve [zamanlama](#schedule-updates) gibi yönetim görevlerinin nasıl gerçekleştirileceğini açıklamaktadır.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Yeniden başlatma
**Yeniden başlatma** dikey penceresi, önbelleğinizin bir veya daha fazla düğümünü yeniden başlatmanızı sağlar. Bu yeniden başlatma özelliği, bir önbellek düğümünde hata oluşursa uygulamanızı dayanıklılık açısından test etmenizi sağlar.

![Yeniden başlatma](./media/cache-administration/redis-cache-administration-reboot.png)

Yeniden başlatılacak düğümleri seçin ve **Yeniden Başlat**' a tıklayın.

![Yeniden başlatma](./media/cache-administration/redis-cache-reboot.png)

Kümelemenin etkinleştirildiği Premium bir önbelleğiniz varsa, önbelleğin hangi parçaları yeniden başlatılacağını seçebilirsiniz.

![Yeniden başlatma](./media/cache-administration/redis-cache-reboot-cluster.png)

Önbelleğinizin bir veya daha fazla düğümünü yeniden başlatmak için, istenen düğümleri seçin ve **Yeniden Başlat**' a tıklayın. Kümelemenin etkinleştirildiği Premium bir önbelleğiniz varsa, yeniden başlatmak için istenen parçaları seçin ve ardından **Yeniden Başlat**' a tıklayın. Birkaç dakika sonra, seçili düğümler yeniden başlatılır ve birkaç dakika sonra tekrar çevrimiçi olacak.

İstemci uygulamalarına etkisi, yeniden başlatmanız gereken düğümlere bağlı olarak değişir.

* **Ana** düğüm-ana düğüm yeniden başlatıldığında, redin Azure önbelleği çoğaltma düğümüne devrediltilir ve onu ana öğe olarak yükseltir. Bu yük devretme sırasında, bağlantıların önbellekte başarısız olabileceği kısa bir Aralık olabilir.
* **Bağımlı** -bağımlı düğüm yeniden başlatıldığında, istemcileri önbelleğe alma işlemi genellikle bir etkisi yoktur.
* **Hem ana** hem de bağımlı-her iki önbellek düğümü yeniden başlatıldığında tüm veriler önbellekte kaybolur ve önbellek bağlantıları birincil düğüm yeniden çevrimiçi olana kadar başarısız olur. [Veri kalıcılığını](cache-how-to-premium-persistence.md)yapılandırdıysanız, önbellek yeniden çevrimiçi olduğunda en son yedekleme geri yüklenir, ancak en son yedeklemeden sonra gerçekleşen tüm önbellek yazmaları kaybedilir.
* **Kümeleme etkinken Premium önbelleğinin düğümleri** -kümelendirmeyi etkin bir Premium önbelleğin bir veya daha fazla düğümünü yeniden başlattığınızda, seçili düğümlerin davranışı ilgili düğümü veya kümelenmemiş bir önbelleğin düğümlerini yeniden başlattığınızda aynı olur.

## <a name="reboot-faq"></a>Yeniden başlatma SSS
* [Uygulamamı test etmek için hangi düğümü yeniden başlatmalıyım?](#which-node-should-i-reboot-to-test-my-application)
* [İstemci bağlantılarını temizlemek için önbelleği yeniden başlatabilirim miyim?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Yeniden başlatma işlemi yapmam durumunda önbelleğim verileri kaybedecek mıyım?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [PowerShell, CLı veya diğer yönetim araçlarını kullanarak önbelleğinizi yeniden başlatabilirim miyim?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Uygulamamı test etmek için hangi düğümü yeniden başlatmalıyım?
Uygulamanızın dayanıklılığını, önbelleğinizin birincil düğümünde hata ile test etmek için **ana** düğümü yeniden başlatın. Uygulamanızın dayanıklılığını ikincil düğümün hatasına karşı test etmek için **bağımlı** düğümü yeniden başlatın. Uygulamanızın dayanıklılığını önbelleğin toplam arızasına karşı test etmek için **her iki** düğümü yeniden başlatın.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>İstemci bağlantılarını temizlemek için önbelleği yeniden başlatabilirim miyim?
Evet, önbelleği yeniden başlattıktan sonra tüm istemci bağlantıları temizlenir. Yeniden başlatma, bir mantık hatası veya istemci uygulamasındaki bir hata nedeniyle tüm istemci bağlantılarının kullanıldığı durumlarda yararlı olabilir. Her fiyatlandırma katmanının çeşitli boyutlarda farklı [istemci bağlantısı limitleri](cache-configure.md#default-redis-server-configuration) vardır ve bu sınırlara ulaşıldığında daha fazla istemci bağlantısı kabul edilmez. Önbelleğin yeniden başlatılması, tüm istemci bağlantılarını temizlemek için bir yol sağlar.

> [!IMPORTANT]
> İstemci bağlantılarını temizlemek için önbelleğinizi yeniden başlattıktan sonra, redin düğümü yeniden çevrimiçi olduktan sonra StackExchange. Red, otomatik olarak yeniden bağlanır. Temel alınan Sorun çözümlenmezse istemci bağlantıları çalışmaya devam edebilir.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Yeniden başlatma işlemi yapmam durumunda önbelleğim verileri kaybedecek mıyım?
Hem **ana** hem de **bağımlı** düğümleri yeniden başlattıktan sonra önbellekteki tüm veriler (veya kümelendirmeyi etkin bir Premium önbellek kullanıyorsanız) kaybolabilir, ancak bu garanti edilmez. [Veri kalıcılığını](cache-how-to-premium-persistence.md)yapılandırdıysanız, önbellek yeniden çevrimiçi olduğunda en son yedekleme geri yüklenir, ancak yedekleme yapıldıktan sonra gerçekleşen tüm önbellek yazmaları kaybedilir.

Düğümlerden yalnızca birini yeniden yüklerseniz, veriler genellikle kaybedilmez, ancak yine de olabilir. Örneğin, ana düğüm yeniden başlatıldığında ve bir önbellek yazma işlemi devam ediyorsa, önbellek yazma verileri kaybedilir. Veri kaybı için başka bir senaryo da bir düğümü yeniden başlattığınızda ve diğer düğüm aynı anda bir hata nedeniyle sona acaksa olur. Veri kaybına ilişkin olası nedenler hakkında daha fazla bilgi için bkz. [redsıs 'deki verilerim ne oldu?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>PowerShell, CLı veya diğer yönetim araçlarını kullanarak önbelleğinizi yeniden başlatabilirim miyim?
Evet, PowerShell yönergeleri için bkz. [Reda Için Azure önbelleğini yeniden başlatma](cache-how-to-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis).

## <a name="schedule-updates"></a>Güncelleştirmeleri zamanlama
**Güncelleştirmeleri zamanla** dikey penceresi, önbellek örneğiniz için bir bakım penceresi atamanıza olanak tanır. Bakım penceresi belirtildiğinde, bu pencere sırasında tüm Red, Server güncelleştirmeleri yapılır. 

> [!NOTE] 
> Bakım penceresi yalnızca Redsıs sunucu güncelleştirmeleri için geçerlidir ve önbelleği barındıran VM 'lerin işletim sisteminde herhangi bir Azure güncelleştirmesi ya da güncelleştirmesi için geçerli değildir.
>

![Güncelleştirmeleri zamanlama](./media/cache-administration/redis-schedule-updates.png)

Bir bakım penceresi belirtmek için, istenen günleri denetleyin ve her gün için bakım penceresi başlangıç saatini belirtip **Tamam**' a tıklayın. Bakım penceresi saatinin UTC olarak olduğunu unutmayın. 

Güncelleştirmeler için varsayılan ve en düşük bakım penceresi beş saattir. Bu değer Azure portal yapılandırılamaz, ancak [New-Azrediscacheschedutaentry](/powershell/module/az.rediscache/new-azrediscachescheduleentry) cmdlet 'inin `MaintenanceWindow` parametresini kullanarak PowerShell 'de yapılandırabilirsiniz. Daha fazla bilgi için bkz. PowerShell, CLı veya diğer yönetim araçlarını kullanarak zamanlanmış güncelleştirmeleri yönetebilir miyim?

## <a name="schedule-updates-faq"></a>Güncelleştirme zamanlama SSS
* [Güncelleştirmeleri zamanla özelliğini kullandığımda güncelleştirmeler ne zaman oluşur?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Zamanlanmış bakım penceresi sırasında ne tür güncelleştirmeler yapılır?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Zamanlanan güncelleştirmeleri PowerShell, CLı veya diğer yönetim araçlarını kullanarak yönetebilir miyim?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Güncelleştirmeleri zamanla özelliğini kullandığımda güncelleştirmeler ne zaman oluşur?
Bakım penceresi belirtmezseniz, güncelleştirmeler herhangi bir zamanda yapılabilir.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Zamanlanmış bakım penceresi sırasında ne tür güncelleştirmeler yapılır?
Zamanlanan bakım penceresi sırasında yalnızca Redsıs sunucu güncelleştirmeleri yapılır. Bakım penceresi, Azure güncelleştirmeleri veya VM işletim sistemi güncelleştirmeleri için geçerlidir.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>PowerShell, CLı veya diğer yönetim araçlarını kullanarak zamanlanmış güncelleştirmeleri yönetibilirim miyim?
Evet, aşağıdaki PowerShell cmdlet 'lerini kullanarak zamanlanmış güncelleştirmelerinizi yönetebilirsiniz:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [New-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [New-Azrediscacheschedutaentry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remove-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

## <a name="next-steps"></a>Sonraki adımlar
* [Redsıs Premium katman özellikleri için daha fazla Azure önbelleği](cache-premium-tier-intro.md) bulun.


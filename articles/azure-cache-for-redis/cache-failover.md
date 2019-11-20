---
title: Yük devretme ve düzeltme eki uygulama-Redsıs için Azure önbelleği
description: Redsıs için Azure önbelleği için yük devretme, düzeltme eki uygulama ve güncelleştirme süreci hakkında bilgi edinin.
author: asasine
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: adsasine
ms.openlocfilehash: 6ff33bd594181aabc4fd7d55ce33f780a0d06086
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122193"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Redsıs için Azure önbelleği için yük devretme ve düzeltme eki uygulama

Esnek ve başarılı istemci uygulamaları oluşturmak için, Redsıs hizmeti için Azure önbelleği bağlamında yük devretmeyi anlamak önemlidir. Yük devretme, planlı yönetim işlemlerinin bir parçası olabilir veya planlanmamış donanım ya da ağ hatalarından kaynaklanıyor olabilir. Önbellek yük devretmesinin yaygın bir kullanımı, yönetim hizmeti Redsıs ikilileri için Azure önbelleğini yayüklerken gelir. Bu makalede, yük devretme işleminin ne olduğu, düzeltme eki sırasında nasıl gerçekleştiği ve dayanıklı bir istemci uygulamasının nasıl oluşturulacağı ele alınmaktadır.

## <a name="what-is-a-failover"></a>Yük devretme nedir?

Redin için Azure önbelleği için yük devretmeye genel bakış ile başlayalım.

### <a name="a-quick-summary-of-cache-architecture"></a>Önbellek mimarisinin hızlı bir özeti

Önbellek, ayrı, özel IP adreslerine sahip birden çok sanal makineden oluşturulur. Düğüm olarak da bilinen her sanal makine, tek bir sanal IP adresine sahip paylaşılan yük dengeleyiciye bağlanır. Her düğüm Redsıs sunucu sürecini çalıştırır ve ana bilgisayar adı ve Redsıs bağlantı noktaları aracılığıyla erişilebilir. Her düğüm, bir ana veya bir çoğaltma düğümü olarak kabul edilir. Bir istemci uygulaması bir önbelleğe bağlanırsa, trafiği bu yük dengeleyiciden geçer ve otomatik olarak ana düğüme yönlendirilir.

Temel bir önbellekte tek düğüm her zaman bir yöneticisidir. Standart veya Premium önbelleğinde iki düğüm vardır: biri ana öğe olarak seçilir ve diğeri çoğaltmadır. Standart ve Premium önbellekler birden çok düğüme sahip olduğundan, diğeri istekleri işlemeye devam ederken bir düğüm kullanılamaz olabilir. Kümelenmiş önbellekler, her biri ayrı ana ve çoğaltma düğümlerine sahip birçok parça oluşur. Bir parça, diğerleri kullanılabilir kaldığı sırada kalmış olabilir.

> [!NOTE]
> Temel bir önbellekte birden fazla düğüm yoktur ve kullanılabilirlik için hizmet düzeyi sözleşmesi (SLA) sunmaz. Temel önbellekler yalnızca geliştirme ve test amaçları için önerilir. Kullanılabilirliği artırmak için çok düğümlü bir dağıtım için standart veya Premium önbellek kullanın.

### <a name="explanation-of-a-failover"></a>Yük devretme açıklaması

Yük devretme, bir çoğaltma düğümü kendisini ana düğüm olacak şekilde yükseltir ve eski ana düğüm varolan bağlantıları kapattığında oluşur. Ana düğüm geri alındıktan sonra, rollerdeki değişikliği fark eder ve bir çoğaltma haline gelmesi için kendisini indirger. Ardından yeni ana ağa bağlanır ve verileri eşitler. Yük devretme planlanmış veya planlanmamış olabilir.

Redis düzeltme eki uygulama, IŞLETIM sistemi yükseltmeleri ve ölçekleme ve yeniden başlatma gibi yönetim işlemleri gibi sistem güncelleştirmeleri sırasında *planlanmış bir yük devretme* gerçekleşir. Düğümler güncelleştirme için öncelikli bir bildirim aldığından, bu roller, kolayca takas rolleri ve değişikliğin yük dengeleyiciyi hızlıca güncelleştirebilirler. Planlanmış bir yük devretme genellikle 1 saniyeden az bir sürede tamamlanır.

Donanım hatası, ağ arızası veya ana düğüme yönelik diğer beklenmedik kesintiler nedeniyle *planlanmamış yük devretme* gerçekleşebilir. Çoğaltma düğümü kendisini ana öğe ile yükseltir, ancak işlem daha uzun sürer. Bir çoğaltma düğümünün, yük devretme işlemini başlatabilmesi için önce ana düğümünün mevcut olmadığından emin olması gerekir. Gereksiz bir yük devretmenin önüne geçmek için, çoğaltma düğümü bu planlanmamış hatanın geçici ya da yerel olmadığından emin olmalıdır. Bu algılama gecikmesi, planlanmamış bir yük devretmenin genellikle 10 ile 15 saniye içinde bittiği anlamına gelir.

## <a name="how-does-patching-occur"></a>Düzeltme eki uygulama nasıl yapılır?

Redsıs hizmeti için Azure önbelleği, en son platform özellikleri ve düzeltmeleriyle önbelleğinizi düzenli olarak güncelleştirir. Bir önbelleğe yama yapmak için hizmet aşağıdaki adımları izler:

1. Yönetim hizmeti düzeltme için bir düğüm seçer.
1. Seçili düğüm bir ana düğümse, karşılık gelen çoğaltma düğümü birlikte kendisini yükseltir. Bu promosyon planlı yük devretme olarak kabul edilir.
1. Seçili düğüm, yeni değişiklikleri yapmak için yeniden başlatılır ve bir çoğaltma düğümü olarak geri gönderilir.
1. Çoğaltma düğümü ana düğüme bağlanır ve verileri eşitler.
1. Veri eşitleme işlemi tamamlandığında, düzeltme eki uygulama, kalan düğümler için yinelenir.

Düzeltme eki uygulama planlı bir yük devretme olduğundan, çoğaltma düğümü bir ana olacak şekilde kendisini hızlı bir şekilde yükseltir ve istekleri ve yeni bağlantıları başlatır. Temel önbellekler bir çoğaltma düğümüne sahip değildir ve güncelleştirme tamamlanana kadar kullanılamaz. Kümelenmiş bir önbelleğin her parçası ayrı düzeltme eki uygulanmış olur ve başka bir parça bağlantılarını kapatmaz.

> [!IMPORTANT]
> Düğümler, veri kaybını engellemek için tek seferde düzeltme eki uygulanmış. Temel önbelleklerin veri kaybı olur. Kümelenmiş önbellekler tek seferde bir parça düzeltme hallenir.

Aynı kaynak grubunda ve bölgede bulunan birden çok önbellek de aynı anda bir tane düzeltme eki uygulanmış.  Farklı kaynak gruplarında veya farklı bölgelerde bulunan önbellekler aynı anda düzeltme olabilir.

Tam veri eşitlemesi işlem tekrardan önce yapıldığından, standart veya Premium bir önbellek kullandığınızda veri kaybı oluşması çok düşüktür. Verileri [dışarı](cache-how-to-import-export-data.md#export) aktarıp [kalıcılığı](cache-how-to-premium-persistence.md)etkinleştirerek veri kaybına karşı daha fazla koruma sağlayabilirsiniz.

## <a name="additional-cache-load"></a>Ek önbellek yükü

Yük devretme gerçekleştiğinde, standart ve Premium önbellekler verileri bir düğümden diğerine çoğaltmalıdır. Bu çoğaltma, sunucu belleğinde ve CPU 'da bazı yük artışına neden olur. Önbellek örneği zaten yüklüyse, istemci uygulamaları daha fazla gecikme yaşar. Olağanüstü durumlarda, istemci uygulamaları zaman aşımı özel durumları alabilir. Bu ek yükün etkisini azaltmaya yardımcı olmak için, önbelleğin `maxmemory-reserved` ayarını [yapılandırın](cache-configure.md#memory-policies) .

## <a name="how-does-a-failover-affect-my-client-application"></a>Yük devretme istemci uygulamamı nasıl etkiler?

İstemci uygulaması tarafından görülen hataların sayısı, yük devretme sırasında o bağlantıda kaç işlemin beklendiğini gösterir. Bağlantılarını kapatan düğüm üzerinden yönlendirilen herhangi bir bağlantı, hataları görürler. Birçok istemci kitaplığı, bağlantı kesme sırasında zaman aşımı özel durumları, bağlantı özel durumları veya yuva özel durumları dahil farklı hata türleri oluşturabilir. Özel durumların sayısı ve türü, isteğin, önbellek bağlantılarını kapatışında, isteğin bulunduğu yere bağlıdır. Örneğin, bir istek gönderen ancak yük devretme gerçekleştiğinde yanıt almamış bir işlem zaman aşımı özel durumu alabilir. Kapalı bağlantı nesnesindeki yeni istekler, yeniden bağlantı başarıyla gerçekleşene kadar bağlantı özel durumları alıyor.

Çoğu istemci kitaplığı, bu şekilde yapılandırıldıysa önbelleğe yeniden bağlanmaya çalışır. Ancak, öngörülemeyen hatalar bazen kitaplık nesnelerini kurtarılamaz bir duruma yerleştirebilir. Hatalar önceden yapılandırılmış bir süreden daha uzun süre devam ediyorsa bağlantı nesnesi yeniden oluşturulmalıdır. Microsoft.NET ve diğer nesne yönelimli dillerde, uygulamayı yeniden başlatmadan bağlantıyı yeniden oluşturmak [yavaş\<t\> bir model](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern)kullanılarak gerçekleştirilebilir.

### <a name="how-do-i-make-my-application-resilient"></a>Uygulamamı dayanıklı hale getirmek Nasıl yaparım??

Yük devretmeleri tamamen önlemenize izin vermekten, bağlantı kesme ve başarısız isteklere dayanıklılık sağlamak için istemci uygulamalarınızı yazın. Çoğu istemci kitaplığı otomatik olarak önbellek uç noktasına yeniden bağlansa da, başarısız istekleri yeniden denemeye çalışır. Uygulama senaryosuna bağlı olarak, yeniden deneme mantığını geri alma ile kullanmak mantıklı olabilir.

İstemci uygulamasının esnekliğini test etmek için, bağlantı sonlarına yönelik el ile tetikleyici olarak [yeniden başlatma](cache-administration.md#reboot) kullanın. Ayrıca, güncelleştirmeleri bir önbellekte [zamanlamanızı](cache-administration.md#schedule-updates) öneririz. Yönetim hizmetine, belirtilen haftalık pencereler sırasında Redsıs çalışma zamanı düzeltme ekleri uygulamayı söyleyin. Olası olayları önlemek için, bu pencereler genellikle istemci uygulama trafiğinin düşük olduğu dönemler olur.

### <a name="client-network-configuration-changes"></a>İstemci ağı-yapılandırma değişiklikleri

İstemci tarafı ağ yapılandırma değişiklikleri, "bağlantı kullanılamıyor" hatalarını tetikleyebilir. Bu değişiklikler şunları içerebilir:

- Hazırlama ve üretim yuvaları arasında bir istemci uygulamasının sanal IP adresini değiştirme.
- Uygulamanızın boyutunu veya örnek sayısını ölçeklendirin.

Bu değişiklikler, bir dakikadan kısa bir sürede bağlantı sorununa neden olabilir. İstemci uygulamanız, Redsıs hizmeti için Azure önbelleğinin yanı sıra diğer dış ağ kaynaklarıyla olan bağlantısını da kaybedecektir.

## <a name="next-steps"></a>Sonraki adımlar

- Önbelleğiniz için [güncelleştirmeleri zamanlayın](cache-administration.md#schedule-updates) .
- [Yeniden başlatma](cache-administration.md#reboot)kullanarak uygulama dayanıklılığı test edin.
- Bellek ayırmalarını ve ilkelerini [yapılandırın](cache-configure.md#memory-policies) .

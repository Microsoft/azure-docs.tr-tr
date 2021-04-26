---
title: Azure 'da doğru PostgreSQL sunucu seçeneğini belirleyin
description: Dağıtımlarınız için doğru PostgreSQL sunucu seçeneğinin seçilmesi için yönergeler sağlar.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: db80ae66761e49ce90f25a22f0e74cf8a1300796
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98797241"
---
# <a name="choose-the-right-postgresql-server-option-in-azure"></a>Azure 'da doğru PostgreSQL sunucu seçeneğini belirleyin

Azure ile PostgreSQL sunucu iş yükleriniz, barındırılan bir sanal makine altyapısı (IaaS) veya hizmet olarak barındırılan bir platform (PaaS) olarak çalışabilir. PaaS 'in her biri birden çok hizmet katmanına sahip birden çok dağıtım seçeneği vardır. IaaS ve PaaS arasında seçim yaptığınızda, veritabanınızı yönetmek, yama uygulamak ve yedeklemeler yapmak istediğinize ya da bu işlemleri Azure 'a atamak istediğinize karar vermelisiniz.

Kararınız yaparken PaaS veya diğer yandan Azure VM 'lerinde (IaaS) çalışan aşağıdaki üç seçeneği göz önünde bulundurun
- [PostgreSQL için Azure veritabanı tek sunucu](./overview-single-server.md)
- [PostgreSQL için Azure veritabanı esnek sunucu](./flexible-server/overview.md)
- [PostgreSQL için Azure veritabanı hiper ölçek (Citus)](hyperscale/index.yml)

**Azure VM 'Lerinde PostgreSQL** seçeneği IaaS sektör kategorisine denk gelir. Bu hizmetle, PostgreSQL sunucusunu Azure bulut platformunda tam olarak yönetilen bir sanal makine içinde çalıştırabilirsiniz. PostgreSQL 'in tüm son sürümleri, IaaS sanal makinesine yüklenebilir. PostgreSQL için Azure veritabanı 'nın en önemli farkından, Azure VM 'lerinde PostgreSQL, veritabanı altyapısı üzerinde denetim sağlar. Ancak, bu denetim, VM 'Leri ve birçok veritabanı yönetimi (DBA) görevini yönetme sorumluluğunda gelir. Bu görevler veritabanı sunucularını, veritabanı kurtarmayı ve yüksek kullanılabilirlik tasarımını koruma ve düzeltme eki uygulama içerir.

Bu seçenekler arasındaki temel farklılıklar aşağıdaki tabloda listelenmiştir:

| **Öznitelik** | **Azure VM 'lerinde Postgres** | **PaaS olarak PostgreSQL** |
| ----- | ----- | ----- |
| <B> Kullanılabilirlik SLA 'Sı |-% 99,99 kullanılabilirlik kümeleriyle <br> -tek VM 'lerle% 99,95 | -Tek sunucu –% 99,99 <br> -Esnek sunucu – önizleme sırasında kullanılamaz <br> -Hyperscale (Citus)-% 99,95 (yüksek kullanılabilirlik etkinleştirildiğinde)|
| <B> OS ve PostgreSQL düzeltme eki uygulama | -Müşterinin yönettiği | -Tek sunucu – otomatik <br> -Esnek sunucu: isteğe bağlı müşteri tarafından yönetilen pencere ile otomatik <br> -Hyperscale (Citus) – otomatik |
| <B> Yüksek kullanılabilirlik | -Müşteriler yüksek kullanılabilirliği mimarileri uygular, test edin ve bakımını yapın. Yetenekler kümeleme, çoğaltma vb. içerebilir. | -Tek sunucu: yerleşik <br> -Esnek sunucu: yerleşik <br> -Hyperscale (Citus): bekleme ile oluşturuldu |
| <B> Bölge artıklığı | -Azure VM 'Leri, farklı kullanılabilirlik bölgelerinde çalışacak şekilde ayarlanabilir. Şirket içi bir çözüm için müşterilerin kendi ikincil veri merkezini oluşturması, yönetmesi ve sürdürmeleri gerekir. | -Tek sunucu: Hayır <br> -Esnek sunucu: Evet <br> -Hyperscale (Citus): Hayır |
| <B> Karma senaryo | -Müşterinin yönettiği |-Tek sunucu: okuma-çoğaltma <br> -Esnek sunucu: Önizleme sırasında kullanılamaz <br> -Hyperscale (Citus): Hayır |
| <B> Yedekleme ve geri yükleme | -Müşterinin yönettiği | -Tek sunucu: yerel ve coğrafi bölge için Kullanıcı yapılandırmasıyla yerleşik <br> -Esnek sunucu: bölge yedekli depolamada Kullanıcı Yapılandırması ile yerleşik olarak <br> -Hyperscale (Citus): yerleşik |
| <B> Veritabanı Işlemlerini izleme | -Müşterinin yönettiği | -Tek sunucu, esnek sunucu ve hiper ölçek (Citus): tüm teklif müşterilerine, veritabanı işleminde uyarı ayarlama ve eşiklere ulaşma konusunda işlem yapma olanağı sunar. |
| <B> Gelişmiş tehdit koruması | -Müşteriler bu korumanın kendileri için derlenmelidir. |-Tek sunucu: Evet <br> -Esnek sunucu: Önizleme sırasında kullanılamaz <br> -Hyperscale (Citus): Hayır |
| <B> Olağanüstü durum kurtarma | -Müşterinin yönettiği | -Tek sunucu: coğrafi olarak yedekli yedekleme ve coğrafi okuma-çoğaltma <br> -Esnek sunucu: Önizleme sırasında kullanılamaz <br> -Hyperscale (Citus): Hayır |
| <B> Akıllı performans | -Müşterinin yönettiği | -Tek sunucu: Evet <br> -Esnek sunucu: Önizleme sırasında kullanılamaz <br> -Hyperscale (Citus): Hayır |

## <a name="total-cost-of-ownership-tco"></a>Toplam sahip olma maliyeti (TCO)

TCO genellikle veritabanlarınızı barındırmak için en iyi çözümü belirleyen birincil noktadır. Bu, sıkı bütçe kısıtlamaları altında çalışan kurulu bir şirkette çok az nakit veya ekip ile bir başlangıç yaptığınız için geçerlidir. Bu bölümde Azure sanal makinelerinde PostgreSQL için Azure veritabanı ve PostgreSQL için Azure veritabanı 'na uygulanan faturalandırma ve lisans temelleri açıklanmaktadır.

## <a name="billing"></a>Faturalandırma

PostgreSQL için Azure veritabanı şu anda birkaç katmanda kaynaklar için farklı fiyatlarla bir hizmet olarak sunulmaktadır. Tüm kaynaklar, saatlik olarak sabit bir fiyat üzerinden faturalandırılır. Şu anda desteklenen hizmet katmanları, işlem boyutları ve depolama tutarları hakkında en son bilgiler için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/postgresql/server/) , hizmet katmanlarını ve işlem boyutlarını uygulamanızın değişen aktarım hızı gereksinimleriyle eşleşecek şekilde dinamik olarak ayarlamanıza olanak sağlar. Giden Internet trafiği için düzenli [veri aktarımı fiyatları](https://azure.microsoft.com/pricing/details/data-transfers/)üzerinden faturalandırılırsınız.

PostgreSQL için Azure veritabanı ile, Microsoft, veritabanı yazılımını otomatik olarak yapılandırır, yaekler ve yükseltir. Bu otomatik eylemler, yönetim maliyetlerinizi azaltır. Ayrıca, PostgreSQL için Azure veritabanı 'nın [otomatik yedekleme bağlantısı]() özellikleri vardır. Bu yetenekler, özellikle çok sayıda veritabanınız olduğunda önemli maliyet tasarrufları elde etmenize yardımcı olur. Buna karşılık, Azure VM 'lerinde PostgreSQL ile herhangi bir PostgreSQL sürümü seçip çalıştırabilirsiniz. Ancak, sağlanan VM için ödeme yapmanız gerekir, veri, yedekleme, izleme verileri ve günlük depolama alanı maliyeti ve kullanılan belirli bir PostgreSQL lisans türünün maliyetleri (varsa).

PostgreSQL için Azure veritabanı, hizmet için% 99,99 SLA garantisi devam ederken her türlü düğüm düzeyinde kesinti için yerleşik yüksek kullanılabilirlik sağlar. Ancak, VM 'Lerde yüksek kullanılabilirlik için, bir PostgreSQL veritabanında bulunan [akış çoğaltma](https://www.postgresql.org/docs/12/warm-standby.html#STREAMING-REPLICATION) gibi yüksek kullanılabilirlik seçeneklerini kullanırsınız. Desteklenen yüksek kullanılabilirlik seçeneğinin kullanılması ek bir SLA sağlamaz. Ancak bu, ek maliyet ve yönetim yüküyle% 99,99 ' den fazla veritabanı kullanılabilirliği elde etmenizi sağlar.

Fiyatlandırma hakkında daha fazla bilgi için aşağıdaki makalelere bakın:
- [PostgreSQL için Azure veritabanı fiyatlandırması](https://azure.microsoft.com/pricing/details/postgresql/server/)
- [Sanal makine fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Yönetim

Birçok işletme için, bir bulut hizmetine geçiş kararı, maliyetle ilgili olduğu gibi yönetim karmaşıklığını boşaltma konusunda çok önemlidir.

IaaS ile Microsoft:

- Temel altyapıyı yönetir.
- Temel alınan donanım ve işletim sistemi için otomatik düzeltme eki uygulama sağlar

PaaS ile Microsoft:

- Temel altyapıyı yönetir.
- Temel alınan donanım, işletim sistemi ve veritabanı altyapısı için otomatik düzeltme eki uygulama sağlar.
- Veritabanının yüksek kullanılabilirliğini yönetir.
- Otomatik olarak yedeklemeleri gerçekleştirir ve olağanüstü durum kurtarma sağlamak için tüm verileri çoğaltır.
- Varsayılan olarak bekleyen ve hareketli verileri şifreler.
- Sunucunuzu izler ve sorgu performansı öngörüleri ve performans önerileri için özellikler sağlar.

PostgreSQL için Azure veritabanı ile veritabanınızı yönetmeye devam edebilirsiniz. Ancak artık veritabanı altyapısını, işletim sistemini veya donanımı yönetmeniz gerekmez. Yönetmeye devam edebilmeniz için öğelerin örnekleri şunlardır:

- Veritabanları
- Oturum açma
- Dizin ayarlama
- Sorgu ayarlama
- Denetim
- Güvenlik

Ayrıca, başka bir veri merkezine yüksek kullanılabilirliği yapılandırmak, yapılandırma veya yönetim gerektirmeyen en az düzeyde olmalıdır.

- Azure VM 'lerinde PostgreSQL ile, işletim sistemi ve PostgreSQL sunucu örneği yapılandırması üzerinde tam denetime sahip olursunuz. Bir VM ile, işletim sisteminin ve veritabanı yazılımının ne zaman güncelleceğine ve hangi düzeltme eklerinin uygulanacağını siz karar verirsiniz. Ayrıca, virüsten koruma uygulaması gibi ek yazılımları ne zaman yükleyeceğinize karar verirsiniz. Düzeltme eki uygulama, yedekleme ve yüksek kullanılabilirliği büyük ölçüde basitleştirmek için bazı otomatik özellikler sağlanır. VM boyutunu, disk sayısını ve bunların depolama yapılandırmalarının boyutunu kontrol edebilirsiniz. Daha fazla bilgi için bkz. [Azure Için sanal makine ve bulut hizmeti boyutları](../virtual-machines/sizes.md).

## <a name="time-to-move-to-azure-postgresql-service-paas"></a>Azure PostgreSQL hizmetine geçiş süresi (PaaS)

- PostgreSQL için Azure veritabanı, geliştirici üretkenliği ve yeni çözümler için hızlı pazar süresi kritik olduğunda bulutta tasarlanan uygulamalar için doğru çözümdür. DBA gibi programlama işlevselliğiyle, hizmet, temel işletim sisteminin ve veritabanının yönetilmesi gereksinimini düşürür, bulut mimarları ve geliştiriciler için uygundur.

- Yeni şirket içi donanım alma zamandan ve masrafından kaçınmak istediğinizde, Azure VM 'lerinde PostgreSQL, hizmet tarafından desteklenmeyen veya temel alınan işletim sistemine erişim gerektiren PostgreSQL altyapısının ayrıntılı bir denetimini ve özelleştirmesini gerektiren uygulamalar için doğru çözümdür.

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/)Için Azure veritabanı fiyatlandırması.
- İlk sunucunuzu oluşturarak başlayın.
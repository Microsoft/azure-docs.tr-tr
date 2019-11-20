---
title: 'Azure ExpressRoute: devre yapılandırma iş akışları'
description: Bu sayfada, ExpressRoute bağlantı hattı ve eşlemeleri yapılandırmak için iş akışlarını gösterilir.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: ae6c2b7257ee6a8184f3a5bb002f24cb75a86d67
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083334"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Devre sağlama ve devre durumları için ExpressRoute iş akışları
Bu sayfa sağlama ve yüksek düzeyde yapılandırma iş akışlarını yönlendirme hizmeti size kılavuzluk eder.

![bağlantı hattı iş akışı](./media/expressroute-workflows/expressroute-circuit-workflow.png)

Aşağıdaki şekil ve ilgili adımlarda, sağlanan bir ExpressRoute devresi için izlemeniz gereken görevleri uçtan uca gösterilmektedir. 

1. Bir ExpressRoute bağlantı hattı yapılandırmak için PowerShell kullanın. Bölümündeki yönergeleri [oluşturma ExpressRoute bağlantı hatları](expressroute-howto-circuit-classic.md) makale daha fazla ayrıntı için.
2. Bağlantı, hizmet sağlayıcısından sipariş. Bu işlem değişir. Bağlantı sipariş hakkında daha fazla ayrıntı için bağlantı sağlayıcınıza başvurun.
3. Devre başarıyla sağlama durumu PowerShell aracılığıyla bir ExpressRoute bağlantı hattı doğrulayarak sağlandığından emin olun. 
4. Yönlendirme etki alanları yapılandırın. Bağlantı sağlayıcınızdan sizin için Katman 3 yönetiyorsa, bağlantı hattı için yönlendirmeyi yapılandıracaksınız. Bağlantı sağlayıcınız yalnızca Katman 2 Hizmetleri sunuyorsa, açıklanan yönergeleri başına yönlendirme yapılandırmalısınız [yönlendirme gereksinimleri](expressroute-routing.md) ve [yönlendirme yapılandırması](expressroute-howto-routing-classic.md) sayfaları.
   
   * Azure özel eşlemeyi etkinleştirmesini - bu eşlemeyi bulut Hizmetleri içindeki sanal ağlar dağıtılan Vm'lere bağlanmak etkinleştirin.

   * Microsoft eşlemesini etkinleştir-Office 365 ' e erişmek için bunu etkinleştirin. Ayrıca, tüm Azure PaaS hizmetlerine Microsoft eşlemesi üzerinden erişilebilir.
     
     > [!IMPORTANT]
     > Ayrı bir ara sunucu kullan / hesaptan Microsoft'a bağlanmak için kenar kullanmak için Internet sağlamalısınız. ExpressRoute ve Internet için aynı edge kullanarak asimetrik yönlendirme neden ve ağınızın bağlantı kesintilerine neden olur.
     > 
     > 
     
     ![Yönlendirme iş akışları](./media/expressroute-workflows/routing-workflow.png)
5. Sanal ağları bağlama ExpressRoute devresine - sanal ağları ExpressRoute bağlantı hattına bağlayabilirsiniz. Yönergeleri izleyerek [sanal ağları bağlamak için](expressroute-howto-linkvnet-arm.md) bağlantı hattınız için. Bu sanal ağlar aynı abonelikte Azure ExpressRoute bağlantı hattı olabilir veya farklı bir abonelikte olabilir.

## <a name="expressroute-circuit-provisioning-states"></a>ExpressRoute bağlantı hattı sağlama durumları
Her bir ExpressRoute bağlantı hattında iki durum vardır:

* Hizmet Sağlayıcısı sağlama durumu
* Durum

Durum Microsoft'un sağlama durumu temsil eder. Bir Expressroute bağlantı hattı oluşturduğunuzda, bu özellik etkin olarak ayarlanır

Bağlantı Sağlayıcısı sağlama durumu bağlantı sağlayıcısı tarafı durumunu temsil eder. Ya da olabilir *NotProvisioned*, *sağlama*, veya *sağlanan*. ExpressRoute bağlantı hattı, onu kullanabilmek sağlanan durumda olması gerekir.

### <a name="possible-states-of-an-expressroute-circuit"></a>Olası bir ExpressRoute bağlantı hattı durumları
Bu bölümde bir ExpressRoute bağlantı hattı için olası durumlar kullanıma listelenir.

**Oluşturma zamanında**

ExpressRoute bağlantı hattı oluşturmak için PowerShell cmdlet'ini çalıştırdığınız hemen sonra aşağıdaki durumundaki ExpressRoute bağlantı hattını görürsünüz.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Bağlantı sağlayıcısı devre sağlama sürecinde olduğunda**

Aşağıdaki durumundaki ExpressRoute bağlantı hattı hizmet anahtarını bağlantı sağlayıcısına geçirilen ve sağlama işlemi başlatıldıktan hemen sonra görürsünüz.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Bağlantı Sağlayıcısı sağlama işlemi tamamlandığında**

Bağlantı sağlayıcısı, sağlama işlemi tamamlandıktan hemen sonra aşağıdaki durumundaki ExpressRoute bağlantı hattını görürsünüz.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled

Ve hazırlanana etkin bağlantı hattını yalnızca durumu da kullanabilmek için olabilir. Bir katman 2 sağlayıcısı kullanıyorsanız, yalnızca bu durumda olduğunda, bağlantı hattı için yönlendirmeyi yapılandırabilirsiniz.

**Ne zaman bağlantı sağlayıcısı devreyi sağlamayı kaldırma**

ExpressRoute bağlantı hattının sağlamasını kaldırmak için hizmet sağlayıcısı istenen hizmet sağlayıcısı sağlamayı kaldırma işlemi tamamlandıktan sonra aşağıdaki duruma ayarlanmış bağlantı hattını görürsünüz.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Gerekli ya da bağlantı hattını silmek için PowerShell cmdlet'lerini çalıştırmak yeniden etkinleştirmeyi seçebilirsiniz.  

> [!IMPORTANT]
> Çalıştırırsanız ServiceProviderProvisioningState sağlanırken bağlantı hattını silmek için PowerShell cmdlet veya sağlanan işlemi başarısız olur. Lütfen önce ExpressRoute bağlantı hattının sağlamasını kaldırmak için bağlantı sağlayıcınıza çalışın ve bağlantı hattı silin. Microsoft, bağlantı hattı bağlantı hattını silmek için PowerShell cmdlet çalıştırılıncaya faturalandırmak devam eder.
> 
> 

## <a name="routing-session-configuration-state"></a>Yönlendirme oturum yapılandırma durumu
Sağlama durumu BGP BGP oturumu Microsoft edge'de etkinleştirilirse bilmenizi sağlar. Durumu eşlemeyi kullanabilmek için etkinleştirilmelidir.

Özellikle de Microsoft eşlemesi için BGP oturumu durumunu denetlemek önemlidir. Sağlama durumu BGP ek olarak, adlı başka bir durum yoktur *tanıtılan genel önekler durumu*. Tanıtılan genel önekler durumu olmalıdır *yapılandırılmış* hem kadar BGP oturumu ve uçtan uca çalışmak için yönlendirme durumu. 

Tanıtılan genel ön eki durumu ayarlanırsa bir *gerekli doğrulama* durumunda, BGP oturumu etkin değil, tanıtılan önekler herhangi bir kayıt defterlerini yönlendirme AS numarasını eşleşmediğinden. 

> [!IMPORTANT]
> Tanıtılan genel önekler durumu ise *el ile doğrulama* durum ile bir destek bileti açmak gerekir [Microsoft Destek](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ve IP adreslerini tanıtılan ile birlikte kendi kanıt ilişkili Otonom sistem numarası.
> 
> 

## <a name="next-steps"></a>Sonraki adımlar
* ExpressRoute bağlantınızı yapılandırın.
  
  * [ExpressRoute bağlantı hattı oluşturma](expressroute-howto-circuit-arm.md)
  * [Yönlendirmeyi yapılandırma](expressroute-howto-routing-arm.md)
  * [ExpressRoute bağlantı hattına bir Sanal Ağ bağlama](expressroute-howto-linkvnet-arm.md)


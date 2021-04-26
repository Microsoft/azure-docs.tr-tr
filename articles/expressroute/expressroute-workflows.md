---
title: 'Azure ExpressRoute: devre yapılandırma iş akışı'
description: Bu sayfada ExpressRoute devreleri ve eşlemeleri yapılandırmak için iş akışı gösterilir
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: duau
ms.custom: contperf-fy21q1
ms.openlocfilehash: 24ad325cae2ee71ad49ee8ee055a83ceb8fa7ef2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101721745"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Devre sağlama ve devre durumları için ExpressRoute iş akışları

Bu makalede, hizmet sağlama ve yapılandırma iş akışlarının yüksek düzeyde yönlendirilmesi gösterilmektedir. Aşağıdaki bölümlerde, bir ExpressRoute bağlantı hattının uçtan uca sağlanması için görevler ana hatlarıyla verilmiştir.

## <a name="workflow-steps"></a>İş akışı adımları

### <a name="1-prerequisites"></a>1. ön koşullar

Önkoşulların karşılandığından emin olun. Tam liste için bkz. [Önkoşullar ve denetim listesi](expressroute-prerequisites.md).

* Bir Azure aboneliği oluşturuldu.
* ExpressRoute ortağıyla fiziksel bağlantı oluşturulmuştur veya ExpressRoute Direct aracılığıyla yapılandırılır. Konumu gözden geçirin, bkz. konum [ve iş ortakları](expressroute-locations-providers.md#partners) ExpressRoute iş ortağı ve ExpressRoute ortak bağlantısını, eşleme konumlarında görüntüleme.

### <a name="2-order-connectivity-or-configure-expressroute-direct"></a>2. bağlantı sıralaması yapın veya ExpressRoute Direct 'i yapılandırın

Hizmet sağlayıcısından bağlantıyı sıralayın veya ExpressRoute Direct 'i yapılandırın.

#### <a name="expressroute-partner-model"></a>ExpressRoute iş ortağı modeli

Hizmet sağlayıcısından bağlantı siparişi. Bu işlem farklılık gösterir. Bağlantı siparişi alma hakkında daha fazla ayrıntı için bağlantı sağlayıcınızla iletişim kurun.

* ExpressRoute iş ortağını seçin
* Eşleme konumunu seçin
* Bant genişliğini seçin
* Faturalandırma modelini seçin
* Standart veya Premium eklentisi seçin

#### <a name="expressroute-direct-model"></a>ExpressRoute doğrudan modeli

* Uygun ExpressRoute doğrudan kapasitesini eşleme konumlarına göre görüntüleyin.
* Azure aboneliğinizde ExpressRoute Direct kaynağını oluşturarak bağlantı noktalarını ayırın.
* Yetkilendirme konumu sağlayıcısından yetkilendirme ve fiziksel çapraz bağlantıları sıralama ve alma.
* [Azure izleyici](expressroute-monitoring-metrics-alerts.md#expressroute-direct-metrics)'yi kullanarak yönetici durumunu etkinleştirin ve ışık düzeylerini ve fiziksel bağlantıyı görüntüleyin.

### <a name="3-create-an-expressroute-circuit"></a>3. bir ExpressRoute bağlantı hattı oluşturma

#### <a name="expressroute-partner-model"></a>ExpressRoute iş ortağı modeli

ExpressRoute iş ortağının bağlantı sağlamaya hazırlanarak emin olun. ExpressRoute bağlantı hattı, hizmet anahtarının verildiği andan itibaren faturalandırılır. Devrenizi oluşturmak için [bir ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) bağlantı hattı oluşturma ' daki yönergeleri kullanın.

#### <a name="expressroute-direct-model"></a>ExpressRoute doğrudan modeli

Fiziksel bağlantının ve yönetici durumunun her iki bağlantı üzerinde de etkinleştirildiğinden emin olun. Rehberlik için [ExpressRoute Direct 'i yapılandırma](how-to-expressroute-direct-portal.md) konusuna bakın. ExpressRoute bağlantı hattı, hizmet anahtarının verildiği andan itibaren faturalandırılır. Devrenizi oluşturmak için [bir ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) bağlantı hattı oluşturma ' daki yönergeleri kullanın.

### <a name="4-service-provider-provisions-connectivity"></a>4. hizmet sağlayıcısı bağlantıyı sağlar

Bu bölüm yalnızca ExpressRoute iş ortağı bağlantı modeliyle ilgilidir:

* Bağlantı sağlayıcısına hizmet anahtarını (s-Key) sağlayın.
* Bağlantı sağlayıcısı için gereken ek bilgileri sağlayın (örneğin, VPN KIMLIĞI).
* Sağlayıcı, yönlendirme yapılandırmasını yönetirse, gerekli ayrıntıları sağlayın.

PowerShell, Azure portal veya CLı kullanarak ExpressRoute bağlantı hattı sağlama durumunu doğrulayarak devre 'nın başarıyla sağlandığını sağlayabilirsiniz.

### <a name="5-configure-routing-domains"></a>5. yönlendirme etki alanlarını yapılandırma

Yönlendirme etki alanlarını yapılandırın. Bağlantı sağlayıcınız katman 3 yapılandırmasını yönetirse, bu, devreniz için yönlendirmeyi yapılandıracaktır. Bağlantı sağlayıcınız yalnızca Katman 2 Hizmetleri sunuyorsa veya ExpressRoute Direct kullanıyorsanız, yönlendirme [gereksinimleri](expressroute-routing.md) ve [yönlendirme yapılandırma](expressroute-howto-routing-classic.md) makalelerinde açıklanan yönergeler temelinde yönlendirmeyi yapılandırmanız gerekir.

#### <a name="for-azure-private-peering"></a>Azure özel eşlemesi için

Azure sanal ağı 'nda dağıtılan VM 'lere ve bulut hizmetlerine bağlanmak için özel eşlemeyi etkinleştirin.

* IPv4 alt ağları:
    * Yol 1 (/30) için eşleme alt ağı
    * Yol 2 (/30) için eşleme alt ağı
* IPv6 alt ağları (isteğe bağlı):
    * Yol 1 için eşleme alt ağı (/126)
    * Yol 2 için eşleme alt ağı (/126)
* Eşleme için VLAN KIMLIĞI
* Eşleme için ASN
* ExpressRoute ASN = 12076
* MD5 karması (Isteğe bağlı)

#### <a name="for-microsoft-peering"></a>Microsoft eşlemesi için

Microsoft 365 gibi Microsoft çevrimiçi hizmetler erişmek için bunu etkinleştirin. Ayrıca, tüm Azure PaaS hizmetlerine Microsoft eşlemesi üzerinden erişilebilir. Internet için kullandığınızdan Microsoft 'a bağlanmak için ayrı bir proxy/kenar kullandığınızdan emin olmanız gerekir. Hem ExpressRoute hem de Internet için aynı kenarı kullanmak asimetrik yönlendirmeye yol açabilir ve ağınız için bağlantı kesintilerine neden olur.

* IPv4 alt ağları:
    * Yol 1 (/30) için eşleme alt ağı-genel IP olmalıdır
    * Yol 2 (/30) için eşleme alt ağı-genel IP olmalıdır
* IPv6 alt ağları (isteğe bağlı):
    * Yol 1 (/126) için eşleme alt ağı-genel IP olmalıdır
    * Yol 2 (/126) için eşleme alt ağı-genel IP olmalıdır
* Eşleme için VLAN KIMLIĞI
* Eşleme için ASN
* Tanıtılan ön ekler-genel IP ön ekleri olmalıdır
* Müşteri ASN (eşleme ASN 'den farklıysa isteğe bağlı)
* IP ve ASN doğrulaması için RıR/IÇ_VERIM_ORANı
* ExpressRoute ASN = 12076
* MD5 karması (Isteğe bağlı)

### <a name="6-start-using-the-expressroute-circuit"></a>6. ExpressRoute devresini kullanmaya başlayın

* Şirket içinden Azure sanal ağına bağlantı sağlamak için Azure sanal ağlarını ExpressRoute bağlantı hattına bağlayabilirsiniz. Rehberlik için [bir sanal ağı bir bağlantı hattına bağlama](expressroute-howto-linkvnet-arm.md) makalesine başvurun. Bu sanal ağlar ExpressRoute devresi ile aynı Azure aboneliğinde olabilir veya farklı bir abonelikte olabilir.
* Microsoft eşlemesi aracılığıyla Azure hizmetleri ve Microsoft bulut hizmetlerine bağlanın.

##  <a name="expressroute-partner-circuit-provisioning-states"></a><a name="expressroute-circuit-provisioning-states"></a>ExpressRoute iş ortağı devre sağlama durumları

Aşağıdaki bölümde, ExpressRoute iş ortağı bağlantı modeli için farklı ExpressRoute devre durumları özetlenmektedir.
Her bir ExpressRoute iş ortağı devresi iki duruma sahiptir:

* **Serviceproviderprovisioningstate** , bağlantı sağlayıcısının tarafındaki durumu temsil eder. *Nottemin* edilebilir, *sağlanıyor* ya da *sağlanabilir*. Eşlemeyi yapılandırmak için ExpressRoute bağlantı hattının sağlanan bir durumda olması gerekir. **Bu durum yalnızca ExpressRoute iş ortağı devrelerine aittir ve bir ExpressRoute doğrudan devresinin özelliklerinde gösterilmez**.

* **Durum** Microsoft 'un sağlama durumunu temsil eder. Bu özellik, bir ExpressRoute bağlantı hattı oluşturduğunuzda etkin olarak ayarlanır

### <a name="possible-states-of-an-expressroute-circuit"></a>Bir ExpressRoute devresine ait olası durumlar

Bu bölümde, ExpressRoute iş ortağı bağlantı modeli altında oluşturulan bir ExpressRoute devresine ait olası durumlar özetlenmektedir.

**Oluşturulma zamanında**

ExpressRoute bağlantı hattı, kaynak oluşturma sırasında aşağıdaki durumları rapor eder.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

**Bağlantı sağlayıcısı, devre sağlama sürecinizdeki olduğunda**

ExpressRoute bağlantı hattı, bağlantı sağlayıcı devreyi sağlamak için çalışırken aşağıdaki durumları bildirir.

```output
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

**Bağlantı sağlayıcısı sağlama işlemini tamamladığında**

Bağlantı sağlayıcısı devresini başarıyla sağladıktan sonra ExpressRoute bağlantı hattı aşağıdaki durumları rapor eder.

```output
ServiceProviderProvisioningState : Provisioned
Status                           : Enabled
```

**Bağlantı sağlayıcısı devre dışı bırakıldığında**

ExpressRoute bağlantı hattının sağlanması gerekiyorsa, hizmet sağlayıcısı sağlamayı kaldırma işlemini tamamladıktan sonra devre aşağıdaki durumları rapor eder.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

Gerekirse yeniden etkinleştirmeyi seçebilir veya devre dışı bırakmak için PowerShell cmdlet 'lerini çalıştırabilirsiniz.  

> [!IMPORTANT]
> ServiceProviderProvisioningState sağlama veya sağlama olduğunda bir devre silinemez. Bağlantı sağlayıcısı silinmeden önce devre sağlamasını kaldırmalıdır. Microsoft, ExpressRoute bağlantı hattı kaynağı Azure 'da silinene kadar devreyi faturalandıralmaya devam edecektir.
> 

## <a name="routing-session-configuration-state"></a>Yönlendirme oturumu yapılandırma durumu

BGP sağlama durumu, BGP oturumunun Microsoft Edge üzerinde etkin olup olmadığını bildirir. Özel veya Microsoft eşlemesi kullanmak için durumun etkin olması gerekir.

BGP oturum durumunu özellikle Microsoft eşlemesi için denetlemek önemlidir. BGP sağlama durumuna ek olarak *tanıtılan genel ön ekler durumu* adlı başka bir durum da vardır. Tanıtılan genel ön eklerin durumu, hem BGP oturumunun açık olması hem de yönlendirmenin uçtan uca çalışması için *yapılandırılmış* durumda olmalıdır. 

Tanıtılan genel ön ek durumu *doğrulama gerekli* durumuna ayarlandıysa, tanıtılan ön ekler yönlendirme kayıt DEFTERLERININ hiçbirinde as NUMARASıYLA eşleşmediğinden BGP oturumu etkinleştirilmez.

> [!IMPORTANT]
> Tanıtılan genel ön ekler *el ile doğrulama* durumundaysa, [Microsoft desteği](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ile bir destek bileti açmanız ve ilgili otonom SISTEM numarasıyla birlikte tanıtılan IP adreslerinden sahip olduğunuz konusunda kanıt sağlamanız gerekir.
> 
> 

## <a name="next-steps"></a>Sonraki adımlar

* ExpressRoute bağlantınızı yapılandırın.
  
  * [ExpressRoute bağlantı hattı oluşturma](expressroute-howto-circuit-arm.md)
  * [Yönlendirmeyi yapılandırma](expressroute-howto-routing-arm.md)
  * [ExpressRoute bağlantı hattına bir Sanal Ağ bağlama](expressroute-howto-linkvnet-arm.md)

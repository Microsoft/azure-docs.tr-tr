---
title: Traffic Manager uç noktası türleri | Microsoft Docs
description: Bu makalede, Azure Traffic Manager ile kullanılabilecek farklı uç nokta türleri açıklanmaktadır
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: allensu
ms.openlocfilehash: 9de5b161c6bb1897058898dddd620ad093f148be
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69981065"
---
# <a name="traffic-manager-endpoints"></a>Traffic Manager uç noktaları

Microsoft Azure Traffic Manager, ağ trafiğinin farklı veri merkezlerinde çalışan uygulama dağıtımlarına nasıl dağıtıldığını denetlemenize olanak tanır. Her uygulama dağıtımını Traffic Manager ' uç nokta ' olarak yapılandırırsınız. Traffic Manager bir DNS isteği aldığında, DNS yanıtında geri dönmek için kullanılabilir bir uç nokta seçer. Traffic Manager, geçerli uç nokta durumu ve trafik yönlendirme yöntemi üzerinde seçimi temel alır. Daha fazla bilgi için bkz. [Traffic Manager nasıl kullanılır](traffic-manager-how-it-works.md).

Traffic Manager tarafından desteklenen üç uç nokta türü vardır:

* Azure **uç noktaları** , Azure 'da barındırılan hizmetler için kullanılır.
* **Dış uç noktalar** , şirket içinde ya da farklı bir barındırma sağlayıcısı ile Azure dışında barındırılan IPv4/IPv6 adresleri, FQDN 'ler veya hizmetler için kullanılır.
* **Iç Içe geçmiş uç noktalar** , daha büyük ve daha karmaşık dağıtımlar ihtiyaçlarını desteklemek için daha esnek trafik yönlendirme şemaları oluşturmak üzere Traffic Manager profillerini birleştirmek için kullanılır.

Farklı türlerin uç noktalarının tek bir Traffic Manager profilinde nasıl birleştirileceği konusunda bir kısıtlama yoktur. Her profil, uç nokta türlerinin herhangi bir karışımını içerebilir.

Aşağıdaki bölümlerde her bir uç nokta türü daha ayrıntılı olarak açıklanır.

## <a name="azure-endpoints"></a>Azure uç noktaları

Azure uç noktaları, Traffic Manager Azure tabanlı hizmetler için kullanılır. Aşağıdaki Azure Kaynak türleri desteklenir:

* PaaS bulut hizmetleri.
* Web Apps
* Web uygulaması Yuvaları
* Publicıpaddress kaynakları (doğrudan ya da bir Azure Load Balancer aracılığıyla VM 'lere bağlanabilir). Publicıpaddress, bir Traffic Manager profilinde kullanılmak üzere atanmış bir DNS adına sahip olmalıdır.

Publicıpaddress kaynakları Azure Resource Manager kaynaklardır. Klasik dağıtım modelinde mevcut değildir. Bu nedenle, yalnızca Traffic Manager Azure Resource Manager deneyimlerinde desteklenir. Diğer uç nokta türleri hem Kaynak Yöneticisi hem de klasik dağıtım modeli aracılığıyla desteklenir.

Azure uç noktaları kullanılırken, Traffic Manager bir Web uygulamasının durdurulup başlatıldığını algılar. Bu durum uç nokta durumuna yansıtılır. Ayrıntılar için [Traffic Manager uç nokta izleme](traffic-manager-monitoring.md#endpoint-and-profile-status) bölümüne bakın. Temel alınan hizmet durdurulduğunda Traffic Manager uç nokta sistem durumu denetimleri gerçekleştirmez veya uç noktaya doğrudan trafik vermez. Durdurulan örnek için Traffic Manager faturalandırma olayı gerçekleşmez. Hizmet yeniden başlatıldığında faturalandırma devam eder ve uç nokta trafik almaya uygun olur. Bu algılama, Publicıpaddress uç noktaları için geçerlidir.

## <a name="external-endpoints"></a>Dış uç noktalar

Dış uç noktalar IPv4/IPv6 adresleri, FQDN 'Ler veya Azure dışındaki hizmetler için kullanılır. IPv4/IPv6 adres uç noktalarının kullanımı, Traffic Manager 'ın bir DNS adı olması gerekmeden uç noktaların sistem durumunu denetlemesini sağlar. Sonuç olarak, bu uç noktayı bir yanıtta döndürürken Traffic Manager bir/AAAA kaydı olan sorgulara yanıt verebilir. Azure dışındaki hizmetler, şirket içinde veya farklı bir sağlayıcı ile barındırılan bir hizmet içerebilir. Dış uç noktalar, yalnızca dış uç noktalar olabilen IPv4 veya IPv6 adresleri olarak belirtilen uç noktalar dışında aynı Traffic Manager profilinde Azure Uç Noktaları ile birlikte kullanılabilir. Azure uç noktalarını dış uç noktalarla birleştirmek çeşitli senaryolara izin vermez:

* Azure kullanarak etkin-etkin veya etkin-Pasif yük devretme modelinde mevcut bir şirket içi uygulama için daha fazla artıklık sağlayın. 
* Trafiği kendileriyle ilişkili bir DNS adı olmayan uç noktalara yönlendirin. Ayrıca, döndürülen bir DNS adının IP adresini almak için ikinci bir DNS sorgusu çalıştırma gereksinimini ortadan kaldırarak genel DNS arama gecikmesini azaltın.
* Dünyanın dört bir yanındaki kullanıcılar için uygulama gecikmesini azaltın, mevcut şirket içi bir uygulamayı Azure 'da ek coğrafi konumlara genişletin. Daha fazla bilgi için bkz. [Traffic Manager ' performans ' trafiği yönlendirme](traffic-manager-routing-methods.md#performance).
* Azure kullanarak isteğe bağlı bir ani artış karşılamak için, mevcut bir şirket içi uygulama için sürekli olarak veya ' patlama-buluta ' çözümü olarak ek kapasite sağlayın.

Bazı durumlarda, Azure hizmetlerine başvurmak için dış uç noktalar kullanmak yararlı olur (örnekler için bkz. [SSS](traffic-manager-faqs.md#traffic-manager-endpoints)). Bu durumda, durum denetimleri dış bitiş noktaları fiyatı değil, Azure bitiş noktaları ücretine göre faturalandırılır. Bununla birlikte, Azure uç noktalarından farklı olarak, temeldeki hizmeti durdurur veya silerseniz, Traffic Manager uç noktasını devre dışı bırakana veya silme tamamlanana kadar sistem durumu denetimi faturalandırmaya devam edersiniz.

## <a name="nested-endpoints"></a>İç içe geçmiş uç noktalar

İç içe geçmiş uç noktalar esnek trafik yönlendirme şemaları oluşturmak ve daha büyük, karmaşık dağıtımlar ihtiyaçlarını desteklemek için birden çok Traffic Manager profilini birleştirir. Iç Içe geçmiş uç noktalar ile ' alt ' profili bir uç nokta olarak ' Parent ' profiline eklenir. Alt ve üst profiller, diğer iç içe profiller de dahil olmak üzere herhangi bir türde diğer uç noktaları içerebilir. Daha fazla bilgi için bkz. [iç içe Traffic Manager profilleri](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Uç noktalar olarak Web Apps

Traffic Manager Web Apps bitiş noktaları olarak yapılandırılırken bazı ek konular geçerlidir:

1. Yalnızca ' standart ' SKU 'sunda veya yukarıdaki Web Apps Traffic Manager kullanım için uygundur. Daha düşük bir SKU 'nun Web uygulamasını ekleme girişimleri başarısız olur. Mevcut bir Web uygulamasının SKU 'SU eski sürüme düşürüyor Traffic Manager, bu Web uygulamasına artık trafik göndermemek için oluşur. Desteklenen planlar hakkında daha fazla bilgi için [App Service planlarına](https://azure.microsoft.com/pricing/details/app-service/plans/) bakın
2. Bir uç nokta bir HTTP isteği aldığında, isteği hangi Web uygulamasının hizmet etmesi gerektiğini belirleyen istekteki ' host ' üst bilgisini kullanır. Ana bilgisayar üst bilgisi, isteği başlatmak için kullanılan DNS adını içerir, örneğin ' contosoapp.azurewebsites.net '. Web uygulamanızla farklı bir DNS adı kullanmak için, DNS adının uygulama için özel bir etki alanı adı olarak kaydedilmesi gerekir. Bir Web uygulaması uç noktasını Azure uç noktası olarak eklerken, uygulama için Traffic Manager profili DNS adı otomatik olarak kaydedilir. Uç nokta silindiğinde bu kayıt otomatik olarak kaldırılır.
3. Her bir Traffic Manager profili, her bir Azure bölgesinden en fazla bir Web uygulaması uç noktasına sahip olabilir. Bu kısıtlamayı geçici olarak çözmek için bir Web uygulamasını dış uç nokta olarak yapılandırabilirsiniz. Daha fazla bilgi için bkz. [SSS](traffic-manager-faqs.md#traffic-manager-endpoints).

## <a name="enabling-and-disabling-endpoints"></a>Uç noktaları etkinleştirme ve devre dışı bırakma

Traffic Manager bir uç noktanın devre dışı bırakılması, trafiği bakım modunda veya yeniden dağıtılmakta olan bir uç noktadan geçici olarak kaldırmak için yararlı olabilir. Uç nokta yeniden çalışmaya başladıktan sonra yeniden etkinleştirilebilir.

Uç noktalar Traffic Manager portalı, PowerShell, CLı veya REST API aracılığıyla etkinleştirilebilir ve devre dışı bırakılabilir.

> [!NOTE]
> Azure uç noktasının devre dışı bırakılması, Azure 'daki dağıtım durumuyla ilgili hiçbir şey yapmaz. Bir Azure hizmeti (örneğin, bir VM veya Web uygulaması gibi) çalışır durumda kalır ve Traffic Manager devre dışı bırakıldığında bile trafiği alabilecektir. Trafik, Traffic Manager profili DNS adı üzerinden değil, doğrudan hizmet örneğine bağlanabilir. Daha fazla bilgi için bkz. [Traffic Manager nasıl kullanılır](traffic-manager-how-it-works.md).

Trafiği almak için her uç noktanın geçerli uygunluğu aşağıdaki etkenlere bağlıdır:

* Profil durumu (etkin/devre dışı)
* Uç nokta durumu (etkin/devre dışı)
* Bu uç nokta için durum denetimlerinin sonuçları

Ayrıntılar için bkz. [Traffic Manager uç nokta izleme](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Traffic Manager DNS düzeyinde çalıştığından, herhangi bir uç noktaya ait varolan bağlantıları etkilemez. Bir uç nokta kullanılamadığında Traffic Manager yeni bağlantıları kullanılabilir başka bir uç noktaya yönlendirir. Ancak, devre dışı veya sağlıksız uç noktanın arkasındaki konak, Bu oturumlar sonlandırılana kadar mevcut bağlantılar aracılığıyla trafik almaya devam edebilir. Uygulamalar, trafiğin mevcut bağlantılardan boşalmaya izin vermek için oturum süresini sınırlımalıdır.

Bir profildeki tüm uç noktalar devre dışıysa veya profil devre dışıysa, Traffic Manager yeni bir DNS sorgusuna ' NXDOMAIN ' yanıtı gönderir.

## <a name="faqs"></a>SSS

* [Birden çok abonelikteki uç noktalarla Traffic Manager kullanabilir miyim?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions)

* [Bulut hizmeti ' hazırlama ' yuvaları ile Traffic Manager kullanabilir miyim?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-cloud-service-staging-slots)

* [Traffic Manager IPv6 uç noktalarını destekliyor mu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-support-ipv6-endpoints)

* [Aynı bölgede birden fazla Web uygulamasıyla Traffic Manager kullanabilir miyim?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region)

* [Traffic Manager profillerinin Azure uç noktalarını farklı bir kaynak grubuna taşımak Nasıl yaparım??](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription)

## <a name="next-steps"></a>Sonraki adımlar

* [Traffic Manager nasıl çalıştığını](traffic-manager-how-it-works.md)öğrenin.
* Traffic Manager [uç nokta izleme ve otomatik yük devretme](traffic-manager-monitoring.md)hakkında bilgi edinin.
* Traffic Manager [trafik yönlendirme yöntemleri](traffic-manager-routing-methods.md)hakkında bilgi edinin.

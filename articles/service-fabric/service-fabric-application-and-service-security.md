---
title: Azure Service Fabric uygulama güvenliği hakkında bilgi edinin | Microsoft Docs
description: Service Fabric 'da mikro hizmet uygulamalarının güvenli bir şekilde çalıştırılmasına genel bakış. Farklı güvenlik hesapları altında hizmetler ve başlatma betiği çalıştırmayı, kullanıcıların kimliklerini doğrulamayı ve yetkilendirmeyi, uygulama gizli dizilerini yönetmeyi, güvenli hizmet iletişimlerini yönetmeyi, API ağ geçidini kullanmayı ve bekleyen uygulama verilerini güvenli hale getirme hakkında bilgi edinin.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: atsenthi
ms.openlocfilehash: 75a82a0915414d24ab9c58ea15d3fdc9c1922c63
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600063"
---
# <a name="service-fabric-application-and-service-security"></a>Uygulama ve hizmet güvenliği Service Fabric
Mikro hizmet mimarisi [birçok avantaj](service-fabric-overview-microservices.md)getirebilir. Ancak mikro hizmetlerin güvenliğini yönetmek, bir zorluk ve geleneksel tek parçalı uygulamalar güvenliğini yönetmekten farklıdır. 

Tek bir, uygulama genellikle bir ağ içindeki bir veya daha fazla sunucu üzerinde çalışır ve açığa çıkarılan bağlantı noktalarını ve API 'Leri ve IP adresini belirlemek daha kolay olur. Genellikle bir çevre veya sınır ve korunacak bir veritabanı vardır. Bir güvenlik ihlali veya saldırı nedeniyle sistemin güvenliği tehlikeye girerse, sistem içindeki her şeyin saldırgan tarafından kullanılabilir olması olasıdır. Mikro hizmetlerle, sistem daha karmaşıktır.  Hizmetler, pek çok konak arasında dağıtılır ve dağıtılır ve konaktan konağa geçirilir.  Uygun güvenlik sayesinde bir saldırganın gerçekleştirebileceği ayrıcalıkları ve tek bir saldırıya bir hizmete ulaşarak tek bir saldırıya açık olan veri miktarını sınırlayabilirsiniz.  İletişim iç değildir, ancak bir ağ üzerinden gerçekleşir ve hizmetler arasında sunulan birçok bağlantı noktası ve etkileşim vardır. Bu hizmet etkileşimlerinin ne olduğunu ve ne zaman meydana gelebileceklerini bilmek, uygulama güveniniz için önemlidir.

Bu makale, mikro hizmetler güvenliğine yönelik bir kılavuz değildir, çevrimiçi olarak sunulan pek çok kaynak vardır, ancak Service Fabric farklı güvenlik yönlerinin nasıl gerçekleştirebileceğinizi açıklar.

## <a name="authentication-and-authorization"></a>Kimlik doğrulama ve yetkilendirme
Genellikle, bir hizmet tarafından sunulan kaynakların ve API 'Lerin belirli Güvenilen Kullanıcı veya istemcilerle sınırlı olması gerekir. Kimlik doğrulaması, bir kullanıcının kimliğini güvenilir bir şekilde belirlememe işlemidir.  Yetkilendirme, API 'Leri veya hizmetleri bazı kimliği doğrulanmış kullanıcılar için kullanılabilir hale getiren işlemdir, ancak diğerleri için kullanılamaz.

### <a name="authentication"></a>Authentication
API düzeyi güven kararlarını yapmanın ilk adımı kimlik doğrulamadır. Kimlik doğrulaması, bir kullanıcının kimliğini güvenilir bir şekilde belirlememe işlemidir.  Mikro hizmet senaryolarında, kimlik doğrulaması genellikle merkezi olarak işlenir. Bir API ağ geçidi kullanıyorsanız, [kimlik doğrulamasını](/azure/architecture/patterns/gateway-offloading) ağ geçidine devreedebilirsiniz. Bu yaklaşımı kullanırsanız, ağ geçidinden gelen veya olmayan iletilerin kimliğini doğrulamak için ek güvenlik yoksa, tek tek hizmetlere doğrudan ulaşılamadığından emin olun (API ağ geçidi olmadan).

Hizmetlere doğrudan erişilemiyorsa, kullanıcıların kimliğini doğrulamak için Azure Active Directory gibi bir kimlik doğrulama hizmeti veya güvenlik belirteci hizmeti (STS) görevi gören ayrılmış bir kimlik doğrulama mikro hizmeti kullanılabilir. Güven kararları, güvenlik belirteçleri veya tanımlama bilgileriyle hizmetler arasında paylaşılır. 

ASP.NET Core için, [kullanıcıların](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) kimliğini doğrulamaya yönelik birincil mekanizma ASP.NET Core kimlik Üyelik sistemidir. ASP.NET Core kimlik, geliştirici tarafından yapılandırılan bir veri deposundaki kullanıcı bilgilerini (oturum açma bilgileri, roller ve talepler dahil) depolar. ASP.NET Core kimlik iki öğeli kimlik doğrulamasını destekler.  Dış kimlik doğrulama sağlayıcıları da desteklenir, böylece kullanıcılar Microsoft, Google, Facebook veya Twitter gibi sağlayıcılardan mevcut kimlik doğrulama süreçlerini kullanarak oturum açabilirler.

### <a name="authorization"></a>Authorization
Kimlik doğrulamasından sonra, hizmetlerin Kullanıcı erişimini yetkilendirmeniz veya kullanıcının ne yapabileceklerini belirlememeniz gerekir. Bu işlem, bir hizmetin API 'Leri bazı kimliği doğrulanmış kullanıcılar için kullanılabilir olmasına izin verir. Yetkilendirme, bir kullanıcının kim olduğunu belirlememe işlemi olan kimlik doğrulamasından ve birbirinden bağımsızdır. Kimlik doğrulaması geçerli kullanıcı için bir veya daha fazla kimlik oluşturabilir.

[ASP.NET Core yetkilendirme](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) , kullanıcıların rollerine bağlı olarak veya özel ilkeye göre yapılabilir ve bu da talepleri veya diğer buluşsal yöntemleri inceleyerek olabilir.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>API ağ geçidi kullanarak erişimi kısıtlama ve güvenli hale getirme
Bulut uygulamalarının normalde kullanıcılar, cihazlar ve diğer uygulamalara tek giriş noktası sağlamak için bir ön uç ağ geçidine ihtiyacı vardır. [API ağ geçidi](/azure/architecture/microservices/gateway) , istemciler ve hizmetler arasında yer alır ve uygulamanızın sağladığı tüm hizmetlere giriş noktasıdır. Ters proxy görevi görür ve istekleri istemcilerden hizmetlere yönlendirme. Ayrıca kimlik doğrulama ve yetkilendirme, SSL sonlandırma ve hız sınırlandırma gibi çeşitli çapraz işlemleri gerçekleştirebilir. Bir ağ geçidi dağıtmazsanız, istemcilerin istekleri doğrudan ön uç hizmetlerine gönderebilmesi gerekir.

Service Fabric ağ geçidi, [ASP.NET Core bir uygulama](service-fabric-reliable-services-communication-aspnetcore.md)gibi herhangi bir durum bilgisi olmayan hizmet veya [Traefik](https://docs.traefik.io/), [Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [IoT Hub](https://docs.microsoft.com/azure/iot-hub/)veya [Azure API Management](https://docs.microsoft.com/azure/api-management)gibi trafik girişi için tasarlanan başka bir hizmet olabilir.

API Management, arka uç Service Fabric hizmetlerinize zengin bir yönlendirme kuralları kümesiyle API 'Ler yayımlamanıza olanak tanıyan doğrudan Service Fabric ile tümleşir.  Arka uç hizmetlerine erişimi güvenli hale getirebilirsiniz, azaltmayı kullanarak DOS saldırılarını engelleyebilir veya API anahtarlarını, JWT belirteçlerini, sertifikaları ve diğer kimlik bilgilerini doğrulayabilirsiniz. Daha fazla bilgi edinmek için [Azure API Management genel bakış ile Service Fabric](service-fabric-api-management-overview.md)okuyun.

## <a name="manage-application-secrets"></a>Uygulama parolalarını yönetme
Gizlilikler, depolama bağlantı dizeleri, parolalar veya düz metin olarak işlenmemelidir diğer değerler gibi herhangi bir hassas bilgi olabilir. Bu makale, anahtarları ve gizli dizileri yönetmek için Azure Key Vault kullanır. Ancak, bir uygulamadaki gizli dizileri *kullanmak* , uygulamaların herhangi bir yerde barındırılan bir kümeye dağıtılmasına olanak tanımak için bulut platformudur.

Hizmet yapılandırma ayarlarını yönetmenin önerilen yolu, [hizmet yapılandırma paketleri][config-package]aracılığıyla yapılır. Yapılandırma paketleri, sistem durumu doğrulama ve otomatik geri alma ile yönetilen sıralı yükseltmeler aracılığıyla sürümlenmiş ve güncelleştirilebilir. Bu, küresel hizmet kesintisi olasılığını azalttığı için genel yapılandırma için tercih edilir. Şifrelenmiş gizli diziler özel durum değildir. Service Fabric, bir yapılandırma paketi Settings. xml dosyasındaki değerleri şifrelemek ve şifrelerini çözmek için sertifika şifrelemesini kullanan yerleşik özelliklere sahiptir.

Aşağıdaki diyagramda Service Fabric uygulamasındaki gizli yönetim için temel akış gösterilmektedir:

![gizli dizi yönetimine genel bakış][overview]

Bu akışta dört ana adım vardır:

1. Veri şifreleme sertifikası alın.
2. Sertifikayı kümenize yükler.
3. Bir uygulamayı sertifikayla dağıtmakta ve bir hizmetin Settings. xml yapılandırma dosyasına eklerken gizli değerleri şifreleyin.
4. Şifrelenmiş değerleri, aynı şifreleme sertifikasıyla şifre çözme yoluyla ayarlar. xml dosyasından okuyun. 

[Azure Key Vault][key-vault-get-started] , sertifikalar için güvenli bir depolama konumu olarak ve Azure 'daki Service Fabric kümelerine yüklü sertifikaları almanın bir yolu olarak kullanılır. Azure 'a dağıtmayın, Service Fabric uygulamalardaki gizli dizileri yönetmek için Key Vault kullanmanız gerekmez.

Bir örnek için bkz. [uygulama gizli dizilerini yönetme](service-fabric-application-secret-management.md).

## <a name="secure-the-hosting-environment"></a>Barındırma ortamının güvenliğini sağlama
Azure Service Fabric kullanarak kümede çalışan uygulamaların farklı Kullanıcı hesapları altında güvenli hale getirebilirsiniz. Service Fabric Ayrıca, uygulamalar tarafından Kullanıcı hesapları (örneğin, dosyalar, dizinler ve sertifikalar) altında dağıtım sırasında kullanılan kaynakların korunmasına yardımcı olur. Bu, paylaşılan barındırılan bir ortamda bile çalışan uygulamaları diğerinden daha güvenli hale getirir.

Uygulama bildirimi, gerekli olan hizmetleri ve güvenli kaynakları çalıştıran güvenlik sorumlularını (kullanıcılar ve gruplar) bildirir.  Bu güvenlik ilkelerine, ilkelerde, örneğin, farklı çalıştır, uç nokta bağlama, paket paylaşımı veya güvenlik erişim ilkeleri gibi başvurulur.  İlkeler daha sonra uygulama bildiriminin **servicemanifestımport** bölümünde hizmet kaynaklarına uygulanır.

Sorumluları bildirirken, bir veya daha fazla kullanıcının birlikte yönetilmesi için her gruba eklenebilmesi için Kullanıcı grupları tanımlayabilir ve oluşturabilirsiniz. Bu, farklı hizmet giriş noktaları için birden fazla kullanıcı olduğunda ve grup düzeyinde kullanılabilen belirli ortak ayrıcalıklara sahip olmaları gerektiğinde yararlıdır.

Varsayılan olarak, Service Fabric uygulamalar yapı. exe işleminin altında çalıştığı hesap altında çalışır. Service Fabric Ayrıca uygulama bildiriminde belirtilen yerel bir kullanıcı hesabı veya yerel sistem hesabı altında uygulama çalıştırma yeteneği sağlar. Daha fazla bilgi için bkz. [bir hizmeti yerel kullanıcı hesabı veya yerel sistem hesabı olarak çalıştırma](service-fabric-application-runas-security.md).  Ayrıca, [bir hizmet başlatma betiğini yerel kullanıcı veya sistem hesabı olarak da çalıştırabilirsiniz](service-fabric-run-script-at-service-startup.md).

Windows bağımsız kümesinde Service Fabric çalıştırırken, [Active Directory etki alanı hesapları](service-fabric-run-service-as-ad-user-or-group.md) veya [Grup yönetilen hizmet hesapları](service-fabric-run-service-as-gmsa.md)altında bir hizmet çalıştırabilirsiniz.

## <a name="secure-containers"></a>Güvenli kapsayıcılar
Service Fabric, bir kapsayıcı içindeki hizmetler için bir Windows veya Linux kümesindeki düğümlere yüklenmiş bir sertifikaya (sürüm 5,7 veya üzeri) erişmek için bir mekanizma sağlar. Bu PFX sertifikası uygulamanın veya hizmetin kimliğini doğrulamak veya diğer hizmetlerle güvenli iletişim sağlamak için kullanılabilir. Daha fazla bilgi için bkz. [bir sertifikayı kapsayıcıya aktarma](service-fabric-securing-containers.md).

Ayrıca, Service Fabric Windows kapsayıcıları için gMSA 'Yı (grup yönetilen hizmet hesapları) da destekler. Daha fazla bilgi için bkz. [Windows kapsayıcıları Için gMSA 'Yı ayarlama](service-fabric-setup-gmsa-for-windows-containers.md).

## <a name="secure-service-communication"></a>Güvenli hizmet iletişimi
Service Fabric, bir hizmet, genellikle birden çok VM arasında dağıtılan bir Service Fabric kümesinde bir yerde çalışır. Service Fabric, hizmet iletişimlerinizi güvenli hale getirmek için çeşitli seçenekler sağlar.

[ASP.NET Core veya Java](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) Web hizmetlerinde HTTPS uç noktalarını etkinleştirebilirsiniz.

Ters proxy ve hizmetler arasında güvenli bağlantı kurabilir ve böylece uçtan uca güvenli kanal sağlayabilirsiniz. Güvenli hizmetlere bağlanma yalnızca ters proxy, HTTPS üzerinde dinlemek üzere yapılandırıldığında desteklenir. Ters proxy 'yi yapılandırma hakkında daha fazla bilgi için [Azure Service Fabric 'de ters proxy](service-fabric-reverseproxy.md)'yi okuyun.  [Güvenli bir hizmete bağlanma](service-fabric-reverseproxy-configure-secure-communication.md) , ters proxy ve hizmetler arasında nasıl güvenli bağlantı kurulacağını açıklar.

Reliable Services uygulama çerçevesi, güvenliği artırmak için kullanabileceğiniz, önceden oluşturulmuş birkaç iletişim yığını ve aracı sağlar. Hizmet uzaktan iletişimini ( [C#](service-fabric-reliable-services-secure-communication.md) veya [Java](service-fabric-reliable-services-secure-communication-java.md)) veya [WCF](service-fabric-reliable-services-secure-communication-wcf.md)'yi kullanırken Güvenliği geliştirmeyi öğrenin.

## <a name="encrypt-application-data-at-rest"></a>Bekleyen uygulama verilerini şifreleme
Azure 'da çalışan bir Service Fabric kümesindeki her [düğüm türü](service-fabric-cluster-nodetypes.md) bir [sanal makine ölçek kümesi](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)tarafından desteklenir. Azure Resource Manager şablonunu kullanarak, Service Fabric kümesini oluşturan ölçek kümelerine veri diskleri ekleyebilirsiniz.  Hizmetleriniz ekli bir veri diskine veri kaydetmişse, uygulama verilerinizi korumak için [Bu veri disklerini şifreleyebilirsiniz](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) .

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Sonraki adımlar
* [Hizmet başlangıcında bir kurulum betiği Çalıştır](service-fabric-run-script-at-service-startup.md)
* [Hizmet bildiriminde kaynakları belirtme](service-fabric-service-manifest-resources.md)
* [Uygulama dağıtma](service-fabric-deploy-remove-applications.md)
* [Küme güvenliği hakkında bilgi edinin](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png
---
title: Azure Kubernetes hizmetinde dağıtılan mikro hizmetlerle Azure API Management kullanma | Microsoft Docs
description: Bu makalede, AKS ile API Management dağıtma seçenekleri açıklanmaktadır
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/14/2019
ms.author: apimpm
ms.openlocfilehash: 36dfc8c906c52c6822e583db3a08c891306f7e78
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047941"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>Azure Kubernetes hizmetinde dağıtılan mikro hizmetlerle Azure API Management kullanma

Mikro hizmetler, API 'Leri oluşturmak için mükemmeldir. [Azure Kubernetes hizmeti](https://azure.microsoft.com/services/kubernetes-service/) (aks) sayesinde, bulutta [mikro hizmet tabanlı bir mimariyi](/azure/architecture/guide/architecture-styles/microservices) hızlıca dağıtabilir ve çalıştırabilirsiniz. Daha sonra, mikro hizmetlerinizi iç ve dış tüketim için API olarak yayımlamak üzere [Azure API Management](https://aka.ms/apimrocks) (API Management) özelliğinden yararlanabilirsiniz. Bu makalede, AKS ile API Management dağıtma seçenekleri açıklanmaktadır. Kubernetes, API Management ve Azure ağı hakkında temel bilgileri varsayar. 

## <a name="background"></a>Arka Plan

Mikro Hizmetleri tüketim için API 'Ler olarak yayımlarken, mikro hizmetler ve bunları kullanan istemciler arasındaki iletişimin yönetilmesi zor olabilir. Kimlik doğrulama, yetkilendirme, azaltma, önbelleğe alma, dönüştürme ve izleme gibi çok sayıda çapraz kesme sorunu vardır. Bu sorunlar, mikro hizmetlerin iç veya dış istemcilere sunulmadığına bakılmaksızın geçerlidir. 

[API ağ geçidi](/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern) modelinde bu sorunlar ele alınmaktadır. Bir API ağ geçidi, mikro hizmetlerden bir ön kapı görevi görür, mikro hizmetinizdeki istemcileri ayırır, ek bir güvenlik katmanı ekler ve çapraz kesme sorunlarını işleme yükünü ortadan kaldırarak mikro hizmetlerinizin karmaşıklığını azaltır. 

[Azure API Management](https://aka.ms/apimrocks) , API ağ geçidi ihtiyaçlarınızı çözmeye yönelik anahtar bir çözümdür. Mikro hizmetleriniz için hızlı bir şekilde tutarlı ve modern bir ağ geçidi oluşturabilir ve bunları API olarak yayımlayabilirsiniz. Tam yaşam döngüsü API yönetimi çözümü olarak, API bulma, API yaşam döngüsü yönetimi ve API Analytics için bir self servis geliştirici portalı dahil ek yetenekler de sağlar.

Birlikte kullanıldığında, AKS ve API Management, mikro hizmet tabanlı API 'lerinizi dağıtmak, yayımlamak, güvenli hale getirmek, izlemek ve yönetmek için bir platform sağlar. Bu makalede, API Management ile birlikte AKS 'leri dağıtmaya yönelik birkaç seçenekten daha fazla gideceğiz. 

## <a name="kubernetes-services-and-apis"></a>Kubernetes Hizmetleri ve API 'Leri

Bir Kubernetes kümesinde kapsayıcılar, kısa ömürlü olan ve yaşam döngüsüne sahip olan [pods](https://kubernetes.io/docs/concepts/workloads/pods/pod/)'de dağıtılır. Bir çalışan düğümü söz konusu olduğunda, düğümde çalışan düğüm kaybedilir. Bu nedenle, Pod 'un IP adresi herhangi bir zamanda değişebilir. Pod ile iletişim kurmak için bu uygulamayı kullanmıyoruz. 

Bu sorunu çözmek için Kubernetes [hizmet](https://kubernetes.io/docs/concepts/services-networking/service/)kavramını sunmuştur. Kubernetes hizmeti, bir dizi mantıksal grubu tanımlayan ve bu FID 'ler için dış trafik pozlaması, yük dengeleme ve hizmet bulmayı sağlayan bir Özet katmandır. 

Mikro hizmetlerimizi API Management aracılığıyla API 'Ler olarak yayımlamaya hazır olduğumuzda, Kubernetes 'deki hizmetlerimizi API Management API 'Lerine nasıl eşleneceğini düşünmemiz gerekir. Hiçbir küme kuralı yok. Bu, iş yeteneklerini veya etki alanlarınızı, en başındaki mikro hizmetlere nasıl tasarlamış ve bölümlendiğinize bağlıdır. Örneğin, bir hizmetin arkasındaki yük, belirli bir kaynaktaki (örn. müşteri) tüm işlemlerden sorumludur, hizmet tek bir API ile eşleştirilebilir. Bir kaynaktaki işlemler birden fazla mikro hizmete (ör. GetOrder, PlaceOrder) bölünmemişse, API Management 'ta tek bir API 'de birden çok hizmet mantıksal olarak toplanabilir (bkz. fig. 1). 

Eşlemeler de gelişebilirler. API Management mikro hizmetlerin önünde bir façlade oluşturduğundan, mikro hizmetlerimizi zaman içinde yeniden düzenlemenize ve doğru boyutlandırmamıza izin verir. 

![Hizmetleri API 'lere eşleme](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>AKS 'in önüne API Management dağıtma

AKS kümesinin önünde API Management dağıtmanın birkaç seçeneği vardır. 

AKS kümesi her zaman bir sanal ağ (VNet) içinde dağıtıldığında, VNet 'te dağıtılması için bir API Management örneği gerekli değildir. API Management küme VNet içinde olmadığında AKS kümesi, API Management bağlanmak için ortak uç noktaları yayımlamaktır. Bu durumda, API Management ile AKS arasındaki bağlantıyı güvenli hale getirmeniz gerekir. Diğer bir deyişle, kümeye yalnızca API Management aracılığıyla erişilebildiğinden emin olunması gerekir. Şimdi seçenekleri geçelim. 

### <a name="option-1-expose-services-publicly"></a>Seçenek 1: Hizmetleri herkese açık kullanıma sunma

AKS kümesindeki hizmetler, NodePort, LoadBalancer veya ExternalName [hizmet türleri](../aks/concepts-network.md) kullanılarak herkese açık bir şekilde sunulabilir. Bu durumda, hizmetlere doğrudan genel İnternet üzerinden erişilebilir. Kümenin önüne API Management dağıttıktan sonra, mikro hizmetlerde kimlik doğrulaması uygulayarak tüm gelen trafiğin API Management gitmesinden emin olunması gerekir. Örneğin, API Management kümeye yapılan her istekte bir erişim belirteci içerebilir. Her mikro hizmet, isteği işlemeden önce belirteci doğrulamaktan sorumludur. 


Bu, özellikle mikro hizmetlerinize kimlik doğrulama mantığınızı uyguladıysanız, AKS 'in önüne API Management dağıtmak için en kolay seçenektir. 

![Hizmetleri doğrudan yayımlayın](./media/api-management-aks/direct.png)

Ları
* API Management tarafında kolay yapılandırma, küme VNet 'e eklenmesi gerekmiyor
* Hizmetler zaten açık ve mikro hizmetlerde kimlik doğrulama mantığı zaten varsa AKS tarafında değişiklik yapılmaz

Larını
* Hizmet uç noktalarının genel görünürlüğü nedeniyle olası güvenlik riski
* Gelen küme trafiği için tek giriş noktası yok
* Yinelenen kimlik doğrulama mantığı ile mikro hizmetleri karmaşıklaştırın

### <a name="option-2-install-an-ingress-controller"></a>Seçenek 2: giriş denetleyicisini yüklerken

1. seçenek daha kolay olabilir, ancak yukarıda bahsedilen gibi önemli dezavantajları vardır. API Management bir örnek küme VNet 'inde yer alıyorsa, karşılıklı TLS kimlik doğrulaması (mTLS), trafiğin güvenli ve güvenilir bir şekilde ve bir AKS kümesi API Management arasında her iki yönde de güvenilir olmasını sağlamaya yönelik sağlam bir yoldur. 

Karşılıklı TLS kimlik doğrulaması, API Management tarafından [yerel olarak desteklenir](./api-management-howto-mutual-certificates.md) ve bir giriş denetleyicisi (Fig. 3) [yüklenerek](../aks/ingress-own-tls.md) Kubernetes 'te etkinleştirilebilir. Sonuç olarak, kimlik doğrulaması, mikro hizmetleri kolaylaştıran giriş denetleyicisinde gerçekleştirilir. Ayrıca, kümeye yalnızca API Management erişiminin olduğundan emin olmak için, API Management IP adreslerini giriş olarak izin verilenler listesine ekleyebilirsiniz.  

 
![Giriş denetleyicisi aracılığıyla yayımlama](./media/api-management-aks/ingress-controller.png)


Ları
* API Management tarafında kolay yapılandırma, küme VNet 'e eklenmesi gerekmiyor ve mTLS yerel olarak destekleniyor
* Giriş denetleyicisi katmanında gelen küme trafiği için korumayı merkezileştirme
* Genel olarak görünen küme uç noktalarını en aza indirerek güvenlik riskini azaltır

Larını
* Giriş denetleyicisini yüklemek, yapılandırmak ve sürdürmek ve mTLS için kullanılan sertifikaları yönetmek için ek çalışma nedeniyle küme yapılandırmasının karmaşıklığını artırır
* Giriş denetleyicisi uç noktalarının genel görünürlüğü nedeniyle güvenlik riski


API 'Leri API Management aracılığıyla yayımladığınızda, abonelik anahtarları kullanılarak bu API 'lere güvenli bir şekilde erişmek çok kolay ve yaygındır. Yayımlanmış API 'leri kullanması gereken geliştiriciler, bu API 'lere çağrılar yaptıklarında HTTP isteklerinde geçerli bir abonelik anahtarı içermelidir. Aksi takdirde, çağrılar API Management ağ geçidi tarafından hemen reddedilir. Bunlar arka uç hizmetlerine iletilmez.

API 'Lere erişim için bir abonelik anahtarı almak üzere bir abonelik gerekir. Abonelik temel olarak bir dizi abonelik anahtarı için adlandırılmış bir kapsayıcıdır. Yayımlanmış API 'Leri kullanması gereken geliştiriciler abonelikleri alabilir. Ve API yayımcılarından onay gerekmez. API yayımcıları, doğrudan API tüketicileri için abonelikler de oluşturabilir.

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>Seçenek 3: küme VNet içinde APıM dağıtma

Bazı durumlarda, mevzuata kısıtlamalar veya katı güvenlik gereksinimleri olan müşteriler, genel kullanıma sunulan uç noktalar nedeniyle 1. ve 2. seçenek önemli çözümler bulabilir. Diğer bir deyişle, AKS kümesi ve mikro hizmetleri kullanan uygulamalar aynı VNet içinde bulunabilir, bu nedenle tüm API trafiği VNet içinde kalacak şekilde kümeyi genel kullanıma sunmak için bir neden yoktur. Bu senaryolar için API Management küme VNet 'ine dağıtabilirsiniz. [API Management geliştirici ve Premium katmanları](https://aka.ms/apimpricing) VNET dağıtımını destekler. 

[Sanal ağa API Management dağıtmanın](./api-management-using-with-vnet.md) iki modu vardır: dış ve iç. 

API tüketicileri küme VNet 'inde yer alıyorsa, dış mod (Fig. 4) kullanılmalıdır. Bu modda API Management ağ geçidi, küme VNet 'e eklenir, ancak dış yük dengeleyici aracılığıyla genel İnternet 'ten erişilebilir. Hala dış istemcilerin mikro hizmetleri kullanmasına izin verirken kümeyi tamamen gizlemeye yardımcı olur. Ayrıca, ağ trafiğini kısıtlamak için ağ güvenlik grupları (NSG) gibi Azure ağ yeteneklerini de kullanabilirsiniz.

![Dış VNet modu](./media/api-management-aks/vnet-external.png)

Tüm API tüketicileri küme VNet 'te bulunuyorsa, Iç mod (Fig. 5) kullanılabilir. Bu modda API Management ağ geçidi, küme VNET 'e eklenir ve yalnızca iç yük dengeleyici aracılığıyla bu VNet içinden erişilebilir. API Management ağ geçidine veya AKS kümesine genel İnternet 'ten ulaşmanız mümkün değildir. 

![İç VNet modu](./media/api-management-aks/vnet-internal.png)

 Her iki durumda da AKS kümesi herkese açık bir şekilde görünür değildir. Seçenek 2 ' ye kıyasla giriş denetleyicisi gerekli olmayabilir. Senaryonuza ve yapılandırmanıza bağlı olarak, API Management ve mikro hizmetleriniz arasında kimlik doğrulaması gerekebilir. Örneğin, bir hizmet ağı benimseme ise, her zaman karşılıklı TLS kimlik doğrulaması gerektirir. 

Ları
* AKS kümesinde ortak uç nokta olmadığından en güvenli seçenek
* Ortak uç noktası olmadığından küme yapılandırmasını basitleştirir
* Iç modu kullanarak VNet içinde hem API Management hem de AKS 'leri gizleyebilme
* Ağ güvenlik grupları (NSG) gibi Azure ağ yeteneklerini kullanarak ağ trafiğini denetleme özelliği

Larını
* API Management dağıtımı ve sanal ağ içinde çalışmak için yapılandırma karmaşıklığını artırır

## <a name="next-steps"></a>Sonraki adımlar

* [AKS 'teki uygulamalar Için ağ kavramları](../aks/concepts-network.md) hakkında daha fazla bilgi edinin
* [Sanal ağlarla API Management kullanma](./api-management-using-with-vnet.md) hakkında daha fazla bilgi edinin

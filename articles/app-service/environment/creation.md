---
title: App Service Ortamı oluşturma
description: App Service Ortamı oluşturma hakkında bilgi edinin.
author: ccompy
ms.assetid: 7690d846-8da3-4692-8647-0bf5adfd862a
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 021c85037ea105ff7d8ff642e3a9f28ed3ebe991
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594103"
---
# <a name="create-an-app-service-environment"></a>App Service Ortamı oluşturma

> [!NOTE]
> Bu makale, App Service Ortamı v3 (Önizleme) ile ilgilidir
> 

[App Service ortamı (ASE)][Intro] , Azure sanal ağınıza (VNet) eklenen App Service tek bir kiracı dağıtımsıdır.  ASEv3 yalnızca sanal ağınızdaki özel bir adreste bulunan uygulamaların sunulmasını destekler. Önizleme sırasında bir ASEv3 oluşturulduğunda, bu kaynaklar aboneliğinize eklenir.

- App Service Ortamı
- Özel uç nokta

Ao 'un dağıtımı iki alt ağın kullanılmasını gerektirir.  Bir alt ağ, Özel uç noktasını tutacaktır.  Bu alt ağ, VM 'Ler gibi diğer şeyler için kullanılabilir.  Diğer alt ağ, Ao 'dan yapılan giden çağrılar için kullanılır.  Bu alt ağ asa dışında başka bir şey için kullanılamaz. 

## <a name="before-you-create-your-ase"></a>ATıCı 'nizi oluşturmadan önce

ALSIZ oluşturulduktan sonra değiştiremezsiniz:

- Konum
- Abonelik
- Kaynak grubu
- Kullanılan Azure sanal ağ (VNet)
- Kullanılan alt ağlar
- Alt ağ boyutu
- ATıCı 'nizin adı

Giden alt ağın, Ao 'nizi ölçekleyecek en büyük boyutu tutabilecek kadar büyük olması gerekir. Oluşturma işleminden sonra değiştirilemediğinden, en büyük ölçek gereksinimlerinizi desteklemek için yeterince büyük bir alt ağ seçin. Önerilen boyut, 256 adres içeren bir/24 ' dir.

Ao oluşturulduktan sonra, bu uygulamaya uygulamalar ekleyebilirsiniz. ASEv3 içinde App Service planı olmadığında, ASE 'de bir I1v2 App Service planının örneğine sahip olsanız da ücretlendirilir.  

ASEv3 yalnızca select bölgelerinde sunulur. Önizleme, GA 'ye doğru bir şekilde ilerledikçe daha fazla bölge eklenecektir. 

## <a name="creating-an-ase-in-the-portal"></a>Portalda ATıCı oluşturma

1. Bir ASEv3 oluşturmak için Market 'te **App Service ortamı (Önizleme)** aratın.  
2. Temel bilgiler: aboneliği seçin, kaynak grubunu seçin veya oluşturun ve ATıCı 'nizin adını girin.  Ao adı ayrıca Ao 'nun etki alanı soneki için de kullanılacaktır.  Ao adınız *contoso* ise, etki alanı son eki *contoso.appserviceenvironment.net* olacaktır.  Bu ad, ASE 'nin dağıtıldığı VNET tarafından kullanılan Azure DNS özel bölgesinde otomatik olarak ayarlanır. 

    ![Temel App Service Ortamı Oluştur sekmesi](./media/creation/creation-basics.png)

3. Barındırma: işletim sistemi tercihi, konak grubu dağıtımı seçin. IŞLETIM sistemi tercihi, bu Ao 'daki uygulamalarınız için başlangıçta kullanacağınız işletim sistemini belirtir. Ao oluşturma işleminden sonra diğer işletim sistemi uygulamaları ekleyebilirsiniz. Ana bilgisayar grubu dağıtımı, adanmış donanım seçmek için kullanılır. ASEv3 ile, etkin ' i ve sonra adanmış donanımda Land ' ı seçin. ASE oluşturma ile tüm adanmış ana bilgisayar için ücretlendirilirsiniz ve sonra App Service plan örneklerinizin daha düşük bir fiyatı vardır. 

    ![Barındırma seçimlerini App Service Ortamı](./media/creation/creation-hosting.png)

4. Ağ iletişimi: Sanal ağınızı seçin veya oluşturun, gelen alt ağınızı seçin veya oluşturun, giden alt ağınızı seçin veya oluşturun. Giden için kullanılan herhangi bir alt ağ boş olmalı ve Microsoft. Web/hostingEnvironments için Temsilcili olmalıdır. Alt ağlarınızı Portal üzerinden oluşturursanız, alt ağ sizin için otomatik olarak Temsilcili olur.

    ![App Service Ortamı ağ seçimleri](./media/creation/creation-networking.png)

5. Gözden geçirin ve oluşturun: yapılandırmanızın doğru olup olmadığını denetleyin ve Oluştur ' u seçin. AŞIRIN oluşturulması yaklaşık bir saat sürer. 

    ![İnceleme ve oluşturma App Service Ortamı](./media/creation/creation-review.png)

AıN oluşturma işlemi tamamlandıktan sonra, uygulamalarınızı oluştururken bir konum olarak seçebilirsiniz. Yeni Ao uygulamanızda uygulama oluşturma hakkında daha fazla bilgi edinmek için [App Service ortamı kullanarak][UsingASE] okuyun

## <a name="os-preference"></a>İşletim sistemi tercihi
Ao 'da Windows uygulamaları, Linux uygulamaları veya her ikisi de olabilir. ASEv2 ' de, oluşturma sırasında seçilen ilk tercih, ASE oluşturma sırasında o işletim sistemine yönelik yüksek kullanılabilirlik altyapısını ekler. Diğer işletim sisteminin uygulamalarını eklemek için uygulamaları her zamanki gibi yapmanız yeterlidir ve istediğiniz işletim sistemini seçin. ASEv3 içinde bu, arka uç davranışını etkilemez appreciatively.  

## <a name="dedicated-hosts"></a>Ayrılmış konaklar
Ao, normalde çok kiracılı bir Hiper yöneticide sağlanan VM 'lerde dağıtılır. Donanım dahil adanmış sistemlerde dağıtmanız gerekiyorsa, ATıCı 'nizi adanmış konaklara sağlayabilirsiniz. İlk ASEv3 önizlemede, adanmış konaklar bir çift gelir. Bölgenin izin vermesi durumunda, her adanmış konak ayrı bir kullanılabilirlik bölgesidir. Adanmış ana bilgisayar tabanlı Ace dağıtımları normalden farklı şekilde fiyatlandırılır. Adanmış ana bilgisayar için bir ücret ve sonra her bir App Service plan örneği için başka ücret ücreti vardır.  

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/alerts/alerts-log.md

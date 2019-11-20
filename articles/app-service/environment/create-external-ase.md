---
title: Dış App Service ortamı oluşturma-Azure
description: Uygulama oluştururken veya tek başına bir App Service ortamı oluşturmayı açıklar
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 19d58ed90de4bdbd3cd7606d15c115bb1633770a
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70069700"
---
# <a name="create-an-external-app-service-environment"></a>Dış App Service ortamı oluşturma

Azure App Service Ortamı, Azure App Service’in Azure sanal ağı (VNet) içindeki bir alt ağa dağıtımıdır.

> [!NOTE]
> Her App Service Ortamı App Service Ortamı iletişim kurmak için kullanılabilen bir sanal IP (VIP) vardır.

Bir App Service Ortamı (ASE) iki şekilde dağıtılabilir:

- Genellikle Dış ASE olarak adlandırılan durumda, bir dış IP adresi üzerindeki VIP ile.
- İç uç nokta bir Iç Load Balancer (ıLB) olduğundan, iç IP adresine sahip VIP ile genellikle ıLB Ao adı verilir.

Bu makalede, dış Ao 'nın nasıl oluşturulacağı gösterilmektedir. Ao 'ya genel bakış için [App Service ortamı giriş][Intro]bölümüne bakın. ILB ATıCı oluşturma hakkında daha fazla bilgi için bkz. [ILB Ai oluşturma ve kullanma][MakeILBASE].

## <a name="before-you-create-your-ase"></a>ATıCı 'nizi oluşturmadan önce

ATıCı 'nizi oluşturduktan sonra, aşağıdakileri değiştiremezsiniz:

- Location
- Subscription
- Resource group
- Kullanılan VNet
- Kullanılan alt ağ
- Alt ağ boyutu

> [!NOTE]
> VNet seçtiğinizde ve bir alt ağ belirttiğinizde, gelecekteki büyüme ve ölçekleme ihtiyaçlarına uyum sağlayacak kadar büyük olduğundan emin olun. 256 adres `/24` içeren bir boyut önerilir.
>

## <a name="three-ways-to-create-an-ase"></a>ATıCı oluşturmanın üç yolu

ATıCı oluşturmanın üç yolu vardır:

- **App Service planı oluşturma sırasında**. Bu yöntem, Ave App Service planını tek bir adımda oluşturur.
- **Tek başına bir eylem olarak**. Bu yöntem, içinde hiçbir şey olmayan tek başına bir ADE oluşturur. Bu yöntem, ATıCı oluşturmak için daha gelişmiş bir işlemdir. ILB ile bir AO oluşturmak için bunu kullanırsınız.
- **Azure Resource Manager şablonundan**. Bu yöntem gelişmiş kullanıcılar içindir. Daha fazla bilgi için, bkz. [bir şablondan atıcı oluşturma][MakeASEfromTemplate].

Dış ASE 'nin genel bir VIP 'si vardır ve bu, ASE 'deki uygulamalara yönelik tüm HTTP/HTTPS trafiğinin internet erişimli bir IP adresi ile aynı olduğunu gösterir. ILB ile bir Ao, Ao tarafından kullanılan alt ağdan bir IP adresine sahiptir. ILB asa 'da barındırılan uygulamalar doğrudan internet 'e gösterilmez.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Bir ACE ve bir App Service planı oluşturma

App Service planı, uygulamaların bir kapsayıcısıdır. App Service bir uygulama oluşturduğunuzda, bir App Service planı seçersiniz veya oluşturursunuz. App Service ortamlar, App Service planlarını ve App Service planlar uygulamaları tutar.

Bir App Service planı oluştururken Ao oluşturmak için:

1.  >  [Azure Portal](https://portal.azure.com/),**Web uygulaması** **Web ve mobil** >  **kaynak oluştur**' u seçin.

    ![Web uygulaması oluşturma][1]

2. Aboneliğinizi seçin. Uygulama ve Ao, aynı aboneliklerde oluşturulur.

3. Kaynak grubunu seçin veya oluşturun. Kaynak grupları ile ilgili Azure kaynaklarını birim olarak yönetebilirsiniz. Kaynak grupları, uygulamalarınız için rol tabanlı Access Control kuralları oluştururken de yararlı olur. Daha fazla bilgi için bkz. [Azure Resource Manager’a genel bakış][ARMOverview].

4. İşletim sistemini (Windows, Linux veya Docker) seçin. 

5. App Service planını seçin ve ardından **Yeni oluştur**' u seçin. Linux Web Apps ve Windows Web Apps aynı App Service planında olamaz, ancak aynı App Service Ortamı olabilir. 

    ![Yeni App Service planı][2]

6. **Konum** açılır listesinde, atıcı 'yi oluşturmak istediğiniz bölgeyi seçin. Var olan bir AO dili seçerseniz, yeni bir ATıCı oluşturulmaz. App Service planı, seçtiğiniz as 'de oluşturulur. 

7. **Fiyatlandırma katmanını**seçin ve **yalıtılmış** fiyatlandırma SKU 'larından birini seçin. **Yalıtılmış** bir SKU kartı ve Ao olmayan bir konum seçerseniz, bu konumda yeni bir as oluşturulur. Bir as oluşturmak için işlemi başlatmak üzere **Seç**' i seçin. **Yalıtılmış** SKU yalnızca bir atıcı ile birlikte kullanılabilir. Ayrıca, **yalıtılmış**dışında başka bir fiyatlandırma SKU 'su de kullanamazsınız. 

    ![Fiyatlandırma Katmanı seçimi][3]

8. ATıCı 'niz için ad girin. Bu ad, uygulamalarınızın adreslenebilir adında kullanılır. Ao 'nun adı _appsvcenvdemo_ise, etki alanı adı *. appsvcenvdemo.p.azurewebsites.net*olur. *Mytestapp*adlı bir uygulama oluşturursanız, mytestapp.appsvcenvdemo.p.azurewebsites.net adresinde adreslenebilir. Adda boşluk kullanamazsınız. Büyük harfli karakterler kullanırsanız, etki alanı adı bu adın toplam küçük harfli sürümüdür.

    ![Yeni App Service plan adı][4]

9. Azure sanal ağ ayrıntılarınızı belirtin. **Yeni oluştur** ' u seçin veya mevcut ' ı **seçin**. Mevcut bir VNet seçme seçeneği yalnızca seçili bölgede bir sanal ağ varsa kullanılabilir. **Yeni oluştur**' u seçerseniz VNET için bir ad girin. Bu ada sahip yeni bir Kaynak Yöneticisi VNet oluşturulur. Seçili bölgedeki adres alanını `192.168.250.0/23` kullanır. **Varolanı Seç**' i seçerseniz şunları yapmanız gerekir:

    a. Birden fazla tane varsa VNet adres bloğunu seçin.

    b. Yeni bir alt ağ adı girin.

    c. Alt ağın boyutunu seçin. *Ao 'nizin gelecekteki büyümesini karşılayacak büyüklükte bir boyut seçip seçmeyi unutmayın.* 128 adresine `/24`sahip ve en yüksek boyutlu bir abi 'yi işleyebilen için önerilir. Örneğin, yalnızca `/28`16 adres kullanılabildiği için bu önerilmez. Altyapı en az yedi adres kullanır ve Azure ağı başka bir 5 kullanır. Bir `/28` alt ağda, bir dış ay için App Service plan örnekleri ve bir ILB ay için yalnızca 3 App Service plan örneği olan 4 en yüksek ölçeklendirmeyle birlikte kalır.

    d. Alt ağ IP aralığını seçin.

10. ATıCı oluşturmak için **Oluştur** ' u seçin. Bu işlem, App Service planı ve uygulamayı da oluşturur. Ao, App Service planı ve uygulama aynı abonelikte ve ayrıca aynı kaynak grubunda bulunur. ATıCı 'niz ayrı bir kaynak grubuna ihtiyaç duyuyorsa veya bir ıLB Ao 'ya ihtiyacınız varsa, kendi başına bir AO oluşturmak için adımları izleyin.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Özel bir Docker görüntüsünü birlikte kullanarak asa ve Linux Web uygulaması oluşturma

1. [Azure Portal](https://portal.azure.com/) **bir kaynak** > **kapsayıcılar için web app**Webvemobil > oluşturun. 

    ![Web uygulaması oluşturma][7]

1. Aboneliğinizi seçin. Uygulama ve Ao, aynı aboneliklerde oluşturulur.

1. Kaynak grubunu seçin veya oluşturun. Kaynak grupları ile ilgili Azure kaynaklarını birim olarak yönetebilirsiniz. Kaynak grupları, uygulamalarınız için rol tabanlı Access Control kuralları oluştururken de yararlı olur. Daha fazla bilgi için bkz. [Azure Resource Manager’a genel bakış][ARMOverview].

1. App Service planını seçin ve ardından **Yeni oluştur**' u seçin. Linux Web Apps ve Windows Web Apps aynı App Service planında olamaz, ancak aynı App Service Ortamı olabilir. 

    ![Yeni App Service planı][8]

1. **Konum** açılır listesinde, atıcı 'yi oluşturmak istediğiniz bölgeyi seçin. Var olan bir AO dili seçerseniz, yeni bir ATıCı oluşturulmaz. App Service planı, seçtiğiniz as 'de oluşturulur. 

1. **Fiyatlandırma katmanını**seçin ve **yalıtılmış** fiyatlandırma SKU 'larından birini seçin. **Yalıtılmış** bir SKU kartı ve Ao olmayan bir konum seçerseniz, bu konumda yeni bir as oluşturulur. Bir as oluşturmak için işlemi başlatmak üzere **Seç**' i seçin. **Yalıtılmış** SKU yalnızca bir atıcı ile birlikte kullanılabilir. Ayrıca, **yalıtılmış**dışında başka bir fiyatlandırma SKU 'su de kullanamazsınız. 

    ![Fiyatlandırma Katmanı seçimi][3]

1. ATıCı 'niz için ad girin. Bu ad, uygulamalarınızın adreslenebilir adında kullanılır. Ao 'nun adı _appsvcenvdemo_ise, etki alanı adı *. appsvcenvdemo.p.azurewebsites.net*olur. *Mytestapp*adlı bir uygulama oluşturursanız, mytestapp.appsvcenvdemo.p.azurewebsites.net adresinde adreslenebilir. Adda boşluk kullanamazsınız. Büyük harfli karakterler kullanırsanız, etki alanı adı bu adın toplam küçük harfli sürümüdür.

    ![Yeni App Service plan adı][4]

1. Azure sanal ağ ayrıntılarınızı belirtin. **Yeni oluştur** ' u seçin veya mevcut ' ı **seçin**. Mevcut bir VNet seçme seçeneği yalnızca seçili bölgede bir sanal ağ varsa kullanılabilir. **Yeni oluştur**' u seçerseniz VNET için bir ad girin. Bu ada sahip yeni bir Kaynak Yöneticisi VNet oluşturulur. Seçili bölgedeki adres alanını `192.168.250.0/23` kullanır. **Varolanı Seç**' i seçerseniz şunları yapmanız gerekir:

    a. Birden fazla tane varsa VNet adres bloğunu seçin.

    b. Yeni bir alt ağ adı girin.

    c. Alt ağın boyutunu seçin. *Ao 'nizin gelecekteki büyümesini karşılayacak büyüklükte bir boyut seçip seçmeyi unutmayın.* 128 adresine `/24`sahip ve en yüksek boyutlu bir abi 'yi işleyebilen için önerilir. Örneğin, yalnızca `/28`16 adres kullanılabildiği için bu önerilmez. Altyapı en az yedi adres kullanır ve Azure ağı başka bir 5 kullanır. Bir `/28` alt ağda, bir dış ay için App Service plan örnekleri ve bir ILB ay için yalnızca 3 App Service plan örneği olan 4 en yüksek ölçeklendirmeyle birlikte kalır.

    d. Alt ağ IP aralığını seçin.

1.  "Kapsayıcıyı Yapılandır" ı seçin.
    * Özel görüntü adınızı girin (Azure Container Registry, Docker Hub ve kendi özel kayıt defteriniz) kullanabilirsiniz. Kendi özel kapsayıcınızı kullanmak istemiyorsanız, yukarıdaki yönergeleri kullanarak yalnızca kodunuzu getirip Linux üzerinde App Service yerleşik bir görüntü kullanabilirsiniz. 

    ![Kapsayıcıyı yapılandırma][9]

1. ATıCı oluşturmak için **Oluştur** ' u seçin. Bu işlem, App Service planı ve uygulamayı da oluşturur. Ao, App Service planı ve uygulama aynı abonelikte ve ayrıca aynı kaynak grubunda bulunur. ATıCı 'niz ayrı bir kaynak grubuna ihtiyaç duyuyorsa veya bir ıLB Ao 'ya ihtiyacınız varsa, kendi başına bir AO oluşturmak için adımları izleyin.


## <a name="create-an-ase-by-itself"></a>Kendi başına bir ATıCı oluşturun

Tek başına bir bağımsız oluşturursanız, içinde hiç bir şey yoktur. Boş bir Ao, altyapı için hala aylık bir ücret doğurur. Bir ıLB ile ATıCı oluşturmak veya kendi kaynak grubunda Ao oluşturmak için bu adımları izleyin. Ace 'nizi oluşturduktan sonra, normal işlemi kullanarak bu uygulamalarda uygulamalar oluşturabilirsiniz. Konum olarak yeni ATıCı 'nizi seçin.

1. **App Service ortamı**için Azure Marketi 'nde arama yapın veya **kaynak** > oluştur**Web mobil** > **App Service ortamı**' i seçin. 

1. ATıCı 'nizin adını girin. Bu ad, Ao 'da oluşturulan uygulamalar için kullanılır. Ad *yenematıcı*ise, alt etki alanı adı *. mynewdemoase.p.azurewebsites.net*olur. *Mytestapp*adlı bir uygulama oluşturursanız, mytestapp.mynewdemoase.p.azurewebsites.net adresinde adreslenebilir. Adda boşluk kullanamazsınız. Büyük harfli karakterler kullanırsanız, etki alanı adı, adın toplam küçük harfli sürümüdür. ILB kullanıyorsanız, Ao adınız alt etki alanında kullanılmaz, ancak bunun yerine ama oluşturma sırasında açıkça belirtilir.

    ![ATıCı adlandırma][5]

1. Aboneliğinizi seçin. Bu abonelik Ayrıca, Ao 'daki tüm uygulamaların kullandığı bir uygulamalardır. ASE 'nizi başka bir abonelikte yer alan bir sanal ağa koyamazsınız.

1. Yeni bir kaynak grubu seçin veya belirtin. ASE 'niz için kullanılan kaynak grubu, VNet 'iniz için kullanılan bir aynı olmalıdır. Var olan bir sanal ağı seçerseniz, ASE 'niz için kaynak grubu seçimi sanal ağınızın içeriğini yansıtacak şekilde güncelleştirilir. *Bir Kaynak Yöneticisi şablonu kullanıyorsanız, VNet kaynak grubundan farklı bir kaynak grubuyla bir ASE oluşturabilirsiniz.* Bir şablondan Ao oluşturmak için, bkz. [bir şablondan App Service ortam oluşturma][MakeASEfromTemplate].

    ![Kaynak grubu seçimi][6]

1. VNet ve konumunuzu seçin. Yeni bir VNet oluşturabilir veya var olan bir sanal ağı seçebilirsiniz: 

    * Yeni bir VNet seçerseniz, bir ad ve konum belirtebilirsiniz. 
    
    * Yeni VNet 'in adres aralığı 192.168.250.0/23 ve varsayılan olarak adlandırılan bir alt ağ vardır. Alt ağ 192.168.250.0/24 olarak tanımlanır. Yalnızca bir Kaynak Yöneticisi VNet seçebilirsiniz. **VIP türü** SEÇIMI, Ao 'larınızın internet 'Ten (harici) doğrudan erişilebildiğini veya ILB kullanıp kullanmadığını belirler. Bu seçenekler hakkında daha fazla bilgi edinmek için bkz. [App Service ortamı ile iç yük dengeleyici oluşturma ve kullanma][MakeILBASE]. 

      * **VIP türü**için **dış** ' i SEÇERSENIZ, IP tabanlı SSL amaçları için SISTEMIN kaç tane dış IP adresi oluşturduğunu seçebilirsiniz. 
    
      * **VIP türü**için **dahili** ' ı seçerseniz, atıcı 'nizin kullandığı etki alanını belirtmeniz gerekir. ASE 'yi ortak veya özel adres aralıklarını kullanan bir sanal ağa dağıtabilirsiniz. Bir VNet 'i ortak adres aralığıyla birlikte kullanmak için, VNet 'i daha önce oluşturmanız gerekir. 
    
    * Mevcut bir sanal ağı seçerseniz, ASE oluşturulduğunda yeni bir alt ağ oluşturulur. *Portalda önceden oluşturulmuş bir alt ağ kullanamazsınız. Kaynak Yöneticisi şablonu kullanıyorsanız, mevcut bir alt ağla bir AO oluşturabilirsiniz.* Şablondan bir AO oluşturmak için, bkz. [bir şablondan App Service ortamı oluşturma][MakeASEfromTemplate].

## <a name="app-service-environment-v1"></a>App Service Ortamı v1

App Service Ortamı (ASEv1) öğesinin ilk sürümünün örneklerini oluşturmaya devam edebilirsiniz. Bu işlemi başlatmak için Market 'Te **App Service ortamı v1**'yi arayın. ATıCı 'yi tek başına Ao 'yu oluşturduğunuz şekilde oluşturursunuz. İşiniz bittiğinde, ASEv1 için iki ön uç ve iki çalışan vardır. ASEv1 ile ön uçları ve çalışanları yönetmeniz gerekir. App Service planlarınızı oluşturduğunuzda otomatik olarak eklenmez. Ön uçlar, HTTP/HTTPS uç noktaları olarak davranır ve çalışanlara trafik gönderir. Çalışanlar, uygulamalarınızı barındıran rollerdir. ATıCı 'nizi oluşturduktan sonra ön uçların ve çalışanların miktarını ayarlayabilirsiniz. 

ASEv1 hakkında daha fazla bilgi edinmek için bkz. [App Service ortamı v1 'ye giriş][ASEv1Intro]. ASEv1 ölçeklendirme, yönetme ve izleme hakkında daha fazla bilgi için bkz. [nasıl yapılandırılır App Service ortamı][ConfigureASEv1].

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png
[7]: ./media/how_to_create_an_external_app_service_environment/createexternalase-createwafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreatewafc.png
[9]: ./media/how_to_create_an_external_app_service_environment/createexternalase-configurecontainer.png



<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md

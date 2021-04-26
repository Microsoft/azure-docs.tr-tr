---
title: Hizmet uç noktaları ile tümleştirme Application Gateway-Azure App Service | Microsoft Docs
description: Application Gateway hizmet uç noktalarıyla güvenli hale getirilmiş Azure App Service ile nasıl tümleştirildiğini açıklar.
services: app-service
documentationcenter: ''
author: madsd
manager: ccompy
editor: ''
ms.assetid: 073eb49c-efa1-4760-9f0c-1fecd5c251cc
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: f1d517ba37bbef95d1863485c8c3b6313f196c11
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374922"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>Hizmet uç noktaları ile Application Gateway tümleştirme
Azure Application Gateway tümleştirmesi için biraz farklı yapılandırma gerektiren App Service üç çeşidi vardır. Çeşitlemeler, çok kiracılı, Iç Load Balancer (ıLB) App Service Ortamı (Ao) ve dış Ao olarak da bilinen normal App Service içerir. Bu makalede, bunu App Service (çok kiracılı) ile yapılandırma ve ıLB ve dış Ao hakkındaki konuları tartışacak.

## <a name="integration-with-app-service-multi-tenant"></a>App Service ile tümleştirme (çok kiracılı)
App Service (çok kiracılı), herkese açık bir internet uç noktası içerir. [Hizmet uç noktalarını](../../virtual-network/virtual-network-service-endpoints-overview.md) kullanarak yalnızca bir Azure sanal ağı içindeki belirli bir alt ağdan gelen trafiğe izin verebilir ve diğer her şeyi engelleyebilirsiniz. Aşağıdaki senaryoda, bir App Service örneğinin yalnızca belirli bir Application Gateway örneğinden gelen trafiği alabilmesi için bu işlevi kullanacağız.

:::image type="content" source="./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png" alt-text="Diyagramda, bir Azure sanal ağındaki Application Gateway Internet akışını ve App Service içindeki uygulama örneklerine bir güvenlik duvarı simgesiyle bu şekilde akan.":::

App Service ve Application Gateway oluşturmanın yanı sıra bu yapılandırmanın iki bölümü vardır. İlk bölüm, Application Gateway dağıtıldığı sanal ağın alt ağında hizmet uç noktalarını etkinleştirir. Hizmet uç noktaları, tüm ağ trafiğinin App Service doğru kalmasını sağlamak için belirli alt ağ KIMLIĞIYLE etiketlenecek. İkinci bölüm, yalnızca bu belirli alt ağ KIMLIĞIYLE etiketlenmiş trafiğe izin verildiğinden emin olmak için belirli bir Web uygulamasına erişim kısıtlaması ayarlansağlamaktır. Tercihe bağlı olarak farklı araçlar kullanarak yapılandırabilirsiniz.

## <a name="using-azure-portal"></a>Azure portalını kullanma
Azure portal, kurulumu sağlamak ve yapılandırmak için dört adımı takip edersiniz. Mevcut kaynaklarınız varsa, ilk adımları atlayabilirsiniz.
1. App Service belgelerindeki hızlı başlangıçlardan birini kullanarak App Service oluşturun, örneğin [.NET Core hızlı](../quickstart-dotnetcore.md) başlangıcı
2. [Portal Hızlı](../../application-gateway/quick-create-portal.md)başlangıcını kullanarak bir Application Gateway oluşturun, ancak arka uç hedefleri ekle bölümünü atlayın.
3. [App Service Application Gateway arka ucu olarak](../../application-gateway/configure-web-app-portal.md)yapılandırın, ancak erişimi kısıtla bölümünü atlayın.
4. Son olarak, [hizmet uç noktalarını kullanarak erişim kısıtlaması](../../app-service/app-service-ip-restrictions.md#set-a-service-endpoint-based-rule)oluşturun.

Artık Application Gateway üzerinden App Service erişebilirsiniz ancak doğrudan App Service erişmeyi denerseniz, Web sitesinin durdurulduğunu belirten 403 HTTP hatası almalısınız.

![Ekran görüntüsünde bir hata 403-Yasak metni gösterilir.](./media/app-gateway-with-service-endpoints/website-403-forbidden.png)

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanma
[Kaynak Yöneticisi dağıtım şablonu][template-app-gateway-app-service-complete] , bir bütün senaryo sağlayacaktır. Senaryo, hizmet uç noktaları ve erişim kısıtlaması ile kilitlenmiş bir App Service örneğinden oluşur ve yalnızca Application Gateway trafiği alırlar. Şablon, çok sayıda akıllı varsayılan ve kaynak adlarına eklenen benzersiz postdüzeltmelerin yanı sıra basit hale gelir. Bunları geçersiz kılmak için depoyu kopyalamanız veya şablonu indirmeniz ve düzenlemeniz gerekir. 

Şablonu uygulamak için, şablonun açıklamasında bulunan Azure 'a dağıt düğmesini kullanabilir veya uygun PowerShell/CLı kullanabilirsiniz.

## <a name="using-azure-command-line-interface"></a>Azure komut satırı arabirimini kullanma
[Azure CLI örneği](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md) , yalnızca Application Gateway trafiği almak için hizmet uç noktaları ve erişim kısıtlaması ile kilitlenmiş bir App Service sağlar. Yalnızca mevcut bir Application Gateway mevcut bir App Service trafiği yalıtmak istiyorsanız aşağıdaki komut yeterlidir.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

Varsayılan yapılandırmada, komut alt ağda hizmet uç noktası yapılandırmasının kurulumunu ve App Service erişim kısıtlamasını güvence altına alacak.

## <a name="considerations-for-ilb-ase"></a>ILB Ao 'nun konuları
ILB Ao, internet 'e açık değildir ve örnek ile bir Application Gateway arasındaki trafik, bu nedenle zaten sanal ağa yalıtılmıştır. Aşağıdaki [nasıl yapılır Kılavuzu](../environment/integrate-with-application-gateway.md) bir ıLB Ai 'yi yapılandırır ve Azure Portal kullanarak bir Application Gateway ile tümleştirir. 

Yalnızca Application Gateway alt ağından gelen trafiğin Ao 'ya ulaşmasını sağlamak istiyorsanız, Ao 'daki tüm Web uygulamalarını etkileyen bir ağ güvenlik grubu (NSG) yapılandırabilirsiniz. NSG için, alt ağ IP aralığını ve isteğe bağlı olarak bağlantı noktalarını (80/443) belirtebilirsiniz. ATıCı 'in düzgün çalışması için [gerekli NSG kurallarını](../environment/network-info.md#network-security-groups) geçersiz kılmayın.

Tek bir Web uygulamasına trafiği yalıtmak için, hizmet uç noktaları Ao 'da çalışmamaları nedeniyle IP tabanlı erişim kısıtlamalarını kullanmanız gerekir. IP adresi Application Gateway örneğinin özel IP 'si olmalıdır.

## <a name="considerations-for-external-ase"></a>Dış Ao ile ilgili konular
Dış ate, çok kiracılı App Service gibi herkese açık bir yük dengeleyiciye sahiptir. Hizmet uç noktaları as için çalışmaz ve Application Gateway örneğinin genel IP 'sini kullanarak IP tabanlı erişim kısıtlamalarını kullanmanız gerekir. Azure portal kullanarak bir dış AI oluşturmak için bu [hızlı](../environment/create-external-ase.md) başlangıcı izleyebilirsiniz

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "Tüm senaryo için şablon Azure Resource Manager"

## <a name="considerations-for-kuduscm-site"></a>Kudu/SCM sitesi konuları
Kudu olarak da bilinen SCM sitesi, her Web uygulaması için mevcut olan bir yönetim sitesidir. Ağ proxy 'si SCM sitesini tersine çevirmek mümkün değildir ve büyük olasılıkla tek tek IP adreslerine veya belirli bir alt ağa de kilitlemek isteyebilirsiniz.

Ana siteyle aynı erişim kısıtlamalarını kullanmak istiyorsanız, aşağıdaki komutu kullanarak ayarları devralmayı sağlayabilirsiniz.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

SCM sitesi için ayrı erişim kısıtlamaları ayarlamak istiyorsanız, aşağıda gösterildiği gibi--SCM-site bayrağını kullanarak erişim kısıtlamalarını ekleyebilirsiniz.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>Sonraki adımlar
App Service Ortamı hakkında daha fazla bilgi için bkz. [App Service ortamı belgeleri](/azure/app-service/environment).

Web uygulamanızı daha güvenli hale getirmek için [Azure Web uygulaması güvenlik duvarı belgelerinde](../../web-application-firewall/ag/ag-overview.md)Application Gateway Web uygulaması güvenlik duvarı ile ilgili bilgiler bulabilirsiniz.

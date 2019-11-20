---
title: Azure Işlevlerinde IP adresleri
description: İşlev uygulamaları için gelen ve giden IP adreslerini bulmayı ve bunların değişmesine neden olduğunu öğrenin.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: glenga
ms.openlocfilehash: d8b6a342dd32d430f7a40a1e0a0a17a482a0816d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469059"
---
# <a name="ip-addresses-in-azure-functions"></a>Azure Işlevlerinde IP adresleri

Bu makalede, işlev uygulamalarının IP adresleriyle ilgili aşağıdaki konular açıklanmaktadır:

* Bir işlev uygulaması tarafından kullanılmakta olan IP adreslerini bulma.
* İşlev uygulamasının IP adreslerinin değiştirilmesine neden olan durum.
* Bir işlev uygulamasına erişebilen IP adreslerini kısıtlama.
* İşlev uygulaması için ayrılmış IP adresleri alma.

IP adresleri, bağımsız işlevlerle değil işlev uygulamalarıyla ilişkilendirilir. Gelen HTTP istekleri bağımsız işlevleri çağırmak için gelen IP adresini kullanamaz; Varsayılan etki alanı adını (functionappname.azurewebsites.net) veya özel bir etki alanı adını kullanmaları gerekir.

## <a name="function-app-inbound-ip-address"></a>İşlev uygulaması gelen IP adresi

Her işlev uygulamasının tek bir gelen IP adresi vardır. Bu IP adresini bulmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. İşlev uygulamasına gidin.
3. **Platform özellikleri**' ni seçin.
4. **Özellikler**' i seçin ve **sanal IP adresı**altında gelen IP adresi görüntülenir.

## <a name="find-outbound-ip-addresses"></a>İşlev uygulaması giden IP adresleri

Her işlev uygulamasının kullanılabilir bir giden IP adresleri kümesi vardır. Bir arka uç veritabanı gibi bir işlevden gelen giden bağlantılar, kaynak IP adresi olarak kullanılabilir giden IP adreslerinden birini kullanır. Belirli bir bağlantının kullanacağı IP adresini önceden kullanacağınızı bilemezsiniz. Bu nedenle, arka uç hizmetinizin güvenlik duvarını tüm işlev uygulamasının giden IP adreslerine açması gerekir.

Bir işlev uygulaması için kullanılabilir giden IP adreslerini bulmak için:

1. [Azure Kaynak Gezgini](https://resources.azure.com)oturum açın.
2. **Microsoft. Web > siteleri > > {aboneliğiniz} > sağlayıcıları**' nı seçin.
3. JSON panelinde, işlev uygulamanızın adında sonlanan bir `id` özelliğine sahip siteyi bulun.
4. Bkz. `outboundIpAddresses` ve `possibleOutboundIpAddresses`. 

`outboundIpAddresses` kümesi, işlev uygulaması tarafından şu anda kullanılabilir. `possibleOutboundIpAddresses` kümesi, yalnızca işlev uygulaması [diğer fiyatlandırma katmanlarına ölçeklenirken](#outbound-ip-address-changes)KULLANILABILIR olacak IP adreslerini içerir.

Kullanılabilir giden IP adreslerini bulmanın alternatif bir yolu [Cloud Shell](../cloud-shell/quickstart.md)kullanmaktır:

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```
> [!NOTE]
> [Tüketim planında](functions-scale.md#consumption-plan) çalışan bir işlev uygulaması ölçeklendirilirse, yeni BIR giden IP adresi aralığı atanabilir. Tüketim planında çalışırken, tüm veri merkezini beyaz listeye almanız gerekebilir.

## <a name="data-center-outbound-ip-addresses"></a>Veri merkezi giden IP adresleri

İşlev uygulamalarınız tarafından kullanılan giden IP adreslerini beyaz listeye almanız gerekiyorsa, uygulamanın veri merkezi (Azure bölgesi) işlevini beyaz bir şekilde listeleyerek başka bir seçenek vardır. [Tüm Azure veri MERKEZLERININ IP adreslerini listeleyen BIR JSON dosyası indirebilirsiniz](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Ardından, işlev uygulamanızın çalıştırıldığı bölge için geçerli olan JSON parçasını bulun.

Örneğin, Batı Avrupa JSON parçası şöyle görünebilir:

```
{
  "name": "AzureCloud.westeurope",
  "id": "AzureCloud.westeurope",
  "properties": {
    "changeNumber": 9,
    "region": "westeurope",
    "platform": "Azure",
    "systemService": "",
    "addressPrefixes": [
      "13.69.0.0/17",
      "13.73.128.0/18",
      ... Some IP addresses not shown here
     "213.199.180.192/27",
     "213.199.183.0/24"
    ]
  }
}
```

 Bu dosyanın ne zaman güncelleştirildiği ve IP adreslerinin ne zaman değiştirileceği hakkında bilgi için [Indirme Merkezi sayfasının](https://www.microsoft.com/en-us/download/details.aspx?id=56519) **Ayrıntılar** bölümünü genişletin.

## <a name="inbound-ip-address-changes"></a>Gelen IP adresi değişiklikleri

Şu durumlarda gelen IP **adresi değişebilir** :

- Bir işlev uygulamasını silip farklı bir kaynak grubunda yeniden oluşturun.
- Bir kaynak grubu ve bölge birleşiminde son işlev uygulamasını silin ve yeniden oluşturun.
- [Sertifika yenileme](../app-service/configure-ssl-certificate.md#renew-certificate)sırasında olduğu gıbı bir SSL bağlamasını silin.

İşlev uygulamanız bir [Tüketim planında](functions-scale.md#consumption-plan)çalıştığında, listede listelenenler gibi herhangi bir eylem gerçekleştirmemeniz durumunda gelen IP adresi de değişebilir.

## <a name="outbound-ip-address-changes"></a>Giden IP adresi değişiklikleri

Bir işlev uygulaması için kullanılabilir giden IP adresleri kümesi şunları yaptığınızda değişebilir:

* Gelen IP adresini değiştirecek herhangi bir işlem yapın.
* App Service planı fiyatlandırma katmanınızı değiştirin. Uygulamanızın kullanabileceği tüm olası giden IP adreslerinin listesi, tüm fiyatlandırma katmanları için `possibleOutboundIPAddresses` özellikte. Bkz. [giden IP 'Leri bulma](#find-outbound-ip-addresses).

İşlev uygulamanız bir [Tüketim planında](functions-scale.md#consumption-plan)çalıştığında, listede listelenenler gibi herhangi bir eylem gerçekleştirmediyse giden IP adresi de değişebilir.

Giden IP adresi değişikliğini kasıtlı olarak zorlamak için:

1. Standart ve Premium v2 fiyatlandırma katmanları arasında App Service planınızı yukarı veya aşağı ölçeklendirin.
2. 10 dakika bekleyin.
3. Başlattığınız yere doğru ölçeklendirin.

## <a name="ip-address-restrictions"></a>IP adresi kısıtlamaları

Bir işlev uygulamasına izin vermek veya erişimi reddetmek istediğiniz IP adreslerinin bir listesini yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Azure App Service STATIK IP kısıtlamaları](../app-service/app-service-ip-restrictions.md).

## <a name="dedicated-ip-addresses"></a>Ayrılmış IP adresleri

Statik, ayrılmış IP adreslerine ihtiyacınız varsa [App Service ortamlar](../app-service/environment/intro.md) (App Service planlarının [yalıtılmış katmanı](https://azure.microsoft.com/pricing/details/app-service/) ) önerilir. Daha fazla bilgi için bkz. [App SERVICE ORTAMı IP adresleri](../app-service/environment/network-info.md#ase-ip-addresses) ve [gelen trafiği bir App Service ortamı denetleme](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).

İşlev uygulamanızın bir App Service Ortamı çalışıp çalışmamasından daha fazla bilgi edinmek için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. İşlev uygulamasına gidin.
3. **Genel bakış** sekmesini seçin.
4. App Service planı katmanı **App Service plan/fiyatlandırma katmanı**altında görünür. App Service Ortamı fiyatlandırma katmanı **yalıtılmıştır**.
 
Alternatif olarak, [Cloud Shell](../cloud-shell/quickstart.md)kullanabilirsiniz:

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

App Service Ortamı `sku` `Isolated`.

## <a name="next-steps"></a>Sonraki adımlar

IP değişikliklerinin yaygın bir nedeni, işlev uygulama ölçeği değişikdir. [İşlev uygulaması Ölçeklendirmesi hakkında daha fazla bilgi edinin](functions-scale.md).

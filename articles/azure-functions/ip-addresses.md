---
title: Azure Işlevlerinde IP adresleri
description: İşlev uygulamaları için gelen ve giden IP adreslerini bulmayı ve bunların değişmesine neden olduğunu öğrenin.
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: 2c248756899459e17082bcab863a4e857b594909
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104608240"
---
# <a name="ip-addresses-in-azure-functions"></a>Azure Işlevlerinde IP adresleri

Bu makalede, işlev uygulamalarının IP adresleriyle ilgili aşağıdaki kavramlar açıklanmaktadır:

* Bir işlev uygulaması tarafından kullanılmakta olan IP adreslerini bulma.
* İşlev uygulaması IP adreslerinin değiştirilmesine neden olan koşullar.
* Bir işlev uygulamasına erişebilen IP adreslerini kısıtlama.
* İşlev uygulaması için adanmış IP adresleri tanımlama.

IP adresleri, bağımsız işlevlerle değil işlev uygulamalarıyla ilişkilendirilir. Gelen HTTP istekleri bağımsız işlevleri çağırmak için gelen IP adresini kullanamaz; Varsayılan etki alanı adını (functionappname.azurewebsites.net) veya özel bir etki alanı adını kullanmaları gerekir.

## <a name="function-app-inbound-ip-address"></a>İşlev uygulaması gelen IP adresi

Her işlev uygulamasının tek bir gelen IP adresi vardır. Bu IP adresini bulmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. İşlev uygulamasına gidin.
3. **Ayarlar** bölümünde **Özellikler**’i seçin. Gelen IP adresi, **sanal IP adresi** altında görünür.

## <a name="function-app-outbound-ip-addresses"></a><a name="find-outbound-ip-addresses"></a>İşlev uygulaması giden IP adresleri

Her işlev uygulamasının kullanılabilir bir giden IP adresleri kümesi vardır. Bir arka uç veritabanı gibi bir işlevden gelen giden bağlantılar, kaynak IP adresi olarak kullanılabilir giden IP adreslerinden birini kullanır. Belirli bir bağlantının kullanacağı IP adresini önceden kullanacağınızı bilemezsiniz. Bu nedenle, arka uç hizmetinizin güvenlik duvarını tüm işlev uygulamasının giden IP adreslerine açması gerekir.

Bir işlev uygulaması için kullanılabilir giden IP adreslerini bulmak için:

1. [Azure Kaynak Gezgini](https://resources.azure.com)oturum açın.
2. **Microsoft. Web > siteleri > > {aboneliğiniz} > sağlayıcıları**' nı seçin.
3. JSON panelinde, `id` işlev uygulamanızın adında sonlanan bir özelliği olan siteyi bulun.
4. Bkz `outboundIpAddresses` `possibleOutboundIpAddresses` . ve. 

Kümesi `outboundIpAddresses` Şu anda işlev uygulaması için kullanılabilir. Kümesi `possibleOutboundIpAddresses` yalnızca işlev uygulaması [diğer fiyatlandırma katmanlarına ölçeklenirken](#outbound-ip-address-changes)kullanılabilir olacak IP adreslerini içerir.

Kullanılabilir giden IP adreslerini bulmanın alternatif bir yolu [Cloud Shell](../cloud-shell/quickstart.md)kullanmaktır:

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

> [!NOTE]
> [Tüketim planı](consumption-plan.md) veya [Premium planı](functions-premium-plan.md) üzerinde çalışan bir işlev uygulaması ölçeklendirilirse, yeni bir giden IP adresi aralığı atanabilir. Bu planlardan birinde çalışırken, tüm veri merkezini izin verilenler listesine eklemeniz gerekebilir.

## <a name="data-center-outbound-ip-addresses"></a>Veri merkezi giden IP adresleri

İşlev uygulamalarınız tarafından kullanılan giden IP adreslerini bir allowlist öğesine eklemeniz gerekiyorsa, ' veri merkezi (Azure bölgesi) işlevini bir allowlist öğesine eklemek başka bir seçenektir. [Tüm Azure veri MERKEZLERININ IP adreslerini listeleyen BIR JSON dosyası indirebilirsiniz](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Ardından, işlev uygulamanızın çalıştırıldığı bölge için geçerli olan JSON parçasını bulun.

Örneğin, aşağıdaki JSON parçası Batı Avrupa için izin şöyle görünebilir:

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

## <a name="inbound-ip-address-changes"></a><a name="inbound-ip-address-changes"></a>Gelen IP adresi değişiklikleri

Şu durumlarda gelen IP **adresi değişebilir** :

- Bir işlev uygulamasını silip farklı bir kaynak grubunda yeniden oluşturun.
- Bir kaynak grubu ve bölge birleşiminde son işlev uygulamasını silin ve yeniden oluşturun.
- [Sertifika yenileme](../app-service/configure-ssl-certificate.md#renew-certificate)sırasında olduğu gıbı bir TLS bağlamasını silin.

İşlev uygulamanız bir [Tüketim planında](consumption-plan.md) veya [Premium bir planda](functions-premium-plan.md)çalıştığında, [Yukarıda listelenenler](#inbound-ip-address-changes)gibi herhangi bir eylem gerçekleştirmemiş olsanız bile gelen IP adresi de değişebilir.

## <a name="outbound-ip-address-changes"></a>Giden IP adresi değişiklikleri

Bir işlev uygulaması için kullanılabilir giden IP adresleri kümesi şunları yaptığınızda değişebilir:

* Gelen IP adresini değiştirecek herhangi bir işlem yapın.
* App Service planı fiyatlandırma katmanınızı değiştirin. Uygulamanızın kullanabileceği tüm olası giden IP adreslerinin listesi, tüm fiyatlandırma katmanları için özellikte yer alabilir `possibleOutboundIPAddresses` . Bkz. [giden IP 'Leri bulma](#find-outbound-ip-addresses).

İşlev uygulamanız bir [Tüketim planında](consumption-plan.md) veya [Premium bir planda](functions-premium-plan.md)çalıştığında, [Yukarıda listelenenler](#inbound-ip-address-changes)gibi herhangi bir eylem gerçekleştirmemiş olsanız bile giden IP adresi de değişebilir.

Giden bir IP adresi değişikliğini kasıtlı olarak zorlamak için aşağıdaki yordamı kullanın:

1. Standart ve Premium v2 fiyatlandırma katmanları arasında App Service planınızı yukarı veya aşağı ölçeklendirin.

2. 10 dakika bekleyin.

3. Başlattığınız yere doğru ölçeklendirin.

## <a name="ip-address-restrictions"></a>IP adresi kısıtlamaları

Bir işlev uygulamasına izin vermek veya erişimi reddetmek istediğiniz IP adreslerinin bir listesini yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Azure App Service STATIK IP kısıtlamaları](../app-service/app-service-ip-restrictions.md).

## <a name="dedicated-ip-addresses"></a>Ayrılmış IP adresleri

İşlev uygulamanız statik, ayrılmış IP adresleri gerektirdiğinde keşfetmeye yönelik çeşitli stratejiler vardır. 

### <a name="virtual-network-nat-gateway-for-outbound-static-ip"></a>Giden statik IP için sanal ağ NAT ağ geçidi

Trafiği statik bir genel IP adresi üzerinden yönlendirmek için bir sanal ağ NAT ağ geçidi kullanarak işlevinizden giden trafiğin IP adresini kontrol edebilirsiniz. [Premium bir planda](functions-premium-plan.md)çalışırken bu topolojiyi kullanabilirsiniz. Daha fazla bilgi edinmek için bkz. [öğretici: Azure işlevleri gıden IP 'Yi Azure sanal ağ NAT ağ geçidiyle denetleme](functions-how-to-use-nat-gateway.md).

### <a name="app-service-environments"></a>App Service ortamları

Hem gelen hem de giden IP adresleri üzerinde tam denetim için [App Service ortamlar](../app-service/environment/intro.md) (App Service planlarının [yalıtılmış katmanı](https://azure.microsoft.com/pricing/details/app-service/) ) önerilir. Daha fazla bilgi için bkz. [App SERVICE ORTAMı IP adresleri](../app-service/environment/network-info.md#ase-ip-addresses) ve [gelen trafiği bir App Service ortamı denetleme](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).

İşlev uygulamanızın bir App Service Ortamı çalışıp çalışmamasından daha fazla bilgi edinmek için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. İşlev uygulamasına gidin.
3. **Genel Bakış** sekmesini seçin.
4. App Service planı katmanı **App Service plan/fiyatlandırma katmanı** altında görünür. App Service Ortamı fiyatlandırma katmanı **yalıtılmıştır**.
 
Alternatif olarak, [Cloud Shell](../cloud-shell/quickstart.md)kullanabilirsiniz:

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

App Service Ortamı `sku` `Isolated` .

## <a name="next-steps"></a>Sonraki adımlar

IP değişikliklerinin yaygın bir nedeni, işlev uygulama ölçeği değişikdir. [İşlev uygulaması Ölçeklendirmesi hakkında daha fazla bilgi edinin](functions-scale.md).

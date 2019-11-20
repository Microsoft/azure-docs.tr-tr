---
title: Gelen/giden IP adresleri-Azure App Service | Microsoft Docs
description: Gelen ve giden IP adreslerinin App Service ' de nasıl kullanıldığını ve bu bilgilerin uygulamanız için nasıl bulunacağını açıklar.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 26033192ee841262345b0fc7f6aec76872379070
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470167"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Azure App Service gelen ve giden IP adresleri

[Azure App Service](overview.md) , [App Service ortamları](environment/intro.md)dışında çok kiracılı bir hizmettir. App Service ortamda olmayan uygulamalar ( [yalıtılmış katmanda](https://azure.microsoft.com/pricing/details/app-service/)değil) ağ altyapısını diğer uygulamalarla paylaşır. Sonuç olarak, bir uygulamanın gelen ve giden IP adresleri farklı olabilir ve hatta belirli durumlarda bile değişebilir. 

[App Service ortamları](environment/intro.md) adanmış ağ altyapıları kullanır, bu nedenle bir App Service ortamında çalışan uygulamalar, hem gelen hem de giden bağlantılar için statik, ayrılmış IP adresleri alır.

## <a name="when-inbound-ip-changes"></a>Gelen IP değiştiğinde

Ölçeği genişletilmiş örneklerin sayısından bağımsız olarak, her uygulamanın tek bir gelen IP adresi vardır. Gelen IP adresi aşağıdaki eylemlerden birini gerçekleştirdiğinizde değişebilir:

- Bir uygulamayı silip farklı bir kaynak grubunda yeniden oluşturun.
- Kaynak grubu _ve_ bölge kombinasyondaki son uygulamayı silip yeniden oluşturun.
- Sertifika yenileme sırasında olduğu gibi var olan bir SSL bağlamasını silin (bkz. [Sertifikayı Yenile](configure-ssl-certificate.md#renew-certificate)).

## <a name="find-the-inbound-ip"></a>Gelen IP 'yi bulma

Yalnızca bir yerel terminalde aşağıdaki komutu çalıştırın:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Statik gelen IP al

Bazen, uygulamanız için adanmış ve statik bir IP adresi isteyebilirsiniz. Statik bir gelen IP adresi almak için, [IP tabanlı BIR SSL bağlaması](configure-ssl-bindings.md#secure-a-custom-domain)yapılandırmanız gerekir. Uygulamanızın güvenliğini sağlamak için gerçekten SSL işlevselliğine ihtiyacınız yoksa, bu bağlama için otomatik olarak imzalanan bir sertifika da yükleyebilirsiniz. IP tabanlı bir SSL bağlamasında, sertifika IP adresinin kendisine bağlıdır, bu nedenle App Service olması için statik bir IP adresi sağlar. 

## <a name="when-outbound-ips-change"></a>Giden IP 'Ler değiştiğinde

Ölçeği genişletilmiş örneklerin sayısından bağımsız olarak, her uygulamanın belirli bir zamanda bir dizi giden IP adresi vardır. Bir arka uç veritabanı gibi App Service uygulamadan giden bağlantı, kaynak IP adresi olarak giden IP adreslerinden birini kullanır. Belirli bir uygulama örneğinin giden bağlantıyı yapmak için kullanacağı IP adresini önceden kullanacağınızı, bu nedenle arka uç hizmetinizin güvenlik duvarını uygulamanızın tüm giden IP adreslerine açması gerekir.

Uygulamanızı daha düşük Katmanlar (**temel**, **Standart**ve **Premium**) ve **Premium v2** KATMANı arasında ölçeklendirirseniz, uygulamanız için giden IP adresleri kümesi değişir.

Fiyatlandırma katmanlarından bağımsız olarak uygulamanızın kullanabileceği tüm olası giden IP adresleri kümesini, `possibleOutboundIpAddresses` özelliğini arayarak veya Azure portal **Özellikler** dikey penceresinde **ek giden IP adresleri** alanına bakarak bulabilirsiniz. Bkz. [giden IP 'Leri bulma](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Giden IP 'Leri bulma

Uygulamanız tarafından Azure portal Şu anda kullandığınız giden IP adreslerini bulmak için uygulamanızın sol tarafında bulunan **Özellikler** ' e tıklayın. Bunlar **gıden IP adresleri** alanında listelenir.

[Cloud Shell](../cloud-shell/quickstart.md)aşağıdaki komutu çalıştırarak aynı bilgileri bulabilirsiniz.

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Fiyatlandırma katmanlarından bağımsız olarak uygulamanıza yönelik _Tüm_ OLASı giden IP adreslerini bulmak için uygulamanızın sol tarafında bulunan **Özellikler** ' e tıklayın. Bunlar, **ek gıden IP adresleri** alanında listelenir.

[Cloud Shell](../cloud-shell/quickstart.md)aşağıdaki komutu çalıştırarak aynı bilgileri bulabilirsiniz.

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Sonraki adımlar

Kaynak IP adresleriyle gelen trafiği kısıtlamayı öğrenin.

> [!div class="nextstepaction"]
> [Statik IP kısıtlamaları](app-service-ip-restrictions.md)

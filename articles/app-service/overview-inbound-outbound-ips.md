---
title: Gelen/giden IP adresleri
description: Gelen ve giden IP adreslerinin Azure App Service, ne zaman değişdikleri ve uygulamanızın adreslerini nasıl bulacağınızı öğrenin.
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 1dda487d23c9f955aea8e35d16e5a560a890a173
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834489"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Azure App Service gelen ve giden IP adresleri

[Azure App Service](overview.md) , [App Service ortamları](environment/intro.md)dışında çok kiracılı bir hizmettir. App Service ortamda olmayan uygulamalar ( [yalıtılmış katmanda](https://azure.microsoft.com/pricing/details/app-service/)değil) ağ altyapısını diğer uygulamalarla paylaşır. Sonuç olarak, bir uygulamanın gelen ve giden IP adresleri farklı olabilir ve hatta belirli durumlarda bile değişebilir.

[App Service ortamları](environment/intro.md) adanmış ağ altyapıları kullanır, bu nedenle bir App Service ortamında çalışan uygulamalar, hem gelen hem de giden bağlantılar için statik, ayrılmış IP adresleri alır.

## <a name="how-ip-addresses-work-in-app-service"></a>IP adreslerinin App Service nasıl çalıştığı

Bir App Service uygulama bir App Service planında çalışır ve App Service planları Azure altyapısındaki dağıtım birimlerinden birine dağıtılır (dahili olarak bir web alanı olarak adlandırılır). Her dağıtım birimine bir genel gelen IP adresi ve bir [gıden IP adresleri](#find-outbound-ips)kümesi içeren bir dızı sanal IP adresi atanır. Aynı dağıtım birimi içindeki tüm App Service planlar ve bunlar içinde çalışan uygulama örnekleri aynı sanal IP adresleri kümesini paylaşır. Bir App Service Ortamı ( [yalıtılmış katmanda](https://azure.microsoft.com/pricing/details/app-service/)bir App Service planı) için, App Service planı dağıtım biriminin kendisidir, bu nedenle sanal IP adresleri buna sonuç olarak ayrılmıştır.

Dağıtım birimleri arasında bir App Service planını taşımaya izin verilmediğinden, uygulamanıza atanan sanal IP adresleri genellikle aynı kalır, ancak özel durumlar vardır.

## <a name="when-inbound-ip-changes"></a>Gelen IP değiştiğinde

Ölçeği genişletilmiş örneklerin sayısından bağımsız olarak, her uygulamanın tek bir gelen IP adresi vardır. Gelen IP adresi aşağıdaki eylemlerden birini gerçekleştirdiğinizde değişebilir:

- Bir uygulamayı silip farklı bir kaynak grubunda yeniden oluşturun (dağıtım birimi değişebilir).
- Kaynak grubu _ve_ bölge kombinasyondaki son uygulamayı silin ve yeniden oluşturun (dağıtım birimi değişebilir).
- Sertifika yenileme sırasında gibi var olan bir IP tabanlı TLS/SSL bağlamasını silin (bkz. [Sertifikayı Yenile](configure-ssl-certificate.md#renew-certificate)).

## <a name="find-the-inbound-ip"></a>Gelen IP 'yi bulma

Yalnızca bir yerel terminalde aşağıdaki komutu çalıştırın:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Statik gelen IP al

Bazen, uygulamanız için adanmış ve statik bir IP adresi isteyebilirsiniz. Statik bir gelen IP adresi almak için [özel bir etki alanını güvenli hale](configure-ssl-bindings.md#secure-a-custom-domain)getirmeniz gerekir. Uygulamanızın güvenliğini sağlamak için gerçekten TLS işlevselliğine ihtiyacınız yoksa, bu bağlama için otomatik olarak imzalanan bir sertifika da yükleyebilirsiniz. IP tabanlı bir TLS bağlamasında, sertifika IP adresinin kendisine bağlıdır, bu nedenle App Service olması için statik bir IP adresi sağlar. 

## <a name="when-outbound-ips-change"></a>Giden IP 'Ler değiştiğinde

Ölçeği genişletilmiş örneklerin sayısından bağımsız olarak, her uygulamanın belirli bir zamanda bir dizi giden IP adresi vardır. Bir arka uç veritabanı gibi App Service uygulamadan giden bağlantı, kaynak IP adresi olarak giden IP adreslerinden birini kullanır. Kullanılacak IP adresi, çalışma zamanında rastgele seçilir, bu nedenle arka uç hizmetiniz uygulamanızın tüm giden IP adreslerine güvenlik duvarını açması gerekir.

Aşağıdaki eylemlerden birini gerçekleştirdiğinizde uygulamanız için giden IP adresleri kümesi değişir:

- Bir uygulamayı silip farklı bir kaynak grubunda yeniden oluşturun (dağıtım birimi değişebilir).
- Kaynak grubu _ve_ bölge kombinasyondaki son uygulamayı silin ve yeniden oluşturun (dağıtım birimi değişebilir).
- Uygulamanızı daha düşük Katmanlar (**temel**, **Standart** ve **Premium**) ve **Premium v2** katmanı (IP adresleri kümeden eklenebilir veya kümeden çıkarılan) arasında ölçeklendirin.

Fiyatlandırma katmanlarından bağımsız olarak, uygulamanızın kullanabileceği tüm olası giden IP adresleri kümesini, `possibleOutboundIpAddresses` özelliği arayarak veya Azure Portal **Özellikler** dikey PENCERESINDE **ek giden IP adresleri** alanında bulabilirsiniz. Bkz. [giden IP 'Leri bulma](#find-outbound-ips).

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

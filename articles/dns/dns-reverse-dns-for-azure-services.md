---
title: Azure hizmetleri için ters DNS | Microsoft Docs
description: Azure 'da barındırılan hizmetler için ters DNS aramalarını yapılandırmayı öğrenin
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: victorh
ms.openlocfilehash: c33914fb404467a20a9799df9643e9702234c300
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "71224497"
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Azure 'da barındırılan hizmetler için ters DNS yapılandırma

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu makalede, Azure 'da barındırılan hizmetler için ters DNS aramalarının nasıl yapılandırılacağı açıklanmaktadır.

Azure 'daki hizmetler, Azure tarafından atanan ve Microsoft 'un sahip olduğu IP adreslerini kullanır. Bu ters DNS kayıtları (PTR kayıtları), Microsoft 'a ait ters DNS arama bölgelerinde oluşturulmalıdır. Bu makalede bunun nasıl yapılacağı açıklanır.

Bu senaryo, [Azure DNS ' deki atanan IP aralıkları için ters DNS arama bölgelerini barındırabilme](dns-reverse-dns-hosting.md)özelliği ile karıştırılmamalıdır. Bu durumda, geriye doğru arama bölgesi tarafından temsil edilen IP aralıklarının kuruluşunuza genellikle ISS 'niz tarafından atanması gerekir.

Bu makaleyi okumadan önce, [Azure 'da ters DNS ve desteğe genel bakış](dns-reverse-dns-overview.md)hakkında bilgi sahibi olmanız gerekir.

Azure DNS, işlem kaynakları (sanal makineler, sanal makine ölçek kümeleri veya Service Fabric kümeler gibi) bir Publicıpaddress kaynağı aracılığıyla sunulur. Ters DNS aramaları, Publicıpaddress 'in ' Smarfqdn ' özelliği kullanılarak yapılandırılır.


Azure App Service için ters DNS şu anda desteklenmiyor.

## <a name="validation-of-reverse-dns-records"></a>Ters DNS kayıtlarının doğrulanması

Üçüncü taraf, DNS etki alanlarınıza yönelik Azure hizmeti eşlemesi için ters DNS kayıtları oluşturmamalıdır. Bunu engellemek için, Azure yalnızca ters DNS kaydında belirtilen etki alanı adının aynı Azure aboneliğindeki bir Publicıpaddress veya Cloud Service DNS adı veya IP adresi ile aynı olduğu veya olarak çözümlenme gibi bir ters DNS kaydı oluşturulmasına izin verir.

Bu doğrulama yalnızca ters DNS kaydı ayarlandığında veya değiştirildiğinde gerçekleştirilir. Düzenli yeniden doğrulama yapılmaz.

Örneğin: Publicıpaddress kaynağının DNS adı contosoapp1.northus.cloudapp.azure.com ve IP Address 23.96.52.53 olduğunu varsayalım. Publicıpaddress için Smarfqdn şu şekilde belirtilebilir:
* Publicıpaddress, contosoapp1.northus.cloudapp.azure.com için DNS adı
* Aynı abonelikte bulunan contosoapp2.westus.cloudapp.azure.com gibi farklı bir Publicıpaddress için DNS adı
* App1.contoso.com gibi bir yol DNS adı, bu ad *ilk önce* ContosoApp1.northus.cloudapp.Azure.com IÇIN bir CNAME veya aynı abonelikte farklı bir publicıpaddress olarak yapılandırılmıştır.
* App1.contoso.com gibi bir yol DNS adı, bu ad *ilk* olarak IP adresi 23.96.52.53 veya aynı abonelikte farklı bir PUBLICıPADDRESS 'in IP adresine bir kayıt olarak yapılandırılmıştır.

Aynı kısıtlamalar Cloud Services için ters DNS için de geçerlidir.


## <a name="reverse-dns-for-publicipaddress-resources"></a>Publicıpaddress kaynakları için ters DNS

Bu bölümde, Kaynak Yöneticisi dağıtım modelindeki Publicıpaddress kaynakları için Azure PowerShell, Azure klasik CLı veya Azure CLı kullanarak ters DNS yapılandırma hakkında ayrıntılı yönergeler sağlanmaktadır. Publicıpaddress kaynakları için ters DNS yapılandırması, şu anda Azure portal aracılığıyla desteklenmemektedir.

Azure Şu anda yalnızca IPv4 Publicıpaddress kaynakları için ters DNS 'i desteklemektedir. IPv6 için desteklenmez.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Mevcut bir Publicıpaddresses 'a ters DNS ekleme

#### <a name="powershell"></a>PowerShell

Ters DNS 'yi mevcut bir Publicıpaddress ile güncelleştirmek için:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

Zaten DNS adı olmayan mevcut bir Publicıpaddress 'e ters DNS eklemek için bir DNS adı da belirtmeniz gerekir:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Azure klasik CLI

Mevcut bir Publicıpaddress 'e ters DNS eklemek için:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Zaten DNS adı olmayan mevcut bir Publicıpaddress 'e ters DNS eklemek için bir DNS adı da belirtmeniz gerekir:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

Mevcut bir Publicıpaddress 'e ters DNS eklemek için:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Zaten DNS adı olmayan mevcut bir Publicıpaddress 'e ters DNS eklemek için bir DNS adı da belirtmeniz gerekir:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Ters DNS ile genel IP adresi oluşturma

Ters DNS özelliği zaten belirtilmiş olan yeni bir Publicıpaddress oluşturmak için:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

#### <a name="azure-classic-cli"></a>Azure klasik CLI

```azurecli
azure network public-ip create -n PublicIp -g MyResourceGroup -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip create --name PublicIp --resource-group MyResourceGroup --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Mevcut bir Publicıpaddress için ters DNS görüntüle

Mevcut bir Publicıpaddress için yapılandırılmış değeri görüntülemek için:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
```

#### <a name="azure-classic-cli"></a>Azure klasik CLI

```azurecli
azure network public-ip show -n PublicIp -g MyResourceGroup
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip show --name PublicIp --resource-group MyResourceGroup
```

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Mevcut Genel IP Adresleri ters DNS 'yi kaldır

Mevcut bir Publicıpaddress öğesinden ters bir DNS özelliği kaldırmak için:

#### <a name="powershell"></a>PowerShell

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Azure klasik CLI

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup –f ""
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn ""
```


## <a name="configure-reverse-dns-for-cloud-services"></a>Cloud Services için ters DNS yapılandırma

Bu bölümde Azure PowerShell kullanılarak klasik dağıtım modelinde Cloud Services için ters DNS yapılandırma hakkında ayrıntılı yönergeler sağlanmaktadır. Cloud Services için ters DNS yapılandırması, Azure portal, Azure klasik CLı veya Azure CLı aracılığıyla desteklenmez.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Mevcut Cloud Services ters DNS ekleyin

Var olan bir bulut hizmetine ters DNS kaydı eklemek için:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Ters DNS ile bulut hizmeti oluşturma

Ters DNS özelliği zaten belirtilmiş yeni bir bulut hizmeti oluşturmak için:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Mevcut Cloud Services için ters DNS görüntüle

Mevcut bir bulut hizmeti için ters DNS özelliğini görüntülemek için:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Mevcut Cloud Services ters DNS 'yi kaldır

Bir ters DNS özelliğini var olan bir bulut hizmetinden kaldırmak için:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>SSS

### <a name="how-much-do-reverse-dns-records-cost"></a>Geriye doğru DNS kaydı ne kadar maliyetlidir?

Bunlar ücretsizdir!  Ters DNS kayıtları veya sorguları için ek maliyet yoktur.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Ters DNS kayıtlarım Internet 'ten çözümlensin mi?

Evet. Azure hizmetiniz için ters DNS özelliğini ayarladıktan sonra Azure, tüm Internet kullanıcıları için ters DNS kaydının çözümlendiğinden emin olmak için gereken tüm DNS temsilcilerini ve DNS bölgelerini yönetir.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Azure Hizmetlerim için varsayılan ters DNS kayıtları oluşturuldu mu?

Hayır. Ters DNS, bir katılım özelliğidir. Yapılandırmayı seçerseniz, hiçbir varsayılan ters DNS kaydı oluşturulmaz.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Tam etki alanı adının (FQDN) biçimi nedir?

FQDN 'Ler ileri sırada belirtilmiştir ve bir nokta ile sonlandırılmalıdır (örneğin, "app1.contoso.com.").

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>Belirtdiğim ters DNS için doğrulama denetimi başarısız olursa ne olur?

Ters DNS doğrulama denetiminin başarısız olduğu durumlarda, ters DNS kaydını yapılandırma işlemi başarısız olur. Ters DNS değerini gereken şekilde düzeltip yeniden deneyin.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Azure App Service için ters DNS yapılandırabilir miyim?

Hayır. Azure App Service için ters DNS desteklenmez.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Azure hizmetim için birden çok ters DNS kaydı yapılandırabilir miyim?

Hayır. Azure, her Azure bulut hizmeti veya Publicıpaddress için tek bir ters DNS kaydını destekler.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>IPv6 Publicıpaddress kaynakları için ters DNS yapılandırabilir miyim?

Hayır. Azure Şu anda yalnızca IPv4 Publicıpaddress kaynakları ve Cloud Services için ters DNS 'i desteklemektedir.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Azure Işlem hizmetlerimin dış etki alanlarına e-posta gönderebilir miyim?

Doğrudan bir Azure dağıtımından e-posta göndermenin teknik özelliği abonelik türüne bağlıdır. Abonelik türünden bağımsız olarak, Microsoft, giden e-posta göndermek için güvenilen posta geçiş hizmetlerinin kullanılmasını önerir. Daha ayrıntılı bilgi için bkz. [e-posta göndermek Için gelişmiş Azure güvenliği – kasım 2017 güncelleştirmesi](https://blogs.msdn.microsoft.com/mast/2017/11/15/enhanced-azure-security-for-sending-emails-november-2017-update/).

## <a name="next-steps"></a>Sonraki adımlar

Ters DNS hakkında daha fazla bilgi için bkz. [Vikipde ters DNS araması](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
[Azure DNS 'de ISS tarafından atanan IP aralığınızı geriye doğru arama bölgesini nasıl barındıracağınızı](dns-reverse-dns-for-azure-services.md)öğrenin.


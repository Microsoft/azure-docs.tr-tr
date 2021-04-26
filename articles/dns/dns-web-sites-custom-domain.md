---
title: 'Öğretici: Bir web uygulaması için özel Azure DNS kayıtları oluşturma'
description: Bu öğreticide Azure DNS'yi kullanarak web uygulamanız için özel etki alanı DNS kayıtları oluşturacaksınız.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: rohink
ms.openlocfilehash: 369c7dab174f0269797b10635882a6821ade8311
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94952956"
---
# <a name="tutorial-create-dns-records-in-a-custom-domain-for-a-web-app"></a>Öğretici: Bir web uygulaması için özel etki alanında DNS kaydı oluşturma 

Azure DNS'yi web uygulamalarınız için özel etki alanı barındıracak şekilde yapılandırabilirsiniz. Örneğin, bir Azure Web uygulaması oluşturabilir ve kullanıcılarınızın \. bir tam etki alanı adı (FQDN) olarak www contoso.com veya contoso.com kullanarak erişmesini sağlayabilirsiniz.

> [!NOTE]
> contoso.com bu öğreticide örnek etki alanı olarak kullanılmıştır. contoso.com yerine kendi etki alanı adınızı yazın.

Bunu yapmak için üç kayıt oluşturmanız gerekir:

* contoso.com adresini gösteren bir kök "A" kaydı
* Doğrulama için bir kök "TXT" kaydı
* www adı için A kaydını gösteren bir "CNAME" kaydı

Azure'daki web uygulamanız için bir A kaydı oluşturduğunuzda web uygulamasının IP adresi her değiştiğinde bu A kaydının da değiştirilmesi gerektiğini unutmayın.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Özel etki alanınız için A ve TXT kaydı oluşturma
> * Özel etki alanınız için CNAME kaydı oluşturma
> * Yeni kayıtları test etme
> * Web uygulamanıza özel ana bilgisayar adları ekleyin
> * Özel ana bilgisayar adlarını test etme

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Azure DNS barındırabilmeniz için, test etmek için kullanılabilir bir etki alanı adına sahip olmanız gerekir. Bu etki alanı üzerinde tam denetime sahip olmanız gerekir. Tam denetim, etki alanı için ad sunucusu (NS) kayıtlarını ayarlama olanağını kapsar.
* [Bir App Service uygulaması oluşturun](../app-service/quickstart-html.md) veya başka bir öğretici için oluşturduğunuz bir uygulamayı kullanın.

* Azure DNS'de bir DNS bölgesi oluşturun ve bölgeyi kayıt kuruluşunuzda Azure DNS'ye devredin.

   1. DNS bölgesi oluşturmak için [DNS bölgesi oluşturma](./dns-getstarted-powershell.md) sayfasındaki adımları izleyin.
   2. Bölgenizi Azure DNS'ye devretmek için [DNS etki alanı devretme](dns-delegate-domain-azure-dns.md) sayfasındaki adımları izleyin.

Bölge oluşturduktan ve Azure DNS'ye devrettikten sonra özel etki alanınız için kayıt oluşturabilirsiniz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-a-record-and-txt-record"></a>A kaydı ve TXT kaydı oluşturma

A kaydı, bir adı bir IP adresiyle eşlemek için kullanılır. Aşağıdaki örnekte web uygulamanızın IPv4 adresini kullanarak "\@" ifadesini A kaydı olarak atayın. \@ genellikle kök etki alanını temsil eder.

### <a name="get-the-ipv4-address"></a>IPv4 adresini alma

Azure portaldaki Uygulama Hizmetleri sayfasının sol tarafından **Özel etki alanları**'nı seçin. 

![Özel etki alanı menüsü](../app-service/./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

**Özel etki alanları** sayfasında, uygulamanın IPv4 adresini kopyalayın:

![Azure uygulamasına portal gezintisi](../app-service/./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="create-the-a-record"></a>A kaydı oluşturma

```azurepowershell
New-AzDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" `
 -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "<your web app IP address>")
```

### <a name="create-the-txt-record"></a>TXT kaydını oluşturma

Uygulama Hizmetleri, bu kaydı yalnızca yapılandırma sırasında, özel etki alanının sahibi olduğunuzu doğrulamak için kullanır. Özel etki alanınız doğrulandıktan ve Uygulama Hizmetleri'nde yapılandırıldıktan sonra, bu TXT kaydını silebilirsiniz.

> [!NOTE]
> Üretim trafiğini Web uygulamasına yönlendirmenize rağmen etki alanı adını doğrulamak istiyorsanız, yalnızca doğrulama adımı için TXT kaydını belirtmeniz gerekir.  Doğrulama, TXT kaydına ek olarak bir veya CNAME kaydı gerektirmez.

```azurepowershell
New-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup `
 -Name "@" -RecordType "txt" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -Value  "contoso.azurewebsites.net")
```

## <a name="create-the-cname-record"></a>CNAME kaydı oluşturma

Etki alanınız Azure DNS (bkz. [DNS etki alanı devretme](dns-domain-delegation.md)) tarafından yönetiliyorsa aşağıdaki örneği kullanarak contoso.azurewebsites.net için bir CNAME kaydı oluşturabilirsiniz.

Azure PowerShell'i açın ve yeni bir CNAME kaydı oluşturun. Bu örnekte CNAME kayıt türüne, 600 saniyelik "yaşam süresi" değerine sahip, "contoso.com" adlı DNS bölgesinde yer alan ve contoso.azurewebsites.net web uygulaması takma adına sahip bir kayıt kümesi oluşturulmaktadır.

### <a name="create-the-record"></a>Kaydı oluşturma

```azurepowershell
New-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName "MyAzureResourceGroup" `
 -Name "www" -RecordType "CNAME" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -cname "contoso.azurewebsites.net")
```

Yanıt aşağıdaki örnek olur:

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

## <a name="test-the-new-records"></a>Yeni kayıtları test etme

Kayıtların doğru şekilde oluşturulup oluşturulmadığını sorgulamak için nslookup aracını kullanarak "www.contoso.com" ve "contoso.com" adreslerini sorgulayabilirsiniz:

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net

> contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    contoso.com
Address:  <ip of web app service>

> set type=txt
> contoso.com

Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
contoso.com text =

        "contoso.azurewebsites.net"
```
## <a name="add-custom-host-names"></a>Özel ana bilgisayar adı ekleme

Artık web uygulamanıza özel ana bilgisayar adları ekleyebilirsiniz:

```azurepowershell
set-AzWebApp `
 -Name contoso `
 -ResourceGroupName MyAzureResourceGroup `
 -HostNames @("contoso.com","www.contoso.com","contoso.azurewebsites.net")
```
## <a name="test-the-custom-host-names"></a>Özel ana bilgisayar adlarını test etme

Bir tarayıcı açıp `http://www.<your domainname>` ve `http://<you domain name>` adreslerine gidin.

> [!NOTE]
> Ön eki eklediğinizden emin olun `http://` , aksi takdirde tarayıcınız sizin için BIR URL öngörme girişiminde bulunabilir!

İki URL'de de aynı sayfayı görmeniz gerekir. Örnek:

![Contoso uygulama hizmeti](media/dns-web-sites-custom-domain/contoso-app-svc.png)


## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide oluşturulan kaynaklara ihtiyacınız kalmadığında **myresourcegroup** kaynak grubunu silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Azure DNS'de özel bölge oluşturmayı öğrenin.

> [!div class="nextstepaction"]
> [PowerShell ile Azure DNS özel bölgelerini kullanmaya başlama](private-dns-getstarted-powershell.md)
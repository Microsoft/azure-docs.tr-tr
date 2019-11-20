---
title: 'Hızlı başlangıç: Azure DNS bölgesi ve kaydı oluşturma-Azure PowerShell'
titleSuffix: Azure DNS
description: Azure DNS'te DNS bölgesi ve kaydı oluşturma hakkında bilgi edinin. Bu hızlı başlangıçta, Azure PowerShell kullanarak ilk DNS bölgenizi ve kaydınızı oluşturup yönetmeniz için adım adım yönergeler sağlanır.
services: dns
author: asudbring
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: allensu
ms.openlocfilehash: 0d3852824702bbc88925981c3c665b1b3fda7404
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072180"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell’i kullanarak Azure DNS bölgesi ve kaydı oluşturma

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu hızlı başlangıçta PowerShell kullanarak ilk DNS bölgenizi ve kaydını oluşturursunuz. Ayrıca, [Azure portal](dns-getstarted-portal.md) veya [Azure CLI](dns-getstarted-cli.md)'sini kullanarak aşağıdaki adımları gerçekleştirebilirsiniz. 

DNS bölgesi, belirli bir etki alanına ait DNS kayıtlarını barındırmak için kullanılır. Etki alanınızı Azure DNS'de barındırmaya başlamak için bir DNS bölgesi oluşturmanız gerekir. Ardından bu DNS bölgesinde etki alanınız için tüm DNS kayıtları oluşturulur. Son olarak, DNS bölgenizi Internet'te yayımlamak için etki alanının ad sunucularını yapılandırmanız gerekir. Bu adımların her biri aşağıda açıklanmıştır.

Azure DNS ayrıca özel etki alanları oluşturmayı da destekler. İlk özel DNS bölgenizi ve kaydınızı oluşturma hakkında adım adım yönergeler için [PowerShell ile Azure DNS özel bölgelerini kullanmaya başlama](private-dns-getstarted-powershell.md) konusunu inceleyin.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-the-resource-group"></a>Kaynak grubunu oluşturma

DNS bölgesini oluşturmadan önce, DNS bölgesini içerecek kaynak grubunu oluşturun:

```powershell
New-AzResourceGroup -name MyResourceGroup -location "eastus"
```

## <a name="create-a-dns-zone"></a>DNS bölgesi oluşturma

DNS bölgesi, `New-AzDnsZone` cmdlet’i kullanılarak oluşturulur. Aşağıdaki örnek, *Myresourcegroup*adlı kaynak grubunda *contoso. xyz* adlı bir DNS bölgesi oluşturur. Değerleri kendinizinkilerle değiştirerek DNS bölgesini oluşturmak için örneği kullanın.

```powershell
New-AzDnsZone -Name contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>DNS kaydı oluşturma

`New-AzDnsRecordSet` cmdlet’ini kullanarak kayıt kümeleri oluşturabilirsiniz. Aşağıdaki örnek, "MyResourceGroup" kaynak grubundaki "contoso. xyz" DNS bölgesinde "www" göreli adına sahip bir kayıt oluşturur. Kayıt kümesinin tam nitelikli adı "www. contoso. xyz" dir. Kayıt türü "A", IP adresi "10.10.10.10" ve TTL 3600 saniyedir.

```powershell
New-AzDnsRecordSet -Name www -RecordType A -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "10.10.10.10")
```

## <a name="view-records"></a>Kayıtları görüntüleme

Bölgenizdeki DNS kayıtlarını listelemek için şu seçenekleri kullanın:

```powershell
Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="test-the-name-resolution"></a>Ad çözümlemesini test etme

Test ' A ' kaydıyla bir test DNS bölgesine sahip olduğunuza göre, ad çözümlemesini *nslookup*adlı bir araçla test edebilirsiniz. 

**DNS ad çözümlemesini test etmek için:**

1. Bölgenizin ad sunucularının listesini almak için aşağıdaki cmdlet 'i çalıştırın:

   ```azurepowershell
   Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -RecordType ns
   ```

1. Ad sunucusu adlarından birini, önceki adımın çıktısından kopyalayın.

1. Bir komut istemi açın ve aşağıdaki komutu çalıştırın:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Örneğin:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Aşağıdaki ekrana benzer bir şey görmeniz gerekir:

   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

**\.contoso. xyz** ana bilgisayar adı, yapılandırdığınız gibi **10.10.10.10**olarak çözümlenir. Bu sonuç, ad çözümlemenin doğru çalıştığını doğrular.

## <a name="delete-all-resources"></a>Tüm kaynakları silme

Artık gerekmediğinde, kaynak grubunu silerek bu hızlı başlangıçta oluşturulan tüm kaynakları silebilirsiniz:

```powershell
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell kullanarak ilk DNS bölgenizi ve kaydı oluşturduğunuza göre artık özel etki alanında bulunan bir web uygulaması için kayıt oluşturabilirsiniz.

> [!div class="nextstepaction"]
> [Özel etki alanında web uygulaması için DNS kayıtları oluşturma](./dns-web-sites-custom-domain.md)


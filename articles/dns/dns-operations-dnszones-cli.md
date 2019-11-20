---
title: Azure DNS-Azure CLı 'da DNS bölgelerini yönetme | Microsoft Docs
description: Azure CLı kullanarak DNS bölgelerini yönetebilirsiniz. Bu makalede, Azure DNS üzerinde DNS bölgelerini güncelleştirme, silme ve oluşturma işlemlerinin nasıl yapılacağı gösterilir.
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: victorh
ms.openlocfilehash: 14d0512a10329f36872d111825261ebc5ef71976
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959375"
---
# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli"></a>Azure CLı kullanarak Azure DNS DNS Bölgeleri Yönetme

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure CLı](dns-operations-dnszones-cli.md)


Bu kılavuzda, Windows, Mac ve Linux 'ta kullanılabilen platformlar arası Azure CLı kullanarak DNS bölgelerinizi nasıl yöneteceğiniz gösterilmektedir. DNS bölgelerinizi [Azure PowerShell](dns-operations-dnszones.md) veya Azure Portal kullanarak da yönetebilirsiniz.

Bu kılavuz özellikle ortak DNS bölgeleriyle ilgilidir. Azure CLı kullanarak Azure DNS özel bölgeleri yönetme hakkında bilgi için bkz. [Azure CLI kullanarak Azure DNS özel bölgeleri kullanmaya başlama](private-dns-getstarted-cli.md).

## <a name="introduction"></a>Giriş

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-for-azure-dns"></a>Azure DNS için Azure CLı 'yı ayarlama

### <a name="before-you-begin"></a>Başlamadan önce

Yapılandırmanıza başlamadan önce aşağıdaki öğelere sahip olduğunuzu doğrulayın.

* Bir Azure aboneliği. Henüz bir Azure aboneliğiniz yoksa, [MSDN abone avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/)için kaydolabilirsiniz.

* Windows, Linux veya MAC için kullanılabilen Azure CLı 'nin en son sürümünü yükler. [Azure CLI 'Yı yüklerken](https://docs.microsoft.com/cli/azure/install-az-cli2)daha fazla bilgi bulabilirsiniz.

### <a name="sign-in-to-your-azure-account"></a>Azure hesabınızda oturum açın

Bir konsol penceresi açın ve kimlik bilgilerinizle kimlik doğrulaması yapın. Daha fazla bilgi için bkz [. Azure CLI 'Dan Azure 'Da oturum açma](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

```
az login
```

### <a name="select-the-subscription"></a>Aboneliği seçin

Hesap için abonelikleri kontrol edin.

```
az account list
```

Hangi Azure aboneliklerinizden kullanacağınızı seçin.

```azurecli
az account set --subscription "subscription name"
```

### <a name="optional-to-installuse-azure-dns-private-zones-feature"></a>İsteğe bağlı: Azure DNS Özel Bölgeleri özelliğini yüklemek/kullanmak Için
Azure DNS özel bölge özelliği, Azure CLı uzantısı aracılığıyla kullanılabilir. "DNS" Azure CLı uzantısını yükler 
```
az extension add --name dns
``` 

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure Resource Manager tüm kaynak gruplarının bir konum belirtmesini gerektirir. Bu kaynak grubundaki kaynaklar için varsayılan konum olarak kullanılır. Ancak, tüm DNS kaynakları bölgesel değil genel olduğundan, kaynak grubu konumu seçiminin Azure DNS hiçbir etkisi yoktur.

Mevcut bir kaynak grubunu kullanıyorsanız, bu adımı atlayabilirsiniz.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="getting-help"></a>Yardım alma

Azure DNS ile ilgili tüm Azure CLı komutları @no__t başlar-0. Her komut için `--help` seçeneği kullanılarak yardım sağlanır (kısa biçimi `-h`).  Örneğin:

```azurecli
az network dns --help
az network dns zone --help
az network dns zone create --help
```

## <a name="create-a-dns-zone"></a>DNS bölgesi oluşturma

@No__t-0 komutu kullanılarak bir DNS bölgesi oluşturulur. Yardım için bkz. `az network dns zone create -h`.

Aşağıdaki örnek, *Myresourcegroup*adlı kaynak grubunda *contoso.com* adlı bir DNS bölgesi oluşturur:

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>Etiketlerle bir DNS bölgesi oluşturmak için

Aşağıdaki örnek, `--tags` parametresini kullanarak, *Proje = demo* ve *env = test*olmak üzere ıkı [Azure Resource Manager etiketleriyle](dns-zones-records.md#tags)bir DNS bölgesi oluşturmayı gösterir (kısa biçimi `-t`):

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com --tags "project=demo" "env=test"
```

## <a name="get-a-dns-zone"></a>DNS bölgesi al

Bir DNS bölgesi almak için `az network dns zone show` ' ı kullanın. Yardım için bkz. `az network dns zone show --help`.

Aşağıdaki örnek, *Myresourcegroup*kaynak GRUBUNDAKI *contoso.com* DNS bölgesini ve onunla ilişkili verileri döndürür. 

```azurecli
az network dns zone show --resource-group myresourcegroup --name contoso.com
```

Aşağıdaki örnekte yanıt verilmiştir.

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-04.azure-dns.com.",
    "ns2-04.azure-dns.net.",
    "ns3-04.azure-dns.org.",
    "ns4-04.azure-dns.info."
  ],
  "numberOfRecordSets": 4,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

DNS kayıtlarının `az network dns zone show` tarafından döndürülmediğini unutmayın. DNS kayıtlarını listelemek için `az network dns record-set list` kullanın.


## <a name="list-dns-zones"></a>DNS bölgelerini listeleme

DNS bölgelerini numaralandırmak için `az network dns zone list` kullanın. Yardım için bkz. `az network dns zone list --help`.

Kaynak grubu belirtildiğinde yalnızca kaynak grubu içindeki bölgeler listelenir:

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

Kaynak grubunun atlanması, abonelikteki tüm bölgeleri listeler:

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>DNS bölgesini güncelleştirme

DNS bölgesi kaynağına yapılan değişiklikler `az network dns zone update` kullanılarak yapılabilir. Yardım için bkz. `az network dns zone update --help`.

Bu komut bölge içindeki DNS kayıt kümelerinden hiçbirini güncelleştirmez (bkz. [DNS kayıtlarını yönetme](dns-operations-recordsets-cli.md)). Yalnızca bölge kaynağının özelliklerini güncelleştirmek için kullanılır. Bu özellikler Şu anda bölge kaynağı için [Azure Resource Manager ' Tags '](dns-zones-records.md#tags) ile sınırlıdır.

Aşağıdaki örnek, bir DNS bölgesindeki etiketlerin nasıl güncelleşbir olduğunu gösterir. Varolan Etiketler, belirtilen değerle değiştirilmiştir.

```azurecli
az network dns zone update --resource-group myresourcegroup --name contoso.com --set tags.team=support
```

## <a name="delete-a-dns-zone"></a>DNS bölgesini silme

DNS bölgeleri, `az network dns zone delete` kullanılarak silinebilir. Yardım için bkz. `az network dns zone delete --help`.

> [!NOTE]
> DNS bölgesini silme, bölge içindeki tüm DNS kayıtlarını da siler. Bu işlem geri alınamaz. DNS bölgesi kullanılıyorsa bölgeyi kullanan hizmetler, bölge silindiğinde başarısız olur.
>
>Yanlışlıkla bölge silmeye karşı korumak için bkz. [DNS bölgelerini ve kayıtlarını koruma](dns-protect-zones-recordsets.md).

Bu komut onay ister. İsteğe bağlı `--yes` anahtarı bu istemi bastırır.

Aşağıdaki örnek, *contoso.com* bölgesinin *myresourcegroup*kaynak grubundan nasıl silineceğini gösterir.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.com
```

## <a name="next-steps"></a>Sonraki adımlar

DNS bölgesindeki [kayıt kümelerini ve kayıtları yönetmeyi](dns-getstarted-create-recordset-cli.md) öğrenin.

[Azure DNS için etki alanınızı nasıl atayacağınızı](dns-domain-delegation.md)öğrenin.


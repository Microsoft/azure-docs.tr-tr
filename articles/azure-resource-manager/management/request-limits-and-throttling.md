---
title: İstek sınırları ve azaltma
description: Abonelik sınırlarına ulaşıldığında Azure Resource Manager isteklerle azaltma kullanımını açıklar.
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: seodec18
ms.openlocfilehash: c1fd707bf9f13ed88bf9acb5d2ad70d56c453a90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024634"
---
# <a name="throttling-resource-manager-requests"></a>Resource Manager isteklerini azaltma

Bu makalede, Azure Resource Manager isteklerinin nasıl kısıtlanmakta olduğu açıklanır. Sınıra ulaşmadan önce kalan isteklerin sayısını ve sınıra ulaştığınızda yanıt vermeyi nasıl izleyeceğinizi gösterir.

Kısıtlama iki düzeyde gerçekleşir. Abonelik ve kiracı için istekleri kısıtlar Azure Resource Manager. İstek, abonelik ve kiracı için azaltma limitlerinin altındaysa, Kaynak Yöneticisi isteği kaynak sağlayıcısına yönlendirir. Kaynak sağlayıcı, işlemlerine uyarlanmış azaltma sınırlarını uygular. Aşağıdaki görüntüde, bir istek kullanıcıdan Azure Resource Manager ve kaynak sağlayıcısına geçtiğinde, kısıtlama nasıl uygulanacağını gösterir.

![İstek azaltma](./media/request-limits-and-throttling/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Abonelik ve kiracı sınırları

Her abonelik düzeyi ve kiracı düzeyindeki işlem, azaltma sınırlarına tabidir. Abonelik istekleri, aboneliğinizdeki kaynak gruplarını alma gibi abonelik KIMLIĞINIZI geçirmeyi içerir. Kiracı istekleri, geçerli Azure konumlarını alma gibi abonelik KIMLIĞINIZI içermez.

Saat başına varsayılan azaltma sınırları aşağıdaki tabloda gösterilmiştir.

| Kapsam | Operations | Sınır |
| ----- | ---------- | ------- |
| Abonelik | okuma | 12000 |
| Abonelik | /Delete | 15000 |
| Abonelik | blok | 1200 |
| Kiracı | okuma | 12000 |
| Kiracı | blok | 1200 |

Bu sınırların kapsamı, istekleri yapan güvenlik sorumlusu ve abonelik kimliği veya kiracı kimliğine göre belirlenir. İstekleriniz birden fazla güvenlik sorumlusundan geliyorsa, abonelik veya kiracıdaki sınırınız saatte 12.000 ve 1.200’den büyüktür.

Bu sınırlar her Azure Resource Manager örneği için geçerlidir. Her Azure bölgesinde birden çok örnek vardır ve Azure Resource Manager tüm Azure bölgelerine dağıtılır.  Bu nedenle, uygulamada sınırlar Bu limitlerden daha yüksektir. Bir kullanıcının istekleri genellikle farklı Azure Resource Manager örnekleri tarafından işlenir.

## <a name="resource-provider-limits"></a>Kaynak sağlayıcısı sınırları

Kaynak sağlayıcıları kendi azaltma sınırlarını uygular. Kaynak Yöneticisi asıl KIMLIĞE ve Kaynak Yöneticisi örneğine göre kısıtladığından, kaynak sağlayıcısı önceki bölümde varsayılan limitlerden daha fazla istek alabilir.

Bu bölümde, yaygın olarak kullanılan bazı kaynak sağlayıcılarının azaltma sınırları ele alınmaktadır.

### <a name="storage-throttling"></a>Depolama alanı azaltma

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Ağ kapasitesi azaltma

Microsoft. Network kaynak sağlayıcısı aşağıdaki kısıtlama sınırlarını uygular:

| İşlem | Sınır |
| --------- | ----- |
| yazma/silme (PUT) | 5 dakikada 1000 |
| okuma (GET) | 5 dakikada 10000 |

### <a name="compute-throttling"></a>İşlem azaltma

İşlem işlemlerine yönelik azaltma sınırları hakkında bilgi için bkz. [API azaltma hataları sorunlarını giderme-işlem](/troubleshoot/azure/virtual-machines/troubleshooting-throttling-errors).

Sanal makine ölçek kümesi içindeki sanal makine örneklerini denetlemek için, [Sanal Makine Ölçek Kümeleri işlemlerini](/rest/api/compute/virtualmachinescalesetvms)kullanın. Örneğin, sanal makine örneklerinin güç durumunu denetlemek için [sanal makine ölçek kümesi VM 'leri-](/rest/api/compute/virtualmachinescalesetvms/list) parametreleri ile Listele seçeneğini kullanın. Bu API, istek sayısını azaltır.

### <a name="azure-resource-graph-throttling"></a>Azure Kaynak Grafiği azaltma

[Azure Kaynak Grafiği](../../governance/resource-graph/overview.md) , istek sayısını işlemlerine göre sınırlandırır. Kalan istekleri ve sınıra ulaşıldığında nasıl yanıt verileceğini öğrenmek için bu makaledeki adımlar kaynak grafı için de geçerlidir. Ancak, kaynak grafiği kendi sınırını ve sıfırlama oranını ayarlar. Daha fazla bilgi için bkz. [Kaynak Grafiği azaltma üstbilgileri](../../governance/resource-graph/concepts/guidance-for-throttled-requests.md#understand-throttling-headers).

### <a name="other-resource-providers"></a>Diğer kaynak sağlayıcıları

Diğer kaynak sağlayıcılarındaki daraltma hakkında daha fazla bilgi için bkz.:

* [Azure Key Vault azaltma yönergeleri](../../key-vault/general/overview-throttling.md)
* [AKS sorunlarını giderme](../../aks/troubleshooting.md#im-receiving-429---too-many-requests-errors)

## <a name="error-code"></a>Hata kodu

Sınıra ulaştığınızda, **429 çok fazla Istek** http durum kodunu alırsınız. Yanıt, bir sonraki isteği göndermeden önce uygulamanızın beklemesi gereken saniye sayısını (veya uyku) belirten bir **yeniden deneme** değeri içerir. Yeniden deneme değeri geçmeden önce bir istek gönderirseniz isteğiniz işlenmez ve yeni bir yeniden deneme değeri döndürülür.

Belirtilen süre bekledikten sonra Azure bağlantınızı kapatıp yeniden açabilirsiniz. Bağlantıyı sıfırlayarak, farklı bir Azure Resource Manager örneğine bağlanabilirsiniz.

Azure SDK kullanıyorsanız, SDK otomatik yeniden deneme yapılandırmasına sahip olabilir. Daha fazla bilgi için bkz. [Azure hizmetleri Için yeniden deneme Kılavuzu](/azure/architecture/best-practices/retry-service-specific).

Bazı kaynak sağlayıcıları geçici bir sorunu raporlamak için 429 döndürür. Bu sorun, isteğiniz tarafından doğrudan neden olmayan bir aşırı yükleme koşulu olabilir. Ya da, hedef kaynağın veya bağımlı kaynağın durumu hakkında geçici bir hata temsil eder. Örneğin, ağ kaynak sağlayıcısı, hedef kaynak başka bir işlem tarafından kilitlendiğinde **RetryableErrorDueToAnotherOperation** hata kodu ile 429 döndürür. Hatanın azaltma veya geçici bir koşuldan mi geldiğini öğrenmek için yanıtta hata ayrıntılarını görüntüleyin.

## <a name="remaining-requests"></a>Kalan istekler

Yanıt üstbilgilerini inceleyerek kalan isteklerin sayısını belirleyebilirsiniz. Okuma istekleri, kalan okuma isteklerinin sayısı için üst bilgide bir değer döndürür. Yazma istekleri, kalan yazma isteklerinin sayısı için bir değer içerir. Aşağıdaki tabloda, bu değerler için inceleyebileceğiniz yanıt üstbilgileri açıklanmaktadır:

| Yanıt üst bilgisi | Description |
| --- | --- |
| x-MS-ratelimit-kalan-abonelik-okumalar |Abonelik kapsamlı okuma kaldı. Bu değer, okuma işlemlerinde döndürülür. |
| x-MS-ratelimit-kalan-abonelik-yazmaları |Abonelik kapsamlı yazmaları kaldı. Bu değer, yazma işlemlerinde döndürülür. |
| x-MS-ratelimit-kalan-kiracı-okuma |Kalan kiracı kapsamlı okuma sayısı |
| x-MS-ratelimit-kalan-kiracı-yazma |Kiracı kapsamlı yazmaları kaldı |
| x-MS-ratelimit-kalan-abonelik-Resource-Requests |Abonelik kapsamlı kaynak türü istekleri kaldı.<br /><br />Bu üstbilgi değeri yalnızca bir hizmet varsayılan sınırı geçersiz kılmışsa döndürülür. Kaynak Yöneticisi, abonelik okuma veya yazma işlemleri yerine bu değeri ekler. |
| x-MS-ratelimit-kalan-abonelik-Resource-Entities-oku |Abonelik kapsamlı kaynak türü toplama istekleri kaldı.<br /><br />Bu üstbilgi değeri yalnızca bir hizmet varsayılan sınırı geçersiz kılmışsa döndürülür. Bu değer, kalan koleksiyon isteklerinin (liste kaynakları) sayısını sağlar. |
| x-MS-ratelimit-kalan-kiracı-kaynak-istekler |Kiracı kapsamlı kaynak türü istekleri kaldı.<br /><br />Bu üst bilgi yalnızca kiracı düzeyindeki istekler için ve yalnızca bir hizmet varsayılan sınırı geçersiz kılmışsa eklenir. Kaynak Yöneticisi kiracı okuma veya yazma işlemleri yerine bu değeri ekler. |
| x-MS-ratelimit-kalan-Tenant-Resource-Entities-oku |Kiracı kapsamlı kaynak türü toplama istekleri kaldı.<br /><br />Bu üst bilgi yalnızca kiracı düzeyindeki istekler için ve yalnızca bir hizmet varsayılan sınırı geçersiz kılmışsa eklenir. |

Kaynak sağlayıcı ayrıca, kalan istekler hakkında bilgi içeren yanıt üstbilgilerini de döndürebilir. Işlem kaynak sağlayıcısı tarafından döndürülen yanıt üstbilgileri hakkında daha fazla bilgi için bkz. [çağrı hızı bilgilendirici yanıt üst bilgileri](/troubleshoot/azure/virtual-machines/troubleshooting-throttling-errors#call-rate-informational-response-headers).

## <a name="retrieving-the-header-values"></a>Üst bilgi değerleri alınıyor

Kodunuzda veya betiğinizdeki Bu üstbilgi değerlerinin alınması herhangi bir üst bilgi değerini almaktan farklı değildir. 

Örneğin, **C#**' de, aşağıdaki kodla **Yanıtla** adlı bir **HttpWebResponse** nesnesinden üst bilgi değerini alırsınız:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

**PowerShell**'de Invoke-WebRequest bir işlemden üst bilgi değerini alırsınız.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Tüm PowerShell örnekleri için bkz. [bir abonelik için Kaynak Yöneticisi sınırlarını denetleme](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).

Hata ayıklama için geri kalan istekleri görmek isterseniz, **PowerShell** cmdlet 'inizdeki **-Debug** parametresini sağlayabilirsiniz.

```powershell
Get-AzResourceGroup -Debug
```

Aşağıdaki yanıt değeri de dahil olmak üzere çok sayıda değer döndürür:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Yazma sınırlarını almak için bir yazma işlemi kullanın: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

Aşağıdaki değerler dahil olmak üzere çok sayıda değer döndürür:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

**Azure CLI**'de, daha ayrıntılı seçeneğini kullanarak üst bilgi değerini alırsınız.

```azurecli
az group list --verbose --debug
```

Aşağıdaki değerler dahil olmak üzere çok sayıda değer döndürür:

```output
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

Yazma sınırlarını almak için bir yazma işlemi kullanın: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Aşağıdaki değerler dahil olmak üzere çok sayıda değer döndürür:

```output
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="next-steps"></a>Sonraki adımlar

* Tüm PowerShell örnekleri için bkz. [bir abonelik için Kaynak Yöneticisi sınırlarını denetleme](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Sınırlamalar ve Kotalar hakkında daha fazla bilgi için bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../../azure-resource-manager/management/azure-subscription-service-limits.md).
* Zaman uyumsuz REST isteklerini işleme hakkında bilgi edinmek için bkz. [zaman uyumsuz Azure Işlemlerini izleme](async-operations.md).
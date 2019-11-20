---
title: İstek sınırları ve azaltma
description: Abonelik sınırlarına ulaşıldı, Azure Resource Manager istekleri azaltma kullanmayı açıklar.
ms.topic: conceptual
ms.date: 10/26/2019
ms.custom: seodec18
ms.openlocfilehash: b47943d69d209f5a0406c293b5a24c6ac0ad0c10
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150481"
---
# <a name="throttling-resource-manager-requests"></a>Resource Manager istekleri azaltma

Bu makalede, Azure Resource Manager isteklerinin nasıl kısıtlanmakta olduğu açıklanır. Sınıra ulaşmadan önce kalan isteklerin sayısını ve sınıra ulaştığınızda yanıt vermeyi nasıl izleyeceğinizi gösterir.

Kısıtlama iki düzeyde gerçekleşir. Abonelik ve kiracı için istekleri kısıtlar Azure Resource Manager. İstek, abonelik ve kiracı için azaltma limitlerinin altındaysa, Kaynak Yöneticisi isteği kaynak sağlayıcısına yönlendirir. Kaynak sağlayıcı, işlemlerine uyarlanmış azaltma sınırlarını uygular. Aşağıdaki görüntüde, bir istek kullanıcıdan Azure Resource Manager ve kaynak sağlayıcısına geçtiğinde, kısıtlama nasıl uygulanacağını gösterir.

![İstek azaltma](./media/resource-manager-request-limits/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Abonelik ve kiracı sınırları

Her abonelik düzeyi ve kiracı düzeyindeki işlem, azaltma sınırlarına tabidir. Abonelik istekleri, aboneliğinizdeki kaynak gruplarını alma gibi abonelik KIMLIĞINIZI geçirmeyi içerir. Kiracı isteklerini, geçerli Azure konumlarının alma gibi abonelik Kimliğinizi dahil değildir.

Saat başına varsayılan azaltma sınırları aşağıdaki tabloda gösterilmiştir.

| Kapsam | İşlemler | Sınır |
| ----- | ---------- | ------- |
| Abonelik | okuma | 12000 |
| Abonelik | /Delete | 15000 |
| Abonelik | blok | 1200 |
| Kiracı | okuma | 12000 |
| Kiracı | blok | 1200 |

Bu sınırların kapsamı, istekleri yapan güvenlik sorumlusu ve abonelik kimliği veya kiracı kimliğine göre belirlenir. İstekleriniz birden fazla güvenlik sorumlusundan geliyorsa, abonelik veya kiracıdaki sınırınız saatte 12.000 ve 1.200’den büyüktür.

Bu limitler her bir Azure Resource Manager örneğine uygulayın. Her Azure bölgesi içinde birden çok örneği vardır ve Azure Resource Manager tüm Azure bölgelerine dağıtılır.  Bu nedenle, uygulamada sınırlar Bu limitlerden daha yüksektir. Bir kullanıcının istekleri genellikle farklı Azure Resource Manager örnekleri tarafından işlenir.

## <a name="resource-provider-limits"></a>Kaynak sağlayıcısı sınırları

Kaynak sağlayıcıları kendi azaltma sınırlarını uygular. Kaynak Yöneticisi asıl KIMLIĞE ve Kaynak Yöneticisi örneğine göre kısıtladığından, kaynak sağlayıcısı önceki bölümde varsayılan limitlerden daha fazla istek alabilir.

Bu bölümde, yaygın olarak kullanılan bazı kaynak sağlayıcılarının azaltma sınırları ele alınmaktadır.

### <a name="storage-throttling"></a>Depolama alanı azaltma

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Ağ azaltma

Microsoft. Network kaynak sağlayıcısı aşağıdaki kısıtlama sınırlarını uygular:

| İşlem | Sınır |
| --------- | ----- |
| yazma/silme (PUT) | 5 dakikada 1000 |
| okuma (GET) | 5 dakikada 10000 |

### <a name="compute-throttling"></a>İşlem azaltma

İşlem işlemlerine yönelik azaltma sınırları hakkında bilgi için bkz. [API azaltma hataları sorunlarını giderme-işlem](../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md).

Sanal makine ölçek kümesi içindeki sanal makine örneklerini denetlemek için, [Sanal Makine Ölçek Kümeleri işlemlerini](/rest/api/compute/virtualmachinescalesetvms)kullanın. Örneğin, sanal makine örneklerinin güç durumunu denetlemek için [sanal makine ölçek kümesi VM 'leri-](/rest/api/compute/virtualmachinescalesetvms/list) parametreleri ile Listele seçeneğini kullanın. Bu API, istek sayısını azaltır.

### <a name="azure-resource-graph-throttling"></a>Azure Kaynak Grafiği azaltma

Azure Kaynak Grafiği, istek sayısını işlemlerine göre sınırlandırır. Kalan istekleri ve sınıra ulaşıldığında nasıl yanıt verileceğini öğrenmek için bu makaledeki adımlar kaynak grafı için de geçerlidir. Ancak, kaynak grafiği kendi sınırını ve sıfırlama oranını ayarlar. Daha fazla bilgi için bkz. [Azure Kaynak Grafı'nda Kapasite Azaltma](../governance/resource-graph/overview.md#throttling).

## <a name="request-increase"></a>İstek artışı

Bazen, kısıtlama limitleri artırılabilir. Senaryonuza yönelik azaltma limitlerinin arttırılabiliyor olup olmadığını görmek için bir destek isteği oluşturun. Çağırma örüntüsünün ayrıntıları değerlendirilecek.

## <a name="error-code"></a>Hata kodu

Sınıra ulaştığınızda, HTTP durum kodu alma **429 çok fazla istek**. Yanıt, bir sonraki isteği göndermeden önce uygulamanızın beklemesi gereken saniye sayısını (veya uyku) belirten bir **yeniden deneme** değeri içerir. Yeniden deneme değeri dolmadan isteği gönderirseniz, isteğiniz işlenir değil ve yeni bir yeniden deneme değeri döndürülür.

Belirtilen süre bekledikten sonra Azure bağlantınızı kapatıp yeniden açabilirsiniz. Bağlantıyı sıfırlayarak, farklı bir Azure Resource Manager örneğine bağlanabilirsiniz.

Azure SDK kullanıyorsanız, SDK otomatik yeniden deneme yapılandırmasına sahip olabilir. Daha fazla bilgi için bkz. [Azure hizmetleri Için yeniden deneme Kılavuzu](/azure/architecture/best-practices/retry-service-specific).

Bazı kaynak sağlayıcıları geçici bir sorunu raporlamak için 429 döndürür. Bu sorun, isteğiniz tarafından doğrudan neden olmayan bir aşırı yükleme koşulu olabilir. Ya da, hedef kaynağın veya bağımlı kaynağın durumu hakkında geçici bir hata temsil eder. Örneğin, ağ kaynak sağlayıcısı, hedef kaynak başka bir işlem tarafından kilitlendiğinde **RetryableErrorDueToAnotherOperation** hata kodu ile 429 döndürür. Hatanın azaltma veya geçici bir koşuldan mi geldiğini öğrenmek için yanıtta hata ayrıntılarını görüntüleyin.

## <a name="remaining-requests"></a>Kalan istekler

Yanıt üst bilgilerini inceleyerek, kalan istek sayısını belirleyebilirsiniz. Okuma istekleri, kalan okuma isteklerinin sayısı için üst bilgide bir değer döndürür. Yazma istekleri, kalan yazma isteklerinin sayısı için bir değer içerir. Aşağıdaki tabloda, bu değerler için inceleyebilirsiniz yanıt üstbilgilerini açıklanmaktadır:

| Yanıt üst bilgisi | Açıklama |
| --- | --- |
| x-MS-ratelimit-Remaining-Subscription-Reads |Abonelik kapsamı, kalan okur. Bu değer, okuma işlemlerinin döndürülür. |
| x-MS-ratelimit-Remaining-Subscription-Writes |Abonelik kapsamı, kalan yazar. Bu değer, yazma işlemlerinin döndürülür. |
| x-MS-ratelimit-Remaining-tenant-Reads |Kalan kapsamlı kiracı okur |
| x-ms-ratelimit-remaining-tenant-writes |Kalan kapsamlı Kiracı yazar. |
| x-MS-ratelimit-Remaining-Subscription-Resource-Requests |Abonelik, kaynak türü istekleri kalan kapsamı.<br /><br />Bu üst bilgi değeri yalnızca bir hizmet varsayılan sınırı geçersiz kılınmış döndürülür. Resource Manager abonelik okuma ve yazma yerine bu değeri ekler. |
| x-MS-ratelimit-Remaining-Subscription-Resource-entities-Read |Abonelik, kaynak türü toplama isteklerini kalan kapsamı.<br /><br />Bu üst bilgi değeri yalnızca bir hizmet varsayılan sınırı geçersiz kılınmış döndürülür. Bu değer, kalan toplama isteklerini (liste kaynaklar) sayısını sağlar. |
| x-MS-ratelimit-Remaining-tenant-Resource-Requests |Kiracı kaynak türü istekleri kalan kapsamı.<br /><br />Kiracı düzeyinde istek için yalnızca bu üst bilgi eklenir ve bir hizmet, yalnızca varsayılan sınırı geçersiz kılınmış. Kaynak Yöneticisi Kiracı okuma veya yazma yerine bu değeri ekler. |
| x-MS-ratelimit-Remaining-tenant-Resource-entities-Read |Kiracı kaynak türü toplama isteklerini kalan kapsamı.<br /><br />Kiracı düzeyinde istek için yalnızca bu üst bilgi eklenir ve bir hizmet, yalnızca varsayılan sınırı geçersiz kılınmış. |

Kaynak sağlayıcı ayrıca, kalan istekler hakkında bilgi içeren yanıt üstbilgilerini de döndürebilir. Işlem kaynak sağlayıcısı tarafından döndürülen yanıt üstbilgileri hakkında daha fazla bilgi için bkz. [çağrı hızı bilgilendirici yanıt üst bilgileri](../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md#call-rate-informational-response-headers).

## <a name="retrieving-the-header-values"></a>Üstbilgi değerlerini alma

Bu kod veya betik üstbilgi değerlerini alma herhangi bir üst bilgi değeri almaktan farklı değildir. 

Örneğin, **C#** , üstbilgi değerini almak bir **HttpWebResponse** adlı nesne **yanıt** aşağıdaki kod ile:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

İçinde **PowerShell**, üst bilgi değeri bir Invoke-WebRequest işlemden alın.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Tam bir PowerShell örnek için bkz: [bir abonelik için Resource Manager sınırları denetle](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).

Hata ayıklama için kalan istekler görmek istiyorsanız, sağlayabilir **-hata ayıklama** parametresi, **PowerShell** cmdlet'i.

```powershell
Get-AzResourceGroup -Debug
```

Şu yanıt değeri de dahil olmak üzere birçok değer döndürür:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Yazma sınırları almak için bir yazma işlemi kullanın: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

Aşağıdaki değerleri dahil olmak üzere birçok değer döndürür:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

İçinde **Azure CLI**, daha ayrıntılı seçeneğini kullanarak üstbilgi değeri alınamıyor.

```azurecli
az group list --verbose --debug
```

Aşağıdaki değerleri dahil olmak üzere birçok değer döndürür:

```azurecli
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

Yazma sınırları almak için bir yazma işlemi kullanın: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Aşağıdaki değerleri dahil olmak üzere birçok değer döndürür:

```azurecli
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

* Tam bir PowerShell örnek için bkz: [bir abonelik için Resource Manager sınırları denetle](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Limitler ve kotalar hakkında daha fazla bilgi için bkz: [Azure aboneliği ve hizmet limitleri, kotalar ve kısıtlamalar](../azure-subscription-service-limits.md).
* Zaman uyumsuz REST istekleri işleme hakkında bilgi edinmek için [Azure zaman uyumsuz işlemleri izleme](resource-manager-async-operations.md).

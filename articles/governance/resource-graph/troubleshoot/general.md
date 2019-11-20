---
title: Sık karşılaşılan hataları giderme
description: Azure Kaynak Grafmasıyla Azure kaynaklarını sorgulama sorunlarını giderme hakkında bilgi edinin.
ms.date: 10/18/2019
ms.topic: troubleshooting
ms.openlocfilehash: e8f42650265b1ca400731365203408eeb22a4e4c
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73958515"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Azure Kaynak Grafiği 'ni kullanarak hatalarda sorun giderme

Azure Kaynak Grafiği ile Azure kaynaklarını sorgularken hatalarla karşılaşabilirsiniz. Bu makalede, oluşabilecek çeşitli hatalar ve bunların nasıl çözümleneceği açıklanmaktadır.

## <a name="finding-error-details"></a>Hata ayrıntılarını bulma

Birçok hata, Azure Kaynak Graf ile bir sorgu çalıştırılırken bir sorunun sonucudur. Sorgu başarısız olduğunda, SDK başarısız sorguyla ilgili ayrıntıları sağlar. Bu bilgiler sorunu, düzeltilmesi ve sonraki bir sorgunun başarılı olması için gösterir.

## <a name="general-errors"></a>Genel hatalar

### <a name="toomanysubscription"></a>Senaryo: çok fazla abonelik

#### <a name="issue"></a>Sorun

[Azure ışıklı](../../../lighthouse/overview.md)abonelikler de dahil olmak üzere 1000 ' den fazla aboneliğe erişimi olan müşteriler, Azure Resource Graph 'a yönelik tek bir çağrıda verileri tüm aboneliklerde getiremez.

#### <a name="cause"></a>Nedeni

Azure CLı ve PowerShell yalnızca ilk 1000 aboneliğini Azure Kaynak grafiğine ilet. Azure Kaynak Grafiği REST API, üzerinde sorgu gerçekleştirmek için en fazla abonelik sayısını kabul eder.

#### <a name="resolution"></a>Çözüm

Aboneliklerin bir alt kümesiyle 1000 abonelik sınırının altında kalmak için toplu istek istekleri. Çözüm, PowerShell 'deki **abonelik** parametresini kullanıyor.

```azurepowershell-interactive
# Replace this query with your own
$query = 'Resources | project type'

# Fetch the full array of subscription IDs
$subscriptions = Get-AzSubscription
$subscriptionIds = $subscriptions.Id

# Create a counter, set the batch size, and prepare a variable for the results
$counter = [PSCustomObject] @{ Value = 0 }
$batchSize = 1000
$response = @()

# Group the subscriptions into batches
$subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

# Run the query for each batch
foreach ($batch in $subscriptionsBatch){ $response += Search-AzGraph -Query $query -Subscription $batch.Group }

# View the completed results of the query on all subscriptions
$response
```

### <a name="rest-contenttype"></a>Senaryo: desteklenmeyen Içerik türü REST üst bilgisi

#### <a name="issue"></a>Sorun

Azure Kaynak grafiğini sorgulayan müşteriler REST API bir _500_ (Iç sunucu hatası) yanıtı alır.

#### <a name="cause"></a>Nedeni

Azure Kaynak Grafiği REST API yalnızca bir **Application/json**`Content-Type` destekler. Bazı REST araçları veya aracıları, REST API tarafından desteklenmeyen **metin/düz**için varsayılan değer.

#### <a name="resolution"></a>Çözüm

Azure Kaynak Grafiği 'ni sorgulamak için kullanmakta olduğunuz aracın veya aracının, **Application/JSON**için yapılandırılmış REST API üst bilgi `Content-Type` olduğunu doğrulayın.

### <a name="rest-403"></a>Senaryo: listedeki tüm abonelikler için okuma izni yok

#### <a name="issue"></a>Sorun

Azure Kaynak grafik sorgusuna sahip bir abonelik listesini açıkça geçiren müşteriler _403_ (yasak) yanıtını alır.

#### <a name="cause"></a>Nedeni

Müşterinin tüm sağlanmış abonelikler üzerinde okuma izni yoksa, uygun güvenlik haklarının olmaması nedeniyle istek reddedilir.

#### <a name="resolution"></a>Çözüm

Sorguyu çalıştıran müşterinin en azından okuma erişimine sahip olduğu abonelik listesine en az bir abonelik ekleyin. Daha fazla bilgi için bkz. [Azure Kaynak Grafında izinler](../overview.md#permissions-in-azure-resource-graph).

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

- Azure [forumları](https://azure.microsoft.com/support/forums/)aracılığıyla Azure uzmanlarından yanıtlar alın.
- [@AzureSupport](https://twitter.com/azuresupport) hesabı ile bağlantı kurun. Bu resmi Microsoft Azure hesabı, müşteri deneyimini geliştirmek için Azure topluluğunu doğru kaynaklara ulaştırır: yanıtlar, destek ve uzmanlar.
- Daha fazla yardıma ihtiyacınız varsa, bir Azure destek olayı dosyası gönderebilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**' ı seçin.
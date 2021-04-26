---
title: 'Hızlı başlangıç: ilk REST API sorgunuz'
description: Bu hızlı başlangıçta, REST API için kaynak grafik uç noktasını çağırmak ve ilk sorgunuzu çalıştırmak için adımları izleyin.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 670ceba95d937be278c44c34704cb844eead480d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920080"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-rest-api"></a>Hızlı başlangıç: REST API kullanarak ilk kaynak grafik sorgunuzu çalıştırın

REST API ile Azure Kaynak grafiğini kullanmanın ilk adımı, bekleyen API 'Leri çağırmak için kullanabileceğiniz bir aracınız olup olmadığını denetletkullanmaktır. Bu hızlı başlangıç, Azure Kaynak Grafiği REST API uç noktası çağırarak bir sorgu çalıştırma ve sonuçları alma sürecinde size yol gösterir.

Bu işlemin sonunda REST API uç noktaları çağırma ve ilk kaynak grafik sorgunuzu çalıştırma araçlarına sahip olacaksınız.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="getting-started-with-rest-api"></a>REST API'sini kullanmaya başlama

REST API konusunda bilginiz yoksa özellikle istek URI'si ve istek gövdesi olmak üzere REST API hakkında genel bilgi edinmek için [Azure REST API Başvurusu](/rest/api/azure/) sayfasını inceleyerek başlayın. Bu makalede, Azure Resource Graph ile çalışmaya yönelik yönergeler sağlamak ve bunlarla ilgili bir bilgi almak için bu kavramlar kullanılmaktadır. [ARMClient](https://github.com/projectkudu/ARMClient) gibi araçlar yetkilendirme adımlarını otomatik olarak gerçekleştirebilir ve bu nedenle yeni başlayanlar için önerilir.

Azure Kaynak Grafiği özellikleri için bkz. [Azure Kaynak grafiği REST API](/rest/api/azure-resourcegraph/).

### <a name="rest-api-and-powershell"></a>REST API ve PowerShell

REST API çağrısı yapmak için bir aracınız yoksa bu yönergeleri PowerShell kullanarak gerçekleştirebilirsiniz. Aşağıdaki kod örneği, Azure ile kimlik doğrulaması için bir üst bilgi alır. Bir kimlik doğrulaması üst bilgisi (bazen **Taşıyıcı belirteç** olarak da adlandırılır) oluşturun ve bağlanılacak REST API URI'sini herhangi bir parametre ya da **İstek Gövdesi** ile sağlayın:

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2020-01-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

`{subscriptionId}` `$restUri` Aboneliğiniz hakkında bilgi almak için değişkende değiştirin.
`$response`Değişkeni, cmdlet 'inin sonucunu tutar `Invoke-RestMethod` ve bu, [ConvertFrom-JSON](/powershell/module/microsoft.powershell.utility/convertfrom-json)gibi cmdlet 'lerle ayrıştırılabilir. REST API hizmet uç noktası bir **İstek Gövdesi** bekliyorsa `Invoke-RestMethod` öğesinin `-Body` parametresine JSON biçiminde bir değişken sağlayın.

## <a name="run-your-first-resource-graph-query"></a>İlk Kaynak Grafiği sorgunuzu çalıştırma

Seçtiğiniz ortamınıza eklenen REST API araçları sayesinde, basit bir kaynak grafiği sorgusu denemenize zaman atalım. Sorgu, ilk beş Azure kaynağını her bir kaynağın **adı** ve **kaynak türü** ile döndürür.

Her REST API çağrısının istek gövdesinde, kendi değeri ile değiştirmeniz gereken bir değişken vardır:

- `{subscriptionID}` - Abonelik kimliğinizle değiştirin

1. REST API ve uç noktasını kullanarak ilk Azure Kaynak Grafiği sorgunuzu çalıştırın `resources` :

   - REST API URI'si

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - İstek Gövdesi

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5"
     }
     ```

   > [!NOTE]
   > Bu sorgu örneği gibi bir sıralama değiştiricisi sağlamadığından `order by` , bu sorguyu birden çok kez çalıştırmak, istek başına farklı bir kaynak kümesi sunmamasından kaynaklanıyor olabilir.

1. `resouces`Uç nokta çağrısını güncelleştirin ve **sorguyu** `order by` **ad** özelliği olarak değiştirin:

   - REST API URI'si

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - İstek Gövdesi

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5 | order by name asc"
     }
     ```

   > [!NOTE]
   > İlk sorguda olduğu gibi, bu sorguyu birden çok kez çalıştırmak, muhtemelen istek başına farklı bir kaynak kümesi sunacaktır. Sorgu komutlarının düzeni önemlidir. Bu örnekte `order by`, `limit`’den sonra gelmektedir. Bu komut sırası ilk olarak sorgu sonuçlarını kısıtlar ve sonra sıralar.

1. `resources`Uç nokta çağrısını güncelleştirin ve **sorguyu** ilk önce `order by` **ad** özelliğine, sonra da `limit` en üstteki beş sonuca değiştirin:

   - REST API URI'si

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - İstek Gövdesi

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | order by name asc | limit 5"
     }
     ```

Son sorgu birkaç kez çalıştırıldığında, ortamınızdaki hiçbir şeyin değişmediği kabul edildiğinde döndürülen sonuçlar tutarlı ve **ad** özelliğine göre sıralanır, ancak yine de en üstteki beş sonuçla sınırlıdır.

Azure Kaynak Grafiği REST API çağrılarına daha fazla örnek için bkz. [Azure Kaynak GRAFIĞI Rest örnekleri](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources#examples).

## <a name="clean-up-resources"></a>Kaynakları temizleme

REST API kaldırılacak kitaplık veya modüller yok. Çağrıları yapmak için _Armclient_ veya _Postman_ gibi bir araç yüklediyseniz ve artık gerekmiyorsa, aracı şimdi kaldırabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, kaynak Graph REST API uç noktası çağırdı ve ilk sorgunuzu çalıştırırsınız. Kaynak grafik dili hakkında daha fazla bilgi edinmek için sorgu dili ayrıntıları sayfasına ilerleyin.

> [!div class="nextstepaction"]
> [Sorgu dili hakkında daha fazla bilgi alın](./concepts/query-language.md)

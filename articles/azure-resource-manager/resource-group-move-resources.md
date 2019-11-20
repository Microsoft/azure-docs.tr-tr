---
title: Kaynakları yeni bir aboneliğe veya kaynak grubuna taşıma
description: Kaynakları yeni kaynak grubuna veya aboneliğe taşıma için Azure Resource Manager'ı kullanın.
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: f106de7fd35bdbe91033af173b1f338dd251f4e8
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149694"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma

Bu makalede, Azure kaynaklarını başka bir Azure aboneliğine veya aynı abonelik altındaki başka bir kaynak grubuna nasıl taşıyacağınız gösterilmektedir. Azure portalı, Azure PowerShell, Azure CLI veya REST API ile kaynakları taşıyabilirsiniz.

Taşıma işlemi sırasında hem kaynak grubu hem de hedef grup kilitlenir. Yazma ve silme işlemleri taşıma işlemi tamamlanana kadar kaynak gruplarında engellenir. Bu kilit, kaynak gruplarındaki kaynakları ekleyemez, güncelleştiremez veya silemezsiniz. Bu, kaynakların dondurulmuş olduğu anlamına gelmez. Örneğin, bir SQL Server ve veritabanı yeni bir kaynak grubuna taşırsanız, veritabanı kullanan bir uygulama kapalı kalma süresi olmadan karşılaşır. Bunu hala okuyabilir ve veritabanına yazma. Kilit en fazla dört saat için en fazla dört saat olabilir, ancak çoğu zaman çok daha az zaman tamamlanır.

Bir kaynağı taşıdığınızda yalnızca onu yeni bir kaynak grubuna veya aboneliğe taşımış olursunuz. Bu işlem kaynağın konumunu değiştirmez.

## <a name="checklist-before-moving-resources"></a>Kaynakları taşımadan önce Yapılacaklar listesi

Bir kaynağı taşımadan önce yapmanız gereken bazı önemli adımlar vardır. Bu koşulları doğrulayarak hataları önleyebilirsiniz.

1. Taşımak istediğiniz kaynaklar taşıma işlemini desteklemelidir. Hangi kaynakların taşınmasını desteklediğini bir liste için bkz. [kaynaklar Için taşıma işlemi desteği](move-support-resources.md).

1. Bazı hizmetlerde kaynakları taşırken belirli sınırlamalar veya gereksinimler vardır. Aşağıdaki hizmetlerden birini taşıyorsanız, taşımadan önce bu Kılavuzu kontrol edin.

   * [Uygulama Hizmetleri taşıma Kılavuzu](./move-limitations/app-service-move-limitations.md)
   * [Azure DevOps Services taşıma Kılavuzu](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Klasik dağıtım modeli taşıma Kılavuzu](./move-limitations/classic-model-move-limitations.md) -klasik işlem, klasik depolama, klasik sanal ağlar ve Cloud Services
   * [Ağ taşıma Kılavuzu](./move-limitations/networking-move-limitations.md)
   * [Kurtarma Hizmetleri taşıma Kılavuzu](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Sanal makineler taşıma Kılavuzu](./move-limitations/virtual-machines-move-limitations.md)

1. Kaynak ve hedef abonelikler etkin olmalıdır. Devre dışı bırakılmış bir hesabı etkinleştirirken sorun yaşıyorsanız, [bir Azure destek isteği oluşturun](../azure-supportability/how-to-create-azure-support-request.md). Seçin **abonelik yönetimi** sorun türü için.

1. Kaynak ve hedef abonelikler aynı içinde bulunmalıdır [Azure Active Directory kiracısı](../active-directory/develop/quickstart-create-new-tenant.md). Her iki aboneliğin aynı Kiracı Kimliğine sahip denetlemek için Azure PowerShell veya Azure CLI'yı kullanın.

   Azure PowerShell için şunu kullanın:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Azure CLI için şunu kullanın:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Kaynak ve hedef abonelikler için Kiracı kimlikleri aynı değilse, Kiracı kimlikleri karşılaştırmak için aşağıdaki yöntemleri kullanın:

   * [Azure aboneliğinin sahipliğini başka bir hesaba devretme](../billing/billing-subscription-transfer.md)
   * [Azure Active Directory'ye bir Azure aboneliğini ekleme veya ilişkilendirme](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Hedef abonelik, taşınan kaynağın kaynak sağlayıcısına kayıtlı olmalıdır. Belirten bir hata alırsanız, **kaynak türü için abonelik kayıtlı değil**. Bir kaynağı yeni bir aboneliğe taşırken bu hatayla karşılaşabilirsiniz, ancak bu abonelik bu kaynak türüyle hiç kullanılmadıysa.

   PowerShell için kayıt durumunu almak için aşağıdaki komutları kullanın:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Bir kaynak sağlayıcısını kaydetmek için kullanın:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Azure CLI için kayıt durumunu almak için aşağıdaki komutları kullanın:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Bir kaynak sağlayıcısını kaydetmek için kullanın:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Kaynakları taşıma hesabı, en az aşağıdaki izinlere sahip olmanız gerekir:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** kaynak kaynak grubu.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** hedef kaynak grubunda.

1. Kaynakları taşımadan önce kaynaklara Taşımakta olduğunuz abonelik için abonelik kotaları denetleyin. Kaynakları taşıma abonelik, sınırları aşamaz anlamına gelir, kota artışı isteği olup olmadığını gözden geçirmek gerekir. Limitler ve bir artış istemek nasıl bir listesi için bkz. [Azure aboneliği ve hizmet limitleri, kotalar ve kısıtlamalar](../azure-subscription-service-limits.md).

1. **Abonelikler arasında geçiş için kaynak ve bağımlı kaynakları aynı kaynak grubunda yer almalıdır ve birlikte taşınmaları gerekir.** Örneğin, yönetilen disklere sahip bir VM, diğer bağımlı kaynaklarla birlikte VM ve yönetilen disklerin birlikte taşınmasını gerektirir.

   Bir kaynağı yeni bir aboneliğe taşıyorsanız, kaynağın bağımlı kaynaklara sahip olup olmadığını ve aynı kaynak grubunda bulunup bulunmadığını kontrol edin. Kaynaklar aynı kaynak grubunda değilse, kaynakların aynı kaynak grubuyla birleştirilebilir olup olmadığını denetleyin. Bu durumda, kaynak grupları arasında taşıma işlemi kullanarak tüm bu kaynakları aynı kaynak grubuna getirin.

   Daha fazla bilgi için bkz. [abonelikler arasında taşıma senaryosu](#scenario-for-move-across-subscriptions).

## <a name="scenario-for-move-across-subscriptions"></a>Abonelikler arasında geçiş senaryosu

Kaynakları bir abonelikten diğerine taşımak üç adımlı bir işlemdir:

![çapraz abonelik taşıma senaryosu](./media/resource-group-move-resources/cross-subscription-move-scenario.png)

Çizim amacıyla yalnızca bir bağımlı kaynağınız var.

* 1\. Adım: bağımlı kaynaklar farklı kaynak grupları arasında dağıtılırsa, önce bunları tek bir kaynak grubuna taşıyın.
* 2\. Adım: kaynağı ve bağımlı kaynakları kaynak aboneliğinden hedef aboneliğe taşıyın.
* 3\. Adım: Isteğe bağlı olarak, bağımlı kaynakları hedef aboneliğin içindeki farklı kaynak gruplarına yeniden dağıtın. 

## <a name="validate-move"></a>Taşıma doğrula

[Taşıma işlemi doğrulama](/rest/api/resources/resources/validatemoveresources) kaynakları taşımadan taşıma senaryonuza sınamanızı sağlar. Taşımanın başarılı olup olmadığını denetlemek için bu işlemi kullanın. Bir taşıma isteği gönderdiğinizde doğrulama otomatik olarak çağrılır. Bu işlemi yalnızca sonuçları önceden belirlemeniz gerektiğinde kullanın. Bu işlemin çalıştırılması için ihtiyacınız vardır:

* Kaynak kaynak grubu adı
* Hedef kaynak grubunun kaynak kimliği
* Her bir kaynağın kaynak kimliği taşımak için
* [erişim belirteci](/rest/api/azure/#acquire-an-access-token) hesabınız için

Aşağıdaki isteği gönder:

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

İstek gövdesi ile:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

İstek düzgün biçimlendirilmiş olup olmadığını, işlemi döndürür:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

Doğrulama isteği kabul edildi, ancak henüz taşıma işlemi başarılı olur, belirlenen taşınmadığından 202 durum kodunu gösterir. `location` Değeri uzun süre çalışan işlemin durumunu denetlemek için kullandığınız bir URL içerir.  

Durumu denetlemek için aşağıdaki isteği gönder:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

İşlemi hala devam ederken, 202 durum kodu almaya devam eder. Belirtilen saniye sayısı bekleyin `retry-after` yeniden denemeden önce değeri. Taşıma işlemi başarıyla doğrular, 204 durum kodu alırsınız. Taşıma doğrulaması başarısız olursa, bir hata iletisi gibi alırsınız:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Portalı kullanma

Kaynakları taşımak için bu kaynaklarla kaynak grubunu seçin ve ardından **Taşı** düğmesini seçin.

![kaynakları taşıma](./media/resource-group-move-resources/select-move.png)

Kaynakları yeni kaynak grubu veya yeni bir aboneliğe taşıyor seçin.

Taşınacak kaynaklar ve hedef kaynak grubunu seçin. Bu kaynaklar için betiklerini güncelleştirin ve seçmek gereken bildirimi **Tamam**. Önceki adımda düzenleme abonelik simgesini seçtiğinizde hedef abonelik de seçmeniz gerekir.

![hedef seçin](./media/resource-group-move-resources/select-destination.png)

İçinde **bildirimleri**, taşıma işleminin çalıştığını görürsünüz.

![taşıma durumu göster](./media/resource-group-move-resources/show-status.png)

Tamamlandığında, sonucunu bildirilir.

![taşıma sonucu göster](./media/resource-group-move-resources/show-result.png)

Bir hata alırsanız bkz. [Azure kaynaklarını yeni kaynak grubuna veya aboneliğe taşıma sorunlarını giderme](troubleshoot-move.md).

## <a name="use-azure-powershell"></a>Azure PowerShell’i kullanma

Mevcut kaynakları başka bir kaynak grubuna veya aboneliğe taşımak için [Move-AzResource](/powershell/module/az.resources/move-azresource) komutunu kullanın. Aşağıdaki örnek, birkaç kaynağın yeni bir kaynak grubuna nasıl taşınacağını gösterir.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Yeni bir aboneliğe taşımak için bir değer içerir. `DestinationSubscriptionId` parametresi.

Bir hata alırsanız bkz. [Azure kaynaklarını yeni kaynak grubuna veya aboneliğe taşıma sorunlarını giderme](troubleshoot-move.md).

## <a name="use-azure-cli"></a>Azure CLI kullanma

Var olan kaynakları başka bir kaynak grubuna veya aboneliğe taşıma için kullanın [az kaynak taşıma](/cli/azure/resource?view=azure-cli-latest#az-resource-move) komutu. Kaynak taşımak için kaynak kimliklerini sağlayın. Aşağıdaki örnek, birkaç kaynağın yeni bir kaynak grubuna nasıl taşınacağını gösterir. İçinde `--ids` parametresi, kaynak kimliklerini taşımak için boşlukla ayrılmış bir listesini sağlayın.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Yeni bir aboneliğe taşımak için sağlamak `--destination-subscription-id` parametresi.

Bir hata alırsanız bkz. [Azure kaynaklarını yeni kaynak grubuna veya aboneliğe taşıma sorunlarını giderme](troubleshoot-move.md).

## <a name="use-rest-api"></a>REST API’yi kullanma

Mevcut kaynakları başka bir kaynak grubuna veya aboneliğe taşımak için [kaynakları taşıma](/rest/api/resources/Resources/MoveResources) işlemini kullanın.

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

İstek gövdesinde taşımak için hedef kaynak grubu ve kaynakları belirtin.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Bir hata alırsanız bkz. [Azure kaynaklarını yeni kaynak grubuna veya aboneliğe taşıma sorunlarını giderme](troubleshoot-move.md).

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

**Soru: genellikle birkaç dakika süren kaynak taşıma işlemi, neredeyse bir saat boyunca çalışmaktadır. Yanlış bir sorun var mı?**

Bir kaynağın taşınması, farklı aşamalara sahip karmaşık bir işlemdir. Taşımaya çalıştığınız kaynağın yalnızca kaynak sağlayıcısından fazlasını içerebilir. Kaynak sağlayıcıları arasındaki bağımlılıklar nedeniyle Azure Resource Manager işlemin tamamlanabilmesi için 4 saat izin verir. Bu zaman dilimi, kaynak sağlayıcılarına geçici sorunlardan kurtulmasına olanak tanır. Taşıma isteğiniz 4 saatlik bir süre içindeyse, işlem tamamlanana kadar devam eder ve yine de başarılı olabilir. Tutarlılık sorunlarından kaçınmak için kaynak ve hedef kaynak grupları bu süre boyunca kilitlenir.

**Soru: kaynak grubu neden kaynak taşıma sırasında 4 saat boyunca kilitlidir?**

4 saatlik pencere, kaynak taşıma için izin verilen en uzun süredir. Taşınan kaynaklarda değişiklik yapılmasını engellemek için kaynak ve hedef kaynak gruplarının her ikisi de kaynak taşıma süresince kilitlenir.

Taşıma isteğinde iki aşama vardır. İlk aşamada kaynak taşınır. İkinci aşamada, bildirimler, taşınan kaynağa bağımlı diğer kaynak sağlayıcılarına gönderilir. Bir kaynak sağlayıcısı her iki aşamada de başarısız olursa, bir kaynak grubu 4 saatlik tüm pencere için kilitlenebilir. İzin verilen süre boyunca başarısız olan adımı yeniden dener Kaynak Yöneticisi.

Bir kaynak 4 saatlik bir pencere içinde taşınamayacağını Kaynak Yöneticisi her iki kaynak grubunun kilidini açar. Başarıyla taşınan kaynaklar hedef kaynak grubunda bulunur. Taşıyacağınız kaynaklar kaynak kaynak grubunun solununlardır.

**Soru: kaynak taşıma sırasında kaynak ve hedef kaynak gruplarının kilitlenmesinin etkileri nelerdir?**

Kilit, kaynak grubunu silmenizi, kaynak grubunda yeni bir kaynak oluşturmayı veya taşımayla ilgili kaynakların herhangi birini silmeyi önler.

Aşağıdaki görüntüde, bir kullanıcı devam eden bir taşımanın parçası olan bir kaynak grubunu silmeye çalıştığında Azure portal bir hata iletisi gösterilmektedir.

![Silmeye çalışan hata iletisini taşı](./media/resource-group-move-resources/move-error-delete.png)

**Soru: "MissingMoveDependentResources" hata kodu ne anlama geliyor?**

Bir kaynağı taşırken, bağımlı kaynakları hedef kaynak grubunda veya abonelikte bulunmalı ya da taşıma isteğine dahil olmalıdır. Bağımlı bir kaynak bu gereksinimi karşılamıyorsa MissingMoveDependentResources hata kodunu alırsınız. Hata iletisinde, taşıma isteğine dahil olması gereken bağımlı kaynakla ilgili ayrıntılar bulunur.

Örneğin, bir sanal makineyi taşımak, yedi kaynak türünün üç farklı kaynak sağlayıcısı ile taşınmasını gerektirebilir. Bu kaynak sağlayıcıları ve türleri şunlardır:

* Microsoft.Compute
   * virtualMachines
   * disklerinden
* Microsoft.Network
  * NetworkInterfaces
  * Publicıpaddresses
  * networkSecurityGroups
  * virtualNetworks
* Microsoft.Storage
  * storageAccounts

Diğer bir yaygın örnek, bir sanal ağın taşınmasını içerir. Bu sanal ağla ilişkili diğer birkaç kaynağı taşımanız gerekebilir. Taşıma isteği, genel IP adreslerinin, yol tablolarının, sanal ağ geçitlerinin, ağ güvenlik gruplarının ve başkalarının taşınmasını gerektirebilir.

**Soru: Azure 'daki bazı kaynakları neden taşıyamıyorum?**

Şu anda Azure desteği 'ndeki tüm kaynaklar hareket etmez. Taşımayı destekleyen kaynakların listesi için bkz. [kaynaklar Için taşıma işlemi desteği](move-support-resources.md).

## <a name="next-steps"></a>Sonraki adımlar

Hangi kaynakların taşınmasını desteklediğini bir liste için bkz. [kaynaklar Için taşıma işlemi desteği](move-support-resources.md).

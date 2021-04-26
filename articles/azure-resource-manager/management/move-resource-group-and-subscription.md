---
title: Kaynakları yeni bir aboneliğe veya kaynak grubuna taşıma
description: Kaynakları yeni bir kaynak grubuna veya aboneliğe taşımak için Azure Resource Manager kullanın.
ms.topic: conceptual
ms.date: 04/16/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7a50ecc6081f8fa7c7600ddf1f98a95eceafa73b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784632"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma

Bu makalede, Azure kaynaklarını başka bir Azure aboneliğine veya aynı abonelik altındaki başka bir kaynak grubuna nasıl taşıyacağınız gösterilmektedir. Azure portalı, Azure PowerShell, Azure CLI veya REST API ile kaynakları taşıyabilirsiniz.

Taşıma işlemi sırasında hem kaynak grubu hem de hedef grup kilitlenir. Taşıma tamamlanana kadar kaynak gruplarında yazma ve silme işlemleri engellenir. Bu kilit, kaynak gruplarındaki kaynakları ekleyemez, güncelleştiremez veya silemezsiniz. Bu, kaynakların dondurulmuş olduğu anlamına gelmez. Örneğin, bir Azure SQL mantıksal sunucusunu ve veritabanlarını yeni bir kaynak grubuna veya aboneliğe taşırsanız veritabanlarını kullanan uygulamalar kesinti yaşmaz. Yine de veritabanlarına okuma ve yazma yapılabilir. Kilit en fazla dört saat için en fazla dört saat olabilir, ancak çoğu zaman çok daha az zaman tamamlanır.

Bir kaynağı taşıdığınızda yalnızca onu yeni bir kaynak grubuna veya aboneliğe taşımış olursunuz. Bu işlem kaynağın konumunu değiştirmez.

## <a name="changed-resource-id"></a>Değiştirilen kaynak KIMLIĞI

Bir kaynağı taşıdığınızda, kaynak KIMLIĞINI değiştirirsiniz. Kaynak KIMLIĞI için standart biçim `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}` . Bir kaynağı yeni bir kaynak grubuna veya aboneliğe taşıdığınızda, bu yoldaki bir veya daha fazla değeri değiştirirsiniz.

Kaynak KIMLIĞINI her yerde kullanırsanız, bu değeri değiştirmeniz gerekir. Örneğin, portalda bir kaynak KIMLIĞINE başvuruda bulunan [özel bir panonuz](../../azure-portal/quickstart-portal-dashboard-azure-cli.md) varsa, bu değeri güncelleştirmeniz gerekir. Yeni kaynak KIMLIĞI için güncelleştirilmeleri gereken betikleri veya şablonları arayın.

## <a name="checklist-before-moving-resources"></a>Kaynakları taşımadan önce kullanılacak kontrol listesi

Bir kaynağı taşımadan önce gerçekleştirmeniz gereken bazı önemli adımlar vardır. Bu koşulları doğrulayarak hataları önleyebilirsiniz.

1. Taşımak istediğiniz kaynaklar taşıma işlemini desteklemelidir. Hangi kaynakların taşınmasını desteklediğini bir liste için bkz. [kaynaklar Için taşıma işlemi desteği](move-support-resources.md).

1. Bazı hizmetlerde kaynakları taşırken belirli sınırlamalar veya gereksinimler vardır. Aşağıdaki hizmetlerden birini taşıyorsanız, taşımadan önce bu Kılavuzu kontrol edin.

   * Azure Stack hub kullanıyorsanız, kaynakları gruplar arasında taşıyamazsınız.
   * [Uygulama Hizmetleri taşıma Kılavuzu](./move-limitations/app-service-move-limitations.md)
   * [Azure DevOps Services taşıma Kılavuzu](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Klasik dağıtım modeli taşıma Kılavuzu](./move-limitations/classic-model-move-limitations.md) -klasik işlem, klasik depolama, klasik sanal ağlar ve Cloud Services
   * [Ağ taşıma Kılavuzu](./move-limitations/networking-move-limitations.md)
   * [Kurtarma Hizmetleri taşıma Kılavuzu](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Sanal makineler taşıma Kılavuzu](./move-limitations/virtual-machines-move-limitations.md)
   * Bir Azure aboneliğini yeni bir yönetim grubuna taşımak için bkz. [abonelikleri taşıma](../../governance/management-groups/manage.md#move-subscriptions).

1. Doğrudan kaynağa (veya bir alt kaynağa) atanmış bir Azure rolü olan bir kaynağı taşırsanız, rol ataması taşınmaz ve yalnız bırakılmış olur. Taşıma işleminden sonra, rol atamasını yeniden oluşturmanız gerekir. Sonuç olarak, yalnız bırakılmış rol ataması otomatik olarak kaldırılır, ancak taşımadan önce rol atamasını kaldırmanızı öneririz.

    Rol atamalarının nasıl yönetileceği hakkında daha fazla bilgi için bkz. [Azure rol atamalarını listeleme](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) ve [Azure rolleri atama](../../role-based-access-control/role-assignments-portal.md).

1. Kaynak ve hedef abonelikler etkin olmalıdır. Devre dışı bırakılmış bir hesabı etkinleştirirken sorun yaşıyorsanız, [bir Azure destek isteği oluşturun](../../azure-portal/supportability/how-to-create-azure-support-request.md). Sorun türü için **Abonelik yönetimi** ' ni seçin.

1. Kaynak ve hedef abonelikler aynı [Azure Active Directory kiracısında](../../active-directory/develop/quickstart-create-new-tenant.md)bulunmalıdır. Her iki aboneliğin de aynı kiracı KIMLIĞINE sahip olup olmadığını denetlemek için Azure PowerShell veya Azure CLı kullanın.

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

   Kaynak ve hedef aboneliklerin kiracı kimlikleri aynı değilse, kiracı kimliklerini mutabık kılmak için aşağıdaki yöntemleri kullanın:

   * [Azure aboneliğinin sahipliğini başka bir hesaba devretme](../../cost-management-billing/manage/billing-subscription-transfer.md)
   * [Azure Active Directory'ye bir Azure aboneliğini ekleme veya ilişkilendirme](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Hedef abonelik, taşınan kaynağın kaynak sağlayıcısına kayıtlı olmalıdır. Aksi takdirde, **aboneliğin bir kaynak türü için kayıtlı** olmadığını belirten bir hata alırsınız. Bir kaynağı yeni bir aboneliğe taşırken bu hatayla karşılaşabilirsiniz, ancak bu abonelik bu kaynak türüyle hiç kullanılmadıysa.

   PowerShell için, kayıt durumunu almak için aşağıdaki komutları kullanın:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Bir kaynak sağlayıcısını kaydetmek için şunu kullanın:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Azure CLı için, kayıt durumunu almak için aşağıdaki komutları kullanın:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Bir kaynak sağlayıcısını kaydetmek için şunu kullanın:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Kaynakları taşıyan hesabın en azından aşağıdaki izinlere sahip olması gerekir:

   * Kaynak kaynak grubundaki **Microsoft. resources/abonelikler/resourceGroups/Moveresobir/eylem** .
   * Hedef kaynak grubunda **Microsoft. resources/abonelikler/resourceGroups/Write** .

1. Kaynakları taşımadan önce, kaynakları taşıdığınız aboneliğin abonelik kotalarını kontrol edin. Kaynakları taşırken aboneliğin sınırları aşılacağından, kotanın bir artış isteyip isteyemeyeceğinizi gözden geçirmeniz gerekir. Limitlerin listesi ve artışın nasıl isteneceğini öğrenmek için bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../../azure-resource-manager/management/azure-subscription-service-limits.md).

1. **Abonelikler arasında geçiş için kaynak ve bağımlı kaynakları aynı kaynak grubunda yer almalıdır ve birlikte taşınmaları gerekir.** Örneğin, yönetilen disklere sahip bir VM, diğer bağımlı kaynaklarla birlikte VM ve yönetilen disklerin birlikte taşınmasını gerektirir.

   Bir kaynağı yeni bir aboneliğe taşıyorsanız, kaynağın bağımlı kaynaklara sahip olup olmadığını ve aynı kaynak grubunda bulunup bulunmadığını kontrol edin. Kaynaklar aynı kaynak grubunda değilse, kaynakların aynı kaynak grubunda birleştirilebilir olup olmadığını denetleyin. Bu durumda, kaynak grupları arasında taşıma işlemi kullanarak tüm bu kaynakları aynı kaynak grubuna getirin.

   Daha fazla bilgi için bkz. [abonelikler arasında taşıma senaryosu](#scenario-for-move-across-subscriptions).

## <a name="scenario-for-move-across-subscriptions"></a>Abonelikler arasında taşıma senaryosu

Kaynakları bir abonelikten diğerine taşımak üç adımlı bir işlemdir:

![çapraz abonelik taşıma senaryosu](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

Çizim amacıyla yalnızca bir bağımlı kaynağınız var.

* 1. Adım: bağımlı kaynaklar farklı kaynak grupları arasında dağıtılırsa, önce bunları tek bir kaynak grubuna taşıyın.
* 2. Adım: kaynağı ve bağımlı kaynakları kaynak aboneliğinden hedef aboneliğe taşıyın.
* 3. Adım: Isteğe bağlı olarak, bağımlı kaynakları hedef aboneliğin içindeki farklı kaynak gruplarına yeniden dağıtın.

## <a name="validate-move"></a>Taşımayı doğrula

[Taşıma işlemini Doğrula işlemi](/rest/api/resources/resources/moveresources) , kaynakları gerçekten taşımadan taşıma senaryonuzu test etmenizi sağlar. Taşımanın başarılı olup olmadığını denetlemek için bu işlemi kullanın. Bir taşıma isteği gönderdiğinizde doğrulama otomatik olarak çağrılır. Bu işlemi yalnızca sonuçları önceden belirlemeniz gerektiğinde kullanın. Bu işlemi çalıştırmak için şunlar gerekir:

* kaynak kaynak grubunun adı
* hedef kaynak grubunun kaynak KIMLIĞI
* Taşınacak her kaynağın kaynak KIMLIĞI
* hesabınız için [erişim belirteci](/rest/api/azure/#acquire-an-access-token)

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

İstek doğru biçimlendirildiğinden, işlem şunu döndürür:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

202 durum kodu, doğrulama isteğinin kabul edildiğini gösterir, ancak taşıma işleminin başarılı olup olmayacağını henüz belirlemiştir. `location`Değer, uzun süre çalışan işlemin durumunu denetlemek için kullandığınız BIR URL içerir.

Durumu denetlemek için aşağıdaki isteği gönderin:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

İşlem çalışmaya devam ederken, 202 durum kodunu almaya devam edersiniz. Yeniden denemeden önce değerde belirtilen saniye sayısını bekleyin `retry-after` . Taşıma işlemi başarıyla doğrulanırsa, 204 durum kodunu alırsınız. Taşıma doğrulaması başarısız olursa, şöyle bir hata iletisi alırsınız:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Portalı kullanma

Kaynakları taşımak için, bu kaynakları içeren kaynak grubunu seçin.

Kaynak grubunu görüntülediğinizde, taşı seçeneği devre dışıdır.

:::image type="content" source="./media/move-resource-group-and-subscription/move-first-view.png" alt-text="taşıma seçeneği devre dışı":::

Taşıma seçeneğini etkinleştirmek için, taşımak istediğiniz kaynakları seçin. Tüm kaynakları seçmek için, listenin en üstündeki onay kutusunu seçin. Ya da kaynakları tek tek seçin. Kaynakları seçtikten sonra taşı seçeneği etkinleştirilir.

:::image type="content" source="./media/move-resource-group-and-subscription/select-resources.png" alt-text="kaynakları seçin":::

**Taşı** düğmesini seçin.

:::image type="content" source="./media/move-resource-group-and-subscription/move-options.png" alt-text="taşıma seçenekleri":::

Bu düğme size üç seçenek sunar:

* Yeni bir kaynak grubuna taşıyın.
* Yeni bir aboneliğe taşıyın.
* Yeni bir bölgeye taşıyın. Bölgeleri değiştirmek için bkz. [kaynakları bölgeler arasında taşıma (kaynak grubundan)](../../resource-mover/move-region-within-resource-group.md?toc=/azure/azure-resource-manager/management/toc.json).

Kaynakları yeni bir kaynak grubuna mı yoksa yeni bir aboneliğe mi taşıdığınızı seçin.

Hedef kaynak grubunu seçin. Bu kaynaklar için betikleri güncelleştirmeniz ve **Tamam**' ı seçmeniz gerektiğini kabul edin. Yeni bir aboneliğe taşımayı seçtiyseniz, hedef aboneliği de seçmeniz gerekir.

:::image type="content" source="./media/move-resource-group-and-subscription/move-destination.png" alt-text="hedef seçin":::

Kaynakların taşınabileceği doğrulandıktan sonra taşıma işleminin çalıştığını belirten bir bildirim görürsünüz.

:::image type="content" source="./media/move-resource-group-and-subscription/move-notification.png" alt-text="uyarıyı":::

Tamamlandığında, sonuç size bildirilir.

## <a name="use-azure-powershell"></a>Azure PowerShell kullanma

Mevcut kaynakları başka bir kaynak grubuna veya aboneliğe taşımak için [Move-AzResource](/powershell/module/az.resources/move-azresource) komutunu kullanın. Aşağıdaki örnek, birkaç kaynağın yeni bir kaynak grubuna nasıl taşınacağını gösterir.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Yeni bir aboneliğe geçmek için parametresi için bir değer ekleyin `DestinationSubscriptionId` .

## <a name="use-azure-cli"></a>Azure CLI kullanma

Mevcut kaynakları başka bir kaynak grubuna veya aboneliğe taşımak için [az Resource Move](/cli/azure/resource#az_resource_move) komutunu kullanın. Taşınacak kaynakların kaynak kimliklerini sağlayın. Aşağıdaki örnek, birkaç kaynağın yeni bir kaynak grubuna nasıl taşınacağını gösterir. `--ids`Parametresinde, taşınacak kaynak kimliklerinin boşlukla ayrılmış bir listesini sağlayın.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Yeni bir aboneliğe geçmek için `--destination-subscription-id` parametresini sağlayın.

## <a name="use-rest-api"></a>REST API’yi kullanma

Mevcut kaynakları başka bir kaynak grubuna veya aboneliğe taşımak için [kaynakları taşıma](/rest/api/resources/resources/moveresources) işlemini kullanın.

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

İstek gövdesinde hedef kaynak grubunu ve taşınacak kaynakları belirtirsiniz.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

**Soru: genellikle birkaç dakika süren kaynak taşıma işlemi, neredeyse bir saat boyunca çalışmaktadır. Yanlış bir sorun var mı?**

Bir kaynağın taşınması, farklı aşamalara sahip karmaşık bir işlemdir. Taşımaya çalıştığınız kaynağın yalnızca kaynak sağlayıcısından fazlasını içerebilir. Kaynak sağlayıcıları arasındaki bağımlılıklar nedeniyle Azure Resource Manager işlemin tamamlanabilmesi için 4 saat izin verir. Bu zaman dilimi, kaynak sağlayıcılarına geçici sorunlardan kurtulmasına olanak tanır. Taşıma isteğiniz dört saatlik dönem içindeyse, işlem tamamlanmaya devam eder ve yine de başarılı olabilir. Tutarlılık sorunlarından kaçınmak için kaynak ve hedef kaynak grupları bu süre boyunca kilitlenir.

**Soru: kaynak grupum neden kaynak taşıma sırasında dört saat boyunca kilitlidir?**

Taşıma isteğine en fazla dört saat tamamlanmak için izin verilir. Taşınan kaynaklarda değişiklik yapılmasını engellemek için kaynak ve hedef kaynak gruplarının her ikisi de kaynak taşıma sırasında kilitlenir.

Taşıma isteğinde iki aşama vardır. İlk aşamada kaynak taşınır. İkinci aşamada, bildirimler, taşınan kaynağa bağımlı diğer kaynak sağlayıcılarına gönderilir. Bir kaynak sağlayıcısı her iki aşamada de başarısız olursa, kaynak grubu dört saatin tamamı için kilitlenebilir. İzin verilen süre boyunca başarısız olan adımı yeniden dener Kaynak Yöneticisi.

Bir kaynak dört saat içinde taşınamayacağını Kaynak Yöneticisi her iki kaynak grubunun da kilidini açar. Başarıyla taşınan kaynaklar hedef kaynak grubunda bulunur. Taşıyacağınız kaynaklar kaynak kaynak grubunun solununlardır.

**Soru: kaynak taşıma sırasında kaynak ve hedef kaynak gruplarının kilitlenmesinin etkileri nelerdir?**

Kilit, kaynak grubunu silmenizi, kaynak grubunda yeni bir kaynak oluşturmayı veya taşımayla ilgili kaynakların herhangi birini silmeyi önler.

Aşağıdaki görüntüde, bir kullanıcı devam eden bir taşımanın parçası olan bir kaynak grubunu silmeye çalıştığında Azure portal bir hata iletisi gösterilmektedir.

![Silmeye çalışan hata iletisini taşı](./media/move-resource-group-and-subscription/move-error-delete.png)

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

**Soru: tek bir işlemde kaç kaynak taşıyabilirim?**

Mümkün olduğunda, büyük taşıma işlemlerini ayrı olarak bölün. Tek bir işlemde 800 'den fazla kaynak olduğunda hemen Kaynak Yöneticisi bir hata döndürür. Ancak, 800 'den az kaynak taşıma zaman aşımına uğramasından de başarısız olabilir.

**Soru: bir kaynağın başarılı durumda olmadığı hatanın anlamı nedir?**

Kaynak başarılı bir durumda olmadığından, bir kaynağın taşınamayacağını belirten bir hata iletisi aldığınızda, gerçekten taşımayı engelleyen bir bağımlı kaynak olabilir. Genellikle, hata kodu **MoveCannotProceedWithResourcesNotInSucceededState**' dir.

Kaynak veya hedef kaynak grubu bir sanal ağ içeriyorsa, sanal ağ için tüm bağımlı kaynakların durumları taşıma sırasında denetlenir. Denetim, bu kaynakları doğrudan ve sanal ağa dolaylı olarak bağlı olarak içerir. Bu kaynaklardan herhangi biri başarısız durumdaysa taşıma engellenir. Örneğin, sanal ağ kullanan bir sanal makine başarısız olduysa, taşıma engellenir. Sanal makine, taşınmakta olan kaynaklardan biri olmadığında ve taşıma için kaynak gruplarından birinde yer alsa bile taşıma engellenir.

Bu hatayı aldığınızda, iki seçeneğiniz vardır. Kaynaklarınızı sanal ağı olmayan bir kaynak grubuna taşıyın veya [desteğe başvurun](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Sonraki adımlar

Hangi kaynakların taşınmasını desteklediğini bir liste için bkz. [kaynaklar Için taşıma işlemi desteği](move-support-resources.md).

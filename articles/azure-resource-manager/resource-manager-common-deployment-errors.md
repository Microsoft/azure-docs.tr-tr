---
title: Sık karşılaşılan dağıtım hatalarını giderme
description: Azure 'a Azure Resource Manager kullanarak kaynak dağıtırken yaygın hataların nasıl çözümleneceğini açıklar.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.openlocfilehash: 27f3b9db40e00ea0a00e50333fe86248906d8560
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150642"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Azure Resource Manager ile yaygın Azure dağıtım hatalarını giderme

Bu makalede bazı yaygın Azure dağıtım hataları açıklanmakta ve hatalar çözümlenme bilgileri sağlanmaktadır. Dağıtım hatası için hata kodu bulamazsanız bkz. [bulma hata kodu](#find-error-code).

Bir hata kodu hakkında bilgi arıyorsanız ve bu makalede bilgi sağlanmazsa bize bize izin verin. Bu sayfanın en altında geri bildirimde olabilirsiniz. Geri bildirim, GitHub sorunlarıyla izlenir.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="error-codes"></a>Hata kodları

| Hata kodu | Risk azaltma | Daha fazla bilgi |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Depolama hesapları için adlandırma kısıtlamalarını izleyin. | [Depolama hesabı adını çözümle](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | Kullanılabilir depolama hesabı özelliklerini denetleyin. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | Küme veya bölgenin kullanılabilir kaynakları yok veya istenen VM boyutunu destekleyemiyorum. İsteği daha sonra yeniden deneyin veya farklı bir VM boyutu isteyin. | Linux, [Windows için sağlama ve ayırma sorunları](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) [için sağlama ve ayırma sorunları](../virtual-machines/linux/troubleshoot-deployment-new-vm.md)ve [ayırma hatalarında sorun giderme](../virtual-machines/troubleshooting/allocation-failure.md)|
| Anotheroperationınprogress | Eşzamanlı işlemin tamamlanmasını bekleyin. | |
| AuthorizationFailed | Hesabınızın veya hizmet sorumlusunun dağıtımı tamamlaması için yeterli erişimi yok. Hesabınızın ait olduğu rolü ve dağıtım kapsamına erişimini denetleyin.<br><br>Gerekli bir kaynak sağlayıcısı kayıtlı olmadığında bu hatayı alabilirsiniz. | [Azure rol tabanlı Access Control](../role-based-access-control/role-assignments-portal.md)<br><br>[Kaydı çözümle](resource-manager-register-provider-errors.md) |
| Işlemindeki hatalı istek | Kaynak Yöneticisi tarafından beklenildiği eşleşmeyen dağıtım değerleri gönderdiniz. Sorun gidermeye yardımcı olması için iç durum iletisini kontrol edin. | [Şablon başvurusu](/azure/templates/) ve [Desteklenen konumlar](resource-location.md) |
| Çakışma | Kaynağın geçerli durumunda izin verilmeyen bir işlem isteğinde bulunuyoruz. Örneğin, yalnızca bir VM oluşturulurken veya VM serbest bırakıldığında disk yeniden boyutlandırılmasına izin verilir. | |
| Deploymentactiveanddüzenlenemeyen | Bu kaynak grubuna yönelik eşzamanlı dağıtımın tamamlanmasını bekleyin. | |
| DeploymentFailedCleanUp | ' I bir bütün modda dağıtırken, şablonda olmayan kaynaklar silinir. Şablonda olmayan tüm kaynakları silmek için yeterli izinlere sahip değilseniz bu hatayı alırsınız. Hatayı önlemek için Dağıtım modunu artımlı olarak değiştirin. | [Azure Resource Manager Dağıtım modları](deployment-modes.md) |
| Deploymentnameınvalidcharacters | Dağıtım adı yalnızca harf, rakam, '-', '. ' veya ' _ ' içerebilir. | |
| Deploymentnamelengthlimitexceıbaşında | Dağıtım adları 64 karakterle sınırlıdır.  | |
| DeploymentFailed | DeploymentFailed hatası, hatayı çözmeniz için gereken ayrıntıları sağlamayan genel bir hatadır. Daha fazla bilgi sağlayan bir hata kodu için hata ayrıntılarına bakın. | [Hata kodunu bul](#find-error-code) |
| DeploymentQuotaExceeded | Kaynak grubu başına 800 dağıtım sınırına ulaşırsanız, artık gerekli olmayan geçmişten dağıtımları silin. | [Dağıtım sayısı 800 ' i aştığında hatayı çözümle](deployment-quota-exceeded.md) |
| DnsRecordInUse | DNS kayıt adı benzersiz olmalıdır. Farklı bir ad girin. | |
| ImageNotFound | VM görüntüsü ayarlarını kontrol edin. |  |
| Inusesubnetcannotbedeleted | Bir kaynağı güncelleştirmeye çalışırken bu hatayı alabilir ve kaynak silinerek ve oluşturularak istek işlenir. Tüm değiştirilmeyen değerleri belirttiğinizden emin olun. | [Güncelleştirme kaynağı](/azure/architecture/building-blocks/extending-templates/update-resource) |
| Invalidauthenticationtokentenant | Uygun kiracı için erişim belirteci alın. Yalnızca hesabınızın ait olduğu kiracının belirtecini alabilir. | |
| InvalidContentLink | Büyük olasılıkla kullanılamayan iç içe bir şablona bağlantı yapmaya çalıştınız. İç içe geçmiş şablon için verdiğiniz URI 'yi iki kez kontrol edin. Şablon bir depolama hesabında varsa, URI 'nin erişilebilir olduğundan emin olun. Bir SAS belirteci geçirmeniz gerekebilir. Şu anda, [Azure Storage güvenlik duvarının](../storage/common/storage-network-security.md)arkasındaki bir depolama hesabında bulunan bir şablona bağlayamazsınız. Şablonunuzu GitHub gibi başka bir depoya taşımayı düşünün. | [Bağlantılı şablonlar](resource-group-linked-templates.md) |
| Invaliddeploymentlocation | Abonelik düzeyinde dağıtım yaparken, daha önce kullanılan bir dağıtım adı için farklı bir konum sağladınız. | [Abonelik düzeyinde dağıtımlar](deploy-to-subscription.md) |
| Geçersiz parametre | Bir kaynak için verdiğiniz değerlerden biri beklenen değerle eşleşmiyor. Bu hata, birçok farklı koşulun oluşmasına neden olabilir. Örneğin, bir parola yetersiz olabilir veya bir blob adı yanlış olabilir. Hata iletisi hangi değerin düzeltilmesi gerektiğini göstermelidir. | |
| Invalidrequestcontent | Dağıtım değerleri, tanınmayan ya da gerekli değerler eksik olan değerleri içerir. Kaynak türü için değerleri onaylayın. | [Şablon başvurusu](/azure/templates/) |
| Invalidrequestformat | Dağıtımı çalıştırırken hata ayıklama günlüğünü etkinleştirin ve isteğin içeriğini doğrulayın. | [Hata ayıklama günlüğü](#enable-debug-logging) |
| Invalidresourcenamespace | **Type** özelliğinde belirttiğiniz kaynak ad alanını denetleyin. | [Şablon başvurusu](/azure/templates/) |
| InvalidResourceReference | Kaynak henüz yok ya da yanlış Başvurulmuş. Bağımlılık eklemeniz gerekip gerekmediğini denetleyin. **Başvuru** işlevi kullanmanın senaryonuz için gereken parametreleri içerdiğini doğrulayın. | [Bağımlılıkları çözümle](resource-manager-not-found-errors.md) |
| Invalidresourcetype | **Tür** özelliğinde belirttiğiniz kaynak türünü denetleyin. | [Şablon başvurusu](/azure/templates/) |
| Invalidsubscriptionregistrationstate | Aboneliğinizi kaynak sağlayıcısına kaydedin. | [Kaydı çözümle](resource-manager-register-provider-errors.md) |
| InvalidTemplate | Hatalar için şablon sözdiziminizi denetleyin. | [Geçersiz şablonu çözümle](resource-manager-invalid-template-errors.md) |
| Invalidtemplateınvalidlardependency | Gereksiz bağımlılıkları kaldırın. | [Döngüsel bağımlılıkları çözümle](resource-manager-invalid-template-errors.md#circular-dependency) |
| LinkedAuthorizationFailed | Hesabınızın, dağıtmakta olduğunuz kaynak grubuyla aynı kiracıya ait olup olmadığını denetleyin. | |
| LinkedInvalidPropertyId | Bir kaynağın kaynak KIMLIĞI doğru çözümlenmiyor. Kaynak KIMLIĞI için abonelik KIMLIĞI, kaynak grubu adı, kaynak türü, üst kaynak adı (gerekirse) ve kaynak adı gibi tüm gerekli değerleri sağlayıp sağlamadıysanız emin olun. | |
| LocationRequired | Kaynak için bir konum belirtin. | [Konum ayarlama](resource-location.md) |
| Hatalı Matchingresourcesegments | İç içe geçmiş kaynağın ad ve tür bölümünde doğru sayıda parçaya sahip olduğundan emin olun. | [Kaynak kesimlerini çözümle](resource-manager-invalid-template-errors.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Kaynak sağlayıcısı kayıt durumunu ve desteklenen konumları denetleyin. | [Kaydı çözümle](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | Aboneliğinizi kaynak sağlayıcısına kaydedin. | [Kaydı çözümle](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | Kaynak sağlayıcısı kayıt durumunu denetleyin. | [Kaydı çözümle](resource-manager-register-provider-errors.md) |
| NotFound | Bağımlı bir kaynağı bir üst kaynakla paralel olarak dağıtmaya çalışıyor olabilirsiniz. Bir bağımlılık eklemeniz gerekiyorsa denetleyin. | [Bağımlılıkları çözümle](resource-manager-not-found-errors.md) |
| OperationNotAllowed | Dağıtım, aboneliğin, kaynak grubunun veya bölgenin kotasını aşan bir işlem gerçekleştirmeye çalışıyor. Mümkünse, Kotalarınızın kotalar dahilinde kalmasını sağlamak için dağıtımınızı gözden geçirin. Aksi takdirde, kotalarınızda değişiklik yapmayı deneyin. | [Kotaları çözümleyin](resource-manager-quota-errors.md) |
| ParentResourceNotFound | Alt kaynakları oluşturmadan önce bir üst kaynağın mevcut olduğundan emin olun. | [Üst kaynağı çözümle](resource-manager-parent-resource-errors.md) |
| PasswordTooLong | Çok fazla karakter içeren bir parola seçmiş olabilirsiniz veya parola değerini parametre olarak geçirmeden önce güvenli bir dizeye dönüştürülemeyebilirsiniz. Şablon bir **güvenli dize** parametresi içeriyorsa, değeri güvenli bir dizeye dönüştürmeniz gerekmez. Parola değerini metin olarak girin. |  |
| PrivateIPAddressInReservedRange | Belirtilen IP adresi, Azure için gereken bir adres aralığını içeriyor. Ayrılmış aralığın önüne geçmek için IP adresini değiştirin. | [IP adresleri](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | Belirtilen IP adresi alt ağ aralığının dışında. IP adresini alt ağ aralığı içinde olacak şekilde değiştirin. | [IP adresleri](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Dağıtılan bir kaynakta bazı özellikler değiştirilemez. Bir kaynağı güncelleştirirken, değişikliklerinizi izin verilen özelliklerle sınırlayın. | [Güncelleştirme kaynağı](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | Aboneliğiniz, dağıtım sırasında gerçekleştirmeye çalıştığınız bir eylemi önleyen bir kaynak ilkesi içerir. Eylemi engelleyen ilkeyi bulun. Mümkünse, ilkeden gelen sınırlamaları karşılamak için dağıtımınızı değiştirin. | [İlkeleri çözümle](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | Ayrılmış bir ad içermeyen bir kaynak adı belirtin. | [Ayrılmış kaynak adları](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Silmenin tamamlanmasını bekleyin. | |
| ResourceGroupNotFound | Dağıtım için hedef kaynak grubunun adını denetleyin. Hedef kaynak grubu aboneliğinizde zaten var olmalıdır. Abonelik bağlamınızı denetleyin. | [Azure CLI](/cli/azure/account?#az-account-set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | Dağıtımınız çözülemeyen bir kaynağa başvuruyor. **Başvuru** işlevi kullanmanın senaryonuz için gereken parametreleri içerdiğini doğrulayın. | [Başvuruları çözümle](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | Dağıtım, aboneliğin, kaynak grubunun veya bölgenin kotasını aşan kaynaklar oluşturmaya çalışıyor. Mümkünse, altyapılarınızın kotalar dahilinde kalacak şekilde gözden geçirin. Aksi takdirde, kotalarınızda değişiklik yapmayı deneyin. | [Kotaları çözümleyin](resource-manager-quota-errors.md) |
| SkuNotAvailable | Seçtiğiniz konum için kullanılabilir SKU (VM boyutu gibi) seçeneğini belirleyin. | [SKU 'YU çözümle](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists var | Depolama hesabına benzersiz bir ad verin. | [Depolama hesabı adını çözümle](resource-manager-storage-account-name-errors.md)  |
| Storageaccountalreadyçekildi | Depolama hesabına benzersiz bir ad verin. | [Depolama hesabı adını çözümle](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | Kullanmayı denediğiniz depolama hesabının aboneliğini, kaynak grubunu ve adını denetleyin. | |
| SubnetsNotInSameVnet | Bir sanal makine yalnızca bir sanal ağa sahip olabilir. Çeşitli NIC 'ler dağıtıldığında aynı sanal ağa ait olduklarından emin olun. | [Birden çok NIC](../virtual-machines/windows/multiple-nics.md) |
| Templateresourcecırculardependency | Gereksiz bağımlılıkları kaldırın. | [Döngüsel bağımlılıkları çözümle](resource-manager-invalid-template-errors.md#circular-dependency) |
| TooManyTargetResourceGroups | Tek bir dağıtım için kaynak gruplarının sayısını azaltın. | [Çapraz kaynak grubu dağıtımı](resource-manager-cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>Hata kodunu bul

İki tür hata alabilirsiniz:

* doğrulama hataları
* dağıtım hataları

Doğrulama hataları dağıtım öncesinde saptanabilen senaryolardan kaynaklanır. Bunlar şablonunuzdaki söz dizimi hataları veya abonelik kotalarınızı aşabilecek kaynak dağıtımı denemeleri olabilir. Dağıtım hataları, dağıtım işlemi sırasında oluşan koşullardan kaynaklanır. Bu paralel olarak dağıtılan bir kaynağa erişme denemesi olabilir.

Her iki tür hata da dağıtım sorunlarını gidermek için kullanabileceğiniz bir hata kodu döndürür. Her iki tür hata da [etkinlik günlüğünde](resource-group-audit.md) görüntülenir. Öte yandan doğrulama hataları dağıtım geçmişinizde görüntülenmez çünkü dağıtım hiç başlatılmamıştır.

### <a name="validation-errors"></a>Doğrulama hataları

Portal üzerinden dağıtım yaparken değerlerinizi gönderdikten sonra doğrulama hatasını görürsünüz.

![Portal doğrulama hatasını göster](./media/resource-manager-common-deployment-errors/validation-error.png)

Ayrıntıları görmek için hatayı seçin. Aşağıdaki görüntüde, bir **ınvalidtemplatedeployment** hatası ve ilke engellenen bir dağıtımı gösteren bir ileti görürsünüz.

![doğrulama ayrıntılarını göster](./media/resource-manager-common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Dağıtım hataları

İşlem doğrulamayı geçer ama dağıtım sırasında başarısız olursa dağıtım hatası alırsınız.

PowerShell'le dağıtım hata kodlarını ve iletileri görmek için şunu kullanın:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Azure CLI ile dağıtım hata kodlarını ve iletileri görmek için şunu kullanın:

```azurecli-interactive
az group deployment operation list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

Portalda bildirimi seçin.

![bildirim hatası](./media/resource-manager-common-deployment-errors/notification.png)

Dağıtım hakkında daha fazla ayrıntı görürsünüz. Hata hakkında daha fazla bilgi bulmak için seçeneği belirtin.

![dağıtım başarısız oldu](./media/resource-manager-common-deployment-errors/deployment-failed.png)

Hata iletisini ve hata kodlarını görürsünüz. İki hata kodu olduğuna dikkat edin. İlk hata kodu (**DeploymentFailed**), hataya çözmek için ihtiyacınız olan ayrıntıları sağlamayan genel bir hatadır. İkinci hata kodu (**StorageAccountNotFound**) ihtiyacınız olan ayrıntıları sağlar.

![hata ayrıntıları](./media/resource-manager-common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Hata ayıklama günlük kaydını etkinleştirme

Bazen sorunun ne olduğunu öğrenmek için istek ve yanıt hakkında daha fazla bilgiye ihtiyacınız vardır. Dağıtım sırasında, dağıtım sırasında ek bilgilerin günlüğe kaydedilmesini isteyebilirsiniz.

### <a name="powershell"></a>PowerShell

PowerShell 'de **Deploymentdebugloglevel** parametresini All, responsecontent veya requestcontent olarak ayarlayın.

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Aşağıdaki cmdlet ile istek içeriğini inceleyin:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

Ya da yanıt içeriği:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Bu bilgiler, şablondaki bir değerin yanlış şekilde ayarlanmış olup olmadığını belirlemenize yardımcı olabilir.

### <a name="azure-cli"></a>Azure CLI

Azure CLı Şu anda hata ayıklama günlük kaydını açmayı desteklemez, ancak hata ayıklama günlüğünü alabilirsiniz.

Aşağıdaki komutla dağıtım işlemlerini inceleyin:

```azurecli
az group deployment operation list \
  --resource-group examplegroup \
  --name exampledeployment
```

İstek içeriğini aşağıdaki komutla inceleyin:

```azurecli
az group deployment operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Yanıt içeriğini aşağıdaki komutla inceleyin:

```azurecli
az group deployment operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>İç içe geçmiş şablon

İç içe geçmiş bir şablon için hata ayıklama bilgilerini günlüğe kaydetmek için **Debugsetting** öğesini kullanın.

```json
{
    "apiVersion": "2016-09-01",
    "name": "nestedTemplate",
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "templateLink": {
            "uri": "{template-uri}",
            "contentVersion": "1.0.0.0"
        },
        "debugSetting": {
           "detailLevel": "requestContent, responseContent"
        }
    }
}
```

## <a name="create-a-troubleshooting-template"></a>Sorun giderme şablonu oluşturma

Bazı durumlarda, şablonunuzda sorun gidermenin en kolay yolu, bunun parçalarını test etmek için kullanılır. Hataya neden olduğunu düşündüğünüz bölüme odaklanmanızı sağlayan basitleştirilmiş bir şablon oluşturabilirsiniz. Örneğin, bir kaynağa başvururken bir hata aldığınızı varsayalım. Tüm bir şablon ile ilgilenmektense, sorununuza neden olabilecek bölümü döndüren bir şablon oluşturun. Doğru parametreleri kullanıp geçirdiğinizi, şablon işlevlerini doğru bir şekilde kullanmayı ve istediğiniz kaynağı almayı belirlemenize yardımcı olabilir.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

Ya da yanlış küme bağımlılıklarıyla ilgili olduğunu düşündüğünüz dağıtım hatalarını aldığınızı varsayalım. Uygulamanızı Basitleştirilmiş şablonlara ayırarak test edin. İlk olarak, yalnızca tek bir kaynağı dağıtan bir şablon oluşturun (SQL Server gibi). Bu kaynağı doğru bir şekilde tanımlamış olduğunuzdan emin olduğunuzda, kendisine bağımlı olan bir kaynak (SQL veritabanı gibi) ekleyin. Bu iki kaynağı doğru bir şekilde tanımladıysanız, diğer bağımlı kaynakları (denetim ilkeleri gibi) ekleyin. Her test dağıtımı arasında, bağımlılıkları yeterince test ettiğinizden emin olmak için kaynak grubunu silin.


## <a name="next-steps"></a>Sonraki adımlar

* Sorun giderme öğreticisini öğrenmek için bkz [. Öğretici: Kaynak Yöneticisi şablonu dağıtımlarının sorunlarını giderme](./resource-manager-tutorial-troubleshoot.md)
* Denetim eylemleri hakkında bilgi edinmek için bkz. [Kaynak Yöneticisi Ile denetim işlemleri](resource-group-audit.md).
* Dağıtım sırasında hataları belirleme eylemleri hakkında bilgi edinmek için bkz. [dağıtım Işlemlerini görüntüleme](resource-manager-deployment-operations.md).

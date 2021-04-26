---
title: Azure RBAC sorunlarını giderme
description: Azure rol tabanlı erişim denetimi (Azure RBAC) ile ilgili sorunları giderin.
services: azure-portal
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/06/2021
ms.author: rolyon
ms.custom: seohack1, devx-track-azurecli
ms.openlocfilehash: d816854c8d8a78931060c6e56fffbaee1fde5150
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771722"
---
# <a name="troubleshoot-azure-rbac"></a>Azure RBAC sorunlarını giderme

Bu makalede, Azure rol tabanlı erişim denetimi (Azure RBAC) ile ilgili bazı yaygın soruların yanıtları sağlanır ve bu sayede, rolleri kullanırken ne kadar beklediğinizi ve erişim sorunlarını giderebileceğinizi bilirsiniz.

## <a name="azure-role-assignments-limit"></a>Azure rol atama sınırı

Azure, abonelik başına en fazla **2000** rol atamasını destekler. Bu sınır, abonelik, kaynak grubu ve kaynak kapsamlarındaki rol atamalarını içerir. "Daha fazla rol ataması Oluşturuasız (Code: Roleatamaadı)" hata iletisini alırsanız, bir rol atamayı denediğinizde, abonelikteki rol atamalarının sayısını azaltmayı deneyin.

> [!NOTE]
> Abonelik başına **2000** rol atama sınırı sabittir ve artırılabilir.

Bu sınıra yakın bir değer alıyorsanız, rol atamalarının sayısını azaltabilmeniz için bazı yollar şunlardır:

- Gruplara kullanıcı ekleyin ve bunun yerine gruplara roller atayın. 
- Birden çok yerleşik rolü özel bir rolle birleştirin. 
- Abonelik veya yönetim grubu gibi daha yüksek bir kapsamda ortak rol atamaları yapın.
- Azure AD Premium P2 varsa, rol atamalarını kalıcı olarak atamak yerine [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) uygun hale getirin. 
- Ek bir abonelik ekleyin. 

Rol atamalarının sayısını almak için [grafiği Azure Portal erişim denetimi (IAM) sayfasında](role-assignments-list-portal.md#list-number-of-role-assignments) görüntüleyebilirsiniz. Aşağıdaki Azure PowerShell komutlarını da kullanabilirsiniz:

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Azure rol atamaları ile ilgili sorunlar

- Rol Ekle ataması **Ekle** seçeneği devre dışı olduğundan veya **erişim denetimi (IAM)** üzerindeki Azure Portal bir rol atamadıysanız  >   veya "nesne kimliği olan istemci, eylemi gerçekleştirmek için yetkilendirmeye sahip değil" hatasını alırsanız, `Microsoft.Authorization/roleAssignments/write` rolü atamaya çalıştığınız kapsamda [sahip](built-in-roles.md#owner) veya [Kullanıcı erişimi Yöneticisi](built-in-roles.md#user-access-administrator) gibi izne sahip bir rol atanmış kullanıcıyla oturum açtığınızdan emin olun.
- Rol atamak için bir hizmet sorumlusu kullanıyorsanız, "işlemi tamamlamaya yönelik ayrıcalıklar yetersiz" hatasını alabilirsiniz. Örneğin, sahip rolüne atanan bir hizmet sorumlusu olduğunu ve Azure CLı kullanarak hizmet sorumlusu olarak aşağıdaki rol atamasını oluşturmayı deneytiğinizi varsayalım:

    ```azurecli
    az login --service-principal --username "SPNid" --password "password" --tenant "tenantid"
    az role assignment create --assignee "userupn" --role "Contributor"  --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

    "İşlemi tamamlamaya yönelik ayrıcalıklar yetersiz" hatasını alırsanız Azure CLı, Azure AD 'de atanan kimliği aramaya çalışıyor ve hizmet sorumlusu varsayılan olarak Azure AD 'yi okuyamıyor olabilir.

    Bu hatayı çözebilecek iki yol vardır. İlk yöntem, Dizin [okuyucuları](../active-directory/roles/permissions-reference.md#directory-readers) rolünü, dizindeki verileri okuyabilmesi için hizmet sorumlusuna atamanız olur.

    Bu hatayı çözmek için ikinci yöntem, yerine parametresini kullanarak rol atamasını oluşturmaktır `--assignee-object-id` `--assignee` . Kullanarak `--assignee-object-id` Azure CLI, Azure AD aramasını atlar. Rolü atamak istediğiniz kullanıcı, Grup veya uygulamanın nesne KIMLIĞINI almanız gerekir. Daha fazla bilgi için bkz. [Azure CLI kullanarak Azure rolleri atama](role-assignments-cli.md#assign-a-role-for-a-new-service-principal-at-a-resource-group-scope).

    ```azurecli
    az role assignment create --assignee-object-id 11111111-1111-1111-1111-111111111111  --role "Contributor" --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

- Yeni bir hizmet sorumlusu oluşturur ve bu hizmet sorumlusuna hemen bir rol atamayı denerseniz, bu rol ataması bazı durumlarda başarısız olabilir.

    Bu senaryoya yönelik olarak, `principalType` rol atamasını oluştururken özelliğini olarak ayarlamanız gerekir `ServicePrincipal` . `apiVersion`Rol atamasının öğesini `2018-09-01-preview` veya daha sonra da ayarlamanız gerekir. Daha fazla bilgi için bkz [. Azure rollerini REST API kullanarak yeni bir hizmet sorumlusuna atama](role-assignments-rest.md#new-service-principal) veya [Azure Resource Manager şablonları kullanarak Azure rollerini yeni bir hizmet sorumlusuna atama](role-assignments-template.md#new-service-principal)

- Bir abonelik için son sahip rol atamasını kaldırmaya çalışırsanız, "son RBAC yönetici ataması silinemiyor." hatasını görebilirsiniz. Aboneliğin en son sahip rol atamasının kaldırılması, aboneliğin orphankaçınmak için desteklenmez. Aboneliğinizi iptal etmek istiyorsanız bkz. [Azure aboneliğinizi Iptal etme](../cost-management-billing/manage/cancel-azure-subscription.md).

## <a name="problems-with-custom-roles"></a>Özel rollerle ilgili sorunlar

- Özel rol oluşturma için adımlara ihtiyacınız varsa, [Azure Portal](custom-roles-portal.md), [Azure POWERSHELL](tutorial-custom-role-powershell.md)veya [Azure CLI](tutorial-custom-role-cli.md)kullanarak özel rol öğreticilerine bakın.
- Mevcut bir özel rolü güncelleştireerişemiyorsanız, `Microsoft.Authorization/roleDefinition/write` [sahip](built-in-roles.md#owner) veya [Kullanıcı erişimi Yöneticisi](built-in-roles.md#user-access-administrator)gibi izne sahip bir rol atanmış kullanıcıyla oturum açmış olup olmadığınızı kontrol edin.
- Özel rolü silemiyor ve "Role başvuran mevcut rol atamaları var (kod: RoleDefinitionHasAssignments)" hata iletisiyle karşılaşıyorsanız, özel rolü kullanan rol atamaları mevcuttur. Bu rol atamalarını kaldırın ve özel rolü silmeyi tekrar deneyin.
- Yeni bir özel rol oluşturmaya çalıştığınızda "Rol tanımı sınırı aşıldı. Başka rol tanımı oluşturulamaz (kod: Roledefinitionlimitexcelıo) "yeni bir özel rol oluşturmaya çalıştığınızda kullanılmayan tüm özel rolleri silin. Azure, bir dizinde en fazla **5000** özel rolü destekler. (Azure Almanya ve Azure Çin 21Vianet için sınır 2000 özel rollerdir.)
- "İstemcinin '/Subscriptions/{SubscriptionID} ' kapsamındaki ' Microsoft. Authorization/roleDefinitions/Write ' eylemini gerçekleştirme izni var, ancak bağlantılı abonelik bulunamadı" özel bir rolü güncelleştirmeye çalıştığınızda, bir veya daha fazla [atanabilir kapsamın](role-definitions.md#assignablescopes) dizinde silinip silinmediğini denetleyin. Kapsam silinmişse, şu anda bir self servis çözüm olmadığı için destek bileti oluşturun.

## <a name="custom-roles-and-management-groups"></a>Özel roller ve yönetim grupları

- Özel bir rol içinde yalnızca bir yönetim grubu tanımlayabilirsiniz `AssignableScopes` . ' Ye bir yönetim grubu eklemek `AssignableScopes` Şu anda önizlemededir.
- `DataActions`Yönetim grubu kapsamında özel roller atanamaz.
- Azure Resource Manager, rol tanımının atanabilir kapsamındaki yönetim grubunun varlığını doğrulamaz.
- Özel roller ve yönetim grupları hakkında daha fazla bilgi için bkz. [Azure Yönetim gruplarıyla kaynaklarınızı düzenleme](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment).

## <a name="transferring-a-subscription-to-a-different-directory"></a>Aboneliği farklı bir dizine aktarma

- Aboneliği farklı bir Azure AD dizinine aktarmaya yönelik adımlara ihtiyacınız varsa, bkz. [Azure aboneliğini farklı bir Azure AD dizinine aktarma](transfer-subscription.md).
- Bir aboneliği farklı bir Azure AD dizinine aktarırsanız, tüm rol atamaları Kaynak Azure AD dizininden **kalıcı olarak** silinir ve hedef Azure AD dizinine geçirilmez. Rol atamalarınızı hedef dizinde yeniden oluşturmanız gerekir. Ayrıca, Azure kaynakları için yönetilen kimlikleri el ile yeniden oluşturmanız gerekir. Daha fazla bilgi için bkz. [SSS ve yönetilen kimliklerle ilgili bilinen sorunlar](../active-directory/managed-identities-azure-resources/known-issues.md).
- Bir Azure AD Genel yöneticisiyseniz ve dizinler arasında aktarıldıktan sonra bir aboneliğe erişiminiz yoksa, aboneliğe erişim sağlamak için erişiminizi geçici olarak [yükseltmek](elevate-access-global-admin.md) üzere **Azure kaynakları için erişim yönetimi** ' ni kullanın.

## <a name="issues-with-service-admins-or-co-admins"></a>Hizmet yöneticileri veya ortak yöneticilerle ilgili sorunlar

- Hizmet Yöneticisi veya ortak yöneticilerle ilgili sorun yaşıyorsanız, bkz. [Azure abonelik yöneticileri](../cost-management-billing/manage/add-change-subscription-administrator.md) ve [Klasik abonelik yöneticisi rolleri, Azure ROLLERI ve Azure AD rolleri](rbac-and-directory-admin-roles.md)ekleme veya değiştirme.

## <a name="access-denied-or-permission-errors"></a>Erişim engellendi veya izin hataları

- "Nesne kimliğine sahip istemcinin kapsam üzerinde işlemi gerçekleştirme yetkisi yok (kod: AuthorizationFailed)" izin hatasını kaynak oluşturmaya çalıştığınızda alıyorsanız, seçilen kapsamda kaynak için yazma iznine sahip bir rolün atanmış olduğu kullanıcı hesabıyla oturum açmış olduğunuzdan emin olun. Örneğin bir kaynak grubundaki sanal makineleri yönetmek için kaynak grubunda (veya üst kapsamda) [Sanal Makine Katılımcısı](built-in-roles.md#virtual-machine-contributor) rolüne sahip olmanız gerekir. Yerleşik her rolün izinlerinin listesi için bkz. [Azure yerleşik rolleri](built-in-roles.md).
- "Destek talebi oluşturma izniniz yok" hatasını alırsanız bir destek bileti oluşturmayı veya güncelleştirmeyi denediğinizde şu anda oturum açmış olan bir kullanıcı ile oturum açtığınızdan emin olun `Microsoft.Support/supportTickets/write` , örneğin destek [isteği katılımcısı](built-in-roles.md#support-request-contributor)gibi bir rol atanmış olan

## <a name="move-resources-with-role-assignments"></a>Rol atamalarıyla kaynakları taşıma

Doğrudan kaynağa (veya bir alt kaynağa) atanmış bir Azure rolü olan bir kaynağı taşırsanız, rol ataması taşınmaz ve yalnız bırakılmış olur. Taşıma işleminden sonra, rol atamasını yeniden oluşturmanız gerekir. Sonuç olarak, yalnız bırakılmış rol ataması otomatik olarak kaldırılır, ancak kaynak taşınmadan önce rol atamasını kaldırmak en iyi uygulamadır.

Kaynakları taşıma hakkında daha fazla bilgi için bkz. [kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma](../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="role-assignments-with-identity-not-found"></a>Kimliği olan rol atamaları bulunamadı

Azure portal için rol atamaları listesinde, güvenlik sorumlusu 'nın (Kullanıcı, Grup, hizmet sorumlusu veya yönetilen kimlik) **Bilinmeyen** bir tür ile **kimlik bulunamadığı** için listelendiğini fark edebilirsiniz.

![Azure rol atamaları 'nda listelenen kimlik bulunamadı](./media/troubleshooting/unknown-security-principal.png)

Kimlik iki nedenden dolayı bulunamamıştır:

- Son zamanlarda bir rol ataması oluştururken Kullanıcı davet ettiniz
- Rol ataması olan bir güvenlik sorumlusunu sildiniz

Bir Kullanıcı bir rol ataması oluştururken yakın zamanda davet ediyorsanız, bu güvenlik sorumlusu bölge genelindeki çoğaltma işleminde hala olabilir. Bu durumda, birkaç dakika bekleyip rol atamaları listesini yenileyin.

Ancak, bu güvenlik sorumlusu son davet edilen bir Kullanıcı değilse, silinen bir güvenlik sorumlusu olabilir. Bir güvenlik sorumlusuna bir rol atarsanız ve daha sonra rol atamasını kaldırmadan bu güvenlik sorumlusunu silerseniz, güvenlik sorumlusu **kimlik bulunamadı** ve **Bilinmeyen** bir tür olarak listelenir.

Bu rol atamasını Azure PowerShell kullanarak listelüyor, boş `DisplayName` ve `ObjectType` **Bilinmeyen** olarak bir küme görebilirsiniz. Örneğin, [Get-Azroleatama](/powershell/module/az.resources/get-azroleassignment) , aşağıdaki çıktıya benzer bir rol ataması döndürür:

```
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

Benzer şekilde, bu rol atamasını Azure CLı kullanarak listelüyor olmanız halinde boş bir durum görebilirsiniz `principalName` . Örneğin, [az role atama listesi](/cli/azure/role/assignment#az_role_assignment_list) aşağıdaki çıktıya benzer bir rol ataması döndürür:

```
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

Güvenlik sorumlusunun silindiği bu rol atamalarından ayrılmayan bir sorun değildir. İsterseniz, diğer rol atamalarına benzer adımları kullanarak bu rol atamalarını kaldırabilirsiniz. Rol atamalarını kaldırma hakkında daha fazla bilgi için bkz. [Azure rol atamalarını kaldırma](role-assignments-remove.md).

PowerShell 'de, rol atamalarını nesne KIMLIĞI ve rol tanımı adı kullanarak kaldırmaya çalışırsanız ve parametreleriniz ile eşleşen birden fazla rol ataması varsa, şu hata iletisini alırsınız: "belirtilen bilgiler bir rol atamasıyla eşlenmiyor". Aşağıdaki çıktıda hata iletisi örneği gösterilmektedir:

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Bu hata iletisini alırsanız, veya parametrelerini de belirttiğinizden emin olun `-Scope` `-ResourceGroupName` .

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>Rol atama değişiklikleri algılanamadı

Azure Resource Manager bazen, performansı geliştirmek için yapılandırma ve verileri önbelleğe alır. Rolleri atarken veya rol atamalarını kaldırdığınızda, değişikliklerin etkili olması 30 dakika kadar sürebilir. Azure portal, Azure PowerShell veya Azure CLı kullanıyorsanız, oturum kapatarak ve oturum açarak rol atama yaptığınız değişiklikleri yenilemeye zorlayabilirsiniz. REST API çağrılarında rol ataması değişikliği yapıyorsanız, erişim belirtecinizi yenileyerek yenilemeye zorlayabilirsiniz.

Yönetim grubu kapsamında bir rol ataması ekler veya kaldırırsanız ve rol varsa `DataActions` , veri düzleminde erişim birkaç saat boyunca güncelleştirilmemiş olabilir. Bu yalnızca yönetim grubu kapsamı ve veri düzlemi için geçerlidir.

## <a name="web-app-features-that-require-write-access"></a>Yazma erişimi gerektiren Web uygulaması özellikleri

Bir kullanıcıya tek bir Web uygulamasına salt okuma erişimi verirseniz, beklememeniz gerekebilecek bazı özellikler devre dışı bırakılır. Aşağıdaki yönetim özellikleri bir Web uygulamasına **yazma** erişimi gerektirir (katkıda bulunan veya sahip) ve herhangi bir salt okuma senaryosunda kullanılamaz.

* Komutlar (başlatma, durdurma vb. gibi)
* Genel yapılandırma, ölçek ayarları, yedekleme ayarları ve izleme ayarları gibi ayarları değiştirme
* Uygulama ayarları ve bağlantı dizeleri gibi diğer gizli bilgilere ve yayımlama kimlik bilgilerine erişme
* Akış günlükleri
* Kaynak günlükleri yapılandırması
* Konsol (komut istemi)
* Etkin ve son dağıtımlar (yerel git sürekli dağıtımı için)
* Tahmini harcama
* Web testleri
* Sanal ağ (yalnızca bir sanal ağ daha önce yazma erişimi olan bir kullanıcı tarafından yapılandırılmışsa görünebilir).

Bu kutucukların herhangi birine erişemiyorsanız, yöneticinizden Web uygulamasına katkıda bulunan erişimi istemesi gerekir.

## <a name="web-app-resources-that-require-write-access"></a>Yazma erişimi gerektiren Web uygulaması kaynakları

Web uygulamaları, çok sayıda farklı kaynağın varlığı tarafından karmaşıktır. Birkaç Web sitesi içeren tipik bir kaynak grubu aşağıda verilmiştir:

![Web uygulaması kaynak grubu](./media/troubleshooting/website-resource-model.png)

Sonuç olarak, birine yalnızca Web uygulamasına erişim verirseniz, Azure portal web sitesi dikey penceresinde işlevselliğin çoğu devre dışıdır.

Bu öğeler, Web sitenize karşılık gelen **App Service planına** **yazma** erişimi gerektirir:  

* Web uygulamasının fiyatlandırma katmanını görüntüleme (ücretsiz veya standart)  
* Ölçeği yapılandırma (örnek sayısı, sanal makine boyutu, otomatik ölçeklendirme ayarları)  
* Kotalar (depolama, bant genişliği, CPU)  

Bu öğeler, Web sitenizi içeren tüm **kaynak grubuna** **yazma** erişimi gerektirir:  

* TLS/SSL sertifikaları ve bağlamaları (TLS/SSL sertifikaları aynı kaynak grubundaki ve coğrafi konumdaki siteler arasında paylaşılabilir)  
* Uyarı kuralları  
* Otomatik ölçeklendirme ayarları  
* Application Insights bileşenleri  
* Web testleri  

## <a name="virtual-machine-features-that-require-write-access"></a>Yazma erişimi gerektiren sanal makine özellikleri

Web uygulamalarına benzer şekilde, sanal makine dikey penceresindeki bazı özellikler sanal makineye veya kaynak grubundaki diğer kaynaklara yazma erişimi gerektirir.

Sanal makineler, etki alanı adları, sanal ağlar, depolama hesapları ve uyarı kuralları ile ilgilidir.

Bu öğeler, **sanal makineye** **yazma** erişimi gerektirir:

* Uç Noktalar  
* IP adresleri  
* Diskler  
* Uzantıları  

Bunlar, hem **sanal makineye** hem de **kaynak grubuna** (etki alanı adıyla birlikte) **yazma** erişimi gerektirir:  

* Kullanılabilirlik kümesi  
* Yük dengeli küme  
* Uyarı kuralları  

Bu kutucukların herhangi birine erişemiyorsanız, yöneticinizden kaynak grubuna katkıda bulunan erişimi isteyin.

## <a name="azure-functions-and-write-access"></a>Azure Işlevleri ve yazma erişimi

[Azure işlevlerinin](../azure-functions/functions-overview.md) bazı özellikleri yazma erişimi gerektirir. Örneğin, bir kullanıcıya [okuyucu](built-in-roles.md#reader) rolü atanırsa, işlevleri bir işlev uygulamasında görüntüleyemeyecektir. Portal görüntülenir **(erişim yok)**.

![İşlev uygulamalarına erişim yok](./media/troubleshooting/functionapps-noaccess.png)

Bir okuyucu, **platform özellikleri** sekmesine tıklayabilir ve ardından **Tüm ayarlar** ' a tıklayarak bir işlev uygulamasıyla ilgili bazı ayarları (bir Web uygulamasına benzer şekilde) görüntüleyebilir, ancak bu ayarlardan herhangi birini değiştiremezler. Bu özelliklere erişmek için [katkıda bulunan](built-in-roles.md#contributor) rolüne ihtiyacınız olacaktır.

## <a name="next-steps"></a>Sonraki adımlar

- [Konuk kullanıcılar için sorun giderme](role-assignments-external-users.md#troubleshoot)
- [Azure portal kullanarak Azure rolleri atama](role-assignments-portal.md)
- [Azure RBAC değişiklikleri için etkinlik günlüklerini görüntüleme](change-history-report.md)

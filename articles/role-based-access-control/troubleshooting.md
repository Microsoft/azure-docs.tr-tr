---
title: Azure kaynakları için RBAC sorunlarını giderme | Microsoft Docs
description: Azure kaynakları için rol tabanlı erişim denetimi (RBAC) ile ilgili sorunları giderin.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: e8a5b8b5794687f9e3b1707fda4cbe381e277317
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819776"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Azure kaynakları için RBAC sorunlarını giderme

Bu makalede, Azure kaynakları için rol tabanlı erişim denetimi (RBAC) hakkında sık sorulan sorular yanıtlanmaktadır ve bu sayede Azure portal roller kullanılırken ne beklendiğini bilmeniz ve erişim sorunlarını giderebiliriz.

## <a name="problems-with-rbac-role-assignments"></a>RBAC rol atamalarıyla ilgili sorunlar

- Azure portal**rol** **ataması ekleme seçeneği** devre dışı bırakıldığından veya izinleri aldığınız için **erişim denetimi 'nde (IAM  > )** bir rol ataması ekleyemezse veya "nesne kimliği olan istemci eylem gerçekleştirme yetkilendirmesi ", şu anda, rolü atamaya çalıştığınız kapsamda [sahip](built-in-roles.md#owner) veya [Kullanıcı erişimi Yöneticisi](built-in-roles.md#user-access-administrator) gibi `Microsoft.Authorization/roleAssignments/write` iznine sahip bir kullanıcı ile oturum açtığınızdan emin olun.
- "Daha fazla rol ataması oluşturuoluşturulamadığı (Code: Roleatamaadı)" hata iletisini alırsanız, rol atamayı denediğinizde, rol atamalarının sayısını azaltmayı deneyin. Azure, abonelik başına en fazla **2000** rol atamasını destekler. Bu rol atama sınırı düzeltildi ve artırılabilir.

## <a name="problems-with-custom-roles"></a>Özel rollerle ilgili sorunlar

- Özel rol oluşturma için adımlara ihtiyacınız varsa, [Azure PowerShell](tutorial-custom-role-powershell.md) veya [Azure CLI](tutorial-custom-role-cli.md)kullanarak özel rol öğreticilerine bakın.
- Mevcut bir özel rolü güncelleştireerişemiyorsanız, [sahip](built-in-roles.md#owner) veya [Kullanıcı erişimi Yöneticisi](built-in-roles.md#user-access-administrator)gibi `Microsoft.Authorization/roleDefinition/write` iznine sahip bir rol atanmış kullanıcıyla oturum açtığınızdan emin olun.
- Özel bir rolü silemeyseniz ve "role başvuran mevcut rol atamaları var (kod: Roledefinitionhasas,)" hata iletisini alırsanız, hala özel rolü kullanan rol atamaları vardır. Bu rol atamalarını kaldırın ve özel rolü silmeyi tekrar deneyin.
- Yeni bir özel rol oluşturmaya çalıştığınızda "Rol tanımı sınırı aşıldı. Başka rol tanımı oluşturulamaz (kod: Roledefinitionlimitexcelıo) "yeni bir özel rol oluşturmaya çalıştığınızda kullanılmayan tüm özel rolleri silin. Azure, bir kiracıda en fazla **5000** özel rolü destekler. (Azure Kamu, Azure Almanya ve Azure Çin 21Vianet gibi özel Bulutlar için 2000 özel rol sınırı vardır.)
- "İstemcinin '/Subscriptions/{SubscriptionID} ' kapsamındaki ' Microsoft. Authorization/roleDefinitions/Write ' eylemini gerçekleştirme izni var, ancak bağlantılı abonelik bulunamadı" özel bir rolü güncelleştirmeye çalıştığınızda, ' i işaretleyin. kiracıya bir veya daha fazla [atanabilir kapsam](role-definitions.md#assignablescopes) silinip silinmediğini belirtir. Kapsam silinmişse, şu anda bir self servis çözüm olmadığı için destek bileti oluşturun.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Abonelikler kiracılar arasında taşınırken RBAC koşullarını kurtarma

- Aboneliği farklı bir Azure AD kiracısına aktarmaya yönelik adımlara ihtiyacınız varsa, bkz. [Azure aboneliğinin sahipliğini başka bir hesaba aktarma](../billing/billing-subscription-transfer.md).
- Bir aboneliği farklı bir Azure AD kiracısına aktardığınızda tüm rol atamaları kaynak Azure AD kiracısından kalıcı olarak silinir ve hedef Azure AD kiracısına geçirilmez. Rol atamalarınızı hedef kiracıda yeniden oluşturmanız gerekir. Ayrıca, Azure kaynakları için yönetilen kimlikleri el ile yeniden oluşturmanız gerekir. Daha fazla bilgi için bkz. [SSS ve yönetilen kimliklerle ilgili bilinen sorunlar](../active-directory/managed-identities-azure-resources/known-issues.md).
- Bir Azure AD Genel yöneticisiyseniz ve kiracılar arasında taşındıktan sonra bir aboneliğe erişiminiz yoksa, aboneliğe erişim sağlamak için erişiminizi geçici olarak [yükseltmek](elevate-access-global-admin.md) üzere **Azure kaynakları için erişim yönetimi** ' ni kullanın.

## <a name="issues-with-service-admins-or-co-admins"></a>Hizmet yöneticileri veya ortak yöneticilerle ilgili sorunlar

- Hizmet Yöneticisi veya ortak yöneticilerle ilgili sorun yaşıyorsanız, bkz. [Azure abonelik yöneticileri](../billing/billing-add-change-azure-subscription-administrator.md) ve [Klasik abonelik Yöneticisi ROLLERI, Azure RBAC ROLLERI ve Azure AD yönetici rolleri](rbac-and-directory-admin-roles.md)ekleme veya değiştirme.

## <a name="access-denied-or-permission-errors"></a>Erişim engellendi veya izin hataları

- "Nesne kimliği olan istemci, kapsam üzerinde eylem gerçekleştirme yetkisine sahip değil (kod: AuthorizationFailed)" hatası alırsanız, bir kaynak oluşturmaya çalıştığınızda şu anda yazılan bir rolün atandığı kullanıcıyla oturum açtığınızdan emin olun. seçilen kapsamdaki kaynak için izin. Örneğin bir kaynak grubundaki sanal makineleri yönetmek için kaynak grubunda (veya üst kapsamda) [Sanal Makine Katılımcısı](built-in-roles.md#virtual-machine-contributor) rolüne sahip olmanız gerekir. Yerleşik rollerin izinlerinin yer aldığı liste için bkz. [Azure kaynakları için yerleşik roller](built-in-roles.md).
- "Destek talebi oluşturma izniniz yok" hatasını alırsanız bir destek bileti oluşturmaya veya güncelleştirmeye çalıştığınızda, şu anda `Microsoft.Support/supportTickets/write` iznine sahip bir rolün atandığı kullanıcıyla oturum açtığınızdan emin olun. gibi, [destek Isteği katılımcısı](built-in-roles.md#support-request-contributor)gibi.

## <a name="role-assignments-with-unknown-security-principal"></a>Bilinmeyen güvenlik sorumlusu olan rol atamaları

Rol atamalarınızı Azure PowerShell kullanarak listeünüzde boş bir `DisplayName` ve `ObjectType` bilinmeyen olarak ayarlanan atamaları görebilirsiniz. Örneğin, [Get-Azroleatama](/powershell/module/az.resources/get-azroleassignment) aşağıdakine benzer bir rol ataması döndürür:

```azurepowershell
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

Benzer şekilde, Azure CLı kullanarak rol atamalarınızı listelüünüzde boş bir `principalName` atamalar görebilirsiniz. Örneğin, [az role atama listesi](/cli/azure/role/assignment#az-role-assignment-list) aşağıdakilere benzer bir rol ataması döndürür:

```azurecli
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

Bu rol atamaları, bir güvenlik sorumlusu (Kullanıcı, Grup, hizmet sorumlusu veya yönetilen kimlik) için bir rol atadığınızda ve daha sonra bu güvenlik sorumlusunu sildikten sonra oluşur. Bu rol atamaları Azure portal gösterilmez ve bunlar ayrılmayan bir sorun değildir. Ancak isterseniz, bu rol atamalarını kaldırabilirsiniz.

Bu rol atamalarını kaldırmak için [Remove-Azroleatama](/powershell/module/az.resources/remove-azroleassignment) veya [az role atama Delete](/cli/azure/role/assignment#az-role-assignment-delete) komutlarını kullanın.

PowerShell 'de, rol atamalarını nesne KIMLIĞI ve rol tanımı adı kullanarak kaldırmaya çalışırsanız ve parametreleriniz ile eşleşen birden fazla rol ataması varsa, şu hata iletisini alırsınız: "belirtilen bilgiler bir rol atamasıyla eşlenmiyor". Aşağıda, hata iletisinin bir örneği gösterilmektedir:

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Bu hata iletisini alırsanız, `-Scope` veya `-ResourceGroupName` parametrelerini de belirttiğinizden emin olun.

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="rbac-changes-are-not-being-detected"></a>RBAC değişiklikleri algılanamadı

Azure Resource Manager bazen, performansı geliştirmek için yapılandırma ve verileri önbelleğe alır. Rol atamaları oluştururken veya silerken değişikliklerin etkili olması 30 dakika kadar sürebilir. Azure portal, Azure PowerShell veya Azure CLı kullanıyorsanız, oturum kapatarak ve oturum açarak rol atama yaptığınız değişiklikleri yenilemeye zorlayabilirsiniz. REST API çağrılarında rol ataması değişikliği yapıyorsanız, erişim belirtecinizi yenileyerek yenilemeye zorlayabilirsiniz.

## <a name="web-app-features-that-require-write-access"></a>Yazma erişimi gerektiren Web uygulaması özellikleri

Bir kullanıcıya tek bir Web uygulamasına salt okuma erişimi verirseniz, beklememeniz gerekebilecek bazı özellikler devre dışı bırakılır. Aşağıdaki yönetim özellikleri bir Web uygulamasına **yazma** erişimi gerektirir (katkıda bulunan veya sahip) ve herhangi bir salt okuma senaryosunda kullanılamaz.

* Komutlar (başlatma, durdurma vb. gibi)
* Genel yapılandırma, ölçek ayarları, yedekleme ayarları ve izleme ayarları gibi ayarları değiştirme
* Uygulama ayarları ve bağlantı dizeleri gibi diğer gizli bilgilere ve yayımlama kimlik bilgilerine erişme
* Akış günlükleri
* Tanılama günlükleri yapılandırması
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

* SSL sertifikaları ve bağlamaları (SSL sertifikaları aynı kaynak grubundaki ve coğrafi konumdaki siteler arasında paylaşılabilir)  
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
* Uzantılar  

Bunlar, hem **sanal makineye**hem de **kaynak grubuna** (etki alanı adıyla birlikte) **yazma** erişimi gerektirir:  

* Kullanılabilirlik kümesi  
* Yük dengeli küme  
* Uyarı kuralları  

Bu kutucukların herhangi birine erişemiyorsanız, yöneticinizden kaynak grubuna katkıda bulunan erişimi isteyin.

## <a name="azure-functions-and-write-access"></a>Azure Işlevleri ve yazma erişimi

[Azure işlevlerinin](../azure-functions/functions-overview.md) bazı özellikleri yazma erişimi gerektirir. Örneğin, bir kullanıcıya [okuyucu](built-in-roles.md#reader) rolü atanırsa, işlevleri bir işlev uygulamasında görüntüleyemeyecektir. Portal görüntülenir **(erişim yok)** .

![İşlev uygulamalarına erişim yok](./media/troubleshooting/functionapps-noaccess.png)

Bir okuyucu, **platform özellikleri** sekmesine tıklayabilir ve ardından **Tüm ayarlar** ' a tıklayarak bir işlev uygulamasıyla ilgili bazı ayarları (bir Web uygulamasına benzer şekilde) görüntüleyebilir, ancak bu ayarlardan herhangi birini değiştiremezler. Bu özelliklere erişmek için [katkıda bulunan](built-in-roles.md#contributor) rolüne ihtiyacınız olacaktır.

## <a name="next-steps"></a>Sonraki adımlar

- [Konuk kullanıcılar için sorun giderme](role-assignments-external-users.md#troubleshoot)
- [RBAC ve Azure portalını kullanarak Azure kaynaklarına erişimi yönetme](role-assignments-portal.md)
- [Azure kaynaklarında RBAC değişiklikleri için etkinlik günlüklerini görüntüleme](change-history-report.md)


---
title: Azure Otomasyonu farklı çalıştır hesabını yönetme
description: Bu makalede, farklı çalıştır hesabınızı PowerShell ile veya Azure portal nasıl yöneteceğiniz açıklanmaktadır.
services: automation
ms.subservice: ''
ms.date: 01/19/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e440a27c8f7778c800148feb5bec76ca5a48f4f5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833931"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Azure Otomasyonu farklı çalıştır hesabını yönetme

Azure Otomasyonu 'nda farklı çalıştır hesapları, Otomasyon Runbook 'larını ve diğer otomasyon özelliklerini kullanarak Azure Resource Manager veya Azure klasik dağıtım modelinde kaynakları yönetmeye yönelik kimlik doğrulaması sağlar. Bu makale, farklı çalıştır veya klasik farklı çalıştır hesabını yönetme hakkında rehberlik sağlar.

Azure Otomasyonu hesap kimlik doğrulaması ve işlem otomasyonu senaryolarıyla ilgili kılavuz hakkında daha fazla bilgi için bkz. [Otomasyon hesabı kimlik doğrulamasına genel bakış](automation-security-overview.md).

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Kendinden imzalı bir sertifikayı yenileme

Farklı Çalıştır hesabı için oluşturduğunuz otomatik olarak imzalanan sertifika, oluşturma tarihinden itibaren bir yıl sonra dolar. Farklı Çalıştır hesabınızın süresi dolmadan önce sertifikayı yenilemeniz gerekir. Bu süreyi, süresi dolmadan önce yenileyebilirsiniz.

Otomatik olarak imzalanan sertifikayı yenilediğinizde, kuyruğa alınan veya etkin olarak çalışan ve farklı çalıştır hesabıyla kimlik doğrulaması yapılan tüm runbook 'ların olumsuz şekilde etkilenmemesini sağlamak için geçerli geçerli sertifika tutulur. Sertifika, sona erme tarihine kadar geçerliliğini sürdürür.

>[!NOTE]
>Farklı çalıştır hesabının tehlikede olduğunu düşünüyorsanız, otomatik olarak imzalanan sertifikayı silip yeniden oluşturabilirsiniz.

>[!NOTE]
>Farklı Çalıştır hesabınızı, kuruluş sertifika yetkiliniz tarafından verilen bir sertifika kullanmak üzere yapılandırdıysanız ve otomatik olarak imzalanan bir sertifika seçeneğini yenileme seçeneğini kullanırsanız, kurumsal sertifika otomatik olarak imzalanan bir sertifika ile değiştirilmiştir.

Otomatik olarak imzalanan sertifikayı yenilemek için aşağıdaki adımları kullanın.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Otomasyon hesabınıza gidin ve hesap ayarları bölümünde **Farklı Çalıştır hesapları** ' nı seçin.

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="Otomasyon hesabı Özellikler bölmesi.":::

1. **Farklı Çalıştır hesapları** özellikleri sayfasında, sertifikayı yenilemeniz gereken hesaba bağlı olarak farklı **Çalıştır hesabını** veya **Klasik farklı çalıştır hesabını** seçin.

1. Seçilen hesabın **Özellikler** sayfasında, **Sertifikayı Yenile**' yi seçin.

    :::image type="content" source="media/manage-runas-account/automation-account-renew-runas-certificate.png" alt-text="Farklı Çalıştır hesabı için sertifikayı yenileyin.":::

1. Sertifika yenilenirken menünün **Bildirimler** öğesi altında ilerleme durumunu izleyebilirsiniz.

## <a name="grant-run-as-account-permissions-in-other-subscriptions"></a>Diğer aboneliklerde farklı çalıştır hesabı izinleri verme

Azure Otomasyonu bir abonelikteki tek bir Otomasyon hesabının kullanılmasını ve Runbook 'ların birden çok abonelikte Azure Resource Manager kaynaklara karşı yürütülmesini destekler. Bu yapılandırma, klasik Azure dağıtım modelini desteklemiyor.

Farklı Çalıştır hesabı hizmet sorumlusu [katılımcısı](../role-based-access-control/built-in-roles.md#contributor) rolünü diğer abonelikte veya daha kısıtlayıcı izinlerle atarsınız. Daha fazla bilgi için bkz. Azure Otomasyonu 'nda [rol tabanlı erişim denetimi](automation-role-based-access-control.md) . Farklı Çalıştır hesabını diğer abonelikteki role atamak için, bu görevi gerçekleştiren kullanıcı hesabının söz konusu abonelikte **sahip** rolünün bir üyesi olması gerekir.

> [!NOTE]
> Bu yapılandırma yalnızca ortak bir Azure AD kiracısı kullanarak bir kuruluşun birden çok aboneliğini destekler.

Farklı Çalıştır hesabı izinlerini vermeden önce, atanacak hizmet sorumlusunun görünen adına göz önüne almanız gerekir.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Otomasyon hesabınızdan **Hesap ayarları** altında **Farklı Çalıştır hesapları** ' nı seçin.
1. **Azure farklı çalıştır hesabı**' nı seçin.
1. **Azure farklı çalıştır hesabı** sayfasında **görünen ad** değerini kopyalayın veya bir yere göz önüne alın.

Rol atamaları ekleme hakkında ayrıntılı adımlar için, kullanmak istediğiniz yönteme bağlı olarak aşağıdaki makalelere göz atın.

* [Azure portal kullanarak Azure rolleri atama](../role-based-access-control/role-assignments-portal.md)
* [Azure PowerShell kullanarak Azure rolleri atama](../role-based-access-control/role-assignments-powershell.md)
* [Azure CLı kullanarak Azure rolleri atama](../role-based-access-control/role-assignments-cli.md)
* [REST API kullanarak Azure rolleri atama](..//role-based-access-control/role-assignments-rest.md)

Farklı Çalıştır hesabını role atadıktan sonra, runbook 'ünüzde `Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"` abonelik bağlamını kullanılacak şekilde ayarlamayı belirleyin. Daha fazla bilgi için bkz. [set-AzContext](/powershell/module/az.accounts/set-azcontext).

## <a name="limit-run-as-account-permissions"></a>Farklı Çalıştır hesabı izinlerini sınırla

Azure 'daki kaynaklara karşı Otomasyon hedeflemesini denetlemek için [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) betiğini çalıştırabilirsiniz. Bu betik, özel bir rol tanımı oluşturmak ve kullanmak için mevcut farklı çalıştır hesabı hizmet sorumlunuzu değiştirir. Rolün [Key Vault](../key-vault/index.yml)hariç tüm kaynaklar için izinleri vardır.

>[!IMPORTANT]
>**Update-AutomationRunAsAccountRoleAssignments.ps1** betiğini çalıştırdıktan sonra, Farklı Çalıştır hesaplarının kullanımı üzerinden Key Vault erişen runbook 'lar artık çalışmaz. Betiği çalıştırmadan önce, Azure Key Vault çağrılar için hesabınızdaki runbook 'ları gözden geçirmeniz gerekir. Azure Otomasyonu runbook 'lardan Key Vault erişimi etkinleştirmek için, [Farklı Çalıştır hesabını Key Vault izinlerine eklemeniz](#add-permissions-to-key-vault)gerekir.

Farklı Çalıştır hizmet sorumlusunun neler yapabileceğini daha fazla kısıtlamanız gerekiyorsa, `NotActions` özel rol tanımının öğesine diğer kaynak türleri ekleyebilirsiniz. Aşağıdaki örnek, erişimini kısıtlar `Microsoft.Compute/*` . Bu kaynak türünü `NotActions` rol tanımı için öğesine eklerseniz, rol herhangi bir işlem kaynağına erişemez. Rol tanımları hakkında daha fazla bilgi edinmek için bkz. [Azure kaynakları için rol tanımlarını anlama](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Farklı Çalıştır hesabınız tarafından kullanılan hizmet sorumlusunun **katkıda** bulunan rolünü mi yoksa özel bir tane mi atandığını belirleyebilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Otomasyon hesabınıza gidin ve hesap ayarları bölümünde **Farklı Çalıştır hesapları** ' nı seçin.
1. **Azure farklı çalıştır hesabı**' nı seçin.
1. Kullanılmakta olan rol tanımını bulmak için **rol** ' i seçin.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Farklı Çalıştır hesabı rolünü doğrulayın." lightbox="media/manage-runas-account/verify-role-expanded.png":::

Farklı Çalıştır hesapları tarafından birden çok abonelik veya Otomasyon hesabı için kullanılan rol tanımını da belirleyebilirsiniz. Bunu PowerShell Galerisi [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) betiği kullanarak yapın.

### <a name="add-permissions-to-key-vault"></a>Key Vault izinler ekleme

Key Vault ve farklı çalıştır hesabı hizmet sorumlunun özel bir rol tanımı kullandığını doğrulamak için Azure Otomasyonu 'na izin verebilirsiniz. Şunları yapmanız gerekir:

* Key Vault izin verin.
* Erişim ilkesini ayarlayın.

Farklı Çalıştır hesabı izinlerini Key Vault vermek için PowerShell Galerisi [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) betiği kullanabilirsiniz. Key Vault izinlerini ayarlama hakkında daha fazla bilgi için bkz. [Key Vault erişim Ilkesi atama](../key-vault/general/assign-access-policy-powershell.md) .

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Farklı Çalıştır hesapları için yanlış yapılandırma sorunlarını çözün

Farklı Çalıştır veya klasik farklı çalıştır hesabı için gerekli bazı yapılandırma öğeleri, ilk kurulum sırasında silinmiş veya hatalı oluşturulmuş olabilir. Hatalı yanlış yapılandırma örnekleri şunları içerir:

* Sertifika varlığı
* Bağlantı varlığı
* Farklı Çalıştır hesabı, katkıda bulunan rolünden kaldırıldı
* Azure AD'de hizmet sorumlusu veya uygulama

Bu tür yanlış yapılandırma örnekleri için, Otomasyon hesabı değişiklikleri algılar ve hesabın farklı çalıştır hesapları Özellikler bölmesinde *tamamlanmamış* durumunu görüntüler.

:::image type="content" source="media/manage-runas-account/automation-account-runas-config-incomplete.png" alt-text="Tamamlanmamış farklı çalıştır hesabı yapılandırması.":::

Farklı Çalıştır hesabını seçtiğinizde, hesap özellikleri bölmesinde aşağıdaki hata iletisi görüntülenir:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Farklı Çalıştır hesabını [silip](delete-run-as-account.md) [yeniden oluşturarak](create-run-as-account.md) bu farklı çalıştır hesabı sorunlarını hızlı bir şekilde çözebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Uygulama nesneleri ve hizmet sorumlusu nesneleri](../active-directory/develop/app-objects-and-service-principals.md).
* [Azure Cloud Services sertifikalara genel bakış](../cloud-services/cloud-services-certs-create.md).
* Farklı Çalıştır hesabı oluşturmak veya yeniden oluşturmak için, bkz. [Farklı Çalıştır hesabı oluşturma](create-run-as-account.md).
* Farklı Çalıştır hesabı kullanmanıza gerek kalmadığında, bkz. [Farklı Çalıştır hesabını silme](delete-run-as-account.md).

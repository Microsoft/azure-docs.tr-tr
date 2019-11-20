---
title: Azure kaynakları için yönetilen kimliklerle ilgili SSS ve bilinen sorunlar
description: Azure kaynakları için yönetilen kimliklerle ilgili bilinen sorunlar.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d882b34bc4f057035a16b7916249cfe8f0b8d0b
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983426"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Azure kaynakları için yönetilen kimliklerle ilgili SSS ve bilinen sorunlar

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Sık Sorulan Sorular (SSS)

> [!NOTE]
> Azure kaynakları için yönetilen kimlikler, Yönetilen Hizmet Kimliği (MSI) olarak bilinen hizmetin yeni adıdır.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Azure kaynakları için Yönetilen kimlikler Azure Cloud Services ile çalışır mi?

Hayır, Azure Cloud Services Azure kaynakları için yönetilen kimlikleri desteklemeye yönelik bir plan yoktur.

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Azure kaynakları için Yönetilen kimlikler Active Directory Authentication Library (ADAL) veya Microsoft kimlik doğrulama kitaplığı (MSAL) ile çalışır mı?

Hayır, Azure kaynakları için Yönetilen kimlikler henüz ADAL veya MSAL ile tümleştirmemiştir. REST uç noktasını kullanarak Azure kaynakları için Yönetilen kimlikler için bir belirteç alma hakkında daha fazla bilgi için bkz. bir [Azure VM 'de Azure kaynakları için Yönetilen kimlikler kullanma, erişim belirteci alma](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Azure kaynakları için yönetilen kimliklerin güvenlik sınırı nedir?

Kimliğin güvenlik sınırı, eklendiği kaynaktır. Örneğin, Azure kaynakları için yönetilen kimliklere sahip bir sanal makine için güvenlik sınırı, sanal makinedir. Bu VM üzerinde çalışan herhangi bir kod, Azure kaynakları uç noktası ve istek belirteçleri için yönetilen kimlikleri çağırabilir. Azure kaynakları için yönetilen kimlikleri destekleyen diğer kaynaklarla benzer deneyimdir.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>İstekte kimliği belirtmezseniz hangi kimlik varsayılan olarak silinecek?

- Sistem tarafından atanan yönetilen kimlik etkinse ve istekte hiçbir kimlik belirtilmemişse, ıDS, sistem tarafından atanan yönetilen kimliğe varsayılan olarak ayarlanır.
- Sistem tarafından atanan yönetilen kimlik etkin değilse ve yalnızca bir Kullanıcı yönetilen kimlik varsa, ıSE 'ler varsayılan olarak bu tek kullanıcı tarafından atanan yönetilen kimlik olur. 
- Sistem tarafından atanan yönetilen kimlik etkinleştirilmemişse ve birden çok kullanıcı atanmış Yönetilen kimlikler varsa, istekte yönetilen bir kimlik belirtilmesi gerekir.

### <a name="should-i-use-the-managed-identities-for-azure-resources-imds-endpoint-or-the-vm-extension-endpoint"></a>Azure kaynakları ıMDS uç noktası veya VM Uzantısı uç noktası için yönetilen kimlikleri mi kullanmalıyım?

VM 'Ler ile Azure kaynakları için Yönetilen kimlikler kullanılırken, ıMDS uç noktasını kullanmanızı öneririz. Azure Instance Metadata Service, Azure Resource Manager kullanılarak oluşturulan tüm IaaS VM 'lerine erişilebilen bir REST uç noktasıdır. 

IDS üzerinden Azure kaynakları için Yönetilen kimlikler kullanmanın avantajlarından bazıları şunlardır:
- Tüm Azure IaaS desteklenen işletim sistemleri, Azure kaynakları için ıMDS üzerinden yönetilen kimlikler kullanabilir.
- Azure kaynakları için yönetilen kimlikleri etkinleştirmek üzere artık sanal makinenize uzantı yüklemeye gerek yoktur. 
- Azure kaynakları için Yönetilen kimlikler tarafından kullanılan sertifikalar artık VM 'de yok.
- IDS uç noktası, yalnızca VM içinden kullanılabilen, iyi bilinen, yönlendirilemeyen bir IP adresidir.
- 1000 Kullanıcı tarafından atanan Yönetilen kimlikler tek bir sanal makineye atanabilir. 

Azure kaynakları için Yönetilen kimlikler sanal makine uzantısı hala kullanılabilir; Bununla birlikte, artık üzerinde yeni işlevsellik geliştiriyoruz. IMDS uç noktasını kullanmak için geçiş yapmanızı öneririz. 

VM Uzantısı uç noktasını kullanmanın sınırlamalarından bazıları şunlardır:
- Linux dağıtımları için sınırlı destek: CoreOS Stable, CentOS 7,1, Red hat 7,2, Ubuntu 15,04, Ubuntu 16,04
- VM 'ye yalnızca Kullanıcı tarafından atanan yönetilen kimliklerin atanabileceği 32.


Not: Azure kaynakları için Yönetilen kimlikler VM uzantısı, Ocak 2019 ' de destek dışı olacaktır. 

Azure Instance Metadata Service hakkında daha fazla bilgi için bkz. [IMDS belgeleri](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Bir aboneliği başka bir dizine taşıdığımda, Yönetilen kimlikler otomatik olarak yeniden oluşturulur mi?

Hayır. Bir aboneliği başka bir dizine taşırsanız, bunları el ile yeniden oluşturmanız ve Azure RBAC rol atamalarını yeniden sağlamanız gerekir.
- Sistem tarafından atanan Yönetilen kimlikler için: devre dışı bırakın ve yeniden etkinleştirin. 
- Kullanıcı tarafından atanan Yönetilen kimlikler için: silin, yeniden oluşturun ve bunları gereken kaynaklara yeniden ekleyin (örneğin, sanal makineler)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Farklı bir dizin/Kiracıdaki bir kaynağa erişmek için yönetilen bir kimlik kullanabilir miyim?

Hayır. Yönetilen kimlikler Şu anda çapraz dizin senaryolarını desteklemez. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Bir kaynaktaki yönetilen kimlik için hangi Azure RBAC izinleri gerekir? 

- Sistem tarafından atanan yönetilen kimlik: Kaynak üzerinde yazma izinlerine sahip olmanız gerekir. Örneğin, sanal makineler için Microsoft. COMPUTE/virtualMachines/Write gerekir. Bu eylem, [sanal makine katılımcısı](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)gibi kaynağa özgü yerleşik rollere dahildir.
- Kullanıcı tarafından atanan yönetilen kimlik: Kaynak üzerinde yazma izinlerine sahip olmanız gerekir. Örneğin, sanal makineler için Microsoft. COMPUTE/virtualMachines/Write gerekir. Yönetilen kimliğe göre [yönetilen kimlik operatörü](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-operator) rolü atamaya ek olarak.

### <a name="how-do-you-restart-the-managed-identities-for-azure-resources-extension"></a>Azure kaynakları uzantısı için Yönetilen kimlikler nasıl yeniden başlatılır?
Windows ve Linux 'un belirli sürümlerinde, uzantı durdurulduğunda aşağıdaki cmdlet 'i el ile yeniden başlatmak için kullanılabilir:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Konumlar: 
- Windows için uzantı adı ve tür: Managedıdentityextensionforwindows
- Linux için uzantı adı ve türü: Managedıdentityextensionforlinux

## <a name="known-issues"></a>Bilinen sorunlar

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Otomasyon betiği", Azure kaynakları uzantısı için Yönetilen kimlikler için şema dışarı aktarma denemesi sırasında başarısız oluyor

Bir VM 'de Azure kaynakları için Yönetilen kimlikler etkinleştirildiğinde, VM için "Otomasyon betiği" özelliğini veya kaynak grubunu kullanmaya çalışırken aşağıdaki hata gösterilir:

![Azure kaynakları için Yönetilen kimlikler Otomasyon betiği dışarı aktarma hatası](./media/msi-known-issues/automation-script-export-error.png)

Azure kaynakları için Yönetilen kimlikler VM Uzantısı (2019 Ocak 'ta kullanımdan kaldırma için planlanmış) Şu anda, şemasını bir kaynak grubu şablonuna aktarma özelliğini desteklememektedir. Sonuç olarak, oluşturulan şablon kaynaktaki Azure kaynakları için yönetilen kimlikleri etkinleştirmek üzere yapılandırma parametrelerini göstermez. Bu bölümler, [bir Azure VM 'de bir şablonları kullanarak Azure kaynakları için yönetilen kimlikleri yapılandırma](qs-configure-template-windows-vm.md)içindeki örnekleri izleyerek el ile eklenebilir.

Şema dışa aktarma işlevselliği Azure kaynakları için Yönetilen kimlikler için kullanılabilir hale geldiğinde (Ocak 2019 ' de kullanımdan kaldırma için planlanmış), [VM uzantıları Içeren kaynak gruplarını dışarı aktarma](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)bölümünde listelenecektir.

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>VM, kaynak grubu veya abonelikten taşındıktan sonra başlatılamaz

Bir VM 'yi çalışır durumda taşırsanız, taşıma sırasında çalışmaya devam eder. Ancak, taşıma işleminden sonra VM durdurulur ve yeniden başlatılırsa başlatılamaz. Bu sorun, VM 'nin Azure kaynakları kimliği için yönetilen kimliklere yönelik başvuruyu güncelleştirmediği ve eski kaynak grubunda ona işaret etmeye devam ettiğinden meydana gelir.

**Geçici çözüm** 
 
Azure kaynakları için yönetilen kimliklerin doğru değerlerini kullanabilmesi için VM 'de bir güncelleştirme tetikleyin. Azure kaynak kimliği için yönetilen kimliklerin başvurusunu güncelleştirmek üzere bir VM özelliği değişikliği yapabilirsiniz. Örneğin, aşağıdaki komutla sanal makinede yeni bir etiket değeri ayarlayabilirsiniz:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Bu komut, VM 'de 1 değeri ile yeni bir "fixVM" etiketi ayarlar. 
 
Bu özelliği ayarlayarak, VM Azure kaynakları Kaynak URI 'SI için doğru yönetilen kimliklerle güncelleştirilir ve sonra VM 'yi başlatabilmelisiniz. 
 
VM başlatıldıktan sonra, etiket aşağıdaki komut kullanılarak kaldırılabilir:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="vm-extension-provisioning-fails"></a>VM Uzantısı sağlama başarısız

VM uzantısının sağlanması DNS arama hatalarından dolayı başarısız olabilir. VM 'yi yeniden başlatın ve yeniden deneyin.
 
> [!NOTE]
> VM uzantısı, Ocak 2019 tarihine kadar kullanımdan kalkmaya planlanmaktadır. IMDS uç noktasını kullanarak geçiş yapmanızı öneririz.

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Azure AD dizinleri arasında abonelik aktarma

Yönetilen kimlikler bir abonelik taşındığında/başka bir dizine aktarıldığında güncellenmez. Sonuç olarak, var olan sistem tarafından atanan veya Kullanıcı tarafından atanan Yönetilen kimlikler bozulur. 

Başka bir dizine taşınmış bir abonelikte Yönetilen kimlikler için geçici çözüm:

 - Sistem tarafından atanan Yönetilen kimlikler için: devre dışı bırakın ve yeniden etkinleştirin. 
 - Kullanıcı tarafından atanan Yönetilen kimlikler için: silin, yeniden oluşturun ve bunları gereken kaynaklara yeniden ekleyin (örneğin, sanal makineler)

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Kullanıcı tarafından atanan yönetilen bir kimliği farklı bir kaynak grubuna/aboneliğe taşıma

Kullanıcı tarafından atanan yönetilen bir kimliği farklı bir kaynak grubuna taşımak kimliğin kesilmesine neden olur. Sonuç olarak, bu kimliği kullanan kaynaklar (örn. VM) bunun için belirteç isteyemeyecektir. 

---
title: Azure Otomasyonu hesabı kimlik doğrulamasına genel bakış
description: Bu makalede, Azure Otomasyonu hesabı kimlik doğrulamasına genel bakış sunulmaktadır.
keywords: otomasyon güvenliği, güvenli otomasyon; otomasyon kimlik doğrulaması
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 546407ce7286cebc04d3c86422f6242051d1dbf3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830439"
---
# <a name="azure-automation-account-authentication-overview"></a>Azure Otomasyonu hesabı kimlik doğrulamasına genel bakış

Azure Automation, Azure’deki, şirket içindeki kaynaklara karşı ve Amazon Web Hizmetleri (AWS) gibi diğer bulut sağlayıcılarıyla görevleri otomatikleştirmenizi sağlar. Azure dışında yönetilecek iş veya işlemsel süreçleriniz varsa, görevlerinizi otomatikleştirmek için Runbook 'ları veya karma runbook çalışanı kullanabilirsiniz. Bu ortamların herhangi birinde çalışmak, gerekli en düşük haklara sahip kaynaklara güvenli bir şekilde erişmek için izinler gerektirir.

Bu makalede, Azure Otomasyonu tarafından desteklenen kimlik doğrulama senaryoları ele alınmaktadır ve yönetmeniz gereken ortam veya ortamlara göre nasıl çalışmaya başladığına değinirsiniz.

## <a name="automation-account"></a>Otomasyon hesabı

Azure Automation’u ilk kez başlattığınızda, en az bir Automation hesabı oluşturmanız gerekir. Otomasyon hesapları, diğer hesapların kaynaklarından Otomasyon kaynaklarınızı, runbook 'larını, varlıklarınızı ve konfigürasyonları yalıtmanızı sağlar. Kaynakları ayrı mantıksal ortamlara veya Temsilcili sorumluluklara ayırmak için Otomasyon hesaplarını kullanabilirsiniz. Örneğin, geliştirme için bir hesap, üretim için başka bir hesap ve şirket içi ortamınız için de başka bir hesap kullanabilirsiniz. Ya da bir Otomasyon hesabını [güncelleştirme yönetimi](update-management/overview.md)tüm makinelerinizde işletim sistemi güncelleştirmelerini yönetmek için ayırabilirsiniz. 

Azure Automation hesabı, Azure aboneliğinizde oluşturduğunuz Microsoft hesabı veya hesaplarından farklıdır. Otomasyon hesabı oluşturmaya giriş için bkz. [Otomasyon hesabı oluşturma](automation-quickstart-create-account.md).

## <a name="automation-resources"></a>Otomasyon kaynakları

Her Otomasyon hesabının Otomasyon kaynakları tek bir Azure bölgesiyle ilişkilendirilir, ancak hesap Azure aboneliğinizdeki tüm kaynakları yönetebilir. Farklı bölgelerde Otomasyon hesapları oluşturmanın temel nedeni, verilerin ve kaynakların belirli bir bölgeye yalıtılması gereken ilkelerinize sahiptir.

Azure Automation 'daki Azure Resource Manager ve PowerShell cmdlet 'lerini kullanarak kaynaklarda oluşturduğunuz tüm görevlerin, Azure Active Directory (Azure AD) kuruluş kimliği kimlik bilgisi tabanlı kimlik doğrulaması kullanarak Azure 'da kimlik doğrulaması yapması gerekir.

## <a name="managed-identities-preview"></a>Yönetilen kimlikler (Önizleme)

Azure Active Directory yönetilen bir kimlik (Azure AD), runbook 'un diğer Azure AD korumalı kaynaklara kolayca erişmesini sağlar. Kimlik, Azure platformu tarafından yönetilir ve herhangi bir gizli dizi sağlamanızı veya döndürmenizi gerektirmez. Azure AD 'de Yönetilen kimlikler hakkında daha fazla bilgi için bkz. [Azure kaynakları Için Yönetilen kimlikler](/azure/active-directory/managed-identities-azure-resources/overview).

Yönetilen kimlikler kullanmanın avantajlarından bazıları şunlardır:

- Azure AD kimlik doğrulamasını destekleyen herhangi bir Azure hizmetinde kimlik doğrulaması yapmak için Yönetilen kimlikler 'i kullanabilirsiniz. Bunlar, karma işler ve bulut için de kullanılabilir. Karma işler, Azure veya Azure dışı VM 'de çalışan bir karma runbook çalışanı üzerinde çalıştırıldığında yönetilen kimlikleri kullanabilir.

- Yönetilen kimlikler herhangi bir ek maliyet olmadan kullanılabilir.

- Otomasyon farklı çalıştır hesabı tarafından kullanılan sertifikayı yenilemeniz gerekmez.

- Runbook kodunuzda farklı çalıştır bağlantı nesnesini belirtmeniz gerekmez. Bir runbook 'tan Otomasyon hesabınızın yönetilen kimliğini kullanarak, sertifikalar, bağlantılar, farklı çalıştır hesapları vb. kaynaklara erişebilirsiniz.

Otomasyon hesabına iki tür kimlik verilebilir:

- Sistem tarafından atanan bir kimlik uygulamanıza bağlanır ve uygulamanız silinirse silinir. Uygulamanın yalnızca bir sistem tarafından atanmış kimliği olabilir.

- Kullanıcı tarafından atanan bir kimlik, uygulamanıza atanabilecek tek başına bir Azure kaynağıdır. Bir uygulamada birden çok kullanıcı tarafından atanan kimlik olabilir.

>[!NOTE]
> Kullanıcı tarafından atanan kimlikler henüz desteklenmiyor.

Yönetilen kimlikleri kullanma hakkında ayrıntılı bilgi için bkz. [Azure Otomasyonu için yönetilen kimliği etkinleştirme (Önizleme)](enable-managed-identity-for-automation.md).

## <a name="run-as-accounts"></a>Farklı Çalıştır hesapları

Azure Otomasyonu 'nda farklı çalıştır hesapları, klasik dağıtım modelinde dağıtılan Azure Resource Manager kaynaklarını veya kaynaklarını yönetmek için kimlik doğrulaması sağlar. Azure Otomasyonu 'nda iki farklı çalıştır hesabı türü vardır:

* Azure farklı çalıştır hesabı: Azure kaynaklarını Azure için Azure Resource Manager dağıtım ve Yönetim hizmetine göre yönetmenizi sağlar.
* Azure klasik farklı çalıştır hesabı: klasik dağıtım modeline göre klasik Azure kaynaklarını yönetmenizi sağlar.

Azure Resource Manager ve klasik dağıtım modelleri hakkında daha fazla bilgi edinmek için, bkz. [Kaynak Yöneticisi ve klasik dağıtım](../azure-resource-manager/management/deployment-models.md).

>[!NOTE]
>Azure bulut çözümü sağlayıcısı (CSP) abonelikleri yalnızca Azure Resource Manager modelini destekler. Azure Resource Manager olmayan hizmetler programda yok. CSP aboneliği kullanırken, Azure klasik farklı çalıştır hesabı oluşturulmaz, ancak Azure farklı çalıştır hesabı oluşturulur. CSP abonelikleri hakkında daha fazla bilgi edinmek için bkz. [CSP aboneliklerinde kullanılabilir hizmetler](/azure/cloud-solution-provider/overview/azure-csp-available-services).

Bir Otomasyon hesabı oluşturduğunuzda, farklı çalıştır hesabı varsayılan olarak aynı anda oluşturulur. Otomasyon hesabı ile birlikte oluşturmamayı tercih ederseniz, daha sonra tek tek oluşturulabilir. Azure klasik farklı çalıştır hesabı isteğe bağlıdır ve klasik kaynakları yönetmeniz gerekiyorsa ayrı olarak oluşturulur.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWwtF3]

### <a name="run-as-account"></a>Farklı Çalıştır hesabı

Farklı Çalıştır hesabı oluşturduğunuzda, aşağıdaki görevleri gerçekleştirir:

* Otomatik olarak imzalanan bir sertifika ile bir Azure AD uygulaması oluşturur, Azure AD 'de uygulama için bir hizmet sorumlusu hesabı oluşturur ve geçerli aboneliğinizde hesap için [katkıda](../role-based-access-control/built-in-roles.md#contributor) bulunan rolünü atar. Sertifika ayarını [okuyucu](../role-based-access-control/built-in-roles.md#reader) olarak veya başka bir rol olarak değiştirebilirsiniz. Daha fazla bilgi için bkz. [Azure Otomasyonu’nda rol tabanlı erişim denetimi](automation-role-based-access-control.md).

* Belirtilen Otomasyon hesabında adlı bir Otomasyon sertifikası varlığı oluşturur `AzureRunAsCertificate` . Sertifika varlığı, Azure AD uygulaması tarafından kullanılan sertifika özel anahtarını içerir.

* Belirtilen Otomasyon hesabında adlı bir Otomasyon bağlantı varlığı oluşturur `AzureRunAsConnection` . Bağlantı varlığı uygulama KIMLIĞI, kiracı KIMLIĞI, abonelik KIMLIĞI ve sertifika parmak izini barındırır.

### <a name="azure-classic-run-as-account"></a>Azure klasik farklı çalıştır hesabı

Azure klasik farklı çalıştır hesabı oluşturduğunuzda, aşağıdaki görevleri gerçekleştirir:

> [!NOTE]
> Bu tür bir farklı çalıştır hesabı oluşturmak veya yenilemek için abonelikte ortak yönetici olmanız gerekir.

* Abonelikte bir yönetim sertifikası oluşturur.

* Belirtilen Otomasyon hesabında adlı bir Otomasyon sertifikası varlığı oluşturur `AzureClassicRunAsCertificate` . Sertifika varlığı, yönetim sertifikası tarafından kullanılan sertifika özel anahtarını içerir.

* Belirtilen Otomasyon hesabında adlı bir Otomasyon bağlantı varlığı oluşturur `AzureClassicRunAsConnection` . Bağlantı varlığı, abonelik adı, abonelik KIMLIĞI ve sertifika varlık adını içerir.

## <a name="service-principal-for-run-as-account"></a>Farklı Çalıştır hesabı için hizmet sorumlusu

Farklı Çalıştır hesabı için hizmet sorumlusu varsayılan olarak Azure AD 'yi okuma izinlerine sahip değildir. Azure AD 'yi okuma veya yönetme izinleri eklemek istiyorsanız, **API izinleri** altında hizmet sorumlusu için izinleri vermeniz gerekir. Daha fazla bilgi edinmek için bkz. [Web API 'nize erişmek için Izin ekleme](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Farklı Çalıştır hesabı izinleri

Bu bölümde hem normal farklı Çalıştır hesaplarının hem de klasik Farklı Çalıştır hesaplarının izinleri tanımlanmaktadır.

* Farklı Çalıştır hesabı oluşturmak veya güncelleştirmek için, Azure Active Directory içindeki bir uygulama Yöneticisi ve abonelikteki bir sahip tüm görevleri tamamlayabilir.
* Klasik farklı çalıştır hesaplarını yapılandırmak veya yenilemek için, abonelik düzeyinde ortak yönetici rolüne sahip olmanız gerekir. Klasik abonelik izinleri hakkında daha fazla bilgi için bkz. [Azure klasik abonelik yöneticileri](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

Görevlerinin ayrılmanız durumunda aşağıdaki tabloda, görevlerin bir listesi, eşdeğer cmdlet ve gerekli izinler gösterilmektedir:

|Görev|Cmdlet  |Minimum Izinler  |İzinleri ayarladığınız yer|
|---|---------|---------|---|
|Azure AD uygulaması oluşturma|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | Uygulama geliştirici rolü<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Azure AD > uygulama kayıtlarını > giriş |
|Uygulamaya bir kimlik bilgisi ekleyin.|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | Uygulama Yöneticisi veya genel yönetici<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Azure AD > uygulama kayıtlarını > giriş|
|Azure AD hizmet sorumlusu oluşturma ve edinme|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | Uygulama Yöneticisi veya genel yönetici<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Azure AD > uygulama kayıtlarını > giriş|
|Belirtilen sorumlu için Azure rolünü ata veya al|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Kullanıcı erişimi Yöneticisi veya sahibi ya da aşağıdaki izinlere sahip:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Abonelik](../role-based-access-control/role-assignments-portal.md)</br>Giriş > abonelikleri > \<subscription name\> -Access Control (IAM)|
|Otomasyon sertifikası oluşturma veya kaldırma|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | Kaynak grubunda katkıda bulunan         |Otomasyon hesabı kaynak grubu|
|Otomasyon bağlantısı oluşturma veya kaldırma|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Kaynak grubunda katkıda bulunan |Otomasyon hesabı kaynak grubu|

<sup>1</sup> Azure AD kiracınızdaki yönetici olmayan kullanıcılar, **Kullanıcı ayarları** sayfasında **uygulamalar kaydedebiliyorsanız** , [ad uygulamalarını kayıt](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) **edebilir.** Uygulama kayıt ayarı **Hayır** ise, bu eylemi gerçekleştiren kullanıcının bu tabloda tanımlanmış olması gerekir.

Aboneliğin genel yönetici rolüne eklenmeden önce aboneliğin Active Directory örneğinin bir üyesi değilseniz, konuk olarak eklendiniz. Bu durumda, `You do not have permissions to create…` **Otomasyon hesabı ekle** sayfasında bir uyarı alırsınız.

Hata iletisini üreten durumun giderilmiş olduğunu doğrulamak için:

1. Azure portal Azure Active Directory bölmesinden **Kullanıcılar ve gruplar**' ı seçin.
2. **Tüm kullanıcılar**' ı seçin.
3. Adınızı seçip **profil**' i seçin.
4. Kullanıcı profili altındaki **Kullanıcı türü** özniteliğinin değerinin **Konuk** olarak ayarlı olmadığından emin olun.

## <a name="role-based-access-control"></a>Rol tabanlı erişim denetimi

Rol tabanlı erişim denetimi, Azure AD Kullanıcı hesabı ve farklı çalıştır hesabı için izin verilen eylemleri vermek ve hizmet sorumlusunun kimliğini doğrulamak için Azure Resource Manager ile kullanılabilir. Otomasyon izinlerinin yönetilmesi için modelinizin geliştirilmesine yardımcı olma hakkında daha fazla bilgi için [Azure Otomasyonu’nda rol tabanlı erişim denetimi](automation-role-based-access-control.md) makalesini okuyun.

Kaynak gruplarında izin ataması için katı güvenlik denetimleriniz varsa, farklı çalıştır hesabı üyeliğini kaynak grubundaki **katkıda** bulunan rolüne atamanız gerekir.

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Karma Runbook Worker ile runbook kimlik doğrulaması

Veri merkezinizdeki bir karma runbook çalışanı üzerinde çalışan runbook 'larda veya AWS gibi diğer bulut ortamlarında bilgi işlem hizmetlerine karşı, genellikle Azure kaynaklarında kimlik doğrulaması yapan runbook 'lar için kullanılan yöntemi kullanamaz. Bunun nedeni, bu kaynakların Azure dışında çalışmasıdır; sonuç olarak da, yerel olarak erişecekleri kaynakların kimliğini doğrulamak için Otomasyon'da tanımlanan kendi güvenlik kimlik bilgileri gerekecektir. Runbook çalışanları ile runbook kimlik doğrulaması hakkında daha fazla bilgi için bkz. runbook ['Ları karma Runbook Worker üzerinde çalıştırma](automation-hrw-run-runbooks.md).

Azure VM 'lerinde karma runbook çalışanları kullanan runbook 'lar için, Azure kaynaklarınızın kimliğini doğrulamak üzere farklı çalıştır hesapları yerine [yönetilen kimliklerle runbook kimlik doğrulamasını](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* Azure portal bir Otomasyon hesabı oluşturmak için bkz. [tek başına Azure Otomasyonu hesabı oluşturma](automation-create-standalone-account.md).
* Hesabınızı bir şablon kullanarak oluşturmayı tercih ediyorsanız, bkz. [Azure Resource Manager şablonu kullanarak Otomasyon hesabı oluşturma](quickstart-create-automation-account-template.md).
* Amazon Web Services kullanarak kimlik doğrulaması için bkz. [Amazon Web Services runbook 'Ları kimlik](automation-config-aws-account.md)doğrulama.
* Azure kaynakları için yönetilen kimlikler özelliğini destekleyen Azure hizmetlerinin listesi için bkz. [Azure kaynakları için yönetilen kimlikleri destekleyen hizmetler](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities).

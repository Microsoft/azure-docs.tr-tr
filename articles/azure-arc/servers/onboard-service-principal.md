---
title: Karma makineleri Azure 'a ölçeklendirmeye bağlama
description: Bu makalede, hizmet sorumlusu kullanarak Azure Arc etkin sunucularını kullanarak makineleri Azure 'a bağlamayı öğreneceksiniz.
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4aad01fd6991c059b2cf891fd4f06ae83a78a0e4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831619"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Karma makineleri Azure 'a ölçeklendirmeye bağlama

Gereksinimlerinize bağlı olarak çeşitli esnek seçeneklerle ortamınızda birden çok Windows veya Linux makinesi için Azure Arc etkin sunucularını etkinleştirebilirsiniz. Sağladığımız şablon betiğini kullanarak, Azure Arc bağlantısı kurulması da dahil olmak üzere, yüklemenin her adımını otomatikleştirebiliriz. Bununla birlikte, bu betiği, hedef makinede ve Azure 'da yükseltilmiş izinlere sahip bir hesapla etkileşimli olarak yürütmeniz gerekir.

Makineleri Azure Arc etkin sunucularına bağlamak için, [makineyi etkileşimli olarak bağlamak](onboard-portal.md)üzere ayrıcalıklı kimliğinizi kullanmak yerine bir Azure Active Directory [hizmet sorumlusu](../../active-directory/develop/app-objects-and-service-principals.md) kullanabilirsiniz. Hizmet sorumlusu, yalnızca komutunu kullanarak makineleri Azure 'a bağlamak için gereken en düşük izne sahip özel sınırlı bir yönetim kimliğidir `azcmagent` . Bu, Kiracı Yöneticisi gibi daha yüksek ayrıcalıklı bir hesap kullanmaktan daha güvenlidir ve erişim denetimi güvenliği en iyi yöntemlerimizi izler. Hizmet sorumlusu yalnızca ekleme sırasında kullanılır, başka bir amaçla kullanılmaz.  

Bağlı makine aracısını yüklemeye ve yapılandırmaya yönelik yükleme yöntemleri, kullandığınız otomatik metodun makinelerde yönetici izinlerine sahip olmasını gerektirir. Linux 'ta, kök hesabı ve Windows üzerinde yerel Yöneticiler grubunun bir üyesi olarak.

Başlamadan önce, [önkoşulları](agent-overview.md#prerequisites) gözden geçirdiğinizden ve aboneliğinizin ve kaynaklarınızın gereksinimleri karşıladığından emin olun. Desteklenen bölgeler ve diğer ilgili konular hakkında daha fazla bilgi için bkz. [desteklenen Azure bölgeleri](overview.md#supported-regions). Ayrıca, tasarım ve dağıtım ölçütlerinin yanı sıra yönetim ve izleme önerilerimizi anlamak için [ölçekli planlama kılavuzumuzu](plan-at-scale-deployment.md) gözden geçirin.  

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Ölçekte ekleme için bir hizmet sorumlusu oluşturma

[New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) cmdlet 'i ile bir hizmet sorumlusu oluşturmak için [Azure PowerShell](/powershell/azure/install-az-ps) kullanabilirsiniz. Ya da bu görevi gerçekleştirmek için [Azure Portal kullanarak hizmet sorumlusu oluşturma](../../active-directory/develop/howto-create-service-principal-portal.md) altında listelenen adımları izleyebilirsiniz.

> [!NOTE]
> Bir hizmet sorumlusu oluşturmadan önce, hesabınız, ekleme için kullanmak istediğiniz abonelikte **sahip** veya **Kullanıcı erişimi Yöneticisi** rolünün bir üyesi olmalıdır. Rol atamalarını yapılandırmak için yeterli izniniz yoksa, hizmet sorumlusu oluşturulmuş olabilir, ancak makine ekleyemez.
>

PowerShell kullanarak hizmet sorumlusu oluşturmak için aşağıdaki adımları uygulayın.

1. Aşağıdaki komutu çalıştırın. Cmdlet 'in çıkışını bir değişkende depolamanız gerekir [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) , aksi durumda daha sonraki bir adımda gereken parolayı alamazsınız.

    ```azurepowershell-interactive
    $sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
    $sp
    ```

    ```output
    Secret                : System.Security.SecureString
    ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
    ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
    ObjectType            : ServicePrincipal
    DisplayName           : Hybrid-RP
    Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
    Type                  :
    ```

2. Değişkende depolanan parolayı almak için `$sp` aşağıdaki komutu çalıştırın:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. Çıktıda, alan **parolasının** altındaki parola değerini bulun ve kopyalayın. Ayrıca, **applicationfield** alanının altındaki değeri bulur ve ayrıca kopyalayın. Daha sonra güvenli bir yerde saklayın. Hizmet sorumlusu Parolanızı unutur veya kaybederseniz, [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) cmdlet 'ini kullanarak sıfırlayabilirsiniz.

Aşağıdaki özelliklerden alınan değerler öğesine geçirilen parametrelerle kullanılır `azcmagent` :

* **ApplicationId** özelliğinden alınan değer `--service-principal-id` parametre değeri için kullanılır
* **Password** özelliğinden alınan değer, `--service-principal-secret` aracıyı bağlamak için kullanılan parametresi için kullanılır.

> [!NOTE]
> **Kimlik** özelliğini değil hizmet sorumlusu **ApplicationId** özelliğini kullandığınızdan emin olun.
>

**Azure bağlı makine ekleme** rolü yalnızca bir makineyi eklemek için gereken izinleri içerir. Kapsamının bir kaynak grubu veya abonelik içermesini sağlamak için hizmet sorumlusu iznini atayabilirsiniz. Rol ataması eklemek için bkz. Azure [rollerini Azure Portal kullanarak atama](../../role-based-access-control/role-assignments-portal.md) veya Azure [CLI kullanarak Azure rolleri atama](../../role-based-access-control/role-assignments-cli.md).

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Azure portal yükleme betiğini oluşturma

İndirme ve yüklemeyi otomatik hale getirmeye ve Azure Arc ile bağlantı kurmaya yönelik betik, Azure portal kullanılabilir. İşlemi gerçekleştirmek için aşağıdaki adımları uygulayın:

1. Tarayıcınızdan [Azure Portal](https://portal.azure.com)gidin.

1. **Sunucular-Azure yay** sayfasında sol üst köşedeki **Ekle** ' yi seçin.

1. **Yöntem seçin** sayfasında, **birden çok sunucu Ekle** kutucuğunu seçin ve ardından **betik oluştur**' u seçin.

1. **Betik oluştur** sayfasında, makinenin Azure 'da yönetilmesini istediğiniz aboneliği ve kaynak grubunu seçin. Makine meta verilerinin depolanacağı Azure konumunu seçin. Bu konum, kaynak grubunun konumuyla aynı veya farklı olabilir.

1. **Önkoşullar** sayfasında, bilgileri gözden geçirin ve ardından **İleri: kaynak ayrıntıları**' nı seçin.

1. **Kaynak ayrıntıları** sayfasında, aşağıdakileri sağlayın:

    1. **Kaynak grubu** açılan listesinde, makinenin yönetilecek kaynak grubunu seçin.
    1. **Bölge** açılan listesinde, sunucu meta verilerini depolamak için Azure bölgesini seçin.
    1. **İşletim sistemi** açılan listesinde, betiğin üzerinde çalışmak üzere yapılandırıldığı işletim sistemini seçin.
    1. Makine Internet 'e bağlanmak için bir proxy sunucusu üzerinden iletişim kurduklarında, proxy sunucusu IP adresini veya makinenin proxy sunucusuyla iletişim kurmak için kullanacağı adı ve bağlantı noktası numarasını belirtin. Değeri biçiminde girin `http://<proxyURL>:<proxyport>` .
    1. **İleri ' yi seçin: kimlik doğrulama**.

1. **Kimlik doğrulama** sayfasında, **hizmet sorumlusu** açılan listesinde, **sunucular için yay**' ı seçin.  Sonra, **İleri: Etiketler**' i seçin.

1. **Etiketler** sayfasında, önerilen varsayılan **fiziksel konum etiketlerini** gözden geçirin ve bir değer girin veya standartlarınızı desteklemek için bir veya daha fazla **özel etiket** belirtin.

1. **İleri ' yi seçin: betiği indir ve Çalıştır**.

1. **Betiği indir ve Çalıştır** sayfasında, Özet bilgilerini gözden geçirin ve ardından **İndir**' i seçin. Hala değişiklik yapmanız gerekiyorsa, **önceki**' yi seçin.

Windows için, `OnboardingScript.ps1` Bilgisayarınızı bilgisayarınıza ve Linux için kaydetmeniz istenir `OnboardingScript.sh` .

## <a name="install-the-agent-and-connect-to-azure"></a>Aracıyı yükleyip Azure 'a bağlanın

Daha önce oluşturulan betik şablonunu alarak, kuruluşların tercih ettiğiniz Otomasyon aracını kullanarak bağlı makine aracısını birden fazla karma Linux ve Windows makinesine yükleyebilir ve yapılandırabilirsiniz. Betik, [Azure Portal makalesinden karma makineleri Azure 'A bağlama](onboard-portal.md) bölümünde açıklanan benzer adımları gerçekleştirir. Bu fark, `azcmagent` hizmet sorumlusunu kullanarak komutunu kullanarak Azure Arc bağlantısını oluşturduğunuz son adımdır.

Aşağıda, `azcmagent` hizmet sorumlusu için kullanmak üzere komutu yapılandırdığınız ayarlar verilmiştir.

* `service-principal-id` : Hizmet sorumlusunun uygulama KIMLIĞINI temsil eden benzersiz tanımlayıcı (GUID).
* `service-principal-secret` | Hizmet sorumlusu parolası.
* `tenant-id` : Adanmış Azure AD örneğinizi temsil eden benzersiz tanımlayıcı (GUID).
* `subscription-id` : İçinde makinelere istediğiniz Azure aboneliğinizin abonelik KIMLIĞI (GUID).
* `resource-group` : Bağlı makinelerinizin ait olmasını istediğiniz kaynak grubu adı.
* `location` : [Desteklenen Azure bölgelerine](overview.md#supported-regions)bakın. Bu konum, kaynak grubunun konumuyla aynı veya farklı olabilir.
* `resource-name` : (*Isteğe bağlı*) Şirket Içi makinenizin Azure Kaynak temsili için kullanılır. Bu değeri belirtmezseniz makine ana bilgisayar adı kullanılır.

`azcmagent` [Azcmagent başvurusunu](./manage-agent.md)inceleyerek komut satırı aracı hakkında daha fazla bilgi edinebilirsiniz.

>[!NOTE]
>Windows PowerShell betiği yalnızca Windows PowerShell 'in 64 bitlik bir sürümünden çalışmayı destekler.
>

Aracıyı yükledikten ve Azure Arc etkin sunucularına bağlanacak şekilde yapılandırdıktan sonra, sunucunun başarıyla bağlandığını doğrulamak için Azure portal gidin. [Azure portalında](https://aka.ms/hybridmachineportal) makinelerinizi görüntüleyin.

![Başarılı bir sunucu bağlantısı](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Sonraki adımlar

- Sorun giderme bilgileri, [bağlı makine Aracısı sorunlarını giderme kılavuzunda](troubleshoot-agent-onboard.md)bulunabilir.

- VM [Konuk yapılandırması](../../governance/policy/concepts/guest-configuration.md), makinenin beklenen Log Analytics çalışma alanına rapor ettiğini doğrulamak, [VM 'lerle Azure izleyici](../../azure-monitor/vm/vminsights-enable-policy.md)ile izlemeyi etkinleştirmek ve çok daha fazlasını yapmak için [Azure ilkesini](../../governance/policy/overview.md)kullanarak makinenizi yönetme hakkında bilgi edinin.

- [Log Analytics Aracısı](../../azure-monitor/agents/log-analytics-agent.md)hakkında daha fazla bilgi edinin. Windows ve Linux için Log Analytics Aracısı, VM'ler için Azure İzleyici ile işletim sistemi ve iş yükü izleme verileri toplamak, Otomasyon Runbook 'larını veya Güncelleştirme Yönetimi gibi özellikleri kullanarak yönetmek ya da [Azure Güvenlik Merkezi](../../security-center/security-center-introduction.md)gibi diğer Azure hizmetlerini kullanmak istediğinizde gereklidir.

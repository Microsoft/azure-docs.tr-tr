---
title: Azure 'da Azure Active Directory kullanarak Windows sanal makinesinde oturum açma (Önizleme)
description: Windows çalıştıran bir Azure VM 'de Azure AD oturum açma
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.custom: references_regions, devx-track-azurecli
ms.collection: M365-identity-device-management
ms.openlocfilehash: 418741c10dfe5f0678d7771d046781697512bafe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776510"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Azure 'da Azure Active Directory kimlik doğrulaması (Önizleme) kullanarak Windows sanal makinesinde oturum açma

Kuruluşlar artık **Windows Server 2019 Datacenter Edition** veya **Windows 10 1809** ve üstünü çalıştıran Azure sanal makineleri (vm 'ler) için Azure Active Directory (ad) kimlik doğrulaması kullanabilir. VM 'Lerde kimlik doğrulaması yapmak için Azure AD 'nin kullanılması, ilkeleri merkezi olarak denetleme ve zorunlu kılmak için bir yol sağlar. Azure rol tabanlı erişim denetimi (Azure RBAC) ve Azure AD koşullu erişim gibi araçlar, bir VM 'ye kimlerin erişebileceğini denetlemenize olanak tanır. Bu makalede, Azure AD kimlik doğrulamasını kullanmak üzere bir Windows Server 2019 VM oluşturma ve yapılandırma açıklanmaktadır.

> [!NOTE]
> Azure Windows VM 'Leri için Azure AD oturum açma, Azure Active Directory genel bir önizleme özelliğidir. Önizlemeler hakkında daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure AD kimlik doğrulamasını kullanarak Azure 'da Windows VM 'lerde oturum açmak için aşağıdakiler de dahil olmak üzere birçok avantaj vardır:

- Normalde kullandığınız Federasyon veya yönetilen Azure AD kimlik bilgilerini kullanın.
- Artık yerel yönetici hesaplarını yönetmek zorunda değildir.
- Azure RBAC, ihtiyaç duymak üzere VM 'lere uygun erişim izni vermenizi ve artık gerekli olmadığında kaldırmanızı sağlar.
- Bir sanal makineye erişime izin vermeden önce Azure AD koşullu erişimi, şöyle ek gereksinimler uygulayabilir: 
   - Multi-factor authentication
   - Oturum açma risk denetimi
- VDı dağıtımlarınız için bir parçası olan Azure Windows VM 'lerinin Azure AD JOIN 'i otomatikleştirin ve ölçeklendirin.

> [!NOTE]
> Bu özelliği etkinleştirdikten sonra Azure 'daki Windows VM 'niz Azure AD 'ye katılmış olur. Şirket içi AD veya Azure AD DS gibi başka bir etki alanına birleştiremezsiniz. Bunu yapmanız gerekirse, uzantıyı kaldırarak VM 'nin Azure AD kiracınızdan bağlantısını kesmeniz gerekir.

## <a name="requirements"></a>Gereksinimler

### <a name="supported-azure-regions-and-windows-distributions"></a>Desteklenen Azure bölgeleri ve Windows dağıtımları

Aşağıdaki Windows dağıtımları Şu anda bu özelliğin önizlemesi sırasında desteklenmektedir:

- Windows Server 2019 Datacenter
- Windows 10 1809 ve üzeri

> [!IMPORTANT]
> Azure AD 'ye katılmış olan sanal makinelere uzak bağlantıya yalnızca Azure AD kayıtlı (Windows 10 20 H1 ' den başlayarak), Azure AD 'ye katılmış veya hibrit Azure AD 'nin VM ile **aynı** dizine katılmış olduğu Windows 10 bilgisayarlardan izin verilir. 

Şu Azure bölgeleri Şu anda bu özelliğin önizlemesi sırasında desteklenmektedir:

- Tüm Azure genel bölgeleri

> [!IMPORTANT]
> Bu önizleme özelliğini kullanmak için, desteklenen bir Windows dağıtımını ve desteklenen bir Azure bölgesinde dağıtın. Bu özellik şu anda Azure Kamu veya bağımsız bulutlarda desteklenmiyor.

### <a name="network-requirements"></a>Ağ gereksinimleri

Azure 'da Windows sanal makinelerinize yönelik Azure AD kimlik doğrulamasını etkinleştirmek için, VM 'Lerin ağ yapılandırmanızın TCP bağlantı noktası 443 üzerinden aşağıdaki uç noktalara giden erişime izin verdiğinden emin olmanız gerekir:

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://pas.windows.net`

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Azure 'da Windows VM için Azure AD oturum açmayı etkinleştirme

Azure 'da Windows VM 'de Azure AD oturum açma 'yı kullanmak için, önce Windows VM 'niz için Azure AD oturum açma seçeneğini etkinleştirmeniz ve ardından VM 'de oturum açma yetkisi olan kullanıcılar için Azure rol atamalarını yapılandırmanız gerekir.
Windows VM 'niz için Azure AD oturum açma özelliğini etkinleştirebilmenizin birden çok yolu vardır:

- Windows VM oluştururken Azure portal deneyimini kullanma
- Windows VM oluştururken **veya mevcut bir WINDOWS VM için** Azure Cloud Shell deneyimini kullanma

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Azure AD oturum açma özelliğini etkinleştirmek için Azure portal VM oluşturma deneyimi kullanma

Windows Server 2019 Datacenter veya Windows 10 1809 ve üzeri VM görüntüleri için Azure AD oturum açmayı etkinleştirebilirsiniz. 

Azure AD oturum açma ile Azure 'da bir Windows Server 2019 Datacenter VM oluşturmak için: 

1. VM oluşturma erişimi olan bir hesapla [Azure Portal](https://portal.azure.com)oturum açın ve **+ kaynak oluştur**' u seçin.
1. Market arama çubuğunu aramak için **Windows Server** yazın.
   1. **Windows Server** ' a tıklayın ve yazılım planı seç açılan listesinden **Windows Server 2019 Datacenter** ' ı seçin.
   1. **Oluştur**'a tıklayın.
1. "Yönetim" sekmesinde, Azure Active Directory bölümü altındaki **AAD kimlik bilgileri (Önizleme) Ile** **Açık '** a oturum açma seçeneğini etkinleştirin.
1. Kimlik bölümünün altında **sistem tarafından atanan yönetilen kimliğin** **Açık** olarak ayarlandığından emin olun. Bu eylem, Azure AD kimlik bilgileriyle oturum açma özelliğini etkinleştirdikten sonra otomatik olarak gerçekleşmelidir.
1. Sanal makine oluşturma deneyiminden geri dönün. Bu önizleme sırasında, VM için bir Yönetici Kullanıcı adı ve parola oluşturmanız gerekecektir.

![Azure AD kimlik bilgileriyle oturum açma VM oluşturma](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Azure AD kimlik bilgilerinizi kullanarak VM 'de oturum açmak için öncelikle aşağıdaki bölümlerden birinde açıklandığı gibi VM için rol atamalarını yapılandırmanız gerekir.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Azure AD oturum açma özelliğini etkinleştirmek için Azure Cloud Shell deneyimini kullanma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın kullanılan Azure araçları hesabınızla kullanmanız için Cloud Shell'de önceden yüklenir ve yapılandırılır. Kopyala’ya tıklayarak kodu kopyalamanız, Cloud Shell’e yapıştırmanız ve Enter tuşuna basarak çalıştırmanız yeterlidir. Cloud Shell’i açmanın birkaç yolu vardır:

- Kod bloğunun sağ üst köşesindeki **Deneyin**’i seçin.
- Cloud Shell’i tarayıcınızda açın.
- [Azure portalının](https://portal.azure.com) sağ üst köşesindeki menüde yer alan Cloud Shell düğmesini seçin.

CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu makale, Azure CLı sürüm 2.0.31 veya üstünü çalıştırıyor olmanızı gerektirir. Sürümü bulmak için az --version komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI 'Yı yüklemeye](/cli/azure/install-azure-cli)yönelik makale.

1. [az group create](/cli/azure/group#az_group_create) ile bir kaynak grubu oluşturun. 
1. Desteklenen bir bölgede desteklenen bir dağıtım kullanarak [az VM Create](/cli/azure/vm#az_vm_create) Ile bir VM oluşturun. 
1. Azure AD oturum açma VM uzantısını yükler. 

Aşağıdaki örnek, Win2019Datacenter kullanan myVM adlı bir VM 'yi, Güneydoğu bölgesinde myResourceGroup adlı bir kaynak grubuna dağıtır. Aşağıdaki örneklerde, gerektiğinde kendi kaynak grubunuzu ve sanal makine adlarınızı sağlayabilirsiniz.

```AzureCLI
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Win2019Datacenter \
    --assign-identity \
    --admin-username azureuser \
    --admin-password yourpassword
```

> [!NOTE]
> Azure AD oturum açma VM uzantısını yüklemeden önce sanal makinenizde sistem tarafından atanan yönetilen kimliği etkinleştirmeniz gerekir.

VM’yi ve destekleyici kaynakları oluşturmak birkaç dakika sürer.

Son olarak, Azure AD oturum açma VM uzantısını yükleyerek Windows VM için Azure AD oturum açma özelliğini etkinleştirin. VM uzantıları, Azure sanal makinelerinde dağıtım sonrası yapılandırma ve otomasyon görevleri sağlayan küçük uygulamalardır. Kaynak grubunda adlı VM 'ye AADLoginForWindows uzantısını yüklemek için [az VM Extension](/cli/azure/vm/extension#az_vm_extension_set) set komutunu kullanın `myVM` `myResourceGroup` :

> [!NOTE]
> Azure AD kimlik doğrulaması için etkinleştirmek üzere, mevcut bir Windows Server 2019 veya Windows 10 1809 ve sonraki bir sanal makineye AADLoginForWindows uzantısını yükleyebilirsiniz. AZ CLı örneği aşağıda gösterilmiştir.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

, `provisioningState` `Succeeded` Uzantısı VM 'ye yüklendikten sonra gösterilir.

## <a name="configure-role-assignments-for-the-vm"></a>VM için rol atamalarını yapılandırma

VM 'yi oluşturduğunuza göre, VM 'de kimlerin oturum Açabili belirleyebilmek için Azure RBAC ilkesini yapılandırmanız gerekir. VM oturum açma yetkisini yetkilendirmek için iki Azure rolü kullanılır:

- **Sanal Makine Yöneticisi oturum açma**: Bu role atanmış kullanıcılar yönetici ayrıcalıklarına sahip bir Azure sanal makinesinde oturum açabilir.
- **Sanal makine Kullanıcı oturumu açma**: Bu role atanmış kullanıcılar, normal kullanıcı ayrıcalıklarına sahip bir Azure sanal makinesinde oturum açabilir.

> [!NOTE]
> Bir kullanıcının VM 'de RDP üzerinden oturum açmasına izin vermek için, sanal makine Yöneticisi oturum açma veya sanal makine Kullanıcı oturum açma rolünü atamanız gerekir. Bir VM için atanan sahip veya katkıda bulunan rollerinin bulunduğu bir Azure kullanıcısının, sanal makinede RDP üzerinden oturum açma ayrıcalıkları otomatik olarak yoktur. Bu, sanal makineleri denetleyen kişi kümesi arasında, sanal makinelere erişebilen kişiler kümesiyle denetlenen ayırma sağlamaktır.

VM için rol atamalarını yapılandırabileceğiniz birçok yol vardır:

- Azure AD Portal deneyimini kullanma
- Azure Cloud Shell deneyimini kullanma

> [!NOTE]
> Sanal Makine Yöneticisi oturum açma ve sanal makine Kullanıcı oturum açma rolleri dataActions kullanır ve bu nedenle yönetim grubu kapsamında atanamaz. Şu anda bu roller yalnızca abonelikte, kaynak grubunda veya kaynak kapsamında atanabilir.

### <a name="using-azure-ad-portal-experience"></a>Azure AD Portal deneyimini kullanma

Azure AD özellikli Windows Server 2019 Datacenter VM 'leriniz için rol atamalarını yapılandırmak için:

1. Belirli bir sanal makineye genel bakış sayfasına gidin
1. Menü seçeneklerinden **erişim denetimi (IAM)** seçeneğini belirleyin
1. Rol ataması Ekle bölmesini açmak için **Ekle**, **rol ataması Ekle** ' yi seçin.
1. **Rol** açılan listesinde, **sanal makine yöneticisi oturumu** veya **sanal makine Kullanıcı oturumu açma** gibi bir rol seçin.
1. **Seç** alanında, bir Kullanıcı, Grup, hizmet sorumlusu veya yönetilen kimlik seçin. Listede güvenlik sorumlusunu görmüyorsanız **Seç** kutusuna giriş yaparak dizinde görünen ad, e-posta adresi ve nesne tanımlayıcısı arayabilirsiniz.
1. Rolü atamak için **Kaydet**' i seçin.

Birkaç dakika sonra, güvenlik sorumlusu seçili kapsamda role atanır.

![VM 'ye erişecek kullanıcılara roller atama](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Azure Cloud Shell deneyimini kullanma

Aşağıdaki örnek, geçerli Azure kullanıcılarınız için sanal makine Yöneticisi oturum açma rolünü VM 'ye atamak için [az role atama Create](/cli/azure/role/assignment#az_role_assignment_create) ' i kullanır. Etkin Azure hesabınızın Kullanıcı adı [az Account Show](/cli/azure/account#az_account_show)komutuyla alınır ve kapsam, [az VM Show](/cli/azure/vm#az_vm_show)ile ÖNCEKI bir adımda oluşturulan VM 'ye ayarlanır. Kapsam Ayrıca bir kaynak grubuna veya abonelik düzeyine atanabilir ve normal Azure RBAC devralma izinleri geçerlidir. Daha fazla bilgi için bkz. [Azure 'da Azure Active Directory kimlik doğrulaması kullanarak bir Linux sanal makinesinde oturum açma](../../virtual-machines/linux/login-using-aad.md).

```   AzureCLI
$username=$(az account show --query user.name --output tsv)
$vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> AAD etki alanı ve oturum açma Kullanıcı adı etki alanınız eşleşmezse, Kullanıcı hesabınızın nesne KIMLIĞINI yalnızca Kullanıcı adı değil ile belirtmeniz gerekir `--assignee-object-id` `--assignee` . Kullanıcı hesabınızın nesne KIMLIĞINI [az ad kullanıcı listesi](/cli/azure/ad/user#az_ad_user_list)ile elde edebilirsiniz.

Azure RBAC 'nin Azure abonelik kaynaklarınıza erişimini yönetmek için nasıl kullanılacağı hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Azure CLı kullanarak Azure rolleri atama](../../role-based-access-control/role-assignments-cli.md)
- [Azure portal kullanarak Azure rolleri atama](../../role-based-access-control/role-assignments-portal.md)
- [Azure PowerShell kullanarak Azure rolleri atayın](../../role-based-access-control/role-assignments-powershell.md).

## <a name="using-conditional-access"></a>Koşullu erişim kullanma

Azure AD oturum açma özelliği ile etkinleştirilen Azure 'da Windows VM 'lerine erişimi yetkilendirmeden önce Multi-Factor Authentication veya Kullanıcı oturum açma riski denetimi gibi koşullu erişim ilkelerini uygulayabilirsiniz. Koşullu erişim ilkesini uygulamak için, bulut uygulamaları veya eylemler atama seçeneğinden "Azure Windows VM oturum açma" uygulamasını seçmeniz ve ardından bir koşul olarak oturum açma riskini kullanmanız ve/veya çok faktörlü kimlik doğrulamasının izin verme erişim denetimi olması gerekir. 

> [!NOTE]
> "Multi-Factor Authentication gerektir" seçeneğini, "Azure Windows VM oturum açma" uygulamasına erişim isteğinde bulunmak için erişim denetimi izni olarak kullanırsanız, Azure 'daki hedef Windows VM 'sine yönelik RDP oturumunu başlatan istemcinin bir parçası olarak Multi-Factor Authentication talebi sağlamalısınız. Bunu bir Windows 10 istemcisinde elde etmenin tek yolu, Iş için Windows Hello PIN 'ini veya RDP istemcisiyle biyometrik kimlik doğrulamasını kullanmaktır. Windows 10 sürüm 1809 ' de RDP istemcisine biyometrik kimlik doğrulaması desteği eklenmiştir. Iş için Windows Hello kimlik doğrulaması kullanan uzak masaüstü yalnızca sertifika güven modeli kullanan dağıtımlar için kullanılabilir ve şu anda anahtar güven modeli için kullanılamaz.

> [!WARNING]
> Kullanıcı başına etkin/Zorlanmış Azure AD Multi-Factor Authentication, VM 'de oturum açma için desteklenmez.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Azure AD kimlik bilgilerini kullanarak bir Windows VM 'de oturum açma

> [!IMPORTANT]
> Azure AD 'ye katılmış olan sanal makinelere uzak bağlantıya yalnızca Azure AD kayıtlı (minimum gerekli derleme 20 H1) veya Azure AD 'ye katılmış ya da karma Azure AD, VM ile **aynı** dizine katılmış olan Windows 10 bilgisayarlardan izin verilir. Ayrıca, Azure AD kimlik bilgilerini kullanarak RDP 'ye Kullanıcı, sanal makine Yöneticisi oturum açma veya sanal makine Kullanıcı oturum açma bilgilerinden birine ait olmalıdır. Azure AD kayıtlı bir Windows 10 PC kullanıyorsanız, kimlik bilgilerini `AzureAD\UPN` (örneğin,) biçiminde girmeniz gerekir `AzureAD\john@contoso.com` . Şu anda, Azure savunma, AADLoginForWindows uzantısıyla Azure Active Directory kimlik doğrulaması kullanılarak oturum açmak için kullanılamaz; yalnızca Direct RDP desteklenir.

Azure AD 'yi kullanarak Windows Server 2019 sanal makinenizde oturum açmak için: 

1. Azure AD oturum açma özelliği ile etkinleştirilen sanal makinenin genel bakış sayfasına gidin.
1. **Bağlan** ' ı seçerek sanal makine bağlantısı dikey penceresini açın.
1. **RDP Dosyasını İndir**’i seçin.
1. Uzak Masaüstü Bağlantısı istemcisini başlatmak için **Aç** ' ı seçin.
1. Windows oturum açma iletişim kutusunu başlatmak için **Bağlan** ' ı seçin.
1. Azure AD kimlik bilgilerinizi kullanarak oturum açın.

Artık Windows Server 2019 Azure sanal makinesinde, atanan VM kullanıcısı veya VM Yöneticisi gibi rol izinleriyle oturum açtınız. 

> [!NOTE]
> ' İ kaydedebilirsiniz. RDP dosyası, Azure portal sanal makineye genel bakış sayfasına gitmek yerine sanal makinenize yerel olarak Uzak Masaüstü bağlantıları başlatmak için ve Connect seçeneğini kullanmaktır.

## <a name="troubleshoot"></a>Sorun giderme

### <a name="troubleshoot-deployment-issues"></a>Dağıtım sorunlarını giderme

VM 'nin Azure AD JOIN işlemini tamamlaması için AADLoginForWindows uzantısının başarıyla yüklenmesi gerekir. VM Uzantısı doğru yüklenemediğinde aşağıdaki adımları gerçekleştirin.

1. Yerel yönetici hesabını kullanarak VM 'ye RDP ve şu `CommandExecution.log` dosyadaki dosyayı inceleyin:
   
   `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0.`

   > [!NOTE]
   > Uzantı ilk hatadan sonra yeniden başlarsa, dağıtım hatası olan günlük kaydı olarak kaydedilir `CommandExecution_YYYYMMDDHHMMSSSSS.log` . "
1. VM 'de bir PowerShell komut istemi açın ve bu sorguların Azure ana bilgisayarında çalışan Instance Metadata Service (ıMDS) uç noktasına karşı doğrulanması için şunu döndürür:

   | Çalıştırılacak komut | Beklenen çıkış |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Azure VM ile ilgili doğru bilgiler |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | Azure aboneliğiyle ilişkili geçerli kiracı KIMLIĞI |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Bu VM 'ye atanmış yönetilen kimlik için Azure Active Directory tarafından verilen geçerli erişim belirteci |

   > [!NOTE]
   > Erişim belirtecinin kodu, [calebb.net](http://calebb.net/)gibi bir araç kullanılarak çözülebilir. `appid`Erişim belirtecindeki ' ın VM 'ye atanan yönetilen kimlikle eşleştiğini doğrulayın.

1. Komut satırını kullanarak gerekli uç noktaların VM 'den erişilebilir olduğundan emin olun:
   
   - `curl https://login.microsoftonline.com/ -D -`
   - `curl https://login.microsoftonline.com/<TenantID>/ -D -`

   > [!NOTE]
   > `<TenantID>`Azure aboneliğiyle Ilişkili Azure AD KIRACı kimliği ile değiştirin.

   - `curl https://enterpriseregistration.windows.net/ -D -`
   - `curl https://device.login.microsoftonline.com/ -D -`
   - `curl https://pas.windows.net/ -D -`

1. Cihaz durumu çalıştırılarak görüntülenebilir `dsregcmd /status` . Amaç, cihaz durumunun as olarak gösterilmesi içindir `AzureAdJoined : YES` .

   > [!NOTE]
   > Azure AD JOIN etkinliği, günlük altındaki Olay Görüntüleyicisi 'nde yakalanır `User Device Registration\Admin` .

AADLoginForWindows uzantısı belirli hata kodu ile başarısız olursa, aşağıdaki adımları uygulayabilirsiniz:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>Sorun 1: AADLoginForWindows uzantısı, Terminal hata kodu ' 1007 ' ve çıkış kodu:-2145648574 ile yüklenemez.

`DSREG_E_MSI_TENANTID_UNAVAILABLE`Uzantı Azure AD kiracı bilgilerini sorgulayamadığından bu çıkış kodu öğesine çevrilir.

1. Azure VM 'nin Instance Metadata Service Tenantıd 'yi alabildiğini doğrulayın.

   - Yerel yönetici olarak VM 'ye RDP ile ve bu komutu VM 'deki yükseltilmiş bir komut satırından çalıştırarak uç noktanın geçerli bir kiracı KIMLIĞI döndürdüğünden emin olun:
      
      - `curl -H Metadata:true http://169.254.169.254/metadata/identity/info?api-version=2018-02-01`

1. VM Yöneticisi AADLoginForWindows uzantısını yüklemeyi dener, ancak sistem tarafından atanmış bir yönetilen kimlik önce VM 'yi etkinleştirmedi. VM 'nin kimlik dikey penceresine gidin. Sistem atandı sekmesinden durumunun açık olarak yapıldığını doğrulayın.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>Sorun 2: AADLoginForWindows uzantısı, çıkış kodu ile yüklenemedi:-2145648607

`DSREG_AUTOJOIN_DISC_FAILED`Uzantı uç noktaya erişemediği için bu çıkış kodu öğesine çevrilir `https://enterpriseregistration.windows.net` .

1. Komut satırını kullanarak gerekli uç noktaların VM 'den erişilebilir olduğundan emin olun:

   - `curl https://login.microsoftonline.com/ -D -`
   - `curl https://login.microsoftonline.com/<TenantID>/ -D -`
   
   > [!NOTE]
   > `<TenantID>`Azure aboneliğiyle Ilişkili Azure AD KIRACı kimliği ile değiştirin. Kiracı KIMLIĞINI bulmanız gerekiyorsa, Dizin/kiracı KIMLIĞINI almak için hesap adınızın üzerine gelebilirler veya Azure portal **> DIZIN kimliği > Azure Active Directory** seçebilirsiniz.

   - `curl https://enterpriseregistration.windows.net/ -D -`
   - `curl https://device.login.microsoftonline.com/ -D -`
   - `curl https://pas.windows.net/ -D -`

1. Komutlardan herhangi biri "ana bilgisayar çözümlenemedi" ile başarısız olursa `<URL>` , VM tarafından kullanılmakta olan DNS sunucusunu öğrenmek için bu komutu çalıştırmayı deneyin.
   
   `nslookup <URL>`

   > [!NOTE] 
   > `<URL>`Uç noktalar tarafından kullanılan tam etki alanı adlarıyla değiştirin, örneğin `login.microsoftonline.com` .

1. Daha sonra, genel bir DNS sunucusu belirtme komutunun başarılı olmasına izin verir:

   `nslookup <URL> 208.67.222.222`

1. Gerekirse, Azure VM 'nin ait olduğu ağ güvenlik grubuna atanan DNS sunucusunu değiştirin.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>Sorun 3: AADLoginForWindows uzantısı, çıkış kodu ile yüklenemedi: 51

Çıkış kodu 51 "Bu uzantı, sanal makinenin işletim sisteminde desteklenmiyor" olarak çevrilir.

Genel önizlemede, AADLoginForWindows uzantısının yalnızca Windows Server 2019 veya Windows 10 (derleme 1809 veya üzeri) üzerinde yüklü olması amaçlanmıştır. Windows sürümünün desteklendiğinden emin olun. Windows derlemesi desteklenmiyorsa, VM uzantısını kaldırın.

### <a name="troubleshoot-sign-in-issues"></a>Oturum açma sorunlarını giderme

Azure AD kimlik bilgileriyle RDP kullanmaya çalıştığınızda oluşan bazı yaygın hatalar, hiçbir Azure rolü atanmamış, yetkisiz istemci veya 2FA oturum açma yöntemi gerekli değildir. Bu sorunları gidermek için aşağıdaki bilgileri kullanın.

Cihaz ve SSO durumu çalıştırılarak görüntülenebilir `dsregcmd /status` . Amaç, cihaz durumunun olarak gösterilmesi `AzureAdJoined : YES` ve `SSO State` gösterilmesi içindir `AzureAdPrt : YES` .

Ayrıca, Azure AD hesaplarını kullanan RDP oturum açma olay günlüğü altında Olay Görüntüleyicisi 'nde yakalanır `AAD\Operational` .

#### <a name="azure-role-not-assigned"></a>Azure rolü atanmadı

Sanal makinenize Uzak Masaüstü bağlantısı başlattığınızda aşağıdaki hata iletisini görürseniz: 

- Hesabınız, bu cihazı kullanmanızı engelleyecek şekilde yapılandırıldı. Daha fazla bilgi için sistem yöneticinize başvurun.

![Hesabınız, bu cihazı kullanmanızı engelleyecek şekilde yapılandırıldı.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

VM için, sanal makine Yöneticisi oturum açma veya sanal makine Kullanıcı oturum açma rolü veren [Azure RBAC ilkelerini yapılandırdığınızdan](../../virtual-machines/linux/login-using-aad.md) emin olun:

> [!NOTE]
> Azure rol atamaları ile ilgili sorunlarla karşılaşırsanız bkz. [Azure RBAC sorunlarını giderme](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit).
 
#### <a name="unauthorized-client"></a>Yetkisiz istemci

Sanal makinenize Uzak Masaüstü bağlantısı başlattığınızda aşağıdaki hata iletisini görürseniz: 

- Kimlik bilgileriniz çalışmadı.

![Kimlik bilgileriniz çalışmadı](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Uzak Masaüstü bağlantısını başlatmak için kullandığınız Windows 10 bilgisayarının Azure AD 'ye katılmış veya karma Azure AD 'nin, sanal makinenizin katıldığı Azure AD dizinine katılmış olduğu bir bağlantı olduğunu doğrulayın. Cihaz kimliği hakkında daha fazla bilgi için, [cihaz kimliği nedir](./overview.md)makalesine bakın.

> [!NOTE]
> Windows 10 derleme 20H1, VM 'nize RDP bağlantısı başlatacak bir Azure AD kayıtlı BILGISAYARı için destek eklendi. VM 'nize yönelik bağlantıları başlatmak üzere RDP istemcisi olarak Azure AD 'ye kayıtlı (Azure AD 'ye katılmış veya hibrit Azure AD 'ye katılmış) bilgisayar kullanırken, kimlik bilgilerini `AzureAD\UPN` (örneğin,) biçiminde girmeniz gerekir `AzureAD\john@contoso.com` .

Azure AD JOIN işlemi tamamlandıktan sonra AADLoginForWindows uzantısının kaldırılmadığından emin olun.

Ayrıca, "ağ güvenliği: güvenlik ilkesi 'nin bu bilgisayara çevrimiçi kimlikleri kullanmasına Izin ver" güvenlik ilkesinin hem sunucuda **hem** de istemcide etkinleştirildiğinden emin olun.
 
#### <a name="mfa-sign-in-method-required"></a>MFA oturum açma yöntemi gerekli

Sanal makinenize Uzak Masaüstü bağlantısı başlattığınızda aşağıdaki hata iletisini görürseniz: 

- Kullanmaya çalıştığınız oturum açma yöntemine izin verilmiyor. Farklı bir oturum açma yöntemi deneyin veya sistem yöneticinize başvurun.

![Kullanmaya çalıştığınız oturum açma yöntemine izin verilmiyor.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Kaynağa erişmeden önce Multi-Factor Authentication (MFA) gerektiren bir koşullu erişim ilkesi yapılandırdıysanız, sanal makinemize Uzak Masaüstü bağlantısı 'nı Başlatan Windows 10 BILGISAYARıN Windows Hello gibi güçlü bir kimlik doğrulama yöntemi kullanarak oturum açtığından emin olmanız gerekir. Uzak Masaüstü bağlantınız için güçlü bir kimlik doğrulama yöntemi kullanmıyorsanız, önceki hatayı görürsünüz.

Iş için Windows Hello dağıtmadıysanız ve bu şimdilik bir seçenek değilse, MFA gereksinimini hariç tutmak için "Azure Windows VM oturum açma" uygulamasını dışlayan koşullu erişim ilkesini yapılandırarak MFA gereksinimini hariç kılabilirsiniz. Iş için Windows Hello hakkında daha fazla bilgi edinmek için bkz. [iş Için Windows Hello 'Ya genel bakış](/windows/security/identity-protection/hello-for-business/hello-identity-verification).

> [!NOTE]
> Windows 10 Windows 10 sürüm 1809 ' de RDP ile Iş için Windows Hello PIN kimlik doğrulaması desteklenir, ancak RDP ile biyometrik kimlik doğrulaması desteği eklenmiştir. RDP sırasında Iş için Windows Hello kimlik doğrulaması kullanılması yalnızca sertifika güven modeli kullanan dağıtımlar için kullanılabilir ve şu anda anahtar güven modeli için kullanılamaz.
 
## <a name="preview-feedback"></a>Önizleme geri bildirimi

Bu önizleme özelliğiyle ilgili geri bildirimlerinizi paylaşabilirsiniz veya [Azure AD geri bildirim forumundan](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)kullanarak sorunları bildirin.

## <a name="next-steps"></a>Sonraki adımlar

Azure Active Directory hakkında daha fazla bilgi için bkz. [Azure Active Directory nedir](../fundamentals/active-directory-whatis.md).

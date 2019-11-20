---
title: Azure 'da Azure Active Directory kullanarak Windows sanal makinesinde oturum açma (Önizleme)
description: Windows çalıştıran bir Azure VM 'de Azure AD oturum açma
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd50ca8b81b933a61a67ac36db6a656791a8121f
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832864"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Azure 'da Azure Active Directory kimlik doğrulaması (Önizleme) kullanarak Windows sanal makinesinde oturum açma

Kuruluşlar artık **Windows Server 2019 Datacenter Edition** veya **Windows 10 1809** ve üstünü çalıştıran Azure sanal makineleri (vm 'ler) için Azure Active Directory (ad) kimlik doğrulaması kullanabilir. VM 'Lerde kimlik doğrulaması yapmak için Azure AD 'nin kullanılması, ilkeleri merkezi olarak denetleme ve zorunlu kılmak için bir yol sağlar. Azure rol tabanlı Access Control (RBAC) ve Azure AD koşullu erişim gibi araçlar, bir VM 'ye kimlerin erişebileceğini denetlemenize olanak tanır. Bu makalede, Azure AD kimlik doğrulamasını kullanmak üzere bir Windows Server 2019 VM oluşturma ve yapılandırma açıklanmaktadır.

|     |
| --- |
| Azure Windows VM 'Leri için Azure AD oturum açma, Azure Active Directory genel bir önizleme özelliğidir. Önizlemeler hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Azure AD kimlik doğrulamasını kullanarak Azure 'da Windows VM 'lerde oturum açmak için aşağıdakiler de dahil olmak üzere birçok avantaj vardır:

- Normalde kullandığınız Federasyon veya yönetilen Azure AD kimlik bilgilerini kullanın.
- Artık yerel yönetici hesaplarını yönetmek zorunda değildir.
- Azure RBAC, ihtiyaç duymak üzere VM 'lere uygun erişim izni vermenizi ve artık gerekli olmadığında kaldırmanızı sağlar.
- Bir sanal makineye erişime izin vermeden önce Azure AD koşullu erişimi, şöyle ek gereksinimler uygulayabilir: 
   - Multi-factor authentication
   - Oturum açma riski
- Azure tabanlı Windows VM 'Leri için Azure AD JOIN 'i otomatikleştirin ve ölçeklendirin.

## <a name="requirements"></a>Gereksinimler

### <a name="supported-azure-regions-and-windows-distributions"></a>Desteklenen Azure bölgeleri ve Windows dağıtımları

Aşağıdaki Windows dağıtımları Şu anda bu özelliğin önizlemesi sırasında desteklenmektedir:

- Windows Server 2019 Datacenter
- Windows 10 1809 ve üzeri

Şu Azure bölgeleri Şu anda bu özelliğin önizlemesi sırasında desteklenmektedir:

- Tüm Azure genel bölgeleri

> [!IMPORTANT]
> Bu önizleme özelliğini kullanmak için, desteklenen bir Windows dağıtımını ve desteklenen bir Azure bölgesinde dağıtın. Bu özellik şu anda Azure Kamu veya bağımsız bulutlarda desteklenmiyor.

### <a name="network-requirements"></a>Ağ gereksinimleri

Azure 'da Windows sanal makinelerinize yönelik Azure AD kimlik doğrulamasını etkinleştirmek için, VM 'Lerin ağ yapılandırmanızın TCP bağlantı noktası 443 üzerinden aşağıdaki uç noktalara giden erişime izin verdiğinden emin olmanız gerekir:

- https://enterpriseregistration.windows.net
- https://login.microsoftonline.com
- https://device.login.microsoftonline.com
- https://pas.windows.net

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Azure 'da Windows VM için Azure AD oturum açmayı etkinleştirme

Azure 'da Windows VM 'de Azure AD oturum açma 'yı kullanmak için, önce Windows VM 'niz için Azure AD oturum açma seçeneğini etkinleştirmeniz, sonra VM 'de oturum açma yetkisi olan kullanıcılar için RBAC rol atamalarını yapılandırmanız gerekir.
Windows VM 'niz için Azure AD oturum açma özelliğini etkinleştirebilmenizin birden çok yolu vardır:

- Windows VM oluştururken Azure portal deneyimini kullanma
- Windows VM oluştururken veya mevcut bir Windows VM için Azure Cloud Shell deneyimini kullanma

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Azure AD oturum açma özelliğini etkinleştirmek için Azure portal VM oluşturma deneyimi kullanma

Windows Server 2019 Datacenter veya Windows 10 1809 ve üzeri VM görüntüleri için Azure AD oturum açmayı etkinleştirebilirsiniz. 

Azure AD oturum açma ile Azure 'da bir Windows Server 2019 Datacenter VM oluşturmak için: 

1. VM oluşturma erişimi olan bir hesapla [Azure Portal](https://portal.azure.com)oturum açın ve **+ kaynak oluştur**' u seçin.
1. Market arama çubuğunu aramak için **Windows Server** yazın.
   1. **Windows Server** ' a tıklayın ve yazılım planı seç açılan listesinden **Windows Server 2019 Datacenter** ' ı seçin.
   1. **Oluştur**'a tıklayın.
1. "Yönetim" sekmesinde, Azure Active Directory bölümü altındaki **AAD kimlik bilgileri (Önizleme) Ile** **Açık '** a oturum açma seçeneğini etkinleştirin.
1. Kimlik bölümünün altında **sistem tarafından atanan yönetilen kimliğin** **Açık**olarak ayarlandığından emin olun. Bu eylem, Azure AD kimlik bilgileriyle oturum açma özelliğini etkinleştirdikten sonra otomatik olarak gerçekleşmelidir.
1. Sanal makine oluşturma deneyiminden geri dönün. Bu önizleme sırasında, VM için bir Yönetici Kullanıcı adı ve parola oluşturmanız gerekecektir.

![Azure AD kimlik bilgileriyle oturum açma VM oluşturma](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Azure AD kimlik bilgilerinizi kullanarak VM 'de oturum açmak için öncelikle aşağıdaki bölümlerden birinde açıklandığı gibi VM için rol atamalarını yapılandırmanız gerekir.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Azure AD oturum açma özelliğini etkinleştirmek için Azure Cloud Shell deneyimini kullanma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın kullanılan Azure araçları hesabınızla kullanmanız için Cloud Shell'de önceden yüklenir ve yapılandırılır. Kodu kopyalamak için Kopyala düğmesini seçin, Cloud Shell yapıştırın ve sonra çalıştırmak için ENTER tuşuna basın. Cloud Shell’i açmanın birkaç yolu vardır:

Bir kod bloğunun sağ üst köşesinde deneyin öğesini seçin.
Cloud Shell’i tarayıcınızda açın.
[Azure Portal](https://portal.azure.com)sağ üst köşesindeki menüdeki Cloud Shell düğmesini seçin.

CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu makale, Azure CLı sürüm 2.0.31 veya üstünü çalıştırıyor olmanızı gerektirir. Sürümü bulmak için az --version komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI 'Yı yüklemeye](https://docs.microsoft.com/cli/azure/install-azure-cli)yönelik makale.

1. [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) ile bir kaynak grubu oluşturun. 
1. Desteklenen bir bölgede desteklenen bir dağıtım kullanarak [az VM Create](https://docs.microsoft.com/cli/azure/vm#az-vm-create) Ile bir VM oluşturun. 
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

VM’yi ve destekleyici kaynakları oluşturmak birkaç dakika sürer.

Son olarak, Azure AD oturum açma VM uzantısını yükleyerek Windows VM için Azure AD oturum açma özelliğini etkinleştirin. VM uzantıları, Azure sanal makinelerinde dağıtım sonrası yapılandırma ve otomasyon görevleri sağlayan küçük uygulamalardır. MyResourceGroup kaynak grubundaki myVM adlı VM 'ye AADLoginForWindows uzantısını yüklemek için [az VM Extension](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) set komutunu kullanın:

> [!NOTE]
> Azure AD kimlik doğrulaması için etkinleştirmek üzere, mevcut bir Windows Server 2019 veya Windows 10 1809 ve sonraki bir sanal makineye AADLoginForWindows uzantısını yükleyebilirsiniz. AZ CLı örneği aşağıda gösterilmiştir.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

Uzantı VM 'ye yüklendikten sonra `Succeeded` `provisioningState` gösterilir.

## <a name="configure-role-assignments-for-the-vm"></a>VM için rol atamalarını yapılandırma

VM 'yi oluşturduğunuza göre, VM 'de kimlerin oturum Açabili belirleyebilmek için Azure RBAC ilkesini yapılandırmanız gerekir. VM oturum açma yetkisini yetkilendirmek için iki RBAC rolü kullanılır:

- **Sanal Makine Yöneticisi oturum açma**: Bu role atanmış kullanıcılar yönetici ayrıcalıklarına sahip bir Azure sanal makinesinde oturum açabilir.
- **Sanal makine Kullanıcı oturumu açma**: Bu role atanmış kullanıcılar, normal kullanıcı ayrıcalıklarına sahip bir Azure sanal makinesinde oturum açabilir.

> [!NOTE]
> Bir kullanıcının VM 'de RDP üzerinden oturum açmasına izin vermek için, sanal makine Yöneticisi oturum açma veya sanal makine Kullanıcı oturum açma rolünü atamanız gerekir. Bir VM için atanan sahip veya katkıda bulunan rollerinin bulunduğu bir Azure kullanıcısının, sanal makinede RDP üzerinden oturum açma ayrıcalıkları otomatik olarak yoktur. Bu, sanal makineleri denetleyen kişi kümesi arasında, sanal makinelere erişebilen kişiler kümesiyle denetlenen ayırma sağlamaktır.

VM için rol atamalarını yapılandırabileceğiniz birçok yol vardır:

- Azure AD Portal deneyimini kullanma
- Azure Cloud Shell deneyimini kullanma

### <a name="using-azure-ad-portal-experience"></a>Azure AD Portal deneyimini kullanma

Azure AD özellikli Windows Server 2019 Datacenter VM 'leriniz için rol atamalarını yapılandırmak için:

1. Belirli bir sanal makineye genel bakış sayfasına gidin
1. Menü seçeneklerinden **erişim denetimi (IAM)** seçeneğini belirleyin
1. Rol ataması Ekle bölmesini açmak için **Ekle**, **rol ataması Ekle** ' yi seçin.
1. **Rol** açılan listesinde, **sanal makine yöneticisi oturumu** veya **sanal makine Kullanıcı oturumu açma**gibi bir rol seçin.
1. **Seç** alanında, bir Kullanıcı, Grup, hizmet sorumlusu veya yönetilen kimlik seçin. Listede güvenlik sorumlusunu görmüyorsanız **Seç** kutusuna giriş yaparak dizinde görünen ad, e-posta adresi ve nesne tanımlayıcısı arayabilirsiniz.
1. Rolü atamak için **Kaydet**' i seçin.

Birkaç dakika sonra, güvenlik sorumlusu seçili kapsamda role atanır.

![VM 'ye erişecek kullanıcılara roller atama](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Azure Cloud Shell deneyimini kullanma

Aşağıdaki örnek, geçerli Azure kullanıcılarınız için sanal makine Yöneticisi oturum açma rolünü VM 'ye atamak için [az role atama Create](https://docs.microsoft.com/cli/azure/role/assignment#az-role-assignment-create) ' i kullanır. Etkin Azure hesabınızın Kullanıcı adı [az Account Show](https://docs.microsoft.com/cli/azure/account#az-account-show)komutuyla alınır ve kapsam, [az VM Show](https://docs.microsoft.com/cli/azure/vm#az-vm-show)ile ÖNCEKI bir adımda oluşturulan VM 'ye ayarlanır. Kapsam Ayrıca bir kaynak grubuna veya abonelik düzeyine atanabilir ve normal RBAC devralma izinleri geçerlidir. Daha fazla bilgi için bkz. [rol tabanlı erişim denetimleri](../../virtual-machines/linux/login-using-aad.md).

```AzureCLI
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> AAD etki alanı ve oturum açma Kullanıcı adı etki alanınız eşleşmiyorsa, `--assignee`için yalnızca Kullanıcı adını değil, `--assignee-object-id`Kullanıcı hesabınızın nesne KIMLIĞINI belirtmeniz gerekir. Kullanıcı hesabınızın nesne KIMLIĞINI [az ad kullanıcı listesi](https://docs.microsoft.com/cli/azure/ad/user#az-ad-user-list)ile elde edebilirsiniz.

Azure abonelik kaynaklarınıza erişimi yönetmek için RBAC kullanma hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [RBAC ve Azure CLı kullanarak Azure kaynaklarına erişimi yönetme](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)
- [RBAC ve Azure portalını kullanarak Azure kaynaklarına erişimi yönetme](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
- [RBAC ve Azure PowerShell kullanarak Azure kaynaklarına erişimi yönetin](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Azure AD kimlik bilgilerini kullanarak bir Windows VM 'de oturum açma

> [!IMPORTANT]
> Azure AD 'ye katılmış olan sanal makinelere uzak bağlantıya yalnızca Azure AD 'ye katılmış veya karma Azure AD olan Windows 10 bilgisayarlardan VM ile **aynı** dizine katılmış izin verilir. Ayrıca, Azure AD kimlik bilgilerini kullanarak RDP 'ye Kullanıcı, sanal makine Yöneticisi oturum açma veya sanal makine Kullanıcı oturum açma bilgilerinden birine ait olmalıdır.

Azure AD 'yi kullanarak Windows Server 2019 sanal makinenizde oturum açmak için: 

1. Azure AD oturum açma özelliği ile etkinleştirilen sanal makinenin genel bakış sayfasına gidin.
1. **Bağlan** ' ı seçerek sanal makine bağlantısı dikey penceresini açın.
1. **RDP dosyasını indir**' i seçin.
1. Uzak Masaüstü Bağlantısı istemcisini başlatmak için **Aç** ' ı seçin.
1. Windows oturum açma iletişim kutusunu başlatmak için **Bağlan** ' ı seçin.
1. Azure AD kimlik bilgilerinizi kullanarak oturum açın.

Artık Windows Server 2019 Azure sanal makinesinde, atanan VM kullanıcısı veya VM Yöneticisi gibi rol izinleriyle oturum açtınız. 

> [!NOTE]
> ' İ kaydedebilirsiniz. RDP dosyası, Azure portal sanal makineye genel bakış sayfasına gitmek yerine sanal makinenize yerel olarak Uzak Masaüstü bağlantıları başlatmak için ve Connect seçeneğini kullanmaktır.

## <a name="troubleshoot"></a>Sorun giderme

### <a name="troubleshoot-deployment-issues"></a>Dağıtım sorunlarını giderme

VM 'nin Azure AD JOIN işlemini tamamlaması için AADLoginForWindows uzantısının başarıyla yüklenmesi gerekir. VM Uzantısı doğru yüklenemediğinde aşağıdaki adımları gerçekleştirin.

1. Yerel yönetici hesabını kullanarak VM 'ye RDP ile ve altındaki CommandExecution. log dosyasını inceleyin.  
   
   C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0. 

   > [!NOTE]
   > Uzantı ilk hatadan sonra yeniden başlarsa, dağıtım hatasına sahip günlük CommandExecution_YYYYMMDDHHMMSSSSS. log olarak kaydedilir. 

1. VM 'de bir komut istemi açın ve Azure ana bilgisayarında çalışan Instance Metadata Service (ıMDS) uç noktasına karşı bu sorguları doğrulayın:

   | Çalıştırılacak komut | Beklenen çıkış |
   | --- | --- |
   | kıvrık-H meta verileri: true "http://169.254.169.254/metadata/instance?api-version=2017-08-01" | Azure VM ile ilgili doğru bilgiler |
   | kıvrık-H meta verileri: true "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01" | Azure aboneliğiyle ilişkili geçerli kiracı KIMLIĞI |
   | kıvrık-H meta verileri: true "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01" | Bu VM 'ye atanmış yönetilen kimlik için Azure Active Directory tarafından verilen geçerli erişim belirteci |

   > [!NOTE]
   > Erişim belirtecinin kodu, [http://calebb.net/](http://calebb.net/)gibi bir araç kullanılarak çözülebilir. Erişim belirtecindeki "AppID" nin VM 'ye atanan yönetilen kimlikle eşleştiğini doğrulayın.

1. Komut satırını kullanarak gerekli uç noktaların VM 'den erişilebilir olduğundan emin olun:
   
   - kıvra https://login.microsoftonline.com/-D –
   - https://login.microsoftonline.com/`<TenantID>`/-D-

   > [!NOTE]
   > `<TenantID>`, Azure aboneliği ile ilişkili Azure AD kiracı KIMLIĞI ile değiştirin.

   - https://enterpriseregistration.windows.net/-D-
   - https://device.login.microsoftonline.com/-D-
   - https://pas.windows.net/-D-

1. `dsregcmd /status`çalıştırılarak cihaz durumu görüntülenebilir. Amaç, cihaz durumunun `AzureAdJoined : YES`olarak gösterilmesi içindir.

   > [!NOTE]
   > Azure AD JOIN etkinliği, Olay Görüntüleyicisi 'nde Kullanıcı aygıtı Registration\Admin günlüğü altında yakalanır.

AADLoginForWindows uzantısı belirli hata kodu ile başarısız olursa, aşağıdaki adımları uygulayabilirsiniz:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>Sorun 1: AADLoginForWindows uzantısı, Terminal hata kodu ' 1007 ' ve çıkış kodu:-2145648574 ile yüklenemez.

Uzantı Azure AD kiracı bilgilerini sorgulayamadığından, bu çıkış kodu DSREG_E_MSI_TENANTID_UNAVAILABLE 'e çevrilir.

1. Azure VM 'nin Instance Metadata Service Tenantıd 'yi alabildiğini doğrulayın.

   - Yerel yönetici olarak VM 'ye RDP ile ve bu komutu VM 'deki yükseltilmiş bir komut satırından çalıştırarak uç noktanın geçerli bir kiracı KIMLIĞI döndürdüğünden emin olun:
      
      - kıvrık-H meta verileri: true http://169.254.169.254/metadata/identity/info?api-version=2018-02-01

1. VM Yöneticisi AADLoginForWindows uzantısını yüklemeyi dener, ancak sistem tarafından atanmış bir yönetilen kimlik önce VM 'yi etkinleştirmedi. VM 'nin kimlik dikey penceresine gidin. Sistem atandı sekmesinden durumunun açık olarak yapıldığını doğrulayın.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>Sorun 2: AADLoginForWindows uzantısı, çıkış kodu ile yüklenemedi:-2145648607

Uzantı https://enterpriseregistration.windows.net uç noktasına ulaşamadığı için bu çıkış kodu DSREG_AUTOJOIN_DISC_FAILED 'e çevrilir.

1. Komut satırını kullanarak gerekli uç noktaların VM 'den erişilebilir olduğundan emin olun:

   - kıvra https://login.microsoftonline.com/-D –
   - https://login.microsoftonline.com/`<TenantID>`/-D-
   
   > [!NOTE]
   > `<TenantID>`, Azure aboneliği ile ilişkili Azure AD kiracı KIMLIĞI ile değiştirin. Kiracı KIMLIĞINI bulmanız gerekiyorsa, Dizin/kiracı KIMLIĞINI almak için hesap adınızın üzerine gelebilirler veya Azure portal > Dizin KIMLIĞI > Azure Active Directory seçebilirsiniz.

   - https://enterpriseregistration.windows.net/-D-
   - https://device.login.microsoftonline.com/-D-
   - https://pas.windows.net/-D-

1. Komutlardan herhangi biri "ana bilgisayar `<URL>`çözümlenemedi" ile başarısız olursa, VM tarafından kullanılmakta olan DNS sunucusunu öğrenmek için bu komutu çalıştırmayı deneyin.
   
   `nslookup <URL>`

   > [!NOTE] 
   > `<URL>`, uç noktalar tarafından kullanılan ve "login.microsoftonline.com" gibi tam etki alanı adlarıyla değiştirin.

1. Daha sonra, genel bir DNS sunucusu belirtme komutunun başarılı olmasına izin verir:

   `nslookup <URL> 208.67.222.222`

1. Gerekirse, Azure VM 'nin ait olduğu ağ güvenlik grubuna atanan DNS sunucusunu değiştirin.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>Sorun 3: AADLoginForWindows uzantısı, çıkış kodu ile yüklenemedi: 51

Çıkış kodu 51 "Bu uzantı, sanal makinenin işletim sisteminde desteklenmiyor" olarak çevrilir.

Genel önizlemede, AADLoginForWindows uzantısının yalnızca Windows Server 2019 veya Windows 10 (derleme 1809 veya üzeri) üzerinde yüklü olması amaçlanmıştır. Windows sürümünün desteklendiğinden emin olun. Windows derlemesi desteklenmiyorsa, VM uzantısını kaldırın.

### <a name="troubleshoot-sign-in-issues"></a>Oturum açma sorunlarını giderme

Azure AD kimlik bilgileriyle RDP kullanmaya çalıştığınızda karşılaşılan bazı yaygın hatalar, hiçbir RBAC rolü atanmamış, yetkisiz istemci veya 2FA oturum açma yöntemi gerekli değildir. Bu sorunları gidermek için aşağıdaki bilgileri kullanın.

`dsregcmd /status`çalıştırılarak cihaz ve SSO durumu görüntülenebilir. Amaç, cihaz durumunun `AzureAdJoined : YES` olarak gösterilmesi ve `AzureAdPrt : YES`göstermek için `SSO State`.

Ayrıca, Azure AD hesaplarını kullanan RDP oturum açma işlemi, Aad\işletimsel olay günlükleri altında Olay Görüntüleyicisi 'nde yakalanır.

#### <a name="rbac-role-not-assigned"></a>RBAC rolü atanmadı

Sanal makinenize Uzak Masaüstü bağlantısı başlattığınızda aşağıdaki hata iletisini görürseniz: 

- Hesabınız, bu cihazı kullanmanızı engelleyecek şekilde yapılandırıldı. Daha fazla bilgi için sistem yöneticinize başvurun

![Hesabınız, bu cihazı kullanmanızı engelleyecek şekilde yapılandırıldı.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

VM için sanal makine yöneticisinin oturum açma veya sanal makine Kullanıcı oturum açma rolünü veren [RBAC ilkelerini yapılandırdığınızdan](../../virtual-machines/linux/login-using-aad.md) emin olun:
 
#### <a name="unauthorized-client"></a>Yetkisiz istemci

Sanal makinenize Uzak Masaüstü bağlantısı başlattığınızda aşağıdaki hata iletisini görürseniz: 

- Kimlik bilgileriniz çalışmadı

![Kimlik bilgileriniz çalışmadı](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Uzak Masaüstü bağlantısını başlatmak için kullandığınız Windows 10 bilgisayarının Azure AD 'ye katılmış veya karma Azure AD 'nin, sanal makinenizin katıldığı Azure AD dizinine katılmış olduğu bir bağlantı olduğunu doğrulayın. Cihaz kimliği hakkında daha fazla bilgi için, [cihaz kimliği nedir](https://docs.microsoft.com/azure/active-directory/devices/overview)makalesine bakın.

> [!NOTE]
> Windows 10 20 H1, sanal makinenize Uzak Masaüstü bağlantısı başlatmak için Azure AD kayıtlı bılgısayar desteği ekler. Windows 10 ' un yeni özelliklerine göz atın ve yeni özellikleri araştırmak için Windows Insider programı 'na katılarak.

Ayrıca, Azure AD katılımı tamamlandıktan sonra AADLoginForWindows uzantısının kaldırılmadığından emin olun.
 
#### <a name="mfa-sign-in-method-required"></a>MFA oturum açma yöntemi gerekli

Sanal makinenize Uzak Masaüstü bağlantısı başlattığınızda aşağıdaki hata iletisini görürseniz: 

- Kullanmaya çalıştığınız oturum açma yöntemine izin verilmiyor. Farklı bir oturum açma yöntemi deneyin veya sistem yöneticinize başvurun.

![Kullanmaya çalıştığınız oturum açma yöntemine izin verilmiyor.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

RBAC kaynağına erişebilmek için MFA 'nın yapılmasını gerektiren bir koşullu erişim ilkesi yapılandırdıysanız, sanal makinenize yönelik uzak masaüstü bağlantısını başlatan Windows 10 BILGISAYARıN, bir güçlü kimlik doğrulama yöntemi kullanarak oturum açtığından emin olmanız gerekir. Windows Hello olarak. Uzak Masaüstü bağlantınız için güçlü bir kimlik doğrulama yöntemi kullanmıyorsanız, aşağıdaki hatayı görürsünüz.
 
## <a name="preview-feedback"></a>Önizleme geri bildirimi

Bu önizleme özelliğiyle ilgili geri bildirimlerinizi paylaşabilirsiniz veya [Azure AD geri bildirim forumundan](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)kullanarak sorunları bildirin.

## <a name="next-steps"></a>Sonraki adımlar
Azure Active Directory hakkında daha fazla bilgi için bkz. [Azure Active Directory nedir?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)

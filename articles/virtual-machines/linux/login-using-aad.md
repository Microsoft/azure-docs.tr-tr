---
title: Azure Active Directory kimlik bilgileriyle Linux VM 'de oturum açma
description: Azure Active Directory kimlik doğrulaması kullanarak oturum açmak için bir Linux VM oluşturma ve yapılandırma hakkında bilgi edinin.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/29/2019
ms.author: iainfou
ms.openlocfilehash: a67d3a9fb74b1a4f07fc4995c268bb40a84834f7
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035935"
---
# <a name="preview-log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>Önizleme: Azure Active Directory kimlik doğrulaması kullanarak Azure 'da Linux sanal makinesinde oturum açma

Azure 'daki Linux sanal makinelerinin (VM) güvenliğini artırmak için Azure Active Directory (AD) kimlik doğrulamasıyla tümleştirebilirsiniz. Linux VM 'Ler için Azure AD kimlik doğrulaması kullandığınızda, sanal makinelere erişime izin veren veya erişimi reddeden ilkeleri merkezi olarak kontrol edin ve zorlayabilirsiniz. Bu makalede, Azure AD kimlik doğrulamasını kullanmak için bir Linux VM oluşturma ve yapılandırma gösterilmektedir.


> [!IMPORTANT]
> Azure Active Directory kimlik doğrulaması şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> Bu özelliği, sınama sonrasında atmayı beklediğinizi bir test sanal makinesinde kullanın.
>


Azure AD kimlik doğrulamasını kullanarak Azure 'da Linux VM 'lerinde oturum açmak için aşağıdakiler de dahil olmak üzere birçok avantaj vardır:

- **Geliştirilmiş güvenlik:**
  - Azure Linux VM 'lerinde oturum açmak için kurumsal AD kimlik bilgilerinizi kullanabilirsiniz. Yerel yönetici hesapları oluşturmanız ve kimlik bilgisi ömrünü yönetmeniz gerekmez.
  - Yerel yönetici hesaplarında rahatlığını azaltarak kimlik bilgisi kaybı/hırsızlık hakkında endişelenmenize gerek kalmaz, zayıf kimlik bilgilerini yapılandıran kullanıcılar.
  - Azure AD dizininiz için yapılandırılan parola karmaşıklığı ve parola yaşam süresi ilkeleri, Linux sanal makinelerinin korunmasına da yardımcı olur.
  - Azure sanal makinelerinde oturum açmayı daha da güvenli hale getirmek için Multi-Factor Authentication 'ı yapılandırabilirsiniz.
  - Azure Active Directory ile Linux VM 'lerinde oturum açma özelliği, [Federasyon Hizmetleri](../../active-directory/hybrid/how-to-connect-fed-whatis.md)kullanan müşteriler için de kullanılabilir.

- **Sorunsuz işbirliği:** Rol tabanlı Access Control (RBAC) ile, belirli bir VM 'de, düzenli bir kullanıcı olarak veya yönetici ayrıcalıklarına sahip kimlerin oturum açmasını belirtebilirsiniz. Kullanıcılar ekibinize katılırsanız veya takımınızda ayrıldığınızda, uygun şekilde erişim sağlamak için VM RBAC ilkesini güncelleştirebilirsiniz. Bu deneyim, gereksiz SSH ortak anahtarlarını kaldırmak için VM 'Leri Temizleme zorunluluktan çok daha basittir. Çalışanlar kuruluştan ayrıldığında ve Kullanıcı hesapları devre dışı bırakıldığında veya Azure AD 'den kaldırıldığında, kaynaklarınıza artık erişemez.

## <a name="supported-azure-regions-and-linux-distributions"></a>Desteklenen Azure bölgeleri ve Linux dağıtımları

Şu Linux dağıtımları Şu anda bu özelliğin önizlemesi sırasında desteklenmektedir:

| Dağıtım | Sürüm |
| --- | --- |
| CentOS | CentOS 6, CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE artık 42,3 |
| RedHat Enterprise Linux | RHEL 6, RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14,04 LTS, Ubuntu Server 16,04 ve Ubuntu Server 18,04 |


Şu Azure bölgeleri Şu anda bu özelliğin önizlemesi sırasında desteklenmektedir:

- Tüm genel Azure bölgeleri

>[!IMPORTANT]
> Bu önizleme özelliğini kullanmak için desteklenen bir Linux dağıtımını ve desteklenen bir Azure bölgesini dağıtın. Özellik, Azure Kamu veya bağımsız bulutlarda desteklenmez.


CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğreticide, Azure CLı sürüm 2.0.31 veya üstünü çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme]( /cli/azure/install-azure-cli).

## <a name="create-a-linux-virtual-machine"></a>Linux sanal makinesi oluşturma

[Az Group Create](/cli/azure/group#az-group-create)ile bir kaynak grubu oluşturun ve ardından desteklenen bir ve desteklenen bir bölgede [az VM Create](/cli/azure/vm#az-vm-create) ile bir VM oluşturun. Aşağıdaki örnek, *Ubuntu 16,04 LTS* kullanan *myvm* adlı bir VM 'yi *Güneydoğu bir ABD* bölgesindeki *myresourcegroup* adlı bir kaynak grubuna dağıtır. Aşağıdaki örneklerde, gerektiğinde kendi kaynak grubunuzu ve sanal makine adlarınızı sağlayabilirsiniz.

```azurecli-interactive
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM’yi ve destekleyici kaynakları oluşturmak birkaç dakika sürer.

## <a name="install-the-azure-ad-login-vm-extension"></a>Azure AD oturum açma VM uzantısını yükler

> [!NOTE]
> Bu uzantıyı daha önce oluşturulmuş bir VM 'ye dağıtırsanız, makinenin en az 1 GB belleğe sahip olduğundan emin olun, aksi takdirde uzantının yüklemesi başarısız olur

Azure AD kimlik bilgileriyle bir Linux VM 'de oturum açmak için Azure Active Directory oturum açma VM uzantısını yükler. VM uzantıları, Azure sanal makinelerinde dağıtım sonrası yapılandırma ve otomasyon görevleri sağlayan küçük uygulamalardır. *Aadloginforlinux* uzantısını *myresourcegroup* kaynak GRUBUNDAKI *myvm* adlı VM 'ye yüklemek için [az VM Extension set](/cli/azure/vm/extension#az-vm-extension-set) komutunu kullanın:

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

Uzantı VM 'ye başarıyla yüklendikten sonra, *başarılı* olan *provisioningstate* gösterilmektedir.

## <a name="configure-role-assignments-for-the-vm"></a>VM için rol atamalarını yapılandırma

Azure rol tabanlı Access Control (RBAC) ilkesi, VM 'de kimlerin oturum Açabili belirler. VM oturum açma yetkisini yetkilendirmek için iki RBAC rolü kullanılır:

- **Sanal Makine Yöneticisi oturum açma**: Bu role atanmış kullanıcılar, Windows Yöneticisi veya Linux kök kullanıcı ayrıcalıklarına sahip bir Azure sanal makinesinde oturum açabilir.
- **Sanal makine Kullanıcı oturumu açma**: Bu role atanmış kullanıcılar, normal kullanıcı ayrıcalıklarına sahip bir Azure sanal makinesinde oturum açabilir.

> [!NOTE]
> Bir kullanıcının SSH üzerinden VM 'de oturum açmasına izin vermek için, *Sanal Makine Yöneticisi oturum açma* veya *sanal makine Kullanıcı oturum açma* rolünü atamanız gerekir. Bir VM için atanan *sahip* veya *katkıda* bulunan rollerinin bulunduğu bir Azure KULLANıCıSıNıN, sanal makinede SSH üzerinden oturum açma ayrıcalıkları otomatik olarak yoktur.

Aşağıdaki örnek, geçerli Azure kullanıcılarınız için *Sanal Makine Yöneticisi oturum açma* rolünü VM 'ye atamak için [az role atama Create](/cli/azure/role/assignment#az-role-assignment-create) ' i kullanır. Etkin Azure hesabınızın Kullanıcı adı [az Account Show](/cli/azure/account#az-account-show)komutuyla alınır ve *kapsam* , [az VM Show](/cli/azure/vm#az-vm-show)ile önceki bir adımda oluşturulan VM 'ye ayarlanır. Kapsam Ayrıca bir kaynak grubuna veya abonelik düzeyine atanabilir ve normal RBAC devralma izinleri geçerlidir. Daha fazla bilgi için bkz. [rol tabanlı erişim denetimleri](../../role-based-access-control/overview.md)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> AAD etki alanı ve oturum açma Kullanıcı adı etki alanınız eşleşmiyorsa,--atane *-nesnesi-kimliği*ile Kullanıcı HESABıNıZıN nesne kimliğini belirtmeniz *gerekir.* Kullanıcı hesabınızın nesne KIMLIĞINI [az ad kullanıcı listesi](/cli/azure/ad/user#az-ad-user-list)ile elde edebilirsiniz.

Azure abonelik kaynaklarınıza erişimi yönetmek için RBAC kullanma hakkında daha fazla bilgi için bkz. [Azure CLI](../../role-based-access-control/role-assignments-cli.md), [Azure Portal](../../role-based-access-control/role-assignments-portal.md)veya [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)kullanma.

Ayrıca, belirli bir kullanıcının Linux sanal makinesinde oturum açması için çok faktörlü kimlik doğrulaması gerektirecek şekilde Azure AD 'yi yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Bulutta Azure Multi-Factor Authentication kullanmaya başlama](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="log-in-to-the-linux-virtual-machine"></a>Linux sanal makinesinde oturum açma

İlk olarak, [az VM Show](/cli/azure/vm#az-vm-show)komutuyla sanal MAKINENIZIN genel IP adresini görüntüleyin:

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Azure AD kimlik bilgilerinizi kullanarak Azure Linux sanal makinesinde oturum açın. `-l` parametresi, kendi Azure AD hesap adresinizi belirtmenize olanak tanır. Örnek hesabı kendi ile değiştirin. Hesap adresleri tümüyle küçük harfle girilmelidir. Örnek IP adresini önceki komuttan VM 'nizin genel IP adresi ile değiştirin.

```azurecli-interactive
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

Azure AD 'de [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)' de bir kerelik kullanım kodu ile oturum açmanız istenir. Bir kerelik kullanım kodunu kopyalayıp cihaz oturum açma sayfasına yapıştırın.

İstendiğinde, oturum açma sayfasında Azure AD oturum açma kimlik bilgilerinizi girin. 

Kimliği başarıyla doğrulandığında, Web tarayıcısında aşağıdaki ileti gösterilir: `You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.`

Tarayıcı penceresini kapatın, SSH istemine geri dönün ve **ENTER** tuşuna basın. 

Azure Linux sanal makinesinde, atanan *VM kullanıcısı* veya *VM Yöneticisi*gibi rol izinleriyle oturum açtınız. Kullanıcı hesabınıza *Sanal Makine Yöneticisi oturum açma* rolü atanırsa, kök ayrıcalıkları gerektiren komutları çalıştırmak için `sudo` kullanabilirsiniz.

## <a name="sudo-and-aad-login"></a>Sudo ve AAD oturumu açma

Sudo 'yi ilk kez çalıştırdığınızda ikinci kez kimlik doğrulaması yapmanız istenir. Sudo çalıştırmak için tekrar kimlik doğrulaması yapmak istemiyorsanız, sudoers dosyanızı `/etc/sudoers.d/aad_admins` düzenleyebilir ve bu satırı değiştirebilirsiniz:

```bash
%aad_admins ALL=(ALL) ALL
```
Bu satırla:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Oturum açma sorunlarını giderme

Azure AD kimlik bilgileriyle SSH kullanmaya çalıştığınızda oluşan bazı yaygın hatalar, atanmış RBAC rolleri ve oturum açmak için yinelenen istemler içerir. Bu sorunları düzeltmek için aşağıdaki bölümleri kullanın.

### <a name="access-denied-rbac-role-not-assigned"></a>Erişim reddedildi: RBAC rolü atanmadı

SSH isteminizde aşağıdaki hatayı görürseniz, VM için *sanal makine yöneticisi oturumu* veya *sanal makine Kullanıcı oturum açma* rolü veren RBAC ilkelerini yapılandırdığınızdan emin olun:

```bash
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>Devam eden SSH oturum açma istemleri

Bir Web tarayıcısında kimlik doğrulama adımını başarıyla tamamlayadıysanız, yeni bir kodla hemen oturum açmanız istenebilir. Bu hata genellikle, SSH isteminde belirttiğiniz oturum açma adı ile Azure AD 'de oturum açtığınız hesap arasında bir uyumsuzluk oluşur. Bu sorunu düzeltmek için:

- SSH isteminde belirttiğiniz oturum açma adının doğru olduğunu doğrulayın. Oturum açma adındaki bir yazım hatası, SSH isteminde belirttiğiniz oturum açma adı ile Azure AD 'de oturum açtığınız hesap arasında uyuşmazlık oluşmasına neden olabilir. Örneğin, *azureuser\@contoso.onmicrosoft.com*yerine *azuresuer\@contoso.onmicrosoft.com* yazdınız.
- Birden çok Kullanıcı hesabınız varsa, Azure AD 'de oturum açarken tarayıcı penceresinde farklı bir kullanıcı hesabı sağlamadığınızdan emin olun.
- Linux, büyük/küçük harfe duyarlı bir işletim sistemidir. 'Azureuser@contoso.onmicrosoft.com' ve 'azureuser@contoso.onmicrosoft.com' arasında bir farklılık var ve bu da uyuşmazlığa neden olabilir. SSH isteminde doğru büyük/küçük harf duyarlılığı ile UPN 'yi belirttiğinizden emin olun.

## <a name="preview-feedback"></a>Önizleme geri bildirimi

Bu önizleme özelliğiyle ilgili geri bildiriminizi paylaşma veya [Azure AD geri bildirim Forumu](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) 'nda bu raporu kullanarak sorun bildirme

## <a name="next-steps"></a>Sonraki adımlar

Azure Active Directory hakkında daha fazla bilgi için bkz. [Azure Active Directory nedir?](../../active-directory/fundamentals/active-directory-whatis.md)

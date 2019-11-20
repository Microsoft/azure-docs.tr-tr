---
title: Azure önkoşulları 'nda OpenShift kapsayıcı platformu 3,11
description: Azure 'da OpenShift kapsayıcı platformu 3,11 dağıtma önkoşulları.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/23/2019
ms.author: haroldw
ms.openlocfilehash: 069561c4bed55bf6021b594d693e076ef8d313bd
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035468"
---
# <a name="common-prerequisites-for-deploying-openshift-container-platform-311-in-azure"></a>Azure 'da OpenShift kapsayıcı platformu 3,11 dağıtmaya yönelik genel Önkoşullar

Bu makalede, Azure 'da OpenShift kapsayıcı platformunu veya OKD 'yi dağıtmaya yönelik genel Önkoşullar açıklanmaktadır.

Openshıft yüklemesi, anormal PlayBook 'lar kullanır. Anerişilebilir, yükleme adımlarını tamamlamaya yönelik tüm küme konaklarına bağlanmak için Secure Shell (SSH) kullanır.

Anormal, uzak konaklara SSH bağlantısı yaptığında, bir parola giremiyorum. Bu nedenle, özel anahtarın kendisiyle ilişkili bir parolası (parola) olamaz veya dağıtım başarısız olur.

Sanal makineler (VM 'Ler) Azure Resource Manager şablonları aracılığıyla dağıtıldığı için, tüm VM 'lere erişim için aynı ortak anahtar kullanılır. Karşılık gelen özel anahtar, tüm PlayBook 'ları yürüten sanal makinede olmalıdır. Bu eylemi güvenli bir şekilde gerçekleştirmek için, özel anahtarı VM 'ye geçirmek üzere bir Azure Anahtar Kasası kullanılır.

Kapsayıcılar için kalıcı depolama gereksinimi varsa kalıcı birimler gereklidir. OpenShift kalıcı birimler için Azure sanal sabit disklerini (VHD) destekler, ancak Azure 'un önce bulut sağlayıcısı olarak yapılandırılması gerekir.

Bu modelde, OpenShift:

- Bir Azure depolama hesabında veya yönetilen diskte bir VHD nesnesi oluşturur.
- VHD 'yi bir VM 'ye takar ve birimi biçimlendirir.
- Birimi Pod 'a bağlar.

Bu yapılandırmanın çalışması için, OpenShift 'in Azure 'da bu görevleri gerçekleştirmek için izinleri olması gerekir. Bu amaçla bir hizmet sorumlusu kullanılır. Hizmet sorumlusu, kaynak izinleri verilen Azure Active Directory bir güvenlik hesabıdır.

Hizmet sorumlusunun, kümeyi oluşturan depolama hesaplarına ve VM 'lere erişimi olması gerekir. Tüm OpenShift kümesi kaynakları tek bir kaynak grubuna dağıtışsam, hizmet sorumlusuna bu kaynak grubuna izinler verilebilir.

Bu kılavuzda, önkoşullara ilişkin yapıların nasıl oluşturulacağı açıklanmaktadır.

> [!div class="checklist"]
> * OpenShift kümesi için SSH anahtarlarını yönetmek üzere bir Anahtar Kasası oluşturun.
> * Azure bulut sağlayıcısı tarafından kullanılmak üzere bir hizmet sorumlusu oluşturun.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma 
[Az Login](/cli/azure/reference-index) komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin veya Cloud Shell kullanmak için **dene** ' ye tıklayın.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[az group create](/cli/azure/group) komutuyla bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Anahtar kasasını barındırmak için adanmış bir kaynak grubu kullanmanız gerekir. Bu grup, OpenShift küme kaynaklarının dağıtıldığı kaynak grubundan ayrıdır.

Aşağıdaki örnek *eastus* konumunda *keyvaultrg* adlı bir kaynak grubu oluşturur:

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Bir anahtar kasası oluşturma
[Az keykasa Create](/cli/azure/keyvault) komutuyla küme için SSH anahtarlarını depolamak üzere bir Anahtar Kasası oluşturun. Anahtar Kasası adı genel olarak benzersiz olmalıdır ve şablon dağıtımı için etkinleştirilmeli ve dağıtım "KeyVaultParameterReferenceSecretRetrieveFailed" hatası ile başarısız olacak.

Aşağıdaki örnek, *keyvaultrg* kaynak grubunda *keykasası* adlı bir Anahtar Kasası oluşturur:

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>SSH anahtarı oluşturma 
OpenShift kümesine erişimin güvenliğini sağlamak için bir SSH anahtarı gereklidir. `ssh-keygen` komutunu kullanarak bir SSH anahtar çifti oluşturun (Linux veya macOS üzerinde):
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> SSH anahtar çiftiniz parola/parola içeremez.

Windows 'da SSH anahtarları hakkında daha fazla bilgi için bkz. [Windows 'DA SSH anahtarları oluşturma](/azure/virtual-machines/linux/ssh-from-windows). OpenSSH biçiminde özel anahtarı dışarı aktardığınızdan emin olun.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>SSH özel anahtarını Azure Key Vault depolama
OpenShift dağıtımı, OpenShift ana yöneticisine güvenli erişim sağlamak için oluşturduğunuz SSH anahtarını kullanır. Dağıtımı, SSH anahtarını güvenli bir şekilde almak üzere etkinleştirmek için aşağıdaki komutu kullanarak anahtarı Key Vault ' de saklayın:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Hizmet sorumlusu oluşturma 
Openshıft, bir Kullanıcı adı ve parola veya hizmet sorumlusu kullanarak Azure ile iletişim kurar. Azure hizmet sorumlusu, OpenShift gibi uygulamalar, hizmetler ve otomasyon araçları ile kullanabileceğiniz bir güvenlik kimliğidir. Hizmet sorumlusunun Azure 'da gerçekleştirebileceği işlemleri kontrol edin ve tanımlar. Hizmet sorumlusu izinlerinin tüm abonelik yerine belirli kaynak gruplarına kapsamını atamak en iyisidir.

[Az ad SP Create-for-RBAC](/cli/azure/ad/sp) ile bir hizmet sorumlusu oluşturun ve OpenShift 'in ihtiyaç duyacağı kimlik bilgilerini çıktı.

Aşağıdaki örnek bir hizmet sorumlusu oluşturur ve bu gruba, openkaydırıcı Trg adlı bir kaynak grubuna katkıda bulunan izinleri atar.

İlk olarak, openkaydırıcı Trg adlı kaynak grubunu oluşturun:

```azurecli
az group create -l eastus -n openshiftrg
```

Hizmet sorumlusu oluşturma:

```azurecli
az group show --name openshiftrg --query id
```
Komutun çıkışını kaydedin ve sonraki komutta $scope yerine kullanın

```azurecli
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --scopes $scope \
```

Komuttan döndürülen AppID özelliğini ve parolayı bir yere göz atın:
```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > Bu parolayı yeniden almak mümkün olmadığından, güvenli parolayı yazdığınızdan emin olun.

Hizmet sorumluları hakkında daha fazla bilgi için bkz. [Azure CLI Ile Azure hizmet sorumlusu oluşturma](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Yalnızca Kaynak Yöneticisi şablona uygulanabilir Önkoşullar

SSH özel anahtarı (**Sshprivatekey**), Azure AD İstemci parolası (**Aadclientsecret**), OpenShift yönetici parolası (**OpenshiftPassword**) ve Red Hat abonelik Yöneticisi parolası ya da etkinleştirme anahtarı (**rhsmPasswordOrActivationKey**) için gizli dizi oluşturulması gerekir.  Ayrıca, özel SSL sertifikaları kullanılıyorsa, altı ek parolaların oluşturulması gerekir- **routingcafile**, **routingsertifikadosyası**, **routingkeyfile**, **mastercafile**, **mastersertifikadosyası**ve **masterkeyfile**.  Bu parametreler daha ayrıntılı olarak açıklanacaktır.

Şablon, belirli gizli adlara başvurur, böylece yukarıda listelenen kalın adları kullanmanız **gerekir** (büyük/küçük harfe duyarlı).

### <a name="custom-certificates"></a>Özel sertifikalar

Varsayılan olarak, şablon OpenShift Web Konsolu ve yönlendirme etki alanı için otomatik olarak imzalanan sertifikaları kullanarak bir OpenShift kümesi dağıtır. Özel SSL sertifikaları kullanmak istiyorsanız ' routingCertType ' öğesini ' Custom ' ve ' masterCertType ' olarak ' Custom ' olarak ayarlayın.  Sertifikalar için. ped biçiminde CA, sertifika ve anahtar dosyaları gerekir.  Özel sertifikaları diğeri için kullanmak mümkündür.

Bu dosyaları Key Vault gizli dizileri içinde depolamanız gerekir.  Özel anahtar için kullanılan Key Vault aynı kullanın.  Gizli adlar için 6 ek giriş gerektirmek yerine, şablon her bir SSL sertifika dosyası için belirli gizli adları kullanmak üzere sabit olarak kodlanır.  Aşağıdaki tablodaki bilgileri kullanarak sertifika verilerini depolayın.

| Gizli dizi adı      | Sertifika dosyası   |
|------------------|--------------------|
| mastercafile     | Ana CA dosyası     |
| mastersertifikadosyası   | Ana SERTIFIKA dosyası   |
| masterkeyfile    | Ana anahtar dosyası    |
| routingcafile    | Yönlendirme CA dosyası    |
| routingsertifikadosyası  | Yönlendirme SERTIFIKASı dosyası  |
| routingkeyfile   | yönlendirme anahtarı dosyası   |

Azure CLı kullanarak gizli dizileri oluşturun. Aşağıda bir örnek verilmiştir.

```bash
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede aşağıdaki konular ele alınmaktadır:
> [!div class="checklist"]
> * OpenShift kümesi için SSH anahtarlarını yönetmek üzere bir Anahtar Kasası oluşturun.
> * Azure bulut çözümü sağlayıcısı tarafından kullanılmak üzere bir hizmet sorumlusu oluşturun.

Sonra, bir OpenShift kümesi dağıtın:

- [OpenShift kapsayıcı platformunu dağıtma](./openshift-container-platform-3x.md)
- [OpenShift kapsayıcı platformu dağıtımını otomatik olarak yönetilen Market Teklifini dağıtma](./openshift-container-platform-3x-marketplace-self-managed.md)

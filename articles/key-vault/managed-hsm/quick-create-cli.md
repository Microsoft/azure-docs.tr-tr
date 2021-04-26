---
title: Hızlı başlangıç-Azure yönetilen bir HSM sağlama ve etkinleştirme
description: Azure CLı kullanarak yönetilen bir HSM 'nin nasıl sağlanacağını ve etkinleşileceğini gösteren hızlı başlangıç
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 86d0a336a7d3f5d12ed8e53de802616f839f9eba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91756832"
---
# <a name="quickstart-provision-and-activate-a-managed-hsm-using-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak yönetilen bir HSM sağlama ve etkinleştirme

Azure Key Vault yönetilen HSM, **fıps 140-2 düzey 3** tarafından doğrulanan HSM 'leri kullanarak bulut uygulamalarınız için şifreleme anahtarlarını korumanızı sağlayan, tam olarak yönetilen, yüksek oranda kullanılabilir, tek kiracılı, standartlara uyumlu bir bulut hizmetidir. Yönetilen HSM hakkında daha fazla bilgi için [genel bakış](overview.md)bölümünü inceleyebilirsiniz. 

Bu hızlı başlangıçta, Azure CLı ile yönetilen bir HSM oluşturup etkinleştireceğinize sahip olursunuz. Bu işlemi tamamladıktan sonra bir gizli dizli depolayacaksınız.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları tamamlayabilmeniz için aşağıdaki öğelere sahip olmanız gerekir:

* Bir Microsoft Azure aboneliği. Hesabınız yoksa, [ücretsiz deneme için kaydolabilirsiniz](https://azure.microsoft.com/pricing/free-trial).
* Azure CLı sürüm 2.12.0 veya üzeri. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme]( /cli/azure/install-azure-cli).
* Aboneliğinizde yönetilen bir HSM. Bkz. [hızlı başlangıç: Azure CLI kullanarak](quick-create-cli.md) yönetilen bir HSM sağlama ve etkinleştirme.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

CLı kullanarak Azure 'da oturum açmak için şunu yazabilirsiniz:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Aşağıdaki örnek *eastus2* konumunda *contosoresourcegroup* adlı bir kaynak grubu oluşturur.

```azurecli-interactive
az group create --name "ContosoResourceGroup" --location eastus2
```

## <a name="create-a-managed-hsm"></a>Yönetilen bir HSM oluşturma

Yönetilen bir HSM oluşturma iki adımlı bir işlemdir:
1. Yönetilen bir HSM kaynağı sağlayın.
1. Güvenlik etki alanını indirerek yönetilen HSM 'nizi etkinleştirin.

### <a name="provision-a-managed-hsm"></a>Yönetilen bir HSM sağlama

`az keyvault create`Yönetilen BIR HSM oluşturmak için komutunu kullanın. Bu betik üç zorunlu parametreye sahiptir: bir kaynak grubu adı, HSM adı ve coğrafi konum.

Yönetilen bir HSM kaynağı oluşturmak için aşağıdaki girişleri sağlamanız gerekir:
- Aboneliğinize yerleştirilecek bir kaynak grubu.
- Azure konumu.
- İlk yöneticilerin listesi.

Aşağıdaki örnekte, **geçerli oturum açmış kullanıcıyla** tek yönetici olarak **Doğu ABD 2** konumunda bulunan **Contosoresourcegroup** kaynak grubunda **contosomhsm** adlı bir HSM oluşturulur.

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid
```

> [!NOTE]
> Oluşturma komutu birkaç dakika sürebilir. Başarılı bir şekilde döndüğünde HSM 'nizi etkinleştirmeye hazırlanın.

Bu komutun çıktısı, oluşturduğunuz yönetilen HSM 'nin özelliklerini gösterir. En önemli iki özellik şunlardır:

* **ad**: örnekte, ad ContosoMHSM ' dir. Bu adı diğer Key Vault komutları için kullanacaksınız.
* **Hsmuri**: örnekte, URI ' https://contosohsm.managedhsm.azure.net . ' HSM 'nizi REST API aracılığıyla kullanan uygulamalar bu URI 'yi kullanmalıdır.

Azure hesabınız artık bu yönetilen HSM üzerinde herhangi bir işlem gerçekleştirmeye yetkilendirildi. Henüz hiç olmadığı için, başka hiç kimse yetkili değil.

### <a name="activate-your-managed-hsm"></a>Yönetilen HSM 'nizi etkinleştirin

Tüm veri düzlemi komutları HSM etkinleştirilinceye kadar devre dışı bırakılır. Anahtar oluşturamaz veya rol atayamazsınız. Yalnızca create komutu sırasında atanan belirlenmiş yöneticiler HSM 'yi etkinleştirebilir. HSM 'yi etkinleştirmek için [güvenlik etki alanını](security-domain.md)indirmeniz gerekir.

HSM 'nizi etkinleştirmek için şunlar gerekir:
- En az 3 RSA anahtar çifti (en fazla 10)
- Güvenlik etki alanının şifresini çözmek için gereken minimum anahtar sayısını belirtin (çekirdek)

HSM 'yi etkinleştirmek için HSM 'ye en az 3 (en fazla 10) RSA ortak anahtarı gönderirsiniz. HSM, güvenlik etki alanını bu anahtarlarla şifreler ve geri gönderir. Bu güvenlik etki alanı indirmesi başarıyla tamamlandıktan sonra HSM 'niz kullanıma hazırız. Ayrıca, güvenlik etki alanının şifresini çözmek için gereken en az sayıda özel anahtar olan çekirdek belirtmeniz gerekir.

Aşağıdaki örnekte,  `openssl` 3 otomatik olarak imzalanan sertifika oluşturmak için nasıl kullanılacağı gösterilmektedir.

```azurecli-interactive
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Bu adımda oluşturulan RSA anahtar çiftlerini ve güvenlik etki alanı dosyasını güvenli bir şekilde oluşturun ve saklayın.

`az keyvault security-domain download`Güvenlik etki alanını indirmek ve YÖNETILEN HSM 'nizi etkinleştirmek için komutunu kullanın. Aşağıdaki örnek, 3 RSA anahtar çifti kullanır (Bu komut için yalnızca ortak anahtarlar gerekir) ve çekirdeği 2 olarak ayarlar.

```azurecli-interactive
az keyvault security-domain download --hsm-name ContosoMHSM --sd-wrapping-keys ./certs/cert_0.cer ./certs/cert_1.cer ./certs/cert_2.cer --sd-quorum 2 --security-domain-file ContosoMHSM-SD.json
```

Lütfen güvenlik etki alanı dosyasını ve RSA anahtar çiftlerini güvenli bir şekilde depolayın. Bu dosyalara olağanüstü durum kurtarma veya aynı güvenlik etki alanını paylaşan başka bir yönetilen HSM oluşturma için ihtiyaç duyarsınız. bu sayede anahtarları paylaşabilir.

Güvenlik etki alanı başarıyla indirildikten sonra, HSM 'niz etkin duruma gelir ve kullanıma sunulacaktır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu koleksiyondaki diğer hızlı başlangıçlar ve öğreticiler bu hızlı başlangıcı temel alır. Sonraki hızlı başlangıç ve öğreticilerle çalışmaya devam etmeyi planlıyorsanız, bu kaynakları yerinde bırakmanız yararlı olabilir.

Artık gerekli değilse, [az group delete](/cli/azure/group) komutunu kullanarak kaynak grubunu ve tüm ilgili kaynakları kaldırabilirsiniz. Kaynakları aşağıda gösterildiği gibi silebilirsiniz:

```azurecli-interactive
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Key Vault oluşturup bir gizli dizi depoladınız. Key Vault ve uygulamalarınızla tümleştirme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere ilerleyin.

- [YÖNETILEN HSM 'ye genel bakış](overview.md) konusunu okuyun
- [Yönetilen BIR HSM 'de anahtarları yönetme](key-management.md) hakkında bilgi edinin
- [YÖNETILEN HSM en iyi yöntemlerini](best-practices.md) gözden geçirme

---
title: CLı-Azure Key Vault kullanarak Azure Key Vault yönetme | Microsoft Docs
description: Azure CLı kullanarak Key Vault ortak görevleri otomatikleştirmek için bu makaleyi kullanın
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 51e5c654da95732409c3bbb7acae088d8935a59d
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000633"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Azure CLı kullanarak Key Vault yönetme 

Bu makalede, Azure CLı kullanarak Azure Key Vault ile çalışmaya başlama işlemi açıklanır.  Şu bilgileri görebilirsiniz:

- Azure 'da sağlamlaştırılmış bir kapsayıcı (kasa) oluşturma
- Anahtar kasasına anahtar, gizli dizi veya sertifika ekleme
- Azure Active Directory ile uygulama kaydetme
- Bir uygulamayı anahtar veya gizli dizi kullanacak şekilde yetkilendirme
- Anahtar Kasası Gelişmiş erişim ilkelerini ayarlama
- Donanım güvenlik modülleri (HSM 'ler) ile çalışma
- Anahtar kasasını ve ilişkili anahtarları ve gizli dizileri silme
- Çeşitli Azure platformlar arası komut satırı arabirimi komutları


Azure Anahtar Kasası çoğu bölgede kullanılabilir. Daha fazla bilgi için bkz. [Anahtar Kasası fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Bu makalede, bir uygulamanın anahtar kasasında anahtar veya gizli dizi kullanma yetkisini nasıl yetkilendirecek hakkında, adımlardan birine dahil olmak üzere Azure uygulamasını yazma yönergeleri dahil değildir.
>

Azure Key Vault genel bir bakış için bkz. [Azure Key Vault nedir?](key-vault-overview.md)
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki Azure CLı komutlarını kullanmak için aşağıdaki öğelere sahip olmanız gerekir:

* Bir Microsoft Azure aboneliği. Hesabınız yoksa, [ücretsiz deneme için kaydolabilirsiniz](https://azure.microsoft.com/pricing/free-trial).
* Azure komut satırı arabirimi sürüm 2,0 veya üzeri. En son sürümü yüklemek için bkz. [Azure CLI 'Yı yüklemek](/cli/azure/install-azure-cli).
* Bu makalede oluşturduğunuz anahtar veya parolayı kullanacak şekilde yapılandırılacak bir uygulama. [Microsoft Yükleme Merkezi](https://www.microsoft.com/download/details.aspx?id=45343)'nde örnek bir uygulama kullanılabilir. Yönergeler için, eklenen Benioku dosyasına bakın.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Azure platformlar arası komut satırı arabirimi ile ilgili yardım alma

Bu makalede komut satırı arabirimi (Bash, Terminal, komut istemi) hakkında bilgi sahibi olduğunuz varsayılır.

--Help veya-h parametresi, belirli komutların yardımını görüntülemek için kullanılabilir. Alternatif olarak, Azure Help [komut] [Options] biçimi de kullanılabilir. Bir komut için gereken parametreler hakkında şüpheli olduğunda, Yardım ' a bakın. Örneğin, aşağıdaki komutların hepsi aynı bilgileri döndürür:

```azurecli
az account set --help
az account set -h
```

Azure platformlar arası komut satırı arabirimindeki Azure Resource Manager hakkında bilgi edinmek için aşağıdaki makaleleri de okuyabilirsiniz:

* [Azure CLI'yı yükleme](/cli/azure/install-azure-cli)
* [Azure CLı ile çalışmaya başlama](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Azure 'da sağlamlaştırılmış bir kapsayıcı (kasa) oluşturma

Kasaları, donanım güvenlik modülleri tarafından desteklenen güvenli kapsayıcılardır. Kasalar, uygulama gizli dizilerinin depolanmasını merkezi hale getirerek güvenlik bilgilerini kazayla kaybetme olasılığını azaltmaya yardımcı olur. Anahtar Kasaları ayrıca içlerinde depolanmış her şeye erişimi denetler ve günlüğe kaydeder. Azure Key Vault, sağlam bir sertifika yaşam döngüsü yönetim çözümü için gereken özellikleri sağlayarak Aktarım Katmanı Güvenliği (TLS) sertifikalarını isteme ve yenileme işlemlerini gerçekleştirebilir. Sonraki adımlarda bir kasa oluşturacaksınız.

### <a name="connect-to-your-subscriptions"></a>Aboneliklerinize bağlanma

Etkileşimli olarak oturum açmak için aşağıdaki komutu kullanın:

```azurecli
az login
```
Bir kuruluş hesabı kullanarak oturum açmak için Kullanıcı adınızı ve parolanızı geçirebilirsiniz.

```azurecli
az login -u username@domain.com -p password
```

Birden fazla aboneliğiniz varsa ve hangisinin kullanılacağını belirtmeniz gerekiyorsa, hesabınıza yönelik abonelikleri görmek için aşağıdakileri yazın:

```azurecli
az account list
```

Abonelik parametresine sahip bir abonelik belirtin.

```azurecli
az account set --subscription <subscription name or ID>
```

Azure platformlar arası komut satırı arabirimini yapılandırma hakkında daha fazla bilgi için bkz. [Azure CLI 'Yı yüklemeye](/cli/azure/install-azure-cli).

### <a name="create-a-new-resource-group"></a>Yeni kaynak grubu oluştur

Azure Resource Manager kullanırken, tüm ilgili kaynaklar bir kaynak grubu içinde oluşturulur. Mevcut bir kaynak grubunda bir Anahtar Kasası oluşturabilirsiniz. Yeni bir kaynak grubu kullanmak istiyorsanız yeni bir tane oluşturabilirsiniz.

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

İlk parametre kaynak grubu adıdır ve ikinci parametre konumudur. Tüm olası konumların bir listesini almak için:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>Key Vault kaynak sağlayıcısını kaydetme

 Yeni bir Anahtar Kasası oluşturmaya çalıştığınızda "abonelik ' Microsoft. Keykasasının ad alanını kullanmak için kayıtlı değil" hatasını görebilirsiniz. Bu ileti görüntülenirse, Key Vault kaynak sağlayıcısı 'nın aboneliğinize kayıtlı olduğundan emin olun. Bu, her bir abonelik için tek seferlik bir işlemdir.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Bir anahtar kasası oluşturma

Bir Anahtar Kasası oluşturmak için komutunukullanın.`az keyvault create` Bu betik üç zorunlu parametreye sahiptir: bir kaynak grubu adı, bir Anahtar Kasası adı ve coğrafi konum.

**Contosokeykasası**adında yeni bir kasa oluşturmak için, **Doğu Asya** konumunda bulunan **contosoresourcegroup**kaynak grubunda şunu yazın: 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

Bu komutun çıktısı, oluşturduğunuz anahtar kasasının özelliklerini gösterir. En önemli iki özellik şunlardır:

* **ad**: Örnekte, bu ad Contosokeykasadır. Bu adı diğer Key Vault komutları için kullanacaksınız.
* **Vaulturi**: Örnekte, URI olur https://contosokeyvault.vault.azure.net. REST API'si aracılığıyla kasanızı kullanan uygulamaların bu URI'yi kullanması gerekir.

Azure hesabınız artık bu anahtar kasasında herhangi bir işlemi gerçekleştirmeye yetkilidir. Henüz hiç olmadığı için, başka hiç kimse yetkili değil.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Anahtar kasasına anahtar, gizli dizi veya sertifika ekleme

Sizin için yazılım korumalı bir anahtar oluşturmak Azure Key Vault istiyorsanız `az key create` komutunu kullanın.

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

. Ped dosyasında mevcut bir anahtarınız varsa, Azure Key Vault yükleyebilirsiniz. Anahtarı yazılım veya HSM ile korumayı seçebilirsiniz. Bu örnek, "hVFkk965BuUv" parolasını kullanarak anahtarı. ped dosyasından içeri aktarır ve yazılımla koruyabilir:

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

Artık oluşturduğunuz veya Azure Key Vault ' a yüklediğiniz anahtara URI 'sini kullanarak başvurabilirsiniz. Her **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** zaman geçerli sürümü almak için kullanın. Bu belirli sürümü almak için https://[keykasası-Name]. kasa. Azure. net/Keys/[KeyName]/[key-Unique-ID] kullanın. Örneğin, **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** . 

Kasaya bir gizli dizi ekleyin, bu, SQLPassword adlı bir paroladır ve "hVFkk965BuUv" değerini Azure Anahtar Kasası 'na taşır. 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

Bu parolaya URI 'sini kullanarak başvurun. Bu **https://ContosoVault.vault.azure.net/secrets/SQLPassword** belirli sürümü almak için her zaman geçerli sürümü almak için kullanın ve https://[keykasaadı]. kasa. Azure. net/Secret/[gizli-adı]/[gizli-Unique-ID]. Örneğin, **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** .

. Pek veya. pfx kullanarak kasaya bir sertifika aktarın.

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

Oluşturduğunuz anahtarı, gizliliği veya sertifikayı görüntüleyelim:

* Anahtarlarınızı görüntülemek için şunu yazın: 

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

* Gizli dizilerinizi görüntülemek için şunu yazın: 

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

* Sertifikaları görüntülemek için şunu yazın: 

```azurecli
az keyvault certificate list --vault-name "ContosoKeyVault"
```

## <a name="registering-an-application-with-azure-active-directory"></a>Azure Active Directory ile uygulama kaydetme

Bu adım genellikle ayrı bir bilgisayarda bir geliştirici tarafından yapılır. Azure Key Vault özgü değildir ancak tanıma için buraya dahil edilmiştir. Uygulama kaydını tamamlamaya yönelik hesabınız, kasalarınızın ve uygulamanın aynı Azure dizininde olması gerekir.

Bir anahtar kasası kullanan uygulamaların, Azure Active Directory'den bir belirteç kullanarak kimlik doğrulama yapması gerekir.  Uygulamanın sahibi ilk olarak Azure Active Directory kaydolmalıdır. Kaydın sonunda, uygulama sahibi aşağıdaki değerleri elde eder:

- **Uygulama kimliği** (AAD istemci kimliği veya AppID olarak da bilinir)
- **Kimlik doğrulaması anahtarı** (paylaşılan gizli dizi olarak da bilinir). 

Uygulamanın bir belirteç almak için bu değerlerin her ikisini de Azure Active Directory'ye sunması gerekir. Bir uygulama belirteç almak üzere yapılandırıldığında uygulamaya bağlı olur. [Key Vault örnek uygulaması](https://www.microsoft.com/download/details.aspx?id=45343) için uygulama sahibi bu değerleri app.config dosyasında ayarlar.

Azure Active Directory ile uygulama kaydetme hakkında ayrıntılı adımlar için, [uygulamaları Azure Active Directory Ile tümleştirme](../active-directory/develop/active-directory-integrating-applications.md)başlıklı makaleleri gözden geçirmeniz gerekir, [Portal 'ı kullanarak bir Azure Active Directory uygulama ve hizmet oluşturun kaynaklara erişebilen](../active-directory/develop/howto-create-service-principal-portal.md)ve [Azure CLI Ile bir Azure hizmet sorumlusu oluşturabileceğiniz](/cli/azure/create-an-azure-service-principal-azure-cli)sorumlu.

Bir uygulamayı Azure Active Directory kaydetmek için:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Bir uygulamayı anahtar veya gizli dizi kullanacak şekilde yetkilendirme

Uygulamanın kasada anahtara veya gizli anahtara erişmesini yetkilendirmek için `az keyvault set-policy` komutunu kullanın.

Örneğin, kasa adınız Contosokeykasası ise, uygulamanın 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed 'e sahip olması ve uygulamanın kasadaki anahtarlarla şifresini çözmesine ve oturum açmalarına izin vermek istiyorsanız aşağıdaki komutu kullanın:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Kasadaki gizli dizileri okumak için aynı uygulamayı yetkilendirmek üzere aşağıdaki komutu yazın:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="bkmk_KVperCLI"></a>Anahtar Kasası Gelişmiş erişim ilkelerini ayarlama

Anahtar Kasası için gelişmiş ilkeleri etkinleştirmek üzere [az keykasa Update](/cli/azure/keyvault#az-keyvault-update) kullanın.

 Dağıtım için Key Vault etkinleştir: Sanal makinelerin kasadan gizli dizi olarak depolanan sertifikaları almasına izin verir.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

Key Vault disk şifrelemesi için etkinleştir: Azure disk şifrelemesi Kasası kullanılırken gereklidir.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

Şablon dağıtımı için Key Vault etkinleştir: Kaynak Yöneticisi kasalardan gizli dizileri almasına izin verir.

```azurecli 
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
 ```

## <a name="working-with-hardware-security-modules-hsms"></a>Donanım güvenlik modülleri (HSM 'ler) ile çalışma

Ek güvence için, HSM sınırını hiçbir şekilde bırakmamış donanım güvenlik modüllerinden (HSM 'ler) anahtarları içeri aktarabilir veya oluşturabilirsiniz. HSM'ler, FIPS 140-2 Düzey 2 doğrulanmasına sahiptir. Bu gereksinim sizin için uygun değilse, bu bölümü atlayın ve anahtar kasasını ve ilişkili anahtarları ve gizli dizileri silme ' ye gidin.

Bu HSM korumalı anahtarları oluşturmak için HSM korumalı anahtarları destekleyen bir kasa aboneliğine sahip olmanız gerekir.

Anahtar Kasası oluşturduğunuzda, ' SKU ' parametresini ekleyin:

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

Bu kasaya yazılım korumalı anahtarlar ve HSM korumalı anahtarlar (daha önce gösterildiği gibi) ekleyebilirsiniz. HSM korumalı bir anahtar oluşturmak için, hedef parametreyi ' HSM ' olarak ayarlayın:

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

Bilgisayarınızdaki. ped dosyasından bir anahtarı içeri aktarmak için aşağıdaki komutu kullanabilirsiniz. Bu komut, anahtarı Anahtar Kasası hizmetindeki HSM'lere aktarır:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

Bir sonraki komut bir "kendi anahtarınızı getirin" (BYOK) paketini içeri aktarır. Böylece anahtar HSM sınırını terk etmeden yerel HSM'nizde anahtarınızı oluşturmanız ve Anahtar Kasası hizmetindeki HSM'lere bunu aktarmanız sağlanır.

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

Bu BYOK paketini oluşturma hakkında daha ayrıntılı yönergeler için bkz. [Azure Key Vault Ile HSM korumalı anahtarları kullanma](key-vault-hsm-protected-keys.md).

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>Anahtar kasasını ve ilişkili anahtarları ve gizli dizileri silme

Artık anahtar kasası ve anahtarları veya gizli dizileri gerekmiyorsa, şu `az keyvault delete` komutu kullanarak anahtar kasasını silebilirsiniz:

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

Alternatif olarak, anahtar kasasını ve bu gruba eklediğiniz herhangi diğer kaynakları içeren Azure kaynak grubunun tümünü silebilirsiniz.

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Çeşitli Azure platformlar arası komut satırı arabirimi komutları

Azure Key Vault yönetmek için yararlı bulabileceğiniz diğer komutlar.

Bu komut, tüm anahtarların ve seçilen özelliklerin tablolu bir görüntüsünü listeler:

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

Bu komut, belirtilen anahtar için özelliklerin tam bir listesini görüntüler:

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Bu komut, tüm gizli adların ve seçilen özelliklerin tablolu bir görüntüsünü listeler:

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

Aşağıda, belirli bir anahtarın nasıl kaldırılacağını gösteren bir örnek verilmiştir:

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Aşağıda, belirli bir parolayı kaldırmaya ilişkin bir örnek verilmiştir:

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>Sonraki adımlar

- Anahtar Kasası komutlarına yönelik tüm Azure CLı başvurusu için bkz. [Key Vault CLI başvurusu](/cli/azure/keyvault).

- Programlama başvuruları için [Azure Key Vault Geliştirici Kılavuzu](key-vault-developers-guide.md) ' na bakın.

- Azure Key Vault ve HSM 'ler hakkında bilgi için bkz. [Azure Key Vault Ile HSM korumalı anahtarları kullanma](key-vault-hsm-protected-keys.md).

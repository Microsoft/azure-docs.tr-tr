---
title: Azure Key Vault yönetilen HSM için HSM korumalı anahtarlar oluşturma ve aktarma-Azure Key Vault | Microsoft Docs
description: Yönetilen HSM ile kullanmak üzere kendi HSM korumalı anahtarlarınızı planlayıp, oluşturmanıza ve aktarmaya yardımcı olması için bu makaleyi kullanın. Kendi anahtarını getir (BYOK) olarak da bilinir.
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: cc9037db3289d7fb3287a8994a8ff6a68fc0583a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790590"
---
# <a name="import-hsm-protected-keys-to-managed-hsm-byok"></a>HSM korumalı anahtarları yönetilen HSM 'ye içeri aktar (BYOK)

 Azure Key Vault Managed HSM, şirket içi donanım güvenlik modülünde (HSM) oluşturulan anahtarların içeri aktarılmasını destekler; Anahtarlar hiçbir şekilde HSM koruma sınırını bırakmaz. Bu senaryo genellikle *kendi anahtarını getir* (bYok) olarak adlandırılır. Yönetilen HSM, anahtarlarınızı korumak için Marvell LiquidSecurity HSM bağdaştırıcılarını (FIPS 140-2 düzey 3 ' ü doğruladı) kullanır.

Yönetilen HSM ile kullanmak üzere kendi HSM korumalı anahtarlarınızı planlayıp, oluşturmanıza ve aktarmaya yardımcı olması için bu makaledeki bilgileri kullanın.

> [!NOTE]
> Bu işlev, Azure Çin 21Vianet için kullanılamaz. Bu içeri aktarma yöntemi yalnızca [desteklenen HSM](#supported-hsms)'ler için kullanılabilir. 

Daha fazla bilgi edinmek ve yönetilen HSM 'yi kullanmaya başlamanıza yönelik bir öğretici için bkz. [YÖNETILEN HSM nedir?](overview.md).

## <a name="overview"></a>Genel Bakış

İşleme genel bakış. Belirli adımları makalenin ilerleyen kısımlarında açıklanmıştır.

* Yönetilen HSM 'de bir anahtar oluşturun ( *anahtar değişim anahtarı* (kek) olarak adlandırılır). KEK yalnızca anahtar işlemi olan bir RSA-HSM anahtarı olmalıdır `import` . 
* KEK ortak anahtarını bir. pek dosyası olarak indirin.
* KEK ortak anahtarını, şirket içi HSM 'ye bağlı bir çevrimdışı bilgisayara aktarın.
* Çevrimdışı bilgisayarda, bir BYOK dosyası oluşturmak için HSM satıcınız tarafından sunulan BYOK aracını kullanın. 
* Hedef anahtar, yönetilen HSM 'ye aktarılana kadar şifrelenmeye devam eden bir KEK ile şifrelenir. Yalnızca anahtarınızın şifrelenmiş sürümü şirket içi HSM 'yi bırakır.
* Yönetilen bir HSM içinde oluşturulan bir KEK dışarı aktarılabilir değildir. HSM 'ler, yönetilen bir HSM dışında KEK 'in hiçbir açık sürümü bulunmadığından kuralı uygular.
* KEK, hedef anahtarın içeri aktarılacağı aynı yönetilen HSM içinde olmalıdır.
* BYOK dosyası yönetilen HSM 'ye yüklendiğinde, yönetilen bir HSM, hedef anahtar malzemelerinin şifresini çözmek ve bir HSM anahtarı olarak içeri aktarmak için KEK özel anahtarını kullanır. Bu işlem tamamen HSM içinde gerçekleşir. Hedef anahtar her zaman HSM koruma sınırında kalır.


## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki Azure CLı komutlarını kullanmak için aşağıdaki öğelere sahip olmanız gerekir:

* Bir Microsoft Azure aboneliği. Hesabınız yoksa, [ücretsiz deneme için kaydolabilirsiniz](https://azure.microsoft.com/pricing/free-trial).
* Azure CLı sürüm 2.12.0 veya üzeri. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme]( /cli/azure/install-azure-cli).
* Yönetilen bir HSM aboneliğinizdeki [desteklenen HSMs listesi](#supported-hsms) . Bkz. [hızlı başlangıç: Azure CLI kullanarak](quick-create-cli.md) yönetilen bir HSM sağlama ve etkinleştirme.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLı kullanarak Azure 'da oturum açmak için şunu yazabilirsiniz:

```azurecli
az login
```

CLı aracılığıyla oturum açma seçenekleri hakkında daha fazla bilgi için [Azure CLI ile oturum açma](/cli/azure/authenticate-azure-cli) bölümüne göz atın

## <a name="supported-hsms"></a>Desteklenen HSM 'ler

|Satıcı adı|Satıcı türü|Desteklenen HSM modelleri|Daha fazla bilgi|
|---|---|---|---|
|nCipher|Üreticisini<br/>Hizmet olarak HSM|<ul><li>HSM 'lerin nShield ailesi</li><li>hizmet olarak nShield</ul>|[nCipher yeni BYOK aracı ve belgeleri](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Üretici|<ul><li>Bellenim sürüm 7,3 veya daha yeni bir sürümü içeren Luna HSM 7 ailesi</li></ul>| [Luna BYOK aracı ve belgeleri](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanx|Üreticisini<br/>Hizmet olarak HSM|<ul><li>Self-Defending anahtar yönetim hizmeti (SDKMS)</li><li>Equinix SmartKey</li></ul>|[SDKMS anahtarlarını BYOK için bulut sağlayıcılarına dışarı aktarma-Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|Üretici|Tüm LiquidSecurity HSM 'leri<ul><li>Bellenim sürümü 2.0.4 veya üzeri</li><li>Bellenim sürüm 3,2 veya daha yenisi</li></ul>|[Marvell BYOK aracı ve belgeleri](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Cryptomathic|ISV (Kurumsal anahtar yönetim sistemi)|Birden çok HSM markalarını ve modellerini kapsayan<ul><li>nCipher</li><li>Thales</li><li>Utıco</li></ul>[Ayrıntılar için bkz. Cryptomathic sitesi](https://www.cryptomathic.com/azurebyok)|[Cryptomathic BYOK aracı ve belgeleri](https://www.cryptomathic.com/azurebyok)|
|Securosys SA|Üretici, hizmet olarak HSM|Primus HSM ailesi, Securosys bulutları HSM|[Primus BYOK aracı ve belgeleri](https://www.securosys.com/primus-azure-byok)|
|StorMagic|ISV (Kurumsal anahtar yönetim sistemi)|Birden çok HSM markalarını ve modellerini kapsayan<ul><li>Utıco</li><li>Thales</li><li>nCipher</li></ul>[Ayrıntılar için bkz. Stormagic sitesi](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[SvKMS ve Azure Key Vault BYOK](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|
|IBM|Üretici|IBM 476x, CryptoExpress|[IBM Kurumsal anahtar yönetim altyapısı](https://www.ibm.com/security/key-management/ekmf-bring-your-own-key-azure)|
|Utıco|Üreticisini<br/>Hizmet olarak HSM|u. güven çıpası, CryptoServer|[Uıtıco BYOK aracı ve tümleştirme Kılavuzu](https://support.hsm.utimaco.com/support/downloads/byok)|
||||


## <a name="supported-key-types"></a>Desteklenen anahtar türleri

|Anahtar adı|Anahtar türü|Anahtar boyutu/eğrisi|Kaynak|Description|
|---|---|---|---|---|
|Anahtar değişim anahtarı (KEK)|RSA| 2.048 bit<br />3.072 bit<br />4.096 bit|Yönetilen HSM|Yönetilen HSM 'de oluşturulan HSM ile desteklenen bir RSA anahtar çifti|
|Hedef anahtar|
||RSA|2.048 bit<br />3.072 bit<br />4.096 bit|Satıcı HSM|Yönetilen HSM 'ye aktarılacak anahtar|
||EC|P-256<br />P-384<br />P-521|Satıcı HSM|Yönetilen HSM 'ye aktarılacak anahtar|
||Simetrik anahtar (OCT-HSM)|128 bit<br />192 bit<br />256 bit|Satıcı HSM|Yönetilen HSM 'ye aktarılacak anahtar|
||||
## <a name="generate-and-transfer-your-key-to-the-managed-hsm"></a>Anahtarınızı oluşturun ve yönetilen HSM 'ye aktarın

Anahtarınızı oluşturmak ve yönetilen bir HSM 'ye aktarmak için:

* [1. Adım: KEK oluşturma](#step-1-generate-a-kek)
* [2. Adım: KEK ortak anahtarını Indirme](#step-2-download-the-kek-public-key)
* [3. Adım: anahtarınızı aktarım için oluşturma ve hazırlama](#step-3-generate-and-prepare-your-key-for-transfer)
* [4. Adım: anahtarınızı yönetilen HSM 'ye aktarma](#step-4-transfer-your-key-to-managed-hsm)

### <a name="step-1-generate-a-kek"></a>1. Adım: KEK oluşturma

Bir KEK, yönetilen bir HSM 'de oluşturulan bir RSA anahtarıdır. KEK, içe aktarmak istediğiniz anahtarı ( *hedef* anahtar) şifrelemek için kullanılır.

KEK şu olmalıdır:
- Bir RSA-HSM anahtarı (2.048-bit; 3.072-bit; veya 4.096-bit)
- Hedef anahtarı içeri aktarmayı planladığınız aynı yönetilen HSM 'de oluşturuldu
- Olarak ayarlanan izin verilen anahtar işlemleri ile oluşturuldu `import`

> [!NOTE]
> KEK, yalnızca izin verilen anahtar işlemi olarak ' import ' içermelidir. ' Import ' diğer tüm anahtar işlemleriyle birbirini dışlıyor.

Anahtar işlemleri olarak ayarlanmış bir KEK oluşturmak için [az keykasa Key Create](/cli/azure/keyvault/key#az_keyvault_key_create) komutunu kullanın `import` . `kid`Aşağıdaki komuttan döndürülen anahtar tanımlayıcısını () kaydedin. ( `kid` [Adım 3](#step-3-generate-and-prepare-your-key-for-transfer)' te bu değeri kullanacaksınız.)

```azurecli-interactive
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --hsm-name ContosoKeyVaultHSM
```
---


### <a name="step-2-download-the-kek-public-key"></a>2. Adım: KEK ortak anahtarını Indirme

KEK ortak anahtarını bir. ped dosyasına indirmek için [az keykasakey Download](/cli/azure/keyvault/key#az_keyvault_key_download) komutunu kullanın. İçeri aktardığınız hedef anahtar, KEK ortak anahtarı kullanılarak şifrelenir.

```azurecli-interactive
az keyvault key download --name KEKforBYOK --hsm-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```
---

KEKforBYOK. PublicKey. ped dosyasını çevrimdışı bilgisayarınıza aktarın. Sonraki adımda bu dosyaya ihtiyacınız olacaktır.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>3. Adım: anahtarınızı aktarım için oluşturma ve hazırlama

BYOK aracını indirip yüklemek için HSM satıcınızın belgelerine bakın. Bir hedef anahtar oluşturmak için HSM satıcınızdan gelen yönergeleri izleyin ve ardından bir anahtar aktarım paketi (BYOK dosyası) oluşturun. BYOK Aracı, `kid` BIR bYok dosyasında [](#step-1-generate-a-kek) şifreli hedef anahtar oluşturmak için [Adım 2](#step-2-download-the-kek-public-key) ' de Indirdiğiniz kekforbyok. PublicKey. ped dosyasını kullanır.

BYOK dosyasını bağlı bilgisayarınıza aktarın.

> [!NOTE] 
> RSA 1.024 bit anahtarlarının içe aktarılması desteklenmez. Şu anda, eliptik eğri (EC) anahtarının içe aktarılması desteklenmez.
>
> **Bilinen sorun**: bir RSA 4k hedef anahtarının yalnızca bir bellenim 7.4.0 veya daha yeni sürümü ile içeri aktarılması desteklenir.

### <a name="step-4-transfer-your-key-to-managed-hsm"></a>4. Adım: anahtarınızı yönetilen HSM 'ye aktarma

Anahtar içeri aktarmayı gerçekleştirmek için, anahtar aktarım paketini (bir BYOK dosyası) bağlantısı kesilen bilgisayarınızdan İnternet 'e bağlı bilgisayara aktarın. BYOK dosyasını yönetilen HSM 'ye yüklemek için [az keykasa Key import](/cli/azure/keyvault/key#az_keyvault_key_import) komutunu kullanın.

```azurecli-interactive
az keyvault key import --hsm-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Karşıya yükleme başarılı olursa, Azure CLı içeri aktarılan anahtarın özelliklerini görüntüler.

## <a name="next-steps"></a>Sonraki adımlar

Artık, yönetilen HSM 'inizdeki bu HSM korumalı anahtarı kullanabilirsiniz. Daha fazla bilgi için [Bu fiyat ve Özellik Karşılaştırması](https://azure.microsoft.com/pricing/details/key-vault/)bölümüne bakın.

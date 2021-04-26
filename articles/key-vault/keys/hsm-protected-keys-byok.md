---
title: '& aktarım HSM korumalı anahtarlar oluşturma – BYOK – Azure Key Vault'
description: Azure Key Vault ile kullanmak üzere kendi HSM korumalı anahtarlarınızı planlayıp, oluşturmanıza ve aktarmaya yardımcı olması için bu makaleyi kullanın. Kendi anahtarını getir (BYOK) olarak da bilinir.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: f1b5d6499594e9026e1615be5361c52c9ce2f4ef
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774818"
---
# <a name="import-hsm-protected-keys-to-key-vault-byok"></a>HSM korumalı anahtarları Key Vault içeri aktar (BYOK)

Azure Key Vault kullandığınızda ek güvence için bir donanım güvenlik modülünde (HSM) bir anahtar alabilir veya oluşturabilirsiniz; anahtar hiçbir şekilde HSM sınırını terk etmeyecektir. Bu senaryo genellikle *kendi anahtarını getir* (bYok) olarak adlandırılır. Key Vault anahtarlarınızı korumak için HSM 'lerin nCipher nShield ailesini (FIPS 140-2 düzey 2 doğrulanan) kullanır.

Azure Key Vault ile kullanmak üzere kendi HSM korumalı anahtarlarınızı planlayıp, oluşturmanıza ve aktarmaya yardımcı olması için bu makaledeki bilgileri kullanın.

> [!NOTE]
> Bu işlev, Azure Çin 21Vianet için kullanılamaz. 
> 
> Bu içeri aktarma yöntemi yalnızca [desteklenen HSM](#supported-hsms)'ler için kullanılabilir. 

Daha fazla bilgi ve Key Vault kullanmaya başlama öğreticisi için (HSM korumalı anahtarlar için bir Anahtar Kasası oluşturma dahil), bkz. [nedir? Azure Key Vault nedir?](../general/overview.md).

## <a name="overview"></a>Genel Bakış

İşleme genel bakış. Belirli adımları makalenin ilerleyen kısımlarında açıklanmıştır.

* Key Vault ' de bir anahtar oluşturun ( *anahtar değişim anahtarı* (kek) olarak adlandırılır). KEK yalnızca anahtar işlemi olan bir RSA-HSM anahtarı olmalıdır `import` . Yalnızca Premium SKU 'SU Key Vault RSA-HSM anahtarlarını destekler.
* KEK ortak anahtarını bir. pek dosyası olarak indirin.
* KEK ortak anahtarını, şirket içi HSM 'ye bağlı bir çevrimdışı bilgisayara aktarın.
* Çevrimdışı bilgisayarda, bir BYOK dosyası oluşturmak için HSM satıcınız tarafından sunulan BYOK aracını kullanın. 
* Hedef anahtar, Key Vault HSM 'ye aktarılana kadar şifreli olarak kalacak bir KEK ile şifrelenir. Yalnızca anahtarınızın şifrelenmiş sürümü şirket içi HSM 'yi bırakır.
* Key Vault HSM içinde oluşturulan bir KEK dışarı aktarılabilir değildir. HSM 'ler, Key Vault HSM dışında KEK 'in hiçbir net sürümünün mevcut olmadığını, kuralı uygular.
* KEK, hedef anahtarın içeri aktarılacağı aynı anahtar kasasında olmalıdır.
* BYOK dosyası Key Vault karşıya yüklendiğinde, Key Vault HSM, hedef anahtar malzemelerinin şifresini çözmek ve bir HSM anahtarı olarak içeri aktarmak için KEK özel anahtarını kullanır. Bu işlem tamamen bir Key Vault HSM içinde gerçekleşir. Hedef anahtar her zaman HSM koruma sınırında kalır.

## <a name="prerequisites"></a>Önkoşullar

Aşağıdaki tabloda Azure Key Vault BYOK kullanımına yönelik önkoşullar listelenmektedir:

| Gereksinim | Daha fazla bilgi |
| --- | --- |
| Bir Azure aboneliği |Azure Key Vault bir Anahtar Kasası oluşturmak için bir Azure aboneliğine ihtiyacınız vardır. [Ücretsiz deneme Için kaydolun](https://azure.microsoft.com/pricing/free-trial/). |
| HSM korumalı anahtarları içeri aktarmak için Key Vault Premium SKU 'SU |Azure Key Vault hizmet katmanları ve özellikleri hakkında daha fazla bilgi için bkz. [Key Vault fiyatlandırması](https://azure.microsoft.com/pricing/details/key-vault/). |
| Desteklenen HSMs listesinden bir HSM ve HSM satıcınız tarafından sunulan bir BYOK aracı ve yönergeleri | HSM 'nizin nasıl kullanılacağına ilişkin bir HSM ve temel bilgi için izinleriniz olmalıdır. Bkz. [desteklenen HSM](#supported-hsms)'ler. |
| Azure CLı sürüm 2.1.0 veya üzeri | Bkz. [Azure CLI 'Yi yüklemeyi](/cli/azure/install-azure-cli).|

## <a name="supported-hsms"></a>Desteklenen HSM 'ler

|Satıcı adı|Satıcı türü|Desteklenen HSM modelleri|Daha fazla bilgi|
|---|---|---|---|
|nCipher|Üreticisini<br/>Hizmet olarak HSM|<ul><li>HSM 'lerin nShield ailesi</li><li>hizmet olarak nShield</ul>|[nCipher yeni BYOK aracı ve belgeleri](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Üretici|<ul><li>Bellenim sürüm 7,3 veya daha yeni bir sürümü içeren Luna HSM 7 ailesi</li></ul>| [Luna BYOK aracı ve belgeleri](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanx|Üreticisini<br/>Hizmet olarak HSM|<ul><li>Self-Defending anahtar yönetim hizmeti (SDKMS)</li><li>Equinix SmartKey</li></ul>|[SDKMS anahtarlarını BYOK için bulut sağlayıcılarına dışarı aktarma-Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|Üretici|Tüm LiquidSecurity HSM 'leri<ul><li>Bellenim sürümü 2.0.4 veya üzeri</li><li>Bellenim sürüm 3,2 veya daha yenisi</li></ul>|[Marvell BYOK aracı ve belgeleri](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Cryptomathic|ISV (Kurumsal anahtar yönetim sistemi)|Birden çok HSM markalarını ve modellerini kapsayan<ul><li>nCipher</li><li>Thales</li><li>Utıco</li></ul>[Ayrıntılar için bkz. Cryptomathic sitesi](https://www.cryptomathic.com/azurebyok)|[Cryptomathic BYOK aracı ve belgeleri](https://www.cryptomathic.com/azurebyok)|
|Securosys SA|Üreticisini<br/>Hizmet olarak HSM|Primus HSM ailesi, Securosys bulutları HSM|[Primus BYOK aracı ve belgeleri](https://www.securosys.com/primus-azure-byok)|
|StorMagic|ISV (Kurumsal anahtar yönetim sistemi)|Birden çok HSM markalarını ve modellerini kapsayan<ul><li>Utıco</li><li>Thales</li><li>nCipher</li></ul>[Ayrıntılar için bkz. Stormagic sitesi](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[SvKMS ve Azure Key Vault BYOK](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|
|IBM|Üretici|IBM 476x, CryptoExpress|[IBM Kurumsal anahtar yönetim altyapısı](https://www.ibm.com/security/key-management/ekmf-bring-your-own-key-azure)|
|Utıco|Üreticisini<br/>Hizmet olarak HSM|u. güven çıpası, CryptoServer|[Uıtıco BYOK aracı ve tümleştirme Kılavuzu](https://support.hsm.utimaco.com/support/downloads/byok)|
||||


## <a name="supported-key-types"></a>Desteklenen anahtar türleri

|Anahtar adı|Anahtar türü|Anahtar boyutu/eğrisi|Kaynak|Description|
|---|---|---|---|---|
|Anahtar değişim anahtarı (KEK)|RSA| 2.048 bit<br />3.072 bit<br />4.096 bit|Azure Key Vault HSM|Azure Key Vault içinde oluşturulan HSM ile desteklenen bir RSA anahtar çifti|
|Hedef anahtar|
||RSA|2.048 bit<br />3.072 bit<br />4.096 bit|Satıcı HSM|Azure Key Vault HSM 'ye aktarılacak anahtar|
||EC|P-256<br />P-384<br />P-521|Satıcı HSM|Azure Key Vault HSM 'ye aktarılacak anahtar|
||||

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Anahtarınızı oluşturun ve Key Vault HSM 'ye aktarın

Anahtarınızı oluşturmak ve Key Vault HSM 'ye aktarmak için:

* [1. Adım: KEK oluşturma](#step-1-generate-a-kek)
* [2. Adım: KEK ortak anahtarını Indirme](#step-2-download-the-kek-public-key)
* [3. Adım: anahtarınızı aktarım için oluşturma ve hazırlama](#step-3-generate-and-prepare-your-key-for-transfer)
* [4. Adım: anahtarınızı Azure Key Vault aktarma](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>1. Adım: KEK oluşturma

Bir KEK, Key Vault HSM 'de oluşturulan bir RSA anahtarıdır. KEK, içe aktarmak istediğiniz anahtarı ( *hedef* anahtar) şifrelemek için kullanılır.

KEK şu olmalıdır:
- Bir RSA-HSM anahtarı (2.048-bit; 3.072-bit; veya 4.096-bit)
- Hedef anahtarı içeri aktarmayı planladığınız aynı anahtar kasasında oluşturuldu
- Olarak ayarlanan izin verilen anahtar işlemleri ile oluşturuldu `import`

> [!NOTE]
> KEK, yalnızca izin verilen anahtar işlemi olarak ' import ' içermelidir. ' Import ' diğer tüm anahtar işlemleriyle birbirini dışlıyor.

Anahtar işlemleri olarak ayarlanmış bir KEK oluşturmak için [az keykasa Key Create](/cli/azure/keyvault/key#az_keyvault_key_create) komutunu kullanın `import` . `kid`Aşağıdaki komuttan döndürülen anahtar tanımlayıcısını () kaydedin. ( `kid` [Adım 3](#step-3-generate-and-prepare-your-key-for-transfer)' te bu değeri kullanacaksınız.)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>2. Adım: KEK ortak anahtarını Indirme

KEK ortak anahtarını bir. ped dosyasına indirmek için [az keykasakey Download](/cli/azure/keyvault/key#az_keyvault_key_download) komutunu kullanın. İçeri aktardığınız hedef anahtar, KEK ortak anahtarı kullanılarak şifrelenir.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

KEKforBYOK. PublicKey. ped dosyasını çevrimdışı bilgisayarınıza aktarın. Sonraki adımda bu dosyaya ihtiyacınız olacaktır.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>3. Adım: anahtarınızı aktarım için oluşturma ve hazırlama

BYOK aracını indirip yüklemek için HSM satıcınızın belgelerine bakın. Bir hedef anahtar oluşturmak için HSM satıcınızdan gelen yönergeleri izleyin ve ardından bir anahtar aktarım paketi (BYOK dosyası) oluşturun. BYOK Aracı, `kid` BIR bYok dosyasında [](#step-1-generate-a-kek) şifreli hedef anahtar oluşturmak için [Adım 2](#step-2-download-the-kek-public-key) ' de Indirdiğiniz kekforbyok. PublicKey. ped dosyasını kullanır.

BYOK dosyasını bağlı bilgisayarınıza aktarın.

> [!NOTE] 
> RSA 1.024 bit anahtarlarının içe aktarılması desteklenmez. P-256K eğrisi ile eliptik eğri anahtarını içeri aktarma desteklenmiyor.
> 
> **Bilinen sorun**: bir RSA 4k hedef anahtarının yalnızca bir bellenim 7.4.0 veya daha yeni sürümü ile içeri aktarılması desteklenir.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>4. Adım: anahtarınızı Azure Key Vault aktarma

Anahtar içeri aktarmayı gerçekleştirmek için, anahtar aktarım paketini (bir BYOK dosyası) bağlantısı kesilen bilgisayarınızdan İnternet 'e bağlı bilgisayara aktarın. BYOK dosyasını Key Vault HSM 'ye yüklemek için [az keykasa Key import](/cli/azure/keyvault/key#az_keyvault_key_import) komutunu kullanın.

Bir RSA anahtarını içeri aktarmak için aşağıdaki komutu kullanın. ----Parametresi isteğe bağlıdır ve varsayılan olarak ' RSA-HSM ' olarak ayarlanır.
```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Bir EC anahtarını içeri aktarmak için anahtar türü ve eğri adı belirtmeniz gerekir.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file --kty EC-HSM --curve-name "P-256" KeyTransferPackage-ContosoFirstHSMkey.byok
```

Karşıya yükleme başarılı olursa, Azure CLı içeri aktarılan anahtarın özelliklerini görüntüler.

## <a name="next-steps"></a>Sonraki adımlar

Artık Anahtar Kasanızda HSM korumalı bu anahtarı kullanabilirsiniz. Daha fazla bilgi için [Bu fiyat ve Özellik Karşılaştırması](https://azure.microsoft.com/pricing/details/key-vault/)bölümüne bakın.

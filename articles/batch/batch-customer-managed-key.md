---
title: Azure Key Vault ve yönetilen kimlik ile Azure Batch hesabınız için müşteri tarafından yönetilen anahtarlar yapılandırın
description: Müşteri tarafından yönetilen anahtarları kullanarak Batch verilerinin nasıl şifreleneceğini öğrenin.
author: pkshultz
ms.topic: how-to
ms.date: 02/11/2021
ms.author: peshultz
ms.openlocfilehash: d3f10436b95aaeb5eb35a873c2a3862c1492bd47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100385073"
---
# <a name="configure-customer-managed-keys-for-your-azure-batch-account-with-azure-key-vault-and-managed-identity"></a>Azure Key Vault ve yönetilen kimlik ile Azure Batch hesabınız için müşteri tarafından yönetilen anahtarlar yapılandırın

Varsayılan olarak Azure Batch, sertifikalar, iş/görev meta verileri gibi Azure Batch hizmetinde depolanan tüm müşteri verilerini şifrelemek için platform tarafından yönetilen anahtarları kullanır. İsteğe bağlı olarak, Azure Batch depolanan verileri şifrelemek için kendi anahtarlarınızı (müşteri tarafından yönetilen anahtarlar) kullanabilirsiniz.

Sağladığınız anahtarların [Azure Key Vault](../key-vault/general/basic-concepts.md)oluşturulması ve [Azure kaynakları için yönetilen kimliklerle](../active-directory/managed-identities-azure-resources/overview.md)erişilmesi gerekir.

İki tür yönetilen kimlik vardır: [ *sistem tarafından atanan* ve *Kullanıcı tarafından atanan*](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).

Batch hesabınızı sistem tarafından atanan yönetilen kimlikle oluşturabilir veya müşterinin yönettiği anahtarlara erişecek şekilde, Kullanıcı tarafından atanan ayrı bir yönetilen kimlik oluşturabilirsiniz. Farkları anlamak için [karşılaştırma tablosunu](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) gözden geçirin ve çözümünüz için hangi seçeneğin en iyi şekilde çalışmadığını değerlendirin. Örneğin, birden çok Azure kaynağına erişmek için aynı yönetilen kimliği kullanmak istiyorsanız, Kullanıcı tarafından atanan bir yönetilen kimlik gerekecektir. Aksi takdirde, Batch hesabınızla ilişkili sistem tarafından atanan bir yönetilen kimlik yeterli olabilir. Kullanıcı tarafından atanan yönetilen kimliğin kullanılması, [Aşağıdaki örnekte](#create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys)gösterildiği gibi, toplu hesap oluşturma sırasında müşteri tarafından yönetilen anahtarları zorunlu kılmak için de sağlar.

## <a name="create-a-batch-account-with-system-assigned-managed-identity"></a>Sistem tarafından atanan yönetilen kimlikle Batch hesabı oluşturma

Kullanıcı tarafından atanan ayrı bir yönetilen kimliğe ihtiyacınız yoksa, Batch hesabınızı oluştururken sistem tarafından atanan yönetilen kimliği etkinleştirebilirsiniz.

### <a name="azure-portal"></a>Azure portalı

[Azure Portal](https://portal.azure.com/), Batch hesapları oluşturduğunuzda, **Gelişmiş** sekmesinde kimlik türünde **sistem atanan** ' ı seçin.

![Sistem tarafından atanan kimlik türüne sahip yeni bir Batch hesabının ekran görüntüsü.](./media/batch-customer-managed-key/create-batch-account.png)

Hesap oluşturulduktan sonra, **Özellikler** bölümünde **kimlik sorumlusu kimliği** alanında benzersiz bir GUID bulabilirsiniz. **Kimlik türü** gösterilecektir `System assigned` .

![Kimlik sorumlusu kimliği alanında benzersiz bir GUID gösteren ekran görüntüsü.](./media/batch-customer-managed-key/linked-batch-principal.png)

Bu toplu Iş hesabına Key Vault erişimini sağlamak için bu değere ihtiyacınız olacak.

### <a name="azure-cli"></a>Azure CLI’si

Yeni bir Batch hesabı oluşturduğunuzda, `SystemAssigned` parametresi için öğesini belirtin `--identity` .

```azurecli
resourceGroupName='myResourceGroup'
accountName='mybatchaccount'

az batch account create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' \
    --identity 'SystemAssigned'
```

Hesap oluşturulduktan sonra, bu hesapta sistem tarafından atanan yönetilen kimliğin etkinleştirildiğini doğrulayabilirsiniz. `PrincipalId`Key Vault bu toplu iş hesabına erişim vermek için bu değerin gerekli olacağı şekilde dikkat edin.

```azurecli
az batch account show \
    -n $accountName \
    -g $resourceGroupName \
    --query identity
```

> [!NOTE]
> Bir Batch hesabında oluşturulan sistem tarafından atanan yönetilen kimlik yalnızca Key Vault müşterinin yönettiği anahtarları almak için kullanılır. Bu kimlik, Batch havuzlarında kullanılamaz. Bir havuzda Kullanıcı tarafından atanan yönetilen kimlik kullanmak için bkz. [Batch havuzlarında yönetilen kimlikleri yapılandırma](managed-identity-pools.md).

## <a name="create-a-user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik oluşturma

İsterseniz, müşteri tarafından yönetilen Anahtarlarınıza erişmek için kullanılabilecek, [Kullanıcı tarafından atanan bir yönetilen kimlik oluşturabilirsiniz](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) .

Key Vault erişebilmesi için bu kimliğin **ISTEMCI kimliği** değerine ihtiyacınız olacaktır.

## <a name="configure-your-azure-key-vault-instance"></a>Azure Key Vault örneğinizi yapılandırma

Anahtarlarınızın üretilecektir Azure Key Vault, Batch hesabınızla aynı kiracıda oluşturulmalıdır. Aynı kaynak grubunda veya hatta aynı abonelikte olması gerekmez.

### <a name="create-an-azure-key-vault"></a>Azure Key Vault oluşturma

Azure Batch için müşteri tarafından yönetilen anahtarlarla [Azure Key Vault bir örnek oluştururken](../key-vault/general/quick-create-portal.md) , **geçici silme** ve **Temizleme korumasının** etkinleştirildiğinden emin olun.

![Key Vault oluşturma ekranının ekran görüntüsü.](./media/batch-customer-managed-key/create-key-vault.png)

### <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Azure Key Vault örneğine bir erişim ilkesi ekleme

Azure portal, Key Vault oluşturulduktan sonra, **Ayarlar**' ın altında bulunan **erişim ilkesi** ' nde, yönetilen kimlik ' i kullanarak Batch hesabı erişimini ekleyin. **Anahtar izinleri** altında **Al**, **sarmalama tuşu** ve **sarmalama tuşunu kaldır**' ı seçin.

![Erişim ilkesi ekle ekranını gösteren ekran görüntüsü.](./media/batch-customer-managed-key/key-permissions.png)

**Asıl** altındaki **Seç** alanında aşağıdakilerden birini girin:

- Sistem tarafından atanan yönetilen kimlik için: `principalId` daha önce aldığınız veya Batch hesabının adını girin.
- Kullanıcı tarafından atanan yönetilen kimlik: daha önce aldığınız **ISTEMCI kimliğini** veya Kullanıcı tarafından atanan yönetilen kimliğin adını girin.

![Sorumlu ekranının ekran görüntüsü.](./media/batch-customer-managed-key/principal-id.png)

### <a name="generate-a-key-in-azure-key-vault"></a>Azure Key Vault bir anahtar oluşturun

Azure portal, **anahtar** bölümündeki Key Vault örneğine gidin, **Oluştur/al**' ı seçin. **Anahtar türünü** `RSA` ve **RSA anahtar boyutunu** en az bit olacak şekilde seçin `2048` . `EC` anahtar türleri şu anda bir Batch hesabında müşteri tarafından yönetilen anahtar olarak desteklenmez.

![Bir anahtar oluşturma](./media/batch-customer-managed-key/create-key.png)

Anahtar oluşturulduktan sonra, yeni oluşturulan anahtara ve geçerli sürüme tıklayın, **Özellikler** bölümü altında **anahtar tanımlayıcısı** ' nı kopyalayın.  **Izin verilen işlemler**, **sarmalama anahtarı** ve **geri saral anahtarının** her ikisinin de işaretlenmiş olduğundan emin olun.

## <a name="enable-customer-managed-keys-on-a-batch-account"></a>Batch hesabında müşteri tarafından yönetilen anahtarları etkinleştirin

Yukarıdaki adımları izledikten sonra, Batch hesabınızda müşteri tarafından yönetilen anahtarları etkinleştirebilirsiniz.

### <a name="azure-portal"></a>Azure portalı

[Azure Portal](https://portal.azure.com/)Batch hesabı sayfasına gidin. **Şifreleme** bölümünde, **müşteri tarafından yönetilen anahtarı** etkinleştirin. Anahtar tanımlayıcıyı doğrudan kullanabilir veya anahtar kasasını seçip anahtar **kasası ve anahtar Seç** öğesine tıklayabilirsiniz.

![Şifreleme bölümünü ve müşterinin yönettiği anahtarı etkinleştirme seçeneğini gösteren ekran görüntüsü](./media/batch-customer-managed-key/encryption-page.png)

### <a name="azure-cli"></a>Azure CLI’si

Batch hesabı sistem tarafından atanan yönetilen kimlikle oluşturulduktan ve Key Vault Erişimi verildikten sonra, Batch hesabını `{Key Identifier}` parametresi altındaki URL ile güncelleştirin `keyVaultProperties` . **Encryption_key_source** olarak da ayarlayın `Microsoft.KeyVault` .

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_source Microsoft.KeyVault \
    --encryption_key_identifier {YourKeyIdentifier} 
```

## <a name="create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys"></a>Kullanıcı tarafından atanan yönetilen kimlik ve müşteri tarafından yönetilen anahtarlarla Batch hesabı oluşturma

Batch yönetimi .NET istemcisini kullanarak, Kullanıcı tarafından atanan yönetilen kimliğe ve müşterinin yönettiği anahtarlara sahip olacak bir Batch hesabı oluşturabilirsiniz.

```c#
EncryptionProperties encryptionProperties = new EncryptionProperties()
{
    KeySource = KeySource.MicrosoftKeyVault,
    KeyVaultProperties = new KeyVaultProperties()
    {
        KeyIdentifier = "Your Key Azure Resource Manager Resource ID"
    }
};

BatchAccountIdentity identity = new BatchAccountIdentity()
{
    Type = ResourceIdentityType.UserAssigned,
    UserAssignedIdentities = new Dictionary<string, BatchAccountIdentityUserAssignedIdentitiesValue>
    {
            ["Your Identity Azure Resource Manager ResourceId"] = new BatchAccountIdentityUserAssignedIdentitiesValue()
    }
};
var parameters = new BatchAccountCreateParameters(TestConfiguration.ManagementRegion, encryption:encryptionProperties, identity: identity);

var account = await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount", parameters); 
```

## <a name="update-the-customer-managed-key-version"></a>Müşteri tarafından yönetilen anahtar sürümünü Güncelleştir

Bir anahtarın yeni bir sürümünü oluşturduğunuzda, Batch hesabını yeni sürümü kullanacak şekilde güncelleştirin. Şu adımları izleyin:

1. Azure portal ' de Batch hesabınıza gidin ve şifreleme ayarlarını görüntüleyin.
2. Yeni anahtar sürümünün URI 'sini girin. Alternatif olarak, sürümü güncelleştirmek için Key Vault ve anahtarı bir kez daha seçebilirsiniz.
3. Yaptığınız değişiklikleri kaydedin.

Ayrıca sürümü güncelleştirmek için Azure CLı 'yi de kullanabilirsiniz.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourKeyIdentifierWithNewVersion} 
```

## <a name="use-a-different-key-for-batch-encryption"></a>Toplu Iş şifrelemesi için farklı bir anahtar kullanın

Toplu şifreleme için kullanılan anahtarı değiştirmek için şu adımları izleyin:

1. Batch hesabınıza gidin ve şifreleme ayarlarını görüntüleyin.
2. Yeni anahtar için URI girin. Alternatif olarak, Key Vault seçip yeni bir anahtar seçebilirsiniz.
3. Yaptığınız değişiklikleri kaydedin.

Azure CLı 'yi farklı bir anahtar kullanmak için de kullanabilirsiniz.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourNewKeyIdentifier} 
```

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

- **Mevcut Batch hesapları için müşteri tarafından yönetilen anahtarlar destekleniyor mu?** Hayır. Müşteri tarafından yönetilen anahtarlar yalnızca yeni Batch hesapları için desteklenir.
- **2048 bitten daha büyük RSA anahtar boyutlarını seçebilir miyim?** Evet, RSA anahtar boyutları `3072` ve `4096` bitleri de desteklenir.
- **Müşteri tarafından yönetilen anahtar iptal edildikten sonra hangi işlemler kullanılabilir?** Toplu işlem, müşteri tarafından yönetilen anahtara erişimi kaybederse, hesap silme işlemine izin verilir.
- **Key Vault anahtarını yanlışlıkla sildiğimde Batch hesabımın erişimini nasıl geri yükleyebilirim?** Temizleme koruması ve geçici silme etkin olduğundan, mevcut anahtarları geri yükleyebilirsiniz. Daha fazla bilgi için bkz. [kurtarma Azure Key Vault](../key-vault/general/key-vault-recovery.md).
- **Müşteri tarafından yönetilen anahtarları devre dışı bırakabilir miyim?** Batch hesabının şifreleme türünü dilediğiniz zaman "Microsoft yönetilen anahtar" olarak ayarlayabilirsiniz. Bundan sonra, anahtarı silmek veya değiştirmek için ücretsiz olursunuz.
- **Anahtarlarımı nasıl döndürebilirim?** Müşteri tarafından yönetilen anahtarlar otomatik olarak döndürülemez. Anahtarı döndürmek için, hesabın ilişkilendirildiği anahtar tanımlayıcısını güncelleştirin.
- **Access 'i geri yükledikten sonra Batch hesabının yeniden çalışması için ne kadar sürer?** Erişim geri yüklendikten sonra hesabın yeniden erişilebilir olması 10 dakika kadar sürebilir.
- **Batch hesabı, kaynaklarıma ne olur? kullanılamıyor mu?** Müşteri tarafından yönetilen anahtarlara toplu erişim kaybedildiğinde çalışan havuzların kaybolması, çalışmaya devam edecektir. Ancak, düğümler kullanılamayan bir duruma geçer ve görevler çalışmayı durdurur (ve yeniden kuyruğa olur). Erişim geri yüklendikten sonra düğümler yeniden kullanılabilir hale gelir ve görevler yeniden başlatılır.
- **Bu şifreleme mekanizması bir toplu Iş havuzundaki VM disklerine mi uygulanabilir?** Hayır. Bulut hizmeti yapılandırma havuzları için, işletim sistemi ve geçici disk için hiçbir şifreleme uygulanmaz. Sanal makine yapılandırma havuzları için, işletim sistemi ve belirtilen veri diskleri varsayılan olarak bir Microsoft Platformu yönetilen anahtarıyla şifrelenir. Şu anda bu diskler için kendi anahtarınızı belirtemezsiniz. Microsoft Platformu yönetilen anahtarıyla bir Batch havuzu için VM 'lerin geçici diskini şifrelemek üzere, [sanal makine yapılandırma](/rest/api/batchservice/pool/add#virtualmachineconfiguration) havuzunuzdaki [diskencryptionconfiguration](/rest/api/batchservice/pool/add#diskencryptionconfiguration) özelliğini etkinleştirmeniz gerekir. Son derece hassas ortamlarda, geçici disk şifrelemeyi etkinleştirmenizi ve hassas verileri işletim sistemi ve veri disklerinde depolamayı önlemeniz önerilir. Daha fazla bilgi için bkz. [disk şifrelemesi etkinken havuz oluşturma](./disk-encryption.md)
- **İşlem düğümlerinde kullanılabilir olan Batch hesabında sistem tarafından atanan yönetilen kimlik mi?** Hayır. Sistem tarafından atanan yönetilen kimlik Şu anda yalnızca müşteri tarafından yönetilen anahtar için Azure Key Vault erişim için kullanılır. İşlem düğümlerinde Kullanıcı tarafından atanan yönetilen kimlik kullanmak için bkz. [Batch havuzlarında yönetilen kimlikleri yapılandırma](managed-identity-pools.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Batch ' deki en iyi güvenlik uygulamaları](security-best-practices.md)hakkında daha fazla bilgi edinin.
- [Azure Key Vault](../key-vault/general/basic-concepts.md)hakkında daha fazla bilgi edinin.

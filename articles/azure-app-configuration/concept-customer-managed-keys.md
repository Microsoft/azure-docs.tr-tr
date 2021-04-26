---
title: Yapılandırma verilerinizi şifrelemek için müşteri tarafından yönetilen anahtarları kullanın
description: Müşteri tarafından yönetilen anahtarları kullanarak yapılandırma verilerinizi şifreleyin
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 07/28/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 46a1e99a7bc75efa85fcb5eff649c14af5abd17b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930509"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>Uygulama yapılandırma verilerinizi şifrelemek için müşteri tarafından yönetilen anahtarları kullanın
Azure Uygulama yapılandırması, [bekleyen gizli bilgileri şifreler](../security/fundamentals/encryption-atrest.md). Müşteri tarafından yönetilen anahtarların kullanımı, şifreleme anahtarlarınızı yönetmenize olanak tanıyarak gelişmiş veri koruması sağlar.  Yönetilen anahtar şifrelemesi kullanıldığında, uygulama yapılandırmasındaki tüm hassas bilgiler Kullanıcı tarafından sağlanmış bir Azure Key Vault anahtarıyla şifrelenir.  Bu, şifreleme anahtarını isteğe bağlı olarak döndürme olanağı sağlar.  Ayrıca, uygulama yapılandırma örneğinin anahtara erişimini iptal ederek Azure uygulama yapılandırmasının hassas bilgilere erişimini iptal etme olanağı da sağlar.

## <a name="overview"></a>Genel Bakış 
Azure Uygulama yapılandırması, Microsoft tarafından sunulan 256 bitlik bir AES şifreleme anahtarı kullanarak bekleyen hassas bilgileri şifreler. Her uygulama yapılandırma örneğinin, hizmet tarafından yönetilen ve hassas bilgileri şifrelemek için kullandığı kendi şifreleme anahtarı vardır. Hassas bilgiler, anahtar-değer çiftleri içinde bulunan değerleri içerir.  Müşteri tarafından yönetilen anahtar özelliği etkinleştirildiğinde, uygulama yapılandırması Azure Active Directory kimlik doğrulaması yapmak için uygulama yapılandırma örneğine atanan yönetilen bir kimlik kullanır. Daha sonra yönetilen kimlik Azure Key Vault çağırır ve uygulama yapılandırma örneğinin şifreleme anahtarını sarmalanmış. Sarmalanan şifreleme anahtarı daha sonra saklanır ve sarmalanmamış şifreleme anahtarı bir saat için uygulama yapılandırması içinde önbelleğe alınır. Uygulama yapılandırması, uygulama yapılandırma örneğinin şifreleme anahtarının sarmalanmamış sürümünü her saat yeniler. Bu, normal işletim koşullarında kullanılabilirliği sağlar. 

>[!IMPORTANT]
> Uygulama yapılandırma örneğine atanan kimlik artık örneğin şifreleme anahtarını sarmalamadan yetkilendirilmemiş veya yönetilen anahtar kalıcı olarak silinirse, uygulama yapılandırma örneğinde depolanan hassas bilgilerin şifresini çözmek artık mümkün olmayacaktır. Azure Key Vault [geçici silme](../key-vault/general/soft-delete-overview.md) işlevini kullanmak, şifreleme anahtarınızı yanlışlıkla silme olasılığını azaltır.

Kullanıcılar, Azure uygulama yapılandırma örneğinde müşterinin yönettiği anahtar özelliğini etkinleştirdiklerinde, hizmetin hassas bilgilerine erişme yeteneğini denetler. Yönetilen anahtar, kök şifreleme anahtarı işlevi görür. Kullanıcı, Anahtar Kasası erişim ilkesini değiştirerek uygulama yapılandırma örneğinin kendi yönetilen anahtarına erişimini iptal edebilir. Bu erişim iptal edildiğinde, uygulama yapılandırması bir saat içinde Kullanıcı verilerinin şifresini çözme özelliğini kaybedecektir. Bu noktada, uygulama yapılandırma örneği tüm erişim girişimlerini yasaklayacaktır. Bu durum, yönetilen anahtara bir kez yeniden hizmet erişimi verilerek kurtarılabilir.  Bir saat içinde, uygulama yapılandırması Kullanıcı verilerinin şifresini çözebilir ve normal koşullarda çalışamaz.

>[!NOTE]
>Tüm Azure uygulama yapılandırma verileri, yalıtılmış bir yedeklemede en fazla 24 saat boyunca depolanır. Bu, sarmalanmamış şifreleme anahtarını içerir. Bu veriler, hizmet veya hizmet ekibi tarafından hemen kullanılamaz. Acil bir geri yükleme durumunda Azure Uygulama yapılandırması kendisini yönetilen anahtar verilerinden yeniden iptal eder.

## <a name="requirements"></a>Gereksinimler
Azure Uygulama yapılandırması için müşteri tarafından yönetilen anahtar özelliğini başarıyla etkinleştirmek üzere aşağıdaki bileşenler gereklidir:
- Standart katman Azure uygulama yapılandırma örneği
- Geçici silme ve Temizleme koruması özellikleri etkin Azure Key Vault
- Key Vault içindeki bir RSA veya RSA-HSM anahtarı
    - Anahtarın geçerliliği dolmamalıdır, etkinleştirilmesi gerekir ve hem sarmalaması hem de sarmalama özelliği etkinleştirilmelidir

Bu kaynaklar yapılandırıldıktan sonra, Azure uygulama yapılandırmasının Key Vault anahtarını kullanmasına izin vermek için iki adım kalır:
1. Azure uygulama yapılandırma örneğine yönetilen bir kimlik atama
2. `GET` `WRAP` `UNWRAP` Hedef Key Vault erişim ilkesinde kimliğe, ve izinlere izin verin.

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>Azure uygulama yapılandırma örneğiniz için müşteri tarafından yönetilen anahtar şifrelemesini etkinleştirme
Başlamak için, düzgün şekilde yapılandırılmış bir Azure uygulama yapılandırma örneğine ihtiyacınız olacaktır. Henüz bir uygulama yapılandırma örneğiniz yoksa, bir tane ayarlamak için şu hızlı başlangıçlardan birini izleyin:
- [Azure Uygulama yapılandırması ile ASP.NET Core uygulaması oluşturma](quickstart-aspnet-core-app.md)
- [Azure Uygulama yapılandırması ile bir .NET Core uygulaması oluşturma](quickstart-dotnet-core-app.md)
- [Azure Uygulama yapılandırması ile .NET Framework uygulaması oluşturma](quickstart-dotnet-app.md)
- [Azure Uygulama yapılandırması ile bir Java Spring uygulaması oluşturma](quickstart-java-spring-app.md)

>[!TIP]
> Azure Cloud Shell, bu makaledeki komut satırı talimatlarını çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur.  .NET Core SDK dahil olmak üzere önceden yüklenmiş ortak Azure araçları vardır. Azure aboneliğinizde oturum açtıysanız, shell.azure.com adresinden [Azure Cloud Shell](https://shell.azure.com) başlatın.  [Belgelerimizi okuyarak](../cloud-shell/overview.md) Azure Cloud Shell hakkında daha fazla bilgi edinebilirsiniz

### <a name="create-and-configure-an-azure-key-vault"></a>Azure Key Vault oluşturma ve yapılandırma
1. Azure CLı kullanarak bir Azure Key Vault oluşturun.  Hem hem de `vault-name` `resource-group-name` Kullanıcı tarafından sağlandığını ve benzersiz olması gerektiğini unutmayın.  `contoso-vault` `contoso-resource-group` Bu örneklerde ve kullanırız.

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. Key Vault için geçici silme ve Temizleme korumasını etkinleştirin. `contoso-vault`1. adımda oluşturulan Key Vault () ve kaynak grubunun () adlarını değiştirin `contoso-resource-group` .

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. Key Vault anahtarı oluşturun. Bu anahtar için benzersiz bir değer sağlayın `key-name` ve `contoso-vault` 1. adımda oluşturulan Key Vault () adlarını değiştirin. Veya şifrelemeyi tercih etmek isteyip istemediğinizi belirtin `RSA` `RSA-HSM` .

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    Bu komutun çıktısı, oluşturulan anahtar için anahtar KIMLIĞINI ("KID") gösterir.  Bu alıştırmada daha sonra kullanmak için anahtar KIMLIĞINI bir yere unutmayın.  Anahtar KIMLIĞI şu biçimdedir: `https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}` .  Anahtar KIMLIĞINDE üç önemli bileşen vardır:
    1. Key Vault URI: ' https://{Anahtar Kasası}. kasa. Azure. net
    1. Key Vault anahtar adı: {Key Name}
    1. Key Vault anahtar sürümü: {Key Version}

1. Azure CLı kullanarak, uygulama yapılandırma örneğinizin adını ve önceki adımlarda kullanılan kaynak grubunu değiştirerek bir sistem tarafından atanmış yönetilen kimlik oluşturun. Yönetilen kimlik, yönetilen anahtara erişmek için kullanılacaktır. `contoso-app-config`Uygulama yapılandırma örneğinin adını göstermek için kullanıyoruz:
    
    ```azurecli
    az appconfig identity assign --name contoso-app-config --resource-group contoso-resource-group --identities [system]
    ```
    
    Bu komutun çıktısı, sistem tarafından atanan kimliğin asıl KIMLIĞINI ("PrincipalId") ve kiracı KIMLIĞINI ("Tenandıd") içerir.  Bu kimlikler, yönetilen anahtara kimlik erişimi vermek için kullanılacaktır.

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. Azure uygulama yapılandırma örneğinin yönetilen kimliği anahtar doğrulama, şifreleme ve şifre çözme işlemleri gerçekleştirmek için anahtara erişmesi gerekir. Erişmesi gereken belirli eylemler kümesi şunları içerir: `GET` , `WRAP` , ve `UNWRAP` anahtarlar için.  Erişim verilmesi, uygulama yapılandırma örneğinin yönetilen kimliğinin sorumlu KIMLIĞI için gereklidir. Bu değer, önceki adımda elde edildi. Aşağıda gösterildiği gibi `contoso-principalId` . Komut satırını kullanarak yönetilen anahtara izin verin:

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. Azure uygulama yapılandırma örneği yönetilen anahtara erişebildikten sonra, Azure CLı kullanarak hizmette müşteri tarafından yönetilen anahtar özelliğini etkinleştirebiliriz. Anahtar oluşturma adımları sırasında kaydedilen aşağıdaki özellikleri geri çekin: `key name` `key vault URI` .

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

Azure uygulama yapılandırma örneğiniz artık Azure Key Vault ' de depolanan, müşteri tarafından yönetilen bir anahtarı kullanacak şekilde yapılandırılmıştır.

## <a name="next-steps"></a>Sonraki Adımlar
Bu makalede, Azure uygulama yapılandırma örneğinizi şifreleme için müşteri tarafından yönetilen bir anahtar kullanacak şekilde yapılandırdınız.  [Hizmetinizi Azure tarafından yönetilen kimliklerle tümleştirmeyi](howto-integrate-azure-managed-service-identity.md)öğrenin.

---
title: Azure Cosmos DB anahtarlarını depolamak ve erişmek için Key Vault kullanma
description: Azure Cosmos DB bağlantı dizesi, anahtarlar, uç noktaları depolamak ve erişmek için Azure Key Vault kullanın.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: how-to
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: a9bea0664f99a21ac734de666c802e9875ff00b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359337"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Azure Key Vault kullanarak Azure Cosmos anahtarlarının güvenliğini sağlama 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

>[!IMPORTANT]
> Azure Cosmos DB anahtarlarına erişmek için önerilen çözüm, [sistem tarafından atanan yönetilen kimlik](managed-identity-based-authentication.md)kullanmaktır. Hizmetiniz yönetilen kimliklerden yararlanıyorsa, [sertifika tabanlı çözümü](certificate-based-authentication.md)kullanın. Hem yönetilen kimlik çözümü hem de sertifika tabanlı çözüm ihtiyaçlarınızı karşılamıyorsa, lütfen aşağıdaki Anahtar Kasası çözümünü kullanın.

Uygulamalarınız için Azure Cosmos DB kullanırken, uç noktayı ve uygulamanın yapılandırma dosyası içindeki anahtarı kullanarak veritabanına, koleksiyonlara ve belgelere erişebilirsiniz.  Bununla birlikte, anahtar ve URL 'YI doğrudan uygulama koduna yerleştirmek güvenli değildir, çünkü tüm kullanıcılara açık metin biçiminde kullanılabilir. Uç noktanın ve anahtarların sağlanmasını ama güvenli bir mekanizma üzerinden sağlanmasını istersiniz. İşte bu noktada Azure Key Vault uygulama gizli dizilerini güvenle depolamanıza ve yönetmenize yardımcı olabilir.

Key Vault Azure Cosmos DB erişim anahtarlarını depolamak ve okumak için aşağıdaki adımlar gereklidir:

* Anahtar kasası oluşturma  
* Key Vault Azure Cosmos DB erişim tuşları ekleyin  
* Azure Web uygulaması oluşturma  
* & Key Vault okumak için uygulamayı kaydetme izinleri verin  


## <a name="create-a-key-vault"></a>Anahtar kasası oluşturma

1. [Azure Portal](https://portal.azure.com/)oturum açın.  
2. **Kaynak oluştur > güvenlik > Key Vault**' yı seçin.  
3. **Anahtar kasası oluşturma** bölümünde aşağıdaki bilgileri sağlayın:  
   * **Ad:** Key Vault için benzersiz bir ad sağlayın.  
   * **Abonelik:** Kullanacağınız aboneliği seçin.  
   * **Kaynak Grubu** altında **Yeni oluştur**’u seçin ve bir kaynak grubu adı girin.  
   * Konum aşağı açılan menüsünde bir konum seçin.  
   * Diğer seçenekleri varsayılanlarına bırakın.  
4. Yukarıdaki bilgileri girdikten sonra **Oluştur**’u seçin.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Key Vault Azure Cosmos DB erişim anahtarları ekleyin.
1. Önceki adımda oluşturduğunuz Key Vault gidin, **gizlilikler** sekmesini açın.  
2. **+ Oluştur/Içeri aktar**' ı seçin. 

   * **Karşıya yükleme seçenekleri** Için **el ile** ' yi seçin.
   * Gizli dizi için bir **ad** girin
   * Cosmos DB hesabınızın bağlantı dizesini **değer** alanına girin. Ardından **Oluştur**’u seçin.

   :::image type="content" source="./media/access-secrets-from-keyvault/create-a-secret.png" alt-text="Gizli anahtar oluşturma":::

4. Gizli dizi oluşturulduktan sonra açın ve aşağıdaki biçimde olan * * gizli tanımlayıcısını kopyalayın. Sonraki bölümde bu tanımlayıcıyı kullanacaksınız. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Azure Web uygulaması oluşturma

1. Bir Azure Web uygulaması oluşturun veya uygulamayı [GitHub deposundan](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/Demo/keyvaultdemo)indirebilirsiniz. Bu basit bir MVC uygulamasıdır.  

2. İndirilen uygulamayı sıkıştırmayı açın ve **HomeController. cs** dosyasını açın. Aşağıdaki satırdaki gizli KIMLIĞI güncelleştirin:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. Dosyayı **kaydedin** , çözümü **derleyin** .  
4. Ardından uygulamayı Azure 'a dağıtın. Projeye sağ tıklayın ve **Yayımla**' yı seçin. Yeni bir App Service profili oluşturun (uygulama WebAppKeyVault1 adını verebilir ve **Yayımla**' yı seçebilirsiniz.   

5. Uygulama dağıtıldıktan sonra. Azure portal, dağıttığınız Web uygulamasına gidin ve bu uygulamanın **yönetilen hizmet kimliğini** açın.  

   :::image type="content" source="./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png" alt-text="Yönetilen hizmet kimliği":::

Uygulamayı şimdi çalıştıracaksınız, Key Vault bu uygulama için herhangi bir izin vermediğinden aşağıdaki hatayı görürsünüz.

:::image type="content" source="./media/access-secrets-from-keyvault/app-deployed-without-access.png" alt-text="Uygulama erişim olmadan dağıtıldı":::

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>& Key Vault okumak için uygulamayı kaydetme izinleri verin

Bu bölümde, uygulamayı Azure Active Directory kaydeder ve uygulamanın Key Vault okuyabilmesi için izinler verirsiniz. 

1. Azure portal gidin, önceki bölümde oluşturduğunuz **Key Vault** açın.  

2. **Erişim ilkelerini** açın, **+ Yeni Ekle** ' yi seçin ve dağıttığınız Web uygulamasını bulun, izinler ' i seçin ve **Tamam**' ı seçin.  

   :::image type="content" source="./media/access-secrets-from-keyvault/add-access-policy.png" alt-text="Erişim İlkesi Ekle":::

Artık uygulamayı çalıştırırsanız, Key Vault gizli anahtarı okuyabilirsiniz.

:::image type="content" source="./media/access-secrets-from-keyvault/app-deployed-with-access.png" alt-text="Gizli anahtar ile dağıtılan uygulama":::
 
Benzer şekilde, anahtar kasasına erişmek için bir kullanıcı ekleyebilirsiniz. **Erişim ilkeleri** ' ni seçerek ve ardından uygulamayı Visual Studio 'dan çalıştırmak için ihtiyacınız olan tüm izinleri vererek kendinizi Key Vault eklemeniz gerekir. Bu uygulama masaüstünüzden çalışırken kimliğinizi alır.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Cosmos DB için bir güvenlik duvarı yapılandırmak için bkz. [güvenlik duvarı destek](how-to-configure-firewall.md) makalesi.
* Sanal ağ hizmeti uç noktasını yapılandırmak için, bkz. [VNET hizmeti uç noktası kullanarak güvenli erişim](how-to-configure-vnet-service-endpoint.md) .

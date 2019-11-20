---
title: Azure Cosmos DB anahtarlarını depolamak ve erişmek için Key Vault kullanma
description: Azure Cosmos DB bağlantı dizesi, anahtarlar, uç noktaları depolamak ve erişmek için Azure Key Vault kullanın.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 55e6bbc338c1ac6f9ef935b4a3a05c32f2b5e9f5
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755218"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Azure Key Vault kullanarak Azure Cosmos anahtarlarını güvenli hale getirme 

Uygulamalarınız için Azure Cosmos DB kullanırken, uç noktayı ve uygulamanın yapılandırma dosyası içindeki anahtarı kullanarak veritabanına, koleksiyonlara ve belgelere erişebilirsiniz.  Bununla birlikte, anahtar ve URL 'YI doğrudan uygulama koduna yerleştirmek güvenli değildir, çünkü tüm kullanıcılara açık metin biçiminde kullanılabilir. Uç noktanın ve anahtarların kullanılabilir olduğundan, ancak güvenli bir mekanizmaya karşı emin olmak istiyorsunuz. Bu, Azure Key Vault uygulama gizli dizilerini güvenli bir şekilde depolamanıza ve yönetmenize yardımcı olabilir.

Key Vault Azure Cosmos DB erişim anahtarlarını depolamak ve okumak için aşağıdaki adımlar gereklidir:

* Anahtar kasası oluşturma  
* Key Vault Azure Cosmos DB erişim tuşları ekleyin  
* Azure Web uygulaması oluşturma  
* & Key Vault okumak için uygulamayı kaydetme izinleri verin  


## <a name="create-a-key-vault"></a>Anahtar kasası oluşturma

1. [Azure portalında](https://portal.azure.com/)oturum açın.  
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

   * **Karşıya yükleme seçenekleri**Için **el ile** ' yi seçin.
   * Gizli dizi için bir **ad** girin
   * Cosmos DB hesabınızın bağlantı dizesini **değer** alanına girin. Sonra **Oluştur**' u seçin.

   ![Gizli anahtar oluşturma](./media/access-secrets-from-keyvault/create-a-secret.png)

4. Gizli dizi oluşturulduktan sonra açın ve aşağıdaki biçimde olan * * gizli tanımlayıcısını kopyalayın. Sonraki bölümde bu tanımlayıcıyı kullanacaksınız. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Azure Web uygulaması oluşturma

1. Bir Azure Web uygulaması oluşturun veya uygulamayı [GitHub deposundan](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/Demo/keyvaultdemo)indirebilirsiniz. Bu basit bir MVC uygulamasıdır.  

2. İndirilen uygulamayı sıkıştırmayı açın ve **HomeController.cs** dosyasını açın. Aşağıdaki satırdaki gizli KIMLIĞI güncelleştirin:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. Dosyayı **kaydedin** , çözümü **derleyin** .  
4. Ardından uygulamayı Azure 'a dağıtın. Projeye sağ tıklayın ve **Yayımla**' yı seçin. Yeni bir App Service profili oluşturun (uygulama WebAppKeyVault1 adını verebilir ve **Yayımla**' yı seçebilirsiniz.   

5. Uygulama dağıtıldıktan sonra. Azure portal, dağıttığınız Web uygulamasına gidin ve bu uygulamanın **yönetilen hizmet kimliğini** açın.  

   ![Yönetilen hizmet kimliği](./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png)

Uygulamayı şimdi çalıştıracaksınız, Key Vault bu uygulama için herhangi bir izin vermediğinden aşağıdaki hatayı görürsünüz.

![Uygulama erişim olmadan dağıtıldı](./media/access-secrets-from-keyvault/app-deployed-without-access.png)

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>& Key Vault okumak için uygulamayı kaydetme izinleri verin

Bu bölümde, uygulamayı Azure Active Directory kaydeder ve uygulamanın Key Vault okuyabilmesi için izinler verirsiniz. 

1. Azure portal gidin, önceki bölümde oluşturduğunuz **Key Vault** açın.  

2. **Erişim ilkelerini**açın, **+ Yeni Ekle** ' yi seçin ve dağıttığınız Web uygulamasını bulun, izinler ' i seçin ve **Tamam**' ı seçin.  

   ![Erişim İlkesi Ekle](./media/access-secrets-from-keyvault/add-access-policy.png)

Artık uygulamayı çalıştırırsanız, Key Vault gizli anahtarı okuyabilirsiniz.

![Gizli anahtar ile dağıtılan uygulama](./media/access-secrets-from-keyvault/app-deployed-with-access.png)
 
Benzer şekilde, anahtar kasasına erişmek için bir kullanıcı ekleyebilirsiniz. **Erişim ilkeleri** ' ni seçerek ve ardından uygulamayı Visual Studio 'dan çalıştırmak için ihtiyacınız olan tüm izinleri vererek kendinizi Key Vault eklemeniz gerekir. Bu uygulama masaüstünüzden çalışırken kimliğinizi alır.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Cosmos DB için bir güvenlik duvarı yapılandırmak için bkz. [güvenlik duvarı destek](firewall-support.md) makalesi.
* Sanal ağ hizmeti uç noktasını yapılandırmak için, bkz. [VNET hizmeti uç noktası kullanarak güvenli erişim](vnet-service-endpoint.md) .

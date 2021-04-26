---
title: Azure Key Vault sertifikalarla TLS sonlandırma
description: HTTPS özellikli dinleyicilerine eklenen sunucu sertifikaları için Azure Application Gateway Key Vault nasıl tümleştirileceğini öğrenin.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: victorh
ms.openlocfilehash: 8a64956deb7849568e70e94c9b58170df60db1e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775756"
---
# <a name="tls-termination-with-key-vault-certificates"></a>Key Vault sertifikaları ile TLS sonlandırma

[Azure Key Vault](../key-vault/general/overview.md) , gizli dizileri, anahtarları ve TLS/SSL sertifikalarını korumak için kullanabileceğiniz, platform tarafından yönetilen bir gizli depodır. Azure Application Gateway, HTTPS özellikli dinleyicilerine eklenen sunucu sertifikaları için Key Vault tümleştirmeyi destekler. Bu destek, Application Gateway v2 SKU 'SU ile sınırlıdır.

Key Vault tümleştirme, TLS sonlandırma için iki model sunar:

- Dinleyiciye iliştirilmiş TLS/SSL sertifikaları açıkça sağlayabilirsiniz. Bu model, TLS/SSL sertifikalarını TLS sonlandırma için Application Gateway geçirmek için geleneksel bir yoldur.
- İsteğe bağlı olarak, HTTPS özellikli bir dinleyici oluştururken Mevcut bir Key Vault sertifikasına veya parolaya bir başvuru sağlayabilirsiniz.

Key Vault ile tümleştirme Application Gateway aşağıdakiler de dahil olmak üzere birçok avantaj sunar:

- Daha güçlü güvenlik, TLS/SSL sertifikaları uygulama geliştirme ekibi tarafından doğrudan işlenmemektedir. Tümleştirme, ayrı bir güvenlik ekibinin şunları yapmasına izin verir:
  * Uygulama ağ geçitlerini ayarlayın.
  * Uygulama ağ geçidi ömürleri kontrol edin.
  * Anahtar Kasanızda depolanan sertifikalara erişmek için seçili uygulama ağ geçitlerine izin verin.
- Mevcut sertifikaları anahtar kasanıza aktarma desteği. Ya da güvenilir Key Vault iş ortaklarıyla yeni sertifikalar oluşturup yönetmek için Key Vault API 'Leri kullanın.
- Anahtar Kasanızda depolanan sertifikaların otomatik yenilenmesi için destek.

Application Gateway Şu anda yalnızca yazılım tarafından doğrulanan sertifikaları desteklemektedir. Donanım güvenlik modülü (HSM)-doğrulanan sertifikalar desteklenmez. Application Gateway Key Vault sertifikaları kullanacak şekilde yapılandırıldıktan sonra, örnekleri sertifikayı Key Vault alır ve TLS sonlandırma için yerel olarak yükler. Örnekler Ayrıca Key Vault, varsa sertifikanın yenilenen bir sürümünü almak için 4 saat aralıklarında yoklama yapılır. Güncelleştirilmiş bir sertifika bulunursa, şu anda HTTPS dinleyicisiyle ilişkili olan TLS/SSL sertifikası otomatik olarak döndürülür.

> [!NOTE]
> Azure portal, gizli dizileri değil yalnızca Keykasa sertifikalarını destekler. Application Gateway, anahtar kasasından gizli dizileri de destekler, ancak yalnızca PowerShell, CLı, API, ARM şablonları gibi portal olmayan kaynaklar aracılığıyla desteklenir. 

## <a name="how-integration-works"></a>Tümleştirme nasıl çalışacaktır?

Key Vault ile tümleştirme Application Gateway üç adımlı bir yapılandırma işlemi gerektirir:

1. **Kullanıcı tarafından atanan yönetilen kimlik oluşturma**

   Application Gateway, Kullanıcı tarafından atanan mevcut bir yönetilen kimliği oluşturur veya yeniden kullanabilirsiniz. Bu, Key Vault sertifikaları sizin yerinize almak için kullanır. Daha fazla bilgi için, bkz. [Azure Portal kullanarak Kullanıcı tarafından atanan yönetilen kimlik için rol oluşturma, listeleme, silme veya atama](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). Bu adım Azure Active Directory kiracısında yeni bir kimlik oluşturur. Kimlik, kimlik oluşturmak için kullanılan abonelik tarafından güvenilirdir.

1. **Anahtar kasanızı yapılandırma**

   Daha sonra mevcut bir sertifikayı içeri aktarırsınız veya Anahtar Kasanızda yeni bir sertifika oluşturacaksınız. Sertifika, uygulama ağ geçidi aracılığıyla çalışan uygulamalar tarafından kullanılacaktır. Bu adımda, parola daha az, Base-64 kodlu PFX dosyası depolamayı da sağlayan bir Key Vault gizli dizisi de kullanabilirsiniz. Key Vault, bu tür nesneler ile kullanılabilen otomatik yenileme özelliği nedeniyle bir "sertifika" türü kullanmanızı öneririz. Bir sertifika veya gizli dizi oluşturduktan sonra, kimliğe gizli erişim izni verilmesini sağlamak için Key Vault erişim Ilkeleri tanımlamanız gerekir.
   
   > [!IMPORTANT]
   > 15 Mart 2021 ' den itibaren, Key Vault Azure Application Gateway güvenilen hizmetlerden biri olarak tanır, böylece Azure 'da güvenli bir ağ sınırı oluşturmanıza olanak tanınır. Bu, tüm ağlardan gelen trafiğe erişimi reddedebilmenizi sağlar (internet trafiği dahil) Key Vault, ancak aboneliğiniz altında Application Gateway kaynak için yine de erişilebilir hale getirir. 

   > Application Gateway kısıtlanmış bir ağda Key Vault aşağıdaki şekilde yapılandırabilirsiniz. <br />
   > a) Key Vault ağ dikey penceresinde <br />
   > b) "güvenlik duvarı ve sanal ağlar" sekmesinde Özel uç nokta ve seçili ağları seçin <br/>
   > c) ardından sanal ağları kullanarak Application Gateway sanal ağını ve alt ağını ekleyin. İşlem sırasında, ' Microsoft. Keykasası ' hizmet uç noktasını da onay kutusunu seçerek yapılandırın. <br/>
   > d) son olarak, güvenilen hizmetlerin Key Vault güvenlik duvarını atlamasına izin vermek için "Evet" seçeneğini belirleyin. <br/>
   > 
   > ![Key Vault güvenlik duvarı](media/key-vault-certs/key-vault-firewall.png)


   > [!NOTE]
   > Application Gateway 'i Azure CLı veya PowerShell kullanarak ya da Azure portal dağıtılan bir Azure uygulaması aracılığıyla bir ARM şablonuyla dağıtırsanız, SSL sertifikası anahtar kasasında Base64 kodlamalı PFX dosyası olarak depolanır. [Dağıtım sırasında güvenli parametre değeri geçirmek için Azure Key Vault kullanma](../azure-resource-manager/templates/key-vault-parameter.md)adımlarını gerçekleştirmeniz gerekir. 
   >
   > Olarak ayarlanması özellikle önemlidir `enabledForTemplateDeployment` `true` . Sertifika parolasız olabilir veya bir parolası olabilir. Parolası olan bir sertifika söz konusu olduğunda, aşağıdaki örnek, `sslCertificates` `properties` bir uygulama ağ GEÇIDI için ARM şablon yapılandırmasında girişi için olası bir yapılandırma gösterir. Ve değerleri, `appGatewaySSLCertificateData` `appGatewaySSLCertificatePassword` anahtar kasasından [dinamik kimliğe sahip başvuru gizli](../azure-resource-manager/templates/key-vault-parameter.md#reference-secrets-with-dynamic-id)dizileri bölümünde açıklandığı gibi aranır. Aramanın nasıl gerçekleştiğini görmek için, öğesinden geriye doğru başvuruları izleyin `parameters('secretName')` . Sertifika passwordless ise `password` girişi atlayın.
   >   
   > ```
   > "sslCertificates": [
   >     {
   >         "name": "appGwSslCertificate",
   >         "properties": {
   >             "data": "[parameters('appGatewaySSLCertificateData')]",
   >             "password": "[parameters('appGatewaySSLCertificatePassword')]"
   >         }
   >     }
   > ]
   > ```

1. **Uygulama ağ geçidini yapılandırma**

   Önceki iki adımı tamamladıktan sonra, mevcut bir uygulama ağ geçidini Kullanıcı tarafından atanan yönetilen kimliği kullanacak şekilde ayarlayabilir veya değiştirebilirsiniz. Daha fazla bilgi için bkz. [set-Azapplicationgatewayıdentity](/powershell/module/az.network/set-azapplicationgatewayidentity).

   HTTP dinleyicisinin TLS/SSL sertifikasını, Key Vault sertifikası veya gizli dizi KIMLIĞI 'nin tüm URI 'sini işaret etmek üzere de yapılandırabilirsiniz.

   ![Anahtar Kasası sertifikaları](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Sonraki adımlar

[Azure PowerShell kullanarak TLS sonlandırmasını Key Vault sertifikalarla yapılandırma](configure-keyvault-ps.md)

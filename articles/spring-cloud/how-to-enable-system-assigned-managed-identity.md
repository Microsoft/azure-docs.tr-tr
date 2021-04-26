---
title: Azure Spring Cloud uygulaması için sistem tarafından atanan yönetilen kimliği etkinleştirme
description: Uygulama için sistem tarafından atanan yönetilen kimliği etkinleştirme.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/13/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 2fbdd46b872c4c70999355d457045f4aac8aa34c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878732"
---
# <a name="how-to-enable-system-assigned-managed-identity-for-azure-spring-cloud-application"></a>Azure yay bulut uygulaması için sistem tarafından atanan yönetilen kimliği etkinleştirme

**Bu makale şu şekilde geçerlidir:** ✔️ Java ✔️ C #

Azure kaynakları için Yönetilen kimlikler, Azure Spring Cloud uygulamanız gibi bir Azure kaynağına Azure Active Directory otomatik olarak yönetilen bir kimlik sağlar. Bu kimliği, kodunuzda kimlik bilgileri olmadan Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmak için kullanabilirsiniz.

Bu makalede, Azure portal ve CLı (Version 0.2.4 'ten ulaşılabilir) kullanılarak bir Azure yay bulutu uygulaması için sistem tarafından atanan yönetilen kimliklerin nasıl etkinleştirileceği ve devre dışı bırakılacağı gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar
Azure kaynakları için Yönetilen kimlikler hakkında bilgi sahibi değilseniz bkz. [genel bakış bölümü](../active-directory/managed-identities-azure-resources/overview.md).
Dağıtılmış bir Azure yay bulutu örneğine ihtiyacınız olacak. [Azure CLI kullanarak dağıtmak Için hızlı](spring-cloud-quickstart.md)başlangıcı izleyin.

## <a name="add-a-system-assigned-identity"></a>Sistem tarafından atanan kimlik ekleme
Sistem tarafından atanan kimlik ile uygulama oluşturmak, uygulamada ek bir özellik ayarlanmasını gerektirir.

### <a name="using-azure-portal"></a>Azure portalını kullanma
[Azure Portal](https://portal.azure.com/)yönetilen bir kimlik ayarlamak için, önce bir uygulama oluşturun ve sonra özelliği etkinleştirin.

1. Portalda genellikle yaptığınız gibi bir uygulama oluşturun. Portalda bu sayfaya gidin.
2. Sol gezinti bölmesindeki **Ayarlar** grubuna gidin.
3. **Kimlik** seçin.
4. **Sistem atandı** sekmesinde **durumu** *Açık* olarak değiştirin. **Kaydet**’e tıklayın.

 ![Portalda yönetilen kimlik](./media/spring-cloud-managed-identity/identity-1.png)

### <a name="using-azure-cli"></a>Azure CLI’yı kullanma
Uygulama oluşturma sırasında veya var olan bir uygulamada, sistem tarafından atanan yönetilen kimliği etkinleştirebilirsiniz.

**Uygulama oluşturma sırasında sistem tarafından atanan yönetilen kimliği etkinleştir**

Aşağıdaki örnek, parametresi tarafından istenen şekilde sistem tarafından atanan yönetilen kimlik ile *app_name* adlı bir uygulama oluşturur `--assign-identity` .

```azurecli
az spring-cloud app create -n app_name -s service_name -g resource_group_name --assign-identity
```

**Mevcut bir uygulamada sistem tarafından atanan yönetilen kimliği etkinleştir** `az spring-cloud app identity assign` Mevcut bir uygulamada sistem tarafından atanan kimliği etkinleştirmek için komutunu kullanın.

```azurecli
az spring-cloud app identity assign -n app_name -s service_name -g resource_group_name
```

## <a name="obtain-tokens-for-azure-resources"></a>Azure kaynakları için belirteçleri alma
Bir uygulama, Azure Key Vault gibi Azure Active Directory tarafından korunan diğer kaynaklara erişmek için belirteçleri almak üzere kendi yönetilen kimliğini kullanabilir. Bu belirteçler, uygulamanın belirli bir kullanıcısı yerine kaynağa erişen uygulamayı temsil eder.

[Uygulamanızdaki erişime izin vermek için hedef kaynağı yapılandırmanız](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)gerekebilir. Örneğin, Key Vault erişmek için bir belirteç istemeniz durumunda uygulamanızın kimliğini içeren bir erişim ilkesi eklediğinizden emin olun. Aksi takdirde, belirteci içerse bile Key Vault çağrılarınız reddedilir. Azure Active Directory belirteçlerini destekleyen kaynaklar hakkında daha fazla bilgi edinmek için bkz. [Azure AD kimlik doğrulamasını destekleyen Azure hizmetleri](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

Azure Spring Cloud, Azure sanal makinesi ile belirteç alımı için aynı uç noktayı paylaşır. Belirteç almak için Java SDK 'sını veya Spring Boot başlangıçlarını kullanmanızı öneririz.  Bkz. [sanal makine belirtecini](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) çeşitli kod ve betik örnekleri için kullanma ve belirteç süre sonu ve HTTP hatalarını işleme gibi önemli konularda yönergeler.

Önerilir: belirteçleri almak için Java SDK 'sını veya Spring Boot başlangıçlarını kullanın.  [Sonraki adımlarda](#next-steps)bulunan örneklere bakın.

## <a name="disable-system-assigned-identity-from-an-app"></a>Uygulamadan sistem tarafından atanan kimliği devre dışı bırak
Sistem tarafından atanan bir kimliğin kaldırılması, Azure AD 'den de silinir. Uygulama kaynağını silme, sistem tarafından atanan kimlikleri Azure AD 'den otomatik olarak kaldırır.

### <a name="using-azure-portal"></a>Azure portalını kullanma
Sistem tarafından atanan yönetilen kimliği, artık ihtiyaç duyulmayan bir uygulamadan kaldırmak için:

1. Azure yay bulutu örneğini içeren Azure aboneliğiyle ilişkili bir hesabı kullanarak [Azure Portal](https://portal.azure.com/) oturum açın.
1. İstediğiniz sanal makineye gidin ve **kimlik**' i seçin.
1. **Sistem tarafından atanan** / **durum** altında **kapalı** ' yı seçin ve ardından **Kaydet**' e tıklayın:

 ![Yönetilen kimlik](./media/spring-cloud-managed-identity/remove-identity.png)

### <a name="using-azure-cli"></a>Azure CLI’yı kullanma
Sisteme atanan yönetilen kimliği, artık ihtiyaç duyulmayan bir uygulamadan kaldırmak için aşağıdaki komutu kullanın:
```azurecli
az spring-cloud app identity remove -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>Sonraki adımlar

* [Spring Boot Starter 'da yönetilen kimliklerle Azure Key Vault erişme](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets/README.md#use-msi--managed-identities)
* [Azure kaynakları için Yönetilen kimlikler hakkında daha fazla bilgi edinin](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Java SDK ile yönetilen kimlikler kullanma](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)

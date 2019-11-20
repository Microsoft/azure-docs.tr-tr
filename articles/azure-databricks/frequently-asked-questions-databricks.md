---
title: 'Azure Databricks: genel sorular ve yardım'
description: Azure Databricks hakkındaki yaygın soruların ve sorun giderme bilgilerinin yanıtlarını alın.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 3febdbc8f55f6ccd65eba97de984a9a38ddf214e
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129328"
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Azure Databricks hakkında sık sorulan sorular

Bu makalede Azure Databricks ilgili olabilecek en önemli sorular listelenmektedir. Ayrıca, Databricks kullanırken karşılaşabileceğiniz bazı yaygın sorunları da listeler. Daha fazla bilgi için bkz. [Azure Databricks nedir?](what-is-azure-databricks.md). 

## <a name="can-i-use-azure-key-vault-to-store-keyssecrets-to-be-used-in-azure-databricks"></a>Azure Databricks kullanılacak anahtarları/gizli dizileri depolamak için Azure Key Vault kullanabilir miyim?
Evet. Azure Databricks ile kullanmak üzere anahtarları/gizli dizileri depolamak için Azure Key Vault kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure Key Vault ile desteklenen kapsamlar](/azure/databricks/security/secrets/secret-scopes).


## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>Databricks ile Azure sanal ağlarını kullanabilir miyim?
Evet. Azure Databricks ile bir Azure sanal ağı (VNET) kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure sanal ağınızda Azure Databricks dağıtma](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

## <a name="how-do-i-access-azure-data-lake-storage-from-a-notebook"></a>Nasıl yaparım? erişim Azure Data Lake Storage bir not defterine mı? 

Şu adımları uygulayın:
1. Azure Active Directory (Azure AD) ' de bir hizmet sorumlusu sağlayın ve anahtarını kaydedin.
1. Data Lake Storage ' deki hizmet sorumlusuna gerekli izinleri atayın.
1. Data Lake Storage bir dosyaya erişmek için not defterinde hizmet sorumlusu kimlik bilgilerini kullanın.

Daha fazla bilgi için bkz. [Azure Databricks ile Azure Data Lake Storage kullanma](/azure/databricks/data/data-sources/azure/azure-datalake).

## <a name="fix-common-problems"></a>Sık karşılaşılan sorunları giderme

Databricks ile karşılaşabileceğiniz bazı sorunlar aşağıda verilmiştir.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Sorun: Bu abonelik ' Microsoft. Databricks ' ad alanını kullanmak için kayıtlı değil

#### <a name="error-message"></a>Hata iletisi

"Bu abonelik, ' Microsoft. Databricks ' ad alanını kullanmak için kayıtlı değil. Aboneliklerin nasıl kaydedileceği https://aka.ms/rps-not-found bakın. (Kod: MissingSubscriptionRegistration) "

#### <a name="solution"></a>Çözüm

1. [Azure Portal](https://portal.azure.com) gidin.
1. **Abonelikler**, kullandığınız abonelik ve ardından **kaynak sağlayıcıları**' nı seçin. 
1. Kaynak sağlayıcıları listesinde, **Microsoft. Databricks**' de, **Kaydet**' i seçin. Kaynak sağlayıcısını kaydetmek için abonelikte katkıda bulunan veya sahip rolü olmalıdır.


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Sorun: {email} hesabınızda, Azure portal Databricks çalışma alanı kaynağında sahip veya katkıda bulunan rolü yok

#### <a name="error-message"></a>Hata iletisi

"{Email} hesabınızın Azure portal Databricks çalışma alanı kaynağında sahip veya katkıda bulunan rolü yok. Bu hata, Kiracıdaki bir Konuk Kullanıcı olduğunuzda da oluşabilir. Yöneticinizden size erişim vermesini veya sizi doğrudan Databricks çalışma alanına kullanıcı olarak eklemesini isteyin. " 

#### <a name="solution"></a>Çözüm

Bu soruna yönelik birkaç çözüm aşağıda verilmiştir:

* Kiracıyı başlatmak için konuk kullanıcı olarak değil, kiracının normal kullanıcısı olarak oturum açmış olmanız gerekir. Ayrıca, Databricks çalışma alanı kaynağında katkıda bulunan bir rolünüzün olması gerekir. Azure portal Databricks çalışma alanınızın içindeki **erişim denetimi (IAM)** sekmesinden bir kullanıcı erişimi verebilirsiniz.

* Bu hata, e-posta etki alanı adınız Azure AD 'de birden çok dizine atanırsa da oluşabilir. Bu sorunu geçici olarak çözmek için Databricks çalışma alanınıza sahip aboneliği içeren dizinde yeni bir kullanıcı oluşturun.

    a. Azure portal Azure AD 'ye gidin. **Kullanıcı eklemek** > **Kullanıcılar ve gruplar ' ı** seçin.

    b. `@<your_domain>` e-posta yerine `@<tenant_name>.onmicrosoft.com` bir e-posta ile Kullanıcı ekleyin. Bu seçeneği, Azure portal Azure AD altında **özel etki alanlarında**bulabilirsiniz.
    
    c. Bu yeni kullanıcıya Databricks çalışma alanı kaynağında **katkıda** bulunan rolü verin.
    
    d. Azure portal yeni kullanıcıyla oturum açın ve Databricks çalışma alanını bulun.
    
    e. Databricks çalışma alanını bu kullanıcı olarak başlatın.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Sorun: {email} hesabınız Databricks 'te kayıtlı değil 

#### <a name="solution"></a>Çözüm

Çalışma alanını oluşturmadıysanız ve Kullanıcı olarak eklenmediyseniz, çalışma alanını oluşturan kişiye başvurun. Azure Databricks Yönetici Konsolu 'Nu kullanarak bu kişinin sizi eklemesini sağlayabilirsiniz. Yönergeler için bkz. [Kullanıcı ekleme ve yönetme](/azure/databricks/administration-guide/users-groups/users). Çalışma alanını oluşturduysanız ve yine de bu hatayı alırsanız, Azure portal **çalışma alanını yeniden Başlat** seçimini yapmayı deneyin.

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>Sorun: küme ayarlanırken bulut sağlayıcısı başlatma hatası (Publicıpcountlimitulaşıldı)

#### <a name="error-message"></a>Hata iletisi

"Bulut sağlayıcısı başlatma hatası: küme ayarlanırken bir bulut sağlayıcısı hatasıyla karşılaşıldı. Daha fazla bilgi için Databricks kılavuzuna bakın. Azure hata kodu: Publicıpcountlimitulaşıldı. Azure hata iletisi: Bu bölgede bu abonelik için 60 taneden fazla genel IP adresi oluşturulamıyor. "

#### <a name="solution"></a>Çözüm

Databricks kümeleri düğüm başına tek bir genel IP adresi kullanır. Aboneliğiniz zaten tüm genel IP 'lerini kullanıyorsa, [kotayı artırmayı istemeniz](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)gerekir. **Sorun türü**olarak **Kota** ' i ve **Ağ: ARM** ' i **Kota türü**olarak seçin. **Ayrıntılar**' da genel IP adresi kotası artışı isteyin. Örneğin, sınırlarınız Şu anda 60 ise ve 100 düğümlü bir küme oluşturmak istiyorsanız, 160 için bir sınır artışı isteyin.

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>Sorun: küme ayarlanırken ikinci bir bulut sağlayıcısı türü başlatma hatası oluştu (MissingSubscriptionRegistration)

#### <a name="error-message"></a>Hata iletisi

"Bulut sağlayıcısı başlatma hatası: küme ayarlanırken bir bulut sağlayıcısı hatasıyla karşılaşıldı. Daha fazla bilgi için Databricks kılavuzuna bakın.
Azure hata kodu: MissingSubscriptionRegistration Azure hata iletisi: abonelik ' Microsoft. COMPUTE ' ad alanını kullanmak için kayıtlı değil. Aboneliklerin nasıl kaydedileceği için bkz. https://aka.ms/rps-not-found. "

#### <a name="solution"></a>Çözüm

1. [Azure Portal](https://portal.azure.com) gidin.
1. **Abonelikler**, kullandığınız abonelik ve ardından **kaynak sağlayıcıları**' nı seçin. 
1. Kaynak sağlayıcıları listesinde, **Microsoft. COMPUTE**' de, **Kaydet**' i seçin. Kaynak sağlayıcısını kaydetmek için abonelikte katkıda bulunan veya sahip rolü olmalıdır.

Daha ayrıntılı yönergeler için bkz. [kaynak sağlayıcıları ve türleri](../azure-resource-manager/resource-manager-supported-services.md).

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>Sorun: Azure Databricks kuruluşunuzdaki kaynaklara erişmek için yalnızca bir yöneticinin izin vermesi gerekir.

#### <a name="background"></a>Arka plan

Azure Databricks Azure Active Directory tümleşiktir. Azure AD 'den kullanıcıları belirterek Azure Databricks (örneğin, Not defterleri veya kümeler) içinde izinler ayarlayabilirsiniz. Azure Databricks, Azure AD 'nizden kullanıcıların adlarını listeyapabilmesi için bu bilgilere ve verilme izin için okuma izni gerektirir. Onay zaten mevcut değilse, hatayı görürsünüz.

#### <a name="solution"></a>Çözüm

Azure portal genel yönetici olarak oturum açın. Azure Active Directory için **Kullanıcı ayarları** sekmesine gidin ve **kullanıcıların kendi adına şirket verilerine erişen uygulamalara izin verebilmeleri** için **Evet**olarak ayarlanmış olduğundan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı başlangıç: Azure Databricks kullanmaya başlayın](quickstart-create-databricks-workspace-portal.md)
- [Azure Databricks nedir?](what-is-azure-databricks.md)


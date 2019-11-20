---
title: Azure kaynakları için yönetilen kimlikler
description: Azure kaynakları için yönetilen kimliklere genel bakış.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5a9f83a8f6e8d5e83f30d30da2b1c4c0f722dc3
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063048"
---
# <a name="what-is-managed-identities-for-azure-resources"></a>Azure kaynakları için yönetilen kimlikler nedir?

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Bulut uygulamaları oluştururken yaygın olarak karşılaşılan bir zorluk, bulut hizmetlerinde kimlik doğrulaması yapmak için kodunuzda bulunan kimlik bilgilerinin yönetimidir. Kimlik bilgilerinin güvenlik altında tutulması önemli bir görevdir. İdeal olan kimlik bilgilerinin geliştirici iş istasyonlarında asla gösterilmemesi ve kaynak denetimine kaydedilmemesidir. Azure Key Vault kimlik bilgilerini, gizli dizileri ve diğer anahtarları güvenle depolamak için bir yol sağlar, ama bunları alabilmek için kodunuzun Key Vault'ta kimlik doğrulaması yapması gerekir. 

Azure Active Directory (Azure AD) hizmetindeki Azure kaynakları yönetilen hizmetleri bu sorunu çözer. Bu özellik, Azure hizmetlerine Azure AD üzerinde otomatik olarak yönetilen bir kimlik sağlar. Bu kimliği kullanarak, Key Vault da dahil olmak üzere Azure AD kimlik doğrulamasını destekleyen tüm hizmetlerde kodunuzda kimlik bilgileri olmadan kimlik doğrulaması yapabilirsiniz.

Azure kaynakları için yönetilen kimlikler özelliği, Azure abonelikleri için Azure AD ile ücretsiz olarak sunulmaktadır. Ek ücret alınmaz.

> [!NOTE]
> Azure kaynakları için yönetilen kimlikler, Yönetilen Hizmet Kimliği (MSI) olarak bilinen hizmetin yeni adıdır.

## <a name="terminology"></a>Terminoloji

Aşağıdaki terimler Azure kaynakları için Yönetilen kimlikler belge kümesi boyunca kullanılır:

- **ISTEMCI kimliği** -Azure AD tarafından oluşturulan ve ilk sağlama sırasında bir uygulamaya ve hizmet sorumlusuna bağlı olan benzersiz bir tanımlayıcı.
- **Asıl kimlik** -Azure kaynağına rol tabanlı erişim sağlamak için kullanılan yönetilen Kimliğiniz için hizmet sorumlusu NESNESININ nesne kimliği.
- **Azure Instance Metadata Service (IMDS)** -Azure Resource Manager kullanılarak oluşturulan tüm IaaS VM 'lerine ERIŞILEBILEN bir REST uç noktası. Uç nokta, yalnızca VM içinden erişilebilen, iyi bilinen yönlendirilebilir olmayan IP adresinde (169.254.169.254) kullanılabilir.

## Azure kaynakları için Yönetilen kimlikler nasıl çalışır?<a name="how-does-it-work"></a>

İki tür yönetilen kimlik vardır:

- **Sistem tarafından atanan yönetilen kimlik** doğrudan bir Azure hizmet örneğinde etkinleştirilir. Kimlik etkinleştirildiğinde Azure, örneğin aboneliğinin güvendiği Azure AD kiracısında örnek için bir kimlik oluşturur. Kimlik oluşturulduktan sonra, kimlik bilgileri örneğe sağlanır. Sistem tarafından atanan kimliğin yaşam döngüsü, içinde etkinleştirildiği Azure hizmet örneğine doğrudan bağlıdır. Örnek silinirse, Azure AD'deki kimlik bilgileri ve kimlik Azure tarafından otomatik olarak temizlenir.
- **Kullanıcı tarafından atanan yönetilen kimlik**, tek başına bir Azure kaynağı olarak oluşturulur. Bir oluşturma işlemi çerçevesinde, Azure kullanılan abonelik tarafından güvenilen Azure AD kiracısında bir kimlik oluşturur. Kimlik oluşturulduktan sonra, bir veya birden çok Azure hizmet örneğine atanabilir. Kullanıcı tarafından atanan kimliğin yaşam döngüsü, bu kimliğin atandığı Azure hizmet örneklerinin yaşam döngüsünden ayrı olarak yönetilir.

Dahili olarak, Yönetilen kimlikler yalnızca Azure kaynaklarıyla kullanılmak üzere kilitlenen özel bir türün hizmet ilkeleridir. Yönetilen kimlik silindiğinde, buna karşılık gelen hizmet sorumlusu otomatik olarak kaldırılır. 

Kodunuzda Azure AD kimlik doğrulamasını destekleyen hizmetler için erişim belirteci istemek üzere yönetilen kimlik kullanılabilir. Hizmet örneği tarafından kullanılan kimlik bilgilerinin dağıtımıyla Azure ilgilenir. 

Aşağıdaki diyagramda yönetilen hizmet kimliklerinin Azure sanal makineleriyle (VM) nasıl çalıştığı gösterilmektedir:

![Yönetilen hizmet kimlikleri ve Azure VM’leri](media/overview/msi-vm-vmextension-imds-example.png)

|  Özellik    | Sistem tarafından atanan yönetilen kimlik | Kullanıcı tarafından atanan yönetilen kimlik |
|------|----------------------------------|--------------------------------|
| Oluşturulurken |  Bir Azure kaynağının parçası olarak oluşturulur (örneğin, bir Azure sanal makinesi veya Azure App Service) | Tek başına Azure kaynağı olarak oluşturulur |
| Yaşam Döngüsü | Yönetilen kimliğin ile oluşturulduğu Azure kaynağıyla paylaşılan yaşam döngüsü. <br/> Üst kaynak silindiğinde, yönetilen kimlik de silinir. | Bağımsız yaşam döngüsü. <br/> Açıkça silinmelidir. |
| Azure kaynakları arasında paylaşım | Paylaştırılamaz. <br/> Yalnızca tek bir Azure kaynağıyla ilişkilendirilebilir. | Paylaşılabilir <br/> Aynı kullanıcı tarafından atanan yönetilen kimlik birden fazla Azure kaynağıyla ilişkilendirilebilir. |
| Genel kullanım | Tek bir Azure kaynağı içinde yer alan iş yükleri <br/> Bağımsız kimliklere ihtiyacınız olan iş yükleri. <br/> Örneğin, tek bir sanal makinede çalışan bir uygulama | Birden çok kaynak üzerinde çalışan ve tek bir kimliği paylaşabilen iş yükleri. <br/> Sağlama akışının bir parçası olarak güvenli bir kaynağa ön kimlik doğrulaması gerektiren iş yükleri. <br/> Kaynakların sık geri dönüştürüleceği iş yükleri, ancak izinlerin tutarlı kalması gerekir. <br/> Örneğin, birden çok sanal makinenin aynı kaynağa erişmesi gereken bir iş yükü | 

### <a name="how-a-system-assigned-managed-identity-works-with-an-azure-vm"></a>Sistem tarafından atanmış yönetilen kimliğin Azure VM ile çalışma şekli

1. Azure Resource Manager sanal makinede sistem tarafından atanan yönetilen kimliği etkinleştirmek için bir istek alır.

2. Azure Resource Manager, Azure AD'de sanal makinenin kimliği için bir hizmet sorumlusu oluşturur. Hizmet sorumlusu, bu abonelik tarafından güvenilen Azure AD kiracısında oluşturulur.

3. Azure Resource Manager, Azure Instance Metadata Service Identity uç noktasını hizmet sorumlusu istemci KIMLIĞI ve sertifikasıyla güncelleştirerek VM 'deki kimliği yapılandırır.

4. VM bir kimliğe sahip olduktan sonra hizmet sorumlusunu kullanarak Azure kaynaklarına VM erişimi sağlayabilirsiniz. Azure Resource Manager'ı çağırmak için Azure AD'de rol tabanlı erişim denetimini (RBAC) kullanarak VM hizmet sorumlusuna uygun rolü atayabilirsiniz. Key Vault'u çağırmak için kodunuza Key Vault'ta belirli bir gizli diziye veya anahtara erişim verebilirsiniz.

5. VM üzerinde çalışan kodunuz Azure örnek meta veri hizmeti uç noktasından yalnızca VM 'nin içinden erişilebilen bir belirteç isteyebilir: `http://169.254.169.254/metadata/identity/oauth2/token`
    - Resource parametresi belirtecin gönderildiği hizmeti belirtir. Azure Resource Manager ile kimlik doğrulaması için `resource=https://management.azure.com/` kullanın.
    - API version parametresi IMDS sürümünü belirtir; api-version=2018-02-01 veya üstünü kullanın.

6. Azure AD'ye erişim belirteci isteyen bir çağrı yapılır (5. adımda belirtildiği gibi) ve bu çağrıda 3. adımda yapılandırılan istemci kimliği ve sertifikası kullanılır. Azure AD bir JSON Web Token (JWT) erişim belirteci döndürür.

7. Kodunuz erişim belirtecini bir çağrıda Azure AD kimlik doğrulamasını destekleyen hizmete gönderir.

### <a name="how-a-user-assigned-managed-identity-works-with-an-azure-vm"></a>Kullanıcı tarafından atanmış yönetilen kimliğin Azure VM ile çalışma şekli

1. Azure Resource Manager kullanıcı tarafından atanan bir yönetilen kimlik oluşturma isteği alır.

2. Azure Resource Manager, Azure AD'de kullanıcı tarafından atanan yönetilen kimlik için bir hizmet sorumlusu oluşturur. Hizmet sorumlusu, bu abonelik tarafından güvenilen Azure AD kiracısında oluşturulur.

3. Azure Resource Manager, Kullanıcı tarafından atanan yönetilen kimliği bir VM üzerinde yapılandırma ve Kullanıcı tarafından atanan yönetilen kimlik hizmet sorumlusu istemci KIMLIĞI ve sertifikasıyla Azure Instance Metadata Service Identity uç noktasını güncelleştirme isteği alır.

4. Kullanıcı tarafından atanan yönetilen kimlik oluşturulduktan sonra hizmet sorumlusu bilgilerini kullanarak Azure kaynaklarına erişim verir. Azure Resource Manager'ı çağırmak için Azure AD'de RBAC özelliğini kullanarak kullanıcı tarafından atanan kimliğin hizmet sorumlusuna uygun rolü atayabilirsiniz. Key Vault'u çağırmak için kodunuza Key Vault'ta belirli bir gizli diziye veya anahtara erişim verebilirsiniz.

   > [!Note]
   > Bu adımı 3. adımdan önce de gerçekleştirebilirsiniz.

5. VM üzerinde çalışan kodunuz Azure Instance Metadata Service Identity uç noktasından yalnızca VM içinden erişilebilen bir belirteç isteyebilir: `http://169.254.169.254/metadata/identity/oauth2/token`
    - Resource parametresi belirtecin gönderildiği hizmeti belirtir. Azure Resource Manager ile kimlik doğrulaması için `resource=https://management.azure.com/` kullanın.
    - Client ID parametresi belirtecin hangi kimlik için istendiğini belirtir. Tek bir sanal makinede birden çok kullanıcı tarafından atanan kimlik olduğunda, belirsizliği ortadan kaldırmak için bu değer gereklidir.
    - API version parametresi, Azure Instance Metadata Service sürümünü belirtir. `api-version=2018-02-01` veya daha yüksek bir değer kullanın.

6. Azure AD'ye erişim belirteci isteyen bir çağrı yapılır (5. adımda belirtildiği gibi) ve bu çağrıda 3. adımda yapılandırılan istemci kimliği ve sertifikası kullanılır. Azure AD bir JSON Web Token (JWT) erişim belirteci döndürür.
7. Kodunuz erişim belirtecini bir çağrıda Azure AD kimlik doğrulamasını destekleyen hizmete gönderir.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Azure kaynakları için yönetilen kimlikleri nasıl kullanabilirim?

Yönetilen kimlikleri farklı Azure kaynaklarına erişme amacıyla kullanmayı öğrenmek için aşağıdaki öğreticileri deneyin.

> [!NOTE]
> Desteklenen birkaç senaryonun ayrıntılı video talimatları da dahil olmak üzere, Yönetilen kimlikler hakkında daha fazla bilgi için [Microsoft Azure kaynakları Için Yönetilen kimlikler uygulama](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing) Kursu ' na göz atın.

Yönetilen kimlikleri Windows VM ile kullanmayı öğrenin:

* [Azure Data Lake Store’a erişme](tutorial-windows-vm-access-datalake.md)
* [Azure Resource Manager’a erişme](tutorial-windows-vm-access-arm.md)
* [Azure SQL’e erişme](tutorial-windows-vm-access-sql.md)
* [Erişim anahtarı kullanarak Azure Depolama’ya erişme](tutorial-windows-vm-access-storage.md)
* [Paylaşılan erişim anahtarlarını kullanarak Azure Depolama’ya erişme](tutorial-windows-vm-access-storage-sas.md)
* [Azure Key Vault ile Azure AD dışı bir kaynağa erişme](tutorial-windows-vm-access-nonaad.md)

Yönetilen kimlikleri Linux VM ile kullanmayı öğrenin:

* [Erişim Azure Container Registry](../../container-registry/container-registry-authentication-managed-identity.md)
* [Azure Data Lake Store’a erişme](tutorial-linux-vm-access-datalake.md)
* [Azure Resource Manager’a erişme](tutorial-linux-vm-access-arm.md)
* [Erişim anahtarı kullanarak Azure Depolama’ya erişme](tutorial-linux-vm-access-storage.md)
* [Paylaşılan erişim anahtarlarını kullanarak Azure Depolama’ya erişme](tutorial-linux-vm-access-storage-sas.md)
* [Azure Key Vault ile Azure AD dışı bir kaynağa erişme](tutorial-linux-vm-access-nonaad.md)

Yönetilen kimlikleri diğer Azure hizmetleri ile kullanmayı öğrenin:

* [Azure App Service](/azure/app-service/overview-managed-identity)
* [Azure API Management](../../api-management/api-management-howto-use-managed-service-identity.md)
* [Azure Container Instances](../../container-instances/container-instances-managed-identity.md)
* [Azure Container Registry görevler](../../container-registry/container-registry-tasks-authentication-managed-identity.md)
* [Azure Event Hubs](../../event-hubs/event-hubs-managed-service-identity.md)
* [Azure İşlevleri](/azure/app-service/overview-managed-identity)
* [Azure Kubernetes Service](/azure/aks/use-managed-identity)
* [Azure Logic Apps](/azure/logic-apps/create-managed-service-identity)
* [Azure Service Bus](../../service-bus-messaging/service-bus-managed-service-identity.md)


## Bu özelliği hangi Azure hizmetleri destekliyor?<a name="which-azure-services-support-managed-identity"></a>

Azure kaynakları için yönetilen kimlikler, Azure AD kimlik doğrulamasını destekleyen hizmetlerde kimlik doğrulaması yapmak için kullanılabilir. Azure kaynakları için yönetilen kimlikler özelliğini destekleyen Azure hizmetlerinin listesi için bkz. [Azure kaynakları için yönetilen kimlikleri destekleyen hizmetler](services-support-msi.md).

## <a name="next-steps"></a>Sonraki adımlar

Azure kaynakları için yönetilen kimlikler özelliğini kullanmaya başlamak için aşağıdaki hızlı başlangıçlardan yararlanın:

* [Resource Manager’a erişmek için Windows VM sistem tarafından atanan yönetilen kimliği kullanma](tutorial-windows-vm-access-arm.md)
* [Resource Manager’a erişmek için Linux VM sistem tarafından atanan yönetilen kimliği kullanma](tutorial-linux-vm-access-arm.md)

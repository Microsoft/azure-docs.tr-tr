---
title: Sağlama yapılandırmasını dışarı aktarın ve olağanüstü durum kurtarma için bilinen iyi duruma geri alın
description: Sağlama yapılandırmanızı dışarı aktarmayı ve olağanüstü durum kurtarma için bilinen iyi duruma geri almayı öğrenin.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 03/19/2020
ms.author: kenwith
ms.openlocfilehash: c6af42c78bda66c4b397cbb99b26af7d6a5c7f07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99256382"
---
# <a name="how-to-export-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>Nasıl yapılır: sağlama yapılandırmasını dışarı aktarma ve bilinen iyi bir duruma geri alma

Bu makalede şunları yapmayı öğreneceksiniz:

- Azure portal sağlama yapılandırmanızı dışarı ve içeri aktarma
- Microsoft Graph API 'sini kullanarak sağlama yapılandırmanızı dışarı ve içeri aktarma

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>Azure portal sağlama yapılandırmanızı dışarı ve içeri aktarma

### <a name="export-your-provisioning-configuration"></a>Sağlama yapılandırmanızı dışarı aktarma

Yapılandırmanızı dışarı aktarmak için:

1. [Azure Portal](https://portal.azure.com/)sol gezinti panelinde **Azure Active Directory**' i seçin.
1. **Azure Active Directory** bölmesinde **Kurumsal uygulamalar** ' ı seçin ve uygulamanızı seçin.
1. Sol gezinti bölmesinde **sağlama**' yı seçin. Sağlama yapılandırma sayfasında, **öznitelik eşlemeleri**' ne tıklayın, ardından **Gelişmiş seçenekleri gösterin** ve son olarak **şemanızı gözden geçirin**. Bu işlem sizi şema düzenleyicisine götürür.
1. Şemayı indirmek için sayfanın en üstündeki komut çubuğunda İndir ' e tıklayın.

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>Olağanüstü durum kurtarma-bilinen iyi bir duruma geri alma

Yapılandırmanızı dışa aktarma ve kaydetme, yapılandırmanızın önceki bir sürümüne geri dönme olanağı sağlar. Sağlama yapılandırmanızı dışa aktarıp daha sonra kullanmak üzere öznitelik eşlemelerinizde veya kapsam filtrelerinizde değişiklik yaptığınızda kaydetmeniz önerilir. Tüm yapmanız gereken, yukarıdaki adımlarda indirdiğiniz JSON dosyasını açın, JSON dosyasının tüm içeriğini kopyalayın, şema düzenleyicisinde JSON yükünün tüm içeriğini değiştirin ve kaydedin. Etkin bir sağlama çevrimi varsa, işlem tamamlanır ve sonraki döngüde güncelleştirilmiş şema kullanılır. Sonraki döngüde bir başlangıç döngüsünün olması gerekir. Bu, yeni yapılandırmaya göre her kullanıcı ve grubu yeniden değerlendirmelidir. Önceki bir yapılandırmaya geri döndüğünüzde aşağıdakileri göz önünde bulundurun:

- Kullanıcılar, kapsam içinde olup olmadıklarını anlamak için yeniden değerlendirilir. Kapsam filtreleri değiştirildiyse, bir Kullanıcı kapsam içinde değilse, devre dışı bırakılacak. Çoğu durumda bu istenen davranış olsa da, bunu engellemek isteyebileceğiniz ve [kapsam silme işlevinin atlanmasını](./skip-out-of-scope-deletions.md) kullanabileceğiniz zamanlar vardır. 
- Sağlama yapılandırmanızın değiştirilmesi hizmeti yeniden başlatır ve bir [Başlangıç döngüsünü](./how-provisioning-works.md#provisioning-cycles-initial-and-incremental)tetikler.

## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>Microsoft Graph API 'sini kullanarak sağlama yapılandırmanızı dışarı ve içeri aktarma

Kullanıcı hazırlama öznitelik eşlemelerinizi ve şemanızı bir JSON dosyasına aktarmak ve yeniden Azure AD 'ye aktarmak için Microsoft Graph API ve Microsoft Graph Gezginini kullanabilirsiniz. Sağlama yapılandırmanızın bir yedeğini oluşturmak için burada yakalanan adımları da kullanabilirsiniz.

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>1. Adım: sağlama App Service asıl KIMLIĞINI alma (nesne KIMLIĞI)

1. [Azure Portal](https://portal.azure.com)başlatın ve sağlama uygulamanızın Özellikler bölümüne gidin. Örneğin, *Workday 'NIZI ad Kullanıcı sağlama uygulama* eşlemesine aktarmak istiyorsanız, uygulamanın Özellikler bölümüne gidin.
1. Sağlama uygulamanızın Özellikler bölümünde, *nesne kimliği* ALANıYLA ilişkili GUID değerini kopyalayın. Bu değer, uygulamanızın **Serviceprincipalıd** olarak da adlandırılır ve Microsoft Graph Explorer işlemlerinde kullanılacaktır.

   ![App Service ana iş günü KIMLIĞI](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>2. Adım: Microsoft Graph Explorer 'da oturum açın

1. [Microsoft Graph Gezginini](https://developer.microsoft.com/graph/graph-explorer) Başlat
1. "Microsoft 'a oturum aç" düğmesine tıklayın ve Azure AD Genel yönetici veya uygulama Yöneticisi kimlik bilgilerini kullanarak oturum açın.

    ![Microsoft Graph oturum açma](./media/export-import-provisioning-configuration/wd_export_02.png)

1. Başarılı oturum açma işlemi tamamlandıktan sonra, sol bölmedeki Kullanıcı hesabı ayrıntılarını görürsünüz.

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>3. Adım: sağlama uygulamasının sağlama Işi KIMLIĞINI alma

Microsoft Graph Gezgini ' nde, [Adım 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)' den ayıklanan **serviceprincipalıd** Ile [serviceprincipalıd] yerine aşağıdaki Get sorgusunu çalıştırın.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Aşağıda gösterildiği gibi bir yanıt alacaksınız. Yanıtta bulunan "ID özniteliği" öğesini kopyalayın. Bu değer **Provisioningjobıd** ' dir ve temel alınan şema meta verilerini almak için kullanılır.

   [![Sağlama Işi KIMLIĞI](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>4. Adım: sağlama şemasını Indirme

Microsoft Graph Gezgini 'nde, aşağıdaki GET sorgusunu çalıştırarak [Serviceprincipalıd] ve [Provisioningjobıd] değerini, önceki adımlarda alınan Serviceprincipalıd ve Provisioningjobıd ile değiştirin.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

JSON nesnesini yanıttan kopyalayın ve şemanın bir yedeğini oluşturmak için dosyayı bir dosyaya kaydedin.

### <a name="step-5-import-the-provisioning-schema"></a>5. Adım: sağlama şemasını Içeri aktarma

> [!CAUTION]
> Bu adımı yalnızca, Azure portal kullanarak değiştirilemeyen yapılandırma şemasını değiştirmeniz gerekiyorsa veya daha önce yedeklenen bir dosyanın geçerli ve çalışma şemasına sahip olan yapılandırmayı geri yüklemeniz gerekiyorsa gerçekleştirin.

Microsoft Graph Gezgini 'nde, aşağıdaki PUT sorgusunu, [Serviceprincipalıd] ve [Provisioningjobıd] değerini, önceki adımlarda alınan Serviceprincipalıd ve Provisioningjobıd ile değiştirerek yapılandırın.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

"Istek gövdesi" sekmesinde, JSON Şema dosyasının içeriğini kopyalayın.

   [![İstek Gövdesi](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

"Istek üstbilgileri" sekmesinde, Içerik türü üst bilgi özniteliğini "Application/JSON" değeriyle ekleyin

   [![İstek üst bilgileri](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

Yeni şemayı içeri aktarmak için **Sorguyu Çalıştır** ' ı seçin.
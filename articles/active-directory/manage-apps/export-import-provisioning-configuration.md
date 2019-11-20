---
title: Sağlama yapılandırmanızı dışarı aktarma veya Graph API ile içeri aktarma | Microsoft Docs
description: Graph API kullanarak sağlama yapılandırmasını dışarı ve içeri aktarmayı öğrenin.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c3e92ee5ffd97174331703b703e811bd1ce5f43
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70815880"
---
# <a name="export-or-import-your-provisioning-configuration-by-using-graph-api"></a>Sağlama yapılandırmanızı Graph API kullanarak dışarı aktarın veya içeri aktarın

Kullanıcı hazırlama öznitelik eşlemelerinizi ve şemanızı bir JSON dosyasına aktarmak ve yeniden Azure AD 'ye aktarmak için Microsoft Graph API ve Graf Gezginini kullanabilirsiniz. Sağlama yapılandırmanızın bir yedeğini oluşturmak için burada yakalanan adımları da kullanabilirsiniz. 

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>1\. adım: Sağlama App Service asıl KIMLIĞINI (nesne KIMLIĞI) alma

1. [Azure Portal](https://portal.azure.com)başlatın ve sağlama uygulamanızın Özellikler bölümüne gidin. Örneğin, *Workday 'NIZI ad Kullanıcı sağlama uygulama* eşlemesinde dışarı aktarmak istiyorsanız, uygulamanın Özellikler bölümüne gidin. 
1. Sağlama uygulamanızın Özellikler bölümünde, *nesne kimliği* ALANıYLA ilişkili GUID değerini kopyalayın. Bu değer, uygulamanızın **Serviceprincipalıd** olarak da adlandırılır ve Graph Explorer işlemlerinde kullanılacaktır.

   ![App Service ana iş günü KIMLIĞI](./media/export-import-provisioning-mappings/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>2\. adım: Microsoft Graph Explorer 'da oturum açın

1. [Microsoft Graph Gezginini](https://developer.microsoft.com/graph/graph-explorer) Başlat
1. "Microsoft 'a oturum aç" düğmesine tıklayın ve Azure AD Genel yönetici veya uygulama Yöneticisi kimlik bilgilerini kullanarak oturum açın.

    ![Graph oturum açma](./media/export-import-provisioning-mappings/wd_export_02.png)

1. Başarılı oturum açma işlemi tamamlandıktan sonra, sol bölmedeki Kullanıcı hesabı ayrıntılarını görürsünüz.

## <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>3\. adım: Sağlama uygulamasının sağlama Işi KIMLIĞINI alma

Microsoft Graph Gezgini ' nde, [Adım 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)' den ayıklanan **serviceprincipalıd** Ile [serviceprincipalıd] yerine aşağıdaki Get sorgusunu çalıştırın.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Aşağıda gösterildiği gibi bir yanıt alacaksınız. Yanıtta bulunan "ID özniteliği" öğesini kopyalayın. Bu değer **Provisioningjobıd** ' dir ve temel alınan şema meta verilerini almak için kullanılır.

   [![Sağlama Işi KIMLIĞI](./media/export-import-provisioning-mappings/wd_export_03.png)](./media/export-import-provisioning-mappings/wd_export_03.png#lightbox)

## <a name="step-4-download-the-provisioning-schema"></a>4\. Adım: Sağlama şemasını indirin

Microsoft Graph Gezgini 'nde, aşağıdaki GET sorgusunu çalıştırarak [Serviceprincipalıd] ve [Provisioningjobıd] değerini, önceki adımlarda alınan Serviceprincipalıd ve Provisioningjobıd ile değiştirin.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

JSON nesnesini yanıttan kopyalayın ve şemanın bir yedeğini oluşturmak için dosyayı bir dosyaya kaydedin.

## <a name="step-5-import-the-provisioning-schema"></a>5\. Adım: Sağlama şemasını içeri aktar

> [!CAUTION]
> Bu adımı yalnızca, Azure portal kullanarak değiştirilemeyen yapılandırma şemasını değiştirmeniz gerekiyorsa veya daha önce yedeklenen bir dosyanın geçerli ve çalışma şemasına sahip olan yapılandırmayı geri yüklemeniz gerekiyorsa gerçekleştirin.

Microsoft Graph Gezgini 'nde, aşağıdaki PUT sorgusunu, [Serviceprincipalıd] ve [Provisioningjobıd] değerini, önceki adımlarda alınan Serviceprincipalıd ve Provisioningjobıd ile değiştirerek yapılandırın.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

"Istek gövdesi" sekmesinde, JSON Şema dosyasının içeriğini kopyalayın.

   [![İstek gövdesi](./media/export-import-provisioning-mappings/wd_export_04.png)](./media/export-import-provisioning-mappings/wd_export_04.png#lightbox)

"Istek üstbilgileri" sekmesinde, Içerik türü üst bilgi özniteliğini "Application/JSON" değeriyle ekleyin

   [![İstek üst bilgileri](./media/export-import-provisioning-mappings/wd_export_05.png)](./media/export-import-provisioning-mappings/wd_export_05.png#lightbox)

Yeni şemayı içeri aktarmak için "Sorguyu Çalıştır" düğmesine tıklayın.

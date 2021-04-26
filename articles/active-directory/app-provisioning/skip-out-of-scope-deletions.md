---
title: Kapsam dışı kullanıcıların silinmesini atlayın
description: Kapsam kullanıcılarının ön sağlamasını kaldırma işlemi için varsayılan davranışı nasıl geçersiz kılacağınızı öğrenin.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 12/10/2019
ms.author: kenwith
ms.reviewer: celested
ms.openlocfilehash: a6cbabe35b223020528d1cf48aa9e0ef9b9f7c05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99256128"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>Kapsam dışına çıkan Kullanıcı hesaplarını silmeyi atlayın

Varsayılan olarak, Azure AD sağlama altyapısı, kapsam dışına çıkan kullanıcıları geçici olarak siler veya devre dışı bırakır. Ancak, Workday gibi AD kullanıcısı gelen sağlama ile ilgili bazı senaryolar için bu davranış beklenmeyebilir ve bu varsayılan davranışı geçersiz kılmak isteyebilirsiniz.  

Bu makalede, kapsam dışına çıkan hesapların işlenmesini denetleyen ***Skipoutofscopesilmelerini*** bayrağını ayarlamak IÇIN Microsoft Graph apı ve Microsoft Graph API Explorer 'ın nasıl kullanılacağı açıklanır. 
* ***Skipoutofscopesilmeleri*** 0 (false) olarak ayarlandıysa, kapsam dışına çıkan hesaplar hedefte devre dışı bırakılır.
* ***Skipoutofscopesilmeleri** _, 1 (true) olarak ayarlandıysa, kapsam dışına çıkan hesaplar hedefte devre dışı olmayacaktır. Bu bayrak _Provisioning App * düzeyinde ayarlanır ve Graph API kullanılarak yapılandırılabilir. 

Bu yapılandırma *Kullanıcı sağlama uygulamasını Active Directory Için Workday* ile yaygın olarak kullanıldığından, Workday uygulamasının ekran görüntülerini içerir. Ancak, yapılandırma ServiceNow, Salesforce ve Dropbox gibi *diğer uygulamalarla* de kullanılabilir.

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>1. Adım: sağlama App Service asıl KIMLIĞINI alma (nesne KIMLIĞI)

1. [Azure Portal](https://portal.azure.com)başlatın ve sağlama uygulamanızın Özellikler bölümüne gidin. Örneğin, *Workday 'NIZI ad Kullanıcı sağlama uygulama* eşlemesinde dışarı aktarmak istiyorsanız, uygulamanın Özellikler bölümüne gidin. 
1. Sağlama uygulamanızın Özellikler bölümünde, *nesne kimliği* ALANıYLA ilişkili GUID değerini kopyalayın. Bu değer, uygulamanızın **Serviceprincipalıd** olarak da adlandırılır ve Graph Explorer işlemlerinde kullanılacaktır.

   ![App Service ana iş günü KIMLIĞI](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>2. Adım: Microsoft Graph Explorer 'da oturum açın

1. [Microsoft Graph Gezginini](https://developer.microsoft.com/graph/graph-explorer) Başlat
1. "Microsoft 'a oturum aç" düğmesine tıklayın ve Azure AD Genel yönetici veya uygulama Yöneticisi kimlik bilgilerini kullanarak oturum açın.

    ![Graph oturum açma](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. Başarılı oturum açma işlemi tamamlandıktan sonra, sol bölmedeki Kullanıcı hesabı ayrıntılarını görürsünüz.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>3. Adım: mevcut uygulama kimlik bilgilerini ve bağlantı ayrıntılarını alın

Microsoft Graph Gezgini ' nde, [Adım 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)' den ayıklanan **serviceprincipalıd** Ile [serviceprincipalıd] yerine aşağıdaki Get sorgusunu çalıştırın.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![İş sorgusunu al](./media/skip-out-of-scope-deletions/skip-03.png)

Yanıtı bir metin dosyasına kopyalayın. Bu, dağıtımınız için özel sarı renkle vurgulanmış şekilde aşağıda gösterildiği gibi JSON metni gibi görünür. Yeşil renkle vurgulanmış satırları sonuna ekleyin ve Blue 'da vurgulanmış olan Workday bağlantı parolasını güncelleştirin. 

   ![İş yanıtı al](./media/skip-out-of-scope-deletions/skip-04.png)

Eşlemeye eklenecek JSON bloğu aşağıda verilmiştir. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>4. Adım: Skipoutofscopesilmeleri bayrağıyla gizli dizileri bitiş noktasını güncelleştirme

Grafik Gezgini 'nde, gizli dizi uç noktasını ***Skipoutofscopesilmeleri*** bayrağıyla güncelleştirmek için aşağıdaki komutu çalıştırın. 

Aşağıdaki URL 'de, [Serviceprincipalıd] öğesini [Adım 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)' den ayıklanan **serviceprincipalıd** ile değiştirin. 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
Adım 3 ' teki güncelleştirilmiş metni "istek gövdesine" kopyalayın ve "Istek üstbilgileri" içinde "Content-Type" başlığını "Application/JSON" olarak ayarlayın. 

   ![İsteği koy](./media/skip-out-of-scope-deletions/skip-05.png)

"Sorguyu Çalıştır" seçeneğine tıklayın. 

Çıktıyı "başarılı – durum kodu 204" olarak almalısınız. 

   ![Yanıtı koy](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>5. Adım: kapsam dışı kullanıcıların devre dışı bırakıldığını doğrulama

Kapsam kurallarınızı belirli bir kullanıcıyı atlayacak şekilde güncelleştirerek, bu bayrak sonuçlarını beklenen davranışa göre test edebilirsiniz. Aşağıdaki örnekte, KIMLIĞI 21173 olan çalışanı (kapsam içinde daha önce olan) yeni bir kapsam kuralı ekleyerek dışlıyoruz: 

   ![Örnek bir Kullanıcı vurgulanmış şekilde "kapsam filtresi ekle" bölümünü gösteren ekran görüntüsü.](./media/skip-out-of-scope-deletions/skip-07.png)

Bir sonraki sağlama döngüsünün Azure AD sağlama hizmeti, 21173 kullanıcısının kapsam dışına çıkış olduğunu ve Skipoutofscopesilmeleri özelliğinin etkin olduğunu belirler, bu durumda Kullanıcı için eşitleme kuralı aşağıda gösterildiği gibi bir ileti görüntüler: 

   ![Kapsam oluşturma örneği](./media/skip-out-of-scope-deletions/skip-08.png)



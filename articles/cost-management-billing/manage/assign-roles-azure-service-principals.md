---
title: Azure Kurumsal Anlaşma hizmet sorumlusu adlarına roller atama
description: Bu makale, PowerShell ve REST API 'Leri kullanarak hizmet sorumlusu adlarına roller atamanıza yardımcı olur.
author: bandersmsft
ms.reviewer: ruturajd
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 04/05/2021
ms.author: banders
ms.openlocfilehash: d348eeb5cc789665d7e7004523b9feba0ea6e413
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490565"
---
# <a name="assign-roles-to-azure-enterprise-agreement-service-principal-names"></a>Azure Kurumsal Anlaşma hizmet sorumlusu adlarına roller atama

[Azure Enterprise Portal](https://ea.azure.com/)'da kurumsal anlaşma (EA) kaydını yönetebilirsiniz. Kuruluşunuzu yönetmek, maliyetleri görüntülemek ve abonelikler oluşturmak için farklı roller oluşturabilirsiniz. Bu makale, Azure hizmet sorumlusu adlarıyla (SPN) Azure PowerShell ve REST API 'Leri kullanarak bu görevlerden bazılarını otomatikleştirmenize yardımcı olur.

Başlamadan önce, aşağıdaki makalelere alışkın olduğunuzdan emin olun:

- [Kurumsal Anlaşma rolleri](understand-ea-roles.md)
- [Azure PowerShell ile oturum açma](/powershell/azure/authenticate-azureps)
- [Postman ile REST API 'Leri çağırma](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

## <a name="create-and-authenticate-your-service-principal"></a>Hizmet sorumlunuzu oluşturma ve kimlik doğrulama

Bir SPN kullanarak EA eylemlerini otomatikleştirmek için bir Azure Active Directory (Azure AD) uygulaması oluşturmanız gerekir. Otomatik bir şekilde kimlik doğrulaması yapabilir. Hizmet sorumlunuzu oluşturmak ve kimliğini doğrulamak için aşağıdaki makaleleri okuyun ve bu adımları izleyin.

1. [Hizmet sorumlusu oluşturma](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. [Oturum açmak için kiracı ve uygulama KIMLIĞI değerlerini al](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)

Uygulama kaydını gösteren örnek bir ekran görüntüsü aşağıda verilmiştir.

:::image type="content" source="./media/assign-roles-azure-service-principals/register-an-application.png" alt-text="Uygulamanın kaydını gösteren ekran görüntüsü." lightbox="./media/assign-roles-azure-service-principals/register-an-application.png" :::

### <a name="find-your-spn-and-tenant-id"></a>SPN ve kiracı KIMLIĞINIZI bulun

Ayrıca, SPN 'nin nesne KIMLIĞINE ve uygulamanın kiracı KIMLIĞINE de ihtiyacınız vardır. Sonraki bölümlerde izin atama işlemleri için bilgilere ihtiyacınız vardır.

Azure AD uygulamasının kiracı KIMLIĞINI uygulamanın genel bakış sayfasında bulabilirsiniz. Azure portal bulmak için Azure Active Directory ' a gidin ve **Kurumsal uygulamalar**' ı seçin. Uygulamayı arayın.

:::image type="content" source="./media/assign-roles-azure-service-principals/enterprise-application.png" alt-text="Örnek bir kurumsal uygulamayı gösteren ekran görüntüsü." lightbox="./media/assign-roles-azure-service-principals/enterprise-application.png" :::

Uygulamayı seçin. Uygulama KIMLIĞI ve nesne KIMLIĞINI gösteren bir örnek aşağıda verilmiştir.

:::image type="content" source="./media/assign-roles-azure-service-principals/application-id-object-id.png" alt-text="Bir kurumsal uygulama için uygulama KIMLIĞI ve nesne KIMLIĞI gösteren ekran görüntüsü." lightbox="./media/assign-roles-azure-service-principals/application-id-object-id.png" :::

Kiracı KIMLIĞINI Microsoft Azure AD Genel Bakış sayfasında bulabilirsiniz.

:::image type="content" source="./media/assign-roles-azure-service-principals/tenant-id.png" alt-text="Kiracı KIMLIĞINIZIN nerede görüntüleneceği gösteren ekran görüntüsü." lightbox="./media/assign-roles-azure-service-principals/tenant-id.png" :::

Asıl kiracı KIMLIĞINIZ ayrıca çeşitli konumlarda sorumlu KIMLIĞI, SPN ve nesne KIMLIĞI olarak da adlandırılır. Azure AD kiracı KIMLIĞINIZIN değeri aşağıdaki biçimde bir GUID gibi görünür: `11111111-1111-1111-1111-111111111111` .

## <a name="permissions-that-can-be-assigned-to-the-spn"></a>SPN 'ye atanabileceği izinler

Sonraki adımlar için, Azure AD uygulamasına bir EA rolü kullanarak eylemler yapması için izin verirsiniz. SPN 'ye yalnızca aşağıdaki rolleri atayabilirsiniz. Rol tanımı KIMLIĞI, tam olarak gösterildiği gibi, daha sonra atama adımlarında kullanılır.

| Rol | İzin verilen eylemler | Rol tanımı KIMLIĞI |
| --- | --- | --- |
| KayıtBir okuyucu | , Tüm hesaplar ve aboneliklerde kullanım ve ücretleri görüntüleyebilir. Kayıt ile ilişkili Azure ön ödemeli (daha önce parasal taahhüt olarak adlandırılır) bakiyesini görüntüleyebilir. | 24f8edb6-1668-4659-b5e2-40bb5f3a7d7e |
| EA Satınalmacı | Rezervasyon siparişleri satın alın ve rezervasyon işlemlerini görüntüleyin. , Tüm hesaplar ve aboneliklerde kullanım ve ücretleri görüntüleyebilir. Kayıt ile ilişkili Azure ön ödemeli (daha önce parasal taahhüt olarak adlandırılır) bakiyesini görüntüleyebilir. | da6647fb-7651-49ee-be91-c43c4877f0c4  |
| DepartmentReader | Yönettikleri departmanın kullanım ayrıntılarını indirin. , Departmanlarıyla ilişkili kullanımı ve ücretleri görüntüleyebilir. | db609904-a47f-4794-9be8-9bd86fbffd8a |
| SubscriptionCreator | Verilen hesap kapsamında yeni abonelikler oluşturun. | a0bcee42-bf30-4d1b-926a-48d21664ef71 |

- Kayıt okuyucuyu yalnızca kayıt yazıcı rolüne sahip bir kullanıcı tarafından bir SPN 'ye atayabilirsiniz.
- Bir departman okuyucusu, yalnızca kayıt yazıcı rolü veya departman yazıcı rolü olan bir kullanıcı tarafından bir SPN 'ye atanabilir.
- Bir abonelik Oluşturucu rolü, yalnızca kayıt hesabının hesap sahibi olan bir kullanıcı tarafından bir SPN 'ye atanabilir. Rol EA portalında gösterilmez. Yalnızca programlı yollarla oluşturulur ve yalnızca programlı kullanım içindir.
- EA Satınalmacı rolü EA portalında gösterilmez. Yalnızca programlı yollarla oluşturulur ve yalnızca programlı kullanım içindir.

## <a name="assign-enrollment-account-role-permission-to-the-spn"></a>SPN 'ye kaydolma hesabı rolü iznini atama

[Rol atamaları-Put](/rest/api/billing/2019-10-01-preview/roleassignments/put) REST API makalesini okuyun.

Makaleyi okurken, SPN 'yi kullanmaya başlamak için **dene** ' yi seçin.

:::image type="content" source="./media/assign-roles-azure-service-principals/put-try-it.png" alt-text="Put makalesindeki BT 'yi deneyin seçeneğini gösteren ekran görüntüsü." lightbox="./media/assign-roles-azure-service-principals/put-try-it.png" :::

Erişim atamak istediğiniz kayda erişimi olan kiracıda hesabınızla oturum açın.

API isteğinin bir parçası olarak aşağıdaki parametreleri sağlayın.

**billingAccountName**

Parametresi faturalandırma hesabı KIMLIĞIDIR. Bunu, Azure portal maliyet yönetimi + Faturalandırma Genel Bakış sayfasında bulabilirsiniz.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Faturalandırma hesabı KIMLIĞINI gösteren ekran görüntüsü." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**Billingroleatamaadı**

Parametresi sağlamanız gereken benzersiz bir GUID 'dir. [New-GUID](/powershell/module/microsoft.powershell.utility/new-guid) PowerShell komutunu kullanarak bir GUID oluşturabilirsiniz.

Veya, [ÇEVRIMIÇI GUID/UUID Oluşturucu](https://guidgenerator.com/) Web sitesini kullanarak BENZERSIZ bir GUID oluşturabilirsiniz.

**api sürümü**

**2019-10-01-Preview** sürümünü kullanın.

İstek gövdesinde kullanmanız gereken JSON kodu var.

[Rol atamaları-put-örnekleri](/rest/api/billing/2019-10-01-preview/roleassignments/put#examples)' nde örnek istek gövdesini kullanın.

JSON 'un bir parçası olarak kullanmanız gereken üç parametre vardır.

| Parametre | Nerede bulunacak |
| --- | --- |
| Properties. PrincipalId | Bkz. [SPN ve KIRACı kimliğinizi bulma](#find-your-spn-and-tenant-id). |
| Properties. Principaltenantıd | Bkz. [SPN ve KIRACı kimliğinizi bulma](#find-your-spn-and-tenant-id). |
| Properties. Roledefinitionıd | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/24f8edb6-1668-4659-b5e2-40bb5f3a7d7e" |

Faturalandırma hesabı adı, API parametrelerinde kullandığınız parametredir. EA portalında gördüğünüz kayıt KIMLIĞI ve Azure portal.

Bir kayıt `24f8edb6-1668-4659-b5e2-40bb5f3a7d7e` okuyucusu için faturalandırma rolü tanım kimliği olduğuna dikkat edin.

Komutu başlatmak için **Çalıştır** ' ı seçin.

:::image type="content" source="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" alt-text="Örnek rol atamasını gösteren ekran görüntüsü bunu çalıştırmaya hazırlanma örnek bilgileri ile deneyin." lightbox="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" :::

`200 OK`Yanıt, SPN 'nin başarıyla eklendiğini gösterir.

Artık, EA API 'Lerine otomatik bir şekilde erişmek için SPN 'YI (nesne KIMLIĞI ile Azure AD Uygulaması) kullanabilirsiniz. SPN kayıtları KayıtAdı ' na sahiptir.

## <a name="assign-ea-purchaser-role-permission-to-the-spn"></a>SPN 'ye EA Satınalmacı rolü izni atama 

EA Satınalmacı rolü için, kayıt okuyucu için aynı adımları kullanın. `roleDefinitionId`Aşağıdaki örneği kullanarak öğesini belirtin.

`"/providers/Microsoft.Billing/billingAccounts/1111111/billingRoleDefinitions/ da6647fb-7651-49ee-be91-c43c4877f0c4"`

 

## <a name="assign-the-department-reader-role-to-the-spn"></a>Bölüm okuyucusu rolünü SPN 'ye atama

Başlamadan önce, [kayıt departmanı rolü atamaları-Put](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put) REST API makalesini okuyun.

Makaleyi okurken **dene**' yi seçin.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Kayıt departmanı rol atamalarında uygulamayı deneyin seçeneğini gösteren ekran görüntüsü makale koy." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

Erişim atamak istediğiniz kayda erişimi olan kiracıda hesabınızla oturum açın.

API isteğinin bir parçası olarak aşağıdaki parametreleri sağlayın.

**billingAccountName**

Bu, faturalandırma hesabı KIMLIĞIDIR. Bunu, Azure portal maliyet yönetimi + Faturalandırma Genel Bakış sayfasında bulabilirsiniz.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Faturalandırma hesabı KIMLIĞINI gösteren ekran görüntüsü." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**Billingroleatamaadı**

Parametresi sağlamanız gereken benzersiz bir GUID 'dir. [New-GUID](/powershell/module/microsoft.powershell.utility/new-guid) PowerShell komutunu kullanarak bir GUID oluşturabilirsiniz.

Veya, [ÇEVRIMIÇI GUID/UUID Oluşturucu](https://guidgenerator.com/) Web sitesini kullanarak BENZERSIZ bir GUID oluşturabilirsiniz.

**departmentName**

Bu departman KIMLIĞIDIR. Azure portal departman kimliklerini görebilirsiniz. Maliyet yönetimi + faturalandırma > **departmanları**' ne gidin.

Bu örnekte, ACE departmanı kullandık. Örneğin KIMLIĞI `84819` .

:::image type="content" source="./media/assign-roles-azure-service-principals/department-id.png" alt-text="Örnek departman KIMLIĞI gösteren ekran görüntüsü." lightbox="./media/assign-roles-azure-service-principals/department-id.png" :::

**api sürümü**

**2019-10-01-Preview** sürümünü kullanın.

İstek gövdesinde kullanmanız gereken JSON kodu var.

[Kayıt departmanı rol atamaları-put](/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put)' da örneği kullanın. JSON 'un bir parçası olarak kullanmanız gereken üç parametre vardır.

| Parametre | Nerede bulunacak |
| --- | --- |
| Properties. PrincipalId | Bkz. [SPN ve KIRACı kimliğinizi bulma](#find-your-spn-and-tenant-id). |
| Properties. Principaltenantıd | Bkz. [SPN ve KIRACı kimliğinizi bulma](#find-your-spn-and-tenant-id). |
| Properties. Roledefinitionıd | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/db609904-a47f-4794-9be8-9bd86fbffd8a" |

Faturalandırma hesabı adı, API parametrelerinde kullandığınız parametredir. EA portalında gördüğünüz kayıt KIMLIĞI ve Azure portal.

Faturalama rolü tanım KIMLIĞI `db609904-a47f-4794-9be8-9bd86fbffd8a` bir departman okuyucusu içindir.

Komutu başlatmak için **Çalıştır** ' ı seçin.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" alt-text="Örnek kayıt departmanı rol atamalarını gösteren ekran görüntüsü – REST koy, çalıştırmaya hazırlanma örnek bilgileri ile deneyin." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" :::

`200 OK`Yanıt, SPN 'nin başarıyla eklendiğini gösterir.

Artık, EA API 'Lerine otomatik bir şekilde erişmek için SPN 'YI (nesne KIMLIĞI ile Azure AD Uygulaması) kullanabilirsiniz. SPN 'nin DepartmentReader rolü vardır.

## <a name="assign-the-subscription-creator-role-to-the-spn"></a>Abonelik Oluşturucu rolünü SPN 'ye atama

[Kayıt hesabı rolü atamaları-put](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put) makalesini okuyun.

Okurken, SPN 'ye abonelik Oluşturucu rolünü atamak için **dene** ' yi seçin.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Kayıt hesabı rol atamalarındaki uygulamayı deneyin seçeneğini gösteren ekran görüntüsü makale koy." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

Erişim atamak istediğiniz kayda erişimi olan kiracıda hesabınızla oturum açın.

API isteğinin bir parçası olarak aşağıdaki parametreleri sağlayın. [Kayıt hesabı rol atamaları-put-URI parametreleri](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put#uri-parameters)' nde makaleyi okuyun.

**billingAccountName**

Parametresi faturalandırma hesabı KIMLIĞIDIR. Bunu, Azure portal maliyet yönetimi + Faturalandırma Genel Bakış sayfasında bulabilirsiniz.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Faturalandırma hesabı KIMLIĞINI gösteren ekran görüntüsü." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**Billingroleatamaadı**

Parametresi sağlamanız gereken benzersiz bir GUID 'dir. [New-GUID](/powershell/module/microsoft.powershell.utility/new-guid) PowerShell komutunu kullanarak bir GUID oluşturabilirsiniz.

Veya, [ÇEVRIMIÇI GUID/UUID Oluşturucu](https://guidgenerator.com/) Web sitesini kullanarak BENZERSIZ bir GUID oluşturabilirsiniz.
**enrollmentAccountName**

Parametresi, hesap KIMLIĞIDIR. Hesap adının hesap KIMLIĞINI, kayıt ve departman kapsamındaki maliyet yönetimi + Faturalandırma ' de Azure portal bulun.

Bu örnekte, GTM sınama hesabını kullandık. KIMLIĞI `196987` .

:::image type="content" source="./media/assign-roles-azure-service-principals/account-id.png" alt-text="Hesap KIMLIĞINI gösteren ekran görüntüsü." lightbox="./media/assign-roles-azure-service-principals/account-id.png" :::

**api sürümü**

**2019-10-01-Preview** sürümünü kullanın.

İstek gövdesinde kullanmanız gereken JSON kodu var.

[Kayıt departmanı rol atamaları-put-örnekleri bölümünde](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put#putenrollmentdepartmentadministratorroleassignment)örneği kullanın.

JSON 'un bir parçası olarak kullanmanız gereken üç parametre vardır.

| Parametre | Nerede bulunacak |
| --- | --- |
| Properties. PrincipalId | Bkz. [SPN ve KIRACı kimliğinizi bulma](#find-your-spn-and-tenant-id). |
| Properties. Principaltenantıd | Bkz. [SPN ve KIRACı kimliğinizi bulma](#find-your-spn-and-tenant-id). |
| Properties. Roledefinitionıd | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountID}/enrollmentAccounts/196987/billingRoleDefinitions/a0bcee42-bf30-4d1b-926a-48d21664ef71" |

Faturalandırma hesabı adı, API parametrelerinde kullandığınız parametredir. EA portalında gördüğünüz kayıt KIMLIĞI ve Azure portal.

Faturalandırma rolü tanım KIMLIĞI, `a0bcee42-bf30-4d1b-926a-48d21664ef71` abonelik Oluşturucu rolü içindir.

Komutu başlatmak için **Çalıştır** ' ı seçin.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" alt-text="Kayıt hesabı rolü atamaları-put makalesinde BT 'yi deneyin seçeneğini gösteren ekran görüntüsü" lightbox="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" :::

`200 OK`Yanıt, SPN 'nin başarıyla eklendiğini gösterir.

Artık, EA API 'Lerine otomatik bir şekilde erişmek için SPN 'YI (nesne KIMLIĞI ile Azure AD Uygulaması) kullanabilirsiniz. SPN 'nin SubscriptionCreator rolü vardır.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure EA Portal yönetimi](ea-portal-administration.md)hakkında daha fazla bilgi edinin.
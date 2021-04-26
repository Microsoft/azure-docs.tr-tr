---
title: Kapsam filtreleriyle uygulama sağlama | Microsoft Docs
description: Bir nesnenin iş gereksinimlerinizi karşılamadığı durumlarda otomatik Kullanıcı sağlamayı destekleyen uygulamalardaki nesneleri engellemek için kapsam filtrelerini nasıl kullanacağınızı öğrenin.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: kenwith
ms.openlocfilehash: 7fff456b7ad6e980fc3c9bda36cfcab02e2ed863
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99255840"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Kapsam filtreleri ile öznitelik tabanlı uygulama sağlama
Bu makalenin amacı, bir uygulamaya hangi kullanıcıların sağlandığını belirleyen öznitelik tabanlı kurallar tanımlamak için kapsam filtrelerinin nasıl kullanılacağını açıklamaktır.

## <a name="scoping-filter-use-cases"></a>Kapsam filtresi kullanım örnekleri

Kapsam filtresi, Azure Active Directory (Azure AD) sağlama hizmeti 'nin belirli bir değerle eşleşen bir özniteliğe sahip tüm kullanıcıları içermesini veya dışlanmasını sağlar. Örneğin, kullanıcıları Azure AD 'den bir satış ekibi tarafından kullanılan bir SaaS uygulamasına sağlarken, yalnızca "Sales" özniteliği olan kullanıcıların sağlama kapsamında olması gerektiğini belirtebilirsiniz.

Kapsam filtreleri, sağlama bağlayıcısının türüne göre farklı şekilde kullanılabilir:

* **Azure AD 'Den SaaS uygulamalarına giden sağlama**. Azure AD kaynak sistem olduğunda, [Kullanıcı ve Grup atamaları](../manage-apps/assign-user-or-group-access-portal.md) , hangi kullanıcıların sağlama kapsamında olduğunu belirlemek için en yaygın yöntemdir. Bu atamalar, çoklu oturum açmayı etkinleştirmek ve erişim ve sağlamayı yönetmek için tek bir yöntem sağlamak için de kullanılır. Kapsam filtreleri, kullanıcıların öznitelik değerlerine göre filtreleneceği şekilde, atamaları veya bunların yerine, isteğe bağlı olarak kullanılabilir.

    >[!TIP]
    > **Tüm kullanıcıları ve grupları eşitlemek** için sağlama ayarları altındaki [kapsam](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) menüsündeki ayarları değiştirerek bir kurumsal uygulama için atamaları temel alarak sağlamayı devre dışı bırakabilirsiniz. 

* **HCM uygulamalarından Azure AD 'ye gelen sağlama ve Active Directory**. [Workday gibi bir HCM uygulaması](../saas-apps/workday-tutorial.md) kaynak sistem olduğunda, kapsam filtreleri hcm uygulamasından Active Directory veya Azure AD 'ye hangi kullanıcıların sağlanması gerektiğini belirlemek için birincil yöntemdir.

Varsayılan olarak, Azure AD sağlama bağlayıcılarında hiçbir öznitelik tabanlı kapsam filtresi yapılandırılmamış. 

## <a name="scoping-filter-construction"></a>Kapsam filtresi oluşturma

Kapsam filtresi bir veya daha fazla *yan tümce* içerir. Yan tümceler, her kullanıcının özniteliklerini değerlendirerek kapsam filtresinden hangi kullanıcıların geçmesine izin verileceğini belirleme. Örneğin, bir kullanıcının "durum" özniteliğinin "New York" değerine eşit olmasını gerektiren bir yan tümcesine sahip olabilirsiniz, bu nedenle yalnızca New York kullanıcıları uygulamaya sağlanabilir. 

Tek bir yan tümce tek bir öznitelik değeri için tek bir koşulu tanımlar. Tek bir kapsam filtresinde birden çok yan tümce oluşturulduysa, "AND" mantığı kullanılarak birlikte değerlendirilir. Bu, bir kullanıcının sağlanması için tüm yan tümcelerinin "true" olarak değerlendirilmesi gerektiği anlamına gelir.

Son olarak, tek bir uygulama için birden çok kapsam filtresi oluşturulabilir. Birden çok kapsam filtresi varsa, bunlar "OR" mantığı kullanılarak birlikte değerlendirilir. Bu, yapılandırılmış kapsam filtrelerinden herhangi birinin tüm yan tümcelerinin "true" olarak değerlendirilmesi durumunda kullanıcının sağlanması anlamına gelir.

Azure AD sağlama hizmeti tarafından işlenen her kullanıcı veya grup her zaman kapsam filtrelerinde ayrı ayrı değerlendirilir.

Örnek olarak, aşağıdaki kapsam filtresini göz önünde bulundurun:

![Kapsam filtresi](./media/define-conditional-rules-for-provisioning-user-accounts/scoping-filter.PNG) 

Bu kapsam filtresine göre, kullanıcıların sağlanması için aşağıdaki ölçütleri karşılaması gerekir:

* Bunların New York 'ta olmaları gerekir.
* Mühendislik bölümünde çalışması gerekir.
* Şirket çalışan KIMLIĞI 1.000.000 ile 2.000.000 arasında olmalıdır.
* İş unvanı null veya boş olmamalıdır.

## <a name="create-scoping-filters"></a>Kapsam filtreleri oluşturma
Kapsam filtreleri, her bir Azure AD Kullanıcı sağlama bağlayıcısının öznitelik eşlemelerinin bir parçası olarak yapılandırılır. Aşağıdaki yordamda, [desteklenen uygulamalardan biri](../saas-apps/tutorial-list.md) için otomatik sağlamayı ayarlamış olduğunuz ve buna kapsam filtresi eklemekte olduğunuz varsayılmaktadır.

### <a name="create-a-scoping-filter"></a>Kapsam filtresi oluşturma
1. [Azure Portal](https://portal.azure.com), **Azure Active Directory**  >  **Enterprise Applications**  >  **tüm uygulamalar** bölümüne gidin.

2. Otomatik sağlamayı yapılandırdığınız uygulamayı seçin: Örneğin, "ServiceNow".

3. **Hazırlama** sekmesini seçin.

4. **Eşlemeler** bölümünde, kapsam filtresi yapılandırmak istediğiniz eşlemeyi seçin: Örneğin, "kullanıcıları ServiceNow 'a Azure Active Directory.

5. **Kaynak nesne kapsamı** menüsünü seçin.

6. **Kapsam filtresi ekle**' yi seçin.

7. Bir kaynak **öznitelik adı**, bir **işleç** ve eşleştirilecek bir **öznitelik değeri** seçerek bir yan tümce tanımlayın. Aşağıdaki işleçler desteklenir:

   a. **Eşittir**. Değerlendirilen öznitelik, giriş dizesi değeri tam olarak (büyük/küçük harfe duyarlı) eşleşiyorsa "true" döndürür.

   b. **eşit değildir**. Değerlendirilen öznitelik, giriş dizesi değeriyle (büyük/küçük harfe duyarlı) eşleşmezse "true" döndürür.

   c. **doğru**. Değerlendirilen öznitelik true Boole değeri içeriyorsa, yan tümce "true" döndürür.

   d. **yanlış**. Değerlendirilen öznitelik yanlış Boole değeri içeriyorsa, yan tümce "true" döndürür.

   e. **null**. Değerlendirilen öznitelik boş ise yan tümce "true" döndürür.

   f. **null değil**. Değerlendirilen öznitelik boş değilse yan tümce "true" döndürür.

   örneğin: **Regex eşleşiyor**. Değerlendirilen öznitelik bir normal ifade düzeniyle eşleşiyorsa, yan tümce "true" döndürür. Örneğin: ([1-9] [0-9]), 10 ile 99 arasında herhangi bir sayıyla eşleşir.

   h. **Regex eşleşmiyor**. Değerlendirilen öznitelik bir normal ifade düzeniyle eşleşmezse, yan tümce "true" döndürür.
   
   i. **Greater_Than.** Değerlendirilen öznitelik değerden büyükse, yan tümce "true" döndürür. Kapsam filtresinde belirtilen değer bir tamsayı olmalı ve kullanıcının özniteliği tamsayı olmalıdır [0, 1, 2,...]. 
   
   j. **Greater_Than_OR_EQUALS.** Değerlendirilen öznitelik değerden büyük veya bu değere eşitse, yan tümce "true" döndürür. Kapsam filtresinde belirtilen değer bir tamsayı olmalı ve kullanıcının özniteliği tamsayı olmalıdır [0, 1, 2,...]. 
   
   k. **İçermektedir.** Değerlendirilen öznitelik, [burada](/dotnet/api/system.string.contains)açıklanan dize değerini (büyük/küçük harfe duyarlı) içeriyorsa "true" döndürür. 


>[!IMPORTANT] 
> - Imemberof filtresi şu anda desteklenmiyor.
> - EŞITTIR ve eşit DEĞILDIR birden çok değerli öznitelikler için desteklenmez

9. İsteğe bağlı olarak, daha fazla kapsam tümcesi eklemek için 7-8 arası adımları tekrarlayın.

10. **Kapsam filtresi başlığı**' nda, kapsam filtreniz için bir ad ekleyin.

11. **Tamam**’ı seçin.

12. **Kapsam filtreleri** ekranında yeniden **Tamam ' ı** seçin. İsteğe bağlı olarak, farklı bir kapsam filtresi eklemek için 6-11 arası adımları tekrarlayın.

13. **Öznitelik eşleme** ekranında **Kaydet** ' i seçin. 

>[!IMPORTANT] 
> Yeni bir kapsam filtresi kaydetmek, uygulama için, kaynak sistemdeki tüm kullanıcıların yeni kapsam filtresine göre yeniden değerlendirildiği yeni bir tam eşitleme tetiklemesini tetikler. Uygulamadaki bir Kullanıcı daha önce sağlama için kapsamdadır, ancak kapsam dışında kalırsa, bu durumda hesabı devre dışı bırakılır veya uygulama tarafından devre dışı bırakılır. Bu varsayılan davranışı geçersiz kılmak için [kapsamdaki Kullanıcı hesapları için silme Işlemini atlama](../app-provisioning/skip-out-of-scope-deletions.md)bölümüne bakın.


## <a name="common-scoping-filters"></a>Ortak kapsam filtreleri
| Target özniteliği| İşleç | Değer | Açıklama|
|----|----|----|----|
|userPrincipalName|REGEX EŞLEŞMESI|.\*@domain.com |Etki alanı olan userPrincipal 'a sahip tüm kullanıcılar @domain.com sağlama kapsamında olacaktır|
|userPrincipalName|REGEX EŞLEŞMIYOR|.\*@domain.com|Etki alanı olan userPrincipal 'a sahip tüm kullanıcılar, @domain.com sağlama için kapsam dışı olacaktır|
|bölüm|EŞITTIR|Sales|Satış departmanından tüm kullanıcılar sağlama kapsamındadır|
|Workerıd|REGEX EŞLEŞMESI|(1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])| 1000000 ve 2000000 arasında Workerıd 'Leri olan tüm çalışanlar sağlama kapsamındadır.|

## <a name="related-articles"></a>İlgili makaleler:
* [SaaS uygulamalarına Kullanıcı hazırlama ve sağlamayı kaldırma işlemlerini otomatikleştirme](../app-provisioning/user-provisioning.md)
* [Kullanıcı hazırlama için öznitelik eşlemelerini özelleştirme](../app-provisioning/customize-application-attributes.md)
* [Öznitelik eşlemeleri için ifadeler yazma](functions-for-customizing-application-data.md)
* [Hesap sağlama bildirimleri](../app-provisioning/user-provisioning.md)
* [Kullanıcıları ve grupları Azure Active Directory uygulamalara otomatik olarak sağlamayı etkinleştirmek için SCıM kullanın](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [SaaS uygulamalarının nasıl tümleştirileceği hakkında öğreticiler listesi](../saas-apps/tutorial-list.md)

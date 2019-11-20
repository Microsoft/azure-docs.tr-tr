---
title: Azure Active Directory B2C Kullanıcı verilerini yönetme | Microsoft Docs
description: Azure AD B2C Kullanıcı verilerini silme veya dışarı aktarma hakkında bilgi edinin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2018
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 78726620db119abf617be8a30cf03697b04e382b
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064075"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Azure Active Directory B2C Kullanıcı verilerini yönetme

 Bu makalede [Azure Active Directory Graph API](/previous-versions/azure/ad/graph/api/api-catalog)tarafından sunulan işlemleri kullanarak Azure Active Directory B2C (Azure AD B2C) içindeki kullanıcı verilerini nasıl yönetebileceğinizi ele alınmaktadır. Kullanıcı verilerinin yönetilmesi, denetim günlüklerinden verileri silme veya dışarı aktarmaya dahildir.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Kullanıcı verilerini sil

Kullanıcı verileri Azure AD B2C dizininde ve denetim günlüklerinde depolanır. Tüm Kullanıcı denetim verileri Azure AD B2C 7 gün boyunca tutulur. Bu 7 günlük süre içinde kullanıcı verilerini silmek istiyorsanız, [Kullanıcı silme](/previous-versions/azure/ad/graph/api/users-operations#DeleteUser) işlemini kullanabilirsiniz. Verilerin bulunabileceği Azure AD B2C kiracıların her biri için SILME işlemi gereklidir.

Azure AD B2C içindeki her kullanıcıya bir nesne KIMLIĞI atanır. Nesne KIMLIĞI, Azure AD B2C Kullanıcı verilerini silmek için kullanabileceğiniz belirsiz bir tanımlayıcı sağlar. Mimarinize bağlı olarak, nesne KIMLIĞI finans, pazarlama ve müşteri ilişkisi yönetim veritabanları gibi diğer hizmetler genelinde yararlı bir bağıntı tanımlayıcısı olabilir.

Bir kullanıcının nesne KIMLIĞINI almanın en doğru yolu, Azure AD B2C ile bir kimlik doğrulama yolculuğunun parçası olarak bunu elde kullanmaktır. Başka yöntemler kullanarak bir kullanıcıdan veri için geçerli bir istek alırsanız, müşteri hizmeti destek aracısına göre arama gibi bir çevrimdışı işlem, kullanıcıyı bulmak ve ilişkili nesne KIMLIĞINI aramak için gerekli olabilir.

Aşağıdaki örnekte olası bir veri silme akışı gösterilmektedir:

1. Kullanıcı oturum açar ve **verilerimi Sil**' i seçer.
2. Uygulama, uygulamanın bir yönetim bölümünde yer alan verileri silme seçeneği sunar.
3. Uygulama, bir kimlik doğrulamasını Azure AD B2C zorlar. Azure AD B2C, kullanıcının nesne KIMLIĞI ile uygulamaya geri bir belirteç sağlar.
4. Belirteç uygulama tarafından alınır ve nesne KIMLIĞI, Azure AD Graph API çağrısıyla Kullanıcı verilerini silmek için kullanılır. Azure AD Graph API, Kullanıcı verilerini siler ve 200 Tamam bir durum kodu döndürür.
5. Uygulama, nesne KIMLIĞINI veya diğer tanımlayıcıları kullanarak gerektiğinde Kullanıcı verilerinin diğer kuruluş sistemlerinde silinmesini sağlar.
6. Uygulama, verilerin silinmesini onaylar ve kullanıcıya sonraki adımları sağlar.

## <a name="export-customer-data"></a>Müşteri verilerini dışarı aktar

Azure AD B2C Müşteri verilerini dışarı aktarma işlemi silme işlemine benzerdir.

Azure AD B2C Kullanıcı verileri şu şekilde sınırlıdır:

- **Azure Active Directory depolanan veriler**: Bir Azure AD B2C kimlik doğrulaması Kullanıcı yolculuğunda verileri, nesne KIMLIĞINI veya bir e-posta adresi veya Kullanıcı adı gibi herhangi bir oturum açma adını kullanarak elde edebilirsiniz.
- **Kullanıcıya özgü denetim olayları raporu**: Nesne KIMLIĞINI kullanarak veri dizini oluşturabilirsiniz.

Aşağıdaki bir dışarı aktarma veri akışı örneğinde, uygulama tarafından gerçekleştirilmekte olan adımlar, bir arka uç işlemi ya da dizinde yönetici rolü olan bir kullanıcı tarafından da gerçekleştirilebilir:

1. Kullanıcı uygulamada oturum açar. Azure AD B2C gerekirse Azure Multi-Factor Authentication kimlik doğrulamasını zorlar.
2. Uygulama, Kullanıcı özniteliklerini almak üzere bir Azure AD Graph API işlemini çağırmak için Kullanıcı kimlik bilgilerini kullanır. Azure AD Graph API, öznitelik verilerini JSON biçiminde sağlar. Şemaya bağlı olarak, KIMLIK belirteç içeriğini bir kullanıcıyla ilgili tüm kişisel verileri içerecek şekilde ayarlayabilirsiniz.
3. Uygulama, Kullanıcı denetim etkinliğini alır. Azure AD Graph API, uygulamaya olay verileri sağlar.
4. Uygulama, verileri toplar ve Kullanıcı tarafından kullanılabilir hale getirir.

## <a name="next-steps"></a>Sonraki adımlar

- Kullanıcıların uygulamanıza nasıl erişebileceğini yönetme hakkında bilgi edinmek için bkz. [Kullanıcı erişimini yönetme](manage-user-access.md).





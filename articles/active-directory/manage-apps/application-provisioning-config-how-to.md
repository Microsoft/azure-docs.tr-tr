---
title: Azure AD Galeri uygulamasına kullanıcı sağlamayı yapılandırma | Microsoft Docs
description: Azure AD uygulama galerisinde zaten listelenen uygulamalara zengin Kullanıcı hesabı sağlama ve sağlamayı kaldırma işlemlerini nasıl hızlı bir şekilde yapılandırabileceğinizi öğrenin
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bcc53b97b1187314404cfe075f6593f437e7bf4
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72789055"
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Azure AD Galeri uygulamasına kullanıcı sağlamayı yapılandırma

*Kullanıcı hesabı sağlama* , bir uygulamanın yerel kullanıcı profili deposundaki kullanıcı hesabı kayıtlarını oluşturma, güncelleştirme ve/veya devre dışı bırakma işlemidir. Çoğu bulut ve SaaS uygulaması kullanıcı rolünü ve izinlerini kullanıcının kendi yerel kullanıcı profili deposunda depolar ve bu tür bir Kullanıcı kaydının, çoklu oturum açma ve çalışma için Kullanıcı yerel deposunda bulunması *gerekir* .

Azure portal, bir kurumsal uygulama için sol gezinti bölmesindeki **sağlama** sekmesi, bu uygulama için hangi sağlama modlarının desteklendiğini gösterir. Bu iki değerden biri olabilir:

## <a name="configuring-an-application-for-manual-provisioning"></a>El Ile sağlama için bir uygulamayı yapılandırma

*El ile* sağlama, Kullanıcı hesaplarının uygulama tarafından sunulan yöntemler kullanılarak el ile oluşturulması gerektiği anlamına gelir. Bu, bu uygulama için bir yönetim portalında oturum açma ve Web tabanlı kullanıcı arabirimi kullanan kullanıcılar ekleme anlamına gelir. Ya da bu uygulama tarafından sunulan bir mekanizmayı kullanarak Kullanıcı hesabı ayrıntısı olan bir elektronik tablo karşıya yüklenebilir. Uygulama tarafından sağlanan belgelere başvurun veya hangi mekanizmaların kullanılabildiğini belirlemek için uygulama geliştiricisine başvurun.

*El ile* belirli bir uygulama için gösterilen tek mod ise, uygulama için henüz OTOMATIK Azure AD sağlama Bağlayıcısı olmadığı anlamına gelir. Ya da uygulamanın, bir otomatik sağlama Bağlayıcısı oluşturmak için kullanılan Microsoft 'un Kullanıcı yönetimi API 'sine yönelik önkoşulları desteklemediği anlamına gelir.

Belirli bir uygulama için otomatik sağlama desteği istemek isterseniz, [Azure Active Directory uygulama isteklerini](https://aka.ms/aadapprequest)kullanarak bir isteği doldurabilirsiniz.

## <a name="configuring-an-application-for-automatic-provisioning"></a>Otomatik sağlama için bir uygulamayı yapılandırma

*Otomatik* , bu uygulama Için BIR Azure AD sağlama bağlayıcısının geliştirildiği anlamına gelir. Azure AD sağlama hizmeti ve nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning).

Bir uygulamaya belirli kullanıcılar ve gruplar sağlama hakkında daha fazla bilgi için bkz. [Enterprise Apps için Kullanıcı hesabı sağlamayı yönetme](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning).

Otomatik sağlamayı etkinleştirmek ve yapılandırmak için gereken gerçek adımlar uygulamaya bağlı olarak değişir.

> [!NOTE]
> Uygulamanız için sağlamayı ayarlamaya özgü kurulum öğreticisini bularak ve sağlama bağlantısı oluşturmak için hem uygulamayı hem de Azure AD 'yi yapılandırmak üzere bu adımları takip ederek başlamanız gerekir. 

Uygulama öğreticileri, [SaaS uygulamalarını Azure Active Directory Ile tümleştirme hakkındaki öğreticiler listesinde](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)bulunabilir.

Sağlamayı ayarlarken göz önünde bulundurmanız gereken önemli bir şey, Azure AD 'den uygulamaya hangi kullanıcı (veya grup) özelliklerinin akmasını tanımlayan öznitelik eşlemelerini ve iş akışlarını gözden geçirmeniz ve yapılandırmaktır. Bu, iki sistem arasında kullanıcıları/grupları benzersiz şekilde tanımlamak ve eşleştirmek için kullanılan "eşleşen özelliği" ayarlamayı içerir. Öznitelik eşlemeleri hakkında daha fazla bilgi için *sonraki adımlarda* yer alan bağlantıya bakın.

## <a name="next-steps"></a>Sonraki adımlar
[Azure Active Directory 'da SaaS uygulamaları için Kullanıcı hazırlama öznitelik eşlemelerini özelleştirme](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)


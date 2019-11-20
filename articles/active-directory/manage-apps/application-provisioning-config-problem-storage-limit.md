---
title: Yönetici kimlik bilgilerini kaydetme sorunu Azure AD Galeri uygulamasını yapılandırma | Microsoft Docs
description: Azure AD uygulama galerisinde zaten listelenen bir uygulamaya Kullanıcı sağlamayı yapılandırırken karşılaştığı yaygın sorunları giderme
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
ms.date: 02/21/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 152b704ca597fb473a820124ee6147d6d9bc7845
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381470"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>Azure Active Directory Galeri uygulamasına kullanıcı sağlamayı yapılandırırken yönetici kimlik bilgileri kaydedilirken sorun oluştu 

Bir kurumsal uygulama için [Otomatik Kullanıcı sağlamayı](user-provisioning.md) yapılandırmak üzere Azure Portal kullanırken şu koşullarda karşılaşabilirsiniz:

* Uygulama için girilen **yönetici kimlik bilgileri** geçerlidir ve **Bağlantıyı Sına** düğmesi kullanılır. Ancak, kimlik bilgileri kaydedilemez ve Azure portal genel bir hata iletisi döndürür.

Aynı uygulama için SAML tabanlı çoklu oturum açma da yapılandırılırsa, hatanın en olası nedeni, Azure AD 'nin iç, sertifikalar ve kimlik bilgileri için uygulama başına depolama sınırının aşılmasından kaynaklanıyor olabilir.

Azure AD 'nin şu anda tüm sertifikalar, gizli belirteçler, kimlik bilgileri ve bir uygulamanın tek bir örneğiyle ilişkili yapılandırma verileri (Azure AD 'de hizmet sorumlusu kaydı olarak da bilinir) için en fazla 1024 bayt depolama kapasitesi vardır.

SAML tabanlı çoklu oturum açma yapılandırıldığında, SAML belirteçlerini imzalamak için kullanılan sertifika burada depolanır ve genellikle alanın yüzde 50 ' ünü kullanır.

Kullanıcı hazırlama kurulumu sırasında girilen gizli belirteçler, URI 'Ler, bildirim e-posta adresleri, Kullanıcı adları ve parolalar, depolama sınırının aşılmasına neden olabilir.

## <a name="how-to-work-around-this-issue"></a>Bu sorunu geçici olarak çözmek 

Bu sorunu çözmek için iki olası yol vardır:

1. **Bir diğeri çoklu oturum açma için bir tane olmak üzere bir tane olmak üzere iki Galeri uygulaması örneğini kullanın** , Galeri uygulaması [LinkedIn](../saas-apps/linkedinelevate-tutorial.md) 'i örnek olarak almak için Galeri 'den LinkedIn 'i ekleyebilir ve çoklu oturum açma için yapılandırabilirsiniz. Sağlama için Azure AD uygulama galerisinden LinkedIn 'in başka bir örneğini ekleyin ve "LinkedIn yükselt (sağlama)" olarak adlandırın. Bu ikinci örnek için [sağlamayı](../saas-apps/linkedinelevate-provisioning-tutorial.md)yapılandırın, ancak çoklu oturum açmayı yapılandırın. Bu geçici çözüm kullanılırken, her iki uygulamaya de aynı kullanıcı ve grupların [atanması](assign-user-or-group-access-portal.md) gerekir. 

2. **Depolanan yapılandırma veri miktarını azaltın** -sağlama sekmesinin [yönetici kimlik bilgileri](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) bölümünde girilen tüm veriler SAML sertifikasıyla aynı yerde saklanır. Bu verilerin tümünün uzunluğunu azaltmak mümkün olmasa da, **bildirim e-postası** gibi bazı isteğe bağlı yapılandırma alanları kaldırılabilir.

## <a name="next-steps"></a>Sonraki adımlar
[SaaS uygulamalarına Kullanıcı hazırlama ve sağlamayı kaldırma işlemlerini yapılandırma](user-provisioning.md)

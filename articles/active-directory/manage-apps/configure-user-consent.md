---
title: Bir uygulama için Kullanıcı onayı Yapılandırma-Azure Active Directory | Microsoft Docs
description: Kullanıcıların uygulama izinlerini kabul etme şeklini yönetmeyi öğrenin. Yönetici onayı vererek Kullanıcı deneyimini basitleştirebilirsiniz. Bu yöntemler Azure Active Directory (Azure AD) kiracınızdaki tüm son kullanıcılar için geçerlidir.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bd746e79bc9d70be23771f97b1757f090f6375f
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709282"
---
# <a name="configure-the-way-end-users-consent-to-an-application-in-azure-active-directory"></a>Azure Active Directory ' deki bir uygulamaya Son Kullanıcı onay biçimini yapılandırma
Kullanıcıların uygulama izinlerini kabul etme şeklini nasıl yapılandıracağınızı öğrenin. Yönetici onayı vererek Kullanıcı deneyimini basitleştirebilirsiniz. Bu makalede, Kullanıcı onayını yapılandırabileceğiniz farklı yollar sunulmaktadır. Yöntemler, Azure Active Directory (Azure AD) kiracınızdaki tüm son kullanıcılar için geçerlidir. 

Uygulamalara yönelik yarışmaya yönelik daha fazla bilgi için bkz. [Azure Active Directory izin çerçevesi](../develop/consent-framework.md).

## <a name="prerequisites"></a>Prerequisites

Yönetici onayı verme, genel yönetici, uygulama Yöneticisi veya bulut uygulama Yöneticisi olarak oturum açmanızı gerektirir.

Uygulamalara erişimi kısıtlamak için Kullanıcı ataması yapmanız ve ardından kullanıcılara veya grupları uygulamaya atamanız gerekir.  Daha fazla bilgi için bkz. [kullanıcıları ve grupları atamaya yönelik yöntemler](methods-for-assigning-users-and-groups.md).

## <a name="grant-admin-consent-to-enterprise-apps-in-the-azure-portal"></a>Azure portal kurumsal uygulamalara yönetici onayı verme

Bir kurumsal uygulamaya yönetici onayı vermek için:

1. [Azure Portal](https://portal.azure.com) genel yönetici, uygulama Yöneticisi veya bulut uygulama Yöneticisi olarak oturum açın.
2. Sol taraftaki Gezinti menüsünün en üstünde bulunan **tüm hizmetler** ' e tıklayın. **Azure Active Directory uzantısı** açılır.
3. Filtre arama kutusuna **"Azure Active Directory"** yazın ve **Azure Active Directory** öğesini seçin.
4. Gezinti menüsünde **Kurumsal uygulamalar**' a tıklayın.
5. İzin için uygulamayı seçin.
6. **İzinler** ' i seçin ve ardından **yönetici izni ver**' e tıklayın. Uygulamayı yönetmek için oturum açmanız istenir.
7. Uygulama için yönetici onayı verme izinlerine sahip bir hesapla oturum açın. 
8. Uygulama izinleri onayı.

Bu seçenek yalnızca uygulama şu olduğunda geçerlidir: 

- Kiracınızda kayıtlı veya
- Başka bir Azure AD kiracısına kaydolmuş ve en az bir son kullanıcı tarafından onaylanan. Son Kullanıcı bir uygulamaya ulaştıktan sonra, Azure AD Azure portal **Kurumsal uygulamalar** altında uygulamayı listeler.

## <a name="grant-admin-consent-when-registering-an-app-in-the-azure-portal"></a>Azure portal uygulama kaydederken yönetici onayı verme

Bir uygulamayı kaydederken yönetici onayı vermek için: 

1. [Azure Portal](https://portal.azure.com) genel yönetici olarak oturum açın.
2. **Uygulama kayıtları** dikey penceresine gidin.
3. Onay için uygulamayı seçin.
4. **API izinleri**' ni seçin.
5. **Yönetici Izni ver**' e tıklayın.


## <a name="grant-admin-consent-through-a-url-request"></a>URL isteği için yönetici onayı verme

Bir URL isteği aracılığıyla yönetici onayı vermek için:

1. Uygulama yapılandırlarınızla *login.microsoftonline.com* için bir istek oluşturun ve `&prompt=admin_consent` ' e ekleyin. Bu URL şöyle görünür: `https://login.microsoftonline.com/<tenant-id>/oauth2/authorize?client_id=<client id>&response_type=code&redirect_uri=<Your-Redirect-URI-Https-Encoded>&nonce=1234&resource=<your-resource-Https-encoded>&prompt=admin_consent`
2. Yönetici kimlik bilgileriyle oturum açtıktan sonra, uygulamaya tüm kullanıcılar için izin verildi.


## <a name="force-user-consent-through-a-url-request"></a>URL isteği aracılığıyla Kullanıcı onayını zorla

Son kullanıcıların her kimlik doğrulamasından geçen her seferinde bir uygulamaya onay vermesini gerektirmek için, kimlik doğrulaması istek URL 'sine `&prompt=consent` ekleyin.
Bu URL şöyle görünür: `https://login.microsoftonline.com/<tenant-id>/oauth2/authorize?client_id=<client id>&response_type=code&redirect_uri=<Your-Redirect-URI-Https-Encoded>&nonce=1234&resource=<your-resource-Https-encoded>&prompt=consent`

## <a name="next-steps"></a>Sonraki adımlar

[Uygulamaları AzureAD 'e onay ve Tümleştirme](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

[AzureAD v 2.0 yakınsanmış uygulamalar için onay ve Izinler](../develop/active-directory-v2-scopes.md)

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

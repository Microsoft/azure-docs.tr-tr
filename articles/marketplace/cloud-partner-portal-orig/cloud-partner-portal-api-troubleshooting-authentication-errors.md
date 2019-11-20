---
title: Yaygın kimlik doğrulama hatalarıyla ilgili sorunları giderme | Azure Marketi
description: Bulut İş Ortağı Portalı API 'Leri kullanılırken ortak kimlik doğrulama hatalarıyla ilgili yardım sağlar.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 0990e9aedf17f6d4ad01e4911e47efd60001f3d7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827361"
---
# <a name="troubleshooting-common-authentication-errors"></a>Yaygın kimlik doğrulama hatalarında sorun giderme

Bu makalede, Bulut İş Ortağı Portalı API 'Leri kullanılırken ortak kimlik doğrulama hatalarıyla ilgili yardım sağlanmaktadır.

## <a name="unauthorized-error"></a>Yetkisiz hata

Sürekli olarak `401 unauthorized` hata alırsanız, geçerli bir erişim belirteciniz olduğunu doğrulayın.  Daha önce yapmadıysanız, [kaynaklara erişebilen Azure Active Directory bir uygulama ve hizmet sorumlusu oluşturmak için Portal kullanma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)bölümünde açıklandığı gibi temel bir Azure Active Directory (Azure AD) uygulaması ve hizmet sorumlusu oluşturun. Daha sonra, erişimi doğrulamak için uygulamayı veya basit bir HTTP POST isteğini kullanın.  Aşağıdaki görüntüde gösterildiği gibi, erişim belirtecini almak için kiracı KIMLIĞI, uygulama KIMLIĞI, nesne KIMLIĞI ve gizli anahtarı dahil edersiniz:

![401 hatası sorunlarını giderme](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Yasak hata

`403 forbidden` bir hata alırsanız, Bulut İş Ortağı Portalı yayımcı hesabınıza doğru hizmet sorumlusu eklendiğinden emin olun.
Hizmet sorumlunuzu portala eklemek için [Önkoşullar](./cloud-partner-portal-api-prerequisites.md) sayfasındaki adımları izleyin.

Doğru hizmet sorumlusu eklendiyse, diğer tüm bilgileri doğrulayın. Portalda girilen nesne KIMLIĞINE yakın bir uyarı ödeyin. Azure Active Directory uygulama kaydı sayfasında iki nesne kimliği vardır ve yerel nesne kimliğini kullanmanız gerekir. Uygulamanızın **uygulama kayıtları** sayfasına giderek ve **yerel dizinde yönetilen uygulama**altındaki uygulama adına tıklayarak doğru değeri bulabilirsiniz. Bu sizi, aşağıdaki şekilde gösterildiği gibi **Özellikler** sayfasında doğru nesne kimliğini bulabileceğiniz uygulamanın yerel özelliklerine götürür. Ayrıca, hizmet sorumlusunu eklerken ve API çağrısını yaptığınızda doğru Yayımcı KIMLIĞINI kullandığınızdan emin olun.

![403 hatası sorunlarını giderme](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)

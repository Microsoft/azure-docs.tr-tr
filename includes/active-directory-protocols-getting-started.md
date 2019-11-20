---
title: Azure AD .NET Protokolü’ne Genel Bakış | Microsoft Docs
description: Kiracınızda Azure AD kullanan web uygulamalarına ve web API’lerine erişim izni vermek için HTTP iletilerini kullanma.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2019
ms.author: priyamo
ms.openlocfilehash: 7772b3ee5d0e27c09e83f7d118eb9f67f17e0d07
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73524018"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Uygulamanızı AD kiracınıza kaydetme
İlk olarak, Azure Active Directory (Azure AD) kiracınızı kullanarak uygulamanızı kaydedin. Bu, uygulamanıza bir Uygulama Kimliği verir ve uygulamanızın belirteçleri alabilmesini sağlar.

1. [Azure portalında](https://portal.azure.com) oturum açın.
   
1. Sayfanın sağ üst köşesindeki hesabınızı seçerek Azure AD kiracınızı seçin ve ardından **Dizin gezintisini Değiştir** ' i seçip uygun kiracıyı seçin. 
   - Hesabınız kapsamında yalnızca bir Azure AD kiracınız varsa veya uygun Azure AD kiracısını zaten seçtiyseniz bu adımı atlayın.
   
1. Azure portal, araması yapın ve **Azure Active Directory**seçin.
   
1. **Azure Active Directory** sol menüsünde **uygulama kayıtları**' nı ve ardından **Yeni kayıt**' yi seçin.
   
1. Komut istemlerini izleyin ve yeni bir uygulama oluşturun. Bu öğretici için bir Web uygulaması ya da bir genel istemci (mobil & Masaüstü) uygulaması olsa da, Web uygulamaları veya genel istemci uygulamalarına yönelik özel örnekler isterseniz [hızlı](../articles/active-directory/develop/v1-overview.md)başlangıçlarımızı inceleyin.
   
   - **Ad**, uygulamanın adıdır ve uygulamanızı son kullanıcılara açıklar.
   - **Desteklenen hesap türleri**altında, **herhangi bir kurumsal dizin ve kişisel Microsoft hesabında hesaplar**' ı seçin.
   - **Yeniden yönlendirme URI 'si**sağlayın. Web uygulamaları için, bu, kullanıcıların oturum açabileceği uygulamanızın temel URL 'sidir.  Örneğin, `http://localhost:12345`. Ortak istemci (mobil & Masaüstü) için Azure AD, belirteç yanıtlarını döndürmek için bunu kullanır. Uygulamanıza özgü bir değer girin.  Örneğin, `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. Kaydı tamamladıktan sonra, Azure AD, uygulamanıza benzersiz bir istemci tanımlayıcısı ( **uygulama kimliği**) atayacaktır. Sonraki bölümlerde bu değere ihtiyacınız olduğundan uygulama sayfasından kopyalayın.
   
1. Uygulamanızı Azure portal bulmak için **uygulama kayıtları**' i seçin ve ardından **tüm uygulamaları görüntüle**' yi seçin.

---
title: Oturum açma sırasında Kullanıcı adı arama Azure Active Directory | Microsoft Docs
description: Ekran mesajlaşması Azure Active Directory oturum açma sırasında Kullanıcı adı aramasını yansıtır
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b6a65a964016f702fcf75aa4cbdab33a952e3b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024245"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Azure Active Directory oturum açma sayfaları için giriş bölgesi bulma

Yeni kimlik doğrulaması yöntemlerine yer açmak ve kullanılabilirliği geliştirmek için Azure Active Directory (Azure AD) oturum açma davranışımızı değiştiriyoruz. Oturum açma işlemi sırasında Azure AD kullanıcının nerede kimlik doğrulaması yapması gerektiğini belirler. Azure AD, oturum açma sayfasında girilen kullanıcı adının kuruluş ve kullanıcı ayarlarını okuyarak akıllı kararlar alır. Bu, FIDO 2.0 gibi ek kimlik bilgilerinin kullanılmasına imkan tanıyan parolasız bir geleceğe doğru atılan bir adım.

## <a name="home-realm-discovery-behavior"></a>Giriş bölgesi bulma davranışı

Tarihsel olarak, giriş bölgesi bulma, oturum açma sırasında veya bazı eski uygulamalar için bir giriş bölgesi bulma ilkesiyle belirtilen etki alanı tarafından yönetilir. Örneğin, bulma davranışımızda Azure Active Directory bir Kullanıcı Kullanıcı adını yanlış bir şekilde yanlış verebilir, ancak kuruluşunuzun kimlik bilgisi toplama ekranına ulaşacak. Bu durum, Kullanıcı kuruluşun "contoso.com" etki alanı adını doğru şekilde sunursa oluşur. Bu davranış, bireysel kullanıcı için deneyimlerin özelleştirilmesi ayrıntı düzeyine izin vermez.

Daha geniş bir kimlik bilgilerini desteklemek ve kullanılabilirliği artırmak için, oturum açma işlemi sırasında Azure Active Directory Kullanıcı adı arama davranışı artık güncellenir. Yeni davranış, oturum açma sayfasına girilen kullanıcı adına göre kiracı ve Kullanıcı düzeyi ayarlarını okuyarak akıllı kararlar verir. Bunu mümkün kılmak için Azure Active Directory, oturum açma sayfasına girilen kullanıcı adının belirtilen etki alanında bulunup bulunmadığını ve Kullanıcı kimlik bilgilerini sağlamak için kullanıcıyı yeniden yönlendirdiği denetlenir.

Bu çalışmanın ek bir avantajı, hata iletileri geliştirilerek geliştirilmiştir. Aşağıda yalnızca Azure Active Directory Kullanıcıları destekleyen bir uygulamada oturum açarken geliştirilmiş hata iletilerine ilişkin bazı örnekler verilmiştir.

- Kullanıcı adı yanlış yazılmış veya Kullanıcı adı henüz Azure AD ile eşitlenmedi:
  
    ![Kullanıcı adı yanlış yazılmış veya bulunamadı](./media/signin-realm-discovery/typo-username.png)
  
- Etki alanı adı yanlış yazılmış:
  
    ![etki alanı adı yanlış yazılmış veya bulunamadı](./media/signin-realm-discovery/typo-domain.png)
  
- Kullanıcı bilinen bir tüketici etki alanıyla oturum açmaya çalışır:
  
    ![bilinen bir tüketici etki alanı ile oturum açın](./media/signin-realm-discovery/consumer-domain.png)
  
- Parola yanlış yazmış, ancak Kullanıcı adı doğru:  
  
    ![parola, iyi Kullanıcı adı ile yanlış yazılmış](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Bu özellik, Federasyonu zorlamak için eski etki alanı düzeyi giriş bölgesi bulma 'ya bağlı olan Federasyon etki alanları üzerinde etkiye sahip olabilir. Federasyon etki alanı desteğinin ekleneceği güncelleştirmeler için, [Microsoft 365 hizmetleri için oturum açma sırasında giriş bölgesi bulma](https://azure.microsoft.com/updates/signin-hrd/)bölümüne bakın. Bu sırada, bazı kuruluşlar çalışanları Azure Active Directory olmayan bir kullanıcı adıyla oturum açmak için eğitildi, ancak etki alanı adları kullanıcıları kendi kuruluşlarının etki alanı uç noktasına yönlendirtiğinden, uygun etki alanı adını içerir. Yeni oturum açma davranışı buna izin vermez. Kullanıcı, Kullanıcı adını düzeltmek üzere bilgilendirilir ve Azure Active Directory mevcut olmayan bir kullanıcı adıyla oturum açmasına izin verilmez.
>
> Siz veya kuruluşunuzda eski davranışa bağlı olan uygulamalar varsa, Kuruluş yöneticilerinin çalışan oturum açma ve kimlik doğrulama belgelerini güncelleştirmesi ve çalışanların oturum açmasını sağlamak için Azure Active Directory Kullanıcı adlarını kullanma konusunda bilgi sahibi olmanız önemlidir.
  
Yeni davranışla ilgili endişeleriniz varsa, bu makalenin **geri bildirim** bölümünde yer alarak yaptığınız açıklamaları bırakın.  

## <a name="next-steps"></a>Sonraki adımlar

[Oturum açma markanızı özelleştirin](../fundamentals/add-custom-domain.md)

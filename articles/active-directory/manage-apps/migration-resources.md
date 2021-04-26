---
title: Azure Active Directory uygulamasına uygulama geçirme kaynakları | Microsoft Docs
description: Uygulama erişimini ve kimlik doğrulamasını Azure Active Directory (Azure AD) ile geçirmenize yardımcı olacak kaynaklar.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/29/2020
ms.author: iangithinji
ms.reviewer: baselden
ms.openlocfilehash: 2d01c174bbfa522700773b87737b1e3da2de422e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376656"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Uygulamaları Azure Active Directory’ye geçirmeye yönelik kaynaklar

Uygulama erişimini ve kimlik doğrulamasını Azure Active Directory (Azure AD) ile geçirmenize yardımcı olacak kaynaklar.

| Kaynak  | Açıklama  |
|:-----------|:-------------|
|[Uygulamalarınızı Azure AD 'ye geçirme](https://aka.ms/migrateapps/whitepaper) | Bu Teknik İnceleme, geçişin avantajlarının yanı sıra, açıkça Seviyelendirilmiş dört aşamada geçişin nasıl planlanacağını açıklar: bulma, sınıflandırma, geçiş ve devam eden yönetim. İşlemin nasıl düşündüğünü ve projenizi kullanımı kolay parçalara nasıl başlayacağınızı öğreneceksiniz. Belge genelinde, bu şekilde size yardımcı olacak önemli kaynakların bağlantıları bulunur. |
|[Çözüm Kılavuzu: uygulamaları Active Directory Federasyon Hizmetleri (AD FS) (AD FS) 'den Azure AD 'ye geçirme](./migrate-adfs-apps-to-azure.md) | Bu çözüm kılavuzu, geçiş teknik incelemesi 'nde daha yüksek bir düzeyde açıklanan uygulama geçiş projesini planlama ve yürütmenin aynı dört aşamada size kılavuzluk eder. Bu kılavuzda, bir uygulamayı Azure Directory Federasyon Hizmetleri 'nden (AD FS) Azure AD 'ye taşımaya yönelik belirli bir hedefe bu aşamaları nasıl uygulayacağınızı öğreneceksiniz.|
|[Geliştirici öğreticisi: geliştiriciler için Azure AD uygulama geçişi PlayBook 'a AD FS](https://aka.ms/adfsplaybook) | Bu ASP.NET kod örnekleri ve eşlik eden öğreticiler, Active Directory Federasyon Hizmetleri (AD FS) (AD FS) ile tümleştirilmiş uygulamalarınızı Azure Active Directory (Azure AD) ile güvenli bir şekilde nasıl geçirebileceğinizi öğrenmenize yardımcı olur. Bu öğreticide, hem AD FS hem de Azure AD 'de uygulama yapılandırma hakkında bilgi edinmeleri ve aynı zamanda kod tabanı bu işlemde gereken değişikliklere ve ne kadar emin olmaya ihtiyaç duyan geliştiricilere odaklanılmıştır.|
| [Araç: Active Directory Federasyon Hizmetleri (AD FS) geçiş hazırlığı betiği](https://aka.ms/migrateapps/adfstools) | Bu, şirket içi Active Directory Federasyon Hizmetleri (AD FS) (AD FS) sunucunuzda çalıştırabileceğiniz ve Azure AD 'ye geçiş için uygulamaların hazır olduğunu belirleyebilmeniz için kullanabileceğiniz bir betiktir.|
| [Dağıtım planı: AD FS 'den Parola karması eşitlemesine geçiş](https://aka.ms/ADFSTOPHSDPDownload) | Parola karması eşitlemeyle, kullanıcı parolalarının karmaları şirket içi Active Directory Azure AD 'ye eşitlenir. Bu, Azure AD 'nin şirket içi Active Directory etkileşime girmeden kullanıcıların kimliğini doğrulamasına olanak sağlar.| 
| [Dağıtım planı: AD FS 'den geçişli kimlik doğrulamaya geçiş](https://aka.ms/ADFSTOPTADPDownload)|Azure AD geçişli kimlik doğrulaması, kullanıcıların aynı parolayı kullanarak hem şirket içi hem de bulut tabanlı uygulamalarda oturum açmalarına yardımcı olur. Bu özellik, daha az bir parolaya sahip olmaları gerektiğinden kullanıcılarınıza daha iyi bir deneyim sağlar. Ayrıca, kullanıcıların yalnızca bir parolayı hatırlamaları gerektiğinde nasıl oturum açabileceğini unutmaları daha az olduğu için BT yardım masası maliyetlerini de azaltır. Kullanıcılar Azure AD'de oturum açtığında bu özellik parolaları doğrudan şirket için Active Directory dizininizde doğrular.|
| [Dağıtım planı: Azure AD ile bir SaaS uygulamasında çoklu oturum açmayı etkinleştirme](https://aka.ms/SSODPDownload) | Çoklu oturum açma (SSO), tek bir kullanıcı hesabı kullanarak yalnızca bir kez oturum açarak iş yapmak için ihtiyacınız olan tüm uygulamalara ve kaynaklara erişmenize yardımcı olur. Örneğin, bir Kullanıcı oturum açtıktan sonra Kullanıcı Microsoft Office, SalesForce 'a (örneğin, bir parola yazarak) ikinci bir kez geçebilir. 
| [Dağıtım planı: uygulama proxy 'Si ile uygulamaları Azure AD 'ye genişletme](https://aka.ms/AppProxyDPDownload)| Çalışan dizüstü bilgisayarlardan ve diğer cihazlardan şirket içi uygulamalara erişim sağlamak, geleneksel olarak sanal özel ağlar (VPN 'Ler) veya sivilleştirilmiş bölgeler (DMZs) ile ilgilidir. Bunlar yalnızca karmaşık ve güvenliği zor sağlanan çözümler değil aynı zamanda kurulumu ve yönetimi yüksek maliyetli seçeneklerdir. Azure AD Uygulama Ara Sunucusu, şirket içi uygulamalara erişmeyi kolaylaştırır. |
| [Dağıtım planları](../fundamentals/active-directory-deployment-plans.md) | Multi-Factor Authentication, koşullu erişim, Kullanıcı sağlama, sorunsuz SSO, self servis parola sıfırlama ve daha fazlası gibi özellikleri dağıtmaya yönelik daha fazla dağıtım planı bulabilirsiniz! |
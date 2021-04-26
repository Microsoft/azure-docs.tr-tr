---
title: 'Azure AD Connect: yükleme türünü seçin | Microsoft Docs'
description: Bu konu, Azure AD Connect için kullanılacak yükleme türünü seçme konusunda size rehberlik eder
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7275d115210100bdd4a3a2eb683c867a6a4a4f4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996619"
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Azure AD Connect için hangi yükleme türünün kullanılacağını seçme
Azure AD Connect yeni yükleme için iki yükleme türü vardır: hızlı ve özelleştirilmiş. Bu konu, yükleme sırasında hangi seçeneği kullanacağınızı belirlemenize yardımcı olur.

## <a name="express"></a>Express
Hızlı, en yaygın seçenektir ve tüm yeni yüklemelerin %90 ' si tarafından kullanılır. En yaygın müşteri senaryolarında çalışacak bir yapılandırma sağlamak üzere tasarlanmıştır.

Şunu varsayar:

- Şirket içinde tek bir Active Directory ormanınızın olması gerekir.
- Yükleme için kullanabileceğiniz bir kurumsal yönetici hesabınız var.
- Şirket içi Active Directory 100.000 ' den az nesneniz var.

Şunu alırsınız:

- Çoklu oturum açma için Şirket içinden Azure AD 'ye [Parola karması eşitleme](how-to-connect-password-hash-synchronization.md) .
- [Kullanıcıları, grupları, kişileri ve Windows 10 bilgisayarlarını](concept-azure-ad-connect-sync-default-configuration.md)eşitleyen bir yapılandırma.
- Tüm etki alanlarında ve tüm OU 'Larda uygun olan tüm nesnelerin eşitlenmesi.
- Her zaman kullanılabilir en son sürümü kullandığınızdan emin olmak için [otomatik yükseltme](how-to-connect-install-automatic-upgrade.md) etkindir.

Express 'ı kullanmaya devam edebilirsiniz seçenekleri:

- Tüm OU 'Ları eşitlemek istemiyorsanız, yine de Express 'ı kullanmaya devam edebilirsiniz, son sayfada * * eşitleme işlemini başlat... * * *. Sonra, Yükleme Sihirbazı 'nı yeniden çalıştırın ve [yapılandırma seçeneklerinde](how-to-connect-installation-wizard.md#customize-synchronization-options) OU 'ları değiştirin ve zamanlanan eşitlemeyi etkinleştirin.
- Parola geri yazma gibi Azure AD Premium özelliklerden birini etkinleştirmek istiyorsunuz. İlk Yüklemenin tamamlanmasını sağlamak için öncelikle Express 'e gidin. Sonra, Yükleme Sihirbazı 'nı yeniden çalıştırın ve [yapılandırma seçeneklerini](how-to-connect-installation-wizard.md#customize-synchronization-options)değiştirin.

## <a name="custom"></a>Özel
Özelleştirilmiş yol, Express 'ten çok daha fazla seçeneğe izin verir. Bu, Express için önceki bölümde açıklanan yapılandırmanın kuruluşunuz için temsilci olmadığı tüm durumlarda kullanılmalıdır.

Şu durumlarda kullanın:

- Active Directory ' de bir kurumsal yönetici hesabına erişiminiz yok.
- Birden fazla ormanınız var veya gelecekte birden fazla orman eşitlemesini planlıyorsunuz.
- Ormanınızda bulunan etki alanlarına Connect sunucusundan ulaşılamıyor.
- Kullanıcı oturumu açmak için Federasyon veya geçişli kimlik doğrulaması kullanmayı planlarsınız.
- 100.000 ' den fazla nesneniz var ve tam SQL Server kullanmanız gerekiyor.
- Yalnızca etki alanı veya OU tabanlı filtreleme değil, grup tabanlı filtreleme kullanmayı planlarsınız.

## <a name="upgrade-from-dirsync"></a>DirSync'ten yükseltme
Şu anda DirSync kullanıyorsanız, var olan yapılandırmanızı yükseltmek için [DirSync 'Ten yükseltme](how-to-dirsync-upgrade-get-started.md) bölümündeki adımları uygulayın. İki farklı yükseltme seçeneği vardır:

- Aynı sunucuya Connect yüklemek için yerinde yükseltme.
- Mevcut DirSync sunucusu çalışmaya devam ederken yeni bir sunucuya Connect 'e yüklenecek paralel dağıtım.

## <a name="upgrade-from-azure-ad-sync"></a>Azure AD Eşitleme 'den yükselt
Şu anda Azure AD Eşitleme kullanıyorsanız, bir Connect sürümünden daha yeni sürümüne yükselttiğinizde [aynı adımları](how-to-upgrade-previous-version.md) izleyebilirsiniz. İki farklı yükseltme seçeneği vardır:

- Aynı sunucuya Connect yüklemek için yerinde yükseltme.
- Esnek-yüklemeye geçiş, mevcut Azure AD Eşitleme sunucusu çalışmaya devam ederken yeni bir sunucuya Bağlan.

## <a name="migrate-from-fim2010-or-mim2016"></a>FıM2010 veya MıM2016 'den geçiş
Şu anda Azure AD Bağlayıcısı ile Forefront Identity Manager 2010 veya Microsoft Identity Manager 2016 kullanıyorsanız, tek seçeneğiniz bir geçiş olur. [Esnek geçiş](how-to-upgrade-previous-version.md#swing-migration)bölümünde açıklanan adımları izleyin. Adımlarda Azure AD Eşitleme bahsetme FıM2010/MıM2016 ile değiştirin.

## <a name="next-steps"></a>Sonraki adımlar
Kullanmayı seçtiğiniz seçeneğe bağlı olarak, ayrıntılı adımlarla makalenizi bulmak için soldaki içerik tablosunu kullanın.

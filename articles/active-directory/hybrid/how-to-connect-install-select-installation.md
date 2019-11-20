---
title: 'Azure AD Connect: Yükleme türünü seçin. | Microsoft Docs'
description: Bu konuda, Azure AD Connect için kullanılacak yükleme türünü seç konusunda yol göstermektedir
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
ms.topic: conceptual
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90a624a6b3b4696899af0d8606f653df260cc201
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60348289"
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Azure AD Connect için kullanılacak yükleme türünü seçin
Azure AD Connect yeni yükleme için iki yükleme türü vardır: Hızlı ve özelleştirilmiş. Bu konu, yükleme sırasında kullanmak için hangi seçeneği karar vermenize yardımcı olur.

## <a name="express"></a>Express
Hızlı, en yaygın kullanılan bir seçenektir ve yaklaşık % 90'ını tarafından tüm yeni yüklemeleri kullanılır. En yaygın müşteri senaryoları için uygun bir yapılandırma sağlamak için tasarlanmıştır.

Bunu varsayılır:

- Şirket içi ormanı tek bir Active Directory var.
- Yükleme için kullanabileceğiniz bir Kurumsal Yönetici hesabınızın olması.
- Şirket içi Active Directory'nizde 100. 000'den az nesneler var.

Şunlara sahip olursunuz:

- [Parola Karması eşitleme](how-to-connect-password-hash-synchronization.md) şirket içinden Azure ad çoklu oturum açma için.
- Eşitleyen bir yapılandırma [kullanıcılar, gruplar, kişiler ve Windows 10 bilgisayarlar](concept-azure-ad-connect-sync-default-configuration.md).
- Tüm etki alanları ve tüm OU'larda uygun tüm nesnelerin eşitleme.
- [Otomatik yükseltme](how-to-connect-install-automatic-upgrade.md) kullanılabilir en son sürüme her zaman kullandığınızdan emin olmak için etkinleştirilir.

Seçenekler burada Express kullanmaya devam edebilirsiniz:

- Tüm OU'larda eşitlenecek istemiyorsanız hala Express ve kullanabilirsiniz son sayfasında seçimini kaldır **...eşitleme işlemini** *. Yükleme Sihirbazı'nı yeniden çalıştırın ve alanındaki OU'ları değiştirmek [yapılandırma seçenekleri](how-to-connect-installation-wizard.md#customize-synchronization-options) ve zamanlanmış eşitlemeyi etkinleştirin.
- Bir Azure AD Premium, parola geri yazma gibi özellikleri etkinleştirmek istiyorsunuz. Önce ilk yükleme almak için express gidin. Yükleme Sihirbazı'nı yeniden çalıştırın ve değiştirme [yapılandırma seçenekleri](how-to-connect-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Özel
Özelleştirilmiş yolu express daha çok daha fazla seçenek verir. Burada express için önceki bölümde açıklanan yapılandırma, kuruluşunuz için temsili olmadığı tüm durumlarda kullanılmalıdır.

Şu durumlarda kullanın:

- Active Directory içinde bir kuruluş yöneticisi hesabı için erişiminiz yok.
- Birden fazla ormanınız veya birden fazla orman gelecekte eşitlemek planlama.
- Connect sunucusundan ulaşılabilir değil, ormanınızdaki etki alanınız.
- Kullanıcı oturum açma için Federasyon veya geçişli kimlik doğrulaması kullanmayı planlayın.
- 100. 000'den fazla nesneniz ve tam SQL Server'ı kullanmanız gerekir.
- Grup tabanlı filtreleme ve yalnızca etki alanı veya OU tabanlı filtreleme kullanmayı planlayın.

## <a name="upgrade-from-dirsync"></a>DirSync'ten yükseltme
Şu anda DirSync kullanıyorsanız, adımları takip edin [Dirsync'ten yükseltme](how-to-dirsync-upgrade-get-started.md) yapılandırmanızda yükseltmek için. İki farklı yükseltme seçenekleri şunlardır:

- Sunucuya Bağlan'ı yüklemek için yerinde yükseltme.
- Mevcut bir DirSync sunucunuz hala çalışır durumdayken Connect'i yeni bir sunucuya yüklemek için paralel dağıtım.

## <a name="upgrade-from-azure-ad-sync"></a>Azure AD eşitleme'den yükseltme
Azure AD eşitleme şu anda kullanmakta olduğunuz sonra izleyebilirsiniz [aynı adımları](how-to-upgrade-previous-version.md) Connect sürümünden yeni bir yükseltme yaptığınızda olarak. İki farklı yükseltme seçenekleri şunlardır:

- Sunucuya Bağlan'ı yüklemek için yerinde yükseltme.
- Swing geçişi sırasında mevcut Connect'i yeni bir sunucuya yüklemek için Azure AD eşitleme sunucusu hala çalışıyor.

## <a name="migrate-from-fim2010-or-mim2016"></a>Fım2010 veya mım2016 geçirme
Şu anda Forefront Identity Manager 2010 veya Microsoft Identity Manager 2016 ile Azure AD bağlayıcısını kullanıyorsanız, tek seçeneğiniz bir geçiş olduğunu. İçinde açıklanan adımları izleyin [swing geçişi](how-to-upgrade-previous-version.md#swing-migration). Aşağıdaki adımlarda, Azure AD eşitleme'nin herhangi bir Bahsetme fım2010/mım2016 ile değiştirin.

## <a name="next-steps"></a>Sonraki adımlar
Kullanmak için seçtiğiniz seçeneğine bağlı olarak, makale ayrıntılı adımları bulmak için sol taraftaki içindekiler tablosunu kullanın.

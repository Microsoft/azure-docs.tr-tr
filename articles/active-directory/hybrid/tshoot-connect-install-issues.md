---
title: Azure AD Connect yüklemeleri sorunlarını giderme | Microsoft Docs '
description: Bu konuda Azure AD Connect yükleme ile ilgili sorunları gidermeye yönelik adımlar sağlanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e75ad29c5b4a76de5317991995f132c6cb53bbe3
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211849"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Giderilmesine Azure AD Connect yükleme sorunları

## <a name="recommended-steps"></a>**Önerilen Adımlar**
Lütfen hangi [Azure AD Connect yükleme türünün](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) sizin için uygun olduğunu denetleyin. Hızlı yükleme ölçütlerini karşılıyoruz, Hızlı yüklemeye gitmeniz önerilir. Hızlı yükleme, yüklemeyi bitirebilmeniz için gereken en az seçenek sağlar, bu nedenle herhangi bir sorunun daha az olma olasılığı vardır. 

Ancak, hızlı yükleme ölçütlerini karşılamıyorsa ve özel yükleme yapmanız gerekiyorsa, yaygın sorunlardan kaçınmak için izleyebileceğiniz bazı en iyi uygulamalar aşağıda verilmiştir. Kolaylık sağlaması için yalnızca seçmeli seçenekler burada belirtilmiştir:

* AAD Connect 'i yüklemekte olduğunuz makinede yönetici olduğunuzdan emin olun. Aynı yönetici kimlik bilgileriyle makinede oturum açın.

* Mevcut SQL Server kullanmak istiyorsanız, "var olan bir SQL Server kullan" dışında tüm seçeneklerin varsayılan olmasına izin verin. Özel yükleme seçeneklerini kullanma hakkında [daha fazla ayrıntı](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom) aşağıda verilmiştir. 

    ![Mevcut SQL Server kullan](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* Aşağıdaki sayfada, mevcut hesapla ilgili herhangi bir izin sorununu önlemek için "yeni AD hesabı oluştur" seçeneğini belirleyin.

    ![AD Ormanı hesabı](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Genel Sorunlar**

* Şirket [içi Active Directory bağlantı sorunları](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools).

* [Çevrimiçi Azure Active Directory bağlantı sorunları](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity).

* Şirket [içi Active Directory izin sorunları](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account).

## <a name="recommended-documents"></a>**Önerilen Belgeler**
* [Azure AD Connect Önkoşulları](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [Azure AD Connect için hangi yükleme türünün kullanılacağını seçme](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [Hızlı ayarları kullanarak Azure AD Connect ile çalışmaya başlama](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Azure AD Connect özel yüklemesi](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect: Önceki bir sürümden en son sürüme yükseltme](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)
* [Azure AD Connect: Hazırlama sunucusu nedir?](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [ADConnectivityTool PowerShell Modülü nedir?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>Sonraki adımlar
- [Azure AD Connect eşitleme](how-to-connect-sync-whatis.md).
- [Hibrit kimlik nedir?](whatis-hybrid-identity.md)






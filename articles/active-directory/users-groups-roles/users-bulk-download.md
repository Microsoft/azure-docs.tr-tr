---
title: Azure Active Directory portalında Kullanıcı (Önizleme) listesini indirme | Microsoft Docs
description: Kullanıcı kayıtlarını Azure Yönetim Merkezi 'nde Azure Active Directory ' de toplu olarak indirin.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 07/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: d106e803fdef4f054b7e4717ba59a43359d1d68a
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720293"
---
# <a name="download-a-list-of-users-preview-in-azure-active-directory-portal"></a>Azure Active Directory portalında kullanıcıların listesini indirme (Önizleme)

Azure Active Directory (Azure AD) toplu Kullanıcı içeri aktarma (oluşturma) işlemlerini destekler.

## <a name="required-permissions"></a>Gerekli izinler

Azure AD Yönetim merkezinden Kullanıcı listesini indirmek için Azure AD 'de kuruluş düzeyindeki bir veya daha fazla yönetici rolüne atanmış bir kullanıcı ile oturum açmanız gerekir. Konuk davetci ve uygulama geliştiricisi yönetici rolleri olarak değerlendirilmez.

## <a name="to-download-a-list-of-users"></a>Kullanıcı listesini indirmek için

1. Kuruluştaki bir Kullanıcı Yöneticisi hesabıyla [Azure AD kuruluşunuzda oturum açın](https://aad.portal.azure.com) .
2. Azure Active Directory > kullanıcılara gidin. Ardından, her kullanıcının yanındaki sol sütundaki kutuyu seçerek karşıdan yüklemeye dahil etmek istediğiniz kullanıcıları seçin. Note: Şu anda, dışarı aktarma için tüm kullanıcıları seçmenin bir yolu yoktur. Her birinin tek tek seçilmiş olması gerekir.
3. Azure AD 'de kullanıcıları **indirmek** > **Kullanıcılar** ' ı seçin.
4. **Kullanıcıları indir** sayfasında, Kullanıcı profili özelliklerini listelemek üzere bir CSV dosyası almak için **Başlat** ' ı seçin. Hatalar varsa, sonuçlar dosyasını toplu işlem sonuçları sayfasında indirebilir ve görüntüleyebilirsiniz. Dosya her hatanın nedenini içerir.

   ![Listeden indirmek istediğiniz kullanıcıları seçin](./media/users-bulk-download/bulk-download.png)

   İndirme dosyası, filtrelenmiş Kullanıcı listesini içerecektir.

   Aşağıdaki Kullanıcı öznitelikleri dahil edilecek: 

   - userPrincipalName
   - displayName
   - Soyadı
   - -
   - givenName
   - Uzantının
   - userType
   - jobTitle
   - bölüme
   - Manager
   - accountEnabled
   - usageLocation
   - streetAddress
   - durum
   - ülke
   - physicalDeliveryOfficeName
   - city
   - postalCode
   - telephoneNumber 'dır
   - Mo
   - authenticationPhoneNumber
   - authenticationAlternativePhoneNumber
   - authenticationEmail
   - Alternateemaadresi
   - Yaş
   - consentProvidedForMinor
   - Ligalagegroupclassification

## <a name="check-status"></a>Durumu kontrol etme

Bekleyen toplu isteklerinizin durumunu **toplu işlem sonuçları (Önizleme)** sayfasında görebilirsiniz.

   ![Toplu Işlemler sonuçları sayfasında karşıya yükleme durumunu kontrol edin](./media/users-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Hizmet sınırlarını toplu indirme

Kullanıcıların bir listesini oluşturmak için her toplu etkinlik, bir saate kadar çalışabilir. Bu, en az 500.000 kullanıcı listesinin oluşturulmasını ve indirilmesini mümkün bir şekilde sunar.

## <a name="next-steps"></a>Sonraki adımlar

- [Toplu Kullanıcı ekleme](users-bulk-add.md)
- [Kullanıcıları toplu silme](users-bulk-delete.md)
- [Kullanıcıları toplu geri yükleme](users-bulk-restore.md)

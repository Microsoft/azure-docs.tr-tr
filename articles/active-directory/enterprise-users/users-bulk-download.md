---
title: Azure Active Directory portalındaki kullanıcıların bir listesini indirin | Microsoft Docs
description: Kullanıcı kayıtlarını Azure Yönetim Merkezi 'nde Azure Active Directory ' de toplu olarak indirin.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 01/04/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57e3a059a5dd846250ba162513ef118e084c4b87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97861597"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Azure Active Directory portalındaki kullanıcıların listesini indirme

Azure Active Directory (Azure AD) toplu Kullanıcı içeri aktarma (oluşturma) işlemlerini destekler.

## <a name="required-permissions"></a>Gerekli izinler

Azure AD yönetim merkezinden kullanıcı listesini indirmek için, Azure AD'de kuruluş düzeyindeki bir veya daha fazla yönetici rolüne (gerekli en düşük rol düzeyi Kullanıcı Yöneticisidir) atanmış bir kullanıcı ile oturum açmanız gerekir. Konuk davet eden ve uygulama geliştiricisi, yönetici rolü kabul edilmez.

## <a name="to-download-a-list-of-users"></a>Kullanıcı listesini indirmek için

1. Kuruluştaki bir Kullanıcı Yöneticisi hesabıyla [Azure AD kuruluşunuzda oturum açın](https://aad.portal.azure.com) .
2. Azure Active Directory > Kullanıcılar'a gidin. Ardından, indirmek istediğiniz her kullanıcıyı sol tarafındaki sütunda bulunan kutuyu işaretleyerek seçin. Not: Şu anda, dışarı aktarmak için tüm kullanıcıları seçebileceğiniz bir yöntem yoktur. Kullanıcıların tek tek seçilmesi gereklidir.
3. Azure AD **'de kullanıcılar**  >  **kullanıcıları indirir**' ı seçin.
4. **Kullanıcıları indir** sayfasında, Kullanıcı profili özelliklerini listelemek üzere bir CSV dosyası almak için **Başlat** ' ı seçin. Hata varsa sonuç dosyasını Toplu işlem sonuçları sayfasından indirip görüntüleyebilirsiniz. Bu dosyada her hatanın nedeni belirtilir.

   ![Listeden indirmek istediğiniz kullanıcıları seçin](./media/users-bulk-download/bulk-download.png)

   İndirilen dosyada kullanıcıların filtrelenmiş listesi bulunur.

   Bu listeye aşağıdaki kullanıcı öznitelikleri dahil edilir:

   - userPrincipalName
   - displayName
   - surname
   - posta
   - givenName
   - objectId
   - userType
   - jobTitle
   - bölüm
   - accountEnabled
   - usageLocation
   - streetAddress
   - state
   - ülke
   - physicalDeliveryOfficeName
   - city
   - postalCode
   - telephoneNumber
   - mobil
   - authenticationAlternativePhoneNumber
   - authenticationEmail
   - alternateEmailAddress
   - ageGroup
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Durumu kontrol etme

Bekleyen toplu isteklerinizin durumunu **toplu işlem sonuçları** sayfasında görebilirsiniz.

[![Toplu Işlemler sonuçları sayfasında durumu kontrol edin.](./media/users-bulk-download/bulk-center.png)](./media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Hizmet sınırlarını toplu indirme

Kullanıcıların bir listesini oluşturmak için her toplu etkinlik, bir saate kadar çalışabilir. Bu, en az 500.000 kullanıcı listesinin oluşturulmasını ve indirilmesini mümkün bir şekilde sunar.

## <a name="next-steps"></a>Sonraki adımlar

- [Toplu Kullanıcı ekleme](users-bulk-add.md)
- [Kullanıcıları toplu silme](users-bulk-delete.md)
- [Kullanıcıları toplu geri yükleme](users-bulk-restore.md)

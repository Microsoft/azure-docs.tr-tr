---
title: Azure AD Yetkilendirme Yönetimi 'nde yaygın senaryolar-Azure Active Directory
description: Azure Active Directory yetkilendirme yönetiminde yaygın senaryolar için izlemeniz gereken üst düzey adımları öğrenin.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/28/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24d2dc82d0e81c9ac374d96387677774dc916b8c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73173491"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Azure AD yetkilendirme yönetiminde yaygın senaryolar

Kuruluşunuz için yetkilendirme yönetimini yapılandırabileceğiniz çeşitli yollar vardır. Ancak, yeni başladıysanız Yöneticiler, Katalog sahipleri, paket yöneticileri, onaylayanlar ve istek sahipleri için yaygın senaryolar anlaşılması yararlı olur.

## <a name="delegate"></a>ğini

### <a name="administrator-delegate-management-of-resources"></a>Yönetici: kaynak yönetimine temsilci seçme

1. [Videoyu izleyin: buradan Departman Yöneticisi 'ne temsilciyi](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [Kullanıcıları Katalog Oluşturucu rolüne devretmek](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>Katalog Oluşturucu: kaynak yönetimine temsilci seçme

- [Yeni Katalog oluşturma](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>Katalog sahibi: kaynak yönetimine temsilci seçme

1. [Kataloğa ikincil sahipler ekleme](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [Kataloğa kaynak ekleme](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>Katalog sahibi: erişim paketleri için temsilci yönetimi

1. [Videoyu izleyin: Katalog sahibinden, paket yöneticisine erişim için temsili](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [Paket Yöneticisi rolüne erişmek için kullanıcıları temsilci seçme](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>Kuruluşunuzdaki kullanıcılar için erişimi yönetir

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>Paket Yöneticisi 'ne erişim: Kuruluşunuzdaki çalışanların kaynaklara erişim istemesine Izin verin

1. [Yeni bir erişim paketi oluşturun](entitlement-management-access-package-create.md#start-new-access-package)
1. [Erişim paketine gruplar, takımlar, uygulamalar veya SharePoint siteleri ekleme](entitlement-management-access-package-create.md#resource-roles)
1. [Dizininizdeki kullanıcıların erişim istemesine izin vermek için bir istek ilkesi ekleyin](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [Süre sonu ayarlarını belirtin](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>İstek sahibi: kaynaklara erişim ISTEME

1. [Erişim Portalı 'nda oturum açın](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Erişim paketi bul
1. [Erişim iste](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Onaylayan: istekleri kaynaklara onaylama

1. [Erişim portalından isteği aç](entitlement-management-request-approve.md#open-request)
1. [Erişim isteğini onaylama veya reddetme](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>İstek sahibi: zaten erişiminizin olduğu kaynakları görüntüleme

1. [Erişim Portalı 'nda oturum açın](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Etkin erişim paketlerini görüntüle

## <a name="govern-access-for-users-outside-your-organization"></a>Kuruluşunuzun dışındaki kullanıcılar için erişimi yönetir

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>Yönetici: dış iş ortağı organizasyonu ile Işbirliği yapma

1. [Dış kullanıcılar için erişimin nasıl çalıştığını okuyun](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [Dış kullanıcıların ayarlarını gözden geçirme](entitlement-management-external-users.md#settings-for-external-users)
1. [Dış kuruluşa bağlantı ekleme](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>Access Package Manager: bir dış iş ortağı kuruluşla Işbirliği yapma

1. [Yeni bir erişim paketi oluşturun](entitlement-management-access-package-create.md#start-new-access-package)
1. [Erişim paketine gruplar, takımlar, uygulamalar veya SharePoint siteleri ekleme](entitlement-management-access-package-resources.md#add-resource-roles)
1. [Dizininizdeki kullanıcıların erişim istemesine izin vermek için bir istek ilkesi ekleyin](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [Süre sonu ayarlarını belirtin](entitlement-management-access-package-create.md#lifecycle)
1. [Erişim paketi istemek için bağlantıyı kopyalayın](entitlement-management-access-package-settings.md)
1. Dış iş ortağı kişi iş ortağınıza ait bağlantıyı, kullanıcılarıyla paylaşmak üzere gönderin

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>İstek sahibi: Dış Kullanıcı olarak kaynaklara erişim ISTEME

1. Kişinizden aldığınız erişim paketi bağlantısını bulun
1. [Erişim Portalı 'nda oturum açın](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [Erişim iste](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Onaylayan: istekleri kaynaklara onaylama

1. [Erişim portalından isteği aç](entitlement-management-request-approve.md#open-request)
1. [Erişim isteğini onaylama veya reddetme](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>İstek sahibi: zaten erişimi olan kaynakları görüntüleme

1. [Erişim Portalı 'nda oturum açın](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Etkin erişim paketlerini görüntüle

## <a name="day-to-day-management"></a>Günlük yönetimi

### <a name="access-package-manager-update-the-resources-for-a-project"></a>Paket Yöneticisi 'ne erişim: bir projenin kaynaklarını güncelleştirme

1. [Videoyu izleyin: günlük yönetim: öğeler değişti](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Erişim paketini açın
1. [Grupları, takımları, uygulamaları veya SharePoint sitelerini ekleme veya kaldırma](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>Paket Yöneticisi 'ne erişim: bir projenin süresini güncelleştirme

1. [Videoyu izleyin: günlük yönetim: öğeler değişti](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Erişim paketini açın
1. [Yaşam döngüsü ayarlarını açın](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [Süre sonu ayarlarını Güncelleştir](entitlement-management-access-package-lifecycle-policy.md#lifecycle)

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>Paket Yöneticisi 'ne erişim: bir proje için erişimin nasıl onaylandığını güncelleştirme

1. [Videoyu izleyin: günlük yönetim: öğeler değişti](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [İstek ve onay ayarlarının mevcut bir ilkesini açın](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [Onay ayarlarını Güncelleştir](entitlement-management-access-package-request-policy.md#approval)

### <a name="access-package-manager-update-the-people-for-a-project"></a>Paket Yöneticisi 'ne erişim: bir proje için kişileri güncelleştirme

1. [Videoyu izleyin: günlük yönetim: öğeler değişti](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Artık erişmesi gerekmeyen kullanıcıları kaldırma](entitlement-management-access-package-assignments.md)
1. [İstek ve onay ayarlarının mevcut bir ilkesini açın](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [Erişmesi gereken kullanıcıları ekleyin](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>Access Package Manager: belirli kullanıcıları doğrudan bir erişim paketine ata

1. [Kullanıcıların farklı yaşam döngüsü ayarlarına ihtiyacı varsa, erişim paketine yeni bir ilke ekleyin](entitlement-management-access-package-request-policy.md#add-a-new-policy-of-request-and-approval-settings)
1. [Erişim paketine doğrudan belirli kullanıcıları ata](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>Atamalar ve raporlar

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>Yönetici: bir erişim paketine kimlerin atamalarını olduğunu görüntüleme

1. Bir erişim paketi açın
1. [Atamaları görüntüle](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)

### <a name="administrator-view-resources-assigned-to-users"></a>Yönetici: kullanıcılara atanan kaynakları görüntüleme

1. [Bir kullanıcı için erişim paketlerini görüntüleme](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [Bir kullanıcı için kaynak atamalarını görüntüleme](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="next-steps"></a>Sonraki adımlar

- [Temsili ve roller](entitlement-management-delegate.md)
- [İşlem ve e-posta bildirimleri iste](entitlement-management-process.md)

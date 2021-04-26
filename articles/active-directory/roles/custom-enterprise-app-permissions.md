---
title: Azure Active Directory | özel roller için uygulama izinleri | Microsoft Docs
description: Azure portal, PowerShell veya Graph API özel Azure AD rolleri için kurumsal uygulama izinlerini önizleyin.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: d2159540c688a63082efb792fd4d261062ef65d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103466802"
---
# <a name="enterprise-application-permissions-for-custom-roles-in-azure-active-directory"></a>Azure Active Directory özel roller için kurumsal uygulama izinleri

Bu makale, Azure Active Directory (Azure AD) içindeki özel rol tanımları için şu anda kullanılabilir olan kurumsal uygulama izinlerini içerir. Bu makalede, bazı yaygın senaryolar için izin listelerini ve kurumsal uygulama izinlerinin tam listesini bulacaksınız. Uygulama proxy 'Si izinleri şu anda bu sürümde kullanıma alınmadı.

## <a name="required-license-plan"></a>Gerekli lisans planı

Bu özelliğin kullanılması için Azure AD kuruluşunuz için Azure AD Premium P1 lisansı gerekir. Gereksinimlerinize uygun lisansı bulmak için bkz. [Ücretsiz, Temel ve Premium sürümlerinin genel olarak sağlanan özelliklerini karşılaştırma](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="enterprise-application-permissions"></a>Kurumsal uygulama izinleri

Bu izinlerin nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [Kurumsal uygulamaları yönetmek için özel roller atama](custom-enterprise-apps.md)

### <a name="assigning-users-or-groups-to-an-application"></a>Bir uygulamaya Kullanıcı veya Grup atama

SAML tabanlı çoklu oturum açma uygulamalarına erişebilen kullanıcı ve grupların atamasını devretmek için. Gerekli izinler

- Microsoft. Directory/Servicesorumlularını/appRoleAssignedTo/Update

### <a name="creating-gallery-applications"></a>Galeri uygulamaları oluşturma

ServiceNow, F5, Salesforce gibi Azure AD Galeri uygulamalarının oluşturulmasını diğerleri arasında devretmek. Gerekli izinler:

- Microsoft. Directory/applicationTemplates/örnek oluştur

### <a name="configuring-basic-saml-urls"></a>Temel SAML URL 'Lerini yapılandırma

SAML tabanlı çoklu oturum açma uygulamaları için güncelleştirmeyi devretmek ve temel SAML yapılandırmalarının okunması için. Gerekli izinler:

- Microsoft. Directory/Servicesorumlularını/kimlik doğrulaması/güncelleştirme
- Microsoft. Directory/Applications. Myorganleştirme/kimlik doğrulama/güncelleştirme

### <a name="rolling-over-or-creating-signing-certs"></a>İmzalama sertifikaları alınıyor veya oluşturuluyor

SAML tabanlı çoklu oturum açma uygulamaları için imza sertifikalarının yönetimini devretmek. İzinler gereklidir.

Microsoft. Directory/uygulamalar/kimlik bilgileri/güncelleştirme

### <a name="update-expiring-sign-in-cert-notification-email-address"></a>Güncelleştirme süresi dolan oturum açma sertifikası bildirim e-posta adresi

SAML tabanlı çoklu oturum açma uygulamaları için süresi dolan oturum açma sertifikaları bildirim e-posta adresleri güncelleştirmesini devretmek. Gerekli izinler:

- Microsoft. Directory/Applications. Myorganleştirme/kimlik doğrulama/güncelleştirme
- Microsoft. Directory/Applications. Myorganleştirme/izinler/güncelleştirme
- Microsoft. Directory/Servicesorumlularını/kimlik doğrulaması/güncelleştirme
- Microsoft. Directory/Servicesorumlularını/temel/güncelleştirme

### <a name="manage-saml-token-signature-and-sign-in-algorithm"></a>SAML belirteci imzasını ve oturum açma algoritmasını yönetme

SAML belirteci imzası ve SAML tabanlı çoklu oturum açma uygulamaları için oturum açma algoritması güncelleştirmesine temsilci seçme. Gerekli izinler:

- Microsoft. Directory/applicationPolicies/Basic/Update
- Microsoft. Directory/uygulamalar/kimlik doğrulaması/güncelleştirme
- Microsoft. Directory/Servicesorumlularını/ilkeleri/güncelleştirme

### <a name="manage-user-attributes-and-claims"></a>Kullanıcı özniteliklerini ve taleplerini yönetme

Kullanıcı özniteliklerinin oluşturma, silme ve güncelleştirme, SAML tabanlı çoklu oturum açma uygulamaları için talepler için temsilci seçme. Gerekli izinler:

- Microsoft. Directory/applicationPolicies/Basic/Update
- Microsoft. Directory/uygulamalar/kimlik doğrulaması/güncelleştirme
- Microsoft. Directory/Servicesorumlularını/ilkeleri/güncelleştirme

## <a name="app-provisioning-permissions"></a>Uygulama sağlama izinleri

İşi, şemayı veya kimlik bilgilerini Kullanıcı arabirimi aracılığıyla yönetme gibi herhangi bir yazma işleminin gerçekleştirilmesi, sağlama sayfasını görüntülemek için de okuma izinleri gerektirir.

Kapsamın tüm kullanıcılar ve gruplar ya da atanan kullanıcılar ve gruplar için ayarlanması şu anda synchronizationJob ve synchronizationCredentials izinlerinin her ikisini de gerektirir.

### <a name="turn-on-or-restart-provisioning-jobs"></a>Sağlama işlerini açma veya yeniden başlatma

Sağlama işlerini açma, kapatma ve yeniden başlatma yeteneğini devretmek. Gerekli izinler:

- Microsoft. Directory/Servicesorumlularını/synchronizationJobs/Manage  

### <a name="configure-the-provisioning-schema"></a>Sağlama şemasını yapılandırma  

Öznitelik eşlemesinde güncelleştirmelerin yetkisini almak için. Gerekli izinler:

- Microsoft. Directory/Servicesorumlularını/synchronizationSchema/Yönet  

### <a name="read-provisioning-settings-associated-with-the-application-object"></a>Uygulama nesnesiyle ilişkili sağlama ayarlarını okuyun

Nesneyle ilişkili sağlama ayarlarını okuma yetkisini sağlamak için. Gerekli izinler:

- Microsoft. Directory/uygulamalar/eşitleme/standart/okuma

### <a name="read-provisioning-settings-associated-with-your-service-principal"></a>Hizmet sorumluyla ilişkili sağlama ayarlarını okuyun

Hizmet sorumlusu ile ilişkili sağlama ayarlarını okumak için temsilci seçme. Gerekli izinler:

- Microsoft. Directory/Servicesorumlularını/eşitleme/standart/okuma

### <a name="authorize-application-access-for-provisioning"></a>Uygulama erişimini sağlama için yetkilendir  

Uygulama erişimini sağlama amacıyla yetkilendirmek için temsilci seçme. Örnek giriş OAuth taşıyıcı belirteci. Gerekli izinler:

- Microsoft. Directory/Servicesorumlularını/synchronizationCredentials/Manage

## <a name="full-list-of-permissions"></a>İzinlerin tam listesi

İzin | Description
---------- | -----------
Microsoft. Directory/applicationPolicies/allProperties/Read | Uygulama ilkelerindeki tüm özellikleri okuyun.
Microsoft. Directory/applicationPolicies/allProperties/Update | Uygulama ilkelerindeki tüm özellikleri güncelleştirin.
Microsoft. Directory/applicationPolicies/Basic/Update | Uygulama ilkelerinin standart özelliklerini güncelleştirin.
Microsoft. Directory/applicationPolicies/Create | Uygulama ilkeleri oluşturun.
Microsoft. Directory/applicationPolicies/createAsOwner | Uygulama ilkeleri oluşturun. Oluşturucu ilk sahip olarak eklenir.
Microsoft. Directory/applicationPolicies/Delete | Uygulama ilkelerini silin.
Microsoft. Directory/applicationPolicies/Owners/Read | Uygulama ilkelerindeki sahipleri okuyun.
Microsoft. Directory/applicationPolicies/Owners/Update | Uygulama ilkelerinin Owner özelliğini güncelleştirin.
Microsoft. Directory/applicationPolicies/policyAppliedTo/Read | Nesneler listesine uygulanan uygulama ilkelerini okuyun.
Microsoft. Directory/applicationPolicies/Standard/Read | Uygulama ilkelerinin standart özelliklerini okuyun.
Microsoft. Directory/Servicesorumlularını/allProperties/allTasks | Servicesorumlularını oluşturup silin ve Azure Active Directory tüm özellikleri okuyun ve güncelleştirin.
Microsoft. Directory/Servicesorumlularını/allProperties/Read | Servicesorumlular üzerindeki tüm özellikleri okuyun.
Microsoft. Directory/Servicesorumlularını/allProperties/Update | Servicesorumluların tüm özelliklerini güncelleştirin.
Microsoft. Directory/Servicesorumlularını/appRoleAssignedTo/Read | Hizmet sorumlusu rol atamalarını okuyun.
Microsoft. Directory/Servicesorumlularını/appRoleAssignedTo/Update | Hizmet sorumlusu rol atamalarını güncelleştirme.
Microsoft. Directory/Servicesorumlularını/Approtaatamalar/okuma | Hizmet sorumlularına atanan rol atamalarını okuyun.
Microsoft. Directory/Servicesorumlularını/hedef kitle/güncelleştirme | Hizmet sorumluları üzerinde hedef kitle özelliklerini güncelleştirin.
Microsoft. Directory/Servicesorumlularını/kimlik doğrulaması/güncelleştirme | Hizmet sorumluları 'nda kimlik doğrulama özelliklerini güncelleştirin.
Microsoft. Directory/Servicesorumlularını/temel/güncelleştirme | Hizmet sorumluları 'ndaki temel özellikleri güncelleştirin.
Microsoft. Directory/Servicesorumlularını/oluşturma | Hizmet sorumluları oluşturun.
Microsoft. Directory/Servicesorumlularını/createAsOwner | Hizmet sorumluları oluşturun. Oluşturucu ilk sahip olarak eklenir.
Microsoft. Directory/Servicesorumlularını/kimlik bilgileri/güncelleştirme | Hizmet sorumluları 'nda kimlik bilgileri özelliklerini güncelleştirin.
Microsoft. Directory/Servicesorumlularını/silme | Hizmet sorumlularını silin.
Microsoft. Directory/Servicesorumlularını/Disable | Hizmet sorumlularını devre dışı bırakın.
Microsoft. Directory/Servicesorumlularını/etkinleştir | Hizmet sorumlularını etkinleştirin.
Microsoft. Directory/Servicesorumlularını/getPasswordSingleSignOnCredentials | Hizmet sorumluları 'ndaki parola çoklu oturum açma kimlik bilgilerini okuyun.
Microsoft. Directory/Servicesorumlularını/managePasswordSingleSignOnCredentials | Hizmet sorumluları 'nda parola çoklu oturum açma kimlik bilgilerini yönetin.
Microsoft. Directory/Servicesorumlularını/oAuth2PermissionGrants/Read | Hizmet sorumluları üzerinde temsilci izni iznini okuyun.
Microsoft. Directory/Servicesorumlularını/Owners/Read | Hizmet sorumluları 'ndaki sahipleri okuyun.
Microsoft. Directory/Servicesorumlularını/Owners/Update | Hizmet sorumluları 'ndaki sahipleri güncelleştirme.
Microsoft. Directory/Servicesorumlularını/izinleri/güncelleştirme |  
Microsoft. Directory/Servicesorumlularını/ilkeleri/okuma | Hizmet sorumluları 'ndaki ilkeleri okuyun.
Microsoft. Directory/Servicesorumlularını/ilkeleri/güncelleştirme | Hizmet sorumluları 'nda ilkeleri güncelleştirin.
Microsoft. Directory/Servicesorumlularını/standart/okuma | Hizmet sorumluları 'nın standart özelliklerini okuyun.
Microsoft. Directory/Servicesorumlularını/eşitleme/standart/okuma | Hizmet sorumlusuyla ilişkili sağlama ayarlarını okuyun.
Microsoft. Directory/Servicesorumlularını/etiketi/güncelleştirme | Hizmet sorumluları 'ndaki Etiketleri Güncelleştir özelliği.
Microsoft. Directory/applicationTemplates/örnek oluştur | Uygulama şablonlarından Galeri uygulamaları oluşturun.
Microsoft. Directory/auditLogs/allProperties/Read | Denetim günlüklerini okuyun.
Microsoft. Directory/Signınreports/allProperties/Read | Oturum açma raporlarını okuyun.
Microsoft. Directory/uygulamalar/eşitleme/standart/okuma | Uygulama nesnesiyle ilişkili sağlama ayarlarını okuyun.
Microsoft. Directory/Servicesorumlularını/synchronizationJobs/Manage | Hizmet sorumlusu kaynakları için iş eşitlemesinin tüm yönlerini yönetin
Microsoft. Directory/Servicesorumlularını/eşitleme/standart/okuma | Hizmet sorumluları ile ilişkili sağlama ayarlarını okuyun
Microsoft. Directory/Servicesorumlularını/synchronizationSchema/Yönet | Hizmet sorumlusu kaynakları için şema eşitlemesinin tüm yönlerini yönetin
Microsoft. Directory/provisioningLogs/allProperties/Read | Sağlama günlüklerinin tüm özelliklerini okuyun

## <a name="next-steps"></a>Sonraki adımlar

- [Azure portal, Azure AD PowerShell ve Graph API kullanarak özel roller oluşturun](custom-create.md)
- [Rol atamalarını listeleme](view-assignments.md)

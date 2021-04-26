---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/05/2021
ms.author: mimart
ms.openlocfilehash: 5a2382146cd8b85b8eef54b924a206dda7107b0f
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382478"
---
#### <a name="app-registrations"></a>[Uygulama kayıtları](#tab/app-reg-ga/) 

1. **Yönet** altında **API izinleri**' ni seçin.
1. **Yapılandırılan izinler** altında **izin Ekle**' yi seçin.
1. **Microsoft API 'leri** sekmesini seçin ve ardından **Microsoft Graph**' yi seçin.
1. **Uygulama izinleri**' ni seçin.
1. Uygun izin grubunu genişletin ve yönetim uygulamanıza verilecek iznin onay kutusunu seçin. Örnek:
    * **Kullanıcı**  >  **User. ReadWrite. All**: Kullanıcı geçişi veya Kullanıcı yönetimi senaryoları için.
    * **Grup**  >  **Group. ReadWrite. All**: gruplar oluşturmak, grup üyeliklerini okumak ve güncelleştirmek ve grupları silmek için.
    * **AuditLog**  >  **Denetimgünlüğü. Read. All**: dizinin denetim günlüklerini okumak için.
    * **İlke**  >  **Policy. ReadWrite. TrustFramework**: sürekli tümleştirme/sürekli teslım (CI/CD) senaryoları için. Örneğin, Azure Pipelines ile özel ilke dağıtımı.
1. **Izin Ekle**' yi seçin. Yönlendirildiğinden, bir sonraki adıma geçmeden önce birkaç dakika bekleyin.
1. **Yönetici onayı ver ' i (kiracı adınız)** seçin.
1. Şu anda genel yönetici hesabı ile oturum açmadıysanız, Azure AD B2C kiracınızdaki bir hesapla oturum açarak en az *bulut uygulama Yöneticisi* rolüne atanır ve ardından **yönetici onayı ver (kiracı adınız)** seçeneğini belirleyin.
1. **Yenile**' yi seçin ve ardından "verilen..." öğesini doğrulayın **durum**' un altında görüntülenir. İzinlerin yayılması birkaç dakika sürebilir.

#### <a name="applications-legacy"></a>[Uygulamalar (eski)](#tab/applications-legacy/)

1. **Kayıtlı uygulamaya** Genel Bakış sayfasında, **Ayarlar**' ı seçin.
1. **API erişimi** altında **gerekli izinler**' i seçin.
1. **Microsoft Graph** seçin.
1. **Uygulama izinleri** altında, yönetim uygulamanıza verilecek iznin onay kutusunu seçin. Örnek:
    * **Tüm denetim günlüğü verilerini okuyun**: dizinin denetim günlüklerini okumak için bu izni seçin.
    * **Dizin verilerini okuma ve yazma**: Kullanıcı geçişi veya Kullanıcı yönetimi senaryoları için bu izni seçin.
    * **Kuruluşunuzun güven çerçevesi Ilkelerini okuyun ve yazın**: sürekli tümleştirme/sürekli teslım (CI/CD) senaryoları için bu izni seçin. Örneğin, Azure Pipelines ile özel ilke dağıtımı.
1. **Kaydet**’i seçin.
1. **Izin ver**' i seçin ve ardından **Evet**' i seçin. İzinlerin tam olarak yayılması birkaç dakika sürebilir.

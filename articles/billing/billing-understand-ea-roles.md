---
title: Azure’da Kuruluş için yönetici rollerini anlama | Microsoft Docs
description: Azure’da Kuruluş yöneticisi rolleri hakkında bilgi edinin.
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2018
ms.author: banders
ms.openlocfilehash: 39526defb8f41ddacb0a26d7ad852f820ca6ea77
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70034536"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Azure'daki Azure Kurumsal Anlaşma yönetici rollerini anlama

Kuruluşunuzun kullanım ve harcamasının yönetilmesine yardımcı olmak için Kurumsal Anlaşması (EA) olan Azure müşterileri beş farklı yönetici rolü atayabilir:

- Kuruluş Yöneticisi
- Kuruluş Yöneticisi (salt okunur)
- Bölüm Yöneticisi
- Bölüm Yöneticisi (salt okunur)
- Hesap Sahibi
 
Bu roller, Azure Kurumsal Anlaşmaları yönetmeye özgüdür ve kaynaklara erişimi denetlemeye yönelik Azure’da yer alan yerleşik rollere ek olarak sunulmuştur. Daha fazla bilgi için bkz. [Azure kaynakları için yerleşik roller](../role-based-access-control/built-in-roles.md).

Aşağıdaki bölümlerde her bir rolün sınırlamaları ve özellikleri açıklanmaktadır.

## <a name="user-limit-for-admin-roles"></a>Yönetici rolleri için kullanıcı sınırı

|Rol| Kullanıcı sınırı|
|---|---|
|Kuruluş Yöneticisi|Sınırsız|
|Kuruluş Yöneticisi (salt okunur)|Sınırsız|
|Bölüm Yöneticisi|Sınırsız|
|Bölüm Yöneticisi (salt okunur)|Sınırsız|
|Hesap Sahibi|Hesap başına 1<sup>1</sup>|

<sup>1</sup> Her hesap için benzersiz bir Microsoft hesabı veya iş ya da okul hesabı gerekir.

## <a name="organization-structure-and-permissions-by-role"></a>Role göre kuruluş yapısı ve izinleri

|Görevler| Kuruluş Yöneticisi|Kuruluş Yöneticisi (salt okunur)|Bölüm Yöneticisi|Bölüm Yöneticisi (salt okunur)|Hesap Sahibi|
|---|---|---|---|---|---|
|Kuruluş Yöneticilerini görüntüleme|✔|✔|✘|✘|✘|
|Kuruluş Yöneticileri ekleme veya kaldırma|✔|✘|✘|✘|✘|
|Bildirim İlgili Kişilerini görüntüleme<sup>2</sup> |✔|✔|✘|✘|✘|
|Bildirim İlgili Kişileri ekleme veya kaldırma<sup>2</sup> |✔|✘|✘|✘|✘|
|Bölümler oluşturma ve yönetme |✔|✘|✘|✘|✘|
|Bölüm Yöneticilerini görüntüleme|✔|✔|✔|✔|✘|
|Bölüm Yöneticileri ekleme veya kaldırma|✔|✘|✔|✘|✘|
|Kayıttaki Hesapları görüntüleme |✔|✔|✔<sup>3</sup>|✔<sup>3</sup>|✘|
|Kayda Hesaplar ekleme ve Hesap Sahibini değiştirme|✔|✘|✔<sup>3</sup>|✘|✘|
|Abonelikler ve abonelik izinleri oluşturma ve yönetme|✘|✘|✘|✘|✔|

- <sup>2</sup> Bildirim ilgili kişilerine Azure Kurumsal Anlaşma ile ilgili e-posta iletişimleri gönderilir.
- <sup>3</sup> Görev, bölümünüzdeki hesaplarla sınırlıdır.


## <a name="usage-and-costs-access-by-role"></a>Role göre kullanım ve maliyet erişimi

|Görevler| Kuruluş Yöneticisi|Kuruluş Yöneticisi (salt okunur)|Bölüm Yöneticisi|Bölüm Yöneticisi (salt okunur) |Hesap Sahibi|
|---|---|---|---|---|---|
|Parasal taahhüt dahil kredi bakiyesini görüntüleme|✔|✔|✘|✘|✘|
|Bölüm harcaması kotalarını görüntüleme|✔|✔|✘|✘|✘|
|Bölüm harcaması kotalarını ayarlama|✔|✘|✘|✘|✘|
|Kuruluşun Kurumsal Anlaşma fiyat listesini görüntüleme|✔|✔|✘|✘|✘|
|Kullanım ve maliyet ayrıntılarını görüntüleme|✔|✔|✔<sup>4</sup>|✔<sup>4</sup>|✔<sup>5</sup>|
|Azure portalında kaynakları yönetme|✘|✘|✘|✘|✔|

- <sup>4</sup> Kuruluş Yöneticisinin Enterprise portal’da **DA ücretleri görüntüleme** ilkesini etkinleştirmesini gerektirir. Bölüm Yöneticisi daha sonra bölüme ait maliyet ayrıntılarını görebilir.
- <sup>5</sup> Kuruluş Yöneticisinin Enterprise portal’da **AO ücretleri görüntüleme** ilkesini etkinleştirmesini gerektirir. Hesap Sahibi daha sonra hesaba ait maliyet ayrıntılarını görebilir.


## <a name="pricing-in-azure-portal"></a>Azure portalında fiyatlandırma

Yönetici rolünüze ve Kuruluş Yöneticisi tarafından ücretleri görüntüleme ilkelerinin nasıl ayarlandığına bağlı olarak Azure portalında farklı fiyatlandırma görebilirsiniz. Azure portalında gördüğünüz fiyatlandırmayı etkileyen Enterprise Portal’daki iki ilke şunlardır:

- DA ücretleri görüntüleme
- AO ücretleri görüntüleme

Bu ilkelerin nasıl ayarlanacağı hakkında bilgi edinmek için bkz. [Azure için faturalama bilgilerine erişimi yönetme](billing-manage-access.md).

Aşağıdaki tabloda, Azure portalında gördüğünüz fiyatlandırma, Azure portalındaki rol tabanlı erişim denetimi (RBAC) rolü, ücretleri görüntüleme ilkesi ve Kurumsal Anlaşma yönetici rolleri arasındaki ilişki gösterilmektedir. Kuruluş Yöneticisi her zaman kuruluşun Kurumsal Anlaşma fiyatlandırmasına göre kullanım ayrıntılarını görür. Ancak Bölüm Yöneticisi ve Hesap Sahibi, ücreti görüntüleme ilkesine ve RBAC rolüne göre farklı fiyatlandırma görünümleri görür. Aşağıdaki tabloda listelenen Bölüm Yöneticisi rolü hem Bölüm Yöneticisi hem de Bölüm Yöneticisi (salt okunur) rolünü ifade eder.

|Kurumsal Anlaşma yönetici rolü|Rol için ücretleri görüntüleme ilkesi|RBAC rolü|Fiyatlandırma görünümü|
|---|---|---|---|
|Hesap Sahibi VEYA Bölüm Yöneticisi|✔ Etkin|Sahip|Kuruluşun Kurumsal Anlaşma fiyatlandırması|
|Hesap Sahibi VEYA Bölüm Yöneticisi|✘ Devre dışı|Sahip|Perakende fiyatlandırması|
|Hesap Sahibi VEYA Bölüm Yöneticisi|✔ Etkin |yok|Fiyatlandırma yok|
|Hesap Sahibi VEYA Bölüm Yöneticisi|✘ Devre dışı |yok|Fiyatlandırma yok|
|None|Uygulanamaz |Sahip|Perakende fiyatlandırması|

Kuruluş yöneticisi rolünü ve ücretleri görüntüleme ilkelerini Enterprise portal’da ayarlarsınız. RBAC rolü, Azure portalında güncelleştirilebilir. Daha fazla bilgi için bkz. [RBAC ve Azure portalını kullanarak erişimi yönetme](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure için faturalama bilgilerine erişimi yönetme](billing-manage-access.md)
- [RBAC ve Azure portalı kullanarak erişimi yönetme](../role-based-access-control/role-assignments-portal.md)
- [Azure kaynakları için yerleşik roller](../role-based-access-control/built-in-roles.md)

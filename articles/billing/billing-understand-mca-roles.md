---
title: Microsoft Müşteri Sözleşmeleri için faturalama yönetimi rollerini anlama - Azure
description: Azure'da Microsoft Müşteri Sözleşmeleri için ödeme hesaplarının faturalama rollerini öğrenin.
author: amberbhargava
manager: amberbhargava
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 8b4aeaa885b18f433c366788af350a7fe9c064e4
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709476"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Azure’daki Microsoft Müşteri Sözleşmesi yönetici rollerini anlama

Microsoft Müşteri Sözleşmesi ödeme hesabını yönetmek için aşağıdaki bölümlerde açıklanan rolleri kullanın. Bu roller kaynaklara erişimi denetlemek için Azure'daki yerleşik rollere eklenmiştir. Daha fazla bilgi için bkz. [Azure kaynakları için yerleşik roller](../role-based-access-control/built-in-roles.md).

Bu makale, Microsoft Müşteri Sözleşmesi’ne ilişkin ödeme hesabı için geçerlidir. Microsoft Müşteri Sözleşmesi’ne erişiminiz olup olmadığını denetleyin.

## <a name="billing-role-definitions"></a>Faturalama rolü tanımları

Aşağıdaki tabloda ödeme hesabınızı, faturalama profillerinizi ve fatura bölümlerinizi yönetirken kullandığınız faturalama rolleri açıklanır.

|Rol|Açıklama|
|---|---|
|Faturalama hesabı sahibi|Ödeme hesabı için her şeyi yönetme|
|Faturalama hesabı katkıda bulunanı|Faturalandırma hesabındaki izinler dışında her şeyi yönetme|
|Faturalama hesabı okuyucusu|Faturalandırma hesabındaki her şeyin salt okunur görünümü|
|Faturalama profili sahibi|Faturalama profili için her şeyi yönetme|
|Faturalama profili katkıda bulunanı|Faturalandırma profilindeki izinler dışında her şeyi yönetme|
|Faturalama profili okuyucusu|Faturalandırma profilindeki her şeyin salt okunur görünümü|
|Fatura yöneticisi|Faturalandırma profili için faturaları görüntüleme ve ödeme|
|Fatura bölümü sahibi|Fatura bölümündeki her şeyi yönetme|
|Fatura bölümü katkıda bulunanı|Fatura bölümündeki izinler dışında her şeyi yönetme|
|Fatura bölümü okuyucusu|Fatura bölümündeki her şeyin salt okunur görünümü|
|Azure aboneliği oluşturucusu|Azure abonelikleri oluşturma|

## <a name="billing-account-roles-and-tasks"></a>Ödeme hesabı rolleri ve görevleri

Ödeme hesabı kuruluşunuz için faturalamayı yönetmenize olanak tanır. Ödeme hesabını kullanarak maliyetleri düzenleyebilir, ücretleri ve faturaları izleyebilir, kuruluşunuzda faturalama erişimini denetleyebilirsiniz. Daha fazla bilgi için bkz. [Ödeme hesabını anlama](billing-mca-overview.md#your-billing-account).

Aşağıdaki tablolarda ödeme hesabı bağlamında görevleri tamamlamak için ihtiyacınız olan roller gösterilir.

### <a name="manage-billing-account-permissions-and-properties"></a>Ödeme hesabı izinlerini ve özelliklerini yönetme

|Görev|Faturalama hesabı sahibi|Faturalama hesabı katkıda bulunanı|Faturalama hesabı okuyucusu|
|---|---|---|---|
|Ödeme hesabı için mevcut izinleri görüntüleme|✔|✔|✔|
|Diğer kullanıcılara ödeme hesabını görüntüleme ve yönetme izinleri verme|✔|✘|✘|
|Şirket adı ve adresi gibi ödeme hesabı özelliklerini görüntüleme|✔|✔|✔|

### <a name="manage-billing-profiles-for-billing-account"></a>Ödeme hesabı için faturalama profillerini yönetme

|Görev|Faturalama hesabı sahibi|Faturalama hesabı katkıda bulunanı|Faturalama hesabı okuyucusu|
|---|---|---|---|
|Hesaptaki tüm faturalama profillerini görüntüleme|✔|✔|✔|

### <a name="manage-invoices-for-billing-account"></a>Ödeme hesabı için faturaları yönetme

|Görev|Faturalama hesabı sahibi|Faturalama hesabı katkıda bulunanı|Faturalama hesabı okuyucusu|
|---|---|---|---|
|Hesaptaki tüm faturaları görüntüleme|✔|✔|✔|
|Hesaptaki faturaları, Azure kullanımı ve ücretleri dosyalarını, fiyat listelerini ve vergi belgelerini indirme|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-account"></a>Ödeme hesabı için fatura bölümlerini yönetme

|Görev|Faturalama hesabı sahibi|Faturalama hesabı katkıda bulunanı|Faturalama hesabı okuyucusu|
|---|---|---|---|
|Hesaptaki tüm fatura bölümlerini görüntüleme|✔|✔|✔|

### <a name="manage-transactions-for-billing-account"></a>Ödeme hesabı için işlemleri yönetme

|Görev|Faturalama hesabı sahibi|Faturalama hesabı katkıda bulunanı|Faturalama hesabı okuyucusu|
|---|---|---|---|
|Hesap için tüm faturalama işlemlerini görüntüleme|✔|✔|✔|
|Hesap için satın alınan tüm ürünleri görüntüleme|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-account"></a>Ödeme hesabı için abonelikleri yönetme

|Görev|Faturalama hesabı sahibi|Faturalama hesabı katkıda bulunanı|Faturalama hesabı okuyucusu|
|---|---|---|---|
|Ödeme hesabındaki tüm Azure aboneliklerini görüntüleme|✔|✔|✔|

## <a name="billing-profile-roles-and-tasks"></a>Faturalama profili rolleri ve görevleri

Faturalama profili faturalarınızı ve ödeme yöntemlerinizi yönetmenize olanak tanır. Azure abonelikleri ve faturalama profili kullanılarak satın alınan diğer ürünler için aylık bir fatura oluşturulur. Faturayı ödemek için ödeme yöntemlerini kullanırsınız. Daha fazla bilgi için bkz. [Faturalama profillerini anlama](billing-mca-overview.md#billing-profiles).

Aşağıdaki tablolarda faturalama profili bağlamında görevleri tamamlamak için ihtiyacınız olan roller gösterilir.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Faturalama profili izinlerini, özelliklerini ve ilkelerini yönetme

|Görev|Faturalama profili sahibi|Faturalama profili katkıda bulunanı|Faturalama profili okuyucusu|Fatura Yöneticisi|Faturalama hesabı sahibi|Faturalama hesabı katkıda bulunanı|Faturalama hesabı okuyucusu
|---|---|---|---|---|---|---|---|
|Faturalama profili için mevcut izinleri görüntüleme|✔|✔|✔|✔|✔|✔|✔|
|Diğer kullanıcılara faturalama profilini görüntüleme ve yönetme izinleri verme|✔|✘|✘|✘|✘|✘|✘|
|PO numarası ve e-postayla fatura tercihi gibi faturalama profili özelliklerini görüntüleme|✔|✔|✔|✔|✔|✔|✔|
|Faturalama profili özelliklerini güncelleştirme |✔|✔|✘|✘|✘|✘|✘|
|Azure rezervasyon satın almalarını etkinleştir ve Azure market satın almalarını etkinleştir gibi faturalama profiline uygulanan ilkeleri görüntüleme|✔|✔|✔|✔|✔|✔|✔|
|Faturalama profiline ilkeleri uygulama |✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-invoices-for-billing-profile"></a>Faturalama profili için faturaları yönetme

|Görev|Faturalama profili sahibi|Faturalama profili katkıda bulunanı|Faturalama profili okuyucusu|Fatura Yöneticisi|Faturalama hesabı sahibi|Faturalama hesabı katkıda bulunanı|Faturalama hesabı okuyucusu
|---|---|---|---|---|---|---|---|
|Faturalama profilinin tüm faturalarını görüntüleme|✔|✔|✔|✔|✔|✔|✔|
|Faturalama profili için faturaları, Azure kullanımı ve ücretleri dosyalarını, fiyat listelerini ve vergi belgelerini indirme|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Faturalama profili için fatura bölümlerini yönetme

|Görev|Faturalama profili sahibi|Faturalama profili katkıda bulunanı|Faturalama profili okuyucusu|Fatura Yöneticisi|Faturalama hesabı sahibi|Faturalama hesabı katkıda bulunanı|Faturalama hesabı okuyucusu
|---|---|---|---|---|---|---|---|
|Faturalama profili için tüm fatura bölümlerini görüntüleme|✔|✔|✔|✔|✔|✔|✔|
|Faturalama profili için yeni fatura bölümü oluşturma|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-transactions-for-billing-profile"></a>Faturalama profili için işlemleri yönetme

|Görev|Faturalama profili sahibi|Faturalama profili katkıda bulunanı|Faturalama profili okuyucusu|Fatura Yöneticisi|Faturalama hesabı sahibi|Faturalama hesabı katkıda bulunanı|Faturalama hesabı okuyucusu
|---|---|---|---|---|---|---|---|
|Faturalama profili için tüm faturalama işlemlerini görüntüleme|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-payment-methods-for-billing-profile"></a>Faturalama profili için ödeme yöntemlerini yönetme

|Görev|Faturalama profili sahibi|Faturalama profili katkıda bulunanı|Faturalama profili okuyucusu|Fatura Yöneticisi|Faturalama hesabı sahibi|Faturalama hesabı katkıda bulunanı|Faturalama hesabı okuyucusu
|---|---|---|---|---|---|---|---|
|Faturalama profili için ödeme yöntemlerini görüntüleme|✔|✔|✔|✔|✔|✔|✔|
|Faturalandırma profili için Azure kredi bakiyesini izleme|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Faturalama profili için abonelikleri yönetme

|Görev|Faturalama profili sahibi|Faturalama profili katkıda bulunanı|Faturalama profili okuyucusu|Fatura Yöneticisi|Faturalama hesabı sahibi|Faturalama hesabı katkıda bulunanı|Faturalama hesabı okuyucusu
|---|---|---|---|---|---|---|---|
|Faturalama profili için tüm Azure aboneliklerini görüntüleme|✔|✔|✔|✔|✔|✔|✔|

## <a name="invoice-section-roles-and-tasks"></a>Fatura bölümü rolleri ve görevleri

Fatura bölümü faturanızdaki maliyetleri düzenlemenize olanak tanır. Maliyetlerinizi bölüme, geliştirme ortamına göre veya kuruluşunuzun ihtiyaçları temelinde düzenlemek için bölümler oluşturabilirsiniz. Diğer kullanıcılara Azure abonelikleri oluşturma izni verebilirsiniz. Bundan sonra aboneliklere yönelik tüm kullanım ücretleri ve satın alma işlemleri faturanın bölümünde gösterilir. Daha fazla bilgi için bkz. [Fatura bölümünü anlama](billing-mca-overview.md#invoice-sections).

Aşağıdaki tablolarda fatura bölümleri bağlamında görevleri tamamlamak için ihtiyacınız olan roller gösterilir.

### <a name="manage-invoice-section-permissions-and-properties"></a>Fatura bölümü izinlerini ve özelliklerini yönetme

|Görevler|Fatura bölümü sahibi|Fatura bölümü katkıda bulunanı|Fatura bölümü okuyucusu|Azure aboneliği oluşturucusu|Faturalama hesabı sahibi|Faturalama hesabı katkıda bulunanı|Faturalama hesabı okuyucusu | |
|---|---|---|---|---|---|---|---|---|
|Fatura bölümü üzerindeki tüm izinleri görüntüleme|✔|✔|✔|✔|✔|✔|✔| |
|Diğer kullanıcılara fatura bölümünü görüntüleme ve yönetme izinleri verme|✔|✘|✘|✘|✘|✘|✘| |
|Fatura bölümü özelliklerini görüntüleme|✔|✔|✔|✔|✔|✔|✔| |
|Fatura bölümü özelliklerini güncelleştirme|✔|✔|✘|✘|✘|✘|✘|✘|

### <a name="manage-products-for-invoice-section"></a>Fatura bölümü için ürünleri yönetme

|Görevler|Fatura bölümü sahibi|Fatura bölümü katkıda bulunanı|Fatura bölümü okuyucusu|Azure aboneliği oluşturucusu|Faturalama hesabı sahibi|Faturalama hesabı katkıda bulunanı|Faturalama hesabı okuyucusu
|---|---|---|---|---|---|---|---|
|Fatura bölümünde satın alınan tüm ürünleri görüntüleme|✔|✔|✔|✘|✔|✔|✔|
|Fatura bölümü için ürünlerin faturalanmasını yönetme; örneğin iptal ve otomatik yenilemeyi kapatma gibi işlemler yapma|✔|✔|✘|✘|✘|✘|✘|
|Ürünler için fatura bölümünü değiştirme|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>Fatura bölümü için abonelikleri yönetme

|Görevler|Fatura bölümü sahibi|Fatura bölümü katkıda bulunanı|Fatura bölümü okuyucusu|Azure aboneliği oluşturucusu|Faturalama hesabı sahibi|Faturalama hesabı katkıda bulunanı|Faturalama hesabı okuyucusu
|---|---|---|---|---|---|---|---|
|Fatura bölümü için tüm Azure aboneliklerini görüntüleme|✔|✔|✔|✘|✔|✔|✔|
|Abonelikler için fatura bölümünü değiştirme|✔|✔|✘|✘|✘|✘|✘|
|Diğer ödeme hesaplarındaki kullanıcılardan aboneliklerin fatura sahipliğini isteme|✔|✔|✘|✘|✘|✘|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Abonelik faturalama rolleri ve görevleri

Aşağıdaki tabloda abonelik bağlamında görevleri tamamlamak için ihtiyacınız olan roller gösterilir.

|Görevler|Fatura bölümü sahibi|Fatura bölümü katkıda bulunanı|Fatura bölümü okuyucusu|Azure aboneliği oluşturucusu|
|---|---|---|---|---|
|Azure abonelikleri oluşturma|✔|✔|✘|✔|
|Abonelik için maliyet merkezini güncelleştirme|✔|✔|✘|✘|
|Abonelik için fatura bölümünü değiştirme|✔|✔|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>Azure portalında yerleşik rolleri yönetme

1. [Azure Portal](https://portal.azure.com) oturum açın.

2. **Maliyet Yönetimi + Faturalama** araması yapın.

   ![Azure portalı aramasını gösteren ekran görüntüsü](./media/billing-understand-mca-roles/billing-search-cost-management-billing.png)

3. Erişim vermek istediğiniz ödeme hesabı, faturalama profili veya fatura bölümü gibi bir kapsamda **Erişim denetimi (IAM)** öğesini seçin.

4. Erişim denetimi (IAM) sayfasında söz konusu kapsam için her role atanan kullanıcılar ve gruplar listelenir.

   ![Ödeme hesabı için yöneticilerin listesini gösteren ekran görüntüsü](./media/billing-understand-mca-roles/billing-list-admins.png)

5. Kullanıcıya erişim vermek için sayfanın en üstündeki **Ekle**'yi seçin. Rol açılan listesinde rolü seçin. Erişim izni vermek istediğiniz kullanıcının e-posta adresini girin. Rolü atamak için **Kaydet**’i seçin.

   ![Ödeme hesabına yönetici ekleme işlemini gösteren ekran görüntüsü](./media/billing-understand-mca-roles/billing-add-admin.png)

6. Kullanıcının erişimini kaldırmak için, kaldırmak istediğiniz rol atamasına sahip kullanıcıyı seçin. Kaldır’ı seçin.

   ![Ödeme hesabından yönetici kaldırma işlemini gösteren ekran görüntüsü](./media/billing-understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft Müşteri Sözleşmesi’ne erişimi denetleme
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Yardıma mı ihtiyacınız var? Desteğe başvurun
Yardıma ihtiyacınız varsa sorununuzun hızla çözülmesini sağlamak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Sonraki adımlar

Ödeme hesabınız hakkında bilgi edinmek için aşağıdaki makalelere bakın:

- [Microsoft Müşteri Sözleşmesi için ödeme hesabınızı kullanmaya başlama](billing-mca-overview.md)
- [Microsoft Müşteri Sözleşmesi için ödeme hesabınıza yönelik bir Azure aboneliği oluşturma](billing-mca-create-subscription.md)

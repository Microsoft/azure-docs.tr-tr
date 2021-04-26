---
title: Azure’da Kurumsal Anlaşmalar (EA) için yönetici rollerini anlama
description: Azure’da Kuruluş yöneticisi rolleri hakkında bilgi edinin. Beş ayrı yönetim rolü atayabilirsiniz.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 7331f9a894d36ee15702a8fe53804efd53049762
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284128"
---
# <a name="managing-azure-enterprise-agreement-roles"></a>Azure Kurumsal Anlaşma rollerini yönetme

Kuruluşunuzun kullanımı ve harcamalarının yönetilmesine yardımcı olmak için Kurumsal Anlaşma olan Azure müşterileri altı ayrı yönetim rolü atayabilir:

- Kuruluş Yöneticisi
- Kuruluş Yöneticisi (salt okunur)<sup>1</sup>
- EA Satınalmacı
- Bölüm Yöneticisi
- Bölüm Yöneticisi (salt okunur)
- Hesap sahibi<sup>2</sup>

<sup>1</sup> EA sözleşmesinin Fatura ilgili kişisi bu rolde olacaktır.

<sup>2</sup> Fatura ilgili kişisi Azure EA portalına eklenemez veya burada değiştirilemez. Bu kişi, anlaşma düzeyinde fatura ilgili kişisi olarak ayarlanmış kullanıcıya göre EA kaydına eklenir. Fatura ilgili kişisini değiştirmek için Bölgesel İşlem Merkezi’nin (ROC) iş ortağı/yazılım danışmanı aracılığıyla istek oluşturmanız gerekir.

Kayıt sağlama sırasında ayarlanan ilk kayıt yöneticisi, Fatura ilgili kişisi hesabının kimlik doğrulaması türünü belirler. Fatura ilgili kişisi EA Portal’a salt okunur bir yönetici olarak eklendiğinde, bu kişiye Microsoft hesabı kimlik doğrulaması verilir. 

Örneğin ilk kimlik doğrulaması türü Karma olarak ayarlandıysa, EA bir Microsoft hesabı olarak eklenir ve Fatura ilgili kişisi salt okunur EA yönetici ayrıcalıklarına sahip olur. EA yöneticisi mevcut Fatura ilgili kişisi için Microsoft hesabı yetkilendirmesini onaylamazsa, EA yöneticisi söz konusu kullanıcıyı silebilir ve müşteriden kullanıcıyı EA portalında yalnızca kayıt düzeyinde ayarlanan bir İş veya Okul hesabıyla salt okunur yönetici olarak yeniden eklemesini isteyebilir.

Bu roller, Azure Kurumsal Anlaşmaları yönetmeye özgüdür ve kaynaklara erişimi denetlemeye yönelik Azure’da yer alan yerleşik rollere ek olarak sunulmuştur. Daha fazla bilgi için bkz. [Azure yerleşik rolleri](../../role-based-access-control/built-in-roles.md).

## <a name="azure-enterprise-portal-hierarchy"></a>Azure Enterprise Portal hiyerarşisi

Azure Enterprise Portal hiyerarşisi şunlardan oluşur:

- **Azure Enterprise Portal**, Azure EA hizmetlerinin maliyetini yönetmenize yardımcı olan bir çevrimiçi yönetim portalıdır. Şunları yapabilirsiniz:

  - Departmanlar, hesaplar ve abonelikler içeren bir Azure EA hiyerarşisi oluşturma.
  - Tüketilen hizmetlerin maliyetlerini mutabık kılma, kullanım raporlarını indirme ve fiyat listelerini görüntüleme.
  - Kaydınız için API anahtarları oluşturma.

- **Departmanlar**, maliyetleri mantıksal gruplar halinde segmentlere ayırmanıza yardımcı olur. Departmanlar sayesinde departman düzeyinde bütçe veya kota belirleyebilirsiniz.

- **Hesaplar**, Azure Enterprise Portal'daki kuruluş birimleridir. Hesapları kullanarak abonelikleri ve erişimi raporlarını yönetebilirsiniz.

- **Abonelikler**, Azure Enterprise Portal'daki en küçük birimdir. Hizmet yöneticisi tarafından yönetilen Azure hizmetlerine yönelik kapsayıcılardır.

Aşağıdaki diyagramda basit Azure EA hiyerarşileri gösterilmektedir.

![Basit Azure EA hiyerarşileri diyagramı](./media/understand-ea-roles/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Kurumsal kullanıcı rolleri

Aşağıdaki yönetici kullanıcı rolleri, kuruluş kaydınızın bir parçasıdır:

- Kuruluş yöneticisi
- EA Satınalmacı
- Departman yöneticisi
- Hesap sahibi
- Hizmet yöneticisi
- Bildirim ilgili kişisi

Roller, farklı görevleri tamamlamak için iki farklı portalda çalışır. Faturaları ve maliyetleri yönetmek için [Azure Enterprise Portal](https://ea.azure.com)'ı, Azure hizmetlerini yönetmek için ise [Azure portalını](https://portal.azure.com) kullanırsınız.

Kullanıcı rolleri bir kullanıcı hesabıyla ilişkilendirilir. Kullanıcının orijinalliğini doğrulamak için her kullanıcının geçerli bir iş, okul veya Microsoft hesabının olması gerekir. Her hesabın etkin bir şekilde izlenen bir e-posta adresiyle ilişkilendirildiğinden emin olun. Hesap bildirimleri e-posta adresine gönderilir.

Kullanıcıları ayarlarken, kuruluş yöneticisi rolüne birden fazla hesap atayabilirsiniz. Ancak hesap sahibi rolü yalnızca bir hesaba ait olabilir. Ayrıca hem kuruluş yöneticisi hem de hesap sahibi rollerini tek bir hesaba atayabilirsiniz.

### <a name="enterprise-administrator"></a>Kuruluş yöneticisi

Bu role sahip olan kullanıcılar en yüksek erişim düzeyine sahiptir. Bu kullanıcılar şunları yapabilir:

- Hesapları ve hesap sahiplerini yönetme.
- Diğer kuruluş yöneticilerini yönetme.
- Departman yöneticilerini yönetme.
- Bildirim kişilerini yönetme.
- Rezervasyonlar dahil olmak üzere Azure hizmetleri satın alın.
- Tüm hesaplarda kullanımı görüntüleme.
- Tüm hesaplarda faturalandırılmamış ücretleri görüntüleme.
- Kurumsal Anlaşma için uygulanan tüm rezervasyon siparişlerini ve rezervasyonları görüntüleyin ve yönetin.
  - Kuruluş yöneticisi (salt okuma), rezervasyon siparişlerini ve rezervasyonlarını görüntüleyebilir. Bunları yönetemez.

Bir kuruluş kaydında birden çok kuruluş yöneticiniz olabilir. Kurumsal yöneticilere salt okuma erişimi verebilirsiniz. Hepsi departman yöneticisi rolünü devralırlar.

### <a name="ea-purchaser"></a>EA Satınalmacı

Bu role sahip olan kullanıcılar Azure hizmetlerini satın alma izinlerine sahiptir, ancak hesapların yönetilmesine izin verilmez. Bu kullanıcılar şunları yapabilir:

- Rezervasyonlar dahil olmak üzere Azure hizmetleri satın alın.
- Tüm hesaplarda kullanımı görüntüleme.
- Tüm hesaplarda faturalandırılmamış ücretleri görüntüleme.
- Kurumsal Anlaşma için uygulanan tüm rezervasyon siparişlerini ve rezervasyonları görüntüleyin ve yönetin.

EA Satınalmacı rolü şu anda yalnızca SPN tabanlı erişim için etkindir. Rolü bir hizmet sorumlusu adına atamayı öğrenmek için bkz. [Azure Kurumsal Anlaşma hizmet sorumlusu adlarına rol atama](assign-roles-azure-service-principals.md).

### <a name="department-administrator"></a>Departman yöneticisi

Bu role sahip kullanıcılar şunları yapabilir:

- Departmanlar oluşturma ve yönetme.
- Yeni hesap sahipleri oluşturma.
- Yönettikleri departmanların kullanım ayrıntılarını görüntüleme.
- Gerekli izinlere sahip olmaları halinde maliyetleri görüntüleme.

Her kuruluş kaydı için birden çok departman yöneticiniz olabilir.

Yeni bir departman yöneticisi oluşturduğunuzda veya var olan departman yöneticisini düzenlediğinizde salt okuma erişimi verebilirsiniz. Salt okuma seçeneğini **Evet** olarak ayarlayın.

### <a name="account-owner"></a>Hesap sahibi

Bu role sahip kullanıcılar şunları yapabilir:

- Abonelik oluşturma ve yönetme.
- Hizmet yöneticilerini yönetme.
- Aboneliklerin kullanımını görüntüleme.

Her hesap için benzersiz bir iş, okul veya Microsoft hesabı gerekir. Azure Enterprise Portal yönetici rolleri hakkında daha fazla bilgi için bkz. [Azure’daki Azure Kurumsal Anlaşma yönetici rollerini anlama](understand-ea-roles.md).

### <a name="service-administrator"></a>Hizmet yöneticisi

Hizmet yöneticisi rolünün Azure portalındaki hizmetleri yönetme ve kullanıcıları eş yönetici rolüne atama izinleri vardır.

### <a name="notification-contact"></a>Bildirim ilgili kişisi

Bildirim ilgili kişisi kayıtla ilgili kullanım bildirimlerini alır.

Aşağıdaki bölümlerde her bir rolün sınırlamaları ve özellikleri açıklanmaktadır.

## <a name="user-limit-for-admin-roles"></a>Yönetici rolleri için kullanıcı sınırı

|Rol| Kullanıcı sınırı|
|---|---|
|Kuruluş Yöneticisi|Sınırsız|
|Kuruluş Yöneticisi (salt okunur)|Sınırsız|
| Bir SPN 'ye atanan EA Satınalmacı | Sınırsız |
|Bölüm Yöneticisi|Sınırsız|
|Bölüm Yöneticisi (salt okunur)|Sınırsız|
|Hesap Sahibi|Hesap başına 1<sup>3</sup>|

<sup>3</sup> Her hesap için benzersiz bir Microsoft hesabı veya iş ya da okul hesabı gerekir.

## <a name="organization-structure-and-permissions-by-role"></a>Role göre kuruluş yapısı ve izinleri

|Görevler| Kuruluş Yöneticisi|Kuruluş Yöneticisi (salt okunur)| EA Satınalmacı | Bölüm Yöneticisi|Bölüm Yöneticisi (salt okunur)|Hesap Sahibi| İş Ortağı|
|---|---|---|---|---|---|---|---|
|Kuruluş Yöneticilerini görüntüleme|✔|✔| ✔|✘|✘|✘|✔|
|Kuruluş Yöneticileri ekleme veya kaldırma|✔|✘|✘|✘|✘|✘|✘|
|Bildirim İlgili Kişilerini görüntüleme<sup>4</sup> |✔|✔|✔|✘|✘|✘|✔|
|Bildirim İlgili Kişilerini ekleme veya kaldırma<sup>4</sup> |✔|✘|✘|✘|✘|✘|✘|
|Bölümler oluşturma ve yönetme |✔|✘|✘|✘|✘|✘|✘|
|Bölüm Yöneticilerini görüntüleme|✔|✔|✔|✔|✔|✘|✔|
|Bölüm Yöneticileri ekleme veya kaldırma|✔|✘|✘|✔|✘|✘|✘|
|Kayıttaki Hesapları görüntüleme |✔|✔|✔|✔<sup>5</sup>|✔<sup>5</sup>|✘|✔|
|Kayda Hesaplar ekleme ve Hesap Sahibini değiştirme|✔|✘|✘|✔<sup>5</sup>|✘|✘|✘|
|Rezervasyon satın alma|✔|✘|✔|✘|✘|✘|✘|
|Abonelikler ve abonelik izinleri oluşturma ve yönetme|✘|✘|✘|✘|✘|✔|✘|

- <sup>4</sup> Bildirim ilgili kişilerine Azure Kurumsal Anlaşma ile ilgili e-posta iletişimleri gönderilir.
- <sup>5</sup> Görev, bölümünüzdeki hesaplarla sınırlıdır.

## <a name="add-a-new-enterprise-administrator"></a>Yeni bir kuruluş yöneticisi ekleme

Kuruluş yöneticileri bir Azure EA kaydını yönetirken en fazla ayrıcalığa sahip olan kullanıcılardır. İlk Azure EA yöneticisi, EA sözleşmesi ayarlanırken oluşturulmuştur. Ancak, dilediğiniz zaman yeni yöneticiler ekleyebilir veya kaldırabilirsiniz. Yeni yöneticiler yalnızca var olan yöneticiler tarafından eklenir. Ek kuruluş yöneticileri ekleme hakkında daha fazla bilgi için bkz. [Başka bir kuruluş yöneticisi oluşturma](ea-portal-administration.md#create-another-enterprise-administrator). Faturalama profili rolleri ve görevleri hakkında daha fazla bilgi için bkz. [Faturalama profili rolleri ve görevleri](understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-account-owner-state-from-pending-to-active"></a>Beklemede olan hesap sahibi durumunu etkin olarak güncelleştirme

Bir Azure EA kaydına ilk kez yeni Hesap Sahipleri (AO) eklendiğinde durumları _beklemede_ olarak görünür. Yeni bir hesap sahibi etkinleştirme karşılama e-postasını aldığında, hesabını etkinleştirmek için oturum açabilir. Hesabını etkinleştirdiğinde, _beklemede_ olan hesap durumu _etkin_ olarak güncelleştirilir. Hesap sahibinin "Uyarı" iletisini okuması ve **Devam**'ı seçmesi gerekir. Yeni kullanıcılardan Bir Ticari Hesap oluşturmak için ad ve soyadı girmeleri istenebilir. Bu durumda, devam etmek için gerekli bilgileri eklemesi gerekir. Bundan sonra hesap etkinleştirilir.

## <a name="add-a-department-admin"></a>Departman yöneticisi ekleme

Azure EA yöneticisi bir departman oluşturduktan sonra, Azure Kuruluş yöneticisi departman yöneticileri ekleyebilir ve her birini bir departmanla ilişkilendirebilir. Bir departman yöneticisi yeni hesaplar oluşturabilir. Azure EA aboneliklerinin oluşturulabilmesi için yeni hesaplar gereklidir.

Departman yöneticisi ekleme hakkında daha fazla bilgi için bkz. [Azure EA departman yöneticisi oluşturma](ea-portal-administration.md#add-a-department-administrator).

## <a name="usage-and-costs-access-by-role"></a>Role göre kullanım ve maliyet erişimi

|Görevler| Kuruluş Yöneticisi|Kuruluş Yöneticisi (salt okunur)|EA Satınalmacı|Bölüm Yöneticisi|Bölüm Yöneticisi (salt okunur) |Hesap Sahibi| İş Ortağı|
|---|---|---|---|---|---|---|---|
|Azure Ön Ödemesini içeren kredi bakiyesini görüntüleme|✔|✔|✔|✘|✘|✘|✔|
|Bölüm harcaması kotalarını görüntüleme|✔|✔|✔|✘|✘|✘|✔|
|Bölüm harcaması kotalarını ayarlama|✔|✘|✘|✘|✘|✘|✘|
|Kuruluşun Kurumsal Anlaşma fiyat listesini görüntüleme|✔|✔|✔|✘|✘|✘|✔|
|Kullanım ve maliyet ayrıntılarını görüntüleme|✔|✔|✔|✔<sup>6</sup>|✔<sup>6</sup>|✔<sup>7</sup>|✔|
|Azure portalında kaynakları yönetme|✘|✘|✘|✘|✘|✔|✘|

- <sup>6</sup> Enterprise Portal’da **DA ücretleri görüntüleme** ilkesinin Kuruluş Yöneticisi tarafından etkinleştirilmesini gerektirir. Bölüm Yöneticisi daha sonra bölüme ait maliyet ayrıntılarını görebilir.
- <sup>7</sup> Enterprise Portal’da **AO ücretleri görüntüleme** ilkesinin Kuruluş Yöneticisi tarafından etkinleştirilmesini gerektirir. Hesap Sahibi daha sonra hesaba ait maliyet ayrıntılarını görebilir.

## <a name="see-pricing-for-different-user-roles"></a>Farklı kullanıcı rolleri için fiyatlandırmayı görüntüleyin

Yönetici rolünüze ve Kuruluş Yöneticisi tarafından ücretleri görüntüleme ilkelerinin nasıl ayarlandığına bağlı olarak Azure portalında farklı fiyatlandırma görebilirsiniz. Azure portalında gördüğünüz fiyatlandırmayı etkileyen Enterprise Portal’daki iki ilke şunlardır:

- DA ücretleri görüntüleme
- AO ücretleri görüntüleme

Bu ilkelerin nasıl ayarlanacağı hakkında bilgi edinmek için bkz. [Azure için faturalama bilgilerine erişimi yönetme](manage-billing-access.md).

Aşağıdaki tabloda, Azure portalında gördüğünüz fiyatlandırma, Azure portalındaki Azure rolü, ücretleri görüntüleme ilkesi ve Kurumsal Anlaşma yönetici rolleri arasındaki ilişki gösterilmektedir. Kuruluş Yöneticisi her zaman kuruluşun Kurumsal Anlaşma fiyatlandırmasına göre kullanım ayrıntılarını görür. Ancak Bölüm Yöneticisi ve Hesap Sahibi, ücreti görüntüleme ilkesine ve Azure rolüne göre farklı fiyatlandırma görünümleri görür. Aşağıdaki tabloda listelenen Bölüm Yöneticisi rolü hem Bölüm Yöneticisi hem de Bölüm Yöneticisi (salt okunur) rolünü ifade eder.

|Kurumsal Anlaşma yönetici rolü|Rol için ücretleri görüntüleme ilkesi|Azure rolü|Fiyatlandırma görünümü|
|---|---|---|---|
|Hesap Sahibi VEYA Bölüm Yöneticisi|✔ Etkin|Sahip|Kuruluşun Kurumsal Anlaşma fiyatlandırması|
|Hesap Sahibi VEYA Bölüm Yöneticisi|✘ Devre dışı|Sahip|Perakende fiyatlandırması|
|Hesap Sahibi VEYA Bölüm Yöneticisi|✔ Etkin |yok|Fiyatlandırma yok|
|Hesap Sahibi VEYA Bölüm Yöneticisi|✘ Devre dışı |yok|Fiyatlandırma yok|
|Hiçbiri|Uygulanamaz |Sahip|Perakende fiyatlandırması|

Kuruluş yöneticisi rolünü ve ücretleri görüntüleme ilkelerini Enterprise portal’da ayarlarsınız. Azure rolü Azure portalında güncelleştirilebilir. Daha fazla bilgi için bkz. [Azure Portal kullanarak Azure rolleri atama](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure için faturalama bilgilerine erişimi yönetme](manage-billing-access.md)
- [Azure portal kullanarak Azure rolleri atama](../../role-based-access-control/role-assignments-portal.md)
- [Azure yerleşik rolleri](../../role-based-access-control/built-in-roles.md)

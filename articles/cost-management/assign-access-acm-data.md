---
title: Azure maliyet yönetimi verilerine erişim atama
description: Bu makalede, çeşitli erişim kapsamları için Azure maliyet yönetimi verilerine izin atama işlemi gösterilmektedir.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: secdec18
ms.openlocfilehash: ebc56d27b7adc8f1fea9eafabe1b211f3f0ad560
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375122"
---
# <a name="assign-access-to-cost-management-data"></a>Maliyet yönetimi verilerine erişim atama

Azure Kurumsal sözleşmeleri olan kullanıcılar için, Azure portal ve Enterprise (EA) portalında verilen izinlerin bir birleşimi kullanıcının Azure maliyet yönetimi verilerine erişim düzeyini tanımlar. Diğer Azure hesabı türlerine sahip kullanıcılar için, bir kullanıcının maliyet yönetimi verilerine erişim düzeyini tanımlamak, Azure rol tabanlı erişim denetimi kullanılarak daha basittir. Bu makalede, maliyet yönetimi verilerine erişim atama işlemi adım adım açıklanmaktadır. İzin birleşimi atandıktan sonra Kullanıcı, erişim sahibi oldukları kapsamı ve Azure portal seçtikleri kapsama göre maliyet yönetimi 'ndeki verileri görüntüler.

Kullanıcının seçtiği kapsam, veri birleştirme sağlamak ve maliyet bilgilerine erişimi denetlemek için maliyet yönetimi genelinde kullanılır. Kapsamları kullanırken, kullanıcılar bunları çoklu seçmezsiniz. Bunun yerine, alt kapsamların toplanan daha büyük bir kapsamı seçer ve ardından bunları görüntülemek istedikleri öğeye göre filtreler. Bazı kişilerin alt kapsamların bir üst kapsama erişimi olmaması gerektiğinden, veri birleştirme anlamak önemlidir.

Azure rol tabanlı erişim denetimi ile maliyetleri ve ücretleri görüntüleme erişimi atama hakkında bilgi edinmek için [Azure maliyet yönetimi videosu Ile nasıl erişim atayacağınızı](https://www.youtube.com/watch?v=J997ckmwTa8) izleyin.

>[!VIDEO https://www.youtube.com/embed/J997ckmwTa8]

## <a name="cost-management-scopes"></a>Maliyet yönetimi kapsamları

Maliyet yönetimi çeşitli Azure hesap türlerini destekler. Desteklenen hesap türlerinin tam listesini görüntülemek için bkz. [maliyet yönetimi verilerini anlama](understand-cost-mgt-data.md). Hesap türü kullanılabilir kapsamları belirler.

### <a name="azure-ea-subscription-scopes"></a>Azure EA abonelik kapsamları

Azure EA abonelikleriyle ilgili maliyet verilerini görüntülemek için, bir kullanıcının aşağıdaki kapsamlardan bir veya daha fazlasına en azından okuma erişimi olması gerekir.

| **Kapsam** | **Tanımlanma yeri** | **Verileri görüntülemek için gerekli erişim** | **Önkoşul EA ayarı** | **Verileri birleştirir** |
| --- | --- | --- | --- | --- |
| Faturalama hesabı<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Kuruluş Yöneticisi | Hiçbiri | Kurumsal sözleşmedeki tüm abonelikler |
| Bölüm | [https://ea.azure.com](https://ea.azure.com/) | Bölüm Yöneticisi | **Da görüntüleme ücretleri** etkin | Bölüme bağlı olan kayıt hesabına ait olan tüm abonelikler |
| Kayıt hesabı<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | Hesap Sahibi | **Ao görüntüleme ücretleri** etkin | Kayıt hesabındaki tüm abonelikler |
| Yönetim grubu | [https://portal.azure.com](https://portal.azure.com/) | Maliyet Yönetimi Okuyucusu (veya Okuyucu) | **Ao görüntüleme ücretleri** etkin | Yönetim grubu altındaki tüm abonelikler |
| Abonelik | [https://portal.azure.com](https://portal.azure.com/) | Maliyet Yönetimi Okuyucusu (veya Okuyucu) | **Ao görüntüleme ücretleri** etkin | Abonelikteki tüm kaynaklar/kaynak grupları |
| Kaynak grubu | [https://portal.azure.com](https://portal.azure.com/) | Maliyet Yönetimi Okuyucusu (veya Okuyucu) | **Ao görüntüleme ücretleri** etkin | Kaynak grubundaki tüm kaynaklar |

<sup>1</sup> faturalandırma hesabına kurumsal anlaşma veya kayıt olarak da başvurulur.

<sup>2</sup> kayıt hesabı da hesap sahibi olarak adlandırılır.

Aşağıdaki diyagramda, rol ve EA Portal ayarlarıyla maliyet yönetimi kapsamları arasındaki ilişki gösterilmektedir.

![Roller ve EA Portal ayarlarıyla maliyet yönetimi kapsamları arasındaki ilişkiyi gösteren diyagram](./media/assign-access-acm-data/scope-access-relationship-diagram.png)

EA portalında **da görüntüleme ücretleri** devre dışı bırakıldığında, departmanlar ve hesapların maliyetlerini görüntülemeye çalıştığınızda *Kuruluşunuz için maliyetleri devre dışı* bildiren bir ileti görürsünüz.

Benzer şekilde, EA portalında, **Ao görüntüleme ücretleri** devre dışı bırakıldığında, kayıt hesapları, yönetim grupları, abonelikler ve kaynak grupları için maliyetleri görüntülemeye çalıştığınızda *Kuruluşunuz için maliyetleri devre dışı* olarak belirten bir ileti görürsünüz.

## <a name="other-azure-account-scopes"></a>Diğer Azure hesap kapsamları

Diğer Azure abonelikleriyle ilgili maliyet verilerini görüntülemek için, bir kullanıcının aşağıdaki kapsamlardan bir veya daha fazlasına en azından okuma erişimi olması gerekir:

- Azure hesabı
- Yönetim grubu
- Kaynak grubu

Çeşitli kapsamlar, iş ortaklarının müşterileri bir Microsoft Müşteri sözleşmesine ekledikten sonra kullanılabilir. CSP müşterileri, CSP iş ortakları tarafından etkinleştirildiğinde maliyet yönetimi özelliklerini kullanabilir. Daha fazla bilgi için bkz. [iş ortakları Için Azure maliyet yönetimi ile çalışmaya başlama](get-started-partners.md).

## <a name="enable-access-to-costs-in-the-ea-portal"></a>EA portalındaki maliyetlere erişimi etkinleştir

Departman kapsamı, EA portalında da **etkin** olan **da görüntüleme ücretleri** seçeneğini gerektirir. Diğer tüm kapsamlar, EA portalında **etkin** bir şekilde **görüntüleme ücretleri** seçeneği gerektirir.

Bir seçeneği etkinleştirmek için:

1. Kurumsal Yönetici hesabıyla [https://ea.azure.com](https://ea.azure.com) ' de EA portalında oturum açın.
2. Sol bölmedeki **Yönet** ' i seçin.
3. Erişim sağlamak istediğiniz maliyet yönetimi kapsamları için, ücretlendirmeyi ve/veya **Ao görünüm ücretlerini** **görüntüleme ücreti** seçeneğini etkinleştirin.  
    ![ ve AO gösteren 0 kayıt sekmesi ücret görüntüleme seçenekleri @ no__t-1

Görüntüleme ücreti seçenekleri etkinleştirildikten sonra çoğu kapsam, Azure portal rol tabanlı erişim denetimi (RBAC) izin yapılandırması da gerektirir.

## <a name="enterprise-administrator-role"></a>Kuruluş Yöneticisi rolü

Varsayılan olarak, bir kuruluş yöneticisinin faturalandırma hesabına (Kurumsal Anlaşma/kayıt) ve alt kapsamlar olan diğer tüm kapsamlara erişimi vardır. Kuruluş Yöneticisi, diğer kullanıcılar için kapsamlara erişim atar. İş sürekliliği için en iyi uygulama olarak, Kurumsal Yönetici erişimine sahip her zaman iki kullanıcınız olmalıdır. Aşağıdaki bölümlerde, kuruluş yöneticisinin diğer kullanıcılara yönelik kapsamlara erişim atama örnekleri verilmiştir.

## <a name="assign-billing-account-scope-access"></a>Faturalandırma hesabı kapsamına erişim atama

Faturalandırma hesabı kapsamına erişim, EA portalında kurumsal yönetici izni gerektirir. Kuruluş yöneticisinin, tüm EA kaydı veya birden çok kayıt genelinde maliyetleri görüntüleme erişimi vardır. Faturalandırma hesabı kapsamında Azure portal hiçbir işlem yapılması gerekmez.

1. Kurumsal Yönetici hesabıyla [https://ea.azure.com](https://ea.azure.com) ' de EA portalında oturum açın.
2. Sol bölmedeki **Yönet** ' i seçin.
3. **Kayıt** sekmesinde, yönetmek istediğiniz kaydı seçin.  
    ![ EA portalında kaydınız seçin @ no__t-1
4. **+ Yönetici Ekle**' ye tıklayın.
5. Yönetici Ekle kutusunda kimlik doğrulaması türünü seçin ve kullanıcının e-posta adresini yazın.
6. Kullanıcının maliyet ve kullanım verilerine salt okuma erişimi olması gerekiyorsa, **salt okunurdur**' ın altında **Evet**' i seçin.  Aksi takdirde **Hayır**' ı seçin.
7. Hesabı oluşturmak için **Ekle** ' ye tıklayın.  
    @no__t-# no__t-1 Yönetici Ekle kutusunda gösterilen bilgiler

Yeni kullanıcının maliyet yönetimi verilerine erişmesi 30 dakika kadar sürebilir.

### <a name="assign-department-scope-access"></a>Bölüm kapsamı erişimi atama

Departman kapsamına erişim, EA portalında Departman Yöneticisi (DA görüntüleme ücretleri) erişimine ihtiyaç duyar. Departman yöneticisinin, bir departmanla veya birden çok departmana ilişkin maliyetleri ve kullanım verilerini görüntüleme erişimi vardır. Departmanın verileri, departmana bağlı bir kayıt hesabına ait olan tüm abonelikleri içerir. Azure portal herhangi bir eylem gerekmiyor.

1. Kurumsal Yönetici hesabıyla [https://ea.azure.com](https://ea.azure.com) ' de EA portalında oturum açın.
2. Sol bölmedeki **Yönet** ' i seçin.
3. **Kayıt** sekmesinde, yönetmek istediğiniz kaydı seçin.
4. **Departman** sekmesine tıklayın ve ardından **yönetici Ekle**' ye tıklayın.
5. Bölüm Yöneticisi Ekle kutusunda kimlik doğrulaması türünü seçin ve ardından kullanıcının e-posta adresini yazın.
6. Kullanıcının maliyet ve kullanım verilerine salt okuma erişimi olması gerekiyorsa, **salt okunurdur**' ın altında **Evet**' i seçin.  Aksi takdirde **Hayır**' ı seçin.
7. Departman yönetici izni vermek istediğiniz departmanları seçin.
8. Hesabı oluşturmak için **Ekle** ' ye tıklayın.  
    ![ Departman Yöneticisi Ekle kutusuna gereken bilgileri girin @ no__t-1

## <a name="assign-enrollment-account-scope-access"></a>Kayıt hesabı kapsamına erişim atama

Kayıt hesabı kapsamına erişim, EA portalında hesap sahibi (AO görüntüleme ücretleri) erişimi gerektirir. Hesap sahibi, bu kayıt hesabından oluşturulan aboneliklerle ilişkili maliyetleri ve kullanım verilerini görüntüleyebilir. Azure portal herhangi bir eylem gerekmiyor.

1. Kurumsal Yönetici hesabıyla [https://ea.azure.com](https://ea.azure.com) ' de EA portalında oturum açın.
2. Sol bölmedeki **Yönet** ' i seçin.
3. **Kayıt** sekmesinde, yönetmek istediğiniz kaydı seçin.
4. **Hesap** sekmesine tıklayın ve ardından **Hesap Ekle**' ye tıklayın.
5. Hesap Ekle kutusunda, hesabı ilişkilendirilecek **departmanı** seçin veya atanmamış olarak bırakın.
6. Kimlik doğrulama türünü seçin ve hesap adını yazın.
7. Kullanıcının e-posta adresini yazın ve isteğe bağlı olarak maliyet merkezini yazın.
8. Hesabı oluşturmak için **Ekle** ' ye tıklayın.  
    ![ kayıt hesabının hesap ekle kutusuna gerekli bilgileri girin @ no__t-1

Yukarıdaki adımları tamamladıktan sonra, Kullanıcı hesabı kurumsal portalda bir kayıt hesabı haline gelir ve abonelikler oluşturabilir. Kullanıcı, oluşturdukları abonelikler için maliyet ve kullanım verilerine erişebilir.

## <a name="assign-management-group-scope-access"></a>Yönetim grubu kapsamına erişim atama

Yönetim grubu kapsamını görüntüleme erişimi için en azından maliyet yönetimi okuyucusu (veya okuyucu) izni gerekir. Azure portal bir yönetim grubu için izinleri yapılandırabilirsiniz. Diğer kullanıcıların erişimini etkinleştirmek için yönetim grubu için en azından Kullanıcı erişimi Yöneticisi (veya sahibi) izninizin olması gerekir. Ayrıca, Azure EA hesaplarında de EA portalındaki **Ao görünüm ücretleri** ayarını etkinleştirmiş olmanız gerekir.

1. [https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.
2. Yan çubukta **tüm hizmetler** ' i seçin, _Yönetim grupları_' nı arayın ve **Yönetim grupları**' nı seçin.
3. Hiyerarşide yönetim grubunu seçin.
4. Yönetim grubunuzun adının yanındaki **Ayrıntılar**' a tıklayın.
5. Sol bölmeden **Access Control (IAM)** seçeneğini belirleyin.
6. **Ekle**'ye tıklayın.
7. **Rol**altında **maliyet yönetimi okuyucusu**' nu seçin.
8. **Erişim ata**' nın altında **Azure AD Kullanıcı, Grup veya uygulama**' yı seçin.
9. Erişim atamak için, kullanıcıyı arayın ve ardından seçin.
10. **Kaydet** düğmesine tıklayın.  
    ![ bir yönetim grubu için izin Ekle kutusunda (örneğin, @ no__t-1)

## <a name="assign-subscription-scope-access"></a>Abonelik kapsamı erişimi atama

Abonelik erişimi için en az maliyet yönetimi okuyucusu (veya okuyucu) izni gerekir. Azure portal bir abonelik için izinleri yapılandırabilirsiniz. Diğer kullanıcıların erişimini etkinleştirmek için abonelik için en azından Kullanıcı erişimi Yöneticisi (veya sahibi) izninizin olması gerekir. Ayrıca, Azure EA hesaplarında de EA portalındaki **Ao görünüm ücretleri** ayarını etkinleştirmiş olmanız gerekir.

1. [https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.
2. Yan çubukta **tüm hizmetler** ' i seçin, _abonelikler_' i arayın ve ardından **abonelikler**' i seçin.
3. Aboneliğinizi seçin.
4. Sol bölmeden **Access Control (IAM)** seçeneğini belirleyin.
5. **Ekle**'ye tıklayın.
6. **Rol**altında **maliyet yönetimi okuyucusu**' nu seçin.
7. **Erişim ata**' nın altında **Azure AD Kullanıcı, Grup veya uygulama**' yı seçin.
8. Erişim atamak için, kullanıcıyı arayın ve ardından seçin.
9. **Kaydet** düğmesine tıklayın.

## <a name="assign-resource-group-scope-access"></a>Kaynak grubu kapsam erişimini ata

Bir kaynak grubuna erişim için en azından maliyet yönetimi okuyucusu (veya okuyucu) izni gerekir. Azure portal bir kaynak grubuna yönelik izinleri yapılandırabilirsiniz. Diğer kullanıcıların erişimini etkinleştirmek için kaynak grubu için en azından Kullanıcı erişimi Yöneticisi (veya sahibi) izninizin olması gerekir. Ayrıca, Azure EA hesaplarında de EA portalındaki **Ao görünüm ücretleri** ayarını etkinleştirmiş olmanız gerekir.

1. [https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.
2. Yan çubukta **tüm hizmetler** ' i seçin, _kaynak grupları_' nı arayın ve **kaynak grupları**' nı seçin.
3. Kaynak grubunuzu seçin.
4. Sol bölmeden **Access Control (IAM)** seçeneğini belirleyin.
5. **Ekle**'ye tıklayın.
6. **Rol**altında **maliyet yönetimi okuyucusu**' nu seçin.
7. **Erişim ata**' nın altında **Azure AD Kullanıcı, Grup veya uygulama**' yı seçin.
8. Erişim atamak için, kullanıcıyı arayın ve ardından seçin.
9. **Kaydet** düğmesine tıklayın.

## <a name="cross-tenant-authentication-issues"></a>Çapraz Kiracı kimlik doğrulama sorunları

Şu anda Azure maliyet yönetimi, çapraz Kiracı kimlik doğrulaması için sınırlı desteğe sahiptir. Kiracılar genelinde kimlik doğrulamaya çalıştığınızda bazı durumlarda, maliyet analizinde **erişim reddedildi** hatası alabilirsiniz. Bu sorun, rol tabanlı erişim denetimi 'ni (RBAC) başka bir kiracının aboneliğine yapılandırırsanız ve maliyet verilerini görüntülemeye çalışırsanız meydana gelebilir.

*Sorunu geçici olarak çözmek için*: çapraz kiracı RBAC yapılandırdıktan sonra bir saat bekleyin. Ardından, maliyet analizinde maliyetleri görüntülemeyi deneyin veya her iki Kiracıdaki kullanıcılara maliyet yönetimi erişimi verin.  


## <a name="next-steps"></a>Sonraki adımlar

- Maliyet yönetimi için ilk hızlı başlangıcı Henüz tamamlamadıysanız, [maliyetleri çözümlemeye başlamak](quick-acm-cost-analysis.md)için bunu okuyun.

---
title: Artık kaynak erişimi olmayan dış kullanıcıları gözden geçirmek ve kaldırmak için Azure AD Identity Governance kullanın
description: İş ortağı kuruluşların üyelerinden erişimi kaldır erişimini genişletmek için erişim Incelemelerini kullanın
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/06/2020
ms.author: ajburnle
ms.openlocfilehash: c976562224d4a0caca8921e46d8f8566800027ee
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532233"
---
# <a name="use-azure-active-directory-azure-ad-identity-governance-to-review-and-remove-external-users-who-no-longer-have-resource-access"></a>Artık kaynak erişimi olmayan dış kullanıcıları gözden geçirmek ve kaldırmak için Azure Active Directory (Azure AD) Identity Idare kullanın

Bu makalede, daha fazla gerekmiyorsa bunları gözden geçirebilmeniz ve Azure AD 'den kaldırabilmeniz için dış kimlikleri belirlemenize ve seçmenizi sağlayan özellikler ve yöntemler açıklanmaktadır. Bulut, iç veya dış kullanıcılarla işbirliği yapmayı her zamankinden daha kolay hale getirir. Office 365 ' i benimseme, kuruluşlar dış kimliklerin (konuklar dahil) tek bir şekilde çalışmaya başlar. kullanıcılar, veriler, belgeler veya takımlar gibi dijital çalışma alanlarında birlikte çalışır. Kuruluşların iş birliği ve toplantı güvenliği ve idare gereksinimlerini dengelenmesi, etkinleştirmesi gerekir. Bu çabaların bir bölümü, kiracınızda işbirliği için davet edilmiş, iş ortağı kuruluşlarından kaynaklanan ve artık gerekli olmadığında Azure AD 'nizden kaldıran dış kullanıcıların değerlendirilmesi ve temizlenmesi dahil edilmelidir.

>[!NOTE]
>Azure AD erişim gözden geçirmeleri kullanmak için geçerli bir Azure AD Premium P2, Enterprise Mobility + Security E5 ücretli veya deneme lisansı gerekir. Daha fazla bilgi için bkz. [Azure Active Directory sürümleri](../fundamentals/active-directory-whatis.md).

## <a name="why-review-users-from-external-organizations-in-your-tenant"></a>Kiracınızdaki dış kuruluşların kullanıcıları neden gözden geçirmeliyim?

Çoğu kuruluşta, son kullanıcılar işbirliği için iş ortakları ve satıcıları davet etme işlemini başlatır. Kaynak sahipleri ve son kullanıcılara, dış kullanıcıları düzenli olarak değerlendirmek ve test etmek için bir yol sağlamak üzere sürücü kuruluşlarının işbirliği yapması gerekir. Genellikle yeni işbirliği iş ortakları ekleme işlemi için planlanmış ve muhasebesi yapılmış, ancak birçok iş ortağı açık bir bitiş tarihine sahip olmasa da, bir Kullanıcı artık erişime gerek kalmadığında her zaman açık değildir. Ayrıca, kimlik yaşam döngüsü yönetimi, kuruluşların, artık kuruluşun kaynaklarına erişmesi gerekmeyen kullanıcıları Azure AD temizlemeyi ve kaldırmasını sağlar. Dizindeki iş ortakları ve satıcılar için yalnızca ilgili kimlik başvurularının tutulması, çalışanlarınız riskini azaltmaya, yanlışlıkla kaldırılması gereken dış kullanıcılara erişim izni vermeye yardımcı olur. Bu belge, dış kimlikleri yönetmek için önerilen proaktif önerilerden, yeniden etkinleştirme ve Temizleme etkinliklerine kadar çeşitli seçenekler sunar.

## <a name="use-entitlement-management-to-grant-and-revoke-access"></a>Erişim verme ve iptal etme için yetkilendirme yönetimini kullanma

Yetkilendirme Yönetimi özellikleri, dış kimliklerin kaynaklarına erişimi olan [Otomatik yaşam döngüsünü](entitlement-management-external-users.md#manage-the-lifecycle-of-external-users) etkinleştirir. Yetkilendirme Yönetimi aracılığıyla erişimi yönetmeye yönelik işlemler ve yordamlar kurarak, erişim paketleri aracılığıyla kaynakları yayımlama, dış Kullanıcı erişimini izlemek için çok daha az karmaşık bir sorun haline gelir. Azure AD 'de [Yetkilendirme Yönetimi erişim paketleri](entitlement-management-overview.md) aracılığıyla erişimi yönetirken, kuruluşunuz kullanıcılarınıza erişimi, ayrıca iş ortağı kuruluşlarından de farklı bir şekilde tanımlayabilir ve yönetebilir. Yetkilendirme Yönetimi, dış kullanıcılara istenen ve atanan erişimi izlemek üzere erişim paketlerinin onaylarını ve atamalarını kullanır. Bir dış Kullanıcı tüm atamalarını kaybedersin, Yetkilendirme Yönetimi bu dış kullanıcıları kiracının otomatik olarak kaldırabilir. 

## <a name="find-guests-not-invited-through-entitlement-management"></a>Yetkilendirme Yönetimi ile davet edilen konukları bulma

Çalışanların dış kullanıcılarla işbirliği yapmasına yetki verildiklerinde, kuruluşunuz dışından istedikleri sayıda kullanıcı davet edebilir. Şirket tarafından hizalanmış ve çok fazla farklı şirket olabileceği ya da kuruluş için bir sahip veya sponsor olmadığı için, dış iş ortakları arayıp bunları gözden geçirmek uygun olmayabilir. Microsoft, bir Kiracıdaki dış kimliklerin kullanımını çözümlemenize yardımcı olabilecek bir örnek PowerShell betiği sağlar. Betik dış kimlikleri numaralandırır ve kategorilere ayırır. Betik, artık gerekli olmayan dış kimlikleri belirleyip temizleyebilmeniz için size yardımcı olabilir. Betiğin çıkışının bir parçası olarak, betik örneği, daha fazla analiz ve Azure AD erişim gözden geçirmeleri ile kullanılması için, tanımlanan grup-daha az dış iş ortaklarını içeren otomatik güvenlik gruplarının oluşturulmasını destekler.
Betik [GitHub](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse)' da kullanılabilir. Betiğin çalışması bittikten sonra, dış kimlikleri özetleyen bir HTML Çıkış dosyası oluşturur:

- Kiracıda artık grup üyeliği yok
- Kiracıda ayrıcalıklı rol ataması yapın
- Kiracıdaki bir uygulamaya atama

Çıktı Ayrıca, bu dış kimliklerin her biri için ayrı etki alanlarını içerir. 

>[!NOTE]
>Yukarıda başvurulan betik, Azure AD 'de grup üyeliği, rol atamaları ve uygulama atamalarını denetleyen örnek bir betiktir. Dış kullanıcıların SharePoint (doğrudan üyelik ataması) veya Azure RBAC veya Azure DevOps gibi Azure AD dışında aldığı uygulamalarda başka atamalar olabilir.

## <a name="review-resources-used-by-external-identities"></a>Dış kimlikler tarafından kullanılan kaynakları gözden geçirme

Ekipler veya henüz Yetkilendirme Yönetimi kapsamında yönetilmeyen diğer uygulamalar gibi kaynakları kullanan dış kimlikleriniz varsa, bu kaynaklara erişimi de düzenli olarak gözden geçirmek isteyebilirsiniz. Azure AD [erişim gözden geçirmeleri](create-access-review.md) , kaynak sahibine, dış kimliklere veya güvendiğiniz başka bir kişiye, gerekli BT erişiminin gerekli olup olmadığına bakılmaksızın, dış kimliklerin erişimini gözden geçirebilme olanağı sağlar. Erişim gözden geçirmeleri bir kaynağı hedefleyin ve yalnızca kaynağa veya Konuk kullanıcılara erişebilen herkese kapsamlı bir gözden geçirme etkinliği oluşturur. Gözden geçiren bu durumda, kuruluşunuzun çalışanları veya yalnızca dış kimlikleriniz dahil olmak üzere tüm kullanıcılar tarafından incelenmeleri gereken kullanıcıların sonuç listesini görür.

![erişimi gözden geçirmek için bir grup kullanma](media/access-reviews-external-users/group-members.png)

Kaynak sahibi odaklı İnceleme kültürü oluşturmak, dış kimliklere erişimi yönetmenize yardımcı olur. Kaynak sahipleri, erişim için muhasebeci tablosu ve sahip oldukları bilgilerin güvenliği, çoğu durumda, en iyi hedef kitlelerinizi, kaynaklarına erişim ve bunlara göre erişen kullanıcılara daha yakın veya çok sayıda dışlar yöneten bir sponsor hale getirebilmenizi sağlar.

## <a name="create-access-reviews-for-external-identities"></a>Dış kimlikler için erişim Incelemeleri oluşturma

Kiracınızdaki herhangi bir kaynağa erişimi olmayan kullanıcılar artık kuruluşunuzla çalışmadıklarında kaldırılabilir. Bu dış kimlikleri engellediğinizden ve silmeden önce, bu dış kullanıcılara ulaşmak ve bir projeyi daha fazla denetlediğinizden ya da hala ihtiyaç duydukları erişimleri olduğundan emin olmak isteyebilirsiniz. Bulduğunuz Üyeler kiracınızdaki herhangi bir kaynağa erişimi olmadığından, tüm dış kimlikleri içeren bir grup oluşturduğunuzda, erişim gözden geçirmelerini kullanarak, hala erişime sahip olup olmadığına veya daha sonra yine de erişime ihtiyaç duyup duymlarına sahip olan tüm dışlar self-test ' i kullanabilirsiniz. İncelemesinin bir parçası olarak, erişim Incelemelerdeki gözden geçirme Oluşturucu, dış kullanıcıların hala kiracınızda ne kadar erişim ihtiyacı olduğunu öğrenirken, devam eden erişim için bir gerekçe sağlamasını gerektirmek için onay için bir **neden iste** işlevi kullanabilir. Ayrıca, **Gözden geçiren Için ek içeriği** Ayarla özelliği, kullanıcılara yanıt vermediklerinde erişimi kaybeteceğimizi ve yine de erişimleri olması gerektiğini bilmesini sağlamak için bir gerekçe gerekli hale getirebilirsiniz. Devam etmek ve erişim Incelemelerinin dış kimlikleri **devre dışı bırakıp silmesini** sağlamak istiyorsanız, yanıt vermesi veya devam etmek için geçerli bir neden sağlanması gerekir, sonraki bölümde açıklandığı gibi devre dışı bırak ve Sil seçeneğini kullanabilirsiniz.

![incelemenin kapsamını yalnızca Konuk kullanıcılarla sınırlandırma](media/access-reviews-external-users/guest-users-only.png)

Gözden geçirme tamamlandığında, **sonuçlar** sayfası her dış kimlik tarafından verilen yanıta genel bir bakış gösterir. Sonuçları otomatik olarak uygulamayı ve erişim Incelemelerinin devre dışı bırakıp silmesine izin vermek için bu seçeneği belirleyebilirsiniz. Alternatif olarak, verilen yanıtlara bakabilir ve bir kullanıcının erişimini kaldırmak ya da bir kararı vermeden önce daha fazla bilgi almak isteyip istemediğinize karar verebilirsiniz. Henüz gözden geçirmemiş olduğunuz kaynaklara hala bazı kullanıcılar erişebilseniz, bulma işlemini bulmanın bir parçası olarak kullanabilir ve bir sonraki İnceleme ve kanıtlama döngünüzü zenginleştirebilirsiniz.

## <a name="disable-and-delete-external-identities-with-azure-ad-access-reviews"></a>Azure AD erişim gözden geçirmeleri ile dış kimlikleri devre dışı bırakma ve silme

Azure AD erişim gözden geçirmeleri, gruplar veya uygulamalar gibi kaynaklardan gelen istenmeyen dış kimlikleri kaldırma seçeneğine ek olarak, dış kimliklerin kiracınızda oturum açmasını engelleyebilir ve 30 gün sonra kiracınızdaki dış kimlikleri silebilir. **30 gün boyunca Kullanıcı oturumu açmayı engelle seçeneğini belirledikten sonra kullanıcıyı kiracının içinden kaldırmanız** durumunda, gözden geçirme 30 gün boyunca "uygulanıyor" durumunda kalır. Bu süre boyunca, geçerli gözden geçirme altındaki ayarlar, sonuçlar, gözden geçirenler veya denetim günlükleri görüntülenebilir veya yapılandırılabilir olmayacaktır. 

![tamamlama ayarlarından sonra](media/access-reviews-external-users/upon-completion-settings.png)

Yeni erişim gözden geçirmesi oluştururken, "Tamamlama Ayarları" bölümünde, **Reddedilen kullanıcılara uygulanacak eylem** için, **Kullanıcıların 30 gün boyunca oturum açmasını tanımlayabilir ve ardından kullanıcıyı kiracıya çıkarabilirsiniz**.

Bu ayar, Azure AD kiracınızdan dış kimlikleri tanımlamanızı, engellemeyi ve silmenizi sağlar. Gözden geçiren tarafından devam eden ve reddedilen dış kimlikler, sahip oldukları kaynak erişimi veya grup üyeliğinden bağımsız olarak engellenir ve silinir. Bu ayar en iyi şekilde, gözden geçirme içindeki dış kullanıcıların artık kaynak erişimi taşımadığını ve kiracınızdan güvenli bir şekilde kaldırılaabileceğini doğruladıktan sonra ya da bulundukları erişimleri ne olursa olsun, kaldırıldıklarından emin olmak istiyorsanız son bir adım olarak kullanılır. "Devre dışı bırak ve Sil" özelliği, ilk olarak dış kullanıcıyı engeller, kiracınızda oturum açma ve kaynaklara erişme imkanlarını ele alırlar. Bu aşamada kaynak erişimi iptal edilmez ve dış Kullanıcı örneğini yeniden başlatmak istediğinizde, oturum açma özelliği yeniden yapılandırılabilir. Başka bir işlem yapmadan, engellenen bir dış kimlik 30 gün sonra dizinden silinir, ayrıca hesap kaldırılır ve erişimleri kaldırılır.

## <a name="next-steps"></a>Sonraki adımlar

- [Erişim gözden geçirmeleri - Graph API](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true)
- [Yetkilendirme yönetimi - Graph API](/graph/api/resources/entitlementmanagement-root)

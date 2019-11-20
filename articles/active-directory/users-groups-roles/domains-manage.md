---
title: Özel etki alanı adlarını ekleme ve doğrulama-Azure Active Directory | Microsoft Docs
description: Azure Active Directory bir etki alanı adını yönetmek için yönetim kavramları ve nasıl yapılır?
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 750b49e149907f204b8b15f0b5728ab25f917743
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844519"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Azure Active Directory özel etki alanı adlarını yönetme

Etki alanı adı, birçok dizin kaynağı tanımlayıcısının önemli bir parçasıdır: bir kullanıcı adının veya bir kullanıcının e-posta adresinin bir parçası, bir grup adresinin bir parçası ve bazen bir uygulama için uygulama KIMLIĞI URI 'sinin bir parçası. Azure Active Directory (Azure AD) içindeki bir kaynak, kaynağı içeren dizine ait bir etki alanı adı içerebilir. Azure AD 'de yalnızca genel yönetici etki alanlarını yönetebilir.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Azure AD dizininiz için birincil etki alanı adını ayarlama

Dizininiz oluşturulduğunda, ' contoso.onmicrosoft.com, ' gibi ilk etki alanı adı da birincil etki alanı adıdır. Birincil etki alanı, yeni bir kullanıcı oluşturduğunuzda yeni bir kullanıcı için varsayılan etki alanı adıdır. Birincil etki alanı adı ayarlandığında, yönetici, portalda Yeni Kullanıcı oluşturma sürecini basitleştirir. Birincil etki alanı adını değiştirmek için:

1. [Azure Portal](https://portal.azure.com) , Dizin Için genel yönetici olan bir hesapla oturum açın.
2. **Azure Active Directory**'yi seçin.
3. **Özel etki alanı adları**'nı seçin.
  
   ![Kullanıcı Yönetimi sayfasını açma](./media/domains-manage/add-custom-domain.png)
4. Birincil etki alanı olmasını istediğiniz etki alanının adını seçin.
5. **Birincil yap** komutunu seçin. İstendiğinde seçiminizi onaylayın.
  
   ![Etki alanı adını birincil yapın](./media/domains-manage/make-primary-domain.png)

Dizininiz için birincil etki alanı adını, federe olmayan doğrulanmış özel etki alanı olacak şekilde değiştirebilirsiniz. Dizininizin birincil etki alanını değiştirmek, mevcut kullanıcıların kullanıcı adını değiştirmez.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Azure AD kiracınıza özel etki alanı adları ekleme

En fazla 900 yönetilen etki alanı adı ekleyebilirsiniz. Tüm etki alanlarınızı şirket içi Active Directory ile Federasyon için yapılandırıyorsanız, her bir dizine en fazla 450 etki alanı adı ekleyebilirsiniz.

## <a name="add-subdomains-of-a-custom-domain"></a>Özel bir etki alanının alt etki alanlarını ekleme

Dizininize ' europe.contoso.com ' gibi bir üçüncü düzey etki alanı adı eklemek istiyorsanız, öncelikle contoso.com gibi ikinci düzey etki alanını eklemeniz ve doğrulamanız gerekir. Alt etki alanı, Azure AD tarafından otomatik olarak doğrulanır. Eklediğiniz alt etki alanının doğrulandığını görmek için tarayıcıdaki etki alanı listesini yenileyin.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Özel etki alanı adınızın DNS kaydedicisinde değişiklik yaparsanız yapmanız gerekenler

DNS kayıt şirketlerinde değiştirirseniz, Azure AD 'de başka yapılandırma görevleri yoktur. Azure AD ile etki alanı adını kesinti olmadan kullanmaya devam edebilirsiniz. Özel etki alanı adınızı Office 365, Intune veya Azure AD 'de özel etki alanı adlarına bağlı diğer hizmetlerle kullanıyorsanız, bu hizmetler için belgelere bakın.

## <a name="delete-a-custom-domain-name"></a>Özel bir etki alanı adını silme

Kuruluşunuz artık bu etki alanı adını kullanmadıysanız veya başka bir Azure AD ile bu etki alanı adını kullanmanız gerekiyorsa, Azure AD 'nizden özel bir etki alanı adını silebilirsiniz.

Özel bir etki alanı adını silmek için, ilk olarak dizininizdeki hiçbir kaynağın etki alanı adına dayandığından emin olmanız gerekir. Şu durumlarda dizininizden bir etki alanı adı silemezsiniz:

* Herhangi bir kullanıcının etki alanı adını içeren bir Kullanıcı adı, e-posta adresi veya proxy adresi vardır.
* Herhangi bir grubun, etki alanı adını içeren bir e-posta adresi veya proxy adresi vardır.
* Azure AD 'deki herhangi bir uygulamanın, etki alanı adını içeren bir uygulama KIMLIĞI URI 'SI vardır.

Özel etki alanı adını silebilmeniz için önce Azure AD dizininizde böyle bir kaynağı değiştirmeniz veya silmeniz gerekir.

### <a name="forcedelete-option"></a>ForceDelete seçeneği

[Azure AD Yönetim merkezinde](https://aad.portal.azure.com) veya [Microsoft Graph API](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta)kullanarak bir etki alanı adını **silmeyi zorla** yapabilirsiniz. Bu seçenekler, zaman uyumsuz bir işlem kullanır ve "user@contoso.com" gibi özel etki alanı adındaki tüm başvuruları "user@contoso.onmicrosoft.com." gibi ilk varsayılan etki alanı adı ile güncelleştirir. 

Azure portal **Forcedelete** ' i çağırmak için, etki alanı adına 1000 ' den az başvuru olduğundan emin olmanız ve Exchange 'in sağlama hizmeti 'Nin [Exchange Yönetim merkezinde](https://outlook.office365.com/ecp/)güncellenmesi veya kaldırılması gerektiği tüm başvurular olması gerekir. Bu, Exchange posta etkin güvenlik gruplarını ve dağıtılmış listeleri içerir; daha fazla bilgi için bkz. [posta etkin güvenlik gruplarını kaldırma](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups). Ayrıca, aşağıdakilerden biri geçerliyse **Forcedelete** işlemi başarılı olmaz:

* Office 365 etki alanı abonelik hizmetleri aracılığıyla bir etki alanı satın aldınız
* Başka bir müşteri kiracısı adına bir iş ortağıysanız

**Forcedelete** işleminin bir parçası olarak aşağıdaki eylemler gerçekleştirilir:

* Özel etki alanı adı ile ilk varsayılan etki alanı adına başvuran Kullanıcı UPN 'sini, Emaadresi ve ProxyAddress yeniden adlandırır.
* Özel etki alanı adı başvuruları olan grupların EMAA adını ilk varsayılan etki alanı adı ile yeniden adlandırır.
* Özel etki alanı adı başvuruları olan uygulamaların ıdentifieruri 'Lerini ilk varsayılan etki alanı adı ile yeniden adlandırır.

Şu durumlarda bir hata döndürülür:

* Yeniden adlandırılacak nesne sayısı 1000 ' den büyük
* Yeniden adlandırılacak uygulamalardan biri çok kiracılı bir uygulamadır

### <a name="frequently-asked-questions"></a>Sık sorulan sorular

**S: Etki alanı silme neden, bu etki alanı adında ana kopyalı grupları değiş tokuş ederim belirten bir hata ile başarısız oluyor mu?** <br>
**C:** Bugün, posta etkin güvenlik grupları ve dağıtılmış listeler gibi bazı gruplar Exchange tarafından sağlanır ve [Exchange Yönetim Merkezi 'nde (EAC)](https://outlook.office365.com/ecp/)el ile temizlenmelidir. Özel etki alanı adını kullanan kalan ProxyAddresses olabilir ve başka bir etki alanı adına el ile güncelleştirilmeleri gerekir. 

**S: Yönetici\@contoso.com olarak oturum açdım, ancak "contoso.com" etki alanı adını silemiyorum.**<br>
**C:** Kullanıcı hesabınızın adında silmeye çalıştığınız özel etki alanı adına başvurulamıyor. Genel yönetici hesabının, gibi ilk varsayılan etki alanı adını (. onmicrosoft.com) admin@contoso.onmicrosoft.comkullandığından emin olun. Gibi farklı bir genel yönetici hesabıyla oturum açın; örneğin admin@contoso.onmicrosoft.com , hesabın olduğu admin@fabrikam.com"fabrikam.com" gibi başka bir özel etki alanı adı.

**S: Etki alanını Sil düğmesine tıkladıktan sonra silme işleminin `In Progress` durumunu görürsünüz. Ne kadar sürer? Başarısız olursa ne olur?**<br>
**C:** Etki alanı silme işlemi, etki alanı adına yapılan tüm başvuruları yeniden adlandıran zaman uyumsuz bir arka plan görevdir. Bir veya iki dakika içinde tamamlanır. Etki alanı silme işlemi başarısız olursa, şu olmadığından emin olun:

* Etki alanı adı üzerinde Appıdentifieruri ile yapılandırılan uygulamalar
* Özel etki alanı adına başvuran posta etkin herhangi bir grup
* Etki alanı adına 1000 'den fazla başvuru

Koşulların herhangi birinin karşılanmadığını fark ederseniz, başvuruları el ile temizleyin ve etki alanını silmeyi yeniden deneyin.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Etki alanı adlarını yönetmek için PowerShell veya Graph API kullanma

Azure Active Directory etki alanı adları için birçok yönetim görevi Microsoft PowerShell kullanılarak veya Azure AD Graph API kullanılarak programlı bir şekilde tamamlanabilir.

* [Azure AD 'de etki alanı adlarını yönetmek için PowerShell kullanma](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#domains)
* [Azure AD 'de etki alanı adlarını yönetmek için Graph API kullanma](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Sonraki adımlar

* [Özel etki alanı adı ekleme](/azure/active-directory/fundamentals/add-custom-domain?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Azure AD 'de özel bir etki alanı adı üzerinde Exchange Yönetim Merkezi 'nde Exchange posta etkin güvenlik gruplarını kaldırma](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)
* [ForceMicrosoft Graph API ile özel bir etki alanı adını silme](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta)

---
title: Özel etki alanı adlarını ekleme ve doğrulama-Azure Active Directory | Microsoft Docs
description: Azure Active Directory bir etki alanı adını yönetmek için yönetim kavramları ve nasıl yapılır?
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 03/12/2021
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a201452a9c708d898ee1762385955b63684876c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577980"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Azure Active Directory özel etki alanı adlarını yönetme

Bir etki alanı adı, birçok Azure Active Directory (Azure AD) kaynağı için tanımlayıcının önemli bir parçasıdır: bir kullanıcı adının veya Kullanıcı için e-posta adresinin bir parçası, bir grup adresinin bir parçası ve bazen bir uygulama için uygulama KIMLIĞI URI 'sinin bir parçası. Azure AD 'deki bir kaynak, kaynağı içeren kuruluşa ait olan bir etki alanı adı içerebilir. Azure AD 'de yalnızca genel yönetici etki alanlarını yönetebilir.

## <a name="set-the-primary-domain-name-for-your-azure-ad-organization"></a>Azure AD kuruluşunuz için birincil etki alanı adını ayarlama

Kuruluşunuz oluşturulduğunda, ' contoso.onmicrosoft.com, ' gibi ilk etki alanı adı da birincil etki alanı adıdır. Birincil etki alanı, yeni bir kullanıcı oluşturduğunuzda yeni bir kullanıcı için varsayılan etki alanı adıdır. Birincil etki alanı adı ayarlandığında, yönetici, portalda Yeni Kullanıcı oluşturma sürecini basitleştirir. Birincil etki alanı adını değiştirmek için:

1. [Azure Portal](https://portal.azure.com) , kuruluş Için genel yönetici olan bir hesapla oturum açın.
2. **Azure Active Directory** seçin.
3. **Özel etki alanı adları**'nı seçin.
  
   ![Kullanıcı Yönetimi sayfasını açma](./media/domains-manage/add-custom-domain.png)
4. Birincil etki alanı olmasını istediğiniz etki alanının adını seçin.
5. **Birincil yap** komutunu seçin. İstendiğinde seçiminizi onaylayın.
  
   ![Etki alanı adını birincil yapın](./media/domains-manage/make-primary-domain.png)

Kuruluşunuzun birincil etki alanı adını, federe olmayan doğrulanmış özel etki alanı olacak şekilde değiştirebilirsiniz. Kuruluşunuzun birincil etki alanını değiştirmek, mevcut kullanıcıların kullanıcı adını değiştirmez.

## <a name="add-custom-domain-names-to-your-azure-ad-organization"></a>Azure AD kuruluşunuza özel etki alanı adları ekleme

En fazla 5000 yönetilen etki alanı adı ekleyebilirsiniz. Tüm etki alanlarınızı şirket içi Active Directory ile Federasyon için yapılandırıyorsanız, her bir kuruluşta en fazla 2500 etki alanı adı ekleyebilirsiniz.

## <a name="add-subdomains-of-a-custom-domain"></a>Özel bir etki alanının alt etki alanlarını ekleme

Kuruluşunuza ' europe.contoso.com ' gibi bir alt etki alanı adı eklemek istiyorsanız, öncelikle contoso.com gibi kök etki alanını eklemeniz ve doğrulamanız gerekir. Alt etki alanı, Azure AD tarafından otomatik olarak doğrulanır. Eklediğiniz alt etki alanının doğrulandığını görmek için tarayıcıdaki etki alanı listesini yenileyin.

Bir Azure AD kuruluşuna zaten bir contoso.com etki alanı eklediyseniz, farklı bir Azure AD kuruluşunda alt etki alanı europe.contoso.com da doğrulayabilirsiniz. Alt etki alanı eklenirken, DNS barındırma sağlayıcısına bir TXT kaydı eklemeniz istenir.



## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Özel etki alanı adınızın DNS kaydedicisinde değişiklik yaparsanız yapmanız gerekenler

DNS kayıt şirketlerinde değiştirirseniz, Azure AD 'de başka yapılandırma görevleri yoktur. Azure AD ile etki alanı adını kesinti olmadan kullanmaya devam edebilirsiniz. Özel etki alanı adınızı Microsoft 365, Intune veya Azure AD 'de özel etki alanı adlarına bağlı diğer hizmetlerle kullanıyorsanız, bu hizmetler için belgelere bakın.

## <a name="delete-a-custom-domain-name"></a>Özel bir etki alanı adını silme

Kuruluşunuz artık bu etki alanı adını kullanmadıysanız veya başka bir Azure AD ile bu etki alanı adını kullanmanız gerekiyorsa, Azure AD 'nizden özel bir etki alanı adını silebilirsiniz.

Özel bir etki alanı adını silmek için, önce kuruluşunuzdaki hiçbir kaynağın etki alanı adına bağlı olmadığından emin olmanız gerekir. Şu durumlarda kuruluşunuzda bir etki alanı adı silemezsiniz:

* Herhangi bir kullanıcının etki alanı adını içeren bir Kullanıcı adı, e-posta adresi veya proxy adresi vardır.
* Herhangi bir grubun, etki alanı adını içeren bir e-posta adresi veya proxy adresi vardır.
* Azure AD 'deki herhangi bir uygulamanın, etki alanı adını içeren bir uygulama KIMLIĞI URI 'SI vardır.

Özel etki alanı adını silebilmeniz için önce Azure AD kuruluşunuzda böyle bir kaynağı değiştirmeniz veya silmeniz gerekir.

### <a name="forcedelete-option"></a>ForceDelete seçeneği

[Azure AD Yönetim merkezinde](https://aad.portal.azure.com) veya [Microsoft Graph API](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)kullanarak bir etki alanı adını **silmeyi zorla** yapabilirsiniz. Bu seçenekler, zaman uyumsuz bir işlem kullanır ve "" gibi özel etki alanı adındaki tüm başvuruları user@contoso.com "." gibi ilk varsayılan etki alanı adı ile güncelleştirir user@contoso.onmicrosoft.com .

Azure portal **Forcedelete** ' i çağırmak için, etki alanı adına 1000 ' den az başvuru olduğundan emin olmanız ve Exchange 'in sağlama hizmeti 'Nin [Exchange Yönetim merkezinde](https://outlook.office365.com/ecp/)güncellenmesi veya kaldırılması gerektiği tüm başvurular olması gerekir. Buna Exchange Mail-Enabled güvenlik grupları ve dağıtılmış listeler dahildir; daha fazla bilgi için bkz. [posta etkin güvenlik gruplarını kaldırma](/Exchange/recipients/mail-enabled-security-groups#Remove%20mail-enabled%20security%20groups&preserve-view=true). Ayrıca, aşağıdakilerden biri geçerliyse **Forcedelete** işlemi başarılı olmaz:

* Microsoft 365 etki alanı abonelik hizmetleri aracılığıyla bir etki alanı satın aldınız
* Başka bir müşteri kuruluşu adına bir iş ortağıysanız

**Forcedelete** işleminin bir parçası olarak aşağıdaki eylemler gerçekleştirilir:

* Özel etki alanı adı ile ilk varsayılan etki alanı adına başvuran Kullanıcı UPN 'sini, Emaadresi ve ProxyAddress yeniden adlandırır.
* Özel etki alanı adı başvuruları olan grupların EMAA adını ilk varsayılan etki alanı adı ile yeniden adlandırır.
* Özel etki alanı adı başvuruları olan uygulamaların ıdentifieruri 'Lerini ilk varsayılan etki alanı adı ile yeniden adlandırır.

Şu durumlarda bir hata döndürülür:

* Yeniden adlandırılacak nesne sayısı 1000 ' den büyük
* Yeniden adlandırılacak uygulamalardan biri çok kiracılı bir uygulamadır

### <a name="frequently-asked-questions"></a>Sık sorulan sorular

**S: etki alanı silme neden, bu etki alanı adında ana kopyalı grupları değiş tokuş ederim belirten bir hata ile başarısız oluyor mu?** <br>
Y **:** Bugün, Mail-Enabled güvenlik grupları ve dağıtılmış listeler gibi bazı gruplar Exchange tarafından sağlanır ve [Exchange Yönetim Merkezi 'nde (EAC)](https://outlook.office365.com/ecp/)el ile temizlenmelidir. Özel etki alanı adını kullanan kalan ProxyAddresses olabilir ve başka bir etki alanı adına el ile güncelleştirilmeleri gerekir. 

**S: yönetici contoso.com olarak oturum açdım \@ , ancak "contoso.com" etki alanı adını silemiyorum?**<br>
Y **:** Kullanıcı hesabınızın adında silmeye çalıştığınız özel etki alanı adına başvurulamıyor. Genel yönetici hesabının, gibi ilk varsayılan etki alanı adını (. onmicrosoft.com) kullandığından emin olun admin@contoso.onmicrosoft.com . Gibi farklı bir genel yönetici hesabıyla oturum açın; örneğin admin@contoso.onmicrosoft.com , hesabın olduğu "fabrikam.com" gibi başka bir özel etki alanı adı admin@fabrikam.com .

**S: etki alanını Sil düğmesine tıkladıktan sonra `In Progress` silme işleminin durumunu görürsünüz. Ne kadar sürer? Başarısız olursa ne olur?**<br>
Y **:** Etki alanı silme işlemi, etki alanı adına yapılan tüm başvuruları yeniden adlandıran zaman uyumsuz bir arka plan görevdir. Bir veya iki dakika içinde tamamlanır. Etki alanı silme işlemi başarısız olursa, şu olmadığından emin olun:

* Etki alanı adı üzerinde Appıdentifieruri ile yapılandırılan uygulamalar
* Özel etki alanı adına başvuran posta etkin herhangi bir grup
* Etki alanı adına 1000 'den fazla başvuru

Koşulların herhangi birinin karşılanmadığını fark ederseniz, başvuruları el ile temizleyin ve etki alanını silmeyi yeniden deneyin.

## <a name="use-powershell-or-the-microsoft-graph-api-to-manage-domain-names"></a>Etki alanı adlarını yönetmek için PowerShell veya Microsoft Graph API 'sini kullanma

Azure Active Directory etki alanı adları için birçok yönetim görevi Microsoft PowerShell kullanılarak veya Microsoft Graph API kullanılarak programlı bir şekilde tamamlanabilir.

* [Azure AD 'de etki alanı adlarını yönetmek için PowerShell kullanma](/powershell/module/azuread/#domains&preserve-view=true)
* [Etki alanı kaynak türü](/graph/api/resources/domain)

## <a name="next-steps"></a>Sonraki adımlar

* [Özel etki alanı adı ekleme](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
* [Azure AD 'de özel bir etki alanı adı üzerinde Exchange Yönetim Merkezi 'nde Exchange posta etkin güvenlik gruplarını kaldırma](/Exchange/recipients/mail-enabled-security-groups#Remove%20mail-enabled%20security%20groups&preserve-view=true)
* [ForceMicrosoft Graph API ile özel bir etki alanı adını silme](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)

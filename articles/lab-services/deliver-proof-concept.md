---
title: Kavram kanıtı sunma-Azure DevTest Labs | Microsoft Docs
description: Azure DevTest Labs bir kurumsal ortama başarıyla dahil edilmesi için bir kavram kanıtı sunma hakkında bilgi edinin.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: takamath
ms.openlocfilehash: 13e3f6be851e81b1186d55bb313dd23f1920f007
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616346"
---
# <a name="deliver-a-proof-of-concept"></a>Kavram kanıtı sunun 

Azure DevTest Labs yönelik temel senaryolarından biri, bulutta geliştirme ve test ortamlarını etkinleştirir. Örneklere şunlar dahildir:

* Bulutta geliştirici masaüstleri oluşturma.
* Ortamları test için yapılandırma.
* Sanal makinelere ve diğer Azure kaynaklarına erişimi etkinleştirme.
* Geliştiricilerin öğrenmek ve denemek için bir korumalı alan alanı ayarlama.

DevTest Labs Ayrıca, kuruluşun kurumsal güvenlik, mevzuata ve uyumluluk ilkelerine bağlı olan geliştiricilere "self servis Azure" sağlamasına olanak tanıyan ilke ve maliyet denetimleri sağlar. 

Her kuruluşun, Azure DevTest Labs başarıyla ortamlarına nasıl dahil edilebilir olması için farklı gereksinimleri vardır. Bu makalede, kuruluşların başarılı bir kavram kanıtı sağlamak için tamamlaması gereken en yaygın adımlar açıklanmaktadır. Kavram kanıtı, başarılı bir uçtan uca dağıtıma yönelik ilk adımdır. 

## <a name="getting-started"></a>Başlarken 

Kavram kanıtı sunmaya başlamak için. Azure ve DevTest Labs hakkında bilgi edinmek için biraz zaman harcamanız önemlidir.  Kaynak başlangıç kaynakları aşağıda verilmiştir: 

* [Azure portal anlama](https://azure.microsoft.com/features/azure-portal/)
* [DevTest Labs ile ilgili temel bilgiler](devtest-lab-overview.md)
* [DevTest Labs desteklenen senaryolar](devtest-lab-guidance-get-started.md)
* [DevTest Labs kurumsal belgeleri](devtest-lab-guidance-prescriptive-adoption.md)
* [Azure ağa giriş](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Önkoşullar 

DevTest Labs ile pilot veya kavram kanıtı 'nı başarılı bir şekilde tamamlamaya yönelik birkaç önkoşul vardır: 

* **Azure aboneliği**: Kuruluşlar, Azure 'a erişim sağlayan mevcut bir [Kurumsal Anlaşma](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) sahiptir ve DevTest Labs için mevcut veya yeni bir abonelik kullanabilirler. Alternatif olarak, kuruluşlar pilot süresince bir [Visual Studio aboneliği](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) kullanabilir (ücretsiz Azure kredilerinden faydalanabilirsiniz). Bu seçeneklerden hiçbiri yoksa, bir kuruluş, [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ)oluşturabilir ve kullanabilir. Bir Kurumsal Anlaşma varsa, [Kurumsal Geliştirme ve test aboneliği](https://azure.microsoft.com/offers/ms-azr-0148p/) kullanmak, Windows 10/Windows 8.1 istemci işletim sistemlerine ve geliştirme ve test iş yüklerine yönelik indirimli ücretler için erişim sağlamak için harika bir seçenektir. 
* **Azure Active Directory kiracı**: Kullanıcıları yönetmeyi etkinleştirmek için (örneğin, Kullanıcı ekleme veya laboratuvar sahipleri ekleme), bu kullanıcıların pilot için Azure aboneliğinde kullanılan [Azure Active Directory kiracının](https://azure.microsoft.com/services/active-directory/) bir parçası olması gerekir. Genellikle kuruluşlar, kullanıcıların bulutta şirket içi kimliklerini kullanmasını sağlamak için [karma kimlik](../active-directory/hybrid/whatis-hybrid-identity.md) ayarlayacaktır, ancak bu, DevTest Labs pilot için gerekli değildir. 

## <a name="scoping-of-the-pilot"></a>Pilot kapsamı 

Uygulamayı başlatmak için önce bir pilot planlaması önemlidir. Kaynakların sonsuza kadar sürekli kalmayacağından, pilot kullanıcıları için uygun beklentileri doğru şekilde ayarlamayacağımızı unutmayın. 

> [!IMPORTANT]
> Pilot ve ayar beklentilerini kapsayan en fazla önem derecesini vurgulamıyoruz.

Pilot uygulamayı başlatmanıza başlamadan önce bu önemli soruları yanıtlayın: 

* Ne öğrenmek istiyorsunuz ve pilot için başarı ne şekilde görünüyor? 
* Pilot 'da hangi iş yükleri veya senaryolar ele alınacaktır? Pilot 'nın kapsama ve hızla tamamlanmasını sağlamak için yalnızca küçük bir küme tanımlamanız önemlidir. 
* Laboratuvarda hangi kaynakların kullanılabilir olması gerekir? Örneğin: özel görüntüler, Market görüntüleri, ilkeler, ağ topolojisi. 
* Deneyimi doğrulamak üzere pilot kullanıcıya dahil edilecek kullanıcılar ve takımlar kim?  
* Pilot 'un süresi nedir? İki hafta veya bir ay gibi planlı kapsama göre iyi bir şekilde hizalanan bir zaman dilimi seçin. 
* Pilot tamamlandıktan sonra, pilot sırasında kullanılan ayrılmış kaynaklarla ne olur? Pilot Kaynakları atmayı planlıyor musunuz? Şunu düşünebilirsiniz:
   
   "Pilot 'ın sonunda sanal makineleri ve laboratuvarları oluşturma konusunda planlayabilmemiz için, pilot için tek bir abonelik ayarlayabiliriz ve tüm çalışmalarımızı paralel olarak ölçeklendirirken tüm çalışmalarınızı yapabiliriz." 

Pilot "kusursuz" hale getirmek, bu sayede, şirket sırasında hizmet alındıktan sonra nihai durumun ne olacağını da temsil eden genel bir uygulamadır. Bu bir yanlış varsayım. Pilot uygulamayı kullanmaya *başlamadan önce* "mükemmel" ne kadar yaklaşıyoruz. Pilot 'ın amacı, son hizmeti ölçeklendirmek ve kullanıma sunmak için doğru kararları almaktır. 

Pilot 'ın odaklanmasına, Azure DevTest Labs kuruluşunuza doğru hizmet olup olmadığı konusunda yanıt vermek için gereken en düşük iş yüklerini ve bağımlılıklarını çekmeye yönelik olması gerekir. Hızlı ve temiz bir başarı sağlamaya yardımcı olmak için en az bağımlılıklara sahip en basit iş yükünü seçmenizi öneririz. Bu mümkün değilse, pilot aşamadaki başarı 'nın ölçek genişletme aşamasında çoğaltılabilmesi için olası karmaşıklıkları ortaya çıkaran en fazla temsili bir iş yükü seçin. 

Aşağıdaki örnek, kavram kapsamlı bir kavram kanıtı göstermektedir. 

## <a name="example-proof-of-concept-plan"></a>Örnek: kavram kanıtı planı 

Bu bölümde, bir pilot for DevTest Labs kavramının kapsamını belirlemek için kullanılacak bir örnek gösterilmektedir. 

> [!TIP]
> Projenizi hata için ayarlama olasılığını en aza indirmek için, bu bölümde açıklanan örneği atlamanızı kesinlikle öneririz. 

### <a name="overview"></a>Genel Bakış 

Şirket ağından yalıtılmış bir ortam olarak kullanılacak satıcıların DevTest Labs 'e dayalı olarak Azure 'da yeni bir ortam geliştirmeyi planlıyoruz. Çözümün gereksinimleri karşılayıp karşılamadığını öğrenmek için, uçtan uca çözümü doğrulamaya yönelik bir kavram kanıtı geliştiriyoruz. Deneyiminizi denemek ve doğrulamak için birkaç satıcı ekledik. 

### <a name="outcomes"></a>Sonucunu 

Kavram kanıtı oluştururken öncelikle sonuçlar üzerine odaklanıyoruz (elde edilmeye çalışıyoruz). Kavram kanıtı 'nın sonuna kadar şunu bekliyoruz: 

* Satıcıların Azure 'daki yalıtılmış bir ortama erişmesi için Azure Active Directory (Azure AD) ' deki Konuk hesaplarını kullanması için bir uçtan uca çözüm. Ortamın üretken olmaları için gereken kaynakları vardır. 
* Daha geniş ölçekli kullanımı ve benimsemeyi etkileyen olası engelleme sorunları numaralandırılır ve anlaşılır.
* Kavram kanıtı geliştirmede yer alan kişiler tüm kodun iyi bir şekilde anlaşılmasıdır. Ayrıca, dahil edilen ve daha fazla benimseme konusunda emin olan yardımcı

### <a name="open-questions-and-prerequisites"></a>Açık soru ve önkoşulları 

* Bu proje için kullanabilmemiz için bir abonelik oluşturuldu mu? 
* Azure AD kiracımız var ve Azure AD Genel Yöneticisi, Azure AD ile ilgili sorulara yönelik yardım ve rehberlik sağlayabilen kişiyi tanımladı mı? 
* Projede çalışan bireyler için işbirliği yapmak üzere bir yer var mı? 

   * Kaynak kodu ve betikler (Azure Repos gibi) 
   * Belgeler (Microsoft ekipleri veya SharePoint gibi)  
   * Konuşmalar (Microsoft ekipleri gibi) 
   * İş öğeleri (Azure Boards gibi) 
* Satıcılar için gereken kaynaklar nelerdir? Bu, yerel olarak sanal makinelerde ve diğer gerekli sunucularda bulunan, ağda bulunan uygulamaları içerir. 
* Sanal makineler, Azure 'daki bir etki alanına katılmalıdır mi? Bu durumda, Azure Active Directory Domain Services (Azure AD DS) veya başka bir şey olacaktır. 
* Kavram kanıtı 'nın hedefi olan ekip veya satıcıları tanımladık mı? Ortamın müşterileri kim olacak?
* Kavram kanıtı için hangi Azure bölgesini kullanacağız? 
* Satıcıların IaaS (VM 'Ler) yanı sıra DevTest Labs aracılığıyla kullanmasına izin verilen bir hizmet listesi var mı? 
* Laboratuvarı kullanarak satıcıları/Kullanıcıları eğitme hakkında nasıl planlıyoruz? 

### <a name="components-of-the-proof-of-concept-solution"></a>Kavram kanıtı çözümünün bileşenleri 

Çözümün aşağıdaki bileşenlere sahip olmasını bekliyor: 

* Çeşitli satıcı takımları Azure 'da bir laboratuvar kümesi kullanacaktır.
* Laboratuvarlar, gereksinimleri destekleyen bir ağ altyapısına bağlıdır.
* Satıcıların Azure AD ve rol atamaları aracılığıyla laboratuvarlara erişimi vardır.
* Satıcıların kaynaklarına başarıyla bağlanması için bir yol vardır. Özellikle, siteden siteye VPN, genel IP adresleri olmadan sanal makinelere doğrudan erişim sağlar.
* Bir yapı kümesi, satıcıların sanal makinelerde ihtiyacı olan gerekli yazılımları ele alır.

## <a name="additional-planning-and-design-decisions"></a>Ek planlama ve tasarım kararları 

Bir tam DevTest Labs çözümünü bırakmadan önce bazı önemli planlama ve tasarım kararları almanız gerekir. Kavram kanıtı üzerinde çalışma deneyimi, bu kararları almanıza yardımcı olabilir. Daha ayrıntılı bilgiler şunlardır: 

* **Abonelik topolojisi**: Azure 'daki kaynaklara yönelik kurumsal düzeyde gereksinimler, [tek bir abonelik içindeki kullanılabilir kotaların](https://docs.microsoft.com/azure/azure-subscription-service-limits)ötesine daha fazla genişletebilir. Bu, ilk abonelik limitlerini artırmak için birden çok Azure aboneliğine ve/veya hizmet isteğine sahiptir. Kaynakları abonelikler arasında nasıl dağıtacağınıza karar vermeniz önemlidir. Bir değerli kaynak, daha sonra kaynakları başka bir aboneliğe taşımak zor olduğundan, [abonelik karar kılavuzudur](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) . Örneğin, bir laboratuvar oluşturulduktan sonra başka bir aboneliğe taşınamaz.  
* **Ağ topolojisi**: DevTest Labs tarafından otomatik olarak oluşturulan [varsayılan ağ altyapısı](../app-service/networking-features.md) , kurumsal kullanıcıların gereksinimlerini ve kısıtlamalarını karşılamak için yeterli olmayabilir. [Azure ExpressRoute 'a bağlı sanal ağlar](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/), abonelikler arasında bağlantı için [hub ve bağlı bileşen](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) , hatta yalnızca şirket içi bağlantıyı sağlamak için de [zorlanacak yönlendirme](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) hakkında bilgi almak yaygındır. DevTest Labs, laboratuvarda yeni sanal makineler oluştururken kullanımını etkinleştirmek için mevcut sanal ağların laboratuvara bağlanmasını sağlar. 
* **Sanal makinelere uzaktan erişim**: DevTest Labs 'de bulunan sanal makinelere uzaktan erişim için birçok seçenek vardır. En kolay genel IP 'Leri veya paylaşılan genel IP 'Leri kullanmaktır. Bunlar [, laboratuvarda kullanılabilir](devtest-lab-shared-ip.md)olan ayarlardır. Bu seçenekler yeterli değilse, uzaktan erişim ağ geçidi kullanmak da bir seçenektir. Bu seçenek [DevTest Labs Enterprise Reference mimarisinde](devtest-lab-reference-architecture.md) gösterilir ve [DevTest Labs Uzak Masaüstü Ağ geçidi belgelerinde](configure-lab-remote-desktop-gateway.md)daha ayrıntılı olarak açıklanmıştır. Kuruluşlar, laboratuvarlarını şirket içi ağına bağlamak için ExpressRoute veya siteden siteye VPN de kullanabilir. Bu seçenek, internet 'te hiçbir etkilenmesiz özel IP adreslerini temel alarak sanal makinelere doğrudan uzak masaüstü veya SSH bağlantıları sağlar. 
* **Izinleri işleme**: DevTest Labs 'de yaygın olarak kullanılan iki anahtar izinleri [sahip ve laboratuar Kullanıcı](devtest-lab-add-devtest-user.md). Laboratuvardaki her erişim düzeyiyle güvenilir olacak şekilde, DevTest Labs 'nin genel kullanıma sunulmadan önce karar vermeniz önemlidir. Ortak bir model, laboratuvar sahibi olarak (örneğin, ekip lideri) laboratuvar sahibi ve takım üyeleri olarak laboratuar kullanıcıları olarak bütçe sahibidir. Bu model, bütçesinden sorumlu olan kişinin (ekip lideri) ilke ayarlarını ayarlamasını ve ekibi bütçe içinde tutmasını sağlar.  

## <a name="completing-the-proof-of-concept"></a>Kavram kanıtı Tamamlanıyor 

Beklenen dersleri ele alındıktan sonra pilot uygulamanın tamamlanışında. Bu, kullanıcılardan geri bildirim toplamak, Pilot uygulamanın başarılı olup olmadığını belirlemek ve kuruluşun kurumsal bir DevTest Labs 'in bir ölçek dağıtımı üzerine ilerleyeceğini belirlemek için zaman alır. Ayrıca, ölçek dağıtımı genelinde tutarlılık sağlamak için DevTest Labs ve ilişkili kaynakların dağıtımını otomatik hale getirmek için harika bir süredir. 

## <a name="next-steps"></a>Sonraki adımlar 

* [DevTest Labs kurumsal belgeleri](devtest-lab-guidance-prescriptive-adoption.md)
* [Bir kuruluş için başvuru mimarisi](devtest-lab-reference-architecture.md)
* [DevTest Labs dağıtımınızı ölçeklendirme](devtest-lab-guidance-orchestrate-implementation.md)
* [Azure DevTest Labs uygulanmasını düzenleme](devtest-lab-guidance-orchestrate-implementation.md)

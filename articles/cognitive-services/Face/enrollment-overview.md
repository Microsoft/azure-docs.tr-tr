---
title: Yüz hizmetine Kullanıcı eklemek için en iyi uygulamalar
titleSuffix: Azure Cognitive Services
description: Yüz tanıma hizmetinde kullanıcıları kaydetmek için yüz kaydı süreci hakkında bilgi edinin.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 04/19/2021
ms.author: pafarley
ms.openlocfilehash: 183983779c09658d8d6f609319a127b1f406452d
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728437"
---
# <a name="best-practices-for-adding-users-to-a-face-service"></a>Yüz hizmetine Kullanıcı eklemek için en iyi uygulamalar

Bilişsel hizmetler 'i yüz doğrulama veya tanımlama için Yüz Tanıma API'si kullanmak için yüzleri bir **Largepersongroup** veya benzer bir veri yapısına kaydetmeniz gerekir. Bu ayrıntılı inceleme, Kullanıcı ve örnek mantığa yönelik anlamlı onay toplamaya yönelik en iyi yöntemleri gösterir ve bu da tanıma doğruluğunu en iyi hale getirecek yüksek kaliteli kayıtlar oluşturabilir. 

## <a name="meaningful-consent"></a>Anlamlı onay 

Yüz tanıma için bir kayıt uygulamasının temel amaçlarından biri, kullanıcılara bir iş sitesine erişim gibi belirli amaçlar için yüz görüntülerinin kullanımını kabul etmek için bir fırsat vermektir. Yüz tanıma teknolojileri hassas kişisel verilerin toplanması olarak tanınabileceğinden, özellikle de hem saydam hem de daha etkili bir şekilde onay istemek önemlidir. İzin, kullanıcılara en iyi şekilde sahip olabilecekleri kararı vermek için bu kabul edildiğinde anlamlıdır.   

Microsoft Kullanıcı araştırması, Microsoft 'un sorumlu AI ilkelerine ve [dış araştırmalara](ftp://ftp.cs.washington.edu/tr/2000/12/UW-CSE-00-12-02.pdf)bağlı olarak, teknolojide kaydolan kullanıcılara aşağıdakileri sunan onay açısından anlamlı olduğunu bulduk:

* Tanıma: kullanıcıların yüz şablonlarını veya kayıt fotoğraflarını sağlamaları istendiğinde şüpheli olmaması gerekir. 
* Anlama: kullanıcılar kendi sözcüklerini, kim tarafından ne tür bir, ne kadar ve ne kadar ilgili olduğunu doğru şekilde açıklayabilmelidir. 
* Seçim özgürlüğü: kullanıcılar, yüz tanıma ve izin verip vermeyeceğinizi seçerken zorlanmamalıdır. 
* Denetim: kullanıcılar istediği zaman onayını iptal edebilir ve verilerini silebilir. 

Bu bölümde, yüz tanıma için bir kayıt uygulaması geliştirmeye yönelik rehberlik sunulmaktadır. Bu kılavuz, Microsoft Kullanıcı araştırması temel alınarak, giriş oluşturmak için yüz tanıma 'da kişileri kaydetme bağlamında geliştirilmiştir. Bu nedenle, bu öneriler tüm yüz tanıma çözümlerine uygulanmayabilir. Yüz Tanıma API'si sorumlu kullanımı, tümleştirildiği belirli bir içeriğe bağlıdır, bu nedenle Bu önerilerin önceliklendirmesi ve uygulaması senaryonuza uyarılmalıdır. 

> [!NOTE]
> Kayıt uygulamanızı yasalara uygun yasal gereksinimlere göre hizalamanız ve tüm veri koleksiyonunuzu ve işleme uygulamalarınızı doğru şekilde yansıtmanız sizin sorumluluğunuzdadır.

## <a name="application-development"></a>Uygulama geliştirme 

Bir kayıt akışını tasarlayabilmeniz için, oluşturmakta olduğunuz uygulamanın, verileri nasıl koruduğu konusunda kullanıcılara yaptığınız tutumunu nasıl tutabildiğini düşünün. Aşağıdaki öneriler, kişisel verileri güvenli hale getirmek, kullanıcıların gizliliğini yönetmek ve uygulamanın tüm kullanıcılar tarafından erişilebilir olmasını sağlamak için sorumlu yaklaşımların bulunduğu bir kayıt deneyimi oluşturmanıza yardımcı olabilir.  

|Kategori | Öneriler |
|---|---|
|Donanım | Kayıt cihazının kamera kalitesini göz önünde bulundurun. |
|Önerilen kayıt özellikleri | Multi-Factor Authentication ile bir oturum açma adımı ekleyin. </br></br>Bir diğer ad veya kimlik numarası gibi Kullanıcı bilgilerini Yüz Tanıma API'si yüz şablonu KIMLIĞIYLE (kişi KIMLIĞI olarak bilinir) bağlayın. Bu eşleme, bir kullanıcının kaydını almak ve yönetmek için gereklidir. Note: kişi KIMLIĞI, uygulamada gizli dizi olarak değerlendirilmelidir.</br></br>Artık, eski çalışanlar gibi yüz tanıma teknolojisinin kullanıcısı olmayan kişilerin yüz şablonları ve kayıt fotoğrafları dahil olmak üzere tüm kayıt verilerini silmek için otomatik bir işlem ayarlayın. </br></br>Kullanıcıya onay almak için önerilen tanıma, anlama, seçim veya denetim gibi konularda otomatik kayıt yapmaktan kaçının. </br></br>Kullanıcılardan kayıt için kullanılan görüntüleri kaydetme izni isteyin. Bu, her 10 ayda bir yeni modelde yeniden kaydolmak için yeni kayıt fotoğraflarının gerekli olacağı için bir model güncelleştirmesi olduğunda yararlıdır. Özgün görüntüler kaydedilmemişse, kullanıcıların baştan itibaren kayıt işlemini yapması gerekir.</br></br>Kullanıcıların resimleri sistemde depolamayı geri yüklemelerine izin verin. Seçimi daha net hale getirmek için, kayıt fotoğraflarını kaydetmek üzere ikinci bir izin isteği ekranı ekleyebilirsiniz. </br></br>Fotoğraflar kaydedilmişse, bir model güncelleştirmesi olduğunda tüm kullanıcıları yeniden kaydetmek için otomatikleştirilmiş bir işlem oluşturun. Kayıt fotoğraflarını kaydeden kullanıcılar kendilerini yeniden kaydetmeleri gerekmez. </br></br>Bir kullanıcının kaydetme sorunu varsa, belirlenen yöneticilerin belirli kalite filtrelerini geçersiz kılmasını sağlayan bir uygulama özelliği oluşturun. |
|Güvenlik | Bilişsel hizmetler, bekleyen ve aktarım sırasında kullanıcı verilerini şifrelemek için [en iyi yöntemleri](../cognitive-services-virtual-networks.md?tabs=portal) takip ediyor. Aşağıda, kayıt deneyimi sırasında kullanıcılara yaptığınız güvenlik ile ilgili güvenliğin sağlanmasına yardımcı olabilecek diğer yöntemler verilmiştir. </br></br>Kayıt sırasında herhangi bir noktada kişi KIMLIĞINE hiç kimsenin erişememesini sağlamak için güvenlik önlemleri alın. Note: PersonID, kayıt sisteminde bir gizli dizi olarak değerlendirilmelidir. </br></br>Bilişsel hizmetler ile [rol tabanlı erişim denetimi](../../role-based-access-control/overview.md) kullanın. </br></br>Veritabanları gibi kaynaklara erişmek için anahtarlar ve gizlilikler üzerinde belirteç tabanlı kimlik doğrulaması ve/veya paylaşılan erişim imzaları (SAS) kullanın. İstek veya SAS belirteçlerini kullanarak, hesap anahtarlarınızın güvenliğini tehlikeye atmadan verilere sınırlı erişim verebilirsiniz ve belirteçte bir süre sonu zamanı belirtebilirsiniz. </br></br>Uygulamanızda gizli dizileri, anahtarları veya parolaları hiçbir şekilde depolamayın. |
|Kullanıcı gizliliği |Farklı gizlilik sorunlarını gidermek için bir dizi kayıt seçeneği sağlar. İnsanların, yüz tanıma sistemine kaydolmak için kişisel cihazlarını kullanmalarını zorunlu değildir. </br></br>Kullanıcıların kayıt uygulamasından istediğiniz zaman ve herhangi bir nedenle yeniden kaydolmasına, izin iptal etmesine ve verileri silmesine izin verin. |
|Erişilebilirlik |Uygulamanın taşınabilirlik veya görsel sorunları olan kişiler tarafından kullanılabilir olduğundan emin olmak için erişilebilirlik standartlarını (örneğin, [ada](https://www.ada.gov/regs2010/2010ADAStandards/2010ADAstandards.htm) veya [W3C](https://www.w3.org/TR/WCAG21/)) izleyin. |

## <a name="next-steps"></a>Sonraki adımlar  

Örnek kayıt uygulaması kullanmaya başlamak için [kayıt uygulaması oluşturma](build-enrollment-app.md) kılavuzunu izleyin. Daha sonra bunu özelleştirin veya ürününüzün ihtiyaçlarına uyacak şekilde kendi uygulamanızı yazın.
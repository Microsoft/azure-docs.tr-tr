---
title: Karma kimlik tasarımı-içerik yönetimi gereksinimleri Azure | Microsoft Docs
description: İşletmenizin içerik yönetimi gereksinimlerini belirleme hakkında öngörüler sağlar. Genellikle bir Kullanıcı kendi cihazına sahip olduğunda, kullandıkları uygulamaya göre farklı olacak birden çok kimlik bilgisi de olabilir. Kişisel kimlik bilgileri kullanılarak oluşturulan içeriklerin ve şirket kimlik bilgileri kullanılarak oluşturulanların ayırt edilmesi önemlidir. Kimlik çözümünüz, gizliliği güvence altına alırken ve veri sızıntılarına karşı korumayı artırarak son kullanıcıya sorunsuz bir deneyim sunmak için bulut hizmetleriyle etkileşime girebilmelidir.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57990fda7475b95bd6582fa5a495ac8d24aa55d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94408541"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Karma kimlik çözümünüz için içerik yönetimi gereksinimlerini belirleme
İşletmenizin içerik yönetimi gereksinimlerini anlamak, hangi karma kimlik çözümünün kullanılacağı kararını doğrudan etkileyebilir. Birden çok cihazın ve kullanıcıların kendi[cihazlarını (](/mem/intune/fundamentals/byod-technology-decisions)KCG) getirebilmesi sayesinde, Şirket kendi verilerini korumalıdır, ancak aynı zamanda kullanıcının gizliliğini korumak zorunda kalır. Genellikle bir Kullanıcı kendi cihazına sahip olduğunda, kullandıkları uygulamaya göre farklı olacak birden çok kimlik bilgisi de olabilir. Kişisel kimlik bilgileri kullanılarak oluşturulan içeriklerin ve şirket kimlik bilgileri kullanılarak oluşturulanların ayırt edilmesi önemlidir. Kimlik çözümünüz, gizliliği güvence altına alırken ve veri sızıntılarına karşı korumayı artırarak son kullanıcıya sorunsuz bir deneyim sunmak için bulut hizmetleriyle etkileşime girebilmelidir. 

Aşağıdaki şekilde gösterildiği gibi, içerik yönetimi sağlamak için kimlik çözümünüz farklı teknik denetimlerle yararlanılabilir olacaktır:

![Güvenlik denetimleri](./media/plan-hybrid-identity-design-considerations/securitycontrols.png)

**Kimlik yönetimi sisteminizi kullanacak güvenlik denetimleri**

Genellikle, içerik yönetimi gereksinimleri aşağıdaki alanlarda kimlik yönetimi sisteminizden yararlanır:

* Gizlilik: bir kaynağa sahip olan kullanıcıyı tanımlama ve bütünlüğü sürdürmek için uygun denetimleri uygulama.
* Veri sınıflandırması: sınıflandırmasına göre bir nesneye kullanıcı veya grup ve erişim düzeyini belirler. 
* Veri sızıntısı koruması: kullanıcının kimliğini doğrulamak için, verilerin korunmasından sorumlu olan güvenlik denetimleri kimlik sistemiyle etkileşmek zorunda kalır. Bu, denetim izi amacı için de önemlidir.

> [!NOTE]
> En iyi uygulamalar ve veri sınıflandırması yönergeleri hakkında daha fazla bilgi için [bulut hazırlığı için veri sınıflandırmasını](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) okuyun.
> 
> 

Karma kimlik çözümünüzü planlarken, kuruluşunuzun gereksinimlerine göre aşağıdaki soruların yanıtlanmasını sağlayın:

* Şirketiniz veri gizliliğini zorlamak için güvenlik denetimlerine sahip mi?
  * Yanıt Evet ise, güvenlik denetimleri benimsemek istediğiniz karma kimlik çözümüyle tümleştirilebilir mi?
* Şirketiniz veri sınıflandırması kullanıyor mu?
  * Yanıt Evet ise, geçerli çözüm benimsemek istediğiniz karma kimlik çözümüyle tümleştirilebiliyor mu?
* Şirketiniz Şu anda veri sızıntısı için herhangi bir çözüme sahip mi? 
  * Yanıt Evet ise, geçerli çözüm benimsemek istediğiniz karma kimlik çözümüyle tümleştirilebiliyor mu?
* Şirketinizin kaynaklara erişimi denetlemesi gerekiyor mu?
  * Yanıt Evet ise, ne tür kaynaklar?
  * Yanıt Evet ise, hangi bilgi düzeyi gereklidir?
  * Yanıt Evet ise, denetim günlüğü nerede bulunmalıdır? Şirket içinde mi yoksa bulutta mı?
* Şirketinizin gizli veriler (SSNs, kredi kartı numaraları vb.) içeren e-postaları şifrelemesine ihtiyacı var mı?
* Şirketinizin dış iş ortaklarıyla paylaşılan tüm belgelerini/içeriğini şifrelemesine ihtiyacı var mı?
* Şirketinizin, belirli e-posta türlerinde şirket ilkelerini zorlaması gerekiyor mu (hiç yanıt verme, iletmeyin)?

> [!NOTE]
> Her yanıtı not almayı unutmayın ve yanıtın arkasındaki mantığı anladığınızdan emin olun. [Veri koruma stratejisini tanımlama](plan-hybrid-identity-design-considerations-data-protection-strategy.md) , kullanılabilir seçenekler ve her seçeneğin olumlu/olumsuz yönleri üzerinden alınacaktır.  Bu sorulara yanıt vererek, iş gereksinimlerinize en uygun seçeneği seçersiniz.
> 
> 

## <a name="next-steps"></a>Sonraki adımlar
[Erişim denetimi gereksinimlerini belirleme](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Ayrıca Bkz.
[Tasarım konularına genel bakış](plan-hybrid-identity-design-considerations-overview.md)
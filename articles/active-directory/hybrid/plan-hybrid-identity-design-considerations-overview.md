---
title: Azure Active Directory karma kimlik tasarımı konuları-genel bakış | Microsoft Docs
description: Karma kimlik tasarımı konuları kılavuzunun genel bakış ve içerik haritası
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 100509c4-0b83-4207-90c8-549ba8372cf7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7f8dd49f3668b8f68753681123a04d21edac46c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997741"
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Azure Active Directory Karma Kimlik Tasarımı ile İlgili Dikkat Edilmesi Gerekenler
Tüketici tabanlı cihazlar şirket dünyasını ve bulut tabanlı hizmet olarak yazılım (SaaS) uygulamalarının kolayca benimsenmesini sağlar. Sonuç olarak, kullanıcıların iç veri merkezleri ve bulut platformları genelinde uygulama erişiminin denetimini sürdürmek zor olur.  

Microsoft’un kimlik çözümleri şirket içi ve bulut tabanlı çözümleri birleştirerek konumdan bağımsız olarak tüm kaynaklara kimlik doğrulaması ve yetkilendirme sağlamak üzere tek bir kullanıcı kimliği oluşturur. Bu kavram karma kimlik olarak bilinir. Microsoft çözümlerini kullanarak hibrit kimlik için farklı tasarım ve yapılandırma seçenekleri vardır ve bazı durumlarda, kuruluşunuzun ihtiyaçlarını en iyi şekilde karşılayabilecek birleşimi belirlemek zor olabilir. 

Bu karma kimlik tasarımı hakkında dikkat edilecek noktalar kılavuzu, kuruluşunuzun iş ve teknoloji gereksinimlerine en uygun bir karma kimlik çözümünün nasıl tasarlanacağını anlamanıza yardımcı olur.  Bu kılavuzda, kuruluşunuzun benzersiz gereksinimlerini karşılayan bir karma kimlik çözümü tasarlamanıza yardımcı olması için izleyebileceğiniz bir dizi adım ve görevin ayrıntıları yer alabilir. Adımlar ve görevler boyunca, kılavuz işlev ve hizmet kalitesi (kullanılabilirlik, ölçeklenebilirlik, performans, yönetilebilirlik ve güvenlik gibi) düzey gereksinimlerini karşılamak için kuruluşların kullanabileceği ilgili teknolojiler ve özellik seçeneklerini sunar. 

Karma kimlik tasarımı hakkında dikkat edilecek noktalar kılavuzu amaçları, özellikle aşağıdaki soruları yanıtlamaktır: 

* Gereksinimlerimi en iyi şekilde karşılayan bir teknoloji veya sorun etki alanı için karma kimliğe özgü bir tasarımı ister yanıtlaması gerekir?
* Teknoloji veya sorun etki alanı için bir karma kimlik çözümü tasarlamak üzere hangi etkinlik dizisini tamamlamam gerekir? 
* Gereksinimlerimi karşılamaktan yardım etmek için hangi karma kimlik teknolojisi ve yapılandırma seçenekleri mevcuttur? İşletmem için en iyi seçeneği belirleyebilmem için bu seçeneklerle ilgili denge nedir?

## <a name="who-is-this-guide-intended-for"></a>Bu kılavuz kime yöneliktir?
 Orta veya büyük kuruluşlar için karma kimlik çözümü tasarlamaktan sorumlu olan CIO, CıTO, kimlik mimarları, kuruluş mimarları ve BT mimarları.

## <a name="how-can-this-guide-help-you"></a>Bu kılavuz size nasıl yardımcı olabilir?
Bu kılavuzu, bulut tabanlı bir kimlik yönetimi sistemini mevcut şirket içi kimlik çözümünüzle tümleştirebilecek bir karma kimlik çözümünün nasıl tasarlanacağını anlamak için kullanabilirsiniz. 

Aşağıdaki grafikte, BT yöneticilerinin bulutta ve şirket içinde bulunan uygulamalar arasında tek Sign-On (SSO) kullanmasına olanak tanımak için BT yöneticilerinin, şirket içinde bulunan geçerli Windows Server Active Directory çözümünü Microsoft Azure Active Directory ile tümleştirmesini sağlayan bir karma kimlik çözümü gösterilmektedir.

![Örnek](media/plan-hybrid-identity-design-considerations/hybridID-example.png)

Yukarıdaki şekil, son kullanıcı kimlik doğrulama sürecine tek bir deneyim sağlamak ve bu kaynakları yönetmeyi kolaylaştırmak amacıyla Şirket içi yetenekler ile tümleşecek bulut hizmetlerinden yararlanan bir karma kimlik çözümü örneğidir. Bu örnek yaygın bir senaryo olsa da, farklı gereksinimler nedeniyle her kuruluşun karma kimlik tasarımı Şekil 1 ' de gösterilen örnekle farklı olabilir. 

Bu kılavuz, kuruluşunuzun benzersiz gereksinimlerini karşılayan bir karma kimlik çözümü tasarlamak için izleyebileceğiniz bir dizi adım ve görev sağlar. Aşağıdaki adımlar ve görevler boyunca kılavuzda, kuruluşunuzun işlevsel ve hizmet kalitesi düzeyi gereksinimlerini karşılamanız için kullanabileceğiniz ilgili teknolojiler ve özellik seçenekleri sunulmaktadır.

**Varsayımlar**: Windows Server, Active Directory Domain Services ve Azure Active Directory ile ilgili bazı deneyimimiz vardır. Bu belgede, bu çözümlerin kendi kendine veya tümleşik bir çözümde iş gereksinimlerinizi nasıl karşılayacağınızla ilgili olduğunu varsaymaktadır.

## <a name="design-considerations-overview"></a>Tasarım konularına genel bakış
Bu belge, gereksinimlerinizi en iyi şekilde karşılayan bir karma kimlik çözümü tasarlamak için izleyebileceğiniz bir dizi adım ve görev sağlar. Adımlar sıralı halde verilmiştir. Daha sonraki adımlarda öğrenirken göz önünde bulundurmanız gereken tasarım konuları, ancak çakışan tasarım seçimlerine bağlı olarak, önceki adımlarda yaptığınız kararları değiştirmenizi gerektirebilir. Her denemede, belge genelinde olası tasarım çakışmaları konusunda sizi uyarmak için her girişimde bulunuldu. 

Gereksinimlerinizi en iyi şekilde karşılayan tasarıma, yalnızca adımlarda, belgede yer alan tüm konuları dahil etmek için gereken birkaç kez yineleme yaptıktan sonra ulaşmanız gerekir. 

| Karma kimlik aşaması | Konu listesi |
| --- | --- |
| Kimlik gereksinimlerini belirleme |[İş ihtiyaçlarını belirleme](plan-hybrid-identity-design-considerations-business-needs.md)<br> [Dizin eşitleme gereksinimlerini belirleme](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Multi-Factor Authentication gereksinimlerini belirleme](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Karma kimlik benimseme stratejisi tanımlama](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| Güçlü kimlik çözümü aracılığıyla veri güvenliğini geliştirmek için plan yapın |[Veri koruma gereksinimlerini belirleme](plan-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [İçerik yönetimi gereksinimlerini belirleme](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Erişim denetimi gereksinimlerini belirleme](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Olay yanıtı gereksinimlerini belirleme](plan-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Veri koruma stratejisini tanımlama](plan-hybrid-identity-design-considerations-data-protection-strategy.md) |
| Hibrit kimlik yaşam döngüsünü planlama |[Karma kimlik yönetimi görevlerini belirleme](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Eşitleme yönetimi](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Karma kimlik yönetimi benimseme stratejisini belirleme](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="next-steps"></a>Sonraki Adımlar
[Kimlik gereksinimlerini belirleme](plan-hybrid-identity-design-considerations-business-needs.md)


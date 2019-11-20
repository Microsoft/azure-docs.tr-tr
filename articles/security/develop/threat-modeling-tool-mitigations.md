---
title: Azaltmaları-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: En çok oluşturulan tehditler için olası çözümleri vurgulayan Microsoft Threat Modeling Tool için azaltma sayfası.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 748d10b994080b667885e5d0d5f4d688269e86ab
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728041"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Microsoft Threat Modeling Tool azaltmaları

Threat Modeling Tool, Microsoft güvenlik geliştirme yaşam döngüsü 'nin (SDL) temel bir öğesidir. Yazılım mimarlarının olası güvenlik sorunlarını daha erken belirlemesine ve hafifletmesine ve bu sorunların çözülmesi için yüksek maliyetli olmasına olanak sağlar. Sonuç olarak, toplam geliştirme maliyetini büyük ölçüde azaltır. Ayrıca, tehdidi güvenlik dışı uzmanlar ile tasarlıyoruz, tehdit modellerini oluşturma ve çözümleme konusunda açık yönergeler sunarak tüm geliştiriciler için tehdit modellemesini kolaylaştırın.

Kullanmaya başlamak için **[Threat Modeling Tool](threat-modeling-tool.md)** ziyaret edin!

## <a name="mitigation-categories"></a>Risk azaltma kategorileri

Threat Modeling Tool azaltmaları, aşağıdakilerden oluşan Web uygulaması güvenlik çerçevesine göre sınıflandırılırdı:

| Category | Açıklama |
| -------- | ----------- |
| **[Denetleme ve günlüğe kaydetme](threat-modeling-tool-auditing-and-logging.md)** | Kim ve ne zaman? Denetim ve günlük kaydı, uygulamanızın güvenlikle ilgili olayları nasıl kayıtlarına başvurur |
| **[Yetkilendirmesi](threat-modeling-tool-authentication.md)** | Kimsiniz? Kimlik doğrulaması, bir varlığın, genellikle Kullanıcı adı ve parola gibi kimlik bilgileri aracılığıyla başka bir varlığın kimliğini kanıtlayan işlemdir |
| **[Yetkisi](threat-modeling-tool-authorization.md)** | Ne yapabilirsiniz? Yetkilendirme, uygulamanızın kaynaklar ve işlemler için erişim denetimleri sağladığı bir uygulamadır |
| **[İletişim güvenliği](threat-modeling-tool-communication-security.md)** | Kimler konuşuyor? İletişim güvenliği, yapılan tüm iletişimin olabildiğince güvenli olmasını sağlar |
| **[Yapılandırma yönetimi](threat-modeling-tool-configuration-management.md)** | Uygulamanız ne gibi çalışıyor? Hangi veritabanlarına bağlanır? Uygulamanız nasıl yönetilir? Bu ayarların güvenliği nasıl sağlanır? Yapılandırma yönetimi, uygulamanızın bu işlem sorunlarını nasıl işleyeceğini belirtir |
| **[To](threat-modeling-tool-cryptography.md)** | Gizli dizileri (Gizlilik) nasıl saklıyorsunuz? Verilerinizi veya kitaplıklarınızı (bütünlük) nasıl oynayabilir? Şifreleme açısından güçlü olması gereken rastgele değerler için çekirdekler nasıl sağlanır? Şifreleme, uygulamanızın gizliliği ve bütünlüğü nasıl zorladığı anlamına gelir |
| **[Özel durum yönetimi](threat-modeling-tool-exception-management.md)** | Uygulamanızdaki bir yöntem çağrısı başarısız olursa, uygulamanız ne olur? Ne kadar açığa çıkar? Son kullanıcılara kolay hata bilgileri döndürüyor musunuz? Değerli özel durum bilgilerini çağırana geri iletmeniz mı? Uygulamanız düzgün şekilde başarısız mı? |
| **[Giriş doğrulaması](threat-modeling-tool-input-validation.md)** | Uygulamanızın alacağı girişin geçerli ve güvenli olduğunu nasıl anlarsınız? Giriş doğrulaması, uygulamanızın ek işlemeden önce girişi nasıl filtreleyeceğini, itilen veya reddettiğini belirtir. Giriş noktaları aracılığıyla girişi kısıtlama ve çıkış noktaları aracılığıyla kodlama çıkışı yapmayı düşünün. Veritabanları ve dosya paylaşımları gibi kaynaklardaki verilere güveniyor musunuz? |
| **[Hassas veriler](threat-modeling-tool-sensitive-data.md)** | Uygulamanız hassas verileri nasıl işler? Hassas veriler, uygulamanızın bellekte, ağ üzerinden veya kalıcı depolarda korunması gereken tüm verileri nasıl işlediğini ifade eder |
| **[Oturum yönetimi](threat-modeling-tool-session-management.md)** | Uygulamanız kullanıcı oturumlarını nasıl işler ve korur? Oturum, bir Kullanıcı ve Web uygulamanız arasındaki bir dizi ilgili etkileşimi ifade eder |

Bu, şunları belirlemenize yardımcı olur:

* En yaygın hatalar nerede yapılır
* En iyi işlem yapılabilir geliştirmeler nerede

Sonuç olarak, giriş doğrulaması, kimlik doğrulama ve yetkilendirme kategorilerinde en yaygın güvenlik sorunlarının gerçekleşeceğinizi biliyorsanız, buradan başladıysanız, güvenlik çalışmalarınızı odaklamak ve önceliklendirmek için bu kategorileri kullanırsınız. Daha fazla bilgi için  **[Bu patent bağlantısını](https://www.google.com/patents/US7818788) ziyaret edin**

## <a name="next-steps"></a>Sonraki adımlar

Aracın olası tasarım tehditleri oluşturmak için kullandığı tehdit kategorileri hakkında daha fazla bilgi edinmek için **[Threat Modeling Tool tehditleri](threat-modeling-tool-threats.md)** ziyaret edin.
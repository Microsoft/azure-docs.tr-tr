---
title: Bölge kullanılabilirliği ve veri yerleşikliği
titleSuffix: Azure AD B2C
description: Bölge kullanılabilirliği, veri yerleşimi ve Azure Active Directory B2C Preview kiracılar hakkında bilgiler.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/31/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: references_regions
ms.openlocfilehash: 6e724b3517d9e5a63d8699e9f66c51cf41f02012
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106092526"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: bölge kullanılabilirliği & veri yerleşimi

Bölge kullanılabilirliği ve veri yerleşimi, Azure 'un geri kalanından Azure AD B2C farklı şekilde uygulanan iki çok farklı kavramlardır. Bu makalede, bu iki kavram arasındaki farklar açıklanmakta ve bunların Azure için nasıl uygulandığı karşılaştırılır Azure AD B2C karşılaştırması açıklanmıştır.

Azure AD B2C, **Dünya çapında** **Birleşik Devletler, Avrupa veya Asya Pasifik** **veri** yerleşimi seçeneği ile birlikte kullanılabilir. Azure AD B2C, Avustralya 'da **genel önizlemededir** .

[Bölge kullanılabilirliği](#region-availability) , bir hizmetin kullanıma hazır olduğu yeri belirtir.

[Veri](#data-residency) yerleşimi, Kullanıcı verilerinin nerede depolanabileceğini belirtir.

## <a name="region-availability"></a>Bölge kullanılabilirliği

Azure AD B2C Azure genel bulutu aracılığıyla dünya çapında kullanılabilir.

Bu, modelden ve sonrasında, genellikle *veri* yerleşimi ile birkaç *kullanılabilirlik* sağlayan diğer Azure hizmetlerinden farklıdır. Bunun örneklerini [bölgeye göre Azure ürünlerinin](https://azure.microsoft.com/regions/services/) yanı sıra [Active Directory B2C Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/details/active-directory-b2c/)' da görebilirsiniz.

## <a name="data-residency"></a>Veri yerleşimi

Azure AD B2C, Kullanıcı verilerini Birleşik Devletler, Avrupa veya Asya Pasifik bölgesinde depolar.

Veri yerleşimi, [Azure AD B2C kiracı oluştururken](tutorial-create-tenant.md)seçtiğiniz ülke/bölge tarafından belirlenir:

![Bir kiracı oluştur formunun, ülke veya bölge seçme ekran görüntüsü.](./media/data-residency/data-residency-b2c-tenant.png)

Veriler aşağıdaki ülkeler/bölgeler için **Birleşik Devletler** bulunur:

> Birleşik Devletler (ABD), Kanada (CA), Kosta Rika (CR), Dominik Cumhuriyeti (DO), El Salvador (ZF), Guatemala (GT), Meksika (MX), Panama (PA), Porto Riko (PR) ve Trinidad & Tobago (TT)

Veriler aşağıdaki ülkeler/bölgeler için **Avrupa** 'da bulunur:

> Cezayir (DZ), Avusturya (AT), Azerbaycan (az), Bahreyn (BH), Belarus (BY), Belçika (BE), Bulgaristan (BG), Hırvatistan (HR), Kıbrıs (CY), Çek Cumhuriyeti (CZ), Danimarka (DK), Mısır (örn., Finlandiya (EE), Finlandiya (FT), Fransa (FR), Almanya (DE), Yunanistan (GR), Macaristan (HU), Voiceland (yanı), Irlanda (IE), Israil (IL), Italya (It), Ürdün (JO), Kazakistan (KZ), Kenya (KE), Kuveyt (KW), Litvanya (LV), Lübnan (LB), Liechtenstein (LI), Litvanya (LT) , Lüksemburg (LU), Kuzey Makedonya (ML), Malta (MT), Karadağ (ME), Fas (MA), Hollanda (NL), Nijerya (&), Norveç (NO), Umman (OM), Pakistan (PK), Polonya (PL), Portekiz (PT), Qtor (QA), Romanya (RO), Rusya (RU), Suudi Arabistan (SA), Sırbistan (RS), Slovakya (SK), Slovenya (ST), Güney Afrika (ZA), Ispanya (ES), Isveç (VAG), Isviçre (CH), Tunus (TN), Türkiye (TR), Ukrayna (UA), Birleşik Arap Emirlikleri (AE) ve Birleşik Krallık (GB)

Veriler aşağıdaki ülkeler/bölgeler için **Asya Pasifik** bulunur:

> Afganistan (AF), Hong Kong ÖIB (HK), Hindistan (ın), Endonezya (ID), Japonya (JP), Kore (KR), Malezya (MY), Filipinler (PH), Singapur (SG), Sri Lanka (LK), Tayvan (TW) ve Tayland (TH)

Veriler, aşağıdaki ülkeler/bölgeler için **Avustralya** 'da (Önizleme) bulunur:

> Avustralya ve Yeni Zelanda

Aşağıdaki ülkeler/bölgeler listeye eklenmekte. Şimdilik, Yukarıdaki ülkelerin/bölgelerin birini seçerek Azure AD B2C kullanmaya devam edebilirsiniz.

> Arjantin, Brezilya, Şili, Kolombiya, Ekvador, Irak, Paraguay, Peru, Uruguay ve Venezuela

## <a name="remote-profile-solution"></a>Uzak profil çözümü

[Özel ilkeler](custom-policy-overview.md)Azure AD B2C, bir uzak veritabanından (bir pazarlama VERITABANı, CRM sistemi veya herhangi bir iş kolu uygulaması) Kullanıcı profillerini depolamanızı ve okumanızı sağlayan, [RESTANY API hizmetleriyle](custom-policy-rest-api-intro.md)tümleştirilebilir.  
- Kaydolma ve profil düzenlemesi sırasında, Azure AD B2C kullanıcı profilini uzak veri kaynağına kalıcı hale getirmek için özel bir REST API çağırır. Kullanıcının kimlik bilgileri Azure AD B2C dizininde depolanır. 
- Oturum açma sırasında, yerel veya sosyal hesapla kimlik bilgileri doğrulamasından sonra, Azure AD B2C kullanıcının benzersiz tanımlayıcısını Kullanıcı birincil anahtarı (e-posta adresi veya Kullanıcı ObjectID) olarak gönderen REST API çağırır. REST API, uzak veritabanındaki verileri okur ve kullanıcı profilini döndürür.  

Kaydolma, profil düzenlemesi veya oturum açma tamamlandıktan sonra, Azure AD B2C uygulamaya döndürülen erişim belirtecindeki kullanıcı profilini içerir. Daha fazla bilgi için GitHub 'daki [Azure AD B2C uzak profil örnek çözümü](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) konusuna bakın.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD B2C kiracı oluşturun](tutorial-create-tenant.md).

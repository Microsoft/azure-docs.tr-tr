---
title: Bölgenin kullanılabilirliği & veri yerleşimi Azure Active Directory B2C
description: Azure Active Directory B2C kiracının türleri hakkında bir konu.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0bb7a7ef2aabd7f93531d9afa662d7e582e05550
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273576"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: Bölge kullanılabilirliği ve veri yerleşikliği

Bölge kullanılabilirliği ve veri yerleşimi, Azure 'un geri kalanından Azure AD B2C farklı şekilde uygulanan iki çok farklı kavramlardır. Bu makalede, bu iki kavram arasındaki farklar açıklanmakta ve bunların Azure için nasıl uygulandığı karşılaştırılır Azure AD B2C karşılaştırması açıklanmıştır.

Azure AD B2C, **Dünya çapında** **Birleşik Devletler, Avrupa veya Asya Pasifik** **veri** yerleşimi seçeneği ile birlikte kullanılabilir.

[Bölge kullanılabilirliği](#region-availability) , bir hizmetin kullanıma hazır olduğu yeri belirtir.

[Veri](#data-residency) yerleşimi, Kullanıcı verilerinin nerede depolanabileceğini belirtir.

## <a name="region-availability"></a>Bölge kullanılabilirliği

Azure AD B2C Azure genel bulutu aracılığıyla dünya çapında kullanılabilir.

Bu, modelden ve sonrasında, genellikle *veri*yerleşimi ile birkaç *kullanılabilirlik* sağlayan diğer Azure hizmetlerinden farklıdır. Bunun örneklerini [bölgeye göre Azure ürünlerinin](https://azure.microsoft.com/regions/services/) yanı sıra [Active Directory B2C Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/details/active-directory-b2c/)' da görebilirsiniz.

## <a name="data-residency"></a>Veri yerleşikliği

Azure AD B2C, Kullanıcı verilerini Birleşik Devletler, Avrupa veya Asya Pasifik bölgesinde depolar.

Veri yerleşimi, [Azure AD B2C kiracı oluştururken](active-directory-b2c-get-started.md)seçtiğiniz ülke/bölge tarafından belirlenir:

![Önizleme kiracısı ekran görüntüsü](./media/active-directory-b2c-reference-tenant-type/data-residency-b2c-tenant.png)

Veriler aşağıdaki ülkeler/bölgeler için **Birleşik Devletler** bulunur:

> Birleşik Devletler, Kanada, Kosta Rika, Dominik Cumhuriyeti, El Salvador, Guatemala, Meksika, Panama, Porto Riko ve Trinidad & Tobago

Veriler aşağıdaki ülkeler/bölgeler için **Avrupa** 'da bulunur:

> Cezayir, Avusturya, Azerbaycan, Bahreyn, Belarus, Belçika, Bulgaristan, Hırvatistan, Kıbrıs, Çek Cumhuriyeti, Danimarka, Mısır, Estonya, Finlandiya, Fransa, Almanya, Yunanistan, Finlandiya Liechtenstein, Litvanya, Lüksemburg, Kuzey Makedonya, Malta, Karadağ, Fas, Hollanda, Nijerya, Norveç, Umman, Pakistan, Polonya, Portekiz, Qtor, Romanya, Rusya, Suudi Arabistan, Sırbistan, Slovakya, Slovenya, Güney Afrika, Ispanya, Isveç, İsviçre, Tunus, Türkiye, Ukrayna, Birleşik Arap Emirlikleri ve Birleşik Krallık.

Veriler aşağıdaki ülkeler/bölgeler için **Asya Pasifik** bulunur:

> Afganistan, Hong Kong ÖİB, Hindistan, Endonezya, Japonya, Kore, Malezya, Filipinler, Singapur, Sri Lanka, Tayvan ve Tayland.

Aşağıdaki ülkeler/bölgeler listeye eklenmekte. Şimdilik, Yukarıdaki ülkelerin/bölgelerin birini seçerek Azure AD B2C kullanmaya devam edebilirsiniz.

> Arjantin, Avustralya, Brezilya, Şili, Kolombiya, Ekvador, Irak, Yeni Zelanda, Paraguay, Peru, Uruguay ve Venezuela.

## <a name="preview-tenant"></a>Önizleme kiracısı

Azure AD B2C's önizleme döneminde bir B2C kiracısı oluşturduysanız, büyük olasılıkla **kiracı türü** **Önizleme kiracısı**yazıyor.

Bu durumda, kiracınızı yalnızca geliştirme ve test amaçlarıyla kullanmanız gerekir. Üretim uygulamaları için bir önizleme kiracısı kullanmayın.

Önizleme B2C kiracısından üretim ölçeğinde B2C kiracısına **geçiş yolu** yoktur. Üretim uygulamalarınız için yeni bir B2C kiracısı oluşturmanız gerekir.

Bir önizleme B2C kiracısının silineceği ve aynı etki alanı adıyla bir üretim ölçeğinde B2C kiracısı oluşturabileceğiniz bilinen sorunlar vardır. *Farklı bir etki alanı adına sahip bir üretim ÖLÇEĞINDE B2C kiracısı oluşturmanız gerekir*.

![Önizleme kiracısı ekran görüntüsü](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)
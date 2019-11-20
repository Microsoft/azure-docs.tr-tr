---
title: Kişisel bilgiler için adlandırılmış varlıklar
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/21/2019
ms.author: aahi
ms.openlocfilehash: 3aa4da9a9cf3d1d4b664e81f1fd18f2b225d731d
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73800203"
---
## <a name="personal-information-entity-types"></a>Kişisel bilgi varlık türleri:

### <a name="phone-number"></a>Telefon Numarası

Telefon numaraları. 

Diller:

* Genel Önizleme: `English`

| Alt tür adı           | Açıklama                                           |
|------------------------|-------------------------------------------------------|
| Yok                    | Telefon numaraları, örneğin `+1 123-123-123`.          |
| AB telefon numarası        | Avrupa Birliği 'ne özgü telefon numaraları.         |
| AB cep telefonu numarası | Avrupa Birliği 'ne özgü cep telefonu numaraları. |

### <a name="eu-gps-coordinates"></a>AB GPS koordinatları

 Avrupa Birliği içindeki konumların GPS koordinatları. 

Diller:

* Genel Önizleme: `English`

| Alt tür adı | Açıklama                               |
|--------------|-------------------------------------------|
| Yok          | Avrupa Birliği içindeki GPS koordinatları |

### <a name="azure-information"></a>Azure bilgileri

Kimlik doğrulama bilgileri ve bağlantı dizeleri dahil olmak üzere tanımlanabilir Azure bilgileri. 

Diller:

* Genel Önizleme: `English`

| Alt tür adı                          | Açıklama                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Azure DocumentDB kimlik doğrulama anahtarı             | Azure DocumentDB sunucusu için yetkilendirme anahtarı.                           |
| Azure ıAAS veritabanı bağlantı dizesi | Bir Azure hizmet olarak altyapı (IaaS) veritabanı için bağlantı dizesi. |
| Azure SQL bağlantı dizesi           | Azure SQL veritabanı için bağlantı dizesi.                                |
| Azure IoT bağlantı dizesi           | Azure nesnelerin Interneti (IoT) için bağlantı dizesi.                        |
| Azure yayımlama ayarı parolası        | Azure yayımlama ayarları için parola.                                        |
| Azure Redis Cache bağlantı dizesi   | Redsıs için bir Azure önbelleği bağlantı dizesi.                             |
| Azure SAS                             | Hizmet olarak Azure yazılım (SAS) için bağlantı dizesi.                     |
| Azure Service Bus bağlantı dizesi   | Azure Service Bus için bağlantı dizesi.                                |
| Azure depolama hesabı anahtarı             | Bir Azure depolama hesabı için hesap anahtarı.                                   |
| Azure depolama hesabı anahtarı (genel)   | Bir Azure depolama hesabı için genel hesap anahtarı.                           |
| SQL Server bağlantı dizesi          | Bir SQL Server için bağlantı dizesi.                                         |

### <a name="identification"></a>İsi

Diller:

* Genel Önizleme: `English`

#### <a name="financial-account-identification"></a>Finansal hesap kimliği

| Alt tür adı               | Açıklama                                                                |
|----------------------------|----------------------------------------------------------------------------|
| ABA yönlendirme numaraları        | American banker Association (ABA) transit yönlendirme numaraları.                  |
| SWIFT kodu                 | Ödeme yönergesi bilgileri için SWIFT kodları.                           |
| Kredi Kartı                | Kredi kartı numaraları.                                                       |
| IBAN kodu                  | Ödeme yönergesi bilgileri için ıBAN kodları.                            |

#### <a name="government-and-country-specific-identification"></a>Kamu ve ülkeye özgü kimlik

Aşağıdaki varlıklar, ülkeye göre gruplandırılır ve listelenir:

Arjantin
* Ulusal kimlik (DNı) numarası

Avustralya
* Vergi dosya numarası 
* Sürücünün lisans KIMLIĞI
* Passport KIMLIĞI
* Tıbbi hesap numarası
* Banka hesap numaraları (örneğin, denetim, tasarruf ve borç hesapları)

Belçika
* Ulusal numara

Brezilya
* Yasal varlık numarası (CNPJ)
* CPF numarası
* Ulusal KIMLIK kartı (RG)

Kanada
* Passport KIMLIĞI
* Sürücünün lisans KIMLIĞI
* Sağlık sigortası numarası
* Kişisel sağlık KIMLIĞI numarası (PHIN)
* Sosyal güvenlik numarası
* Banka hesap numaraları (örneğin, denetim, tasarruf ve borç hesapları)

Şili
* Kimlik kartı numarası 

Çin
* Kimlik kartı numarası
* Yerleşik KIMLIK kartı (ÇHC) numarası

Hırvatistan
* KIMLIK kartı numarası
* Kişisel KIMLIK (OıB) numarası

Çek Cumhuriyeti
* Ulusal KIMLIK kartı numarası

Danimarka
* Kişisel KIMLIK numarası

Avrupa Birliği (AB)
* Ulusal KIMLIK numarası
* Passport KIMLIĞI
* Sürücünün lisans KIMLIĞI
* Sosyal güvenlik numarası (SSN) veya eşdeğer KIMLIK
* AB vergi kimlik numarası (TIN)
* AB banka kartı numarası

Finlandiya
* Ulusal KIMLIK numarası
* Passport KIMLIĞI

Fransa
* Ulusal KIMLIK kartı (CNı)
* Sosyal güvenlik numarası (ıNSEE)
* Passport KIMLIĞI
* Sürücünün lisans KIMLIĞI

Almanya
* KIMLIK kartı numarası
* Passport KIMLIĞI
* Sürücünün lisans KIMLIĞI

Yunanistan 
* Ulusal KIMLIK kartı numarası

Hong Kong
* KIMLIK kartı (HKıD) numarası

Hindistan
* Kalıcı hesap numarası (PAN)
* Benzersiz KIMLIK (aadhaar) numarası

Endonezya
* KIMLIK kartı numarası (KTP)

İrlanda
* Kişisel genel hizmet (PPS) numarası

İsrail
* Ulusal KIMLIK
* Banka hesap numaraları (örneğin, denetim, tasarruf ve borç hesapları)

İtalya
* Sürücünün lisans KIMLIĞI

Japonya
* Yerleşik kayıt numarası
* İkamet kartı numarası
* Sürücünün lisans KIMLIĞI
* Sosyal sigorta numarası (SIN)
* Passport KIMLIĞI
* Banka hesap numaraları (örneğin, denetim, tasarruf ve borç hesapları)

Malezya
* KIMLIK kartı numarası

Hollanda
* Vatandaşlık hizmeti (BSN) numarası

Yeni Zelanda
* Sağlık numarası için minbakanlığı

Norveç
* KIMLIK kartı numarası

Filipinler
* Birleşik çok amaçlı KIMLIK numarası

Polonya
* KIMLIK kartı numarası
* Ulusal KIMLIK (PESEL)
* Passport KIMLIĞI

Portekiz 
* Vatandaşlık kartı numarası

Suudi Arabistan
* Ulusal KIMLIK

Singapur
* Ulusal kayıt KIMLIĞI kartı (NRIC) numarası

Güney Afrika
* KIMLIK numarası
* Yerleşik kayıt numarası

Güney Kore
* Yerleşik kayıt numarası

İspanya 
* Sosyal güvenlik numarası (SSN)

İsveç
* Ulusal KIMLIK
* Passport KIMLIĞI

Tayvan 
* Ulusal KIMLIK
* Yerleşik sertifika (yay/TARC) numarası
* Passport KIMLIĞI

Tayland
* Popülasyon kimlik kodu

Birleşik Krallık
* Passport KIMLIĞI
* Sürücünün lisans KIMLIĞI
* Ulusal sigorta numarası (NINO)
* Ulusal Sistem Sağlığı Hizmeti (NHS) numarası

Amerika Birleşik Devletleri
* Sosyal güvenlik numarası (SSN)
* Sürücünün lisans KIMLIĞI
* Passport KIMLIĞI
* Elektrotoral toplama numarası
* Bireysel vergi KIMLIK numarası (ITIN)
* İlaç zorlama Kurumu (DEA) numarası
* Banka hesap numaraları (örneğin, denetim, tasarruf ve borç hesapları)

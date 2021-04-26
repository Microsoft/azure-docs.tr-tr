---
title: Desteklenen sınıflandırmaların listesi
description: Bu sayfa, Azure purview 'da desteklenen sistem sınıflandırmalarını listeler.
author: anmuk601
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: reference
ms.date: 4/1/2021
ms.openlocfilehash: e1d3d495d958465e966701aa7ce91bc2706b48e0
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219595"
---
# <a name="supported-classifications-in-azure-purview"></a>Azure purview 'da desteklenen sınıflandırmalar

Bu makalede, Azure purview (Önizleme) içinde desteklenen ve tanımlanan sistem sınıflandırmaları listelenmektedir.


- **Ayrı eşleşme eşiği**: tarayıcıdan veri modelini çalıştırmadan önce bir sütunda bulunması gereken ayrı veri değerlerinin toplam sayısı. DISTINCT eşleşme eşiğinin, model eşleştirme ile hiçbir şey yok, ancak bu, bir model eşleştirme için önkoşul. Sistem sınıflandırma kurallarımız, her sütunda sınıflandırılmaları için en az 8 farklı değer olmasını gerektirir. Bu değer, sütunun tarayıcıya doğru bir şekilde sınıflandırmasına yetecek kadar veri içerdiğinden emin olmak için bu değeri gerektirir. Örneğin, 1 değerini içeren birden çok satır içeren bir sütun sınıflandırılmayacaktır. Değer içeren bir satır içeren sütunlar ve satırların geri kalanı null değerleri de sınıflandırılmayacaktır. Birden çok desen belirtirseniz bu değer bunların her biri için geçerlidir.

- **En küçük eşleşme eşiği**: sınıflandırmanın uygulanması için tarayıcı tarafından bulunması gereken bir sütundaki veri değeri eşleştirmelerinin en düşük yüzdesidir. Sistem sınıflandırma değeri %60 olarak ayarlanır.


## <a name="defined-system-classifications"></a>Tanımlı sistem sınıflandırmaları

Azure purview, verileri [Regex](https://wikipedia.org/wiki/Regular_expression) ve [Bloom filtresine](https://wikipedia.org/wiki/Bloom_filter)göre sınıflandırır. Aşağıdaki listeler, Azure purview tanımlı sistem sınıflandırmalarının biçimini, modelini ve anahtar sözcüklerini anlatmaktadır.

Her sınıflandırma adı MICROSOFT tarafından ön yüklenir.

## <a name="bloom-filter-classifications"></a>Bloom filtre sınıflandırmaları

## <a name="city-country-and-place"></a>Şehir, ülke ve yer

Şehir, ülke ve yer filtreleri, verileri hazırlamak için kullanılabilecek en iyi veri kümeleri kullanılarak hazırlandı.

## <a name="person"></a>Kişi

Kişi Bloom filtresi, aşağıdaki iki veri kümesi kullanılarak hazırlandı.

- [Son adlarla ilgili 2010 ABD Census verileri (162-K girişleri)](https://www.census.gov/topics/population/genealogy/data/2010_surnames.html)
- [Tüm yılları kullanarak popüler bebek adları (SSN 'den) 1880-2019 (98-K girişleri)](https://www.ssa.gov/oact/babynames/limits.html)

## <a name="regex-classifications"></a>RegEx sınıflandırmaları

## <a name="aba-routing"></a>ABA Yönlendirme

### <a name="format"></a>Biçimlendir

Biçimlendirilmemiş veya biçimlendirilmemiş bir düzende olabilecek dokuz basamak

### <a name="pattern"></a>Desen

Miyor

- 0, 1, 2, 3, 6, 7 veya 8 ' den başlayarak dört basamak
- kısa çizgi
- dört basamak
- kısa çizgi
- bir basamak

Biçimlendirilmemiş: 0, 1, 2, 3, 6, 7 veya 8 ' den başlayarak dokuz ardışık basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_aba_routing"></a>Keyword_aba_routing

```
amba number
aba #
aba
abarouting#
abaroutingnumber
americanbankassociationrouting#
americanbankassociationroutingnumber
bank routing#
bankroutingnumber
routing #
routing no
routing number
routing transit number
routing#
RTN
```

## <a name="argentina-national-identity-dni-number"></a>Arjantin Ulusal kimlik (DNı) numarası

### <a name="format"></a>Biçimlendir

Dönemleri olan veya olmayan sekiz basamak

### <a name="pattern"></a>Desen

Sekiz basamak:

- iki basamak
- isteğe bağlı bir dönem
- üç basamak
- isteğe bağlı bir dönem
- üç basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_argentina_national_id"></a>Keyword_argentina_national_id

```
Argentina National Identity number
cedula
cédula
dni
documento nacional de identidad
documento número
documento numero
registro nacional de las personas
rnp
```

## <a name="australia-bank-account-number"></a>Avustralya banka hesap numarası

### <a name="format"></a>Biçimlendir

Bir banka durumu dal numarası ile veya olmayan altı ila 10 basamak

### <a name="pattern"></a>Desen

Hesap numarası altı ila 10 rakamdan oluşur.

Avustralya banka durumu dal numarası:

- üç basamak
- kısa çizgi
- üç basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_australia_bank_account_number"></a>Keyword_australia_bank_account_number

```
swift bank code
correspondent bank
base currency
usa account
holder address
bank address
information account
fund transfers
bank charges
bank details
banking information
full names
iaea
```

## <a name="australia-drivers-license-number"></a>Avustralya sürücüsünün lisans numarası

### <a name="format"></a>Biçimlendir
Dokuz harf ve rakam

### <a name="pattern"></a>Desen
Dokuz harf ve rakam:

- iki rakam veya harf (büyük/küçük harfe duyarlı değil)
- iki basamak
- Beş rakam veya harf (büyük/küçük harfe duyarlı değil)

VEYA

- bir-iki isteğe bağlı harf (büyük/küçük harfe duyarlı değil)
- dört-dokuz basamak

VEYA

- dokuz basamak veya harf (büyük/küçük harfe duyarlı değil)

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_australia_drivers_license_number"></a>Keyword_australia_drivers_license_number

```
international driving permits
australian automobile association
international driving permit
DriverLicence
DriverLicences
Driver Lic
Driver License
Driver Licenses
DriversLic
DriversLicence
DriversLicences
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
DriverLic#
DriverLics#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
```

#### <a name="keyword_australia_drivers_license_number_exclusions"></a>Keyword_australia_drivers_license_number_exclusions

```
aaa
DriverLicense
DriverLicenses
Driver License
Driver Licenses
DriversLicense
DriversLicenses
Drivers License
Drivers Licenses
Driver'License
Driver'Licenses
Driver' License
Driver' Licenses
Driver'sLicense
Driver'sLicenses
Driver's License
Driver's Licenses
DriverLicense#
DriverLicenses#
Driver License#
Driver Licenses#
DriversLicense#
DriversLicenses#
Drivers License#
Drivers Licenses#
Driver'License#
Driver'Licenses#
Driver' License#
Driver' Licenses#
Driver'sLicense#
Driver'sLicenses#
Driver's License#
Driver's Licenses#
```

## <a name="australian-medicare-number"></a>Avustralya Medicare numarası

### <a name="format"></a>Biçimlendir

10-11 basamak

### <a name="pattern"></a>Desen

10-11 basamak:

- ilk basamak 2-6 aralığındadır
- Dokuzuncu basamak bir denetim basamağına sahiptir
- Onuncu basamak, sorun basamağına
- on birinci basamağı (isteğe bağlı) tek sayıdır

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_australia_medicare_number"></a>Keyword_Australia_Medicare_Number

```
bank account details
medicare payments
mortgage account
bank payments
information branch
credit card loan
department of human services
local service
medicare
```

## <a name="australia-passport-number"></a>Avustralya Passport numarası

### <a name="format"></a>Biçimlendir

Yedi basamak izleyen bir harf

### <a name="pattern"></a>Desen

Bir harf (büyük/küçük harfe duyarlı değil) sonrasında yedi basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_passport"></a>Anahtar sözcük \_ Passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

#### <a name="keyword_australia_passport_number"></a>Anahtar kelime \_ Avustralya \_ Passport \_ numarası

```
passport
passport details
immigration and citizenship
commonwealth of australia
department of immigration
residential address
department of immigration and citizenship
visa
national identity card
passport number
travel document
issuing authority
```

## <a name="australia-tax-file-number"></a>Avustralya Vergi dosya numarası

### <a name="format"></a>Biçimlendir

sekiz-dokuz basamak

### <a name="pattern"></a>Desen

genellikle aşağıdaki gibi boşluklarla birlikte sunulan sekiz-dokuz basamak:

- üç basamak
- isteğe bağlı bir alan
- üç basamak
- isteğe bağlı bir alan
- Son basamağın bir denetim hanesi olduğu iki-üç basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_australia_tax_file_number"></a>Anahtar kelime \_ Avustralya \_ vergi \_ Dosya \_ numarası

```
australian business number
marginal tax rate
medicare levy
portfolio number
service veterans
withholding tax
individual tax return
tax file number
tfn
```

## <a name="belgium-national-number"></a>Belçika Ulusal numarası

### <a name="format"></a>Biçimlendir

11 basamak ve isteğe bağlı sınırlayıcılar

### <a name="pattern"></a>Desen

11 basamak artı sınırlayıcılar:

- altı basamaklı ve YY biçiminde iki isteğe bağlı nokta. Doğum tarihi için MM. DD
- Nokta, tire, boşluk ile isteğe bağlı bir sınırlayıcı
- üç sıralı basamak (Females için bile, males için tek)
- Nokta, tire, boşluk ile isteğe bağlı bir sınırlayıcı
- iki denetim basamağı

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_belgium_national_number"></a>Anahtar sözcük \_ Belçika \_ Ulusal \_ numarası

```
be lasting aantal
bnn#
bnn
carte d'identité
identifiant national
identifiantnational#
identificatie
identification
identifikation
identifikationsnummer
identifizierung
identité
identiteit
identiteitskaart
identity
inscription
national number
national register
national number#
national number
nif#
nif
numéro d'assuré
numéro de registre national
numéro de sécurité
numéro d'identification
numéro d'immatriculation
numéro national
numéronational#
personal ID number
personalausweis
personalidnumber#
registratie
registration
registrationsnumme
registrierung
social security number
ssn#
ssn
steuernummer
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="brazil-cpf-number"></a>Brezilya CPF numarası

### <a name="format"></a>Biçimlendir

bir denetim basamağı içeren ve formatlanabilir veya biçimlendirilmemiş olan 11 basamak

### <a name="pattern"></a>Desen

Miyor

- üç basamak
- bir nokta
- üç basamak
- bir nokta
- üç basamak
- kısa çizgi
- Denetim rakamları olan iki basamak

Biçimlendirilmemiş:

- en son iki basamağın denetim basamağının bulunduğu 11 basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_brazil_cpf"></a>\_Brezilya \_ CPF anahtar sözcüğü

```
CPF
Identification
Registration
Revenue
Cadastro de Pessoas Físicas
Imposto
Identificação
Inscrição
Receita
```

## <a name="brazil-legal-entity-number-cnpj"></a>Brezilya legal varlık numarası (CNPJ)

### <a name="format"></a>Biçimlendir

bir kayıt numarası, Şube numarası ve denetim rakamları içeren 14 basamak, ayrıca sınırlayıcılar

### <a name="pattern"></a>Desen

14 basamak, artı sınırlayıcılar:

- iki basamak
- bir nokta
- üç basamak
- bir nokta
- üç basamak (ilk sekiz basamak kayıt numarasıdır)
- eğik çizgi
- dört basamaklı Şube numarası
- kısa çizgi
- Denetim rakamları olan iki basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_brazil_cnpj"></a>\_Brezilya \_ CNPJ anahtar sözcüğü

```
CNPJ
CNPJ/MF
CNPJ-MF
National Registry of Legal Entities
Taxpayers Registry
Legal entity
Legal entities
Registration Status
Business
Company
CNPJ
Cadastro Nacional da Pessoa Jurídica
Cadastro Geral de Contribuintes
CGC
Pessoa jurídica
Pessoas jurídicas
Situação cadastral
Inscrição
Empresa
```

## <a name="brazil-national-identification-card-rg"></a>Brezilya Ulusal kimlik kartı (RG)

### <a name="format"></a>Biçimlendir

:::no-loc text="Registro Geral (old format): Nine digits":::

:::no-loc text="Registro de Identidade (RIC) (new format): 11 digits":::

### <a name="pattern"></a>Desen

:::no-loc text="Registro Geral (old format):":::

- iki basamak
- bir nokta
- üç basamak
- bir nokta
- üç basamak
- kısa çizgi
- bir denetim hanesi olan bir basamak

:::no-loc text="Registro de Identidade (RIC) (new format):":::

- 10 basamak
- kısa çizgi
- bir denetim hanesi olan bir basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_brazil_rg"></a>\_Brezilya \_ RG anahtar sözcüğü

```
Cédula de identidade
identity card
national ID
número de rregistro
registro de Iidentidade
registro geral
RG (this keyword is case-sensitive)
RIC (this keyword is case-sensitive)
```

## <a name="canada-bank-account-number"></a>Kanada banka hesap numarası

### <a name="format"></a>Biçimlendir

7 veya 12 basamak

### <a name="pattern"></a>Desen

Bir Kanada banka hesap numarası 7 veya 12 rakamdan oluşur.

Bir Kanada banka hesabı transit numarası:

- beş basamak
- kısa çizgi
- üç basamak veya
- sıfır &quot; 0&quot;
- sekiz basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_canada_bank_account_number"></a>Anahtar sözcüğü \_ Kanada \_ Banka \_ Hesap \_ numarası

```
canada savings bonds
canada revenue agency
canadian financial institution
direct deposit form
canadian citizen
legal representative
notary public
commissioner for oaths
child care benefit
universal child care
canada child tax benefit
income tax benefit
harmonized sales tax
social insurance number
income tax refund
child tax benefit
territorial payments
institution number
deposit request
banking information
direct deposit
```

## <a name="canada-drivers-license-number"></a>Kanada sürücüsünün lisans numarası

### <a name="format"></a>Biçimlendir

Bölgeye göre değişir

### <a name="pattern"></a>Desen

Alberta, Ingiliz Kolumbiyası, Manitoba, Yeni Brunswick, Newfoundland/Labrador, Nuscolet, Ontario, Prince Edward Adası, Quebec ve Saskatchewan kapsayan çeşitli desenler

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_province_name_drivers_license_name"></a>Anahtar sözcüğü \_ [il \_ Name] \_ sürücüler \_ Lisans \_ adı

- Eyalet kısaltması, örneğin AB
- Eyalet adı, örneğin Alberta

#### <a name="keyword_canada_drivers_license"></a>Kanada sürücüleri için anahtar sözcük \_ \_ \_ lisansı

```
DL
DLS
CDL
CDLS
DriverLic
DriverLics
DriverLicense
DriverLicenses
DriverLicence
DriverLicences
Driver Lic
Driver Lics
Driver License
Driver Licenses
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicence
DriversLicences
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
Driver's License
Driver's Licenses
Permis de Conduire
ID
IDs
ID card number
ID card numbers
ID card #
ID card #s
ID card
ID cards
ID card
identification number
identification numbers
identification #
identification #s
identification card
identification cards
identification
DL#
DLS#
CDL#
CDLS#
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
Driver's License#
Driver's Licenses#
Permis de Conduire#
ID#
IDs#
ID card#
ID cards#
ID card#
identification card#
identification cards#
identification#
```

## <a name="canada-health-service-number"></a>Kanada sağlık hizmeti numarası

### <a name="format"></a>Biçimlendir

10 basamak

### <a name="pattern"></a>Desen

10 basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_canada_health_service_number"></a>Anahtar sözcük \_ Kanada \_ sistem durumu \_ hizmet \_ numarası

```
personal health number
patient information
health services
specialty services
automobile accident
patient hospital
psychiatrist
workers compensation
disability
```

## <a name="canada-passport-number"></a>Kanada Passport numarası

### <a name="format"></a>Biçimlendir

izleyen iki büyük harf ve altı basamak

### <a name="pattern"></a>Desen

izleyen iki büyük harf ve altı basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_canada_passport_number"></a>\_Kanada \_ Passport \_ numarası anahtar sözcüğü

```
canadian citizenship
canadian passport
passport application
passport photos
certified translator
canadian citizens
processing times
renewal application
```

#### <a name="keyword_passport"></a>Anahtar sözcük \_ Passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="canada-personal-health-identification-number-phin"></a>Kanada Kişisel sağlık kimlik numarası (PHIN)

### <a name="format"></a>Biçimlendir

dokuz basamak

### <a name="pattern"></a>Desen

dokuz basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_canada_phin"></a>\_Kanada \_ phın anahtar sözcüğü

```
social insurance number
health information act
income tax information
manitoba health
health registration
prescription purchases
benefit eligibility
personal health
power of attorney
registration number
personal health number
practitioner referral
wellness professional
patient referral
health and wellness
```

#### <a name="keyword_canada_provinces"></a>\_Kanada \_ eyaletler anahtar sözcüğü

```
Nunavut
Quebec
Northwest Territories
Ontario
British Columbia
Alberta
Saskatchewan
Manitoba
Yukon
Newfoundland and Labrador
New Brunswick
Nova Scotia
Prince Edward Island
Canada
```

## <a name="canada-social-insurance-number"></a>Kanada Sosyal sigorta numarası

### <a name="format"></a>Biçimlendir

isteğe bağlı kısa çizgi veya boşluk ile dokuz basamak

### <a name="pattern"></a>Desen

Miyor

- üç basamak
- kısa çizgi veya boşluk
- üç basamak
- kısa çizgi veya boşluk
- üç basamak

Biçimlendirilmemiş: dokuz basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_sin"></a>Anahtar sözcük \_ Sin

```
sin
social insurance
numero d'assurance sociale
sins
ssn
ssns
social security
numero d'assurance social
national identification number
national ID
sin#
soc ins
social ins
```

#### <a name="keyword_sin_collaborative"></a>Sinüs anahtar sözcüğü \_ \_

```
driver's license
drivers license
driver's license
drivers license
DOB
Birthdate
Birthday
Date of Birth
```

## <a name="chile-identity-card-number"></a>Şili kimlik kartı numarası

### <a name="format"></a>Biçimlendir

yedi-sekiz basamak, bir denetim basamağının veya harfinin sınırlayıcılarını

### <a name="pattern"></a>Desen

yedi-sekiz basamak ve sınırlayıcı:

- bire iki basamak
- isteğe bağlı bir dönem
- üç basamak
- isteğe bağlı bir dönem
- üç basamak
- tire
- bir denetim hanesi olan bir rakam veya harf (büyük/küçük harfe duyarlı değil)

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_chile_id_card"></a>Anahtar sözcük \_ Şili \_ kimlik \_ kartı

```
cédula de identidad
identificación
national identification
national identification number
national ID
número de identificación nacional
rol único nacional
rol único tributario
RUN
RUT
tarjeta de identificación
Rol Unico Nacional
Rol Unico Tributario
RUN#
RUT#
nationaluniqueroleID#
nacional identidad
número identificación
identidad número
numero identificacion
identidad numero
Chilean identity no.
Chilean identity number
Chilean identity #
Unique Tax Registry
Unique Tributary Role
Unique Tax Role
Unique Tributary Number
Unique National Number
Unique National Role
National unique role
Chile identity no.
Chile identity number
Chile identity #
```

## <a name="china-resident-identity-card-prc-number"></a>Çin-yerleşik kimlik kartı (ÇHC) numarası

### <a name="format"></a>Biçimlendir

18 basamak

### <a name="pattern"></a>Desen

18 basamak:

- bir adres kodu olan altı basamak
- YYYYMMDD biçiminde sekiz basamak, Doğum tarihi olan
- Sipariş kodu olan üç basamak
- bir denetim hanesi olan bir basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_china_resident_id"></a>Anahtar \_ sözcük \_ yerleşik \_ kimliği

```
Resident Identity Card
PRC
National Identification Card
身份证
居民身份证
居民身份证
鉴定
身分證
居民身份證
鑑定
```

## <a name="credit-card-number"></a>Kredi kartı numarası

### <a name="format"></a>Biçimlendir

14 ' e 16 basamağa kadar biçimlendirilebilir veya formatlanabilir (dddddddddddddddd) ve Luhn testi geçmesi gerekir.

### <a name="pattern"></a>Desen

Visa, MasterCard, bulma kartı, JCB, Amerikan Express, hediye kartları ve dinleyici kartları dahil tüm büyük markalardan kartları algılayan karmaşık ve güçlü bir model.

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_cc_verification"></a>\_CC \_ doğrulaması anahtar sözcüğü

```
card verification
card identification number
cvn
cid
cvc2
cvv2
pin block
security code
security number
security no
issue number
issue no
cryptogramme
numéro de sécurité
numero de securite
kreditkartenprüfnummer
kreditkartenprufnummer
prüfziffer
prufziffer
sicherheits Kode
sicherheitscode
sicherheitsnummer
verfalldatum
codice di verifica
cod. sicurezza
cod sicurezza
n autorizzazione
código
codigo
cod. seg
cod seg
código de segurança
codigo de seguranca
codigo de segurança
código de seguranca
cód. segurança
cod. seguranca
cod. segurança
cód. seguranca
cód segurança
cod seguranca
cod segurança
cód seguranca
número de verificação
numero de verificacao
ablauf
gültig bis
gültigkeitsdatum
gultig bis
gultigkeitsdatum
scadenza
data scad
fecha de expiracion
fecha de venc
vencimiento
válido hasta
valido hasta
vto
data de expiração
data de expiracao
data em que expira
validade
valor
vencimento
transaction
transaction number
reference number
セキュリティコード
セキュリティ コード
セキュリティナンバー
セキュリティ ナンバー
セキュリティ番号
```

#### <a name="keyword_cc_name"></a>\_CC \_ ad anahtar sözcüğü

```
amex
Americans express
Americans express
americano espresso
Visa
Mastercard
master card
mc
master cards
master cards
diner's Club
diners club
diners club
discover
discover card
discover card
discover cards
JCB
BrandSmart
japanese card bureau
carte blanche
carteblanche
credit card
cc#
cc#:
expiration date
exp date
expiry date
date d'expiration
date d'exp
date expiration
bank card
bank card
card number
card num
card number
card numbers
card numbers
credit card
credit cards
credit cards
ccn
card holder
cardholder
card holders
cardholders
check card
check card
check cards
check cards
debit card
debit card
debit cards
debit cards
atm card
atmcard
atm cards
atmcards
enroute
en route
card type
Cardmember Acct
cardmember account
Card no
Corporate Card
Corporate cards
Type of card
card account number
card member account
Cardmember Acct.
card no.
card no
card number
carte bancaire
carte de crédit
carte de credit
numéro de carte
numero de carte
nº de la carte
nº de carte
kreditkarte
karte
karteninhaber
karteninhabers
kreditkarteninhaber
kreditkarteninstitut
kreditkartentyp
eigentümername
kartennr
kartennummer
kreditkartennummer
kreditkarten-nummer
carta di credito
carta credito
n. carta
n carta
nr. carta
nr carta
numero carta
numero della carta
numero di carta
tarjeta credito
tarjeta de credito
tarjeta crédito
tarjeta de crédito
tarjeta de atm
tarjeta atm
tarjeta debito
tarjeta de debito
tarjeta débito
tarjeta de débito
nº de tarjeta
no. de tarjeta
no de tarjeta
numero de tarjeta
número de tarjeta
tarjeta no
tarjetahabiente
cartão de crédito
cartão de credito
cartao de crédito
cartao de credito
cartão de débito
cartao de débito
cartão de debito
cartao de debito
débito automático
debito automatico
número do cartão
numero do cartão
número do cartao
numero do cartao
número de cartão
numero de cartão
número de cartao
numero de cartao
nº do cartão
nº do cartao
nº. do cartão
no do cartão
no do cartao
no. do cartão
no. do cartao
クレジットカード番号
クレジットカードナンバー
クレジットカード＃
クレジットカード
クレジット
クレカ
カード番号
カードナンバー
カード＃
アメックス
アメリカンエクスプレス
アメリカン エクスプレス
Visaカード
Visa カード
マスターカード
マスター カード
マスター
ダイナースクラブ
ダイナース クラブ
ダイナース
有効期限
期限
キャッシュカード
キャッシュ カード
カード名義人
カードの名義人
カードの名義
デビット カード
デビットカード
```

## <a name="croatia-drivers-license-number"></a>Hırvatistan sürücüsünün lisans numarası

Bu hassas bilgi türü varlığı yalnızca, AB sürücüsünün lisans numarası hassas bilgi türünde bulunur.

### <a name="format"></a>Biçimlendir

boşluk ve sınırlayıcılar olmadan sekiz basamak

### <a name="pattern"></a>Desen

sekiz basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keywords_eu_drivers_license_number"></a>Anahtar sözcükler \_ AB \_ sürücüsünün \_ Lisans \_ numarası

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_croatia_eu_drivers_license_number"></a>Anahtar kelimeler \_ Hırvatistan \_ AB \_ sürücüsünün \_ Lisans \_ numarası

```
vozačka dozvola
vozačke dozvole
```

## <a name="croatia-identity-card-number"></a>Hırvatistan kimlik kartı numarası

Bu hassas bilgi türü varlığı, AB Ulusal kimlik numarası hassas bilgi türüne dahildir ve tek başına hassas bilgi türü varlığı olarak kullanılabilir.

### <a name="format"></a>Biçimlendir

dokuz basamak

### <a name="pattern"></a>Desen

Dokuz ardışık basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_croatia_id_card"></a>Anahtar sözcük \_ Hırvatistan \_ ID \_ kartı

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="croatia-personal-identification-oib-number"></a>Hırvatistan kişisel kimlik (OıB) numarası

### <a name="format"></a>Biçimlendir

11 basamak

### <a name="pattern"></a>Desen

11 basamak:

- 10 basamak
- son basamak bir denetim hanesi

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_croatia_oib_number"></a>Anahtar sözcüğü \_ Hırvatistan \_ OIB \_ numarası

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax Id number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="denmark-personal-identification-number"></a>Danimarka kişisel kimlik numarası

### <a name="format"></a>Biçimlendir

kısa çizgi içeren 10 basamak

### <a name="pattern"></a>Desen

10 basamak:

- GGMMYY biçiminde, Doğum tarihi olan altı basamak
- kısa çizgi
- Son basamağın bir denetim hanesi olduğu dört basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_denmark_id"></a>Anahtar sözcük \_ Danimarka \_ kimliği

```
centrale person register
civilt registreringssystem
cpr
cpr#
gesundheitskarte nummer
gesundheitsversicherungkarte nummer
health card
health insurance card number
health insurance number
identification number
identifikationsnummer
identifikationsnummer#
identity number
krankenkassennummer
national ID#
national number#
national number
personalidnumber#
personalidentityno#
personal ID number
personnummer
personnummer#
reisekrankenversicherungskartenummer
rejsesygesikringskort
ssn
ssn#
skat ID
skat kode
skat nummer
skattenummer
social security number
sundhedsforsikringskort
sundhedsforsikringsnummer
sundhedskort
sundhedskortnummer
sygesikring
sygesikringkortnummer
tax code
travel health insurance card
uniqueidentityno#
tax number
tax registration number
tax ID
tax identification number
tax ID#
tax number#
tax no
tax no#
tax number
tax identification no
tin#
tax ID no#
tax ID number#
tax no#
tin ID
tin no
cpr.nr
cprnr
cprnummer
personnr
person register
sygesikringsbevis
sygesikringsbevisnr
sygesikringsbevisnummer
sygesikringskort
sygesikringskortnr
sygesikringskortnummer
sygesikringsnr
sygesikringsnummer
```

## <a name="eu-debit-card-number"></a>AB banka kartı numarası

### <a name="format"></a>Biçimlendir

16 basamak

### <a name="pattern"></a>Desen

Karmaşık ve güçlü desenler

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_eu_debit_card"></a>Anahtar sözcüğü \_ AB \_ Banka \_ kartı

```
account number
card number
card no.
security number
cc#
```

#### <a name="keyword_card_terms_dict"></a>Anahtar sözcük \_ kartı \_ terimleri \_ dict

```
acct nbr
acct num
acct no
Americans express
Americans express
americano espresso
amex
atm card
atm cards
atm kaart
atmcard
atmcards
atmkaart
atmkaarten
ban contact
bank card
bankkaart
card holder
card holders
card num
card number
card numbers
card type
cardano numerico
cardholder
cardholders
card number
card numbers
carta bianca
carta credito
carta di credito
cartao de credito
cartao de crédito
cartao de debito
cartao de débito
carte bancaire
carte blanche
carte bleue
carte de credit
carte de crédit
carte di credito
carteblanche
cartão de credito
cartão de crédito
cartão de debito
cartão de débito
cb
ccn
check card
check cards
check card
check cards
chequekaart
cirrus
cirrus-edc-maestro
controlekaart
controlekaarten
credit card
credit cards
credit card
credit cards
debetkaart
debetkaarten
debit card
debit cards
debit card
debit cards
debito automatico
diners club
diners club
discover
discover card
discover cards
discover card
discover cards
débito automático
edc
eigentümername
european debit card
hoofdkaart
hoofdkaarten
in viaggio
japanese card bureau
japanse kaartdienst
jcb
kaart
kaart num
kaartaantal
kaartaantallen
kaarthouder
kaarthouders
karte
karteninhaber
karteninhabers
kartennr
kartennummer
kreditkarte
kreditkarten-nummer
kreditkarteninhaber
kreditkarteninstitut
kreditkartennummer
kreditkartentyp
maestro
master card
master cards
Mastercard
master cards
mc
mister cash
n carta
carta
no de tarjeta
no do cartao
no do cartão
no. de tarjeta
no. do cartao
no. do cartão
nr carta
nr. carta
numeri di scheda
numero carta
numero de cartao
numero de carte
numero de cartão
numero de tarjeta
numero della carta
numero di carta
numero di scheda
numero do cartao
numero do cartão
numéro de carte
nº carta
nº de carte
nº de la carte
nº de tarjeta
nº do cartao
nº do cartão
nº. do cartão
número de cartao
número de cartão
número de tarjeta
número do cartao
scheda dell'assegno
scheda dell'atmosfera
scheda dell'atmosfera
scheda della banca
scheda di controllo
scheda di debito
scheda matrice
schede dell'atmosfera
schede di controllo
schede di debito
schede matrici
scoprono la scheda
scoprono le schede
solo
supporti di scheda
supporto di scheda
switch
tarjeta atm
tarjeta credito
tarjeta de atm
tarjeta de credito
tarjeta de debito
tarjeta debito
tarjeta no
tarjetahabiente
tipo della scheda
ufficio giapponese della
scheda
v pay
v-pay
visa
visa plus
visa electron
visto
visum
vpay
```

#### <a name="keyword_card_security_terms_dict"></a>Anahtar sözcük \_ kartı \_ Güvenlik \_ Koşulları \_ dict

```
card identification number
card verification
cardi la verifica
cid
cod seg
cod seguranca
cod segurança
cod sicurezza
cod. seg
cod. seguranca
cod. segurança
cod. sicurezza
codice di sicurezza
codice di verifica
codigo
codigo de seguranca
codigo de segurança
crittogramma
cryptogram
cryptogramme
cv2
cvc
cvc2
cvn
cvv
cvv2
cód seguranca
cód segurança
cód. seguranca
cód. segurança
código
código de seguranca
código de segurança
de kaart controle
geeft nr uit
issue no
issue number
kaartidentificatienummer
kreditkartenprufnummer
kreditkartenprüfnummer
kwestieaantal
no. dell'edizione
no. di sicurezza
numero de securite
numero de verificacao
numero dell'edizione
numero di identificazione della
scheda
numero di sicurezza
numero van veiligheid
numéro de sécurité
nº autorizzazione
número de verificação
perno il blocco
pin block
prufziffer
prüfziffer
security code
security no
security number
sicherheits kode
sicherheitscode
sicherheitsnummer
speldblok
veiligheid nr
veiligheidsaantal
veiligheidscode
veiligheidsnummer
verfalldatum
```

#### <a name="keyword_card_expiration_terms_dict"></a>Anahtar sözcük \_ kartı \_ süre sonu \_ Koşulları \_ dict

```
ablauf
data de expiracao
data de expiração
data del exp
data di exp
data di scadenza
data em que expira
data scad
data scadenza
date de validité
datum afloop
datum van exp
de afloop
espira
espira
exp date
exp datum
expiration
expire
expires
expiry
fecha de expiracion
fecha de venc
gultig bis
gultigkeitsdatum
gültig bis
gültigkeitsdatum
la scadenza
scadenza
valable
validade
valido hasta
valor
venc
vencimento
vencimiento
verloopt
vervaldag
vervaldatum
vto
válido hasta
```

## <a name="eu-drivers-license-number"></a>AB sürücüsünün lisans numarası

Bunlar, AB sürücüsünün lisans numarası duyarlı bilgi türündeki varlıklardır.

- Avusturya
- Belçika
- Bulgaristan
- Hırvatistan
- Kıbrıs
- Çekçe
- Danimarka
- Estonya
- Finlandiya
- Fransa
- Almanya
- Yunanistan
- Macaristan
- İrlanda
- İtalya
- Letonya
- Litvanya
- Luxemburg
- Malta
- Hollanda
- Polonya
- Portekiz
- Romanya
- Slovakya
- Slovenya
- İspanya
- İsveç
- Krallık

## <a name="eu-national-identification-number"></a>AB Ulusal kimlik numarası

Bunlar, AB Ulusal kimlik numarası hassas bilgi türünde varlıklardır.

- Avusturya
- Belçika
- Bulgaristan
- Hırvatistan
- Kıbrıs
- Çekçe
- Danimarka
- Estonya
- Finlandiya
- Fransa
- Almanya
- Yunanistan
- Macaristan
- İrlanda
- İtalya
- Letonya
- Litvanya
- Luxemburg
- Malta
- Hollanda
- Polonya
- Portekiz
- Romanya
- Slovakya
- Slovenya
- İspanya
- Krallık

## <a name="eu-passport-number"></a>AB Passport numarası

Bunlar, AB Passport numarası grubundaki varlıklardır. Bunlar, AB Passport numarası demeti içindeki varlıklardır.

- Avusturya
- Belçika
- Bulgaristan
- Hırvatistan
- Kıbrıs
- Çekçe
- Danimarka
- Estonya
- Finlandiya
- Fransa
- Almanya
- Yunanistan
- Macaristan
- İrlanda
- İtalya
- Letonya
- Litvanya
- Luxemburg
- Malta
- Hollanda
- Polonya
- Portekiz
- Romanya
- Slovakya
- Slovenya
- İspanya
- İsveç
- Krallık

## <a name="eu-social-security-number-or-equivalent-identification"></a>AB sosyal güvenlik numarası veya eşdeğer tanımlayıcı

Bunlar, AB sosyal güvenlik numarasında veya eşdeğer kimliğe duyarlı bilgi türünde olan varlıklardır.

- Avusturya
- Belçika
- Hırvatistan
- Çekçe
- Danimarka
- Finlandiya
- Fransa
- Almanya
- Yunanistan
- Macaristan
- Portekiz
- İspanya
- İsveç

## <a name="eu-tax-identification-number"></a>AB vergi kimlik numarası

Bu varlıklar, AB vergi kimlik numarası hassas bilgi türü ' nde bulunur.

- Avusturya
- Belçika
- Bulgaristan
- Hırvatistan
- Kıbrıs
- Çekçe
- Danimarka
- Estonya
- Finlandiya
- Fransa
- Almanya
- Yunanistan
- Macaristan
- İrlanda
- İtalya
- Letonya
- Litvanya
- Luxemburg
- Malta
- Hollanda
- Polonya
- Portekiz
- Romanya
- Slovakya
- Slovenya
- İspanya
- İsveç
- Krallık



## <a name="finland-national-id"></a>Finlandiya Ulusal KIMLIĞI

### <a name="format"></a>Biçimlendir

altı basamak ve yüzyıl Plus üç basamağı ve bir denetim hanesi belirten bir karakter

### <a name="pattern"></a>Desen

Model aşağıdakilerin tümünü içermelidir:

- GGMMYY biçiminde, Doğum tarihi olan altı basamak
- Century işaretleyicisi ('-', ' + ' ya da ' a ')
- üç basamaklı kişisel kimlik numarası
- bir denetim hanesi olan rakam veya harf (büyük/küçük harf duyarsız)

### <a name="keywords"></a>Anahtar sözcükler

```
ainutlaatuinen henkilökohtainen tunnus
henkilökohtainen tunnus
henkilötunnus
henkilötunnusnumero#
henkilötunnusnumero
hetu
ID no
ID number
identification number
identiteetti numero
identity number
ID number
kansallinen henkilötunnus
kansallisen henkilökortin
national ID card
national ID no.
personal ID
personal identity code
personalidnumber#
personbeteckning
personnummer
social security number
sosiaaliturvatunnus
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
tunnistenumero
tunnus numero
tunnusluku
tunnusnumero
verokortti
veronumero
verotunniste
verotunnus
```

## <a name="finland-passport-number"></a>Finlandiya Passport numarası

Bu hassas bilgi türü varlığı, AB Passport numarası hassas bilgi türünde mevcuttur ve tek başına hassas bilgi türü varlığı olarak kullanılabilir.

### <a name="format"></a>Biçimlendir

Dokuz harf ve rakam birleşimi

### <a name="pattern"></a>Desen

Dokuz harf ve rakam birleşimi:

- iki harf (büyük/küçük harfe duyarlı değil)
- yedi basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keywords_eu_passport_number_common"></a>Anahtar sözcükler \_ AB \_ Passport \_ numarası \_ ortak

```
passport#
passport #
passport ID
passports
passport no
passport no
passport number
passport number
passport numbers
passport numbers
```

#### <a name="keyword_finland_passport_number"></a>Anahtar sözcük \_ Finlandiya \_ Passport \_ numarası

```
suomalainen passi
passin numero
passin numero.#
passin numero#
passin numero.
passi#
passi number
```

## <a name="france-drivers-license-number"></a>Fransa sürücüsünün lisans numarası

Bu hassas bilgi türü varlığı, AB sürücüsünün lisans numarası hassas bilgi türünde bulunur ve tek başına hassas bilgi türü varlığı olarak kullanılabilir.

### <a name="format"></a>Biçimlendir

12 basamak

### <a name="pattern"></a>Desen

Fransız telefon numaraları gibi benzer desenleri indirimle doğrulama ile 12 basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_french_drivers_license"></a>Anahtar sözcük \_ Fransızca \_ sürücüleri \_ lisansı

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
permis de conduire
license number
license number
license numbers
license numbers
numéros de license
```

## <a name="france-national-id-card-cni"></a>Fransa Ulusal KIMLIK kartı (CNı)

### <a name="format"></a>Biçimlendir

12 basamak

### <a name="pattern"></a>Desen

12 basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keywords_france_eu_national_id_card"></a>Anahtar sözcükler \_ Fransa \_ AB \_ Ulusal \_ kimlik \_ kartı

```
card number
carte nationale d'identité
carte nationale d'idenite no
cni#
cni
compte bancaire
national identification number
national identity
nationalidno#
numéro d'assurance maladie
numéro de carte vitale
```

## <a name="france-passport-number"></a>Fransa Passport numarası

Bu hassas bilgi türü varlığı, AB Passport numarası hassas bilgi türünde mevcuttur ve tek başına hassas bilgi türü varlığı olarak kullanılabilir.

### <a name="format"></a>Biçimlendir

dokuz basamak ve harf

### <a name="pattern"></a>Desen

dokuz basamak ve harf:

- iki basamak
- iki harf (büyük/küçük harfe duyarlı değil)
- beş basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_passport"></a>Anahtar sözcük \_ Passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="france-social-security-number-insee-or-equivalent-identification"></a>Fransa sosyal güvenlik numarası (ıNSEE) veya eşdeğer tanımlayıcı

Bu hassas bilgi türü varlığı, AB sosyal güvenlik numarasına ve eşdeğer KIMLIĞE duyarlı bilgi türüne dahildir ve tek başına hassas bilgi türü varlığı olarak kullanılabilir.

### <a name="format"></a>Biçimlendir

15 basamak

### <a name="pattern"></a>Desen

İki desenden biriyle eşleşmelidir:

- 13 basamak izleyen bir boşluk, iki basamakla veya
- 15 ardışık basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_fr_insee"></a>\_Fr \_ INSEE anahtar sözcüğü

```
insee
securité sociale
securite sociale
national ID
national identification
numéro d'identité
no d'identité
no. d'identité
numero d'identite
no d'identite
no. d'identite
social security number
social security code
social insurance number
le numéro d'identification nationale
d'identité nationale
numéro de sécurité sociale
le code de la sécurité sociale
numéro d'assurance sociale
numéro de sécu
code sécu
```

## <a name="germany-drivers-license-number"></a>Almanya sürücüsünün lisans numarası

Bu hassas bilgi türü varlığı, AB sürücüsünün lisans numarası hassas bilgi türüne dahildir ve tek başına hassas bilgi türü varlığı olarak kullanılabilir.

### <a name="format"></a>Biçimlendir

11 basamak ve harf birleşimi

### <a name="pattern"></a>Desen

11 basamak ve harf (büyük/küçük harfe duyarlı değil):

- rakam veya harf
- iki basamak
- altı rakam veya harf
- bir basamak
- rakam veya harf

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_german_drivers_license_number"></a>Alman sürücüleri için anahtar sözcük \_ \_ \_ Lisans \_ numarası

```
ausstellungsdatum
ausstellungsort
ausstellende behöde
ausstellende behorde
ausstellende behoerde
führerschein
fuhrerschein
fuehrerschein
führerscheinnummer
fuhrerscheinnummer
fuehrerscheinnummer
führerschein-
fuhrerschein-
fuehrerschein-
führerscheinnummernr
fuhrerscheinnummernr
fuehrerscheinnummernr
führerscheinnummerklasse
fuhrerscheinnummerklasse
fuehrerscheinnummerklasse
nr-führerschein
nr-fuhrerschein
nr-fuehrerschein
no-führerschein
no-fuhrerschein
no-fuehrerschein
n-führerschein
n-fuhrerschein
n-fuehrerschein
permis de conduire
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dlno
```

## <a name="germany-identity-card-number"></a>Almanya kimlik kartı numarası

### <a name="format"></a>Biçimlendir

1 Kasım 2010: dokuz harf ve rakam

1 Nisan 1987 tarihine kadar 31 Ekim 2010:10 basamak

### <a name="pattern"></a>Desen

1 Kasım 2010 ' den itibaren:

- bir harf (büyük/küçük harfe duyarlı değil)
- sekiz basamak

1 Nisan 1987 ' den 31 Ekim 2010 tarihine kadar:

- 10 basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_germany_id_card"></a>Anahtar sözcük \_ Almanya \_ kimlik \_ kartı

```
ausweis
gpid
identification
identifikation
identifizierungsnummer
identity card
identity number
id-nummer
personal ID
personalausweis
persönliche ID nummer
persönliche identifikationsnummer
persönliche-id-nummer
```

## <a name="germany-passport-number"></a>Almanya Passport numarası

Bu hassas bilgi türü varlığı, AB Passport numarası hassas bilgi türüne dahildir ve tek başına hassas bilgi türü varlığı olarak kullanılabilir.

### <a name="format"></a>Biçimlendir

10 rakam veya harf

### <a name="pattern"></a>Desen

Model aşağıdakilerin tümünü içermelidir:

- ilk karakter bu kümeden bir rakam veya bir harftir (C, F, G, H, J, K)
- üç basamak
- Bu kümeden beş rakam veya harf (C,-H, J-N, P, R, T, V-Z)
- bir basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_german_passport"></a>\_Alman \_ Passport anahtar sözcüğü

```
reisepasse
reisepassnummer
No-Reisepass
Nr-Reisepass
Reisepass-Nr
Passnummer
reisepässe
passeport no.
passeport no
```

## <a name="greece-national-id-card"></a>Yunanistan Ulusal KIMLIK kartı

### <a name="format"></a>Biçimlendir

7-8 harflerin ve sayıların birleşimi ve tire

### <a name="pattern"></a>Desen

Yedi harf ve sayı (eski biçim):

- Bir harf (Yunan alfabesi 'nin herhangi bir harfi)
- Tire
- Altı basamak

Sekiz harf ve sayı (yeni biçim):

- Büyük harfli karakteri Yunan ve Latin harfler (ABEZHıKMNOPTYX) içinde oluşan iki harf
- Tire
- Altı basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_greece_id_card"></a>Anahtar sözcüğü \_ Yunanistan \_ kimlik \_ kartı

```
greek ID
greek national ID
greek personal ID card
greek police ID
identity card
tautotita
ταυτότητα
ταυτότητας
```

## <a name="hong-kong-identity-card-hkid-number"></a>Hong Kong kimlik kartı (HKıD) numarası

### <a name="format"></a>Biçimlendir

8-9 harflerin ve sayıların birleşimi ve son karakter etrafında isteğe bağlı parantezler

### <a name="pattern"></a>Desen

8-9 harflerin birleşimi:

- 1-2 harf (büyük/küçük harfe duyarlı değil)
- Altı basamak
- Denetim hanesi olan ve isteğe bağlı olarak parantez içine alınmış son karakter (herhangi bir rakam veya harf A).

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_hong_kong_id_card"></a>Anahtar sözcük \_ Hong \_ Kong \_ kimlik \_ kartı

```
hkid
Hong Kong identity card
HKIDC
ID card
identity card
hk identity card
Hong Kong ID
香港身份證
香港永久性居民身份證
身份證
身份証
身分證
身分証
香港身份証
香港身分證
香港身分証
香港身份證
香港居民身份證
香港居民身份証
香港居民身分證
香港居民身分証
香港永久性居民身份証
香港永久性居民身分證
香港永久性居民身分証
香港永久性居民身份證
香港非永久性居民身份證
香港非永久性居民身份証
香港非永久性居民身分證
香港非永久性居民身分証
香港特別行政區永久性居民身份證
香港特別行政區永久性居民身份証
香港特別行政區永久性居民身分證
香港特別行政區永久性居民身分証
香港特別行政區非永久性居民身份證
香港特別行政區非永久性居民身份証
香港特別行政區非永久性居民身分證
香港特別行政區非永久性居民身分証
```

## <a name="ip-address"></a>IP Adresi

### <a name="format"></a>Biçimlendir

#### <a name="ipv4"></a>IPv4

IPv4 adreslerinin biçimlendirilen (dönemler) ve biçimlendirilmemiş (dönem olmayan) sürümleri için olan karmaşık desenler

#### <a name="ipv6"></a>IPv6

Biçimlendirilen IPv6 numaraları (iki nokta üst üste dahil) için hesaplar için karmaşık model

### <a name="pattern"></a>Desen

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_ipaddress"></a>Anahtar sözcük \_ IPAdresi

```
IP (this keyword is case-sensitive)
ip address
ip addresses
internet protocol
IP-כתובת ה
```

## <a name="india-permanent-account-number-pan"></a>Hindistan kalıcı hesap numarası (PAN)

### <a name="format"></a>Biçimlendir

10 harf veya rakam

### <a name="pattern"></a>Desen

10 harf veya basamak:

- Üç harf (büyük/küçük harfe duyarlı değil)
- C, P, H, F, A, T, B, L, J, G (büyük/küçük harfe duyarlı değil) içinde bir harf
- Bir harf
- Dört basamak
- Bir harf (büyük/küçük harfe duyarlı değil)

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_india_permanent_account_number"></a>Anahtar sözcüğü \_ Hindistan \_ kalıcı \_ Hesap \_ numarası

```
Permanent Account Number
PAN
```

## <a name="india-unique-identification-aadhaar-number"></a>Hindistan benzersiz tanımlama (aadhaar) numarası

### <a name="format"></a>Biçimlendir

isteğe bağlı boşluk veya tire içeren 12 basamak

### <a name="pattern"></a>Desen

12 basamak:

- 0 veya 1 olmayan bir basamak
- Üç basamak
- İsteğe bağlı bir boşluk veya tire
- Dört basamak
- İsteğe bağlı bir boşluk veya tire
- Denetim hanesi olan son basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_india_aadhar"></a>\_Hindistan \_ Aadhar anahtar sözcüğü

```
aadhaar
aadhar
aadhar#
uid
आधार
uidai
```

## <a name="indonesia-identity-card-ktp-number"></a>Endonezya kimlik kartı (KTP) numarası

### <a name="format"></a>Biçimlendir

isteğe bağlı dönemleri içeren 16 basamak

### <a name="pattern"></a>Desen

16 basamak:

- İki basamaklı Eyalet kodu
- Bir nokta (isteğe bağlı)
- İki basamaklı Regency veya şehir kodu
- İki basamaklı alt bölge kodu
- Bir nokta (isteğe bağlı)
- GGMMYY biçiminde, Doğum tarihi olan altı basamak
- Bir nokta (isteğe bağlı)
- Dört basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_indonesia_id_card"></a>Anahtar sözcük \_ Endonezya \_ kimliği \_ kartı

```
KTP
Kartu Tanda Penduduk
Nomor Induk Kependudukan
```

## <a name="international-banking-account-number-iban"></a>Uluslararası Bankacılık hesap numarası (ıBAN)

### <a name="format"></a>Biçimlendir

Ülke kodu (iki harf) ve basamak rakamları (iki basamak) artı :::no-loc text="bban"::: sayı (en fazla 30 karakter)

### <a name="pattern"></a>Desen

Model aşağıdakilerin tümünü içermelidir:

- İki harfli ülke kodu
- İki denetim basamağı (sonra isteğe bağlı bir alan)
- dört harf veya basamaklı 1-7 grup (boşluklarla ayrılabilir)
- 1-3 harf veya rakam

Her ülkenin biçimi biraz farklıdır. IBAN hassas bilgi türü şu 60 ülkeleri içerir:

```
ad, ae, al, at, az, ba, be, bg, bh, ch, cr, cy, cz, de, dk, do, ee, es, fi, fo, fr, gb, ge, gi, gl, gr, hr, hu, that is, il, is, it, kw, kz, lb, li, lt, lu, lv, mc, md, me, mk, mr, mt, mu, nl, no, pl, pt, ro, rs, sa, se, si, sk, sm, tn, tr, vg
```

### <a name="keywords"></a>Anahtar sözcükler

Yok

## <a name="ireland-personal-public-service-pps-number"></a>İrlanda Personal kamu hizmeti (PPS) numarası

### <a name="format"></a>Biçimlendir

Eski biçim (31 Dec 2012 tarihine kadar):

- yedi basamak sonra 1-2 harf

Yeni biçim (1 Ocak 2013 ve sonrası):

- yedi basamak ve ardından iki harf

### <a name="pattern"></a>Desen

Eski biçim (31 Dec 2012 tarihine kadar):

- yedi basamak
- bir-iki harf (büyük/küçük harfe duyarlı değil)

Yeni biçim (1 Ocak 2013 ve sonrası):

- yedi basamak
- alfabetik bir denetim hanesi olan bir harf (büyük/küçük harfe duyarlı değil)
- A-I veya W aralığındaki isteğe bağlı bir harf &quot;&quot;

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keywords_ireland_eu_national_id_card"></a>Anahtar kelimeler \_ İrlanda \_ AB \_ Ulusal \_ kimlik \_ kartı

```
client identity service
identification number
personal ID number
personal public service number
personal service no
phearsanta seirbhíse poiblí
pps no
pps number
pps num
pps service no
ppsn
ppsno#
ppsno
psp
public service no
publicserviceno#
publicserviceno
revenue and social insurance number
rsi no
rsi number
rsin
seirbhís aitheantais cliant
uimh
uimhir aitheantais chánach
uimhir aitheantais phearsanta
uimhir phearsanta seirbhíse poiblí
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="israel-bank-account-number"></a>İsrail banka hesap numarası

### <a name="format"></a>Biçimlendir

13 basamak

### <a name="pattern"></a>Desen

Miyor

- iki basamak
- tire
- üç basamak
- tire
- sekiz basamak

Biçimlendirilmemiş:

- 13 ardışık basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_israel_bank_account_number"></a>\_İsrail Bankası anahtar sözcüğünün \_ Banka \_ Hesap \_ numarası

```
Bank Account Number
Bank Account
Account Number
מספר חשבון בנק
```

## <a name="israel-national-identification-number"></a>İsrail Ulusal kimlik numarası

### <a name="format"></a>Biçimlendir

dokuz basamak

### <a name="pattern"></a>Desen

Dokuz ardışık basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_israel_national_id"></a>\_İsrail \_ Ulusal \_ kimliği anahtar sözcüğü

```
מספר זהות
מספר זיה וי
מספר זיהוי ישר אלי
זהותישר אלית
هو ية اسرائيل ية عدد
هوية إسرائ يلية
رقم الهوية
عدد هوية فريدة من نوعها
ID number#
ID number
identity no
identity number#
identity number
israeliidentitynumber
personal ID
unique ID
```

## <a name="italy-drivers-license-number"></a>İtalya sürücüsünün lisans numarası

Bu hassas bilgi türü varlığı, AB sürücüsünün lisans numarası hassas bilgi türüne dahildir ve tek başına hassas bilgi türü varlığı olarak kullanılabilir.

### <a name="format"></a>Biçimlendir

10 harften ve rakamlardan oluşan bir bileşim

### <a name="pattern"></a>Desen

10 harften ve rakamlardan oluşan bir bileşim:

- bir harf (büyük/küçük harfe duyarlı değil)
- &quot;A &quot; veya V harfi &quot; &quot; (büyük/küçük harfe duyarlı değil)
- yedi basamak
- bir harf (büyük/küçük harfe duyarlı değil)

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_italy_drivers_license_number"></a>Anahtar sözcük \_ İtalya \_ sürücüleri \_ Lisans \_ numarası

```
numero di patente
patente di guida
patente guida
patenti di guida
patenti guida
```

## <a name="japan-bank-account-number"></a>Japon banka hesap numarası

### <a name="format"></a>Biçimlendir

yedi veya sekiz basamak

### <a name="pattern"></a>Desen

Banka hesap numarası:

- yedi veya sekiz basamak
- banka hesabı dal kodu:
- dört basamak
- boşluk veya tire (isteğe bağlı)
- üç basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_jp_bank_account"></a>Anahtar sözcük \_ JP \_ Banka \_ hesabı

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
口座番号
銀行口座
銀行口座番号
総合口座
普通預金口座
普通口座
当座預金口座
当座口座
預金口座
振替口座
銀行
バンク
```

#### <a name="keyword_jp_bank_branch_code"></a>Anahtar sözcük \_ JP \_ Banka \_ dal \_ kodu

```
支店番号
支店コード
店番号
```

## <a name="japan-drivers-license-number"></a>Japon sürücüsünün lisans numarası

### <a name="format"></a>Biçimlendir

12 basamak

### <a name="pattern"></a>Desen

12 ardışık basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_jp_drivers_license_number"></a>Anahtar sözcük \_ JP \_ sürücüleri \_ Lisans \_ numarası

```
driver license
drivers license
driver'license
drivers licenses
driver'licenses
driver licenses
dl#
dls#
lic#
lics#
運転免許証
運転免許
免許証
免許
運転免許証番号
運転免許番号
免許証番号
免許番号
運転免許証ナンバー
運転免許ナンバー
免許証ナンバー
運転免許証no
運転免許no
免許証no
免許no
運転経歴証明書番号
運転経歴証明書
運転免許証No.
運転免許No.
免許証No.
免許No.
運転免許証#
運転免許#
免許証#
免許#
```

## <a name="japan-passport-number"></a>Japonya Passport numarası

### <a name="format"></a>Biçimlendir

iki harf ve sonra yedi basamak

### <a name="pattern"></a>Desen

iki harf (büyük/küçük harfe duyarlı değil) sonra yedi basamak

#### <a name="keyword_jp_passport"></a>\_JP \_ Passport anahtar sözcüğü

```
Passport
Passport Number
Passport No.
Passport #
パスポート
パスポート番号
パスポートナンバー
パスポート＃
パスポート#
パスポートNo.
旅券番号
旅券番号＃
旅券番号♯
旅券ナンバー
```

## <a name="japan-residence-card-number"></a>Japon konut kartı numarası

### <a name="format"></a>Biçimlendir

12 harf ve rakam

### <a name="pattern"></a>Desen

12 harf ve rakam:

- iki harf (büyük/küçük harfe duyarlı değil)
- sekiz basamak
- iki harf (büyük/küçük harfe duyarlı değil)

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_jp_residence_card_number"></a>Anahtar kelime \_ \_ \_ kartı \_ numarası

```
Residence card number
Residence card no
Residence card #
在留カード番号
在留カード
在留番号
```

## <a name="japan-resident-registration-number"></a>Japonya 'da yerleşik kayıt numarası

### <a name="format"></a>Biçimlendir

11 basamak

### <a name="pattern"></a>Desen

11 ardışık basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_jp_resident_registration_number"></a>Anahtar sözcük \_ JP \_ yerleşik \_ kayıt \_ numarası

```
Resident Registration Number
Residents Basic Registry Number
Resident Registration No.
Resident Register No.
Residents Basic Registry No.
Basic Resident Register No.
外国人登録証明書番号
証明書番号
登録番号
外国人登録証
```

## <a name="japan-social-insurance-number-sin"></a>Japonya sosyal sigorta numarası (SIN)

### <a name="format"></a>Biçimlendir

7-12 basamak

### <a name="pattern"></a>Desen

7-12 basamak:

- dört basamak
- kısa çizgi (isteğe bağlı)
- altı basamak veya
- 7-12 ardışık basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_jp_sin"></a>Anahtar sözcük \_ JP \_

```
Social Insurance No.
Social Insurance Num
Social Insurance Number
健康保険被保険者番号
健保番号
基礎年金番号
雇用保険被保険者番号
雇用保険番号
保険証番号
社会保険番号
社会保険No.
社会保険
介護保険
介護保険被保険者番号
健康保険被保険者整理番号
雇用保険被保険者整理番号
厚生年金
厚生年金被保険者整理番号
```

## <a name="malaysia-identification-card-number"></a>Malezya kimlik kartı numarası

### <a name="format"></a>Biçimlendir

isteğe bağlı kısa çizgileri içeren 12 basamak

### <a name="pattern"></a>Desen

12 basamak:

- Doğum tarihi olan YGG biçiminde altı basamak
- bir tire (isteğe bağlı)
- iki harfli Doğum yeri kodu
- bir tire (isteğe bağlı)
- Üç rastgele basamak
- Tek basamaklı cinsiyet kodu

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_malaysia_id_card_number"></a>Anahtar kelime \_ Malezya \_ kimlik \_ kartı \_ numarası

```
digital application card
i/c
i/c no
ic
ic no
ID card
identification Card
identity card
k/p
k/p no
kad akuan diri
kad aplikasi digital
kad pengenalan malaysia
kp
kp no
mykad
mykas
mykid
mypr
mytentera
malaysia identity card
malaysian identity card
nric
personal identification card
```

## <a name="netherlands-citizens-service-bsn-number"></a>Hollanda vatandaşlık hizmeti (BSN) numarası

### <a name="format"></a>Biçimlendir

isteğe bağlı boşluklar içeren sekiz basamaklı sayı

### <a name="pattern"></a>Desen

sekiz dokuz basamak:

- üç basamak
- boşluk (isteğe bağlı)
- üç basamak
- boşluk (isteğe bağlı)
- iki üç basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keywords_netherlands_eu_national_id_card"></a>Anahtar kelimeler \_ Hollanda \_ AB \_ Ulusal \_ kimlik \_ kartı

```
bsn#
bsn
burgerservicenummer
citizen service number
person number
personal number
personal numeric code
person-related number
persoonlijk nummer
persoonlijke numerieke code
persoonsgebonden
persoonsnummer
sociaal-fiscaal nummer
social-fiscal number
sofi
sofinummer
uniek identificatienummer
uniek identiteitsnummer
unique identification number
unique identity number
uniqueidentityno#
```

## <a name="new-zealand-ministry-of-health-number"></a>Yeni Zelanda sağlık numarası Bakanlığı

### <a name="format"></a>Biçimlendir

üç harf, boşluk (isteğe bağlı) ve dört basamak

### <a name="pattern"></a>Desen

- ' I ' ve ' O ' dışında üç harf (büyük/küçük harfe duyarlı değil)
- boşluk (isteğe bağlı)
- dört basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_nz_terms"></a>\_NZ \_ terimleri anahtar sözcüğü

```
NHI
New Zealand
Health
treatment
National Health Index Number
nhi number
nhi no.
NHI#
National Health Index No.
National Health Index Id
National Health Index #
```

## <a name="norway-identification-number"></a>Norveç kimlik numarası

### <a name="format"></a>Biçimlendir

11 basamak

### <a name="pattern"></a>Desen

11 basamak:

- GGMMYY biçiminde, Doğum tarihi olan altı basamak
- üç basamaklı tek sayı
- iki denetim basamağı

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_norway_id_number"></a>Anahtar sözcüğü \_ Norveç \_ kimlik \_ numarası

```
Personal identification number
Norwegian ID Number
ID Number
Identification
Personnummer
Fødselsnummer
```

## <a name="philippines-unified-multi-purpose-identification-number"></a>Filipinler Birleşik çok amaçlı kimlik numarası

### <a name="format"></a>Biçimlendir

kısa çizgilerden ayrılan 12 basamak

### <a name="pattern"></a>Desen

12 basamak:

- dört basamak
- kısa çizgi
- yedi basamak
- kısa çizgi
- Tek basamaklı

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_philippines_id"></a>Anahtar sözcük \_ Filipinler \_ kimliği

```
Unified Multi-Purpose ID
UMID
Identity Card
Pinag-isang Multi-Layunin ID
```

## <a name="poland-identity-card"></a>Polonya kimlik kartı

### <a name="format"></a>Biçimlendir

üç harf ve altı basamak

### <a name="pattern"></a>Desen

üç harf (büyük/küçük harfe duyarlı değil), altı basamaktan sonra

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_poland_national_id_passport_number"></a>Anahtar sözcük \_ Polonya \_ Ulusal \_ kimliği \_ Passport \_ numarası

```
Dowód osobisty
Numer dowodu osobistego
Nazwa i numer dowodu osobistego
Nazwa i nr dowodu osobistego
Nazwa i nr dowodu tożsamości
Dowód Tożsamości
dow. os.
```

## <a name="poland-national-id-pesel"></a>Polonya Ulusal KIMLIĞI (PESEL)

### <a name="format"></a>Biçimlendir

11 basamak

### <a name="pattern"></a>Desen

- doğun tarihini YYAAGG biçiminde temsil eden 6 basamak
- 4 basamak
- 1 denetim hanesi

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_pesel_identification_number"></a>Anahtar sözcük \_ Pesel \_ kimlik \_ numarası

```
dowód osobisty
dowódosobisty
niepowtarzalny numer
niepowtarzalnynumer
nr.-pesel
nr-pesel
numer identyfikacyjny
pesel
tożsamości narodowej
```

## <a name="poland-passport-number"></a>Polonya Passport numarası

Bu hassas bilgi türü varlığı, AB Passport numarası hassas bilgi türüne dahildir ve tek başına hassas bilgi türü varlığı olarak kullanılabilir.

### <a name="format"></a>Biçimlendir

iki harf ve yedi basamak

### <a name="pattern"></a>Desen

İki harf (büyük/küçük harfe duyarlı değil) sonra yedi basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_poland_national_id_passport_number"></a>Anahtar sözcük \_ Polonya \_ Ulusal \_ kimliği \_ Passport \_ numarası

```
Numer paszportu
Nr. Paszportu
Paszport
```

## <a name="portugal-citizen-card-number"></a>Portekiz vatandaşlık kart numarası

### <a name="format"></a>Biçimlendir

sekiz basamak

### <a name="pattern"></a>Desen

sekiz basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_portugal_citizen_card"></a>\_Portekiz \_ vatandaşlık \_ Card anahtar sözcüğü

```
bilhete de identidade
cartão de cidadão
citizen card
document number
documento de identificação
ID number
identification no
identification number
identity card no
identity card number
national ID card
nic
número bi de portugal
número de identificação civil
número de identificação fiscal
número do documento
portugal bi number
```

## <a name="portugal-drivers-license-number"></a>Portekiz sürücüsünün lisans numarası

Bu hassas bilgi türü varlığı yalnızca, AB sürücüsünün lisans numarası hassas bilgi türünde bulunur.

### <a name="format"></a>Biçimlendir

iki desen-iki harf ve ardından özel karakterlerle 5-8 basamak

### <a name="pattern"></a>Desen

1. kalıp: özel karakterlerle 5/6 izleyen Iki harf:

- İki harf (büyük/küçük harfe duyarlı değil)
- Bir tire
- Beş veya altı basamak
- Bir boşluk
- Tek basamaklı

2. Model: özel karakterlerle 6/8 basamaklı bir harf gelmelidir:

- Bir harf (büyük/küçük harfe duyarlı değil)
- Bir tire
- Altı veya sekiz basamak
- Bir boşluk
- Tek basamaklı

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keywords_eu_drivers_license_number"></a>Anahtar sözcükler \_ AB \_ sürücüsünün \_ Lisans \_ numarası

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_portugal_eu_drivers_license_number"></a>Anahtar sözcükler \_ Portekiz \_ AB \_ sürücüsünün \_ Lisans \_ numarası

```
carteira de motorista
carteira motorista
carteira de habilitação
carteira habilitação
número de licença
número licença
permissão de condução
permissão condução
Licença condução Portugal
carta de condução
```

## <a name="saudi-arabia-national-id"></a>Suudi Arabistan Ulusal KIMLIĞI

### <a name="format"></a>Biçimlendir

10 basamak

### <a name="pattern"></a>Desen

10 ardışık basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_saudi_arabia_national_id"></a>Anahtar sözcüğü \_ Suudi \_ Arabistan \_ Ulusal \_ kimliği

```
Identification Card
I card number
ID number
الوطنية الهوية بطاقة رقم
```

## <a name="singapore-national-registration-identity-card-nric-number"></a>Singapur Ulusal kayıt kimlik kartı (NRIC) numarası

### <a name="format"></a>Biçimlendir

Dokuz harf ve rakam

### <a name="pattern"></a>Desen

- Dokuz harf ve rakam:
- &quot;F &quot; , &quot; G &quot; , &quot; S &quot; veya &quot; T harfi &quot; (büyük/küçük harfe duyarlı değil)
- yedi basamak
- alfabetik bir denetim hanesi

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_singapore_nric"></a>Anahtar sözcük \_ Singapur \_ nric

```
National Registration Identity Card
Identity Card Number
NRIC
IC
Foreign Identification Number
FIN
身份证
身份證
```

## <a name="south-africa-identification-number"></a>Güney Afrika kimlik numarası

### <a name="format"></a>Biçimlendir

boşluk içerebilen 13 basamak

### <a name="pattern"></a>Desen

13 basamak:

- Doğum tarihi olan YGG biçiminde altı basamak
- dört basamak
- Tek basamaklı vatandaşlık göstergesi
- &quot;8 &quot; veya &quot; 9 rakamı&quot;
- bir sayı, sağlama toplamı basamağı

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_south_africa_identification_number"></a>\_Güney \_ Afrika \_ kimlik \_ numarası anahtar sözcüğü

```
Identity card
ID
Identification
```

## <a name="south-korea-resident-registration-number"></a>Güney Kore yerleşik kayıt numarası

### <a name="format"></a>Biçimlendir

kısa çizgi içeren 13 basamak

### <a name="pattern"></a>Desen

13 basamak:

- Doğum tarihi olan YGG biçiminde altı basamak
- kısa çizgi
- yüzyıl ve cinsiyet tarafından belirlenen bir basamak
- dört basamaklı Doğum bölgesi kodu
- önceki sayıların aynısı olan kişileri ayırt etmek için kullanılan bir basamak
- denetim hanesi.

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_south_korea_resident_number"></a>\_Güney \_ Kore \_ yerleşik \_ numarası anahtar sözcüğü

```
National ID card
Citizen's Registration Number
Jumin deungnok beonho
RRN
주민등록번호
```

## <a name="spain-social-security-number-ssn"></a>İspanya sosyal güvenlik numarası (SSN)

Bu hassas bilgi türü varlığı, AB sosyal güvenlik numarası veya eşdeğer KIMLIĞI hassas bilgi türüne dahil edilmiştir ve tek başına hassas bilgi türü varlığı olarak kullanılabilir.

### <a name="format"></a>Biçimlendir

11-12 basamak

### <a name="pattern"></a>Desen

11-12 basamak:

- iki basamak
- eğik çizgi (isteğe bağlı)
- yedi-sekiz basamak
- eğik çizgi (isteğe bağlı)
- iki basamak

### <a name="keywords"></a>Anahtar sözcükler

Yok

## <a name="sweden-national-id"></a>İsveç Ulusal KIMLIĞI

### <a name="format"></a>Biçimlendir

10 veya 12 basamak ve isteğe bağlı sınırlayıcı

### <a name="pattern"></a>Desen

10 veya 12 basamak ve isteğe bağlı sınırlayıcı:

- iki basamak (isteğe bağlı)
- Tarih biçiminde altı basamak yaa
- or sınırlayıcısı &quot; - &quot; &quot; + &quot; (isteğe bağlı)
- dört basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keywords_swedish_national_identifier"></a>Anahtar \_ sözcükleri \_ Ulusal \_ tanımlayıcı

```
ID no
ID number
ID#
identification no
identification number
identifikationsnumret#
identifikationsnumret
identitetshandling
identity document
identity no
identity number
id-nummer
personal ID
personnummer#
personnummer
skatteidentifikationsnummer
```

## <a name="sweden-passport-number"></a>İsveç Passport numarası

Bu hassas bilgi türü varlığı, AB Passport numarası hassas bilgi türüne dahildir ve tek başına hassas bilgi türü varlığı olarak kullanılabilir.

### <a name="format"></a>Biçimlendir

sekiz basamak

### <a name="pattern"></a>Desen

Sekiz ardışık basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_sweden_passport"></a>\_İsveç \_ Passport anahtar sözcüğü

```
visa requirements
Alien Registration Card
Schengen visas
Schengen visa
Visa Processing
Visa Type
Single Entry
Multiple Entries
G3 Processing Fees
```

#### <a name="keyword_passport"></a>Anahtar sözcük \_ Passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="swift-code"></a>SWIFT kodu

### <a name="format"></a>Biçimlendir

dört harf ve ardından 5-31 harf veya rakam

### <a name="pattern"></a>Desen

dört harf ve ardından 5-31 harf veya rakam:

- dört harfli banka kodu (büyük/küçük harfe duyarlı değil)
- isteğe bağlı bir alan
- 4-28 harf veya basamak (temel banka hesap numarası (BBAN))
- isteğe bağlı bir alan
- bir-üç harf veya basamak (BBAN 'nin geri kalanı)

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_swift"></a>Anahtar sözcük \_ Swift

```
international organization for standardization 9362
iso 9362
iso9362
swift#
swift code
swift number
swiftroutingnumber
swift code
swift number #
swift routing number
bic number
bic code
bic #
bic#
bank identifier code
Organization internationale de normalization 9362
rapide #
code SWIFT
le numéro de swift
swift numéro d'acheminement
le numéro BIC
#BIC
code identificateur de banque
SWIFTコード
SWIFT番号
BIC番号
BICコード
SWIFT コード
SWIFT 番号
BIC 番号
BIC コード
金融機関識別コード
金融機関コード
銀行コード
```

## <a name="taiwan-national-identification-number"></a>Tayvan Ulusal kimlik numarası

### <a name="format"></a>Biçimlendir

bir harf (Ingilizce) ve ardından dokuz basamak

### <a name="pattern"></a>Desen

bir harf (Ingilizce) ve ardından dokuz basamak:

- bir harf (Ingilizce, büyük/küçük harfe duyarlı değil)
- &quot;1 &quot; veya &quot; 2 basamağı&quot;
- sekiz basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_taiwan_national_id"></a>Anahtar sözcük \_ Tayvan \_ Ulusal \_ kimliği

```
身份證字號
身份證
身份證號碼
身份證號
身分證字號
身分證
身分證號碼
身份證號
身分證統一編號
國民身分證統一編號
簽名
蓋章
簽名或蓋章
簽章
```

## <a name="taiwan-passport-number"></a>Tayvan Passport numarası

### <a name="format"></a>Biçimlendir

- Biyometrik Passport numarası: dokuz basamak
- biyometrik olmayan pasaport numarası: dokuz basamak

### <a name="pattern"></a>Desen

Biyometrik Passport numarası:

- 3 karakteri &quot;&quot;
- sekiz basamak

biyometrik olmayan Passport numarası:

- dokuz basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_taiwan_passport"></a>\_Tayvan \_ Passport anahtar sözcüğü

```
ROC passport number
Passport number
Passport no
Passport Num
Passport #
护照
中華民國護照
Zhōnghuá Mínguó hùzhào
```

## <a name="taiwan-resident-certificate-arctarc-number"></a>Tayvan yerleşik sertifikası (yay/TARC) numarası

### <a name="format"></a>Biçimlendir

10 harf ve rakam

### <a name="pattern"></a>Desen

10 harf ve rakam:

- iki harf (büyük/küçük harfe duyarlı değil)
- sekiz basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_taiwan_resident_certificate"></a>\_Tayvan \_ yerleşik \_ sertifikası anahtar sözcüğü

```
Resident Certificate
Resident Cert
Resident Cert.
Identification card
Alien Resident Certificate
ARC
Taiwan Area Resident Certificate
TARC
居留證
外僑居留證
台灣地區居留證
```

## <a name="uk-drivers-license-number"></a>Krallık sürücünün lisans numarası

Bu hassas bilgi türü varlığı, AB sürücüsünün lisans numarası hassas bilgi türüne dahildir ve tek başına hassas bilgi türü varlığı olarak kullanılabilir.

### <a name="format"></a>Biçimlendir

Belirtilen biçimde 18 harf ve rakam birleşimi

### <a name="pattern"></a>Desen

18 harf ve rakam:

- beş harf (büyük/küçük harfe duyarlı değil) ya da &quot; &quot; bir harf yerine 9 basamak
- Tek basamaklı
- Doğum tarihi için MMDDY tarih biçiminde beş basamak (sürücü kadın ise, bu, 62 51 ila 12 ' ye kadar 50 arasında)
- iki harf (büyük/küçük harfe duyarlı değil) ya da &quot; &quot; bir harf yerine 9 basamak
- beş basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_uk_drivers_license"></a>Anahtar sözcük \_ UK \_ sürücüleri \_ lisansı

```
DVLA
light vans
quad bikes
motor cars
125cc
sidecar
tricycles
motorcycles
photo card license
learner drivers
license holder
license holders
driving licenses
driving license
dual control car
```

## <a name="uk-electoral-roll-number"></a>Krallık elektrotoral toplama numarası

### <a name="format"></a>Biçimlendir

İki harften sonra 1-4 basamak

### <a name="pattern"></a>Desen

iki harf (büyük/küçük harfe duyarlı değil) izleyen 1-4 sayı

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_uk_electoral"></a>Anahtar sözcüğü \_ UK \_ elektrotoral

- Council aday
- Aday formu
- elektrotoral kaydı
- elektrotoral rulosu

## <a name="uk-national-health-service-number"></a>Krallık Ulusal Sağlık hizmeti numarası

### <a name="format"></a>Biçimlendir

boşluklara göre ayrılan 10-17 basamak

### <a name="pattern"></a>Desen

10-17 basamak:

- üç ya da 10 basamak
- bir boşluk
- üç basamak
- bir boşluk
- dört basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_uk_nhs_number"></a>Anahtar sözcük \_ UK \_ NHS \_ numarası

```
national health service
nhs
health services authority
health authority
```

#### <a name="keyword_uk_nhs_number1"></a>Anahtar sözcük \_ UK \_ NHS \_ Sayı1

```
patient ID
patient identification
patient no
patient number
```

#### <a name="keyword_uk_nhs_number_dob"></a>Anahtar sözcük \_ UK \_ NHS \_ numarası \_ DOB

```
GP
DOB
D.O.B
Date of Birth
Birth Date
```

## <a name="uk-national-insurance-number-nino"></a>Krallık Ulusal sigorta numarası (NINO)

Bu hassas bilgi türü varlığı, AB Ulusal kimlik numarası hassas bilgi türüne dahildir ve tek başına hassas bilgi türü varlığı olarak kullanılabilir.

### <a name="format"></a>Biçimlendir

boşluk veya kesik çizgilerle ayrılmış yedi karakter veya dokuz karakter

### <a name="pattern"></a>Desen

olası iki desen:

- iki harf (geçerli bir Dokos bu ön ek içinde yalnızca belirli karakterleri kullanır; bu, bu düzenin doğrulaması, büyük/küçük harfe duyarlı değildir
- altı basamak
- ' A ', ' B ', ' C ' veya ya da ' (ön ek gibi), Sonekte yalnızca belirli karakterlere izin verilir; büyük/küçük harfe duyarlı değildir)

VEYA

- iki harf
- boşluk veya tire
- iki basamak
- boşluk veya tire
- iki basamak
- boşluk veya tire
- iki basamak
- boşluk veya tire
- ' A ', ' B ', ' C ' veya ' i '


### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_uk_nino"></a>Anahtar sözcük \_ UK \_ Nino

```
national insurance number
national insurance contributions
protection act
insurance
social security number
insurance application
medical application
social insurance
medical attention
social security
Great Britain
NI Number
NI No.
NI #
NI#
insurance#
insurance number
national insurance#
nationalinsurancenumber
```

## <a name="uk-unique-taxpayer-reference-number"></a>Krallık Benzersiz vergi mükellefi başvuru numarası

Bu hassas bilgi türü yalnızca ' de kullanılabilir:

- veri kaybı önleme ilkeleri
- iletişim uyumluluk ilkeleri
- bilgi yönetimi
- kayıt yönetimi
- Microsoft Cloud App Security

### <a name="format"></a>Biçimlendir

boşluk ve sınırlayıcılar olmadan 10 basamak

### <a name="pattern"></a>Desen

10 basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keywords_uk_eu_tax_file_number"></a>Anahtar kelimeler \_ UK \_ AB \_ vergi \_ Dosya \_ numarası

```
tax number
tax file
tax ID
tax identification no
tax identification number
tax no#
tax no
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="us-bank-account-number"></a>ABD banka hesap numarası

### <a name="format"></a>Biçimlendir

6-17 basamak

### <a name="pattern"></a>Desen

6-17 ardışık basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_usa_bank_account"></a>Anahtar sözcüğü \_ ABD \_ Banka \_ hesabı

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account.
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
```

## <a name="us-drivers-license-number"></a>ABD sürücüsünün lisans numarası

### <a name="format"></a>Biçimlendir

Duruma bağlıdır

### <a name="pattern"></a>Desen

duruma bağlıdır; Örneğin, New York:

- ddd gibi biçimlendirilen dokuz basamak eşleştirecektir.
- bbbbbbbbb gibi dokuz basamak eşleşmeyecektir.

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_us_drivers_license_abbreviations"></a>\_US \_ sürücüleri \_ Lisans \_ kısaltmalarının anahtar sözcüğü

```
DL
DLS
CDL
CDLS
ID
IDs
DL#
DLS#
CDL#
CDLS#
ID#
IDs#
ID number
ID numbers
LIC
LIC#
```

#### <a name="keyword_us_drivers_license"></a>\_US sürücüleri lisans anahtar sözcüğü \_ \_ lisansı

```
DriverLic
DriverLics
DriverLicense
DriverLicenses
Driver Lic
Driver Lics
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
identification number
identification numbers
identification #
ID card
ID cards
identification card
identification cards
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
ID card#
ID cards#
identification card#
identification cards#
```

#### <a name="keyword_state_name_drivers_license_name"></a>Anahtar sözcüğü \_ [durum \_ adı] \_ sürücüler \_ Lisans \_ adı

- durum kısaltması (örneğin, &quot; NY &quot; )
- durum adı (örneğin, &quot; New York &quot; )

## <a name="us-individual-taxpayer-identification-number-itin"></a>ABD bireysel vergi mükellefi kimlik numarası (ITIN)

### <a name="format"></a>Biçimlendir

9 ile başlayan &quot; &quot; ve &quot; &quot; dördüncü basamak olarak 7 veya 8 içeren dokuz &quot; basamak &quot; , isteğe bağlı olarak boşluk veya tire ile biçimlendirilir

### <a name="pattern"></a>Desen

miyor

- 9. basamak &quot;&quot;
- iki basamak
- boşluk veya tire
- &quot;7 &quot; veya &quot; 8&quot;
- bir basamak
- boşluk veya tire
- dört basamak

büyüklükteki

- 9. basamak &quot;&quot;
- iki basamak
- &quot;7 &quot; veya &quot; 8&quot;
- beş basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_itin"></a>İtin anahtar sözcüğü \_

```
taxpayer
tax ID
tax identification
itin
i.t.i.n.
ssn
tin
social security
tax payer
itins
tax ID
individual taxpayer
```

## <a name="us-social-security-number-ssn"></a>ABD sosyal güvenlik numarası (SSN)

### <a name="format"></a>Biçimlendir

biçimlendirilmemiş veya biçimlendirilmemiş bir düzende olabilecek dokuz basamak

>[!Note]
> PARÇAAL-2011 ' den önce verildiyse, bu sayının belirli bölümlerinin belirli aralıklarda geçerli olması gerektiği (ancak sağlama toplamı olmadığı) güçlü bir biçimlendirmeye sahip olur.
>

### <a name="pattern"></a>Desen

dört işlev, SSNs 'yi dört farklı desende arar:

- Func \_ SSNs, tire veya boşluk ile biçimlendirilmiş 2011 öncesi güçlü biçimlendirmeye sahip SSNs 'yi bulur (ddd-gg-gggg veya ddd gg gggg)
- Func \_ biçimlendirilmemiş SSK \_ , dokuz ardışık basamak olarak biçimlendirilmemiş, 2011 öncesi güçlü biçimlendirmeye sahip SSNs 'yi bulur (ddddddddd)
- Func \_ rastgele \_ biçimlendirilen \_ SSK, tireler veya boşluklar ile biçimlendirilen 2011 sonrası SSNs 'yi bulur (ddd-gg-gggg veya ddd gg gggg)
- Func \_ rastgele \_ biçimlendirilmemiş \_ SSN, dokuz ardışık basamak olarak biçimlendirilmemiş 2011 sonrasındaki SSNs 'yi bulur (ddddddggg)

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_ssn"></a>\_SSK anahtar sözcüğü

```
SSA Number
social security number
social security #
social security#
social security no
Social Security#
Soc Sec
SSN
SSNS
SSN#
SS#
SSID
```

## <a name="us--uk-passport-number"></a>ABD/BIRLEŞIK KRALLıK Passport numarası

Birleşik Krallık Passport numarası hassas bilgi türü varlığı, AB Passport numarası hassas bilgi türünde mevcuttur ve tek başına hassas bilgi türü varlığı olarak kullanılabilir.

### <a name="format"></a>Biçimlendir

dokuz basamak

### <a name="pattern"></a>Desen

Dokuz ardışık basamak

### <a name="keywords"></a>Anahtar sözcükler

#### <a name="keyword_passport"></a>Anahtar sözcük \_ Passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

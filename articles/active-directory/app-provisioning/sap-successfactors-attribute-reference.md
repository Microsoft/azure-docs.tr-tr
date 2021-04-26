---
title: SAP başarılı etmenleri öznitelik başvurusu
description: Başarılı bir şekilde hangi özniteliklerin desteklendiğini
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.reviewer: celested
ms.openlocfilehash: 0d1a25f67d3550e79b2339ee060d37acaea0c1c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99255551"
---
# <a name="sap-successfactors-attribute-reference"></a>SAP başarılı etmenleri öznitelik başvurusu

Bu makalede şu bilgileri bulabilirsiniz:

- [Varlıkları ve öznitelikleri başarılı bir şekilde çarpanları](#supported-successfactors-entities-and-attributes)
- [Varsayılan öznitelik eşlemesi](#default-attribute-mapping)

## <a name="supported-successfactors-entities-and-attributes"></a>Desteklenen başarılı etken varlıkları ve öznitelikleri

Aşağıdaki tabloda, varsayılan olarak aşağıdaki iki sağlama uygulamasında yer alan başarılı etmenler özniteliklerinin listesi verilmiştir:

- [Kullanıcı sağlamayı Active Directory için başarılı etken](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
- [Azure AD Kullanıcı sağlama için başarılı etken](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)

Ek özniteliklerin şemasını genişletmek için lütfen [SAP başarılı faktörleri tümleştirme başvurusuna](./sap-successfactors-integration-reference.md#retrieving-additional-attributes) bakın. 

| \# | Başarılı etken varlığı                  | Başarılı etken özniteliği     | İşlem Türü |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | Personıdexternal             | Okuma           |
| 2  | PerPerson                              | PersonID                     | Okuma           |
| 3  | PerPerson                              | perPersonUuid                | Okuma           |
| 4  | PerPersonal                            | displayName                  | Okuma           |
| 5  | PerPersonal                            | firstName                    | Okuma           |
| 6  | PerPersonal                            | cinsiyet                       | Okuma           |
| 7  | PerPersonal                            | lastName                     | Okuma           |
| 8  | PerPersonal                            | middleName                   | Okuma           |
| 9  | PerPersonal                            | preferredName                | Okuma           |
| 10 | Kullanıcı                                   | addressLine1                 | Okuma           |
| 11 | Kullanıcı                                   | addressLine2                 | Okuma           |
| 12 | Kullanıcı                                   | addressLIne3                 | Okuma           |
| 13 | Kullanıcı                                   | businessPhone                | Okuma           |
| 14 | Kullanıcı                                   | cellPhone                    | Okuma           |
| 15 | Kullanıcı                                   | city                         | Okuma           |
| 16 | Kullanıcı                                   | ülke                      | Okuma           |
| 17 | Kullanıcı                                   | custom01                     | Okuma           |
| 18 | Kullanıcı                                   | custom02                     | Okuma           |
| 19 | Kullanıcı                                   | custom03                     | Okuma           |
| 20 | Kullanıcı                                   | custom04                     | Okuma           |
| 21 | Kullanıcı                                   | custom05                     | Okuma           |
| 22 | Kullanıcı                                   | custom06                     | Okuma           |
| 23 | Kullanıcı                                   | custom07                     | Okuma           |
| 24 | Kullanıcı                                   | custom08                     | Okuma           |
| 25 | Kullanıcı                                   | custom09                     | Okuma           |
| 26 | Kullanıcı                                   | custom10                     | Okuma           |
| 27 | Kullanıcı                                   | custom11                     | Okuma           |
| 28 | Kullanıcı                                   | custom12                     | Okuma           |
| 29 | Kullanıcı                                   | custom13                     | Okuma           |
| 30 | Kullanıcı                                   | custom14                     | Okuma           |
| 31 | Kullanıcı                                   | EmpID                        | Okuma           |
| 32 | Kullanıcı                                   | homePhone 'dur                    | Okuma           |
| 33 | Kullanıcı                                   | jobFamily                    | Okuma           |
| 34 | Kullanıcı                                   | takma ad                     | Okuma           |
| 35 | Kullanıcı                                   | state                        | Okuma           |
| 36 | Kullanıcı                                   | timeZone                     | Okuma           |
| 37 | Kullanıcı                                   | username                     | Okuma           |
| 38 | Kullanıcı                                   | Posta kodu                      | Okuma           |
| 39 | PerPhone                               | areaCode                     | Okuma           |
| 40 | PerPhone                               | countryCode                  | Okuma           |
| 41 | PerPhone                               | uzantı                    | Okuma           |
| 42 | PerPhone                               | phoneNumber                  | Okuma           |
| 43 | PerPhone                               | phoneType                    | Okuma           |
| 44 | PerEmail                               | emailAddress                 | Okuma, yazma    |
| 45 | PerEmail                               | emailType                    | Okuma           |
| 46 | Empistihdam                          | Firstdateçalıştı              | Okuma           |
| 47 | Empistihdam                          | Lastdateçalıştı               | Okuma           |
| 48 | Empistihdam                          | userId                       | Okuma           |
| 49 | Empistihdam                          | Iskıgentworker           | Okuma           |
| 50 | EmpJob                                 | countryOfCompany             | Okuma           |
| 51 | EmpJob                                 | emplStatus                   | Okuma           |
| 52 | EmpJob                                 | endDate                      | Okuma           |
| 53 | EmpJob                                 | Başlangıç                    | Okuma           |
| 54 | EmpJob                                 | jobTitle                     | Okuma           |
| 55 | EmpJob                                 | konum                     | Okuma           |
| 65 | EmpJob                                 | customString13               | Okuma           |
| 56 | EmpJob                                 | Yönetici kimliği                    | Okuma           |
| 57 | EmpJob \. businessunit                   | businessUnit                 | Okuma           |
| 58 | EmpJob \. businessunit                   | Businessunid               | Okuma           |
| 59 | EmpJob \. şirketi                        | şirket                      | Okuma           |
| 60 | EmpJob \. şirketi                        | CompanyID                    | Okuma           |
| 61 | EmpJob \. şirketi \. countryofregistration | twoCharCountryCode           | Okuma           |
| 62 | EmpJob \. costcenter                     | costCenter                   | Okuma           |
| 63 | EmpJob \. costcenter                     | Costcenterıd                 | Okuma           |
| 64 | EmpJob \. costcenter                     | costCenterDescription        | Okuma           |
| 65 | EmpJob \. departmanı                     | bölüm                   | Okuma           |
| 66 | EmpJob \. departmanı                     | departmentId                 | Okuma           |
| 67 | EmpJob \. bölmesi                       | bölmenin                     | Okuma           |
| 68 | EmpJob \. bölmesi                       | divisionId                   | Okuma           |
| 69 | EmpJob \. jobcode                        | jobCode                      | Okuma           |
| 70 | EmpJob \. jobcode                        | Jobcodeıd                    | Okuma           |
| 71 | EmpJob \. konumu                       | LocationName                 | Okuma           |
| 72 | EmpJob \. konumu                       | officeLocationAddress        | Okuma           |
| 73 | EmpJob \. konumu                       | officeLocationCity           | Okuma           |
| 74 | EmpJob \. konumu                       | officeLocationCustomString4  | Okuma           |
| 75 | EmpJob \. konumu                       | officeLocationZipCode        | Okuma           |
| 76 | EmpJob \. paygrad                       | Paygrad                     | Okuma           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Okuma           |
| 78 | EmpEmploymentTermination               | Latestsonlandırmatarihi        | Okuma           |

## <a name="default-attribute-mapping"></a>Varsayılan öznitelik eşlemesi

Aşağıdaki tabloda, yukarıda listelenen başarılı Özellikler ve AD/Azure AD öznitelikleri arasında varsayılan öznitelik eşlemesi sağlanmaktadır. Azure AD sağlama uygulaması "eşleme" dikey penceresinde, yukarıdaki listeden öznitelikleri dahil etmek için bu varsayılan eşlemeyi değiştirebilirsiniz. 

| \# | Başarılı etken varlığı                  | Başarılı etken özniteliği | Varsayılan AD/Azure AD öznitelik eşlemesi   | İşlem açıklaması                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | Personıdexternal         | Çalışan                              | Eşleşen öznitelik olarak kullanıldı                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[Eşlenen \- kaynak Bağlayıcısı olarak kullanılmadı\] | İlk eşitleme sırasında, sağlama hizmeti personUuid var olan Objectguid\ dizinine bağlar.  |
| 3  | PerPersonal                            | displayName              | displayName                             | NA                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | NA                                                                                           |
| 5  | PerPersonal                            | lastName                 | sn                                      | NA                                                                                           |
| 6  | Kullanıcı                                   | addressLine1             | streetAddress                           | NA                                                                                           |
| 7  | Kullanıcı                                   | city                     | l                                       | NA                                                                                           |
| 8  | Kullanıcı                                   | ülke                  | co                                      | NA                                                                                           |
| 9  | Kullanıcı                                   | state                    | st                                      | NA                                                                                           |
| 10 | Kullanıcı                                   | username                 | samAccountName                          | NA                                                                                           |
| 11 | Kullanıcı                                   | Posta kodu                  | postalCode                              | NA                                                                                           |
| 12 | PerEmail                               | emailAddress             | posta                                    | NA                                                                                           |
| 13 | EmpJob                                 | jobTitle                 | başlık                                   | NA                                                                                           |
| 14 | EmpJob                                 | Yönetici kimliği                | manager                                 | NA                                                                                           |
| 15 | EmpJob \. şirketi \. countryofregistration | twoCharCountryCode       | c                                       | NA                                                                                           |
| 16 | EmpJob \. departmanı                     | bölüm               | bölüm                              | NA                                                                                           |
| 17 | EmpJob \. bölmesi                       | bölmenin                 | şirket                                 | NA                                                                                           |
| 18 | EmpJob \. konumu                       | officeLocationAddress    | streetAddress                           | NA                                                                                           |
| 19 | EmpJob \. konumu                       | officeLocationZipCode    | postalCode                              | NA                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | activeEmploymentsCount = 0 ise, account\'ı devre dışı bırakın.                                           |

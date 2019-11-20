---
title: Dinamik grup üyeliği için kurallar-Azure AD | Microsoft Docs
description: Grupları otomatik olarak doldurmak için üyelik kuralları ve bir kural başvurusu oluşturma.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6cfad04d9b0dd3537d60f2651ed341d96bd0210
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74027123"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Azure Active Directory gruplar için dinamik üyelik kuralları

Azure Active Directory (Azure AD) ' de, gruplar için dinamik üyelikleri etkinleştirmek üzere karmaşık öznitelik tabanlı kurallar oluşturabilirsiniz. Dinamik grup üyeliği, Kullanıcı ekleme ve kaldırma yönetim yükünü azaltır. Bu makalede, kullanıcılar veya cihazlar için dinamik üyelik kuralları oluşturmaya yönelik özellikler ve söz dizimi ayrıntılı olarak açıklanır. Güvenlik gruplarında veya Office 365 gruplarında dinamik üyelik için bir kural ayarlayabilirsiniz.

Bir kullanıcının veya cihazın herhangi bir özniteliği değiştiğinde, sistem, değişikliğin herhangi bir grup ekleme veya kaldırma tetikleyip tetikleyemeyeceğini görmek için bir dizindeki tüm dinamik grup kurallarını değerlendirir. Bir kullanıcı veya cihaz bir gruptaki bir kuralı karşılıyorsa, bu grubun üyesi olarak eklenir. Kuralı artık karşılamadığı takdirde bunlar kaldırılır. Dinamik bir grubun bir üyesini el ile ekleyemez veya kaldıramazsınız.

- Cihazlar veya kullanıcılar için dinamik bir grup oluşturabilirsiniz, ancak hem Kullanıcı hem de cihaz içeren bir kural oluşturamazsınız.
- Cihaz sahiplerinin özniteliklerini temel alan bir cihaz grubu oluşturamazsınız. Cihaz Üyelik kuralları yalnızca cihaz özniteliklerine başvurabilir.

> [!NOTE]
> Bu özellik bir veya daha fazla dinamik grubun üyesi olan her benzersiz kullanıcı için Azure AD Premium P1 lisansı gerektirir. Kullanıcılara, dinamik grupların üyesi olmaları için lisans atamanız gerekmez, ancak bu gibi tüm kullanıcıları kapsayacak şekilde Kiracıdaki en az sayıda lisansa sahip olmanız gerekir. Örneğin, kiracınızdaki tüm dinamik gruplarda toplam 1.000 benzersiz kullanıcınız varsa, lisans gereksinimini karşılamak için Azure AD Premium P1 için en az 1.000 lisansa sahip olmanız gerekir.
>

## <a name="rule-builder-in-the-azure-portal"></a>Azure portal kural Oluşturucu

Azure AD, önemli kurallarınızı daha hızlı bir şekilde oluşturmak ve güncelleştirmek için bir kural Oluşturucusu sağlar. Kural Oluşturucu, en fazla beş ifadeye kadar oluşturmayı destekler. Kural Oluşturucu, birkaç basit ifadeye sahip bir kural oluşturulmasını kolaylaştırır, ancak her kuralı yeniden oluşturmak için kullanılamaz. Kural Oluşturucu oluşturmak istediğiniz kuralı desteklemiyorsa, metin kutusunu kullanabilirsiniz.

Aşağıda, metin kutusunu kullanarak oluşturmanızı önerdiğimiz gelişmiş kuralların veya sözdizimi örnekleri verilmiştir:

- Beşten fazla ifadeye sahip kural
- Doğrudan raporlar kuralı
- [İşleç önceliğini](groups-dynamic-membership.md#operator-precedence) ayarlama
- [Karmaşık Ifadelerle kurallar](groups-dynamic-membership.md#rules-with-complex-expressions); Örneğin `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Kural Oluşturucu, metin kutusunda oluşturulan bazı kuralları görüntüleyemeyebilir. Kural Oluşturucu kuralı görüntüleyebilmediğinden bir ileti görebilirsiniz. Kural Oluşturucu, desteklenen sözdizimini, doğrulamayı veya dinamik grup kurallarının işlenmesini herhangi bir şekilde değiştirmez.

Daha fazla adım adım yönergeler için bkz. [dinamik grubu güncelleştirme](groups-update-rule.md).

![Dinamik bir grup için üyelik kuralı ekle](./media/groups-update-rule/update-dynamic-group-rule.png)

### <a name="rule-syntax-for-a-single-expression"></a>Tek bir ifade için kural sözdizimi

Tek bir ifade, üyelik kuralının en basit biçimidir ve yalnızca yukarıda belirtilen üç bölümden oluşur. Tek bir ifade içeren bir kural şuna benzer: `Property Operator Value`, burada özelliğin sözdizimi Object. Property adıdır.

Aşağıda, tek bir ifadeyle doğru şekilde oluşturulmuş bir üyelik kuralına örnek verilmiştir:

```
user.department -eq "Sales"
```

Parantezler tek bir ifade için isteğe bağlıdır. Üyelik kuralınız gövdesinin toplam uzunluğu 2048 karakteri aşamaz.

## <a name="constructing-the-body-of-a-membership-rule"></a>Üyelik kuralının gövdesini oluşturma

Bir grubu Kullanıcı veya cihazlarla otomatik olarak dolduran bir üyelik kuralı, doğru veya yanlış bir sonuç elde eden bir ikili ifadedir. Basit bir kuralın üç bölümü şunlardır:

- Özellik
- İşleç
- Değer

İfade içindeki parçaların sırası, söz dizimi hatalarından kaçınmak için önemlidir.

## <a name="supported-properties"></a>Desteklenen özellikler

Üyelik kuralı oluşturmak için kullanılabilecek üç tür özellik vardır.

- Boole
- Dize
- Dize koleksiyonu

Aşağıda, tek bir ifade oluşturmak için kullanabileceğiniz Kullanıcı özellikleri verilmiştir.

### <a name="properties-of-type-boolean"></a>Boole türü özellikleri

| Özellikler | İzin verilen değerler | Kullanım |
| --- | --- | --- |
| accountEnabled |doğru yanlış |User. accountEnabled-EQ doğru |
| dirSyncEnabled |doğru yanlış |User. dirSyncEnabled-EQ doğru |

### <a name="properties-of-type-string"></a>Dize türü özellikleri

| Özellikler | İzin verilen değerler | Kullanım |
| --- | --- | --- |
| city |Herhangi bir dize değeri veya *null* |(User. City-EQ "değer") |
| ülke |Herhangi bir dize değeri veya *null* |(User. Country-EQ "değer") |
| Tadı | Herhangi bir dize değeri veya *null* | (User. companyName-EQ "değer") |
| Bölüm |Herhangi bir dize değeri veya *null* |(User. Department-EQ "değer") |
| displayName |Herhangi bir dize değeri |(User. displayName-EQ "değer") |
| Çalışan |Herhangi bir dize değeri |(User. EmployeeID-EQ "Value")<br>(User. EmployeeID-ne *null*) |
| facsimileTelephoneNumber |Herhangi bir dize değeri veya *null* |(User. facsimileTelephoneNumber-EQ "değer") |
| givenName |Herhangi bir dize değeri veya *null* |(User.,-EQ "Value") |
| İş Unvanı |Herhangi bir dize değeri veya *null* |(User. jobTitle-EQ "değer") |
| posta |Herhangi bir dize değeri veya *null* (kullanıcının SMTP adresi) |(User. Mail-EQ "değer") |
| mailNickName |Herhangi bir dize değeri (kullanıcının posta diğer adı) |(User. Mailtakma ad-EQ "değer") |
| Mobil |Herhangi bir dize değeri veya *null* |(User. Mobile-EQ "değer") |
| objectId |Kullanıcı nesnesinin GUID 'SI |(User. ObjectID-EQ "11111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | Şirket içinden buluta eşitlenen kullanıcılar için şirket içi güvenlik tanımlayıcısı (SID). |(User. onPremisesSecurityIdentifier-EQ "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |None DisableStrongPassword Disablepasswordexpiasyon Disablepasswordexpima, DisableStrongPassword |(User. passwordPolicies-EQ "DisableStrongPassword") |
| physicalDeliveryOfficeName |Herhangi bir dize değeri veya *null* |(User. Physicaldeliveryofficeename-EQ "Value") |
| posta kodu |Herhangi bir dize değeri veya *null* |(User. PostaKodu-EQ "değer") |
| preferredLanguage |ISO 639-1 kodu |(User. preferredLanguage-EQ "en-US") |
| sipProxyAddress |Herhangi bir dize değeri veya *null* |(User. sipProxyAddress-EQ "değer") |
| durum |Herhangi bir dize değeri veya *null* |(User. State-EQ "değer") |
| streetAddress |Herhangi bir dize değeri veya *null* |(User. streetAddress-EQ "değer") |
| Soyadı |Herhangi bir dize değeri veya *null* |(User. soyad-EQ "Value") |
| telephoneNumber 'dır |Herhangi bir dize değeri veya *null* |(User. telephoneNumber-EQ "değer") |
| usageLocation |İki kodlu ülke kodu |(User. usageLocation-EQ "US") |
| userPrincipalName |Herhangi bir dize değeri |(User. userPrincipalName-EQ "alias@domain") |
| userType |üye Konuk *null* |(User. userType-EQ "üye") |

### <a name="properties-of-type-string-collection"></a>Dize koleksiyonu türü özellikleri

| Özellikler | İzin verilen değerler | Kullanım |
| --- | --- | --- |
| Diğer postalar |Herhangi bir dize değeri |(User. Otherpostalarını-"alias@domain" içerir) |
| proxyAddresses |SMTP: alias@domain SMTP: alias@domain |(User. proxyAddresses-"SMTP: alias@domain" içerir) |

Cihaz kuralları için kullanılan özellikler için bkz. [Cihazlar Için kurallar](#rules-for-devices).

## <a name="supported-expression-operators"></a>Desteklenen ifade işleçleri

Aşağıdaki tabloda, tek bir ifade için desteklenen tüm işleçler ve bunların sözdizimi listelenmektedir. İşleçler, kısa çizgi (-) öneki ile veya bu önek olmadan kullanılabilir.

| İşleç | Sözdizimi |
| --- | --- |
| Eşit değildir |-ne |
| Eşittir |-EQ |
| Ile birlikte başlar |-notStartsWith |
| Şununla başlar |-startsWith |
| İçermez |-notContains |
| Contains |-içerir |
| Eşleşmiyor |-notMatch |
| Eşleşebilir |-Match |
| İçinde | -ın |
| Not ın | -Notın |

### <a name="using-the--in-and--notin-operators"></a>-İn ve-Notın işleçlerini kullanma

Bir kullanıcı özniteliğinin değerini bir dizi farklı değerle karşılaştırmak istiyorsanız-ın veya-Notın işleçlerini kullanabilirsiniz. Değer listesini başlatmak ve sonlandırmak için köşeli ayraç sembolleri "[" ve "]" kullanın.

 Aşağıdaki örnekte, User. Department değeri listedeki değerlerden birine eşitse ifade true olarak değerlendirilir:

```
   user.department -in ["50001","50002","50003","50005","50006","50007","50008","50016","50020","50024","50038","50039","51100"]
```


### <a name="using-the--match-operator"></a>-Match işlecini kullanma 
**-Match** işleci herhangi bir normal ifadeyi eşleştirmek için kullanılır. Örnekler:

```
user.displayName -match "Da.*"   
```
Da DAV, David true olarak değerlendirilir, aDa yanlış olarak değerlendirilir.

```
user.displayName -match ".*vid"
```
David true olarak değerlendirilir, da false olarak değerlendirilir.

## <a name="supported-values"></a>Desteklenen değerler

Bir ifadede kullanılan değerler, aşağıdakiler de dahil olmak üzere çeşitli türlerden oluşabilir:

* Dizeler
* Boolean – true, false
* Sayılarının
* Diziler – sayı dizisi, dize dizisi

Bir ifade içinde bir değer belirtirken, hataları önlemek için doğru sözdiziminin kullanılması önemlidir. Bazı sözdizimi ipuçları şunlardır:

* Değer bir dize değilse çift tırnak işaretleri isteğe bağlıdır.
* Dize ve Regex işlemleri büyük/küçük harfe duyarlı değildir.
* Bir dize değeri çift tırnak içerdiğinde, her iki tırnak de \` karakteri kullanılarak atlanmalıdır, örneğin \`, "Sales" değeri "Sales\`" ise uygun sözdizimidir.
* Null denetimleri, değer olarak null kullanarak da yapabilirsiniz; Örneğin, `user.department -eq null`.

### <a name="use-of-null-values"></a>Null değer kullanımı

Bir kuralda null değer belirtmek için *null* değeri kullanabilirsiniz. 

* Bir ifadede *null* değeri karşılaştırırken-EQ veya-ne ' i kullanın.
* *Yalnızca bir* sabit değer dize değeri olarak yorumlanması istiyorsanız, sözcüğü etrafında tırnak işareti kullanın.
* -Not işleci, null için karşılaştırılma işleci olarak kullanılamaz. Kullanıyorsanız, null veya $null kullanıp kullanmayacağınızı bir hata alırsınız.

Null değere başvurmak için doğru yol aşağıdaki gibidir:

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>Birden çok ifade olan kurallar

Bir grup üyeliği kuralı-ve,-veya ve olmayan mantıksal işleçlerle bağlanmış birden fazla tek ifadeden oluşabilir. Mantıksal işleçler de birlikte kullanılabilir. 

Aşağıda, birden çok ifadeye sahip doğru şekilde oluşturulmuş Üyelik kuralları örnekleri verilmiştir:

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>İşleç önceliği

Tüm işleçler, en yüksekten en düşüğe öncelik sırasına göre aşağıda listelenmiştir. Aynı satırdaki operatörler eşit önceliğe sahiptir:

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

Aşağıda, iki ifadenin Kullanıcı için değerlendirildiği operatör önceinin bir örneği verilmiştir:

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

Parantezler yalnızca öncelik gereksinimlerinizi karşılamıyorsa gereklidir. Örneğin, bölümün ilk olarak değerlendirilmesini istiyorsanız, sıralamayı belirlemede parantez nasıl kullanılabileceğini gösterir:

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>Karmaşık ifadelerle kurallar

Bir üyelik kuralı, özelliklerin, işleçlerin ve değerlerin daha karmaşık formlar üzerinde aldığı karmaşık ifadelerden oluşabilir. Aşağıdakilerden biri doğruysa ifadeler karmaşık kabul edilir:

* Özelliği bir değerler koleksiyonundan oluşur; Özellikle, çok değerli özellikler
* İfadeler-any ve-All işleçlerini kullanır
* İfadenin değeri bir veya daha fazla ifade olabilir

## <a name="multi-value-properties"></a>Çoklu değerli özellikler

Birden çok değerli özellikler aynı türdeki nesne koleksiyonlarıdır. -Any ve-All mantıksal işleçlerini kullanarak Üyelik kuralları oluşturmak için kullanılabilirler.

| Özellikler | Değerler | Kullanım |
| --- | --- | --- |
| assignedPlans | Koleksiyondaki her nesne şu dize özelliklerini kullanıma sunar: capabilityStatus, Service, Serviceplanıd |User. assignedPlans-any (assignedPlan. Serviceplanıd-EQ "efb87545-963c-4e0d-99df-69c6916d9eb0"-ve assignedPlan. capabilityStatus-EQ "Enabled") |
| proxyAddresses| SMTP: alias@domain SMTP: alias@domain | (User. proxyAddresses-any (\_-içerir "contoso")) |

### <a name="using-the--any-and--all-operators"></a>-Any ve-All işleçlerini kullanma

-Any ve-All işleçlerini, sırasıyla koleksiyondaki öğelerin birine veya tümüne koşul uygulamak için kullanabilirsiniz.

* -Any (koleksiyonda en az bir öğe koşulla eşleştiğinde karşılandı)
* -Tümü (koleksiyondaki tüm öğeler koşulla eşleşiyorsa karşılandı)

#### <a name="example-1"></a>Örnek 1

assignedPlans, kullanıcıya atanan tüm hizmet planlarını listeleyen bir çoklu değerli özelliktir. Aşağıdaki ifade, etkin durumda olan Exchange Online (plan 2) hizmet planına (GUID değeri olarak) sahip kullanıcıları seçer:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

Bu bir kural, bir Office 365 (veya diğer Microsoft çevrimiçi hizmeti) özelliğinin etkinleştirildiği tüm kullanıcıları gruplandırmak için kullanılabilir. Daha sonra gruba bir ilke kümesiyle uygulayabilirsiniz.

#### <a name="example-2"></a>Örnek 2

Aşağıdaki ifade, Intune hizmetiyle ilişkili herhangi bir hizmet planına sahip tüm kullanıcıları seçer (hizmet adı "SCO" ile tanımlanır):

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

### <a name="using-the-underscore-_-syntax"></a>Alt çizgi (\_) sözdizimini kullanma

Alt çizgi (\_) sözdizimi, dinamik bir gruba kullanıcı veya cihaz eklemek için çok değerli dize koleksiyonu özelliklerinden birindeki belirli bir değerin oluşumlarıyla eşleşir. -Any veya-All işleçleri ile kullanılır.

Burada, User. proxyAddress tabanlı üye eklemek için bir kuralda alt çizgi (\_) kullanılmasına örnek verilmiştir (User. Otherpostalarda aynı şekilde çalışmaktadır). Bu kural, "contoso" içeren proxy adresine sahip tüm kullanıcıları gruba ekler.

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>Diğer özellikler ve ortak kurallar

### <a name="create-a-direct-reports-rule"></a>"Doğrudan rapor" kuralı oluşturma

Bir yöneticinin tüm doğrudan raporlarını içeren bir grup oluşturabilirsiniz. Daha sonra yöneticinin doğrudan raporları değiştiğinde grubun üyeliği otomatik olarak ayarlanır.

Doğrudan raporlar kuralı aşağıdaki sözdizimi kullanılarak oluşturulur:

```
Direct Reports for "{objectID_of_manager}"
```

Aşağıda, "62e19b97-8b3d-4d4a-A106-4ce66896a863" öğesinin, yöneticinin ObjectID olduğu geçerli bir kurala örnek verilmiştir:

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

Aşağıdaki ipuçları, kuralı düzgün şekilde kullanmanıza yardımcı olabilir.

- **Yönetıcı kimliği** , YÖNETICININ nesne kimliğidir. Bu, yöneticinin **profilinde**bulunabilir.
- Kuralın çalışması için, kiracınızdaki kullanıcılar için **Manager** özelliğinin doğru ayarlandığından emin olun. Kullanıcının **profilindeki**geçerli değeri kontrol edebilirsiniz.
- Bu kural yalnızca yöneticinin doğrudan raporlarını destekler. Diğer bir deyişle, yöneticinin doğrudan raporlarının *ve* raporlarının bulunduğu bir grup oluşturamazsınız.
- Bu kural diğer üyelik kurallarıyla birleştirilemez.

### <a name="create-an-all-users-rule"></a>"Tüm kullanıcılar" kuralı oluşturma

Bir üyelik kuralı kullanarak bir kiracının içindeki tüm kullanıcıları içeren bir grup oluşturabilirsiniz. Kullanıcılar gelecekte kiracıya eklendiğinde veya kiracıdan çıkarıldığında grubun üyeliği otomatik olarak ayarlanır.

"Tüm kullanıcılar" kuralı-ne işleci ve null değeri kullanılarak tek bir ifade kullanılarak oluşturulur. Bu kural, B2B Konuk kullanıcılarını ve üye kullanıcıları gruba ekler.

```
user.objectid -ne null
```

### <a name="create-an-all-devices-rule"></a>"Tüm cihazlar" kuralı oluşturma

Bir üyelik kuralı kullanarak bir kiracının içindeki tüm cihazları içeren bir grup oluşturabilirsiniz. Cihazlarda bir cihaz eklendiğinde veya kiracıya kaldırıldığında grubun üyeliği otomatik olarak ayarlanır.

"Tüm cihazlar" kuralı-ne işleci ve null değeri kullanılarak tek bir ifade kullanılarak oluşturulur:

```
device.objectid -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>Uzantı özellikleri ve özel uzantı özellikleri

Uzantı öznitelikleri ve özel uzantı özellikleri, dinamik üyelik kurallarında dize özellikleri olarak desteklenir. Uzantı öznitelikleri şirket içi Windows Server AD 'den eşitlenir ve "ExtensionAttributeX" biçimini alır, burada X eşittir 1-15. Bir özellik olarak uzantı özniteliği kullanan bir kurala örnek aşağıda verilmiştir:

```
(user.extensionAttribute15 -eq "Marketing")
```

Özel uzantı özellikleri, şirket içi Windows Server AD 'den veya bağlı bir SaaS uygulamasından eşitlenir ve `user.extension_[GUID]_[Attribute]`biçimindedir, burada:

* [GUID], Azure AD 'de özelliği oluşturan uygulama için Azure AD 'de benzersiz tanıtıcıdır
* [Attribute], oluşturulduğu şekliyle özelliğin adıdır

Özel uzantı özelliği kullanan bir kurala örnek:

```
user.extension_c272a57b722d4eb29bfe327874ae79cb_OfficeNumber -eq "123"
```

Özel özellik adı, Graph Explorer kullanılarak bir kullanıcının özelliği sorgulanarak ve özellik adı aranırken dizinde bulunabilir. Ayrıca, benzersiz bir uygulama KIMLIĞI girmek ve dinamik üyelik kuralı oluştururken kullanmak üzere özel uzantı özelliklerinin tam listesini almak için dinamik Kullanıcı grubu kural tasarımcısında **özel uzantı özellikleri al** bağlantısını seçebilirsiniz. Bu liste, bu uygulama için tüm yeni özel uzantı özellikleri almak için aynı zamanda yenilenebilir.

## <a name="rules-for-devices"></a>Cihazlar için kurallar

Ayrıca, bir gruptaki üyelik için cihaz nesneleri seçen bir kural oluşturabilirsiniz. Grup üyeleri olarak hem Kullanıcı hem de cihaz ekleyemezsiniz. 

> [!NOTE]
> **OrganizationalUnit** özniteliği artık listelenmez ve kullanılmamalıdır. Bu dize, Intune tarafından belirli durumlarda ayarlanır ancak Azure AD tarafından tanınmamaktadır, bu nedenle bu özniteliğe göre gruplara hiçbir cihaz eklenmez.

> [!NOTE]
> systemlabels, Intune ile ayarlanamaz salt okunurdur.
>
> Windows 10 için, deviceOSVersion özniteliğinin doğru biçimi şu şekildedir: (Device. deviceOSVersion-EQ "10,0 (17763)"). Biçimlendirme, Get-MsolDevice PowerShell cmdlet 'i ile doğrulanabilir.

Aşağıdaki cihaz öznitelikleri kullanılabilir.

 Cihaz özniteliği  | Değerler | Örnek
 ----- | ----- | ----------------
 accountEnabled | doğru yanlış | (Device. accountEnabled-EQ true)
 displayName | Herhangi bir dize değeri |(Device. displayName-EQ "Ramiz iPhone")
 Cihazostürü | Herhangi bir dize değeri | (Device. deviceOSType-EQ "iPad")-veya (Device. deviceOSType-EQ "iPhone")<br>(Device. deviceOSType-"AndroidEnterprise" içerir)<br>(Device. deviceOSType-EQ "AndroidForWork")
 deviceOSVersion | Herhangi bir dize değeri | (Device. deviceOSVersion-EQ "9,1")
 deviceCategory | geçerli bir cihaz kategorisi adı | (Device. deviceCategory-EQ "BYOD")
 deviceManufacturer | Herhangi bir dize değeri | (Device. deviceManufacturer-EQ "Samsung")
 DeviceModel | Herhangi bir dize değeri | (Device. deviceModel-EQ "iPad hava")
 Devicesahiplik | Kişisel, Şirket, bilinmeyen | (Device. Devicesahiplik-EQ "Şirket")
 enrollmentProfileName | Apple cihaz kayıt profili, cihaz kaydı-kurumsal cihaz tanımlayıcıları (Android-bilgi noktası) veya Windows Autopilot profili adı | (Device. kayıtlarına Mentprofilename-EQ "DEP IPhone")
 IBir kökü belirtilmiş | doğru yanlış | (Device. ısınroot-EQ true)
 managementType | MDM (mobil cihazlar için)<br>BILGISAYAR (Intune bılgısayar Aracısı tarafından yönetilen bilgisayarlar için) | (Device. managementType-EQ "MDM")
 deviceId | geçerli bir Azure AD cihaz KIMLIĞI | (Device. DeviceID-EQ "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | geçerli bir Azure AD nesne KIMLIĞI |  (Device. ObjectID-EQ "76ad43c9-32c5-45e8-a272-7b58b58f596d")
 Devicephysicilar | Tüm Autopilot cihazları, OrderID veya PurchaseOrderID gibi Autopilot tarafından kullanılan herhangi bir dize değeri  | (Device. Devicephysicids-any _-Contains "[Ztdıd]") (Device. Devicephysicids-any _-EQ "[OrderID]: 179887111881") (Device. Devicephysicids-any _-EQ "[PurchaseOrderId]: 76222342342")
 systemLabels | Modern çalışma alanı cihazlarını etiketlemek için Intune cihaz özelliği ile eşleşen tüm dizeler | (Device. systemLabels-"M365Managed" içerir)

> [!Note]  
> Cihazlar için dinamik gruplar oluştururken Devicesahiplik için, "Şirket" değerine eşit değeri ayarlamanız gerekir. Intune 'da cihaz sahipliği, şirket yerine temsil edilir. Daha fazla ayrıntı için [Ownertypes](https://docs.microsoft.com/intune/reports-ref-devices#ownertypes) öğesine bakın. 

## <a name="next-steps"></a>Sonraki adımlar

Bu makaleler Azure Active Directory gruplar hakkında ek bilgiler sağlar.

- [Var olan grupları görme](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Yeni grup oluşturma ve üye ekleme](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Bir grubun ayarlarını yönetme](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Bir grubun üyeliklerini yönetme](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Bir gruptaki kullanıcılar için dinamik kuralları yönetme](groups-create-rule.md)

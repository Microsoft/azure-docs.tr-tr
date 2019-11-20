---
title: Azure blok zinciri çalışma ekranı önizlemesinde veritabanı görünümleri
description: Azure blok zinciri 'nin önizlemesine genel bakış SQL DB veritabanı görünümleri.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 7548d460d0d99642d11e4eb5755730400b509e94
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845050"
---
# <a name="database-views-in-azure-blockchain-workbench-preview"></a>Azure blok zinciri çalışma ekranı önizlemesinde veritabanı görünümleri

Azure blok zinciri çalışma ekranı önizlemesi, dağıtılmış bağış *zincirlerden zincir dışı* BIR SQL DB veritabanına veri sağlar. Zincir dışı veritabanı, blok zinciri verileriyle etkileşim kurmak için SQL ve [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)gibi mevcut araçları kullanmayı mümkün kılar.

Azure blok zinciri çalışma ekranı, sorgularınızı gerçekleştirirken yararlı olacak verilere erişim sağlayan bir veritabanı görünümleri kümesi sağlar. Bu görünümler, kolayca rapor, analiz oluşturmaya ve mevcut araçlarla blok zinciri verilerini kullanmaya ve veritabanı personelini yeniden eğmenize gerek kalmadan hızlı bir şekilde daha kolay hale getirilir.

Bu bölüm, veritabanı görünümlerine ve içerdikleri verilere ilişkin bir genel bakış içerir.

> [!NOTE]
> Veritabanında bu görünümlerin dışında bulunan veritabanı tablolarının doğrudan kullanımı, mümkün olduğunca desteklenmez.
>

## <a name="vwapplication"></a>diğer uygulama

Bu görünüm, Azure blok zinciri çalışma ekranına yüklenmiş **uygulamalarla** ilgili ayrıntıları sağlar.

| Name                             | Type          | Null olabilir | Açıklama                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                    | int           | Hayır          | Uygulama için benzersiz bir tanımlayıcı |
| ApplicationName                  | nvarchar (50)  | Hayır          | Uygulamanın adı |
| ApplicationDescription           | nvarchar (255) | Evet         | Uygulamanın açıklaması |
| applicationDisplayName           | nvarchar (255) | Hayır          | Kullanıcı arabiriminde görüntülenecek ad |
| ApplicationEnabled               | bit           | Hayır          | Uygulamanın Şu anda etkin olup olmadığını belirtir<br /> **Not:** Bir uygulama veritabanında devre dışı olarak yansıtılmasına rağmen, ilişkili sözleşmeler blok zincirinde kalır ve bu anlaşmalar hakkındaki veriler veritabanında kalır. |
| UploadedDtTm                     | datetime2 (7)  | Hayır          | Bir sözleşmenin karşıya yüklendiği tarih ve saat |
| Uploadedbyuserıd                 | int           | Hayır          | Uygulamayı karşıya yükleyen kullanıcının KIMLIĞI |
| Uploadedbyuserexternalıd         | nvarchar (255) | Hayır          | Uygulamayı karşıya yükleyen kullanıcının dış tanımlayıcısı. Varsayılan olarak, bu KIMLIK, konsorsiyumun Azure Active Directory Kullanıcı olur.                                                                                                |
| UploadedByUserProvisioningStatus | int           | Hayır          | Kullanıcı için sağlama işleminin geçerli durumunu tanımlar. Olası değerler şunlardır: <br />0 – Kullanıcı API tarafından oluşturuldu<br />1 – veritabanındaki kullanıcıyla ilişkili bir anahtar<br />2 – Kullanıcı tam olarak sağlanıyor                         |
| UploadedByUserFirstName          | nvarchar (50)  | Evet         | Sözleşmeyi karşıya yükleyen kullanıcının adı |
| UploadedByUserLastName           | nvarchar (50)  | Evet         | Sözleşmeyi karşıya yükleyen kullanıcının soyadı |
| Uploadedbyuseremapostaadresi       | nvarchar (255) | Evet         | Sözleşmeyi karşıya yükleyen kullanıcının e-posta adresi |

## <a name="vwapplicationrole"></a>Öapplicationrole

Bu görünüm, Azure blok zinciri çalışma ekranı uygulamalarında tanımlanmış rollerle ilgili ayrıntıları sağlar.

Bir *varlık aktarım* uygulamasında, örneğin, *Satınalmacı* ve *satıcı* rolleri gibi roller tanımlanabilir.

| Name                   | Type             | Null olabilir | Açıklama                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationId          | int              | Hayır          | Uygulama için benzersiz bir tanımlayıcı           |
| ApplicationName        | nvarchar (50)     | Hayır          | Uygulamanın adı                       |
| ApplicationDescription | nvarchar (255)    | Evet         | Uygulamanın açıklaması                  |
| applicationDisplayName | nvarchar (255)    | Hayır          | Kullanıcı arabiriminde görüntülenecek ad      |
| RoleID                 | int              | Hayır          | Uygulamadaki bir rol için benzersiz bir tanımlayıcı |
| RoleName               | nvarchar50)      | Hayır          | Rolün adı                              |
| RoleDescription        | Açıklama (255) | Evet         | Rolün açıklaması                         |

## <a name="vwapplicationroleuser"></a>Öapplicationroleuser

Bu görünüm, Azure blok zinciri çalışma ekranı uygulamalarında tanımlanmış roller ve bunlarla ilişkili kullanıcılar hakkında ayrıntılar sağlar.

Bir *varlık aktarım* uygulamasında, örneğin *John Smith* , *alıcı* rolüyle ilişkilendirilebilir.

| Name                       | Type          | Null olabilir | Açıklama                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId              | int           | Hayır          | Uygulama için benzersiz bir tanımlayıcı                                                                                                                                                                                               |
| ApplicationName            | nvarchar (50)  | Hayır          | Uygulamanın adı                                                                                                                                                                                                           |
| ApplicationDescription     | nvarchar (255) | Evet         | Uygulamanın açıklaması                                                                                                                                                                                                      |
| applicationDisplayName     | nvarchar (255) | Hayır          | Kullanıcı arabiriminde görüntülenecek ad                                                                                                                                                                                          |
| Applicationroleıd          | int           | Hayır          | Uygulamadaki bir rol için benzersiz bir tanımlayıcı                                                                                                                                                                                     |
| ApplicationRoleName        | nvarchar50)   | Hayır          | Rolün adı                                                                                                                                                                                                                  |
| ApplicationRoleDescription | nvarchar (255) | Evet         | Rolün açıklaması                                                                                                                                                                                                             |
| UserId                     | int           | Hayır          | Rolle ilişkili kullanıcının KIMLIĞI |
| Userexternalıd             | nvarchar (255) | Hayır          | Rolüyle ilişkilendirilen kullanıcı için dış tanımlayıcı. Varsayılan olarak, bu KIMLIK, konsorsiyumun Azure Active Directory Kullanıcı olur.                                                                     |
| UserProvisioningStatus     | int           | Hayır          | Kullanıcı için sağlama işleminin geçerli durumunu tanımlar. Olası değerler şunlardır: <br />0 – Kullanıcı API tarafından oluşturuldu<br />1 – veritabanındaki kullanıcıyla ilişkili bir anahtar<br />2 – Kullanıcı tam olarak sağlanıyor |
| Kullanıcı adı              | nvarchar (50)  | Evet         | Rolle ilişkilendirilen kullanıcının adı |
| UserLastName               | nvarchar (255) | Evet         | Rolle ilişkili kullanıcının soyadı |
| Kullanıcıeadresi           | nvarchar (255) | Evet         | Rolle ilişkili kullanıcının e-posta adresi |

## <a name="vwconnectionuser"></a>Diğer Kullanıcı

Bu görünüm, Azure blok zinciri çalışma ekranı ve bunlarla ilişkili kullanıcılarla ilgili ayrıntıları sağlar. Her bağlantı için, bu görünüm aşağıdaki verileri içerir:

-   İlişkili muhasebe ayrıntıları
-   İlişkili kullanıcı bilgileri

| Name                     | Type          | Null olabilir | Açıklama                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionID             | int           | Hayır          | Azure blok zinciri çalışma ekranı 'ndaki bir bağlantının benzersiz tanımlayıcısı |
| ConnectionEndpointUrl    | nvarchar (50)  | Hayır          | Bir bağlantı için uç nokta URL 'si |
| ConnectionFundingAccount | nvarchar (255) | Evet         | Varsa, bir bağlantıyla ilişkili komik hesap |
| LedgerID                 | int           | Hayır          | Bir muhasebe için benzersiz tanımlayıcı |
| LedgerName               | nvarchar (50)  | Hayır          | Genel muhasebe adı |
| LedgerDisplayName        | nvarchar (255) | Hayır          | Kullanıcı arabiriminde görüntülenecek olan genel muhasebe adı |
| UserId                   | int           | Hayır          | Bağlantıyla ilişkili kullanıcının KIMLIĞI |
| Userexternalıd           | nvarchar (255) | Hayır          | Bağlantıyla ilişkili kullanıcı için dış tanımlayıcı. Varsayılan olarak, bu KIMLIK, konsorsiyumun Azure Active Directory Kullanıcı olur. |
| UserProvisioningStatus   | int           | Hayır          |Kullanıcı için sağlama işleminin geçerli durumunu tanımlar. Olası değerler şunlardır: <br />0 – Kullanıcı API tarafından oluşturuldu<br />1 – veritabanındaki kullanıcıyla ilişkili bir anahtar<br />2 – Kullanıcı tam olarak sağlanıyor |
| Kullanıcı adı            | nvarchar (50)  | Evet         | Bağlantıyla ilişkili kullanıcının adı |
| UserLastName             | nvarchar (255) | Evet         | Bağlantıyla ilişkili kullanıcının soyadı |
| Kullanıcıeadresi         | nvarchar (255) | Evet         | Bağlantıyla ilişkili kullanıcının e-posta adresi |

## <a name="vwcontract"></a>diğer sözleşme

Bu görünüm, dağıtılan sözleşmeler hakkında ayrıntılı bilgi sağlar. Her sözleşme için, bu görünüm aşağıdaki verileri içerir:

-   İlişkili uygulama tanımı
-   İlişkili iş akışı tanımı
-   İşlev için ilişkili defter uygulama
-   Eylemi başlatan kullanıcının ayrıntıları
-   Blok zinciri bloğu ve işlemle ilgili ayrıntılar

| Name                                     | Type           | Null olabilir | Açıklama                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionID                             | int            | Hayır          | Azure blok zinciri çalışma ekranı 'ndaki bir bağlantı için benzersiz tanımlayıcı.                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar (50)   | Hayır          | Bir bağlantı için uç nokta URL 'si |
| ConnectionFundingAccount                 | nvarchar (255)  | Evet         | Varsa, bir bağlantıyla ilişkili komik hesap |
| LedgerID                                 | int            | Hayır          | Bir muhasebe için benzersiz tanımlayıcı |
| LedgerName                               | nvarchar (50)   | Hayır          | Genel muhasebe adı |
| LedgerDisplayName                        | nvarchar (255)  | Hayır          | Kullanıcı arabiriminde görüntülenecek olan genel muhasebe adı |
| ApplicationId                            | int            | Hayır          | Uygulama için benzersiz bir tanımlayıcı |
| ApplicationName                          | nvarchar (50)  | Hayır          | Uygulamanın adı |
| applicationDisplayName                   | nvarchar (255) | Hayır          | Kullanıcı arabiriminde görüntülenecek ad |
| ApplicationEnabled                       | bit            | Hayır          | Uygulamanın Şu anda etkin olup olmadığını tanımlar.<br /> **Not:** Bir uygulama veritabanında devre dışı olarak yansıtılmasına rağmen, ilişkili sözleşmeler blok zincirinde kalır ve bu anlaşmalar hakkındaki veriler veritabanında kalır.  |
| Workflowıd                               | int            | Hayır          | Bir sözleşmeyle ilişkili iş akışı için benzersiz bir tanımlayıcı |
| workflowName                             | nvarchar (50)   | Hayır          | Bir sözleşmeyle ilişkili iş akışının adı |
| WorkflowDisplayName                      | nvarchar (255)  | Hayır          | Kullanıcı arabiriminde görünen sözleşmeyle ilişkili iş akışının adı |
| WorkflowDescription                      | nvarchar (255)  | Evet         | Bir sözleşmeyle ilişkili iş akışının açıklaması |
| Contractcodeıd                           | int            | Hayır          | Sözleşmeyle ilişkili sözleşme kodu için benzersiz bir tanımlayıcı |
| ContractFileName                         | int            | Hayır          | Bu iş akışının akıllı sözleşme kodunu içeren dosyanın adı. |
| ContractUploadedDtTm                     | int            | Hayır          | Sözleşme kodunun karşıya yüklendiği tarih ve saat |
| sözleşme kimliği                               | int            | Hayır          | Sözleşme için benzersiz tanımlayıcı |
| ContractProvisioningStatus               | int            | Hayır          | Sözleşme için sağlama işleminin geçerli durumunu tanımlar. Olası değerler şunlardır: <br />0 – sözleşme, veritabanında API tarafından oluşturulmuştur<br />1 – sözleşme, genel muhasebeye gönderildi<br />2 – sözleşme, genel muhasebeye başarıyla dağıtıldı<br />3 veya 4-Sözleşme, muhasebeye dağıtılamadı<br />5-sözleşme, genel muhasebeye başarıyla dağıtıldı <br /><br />Sürüm 1,5 ' den başlayarak, 0 ile 5 arasındaki değerler desteklenir. Geçerli yayında geriye dönük uyumluluk için, **vwContractV0** , yalnızca 0 ile 2 arasındaki değerleri destekleyen bir görünüm bulunur. |
| Contractledgerıdentifier                 | nvarchar (255) |             | Sözleşmeyi dağıtan kullanıcının e-posta adresi |
| Contractdeployedbyuserıd                 | int            | Hayır          | Sözleşmeyi dağıtan Kullanıcı için bir dış tanımlayıcı. Varsayılan olarak, bu KIMLIK kullanıcının Azure Active Directory KIMLIĞINI temsil eden GUID 'dir.                                                                                                          |
| Contractdeployedbyuserexternalıd         | nvarchar (255)  | Hayır          | Sözleşmeyi dağıtan Kullanıcı için bir dış tanımlayıcı. Varsayılan olarak, bu KIMLIK kullanıcının Azure Active Directory KIMLIĞINI temsil eden GUID 'dir.                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | Hayır          | Kullanıcı için sağlama işleminin geçerli durumunu tanımlar. Olası değerler şunlardır: <br />0 – Kullanıcı API tarafından oluşturuldu<br />1 – veritabanındaki kullanıcıyla ilişkili bir anahtar <br />2 – Kullanıcı tam olarak sağlanıyor                     |
| ContractDeployedByUserFirstName          | nvarchar (50)   | Evet         | Sözleşmeyi dağıtan kullanıcının adı |
| ContractDeployedByUserLastName           | nvarchar (255)  | Evet         | Sözleşmeyi dağıtan kullanıcının soyadı |
| Contractdeployedbyuseremaadresi       | nvarchar (255)  | Evet         | Sözleşmeyi dağıtan kullanıcının e-posta adresi |

## <a name="vwcontractaction"></a>Öcontractaction

Bu görünüm, sözleşmelerde gerçekleştirilen eylemlerle ilgili bilgilerin büyük bölümünü temsil eder ve ortak raporlama senaryolarını kolaylaştırmak için tasarlanmıştır. Gerçekleştirilen her bir eylem için, bu görünüm aşağıdaki verileri içerir:

-   İlişkili uygulama tanımı
-   İlişkili iş akışı tanımı
-   İlişkili akıllı sözleşme işlevi ve parametre tanımı
-   İşlev için ilişkili defter uygulama
-   Parametreler için belirtilen belirli örnek değerleri
-   Eylemi başlatan kullanıcının ayrıntıları
-   Blok zinciri bloğu ve işlemle ilgili ayrıntılar

| Name                                     | Type          | Null olabilir | Açıklama                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                            | int           | Hayır          | Uygulama için benzersiz bir tanımlayıcı |
| ApplicationName                          | nvarchar (50)  | Hayır          | Uygulamanın adı |
| applicationDisplayName                   | nvarchar (255) | Hayır          | Kullanıcı arabiriminde görüntülenecek ad |
| ApplicationEnabled                       | bit           | Hayır          | Bu alan, uygulamanın Şu anda etkin olup olmadığını belirtir. Note: bir uygulamanın veritabanında devre dışı olarak yansıtılmasına karşın, ilişkili sözleşmeler blok zincirinde kalır ve bu anlaşmalar hakkındaki veriler veritabanında kalır.                                                  |
| Workflowıd                               | int           | Hayır          | Bir iş akışı için benzersiz bir tanımlayıcı |
| workflowName                             | nvarchar (50)  | Hayır          | İş akışının adı |
| WorkflowDisplayName                      | nvarchar (255) | Hayır          | Kullanıcı arabiriminde görüntülenecek iş akışı adı |
| WorkflowDescription                      | nvarchar (255) | Evet         | İş akışının açıklaması |
| sözleşme kimliği                               | int           | Hayır          | Sözleşme için benzersiz bir tanımlayıcı |
| ContractProvisioningStatus               | int           | Hayır          | Sözleşme için sağlama işleminin geçerli durumunu tanımlar. Olası değerler şunlardır: <br />0 – sözleşme, veritabanında API tarafından oluşturulmuştur<br />1 – sözleşme, genel muhasebeye gönderildi<br />2 – sözleşme, genel muhasebeye başarıyla dağıtıldı<br />3 veya 4-Sözleşme, muhasebeye dağıtılamadı<br />5-sözleşme, genel muhasebeye başarıyla dağıtıldı <br /><br />Sürüm 1,5 ' den başlayarak, 0 ile 5 arasındaki değerler desteklenir. Geçerli yayında geriye dönük uyumluluk için, **vwContractActionV0** , yalnızca 0 ile 2 arasındaki değerleri destekleyen bir görünüm bulunur. |
| Contractcodeıd                           | int           | Hayır          | Sözleşmenin kod uygulamasına yönelik benzersiz bir tanımlayıcı |
| Contractledgerıdentifier                 | nvarchar (255) | Evet         | Belirli bir dağıtılmış defter için, bir akıllı sözleşmenin dağıtılan sürümüyle ilişkili benzersiz bir tanımlayıcı. Örneğin, Ethereum. |
| Contractdeployedbyuserıd                 | int           | Hayır          | Sözleşmeyi dağıtan kullanıcının benzersiz tanımlayıcısı |
| ContractDeployedByUserFirstName          | nvarchar (50)  | Evet         | Sözleşmeyi dağıtan kullanıcının adı |
| ContractDeployedByUserLastName           | nvarchar (255) | Evet         | Sözleşmeyi dağıtan kullanıcının soyadı |
| Contractdeployedbyuserexternalıd         | nvarchar (255) | Hayır          | Sözleşmeyi dağıtan kullanıcının dış tanımlayıcısı. Varsayılan olarak, bu KIMLIK, Konsorsiyumu Azure Active Directory kimliğini temsil eden GUID 'dir.                                                                                                                                                |
| Contractdeployedbyuseremaadresi       | nvarchar (255) | Evet         | Sözleşmeyi dağıtan kullanıcının e-posta adresi |
| Workflowfunctionıd                       | int           | Hayır          | Bir iş akışı işlevi için benzersiz bir tanımlayıcı |
| Workflowfonksiyonadı                     | nvarchar (50)  | Hayır          | İşlevin adı |
| WorkflowFunctionDisplayName              | nvarchar (255) | Hayır          | Kullanıcı arabiriminde gösterilecek işlevin adı |
| WorkflowFunctionDescription              | nvarchar (255) | Hayır          | İşlevin açıklaması |
| Sözleşmeli Tactionıd                         | int           | Hayır          | Bir sözleşme eyleminin benzersiz tanımlayıcısı |
| Sözleşmeli Tactionprovisioningstatus         | int           | Hayır          | Sözleşme eylemi için sağlama işleminin geçerli durumunu tanımlar. Olası değerler şunlardır: <br />0 – sözleşme eylemi, veritabanında API tarafından oluşturuldu<br />1 – sözleşme eylemi, genel muhasebeye gönderildi<br />2 – sözleşme eylemi, genel muhasebeye başarıyla dağıtıldı<br />3 veya 4-Sözleşme, muhasebeye dağıtılamadı<br />5-sözleşme, genel muhasebeye başarıyla dağıtıldı <br /><br />Sürüm 1,5 ' den başlayarak, 0 ile 5 arasındaki değerler desteklenir. Geçerli yayında geriye dönük uyumluluk için, **vwContractActionV0** , yalnızca 0 ile 2 arasındaki değerleri destekleyen bir görünüm bulunur. |
| Sözleşmeli Tactiontimestamp                  | Tarih/saat (2, 7) | Hayır          | Sözleşme eyleminin zaman damgası |
| Sözleşmeli Tactionexecutedbyuserıd           | int           | Hayır          | Sözleşme eylemini yürüten kullanıcının benzersiz tanımlayıcısı |
| Sözleşmeli Tactionexecutedbyuserfirstname    | int           | Evet         | Sözleşme eylemini yürüten kullanıcının adı |
| ContractActionExecutedByUserLastName     | nvarchar (50)  | Evet         | Sözleşme eylemini yürüten kullanıcının soyadı |
| Sözleşmeli Tactionexecutedbyuserexternalıd   | nvarchar (255) | Evet         | Sözleşme eylemini yürüten kullanıcının dış tanımlayıcısı. Varsayılan olarak, bu KIMLIK, Konsorsiyumu Azure Active Directory kimliğini temsil eden GUID 'dir. |
| Sözleşmeli Tactionexecutedbyuseremapostaadresi | nvarchar (255) | Evet         | Sözleşme eylemini yürüten kullanıcının e-posta adresi |
| Workflowfunctionparameterıd              | int           | Hayır          | İşlevin parametresi için benzersiz bir tanımlayıcı |
| WorkflowFunctionParameterName            | nvarchar (50)  | Hayır          | İşlevin parametresinin adı |
| WorkflowFunctionParameterDisplayName     | nvarchar (255) | Hayır          | Kullanıcı arabiriminde gösterilecek bir işlev parametresinin adı |
| Workflowfunctionparameterdatatypeıd      | int           | Hayır          | Bir iş akışı işlev parametresiyle ilişkili veri türü için benzersiz tanımlayıcı |
| WorkflowParameterDataTypeName            | nvarchar (50)  | Hayır          | Bir iş akışı işlev parametresiyle ilişkili bir veri türünün adı |
| Sözleşmeli Tactionparametervalue             | nvarchar (255) | Hayır          | Akıllı sözleşmede depolanan parametrenin değeri |
| Blok karması                                | nvarchar (255) | Evet         | Bloğun karması |
| Blok numarası                              | int           | Evet         | Genel muhasebedeki blok sayısı |
| blockTimestamp                           | Tarih/saat (2, 7) | Evet         | Bloğun zaman damgası |
| TransactionId                            | int           | Hayır          | İşlem için benzersiz bir tanımlayıcı |
| TransactionFrom                          | nvarchar (255) | Evet         | İşlemin kaynaklandığı taraf |
| TransactionTo                            | nvarchar (255) | Evet         | İle işlenen taraf |
| Işlem karması                          | nvarchar (255) | Evet         | İşlemin karması |
| Transactionısworkbenchtransaction        | bit           | Evet         | İşlemin bir Azure blok zinciri çalışma ekranı işlemi olup olmadığını tanımlayan bir bit |
| TransactionProvisioningStatus            | int           | Evet         | İşlem için sağlama işleminin geçerli durumunu tanımlar. Olası değerler şunlardır: <br />0 – işlem, veritabanında API tarafından oluşturulmuştur<br />1 – işlem, genel muhasebeye gönderildi<br />2 – işlem, genel muhasebeye başarıyla dağıtıldı                 |
| Işlem değeri                         | ondalık (32, 2) | Evet         | İşlemin değeri |

## <a name="vwcontractproperty"></a>Öcontractözelliği

Bu görünüm, bir sözleşmeyle ilişkili özelliklerle ilgili bilgilerin büyük bölümünü temsil eder ve ortak raporlama senaryolarını kolaylaştırmak için tasarlanmıştır. Alınan her bir özellik için, bu görünüm aşağıdaki verileri içerir:

-   İlişkili uygulama tanımı
-   İlişkili iş akışı tanımı
-   İş akışını dağıtan kullanıcının ayrıntıları
-   İlişkili akıllı anlaşma özelliği tanımı
-   Özellikler için belirli örnek değerleri
-   Sözleşmenin State özelliği için Ayrıntılar

| Name                               | Type          | Null olabilir | Açıklama                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Hayır          | Uygulama için benzersiz bir tanımlayıcı |
| ApplicationName                    | nvarchar (50)  | Hayır          | Uygulamanın adı |
| applicationDisplayName             | nvarchar (255) | Hayır          | Kullanıcı arabiriminde görüntülenecek ad |
| ApplicationEnabled                 | bit           | Hayır          | Uygulamanın Şu anda etkin olup olmadığını tanımlar.<br />**Not:** Bir uygulama veritabanında devre dışı olarak yansıtılmasına rağmen, ilişkili sözleşmeler blok zincirinde kalır ve bu anlaşmalar hakkındaki veriler veritabanında kalır.                      |
| Workflowıd                         | int           | Hayır          | İş akışı için benzersiz tanımlayıcı |
| workflowName                       | nvarchar (50)  | Hayır          | İş akışının adı |
| WorkflowDisplayName                | nvarchar (255) | Hayır          | Kullanıcı arabiriminde görünen iş akışının adı |
| WorkflowDescription                | nvarchar (255) | Evet         | İş akışının açıklaması |
| sözleşme kimliği                         | int           | Hayır          | Sözleşme için benzersiz tanımlayıcı |
| ContractProvisioningStatus         | int           | Hayır          | Sözleşme için sağlama işleminin geçerli durumunu tanımlar. Olası değerler şunlardır: <br />0 – sözleşme, veritabanında API tarafından oluşturulmuştur<br />1 – sözleşme, genel muhasebeye gönderildi<br />2 – sözleşme, genel muhasebeye başarıyla dağıtıldı<br />3 veya 4-Sözleşme, muhasebeye dağıtılamadı<br />5-sözleşme, genel muhasebeye başarıyla dağıtıldı <br /><br />Sürüm 1,5 ' den başlayarak, 0 ile 5 arasındaki değerler desteklenir. Geçerli yayında geriye dönük uyumluluk için, **vwContractPropertyV0** , yalnızca 0 ile 2 arasındaki değerleri destekleyen bir görünüm bulunur. |
| Contractcodeıd                     | int           | Hayır          | Sözleşmenin kod uygulamasına yönelik benzersiz bir tanımlayıcı |
| Contractledgerıdentifier           | nvarchar (255) | Evet         | Belirli bir dağıtılmış defter için, bir akıllı sözleşmenin dağıtılan sürümüyle ilişkili benzersiz bir tanımlayıcı. Örneğin, Ethereum. |
| Contractdeployedbyuserıd           | int           | Hayır          | Sözleşmeyi dağıtan kullanıcının benzersiz tanımlayıcısı |
| ContractDeployedByUserFirstName    | nvarchar (50)  | Evet         | Sözleşmeyi dağıtan kullanıcının adı |
| ContractDeployedByUserLastName     | nvarchar (255) | Evet         | Sözleşmeyi dağıtan kullanıcının soyadı |
| Contractdeployedbyuserexternalıd   | nvarchar (255) | Hayır          | Sözleşmeyi dağıtan kullanıcının dış tanımlayıcısı. Varsayılan olarak, bu KIMLIK, konsorsiyumde kimliklerini temsil eden GUID 'dir Azure Active Directory |
| Contractdeployedbyuseremaadresi | nvarchar (255) | Evet         | Sözleşmeyi dağıtan kullanıcının e-posta adresi |
| Workflowpropertyıd                 | int           |             | Bir iş akışının özelliği için benzersiz bir tanımlayıcı |
| Workflowpropertydatatypeıd         | int           | Hayır          | Özelliğin veri türünün KIMLIĞI |
| WorkflowPropertyDataTypeName       | nvarchar (50)  | Hayır          | Özelliğin veri türünün adı |
| WorkflowPropertyName               | nvarchar (50)  | Hayır          | Workflow özelliğinin adı |
| WorkflowPropertyDisplayName        | nvarchar (255) | Hayır          | Workflow özelliğinin görünen adı |
| WorkflowPropertyDescription        | nvarchar (255) | Evet         | Özelliğin açıklaması |
| ContractPropertyValue              | nvarchar (255) | Hayır          | Sözleşmede bir özelliğin değeri |
| StateName                          | nvarchar (50)  | Evet         | Bu özellik sözleşmenin durumunu içeriyorsa, bu durum için görünen addır. Durumla ilişkilendirilmediği takdirde değer null olur. |
| StateDisplayName                   | nvarchar (255) | Hayır          | Bu özellik durumu içeriyorsa, durum için görünen addır. Durumla ilişkilendirilmediği takdirde değer null olur. |
| StateValue                         | nvarchar (255) | Evet         | Bu özellik durumu içeriyorsa, durum değeridir. Durumla ilişkilendirilmediği takdirde değer null olur. |

## <a name="vwcontractstate"></a>Öcontractstate

Bu görünüm, belirli bir sözleşmenin durumuyla ilgili bilgilerin çoğunluğunu temsil eder ve ortak raporlama senaryolarını kolaylaştırmak için tasarlanmıştır. Bu görünümdeki her kayıt aşağıdaki verileri içerir:

-   İlişkili uygulama tanımı
-   İlişkili iş akışı tanımı
-   İş akışını dağıtan kullanıcının ayrıntıları
-   İlişkili akıllı anlaşma özelliği tanımı
-   Sözleşmenin State özelliği için Ayrıntılar

| Name                               | Type          | Null olabilir | Açıklama                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Hayır          | Uygulama için benzersiz bir tanımlayıcı |
| ApplicationName                    | nvarchar (50)  | Hayır          | Uygulamanın adı |
| applicationDisplayName             | nvarchar (255) | Hayır          | Kullanıcı arabiriminde görüntülenecek ad |
| ApplicationEnabled                 | bit           | Hayır          | Uygulamanın Şu anda etkin olup olmadığını tanımlar.<br />**Not:** Bir uygulama veritabanında devre dışı olarak yansıtılmasına rağmen, ilişkili sözleşmeler blok zincirinde kalır ve bu anlaşmalar hakkındaki veriler veritabanında kalır. |
| Workflowıd                         | int           | Hayır          | İş akışı için benzersiz bir tanımlayıcı |
| workflowName                       | nvarchar (50)  | Hayır          | İş akışının adı |
| WorkflowDisplayName                | nvarchar (255) | Hayır          | Kullanıcı arabiriminde görünen ad |
| WorkflowDescription                | nvarchar (255) | Evet         | İş akışının açıklaması |
| Contractledgerımplementationıd     | nvarchar (255) | Evet         | Belirli bir dağıtılmış defter için, bir akıllı sözleşmenin dağıtılan sürümüyle ilişkili benzersiz bir tanımlayıcı. Örneğin, Ethereum. |
| sözleşme kimliği                         | int           | Hayır          | Sözleşme için benzersiz bir tanımlayıcı |
| ContractProvisioningStatus         | int           | Hayır          |Sözleşme için sağlama işleminin geçerli durumunu tanımlar. Olası değerler şunlardır: <br />0 – sözleşme, veritabanında API tarafından oluşturulmuştur<br />1 – sözleşme, genel muhasebeye gönderildi<br />2 – sözleşme, genel muhasebeye başarıyla dağıtıldı<br />3 veya 4-Sözleşme, muhasebeye dağıtılamadı<br />5-sözleşme, genel muhasebeye başarıyla dağıtıldı <br /><br />Sürüm 1,5 ' den başlayarak, 0 ile 5 arasındaki değerler desteklenir. Geçerli yayında geriye dönük uyumluluk için, **vwContractStateV0** , yalnızca 0 ile 2 arasındaki değerleri destekleyen bir görünüm bulunur. |
| ConnectionID                       | int           | Hayır          | İş akışının dağıtıldığı blok zinciri örneği için benzersiz bir tanımlayıcı |
| Contractcodeıd                     | int           | Hayır          | Sözleşmenin kod uygulamasına yönelik benzersiz bir tanımlayıcı |
| Contractdeployedbyuserıd           | int           | Hayır          | Sözleşmeyi dağıtan kullanıcının benzersiz tanımlayıcısı |
| Contractdeployedbyuserexternalıd   | nvarchar (255) | Hayır          | Sözleşmeyi dağıtan kullanıcının dış tanımlayıcısı. Varsayılan olarak, bu KIMLIK, Konsorsiyumu Azure Active Directory kimliğini temsil eden GUID 'dir. |
| ContractDeployedByUserFirstName    | nvarchar (50)  | Evet         | Sözleşmeyi dağıtan kullanıcının adı |
| ContractDeployedByUserLastName     | nvarchar (255) | Evet         | Sözleşmeyi dağıtan kullanıcının soyadı |
| Contractdeployedbyuseremaadresi | nvarchar (255) | Evet         | Sözleşmeyi dağıtan kullanıcının e-posta adresi |
| Workflowpropertyıd                 | int           | Hayır          | Bir iş akışı özelliği için benzersiz bir tanımlayıcı |
| Workflowpropertydatatypeıd         | int           | Hayır          | Workflow özelliğinin veri türünün KIMLIĞI |
| WorkflowPropertyDataTypeName       | nvarchar (50)  | Hayır          | Workflow özelliğinin veri türünün adı |
| WorkflowPropertyName               | nvarchar (50)  | Hayır          | Workflow özelliğinin adı |
| WorkflowPropertyDisplayName        | nvarchar (255) | Hayır          | Bir kullanıcı arabiriminde gösterilecek özelliğin görünen adı |
| WorkflowPropertyDescription        | nvarchar (255) | Evet         | Özelliğin açıklaması |
| ContractPropertyValue              | nvarchar (255) | Hayır          | Sözleşmede depolanan bir özelliğin değeri |
| StateName                          | nvarchar (50)  | Evet         | Bu özellik durumu içeriyorsa, durumun görünen adı. Durumla ilişkilendirilmediği takdirde değer null olur. |
| StateDisplayName                   | nvarchar (255) | Hayır          | Bu özellik durumu içeriyorsa, durum için görünen addır. Durumla ilişkilendirilmediği takdirde değer null olur. |
| StateValue                         | nvarchar (255) | Evet         | Bu özellik durumu içeriyorsa, durum değeridir. Durumla ilişkilendirilmediği takdirde değer null olur. |

## <a name="vwuser"></a>Ökullanıcı

Bu görünüm, Azure blok zinciri çalışma ekranı kullanımı için sağlanan konsorsiyum üyeleri hakkında ayrıntılar sağlar. Varsayılan olarak, veriler kullanıcının ilk sağlanması yoluyla doldurulur.

| Name               | Type          | Null olabilir | Açıklama                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID                 | int           | Hayır          | Bir kullanıcı için benzersiz bir tanımlayıcı |
| externalID         | nvarchar (255) | Hayır          | Bir kullanıcı için dış tanımlayıcı. Varsayılan olarak, bu KIMLIK kullanıcının Azure Active Directory KIMLIĞINI temsil eden GUID 'dir. |
| provisioningStatus | int           | Hayır          |Kullanıcı için sağlama işleminin geçerli durumunu tanımlar. Olası değerler şunlardır: <br />0 – Kullanıcı API tarafından oluşturuldu<br />1 – veritabanındaki kullanıcıyla ilişkili bir anahtar<br />2 – Kullanıcı tam olarak sağlanıyor |
| FirstName          | nvarchar (50)  | Evet         | Kullanıcının ilk adı |
| LastName           | nvarchar (50)  | Evet         | Kullanıcının soyadı |
| EmailAddress       | nvarchar (255) | Evet         | Kullanıcının e-posta adresi |

## <a name="vwworkflow"></a>diğer Iş akışı

Bu görünüm, temel iş akışı meta verilerinin yanı sıra iş akışının işlevlerini ve parametrelerini temsil eder. Raporlama için tasarlanan, iş akışıyla ilişkili uygulamayla ilgili meta verileri de içerir. Bu görünüm, iş akışlarında raporlamayı kolaylaştırmak için birden çok temel tablodaki verileri içerir. Her iş akışı için, bu görünüm aşağıdaki verileri içerir:

-   İlişkili uygulama tanımı
-   İlişkili iş akışı tanımı
-   İlişkili iş akışı başlatma durumu bilgileri

| Name                              | Type          | Null olabilir | Açıklama                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                     | int           | Hayır          | Uygulama için benzersiz bir tanımlayıcı |
| ApplicationName                   | nvarchar (50)  | Hayır          | Uygulamanın adı |
| applicationDisplayName            | nvarchar (255) | Hayır          | Kullanıcı arabiriminde görüntülenecek ad |
| ApplicationEnabled                | bit           | Hayır          | Uygulamanın etkinleştirilip etkinleştirilmediğini tanımlar |
| Workflowıd                        | int           | Evet         | Bir iş akışı için benzersiz bir tanımlayıcı |
| workflowName                      | nvarchar (50)  | Hayır          | İş akışının adı |
| WorkflowDisplayName               | nvarchar (255) | Hayır          | Kullanıcı arabiriminde görünen ad |
| WorkflowDescription               | nvarchar (255) | Evet         | İş akışının açıklaması. |
| Workflowconstructorfunctionıd     | int           | Hayır          | İş akışı için Oluşturucu olarak hizmet veren iş akışı işlevinin tanımlayıcısı |
| Workflowstartstateıd              | int           | Hayır          | Durum için benzersiz bir tanımlayıcı |
| WorkflowStartStateName            | nvarchar (50)  | Hayır          | Durum adı |
| WorkflowStartStateDisplayName     | nvarchar (255) | Hayır          | Durum için Kullanıcı arabiriminde görüntülenecek ad |
| WorkflowStartStateDescription     | nvarchar (255) | Evet         | İş akışı durumunun açıklaması |
| WorkflowStartStateStyle           | nvarchar (50)  | Evet         | Bu değer, bu durumda iş akışının tamamlanma yüzdesini tanımlar |
| WorkflowStartStateValue           | int           | Hayır          | Durumun değeri |
| Workflowstartstatepercenttamamlanmıştır | int           | Hayır          | Kullanıcı arabiriminde bu durumun nasıl işleneceğini gösteren istemcilere bir ipucu sağlayan metin açıklaması. Desteklenen durumlar *başarı* ve *başarısızlık* içerir |

## <a name="vwworkflowfunction"></a>bir Öworkflowfunction

Bu görünüm, temel iş akışı meta verilerinin yanı sıra iş akışının işlevlerini ve parametrelerini temsil eder. Raporlama için tasarlanan, iş akışıyla ilişkili uygulamayla ilgili meta verileri de içerir. Bu görünüm, iş akışlarında raporlamayı kolaylaştırmak için birden çok temel tablodaki verileri içerir. Her bir iş akışı işlevi için, bu görünüm aşağıdaki verileri içerir:

-   İlişkili uygulama tanımı
-   İlişkili iş akışı tanımı
-   İş akışı işlev ayrıntıları

| Name                                 | Type          | Null olabilir | Açıklama                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationId                        | int           | Hayır          | Uygulama için benzersiz bir tanımlayıcı |
| ApplicationName                      | nvarchar (50)  | Hayır          | Uygulamanın adı |
| applicationDisplayName               | nvarchar (255) | Hayır          | Kullanıcı arabiriminde görüntülenecek ad |
| ApplicationEnabled                   | bit           | Hayır          | Uygulamanın etkinleştirilip etkinleştirilmediğini tanımlar |
| Workflowıd                           | int           | Hayır          | Bir iş akışı için benzersiz bir tanımlayıcı |
| workflowName                         | nvarchar (50)  | Hayır          | İş akışının adı |
| WorkflowDisplayName                  | nvarchar (255) | Hayır          | Kullanıcı arabiriminde görünen iş akışının adı |
| WorkflowDescription                  | nvarchar (255) | Evet         | İş akışının açıklaması |
| Workflowfunctionıd                   | int           | Hayır          | Bir işlev için benzersiz tanımlayıcı |
| Workflowfonksiyonadı                 | nvarchar (50)  | Evet         | İşlevin adı |
| WorkflowFunctionDisplayName          | nvarchar (255) | Hayır          | Kullanıcı arabiriminde gösterilecek işlevin adı |
| WorkflowFunctionDescription          | nvarchar (255) | Evet         | İş akışı işlevinin açıklaması |
| WorkflowFunctionIsConstructor        | bit           | Hayır          | İş akışı işlevinin iş akışı için Oluşturucu olup olmadığını belirler |
| Workflowfunctionparameterıd          | int           | Hayır          | Bir işlevin parametresi için benzersiz bir tanımlayıcı |
| WorkflowFunctionParameterName        | nvarchar (50)  | Hayır          | İşlevin parametresinin adı |
| WorkflowFunctionParameterDisplayName | nvarchar (255) | Hayır          | Kullanıcı arabiriminde gösterilecek bir işlev parametresinin adı |
| Workflowfunctionparameterdatatypeıd  | int           | Hayır          | Bir iş akışı işlev parametresiyle ilişkili veri türü için benzersiz bir tanımlayıcı |
| WorkflowParameterDataTypeName        | nvarchar (50)  | Hayır          | Bir iş akışı işlev parametresiyle ilişkili bir veri türünün adı |

## <a name="vwworkflowproperty"></a>Öworkflowproperty

Bu görünüm, bir iş akışı için tanımlanan özellikleri temsil eder. Her özellik için, bu görünüm aşağıdaki verileri içerir:

-   İlişkili uygulama tanımı
-   İlişkili iş akışı tanımı
-   İş akışı özellik ayrıntıları

| Name                         | Type          | Null olabilir | Açıklama                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Hayır          | Uygulama için benzersiz bir tanımlayıcı |
| ApplicationName              | nvarchar (50)  | Hayır          | Uygulamanın adı |
| applicationDisplayName       | nvarchar (255) | Hayır          | Kullanıcı arabiriminde görüntülenecek ad |
| ApplicationEnabled           | bit           | Hayır          | Uygulamanın Şu anda etkin olup olmadığını tanımlar.<br />**Not:** Bir uygulama veritabanında devre dışı olarak yansıtılmasına rağmen, ilişkili sözleşmeler blok zincirinde kalır ve bu anlaşmalar hakkındaki veriler veritabanında kalır. |
| Workflowıd                   | int           | Hayır          | İş akışı için benzersiz bir tanımlayıcı |
| workflowName                 | nvarchar (50)  | Hayır          | İş akışının adı |
| WorkflowDisplayName          | nvarchar (255) | Hayır          | Kullanıcı arabirimindeki iş akışı için görüntülenecek ad |
| WorkflowDescription          | nvarchar (255) | Evet         | İş akışının açıklaması |
| Workflowpropertyıd           | int           | Hayır          | Bir iş akışının özelliği için benzersiz bir tanımlayıcı |
| WorkflowPropertyName         | nvarchar (50)  | Hayır          | Özelliğin adı |
| WorkflowPropertyDescription  | nvarchar (255) | Evet         | Özelliğin açıklaması |
| WorkflowPropertyDisplayName  | nvarchar (255) | Hayır          | Kullanıcı arabiriminde görüntülenecek ad |
| Workflowpropertyworkflowwıd   | int           | Hayır          | Bu özelliğin ilişkilendirildiği iş akışının KIMLIĞI |
| Workflowpropertydatatypeıd   | int           | Hayır          | Özellik için tanımlanan veri türünün KIMLIĞI |
| WorkflowPropertyDataTypeName | nvarchar (50)  | Hayır          | Özellik için tanımlanan veri türünün adı |
| Workflowpropertyısstate      | bit           | Hayır          | Bu alan, bu iş akışı özelliğinin iş akışının durumunu içerip içerne olduğunu tanımlar |

## <a name="vwworkflowstate"></a>daha fazla Workflowstate

Bu görünüm bir iş akışıyla ilişkili özellikleri temsil eder. Her sözleşme için, bu görünüm aşağıdaki verileri içerir:

-   İlişkili uygulama tanımı
-   İlişkili iş akışı tanımı
-   İş akışı durum bilgileri

| Name                         | Type          | Null olabilir | Açıklama                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Hayır          | Uygulama için benzersiz bir tanımlayıcı |
| ApplicationName              | nvarchar (50)  | Hayır          | Uygulamanın adı |
| applicationDisplayName       | nvarchar (255) | Hayır          | Uygulamanın açıklaması |
| ApplicationEnabled           | bit           | Hayır          | Uygulamanın Şu anda etkin olup olmadığını tanımlar.<br />**Not:** Bir uygulama veritabanında devre dışı olarak yansıtılmasına rağmen, ilişkili sözleşmeler blok zincirinde kalır ve bu anlaşmalar hakkındaki veriler veritabanında kalır. |
| Workflowıd                   | int           | Hayır          | İş akışı için benzersiz tanımlayıcı |
| workflowName                 | nvarchar (50)  | Hayır          | İş akışının adı |
| WorkflowDisplayName          | nvarchar (255) | Hayır          | İş akışı için Kullanıcı arabiriminde görünen ad |
| WorkflowDescription          | nvarchar (255) | Evet         | İş akışının açıklaması |
| Workflowstateıd              | int           | Hayır          | Durum için benzersiz tanımlayıcı |
| WorkflowStateName            | nvarchar (50)  | Hayır          | Durum adı |
| WorkflowStateDisplayName     | nvarchar (255) | Hayır          | Durum için Kullanıcı arabiriminde görüntülenecek ad |
| WorkflowStateDescription     | nvarchar (255) | Evet         | İş akışı durumunun açıklaması |
| Workflowstatepercenttamamlanmıştır | int           | Hayır          | Bu değer, bu durumda iş akışının tamamlanma yüzdesini tanımlar |
| WorkflowStateValue           | nvarchar (50)  | Hayır          | Durumun değeri |
| WorkflowStateStyle           | nvarchar (50)  | Hayır          | Kullanıcı arabiriminde bu durumun nasıl işleneceğini gösteren istemcilere bir ipucu sağlayan metin açıklaması. Desteklenen durumlar *başarı* ve *başarısızlık* içerir |

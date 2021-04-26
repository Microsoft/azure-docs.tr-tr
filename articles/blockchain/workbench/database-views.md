---
title: Azure blok zinciri çalışma ekranı veritabanı görünümleri
description: Kullanılabilir Azure blok zinciri 'nin önizlemesine genel bakış SQL DB veritabanı görünümleri.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 6c7fba590c9c8951e1862c28cb24dbde52c8da8d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625543"
---
# <a name="azure-blockchain-workbench-database-views"></a>Azure blok zinciri çalışma ekranı veritabanı görünümleri

Azure blok zinciri çalışma ekranı önizlemesi, dağıtılmış bağış *zincirlerden zincir dışı* BIR SQL DB veritabanına veri sağlar. Zincir dışı veritabanı, blok zinciri verileriyle etkileşim kurmak için SQL ve [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)gibi mevcut araçları kullanmayı mümkün kılar.

Azure blok zinciri çalışma ekranı, sorgularınızı gerçekleştirirken yararlı olacak verilere erişim sağlayan bir veritabanı görünümleri kümesi sağlar. Bu görünümler, kolayca rapor, analiz oluşturmaya ve mevcut araçlarla blok zinciri verilerini kullanmaya ve veritabanı personelini yeniden eğmenize gerek kalmadan hızlı bir şekilde daha kolay hale getirilir.

Bu bölüm, veritabanı görünümlerine ve içerdikleri verilere ilişkin bir genel bakış içerir.

> [!NOTE]
> Veritabanında bu görünümlerin dışında bulunan veritabanı tablolarının doğrudan kullanımı, mümkün olduğunca desteklenmez.
>

## <a name="vwapplication"></a>diğer uygulama

Bu görünüm, Azure blok zinciri çalışma ekranına yüklenmiş **uygulamalarla** ilgili ayrıntıları sağlar.

| Ad                             | Tür          | Null olabilir | Description                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                    | int           | No          | Uygulama için benzersiz bir tanımlayıcı |
| ApplicationName                  | nvarchar (50)  | No          | Uygulamanın adı |
| ApplicationDescription           | nvarchar (255) | Yes         | Uygulamanın açıklaması |
| ApplicationDisplayName           | nvarchar (255) | No          | Kullanıcı arabiriminde görüntülenecek ad |
| ApplicationEnabled               | bit           | No          | Uygulamanın Şu anda etkin olup olmadığını belirtir<br /> **Note:** Bir uygulama veritabanında devre dışı olarak yansıtılmasına rağmen, ilişkili sözleşmeler blok zincirinde kalır ve bu anlaşmalar hakkındaki veriler veritabanında kalır. |
| UploadedDtTm                     | datetime2 (7)  | No          | Bir sözleşmenin karşıya yüklendiği tarih ve saat |
| Uploadedbyuserıd                 | int           | No          | Uygulamayı karşıya yükleyen kullanıcının KIMLIĞI |
| Uploadedbyuserexternalıd         | nvarchar (255) | No          | Uygulamayı karşıya yükleyen kullanıcının dış tanımlayıcısı. Varsayılan olarak, bu KIMLIK, konsorsiyumun Azure Active Directory Kullanıcı olur.                                                                                                |
| UploadedByUserProvisioningStatus | int           | No          | Kullanıcı için sağlama işleminin geçerli durumunu tanımlar. Olası değerler şunlardır: <br />0 – Kullanıcı API tarafından oluşturuldu<br />1 – veritabanındaki kullanıcıyla ilişkili bir anahtar<br />2 – Kullanıcı tam olarak sağlanıyor                         |
| UploadedByUserFirstName          | nvarchar (50)  | Yes         | Sözleşmeyi karşıya yükleyen kullanıcının adı |
| UploadedByUserLastName           | nvarchar (50)  | Yes         | Sözleşmeyi karşıya yükleyen kullanıcının soyadı |
| Uploadedbyuseremapostaadresi       | nvarchar (255) | Yes         | Sözleşmeyi karşıya yükleyen kullanıcının e-posta adresi |

## <a name="vwapplicationrole"></a>Öapplicationrole

Bu görünüm, Azure blok zinciri çalışma ekranı uygulamalarında tanımlanmış rollerle ilgili ayrıntıları sağlar.

Bir *varlık aktarım* uygulamasında, örneğin, *Satınalmacı* ve *satıcı* rolleri gibi roller tanımlanabilir.

| Ad                   | Tür             | Null olabilir | Description                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationID          | int              | No          | Uygulama için benzersiz bir tanımlayıcı           |
| ApplicationName        | nvarchar (50)     | No          | Uygulamanın adı                       |
| ApplicationDescription | nvarchar (255)    | Yes         | Uygulamanın açıklaması                  |
| ApplicationDisplayName | nvarchar (255)    | No          | Kullanıcı arabiriminde görüntülenecek ad      |
| RoleID                 | int              | No          | Uygulamadaki bir rol için benzersiz bir tanımlayıcı |
| RoleName               | nvarchar50)      | No          | Rolün adı                              |
| RoleDescription        | Açıklama (255) | Yes         | Rolün açıklaması                         |

## <a name="vwapplicationroleuser"></a>Öapplicationroleuser

Bu görünüm, Azure blok zinciri çalışma ekranı uygulamalarında tanımlanmış roller ve bunlarla ilişkili kullanıcılar hakkında ayrıntılar sağlar.

Bir *varlık aktarım* uygulamasında, örneğin *John Smith* , *alıcı* rolüyle ilişkilendirilebilir.

| Ad                       | Tür          | Null olabilir | Description                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID              | int           | No          | Uygulama için benzersiz bir tanımlayıcı                                                                                                                                                                                               |
| ApplicationName            | nvarchar (50)  | No          | Uygulamanın adı                                                                                                                                                                                                           |
| ApplicationDescription     | nvarchar (255) | Yes         | Uygulamanın açıklaması                                                                                                                                                                                                      |
| ApplicationDisplayName     | nvarchar (255) | No          | Kullanıcı arabiriminde görüntülenecek ad                                                                                                                                                                                          |
| Applicationroleıd          | int           | No          | Uygulamadaki bir rol için benzersiz bir tanımlayıcı                                                                                                                                                                                     |
| ApplicationRoleName        | nvarchar50)   | No          | Rolün adı                                                                                                                                                                                                                  |
| ApplicationRoleDescription | nvarchar (255) | Yes         | Rolün açıklaması                                                                                                                                                                                                             |
| UserId                     | int           | No          | Rolle ilişkili kullanıcının KIMLIĞI |
| Userexternalıd             | nvarchar (255) | No          | Rolüyle ilişkilendirilen kullanıcı için dış tanımlayıcı. Varsayılan olarak, bu KIMLIK, konsorsiyumun Azure Active Directory Kullanıcı olur.                                                                     |
| UserProvisioningStatus     | int           | No          | Kullanıcı için sağlama işleminin geçerli durumunu tanımlar. Olası değerler şunlardır: <br />0 – Kullanıcı API tarafından oluşturuldu<br />1 – veritabanındaki kullanıcıyla ilişkili bir anahtar<br />2 – Kullanıcı tam olarak sağlanıyor |
| Kullanıcı adı              | nvarchar (50)  | Yes         | Rolle ilişkilendirilen kullanıcının adı |
| UserLastName               | nvarchar (255) | Yes         | Rolle ilişkili kullanıcının soyadı |
| Kullanıcıeadresi           | nvarchar (255) | Yes         | Rolle ilişkili kullanıcının e-posta adresi |

## <a name="vwconnectionuser"></a>Diğer Kullanıcı

Bu görünüm, Azure blok zinciri çalışma ekranı ve bunlarla ilişkili kullanıcılarla ilgili ayrıntıları sağlar. Her bağlantı için, bu görünüm aşağıdaki verileri içerir:

-   İlişkili muhasebe ayrıntıları
-   İlişkili kullanıcı bilgileri

| Ad                     | Tür          | Null olabilir | Description                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionID             | int           | No          | Azure blok zinciri çalışma ekranı 'ndaki bir bağlantının benzersiz tanımlayıcısı |
| ConnectionEndpointUrl    | nvarchar (50)  | No          | Bir bağlantı için uç nokta URL 'si |
| ConnectionFundingAccount | nvarchar (255) | Yes         | Varsa, bir bağlantıyla ilişkili komik hesap |
| LedgerID                 | int           | No          | Bir muhasebe için benzersiz tanımlayıcı |
| LedgerName               | nvarchar (50)  | No          | Genel muhasebe adı |
| LedgerDisplayName        | nvarchar (255) | No          | Kullanıcı arabiriminde görüntülenecek olan genel muhasebe adı |
| UserId                   | int           | No          | Bağlantıyla ilişkili kullanıcının KIMLIĞI |
| Userexternalıd           | nvarchar (255) | No          | Bağlantıyla ilişkili kullanıcı için dış tanımlayıcı. Varsayılan olarak, bu KIMLIK, konsorsiyumun Azure Active Directory Kullanıcı olur. |
| UserProvisioningStatus   | int           | No          |Kullanıcı için sağlama işleminin geçerli durumunu tanımlar. Olası değerler şunlardır: <br />0 – Kullanıcı API tarafından oluşturuldu<br />1 – veritabanındaki kullanıcıyla ilişkili bir anahtar<br />2 – Kullanıcı tam olarak sağlanıyor |
| Kullanıcı adı            | nvarchar (50)  | Yes         | Bağlantıyla ilişkili kullanıcının adı |
| UserLastName             | nvarchar (255) | Yes         | Bağlantıyla ilişkili kullanıcının soyadı |
| Kullanıcıeadresi         | nvarchar (255) | Yes         | Bağlantıyla ilişkili kullanıcının e-posta adresi |

## <a name="vwcontract"></a>diğer sözleşme

Bu görünüm, dağıtılan sözleşmeler hakkında ayrıntılı bilgi sağlar. Her sözleşme için, bu görünüm aşağıdaki verileri içerir:

-   İlişkili uygulama tanımı
-   İlişkili iş akışı tanımı
-   İşlev için ilişkili defter uygulama
-   Eylemi başlatan kullanıcının ayrıntıları
-   Blok zinciri bloğu ve işlemle ilgili ayrıntılar

| Ad                                     | Tür           | Null olabilir | Description                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionID                             | int            | No          | Azure blok zinciri çalışma ekranı 'ndaki bir bağlantı için benzersiz tanımlayıcı.                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar (50)   | No          | Bir bağlantı için uç nokta URL 'si |
| ConnectionFundingAccount                 | nvarchar (255)  | Yes         | Varsa, bir bağlantıyla ilişkili komik hesap |
| LedgerID                                 | int            | No          | Bir muhasebe için benzersiz tanımlayıcı |
| LedgerName                               | nvarchar (50)   | No          | Genel muhasebe adı |
| LedgerDisplayName                        | nvarchar (255)  | No          | Kullanıcı arabiriminde görüntülenecek olan genel muhasebe adı |
| ApplicationID                            | int            | No          | Uygulama için benzersiz bir tanımlayıcı |
| ApplicationName                          | nvarchar (50)  | No          | Uygulamanın adı |
| ApplicationDisplayName                   | nvarchar (255) | No          | Kullanıcı arabiriminde görüntülenecek ad |
| ApplicationEnabled                       | bit            | No          | Uygulamanın Şu anda etkin olup olmadığını tanımlar.<br /> **Note:** Bir uygulama veritabanında devre dışı olarak yansıtılmasına rağmen, ilişkili sözleşmeler blok zincirinde kalır ve bu anlaşmalar hakkındaki veriler veritabanında kalır.  |
| Workflowıd                               | int            | No          | Bir sözleşmeyle ilişkili iş akışı için benzersiz bir tanımlayıcı |
| WorkflowName                             | nvarchar (50)   | No          | Bir sözleşmeyle ilişkili iş akışının adı |
| WorkflowDisplayName                      | nvarchar (255)  | No          | Kullanıcı arabiriminde görünen sözleşmeyle ilişkili iş akışının adı |
| WorkflowDescription                      | nvarchar (255)  | Yes         | Bir sözleşmeyle ilişkili iş akışının açıklaması |
| Contractcodeıd                           | int            | No          | Sözleşmeyle ilişkili sözleşme kodu için benzersiz bir tanımlayıcı |
| ContractFileName                         | int            | No          | Bu iş akışının akıllı sözleşme kodunu içeren dosyanın adı. |
| ContractUploadedDtTm                     | int            | No          | Sözleşme kodunun karşıya yüklendiği tarih ve saat |
| Sözleşme kimliği                               | int            | No          | Sözleşme için benzersiz tanımlayıcı |
| ContractProvisioningStatus               | int            | No          | Sözleşme için sağlama işleminin geçerli durumunu tanımlar. Olası değerler şunlardır: <br />0 – sözleşme, veritabanında API tarafından oluşturulmuştur<br />1 – sözleşme, genel muhasebeye gönderildi<br />2 – sözleşme, genel muhasebeye başarıyla dağıtıldı<br />3 veya 4-Sözleşme, muhasebeye dağıtılamadı<br />5-sözleşme, genel muhasebeye başarıyla dağıtıldı <br /><br />Sürüm 1,5 ' den başlayarak, 0 ile 5 arasındaki değerler desteklenir. Geçerli yayında geriye dönük uyumluluk için, **vwContractV0** , yalnızca 0 ile 2 arasındaki değerleri destekleyen bir görünüm bulunur. |
| Contractledgerıdentifier                 | nvarchar (255) |             | Sözleşmeyi dağıtan kullanıcının e-posta adresi |
| Contractdeployedbyuserıd                 | int            | No          | Sözleşmeyi dağıtan Kullanıcı için bir dış tanımlayıcı. Varsayılan olarak, bu KIMLIK kullanıcının Azure Active Directory KIMLIĞINI temsil eden GUID 'dir.                                                                                                          |
| Contractdeployedbyuserexternalıd         | nvarchar (255)  | No          | Sözleşmeyi dağıtan Kullanıcı için bir dış tanımlayıcı. Varsayılan olarak, bu KIMLIK kullanıcının Azure Active Directory KIMLIĞINI temsil eden GUID 'dir.                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | No          | Kullanıcı için sağlama işleminin geçerli durumunu tanımlar. Olası değerler şunlardır: <br />0 – Kullanıcı API tarafından oluşturuldu<br />1 – veritabanındaki kullanıcıyla ilişkili bir anahtar <br />2 – Kullanıcı tam olarak sağlanıyor                     |
| ContractDeployedByUserFirstName          | nvarchar (50)   | Yes         | Sözleşmeyi dağıtan kullanıcının adı |
| ContractDeployedByUserLastName           | nvarchar (255)  | Yes         | Sözleşmeyi dağıtan kullanıcının soyadı |
| Contractdeployedbyuseremaadresi       | nvarchar (255)  | Yes         | Sözleşmeyi dağıtan kullanıcının e-posta adresi |

## <a name="vwcontractaction"></a>Öcontractaction

Bu görünüm, sözleşmelerde gerçekleştirilen eylemlerle ilgili bilgilerin büyük bölümünü temsil eder ve ortak raporlama senaryolarını kolaylaştırmak için tasarlanmıştır. Gerçekleştirilen her bir eylem için, bu görünüm aşağıdaki verileri içerir:

-   İlişkili uygulama tanımı
-   İlişkili iş akışı tanımı
-   İlişkili akıllı sözleşme işlevi ve parametre tanımı
-   İşlev için ilişkili defter uygulama
-   Parametreler için belirtilen belirli örnek değerleri
-   Eylemi başlatan kullanıcının ayrıntıları
-   Blok zinciri bloğu ve işlemle ilgili ayrıntılar

| Ad                                     | Tür          | Null olabilir | Description                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                            | int           | No          | Uygulama için benzersiz bir tanımlayıcı |
| ApplicationName                          | nvarchar (50)  | No          | Uygulamanın adı |
| ApplicationDisplayName                   | nvarchar (255) | No          | Kullanıcı arabiriminde görüntülenecek ad |
| ApplicationEnabled                       | bit           | No          | Bu alan, uygulamanın Şu anda etkin olup olmadığını belirtir. Note: bir uygulamanın veritabanında devre dışı olarak yansıtılmasına karşın, ilişkili sözleşmeler blok zincirinde kalır ve bu anlaşmalar hakkındaki veriler veritabanında kalır.                                                  |
| Workflowıd                               | int           | No          | Bir iş akışı için benzersiz bir tanımlayıcı |
| WorkflowName                             | nvarchar (50)  | No          | İş akışının adı |
| WorkflowDisplayName                      | nvarchar (255) | No          | Kullanıcı arabiriminde görüntülenecek iş akışı adı |
| WorkflowDescription                      | nvarchar (255) | Yes         | İş akışının açıklaması |
| Sözleşme kimliği                               | int           | No          | Sözleşme için benzersiz bir tanımlayıcı |
| ContractProvisioningStatus               | int           | No          | Sözleşme için sağlama işleminin geçerli durumunu tanımlar. Olası değerler şunlardır: <br />0 – sözleşme, veritabanında API tarafından oluşturulmuştur<br />1 – sözleşme, genel muhasebeye gönderildi<br />2 – sözleşme, genel muhasebeye başarıyla dağıtıldı<br />3 veya 4-Sözleşme, muhasebeye dağıtılamadı<br />5-sözleşme, genel muhasebeye başarıyla dağıtıldı <br /><br />Sürüm 1,5 ' den başlayarak, 0 ile 5 arasındaki değerler desteklenir. Geçerli yayında geriye dönük uyumluluk için, **vwContractActionV0** , yalnızca 0 ile 2 arasındaki değerleri destekleyen bir görünüm bulunur. |
| Contractcodeıd                           | int           | No          | Sözleşmenin kod uygulamasına yönelik benzersiz bir tanımlayıcı |
| Contractledgerıdentifier                 | nvarchar (255) | Yes         | Belirli bir dağıtılmış defter için, bir akıllı sözleşmenin dağıtılan sürümüyle ilişkili benzersiz bir tanımlayıcı. Örneğin, Ethereum. |
| Contractdeployedbyuserıd                 | int           | No          | Sözleşmeyi dağıtan kullanıcının benzersiz tanımlayıcısı |
| ContractDeployedByUserFirstName          | nvarchar (50)  | Yes         | Sözleşmeyi dağıtan kullanıcının adı |
| ContractDeployedByUserLastName           | nvarchar (255) | Yes         | Sözleşmeyi dağıtan kullanıcının soyadı |
| Contractdeployedbyuserexternalıd         | nvarchar (255) | No          | Sözleşmeyi dağıtan kullanıcının dış tanımlayıcısı. Varsayılan olarak, bu KIMLIK, Konsorsiyumu Azure Active Directory kimliğini temsil eden GUID 'dir.                                                                                                                                                |
| Contractdeployedbyuseremaadresi       | nvarchar (255) | Yes         | Sözleşmeyi dağıtan kullanıcının e-posta adresi |
| Workflowfunctionıd                       | int           | No          | Bir iş akışı işlevi için benzersiz bir tanımlayıcı |
| Workflowfonksiyonadı                     | nvarchar (50)  | No          | İşlevin adı |
| WorkflowFunctionDisplayName              | nvarchar (255) | No          | Kullanıcı arabiriminde gösterilecek işlevin adı |
| WorkflowFunctionDescription              | nvarchar (255) | No          | İşlevin açıklaması |
| Sözleşmeli Tactionıd                         | int           | No          | Bir sözleşme eyleminin benzersiz tanımlayıcısı |
| Sözleşmeli Tactionprovisioningstatus         | int           | No          | Sözleşme eylemi için sağlama işleminin geçerli durumunu tanımlar. Olası değerler şunlardır: <br />0 – sözleşme eylemi, veritabanında API tarafından oluşturuldu<br />1 – sözleşme eylemi, genel muhasebeye gönderildi<br />2 – sözleşme eylemi, genel muhasebeye başarıyla dağıtıldı<br />3 veya 4-Sözleşme, muhasebeye dağıtılamadı<br />5-sözleşme, genel muhasebeye başarıyla dağıtıldı <br /><br />Sürüm 1,5 ' den başlayarak, 0 ile 5 arasındaki değerler desteklenir. Geçerli yayında geriye dönük uyumluluk için, **vwContractActionV0** , yalnızca 0 ile 2 arasındaki değerleri destekleyen bir görünüm bulunur. |
| Sözleşmeli Tactiontimestamp                  | Tarih/saat (2, 7) | No          | Sözleşme eyleminin zaman damgası |
| Sözleşmeli Tactionexecutedbyuserıd           | int           | No          | Sözleşme eylemini yürüten kullanıcının benzersiz tanımlayıcısı |
| Sözleşmeli Tactionexecutedbyuserfirstname    | int           | Yes         | Sözleşme eylemini yürüten kullanıcının adı |
| ContractActionExecutedByUserLastName     | nvarchar (50)  | Yes         | Sözleşme eylemini yürüten kullanıcının soyadı |
| Sözleşmeli Tactionexecutedbyuserexternalıd   | nvarchar (255) | Yes         | Sözleşme eylemini yürüten kullanıcının dış tanımlayıcısı. Varsayılan olarak, bu KIMLIK, Konsorsiyumu Azure Active Directory kimliğini temsil eden GUID 'dir. |
| Sözleşmeli Tactionexecutedbyuseremapostaadresi | nvarchar (255) | Yes         | Sözleşme eylemini yürüten kullanıcının e-posta adresi |
| Workflowfunctionparameterıd              | int           | No          | İşlevin parametresi için benzersiz bir tanımlayıcı |
| WorkflowFunctionParameterName            | nvarchar (50)  | No          | İşlevin parametresinin adı |
| WorkflowFunctionParameterDisplayName     | nvarchar (255) | No          | Kullanıcı arabiriminde gösterilecek bir işlev parametresinin adı |
| Workflowfunctionparameterdatatypeıd      | int           | No          | Bir iş akışı işlev parametresiyle ilişkili veri türü için benzersiz tanımlayıcı |
| WorkflowParameterDataTypeName            | nvarchar (50)  | No          | Bir iş akışı işlev parametresiyle ilişkili bir veri türünün adı |
| Sözleşmeli Tactionparametervalue             | nvarchar (255) | No          | Akıllı sözleşmede depolanan parametrenin değeri |
| Blok karması                                | nvarchar (255) | Yes         | Bloğun karması |
| Blok numarası                              | int           | Yes         | Genel muhasebedeki blok sayısı |
| BlockTimestamp                           | Tarih/saat (2, 7) | Yes         | Bloğun zaman damgası |
| TransactionId                            | int           | No          | İşlem için benzersiz bir tanımlayıcı |
| TransactionFrom                          | nvarchar (255) | Yes         | İşlemin kaynaklandığı taraf |
| TransactionTo                            | nvarchar (255) | Yes         | İle işlenen taraf |
| Işlem karması                          | nvarchar (255) | Yes         | İşlemin karması |
| Transactionısworkbenchtransaction        | bit           | Yes         | İşlemin bir Azure blok zinciri çalışma ekranı işlemi olup olmadığını tanımlayan bir bit |
| TransactionProvisioningStatus            | int           | Yes         | İşlem için sağlama işleminin geçerli durumunu tanımlar. Olası değerler şunlardır: <br />0 – işlem, veritabanında API tarafından oluşturulmuştur<br />1 – işlem, genel muhasebeye gönderildi<br />2 – işlem, genel muhasebeye başarıyla dağıtıldı                 |
| Işlem değeri                         | ondalık (32, 2) | Yes         | İşlemin değeri |

## <a name="vwcontractproperty"></a>Öcontractözelliği

Bu görünüm, bir sözleşmeyle ilişkili özelliklerle ilgili bilgilerin büyük bölümünü temsil eder ve ortak raporlama senaryolarını kolaylaştırmak için tasarlanmıştır. Alınan her bir özellik için, bu görünüm aşağıdaki verileri içerir:

-   İlişkili uygulama tanımı
-   İlişkili iş akışı tanımı
-   İş akışını dağıtan kullanıcının ayrıntıları
-   İlişkili akıllı anlaşma özelliği tanımı
-   Özellikler için belirli örnek değerleri
-   Sözleşmenin State özelliği için Ayrıntılar

| Ad                               | Tür          | Null olabilir | Description                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                      | int           | No          | Uygulama için benzersiz bir tanımlayıcı |
| ApplicationName                    | nvarchar (50)  | No          | Uygulamanın adı |
| ApplicationDisplayName             | nvarchar (255) | No          | Kullanıcı arabiriminde görüntülenecek ad |
| ApplicationEnabled                 | bit           | No          | Uygulamanın Şu anda etkin olup olmadığını tanımlar.<br />**Note:** Bir uygulama veritabanında devre dışı olarak yansıtılmasına rağmen, ilişkili sözleşmeler blok zincirinde kalır ve bu anlaşmalar hakkındaki veriler veritabanında kalır.                      |
| Workflowıd                         | int           | No          | İş akışı için benzersiz tanımlayıcı |
| WorkflowName                       | nvarchar (50)  | No          | İş akışının adı |
| WorkflowDisplayName                | nvarchar (255) | No          | Kullanıcı arabiriminde görünen iş akışının adı |
| WorkflowDescription                | nvarchar (255) | Yes         | İş akışının açıklaması |
| Sözleşme kimliği                         | int           | No          | Sözleşme için benzersiz tanımlayıcı |
| ContractProvisioningStatus         | int           | No          | Sözleşme için sağlama işleminin geçerli durumunu tanımlar. Olası değerler şunlardır: <br />0 – sözleşme, veritabanında API tarafından oluşturulmuştur<br />1 – sözleşme, genel muhasebeye gönderildi<br />2 – sözleşme, genel muhasebeye başarıyla dağıtıldı<br />3 veya 4-Sözleşme, muhasebeye dağıtılamadı<br />5-sözleşme, genel muhasebeye başarıyla dağıtıldı <br /><br />Sürüm 1,5 ' den başlayarak, 0 ile 5 arasındaki değerler desteklenir. Geçerli yayında geriye dönük uyumluluk için, **vwContractPropertyV0** , yalnızca 0 ile 2 arasındaki değerleri destekleyen bir görünüm bulunur. |
| Contractcodeıd                     | int           | No          | Sözleşmenin kod uygulamasına yönelik benzersiz bir tanımlayıcı |
| Contractledgerıdentifier           | nvarchar (255) | Yes         | Belirli bir dağıtılmış defter için, bir akıllı sözleşmenin dağıtılan sürümüyle ilişkili benzersiz bir tanımlayıcı. Örneğin, Ethereum. |
| Contractdeployedbyuserıd           | int           | No          | Sözleşmeyi dağıtan kullanıcının benzersiz tanımlayıcısı |
| ContractDeployedByUserFirstName    | nvarchar (50)  | Yes         | Sözleşmeyi dağıtan kullanıcının adı |
| ContractDeployedByUserLastName     | nvarchar (255) | Yes         | Sözleşmeyi dağıtan kullanıcının soyadı |
| Contractdeployedbyuserexternalıd   | nvarchar (255) | No          | Sözleşmeyi dağıtan kullanıcının dış tanımlayıcısı. Varsayılan olarak, bu KIMLIK, konsorsiyumde kimliklerini temsil eden GUID 'dir Azure Active Directory |
| Contractdeployedbyuseremaadresi | nvarchar (255) | Yes         | Sözleşmeyi dağıtan kullanıcının e-posta adresi |
| Workflowpropertyıd                 | int           |             | Bir iş akışının özelliği için benzersiz bir tanımlayıcı |
| Workflowpropertydatatypeıd         | int           | No          | Özelliğin veri türünün KIMLIĞI |
| WorkflowPropertyDataTypeName       | nvarchar (50)  | No          | Özelliğin veri türünün adı |
| WorkflowPropertyName               | nvarchar (50)  | No          | Workflow özelliğinin adı |
| WorkflowPropertyDisplayName        | nvarchar (255) | No          | Workflow özelliğinin görünen adı |
| WorkflowPropertyDescription        | nvarchar (255) | Yes         | Özelliğin açıklaması |
| ContractPropertyValue              | nvarchar (255) | No          | Sözleşmede bir özelliğin değeri |
| StateName                          | nvarchar (50)  | Yes         | Bu özellik sözleşmenin durumunu içeriyorsa, bu durum için görünen addır. Durumla ilişkilendirilmediği takdirde değer null olur. |
| StateDisplayName                   | nvarchar (255) | No          | Bu özellik durumu içeriyorsa, durum için görünen addır. Durumla ilişkilendirilmediği takdirde değer null olur. |
| StateValue                         | nvarchar (255) | Yes         | Bu özellik durumu içeriyorsa, durum değeridir. Durumla ilişkilendirilmediği takdirde değer null olur. |

## <a name="vwcontractstate"></a>Öcontractstate

Bu görünüm, belirli bir sözleşmenin durumuyla ilgili bilgilerin çoğunluğunu temsil eder ve ortak raporlama senaryolarını kolaylaştırmak için tasarlanmıştır. Bu görünümdeki her kayıt aşağıdaki verileri içerir:

-   İlişkili uygulama tanımı
-   İlişkili iş akışı tanımı
-   İş akışını dağıtan kullanıcının ayrıntıları
-   İlişkili akıllı anlaşma özelliği tanımı
-   Sözleşmenin State özelliği için Ayrıntılar

| Ad                               | Tür          | Null olabilir | Description                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                      | int           | No          | Uygulama için benzersiz bir tanımlayıcı |
| ApplicationName                    | nvarchar (50)  | No          | Uygulamanın adı |
| ApplicationDisplayName             | nvarchar (255) | No          | Kullanıcı arabiriminde görüntülenecek ad |
| ApplicationEnabled                 | bit           | No          | Uygulamanın Şu anda etkin olup olmadığını tanımlar.<br />**Note:** Bir uygulama veritabanında devre dışı olarak yansıtılmasına rağmen, ilişkili sözleşmeler blok zincirinde kalır ve bu anlaşmalar hakkındaki veriler veritabanında kalır. |
| Workflowıd                         | int           | No          | İş akışı için benzersiz bir tanımlayıcı |
| WorkflowName                       | nvarchar (50)  | No          | İş akışının adı |
| WorkflowDisplayName                | nvarchar (255) | No          | Kullanıcı arabiriminde görünen ad |
| WorkflowDescription                | nvarchar (255) | Yes         | İş akışının açıklaması |
| Contractledgerımplementationıd     | nvarchar (255) | Yes         | Belirli bir dağıtılmış defter için, bir akıllı sözleşmenin dağıtılan sürümüyle ilişkili benzersiz bir tanımlayıcı. Örneğin, Ethereum. |
| Sözleşme kimliği                         | int           | No          | Sözleşme için benzersiz bir tanımlayıcı |
| ContractProvisioningStatus         | int           | No          |Sözleşme için sağlama işleminin geçerli durumunu tanımlar. Olası değerler şunlardır: <br />0 – sözleşme, veritabanında API tarafından oluşturulmuştur<br />1 – sözleşme, genel muhasebeye gönderildi<br />2 – sözleşme, genel muhasebeye başarıyla dağıtıldı<br />3 veya 4-Sözleşme, muhasebeye dağıtılamadı<br />5-sözleşme, genel muhasebeye başarıyla dağıtıldı <br /><br />Sürüm 1,5 ' den başlayarak, 0 ile 5 arasındaki değerler desteklenir. Geçerli yayında geriye dönük uyumluluk için, **vwContractStateV0** , yalnızca 0 ile 2 arasındaki değerleri destekleyen bir görünüm bulunur. |
| ConnectionID                       | int           | No          | İş akışının dağıtıldığı blok zinciri örneği için benzersiz bir tanımlayıcı |
| Contractcodeıd                     | int           | No          | Sözleşmenin kod uygulamasına yönelik benzersiz bir tanımlayıcı |
| Contractdeployedbyuserıd           | int           | No          | Sözleşmeyi dağıtan kullanıcının benzersiz tanımlayıcısı |
| Contractdeployedbyuserexternalıd   | nvarchar (255) | No          | Sözleşmeyi dağıtan kullanıcının dış tanımlayıcısı. Varsayılan olarak, bu KIMLIK, Konsorsiyumu Azure Active Directory kimliğini temsil eden GUID 'dir. |
| ContractDeployedByUserFirstName    | nvarchar (50)  | Yes         | Sözleşmeyi dağıtan kullanıcının adı |
| ContractDeployedByUserLastName     | nvarchar (255) | Yes         | Sözleşmeyi dağıtan kullanıcının soyadı |
| Contractdeployedbyuseremaadresi | nvarchar (255) | Yes         | Sözleşmeyi dağıtan kullanıcının e-posta adresi |
| Workflowpropertyıd                 | int           | No          | Bir iş akışı özelliği için benzersiz bir tanımlayıcı |
| Workflowpropertydatatypeıd         | int           | No          | Workflow özelliğinin veri türünün KIMLIĞI |
| WorkflowPropertyDataTypeName       | nvarchar (50)  | No          | Workflow özelliğinin veri türünün adı |
| WorkflowPropertyName               | nvarchar (50)  | No          | Workflow özelliğinin adı |
| WorkflowPropertyDisplayName        | nvarchar (255) | No          | Bir kullanıcı arabiriminde gösterilecek özelliğin görünen adı |
| WorkflowPropertyDescription        | nvarchar (255) | Yes         | Özelliğin açıklaması |
| ContractPropertyValue              | nvarchar (255) | No          | Sözleşmede depolanan bir özelliğin değeri |
| StateName                          | nvarchar (50)  | Yes         | Bu özellik durumu içeriyorsa, durumun görünen adı. Durumla ilişkilendirilmediği takdirde değer null olur. |
| StateDisplayName                   | nvarchar (255) | No          | Bu özellik durumu içeriyorsa, durum için görünen addır. Durumla ilişkilendirilmediği takdirde değer null olur. |
| StateValue                         | nvarchar (255) | Yes         | Bu özellik durumu içeriyorsa, durum değeridir. Durumla ilişkilendirilmediği takdirde değer null olur. |

## <a name="vwuser"></a>Ökullanıcı

Bu görünüm, Azure blok zinciri çalışma ekranı kullanımı için sağlanan konsorsiyum üyeleri hakkında ayrıntılar sağlar. Varsayılan olarak, veriler kullanıcının ilk sağlanması yoluyla doldurulur.

| Ad               | Tür          | Null olabilir | Description                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID                 | int           | No          | Bir kullanıcı için benzersiz bir tanımlayıcı |
| ExternalID         | nvarchar (255) | No          | Bir kullanıcı için dış tanımlayıcı. Varsayılan olarak, bu KIMLIK kullanıcının Azure Active Directory KIMLIĞINI temsil eden GUID 'dir. |
| ProvisioningStatus | int           | No          |Kullanıcı için sağlama işleminin geçerli durumunu tanımlar. Olası değerler şunlardır: <br />0 – Kullanıcı API tarafından oluşturuldu<br />1 – veritabanındaki kullanıcıyla ilişkili bir anahtar<br />2 – Kullanıcı tam olarak sağlanıyor |
| FirstName          | nvarchar (50)  | Yes         | Kullanıcının ilk adı |
| LastName           | nvarchar (50)  | Yes         | Kullanıcının soyadı |
| EmailAddress       | nvarchar (255) | Yes         | Kullanıcının e-posta adresi |

## <a name="vwworkflow"></a>diğer Iş akışı

Bu görünüm, temel iş akışı meta verilerinin yanı sıra iş akışının işlevlerini ve parametrelerini temsil eder. Raporlama için tasarlanan, iş akışıyla ilişkili uygulamayla ilgili meta verileri de içerir. Bu görünüm, iş akışlarında raporlamayı kolaylaştırmak için birden çok temel tablodaki verileri içerir. Her iş akışı için, bu görünüm aşağıdaki verileri içerir:

-   İlişkili uygulama tanımı
-   İlişkili iş akışı tanımı
-   İlişkili iş akışı başlatma durumu bilgileri

| Ad                              | Tür          | Null olabilir | Description                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                     | int           | No          | Uygulama için benzersiz bir tanımlayıcı |
| ApplicationName                   | nvarchar (50)  | No          | Uygulamanın adı |
| ApplicationDisplayName            | nvarchar (255) | No          | Kullanıcı arabiriminde görüntülenecek ad |
| ApplicationEnabled                | bit           | No          | Uygulamanın etkinleştirilip etkinleştirilmediğini tanımlar |
| Workflowıd                        | int           | Yes         | Bir iş akışı için benzersiz bir tanımlayıcı |
| WorkflowName                      | nvarchar (50)  | No          | İş akışının adı |
| WorkflowDisplayName               | nvarchar (255) | No          | Kullanıcı arabiriminde görünen ad |
| WorkflowDescription               | nvarchar (255) | Yes         | İş akışının açıklaması. |
| Workflowconstructorfunctionıd     | int           | No          | İş akışı için Oluşturucu olarak hizmet veren iş akışı işlevinin tanımlayıcısı |
| Workflowstartstateıd              | int           | No          | Durum için benzersiz bir tanımlayıcı |
| WorkflowStartStateName            | nvarchar (50)  | No          | Durum adı |
| WorkflowStartStateDisplayName     | nvarchar (255) | No          | Durum için Kullanıcı arabiriminde görüntülenecek ad |
| WorkflowStartStateDescription     | nvarchar (255) | Yes         | İş akışı durumunun açıklaması |
| WorkflowStartStateStyle           | nvarchar (50)  | Yes         | Bu değer, bu durumda iş akışının tamamlanma yüzdesini tanımlar |
| WorkflowStartStateValue           | int           | No          | Durumun değeri |
| Workflowstartstatepercenttamamlanmıştır | int           | No          | Kullanıcı arabiriminde bu durumun nasıl işleneceğini gösteren istemcilere bir ipucu sağlayan metin açıklaması. Desteklenen durumlar *başarı* ve *başarısızlık* içerir |

## <a name="vwworkflowfunction"></a>bir Öworkflowfunction

Bu görünüm, temel iş akışı meta verilerinin yanı sıra iş akışının işlevlerini ve parametrelerini temsil eder. Raporlama için tasarlanan, iş akışıyla ilişkili uygulamayla ilgili meta verileri de içerir. Bu görünüm, iş akışlarında raporlamayı kolaylaştırmak için birden çok temel tablodaki verileri içerir. Her bir iş akışı işlevi için, bu görünüm aşağıdaki verileri içerir:

-   İlişkili uygulama tanımı
-   İlişkili iş akışı tanımı
-   İş akışı işlev ayrıntıları

| Ad                                 | Tür          | Null olabilir | Description                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationID                        | int           | No          | Uygulama için benzersiz bir tanımlayıcı |
| ApplicationName                      | nvarchar (50)  | No          | Uygulamanın adı |
| ApplicationDisplayName               | nvarchar (255) | No          | Kullanıcı arabiriminde görüntülenecek ad |
| ApplicationEnabled                   | bit           | No          | Uygulamanın etkinleştirilip etkinleştirilmediğini tanımlar |
| Workflowıd                           | int           | No          | Bir iş akışı için benzersiz bir tanımlayıcı |
| WorkflowName                         | nvarchar (50)  | No          | İş akışının adı |
| WorkflowDisplayName                  | nvarchar (255) | No          | Kullanıcı arabiriminde görünen iş akışının adı |
| WorkflowDescription                  | nvarchar (255) | Yes         | İş akışının açıklaması |
| Workflowfunctionıd                   | int           | No          | Bir işlev için benzersiz tanımlayıcı |
| Workflowfonksiyonadı                 | nvarchar (50)  | Yes         | İşlevin adı |
| WorkflowFunctionDisplayName          | nvarchar (255) | No          | Kullanıcı arabiriminde gösterilecek işlevin adı |
| WorkflowFunctionDescription          | nvarchar (255) | Yes         | İş akışı işlevinin açıklaması |
| WorkflowFunctionIsConstructor        | bit           | No          | İş akışı işlevinin iş akışı için Oluşturucu olup olmadığını belirler |
| Workflowfunctionparameterıd          | int           | No          | Bir işlevin parametresi için benzersiz bir tanımlayıcı |
| WorkflowFunctionParameterName        | nvarchar (50)  | No          | İşlevin parametresinin adı |
| WorkflowFunctionParameterDisplayName | nvarchar (255) | No          | Kullanıcı arabiriminde gösterilecek bir işlev parametresinin adı |
| Workflowfunctionparameterdatatypeıd  | int           | No          | Bir iş akışı işlev parametresiyle ilişkili veri türü için benzersiz bir tanımlayıcı |
| WorkflowParameterDataTypeName        | nvarchar (50)  | No          | Bir iş akışı işlev parametresiyle ilişkili bir veri türünün adı |

## <a name="vwworkflowproperty"></a>Öworkflowproperty

Bu görünüm, bir iş akışı için tanımlanan özellikleri temsil eder. Her özellik için, bu görünüm aşağıdaki verileri içerir:

-   İlişkili uygulama tanımı
-   İlişkili iş akışı tanımı
-   İş akışı özellik ayrıntıları

| Ad                         | Tür          | Null olabilir | Description                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                | int           | No          | Uygulama için benzersiz bir tanımlayıcı |
| ApplicationName              | nvarchar (50)  | No          | Uygulamanın adı |
| ApplicationDisplayName       | nvarchar (255) | No          | Kullanıcı arabiriminde görüntülenecek ad |
| ApplicationEnabled           | bit           | No          | Uygulamanın Şu anda etkin olup olmadığını tanımlar.<br />**Note:** Bir uygulama veritabanında devre dışı olarak yansıtılmasına rağmen, ilişkili sözleşmeler blok zincirinde kalır ve bu anlaşmalar hakkındaki veriler veritabanında kalır. |
| Workflowıd                   | int           | No          | İş akışı için benzersiz bir tanımlayıcı |
| WorkflowName                 | nvarchar (50)  | No          | İş akışının adı |
| WorkflowDisplayName          | nvarchar (255) | No          | Kullanıcı arabirimindeki iş akışı için görüntülenecek ad |
| WorkflowDescription          | nvarchar (255) | Yes         | İş akışının açıklaması |
| Workflowpropertyıd           | int           | No          | Bir iş akışının özelliği için benzersiz bir tanımlayıcı |
| WorkflowPropertyName         | nvarchar (50)  | No          | Özelliğin adı |
| WorkflowPropertyDescription  | nvarchar (255) | Yes         | Özelliğin açıklaması |
| WorkflowPropertyDisplayName  | nvarchar (255) | No          | Kullanıcı arabiriminde görüntülenecek ad |
| Workflowpropertyworkflowwıd   | int           | No          | Bu özelliğin ilişkilendirildiği iş akışının KIMLIĞI |
| Workflowpropertydatatypeıd   | int           | No          | Özellik için tanımlanan veri türünün KIMLIĞI |
| WorkflowPropertyDataTypeName | nvarchar (50)  | No          | Özellik için tanımlanan veri türünün adı |
| Workflowpropertyısstate      | bit           | No          | Bu alan, bu iş akışı özelliğinin iş akışının durumunu içerip içerne olduğunu tanımlar |

## <a name="vwworkflowstate"></a>daha fazla Workflowstate

Bu görünüm bir iş akışıyla ilişkili özellikleri temsil eder. Her sözleşme için, bu görünüm aşağıdaki verileri içerir:

-   İlişkili uygulama tanımı
-   İlişkili iş akışı tanımı
-   İş akışı durum bilgileri

| Ad                         | Tür          | Null olabilir | Description                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                | int           | No          | Uygulama için benzersiz bir tanımlayıcı |
| ApplicationName              | nvarchar (50)  | No          | Uygulamanın adı |
| ApplicationDisplayName       | nvarchar (255) | No          | Uygulamanın açıklaması |
| ApplicationEnabled           | bit           | No          | Uygulamanın Şu anda etkin olup olmadığını tanımlar.<br />**Note:** Bir uygulama veritabanında devre dışı olarak yansıtılmasına rağmen, ilişkili sözleşmeler blok zincirinde kalır ve bu anlaşmalar hakkındaki veriler veritabanında kalır. |
| Workflowıd                   | int           | No          | İş akışı için benzersiz tanımlayıcı |
| WorkflowName                 | nvarchar (50)  | No          | İş akışının adı |
| WorkflowDisplayName          | nvarchar (255) | No          | İş akışı için Kullanıcı arabiriminde görünen ad |
| WorkflowDescription          | nvarchar (255) | Yes         | İş akışının açıklaması |
| Workflowstateıd              | int           | No          | Durum için benzersiz tanımlayıcı |
| WorkflowStateName            | nvarchar (50)  | No          | Durum adı |
| WorkflowStateDisplayName     | nvarchar (255) | No          | Durum için Kullanıcı arabiriminde görüntülenecek ad |
| WorkflowStateDescription     | nvarchar (255) | Yes         | İş akışı durumunun açıklaması |
| Workflowstatepercenttamamlanmıştır | int           | No          | Bu değer, bu durumda iş akışının tamamlanma yüzdesini tanımlar |
| WorkflowStateValue           | nvarchar (50)  | No          | Durumun değeri |
| WorkflowStateStyle           | nvarchar (50)  | No          | Kullanıcı arabiriminde bu durumun nasıl işleneceğini gösteren istemcilere bir ipucu sağlayan metin açıklaması. Desteklenen durumlar *başarı* ve *başarısızlık* içerir |

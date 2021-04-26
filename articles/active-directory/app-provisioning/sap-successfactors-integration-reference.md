---
title: Azure Active Directory ve SAP başarılı etmenleri tümleştirme başvurusu
description: Hızlı bir şekilde SAP 'ye yakından bakış-HR odaklı sağlama
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: ed97600ca1802629f81f93f4f51c92ad4b1c9bd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99256230"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-sap-successfactors"></a>Azure Active Directory sağlama, SAP başarılı faktörlerle nasıl tümleştirilir? 

[Azure Active Directory Kullanıcı sağlama hizmeti](../app-provisioning/user-provisioning.md) , kullanıcıların kimlik yaşam döngüsünü yönetmek Için [SAP başarılı, çalışan merkezi](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) ile tümleşir. Azure Active Directory önceden oluşturulmuş üç tümleştirme sunar: 

* [Şirket içi Active Directory Kullanıcı sağlama için başarılı etken](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [Kullanıcı sağlamayı Azure Active Directory için başarılı etken](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)
* [Başarılı bir şekilde geri yazma](../saas-apps/sap-successfactors-writeback-tutorial.md)

Bu makalede tümleştirmenin nasıl çalıştığı ve farklı ık senaryolar için sağlama davranışının nasıl özelleştirileceği açıklanmaktadır. 

## <a name="establishing-connectivity"></a>Bağlantı kuruluyor 
Azure AD sağlama hizmeti, çalışan merkezi OData API uç noktalarına bağlanmak için temel kimlik doğrulamasını kullanır. Başarılı etmenleri sağlama uygulamasını ayarlarken, [API veri MERKEZI URL](https://apps.support.sap.com/sap/support/knowledge/en/2215682)'sini yapılandırmak Için *yönetici kimlik bilgileri* bölümünde *kiracı URL 'si* parametresini kullanın. 

Azure AD sağlama hizmeti ile başarılı etmenler arasındaki bağlantıyı daha da güvenli hale getirmek için, aşağıdaki adımları kullanarak Azure AD IP aralıklarını başarılı etmenlerin IP izin verilenler-listesine ekleyebilirsiniz:

1. Azure genel bulutu için [en son IP aralıklarını](https://www.microsoft.com/download/details.aspx?id=56519) indirin 
1. Dosyayı açın ve etiket **AzureActiveDirectory** arayın 

   >[!div class="mx-imgBorder"] 
   >![Azure AD IP aralığı](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. Bu öğe *Adresspredüzeltmeleriyle* LISTELENEN tüm IP adresi aralıklarını KOPYALAYıN ve IP adresi kısıtlama listenizi derlemek için aralığı kullanın.
1. CıDR değerlerini IP aralıklarına çevirin.  
1. İzin verilenler listesine IP aralıkları eklemek için başarılı bir Yönetim Portalı ' nda oturum açın. SAP [destek notuna 2253200](https://apps.support.sap.com/sap/support/knowledge/en/2253200)bakın. Artık bu araca [IP aralıklarını girebilirsiniz](https://answers.sap.com/questions/12882263/whitelisting-sap-cloud-platform-ip-address-range-i.html) . 

## <a name="supported-entities"></a>Desteklenen varlıklar
Başarılı faktörlerdeki her kullanıcı için, Azure AD sağlama hizmeti aşağıdaki varlıkları alır. Her varlık OData API *$Expand* sorgu parametresi kullanılarak genişletilir. Aşağıdaki *alma kuralı* sütununa bakın. Bazı varlıklar varsayılan olarak genişletilir, ancak bazı varlıklar yalnızca eşlemede belirli bir öznitelik mevcutsa genişletilir. 

| \# | Başarılı etken varlığı                  | OData düğümü     | Alma kuralı |
|----|----------------------------------------|------------------------------|------------------|
| 1  | PerPerson                              | *kök düğüm*                  | Her zaman           |
| 2  | PerPersonal                            | Kişiselleştirilfonav              | Her zaman           |
| 3  | PerPhone                               | phoneNav                     | Her zaman           |
| 4  | PerEmail                               | emailNav                     | Her zaman           |
| 5  | Empistihdam                          | employmentNav                | Her zaman           |
| 6  | Kullanıcı                                   | employmentNav/userNav        | Her zaman           |
| 7  | EmpJob                                 | employmentNav/Jobınfonav     | Her zaman           |
| 8  | EmpEmploymentTermination               | activeEmploymentsCount       | Her zaman           |
| 9  | Kullanıcının Yöneticisi                         | employmentNav/userNav/Manager/Empınfo | Her zaman  |
| 10 | FOCompany                              | employmentNav/Jobınfonav/companyNav | Yalnızca `company` veya `companyId` özniteliği eşlenmişse |
| 11 | FODepartment                           | employmentNav/Jobınfonav/departmentNav | Yalnızca `department` veya `departmentId` özniteliği eşlenmişse |
| 12 | FOBusinessUnit                         | employmentNav/Jobınfonav/businessUnitNav | Yalnızca `businessUnit` veya `businessUnitId` özniteliği eşlenmişse |
| 13 | FOCostCenter                           | employmentNav/Jobınfonav/costCenterNav | Yalnızca `costCenter` veya `costCenterId` özniteliği eşlenmişse |
| 14 | Fobölüm                             | employmentNav/Jobınfonav/divisionNav  | Yalnızca `division` veya `divisionId` özniteliği eşlenmişse |
| 15 | FOJobCode                              | employmentNav/Jobınfonav/jobCodeNav  | Yalnızca `jobCode` veya `jobCodeId` özniteliği eşlenmişse |
| 16 | Fopaygrad                             | employmentNav/Jobınfonav/payGradeNav  | Yalnızca `payGrade` özniteliği eşlenmişse |
| 17 | Katmanma                             | employmentNav/Jobınfonav/locationNav  | Yalnızca `location` özniteliği eşlenmişse |
| 18 | FOCorporateAddressDEFLT                | employmentNav/Jobınfonav/addressNavDEFLT  | Eşleme aşağıdaki özniteliklerden birini içeriyorsa: `officeLocationAddress,  officeLocationCity, officeLocationZipCode` |
| 19 | FOEventReason                          | employmentNav/Jobınfonav/eventReasonNav  | Yalnızca `eventReason` özniteliği eşlenmişse |
| 20 | EmpGlobalAssignment                    | employmentNav/empGlobalAssignmentNav | Yalnızca `assignmentType` eşlenmişse |
| 21 | EmploymentType seçim listesi                | employmentNav/Jobınfonav/employmentTypeNav | Yalnızca `employmentType` eşlenmişse |
| 22 | EmployeeClass seçim listesi                 | employmentNav/Jobınfonav/employeeClassNav | Yalnızca `employeeClass` eşlenmişse |
| 23 | EmplStatus seçim listesi                    | employmentNav/Jobınfonav/emplStatusNav | Yalnızca `emplStatus` eşlenmişse |
| 24 | Atamatürü seçim listesi                | employmentNav/empGlobalAssignmentNav/Atamatypenav | Yalnızca `assignmentType` eşlenmişse |

## <a name="how-full-sync-works"></a>Tam eşitlemenin nasıl çalıştığı
Öznitelik eşleme temel alınarak, tam eşitleme sırasında Azure AD sağlama hizmeti, tüm etkin kullanıcıların etkili verilerini getirmek için aşağıdaki "GET" OData API sorgusunu gönderir. 

> [!div class="mx-tdCol2BreakAll"]
>| Parametre | Açıklama |
>| ----------|-------------|
>| OData API ana bilgisayarı | Https *'Yi kiracı URL*'sine ekler. Örnek: `https://api4.successfactors.com` |
>| OData API uç noktası | `/odata/v2/PerPerson` |
>| OData $format sorgu parametresi | `json` |
>| OData $filter sorgu parametresi | `(personEmpTerminationInfoNav/activeEmploymentsCount ge 1) and (lastModifiedDateTime le <CurrentExecutionTime>)` |
>| OData $expand sorgu parametresi | Bu parametre değeri, eşlenen özniteliklere bağlıdır. Örnek: `employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,phoneNav,emailNav,employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav` |
>| OData customPageSize sorgu parametresi | `100` |

> [!NOTE]
> İlk ilk tam eşitleme sırasında Azure AD sağlama hizmeti, etkin olmayan/sonlandırılmış çalışan verileri çekmez.

Her başarılı Kullanıcı için, sağlama hizmeti, eşlemede tanımlanan eşleşen özniteliği kullanarak hedefte (Azure AD/şirket içi Active Directory) bir hesap arar. Örneğin: *Personıdexternal* , *EmployeeID* ile eşlenir ve eşleşen öznitelik olarak ayarlandıysa, sağlama hizmeti kullanıcıyı *EmployeeID* filtresiyle aramak için *personıdexternal* değerini kullanır. Bir Kullanıcı eşleşmesi bulunursa, hedef öznitelikleri günceller. Eşleşme bulunmazsa, hedefte yeni bir giriş oluşturur. 

OData API uç noktanız tarafından döndürülen verileri belirli bir için doğrulamak üzere `personIdExternal` `SuccessFactorsAPIEndpoint` API veri MERKEZI sunucu URL 'niz Ile aşağıdaki API sorgusunda güncelleştirin ve sorguyu çağırmak Için [Postman](https://www.postman.com/downloads/) gibi bir araç kullanın. 

```
https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
$filter=(personIdExternal in '[personIdExternalValue]')&
$expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
```

## <a name="how-incremental-sync-works"></a>Artımlı eşitlemenin nasıl çalıştığı

Tam eşitlemeden sonra Azure AD sağlama hizmeti, `LastExecutionTimestamp` artımlı değişiklikleri almak için Delta sorguları oluşturmak üzere onu saklar ve kullanır. ,, Ve gibi her bir başarılı zaman damgası öznitelikleri, `lastModifiedDateTime` `startDate` `endDate` `latestTerminationDate` değişikliğin ve arasında olup olmadığını görmek için değerlendirilir `LastExecutionTimestamp` `CurrentExecutionTime` . Yanıt Evet ise, giriş değişikliğinin etkili olduğu kabul edilir ve eşitleme için işlenir. 

## <a name="reading-attribute-data"></a>Öznitelik verilerini okuma

Azure AD sağlama hizmeti başarılı faktörleri sorguladığında bir JSON sonuç kümesi alır. JSON sonuç kümesi, çalışan merkezi 'nde depolanan bir dizi özniteliği içerir. Varsayılan olarak, sağlama şeması bu özniteliklerin yalnızca bir alt kümesini almak için yapılandırılır. 

Ek öznitelikler almak için aşağıda listelenen adımları izleyin:
    
1. **Kurumsal uygulamalara** gidin başarılı bir şekilde  ->  **uygulama**  ->  **sağlama**  ->    ->  **özelliği sağlama özniteliği eşleme sayfası**.
1. Aşağı kaydırın ve **Gelişmiş seçenekleri göster**' e tıklayın.
1. **Başarılı etmenler için öznitelik listesini düzenle**' ye tıklayın. 

   > [!NOTE] 
   > **Başarılı etmenleri için öznitelik listesini düzenle** seçeneği Azure Portal görünmüyorsa, *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* sayfaya erişmek için URL 'yi kullanın. 

1. Bu görünümdeki **API ifadesi** sütunu, bağlayıcı tarafından kullanılan jsonpath ifadelerini görüntüler.

   >[!div class="mx-imgBorder"] 
   >![API Ifadesi](media/sap-successfactors-integration-reference/jsonpath-api-expressions.png#lightbox)  

1. Varolan bir JSONPath değerini düzenleyebilir ya da şemaya geçerli bir JSONPath ifadesi ile yeni bir öznitelik ekleyebilirsiniz. 

Sonraki bölümde, JSONPath değerlerini düzenlemeyle ilgili yaygın senaryoların bir listesi verilmiştir. 

## <a name="handling-different-hr-scenarios"></a>Farklı ık senaryolarını işleme

JSONPath, XML için XPath 'e benzer bir JSON sorgu dilidir. XPath gibi, JSONPath de JSON yükünün dışına ayıklanmasını ve verilerin ölçeğini dışarı aktarmanıza olanak tanır.

JSONPath dönüşümünü kullanarak, Azure AD sağlama uygulamasının davranışını özel öznitelikler almak ve yeniden işe alma, çalışan dönüştürme ve küresel atama gibi senaryoları işlemek için özelleştirebilirsiniz. 

Bu bölüm, aşağıdaki HR senaryolarında sağlama uygulamasını nasıl özelleştirebileceğinizi anlatmaktadır: 
* [Ek öznitelikleri alma](#retrieving-additional-attributes)
* [Özel öznitelikler alınıyor](#retrieving-custom-attributes)
* [Çalışan dönüştürme senaryosunu işleme](#handling-worker-conversion-scenario)
* [Yeniden işe alma senaryosu işleniyor](#handling-rehire-scenario)
* [Genel atama senaryosunu işleme](#handling-global-assignment-scenario)
* [Eşzamanlı işleri işleme senaryosu](#handling-concurrent-jobs-scenario)

### <a name="retrieving-additional-attributes"></a>Ek öznitelikleri alma

Varsayılan Azure AD başarılı etmenleri sağlama uygulama şeması, [90 + önceden tanımlanmış özniteliklerle](sap-successfactors-attribute-reference.md)birlikte gelir. Sağlama şemasına daha fazla başarılı bir öznitelik eklemek için aşağıda listelenen adımları kullanın: 

1. Çalışan merkezi 'nden geçerli bir test kullanıcısına ait verileri almak için aşağıdaki OData sorgusunu kullanın. 

   ```
    https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
    $filter=(personIdExternal in '[personIdExternalValue]')&
    $expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
    phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
    employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
    employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
    employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
    employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
    employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
   ```

1. Öznitelikle ilişkili çalışan merkezi varlığını belirleme
   * Özniteliği *Empistihdam* varlığının bir parçasıysa, *employmentNav* node altındaki özniteliği arayın. 
   * Özniteliği *Kullanıcı* varlığının parçasıysa, *EmploymentNav/usernav* düğümündeki özniteliği bulun.
   * Özniteliği *Empjob* varlığının parçasıysa, *EmploymentNav/Jobınfonav* düğümündeki özniteliği bulun. 
1. Özniteliğiyle ilişkili JSON yolunu oluşturun ve bu yeni özniteliği başarılı Özellikler listesine ekleyin. 
   * Örnek 1: *employmentNav* varlığının bir parçası olan *Oktoreişe alma* özniteliğini eklemek ve ardından jsonpath 'i kullanmak istediğinizi varsayalım`$.employmentNav.results[0].okToRehire`
   * Örnek 2: *Usernav* varlığının bir parçası olan Attribute *saat dilimini* eklemek Istediğinizi söyleyin, sonra jsonpath kullanın`$.employmentNav.results[0].userNav.timeZone`
   * Örnek 3: bir *Jobınfonav* varlığının parçası olan *flsastatus* özniteliğini eklemek Istediğinizi söyleyin, sonra jsonpath kullanın`$.employmentNav.results[0].jobInfoNav.results[0].flsaStatus`
1. Şemayı kaydedin. 
1. Sağlamayı yeniden başlatın.

### <a name="retrieving-custom-attributes"></a>Özel öznitelikler alınıyor

Varsayılan olarak, aşağıdaki özel öznitelikler Azure AD başarılı etmenleri sağlama uygulamasında önceden tanımlanmıştır: 
* *custom01-Custom15-* User (usernav) varlığındaki
* *empNavCustomString1-empNavCustomString15* adlı empistihdam (employmentNav) varlığındaki *customString1-customString15*
* *empJobNavCustomString1-empNavJobCustomString15* adlı Empjobınfo (Jobınfonav) varlığındaki *customString1-customString15*

Diyelim ki, çalışan merkezi Örneğinizde, *Empjobınfo* 'daki *customString35* özniteliği konum açıklamasını depolar. Bu değeri Active Directory *physicalDeliveryOfficeName* özniteliğine akmasını istiyorsunuz. Bu senaryoya yönelik öznitelik eşlemesini yapılandırmak için aşağıda verilen adımları kullanın: 

1. *EmpJobNavCustomString35* adlı yeni bir öznitelik eklemek Için başarılı etmenler öznitelik listesini düzenleyin.
1. Bu öznitelik için JSONPath API ifadesini şu şekilde ayarlayın: `$.employmentNav.results[0].jobInfoNav.results[0].customString35`
1. Eşleme değişikliğini Azure portal kaydedin ve yeniden yükleyin.  
1. Öznitelik eşleme dikey penceresinde *empJobNavCustomString35* öğesini *physicalDeliveryOfficeName* olarak eşleyin.
1. Eşlemeyi kaydedin.

Bu senaryo genişletiliyor: 
* *Kullanıcı* varlığındaki *custom35* özniteliğini eşlemek istiyorsanız, jsonpath kullanın`$.employmentNav.results[0].userNav.custom35`
* *Empistihdam* varlığındaki *customString35* özniteliğini eşlemek istiyorsanız, jsonpath kullanın`$.employmentNav.results[0].customString35`

### <a name="handling-worker-conversion-scenario"></a>Çalışan dönüştürme senaryosunu işleme

Çalışan dönüştürme işlemi, mevcut bir tam zamanlı çalışanı bir yüklenicisi veya yüklenicisi için tam zamanlı olarak dönüştürme işlemidir. Bu senaryoda, çalışan Merkezi aynı *kişi* varlığı için yeni bir *Kullanıcı* varlığıyla birlikte yeni bir *empistihdam* varlığı ekler. Önceki *Empistihdam* varlığı altında iç Içe geçmiş *Kullanıcı* varlığı null olarak ayarlanır. Bir dönüştürme gerçekleştiğinde yeni çalışma verilerinin görünmesi için bu senaryoyu işlemek üzere, sağlama uygulama şemasını aşağıda listelenen adımları kullanarak toplu olarak güncelleştirebilirsiniz:  

1. Başarılı etken sağlama uygulamanızın öznitelik eşleme dikey penceresini açın. 
1. Aşağı kaydırın ve **Gelişmiş seçenekleri göster**' e tıklayın.
1. Şema düzenleyicisini açmak için **şemanızın bağlantısını inceleyin** . 

   >![Ekran görüntüsünde, şema düzenleyicisini açan şemayı buraya gözden geçirin bağlantısı gösterilir.](media/sap-successfactors-integration-reference/review-schema.png#lightbox)

1. Düzenlemeden önce şemanın bir kopyasını kaydetmek için **indirme** bağlantısına tıklayın. 

   >![Ekran görüntüsü şemayı bir kopyasını kaydetmek için, Indirme ile şema düzenleyicisini gösterir.](media/sap-successfactors-integration-reference/download-schema.png#lightbox)
1. Şema düzenleyicisinde, CTRL-H tuşuna basarak Bul-Değiştir denetimini açın.
1. Metin Bul kutusunda, değeri kopyalayın ve yapıştırın `$.employmentNav.results[0]`
1. Değiştir metin kutusuna değeri kopyalayın ve yapıştırın `$.employmentNav.results[?(@.userNav != null)]` . `!=`JSONPath ifadesinin başarılı işlenmesi için önemli olan işleci çevreleyen boşluğu dikkat edin. 
   >![Bul-Değiştir-dönüştürme](media/sap-successfactors-integration-reference/find-replace-conversion-scenario.png#lightbox)
1. Şemayı güncelleştirmek için "Tümünü Değiştir" seçeneğine tıklayın. 
1. Şemayı kaydedin. 
1. Yukarıdaki işlem, tüm JSONPath ifadelerini aşağıdaki gibi güncelleştirir: 
   * Eski JSONPath: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * Yeni JSONPath: `$.employmentNav.results[?(@.userNav != null)].jobInfoNav.results[0].departmentNav.name_localized`
1. Sağlamayı yeniden başlatın. 

### <a name="handling-rehire-scenario"></a>Yeniden işe alma senaryosu işleniyor

Genellikle, yeniden işlemeyi işlemek için iki seçenek vardır: 
* Seçenek 1: çalışan merkezi 'nde yeni bir kişi profili oluşturma
* Seçenek 2: çalışan merkezi 'nde var olan kişi profilini yeniden kullanma

HR işleminiz 1 seçeneğini kullanıyorsa, sağlama şemasında hiçbir değişiklik yapılması gerekmez. HR işleminiz seçenek 2 ' yi kullanıyorsa, çalışan Merkezi, aynı *kişi* varlığı için yeni bir *Kullanıcı* varlığıyla birlikte yeni bir *empistihdam* varlığı ekler. Dönüştürme senaryosunun aksine, önceki *Empistihdam* varlığındaki *Kullanıcı* varlığı null olarak ayarlanmadı. 

Bu yeniden işe alma senaryosunu (2. seçenek) işlemek için, en son iş verilerinin yeniden işe alma profillerinin görünmesi için, sağlama uygulama şemasını aşağıda listelenen adımları kullanarak toplu olarak güncelleştirebilirsiniz:  

1. Başarılı etken sağlama uygulamanızın öznitelik eşleme dikey penceresini açın. 
1. Aşağı kaydırın ve **Gelişmiş seçenekleri göster**' e tıklayın.
1. Şema düzenleyicisini açmak için **şemanızın bağlantısını inceleyin** .   
1. Düzenlemeden önce şemanın bir kopyasını kaydetmek için **indirme** bağlantısına tıklayın.   
1. Şema düzenleyicisinde, CTRL-H tuşuna basarak Bul-Değiştir denetimini açın.
1. Metin Bul kutusunda, değeri kopyalayın ve yapıştırın `$.employmentNav.results[0]`
1. Değiştir metin kutusuna değeri kopyalayın ve yapıştırın `$.employmentNav.results[-1:]` . Bu JSONPath ifadesi en son *Empistihdam* kaydını döndürür.   
1. Şemayı güncelleştirmek için "Tümünü Değiştir" seçeneğine tıklayın. 
1. Şemayı kaydedin. 
1. Yukarıdaki işlem, tüm JSONPath ifadelerini aşağıdaki gibi güncelleştirir: 
   * Eski JSONPath: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * Yeni JSONPath: `$.employmentNav.results[-1:].jobInfoNav.results[0].departmentNav.name_localized`
1. Sağlamayı yeniden başlatın. 

Bu şema değişikliği, çalışan dönüştürme senaryosunu da destekler. 

### <a name="handling-global-assignment-scenario"></a>Genel atama senaryosunu işleme

Çalışan merkezi 'nde bir Kullanıcı genel atama için işlendiğinde, başarılı bir şekilde yeni bir *Empistihdam* varlığı ekler ve *ATAMASıNıFı* "ga" olarak ayarlar. Ayrıca yeni *Kullanıcı* varlığı da oluşturur. Bu nedenle, Kullanıcı şu anda şunları içerir:
*   +  *Atamasınıfı* ile ana atamaya karşılık gelen bir empistihdam *Kullanıcı* varlığı "St" olarak ayarlanmış ve 
*   +  *Atamasınıfı* ile genel atamaya karşılık gelen, "ga" olarak ayarlanmış başka bir empistihdam *Kullanıcı* varlığı

Standart atamaya ve genel atama Kullanıcı profiline ait öznitelikleri getirmek için aşağıda listelenen adımları kullanın: 

1. Başarılı etken sağlama uygulamanızın öznitelik eşleme dikey penceresini açın. 
1. Aşağı kaydırın ve **Gelişmiş seçenekleri göster**' e tıklayın.
1. Şema düzenleyicisini açmak için **şemanızın bağlantısını inceleyin** .   
1. Düzenlemeden önce şemanın bir kopyasını kaydetmek için **indirme** bağlantısına tıklayın.   
1. Şema düzenleyicisinde, CTRL-H tuşuna basarak Bul-Değiştir denetimini açın.
1. Metin Bul kutusunda, değeri kopyalayın ve yapıştırın `$.employmentNav.results[0]`
1. Değiştir metin kutusuna değeri kopyalayın ve yapıştırın `$.employmentNav.results[?(@.assignmentClass == 'ST')]` . 
1. Şemayı güncelleştirmek için "Tümünü Değiştir" seçeneğine tıklayın. 
1. Şemayı kaydedin. 
1. Yukarıdaki işlem, tüm JSONPath ifadelerini aşağıdaki gibi güncelleştirir: 
   * Eski JSONPath: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * Yeni JSONPath: `$.employmentNav.results[?(@.assignmentClass == 'ST')].jobInfoNav.results[0].departmentNav.name_localized`
1. Uygulamanın öznitelik eşleme dikey penceresini yeniden yükleyin. 
1. Aşağı kaydırın ve **Gelişmiş seçenekleri göster**' e tıklayın.
1. **Başarılı etmenler için öznitelik listesini düzenle**' ye tıklayın.
1. Genel atama verilerini getirmek için yeni öznitelikler ekleyin. Örneğin: bir genel atama profiliyle ilişkili departman adını getirmek istiyorsanız, *globalAssignmentDepartment* özniteliğini jsonpath ifadesi olarak ayarlanmış olarak ekleyebilirsiniz `$.employmentNav.results[?(@.assignmentClass == 'GA')].jobInfoNav.results[0].departmentNav.name_localized` . 
1. Artık her iki departman değerini Active Directory özniteliklerine akabilir ya da ifade eşlemesini kullanarak bir değeri seçmeli olarak akışla aktarabilirsiniz. Örnek: aşağıdaki ifade, varsa AD *departmanı* özniteliğinin değerini *globalAssignmentDepartment* olarak ayarlar, aksi durumda değeri standart atamayla ilişkili *departmana* ayarlar. 
   * `IIF(IsPresent([globalAssignmentDepartment]),[globalAssignmentDepartment],[department])`

1. Eşlemeyi kaydedin. 
1. Sağlamayı yeniden başlatın. 

### <a name="handling-concurrent-jobs-scenario"></a>Eşzamanlı işleri işleme senaryosu

Çalışan merkezi 'ndeki bir kullanıcının eşzamanlı/birden çok işi olduğunda, *Atamasınıfı* "St" olarak ayarlanmış Iki *Empistihdam* ve *Kullanıcı* varlığı vardır. Her iki işi de ait öznitelikleri getirmek için aşağıda listelenen adımları kullanın: 

1. Başarılı etken sağlama uygulamanızın öznitelik eşleme dikey penceresini açın. 
1. Aşağı kaydırın ve **Gelişmiş seçenekleri göster**' e tıklayın.
1. **Başarılı etmenler için öznitelik listesini düzenle**' ye tıklayın.
1. İş 1 ve iş 2 ile ilişkili departmanı çekmek istediğinizi varsayalım. Önceden tanımlanmış öznitelik *departmanı* , ilk iş için departmanın değerini zaten getirir. *Secondjobdepartment* adlı yeni bir öznitelik tanımlayabilir ve jsonpath ifadesini şu şekilde ayarlayabilirsiniz`$.employmentNav.results[1].jobInfoNav.results[0].departmentNav.name_localized`
1. Artık her iki departman değerini Active Directory özniteliklerine akabilir ya da ifade eşlemesini kullanarak bir değeri seçmeli olarak akışla aktarabilirsiniz. 
1. Eşlemeyi kaydedin. 
1. Sağlamayı yeniden başlatın. 

## <a name="writeback-scenarios"></a>Geri yazma senaryoları

Bu bölümde, farklı geri yazma senaryoları ele alınmaktadır. E-posta ve telefon numarasının başarılı bir şekilde nasıl ayarlanacağını temel alarak yapılandırma yaklaşımları önerir.

### <a name="supported-scenarios-for-phone-and-email-write-back"></a>Telefon ve e-posta geri yazma için desteklenen senaryolar 

| \# | Senaryo gereksinimi | Birincil e-posta <br> bayrak değeri | İş telefonu <br> birincil bayrak değeri | Cep telefonu <br> birincil bayrak değeri | İş telefonu <br> eşleme | Cep telefonu <br> eşleme |
|--|--|--|--|--|--|--|
| 1 | * İş e-postasını yalnızca birincil olarak ayarlayın. <br> * Telefon numaralarını ayarlama. | true | true | yanlış | \[Ayarlanmadı\] | \[Ayarlanmadı\] | 
| 2 | * Başarılı etmenler, iş e-postası ve iş telefonu birincil <br> * Her zaman Azure AD telefon numarasını iş telefonuna ve cep telefonlarına cep telefonuna Flow. | true | true | yanlış | telephoneNumber | mobil | 
| 3 | * Başarılı etkenlere, iş e-postasına ve cep telefonunun birincil <br> * Her zaman Azure AD telefon numarasını iş telefonuna ve cep telefonlarına cep telefonuna Flow | true | yanlış | true |  telephoneNumber | mobil | 
| 4 | * Başarılı bir şekilde iş e-postası birincil <br> * Azure AD 'de iş telefonu numarasının mevcut olup olmadığını denetleyin, varsa, cep telefonu numarasını yalnızca cep telefonu numarası yoksa birincil olarak işaretleyin. | true | İfade eşlemeyi kullan: `IIF(IsPresent([telephoneNumber]), IIF(IsPresent([mobile]),"false", "true"), "false")` | İfade eşlemeyi kullan: `IIF(IsPresent([mobile]),"false", "true")` | telephoneNumber | mobil | 
| 5 | * Başarılı bir şekilde iş e-postası ve iş telefonu, birincil. <br> * Azure AD 'de mobil kullanılabiliyorsa iş telefonu olarak ayarlayın, aksi takdirde telephoneNumber kullanın. | true | true | yanlış | `IIF(IsPresent([mobile]), [mobile], [telephoneNumber])` | \[Ayarlanmadı\] | 

* Sonradan yazma özniteliği eşlemesinde telefon numarası için bir eşleme yoksa, yalnızca e-posta geri yazmaya dahil edilir.
* Çalışan merkezi 'nde yeni işe alma sırasında iş e-postası ve telefon numarası kullanılamıyor olabilir. Ekleme sırasında iş e-postasını ve iş telefonunu birincil olarak ayarlarsanız, yeni işe alma işlemi sırasında iş telefonu ve e-posta için bir kukla değer ayarlayabilirsiniz. Bu, sonunda geri yazma uygulaması tarafından güncelleştirilecektir.
 
### <a name="unsupported-scenarios-for-phone-and-email-write-back"></a>Telefon ve e-posta geri yazma için desteklenmeyen senaryolar

* Çalışan merkezi 'nde, kişisel e-posta ve kişisel telefon ekleme sırasında birincil olarak ayarlanır. Geri yazma uygulaması bu ayarı geçip iş e-postasını ve iş telefonunu birincil olarak ayarlayabilir.
* Çalışan merkezi 'nde iş telefonu birincil olarak ayarlanır. Geri yazma uygulaması bu ayarı değiştiremez ve hücre telefonunu birincil olarak ayarlayabilir.
* Geri yazma uygulaması geçerli birincil bayrak ayarlarını okuyamıyor ve yazma işlemi için aynı değerleri kullanamıyor. Öznitelik eşlemesinde yapılandırılan bayrak değerleri her zaman kullanılacaktır. 

## <a name="next-steps"></a>Sonraki adımlar

* [Sağlamayı Active Directory için başarılı faktörleri yapılandırmayı öğrenin](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [Geri yazmayı başarılı etkenlere nasıl yapılandıracağınızı öğrenin](../saas-apps/sap-successfactors-writeback-tutorial.md)
* [Gelen sağlama için desteklenen başarılı etmenler öznitelikleri hakkında daha fazla bilgi edinin](sap-successfactors-attribute-reference.md)











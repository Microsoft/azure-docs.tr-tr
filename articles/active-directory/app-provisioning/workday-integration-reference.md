---
title: Azure Active Directory ve Workday tümleştirme başvurusu
description: İş Workday 'e yönelik teknik kapsamlı bakış-HR odaklı sağlama
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 02/09/2021
ms.author: chmutali
ms.openlocfilehash: 2b1a43ee6b13d32c0eaed92538cf9c25405e061b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100104340"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-workday"></a>Azure Active Directory sağlama Workday ile nasıl tümleştirilir

[Azure Active Directory Kullanıcı sağlama hizmeti](../app-provisioning/user-provisioning.md) , kullanıcıların kimlik yaşam döngüsünü yönetmek için [Workday HCM](https://www.workday.com) ile tümleşir. Azure Active Directory önceden oluşturulmuş üç tümleştirme sunar: 

* [Şirket içi Active Directory Kullanıcı sağlama için Workday](../saas-apps/workday-inbound-tutorial.md)
* [Kullanıcı sağlamayı Azure Active Directory için Workday](../saas-apps/workday-inbound-cloud-only-tutorial.md)
* [Workday Geri Yazma](../saas-apps/workday-writeback-tutorial.md)

Bu makalede tümleştirmenin nasıl çalıştığı ve farklı ık senaryolar için sağlama davranışının nasıl özelleştirileceği açıklanmaktadır. 

## <a name="establishing-connectivity"></a>Bağlantı kuruluyor 

### <a name="restricting-workday-api-access-to-azure-ad-endpoints"></a>Azure AD uç noktalarına Workday API erişimini kısıtlama
Azure AD sağlama hizmeti, Workday Web Hizmetleri API uç noktalarına bağlanmak için temel kimlik doğrulamasını kullanır.  

Azure AD sağlama hizmeti ile Workday arasındaki bağlantıyı daha da güvenli hale getirmek için, belirtilen tümleştirme sistemi kullanıcısının yalnızca izin verilen Azure AD IP aralıklarından Workday API 'Lerine erişmesi için erişimi kısıtlayabilirsiniz. Workday kiracınızda aşağıdaki yapılandırmayı gerçekleştirmek için lütfen Workday yöneticinize danışın. 

1. Azure genel bulutu için [en son IP aralıklarını](https://www.microsoft.com/download/details.aspx?id=56519) indirin. 
1. Dosyayı açın ve etiket **AzureActiveDirectory** arayın 

   >[!div class="mx-imgBorder"] 
   >![Azure AD IP aralığı](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. Bu öğe *Adresspredüzeltmeleriyle* LISTELENEN tüm IP adresi aralıklarını KOPYALAYıN ve IP adresi listenizi derlemek için aralığı kullanın.
1. Workday Yönetim Portalı ' nda oturum açın. 
1. Azure veri merkezleri için yeni bir IP aralığı oluşturmak üzere **IP aralıklarını koru** görevine erişin. IP aralıklarını (CıDR gösterimini kullanarak) virgülle ayrılmış bir liste olarak belirtin.  
1. Yeni bir kimlik doğrulama ilkesi oluşturmak için **kimlik doğrulama Ilkelerini Yönet** görevine erişin. Kimlik doğrulama ilkesinde, Azure AD IP aralığını ve bu IP aralığından erişime izin verilecek güvenlik grubunu belirtmek için kimlik doğrulaması izin verilenler listesini kullanın. Değişiklikleri kaydedin. 
1. Değişiklikleri onaylamak için **tüm bekleyen kimlik doğrulama Ilkesi değişikliklerini etkinleştir** görevine erişin.

### <a name="limiting-access-to-worker-data-in-workday-using-constrained-security-groups"></a>Kısıtlanmış güvenlik grupları kullanarak Workday 'de çalışan verilerine erişimi sınırlama

[Workday tümleştirme sistem kullanıcısını yapılandırmak](../saas-apps/workday-inbound-tutorial.md#configure-integration-system-user-in-workday) için varsayılan adımlar, Workday kiracınızdaki tüm kullanıcıları alma erişimi verir. Belirli tümleştirme senaryolarında, erişimi kısıtlamak isteyebilirsiniz. böylece, yalnızca belirli bir süper kuruluşa ait olan kullanıcılar Get_Workers API çağrısı tarafından döndürülür ve Workday Azure AD Bağlayıcısı tarafından işlenir. 

Bu gereksinimi, Workday yöneticinizle birlikte çalışarak ve kısıtlanmış tümleştirme sistem güvenlik gruplarını yapılandırarak sağlayabilirsiniz. Bunun nasıl yapılacağı hakkında daha fazla bilgi için lütfen [Bu Iş günü topluluk makalesine](https://community.workday.com/forums/customer-questions/620393) başvurun (*Bu makaleye erişmek için Workday Community oturum açma kimlik bilgileri gereklidir*)

Kısıtlanmış ıSG (Tümleştirme sistemi güvenlik grupları) kullanarak erişimi sınırlayan bu strateji, aşağıdaki senaryolarda özellikle yararlıdır: 
* **Aşamalı dağıtım senaryosu**: büyük bir Workday kiracınız var ve Azure AD otomatik sağlama için Workday 'in aşamalı bir şekilde dağıtımını yapmayı planlıyorsunuz. Bu senaryoda, geçerli aşamanın kapsamında olmayan kullanıcıları Azure AD kapsam filtreleriyle dışlamak yerine, kısıtlanmış ıSG 'ları yalnızca kapsam içi çalışanların Azure AD tarafından görülebilmesi için yapılandırmanızı öneririz.
* **Çoklu sağlama işleri senaryosu**: her biri farklı bir iş birimini/bölümü/şirketi destekleyen, büyük bir Workday kiracısı ve bırden çok ad etki alanı vardır. Bu topolojiyi desteklemek için, her iş için belirli bir çalışan kümesi sağlamak üzere birden çok Workday 'yi Azure AD sağlama işlerine çalıştırmak istersiniz. Bu senaryoda, çalışan verileri hariç tutmak için Azure AD kapsam filtrelerini kullanmak yerine, kısıtlanmış ıSG 'yi yalnızca ilgili çalışan verilerinin Azure AD 'ye görünür olması için yapılandırmanızı öneririz. 

### <a name="workday-test-connection-query"></a>Workday test bağlantısı sorgusu

Azure AD, Workday bağlantısını test etmek için aşağıdaki *Get_Workers* Workday Web Hizmetleri isteğini gönderir. 

```XML
<!-- Test connection query tries to retrieve one record from the first page -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to the test connection event -->
<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:28:50.1491022Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:28:50.1491022Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
    <p1:Exclude_Employees>true</p1:Exclude_Employees>
    <p1:Exclude_Contingent_Workers>true</p1:Exclude_Contingent_Workers>
    <p1:Exclude_Inactive_Workers>true</p1:Exclude_Inactive_Workers>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:28:50.1491022Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:28:50.1491022Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>1</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="how-full-sync-works"></a>Tam eşitlemenin nasıl çalıştığı

Workday odaklı sağlama bağlamında **tam eşitleme** , Workday 'den tüm kimlikleri getirme ve her çalışan nesnesine hangi sağlama kurallarının uygulanacağını belirleme sürecinizi gösterir. Hazırlama işlemini ilk kez açtığınızda ve ayrıca Azure portal ya da Graph API 'Leri kullanarak *sağlamayı yeniden başlattığınızda* tam eşitleme gerçekleşir. 

Azure AD, çalışan verileri almak için aşağıdaki *Get_Workers* Workday Web Hizmetleri isteğini gönderir. Sorgu, tam eşitleme çalıştırmasına karşılık gelen zaman itibariyle tüm etkin çalışan girişleri için Workday işlem günlüğünü arar. 

```XML
<!-- Workday full sync query -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to full sync run -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Type_References>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Hire Employee</p1:ID>
        </p1:Transaction_Type_Reference>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Contract Contingent Worker</p1:ID>
        </p1:Transaction_Type_Reference>
      </p1:Transaction_Type_References>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Transaction_Log_Data>1</p1:Include_Transaction_Log_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```
*Response_Group* düğümü, Workday 'den hangi çalışan özniteliklerinin getirileceği belirlemek için kullanılır. *Response_Group* düğümündeki her bayrağın açıklaması Için lütfen Workday [Get_Workers API belgelerine](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v35.2/Get_Workers.html#Worker_Response_GroupType)başvurun. 

*Response_Group* düğümünde belirtilen belirli bayrak değerleri, Workday Azure AD sağlama uygulamasında yapılandırılan özniteliklere göre hesaplanır. Bayrak değerlerini ayarlamak için kullanılan ölçütler için *desteklenen varlıkların* bölümüne bakın. 

Yukarıdaki sorgu için Workday 'den alınan *Get_Workers* yanıtı, çalışan kayıtlarının sayısını ve sayfa sayısını içerir.

```XML
  <wd:Response_Results>
    <wd:Total_Results>509</wd:Total_Results>
    <wd:Total_Pages>17</wd:Total_Pages>
    <wd:Page_Results>30</wd:Page_Results>
    <wd:Page>1</wd:Page>
  </wd:Response_Results>
```
Sonuç kümesinin bir sonraki sayfasını almak için, sonraki *Get_Workers* sorgusu sayfa numarasını *Response_Filter* bir parametre olarak belirtir.

```XML
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Page>2</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
```
Azure AD sağlama hizmeti her sayfayı işler ve tam eşitleme sırasında tüm etkin çalışanlar arasında yinelenir. Workday 'den içeri aktarılan her çalışan girişi için:
* [XPath ifadesi](workday-attribute-reference.md) , Workday 'den öznitelik değerlerini almak için uygulanır.
* Öznitelik eşleme ve eşleştirme kuralları uygulanır ve 
* Hizmet, hedefte hangi işlemin gerçekleştirileceğini belirler (Azure AD/AD). 

İşlem tamamlandıktan sonra, tam eşitlemenin başlangıcı ile ilişkili zaman damgasını bir filigran olarak kaydeder. Bu filigran artımlı eşitleme döngüsünün başlangıç noktası olarak görev yapar. 

## <a name="how-incremental-sync-works"></a>Artımlı eşitlemenin nasıl çalıştığı

Tam eşitlemeden sonra Azure AD sağlama hizmeti, `LastExecutionTimestamp` artımlı değişiklikleri almak üzere Delta sorguları oluşturmak için bunu korur ve kullanır. Artımlı eşitleme sırasında Azure AD, Workday 'e aşağıdaki sorgu türlerini gönderir: 

* [El ile güncelleştirmeler için sorgu](#query-for-manual-updates)
* [Etkin tarihli güncelleştirmeler ve sonlandırmalar için sorgu](#query-for-effective-dated-updates-and-terminations)
* [Geleceğe yönelik sorgu](#query-for-future-dated-hires)

### <a name="query-for-manual-updates"></a>El ile güncelleştirmeler için sorgu

Aşağıdaki *Get_Workers* , son yürütme ve geçerli yürütme süresi arasında gerçekleşen el ile güncelleştirme sorguları ister. 

```xml
<!-- Workday incremental sync query for manual updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:29:16.0094202Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:49:06.290136Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-effective-dated-updates-and-terminations"></a>Etkin tarihli güncelleştirmeler ve sonlandırmalar için sorgu

Aşağıdaki *Get_Workers* , son yürütme ve geçerli yürütme süresi arasında gerçekleşen, etkin tarihli güncelleştirmeler için sorgular ister. 

```xml
<!-- Workday incremental sync query for effective-dated updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Effective_From>2021-01-19T02:29:16.0094202Z</p1:Effective_From>
        <p1:Effective_Through>2021-01-19T02:49:06.290136Z</p1:Effective_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-future-dated-hires"></a>Geleceğe yönelik sorgu

Yukarıdaki sorguların herhangi biri gelecek tarihli bir işe dönerse, gelecekteki bir yeni işe alma hakkında bilgi getirmek için aşağıdaki *Get_Workers* isteği kullanılır. Yeni işe alma 'nın *WID* özniteliği, aramayı gerçekleştirmek için kullanılır ve geçerlilik tarihi işe alma tarih ve saatine ayarlanır. 

```xml
<!-- Workday incremental sync query to get new hire data effective as on hire date/first day of work -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below hire date/first day of work -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_References>
    <p1:Worker_Reference>
      <p1:ID p1:type="WID">7bf6322f1ea101fd0b4433077f09cb04</p1:ID>
    </p1:Worker_Reference>
  </p1:Request_References>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-02-01T08:00:00+00:00</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-02-01T08:00:00+00:00</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="retrieving-worker-data-attributes"></a>Çalışan verileri öznitelikleri alınıyor

*Get_Workers* API 'si bir çalışan ile ilişkili farklı veri kümeleri döndürebilir. Sağlama şemasında yapılandırılan [XPath API ifadelerine](workday-attribute-reference.md) bağlı olarak, Azure AD sağlama hizmeti, Workday 'den hangi veri kümelerinin alınacağı belirler. Buna uygun olarak, *Response_Group* bayrakları *Get_Workers* isteğinde ayarlanır. 

Aşağıdaki tablo, belirli bir veri kümesini almak için kullanmak üzere eşleme yapılandırması hakkında rehberlik sağlar. 

| \# | Workday varlığı                       | Varsayılan olarak eklendi | Varsayılan olmayan varlıkları getirmeye yönelik eşlemede belirtmek için XPATH deseninin             |
|----|--------------------------------------|---------------------|-------------------------------------------------------------------------------|
| 1  | Kişisel veriler                        | Yes                 | WD: çalışan \_ verileri/WD: kişisel \_ veriler                                             |
| 2  | Çalışma verileri                      | Yes                 | WD: çalışan \_ verileri/WD: iş \_ verileri                                           |
| 3  | Ek Iş verileri                  | Yes                 | WD: Worker \_ Data/WD: iş \_ verileri/WD: çalışan \_ iş \_ verisi \[ @wd:Primary \_ işi = 0\]|
| 4  | Kuruluş verileri                    | Yes                 | WD: Worker \_ Data/WD: kuruluş \_ verileri                                         |
| 5  | Yönetim zinciri verileri                | Yes                 | WD: çalışan \_ verileri/WD: Yönetim \_ zinciri \_ verileri                                    |
| 6  | Gözevizör kuruluşu             | Yes                 | DENETIM                                                                 |
| 7  | Şirket                              | Yes                 | ŞIRKETLERIN                                                                     |
| 8  | Business Unit (İş Birimi)                        | No                  | ' Iş \_ Birimi '                                                              |
| 9  | İş birimi hiyerarşisi              | No                  | ' Iş \_ birimi \_ hiyerarşisi '                                                   |
| 10 | Şirket hiyerarşisi                    | No                  | ' ŞIRKET \_ hiyerarşisi '                                                          |
| 11 | Maliyet Merkezi                          | Hayır                  | ' MALIYET \_ Merkezi '                                                                |
| 12 | Maliyet merkezi hiyerarşisi                | No                  | ' MALIYET \_ Merkezi \_ hiyerarşisi '                                                     |
| 13 | Katılın                                 | No                  | KATıLıN                                                                        |
| 14 | Fon hiyerarşisi                       | No                  | ' fon \_ hiyerarşisi '                                                             |
| 15 | Hediyesi                                 | No                  | HEDIYESI                                                                        |
| 16 | Hediye hiyerarşisi                       | No                  | ' hedıye \_ hiyerarşisi '                                                             |
| 17 | İzin verme                                | No                  | SEMANTIĞI                                                                       |
| 18 | Hiyerarşiye izin ver                      | No                  | ' \_ HIYERARŞI ver '                                                            |
| 19 | İş sitesi hiyerarşisi              | No                  | ' Iş \_ sitesi \_ hiyerarşisi '                                                   |
| 20 | Matris organizasyonu                  | No                  | MATRISIN                                                                      |
| 21 | Ödeme grubu                            | No                  | ' ödeme \_ grubu '                                                                  |
| 22 | Programlar                             | No                  | PROGRAMLARıNıZ                                                                    |
| 23 | Program hiyerarşisi                    | No                  | ' PROGRAM \_ hiyerarşisi '                                                          |
| 24 | Region                               | No                  | ' bölge \_ hiyerarşisi '                                                           |
| 25 | Konum hiyerarşisi                   | No                  | ' konum \_ hiyerarşisi '                                                         |
| 26 | Hesap sağlama verileri            | No                  | WD: Worker \_ Data/WD: hesap \_ sağlama \_ verileri                                |
| 27 | Arka planda denetim verileri                | No                  | WD: Worker \_ Data/WD: arka planda \_ Denetim \_ verileri                                    |
| 28 | Avantaj uygunluk verileri             | No                  | WD: çalışan \_ verileri/WD: avantaj \_ uygunluk \_ verileri                                 |
| 29 | Avantaj kayıt verileri              | No                  | WD: çalışan \_ verileri/WD: avantaj \_ kayıt \_ verileri                                  |
| 30 | Kariyer verileri                          | No                  | WD: çalışan \_ verileri/WD: kariyer \_ verileri                                               |
| 31 | Dengeleme verileri                    | No                  | WD: Worker \_ Data/WD: Dengeleme \_ verileri                                         |
| 32 | Çalışanların çalışan vergi yetkilisi verileri | No                  | WD: çalışan \_ verileri/WD: Conteningent \_ çalışan \_ vergi \_ yetkilisi \_ form \_ türü \_ verileri       |
| 33 | Geliştirme öğesi verileri                | No                  | WD: Worker \_ Data/WD: geliştirme \_ öğesi \_ verileri                                    |
| 34 | Çalışan sözleşmeleri verileri              | No                  | WD: çalışan \_ verileri/WD: çalışan \_ sözleşmeleri \_ verileri                                  |
| 35 | Çalışan gözden geçirme verileri                 | No                  | WD: çalışan \_ verileri/WD: çalışan \_ Gözden geçirme \_ verileri                                     |
| 36 | Geri bildirim alınan veriler               | No                  | WD: çalışan \_ verileri/WD: geri bildirim \_ alınan \_ veriler                                   |
| 37 | Çalışan hedefi verileri                     | No                  | WD: çalışan \_ verileri/WD: çalışan \_ hedefi \_ verileri                                         |
| 38 | Fotoğraf verileri                           | No                  | WD: çalışan \_ verileri/WD: fotoğraf \_ verileri                                                |
| 39 | Verileri nitelendirme                   | No                  | WD: Worker \_ Data/WD: nitelik \_ verileri                                        |
| 40 | İlgili kişi verileri                 | No                  | WD: Worker \_ Data/WD: ilgili \_ Kişiler \_ verileri                                     |
| 41 | Rol verileri                            | No                  | WD: Worker \_ Data/WD: rol \_ verileri                                                 |
| 42 | Yetenek verileri                           | No                  | WD: Worker \_ Data/WD: beceri \_ verileri                                                |
| 43 | Art arda profil verileri              | No                  | WD: Worker \_ Data/WD: Art arda \_ profil \_ verileri                                  |
| 44 | Taödünme değerlendirmesi verileri               | No                  | WD: çalışan \_ verileri/WD: Taödünme \_ değerlendirmesi \_ verileri                                   |
| 45 | Kullanıcı hesabı verileri                    | No                  | WD: çalışan \_ verileri/WD: Kullanıcı \_ hesabı \_ verileri                                        |
| 46 | Çalışan belge verileri                 | No                  | WD: Worker \_ Data/WD: çalışan \_ Belge \_ verileri                                     |

>[!NOTE]
>Tabloda listelenen her Workday varlığı, Workday içindeki bir **etki alanı güvenlik ilkesi** tarafından korunur. Doğru XPATH 'i ayarladıktan sonra varlıkla ilişkili herhangi bir özniteliği alamadıysanız, uygun etki alanı güvenlik ilkesinin, sağlama uygulamasıyla ilişkili tümleştirme sistemi kullanıcısı için yapılandırıldığından emin olmak üzere Workday yöneticinizle görüşün. Örneğin, *beceri verileri* almak Için, Workday etki alanı *çalışan verileri: yetenekler ve deneyim* üzerinde erişim *elde* edin. 

İşte, belirli gereksinimleri karşılamak için Workday tümleştirmesini nasıl genişletebileceğinizi gösteren bazı örnekler aşağıda verilmiştir. 

**Örnek 1**

Workday 'den aşağıdaki veri kümelerini almak ve bunları sağlama kurallarınızın içinde kullanmak istediğinizi varsayalım:

* Maliyet merkezi
* Maliyet merkezi hiyerarşisi
* Ödeme grubu

Yukarıdaki veri kümeleri varsayılan olarak dahil edilmez. Bu veri kümelerini almak için:
1. Azure portal oturum açın ve iş Workday 'nizi AD/Azure AD Kullanıcı sağlama uygulamasına açın. 
1. Sağlama dikey penceresinde eşlemeleri düzenleyin ve Gelişmiş bölümünde Workday öznitelik listesini açın. 
1. Aşağıdaki öznitelik tanımlarını ekleyin ve "gerekli" olarak işaretleyin. Bu öznitelikler AD veya Azure AD 'de herhangi bir özniteliğe eşlenmeyecektir. Bu kişiler yalnızca, uygun maliyet merkezini, maliyet merkezi hiyerarşisini ve ödeme grubu bilgilerini almak için bağlayıcıya bir sinyal işlevi görür. 

     > [!div class="mx-tdCol2BreakAll"]
     >| Öznitelik adı | XPATH API ifadesi |
     >|---|---|
     >| CostCenterHierarchyFlag  |  WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: ID [ @wd:type = ' Organization_Type_ID '] = ' COST_CENTER_HIERARCHY ']/wd:Organization_Reference/@wd:Descriptor |
     >| Costcenterbayrağı  |  WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: ID [ @wd:type = ' Organization_Type_ID '] = ' COST_CENTER ']/WD: Organization_Data/WD: Organization_Code/Text () |
     >| PayGroupFlag bayrağı  |  WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: ID [ @wd:type = ' Organization_Type_ID '] = ' PAY_GROUP ']/WD: Organization_Data/WD: Organization_Reference_ID/Text () |

1. Maliyet merkezi ve ödeme grubu veri kümesi *Get_Workers* yanıtında kullanılabilir olduğunda, maliyet merkezi adı, maliyet merkezi kodu ve ödeme grubunu almak IÇIN aşağıdaki XPath değerlerini kullanabilirsiniz. 

     > [!div class="mx-tdCol2BreakAll"]
     >| Öznitelik adı | XPATH API ifadesi |
     >|---|---|
     >| CostCenterName  | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = ' maliyet merkezi ']/WD: Organization_Name/Text () |
     >| CostCenterCode | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = ' maliyet merkezi ']/WD: Organization_Code/Text () |
     >| PayGroup | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = ' ödeme grubu ']/WD: Organization_Name/Text () |

**Örnek 2**

Bir kullanıcıyla ilişkili sertifikaları almak istediğinizi varsayalım. Bu bilgiler, *nitelik veri* kümesinin bir parçası olarak kullanılabilir. *Get_Workers* yanıtının bir parçası olarak bu veri kümesini almak IÇIN aşağıdaki XPath 'i kullanın: 

`wd:Worker/wd:Worker_Data/wd:Qualification_Data/wd:Certification/wd:Certification_Data/wd:Issuer/text()`

**Örnek 3**

Bir çalışana atanan *sağlama gruplarını* almak istediğinizi varsayalım. Bu bilgiler, *hesap sağlama veri* kümesinin bir parçası olarak kullanılabilir. *Get_Workers* yanıtının bir parçası olarak bu veri kümesini almak IÇIN aşağıdaki XPath 'i kullanın: 

`wd:Worker/wd:Worker_Data/wd:Account_Provisioning_Data/wd:Provisioning_Group_Assignment_Data[wd:Status='Assigned']/wd:Provisioning_Group/text()`

## <a name="handling-different-hr-scenarios"></a>Farklı ık senaryolarını işleme

### <a name="retrieving-international-job-assignments-and-secondary-job-details"></a>Uluslararası iş atamaları ve ikincil iş ayrıntıları alınıyor

Varsayılan olarak, Workday Bağlayıcısı çalışanın birincil işiyle ilişkili öznitelikleri alır. Bağlayıcı ayrıca uluslararası iş atamaları veya ikincil işlerle ilişkili *ek Iş verilerinin* alınmasını da destekler. 

Uluslararası iş atamalarıyla ilişkili öznitelikleri almak için aşağıdaki adımları kullanın: 

1. Workday bağlantı URL 'sini ayarlama Workday Web hizmeti API 'SI sürüm 30,0 veya üstünü kullanır. İş günü sağlama uygulamanızda [doğru XPath değerlerini](workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30) buna göre ayarlayın. 
1. `@wd:Primary_Job=0` `Worker_Job_Data` Doğru özniteliği almak için düğümdeki seçiciyi kullanın. 
   * **Örnek 1:** XPATH 'i `SecondaryBusinessTitle` kullanmak için `wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Worker_Job_Data[@wd:Primary_Job=0]/wd:Position_Data/wd:Business_Title/text()`
   * **Örnek 2:** XPATH 'i `SecondaryBusinessLocation` kullanmak için `wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Worker_Job_Data[@wd:Primary_Job=0]/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Location_Reference/@wd:Descriptor`

 

## <a name="next-steps"></a>Sonraki adımlar

* [İş Workday 'i Active Directory sağlamayı nasıl yapılandıracağınızı öğrenin](../saas-apps/workday-inbound-tutorial.md)
* [Workday 'e geri yazma yapılandırma hakkında bilgi edinin](../saas-apps/workday-writeback-tutorial.md)
* [Gelen sağlama için desteklenen Workday öznitelikleri hakkında daha fazla bilgi edinin](workday-attribute-reference.md)


---
title: Azure Data Lake Storage 1. Azure Stream Analytics kimlik doğrulama
description: Bu makalede, Azure Data Lake Storage 1. çıkışı için Azure Stream Analytics işinizin kimliğini doğrulamak üzere yönetilen kimliklerin nasıl kullanılacağı açıklanır.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: 01741ea56b9e6f55c1393e88fc7991d410c33119
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934988"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>Yönetilen kimlikleri kullanarak Azure Data Lake Storage 1. Stream Analytics kimlik doğrulama

Azure Stream Analytics, Azure Data Lake Storage (ADLS) Gen1 çıkışıyla yönetilen kimlik kimlik doğrulamasını destekler. Belirli bir Stream Analytics işini temsil eden bu kimlik, Azure Active Directory’de kayıtlı olan bir yönetilen uygulamadır ve hedeflenen bir kaynağın kimliğini doğrulamak için kullanılabilir. Yönetilen kimlikler, parola değişiklikleri nedeniyle yeniden kimlik doğrulaması yapma gerekliliği veya her 90 günde bir kullanıcı belirtecinin süresi dolması gibi kullanıcı temelli kimlik doğrulaması yöntemlerinin sınırlamalarını ortadan kaldırır. Ayrıca, Yönetilen kimlikler Azure Data Lake Storage 1. çıkış yapan Stream Analytics iş dağıtımlarının otomatikleştirilmesine yardımcı olur.

Bu makalede, Visual Studio için Azure portal, Azure Resource Manager şablon dağıtımı ve Azure Stream Analytics araçları aracılığıyla bir Azure Data Lake Storage 1. çıkış yapan bir Azure Stream Analytics işi için yönetilen kimliği etkinleştirmek için üç yol gösterilmektedir.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Azure portalı

1. Yeni bir Stream Analytics işi oluşturarak veya mevcut bir işi Azure portal açarak başlatın. Ekranın sol tarafındaki menü çubuğundan **Yapılandır**' ın altında bulunan **yönetilen kimlik** ' i seçin.

   ![Stream Analytics yönetilen kimliği yapılandırma](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Sağ tarafta görüntülenen pencereden **sistem tarafından atanan yönetilen kimliği kullan** ' ı seçin. Azure Active Directory Stream Analytics işinin kimliği için hizmet sorumlusuna **Kaydet** ' e tıklayın. Yeni oluşturulan kimliğin yaşam döngüsü Azure tarafından yönetilecektir. Stream Analytics işi silindiğinde, ilişkili kimlik (hizmet sorumlusu) Azure tarafından otomatik olarak silinir.

   Yapılandırma kaydedildiğinde, hizmet sorumlusunun nesne KIMLIĞI (OID) aşağıda gösterildiği gibi asıl KIMLIK olarak listelenir:

   ![Stream Analytics hizmet sorumlusu KIMLIĞI](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   Hizmet sorumlusu Stream Analytics işle aynı ada sahiptir. Örneğin, işinizin adı **MyASAJob**ise oluşturulan hizmet sorumlusu adı da **MyASAJob**olur.

3. ADLS 1. çıkış havuzunun çıkış özellikleri penceresinde, kimlik doğrulama modu açılan listesine tıklayın ve * * yönetilen kimlik * * öğesini seçin.

4. Kalan özellikleri doldurun. Bir ADLS çıkışı oluşturma hakkında daha fazla bilgi edinmek için bkz. [Stream Analytics Ile Data Lake Store çıkışı oluşturma](../data-lake-store/data-lake-store-stream-analytics.md). İşiniz bittiğinde **Kaydet**' e tıklayın.

   ![Azure Data Lake Storage Yapılandır](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. ADLS 1. genel bakış sayfasına gidin ve **Veri Gezgini**' ne tıklayın.

   ![Data Lake Storage genel bakış 'ı yapılandırın](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. Veri Gezgini bölmesinde, **erişim** ' i seçin ve erişim bölmesinde **Ekle** ' ye tıklayın.

   ![Data Lake Storage erişimini yapılandırma](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. **Kullanıcı veya Grup Seç** bölmesindeki metin kutusunda, hizmet sorumlusunun adını yazın. Hizmet sorumlusu adının de karşılık gelen Stream Analytics işinin adı olduğunu unutmayın. Asıl adı yazmaya başladığınızda metin kutusunun altında görünür. İstenen hizmet asıl adını seçip **Seç**' e tıklayın.

   ![Hizmet sorumlusu adı seçin](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. **İzinler** bölmesinde, **yazma** ve **yürütme** izinlerini denetleyin ve **Bu klasöre ve tüm alt öğelere**atayın. Ardından **Tamam**' a tıklayın.

   ![Yazma ve yürütme izinlerini seçin](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. Hizmet sorumlusu aşağıda gösterildiği gibi **erişim** bölmesinde **atanan izinler** altında listelenir. Şimdi geri dönüp Stream Analytics işinizi başlatabilirsiniz.

   ![Portalda erişim listesi Stream Analytics](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Data Lake Storage 1. dosya sistemi izinleri hakkında daha fazla bilgi edinmek için bkz. [Azure Data Lake Storage 1. Access Control](../data-lake-store/data-lake-store-access-control.md).

## <a name="stream-analytics-tools-for-visual-studio"></a>Visual Studio için Stream Analytics araçları

1. JobConfig. json dosyasında, **sistem tarafından atanan kimliği kullan** seçeneğini **true**olarak ayarlayın.

   ![Stream Analytics iş yapılandırması tarafından yönetilen kimlikler](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. ADLS 1. çıkış havuzunun çıkış özellikleri penceresinde, kimlik doğrulama modu açılan listesine tıklayın ve * * yönetilen kimlik * * öğesini seçin.

   ![ADLS çıkış Yönetilen kimlikler](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Özellikleri geri kalanı doldurun ve **Kaydet**' e tıklayın.

4. Sorgu Düzenleyicisi 'nde **Azure 'A Gönder ' e** tıklayın.

   İşi gönderdiğinizde araçlar iki şey yapar:

   * , Azure Active Directory Stream Analytics işin kimliği için otomatik olarak bir hizmet sorumlusu oluşturur. Yeni oluşturulan kimliğin yaşam döngüsü Azure tarafından yönetilecektir. Stream Analytics işi silindiğinde, ilişkili kimlik (hizmet sorumlusu) Azure tarafından otomatik olarak silinir.

   * İşte kullanılan ADLS 1. önek yolu için **yazma** ve **yürütme** izinlerini otomatik olarak ayarlayın ve bu klasöre ve tüm alt öğelere atayın.

5. Stream Analytics CI kullanarak aşağıdaki özelliği içeren Kaynak Yöneticisi şablonları oluşturabilirsiniz [. ](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/)Bir derleme makinesinde (Visual Studio dışında) CD NuGet paketi sürüm 1.5.0 veya üzeri. Hizmet sorumlusunu almak ve PowerShell aracılığıyla hizmet sorumlusuna erişim sağlamak için sonraki bölümde Kaynak Yöneticisi şablonu dağıtım adımlarını izleyin.

## <a name="resource-manager-template-deployment"></a>Kaynak Yöneticisi şablonu dağıtımı

1. Kaynak Yöneticisi şablonunuzun kaynak bölümüne aşağıdaki özelliği ekleyerek, yönetilen bir kimlikle bir *Microsoft. StreamAnalytics/streamingjobs* kaynağı oluşturabilirsiniz:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Bu özellik, Azure Resource Manager Azure Stream Analytics işiniz için kimlik oluşturup yönetmeyeceğini söyler.

   **Örnek iş**
   
   ```json
   {
     "Name": "AsaJobWithIdentity",
     "Type": "Microsoft.StreamAnalytics/streamingjobs",
     "Location": "West US",
     "Identity": {
       "Type": "SystemAssigned",
     },
     "properties": {
       "sku": {
         "name": "standard"
       },
       "outputs": [
         {
           "name": "string",
           "properties":{
             "datasource": {
               "type": "Microsoft.DataLake/Accounts",
               "properties": {
                 "accountName": "myDataLakeAccountName",
                 "filePathPrefix": "cluster1/logs/{date}/{time}",
                 "dateFormat": "YYYY/MM/DD",
                 "timeFormat": "HH",
                 "authenticationMode": "Msi"
             }
           }
         }
       }
     }
   }
   ```
  
   **Örnek iş yanıtı**

   ```json
   {
    "Name": "mySAJob",
    "Type": "Microsoft.StreamAnalytics/streamingjobs",
    "Location": "West US",
    "Identity": {
      "Type": "SystemAssigned",
        "principalId": "GUID",
        "tenantId": "GUID",
      },
      "properties": {
        "sku": {
          "name": "standard"
        },
     }
   }
   ```

   Gerekli ADLS kaynaklarına erişim izni vermek için iş yanıtından sorumlu KIMLIĞI ' ni bir yere göz atın.

   **KIRACı kimliği** , hizmet sorumlusunun oluşturulduğu Azure Active Directory kiracının kimliğidir. Hizmet sorumlusu, abonelik tarafından güvenilen Azure kiracısında oluşturulur.

   **Türü** , yönetilen kimlik türleri bölümünde açıklandığı gibi yönetilen kimliğin türünü gösterir. Yalnızca sistem tarafından atanan tür desteklenir.

2. PowerShell kullanarak hizmet sorumlusuna erişim sağlama. PowerShell aracılığıyla hizmet sorumlusuna erişim vermek için aşağıdaki komutu yürütün:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   **PrincipalId** , hizmet SORUMLUSUNUN nesne kimliğidir ve hizmet sorumlusu oluşturulduktan sonra Portal ekranında listelenir. İşi Kaynak Yöneticisi Şablon dağıtımı kullanarak oluşturduysanız, nesne KIMLIĞI iş yanıtının Identity özelliğinde listelenir.

   **Örnek**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Yukarıdaki PowerShell komutu hakkında daha fazla bilgi edinmek için [set-AzDataLakeStoreItemAclEntry](/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry) belgelerine bakın.

## <a name="limitations"></a>Sınırlamalar
Bu özellik şunları desteklemez:

1. **Çok kiracılı erişim**: belirli bir Stream Analytics işi Için oluşturulan hizmet sorumlusu, işin oluşturulduğu Azure Active Directory kiracısında bulunur ve farklı bir Azure Active Directory bulunan bir kaynağa karşı kullanılamaz Kiracı. Bu nedenle, MSI 'yi yalnızca Azure Stream Analytics işiniz ile aynı Azure Active Directory kiracısındaki ADLS Gen 1 kaynakları üzerinde kullanabilirsiniz. 

2. **[Kullanıcı tarafından atanan kimlik](../active-directory/managed-identities-azure-resources/overview.md)** : desteklenmiyor. Bu, kullanıcının Stream Analytics işleri tarafından kullanılmak üzere kendi hizmet sorumlusunu giremediği anlamına gelir. Hizmet sorumlusu Azure Stream Analytics tarafından oluşturulur.

## <a name="next-steps"></a>Sonraki adımlar

* [Stream Analytics ile Data Lake Store çıkışı oluşturma](../data-lake-store/data-lake-store-stream-analytics.md)
* [Visual Studio ile yerel olarak Stream Analytics sorguları test etme](stream-analytics-vs-tools-local-run.md)
* [Visual Studio için Azure Stream Analytics araçlarını kullanarak canlı verileri yerel olarak test etme](stream-analytics-live-data-local-testing.md) 

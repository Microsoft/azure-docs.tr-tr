---
title: Azure Analysis Services istemci kitaplıkları | Microsoft Docs
description: İstemci uygulamaları ve araçları 'na bağlanmak için gereken istemci kitaplıklarını açıklar Azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: ad7165b1205921ad324525b9f46a22bec5cebaa9
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885309"
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Azure Analysis Services bağlantı için istemci kitaplıkları

İstemci kitaplıkları, Analysis Services sunucularına bağlanmak için istemci uygulamalar ve araçlar için gereklidir. Visual Studio için Power BI Desktop, Excel, SQL Server Management Studio (SSMS) ve Analysis Services projeleri uzantısı gibi Microsoft istemci uygulamaları, her üç istemci kitaplığı da yükler ve düzenli uygulama güncelleştirmeleriyle birlikte günceller. Bazı durumlarda, istemci kitaplıklarının daha yeni sürümlerini yüklemeniz gerekebilir. Özel istemci uygulamaları da istemci kitaplıklarının yüklü olmasını gerektirir.

## <a name="download-the-latest-client-libraries-windows-installer"></a>En son istemci kitaplıklarını indir (Windows Installer)  

|İndir  |Ürün sürümü  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.1.10.25    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |     15.1.10.25       |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   18.2.3.0    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    18.2.3.0     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO ve ADOMD (NuGet paketleri)

Analysis Services Management Objects (AMO) ve ADOMD istemci kitaplıkları, [NuGet.org](https://www.nuget.org/)adresinden yüklenebilen paketler olarak sunulmaktadır. Windows Installer kullanmak yerine NuGet başvurularına geçiş yapmanız önerilir. 

|Paket  | Ürün sürümü  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    18.2.3     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   18.2.3      |

NuGet Paket derlemeleri AssemblyVersion anlam sürümü oluşturma: büyük. Bazı. Düzeltmesi. NuGet başvuruları GAC 'de farklı bir sürüm olsa bile beklenen sürümü yükler (MSI yüklemesi sonucu olur). Her sürüm için düzeltme eki artırılır. AMO ve ADOMD sürümleri eşitlenmiş olarak tutulur.

## <a name="understanding-client-libraries"></a>İstemci kitaplıklarını anlama

Analysis Services, veri sağlayıcıları olarak da bilinen üç istemci kitaplığı kullanır. ADOMD.NET ve Analysis Services Management Objects (AMO), yönetilen istemci kitaplıklarıdır. Analysis Services OLE DB Sağlayıcısı (MSOLAP DLL) yerel bir istemci kitaplığı. Genellikle, üçü de aynı anda yüklenir. **Azure Analysis Services, üç kitaplık için en son sürümleri gerektirir**. 

Power BI Desktop ve Excel gibi Microsoft istemci uygulamaları, her üç istemci kitaplığı da yükler ve yeni sürümler kullanılabilir olduğunda bunları güncelleştirir. Güncelleştirmelerin sürümüne veya sıklığına bağlı olarak, bazı istemci kitaplıkları Azure Analysis Services için gereken en son sürümler olmayabilir. Aynı durum AsCmd, TOM, ADOMD.NET gibi özel uygulamalar ve diğer arabirimler için de geçerlidir. Bu uygulamalar, kitaplıkları el ile veya program aracılığıyla yüklemeyi gerektirir. El ile yükleme için istemci kitaplıkları, dağıtılabilir paketler olarak SQL Server Özellik paketlerine dahil edilmiştir. Ancak, bu istemci kitaplıkları SQL Server sürüme bağlanır ve en son sürümü olmayabilir.  

İstemci bağlantıları için istemci kitaplıkları, bir Azure Analysis Services sunucusundan bir veri kaynağına bağlanmak için gereken veri sağlayıcılarından farklıdır. Veri kaynağı bağlantıları hakkında daha fazla bilgi için bkz. [veri kaynağı bağlantıları](analysis-services-datasource.md).

## <a name="client-library-types"></a>İstemci kitaplığı türleri

### <a name="analysis-services-ole-db-provider-msolap"></a>Analysis Services OLE DB Sağlayıcısı (MSOLAP) 

 Analysis Services OLE DB Sağlayıcısı (MSOLAP), Analysis Services veritabanı bağlantıları için yerel istemci kitaplığıdır. Bu, hem ADOMD.NET hem de AMO tarafından dolaylı olarak kullanılır, bu da veri sağlayıcısına bağlantı istekleri temsilcisi olarak yapılır. OLE DB sağlayıcıyı doğrudan uygulama kodundan da çağırabilirsiniz.  
  
 Analysis Services OLE DB Sağlayıcısı, Analysis Services veritabanlarına erişmek için kullanılan çoğu araç ve istemci uygulaması tarafından otomatik olarak yüklenir. Analysis Services verilerine erişmek için kullanılan bilgisayarlarda yüklü olmalıdır.  
  
 OLE DB sağlayıcılar genellikle bağlantı dizelerinde belirtilir. Analysis Services bağlantı dizesi, OLE DB sağlayıcısına başvurmak için farklı bir terminolojisi kullanır: MSOLAP.\<sürümü >. dll.

### <a name="amo"></a>AMO  

 AMO, sunucu yönetimi ve veri tanımı için kullanılan bir yönetilen istemci kitaplığıdır. Bu, Araçlar ve istemci uygulamaları tarafından yüklenir ve kullanılır. Örneğin, SQL Server Management Studio (SSMS) Analysis Services bağlanmak için AMO kullanır. AMO kullanan bir bağlantı genellikle en az `"data source=\<servername>"`oluşur. Bir bağlantı kurulduktan sonra, veritabanı koleksiyonları ve ana nesnelerle çalışmak için API 'yi kullanırsınız. Hem Visual Studio hem de SSMS, bir Analysis Services örneğine bağlanmak için AMO kullanır.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET, Analysis Services verileri sorgulamak için kullanılan bir yönetilen veri istemci kitaplığıdır. Bu, Araçlar ve istemci uygulamaları tarafından yüklenir ve kullanılır. 
  
 Bir veritabanına bağlanırken, üç kitaplıkların hepsi için bağlantı dizesi özellikleri benzerdir. ADOMD.NET için tanımladığınız neredeyse tüm bağlantı dizeleri [Microsoft. AnalysisServices. AdomdClient. AdomdConnection. ConnectionString](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString) Ile de amo ve ANALYSIS SERVICES OLE DB sağlayıcısı (msolap) için de kullanılır. Daha fazla bilgi için bkz. [bağlantı dizesi &#40;özellikleri&#41;Analysis Services](https://docs.microsoft.com/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="bkmk_LibUpdate"></a>İstemci kitaplığı sürümünü belirleme   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  `C:\Program Files\Microsoft Analysis Services\AS OLEDB\` kısmına gidin. Birden fazla klasörünüz varsa, daha yüksek bir sayı seçin.
  
2.  **MSOLAP. dll** > **Özellikler** > **Ayrıntılar**' a sağ tıklayın. Dosya adı msolap140. dll ise, en son sürümden daha eski ve yükseltilmesi gerekir.
    
    ![İstemci kitaplığı ayrıntıları](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\` kısmına gidin. Birden fazla klasörünüz varsa, daha yüksek bir sayı seçin.
2. **Microsoft. AnalysisServices** > **Özellikler** > **Ayrıntılar**' a sağ tıklayın.  

### <a name="adomd"></a>ADOMD

1. `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\` kısmına gidin. Birden fazla klasörünüz varsa, daha yüksek bir sayı seçin.
2. **Microsoft. AnalysisServices. AdomdClient** > **Özellikler** > **Ayrıntılar**' a sağ tıklayın.  


## <a name="next-steps"></a>Sonraki adımlar
[Excel   bağlanma](analysis-services-connect-excel.md)  
[Power BI ile bağlanma](analysis-services-connect-pbi.md)

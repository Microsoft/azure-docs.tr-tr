---
title: . Odc dosyası ile Azure Analysis Services bağlanma | Microsoft Docs
description: Azure 'da bir Analysis Services sunucusuna bağlanmak ve veri almak için Office veri bağlantısı dosyası oluşturmayı öğrenin.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8fa657f3b343cdf49723dc68601bb1c9513ff504
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96487346"
---
# <a name="create-an-office-data-connection-file"></a>Office veri bağlantısı dosyası oluşturma

Bu makaledeki bilgiler, Excel 2016 sürüm numarası 16.0.7369.2117 veya daha önceki bir sürümü veya Excel 2013 ' den bir Azure Analysis Services sunucusuna bağlanmak için Office veri bağlantısı dosyası oluşturma işlemini açıklar. Güncelleştirilmiş bir [MSOLAP. 7 sağlayıcısı](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true) da gereklidir.


1. Aşağıdaki örnek bağlantı dosyasını kopyalayın ve bir metin düzenleyicisine yapıştırın. 

2. İçinde `odc:ConnectionString` , aşağıdaki özellikleri değiştirin:

    *   `Data Source=asazure://<region>.asazure.windows.net/<servername>;` `<region>` Analysis Services sunucunuzun bölgesinde ve `<servername>` sunucunuzun adıyla değiştirin.

    *   `Initial Catalog=<database>;` `<database>` Veritabanınızın adına değiştirin.

3. `<odc:CommandText>Model</odc:CommandText>` `Model` Modeliniz veya bakış açısına göre değişir. 

4. Dosyayı `.odc` C:\Users \\ *Kullanıcı adı*\ belgeleriveri kaynakları klasörüne bir uzantısı ile kaydedin.

5. Dosyaya sağ tıklayın ve ardından **Excel 'de aç**' a tıklayın. Ya da Excel 'de, **veri** şeridinde, **mevcut bağlantılar**' a tıklayın, dosyanızı seçin ve ardından **Aç**' a tıklayın.



**Örnek bağlantı dosyası**
```
<html xmlns:o="urn:schemas-microsoft-com:office:office"
xmlns="https://www.w3.org/TR/REC-html40">

<head>
<meta http-equiv=Content-Type content="text/x-ms-odc; charset=utf-8">
<meta name=ProgId content=ODC.Cube>
<meta name=SourceType content=OLEDB>
<meta name=Catalog content="Database">
<meta name=Table content=Model>
<title>AzureAnalysisServicesConnection</title>
<xml id=docprops><o:DocumentProperties
  xmlns:o="urn:schemas-microsoft-com:office:office"
  xmlns="https://www.w3.org/TR/REC-html40">
  <o:Name>SampleAzureAnalysisServices</o:Name>
 </o:DocumentProperties>
</xml><xml id=msodc><odc:OfficeDataConnection
  xmlns:odc="urn:schemas-microsoft-com:office:odc"
  xmlns="https://www.w3.org/TR/REC-html40">
  <odc:Connection odc:Type="OLEDB">
   <odc:ConnectionString>Provider=MSOLAP.7;Data Source=asazure://<region>.asazure.windows.net/<servername>;Initial Catalog=<database>;</odc:ConnectionString>
   <odc:CommandType>Cube</odc:CommandType>
   <odc:CommandText>Model</odc:CommandText>
  </odc:Connection>
 </odc:OfficeDataConnection>
</xml>
<style>
<!--
    .ODCDataSource
    {
    behavior: url(dataconn.htc);
    }
-->
</style>
 
</head>

<body onload='init()' scroll=no leftmargin=0 topmargin=0 rightmargin=0 style='border: 0px'>
<table style='border: solid 1px threedface; height: 100%; width: 100%' cellpadding=0 cellspacing=0 width='100%'> 
  <tr> 
    <td id=tdName style='font-family:arial; font-size:medium; padding: 3px; background-color: threedface'> 
      &nbsp; 
    </td> 
     <td id=tdTableDropdown style='padding: 3px; background-color: threedface; vertical-align: top; padding-bottom: 3px'>

      &nbsp; 
    </td> 
  </tr> 
  <tr> 
    <td id=tdDesc colspan='2' style='border-bottom: 1px threedshadow solid; font-family: Arial; font-size: 1pt; padding: 2px; background-color: threedface'>

      &nbsp; 
    </td> 
  </tr> 
  <tr> 
    <td colspan='2' style='height: 100%; padding-bottom: 4px; border-top: 1px threedhighlight solid;'> 
      <div id='pt' style='height: 100%' class='ODCDataSource'></div> 
    </td> 
  </tr> 
</table> 

  
<script language='javascript'> 

function init() { 
  var sName, sDescription; 
  var i, j; 
  
  try { 
    sName = unescape(location.href) 
  
    i = sName.lastIndexOf(".") 
    if (i>=0) { sName = sName.substring(1, i); } 
  
    i = sName.lastIndexOf("/") 
    if (i>=0) { sName = sName.substring(i+1, sName.length); } 

    document.title = sName; 
    document.getElementById("tdName").innerText = sName; 

    sDescription = document.getElementById("docprops").innerHTML; 
  
    i = sDescription.indexOf("escription>") 
    if (i>=0) { j = sDescription.indexOf("escription>", i + 11); } 

    if (i>=0 && j >= 0) { 
      j = sDescription.lastIndexOf("</", j); 

      if (j>=0) { 
          sDescription = sDescription.substring(i+11, j); 
        if (sDescription != "") { 
            document.getElementById("tdDesc").style.fontSize="x-small"; 
          document.getElementById("tdDesc").innerHTML = sDescription; 
          } 
        } 
      } 
    } 
  catch(e) { 

    } 
  } 
</script> 

</body> 
 
</html>

```
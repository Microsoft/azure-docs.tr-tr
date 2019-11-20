---
title: Azure Stream Analytics CI/CD NuGet paketini kullanın
description: Bu makalede bir sürekli tümleştirme ve dağıtım işlemi ayarlamak için Azure Stream Analytics CI/CD NuGet paketinin nasıl kullanılacağı açıklanır.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 6f5ba06841af580cd80b3b46bec972f717ded044
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934232"
---
# <a name="use-the-azure-stream-analytics-cicd-nuget-package-for-integration-and-development"></a>Tümleştirme ve geliştirme için Azure Stream Analytics CI/CD NuGet paketini kullanın 
Bu makalede, Azure Stream Analytics CI/CD NuGet paketinin bir sürekli tümleştirme ve dağıtım işlemi ayarlamak için nasıl kullanılacağı açıklanır.

MSBuild için destek almak üzere [Visual Studio için Stream Analytics araçları](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) ' nın 2.3.0000.0 veya üzeri sürümlerini kullanın.

Bir NuGet paketi kullanılabilir: [Microsoft. Azure. Stream Analytics. CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). [Stream Analytics Visual Studio projelerinin](stream-analytics-vs-tools.md)sürekli tümleştirme ve dağıtım sürecini destekleyen MSBuild, yerel çalıştırma ve dağıtım araçlarını sağlar. 
> [!NOTE]
> NuGet paketi yalnızca Visual Studio için Stream Analytics araçları 'nın 2.3.0000.0 veya üzeri sürümü ile kullanılabilir. Visual Studio araçlarının önceki sürümlerinde oluşturulmuş projeleriniz varsa, bunları 2.3.0000.0 veya sonraki sürümüyle açmanız ve kaydetmeniz yeterlidir. Ardından yeni yetenekler etkinleştirilir. 

Daha fazla bilgi için bkz. [Visual Studio için Stream Analytics araçları](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="msbuild"></a>MSBUILD
Standart Visual Studio MSBuild deneyimi gibi bir proje oluşturmak için iki seçeneğiniz vardır. Projeye sağ tıklayıp ardından **Oluştur**' u seçin. Ayrıca, komut satırından NuGet paketindeki **MSBuild** 'i de kullanabilirsiniz.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Bir Stream Analytics Visual Studio projesi başarıyla oluşturduğunda, **bin/[Debug/Retail]/Deploy** klasörü altında aşağıdaki iki Azure Resource Manager şablon dosyasını oluşturur: 

*  Şablon dosyası Kaynak Yöneticisi

       [ProjectName].JobTemplate.json 

*  Kaynak Yöneticisi Parameters dosyası

       [ProjectName].JobTemplate.parameters.json   

Parameters. JSON dosyasındaki varsayılan parametreler, Visual Studio projenizin ayarlarından alınır. Başka bir ortama dağıtmak istiyorsanız, parametreleri uygun şekilde değiştirin.

> [!NOTE]
> Tüm kimlik bilgileri için varsayılan değerler null olarak ayarlanır. Buluta dağıtmadan önce değerleri ayarlamanız **gerekir** .

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
[Kaynak Yöneticisi Şablon dosyası ve Azure PowerShell dağıtma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)hakkında daha fazla bilgi edinin. Bir [nesnenin kaynak yöneticisi şablonunda parametre olarak nasıl kullanılacağı](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)hakkında daha fazla bilgi edinin.

Azure Data Lake Store Gen1 için yönetilen kimliği çıkış havuzu olarak kullanmak için, Azure 'a dağıtılmadan önce PowerShell kullanarak hizmet sorumlusuna erişim sağlamanız gerekir. [Kaynak Yöneticisi şablonuyla yönetilen kimlik ile ADLS 1. dağıtma](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment)hakkında daha fazla bilgi edinin.


## <a name="command-line-tool"></a>Komut satırı aracı

### <a name="build-the-project"></a>Projeyi derleme
NuGet paketinin **sa. exe**adlı bir komut satırı aracı vardır. Sürekli tümleştirme ve sürekli teslim sürecinde kullanabileceğiniz rastgele bir makinede proje derlemesini ve yerel sınamayı destekler. 

Dağıtım dosyaları varsayılan olarak geçerli dizinin altına yerleştirilir. Aşağıdaki-OutputPath parametresini kullanarak çıkış yolunu belirtebilirsiniz:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Betiği yerel olarak test etme

Projenizde Visual Studio 'da yerel giriş dosyaları belirtilmişse, *localrun* komutunu kullanarak otomatikleştirilmiş bir betik testi çalıştırabilirsiniz. Çıkış sonucu geçerli dizinin altına yerleştirilir.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Stream Analytics PowerShell API 'SI ile kullanmak için bir iş tanımı dosyası oluşturma

*ARM* komutu, giriş olarak derleme ile oluşturulan iş şablonu ve iş şablonu parametre dosyalarını alır. Daha sonra bunları, Stream Analytics PowerShell API 'siyle birlikte kullanılabilecek bir iş tanımı JSON dosyası halinde birleştirir.

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Örnek:
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```



## <a name="next-steps"></a>Sonraki adımlar

* [Hızlı başlangıç: Visual Studio 'da Azure Stream Analytics bulut işi oluşturma](stream-analytics-quick-create-vs.md)
* [Visual Studio ile yerel olarak Stream Analytics sorguları test etme](stream-analytics-vs-tools-local-run.md)
* [Visual Studio ile Azure Stream Analytics işleri keşfet](stream-analytics-vs-tools.md)

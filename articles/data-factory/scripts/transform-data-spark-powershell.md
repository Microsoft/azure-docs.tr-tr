---
title: PowerShell kullanarak buluttaki verileri dönüştürme
description: Bu PowerShell betiği, bir Azure HDInsight Spark kümesinde Spark programını çalıştırarak buluttaki verileri dönüştürür.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/12/2017
ms.openlocfilehash: 9bdb6521cd9361e5588233ebbb50a59788df88b4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783260"
---
# <a name="powershell-script---transform-data-in-cloud-using-azure-data-factory"></a>PowerShell betiği-Azure Data Factory kullanarak buluttaki verileri dönüştürme

Bu örnek PowerShell betiği, bir Azure HDInsight Spark kümesinde Spark programını çalıştırarak buluttaki verileri dönüştüren bir işlem hattı oluşturur. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Önkoşullar
* **Azure depolama hesabı**. Bir Python betiği ve bir giriş dosyası oluşturun ve bunları Azure depolama 'ya yükleyin. Spark programının çıktısı bu depolama hesabında depolanır. İsteğe bağlı Spark kümesi, birincil depolama alanıyla aynı depolama hesabını kullanır.  

### <a name="upload-python-script-to-your-blob-storage-account"></a>Python betiğini Blob Depolama hesabınıza yükleme
1. Aşağıdaki içerikle **WordCount_Spark. Kopyala** adlı bir Python dosyası oluşturun: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. **&lt; StorageAccountName &gt;** ' i Azure depolama hesabınızın adıyla değiştirin. Ardından dosyayı kaydedin. 
3. Azure Blob depolama alanınızda henüz yoksa **adftutorial** adlı bir kapsayıcı oluşturun. 
4. **Spark** adlı bir klasör oluşturun.
5. **Spark** klasörünün altında **script** adlı bir alt klasör oluşturun. 
6. **WordCount_Spark.py** dosyasını **script** alt klasörüne yükleyin. 


### <a name="upload-the-input-file"></a>Girdi dosyasını yükleme
1. Bazı metinlerle **minecraftstory.txt** adlı bir dosya oluşturun. Spark programı bu metindeki sözcükleri sayar. 
2. `inputfiles`BLOB kapsayıcısının klasöründe adlı bir alt klasör oluşturun `spark` . 
3. `minecraftstory.txt` dosyasını `inputfiles` alt klasörüne yükleyin. 

## <a name="sample-script"></a>Örnek betik
> [!IMPORTANT]
> Bu betik, c:\ ' daki sabit sürücünüzde Data Factory varlıkları (bağlı hizmet, veri kümesi ve işlem hattı) tanımlayan JSON dosyaları oluşturur. klasörde.

[!code-powershell[main](../../../powershell_scripts/data-factory/transform-data-using-spark/transform-data-using-spark.ps1 "Transform data using Spark")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme

Örnek betiği çalıştırdıktan sonra, kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komutu kullanabilirsiniz:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Veri fabrikasını kaynak grubundan kaldırmak için aşağıdaki komutu çalıştırın: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik şu komutları kullanır:

| Komut | Notlar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Veri fabrikası oluşturma. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-Azdatafactoryv2linkedservice) | Veri fabrikasında bağlı bir hizmet oluşturur. Bağlı hizmet bir veri deposunu veya işlem verilerini bir veri fabrikasına bağlar. |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-Azdatafactoryv2pipeline) | Veri fabrikasında bir işlem hattı oluşturur. İşlem hattı, belirli bir işlemi gerçekleştiren bir veya daha fazla etkinlik içerir. Bu işlem hattında, bir Spark etkinliği Azure HDInsight Spark kümesinde bir programı çalıştırarak verileri dönüştürür. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-Azdatafactoryv2pipeline) | İşlem hattı için bir çalıştırma oluşturur. Diğer bir deyişle, işlem hattını çalıştırır. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | İşlem hattında etkinliğin (etkinlik çalıştırması) çalıştırılmasıyla ilgili ayrıntıları alır. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |
|||

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/).

Ek Azure Data Factory PowerShell betiği örnekleri [Azure Data Factory PowerShell örneklerinde](../samples-powershell.md)bulunabilir.
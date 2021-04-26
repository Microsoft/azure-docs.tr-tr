---
title: Sertifika içeriğini temele dönüştürme-64-Azure HDInsight
description: Azure HDInsight 'ta hizmet sorumlusu sertifika içeriğini Base-64 kodlu dize biçimine dönüştürme
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 0ee2e783a7a9443db4c96817cf611272667c675a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944419"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>HDInsight 'ta hizmet sorumlusu sertifika içeriğini Base-64 kodlu dize biçimine dönüştürme

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Temel olmayan bir 64 karakteri, ikiden fazla doldurma karakteri veya doldurma karakterleri arasında boşluk olmayan bir karakter içerdiğinden girişin geçerli bir Base-64 dizesi olduğunu belirten bir hata iletisi alırsınız.

## <a name="cause"></a>Nedeni

Birincil veya ek depolama alanı olarak Data Lake sahip kümeler oluşturmak için PowerShell veya Azure şablon dağıtımı kullanılırken, Data Lake depolama hesabına erişmek için belirtilen hizmet sorumlusu sertifika içerikleri Base-64 biçimindedir. PFX Sertifika içeriklerinin Base-64 kodlu dizeye hatalı dönüştürülmesi bu hataya neden olabilir.

## <a name="resolution"></a>Çözüm

Pfx biçiminde hizmet sorumlusu sertifikası aldıktan sonra (örnek hizmet sorumlusu oluşturma adımları için [buraya](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) bakın), sertifika içeriğini base-64 biçimine dönüştürmek Için aşağıdaki PowerShell komutunu veya C# kod parçacığını kullanın.

```powershell
$servicePrincipalCertificateBase64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(path-to-servicePrincipalCertificatePfxFile))
```

```csharp
using System;
using System.IO;

namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            var certContents = File.ReadAllBytes(@"<path to pfx file>");
            string certificateData = Convert.ToBase64String(certContents);
            System.Diagnostics.Debug.WriteLine(certificateData);
        }
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
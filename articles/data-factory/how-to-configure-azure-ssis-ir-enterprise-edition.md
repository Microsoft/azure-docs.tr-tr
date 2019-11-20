---
title: 'Azure-SSIS Integration Runtime için Enterprise Edition sağlama '
description: Bu makalede, Enterprise Edition 'ın Azure-SSIS Integration Runtime ve sağlama özellikleri açıklanmaktadır
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 8c9db18d416d508dd5a98cbacc14b7db6c075a55
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73673968"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime için Enterprise Edition sağlama

Azure-SSIS Integration Runtime Enterprise sürümü aşağıdaki gelişmiş ve Premium özellikleri kullanmanıza olanak sağlar:
-   Değişiklik verilerini yakalama (CDC) bileşenleri
-   Oracle, Teradata ve SAP BW bağlayıcıları
-   SQL Server Analysis Services (SSAS) ve Azure Analysis Services (AAS) bağlayıcıları ve dönüştürmeleri
-   Benzer gruplandırma ve benzer arama dönüşümleri
-   Terim ayıklama ve terim arama dönüşümleri

Bu özelliklerden bazıları, Azure-SSIS IR özelleştirmek için ek bileşenler yüklemenizi gerektirir. Ek bileşenleri yükleme hakkında daha fazla bilgi için bkz. [Azure-SSIS tümleştirme çalışma zamanı Için özel kurulum](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>Kurumsal özellikler

| **Kurumsal özellikler** | **Tanımlarını** |
|---|---|
| CDC bileşenleri | CDC kaynağı, denetim görevi ve Bölümlendirici dönüştürmesi Azure-SSIS IR Enterprise sürümüne önceden yüklenir. Oracle 'a bağlanmak için, başka bir bilgisayara CDC tasarımcısını ve hizmetini de yüklemeniz gerekir. |
| Oracle bağlayıcıları | Oracle bağlantı Yöneticisi, kaynak ve hedef Azure-SSIS IR Enterprise sürümüne önceden yüklenir. Oracle call Interface (OCı) sürücüsünü de yüklemeniz gerekir ve gerekirse Azure-SSIS IR Oracle Transport Network alt Strate (TNS) öğesini yapılandırın. Daha fazla bilgi için bkz. [Azure-SSIS tümleştirmesi çalışma zamanı için özel kurulum](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Teradata bağlayıcıları | Azure-SSIS IR Enterprise sürümünde Teradata bağlantı yöneticisini, kaynağını ve hedefini, Teradata Parallel Transporter (TPT) API 'sini ve Teradata ODBC sürücüsünü yüklemeniz gerekir. Daha fazla bilgi için bkz. [Azure-SSIS tümleştirmesi çalışma zamanı için özel kurulum](how-to-configure-azure-ssis-ir-custom-setup.md). |
| SAP BW bağlayıcıları | SAP BW bağlantı Yöneticisi, kaynak ve hedef Azure-SSIS IR Enterprise sürümüne önceden yüklenir. Ayrıca, Azure-SSIS IR SAP BW sürücüsünü de yüklemeniz gerekir. Bu bağlayıcılar SAP BW 7,0 veya önceki sürümleri destekler. SAP BW veya diğer SAP ürünlerinin sonraki sürümlerine bağlanmak için, Azure-SSIS IR üçüncü taraf ISV 'lerden SAP bağlayıcıları satın alabilir ve yükleyebilirsiniz. Ek bileşenleri yükleme hakkında daha fazla bilgi için bkz. [Azure-SSIS tümleştirme çalışma zamanı Için özel kurulum](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Analysis Services bileşenleri               | Veri araştırma modeli eğitimi hedefi, boyut Işleme hedefi ve bölüm Işleme hedefi, Ayrıca, veri araştırma sorgusu dönüştürmesi, Azure-SSIS IR Enterprise Edition 'da önceden yüklenir. Tüm bu bileşenler SQL Server Analysis Services (SSAS) destekler, ancak yalnızca bölüm Işleme hedefi Azure Analysis Services (AAS) destekler. SSAS 'ye bağlanmak için, [SSSıSDB 'de Windows kimlik doğrulama kimlik bilgilerini de yapılandırmanız](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)gerekir. Bu bileşenlere ek olarak, Analysis Services Execute DDL görevi, Analysis Services Işleme görevi ve veri araştırma sorgusu görevi Azure-SSIS IR Standard/Enterprise sürümüne de önceden yüklenir. |
| Benzer gruplandırma ve benzer arama dönüşümleri  | Benzer gruplandırma ve benzer arama dönüştürmeleri Azure-SSIS IR Enterprise sürümüne önceden yüklenir. Bu bileşenler, başvuru verilerini depolamak için hem SQL Server hem de Azure SQL veritabanı 'nı destekler. |
| Terim ayıklama ve terim arama dönüşümleri | Ayıklama ve terim arama dönüştürmeleri terimi Azure-SSIS IR Enterprise sürümüne önceden yüklenir. Bu bileşenler, başvuru verilerini depolamak için hem SQL Server hem de Azure SQL veritabanı 'nı destekler. |

## <a name="instructions"></a>Yönergeler

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1.  [Azure PowerShell](/powershell/azure/install-az-ps)indirin ve yükleyin.

2.  PowerShell ile Azure-SSIS IR sağladığınızda veya yeniden yapılandırdığınızda, Azure-SSIS IR başlamadan önce **Sürüm** parametresinin değeri olarak **Kurumsal** `Set-AzDataFactoryV2IntegrationRuntime` ' i çalıştırın. Örnek bir betik aşağıda verilmiştir:

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>Sonraki adımlar

-   [Azure-SSIS tümleştirme çalışma zamanı için özel kurulum](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Azure-SSIS tümleştirme çalışma zamanı için ücretli veya lisanslı özel bileşenler geliştirme](how-to-develop-azure-ssis-ir-licensed-components.md)

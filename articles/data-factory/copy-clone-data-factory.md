---
title: Azure Data Factory bir veri fabrikası kopyalama veya kopyalama
description: Azure Data Factory ' de bir veri fabrikasını kopyalama veya kopyalama hakkında bilgi edinin
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: bafe70655f05b5dda32f51393591f82b4b5625f1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678121"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Azure Data Factory bir veri fabrikası kopyalama veya kopyalama

Bu makalede, Azure Data Factory ' de bir veri fabrikasının nasıl kopyalanacağı veya kopyalanacağı açıklanır.

## <a name="use-cases-for-cloning-a-data-factory"></a>Veri fabrikasını kopyalamak için kullanım örnekleri

Veri fabrikasını kopyalamak veya klonlamak için yararlı bulabileceğiniz durumlardan bazıları şunlardır:

-   **Kaynakları yeniden adlandırma**. Azure, kaynakların yeniden adlandırılmasını desteklemez. Bir veri fabrikasını yeniden adlandırmak isterseniz, veri fabrikasını farklı bir adla kopyalayabilir ve ardından var olanı silebilirsiniz.

-   Hata ayıklama özellikleri yeterli olmadığında **değişiklikleri hata ayıklama** . Bazen yaptığınız değişiklikleri test etmek için, değişikliklerinizi ana birine uygulamadan önce farklı bir fabrikada test etmek isteyebilirsiniz. Çoğu senaryoda hata ayıkla ' yı kullanabilirsiniz. Ancak tetikleyicide yapılan değişiklikler; örneğin, bir tetikleyici otomatik olarak çağrıldığında veya bir zaman penceresinde, iade etmeden kolayca çalışmayabilir. Bu durumlarda, fabrikası klonlamak ve yaptığınız değişiklikleri uygulamak çok sayıda fikir sunar. Azure Data Factory öncelikle çalışma sayısına göre ücretlendirdiği için ikinci fabrika hiçbir ek ücrete neden olmaz.

## <a name="how-to-clone-a-data-factory"></a>Veri fabrikasını kopyalama

1. Azure portal Data Factory Kullanıcı arabirimi, veri fabrikanızın tüm yükünü bir Kaynak Yöneticisi şablonuna dışa aktarmanıza olanak tanır ve fabrikanızı kopyaladığınızda değiştirmek istediğiniz değerleri değiştirmenize olanak tanıyan bir parametre dosyası ile birlikte.

1. Bir önkoşul olarak, hedef veri fabrikanızı Azure portal oluşturmanız gerekir.

1. Kaynak fabrikasında SelfHosted bir tümleştirme çalışma zamanı varsa, bu adı hedef fabrikasında aynı adla oluşturmanız gerekir. Şirket içinde barındırılan IRS 'yi farklı fabrikalar arasında paylaştırmak istiyorsanız [burada](source-control.md#best-practices-for-git-integration)yayınlanan kalıbı kullanabilirsiniz.

1. GIT modundaysanız, portaldan yayımladığınızda, fabrikanızın Kaynak Yöneticisi şablonu, deponun adf_publish dalındaki GIT 'e kaydedilir.

1. Diğer senaryolar için Kaynak Yöneticisi şablonu, portalda **Kaynak Yöneticisi şablonu dışarı aktar** düğmesine tıklanarak indirilebilir.

1. Kaynak Yöneticisi şablonunu indirdikten sonra standart Kaynak Yöneticisi şablonu dağıtım yöntemleri aracılığıyla dağıtabilirsiniz.

1. Güvenlik nedenleriyle, oluşturulan Kaynak Yöneticisi şablonu bağlantılı hizmetlerin parolaları gibi gizli bilgiler içermez. Sonuç olarak, bu parolaları dağıtım parametreleri olarak sağlamanız gerekir. Parametre sağlanması istenmediğinde, Azure Key Vault bağlı hizmetlerin bağlantı dizelerini ve parolalarını edinmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Data Factory Kullanıcı arabirimini kullanarak veri fabrikası oluşturma](quickstart-create-data-factory-portal.md)içindeki Azure Portal veri fabrikası oluşturma kılavuzunu gözden geçirin.

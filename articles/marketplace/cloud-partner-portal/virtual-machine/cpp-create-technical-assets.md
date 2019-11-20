---
title: Azure Marketi için bir sanal makine teklifi için teknik varlıklar oluşturma
description: Azure Marketi 'nde bir sanal makine teklifi için teknik varlıkların nasıl oluşturulacağını açıklar.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/20/2018
ms.author: pabutler
ms.openlocfilehash: 45d0ff5b7b3fea1566b13b61bd01cc17da61e4b3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824504"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>Bir sanal makine teklifi için teknik varlıklar oluşturma

Bu bölümde, Azure Marketi için bir sanal makine (VM) teklifiyle ilgili teknik varlıkları oluşturma ve yapılandırma adımları anlatılmaktadır.  VM iki bileşen içerir: çözüm sanal sabit diski (VHD) ve isteğe bağlı olarak ilişkili veri diskleri.  

- Azure Market teklifinizle birlikte dağıtacağınız işletim sistemini ve çözümünüzü içeren *sanal sabit diskler (VHD)* . VHD 'yi hazırlama işlemi, Linux tabanlı, Windows tabanlı veya özel tabanlı bir VM olmasına bağlı olarak farklılık gösterir.
- *Veri diskleri* , bir sanal makine için adanmış ve kalıcı depolamayı temsil eder. Kalıcı bilgileri depolamak için çözüm VHD 'yi (örneğin, `C:` sürücüsü *) kullanmayın.*

Bir VM görüntüsü, bir işletim sistemi diski ve sıfır veya daha fazla veri diski içerir. Disk başına bir VHD gereklidir. Boş veri diskleri bile, bir VHD 'nin oluşturulmasını gerektirir.
VM işletim sistemini, VM boyutunu, açılacak bağlantı noktalarını ve en fazla 15 bağlı veri diski yapılandırmanız gerekir.

> [!TIP] 
> Kullandığınız işletim sisteminden bağımsız olarak yalnızca SKU için gereken en az sayıda veri diski ekleyin. Müşteriler, dağıtım sırasında bir görüntünün parçası olan diskleri kaldıramaz, ancak dağıtım sırasında veya sonrasında her zaman disk ekleyebilirler. 

> [!IMPORTANT]
> *Yeni bir görüntü sürümündeki disk sayısını değiştirmeyin.* Görüntüdeki veri disklerini yeniden yapılandırmanız gerekiyorsa, yeni bir SKU tanımlayın. Farklı disk sayımlarıyla yeni bir görüntü sürümünün yayımlanması, otomatik ölçeklendirme, çözümlerin Azure Resource Manager şablonları ve diğer senaryolar aracılığıyla otomatik olarak dağıtılması gibi yeni görüntü sürümüne bağlı olarak yeni dağıtım olasılığını ortadan kaldırılacaktır.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>Temel teknik bilgi

Bu varlıkların tasarlanmasına, oluşturulmasına ve test edilmesine zaman alır ve hem Azure platformu hem de teklifi oluşturmak için kullanılan teknolojiler hakkında teknik bilgi gerektirir. Çözüm etki alanınız ' ne ek olarak, mühendislik ekibiniz aşağıdaki Microsoft teknolojileri hakkında bilgi sahibi olmalıdır: 
-   [Azure hizmetlerini](https://azure.microsoft.com/services/) temel olarak anlama 
-   [Azure uygulamalarını tasarlama ve mimarın](https://azure.microsoft.com/solutions/architecture/)
-   [Azure sanal makineler](https://azure.microsoft.com/services/virtual-machines/), [Azure depolama](https://azure.microsoft.com/services/?filter=storage) ve [Azure ağı](https://azure.microsoft.com/services/?filter=networking) ile çalışma hakkında bilgi
-   [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) çalışma bilgileri
-   [JSON](https://www.json.org/) hakkında çalışma bilgisi


## <a name="suggested-tools"></a>Önerilen araçlar 

VHD 'Lerin ve VM 'Lerin yönetilmesine yardımcı olmak için aşağıdaki komut dosyası ortamlarının birini veya her ikisini birden seçin:
-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

Ayrıca, aşağıdaki araçların geliştirme ortamınıza eklenmesini öneririz: 

-   [Azure Depolama Gezgini](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Uzantı: [Azure Resource Manager araçları](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Uzantı: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Uzantı: [prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Ayrıca, [Azure Geliştirici Araçları](https://azure.microsoft.com/tools/) sayfasındaki kullanılabilir araçların incelenmesi önerilir ve Visual Studio kullanıyorsanız, [Visual Studio Market](https://marketplace.visualstudio.com/).


## <a name="next-steps"></a>Sonraki adımlar

Bu bölümdeki sonraki makaleler, bu VM varlıklarını oluşturma ve kaydetme adımlarında size yol gösterir:

1. [Azure ile uyumlu bir sanal sabit disk oluşturma](./cpp-create-vhd.md) , Azure ile uyumlu bir Linux veya WINDOWS tabanlı VHD oluşturmayı açıklar.  Bu, boyutlandırma, düzeltme eki uygulama ve VM 'yi karşıya yükleme için hazırlama gibi en iyi uygulamaları içerir.

2. [Sanal makineye bağlanma,](./cpp-connect-vm.md) yenı oluşturulan sanal makinenize uzaktan bağlanmayı ve oturum açmayı açıklar.  Bu makalede ayrıca, kullanım maliyetlerinde kaydedilecek VM 'nin nasıl durdurulacağı açıklanmaktadır.

3. [Sanal makineyi yapılandırma](./cpp-configure-vm.md) doğru VHD boyutunu seçme, görüntünüzü Genelleştirme, en son güncelleştirmeleri uygulama (düzeltme ekleri) ve özel yapılandırma zamanlama hakkında bilgiler sağlar.

4. Sanal bir [sabit diskten bir sanal makine dağıtmak](./cpp-deploy-vm-vhd.md) , bir VM 'nin Azure tarafından DAĞıTıLAN bir VHD 'den nasıl kaydedileceği açıklanmaktadır.  Gerekli araçları ve bunların bir Kullanıcı VM görüntüsü oluşturmak için nasıl kullanılacağını ve [Microsoft Azure Portal](https://ms.portal.azure.com/) ya da PowerShell betiklerini kullanarak Azure 'a dağıtmayı listeler. 

5. [Bir sanal makine görüntüsünü onaylama](./cpp-certify-vm.md) Azure Market sertifikası IÇIN bir VM görüntüsünü test etme ve gönderme hakkında bilgiler sağlar. *Azure Sertifikalı aracı Için sertifika test aracı* 'nı nereden ALACAĞıNıZ ve VM görüntünüzü onaylamak için bu aracın nasıl kullanılacağı açıklanmaktadır. 

6. [SAS URI 'Si alma](./cpp-get-sas-uri.md) , sanal makine görüntülerinizin paylaşılan erişim IMZASıNı (SAS) URI 'sini almayı açıklar.
 
Destekleyici bir makale olarak, [ortak paylaşılan erişim imzası URL 'si sorunları](./cpp-common-sas-url-issues.md) , SAS URI 'lerini ve ilgili olası çözümleri kullanarak karşılaşabileceğiniz bazı yaygın sorunları listeler.

Tüm bu adımları tamamladıktan sonra, [sanal makine teklifinizi](./cpp-publish-offer.md) Azure Marketi 'ne yayımlamaya hazırlarsınız.

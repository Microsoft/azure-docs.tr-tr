---
title: Azure Güvenlik Merkezi 'ne Azure Stack sanal makineler ekleme
description: Bu hızlı başlangıçta Azure Izleyici, güncelleştirme ve yapılandırma yönetimi sanal makine uzantısının Azure Stack sanal makinelerde nasıl sağlanacağı gösterilmektedir.
services: security-center
documentationcenter: na
author: pipposera
manager: dsavage
ms.assetid: 8982348a-0624-40c7-8a1e-642a523c7f6b
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: fiseraci
ms.openlocfilehash: c186dcb2d7d7d423f5c001bdb4d3f3503beebd9c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686517"
---
# <a name="quickstart-onboard-your-azure-stack-virtual-machines-to-security-center"></a>Hızlı başlangıç: Azure Stack sanal makinelerinizi Güvenlik Merkezi 'ne ekleme
Azure aboneliğinizi ekledikten sonra, Azure Stack **Azure izleyici, güncelleştirme ve yapılandırma yönetimi** sanal makine uzantısını ekleyerek Azure Stack çalıştıran sanal makinelerinizi korumak Için Güvenlik Merkezi 'ni etkinleştirebilirsiniz. 'nde.

Bu hızlı başlangıçta, **Azure izleyici, güncelleştirme ve yapılandırma yönetimi** sanal makinesi uzantısının bir sanal makinede (Linux ve Windows 'un her ikisi de desteklenir) Azure Stack nasıl ekleneceğini gösterir.

## <a name="prerequisites"></a>Ön koşullar
Güvenlik Merkezi ile çalışmaya başlamak için Microsoft Azure aboneliğinizin olması gerekir. Bir aboneliğiniz yoksa [ücretsiz hesap](https://azure.microsoft.com/pricing/free-trial/) için kaydolabilirsiniz.

Bu hızlı başlangıcı başlatmadan önce güvenlik merkezi 'nin standart katmanında bir Azure aboneliğinizin olması gerekir. Yükseltme yönergeleri için bkz. [Azure aboneliğinizi Güvenlik Merkezi Standart katmanına ekleme](security-center-get-started.md). Güvenlik Merkezi Standart katmanını, 30 gün boyunca ücretsiz olarak deneyebilirsiniz. Daha fazla bilgi için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="select-your-workspace-in-azure-security-center"></a>Azure Güvenlik Merkezi 'nde çalışma alanınızı seçme

1. [Azure portalında](https://azure.microsoft.com/features/azure-portal/) oturum açın.
2. **Microsoft Azure** menüsünde **Güvenlik Merkezi**’ni seçin. **Güvenlik Merkezi - Genel Bakış** açılır. 

   ![Güvenlik Merkezine genel bakış][2]

3. Güvenlik Merkezi ana menüsü altında, **Başlarken**’i seçin.
4. **Başlangıç** sekmesini seçin.

   ![Başlarken][3]

5. **Yeni Azure dışı bilgisayarlar ekle** altında, **Yapılandır**’a tıklayın. Log Analytics çalışma alanlarınızın bir listesi gösterilir. Listede, varsa, otomatik sağlama etkinleştirildiğinde Güvenlik Merkezi tarafından sizin için oluşturulan varsayılan çalışma alanı bulunur. Bu çalışma alanını veya Azure Stack VM 'nin güvenlik verilerini rapor etmek istediğiniz başka bir çalışma alanını seçin.

    ![Azure dışı bilgisayar ekleme](./media/quick-onboard-windows-computer/non-azure.png)

   Aracı yapılandırma bölümünde kullanmak üzere çalışma alanı KIMLIĞINIZ için aracı ve anahtarları indirme bağlantısı ile **doğrudan aracı** dikey penceresi açılır.

   >[!NOTE]
   > Aracıyı el ile indirmeniz gerekmez. Aracı aşağıdaki adımlarda bir VM uzantısı olarak yüklenecektir.

6. **Çalışma Alanı Kimliği**’nin sağ tarafında, kopyala simgesini seçin ve kimliği Not Defteri’ne yapıştırın.

7. **Birincil Anahtar**’ın sağ tarafında, kopyala simgesini seçin ve anahtarı Not Defteri’ne yapıştırın.

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>Sanal makine uzantısını mevcut Azure Stack sanal makinelerinize ekleyin
Artık Azure Stack çalışan sanal makinelere **Azure izleyici, güncelleştirme ve yapılandırma yönetimi** sanal makine uzantısını eklemeniz gerekir.

1. Yeni bir tarayıcı sekmesinde **Azure Stack** portalda oturum açın.
2. **Sanal makineler** sayfasına gidin, güvenlik merkezi ile korumak istediğiniz sanal makineyi seçin. Azure Stack sanal makine oluşturma hakkında daha fazla bilgi için, [Windows sanal makineler için bu hızlı başlangıç](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) veya [Linux sanal makineleri için bu hızlı başlangıç](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal)bölümüne bakın.
3. **Uzantılar**'ı seçin. Bu sanal makinede yüklü olan sanal makine uzantılarının listesi gösteriliyor.
4. **Ekle** sekmesine tıklayın. **Yeni kaynak** menüsü dikey penceresi açılır ve kullanılabilir sanal makine uzantılarının listesini gösterir. 
5. **Azure izleyici, güncelleştirme ve yapılandırma yönetimi** uzantısını seçin ve **Oluştur**' a tıklayın. **Uzantı** yapılandırma yapılandırma dikey penceresi açılır.

>[!NOTE]
> Market 'te listelenen **Azure izleyici, güncelleştirme ve yapılandırma yönetimi** uzantısını görmüyorsanız, kullanılabilir hale getirmek için lütfen Azure Stack işletmenine ulaşın.

6. **Uzantı** yapılandırma yapılandırma dikey penceresinde, önceki yordamda not defteri ' ne kopyaladığınız **çalışma alanı kimliğini** ve **çalışma alanı anahtarını (birincil anahtar)** yapıştırın.
7. Gerekli yapılandırma ayarlarını sağlamayı tamamladığınızda **Tamam**' a tıklayın.
8. Uzantı yüklemesi tamamlandıktan sonra, durumu **sağlama başarılı**olarak görünür. Sanal makinenin güvenlik merkezi portalında görünmesi bir saate kadar sürebilir.

Windows için aracı yükleme ve yapılandırma hakkında daha fazla bilgi için bkz. [Windows bilgisayarlarını bağlama](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Aracı sorunlarını gidermek için bkz. [Azure Log Analytics Linux Aracısı sorunlarını giderme](../azure-monitor/platform/agent-linux-troubleshoot.md).

Artık Azure VM’lerinizi ve Azure dışı bilgisayarlarınızı tek bir yerde izleyebilirsiniz. Azure 'daki Güvenlik Merkezi portalında, **işlem**altında tüm VM 'lere ve bilgisayarlara yönelik önerilerle ilgili bir genel bakış sunulmaktadır. Güvenlik Merkezi ayrıca güvenlik uyarılarında bu bilgisayarlar için herhangi bir algılamayı yüzeyler.

  ![Bilgi İşlem dikey penceresi][6]

**Bilgi İşlem** dikey penceresinde gösterilen iki türlü simge vardır:

![simge1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Azure dışı bilgisayar 

![simge2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Azure VM (Azure Stack VM 'Ler bu grupta görünür)

## <a name="clean-up-resources"></a>Kaynakları temizleme
Artık gerekli değilse, Azure Stack portalı aracılığıyla sanal makineden uzantıyı kaldırabilirsiniz.

Uzantıyı kaldırmak için:

1. **Azure Stack portalını**açın.
2. **Sanal makineler** sayfasına gidin, uzantıyı kaldırmak istediğiniz sanal makineyi seçin.
3. **Uzantılar**' ı seçin, **Microsoft. Enterprisecloud. Monitoring**uzantısını seçin.
4. **Kaldır**' a tıklayın ve **Evet**' e tıklayarak seçiminizi onaylayın.

## <a name="next-steps"></a>Sonraki adımlar
Bu hızlı başlangıçta, Azure Stack çalıştıran bir sanal makinede Azure Izleyici, güncelleştirme ve yapılandırma yönetimi uzantısını sağladınız. Güvenlik Merkezi'ni kullanma hakkında daha fazla bilgi için bir güvenlik ilkesi yapılandırma ve kaynaklarınızın güvenliğini değerlendirme ile ilgili öğreticiye geçin.

> [!div class="nextstepaction"]
> [Öğretici: Güvenlik ilkelerini tanımlama ve değerlendirme](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png

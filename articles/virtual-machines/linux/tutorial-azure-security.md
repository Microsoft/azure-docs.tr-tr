---
title: Öğretici-Azure 'da Linux sanal makineleri için Azure Güvenlik Merkezi 'Ni kullanma
description: Bu öğreticide, Azure’da Linux sanal makinelerinizi korumaya ve güvenliğini sağlamaya yardımcı olmak için Azure Güvenlik Merkezi özelliklerini öğreneceksiniz.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/11/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4046aac5e8362bf315eca11d9cd96a6e862e2c55
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034598"
---
# <a name="tutorial-use-azure-security-center-to-monitor-linux-virtual-machines"></a>Öğretici: Linux sanal makinelerini izlemek için Azure Güvenlik Merkezi kullanma

Azure Güvenlik Merkezi, Azure kaynak güvenliği uygulamalarınıza yönelik görünürlük elde etmenize yardımcı olabilir. Güvenlik Merkezi, tümleşik güvenlik izleme işlevi sunar. Gözden kaçabilecek tehditleri algılayabilir. Bu öğreticide, Azure Güvenlik Merkezi hakkında bilgi edinecek ve aşağıdakilerin nasıl yapılacağını öğreneceksiniz:
 
> [!div class="checklist"]
> * Veri toplamayı ayarlama
> * Güvenlik ilkeleri ayarlama
> * Yapılandırma durumu sorunlarını görüntüleme ve düzeltme
> * Algılanan tehditleri gözden geçirme

## <a name="security-center-overview"></a>Güvenlik Merkezine genel bakış

Güvenlik Merkezi, olası sanal makine (VM) yapılandırma sorunlarını ve hedeflenmiş güvenlik tehditlerini algılar. Bunlar arasında, ağ güvenlik grupları olmayan, şifrelenmemiş diskler ve deneme yanılma Uzak Masaüstü Protokolü (RDP) saldırıları içeren sanal makineler yer alır. Güvenlik Merkezi panosunda bilgiler kolay okunabilen graflarda gösterilir.

Güvenlik Merkezi panosuna erişmek için Azure portalındaki menüde **Güvenlik Merkezi**’ni seçin. Panoda, Azure ortamınızın güvenlik durumunu görebilir, geçerli öneri sayısını bulabilir ve tehdit uyarılarının geçerli durumunu görüntüleyebilirsiniz. Daha fazla ayrıntı görmek için her bir üst düzey grafiği genişletebilirsiniz.

![Güvenlik Merkezi panosu](./media/tutorial-azure-security/asc-dash.png)

Güvenlik Merkezi, algıladığı sorunlara yönelik öneriler sağlamak için veri keşfinin ötesinde işlev sunar. Örneğin, bir sanal makine, ağ güvenliği grubu eklenmemiş şekilde dağıtıldıysa Güvenlik Merkezi, uygulayabileceğiniz düzeltme adımlarıyla birlikte bir öneri görüntüler. Güvenlik Merkezi bağlamından çıkmadan otomatik düzeltme elde edersiniz.  

![Öneriler](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>Veri toplamayı ayarlama

Sanal makine güvenlik yapılandırmalarına yönelik görünürlük elde edebilmeniz için önce Güvenlik Merkezi veri toplama ayarlamanız gerekir. Microsoft Monitoring Agent'ı aboneliğinizdeki tüm VM'lere otomatik olarak yükleyen veri toplamayı açmayı içerir.

1. Güvenlik Merkezi panosunda **Güvenlik ilkesi**’ne tıklayın ve sonra aboneliğinizi seçin. 
2. **Veri toplama** için, **Otomatik Sağlama**'da **Açık**'ı seçin.
3. **Varsayılan çalışma alanı yapılandırmasını** **Güvenlik Merkezi’nin oluşturduğu çalışma alanlarını kullan (varsayılan)** olarak bırakın.
4. **Güvenlik Olayları**'nın altında varsayılan **Ortak** seçeneğini koruyun.
4. Sayfanın üst kısmından **Kaydet**'e tıklayın. 

Güvenlik Merkezi veri toplama aracısı tüm sanal makinelere yüklenir ve veri toplama başlar. 

## <a name="set-up-a-security-policy"></a>Güvenlik ilkesi ayarlama

Güvenlik ilkeleri, Güvenlik Merkezi’nin kendisi için veriler topladığı ve önerilerde bulunduğu öğeleri tanımlamak için kullanılır. Farklı Azure kaynaklarına farklı güvenlik ilkeleri uygulayabilirsiniz. Varsayılan olarak Azure kaynakları tüm ilke öğelerine karşı değerlendirilse de, tüm Azure kaynakları için veya bir kaynak grubu için tek tek ilke öğelerini kapatabilirsiniz. Güvenlik Merkezi güvenlik ilkeleri hakkında ayrıntılı bilgi için bkz. [Azure Güvenlik Merkezi’nde güvenlik ilkelerini ayarlama](../../security-center/security-center-policies.md). 

Aboneliğin tümüne bir güvenlik ilkesi oluşturmak için:

1. Güvenlik Merkezi panosunda **Güvenlik ilkesini**, ardından aboneliğinizi seçin.
2. **Güvenlik ilkesi** dikey penceresinde **Güvenlik ilkesini** seçin. 
3. **Güvenlik ilkesi-güvenlik ilkesi** dikey penceresinde, aboneliğe uygulamak istediğiniz ilke öğelerini açın veya kapatın.
4. Ayarlarınızı seçmeyi tamamladığınızda dikey pencerenin en üstünden **Kaydet**'i seçin. 

![Benzersiz ilke](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>Sanal makine yapılandırma durumunu görüntüleme

Veri toplamayı açıp bir güvenlik ilkesi ayarlamanızın ardından Güvenlik Merkezi, uyarılar ve öneriler sağlamaya başlar. Sanal makineler dağıtılırken veri toplama aracısı yüklenir. Güvenlik Merkezi, yeni sanal makineler için verilerle doldurulur. Sanal makine yapılandırma durumu hakkında ayrıntılı bilgi için bkz. [Güvenlik Merkezinizdeki sanal makinelerinizi koruma](../../security-center/security-center-virtual-machine-protection.md). 

Veriler toplanırken, her bir sanal makine ve ilgili Azure kaynağı için kaynak durumu toplanır. Bilgiler, kolay okunur bir grafikte gösterilir. 

Kaynak durumunu görüntülemek için:

1.  Güvenlik Merkezi panosunda, **Önleme**'nin altından **İşlem**'i seçin. 
2.  **İşlem** dikey penceresinde **VM'ler ve bilgisayarlar**'ı seçin. Bu görünüm, tüm sanal makinelerinizin yapılandırma durumunun özetini sağlar.

![İşlem durumu](./media/tutorial-azure-security/compute-health.png)

Bir sanal makineye yönelik tüm önerileri görmek için sanal makineyi seçin. 

## <a name="remediate-configuration-issues"></a>Yapılandırma sorunlarını düzeltme

Güvenlik Merkezi, yapılandırma verileriyle doldurma işlemine başladıktan sonra, ayarladığınız güvenlik ilkesine göre önerilerde bulunulur. Örneğin, ilişkili ağ güvenlik grubu olmadan bir sanal makine ayarlandıysa, bir tane oluşturulması için öneride bulunulur. 

Tüm önerilerin listesini göstermek için: 

1. Güvenlik Merkezi panosunda **Öneriler**’i seçin.
2. Belirli bir öneri seçin. Önerinin geçerli olduğu tüm kaynakların listesi görüntülenir.
3. Bir öneriyi uygulamak için kaynağı seçin. 
4. Düzeltme adımları için yönergeleri izleyin. 

Çoğu durumda Güvenlik Merkezi, Güvenlik Merkezi’nden çıkmadan bir öneriyi ele almak için uygulayabileceğiniz adımları sağlar. Aşağıdaki örnekte Güvenlik Merkezi, sınırsız gelen kuralı olan bir ağ güvenlik grubunu algılar. Öneri sayfasında **Gelen kurallarını düzenle** düğmesini seçebilirsiniz. Kuralı değiştirmek için gerekli kullanıcı arabirimi görüntülenir. 

![Öneriler](./media/tutorial-azure-security/remediation.png)

Öneriler düzeltildikçe çözümlendi olarak işaretlenir. 

## <a name="view-detected-threats"></a>Algılanan tehditleri görüntüleme

Güvenlik Merkezi, kaynak yapılandırma önerilerine ek olarak tehdit algılama uyarıları görüntüler. Güvenlik uyarıları özelliği, Azure kaynaklarına karşı güvenlik tehditlerini algılamak için her bir sanal makineden, Azure ağ bağlantısı günlükleri ve bağlantılı iş ortağı çözümlerinden toplanan verileri bir araya getirir. Güvenlik Merkezi tehdit algılama özellikleri hakkında ayrıntılı bilgi için bkz. [Güvenlik Merkezi tehditleri nasıl algılar?](../../security-center/security-center-alerts-overview.md#detect-threats).

Güvenlik uyarıları özelliği, Güvenlik Merkezi fiyatlandırma katmanının *Ücretsiz* katmanından *Standart* katmanına yükseltilmesini gerektirir. Bu daha yüksek fiyatlandırma katmanına geçtiğinizde **ücretsiz deneme sürümü** kullanılabilir. 

Fiyatlandırma katmanını değiştirmek için:  

1. Güvenlik Merkezi panosunda **Güvenlik ilkesi**’ne tıklayın ve sonra aboneliğinizi seçin.
2. **Fiyatlandırma katmanı**'nı seçin.
3. **Standart**’ı seçin ve ardından dikey pencerenin üstündeki **Kaydet**’i tıklatın.


Fiyatlandırma katmanını değiştirmenizin ardından, güvenlik tehditleri algılandıkça güvenlik uyarıları grafı doldurulmaya başlar.

![Güvenlik uyarıları](./media/tutorial-azure-security/security-alerts.png)

Bilgileri görüntülemek için bir uyarı seçin. Örneğin, tehdidin bir açıklamasını, algılama süresini, tüm tehdit girişimlerini ve önerilen düzeltmeyi görebilirsiniz. Aşağıdaki örnekte, 294 başarısız RDP girişimiyle birlikte bir RDP deneme yanılma saldırısı algılandı. Önerilen bir çözüm sağlanır.

![RDP saldırısı](./media/tutorial-azure-security/rdp-attack.png)

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, Azure Güvenlik Merkezi’ni ayarladınız ve sonra Güvenlik Merkezi'ndeki sanal makineleri gözden geçirdiniz. Şunları öğrendiniz:

> [!div class="checklist"]
> * Veri toplamayı ayarlama
> * Güvenlik ilkeleri ayarlama
> * Yapılandırma durumu sorunlarını görüntüleme ve düzeltme
> * Algılanan tehditleri gözden geçirme

Jenkins, GitHub ve Docker ile bir CI/CD işlem hattı oluşturma hakkında daha fazla bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Jenkins, GitHub ve Docker ile CI/CD altyapısı oluşturma](tutorial-jenkins-github-docker-cicd.md)


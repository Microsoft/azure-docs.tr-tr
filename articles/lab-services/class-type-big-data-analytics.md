---
title: Azure Lab Services kullanarak büyük veri analizlerini öğretmek için laboratuvar ayarlama | Microsoft Docs
description: Hortonçalışmalar veri platformunun (HDP) Docker dağıtımını kullanarak büyük veri analizlerini öğretmek için laboratuvar ayarlamayı öğrenin.
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 5eb9cd00350c41645d4427e30a6f25a6c163358c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94659905"
---
# <a name="set-up-a-lab-for-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>Hortonçalışmalar veri platformunun Docker dağıtımını kullanarak büyük veri analizi için laboratuvar ayarlama

Bu makalede, büyük bir veri analizi sınıfı öğretmek için bir laboratuvarın nasıl ayarlanacağı gösterilmektedir.  Bu tür bir sınıfta, öğrenciler büyük hacimlere yönelik verileri nasıl işleyeceğinizi ve veri öngörüleri türetmede makine ve istatistiksel öğrenme algoritmaları uygulamayı öğreniyor.  Öğrenciler için önemli bir amaç, büyük verileri depolamak, yönetmek ve işlemek için araçlar sağlayan [Apache Hadoop açık kaynaklı yazılım paketi](https://hadoop.apache.org/) gibi veri analizi araçlarını kullanmayı öğrenmektir.

Bu laboratuvarda, öğrenciler, [Hortonçalışmalar veri platformu (HDP)](https://www.cloudera.com/products/hdp.html)olarak adlandırılan [Cloudera](https://www.cloudera.com/)tarafından verilen popüler bir Hadoop sürümü kullanacaktır.  Özel olarak, öğrenciler ücretsiz ve öğrenme ve deneme için tasarlanan, platformun Basitleştirilmiş, kullanımı kolay bir sürümü olan [HDP Sandbox 3.0.1](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html) kullanır.  Bu sınıf, HDP korumalı alanı dağıtılan Windows veya Linux sanal makinelerini (VM) kullanabilir, ancak bu makalede Windows 'un nasıl kullanılacağı gösterilir.

Bu laboratuvarın başka bir ilginç yönü de, [Docker](https://www.docker.com/) kapsayıcıları kullanılarak laboratuvar VM 'lerinde HDP korumalı alanı dağıtacağız.  Her Docker kapsayıcısı, yazılım uygulamalarının içinde çalışması için kendi yalıtılmış ortamını sağlar.  Kavramsal olarak Docker kapsayıcıları, iç içe geçmiş VM 'Ler gibidir ve [Docker Hub 'ında](https://www.docker.com/products/docker-hub)sunulan kapsayıcı görüntülerine göre çok çeşitli yazılım uygulamalarını kolayca dağıtmak ve çalıştırmak için kullanılabilir.  HDP korumalı alan için Cloudera 'nın dağıtım betiği, Docker Hub 'ından [HDP Sandbox 3.0.1 Docker görüntüsünü](https://hub.docker.com/r/hortonworks/sandbox-hdp) otomatik olarak çeker ve Iki Docker kapsayıcısı çalıştırır:
  - korumalı alan-HDP
  - korumalı alan-ara sunucu

## <a name="lab-configuration"></a>Laboratuvar yapılandırması

Bu Laboratuvarı ayarlamak için, başlamak üzere bir Azure aboneliğine ve laboratuvar hesabına sahip olmanız gerekir. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun. Bir Azure aboneliği aldıktan sonra, Azure Lab Services yeni bir laboratuvar hesabı oluşturabilirsiniz. Yeni laboratuvar hesabı oluşturma hakkında daha fazla bilgi için bkz. [Laboratuvar hesabı kurmak Için öğretici](tutorial-setup-lab-account.md).  Ayrıca var olan bir laboratuvar hesabı da kullanabilirsiniz.

### <a name="lab-account-settings"></a>Laboratuvar hesabı ayarları

Laboratuvar hesabı için aşağıdaki tabloda açıklanan ayarları etkinleştirin. Market görüntülerinin nasıl etkinleştirileceği hakkında daha fazla bilgi için bkz. [Laboratuvar oluşturucuları için kullanılabilen Market görüntülerini belirtme](./specify-marketplace-images.md).

| Laboratuvar hesabı ayarı | Yönergeler |
| ------------------- | ------------ |
|Market görüntüsü| Laboratuvar hesabınızda kullanılmak üzere Windows 10 Pro görüntüsünü etkinleştirin.|

### <a name="lab-settings"></a>Laboratuvar ayarları

Bir sınıf Laboratuvarı ayarlarken aşağıdaki tablodaki ayarları kullanın.  Sınıf Laboratuvarı oluşturma hakkında daha fazla bilgi için bkz. [bir derslik Laboratuvarı ayarlama öğreticisi](tutorial-setup-classroom-lab.md).

| Laboratuvar ayarları | Değer/yönergeler |
| ------------ | ------------------ |
|Sanal makine boyutu| Orta (Iç Içe sanallaştırma). Bu VM boyutu, ilişkisel veritabanları, bellek içi önbelleğe alma ve analiz için idealdir.  Bu boyut, iç içe sanallaştırmayı da destekler.|  
|Sanal makine görüntüsü| Windows 10 Pro|

> [!NOTE] 
> Docker kullanarak HDP korumalı alan dağıtmak için, Orta (Iç Içe sanallaştırma) kullanmanız gerekir:
>   - Windows Hyper-V iç içe sanallaştırma
>   - En az 10 GB RAM

## <a name="template-machine-configuration"></a>Şablon makine yapılandırması

Şablon makinesini ayarlamak için şunları göndereceğiz:
- Docker'ı yükleme
- HDP korumalı alanı dağıtma
- Docker kapsayıcılarını otomatik olarak başlatmak için PowerShell ve Windows Görev Zamanlayıcı kullanma

### <a name="install-docker"></a>Docker'ı yükleme

Bu bölümdeki adımlar, [Cloudera 'Nın Docker kapsayıcılarıyla dağıtım yönergelerini](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)temel alır. 

Docker kapsayıcılarını kullanmak için, önce şablon VM 'ye Docker Desktop 'ı yüklemeniz gerekir:

1. [Docker for Windows](https://docs.docker.com/docker-for-windows/install/)yüklemek için [Önkoşullar bölümündeki](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) adımları izleyin. 

    > [!IMPORTANT] 
    > **Linux kapsayıcıları yerine Windows kapsayıcıları kullan** yapılandırma seçeneğinin işaretli olmadığından emin olun.

1. **Windows kapsayıcıları ve Hyper-V özelliklerinin** açık olduğundan emin olun.
   ![Windows özelliklerini etkinleştirme veya devre dışı bırakma](./media/class-type-big-data-analytics/windows-hyperv-features.png)

1. Docker 'ın bellek yapılandırmasını yapılandırmak için [Windows Için bellek](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows) bölümündeki adımları izleyin.

    > [!WARNING]
    > Docker 'ı yüklerken **Linux kapsayıcıları yerine Windows kapsayıcıları kullan** seçeneğini yanlışlıkla kontrol ederseniz bellek yapılandırma ayarlarını görmezsiniz.  Bunu yapmak için, [Windows Sistem tepsisindeki Docker simgesine tıklayarak](https://docs.docker.com/docker-for-windows/#docker-settings-dialog)Linux kapsayıcıları kullanmaya geçiş yapabilirsiniz; Docker masaüstü menüsü açıldığında, **Linux kapsayıcılarına geç**' i seçin.
 
### <a name="deploy-hdp-sandbox"></a>HDP korumalı alanı dağıtma

Bu bölümde, HDP korumalı alanı dağıtırsınız ve ayrıca, tarayıcıyı kullanarak HDP korumalı alana erişirsiniz.

1. Sonraki adımları tamamlamak için önerildiği için, bu, kılavuzun [Önkoşullar bölümünde](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) listelenen [Git Bash](https://gitforwindows.org/) ' i yüklediğinizden emin olun.

1. [Cloudera 'nın, Docker Için dağıtım ve kurulum kılavuzunu](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)kullanarak, aşağıdaki bölümlerdeki adımları uygulayın:
   
   -    HDP korumalı alanı dağıtma
   -    HDP korumalı alanını doğrula

    > [!WARNING] 
    > HDP için en son. zip dosyasını indirdiğinizde,. zip dosyasını boşluk içeren bir dizin *yoluna kaydetmeyin* .

    > [!NOTE] 
    > Dağıtım sırasında **sürücü paylaşılmadığını** belirten bir özel durum alırsanız, HDP 'nin Linux kapsayıcılarının yerel Windows dosyalarına erişebilmesi için C sürücünüzü Docker ile paylaşmanız gerekir.  Bunu yapmak için, [Windows Sistem tepsisindeki Docker simgesine tıklayarak](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) Docker Desktop menüsünü açın ve **Ayarlar**' ı seçin.  **Docker 'ın ayarlar** iletişim kutusu açıldığında, **kaynak > dosya paylaşımı** ' nı seçin ve **C** sürücüsünü denetleyin.  Ardından, HDP korumalı alanı dağıtmak için adımları tekrarlayabilirsiniz.

1. HDP korumalı alan için Docker Kapsayıcıları dağıtıldıktan ve çalışır olduktan sonra, tarayıcınızı başlatarak ve [korumalı alan karşılama sayfasını](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page) açmak ve HDP panosunu başlatmak Için aşağıdaki Cloudera yönergelerini izleyerek ortama erişebilirsiniz.

    > [!NOTE] 
    > Bu yönergelerde, önce Sandbox ortamının yerel IP adresini, şablon VM 'nizin ana bilgisayar dosyasındaki sandbox-hdp.hortonworks.com ile eşleştirdiğini varsayalım.  Bu **eşlemeyi yapmazsanız,** ' a giderek korumalı alan karşılama sayfasına erişebilirsiniz `http://localhost:8080` .

### <a name="automatically-start-docker-containers-when-students-log-in"></a>Öğrenciler oturum açarken Docker kapsayıcılarını otomatik olarak Başlat

Öğrenciler için kullanımı kolay bir deneyim sağlamak üzere otomatik olarak şunları yapan bir PowerShell betiği kullanacağız:
  - Bir öğrenci başlatıldığında ve laboratuvar VM 'lerine bağlanırken HDP korumalı alan Docker kapsayıcılarını başlatır.
  - Tarayıcıyı başlatır ve korumalı alan karşılama sayfasına gider.
Ayrıca, bir öğrenci sanal makinesine oturum açtığında bu betiği otomatik olarak çalıştırmak için Windows Görev Zamanlayıcı de kullanacağız.
Bunu ayarlamak için şu adımları izleyin: [büyük veri analizi betiği](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/).

## <a name="cost-estimate"></a>Maliyet tahmini

Bu laboratuvarın maliyetini tahmin etmek isterseniz, aşağıdaki örneği kullanabilirsiniz.

20 saatlik zamanlanan sınıf süresi ve ev ödevi veya atamalar için 10 saatlik kota içeren 25 öğrenciye sahip bir sınıf için, laboratuvar fiyatı şöyle olacaktır:
  - 25 öğrenci * (20 + 10) saat * 55 laboratuvar birimi * 0,01 saat başına USD = 412,50 ABD Doları

Fiyatlandırma hakkında daha fazla bilgi için bkz. [Azure Lab Services fiyatlandırması](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Sonuç

Bu makalede, Docker ile dağıtılan Hortonçalışmalar veri platformunu kullanan büyük bir veri analizi sınıfına yönelik bir laboratuvar oluşturmak için gereken adımlarda sorun ele geçirildi.  Bu sınıf türü için kurulum, benzer veri analizi sınıfları için kullanılabilir.  Bu kurulum Ayrıca, dağıtım için Docker kullanan diğer sınıf türleri için de geçerli olabilir.

## <a name="next-steps"></a>Sonraki adımlar

Sonraki adımlar, herhangi bir laboratuvarı ayarlamak için ortaktır.

- [Şablon oluşturma ve yönetme](how-to-create-manage-template.md)
- [Kullanıcı ekle](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kota ayarlama](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zamanlama ayarlama](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Öğrenciler için e-posta kaydı bağlantıları](how-to-configure-student-usage.md#send-invitations-to-users)
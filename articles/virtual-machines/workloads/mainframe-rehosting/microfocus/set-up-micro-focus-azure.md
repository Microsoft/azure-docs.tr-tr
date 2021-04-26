---
title: Azure 'da Micro Focus Enterprise Server 5,0 ve Enterprise Developer 5,0 'yi yükler | Microsoft Docs
description: Bu makalede, Microsoft Azure için Micro Focus Enterprise Server 5,0 ve Enterprise Developer 5,0 ' yi nasıl yükleyeceğinizi öğrenin.
services: virtual-machines
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 5001f5df8ebab3e8366a2b31b1c5eb8ccc69c985
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951360"
---
# <a name="install-micro-focus-enterprise-server-50-and-enterprise-developer-50-on-azure"></a>Azure 'da Micro Focus Enterprise Server 5,0 ve Enterprise Developer 5,0 'yi yükler

Bu makalede, Microsoft Azure 'de [Micro Focus Enterprise Server 5,0](https://www.microfocus.com/documentation/enterprise-developer/ed50pu5/ES-WIN/GUID-F7D8FD6E-BDE0-4169-8D8C-96DDFFF6B495.html) ve [Micro focus Enterprise Developer 5,0](https://www.microfocus.com/documentation/enterprise-developer/ed50/) ' nin nasıl ayarlanacağı gösterilmektedir.

Azure 'da ortak bir iş yükü geliştirme ve test ortamıdır. Bu senaryo, daha uygun maliyetli ve dağıtım ve ayırma kolaylığı açısından yaygındır. Enterprise Server ile mikro odak, kullanılabilir en büyük anabilgisayar yeniden barındırma platformlarından birini oluşturmuştur. Windows ya da Linux sanal makinelerini (VM 'Ler) kullanarak Azure 'da daha az pahalı bir x86 platformunda z/OS iş yüklerini çalıştırabilirsiniz.

Bu kurulum, Azure Marketi 'nden Windows Server 2016 görüntüsünü çalıştıran Azure sanal makinelerini kullanır Microsoft SQL Server 2017 ' de zaten yüklüdür. Bu kurulum Azure Stack için de geçerlidir.

Enterprise Server için karşılık gelen geliştirme ortamı, Microsoft Visual Studio 2017 veya üzeri, Visual Studio Community (indirme için ücretsiz) veya çakışan küreler üzerinde çalışan Enterprise Developer ' dır. Bu makalede, Visual Studio 2017 veya üzeri yüklü bir Windows Server 2016 sanal makinesi kullanılarak nasıl dağıtılacağı gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce Şu önkoşullara göz atın:

-   Azure aboneliği. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

-   Mikro odak yazılımı ve geçerli bir lisans (veya deneme lisansı). Mevcut bir Micro Focus müşterisiyseniz, mikro odak temsilcinizle iletişime geçin. Aksi takdirde, [bir deneme isteyin](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

-   [Enterprise Server ve Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/ed50/)belgelerini alın.

    > [!Note]
    > Sanal makinelerinize erişimi denetlemek için birkaç seçenek vardır:
    > -   En iyi uygulama, [Azure](https://azure.microsoft.com/services/azure-bastion/)Savunma kurulumu kullanmaktır.
    > -   [Siteden siteye sanal özel ağ (VPN)](../../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) tüneli.
    > -   Bir sıçrama kutusu VM 'si.

## <a name="install-enterprise-server"></a>Enterprise Server’ı yükleme

1.  Daha iyi güvenlik ve yönetilebilirlik için, yalnızca bu proje için yeni bir kaynak grubu oluşturmayı düşünün — Örneğin, **Rgmikro Focusentserver**. Bir listede daha kolay olmasını sağlamak üzere kaynak türünü seçmek için Azure 'daki adın ilk kısmını kullanın.

2.  Sanal makine oluşturur. Azure Marketi 'nden istediğiniz sanal makineyi ve işletim sistemini seçin. Önerilen bir kurulum aşağıda verilmiştir:

    -   **Kurumsal Sunucu:** Windows Server 2016 ve SQL Server 2017 yüklü **ES2 v3 VM** (2 vCPU ve 16 GB bellek ile) seçeneğini belirleyin. Bu görüntü, Azure Marketi 'nden edinilebilir. Enterprise Server, Azure SQL veritabanı 'nı da kullanabilir.

    -   **Kurumsal Geliştirici:** Windows 10 ve Visual Studio yüklü **B2MS VM** 'yi (2 vCPU ve 8 GB bellekle) seçin. Bu görüntü, Azure Marketi 'nden edinilebilir.

3.  **Temel bilgiler** bölümünde, Kullanıcı adınızı ve parolanızı girin. VM 'Ler için kullanmak istediğiniz **aboneliği** ve **konumu/bölgeyi** seçin. Kaynak grubu için **Rgmikro Focusentserver** ' ı seçin.

4.  Her iki VM 'yi aynı sanal ağa yerleştirin, böylece birbirleriyle iletişim kurabilir.

5.  Geri kalan ayarlar için varsayılan değerleri kabul edin. Bu VM 'lerin Yöneticisi için oluşturduğunuz Kullanıcı adını ve parolayı unutmayın.

6.  Sanal makineler oluşturulduğunda, **3389** HTTP için **9003, 86** ve **80** gelen bağlantı noktalarını, Enterprise Server makinesinde Uzak Masaüstü Protokolü (RDP) ve geliştirici makinesinde **3389** ' yi açın.

7.  Enterprise Server sanal makinesinde oturum açmak için, Azure portal ' de ES2 v3 VM ' yi seçin. **Genel bakış** bölümüne gidin ve bir RDP oturumu başlatmak için **Bağlan** ' ı seçin. VM için oluşturduğunuz kimlik bilgilerini kullanarak oturum açın.

8.  RDP oturumunda, aşağıdaki iki dosyayı yükleyin. Windows 'u kullandığınız için dosyaları RDP oturumuna sürükleyip bırakabilirsiniz:

    -   `es\_50.exe`, kurumsal sunucu yükleme dosyası.

    -   `mflic`, ilgili lisans dosyası — Enterprise Server bu olmadan yüklenmez.

9.  Yüklemeyi başlatmak için dosyaya çift tıklayın. İlk pencerede, yükleme konumunu seçin ve Son Kullanıcı Lisans sözleşmesini kabul edin.

    ![Ekran görüntüsü, yüklemeyi başlatabileceğiniz mikro odak kurumsal sunucu iletişim kutusunu gösterir.](media/install-image-1.png)

    Kurulum tamamlandığında, aşağıdaki ileti görünür:

    ![Ekran görüntüsü, mikro odaklı kurumsal sunucu iletişim kutusunda bir başarı iletisi gösterir.](media/install-image-2.png)

 ### <a name="check-for-updates"></a>Güncelleştirmeleri denetle

Yükleme sonrasında, Microsoft C++ yeniden dağıtılabilir ve .NET Framework gibi bir dizi önkoşul olarak Enterprise Server ile birlikte yüklü olan ek güncelleştirmeleri denetlediğinizden emin olun.

### <a name="upload-the-license"></a>Lisansı karşıya yükleyin

1.  Micro Focus lisans yönetimini başlatın.

2.   \> **Micro Focus License Manager** \> **lisans yönetimini** Başlat ' ı seçin ve ardından **Yükle** sekmesine tıklayın. karşıya yüklenecek lisans biçimi türünü seçin: bir lisans dosyası veya 16 karakterlik lisans kodu. Örneğin, bir dosya için, **Lisans dosyasında**, `mflic` daha önce VM 'ye yüklenen * dosyasına gidin ve **Lisansları Yükleme ' yi** seçin.

    ![Ekran görüntüsü, Lisans yüklemesi ' ni seçebileceğiniz mikro odak lisans yönetimi iletişim kutusunu gösterir.](media/install-image-3.png)

3.  Enterprise Server 'ın yüklendiğini doğrulayın. Şu URL 'YI kullanarak bir tarayıcıdan Enterprise Server yönetim sitesini başlatmayı deneyin: `http://localhost:86/` . Kurumsal sunucu yönetim sayfası gösterildiği gibi görüntülenir.

    ![Kurumsal sunucu yönetim sayfası](media/install-image-4.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Geliştirici makinesine Enterprise Developer 'ı yükler

1.  Daha önce oluşturulmuş kaynak grubunu (örneğin, **Rgmikro Focusentserver**) seçin ve ardından Geliştirici görüntüsünü seçin.

2.  Sanal makinede oturum açmak için **genel bakış** bölümüne gidin ve **Bağlan**' ı seçin. Bu oturum açma, bir RDP oturumu başlatır. VM için oluşturduğunuz kimlik bilgilerini kullanarak oturum açın.

3.  RDP oturumunda, aşağıdaki iki dosyayı yükleyin (isterseniz sürükleyip bırakın):

    -   `edvs2017.exe`, kurumsal sunucu yükleme dosyası.

    -   `mflic`, ilgili lisans dosyası (Kurumsal Geliştirici bu olmadan yüklenmez).

4.  Yüklemeyi başlatmak için **edvs2017.exe** dosyasına çift tıklayın. İlk pencerede, yükleme konumunu seçin ve Son Kullanıcı Lisans sözleşmesini kabul edin. İsterseniz, büyük olasılıkla ihtiyaç duyacağınız bu Terminal öykünücüsünü yüklemek için **Rumba 9,5 'Yi yükleyebilirsiniz** .

    ![Visual Studio 2017 için Micro Focus Enterprise Developer kurulum iletişim kutusu](media/install-image-5.png)

5.  Kurulum tamamlandıktan sonra aşağıdaki ileti görünür:

    ![Kurulum başarılı iletisi](media/install-image-6.png)

6.  Micro Focus lisans yöneticisini, yalnızca kurumsal sunucu için yaptığınız gibi başlatın.  \> **Micro Focus License Manager** \> **lisans yönetimini** Başlat ' ı seçin ve sonra da **Install** sekmesine tıklayın.

7.  Karşıya yüklenecek lisans biçimi türünü seçin: bir lisans dosyası veya 16 karakterlik lisans kodu. Örneğin, bir dosya için, **Lisans dosyasında**, `mflic` daha önce VM 'ye yüklenen dosyaya gidin ve  **Lisansları Yükleme ' yi** seçin.

    ![Mikro odaklı lisans yönetimi iletişim kutusu](media/install-image-7.png)

Enterprise Developer yüklendiğinde, Azure 'da bir Micro Focus geliştirme ve test ortamı dağıtımınız tamamlanmıştır!

**Sonraki adımlar**

-   [BankDemo uygulamasını ayarlama](./demo.md)

-   [Docker kapsayıcılarında kurumsal sunucu Çalıştır](./run-enterprise-server-container.md)

-   [Ana bilgisayar uygulaması geçişi](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
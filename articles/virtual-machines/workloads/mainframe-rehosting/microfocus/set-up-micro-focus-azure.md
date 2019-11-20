---
title: Azure 'da Micro Focus Enterprise Server 4,0 ve Enterprise Developer 4,0 'yi yükler | Microsoft Docs
description: Azure sanal makinelerinde (VM) mikro odak geliştirme ve test ortamını kullanarak IBM z/OS ana bilgisayar iş yüklerinizi yeniden barındırın.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 05/29/2019
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: a5426c3cd7552b24739f9a20e01d5a4b42bd383c
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834564"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>Azure 'da Micro Focus Enterprise Server 4,0 ve Enterprise Developer 4,0 'yi yükler

Bu makalede, Azure 'da [Micro Focus Enterprise Server 4,0](https://www.microfocus.com/documentation/enterprise-developer/es30/) ve [Micro focus Enterprise Developer 4,0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) ' nin nasıl ayarlanacağı gösterilmektedir.

Azure 'da ortak bir iş yükü geliştirme ve test ortamıdır. Bu senaryo, daha uygun maliyetli ve dağıtım ve ayırma kolaylığı açısından yaygındır. Enterprise Server ile mikro odak, kullanılabilir en büyük anabilgisayar yeniden barındırma platformlarından birini oluşturmuştur. Windows ya da Linux sanal makinelerini (VM 'Ler) kullanarak Azure 'da daha az pahalı bir x86 platformunda z/OS iş yüklerini çalıştırabilirsiniz.

Bu kurulum, Azure Marketi 'nden Windows Server 2016 görüntüsünü çalıştıran Azure sanal makinelerini kullanır Microsoft SQL Server 2017 ' de zaten yüklüdür. Bu kurulum Azure Stack için de geçerlidir.

Enterprise Server için karşılık gelen geliştirme ortamı, Microsoft Visual Studio 2017 veya üzeri, Visual Studio Community (indirme için ücretsiz) veya çakışan küreler üzerinde çalışan Enterprise Developer ' dır. Bu makalede, Visual Studio 2017 veya üzeri yüklü bir Windows Server 2016 sanal makinesi kullanılarak nasıl dağıtılacağı gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce Şu önkoşullara göz atın:

- Azure aboneliği. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

- Mikro odak yazılımı ve geçerli bir lisans (veya deneme lisansı). Mevcut bir Micro Focus müşterisiyseniz, mikro odak temsilcinizle iletişime geçin. Aksi takdirde, [bir deneme isteyin](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

- [Enterprise Server ve Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#")belgelerini alın.

> [!NOTE]
> Azure VM 'lerine erişimi denetleyebilmeniz için bir siteden siteye sanal özel ağ (VPN) tüneli veya bir atlama kutusu kurmak en iyi uygulamadır.

## <a name="install-enterprise-server"></a>Enterprise Server’ı yükleme

1. Daha iyi güvenlik ve yönetilebilirlik için, yalnızca bu proje için yeni bir kaynak grubu oluşturmayı düşünün — Örneğin, **Rgmikro Focusentserver**. Bir listede daha kolay olmasını sağlamak üzere kaynak türünü seçmek için Azure 'daki adın ilk kısmını kullanın.

2. Sanal makine oluşturur. Azure Marketi 'nden istediğiniz sanal makineyi ve işletim sistemini seçin. Önerilen bir kurulum aşağıda verilmiştir:

    - **Kurumsal Sunucu**: Windows Server 2016 ve SQL Server 2017 yüklü ES2 v3 VM (2 vCPU ve 16 GB bellek ile) seçeneğini belirleyin. Bu görüntü, Azure Marketi 'nden edinilebilir. Enterprise Server, Azure SQL veritabanı 'nı da kullanabilir.

    - **Kurumsal Geliştirici**: Windows 10 ve Visual Studio yüklü B2ms VM 'yi (2 vCPU ve 8 GB bellekle) seçin. Bu görüntü, Azure Marketi 'nden edinilebilir.

3. **Temel bilgiler** bölümünde, Kullanıcı adınızı ve parolanızı girin. VM 'Ler için kullanmak istediğiniz **aboneliği** ve **konumu/bölgeyi** seçin. Kaynak grubu için **Rgmikro Focusentserver** ' ı seçin.

4. Her iki VM 'yi aynı sanal ağa yerleştirin, böylece birbirleriyle iletişim kurabilir.

5. Geri kalan ayarlar için varsayılan değerleri kabul edin. Bu VM 'lerin Yöneticisi için oluşturduğunuz Kullanıcı adını ve parolayı unutmayın.

6. Sanal makineler oluşturulduğunda, HTTP için 9003, 86 ve 80 gelen bağlantı noktalarını ve Enterprise Server makinesinde RDP için 3389 ve geliştirici makinesinde 3389 ' i açın.

7. Enterprise Server sanal makinesinde oturum açmak için, Azure portal ' de ES2 v3 VM ' yi seçin. **Genel bakış** bölümüne gidin ve bir RDP oturumu başlatmak için **Bağlan** ' ı seçin. VM için oluşturduğunuz kimlik bilgilerini kullanarak oturum açın.

8. RDP oturumunda, aşağıdaki iki dosyayı yükleyin. Windows 'u kullandığınız için dosyaları RDP oturumuna sürükleyip bırakabilirsiniz:

    - **es\_40. exe**, kurumsal sunucu yükleme dosyası.

    - **mflik**, ilgili lisans dosyası — kurumsal sunucu bu olmadan yüklenmez.

9. Yüklemeyi başlatmak için dosyaya çift tıklayın. İlk pencerede, yükleme konumunu seçin ve Son Kullanıcı Lisans sözleşmesini kabul edin.

     ![Mikro odaklı kurumsal sunucu kurulum ekranı](media/01-enterprise-server.png)

     Kurulum tamamlandığında, aşağıdaki ileti görünür:

     ![Mikro odaklı kurumsal sunucu kurulum ekranı](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>Güncelleştirmeleri denetle

Yükleme sonrasında, Microsoft C++ yeniden dağıtılabilir ve .NET Framework gibi birçok önkoşul, Enterprise Server ile birlikte yüklendiği için ek güncelleştirmeleri denetlediğinizden emin olun.

### <a name="upload-the-license"></a>Lisansı karşıya yükleyin

1. Micro Focus lisans yönetimini başlatın.

2. **Micro Focus License** Manager\> **lisans yönetimini** **Başlat** \> ' a tıklayın ve ardından, sonra da **Install** sekmesine tıklayın. Karşıya yüklenecek lisans biçimi türünü seçin: bir lisans dosyası veya 16 karakterlik lisans kodu. Örneğin, bir dosya için, **Lisans dosyasında**, daha önce VM 'ye yüklenen **mflik** dosyasına gidin ve **Lisansları Yükleme ' yi**seçin.

     ![Mikro odaklı lisans yönetimi iletişim kutusu](media/03-enterprise-server.png)

3. Enterprise Server 'ın yüklendiğini doğrulayın. Bu URL 'YI <http://localhost:86/> kullanarak bir tarayıcıdan Enterprise Server yönetim sitesini başlatmayı deneyin. Kurumsal sunucu yönetim sayfası gösterildiği gibi görüntülenir.

     ![Kurumsal sunucu yönetim sayfası](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Geliştirici makinesine Enterprise Developer 'ı yükler

1. Daha önce oluşturulmuş kaynak grubunu (örneğin, **Rgmikro Focusentserver**) seçin ve ardından Geliştirici görüntüsünü seçin.

2. Sanal makinede oturum açmak için **genel bakış** bölümüne gidin ve **Bağlan**' ı seçin. Bu oturum açma, bir RDP oturumu başlatır. VM için oluşturduğunuz kimlik bilgilerini kullanarak oturum açın.

3. RDP oturumunda, aşağıdaki iki dosyayı yükleyin (isterseniz sürükleyip bırakın):

    - **edvs2017. exe**, kurumsal sunucu yükleme dosyası.

    - **mflik**, karşılık gelen lisans dosyası (Kurumsal Geliştirici bu olmadan yüklenmez).

4. Yüklemeyi başlatmak için **edvs2017. exe** dosyasına çift tıklayın. İlk pencerede, yükleme konumunu seçin ve Son Kullanıcı Lisans sözleşmesini kabul edin. İsterseniz, büyük olasılıkla ihtiyaç duyacağınız bu Terminal öykünücüsünü yüklemek için **Rumba 9,5 'Yi yükleyebilirsiniz** .

     ![Visual Studio 2017 için Micro Focus Enterprise Developer kurulum iletişim kutusu](media/04-enterprise-server.png)

5. Kurulum tamamlandıktan sonra aşağıdaki ileti görünür:

     ![Kurulum başarılı iletisi](media/05-enterprise-server.png)

6. Micro Focus lisans yöneticisini, yalnızca kurumsal sunucu için yaptığınız gibi başlatın. **Micro Focus License** Manager\> **lisans yönetimini** **Başlat** \> ' ı seçin ve sonra da **Install** sekmesine tıklayın.

7. Karşıya yüklenecek lisans biçimi türünü seçin: bir lisans dosyası veya 16 karakterlik lisans kodu. Örneğin, bir dosya için, **Lisans dosyasında**, daha önce VM 'ye yüklenen **mflik** dosyasına gidin ve **Lisansları Yükleme ' yi**seçin.

     ![Mikro odaklı lisans yönetimi iletişim kutusu](media/07-enterprise-server.png)

Enterprise Developer yüklendiğinde, Azure 'da bir Micro Focus geliştirme ve test ortamı dağıtımınız tamamlanmıştır!

## <a name="next-steps"></a>Sonraki adımlar

- [Banka demo uygulamasını ayarlama](./demo.md)
- [Docker kapsayıcılarında kurumsal sunucu Çalıştır](./run-enterprise-server-container.md)
- [Ana bilgisayar uygulaması geçişi](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)

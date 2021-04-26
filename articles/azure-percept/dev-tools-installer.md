---
title: Azure Percept dev araçları paketi yükleyicisine genel bakış
description: Azure Percept ile gelişmiş geliştirmeyi hızlandırmak için geliştirme araçları paketi yükleyicisini kullanma hakkında daha fazla bilgi edinin
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: f81f7922431f85cfc2a98261a128ba66d23a984f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608604"
---
# <a name="dev-tools-pack-installer-overview"></a>Geliştirme araçları paketi yükleyicisine genel bakış

Geliştirme araçları paketi yükleyicisi, gelişmiş bir akıllı kenar çözümü geliştirmek için gereken tüm araçları yükleyen ve yapılandıran tek bir durdurma çözümüdür.

## <a name="mandatory-tools"></a>Zorunlu Araçlar

* [Visual Studio Code](https://code.visualstudio.com/)
* [Python 3,6 veya üzeri](https://www.python.org/)
* [Docker 19,03](https://www.docker.com/)
* [PIP3](https://pip.pypa.io/en/stable/user_guide/)
* [TensorFlow 1,13](https://www.tensorflow.org/)
* [Azure Machine Learning SDK 1,1](/python/api/overview/azure/ml/)

## <a name="optional-tools"></a>İsteğe bağlı Araçlar

* [NVIDIA ayrıntılı AKıŞ SDK 5](https://developer.nvidia.com/deepstream-sdk) (NVIDIA Hızlandırıcılar için çözümler geliştirmek için araç seti)
* [Intel OpenVino Toolkit 2020,2](https://docs.openvinotoolkit.org/) (Intel hızlandırıcıları için çözüm geliştirmeye yönelik araç seti)
* [Lobe.ai](https://lobe.ai/)  
* [Streamaydınlatmalı](https://www.streamlit.io/)
* [Pytorch 1.4.0 (Windows) veya 1.2.0 (Linux)](https://pytorch.org/)
* [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
* [Chainer 5,2](https://chainer.org/)
* [Caffe](https://caffe.berkeleyvision.org/)
* [CUDA araç seti 10.0.130](https://developer.nvidia.com/cuda-toolkit)
* [Microsoft Cognitive Toolkit 2.5.1](https://www.microsoft.com/research/product/cognitive-toolkit/?lang=fr_ca)

## <a name="known-issues"></a>Bilinen sorunlar

- Docker düzgün çalışmıyorsa isteğe bağlı Caffe yüklemesi başarısız olabilir. Caffe 'yi yüklemek isterseniz, Caffe yüklemesini geliştirme araçları paketi yükleyicisi aracılığıyla denemeden önce Docker 'ın yüklü olduğundan ve çalıştığından emin olun.

- İsteğe bağlı CUDA yüklemesi uyumsuz sistemlerde başarısız olur. Dev araçları paketi yükleyicisi aracılığıyla [CUDA araç setini 10.0.130](https://developer.nvidia.com/cuda-toolkit) yüklemeye çalışmadan önce sistem uyumluluklarını doğrulayın.

## <a name="docker-minimum-requirements"></a>Docker minimum gereksinimleri

### <a name="windows"></a>Windows

- Windows 10 64-bit: Pro, Enterprise veya eğitim (derleme 16299 veya üzeri).

- Hyper-V ve kapsayıcılar Windows özelliklerinin etkinleştirilmesi gerekir. Windows 10 ' da Hyper-V ' d e başarıyla çalıştırmak için aşağıdaki donanım önkoşulları gereklidir:

    - [Ikinci düzey adres çevirisi (SLAT)](https://en.wikipedia.org/wiki/Second_Level_Address_Translation) ile 64 bit işlemci
    - 4 GB sistem RAM 'i
    - BIOS düzeyinde donanım sanallaştırma desteğinin BIOS ayarları etkinleştirilmiş olması gerekir. Daha fazla bilgi için bkz. sanallaştırma.

> [!NOTE]
> Docker, Microsoft 'un Windows 10 işletim sistemi için destek yaşam döngüsünü temel alan Windows 'da Docker Desktop 'ı destekler. Daha fazla bilgi için bkz. [Windows yaşam döngüsü olgu sayfası](https://support.microsoft.com/help/13853/windows-lifecycle-fact-sheet).

[Windows 'A Docker Desktop yükleme](https://docs.docker.com/docker-for-windows/install/#install-docker-desktop-on-windows)hakkında daha fazla bilgi edinin.

### <a name="mac"></a>Mac

- Mac, aşağıdaki özniteliklere sahip bir 2010 veya daha yeni bir model olmalıdır:
    - Intel işlemcisi
    - Genişletilmiş sayfa tabloları (EPT) ve Kısıtlanmamış mod dahil olmak üzere, bellek yönetimi birimi (MMU) sanallaştırmaya yönelik Intel 'in donanım desteği. Bir terminalde aşağıdaki komutu çalıştırarak makinenizin bu desteğe sahip olup olmadığını kontrol edebilirsiniz: ```sysctl kern.hv_support``` . Mac 'niz hiper yönetici çerçevesini destekliyorsa, komut yazdırılır ```kern.hv_support: 1``` .

- macOS sürüm 10,14 veya üzeri (Mojave, Catalina veya Big Sur). MacOS 'un en son sürümüne yükseltmenizi öneririz. MacOS sürümünüzü sürüm 10,15 ' e yükselttikten sonra herhangi bir sorunla karşılaşırsanız, macOS 'un bu sürümü ile uyumlu olması için Docker Desktop 'ın en son sürümünü yüklemelisiniz.

- En az 4 GB RAM.

- Sürüm 4.3.30 'den önce VirtualBox yüklemeyin; Docker Desktop ile uyumlu değildir.

- Yükleyici, Apple M1 üzerinde desteklenmez.

[Mac 'e Docker Desktop yükleme](https://docs.docker.com/docker-for-mac/install/#system-requirements)hakkında daha fazla bilgi edinin.

## <a name="launch-the-installer"></a>Yükleyiciyi başlatın

[Windows](https://go.microsoft.com/fwlink/?linkid=2132187), [Linux](https://go.microsoft.com/fwlink/?linkid=2132186)veya [Mac](https://go.microsoft.com/fwlink/?linkid=2132296)için geliştirme araçları paketi yükleyicisini indirin. Yükleyiciyi, aşağıda açıklandığı gibi platformunuza uygun olarak başlatın.

### <a name="windows"></a>Windows

1. Yükleme Sihirbazı 'nı açmak için **geliştirme-Araçlar-paketi-yükleyicisi** ' ne tıklayın.

### <a name="mac"></a>Mac

1. İndirdikten sonra, **dev-Tools-Pack-Installer.app** dosyasını **uygulamalar** klasörüne taşıyın.

1. Yükleme Sihirbazı 'nı açmak için **dev-Tools-Pack-Installer.app** 'ye tıklayın.

1. "Tanımlanamayan bir geliştirici" güvenlik iletişim kutusu alırsanız:

    1. **Sistem Tercihleri**  ->  **güvenlik & gizlilik**  ->  **genel** ' e gidin ve **dev-Tools-Pack-Installer.app**' nin yanındaki **yine aç** ' a tıklayın.
    1. Elektron simgesine tıklayın.
    1. Güvenlik iletişim kutusunda **Aç** ' a tıklayın.

### <a name="linux"></a>Linux

1. Tarayıcı tarafından istendiğinde **Kaydet** ' e tıklayarak yükleyici indirmeyi doldurun.

1. **. Appımage** dosyasına yürütme izinleri ekleyin:

    1. Bir Linux terminali açın.

    1. **İndirmeler** klasörüne gitmek için terminalde aşağıdakileri girin:

        ```bash
        cd ~/Downloads/
        ```

    1. Appımage çalıştırılabilir dosyasını oluşturun:

        ```bash
        chmod +x Dev-Tools-Pack-Installer.AppImage
        ```

    1. Yükleyiciyi çalıştırın:

        ```bash
        ./Dev-Tools-Pack-Installer.AppImage
        ```

1. **. Appımage** dosyasına yürütme izinleri ekleyin:

    1. . Appımage dosyasına sağ tıklayın ve **Özellikler**' i seçin.
    1. **İzinler** sekmesini açın.
    1. **Dosya yürütmeye Izin ver**' in yanındaki kutuyu işaretleyin.
    1. **Özellikleri** kapatın ve **. appımage** dosyasını açın.

## <a name="run-the-installer"></a>Yükleyiciyi çalıştırma

1. **Geliştirme araçları paketi yükleyicisini yükleme** sayfasında, yükleyicide bulunan her yazılım paketinin lisans sözleşmelerini görüntülemek Için **lisansı görüntüle** ' ye tıklayın. Lisans sözleşmeleri içindeki koşulları kabul ediyorsanız, kutuyu işaretleyin ve **İleri**' ye tıklayın.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-license-agreements.png" alt-text="Yükleyicideki lisans sözleşmesi ekranı.":::

1. Microsoft gizlilik bildirimi ' ni gözden geçirmek için **Gizlilik bildirimi** ' ne tıklayın. Gizlilik bildirimi koşullarını kabul ediyorsanız ve Microsoft 'a Tanılama verileri göndermek istiyorsanız **Evet** ' i seçin ve **İleri**' ye tıklayın. Aksi takdirde **Hayır** ' ı seçin ve **İleri**' ye tıklayın.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-privacy-statement.png" alt-text="Yükleyicideki gizlilik bildirimi sözleşmesi ekranı.":::

1. **Bileşenleri Yapılandır** sayfasında, yüklemek istediğiniz isteğe bağlı araçları seçin (varsayılan olarak, zorunlu araçlar yüklenir).

    1. Azure Percept DK 'nin bir parçası olan Azure Percept Audio SoM ile çalışıyorsanız, Intel OpenVino Toolkit ve Miniconda3 ' yi yüklediğinizden emin olun.

    1. Yüklemeye devam etmek için **yükleme** ' ye tıklayın.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-configure-components.png" alt-text="Kullanılabilir yazılım paketlerini gösteren Yükleyici ekranı.":::

1. Seçilen tüm bileşenlerin başarılı bir şekilde yüklenmesinden sonra sihirbaz, **Kurulum Sihirbazı 'Nı tamamlama** sayfasına ilerler. Yükleyiciden çıkmak için **son** ' a tıklayın.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-finish.png" alt-text="Yükleyici tamamlama ekranı.":::

## <a name="docker-status-check"></a>Docker durum denetimi

Yükleyici, Docker Desktop 'ın iyi çalışan bir durumda olduğunu doğrulamak için size bildirimde bulunursa aşağıdaki adımlara bakın:

### <a name="windows"></a>Windows

1. Sistem tepsisindeki gizli simgeler ' i genişletin.

    :::image type="content" source="./media/dev-tools-installer/system-tray.png" alt-text="Sistem tepsisi.":::

1. Docker Desktop **simgesinin çalıştığını doğrulayın.**

    :::image type="content" source="./media/dev-tools-installer/docker-status-running.png" alt-text="Docker durumu.":::

1. Sistem tepsisinde yukarıdaki simgeyi görmüyorsanız, Başlat menüsünden Docker Desktop ' ı başlatın.

1. Docker yeniden başlatmanızı isterse, yükleyiciyi kapatmak ve yeniden başlatma tamamlandıktan sonra Docker çalışır durumda olduktan sonra yeniden başlatmak iyi olur. Başarıyla yüklenen üçüncü taraf uygulamaların algılanması ve otomatik olarak yeniden yüklenmesi gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Azure Percept DK için gelişmiş geliştirmeyi kullanmaya başlamak üzere [Azure Percept gelişmiş geliştirme deposuna](https://github.com/microsoft/azure-percept-advanced-development) göz atın.

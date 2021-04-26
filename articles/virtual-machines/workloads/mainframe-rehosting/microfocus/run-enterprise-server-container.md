---
title: Azure 'daki bir Docker kapsayıcısında mikro odak kurumsal sunucu 5,0 ' i çalıştırın | Microsoft Docs
description: Bu makalede, Microsoft Azure 'de bir Docker kapsayıcısında Micro Focus Enterprise Server 5,0 ' i nasıl çalıştıracağınızı öğrenin.
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
ms.openlocfilehash: 146fc59704719fd3aebb03b9b90a176221beea10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950697"
---
# <a name="run-micro-focus-enterprise-server-50-in-a-docker-container-on-azure"></a>Azure 'daki bir Docker kapsayıcısında mikro odak kurumsal sunucu 5,0 ' i çalıştırın

Micro Focus Enterprise Server 5,0 ' i Azure 'daki bir Docker kapsayıcısında çalıştırabilirsiniz. Bu öğreticide nasıl yapılacağı gösterilmektedir. Enterprise Server için Windows CICS (müşteri bilgileri denetim sistemi) acctdemo gösterisini kullanır.

Docker, uygulamalara taşınabilirlik ve yalıtım ekler. Örneğin, bir Windows sanal makinesinden (VM) bir Docker görüntüsünü başka bir şekilde veya bir depodan Docker ile bir Windows Server 'a aktarabilirsiniz. Docker görüntüsü, kurumsal sunucu yüklemesi gerekmeden aynı yapılandırmaya sahip yeni konumda çalışır. Bu, görüntünün bir parçasıdır. Lisanslama konuları hala geçerlidir.

Bu öğretici, Azure Marketi 'nden **Windows 2016 Datacenter 'ı kapsayıcı VM 'si ile birlikte** yüklenir. Bu VM, **Docker 18.09.0** içerir. Aşağıdaki adımlar, kapsayıcının nasıl dağıtılacağını, çalıştırılacağını nasıl çalıştıracağınızı ve bir 3270 öykünücüsü ile bu sunucuya nasıl bağlanacağını göstermektedir.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce Şu önkoşullara göz atın:

-   Azure aboneliği. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

-   Mikro odak yazılımı ve geçerli bir lisans (veya deneme lisansı). Mevcut bir Micro Focus müşterisiyseniz, mikro odak temsilcinizle iletişime geçin. Aksi takdirde, [bir deneme isteyin](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

    > [!Note] 
    > Docker tanıtım dosyaları Enterprise Server 5,0 ' ye dahildir. Bu öğretici, ENT \_ Server \_ dockerfile 'ları destekliyor \_ 5,0 \_windows.zip kullanır. Bu, kurumsal sunucu yükleme dosyasına eriştiğiniz yerden erişin veya başlamak için *Micro Focus* ' a gidin.

-   [Kurumsal sunucu ve Kurumsal Geliştirici](https://www.microfocus.com/documentation/enterprise-developer/#%22)belgeleri.

## <a name="create-a-vm"></a>VM oluşturma

1.  Medyayı ENT \_ Server \_ dockerfile 'ları destekliyor \_ 5,0 \_windows.zip dosyasından güvenli hale getirin. ES-Docker-prod-XXXXXXXX. mflik lisanslama dosyasının güvenliğini sağlayın (Docker görüntülerini derlemek için gereklidir).

2.  VM 'yi oluşturun. Bunu yapmak için, Azure portal açın, sol üstteki menüden **kaynak oluştur** ' u seçin ve *Windows Server işletim sistemine* göre filtreleyin. Sonuçlarda **Windows Server** ' ı seçin. Sonraki ekranda, **kapsayıcılar Ile Windows Server 2016 Datacenter**' u seçin.

    ![Azure portal arama sonuçlarının ekran görüntüsü](./media/run-image-1.png)

3.  VM 'nin özelliklerini yapılandırmak için örnek ayrıntıları ' nı seçin:

    1.  Bir VM boyutu seçin. Bu öğreticide, 2 sanal CPU ve 16 GB bellek içeren **Standart BIR DS2 \_ v3** VM kullanmayı düşünün.

    2.  Dağıtmak istediğiniz **bölgeyi** ve **kaynak grubunu** seçin.

    3.  **Kullanılabilirlik seçenekleri** için varsayılan ayarı kullanın.

    4.  **Kullanıcı adı** için, kullanmak istediğiniz yönetici hesabını ve parolayı yazın.

    5.  **3389 RDP bağlantı noktasının** açık olduğundan emin olun. VM 'de oturum açabilmeniz için yalnızca bu bağlantı noktasının herkese açık olması gerekir. Ardından, tüm varsayılan değerleri kabul edin ve **gözden geçir + oluştur**' a tıklayın.

    ![Sanal makine oluştur bölmesinin ekran görüntüsü](./media/run-image-2.png)

4.  Dağıtımın bitmesini bekleyin (birkaç dakika). Bir ileti, sanal makinenizin oluşturulduğunu belirtir.

5.  VM 'nizin **genel bakış** dikey penceresine gitmek Için **Kaynağa Git** ' i seçin.

6.  Sağ tarafta **Bağlan**' ı seçin. **Sanal makineye bağlan** seçenekleri sağda görünür.

7.  VM 'ye eklemenize olanak tanıyan Uzak Masaüstü Protokolü (RDP) dosyasını indirmek için **RDP dosyasını indir** düğmesini seçin.

8.  Dosya indirmeyi tamamladıktan sonra, açın ve VM için oluşturduğunuz Kullanıcı adını ve parolayı yazın.

    > [!Note]    
    > Oturum açmak için şirket kimlik bilgilerinizi kullanmayın. (RDP istemcisi bunları kullanmak isteyebileceğiniz varsayılır. Değilsiniz.)

9.  **Diğer seçimler**' ı seçin, ardından VM kimlik bilgilerinizi seçin.

Bu noktada, VM çalışır ve RDP aracılığıyla iliştirilir. Oturumunuz açıldı ve bir sonraki adım için hazırsınız.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Korumalı alan dizini oluşturma ve ZIP dosyasını karşıya yükleme

1.  VM üzerinde tanıtım ve lisans dosyalarını karşıya yükleyebileceğiniz bir dizin oluşturun. Örneğin, **C: \\ Sandbox**.

2.  **ENT \_ Server \_ dockerfile 'ları destekliyor \_ 5,0 \_windows.zip** ve **es-Docker-prod-xxxxxxxx. mflik** dosyasını oluşturduğunuz dizine yükleyin.

3.  ZIP dosyasının içeriğini, ayıklama işlemi tarafından oluşturulan **ENT \_ Server \_ dockerfile 'ları destekliyor \_ 5,0 \_ Windows** dizinine ayıklayın. Bu dizin bir Benioku dosyası (. html ve. txt dosyası) ve iki alt dizin, **Enterpriseserver** ve **örnekleri** içerir.

4.  **Es-Docker-prod-xxxxxxxx. mflik** 'i C: \\ Sandbox \\ ENT \_ Server \_ dockerfile 'ları destekliyor \_ 5,0 \_ Windows \\ enterpriseserver ve C: \\ Sandbox \\ ENT \_ Server \_ dockerfile 'ları destekliyor \_ 5,0 \_ Windows \\ örnekleri \\ CICS dizinleri olarak kopyalayın.  
      
    > [!Note]
    > Lisanslama dosyasını her iki dizine da kopyalamadığınızdan emin olun. Görüntülerin doğru lisanslanmış olduğundan emin olmak için Docker derleme adımının olması gerekir.

## <a name="check-docker-version-and-create-base-image"></a>Docker sürümünü denetleyin ve temel görüntü oluşturun

> [!Important]  
> Uygun Docker görüntüsünü oluşturmak, iki adımlı bir işlemdir. İlk olarak, Enterprise Server 5,0 temel görüntüsünü oluşturun. Ardından x64 platformu için başka bir görüntü oluşturun. Bir x86 (32-bit) görüntüsü oluşturabilseniz de 64 bit görüntüsünü kullanın.

1.  Bir komut istemi açın.

2.  Docker 'ın yüklü olduğundan emin olun. Komut isteminde şunu yazın: **Docker sürümü**  
    Örneğin, bu yazıldığında sürüm 18.09.0 idi.

3.  Dizini değiştirmek için şunu yazın:  
    **CD \\ Korumalı \\ alan \_ sunucu \_ dockerfile 'ları destekliyor \_ 5,0 \_ Windows \\ enterpriseserver**.

4.  İlk temel görüntü için derleme işlemini başlatmak üzere **IAcceptEulabld.bat** yazın. Bu işlemin çalışması için birkaç dakika bekleyin. Sonuçlarda, biri x64 ve diğeri x86 için oluşturulmuş iki resme dikkat edin:

    ![Görüntüleri gösterme Komut penceresi](./media/run-image-3.png)

5.  CICS demo 'in son görüntüsünü oluşturmak için, **CD \\ korumalı \\ \_ sunucu \_ Dockerfile 'ları destekliyor \_ 5,0 \_ Windows \\ örnekleri \\ CICS** yazarak CICS dizinine geçin.

6.  Görüntüyü oluşturmak için **bld.bat x64** yazın. İşlemin çalışması için birkaç dakika bekleyin ve görüntünün oluşturulduğunu belirten ileti.

7.  VM 'de yüklü olan tüm Docker görüntülerinin listesini göstermek için **Docker görüntülerini** yazın. **Mikro Focus/es-acctdemo** 'in bunlardan biri olduğundan emin olun.

    ![Es-acctdemo görüntüsünü gösteren Komut penceresi](./media/run-image-4.png)

## <a name="run-the-image"></a>Görüntüyü çalıştırma

1.  Enterprise Server 5,0 ve acctdemo uygulamasını başlatmak için komut isteminde şunu yazın:

    ```
    **docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win\_5.0\_x64
    ```

1.  [X3270](http://x3270.bgp.nu/) gibi bir 3270 Terminal öykünücüsü yükleyip, bağlantı noktası 9040 üzerinden, çalıştıran görüntüye eklemek için kullanın.

2.  Docker 'ın yönettiği kapsayıcılar için dinamik ana bilgisayar Yapılandırma Protokolü (DHCP) sunucusu olarak davranabilmesi için acctdemo kapsayıcısının IP adresini alın:

    1.  Çalışan kapsayıcının KIMLIĞINI alın. Komut istemine **Docker PS** YAZıN ve kimliği (Bu örnekte **22a0fe3159d0** ) göz önünde bulun. Bir sonraki adım için kaydedin.

    2.  Acctdemo kapsayıcısının IP adresini almak için önceki adımdaki kapsayıcı KIMLIĞINI aşağıdaki gibi kullanın:

    ```
    docker inspect \<containerID\> --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

    Örnek:

    ```
    docker inspect 22a0fe3159d0 --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

4. Acctdemo görüntüsünün IP adresini aklınızda tut. Örneğin, aşağıdaki çıktıda bulunan adres 172.19.202.52 ' dir.

    ![IP adresini gösteren Komut penceresi ekran görüntüsü](./media/run-image-5.png)

5. Öykünücüyü kullanarak görüntüyü bağlayın. Öykünücüyü, acctdemo görüntüsünün adresini ve 9040 numaralı bağlantı noktasını kullanacak şekilde yapılandırın. İşte bu **172.19.202.52:9040**. Sizinki benzer olacaktır. **CICS üzerinde oturum açma** ekranı açılır.

    ![CICS için oturum açma ekranının ekran görüntüsü](./media/run-image-6.png)

6. **Kullanıcı kimliği** Için **Sysad** ve **parola** için **sysad** girerek CICS bölgesinde oturum açın.

7. Öykünücü keymap 'i kullanarak ekranı temizleyin. X3270 için, **keymap** menü seçeneğini belirleyin.

8. Acctdemo uygulamasını başlatmak için **ACCT** yazın. Uygulama için ilk ekran görüntülenir.

     ![Ekran görüntüsü, uygulamayı görüntüleyen bir konsol penceresi gösterir.](./media/run-image-7.png)

9. Görüntüleme hesabı türleriyle denemeler yapın. Örneğin, Istek için **D** , **Hesap** için **11111** yazın. Deneyebileceğiniz diğer hesap numaraları 22222, 33333 vb...

    ![Ekran görüntüsü, uygulamadaki farklı değerleri düzenlemenizi gösterir.](./media/run-image-8.png)

10. Enterprise Server Yönetim konsolunu göstermek için komut istemine gidin ve **Start http: 172.19.202.52:86** yazın.

    ![Enterprise Server Yönetim Konsolu](media/run-image-9.png)

İşte bu kadar! Artık bir Docker kapsayıcısında bir CICS uygulaması çalıştırıyorsunuz ve yönetiyorsunuz.

## <a name="next-steps"></a>Sonraki adımlar

-   [Azure 'da Micro Focus Enterprise Server 5,0 ve Enterprise Developer 5,0 'yi yükler](./set-up-micro-focus-azure.md)

-   [Ana bilgisayar uygulaması geçişi](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)

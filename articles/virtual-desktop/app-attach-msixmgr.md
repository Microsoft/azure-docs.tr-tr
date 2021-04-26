---
title: MSıXMGR Tool 'u kullanma-Azure
description: Windows sanal masaüstü için MSıXMGR aracını kullanma.
author: Heidilohr
ms.topic: how-to
ms.date: 02/23/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: b2cab56fb99bda7da361cc2068396e53e794501d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448347"
---
# <a name="using-the-msixmgr-tool"></a>MSıXMGR aracını kullanma

MSıXMGR Aracı, MSIX 'nin maltılı bir uygulama genişletmekte. Araç, MALTıDA paketlenmiş bir uygulama alır (. MSIX) ve onu bir VHD, VHDx veya CıM dosyasına genişletir. Elde edilen MSIX görüntüsü, Windows sanal masaüstü dağıtımınızın kullandığı Azure depolama hesabında depolanır. Bu makalede, MSıXMGR aracının nasıl kullanılacağı gösterilir.

>[!NOTE]
>Uyumluluğu güvence altına almak için, MSIX görüntülerinizi depolayan CIMs 'nin Windows sanal masaüstü konak Havuzlarınızda çalıştırdığınız işletim sistemi sürümünde oluşturulduğundan emin olun. MSıXMGR CıM dosyaları oluşturabilir, ancak bu dosyaları yalnızca Windows 10 20 H2 çalıştıran bir konak havuzu ile kullanabilirsiniz.

## <a name="requirements"></a>Gereksinimler

Bu makaledeki yönergeleri izlemeden önce aşağıdaki işlemleri yapmanız gerekir:

- [MSıXMGR aracını indirin](https://aka.ms/msixmgr)
- MALTıDA paketlenmiş bir uygulama (. MSIX dosyası)
- MSIX görüntüsünü oluşturacağınız makinede yönetici izinleri alın

## <a name="create-an-msix-image"></a>MSIX görüntüsü oluşturma

Genişletme, MSIX paketlenmiş bir uygulama alma işlemidir (. MALTıDAN) ve bunu MALTıDA bir görüntüye geri alabilir (. VHD (x) veya. CıM dosyası).

Bir MSIX dosyasını genişletmek için:

1. Henüz yapmadıysanız [MSıXMGR aracını indirin](https://aka.ms/msixmgr) .

2. MSIXMGR.zip yerel bir klasöre ayıklayın.

3. Yükseltilmiş modda bir komut istemi açın.

4. 2. adımdaki yerel klasörü bulun.

5. Bir MSIX görüntüsü oluşturmak için komut isteminde aşağıdaki komutu çalıştırın.

    ```cmd
    msixmgr.exe -Unpack -packagePath <path to package> -destination <output folder> [-applyacls] [-create] [-vhdSize <size in MB>] [-filetype <CIM | VHD | VHDX>] [-rootDirectory <rootDirectory>]
    ```

    Yer tutucu değerlerini ilgili değerlerle değiştirmeyi unutmayın. Örnek:

    ```cmd
    msixmgr.exe -Unpack -packagePath "C:\Users\%username%\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
    ```

6. Görüntüyü oluşturduğunuza göre, hedef klasöre gidin ve MSIX görüntüsünü başarıyla oluşturduğunuzdan emin olun (. VHDX).

## <a name="create-an-msix-image-in-a-cim-file"></a>CıM dosyasında MSIX görüntüsü oluşturma

Dosya türü ve hedef yolunu değiştirerek CıM ve VHDX dosyaları oluşturmak için [5. adımdaki](#create-an-msix-image) komutunu da kullanabilirsiniz.

Örneğin, bu komutu CıM dosyası yapmak için nasıl kullanacağınız aşağıda verilmiştir:

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.cim" -applyacls -create -vhdSize 200 -filetype "cim" -rootDirectory apps
```

Bu komutu bir VHDX oluşturmak için nasıl kullanacağınızı aşağıda bulabilirsiniz:

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
```

## <a name="next-steps"></a>Sonraki adımlar

Maltı uygulama iliştirme hakkında daha fazla bilgi [](what-is-app-attach.md) edinin.

Uygulama eklemeyi ayarlamayı öğrenmek için şu makalelere göz atın:

- [Azure portalla MSIX uygulama iliştirmeyi ayarlama](app-attach-azure-portal.md)
- [PowerShell kullanarak MSIX uygulama eklemeyi ayarlama](app-attach-powershell.md)
- [MSIX uygulama iliştirme için PowerShell betikleri oluşturma](app-attach.md)
- [Windows sanal masaüstü için MSIX görüntüsü hazırlama](app-attach-image-prep.md)
- [MSIX uygulama iliştirme için dosya paylaşma ayarlama](app-attach-file-share.md)

MALTıDA uygulama iliştirme hakkında sorularınız varsa bkz. [uygulamamız IÇIN SSS](app-attach-faq.md) ve [uygulama iliştirme sözlüğü](app-attach-glossary.md)Ekle.

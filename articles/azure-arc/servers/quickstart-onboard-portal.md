---
title: Hızlı başlangıç-sunucular için Azure Arc kullanarak makineleri Azure 'a bağlama-Portal
description: Bu hızlı başlangıçta portaldan sunucular için Azure Arc kullanarak makineleri Azure 'a bağlamayı öğreneceksiniz
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: Azure Otomasyonu, DSC, PowerShell, istenen durum yapılandırması, güncelleştirme yönetimi, değişiklik izleme, envanter, runbook 'lar, Python, grafik, karma, yerleşik
ms.date: 08/25/2019
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 2ae7c8545286baebc83077276e356cd2e41f0dc3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73668670"
---
# <a name="quickstart-connect-machines-to-azure-using-azure-arc-for-servers---portal"></a>Hızlı başlangıç: sunucular için Azure Arc kullanarak makineleri Azure 'a bağlama-Portal

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Ön koşullar

Desteklenen istemcileri ve [sunucu Için Azure Arc genel bakış](overview.md)' da gereken ağ yapılandırmasını gözden geçirin.

## <a name="generate-the-agent-install-script-using-the-azure-portal"></a>Azure portal kullanarak aracı yüklemesi betiği oluşturma

1. [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal) Başlat
1. **+ Ekle** 'ye tıklayın
1. Tamamlamak için Sihirbazı izleyin
1. Son sayfada, kopyalayabileceğiniz (veya indirebileceğiniz) bir komut dosyası oluşturulur.

Betiğin, bağlanmak istediğiniz hedef makinede çalıştırılması gerekir. Aracıyı indirir, yükler ve makinenin tek bir işlem olarak bağlanmasını sağlar.

Yönetmek istediğiniz Azure dışı sunucularda:

1. Sunucuda oturum açma (SSH, RDP veya PowerShell uzaktan iletişimini kullanarak)
1. Bir Shell başlatın: Linux üzerinde Bash, Windows 'da yönetici olarak PowerShell
1. Portaldan betiği yapıştırın ve Azure 'a bağlanmak için sunucuda yürütün.
1. Tek bir sunucuyu eklemeye yönelik varsayılan kimlik doğrulaması, Azure ' Device login' kullanılarak *etkileşimlidir* . Betiği çalıştırdığınızda şuna benzer bir ileti görürsünüz:

  ```none
  To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B3V3NLWRF to authenticate.
  ```
  
   Bir tarayıcı açın ve kimlik doğrulaması yapmak için kodu girin. Tarayıcının, ekleme yaptığınız sunucuda çalıştırılması gerekmez, dizüstü bilgisayarınız gibi başka bir bilgisayarda olabilir.

1. Etkileşimli olmayan kimlik doğrulaması yapmak isterseniz, [hizmet sorumlusu oluşturma](quickstart-onboard-powershell.md#create-a-service-principal-for-onboarding-at-scale) ve portaldan oluşturulan betiği değiştirme bölümündeki adımları izleyin.

> [!NOTE]
> Sunucuda ilk kez oturum açmak için Internet Explorer kullanıyorsanız, hata dışarı çıkar. Yalnızca tarayıcıyı yeniden açıp tekrar yapabilirsiniz.

## <a name="execute-the-script-on-target-nodes"></a>Betiği hedef düğümlerde yürütme

Her bir düğümde oturum açın ve portaldan oluşturduğunuz betiği yürütün. Betik başarıyla tamamlandıktan sonra, sunucunun başarıyla bağlandığını doğrulamak Azure portal gidin.

![Başarılı ekleme](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>Temizleme

Bir makinenin sunucular için Azure Arc bağlantısını kesmek için iki adım gerçekleştirmeniz gerekir.

1. [Portalda](https://aka.ms/hybridmachineportal)makineyi seçin, üç noktaya (`...`) tıklayın ve **Sil**' i seçin.
1. Aracıyı makineden kaldırın.

   Windows 'da Aracıyı kaldırmak için "uygulamalar & özellikleri" denetim masasını kullanabilirsiniz.
  
  ![Uygulamalar & özellikleri](./media/quickstart-onboard/apps-and-features.png)

   Kaldırma işlemini yapmak isterseniz, **PackageID** 'yi alan ve `msiexec /X`kullanarak aracıyı kaldırabilen aşağıdaki örneği kullanabilirsiniz.

   `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` kayıt defteri anahtarını bulun ve **PackageID**' i bulun. Ardından `msiexec`kullanarak aracıyı kaldırabilirsiniz.

   Aşağıdaki örnekte aracının kaldırılması gösterilmektedir.

   ```powershell
   Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
   Get-ItemProperty | `
   Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
   ForEach-Object {MsiExec.exe /Quiet /X "$($_.PsChildName)"}
   ```

   Linux 'ta Aracıyı kaldırmak için aşağıdaki komutu yürütün.

   ```bash
   sudo apt purge hybridagent
   ```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bağlı makinelere Ilke atama](../../governance/policy/assign-policy-portal.md)
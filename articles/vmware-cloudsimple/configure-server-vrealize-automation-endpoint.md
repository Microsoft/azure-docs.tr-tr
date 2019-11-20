---
title: CloudSimple tarafından Azure VMware çözümü-Vfarkında otomasyonu için özel bulutta vCenter ayarla
description: VMware Vfarkında otomasyonu için bir uç nokta olarak CloudSimple özel bulutunuzda bir VMware vCenter Server ayarlamayı açıklar
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: df73acfc469a8b7b5329b61095aefdbd73baafd4
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642402"
---
# <a name="set-up-vcenter-on-your-private-cloud-for-vmware-vrealize-automation"></a>VMware Vfarkında otomasyonu için özel bulutunuzda vCenter 'ı ayarlama

VMware Vfarkında Automation için bir uç nokta olarak CloudSimple özel bulutunuzda bir VMware vCenter Server ayarlayabilirsiniz.

## <a name="before-you-begin"></a>Başlamadan önce

VCenter sunucusunu yapılandırmadan önce bu görevleri doldurun:

* Şirket içi ortamınız ile özel bulutunuz arasında [siteden sıteye VPN bağlantısı](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) yapılandırın.
* [Şirket ıçı DNS isteklerinin DNS Isteklerini](on-premises-dns-setup.md) özel bulutunuzun DNS sunucularına iletmeyi yapılandırın.
* Aşağıdaki tabloda listelenen izinler kümesiyle bir Vanautomation IaaS yönetim kullanıcısı oluşturmak için bir [destek isteği](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) gönderme.

| Öznitelik Değeri | İzin |
------------ | ------------- |  
| Veri deposu |  Alan ayır <br> Veri deposuna gözatamıyorum |
| Veri deposu kümesi | Veri deposu kümesi yapılandırma |
| Klasör | Klasör Oluştur <br>Klasör Sil |
| Genel |  Özel öznitelikleri Yönet<br>Özel öznitelik ayarla |
| Ağ | Ağ ata |
| İzinler | Izinleri Değiştir |
| Resource | VM 'yi kaynak havuzuna ata<br>Sanal makineyi kapalı geçir<br>Sanal makinede güç geçişi gerçekleştir |
| Sanal makine envanteri |  Mevcut kaynaktan oluştur<br>Yeni Oluştur<br>Taşı<br>Kaldır | 
| Sanal makine etkileşimi |  CD medyasını yapılandırma<br>Konsol etkileşimi<br>Cihaz Bağlantısı<br>Kapatıldı<br>Açma<br>Sıfırla<br>Askıya alma<br>Araç yüklemesi | 
| Sanal makine yapılandırması |  Mevcut diski Ekle<br>Yeni Disk Ekle<br>Ekle veya Kaldır<br>Diski Kaldır<br>Gelişmiş<br>Değişiklik CPU sayısı<br>Kaynağı değiştir<br>Sanal diski Genişlet<br>Disk Değişiklik İzleme<br>Bellek<br>Cihaz ayarlarını değiştir<br>Yeniden Adlandır<br>Ek açıklama ayarla (sürüm 5,0 ve üzeri)<br>Ayarlar<br>Swapfile yerleşimi |
| Sağlama |  Özelleştir<br>Şablonu kopyala<br>Sanal makineyi Kopyala<br>Şablon dağıtma<br>Özelleştirme özelliklerini okuyun |
| Sanal makine durumu | Anlık görüntü oluştur<br>Anlık görüntüyü kaldır<br>Anlık görüntüye dön |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>Şirket içi ortamınıza Vfarkında Otomasyonu 'nı yüklemeyin

1. VNET basit desteğinin sizin için oluşturulmuş IaaS Yöneticisi olarak Vfarkında Automation IaaS sunucu gereci 'nda oturum açın.
2. Vanautomation uç noktası için bir vSphere Aracısı dağıtın.
    1. Https://*vra-URL*: 5480/Installer adresine gidin; burada *vra-URL* , VANAUTOMATION yönetim Kullanıcı ARABIRIMINE erişmek için kullandığınız URL 'dir.
    2. Yükleyiciyi indirmek için **IaaS yükleyicisi** ' ne tıklayın.<br>
    Yükleyici dosyasının adlandırma kuralı setup_*vra-URL*@5480.exe'dir.
    3. Yükleyiciyi çalıştırın. Hoş Geldiniz ekranında **İleri**' ye tıklayın.
    4. EULA 'Yı kabul edin ve **İleri**' ye tıklayın.
    5. Oturum açma bilgilerini girin, **sertifikayı kabul et**' e ve ardından **İleri**' ye tıklayın.
    ![vRA kimlik bilgileri](media/configure-vra-endpoint-login.png)
    6. **Özel yüklemeleri** ve **proxy aracılarını** seçip **İleri**' ye tıklayın.
    ![vRA yüklemesi türü](media/configure-vra-endpoint-install-type.png)
    7. IaaS sunucusu oturum açma bilgilerini girin ve **İleri**' ye tıklayın. Active Directory kullanıyorsanız, Kullanıcı adını **etkialanı \ Kullanıcı** biçiminde girin. Aksi takdirde, **user@domain** biçimini kullanın.
    ![vRA oturum açma bilgileri](media/configure-vra-endpoint-account.png)
    8. Ara sunucu ayarları için, **Aracı türü**Için **vSphere** girin. Aracı için bir ad girin.
    9. **Yönetim hizmeti ana** bilgisayarına ve **model Yöneticisi Web hizmeti ana bilgisayar** ALANLARıNA IaaS sunucu FQDN 'sini girin. Her FQDN değeri bağlantısını test etmek için **Test** ' e tıklayın. Test başarısız olursa, IaaS sunucusu ana bilgisayar adının çözümlenmesi için DNS ayarlarınızı değiştirin.
    10. Özel bulut için vCenter Server uç noktası için bir ad girin. Yapılandırma sürecinde daha sonra kullanmak için adı kaydedin.

        ![vRA yüklemesi proxy 'si](media/configure-vra-endpoint-proxy.png)

    11. **İleri**'ye tıklayın.
    12. **Yükle**'ye tıklatın.

## <a name="configure-the-vsphere-agent"></a>VSphere aracısını yapılandırma

1. Https://*vra-URL*/vcac adresine gidin ve **configurationadmin**olarak oturum açın.
2. **Altyapı** > **uç**noktalarıuç > **noktalarını**seçin.
3. **Yeni** > **sanal**vSphere öğesini seçin. > 
4. Önceki yordamda belirttiğiniz vSphere uç nokta adını girin.
5. **Adres**Için, özel bulut vCenter Server URL 'sini https://*vCenter-FQDN*/SDK biçiminde girin; burada *vCenter-FQDN* , vCenter sunucusunun adıdır.
6. Sanal bir Otomasyon IaaS yönetim kullanıcısına yönelik kimlik bilgilerini, CloudSimple desteğinin sizin için oluşturulduğunu girin.
7. Kullanıcı kimlik bilgilerini doğrulamak için **Bağlantıyı Sına** ' ya tıklayın. Test başarısız olursa, URL, hesap bilgileri ve [uç nokta adı](#verify-the-endpoint-name) ve test ' i doğrulayın.
8. Başarılı bir testten sonra, vSphere uç noktasını oluşturmak için **Tamam** ' ı tıklatın.
    ![vRA uç noktası yapılandırma erişimi](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>Uç nokta adını doğrulama

Doğru vCenter sunucusu uç noktası adını belirlemek için aşağıdakileri yapın:

1. IaaS gereci üzerinde bir komut istemi açın.
2. Dizini C:\Program Files (x86) \VMware\vCAC\Agents\agent-name olarak değiştirin; burada *Aracı adı* vCenter Server uç noktasına atadığınız addır.
3. Aşağıdaki komutu çalıştırın.

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

Çıktı aşağıdakine benzer. `managementEndpointName` Alanın değeri, uç nokta adıdır.

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```

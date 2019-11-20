---
title: AWS’de bir Service Fabric kümesi için altyapı oluşturma öğreticisi - Azure Service Fabric | Microsoft Docs
description: Bu öğreticide bir Service Fabric kümesi çalıştırmak için AWS altyapısını nasıl ayarlayacağınızı öğreneceksiniz.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 048051a612793cbe82f82fbde482ed470ad3758c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177832"
---
# <a name="tutorial-create-aws-infrastructure-to-host-a-service-fabric-cluster"></a>Öğretici: Bir Service Fabric kümesini barındıracak AWS altyapısı oluşturma

Service Fabric tek başına kümeleri, kendi ortamınızı seçme ve Service Fabric’in benimsediği "her işletim sistemi, her bulut" yaklaşımının bir parçası olarak bir küme oluşturma seçeneği sunar. Bu öğretici serisinde, AWS üzerinde barındırılan bir tek başına küme oluşturacak ve içine bir uygulama yükleyeceksiniz.

Bu öğretici, bir dizinin birinci bölümüdür. Bu makalede, tek başına Service Fabric kümenizi barındırmak için gerekli AWS kaynaklarını oluşturacaksınız. Gelecek makalelerde, Service Fabric tek başına paketini yüklemeniz, kümenize bir örnek uygulama yüklemeniz ve son olarak kümenizi temizlemeniz gerekir.

Serinin birinci bölümünde şunları öğrenirsiniz:

> [!div class="checklist"]
> * Bir EC2 örnekleri kümesi oluşturma
> * Güvenlik grubunu değiştirme
> * Örneklerden birinde oturum açın
> * Service Fabric örneğini hazırlama

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için bir AWS hesabınızın olması gerekir.  Henüz bir hesabınız yoksa, bir tane oluşturmak için [AWS konsoluna](https://aws.amazon.com/) gidin.

## <a name="create-ec2-instances"></a>EC2 örnekleri oluşturma

AWS konsolunda oturum açın > > **EC2 sanal sunucuları** ara kutusuna

![AWS konsol araması][aws-console]

**Örneği Başlat**’ı seçin, sonraki ekranda Microsoft Windows Server 2016 Temel’in yanındaki **Seç** öğesini seçin.

![EC2 örneği seçimi][aws-ec2instance]

**t2.medium** öğesini ve ardından **İleri: Örnek Ayrıntılarını Yapılandırma**’yı seçin, sonraki ekranda örnek sayısını `3` olarak değiştirin, ardından **Gelişmiş Ayrıntılar**’ı seçerek bu seçimi genişletin.

Service Fabric’te sanal makinelerinizi birbirine bağlamak için, altyapınızı barındıran VM’lerin aynı kimlik bilgilerine sahip olması gerekir.  Tutarlı kimlik bilgileri elde etmenin iki yaygın yolu vardır: tümünü aynı etki alanına eklemek veya her sanal makinede aynı yönetici parolasını ayarlamak.  Bu öğreticide, EC2 örneklerinin tümünü aynı parolaya ayarlamak için bir kullanıcı verileri betiği kullanacaksınız.  Bir üretim ortamında, ana bilgisayarların bir Windows etki alanına eklenmesi daha güvenlidir.

Aşağıdaki betiği konsoldaki kullanıcı veri alanına girin:

```powershell
<powershell>
$user = [adsi]"WinNT://localhost/Administrator,user"
$user.SetPassword("serv1ceF@bricP@ssword")
$user.SetInfo()
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
</powershell>
```

PowerShell betiğini girdikten sonra **Gözden Geçir ve Başlat**’ı seçin

![EC2 gözden geçirme ve başlatma][aws-ec2configure2]

Gözden geçirme ekranında **Başlat**’ı seçin.  Ardından açılır listeyi **Anahtar çifti olmadan ilerle** olarak değiştirin ve parolayı bildiğinizi belirten onay kutusunu işaretleyin.

![AWS anahtar çifti seçimi][aws-keypair]

Son olarak, **Örnekleri Başlat** ve ardından **Örnekleri Görüntüle**’yi seçin.  Service Fabric kümenizin temelini oluşturdunuz. Şimdi örnekleri Service Fabric yapılandırmasına hazırlamak için birkaç son yapılandırma eklemeniz gerekiyor.

## <a name="modify-the-security-group"></a>Güvenlik grubunu değiştirme

Service Fabric, kümenizdeki bağlantı noktaları arasında birkaç bağlantı noktasının açık olmasını gerektirir. Bu bağlantı noktalarını AWS altyapısında açmak için, oluşturduğunuz örneklerden birini seçin. Sonra güvenlik grubunun adını seçin; örneğin, **launch-wizard-1**. Şimdi, **Gelen** sekmesini seçin.

Bu bağlantı noktalarını genel erişime açmayı önlemek için yalnızca aynı güvenlik grubundaki ana bilgisayarlara açın. Güvenlik grubu kimliğini not alın (bu örnekte **sg-c4fb1eba**).  Ardından **Düzenle**’yi seçin.

Sonra, güvenlik grubuna hizmet bağımlılıkları için dört kural ve Service Fabric için üç kural daha ekleyin. Birinci kural, temel bağlantı denetimleri için ICMP trafiğine izin vermektir. Diğer kurallar, uzak kayıt defterini etkinleştirmek için gerekli bağlantı noktalarını açar.

Birinci kural için **Kural Ekle**’yi ve sonra açılır listeden **Tüm ICMP - IPv4**’ü seçin. Özel seçeneğinin yanındaki giriş kutusunu seçin ve yukarıdaki güvenlik grubu kimliğinizi girin.

Son üç bağımlılık için de benzer bir işlemi izlemeniz gerekir.  **Kural Ekle**’yi ve açılır listeden **Özel TCP Kuralı**’nı seçin, bağlantı noktası aralığına ise her kural için `135`, `137-139` ve `445` değerlerinden birini girin. Son olarak, kaynak kutusuna güvenlik grubu kimliğinizi girin.

![Güvenlik grubu bağlantı noktaları][aws-ec2securityports]

Bağımlılıkların bağlantı noktaları açıldıktan sonra aynı işlemi Service Fabric’in iletişim kurmak için kullandığı bağlantı noktaları için de yapmanız gerekir. **Kural Ekle**’yi seçin, açılır listeden **Özel TCP Kuralı**’nı seçin ve bağlantı noktası aralığına kaynak kutusundaki güvenlik grubu için `20001-20031` girin.

Ardından, kısa ömürlü bağlantı noktası aralığı için kural ekleyin.  **Kural Ekle**’yi ve açılır listeden **Özel TCP Kuralı**’nı seçin, bağlantı noktası aralığına ise `20606-20861` girin. Son olarak, kaynak kutusuna güvenlik grubu kimliğinizi girin.

Service Fabric kümenizi kişisel bilgisayarınızdan yönetebilmek için, Service Fabric’in son iki kuralını genel erişime açın. **Kural Ekle**’yi ve açılır listeden **Özel TCP Kuralı**’nı seçin, bağlantı noktası aralığına ise her kural için `19000-19003` ve `19080-19081` değerlerinden birini girin, ardından Kaynak açılır listesini Her Yer olarak değiştirin.

Son olarak, dağıtıldığında uygulamayı görebilmeniz için 8080 numaralı bağlantı noktasını açmanız gerekir. **Kural Ekle**’yi ve açılır listeden **Özel TCP Kuralı**’nı seçin, bağlantı noktası aralığına ise her kural için `8080` girin, ardından Kaynak açılır listesini Her Yer olarak değiştirin.

Tüm kurallar artık girilmiştir. **Kaydet**’i seçin.

## <a name="connect-to-an-instance-and-validate-connectivity"></a>Örneğe bağlanma ve bağlantıyı doğrulama

Güvenlik grubu sekmesinde soldaki menüden **Örnekler**’i seçin.  Oluşturduğunuz her bir örneği seçin ve aşağıdaki örnekler için özel IP adreslerinin `172.31.21.141` ve `172.31.20.163` kullandığına dikkat edin.

Tüm IP adreslerini elde ettikten sonra bağlanacak bir örnek seçin, örneğe sağ tıklayın ve **Bağlan**’ı seçin.  Buradan, bu belirli örneğin RDP dosyasını indirebilirsiniz.  **Uzak Masaüstü Dosyası**’nı seçin ve bu örnekte sonra uzak masaüstü bağlantınızı (RDP) kurmak için indirilen dosyayı açın.  Sorulduğunda parolanızı girin `serv1ceF@bricP@ssword`.

![Uzak Masaüstü Dosyasını İndir][aws-rdp]

Örneğinize başarıyla bağlandıktan sonra örnekler arasında bağlantı kurabilir ve ayrıca dosya paylaşabilirsiniz.  Tüm örneklerin IP adreslerini toplayın ve şu anda bağlı olmadığınız bir adresi seçin. **Başlat**’a gidin, `cmd` girip **Komut İstemi**’ni seçin.

Bu örneklerde RDP bağlantısı şu IP adresiyle kurulmuştur: 172.31.21.141. Bu durumda tüm bağlantı testleri diğer IP adresinde gerçekleştirilir: 172.31.20.163.

Temel bağlantının çalıştığını doğrulamak için ping komutunu kullanın.

```
ping 172.31.20.163
```

Çıktınız dört kez tekrar eden `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` gibi görünüyorsa, örnekler arasındaki bağlantınız çalışıyordur.  

## <a name="prep-instances-for-service-fabric"></a>Service Fabric örneklerini hazırlama

Sıfırdan oluşturuyorsanız fazladan birkaç adım uygulamanız gerekir.  Yani, uzak kayıt defterinin çalıştığını doğrulamanız ve önkoşul bağlantı noktalarını açmanız gerekir.

Kolaylaştırmak için, bu işlerin tümünü, kullanıcı veri betiğinizle örnekleri önyüklediğinizde eklemiştiniz.

Buradaki güvenlik duvarında bağlantı noktalarını açmak için şu PowerShell komutunu kullanın:

```powershell
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139
```

## <a name="next-steps"></a>Sonraki adımlar

Serinin birinci bölümünde üç EC2 örneğini başlatmayı ve Service Fabric yüklemesi için yapılandırmayı öğrendiniz:

> [!div class="checklist"]
> * Bir EC2 örnekleri kümesi oluşturma
> * Güvenlik grubunu değiştirme
> * Örneklerden birinde oturum açın
> * Service Fabric örneğini hazırlama

Service Fabric’i kümenizde yapılandırmak için serinin ikinci bölümüne ilerleyin.

> [!div class="nextstepaction"]
> [Service Fabric yükleme](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[aws-console]: ./media/service-fabric-tutorial-standalone-cluster/aws-console.png
[aws-ec2instance]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2instance.png
[aws-ec2configure2]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2configure2.png
[aws-rdp]: ./media/service-fabric-tutorial-standalone-cluster/aws-rdp.png
[aws-ec2securityports]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2securityports.png
[aws-keypair]: ./media/service-fabric-tutorial-standalone-cluster/aws-keypair.png
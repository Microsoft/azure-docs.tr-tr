---
title: Etkileyen Linux VM için özel ölçümler toplayın telegraf Agent
description: Etkileyen bir Linux VM için özel ölçümler toplama telegraf Aracısı
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 05cc1dcb2a6fa4e7790fa57cd2136d21d94b8a0b
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200531"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Etkileyen bir Linux VM için özel ölçümler toplama telegraf Aracısı

Azure Izleyici 'yi kullanarak, uygulama telemetriniz, Azure kaynaklarınızda çalışan bir aracı veya hatta iç izleme sistemleri aracılığıyla özel ölçümler toplayabilirsiniz. Ardından, bunları doğrudan Azure Izleyici 'ye gönderebilirsiniz. Bu makalede, Azure 'daki bir Linux sanal makinesine [etkileyen](https://www.influxdata.com/) bir Linux VM 'de etkileyen ve aracıyı Azure izleyici 'ye yayımlamak üzere yapılandırmanın nasıl yapılandırılacağı hakkında yönergeler sağlanmaktadır. 

## <a name="influxdata-telegraf-agent"></a>Etkileyen XData telegraf Aracısı 

[Telegraf](https://docs.influxdata.com/telegraf/v1.7/) , 150 farklı kaynaklardan ölçüm toplamayı sağlayan bir eklenti temelli aracıdır. SANAL makinenizde çalışan iş yüklerine bağlı olarak, bir aracıyı, ölçümleri toplamak için özel giriş eklentilerinden faydalanmak üzere yapılandırabilirsiniz. MySQL, NGıNX ve Apache örnekleri aşağıda verilmiştir. Çıkış eklentilerini kullanarak, aracı seçtiğiniz hedeflere yazabilir. Telegraf Aracısı, Azure Izleyici özel ölçümleriyle REST API doğrudan tümleştirilmiştir. Azure Izleyici çıkış eklentisini destekler. Bu eklentiyi kullanarak, aracı Linux sanal makinenizde iş yüküne özgü ölçümler toplayabilir ve bunları Azure Izleyici 'ye özel ölçümler olarak gönderebilir. 

 ![Telgraf aracısına genel bakış](./media/collect-custom-metrics-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>Özel ölçümleri gönder 

Bu öğretici için Ubuntu 16,04 LTS işletim sistemini çalıştıran bir Linux sanal makinesi dağıyoruz. Telegraf Aracısı çoğu Linux işletim sistemi için desteklenir. IBU ve RPM paketlerinin her ikisi de [etkileyen](https://portal.influxdata.com/downloads)Linux ikilileri ile birlikte kullanılabilir. Ek yükleme yönergeleri ve seçenekleri için bu [telegraf yükleme kılavuzuna](https://docs.influxdata.com/telegraf/v1.8/introduction/installation/) bakın. 

[Azure Portal](https://portal.azure.com)’ında oturum açın.

Yeni bir Linux sanal makinesi oluşturun: 

1. Sol taraftaki gezinti bölmesinden **kaynak oluştur** seçeneğini belirleyin. 
1. **Sanal makine**araması yapın.  
1. **Ubuntu 16,04 LTS** ' yi seçin ve **Oluştur**' u seçin. 
1. **MyTelegrafVM**gıbı bir VM adı sağlayın.  
1. Disk türünü **SSD**olarak bırakın. Daha sonra **azureuser**gibi bir **Kullanıcı adı**sağlayın. 
1. **Kimlik doğrulama türü**için **parola**' yı seçin. Daha sonra bu VM 'ye SSH için kullanacağınız bir parola girin. 
1. **Yeni kaynak grubu oluşturmayı**seçin. Daha sonra **Myresourcegroup**gibi bir ad sağlayın. **Konumunuzu**seçin. Sonra **Tamam**’ı seçin. 

    ![Ubuntu sanal makinesi oluşturma](./media/collect-custom-metrics-linux-telegraf/create-vm.png)

1. VM için bir boyut seçin. Örneğin, **İşlem türü** veya **Disk türü**’ne göre filtreleyebilirsiniz. 

    ![Sanal makine boyutu telgraf aracısına genel bakış](./media/collect-custom-metrics-linux-telegraf/vm-size.png)

1. **Ağ > ağ** **güvenlik grubu** 'ndaki **Ayarlar** sayfasında, **ortak gelen bağlantı noktalarını seçin** > **http** ve **SSH (22)** öğesini seçin. Diğer varsayılan ayarları olduğu gibi bırakın ve **Tamam**’ı seçin. 

1. Özet sayfasında **Oluştur**’u seçerek sanal makine dağıtımını başlatın. 

1. VM, Azure portalı panosuna sabitlenir. Dağıtım tamamlandıktan sonra VM Özeti otomatik olarak açılır. 

1. VM bölmesinde **kimlik** sekmesine **gidin. sanal**makinenizde sistem tarafından atanan bir kimlik ayarlanmış olduğundan emin olun. 
 
    ![Telegraf VM kimlik önizlemesi](./media/collect-custom-metrics-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>VM’ye bağlanma 

VM ile bir SSH bağlantısı oluşturun. VM’nizin genel bakış sayfasından **Bağlan** düğmesini seçin. 

![Telegraf VM genel bakış sayfası](./media/collect-custom-metrics-linux-telegraf/connect-VM-button2.png)

**Sanal makineye bağlan** sayfasında, 22 numaralı bağlantı noktası üzerinden DNS adına göre bağlanmak için varsayılan seçenekleri olduğu gibi bırakın. **VM yerel hesabı 'nı kullanarak oturum açma**bölümünde bir bağlantı komutu gösterilir. Komutu kopyalamak için düğmeyi seçin. Aşağıdaki örnekte SSH bağlantı komutunun nasıl göründüğü gösterilmiştir: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

SSH bağlantısı komutunu, Windows üzerinde Ubuntu üzerinde Azure Cloud Shell veya Bash gibi bir kabuğa yapıştırın veya bağlantıyı oluşturmak için seçtiğiniz bir SSH istemcisini kullanın. 

## <a name="install-and-configure-telegraf"></a>Telegraf 'yi yükleyip yapılandırın 

Telegraf de, paketini sanal makineye yüklemek için SSH oturumınızdan aşağıdaki komutları çalıştırın: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Telegraf 'in yapılandırma dosyası telegraf 'ın işlemlerini tanımlıyor. Varsayılan olarak, **/etc/telegraf/telegraf.conf**yoluna örnek bir yapılandırma dosyası yüklenir. Örnek yapılandırma dosyasında tüm olası giriş ve çıkış eklentileri listelenir. Ancak, özel bir yapılandırma dosyası oluşturacağız ve aracının bunu aşağıdaki komutları çalıştırarak kullanması gerekir: 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> Yukarıdaki kod yalnızca iki giriş eklentisi sunar: **CPU** ve **bellek**. Makinenizde çalışan iş yüküne bağlı olarak daha fazla giriş eklentisi ekleyebilirsiniz. Docker, MySQL ve NGıNX örnekleri örnektir. Giriş eklentilerinin tam listesi için **ek yapılandırma** bölümüne bakın. 

Son olarak, aracının yeni yapılandırmayı kullanmaya başlamasını sağlamak için, aşağıdaki komutları çalıştırarak aracının durdurulmasını ve başlamasını zorlıyoruz: 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Artık Aracı, belirtilen giriş eklentilerinden her bir ölçüm toplayacak ve bunları Azure Izleyici 'ye yayacaktır. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Telegraf ölçümlerini Azure portal çizme 

1. [Azure portalı](https://portal.azure.com) açın. 

1. Yeni **izleyici** sekmesine gidin. Ardından **ölçümler**' i seçin.  

     ![İzleme-ölçümler (Önizleme)](./media/collect-custom-metrics-linux-telegraf/metrics.png)

1. Kaynak seçicideki sanal makineyi seçin.

     ![Ölçüm grafiği](./media/collect-custom-metrics-linux-telegraf/metric-chart.png)

1. **Telegraf/CPU** ad alanını seçin ve **usage_system** ölçümünü seçin. Bu ölçümdeki boyutlara göre filtreleme veya bunları bölme seçeneklerinden birini belirleyebilirsiniz.  

     ![Ad alanı ve ölçüm seçin](./media/collect-custom-metrics-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Ek yapılandırma 

Önceki izlenecek yol, birkaç temel giriş eklentisinden ölçümleri toplamak üzere telegraf aracısının nasıl yapılandırılacağı hakkında bilgi sağlar. Telegraf Aracısı, bazı destekleyici ek yapılandırma seçenekleri ile 150 fazla giriş eklentisi desteği içerir. Etkileyen, [desteklenen eklentilerin bir listesini](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/) ve [bunların nasıl yapılandırılacağı](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/)hakkında yönergeler yayımlamıştır.  

Ayrıca, bu kılavuzda, aracının dağıtıldığı VM hakkında ölçümleri göstermek için telegraf aracısını kullandınız. Telegraf Aracısı, diğer kaynaklar için bir toplayıcı ve bir ölçüm ileticisi olarak da kullanılabilir. Aracıyı diğer Azure kaynakları için ölçümleri göstermek üzere nasıl yapılandıracağınızı öğrenmek için bkz. [telegraf Için Azure Izleyici özel ölçüm çıktısı](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md).  

## <a name="clean-up-resources"></a>Kaynakları temizleme 

Artık gerekli olmadığında kaynak grubunu, sanal makineyi ve tüm ilgili kaynakları silebilirsiniz. Bunu yapmak için, sanal makine için kaynak grubunu seçin ve **Sil**' i seçin. Ardından, silinecek kaynak grubunun adını onaylayın. 

## <a name="next-steps"></a>Sonraki adımlar
- [Özel ölçümler](metrics-custom-overview.md)hakkında daha fazla bilgi edinin.




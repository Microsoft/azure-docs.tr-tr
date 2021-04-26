---
title: Apache Cassandra için Azure yönetilen örneğinden alınan ölçümleri görselleştirmek için Grafana 'ı yapılandırın
description: Apache Cassandra kümesi için Azure yönetilen örneğinden alınan ölçümleri görselleştirmek üzere bir VM 'de Grafana 'i yüklemeyi ve yapılandırmayı öğrenin.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: ed0ff343595429a4cb81fef280203f1180eeb098
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101746584"
---
# <a name="configure-grafana-to-visualize-metrics-emitted-from-the-managed-instance-cluster"></a>Yönetilen örnek kümesinden alınan ölçümleri görselleştirmek için Grafana 'ı yapılandırın

> [!IMPORTANT]
> Apache Cassandra için Azure yönetilen örneği şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Apache Cassandra kümesi için bir Azure yönetilen örneği dağıttığınızda, hizmet çeşitli istemci araçları tarafından tüketilen [Prometheus](https://prometheus.io/) 'yi barındıran bir sunucu sağlar. Prometheus, açık kaynaklı bir izleme çözümüdür. Yönetilen örnek ölçümleri yayacaktır ve 10 dakika ya da 10 GB veri tutar (ilk olarak eşiğe ulaşıldığında). Bu makalede, yönetilen örnek kümesinden alınan ölçümleri görselleştirmek için Grafana nasıl yapılandırılacağı açıklanır. Ölçümleri görselleştirmek için aşağıdaki görevler gereklidir:

* Yönetilen örneğin mevcut olduğu Azure sanal ağı içinde bir Ubuntu sanal makinesi dağıtın.
* Panolar derlemek ve Prometheus 'tan yayılan ölçümleri görselleştirmek için açık kaynak [Grafana aracını](https://grafana.com/grafana/) yükler.

## <a name="deploy-a-ubuntu-server"></a>Ubuntu sunucusu dağıtma

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. Yönetilen örnek kümenizin bulunduğu kaynak grubuna gidin. **Ubuntu Server 18,04 LTS** görüntüsünü **Ekle** ve ara ' yı seçin:

   :::image type="content" source="./media/visualize-prometheus-grafana/select-ubuntu-image.png" alt-text="Azure portal Ubuntu sunucu görüntüsünü bulun." border="true":::

1. Görüntüyü seçin ve **Oluştur**' u seçin.

1. **Sanal makine oluştur** dikey penceresinde, aşağıdaki alanlar için değerler girin, diğer alanlar için varsayılan değerleri bırakabilirsiniz:

   * **Sanal makine adı** -VM için bir ad girin.
   * **Bölge** -sanal ağınızın dağıtıldığı bölgeyi seçin.

   :::image type="content" source="./media/visualize-prometheus-grafana/create-vm-ubuntu.png" alt-text="Ubuntu sunucu görüntüsüne sahip bir VM oluşturmak için formu doldurun." border="true":::

1. **Ağ** sekmesinde, yönetilen örneğinizin dağıtıldığı sanal ağa gelen seçin:

   :::image type="content" source="./media/visualize-prometheus-grafana/configure-networking-details.png" alt-text="Ubuntu sunucusunun ağ ayarlarını yapılandırın." border="true":::

1. Son olarak Grafana sunucunuzu oluşturmak için **gözden geçir + oluştur** ' u seçin.

## <a name="install-grafana"></a>Grafana 'i yükler

1. Azure portal, yönetilen örneği ve Grafana sunucusunu dağıttığınız sanal ağı açın. **Cassandra-atlaya(örnek 0)** adlı bir sanal makine ölçek kümesi örneği görmeniz gerekir. Bu Prometheus ölçümleri bu sanal makine ölçek kümesinde barındırılır. Bu örneğin IP adresini bir yere göz önünde oluşturun:

   :::image type="content" source="./media/visualize-prometheus-grafana/prometheus-instance-address.png" alt-text="Prometheus örneğinin IP adresini alın." border="true":::

1. SSH aracılığıyla bağlanmak için [Azure CLI](../virtual-machines/linux/ssh-from-windows.md#ssh-clients) veya tercih ettiğiniz istemci aracını kullanarak yeni oluşturduğunuz Ubuntu sunucunuza bağlanın.

1. VM 'ye bağlandıktan sonra, ölçümlerin barındırıldığı sanal makine ölçek kümesine bağlanmak için Grafana yükleyip yapılandırmanız gerekir. Bir komut istemi açın ve `nano` bir nano metin Düzenleyicisi açmak için komutunu girin. Aşağıdaki betiği metin düzenleyicisine yapıştırın, öğesini `<prometheus IP address>` önceki adımda KAYDETTIĞINIZ IP adresiyle değiştirdiğinizden emin olun:

   ```bash
   #!/bin/bash
   
   echo "Installing Grafana..."
   
   if ! $SSH dpkg -s grafana prometheus > /dev/null; then
       echo "Installing packages."
       echo 'deb https://packages.grafana.com/oss/deb stable main' | $SSH sudo tee /etc/apt/sources.list.d/grafana.list > /dev/null
       curl https://packages.grafana.com/gpg.key | $SSH sudo apt-key add -
       $SSH sudo apt-get update
       $SSH sudo apt-get install -y grafana prometheus
   else
       echo "Skipping package installation"
   fi
   
   echo "Configuring grafana"
   cat <<EOF | $SSH sudo tee /etc/grafana/provisioning/datasources/prometheus.yml
   apiVersion: 1
   datasources:
     - name: Prometheus
       type: prometheus
       url: https://<prometheus IP address>:9443
       jsonData:
         tlsSkipVerify: true
   EOF
   
   echo "Restarting Grafana"
   $SSH sudo systemctl enable grafana-server
   $SSH sudo systemctl restart grafana-server
   
   echo "Installing Grafana plugins"
   $SSH sudo grafana-cli plugins install natel-discrete-panel
   $SSH sudo grafana-cli plugins install grafana-polystat-panel
   $SSH sudo systemctl restart grafana-server
   ```

1. `ctrl + X`Dosyayı kaydetmek için yazın. Dosyayı adı verebilirsiniz `grafana.sh` .

1. `./grafana.sh`Grafana yüklemek için komut istemine komutu girin.

1. Yükleme tamamlandıktan sonra, aşağıdaki ekran görüntüsünde gösterildiği gibi sunucunun IP adresinde **3000 numaralı bağlantı noktasında** Grafana kullanılabilir olacaktır:

   :::image type="content" source="./media/visualize-prometheus-grafana/open-grafana-port.png" alt-text="3000 numaralı bağlantı noktasında Grafana çalıştırın." border="true":::

1. [Küme genel bakış](https://github.com/TheovanKraay/cassandra-exporter/blob/master/grafana/instaclustr/cluster-overview.json) JSON dosyası gibi Grafana Içinde Apache Cassandra için oluşturulan açık kaynaklı panolardan seçim yapabilirsiniz. Panonun JSON tanımını indirip Grafana 'e aktarın:

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-import.png" alt-text="Grafana JSON tanımını içeri aktarın." border="true":::

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-upload-json.png" alt-text="Grafana JSON tanımını karşıya yükleyin." border="true":::

1. Ardından, Cassandra yönetilen örnek kümenizi Seçilen Pano ile izleyebilirsiniz:

   :::image type="content" source="./media/visualize-prometheus-grafana/monitor-cassandra-metrics.gif" alt-text="Panodaki Cassandra yönetilen örnek ölçümlerini görüntüleyin." border="true":::

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Grafana kullanarak Prometheus içindeki ölçümleri görselleştirmek için panoları nasıl yapılandıracağınızı öğrendiniz. Aşağıdaki makalelerde Apache Cassandra için Azure yönetilen örneği hakkında daha fazla bilgi edinin:

* [Apache Cassandra için Azure yönetilen örneğine genel bakış](introduction.md)
* [Azure Databricks ile yönetilen Apache Spark kümesi dağıtma (Önizleme)](deploy-cluster-databricks.md)

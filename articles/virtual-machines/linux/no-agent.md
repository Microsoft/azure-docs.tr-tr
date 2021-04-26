---
title: Sağlama Aracısı olmadan Linux görüntüleri oluşturma
description: Azure 'da sağlama Aracısı olmadan Genelleştirilmiş Linux görüntüleri oluşturun.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/01/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: c7ca147f0a5b907ee0c5c66d53a219fe75ab2179
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102551717"
---
# <a name="creating-generalized-images-without-a-provisioning-agent"></a>Sağlama Aracısı olmadan Genelleştirilmiş görüntüler oluşturma

Microsoft Azure, [vmınuxagent](https://github.com/Azure/WALinuxAgent) veya [Cloud-Init](https://github.com/canonical/cloud-init) biçiminde Linux sanal makineleri için sağlama aracıları sağlar (önerilir). Ancak aşağıdaki gibi, sağlama aracınız için bu uygulamalardan birini kullanmak istemediğinizde bir senaryo olabilir:

- Linux sürümü/sürümü, Cloud-init/Linux aracısını desteklemez.
- Ana bilgisayar adı gibi belirli VM özelliklerinin ayarlanmış olması gerekir.

> [!NOTE] 
>
> Herhangi bir özellik ayarlanması veya herhangi bir sağlama biçimi olması gerekmez, özel bir görüntü oluşturmayı düşünmelisiniz.

Bu makalede, bir sağlama Aracısı yüklemeden, VM görüntünüzü Azure platform gereksinimlerini karşılamak ve ana bilgisayar adını ayarlamak için nasıl ayarlayabileceğinizi gösterir.

## <a name="networking-and-reporting-ready"></a>Ağ ve raporlama hazırlanıyor

Linux VM 'nizin Azure bileşenleriyle iletişim kurmasını sağlamak için, bir DHCP istemcisinin sanal ağdan bir ana bilgisayar IP 'si alması ve DNS çözümleme ve Yönlendirme yönetimi gerekir. Çoğu distro 'lara bu yardımcı programlarla birlikte kullanıma sunamaz. Linux 'tan alınmış satıcılar tarafından Azure 'da test edilmiş araçlar `dhclient` , `network-manager` `systemd-networkd` ve diğer kullanıcılar.

> [!NOTE]
>
> Şu anda bir sağlama Aracısı olmadan Genelleştirilmiş görüntüler oluşturmak yalnızca DHCP özellikli VM 'Leri destekler.

Ağ kurulumu ve yapılandırıldıktan sonra, "rapor hazırlanıyor" yapmanız gerekir. Bu, Azure 'un VM 'nin başarıyla sağlanmasına yönelik olduğunu bildirir.

> [!IMPORTANT]
>
> Azure 'a hazırlanma raporunun başarısız olması, sanal makinenizin yeniden başlatılmasını sağlar!

## <a name="demosample"></a>Demo/örnek

Bu demo, mevcut bir market görüntüsünü (Bu durumda, bir dekın bir sanal makine) alıp Linux aracısını (walınuxagent) nasıl kaldırabilmesinin yanı sıra, VM 'nin "Ready" olduğu Azure 'a raporlamak için en temel süreci de oluşturmayı gösterir.

### <a name="create-the-resource-group-and-base-vm"></a>Kaynak grubunu ve temel VM 'yi oluşturun:

```bash
$ az group create --location eastus --name demo1
```

Temel VM oluşturma:

```bash
$ az vm create \
    --resource-group demo1 \
    --name demo1 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo1 \
    --image "debian:debian-10:10:latest"
```

### <a name="remove-the-image-provisioning-agent"></a>Görüntü sağlama aracısını kaldırma

VM sağlandıktan sonra, içinde SSH oluşturabilir ve Linux aracısını kaldırabilirsiniz:

```bash
$ sudo apt purge -y waagent
$ sudo rm -rf /var/lib/waagent /etc/waagent.conf /var/log/waagent.log
```

### <a name="add-required-code-to-the-vm"></a>Gerekli kodu VM 'ye ekleme

Ayrıca, VM 'nin içinde, Azure Linux aracısını kaldırdığımız için, rapor etmeye yönelik bir mekanizma sağlamamız gerekir. 

#### <a name="python-script"></a>Python betiği

```python
import http.client
import sys
from xml.etree import ElementTree

wireserver_ip = '168.63.129.16'
wireserver_conn = http.client.HTTPConnection(wireserver_ip)

print('Retrieving goal state from the Wireserver')
wireserver_conn.request(
    'GET',
    '/machine?comp=goalstate',
    headers={'x-ms-version': '2012-11-30'}
)

resp = wireserver_conn.getresponse()

if resp.status != 200:
    print('Unable to connect with wireserver')
    sys.exit(1)

wireserver_goalstate = resp.read().decode('utf-8')

xml_el = ElementTree.fromstring(wireserver_goalstate)

container_id = xml_el.findtext('Container/ContainerId')
instance_id = xml_el.findtext('Container/RoleInstanceList/RoleInstance/InstanceId')
print(f'ContainerId: {container_id}')
print(f'InstanceId: {instance_id}')

# Construct the XML response we need to send to Wireserver to report ready.
health = ElementTree.Element('Health')
goalstate_incarnation = ElementTree.SubElement(health, 'GoalStateIncarnation')
goalstate_incarnation.text = '1'
container = ElementTree.SubElement(health, 'Container')
container_id_el = ElementTree.SubElement(container, 'ContainerId')
container_id_el.text = container_id
role_instance_list = ElementTree.SubElement(container, 'RoleInstanceList')
role = ElementTree.SubElement(role_instance_list, 'Role')
instance_id_el = ElementTree.SubElement(role, 'InstanceId')
instance_id_el.text = instance_id
health_second = ElementTree.SubElement(role, 'Health')
state = ElementTree.SubElement(health_second, 'State')
state.text = 'Ready'

out_xml = ElementTree.tostring(
    health,
    encoding='unicode',
    method='xml'
)
print('Sending the following data to Wireserver:')
print(out_xml)

wireserver_conn.request(
    'POST',
    '/machine?comp=health',
    headers={
        'x-ms-version': '2012-11-30',
        'Content-Type': 'text/xml;charset=utf-8',
        'x-ms-agent-name': 'custom-provisioning'
    },
    body=out_xml
)

resp = wireserver_conn.getresponse()
print(f'Response: {resp.status} {resp.reason}')

wireserver_conn.close()
```

#### <a name="generic-steps-without-using-python"></a>Genel adımlar (Python kullanılmadan)

SANAL makinenizde Python yüklü değilse veya kullanılabilir değilse, aşağıdaki adımlarla bu Yukarıdaki betik mantığını programlı bir şekilde yeniden oluşturabilirsiniz:

1. `ContainerId` `InstanceId` Kablolu sunucudan gelen yanıtı ayrıştırarak ve alın: `curl -X GET -H 'x-ms-version: 2012-11-30' http://168.63.129.16/machine?comp=goalstate` .

2. Aşağıdaki XML verilerini oluşturun, ayrıştırılmış `ContainerId` ve `InstanceId` Yukarıdaki adımdan ekleme:
   ```xml
   <Health>
     <GoalStateIncarnation>1</GoalStateIncarnation>
     <Container>
       <ContainerId>CONTAINER_ID</ContainerId>
       <RoleInstanceList>
         <Role>
           <InstanceId>INSTANCE_ID</InstanceId>
           <Health>
             <State>Ready</State>
           </Health>
         </Role>
       </RoleInstanceList>
     </Container>
   </Health>
   ```

3. Bu verileri kablolu sunucuya gönder: `curl -X POST -H 'x-ms-version: 2012-11-30' -H "x-ms-agent-name: WALinuxAgent" -H "Content-Type: text/xml;charset=utf-8" -d "$REPORT_READY_XML" http://168.63.129.16/machine?comp=health`

### <a name="automating-running-the-code-at-first-boot"></a>İlk önyüklemede kodu çalıştırmanın otomatikleştirilmesi

Bu tanıtımda, modern Linux distros 'de en yaygın init sistemi olan systemd kullanılmaktadır. Bu nedenle, bu rapor için uygun mekanizmanın en kolay ve en yerel yolu, bir systemd hizmet birimi oluşturmaktır. Aşağıdaki birim dosyasını öğesine ekleyebilirsiniz `/etc/systemd/system` (Bu örnek, birim dosyasını adlandırır `azure-provisioning.service` ):

```bash
[Unit]
Description=Azure Provisioning

[Service]
Type=oneshot
ExecStart=/usr/bin/python3 /usr/local/azure-provisioning.py
ExecStart=/bin/bash -c "hostnamectl set-hostname $(curl \
    -H 'metadata: true' \
    'http://169.254.169.254/metadata/instance/compute/name?api-version=2019-06-01&format=text')"
ExecStart=/usr/bin/systemctl disable azure-provisioning.service

[Install]
WantedBy=multi-user.target
```

Bu systemd hizmeti temel sağlama için üç şey yapar:

1. Azure 'a (başarıyla geldiğini göstermek için) yönelik raporlar.
1. Bu verileri [Azure Instance Metadata Service (ıMDS)](./instance-metadata-service.md)' den çekerek Kullanıcı tarafından sağlanan VM adından sanal makineyi yeniden adlandırır. **Göz önünde** IDS Ayrıca SSH ortak anahtarları gibi diğer [örnek meta verilerini](./instance-metadata-service.md#access-azure-instance-metadata-service)de sağlar, bu sayede ana bilgisayar adından fazlasını ayarlayabilirsiniz.
1. Kendisini, sonraki yeniden başlatmalar üzerinde değil yalnızca ilk önyüklemede çalışacak şekilde devre dışı bırakır.

Dosya sisteminde birimle birlikte etkinleştirmek için aşağıdakileri çalıştırın:

```bash
$ sudo systemctl enable azure-provisioning.service
```

Artık VM genelleştirilemez ve bundan sonra bir görüntü oluşturulabilir. 

#### <a name="completing-the-preparation-of-the-image"></a>Görüntünün hazırlanması Tamamlanıyor

Geliştirme makinenize geri döndüğünüzde, temel VM 'den görüntü oluşturmaya hazırlanmak için aşağıdakileri çalıştırın:

```bash
$ az vm deallocate --resource-group demo1 --name demo1
$ az vm generalize --resource-group demo1 --name demo1
```

Ve bu VM 'den görüntü oluşturun:

```bash
$ az image create \
    --resource-group demo1 \
    --source demo1 \
    --location eastus \
    --name demo1img
```

Artık görüntüden yeni bir VM (veya birden çok VM) oluşturmaya hazırız:

```bash
$ IMAGE_ID=$(az image show -g demo1 -n demo1img --query id -o tsv)
$ az vm create \
    --resource-group demo12 \
    --name demo12 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo12 \
    --image "$IMAGE_ID" 
    --enable-agent false
```

> [!NOTE]
>
> `--enable-agent` `false` Bu sanal makinede, görüntüden oluşturulacak olan walınuxagent olmadığından, olarak ayarlanması önemlidir.

Bu sanal makinenin başarıyla sağlanması gerekir. Yeni sağlama VM 'de oturum açmak için, rapor için Ready systemd hizmetinin çıkışını görmeniz gerekir:

```bash
$ sudo journalctl -u azure-provisioning.service
-- Logs begin at Thu 2020-06-11 20:28:45 UTC, end at Thu 2020-06-11 20:31:24 UTC. --
Jun 11 20:28:49 thstringnopa systemd[1]: Starting Azure Provisioning...
Jun 11 20:28:54 thstringnopa python3[320]: Retrieving goal state from the Wireserver
Jun 11 20:28:54 thstringnopa python3[320]: ContainerId: 7b324f53-983a-43bc-b919-1775d6077608
Jun 11 20:28:54 thstringnopa python3[320]: InstanceId: fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2
Jun 11 20:28:54 thstringnopa python3[320]: Sending the following data to Wireserver:
Jun 11 20:28:54 thstringnopa python3[320]: <Health><GoalStateIncarnation>1</GoalStateIncarnation><Container><ContainerId>7b324f53-983a-43bc-b919-1775d6077608</ContainerId><RoleInstanceList><Role><InstanceId>fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2</InstanceId><Health><State>Ready</State></Health></Role></RoleInstanceList></Container></Health>
Jun 11 20:28:54 thstringnopa python3[320]: Response: 200 OK
Jun 11 20:28:56 thstringnopa bash[472]:   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Jun 11 20:28:56 thstringnopa bash[472]:                                  Dload  Upload   Total   Spent    Left  Speed
Jun 11 20:28:56 thstringnopa bash[472]: [158B blob data]
Jun 11 20:28:56 thstringnopa2 systemctl[475]: Removed /etc/systemd/system/multi-user.target.wants/azure-provisioning.service.
Jun 11 20:28:56 thstringnopa2 systemd[1]: azure-provisioning.service: Succeeded.
Jun 11 20:28:56 thstringnopa2 systemd[1]: Started Azure Provisioning.
```

## <a name="support"></a>Destek

Kendi sağlama kodunuzu/aracınızı uygularsanız, bu kodun desteğine sahip olursunuz, Microsoft destek yalnızca kullanılamayan sağlama arabirimleriyle ilgili sorunları araştıracaktır. Bu alanda sürekli olarak geliştirmeler ve değişiklikler yapıyoruz, bu nedenle API değişikliklerini sağlamak için Cloud-init ve Azure Linux aracısındaki değişiklikleri izlemeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [Linux sağlama](provisioning.md).

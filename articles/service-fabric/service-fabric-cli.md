---
title: Azure Service Fabric CLI'sı ile çalışmaya başlama
description: Azure Service Fabric CLI’sını kullanmayı öğrenin. Kümeye bağlanmayı ve uygulamaları yönetmeyi öğrenin.
author: jeffj6123
ms.topic: conceptual
ms.date: 5/19/2020
ms.author: jejarry
ms.openlocfilehash: ff322cb3633616e9004bc8fa25b048b6f1263008
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046343"
---
# <a name="azure-service-fabric-cli"></a>Azure Service Fabric CLI'sı

Azure Service Fabric komut satırı arabirimi (CLI) Service Fabric varlıklarıyla etkileşimde bulunmak ve bunları yönetmek için kullanılan bir komut satırı yardımcı programıdır. Service Fabric CLI'sı, Windows veya Linux kümeleriyle kullanılabilir. Service Fabric CLI'sı Python'ın desteklendiği tüm platformlarda çalışır.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="prerequisites"></a>Önkoşullar

Yüklemeden önce ortamınızda Python ve pip uygulamalarının yüklü olduğundan emin olun. Daha fazla bilgi için [pip hızlı başlangıç belgelerine](https://pip.pypa.io/en/latest/quickstart/) ve resmi [Python yükleme belgelerine](https://wiki.python.org/moin/BeginnersGuide/Download) bakın.

CLı, Python 3. x ile önerilen Python 2,7 ve 3.6 + sürümlerini destekler.

### <a name="service-fabric-target-runtime"></a>Service Fabric hedef çalışma zamanı

Service Fabric CLI, Service Fabric SDK'sının en son çalışma zamanı sürümünü destekleyecek şekilde tasarlanmıştır. Yüklemeniz gereken CLI sürümünü belirlemek için aşağıdaki tabloyu kullanın:

| CLI sürümü   | desteklenen çalışma zamanı sürümü |
|---------------|---------------------------|
| En son (~ = 10) | En son (~ = 7,1)            |
| 9.0.0         | 7.1                       |
| 8.0.0         | 6.5                       |
| 7.1.0         | 6.4                       |
| 6.0.0         | 6.3                       |
| 5.0.0         | 6.2                       |
| 4.0.0         | 6.1                       |
| 3.0.0         | 6.0                       |
| 1.1.0         | 5.6, 5.7                  |

İsteğe bağlı olarak `pip install` komutuna `==<version>` ekleyerek yüklenecek hedef CLI sürümünü belirtebilirsiniz. Örneğin 1.1.0 sürümü için söz dizimi şu şekilde olacaktır:

```shell
pip install -I sfctl==1.1.0
```

Gerekli olduğunda aşağıdaki `pip install` komutunu önceden verilen komutla değiştirin.

Service Fabric CLI sürümleri hakkında daha fazla bilgi için bkz. [GitHub belgeleri](https://github.com/Azure/service-fabric-cli/releases).

## <a name="install-pip-python-and-the-service-fabric-cli"></a>pip, Python ve Service Fabric CLI'sını yükleme

pip ve Python'u platformunuza yüklemek için kullanabileceğiniz birçok yol vardır. Çok kullanılan işletim sistemlerinde Python 3 ve pip uygulamalarını hızlıca kurmak için aşağıdaki adımlardan faydalanabilirsiniz.

### <a name="windows"></a>Windows

Windows 10, Windows Server 2016 ve Windows Server 2012 R2 için standart resmi yükleme talimatlarını kullanın. Python yükleyici pip'i de varsayılan olarak yükler.

1. Resmi [Python İndirmeleri sayfasına](https://www.python.org/downloads/)gidin ve Python 3. x ' in en son sürümünü indirin.

2. Yükleyiciyi başlatın.

3. İsteminin en altında, **Python 3. x ' e yol Ekle**' yi seçin.

4. **Şimdi Yükle**'yi seçin ve yüklemeyi tamamlayın.

Şimdi yeni bir komut penceresi açıp hem Python hem de pip sürümünü öğrenebilirsiniz.

```shell
python --version
pip --version
```

Ardından, Azure Service Fabric CLı (sfctl) yüklemek ve CLı yardım sayfasını görüntülemek için aşağıdaki komutu çalıştırın:

```shell
pip install sfctl
sfctl -h
```

### <a name="ubuntu-and-windows-subsystem-for-linux"></a>Linux için Ubuntu ve Windows alt sistemi

Service Fabric CLI'sını yüklemek için aşağıdaki komutları çalıştırın:

```bash
sudo apt-get install python3
sudo apt-get install python3-pip
pip3 install sfctl
```

Ardından yükleme işlemini şu şekilde test edebilirsiniz:

```bash
sfctl -h
```

Aşağıdakine benzer bir komut bulunamadı hatası alırsanız:

`sfctl: command not found`

`~/.local/bin` öğesine `$PATH` kaynağından erişim sağlandığına emin olun:

```bash
export PATH=$PATH:~/.local/bin
echo "export PATH=$PATH:~/.local/bin" >> .shellrc
```

Linux için Windows alt sisteminin yüklemesi hatalı klasör izinleri nedeniyle başarısız olursa yükseltilmiş izinlerle tekrar denemeniz gerekebilir:

```bash
sudo pip3 install sfctl
```

### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (Service Fabric önizleme desteği)

Red Hat 'te CLı Service Fabric yüklemek için aşağıdaki komutları çalıştırın:

```bash
sudo yum install -y python38
sudo yum install python38-setuptools
sudo easy_install-3.4 pip
sudo pip3 install sfctl
```

Yüklemeyi test etmek için **Ubuntu ve Linux Için Windows alt sistemi** bölümünde bahsedilen adımlara başvurabilirsiniz

<a name = "cli-mac"></a>
### <a name="macos"></a>macOS

MacOS için [HomeBrew paket yöneticisini](https://brew.sh) kullanmanızı öneririz. HomeBrew yüklü değilse aşağıdaki komutu çalıştırarak yükleyin:

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Ardından terminalden aşağıdaki komutları çalıştırarak, en son Python 3. x, PIP ve Service Fabric CLı sürümünü yüklemelisiniz:

```bash
brew install python3
pip3 install sfctl
sfctl -h
```

## <a name="cli-syntax"></a>CLI söz dizimi

Komutlarda her zaman `sfctl` ön eki bulunur. Kullanabileceğiniz tüm komutlarla ilgili genel bilgi için `sfctl -h` kullanın. Tek bir komutla ilgili yardım için, `sfctl <command> -h` kullanın.

Komutlar, komut hedefinin fiilin veya eylemin öncesinde olduğu yinelenebilir bir yapıya sahiptir.

```shell
sfctl <object> <action>
```

Bu örnekte, `<action>` için `<object>` hedeftir.

## <a name="select-a-cluster"></a>Küme seçme

Herhangi bir işlem gerçekleştirmeden önce bağlantı kurulacak bir küme seçmeniz gerekir. Örneğin, `testcluster.com` adına sahip bir kümeyi seçip bu kümeye bağlanmak için aşağıdaki komutu çalıştırın:

> [!WARNING]
> Üretim ortamında güvenli olmayan Service Fabric kümelerini kullanmayın.

```shell
sfctl cluster select --endpoint http://testcluster.com:19080
```

Küme uç noktasının `http` veya `https` ön eki olmalıdır. HTTP ağ geçidi için bağlantı noktasını içermelidir. Bağlantı noktası ve adres, Service Fabric Explorer URL'si ile aynıdır.

Güvenliği bir sertifika ile sağlanan kümeler için PEM kodlu bir sertifika belirtebilirsiniz. Sertifika, tek bir dosya veya sertifika ve anahtar çifti olarak belirtilebilir. CA imzalı olmayan bir otomatik olarak imzalanan sertifika ise, CA doğrulamasını atlamak için `--no-verify` seçeneğini geçebilirsiniz.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Daha fazla bilgi için bkz. [Güvenli Azure Service Fabric kümesine bağlanma](service-fabric-connect-to-secure-cluster.md).

## <a name="basic-operations"></a>Temel işlemler

Küme bağlantı bilgileri birden çok Service Fabric CLI'sı oturumu arasında kalıcıdır. Service Fabric kümesini seçtikten sonra, kümede tüm Service Fabric komutlarını çalıştırabilirsiniz.

Örneğin, Service Fabric kümesinin sistem durumunu almak için aşağıdaki komutu kullanın:

```shell
sfctl cluster health
```

Komut şu çıktıyı verir:

```json
{
  "aggregatedHealthState": "Ok",
  "applicationHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "name": "fabric:/System"
    }
  ],
  "healthEvents": [],
  "nodeHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "id": {
        "id": "66aa824a642124089ee474b398d06a57"
      },
      "name": "_Test_0"
    }
  ],
  "unhealthyEvaluations": []
}
```

## <a name="tips-and-troubleshooting"></a>İpuçları ve sorun giderme

Aşağıda bazı öneriler ve sık karşılaşılan sorunları çözmek için ipuçları verilmiştir.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Sertifikayı PFX’ten PEM biçimine dönüştürme

Service Fabric CLI’sı de PEM (.pem uzantısı) dosyaları gibi istemci tarafı sertifikalarını destekler. Windows'dan PFX dosyalarını kullanıyorsanız, söz konusu sertifikaları PEM biçimine dönüştürmelisiniz. PFX dosyasını PEM dosyasına dönüştürmek için aşağıdaki komutu kullanın:

```shell
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Benzer şekilde, PEM dosyasından PFX dosyasına dönüştürmek için de aşağıdaki komutu kullanabilirsiniz (burada parola sağlanmaz):

```shell
openssl  pkcs12 -export -out Certificates.pfx -inkey Certificates.pem -in Certificates.pem -passout pass:'' 
```

Daha fazla bilgi için bkz. [OpenSSL belgeleri](https://www.openssl.org/docs/).

### <a name="connection-problems"></a>Bağlantı sorunları

Bazı işlemler aşağıdaki iletiyi oluşturabilir:

`Failed to establish a new connection`

Belirtilen küme uç noktasının kullanılabilir olduğunu ve dinlediğini doğrulayın. Ayrıca söz konusu konakta ve bağlantı noktasında Service Fabric Explorer Kullanıcı Arabiriminin kullanılabilir olduğunu da doğrulayın. Uç noktayı güncelleştirmek için `sfctl cluster select` kullanın.

### <a name="detailed-logs"></a>Ayrıntılı günlükler

Hata ayıkladığınız veya sorun bildirdiğiniz sırada ayrıntılı günlükler çoğunlukla yararlı olur. `--debug`Bayrak, çıkışın ayrıntı düzeyini artırır.

### <a name="command-help-and-syntax"></a>Komut yardımı ve söz dizimi

Belirli bir komut veya komut grubuyla ilgili yardım almak için `-h` bayrağını kullanın.

```shell
sfctl application -h
```

Bir örnek daha:

```shell
sfctl application create -h
```

## <a name="updating-the-service-fabric-cli"></a>Service Fabric CLI’sını güncelleştirme 

Service Fabric CLI’yı güncelleştirmek için aşağıdaki komutları çalıştırın (özgün yüklemenizde belirlediğiniz seçeneklere bağlı olarak `pip` öğesini `pip3` olarak değiştirin):

```shell
pip uninstall sfctl
pip install sfctl
```

## <a name="next-steps"></a>Sonraki adımlar

* [Bir uygulamayı Azure Service Fabric CLI ile dağıtma](service-fabric-application-lifecycle-sfctl.md)
* [Linux’ta Service Fabric kullanmaya başlama](service-fabric-get-started-linux.md)

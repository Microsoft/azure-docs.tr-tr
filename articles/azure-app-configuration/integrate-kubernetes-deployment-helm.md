---
title: Held kullanarak Kubernetes dağıtımı ile Azure uygulama yapılandırmasını tümleştirme
description: Held ile Kubernetes dağıtımında dinamik yapılandırmaların nasıl kullanılacağını öğrenin.
services: azure-app-configuration
author: shenmuxiaosen
manager: zhenlan
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: shuawan
ms.openlocfilehash: 6276fc2027e92d5b7baaf9237a928e7828a3b021
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775778"
---
# <a name="integrate-with-kubernetes-deployment-using-helm"></a>Held kullanarak Kubernetes dağıtımıyla tümleştirin

Held, Kubernetes 'te çalışan uygulamaları tanımlamak, yüklemek ve yükseltmek için bir yol sağlar. Helk grafiği bir Kubernetes uygulamasının örneğini oluşturmak için gereken bilgileri içerir. Yapılandırma,, *values. YAML* adlı bir dosyada grafiğin dışında saklanır. 

Yayın işlemi sırasında, HELI uygulamayı çalıştırmak için uygun yapılandırmayla grafiği birleştirir. Örneğin, *values. YAML* içinde tanımlanan değişkenlere, çalışan kapsayıcılar içinde ortam değişkenleri olarak başvurulabilir. HELI Ayrıca veri birimleri olarak veya ortam değişkenleri olarak kullanıma sunulacak Kubernetes gizli dizileri oluşturulmasını destekler.

Held çalıştırırken komut satırına ek YAML tabanlı yapılandırma dosyaları sağlayarak *values. YAML* içinde depolanan değerleri geçersiz kılabilirsiniz. Azure Uygulama yapılandırması, yapılandırma değerlerinin YAML dosyalarına verilmesini destekler. Bu dışarı aktarma özelliğinin dağıtımınız ile tümleştirilmesi, Kubernetes uygulamalarınızın uygulama yapılandırmasında depolanan yapılandırma değerlerinden yararlanmasını sağlar.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Held kullanarak Kubernetes 'e uygulama dağıttığınızda uygulama yapılandırmasındaki değerleri kullanın.
> * Uygulama yapılandırmasındaki bir Key Vault başvurusunu temel alarak bir Kubernetes gizli dizisi oluşturun.

Bu öğretici, Held ile Kubernetes yönetimi hakkında temel anladığını varsayar. [Azure Kubernetes hizmetinde](../aks/kubernetes-helm.md)Held ile uygulama yükleme hakkında daha fazla bilgi edinin.

## <a name="prerequisites"></a>Önkoşullar

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
- [Azure CLI](/cli/azure/install-azure-cli) 'yı (sürüm 2.4.0 veya üzeri) yükler
- [Held](https://helm.sh/docs/intro/install/) 'yi (sürüm 2.14.0 veya üzeri) yükler
- Bir Kubernetes kümesi.

## <a name="create-an-app-configuration-store"></a>Uygulama yapılandırma deposu oluşturma

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7.   >  Aşağıdaki anahtar-değer çiftlerini eklemek için yapılandırma Gezgini **Oluştur** ' u seçin:

    | Anahtar | Değer |
    |---|---|
    | Settings. Color | Beyaz |
    | Settings. Message | Azure Uygulama yapılandırmasından veriler |

    **Etiket** ve **içerik türü** şimdilik boş bırakın.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Uygulama yapılandırmasına Key Vault başvurusu ekleme
1. [Azure Portal](https://portal.azure.com) oturum açın ve ad **parolasıyla** ve **mypassword** değeriyle [Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) bir parola ekleyin. 
2. Önceki bölümde oluşturduğunuz uygulama yapılandırma deposu örneğini seçin.

3. **Yapılandırma Gezgini**' ni seçin.

4. **+**  >  **Anahtar Kasası başvurusu** oluştur ' u seçin ve ardından aşağıdaki değerleri belirtin:
    - **Anahtar**: **gizlilikler. Password** öğesini seçin.
    - **Etiket**: Bu değeri boş bırakın.
    - **Abonelik**, **kaynak grubu** ve **Anahtar Kasası**: önceki adımda oluşturduğunuz anahtar kasasında bunlara karşılık gelen değerleri girin.
    - **Gizli** dizi: önceki bölümde oluşturduğunuz gizli **parolayı** seçin.

## <a name="create-helm-chart"></a>Held grafiği oluşturma ##
İlk olarak, aşağıdaki komutla bir örnek HELI grafiği oluşturun
```console
helm create mychart
```

Helm, aşağıda gösterilen yapıyla MyChart adlı yeni bir dizin oluşturur. 

> [!TIP]
> Daha fazla bilgi edinmek için bu [grafik kılavuzunu](https://helm.sh/docs/chart_template_guide/getting_started/) izleyin.

```
mychart
|-- Chart.yaml
|-- charts
|-- templates
|   |-- NOTES.txt
|   |-- _helpers.tpl
|   |-- deployment.yaml
|   |-- ingress.yaml
|   `-- service.yaml
`-- values.yaml
```

Sonra, *Deployment. YAML* dosyasının **spec: Template: spec: containers** bölümünü güncelleştirin. Aşağıdaki kod parçacığı kapsayıcıya iki ortam değişkeni ekler. Değerlerini dağıtım zamanında dinamik olarak ayarlayacaksınız.

```yaml
env:
- name: Color
    value: {{ .Values.settings.color }}
- name: Message
    value: {{ .Values.settings.message }}
``` 

Güncelleştirme sonrasında *Deployment. YAML* dosyasının tamamı aşağıdaki gibi görünmelidir.

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "mychart.fullname" . }}
  labels:
    app.kubernetes.io/name: {{ include "mychart.name" . }}
    helm.sh/chart: {{ include "mychart.chart" . }}
    app.kubernetes.io/instance: {{ .Release.Name }}
    app.kubernetes.io/managed-by: {{ .Release.Service }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app.kubernetes.io/name: {{ include "mychart.name" . }}
      app.kubernetes.io/instance: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mychart.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          env:
            - name: Color
              value: {{ .Values.settings.color }}
            - name: Message
              value: {{ .Values.settings.message }}
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
{{ toYaml .Values.resources | indent 12 }}
    {{- with .Values.nodeSelector }}
      nodeSelector:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.affinity }}
      affinity:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.tolerations }}
      tolerations:
{{ toYaml . | indent 8 }}
    {{- end }}
```

Hassas verileri Kubernetes gizli dizileri olarak depolamak için, Şablonlar klasörü altına bir *gizli dizi. YAML* dosyası ekleyin.

> [!TIP]
> [Kubernetes gizliliklerini](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets)kullanma hakkında daha fazla bilgi edinin.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  password: {{ .Values.secrets.password | b64enc }}
```

Son olarak, aşağıdaki içerikle *values. YAML* dosyasını güncelleştirin ve *dağıtım. YAML* ve *gizlilikler. YAML* dosyalarında başvurulan yapılandırma ayarları ve gizli dizileri için varsayılan değerleri sağlayın. Uygulama yapılandırmasından alınan yapılandırma ile gerçek değerlerinin üzerine yazılacak.

```yaml
# settings will be overwritten by App Configuration
settings:
    color: red
    message: myMessage
```

## <a name="pass-configuration-from-app-configuration-in-helm-install"></a>Held yüklemede uygulama yapılandırmasından yapılandırmayı geçir ##
İlk olarak, yapılandırmayı uygulama yapılandırmasından bir *MyConfig. YAML* dosyasına indirin. Yalnızca ayarlarla başlayan anahtarları indirmek için bir anahtar filtresi kullanın **.** Anahtar filtresi, Key Vault başvuruların anahtarlarını hariç tutmak için yeterli değilse, **--Skip-keykasa** bağımsız değişkenini kullanarak bunları dışlayabilirsiniz. 

> [!TIP]
> [Dışa aktarma komutu](/cli/azure/appconfig/kv#az_appconfig_kv_export)hakkında daha fazla bilgi edinin. 

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path myConfig.yaml --key "settings.*"  --separator "." --format yaml
```

Ardından, gizli dizileri *Mygizlilikler. YAML* adlı bir dosyaya indirin. Komut satırı bağımsız değişkeni **--Resolve-keykasası** , Key Vault içindeki gerçek değerleri alarak Key Vault başvurularını çözer. Bu komutu, karşılık gelen Key Vault erişim izinleri olan kimlik bilgileriyle çalıştırmanız gerekir.

> [!WARNING]
> Bu dosya hassas bilgiler içerdiğinden dosyayı dikkatli tutun ve artık gerekli olmadığında temizleyin.

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path mySecrets.yaml --key "secrets.*" --separator "." --resolve-keyvault --format yaml
```

Oluşturduğunuz iki yapılandırma dosyasını geçirmek için helk Upgrade **-f** bağımsız değişkenini kullanın. Bunlar, *values. YAML* içinde tanımlanan yapılandırma değerlerini uygulama yapılandırmasından içe aktarılmış değerlerle geçersiz kılar.

```console
helm upgrade --install -f myConfig.yaml -f mySecrets.yaml "example" ./mychart 
```

Ayrıca, sabit anahtar değerlerini iletmek için Held yükseltmesi için **--set** bağımsız değişkenini de kullanabilirsiniz. **--Set** bağımsız değişkeninin kullanılması, diske gizli verilerin kalıcı olmasını önlemek için iyi bir yoldur. 

```powershell
$secrets = az appconfig kv list -n myAppConfiguration --key "secrets.*" --resolve-keyvault --query "[*].{name:key, value:value}" | ConvertFrom-Json

foreach ($secret in $secrets) {
  $keyvalues += $secret.name + "=" + $secret.value + ","
}

if ($keyvalues){
  $keyvalues = $keyvalues.TrimEnd(',')
  helm upgrade --install --set $keyvalues "example" ./mychart 
}
else{
  helm upgrade --install "example" ./mychart 
}

```

[Kubernetes panosuna](../aks/kubernetes-dashboard.md)erişerek yapılandırmaların ve parolaların başarıyla ayarlandığını doğrulayın. Uygulama yapılandırmasından **renk** ve **mesaj** değerlerinin kapsayıcının ortam değişkenlerine doldurulduğunu görürsünüz.

![Hızlı başlangıç uygulaması başlatma yerel](./media/kubernetes-dashboard-env-variables.png)

Bir gizli dizi, **parola**, uygulama yapılandırmasında Key Vault başvuru olarak depolar da Kubernetes sırlarına eklenmiştir. 

![Veri bölümündeki parolayı vurgulayan ekran görüntüsü.](./media/kubernetes-dashboard-secrets.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Held ile bir Kubernetes dağıtımında kullanılacak Azure uygulama yapılandırma verilerini aktarmış olursunuz. Uygulama yapılandırmasını kullanma hakkında daha fazla bilgi için Azure CLı örneklerine devam edin.

> [!div class="nextstepaction"]
> [Azure CLI](/cli/azure/appconfig)

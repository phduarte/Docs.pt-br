---
title: Hospedar o ASP.NET Core no Linux com o Apache
description: "Saiba como configurar o Apache como um servidor de proxy reverso CentOS para redirecionar o tráfego HTTP para um aplicativo web do ASP.NET Core em execução no Kestrel."
author: spboyer
manager: wpickett
ms.author: spboyer
ms.custom: mvc
ms.date: 10/19/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: host-and-deploy/linux-apache
ms.openlocfilehash: aa55ecd6dc8169e0e77b3899389ec924b1e1ae4a
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2018
---
# <a name="host-aspnet-core-on-linux-with-apache"></a>Hospedar o ASP.NET Core no Linux com o Apache

Por [Shayne Boyer](https://github.com/spboyer)

Usando este guia, saiba como configurar [Apache](https://httpd.apache.org/) como um servidor de proxy reverso na [CentOS 7](https://www.centos.org/) para redirecionar o tráfego de HTTP para um aplicativo web do ASP.NET Core em execução no [Kestrel](xref:fundamentals/servers/kestrel). O [mod_proxy extensão](http://httpd.apache.org/docs/2.4/mod/mod_proxy.html) e módulos relacionados Criar proxy reverso do servidor.

## <a name="prerequisites"></a>Pré-requisitos

1. Servidor que executa o CentOS 7 com uma conta de usuário padrão com privilégios sudo
2. Aplicativo do ASP.NET Core

## <a name="publish-the-app"></a>Publique o aplicativo

Publicar o aplicativo como um [implantação autossuficiente](/dotnet/core/deploying/#self-contained-deployments-scd) na configuração de versão para o tempo de execução do CentOS 7 (`centos.7-x64`). Copie o conteúdo de *bin/Release/netcoreapp2.0/centos.7-x64/publish* pasta ao servidor usando o SCP, FTP ou outro método de transferência de arquivo.

> [!NOTE]
> Em um cenário de implantação de produção, um fluxo de trabalho de integração contínua faz o trabalho de publicação do aplicativo e copiar os ativos para o servidor. 

## <a name="configure-a-proxy-server"></a>Configurar um servidor proxy

Um proxy reverso é uma instalação comum para que serve a aplicativos web dinâmicos. O proxy inverso encerra a solicitação HTTP e as encaminha para o aplicativo ASP.NET.

Um servidor proxy é aquele que encaminha as solicitações de cliente para outro servidor, em vez de atendê-las por conta própria. Um proxy reverso encaminha para um destino fixo, normalmente, em nome de clientes arbitrários. Neste guia, Apache é configurado como o proxy inverso em execução no mesmo servidor que Kestrel está atendendo o aplicativo ASP.NET Core.

### <a name="install-apache"></a>Instalar o Apache

CentOS pacotes de atualização de suas versões estável mais recente:

```bash
sudo yum update -y
```

Instalar o servidor web Apache em CentOS com um único `yum` comando:

```bash
sudo yum -y install httpd mod_ssl
```

Exemplo de saída depois de executar o comando:

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> Neste exemplo, a saída reflete httpd.86_64 desde a versão CentOS 7 é de 64 bits. Para verificar o local em que o Apache está instalado, execute `whereis httpd` em um prompt de comando. 

### <a name="configure-apache-for-reverse-proxy"></a>Configurar o Apache para proxy reverso

Os arquivos de configuração do Apache estão localizados no diretório `/etc/httpd/conf.d/`. Qualquer arquivo com o *.conf* extensão é processada em ordem alfabética, além dos arquivos de configuração do módulo em `/etc/httpd/conf.modules.d/`, que contém todas as configurações de arquivos necessário para carregar módulos.

Criar um arquivo de configuração para o aplicativo chamado `hellomvc.conf`:

```
<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/hellomvc-error.log
    CustomLog /var/log/httpd/hellomvc-access.log common
</VirtualHost>
```

O **VirtualHost** nó pode aparecer várias vezes em um ou mais arquivos em um servidor. **VirtualHost** está definido para escutar em qualquer endereço IP usando a porta 80. As duas linhas são definidas para solicitações de proxy na raiz do servidor no 127.0.0.1 na porta 5000. Para a comunicação bidirecional, *ProxyPass* e *ProxyPassReverse* são necessários.

Registro em log pode ser configurado por **VirtualHost** usando **ErrorLog** e **CustomLog** diretivas. **Log de erros** é o local onde o servidor registra erros, e **CustomLog** define o nome de arquivo e o formato do arquivo de log. Nesse caso, isso é onde as informações de solicitação são registradas. Há uma linha para cada solicitação.

Salve o arquivo e testar a configuração. Se tudo passar, a resposta deverá ser `Syntax [OK]`.

```bash
sudo service httpd configtest
```

Reinicie o Apache:

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitoring-the-app"></a>Monitoramento do aplicativo

Apache agora está configurado para encaminhar solicitações feitas a `http://localhost:80` para o aplicativo ASP.NET Core em execução em Kestrel em `http://127.0.0.1:5000`.  No entanto, o Apache não está configurado para gerenciar o processo de Kestrel. Use *systemd* e crie um arquivo de serviço para iniciar e monitorar o aplicativo web subjacente. *systemd* é um sistema de inicialização que fornece muitos recursos poderosos para iniciar, parar e gerenciar processos. 


### <a name="create-the-service-file"></a>Criar o arquivo de serviço

Crie o arquivo de definição de serviço:

```bash
sudo nano /etc/systemd/system/kestrel-hellomvc.service
```

Um arquivo de serviço de exemplo para o aplicativo:

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/aspnetcore/hellomvc
ExecStart=/usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
Restart=always
# Restart service after 10 seconds if dotnet service crashes
RestartSec=10
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

> [!NOTE]
> **Usuário** &mdash; se o usuário *apache* não é usado pela configuração, o usuário deve criado pela primeira vez e determinado propriedade adequada de arquivos.

Salve o arquivo e habilitar o serviço:

```bash
systemctl enable kestrel-hellomvc.service
```

Inicie o serviço e verifique se ele está em execução:

```bash
systemctl start kestrel-hellomvc.service
systemctl status kestrel-hellomvc.service

● kestrel-hellomvc.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-hellomvc.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-hellomvc.service
            └─9021 /usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
```

Com o proxy reverso configurado e gerenciadas por meio de Kestrel *systemd*, o aplicativo web está totalmente configurado e pode ser acessado a partir de um navegador no computador local em `http://localhost`. Inspecionando os cabeçalhos de resposta, o **Server** cabeçalho indica que o aplicativo ASP.NET Core é atendido por Kestrel:

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="viewing-logs"></a>Exibindo logs

Desde que o aplicativo web usar Kestrel é gerenciada usando *systemd*, eventos e os processos são registrados para um diário centralizado. No entanto, esse diário contém entradas para todos os serviços e processos gerenciados pelo *systemd*. Para exibir os itens específicos de `kestrel-hellomvc.service`, use o seguinte comando:

```bash
sudo journalctl -fu kestrel-hellomvc.service
```

Para filtragem de hora, especifica opções de tempo com o comando. Por exemplo, use `--since today` para filtrar o dia atual ou `--until 1 hour ago` para ver as entradas da hora anterior. Para obter mais informações, consulte o [página para journalctl](https://www.unix.com/man-page/centos/1/journalctl/).

```bash
sudo journalctl -fu kestrel-hellomvc.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="securing-the-app"></a>Protegendo o aplicativo

### <a name="configure-firewall"></a>Configurar o firewall

*Firewalld* é um daemon dinâmico para gerenciar o firewall com suporte para zonas de rede. Portas e filtragem de pacote ainda podem ser gerenciados pelo iptables. *Firewalld* devem ser instalados por padrão. `yum`pode ser usado para instalar o pacote ou verifique se que ele está instalado.

```bash
sudo yum install firewalld -y
```

Use `firewalld` para abrir somente as portas necessárias para o aplicativo. Nesse caso, as portas 80 e 443 são usadas. Os comandos a seguir definem permanentemente as portas 80 e 443 para abrir:

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

Recarrega as configurações de firewall. Verifique os serviços disponíveis e as portas na zona padrão. Opções estão disponíveis por meio da inspeção `firewall-cmd -h`.

```bash 
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="ssl-configuration"></a>Configuração do SSL

Para configurar o Apache para SSL, o *mod_ssl* módulo é usado. Quando o *httpd* módulo foi instalado, o *mod_ssl* módulo também foi instalado. Se ele não foi instalado, use `yum` para adicioná-lo para a configuração.

```bash
sudo yum install mod_ssl
```
Para impor a SSL, instale o `mod_rewrite` módulo para habilitar a regravação de URL:

```bash
sudo yum install mod_rewrite
```

Modificar o *hellomvc.conf* arquivo para habilitar a regravação de URL e proteger a comunicação na porta 443:

```
<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/ [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/hellomvc-error.log
    CustomLog /var/log/httpd/hellomvc-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> Este exemplo está usando um certificado gerado localmente. **SSLCertificateFile** deve ser o arquivo de certificado primário para o nome de domínio. **SSLCertificateKeyFile** deve ser o arquivo de chave gerado quando CSR é criado. **SSLCertificateChainFile** deve ser o arquivo de certificado intermediário (se houver) que foi fornecido pela autoridade de certificação.

Salve o arquivo e a configuração de teste:

```bash
sudo service httpd configtest
```

Reinicie o Apache:

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a>Sugestões adicionais do Apache

### <a name="additional-headers"></a>Cabeçalhos adicionais

Para proteger contra ataques mal-intencionados, há alguns cabeçalhos que devem ser modificados ou adicionados. Certifique-se de que o `mod_headers` módulo está instalado:

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a>Apache seguro contra ataques de clickjacking

[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), também conhecido como um *UI redress ataque*, é um ataque mal-intencionado em que o visitante do site seja levado a clicar em um link ou botão em uma página diferente daquele que está visitando atualmente. Use `X-FRAME-OPTIONS` para proteger o site.

Editar o *httpd* arquivo:

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

Adicione a linha `Header append X-FRAME-OPTIONS "SAMEORIGIN"`. Salve o arquivo. Reinicie o Apache.

#### <a name="mime-type-sniffing"></a>Detecção de tipo MIME

O `X-Content-Type-Options` cabeçalho impedirá que o Internet Explorer *MIME-sniffing* (determinando um arquivo `Content-Type` do conteúdo do arquivo). Se o servidor define a `Content-Type` cabeçalho para `text/html` com o `nosniff` opção definida, o Internet Explorer renderiza o conteúdo como `text/html` , independentemente do conteúdo do arquivo.

Editar o *httpd* arquivo:

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

Adicione a linha `Header set X-Content-Type-Options "nosniff"`. Salve o arquivo. Reinicie o Apache.

### <a name="load-balancing"></a>Balanceamento de carga 

Este exemplo mostra como instalar e configurar o Apache no CentOS 7 e no Kestrel no mesmo computador da instância. Para não ter um ponto único de falha; usando *mod_proxy_balancer* e modificando o **VirtualHost** permitiria para gerenciar instâncias de vários aplicativos web por trás do servidor de proxy do Apache.

```bash
sudo yum install mod_proxy_balancer
```

No arquivo de configuração mostrada abaixo, uma instância adicional do `hellomvc` aplicativo está configurado para ser executado na porta 5001. O *Proxy* seção é definida com uma configuração de Balanceador com dois membros para balancear a carga *byrequests*.

```
<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/ [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/hellomvc-error.log
    CustomLog /var/log/httpd/hellomvc-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a>Limites de taxa
Usando *mod_ratelimit*, que está incluído o *httpd* módulo, a largura de banda de clientes pode ser limitada:

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```
O arquivo de exemplo limita a largura de banda como 600 KB/s no local da raiz:

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

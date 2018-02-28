---
layout: post
title:  "Configurando Gogs com MySQL no Docker"
date:   2018-02-28 08:53:10 -0400
categories: docker 
---

Algumas vezes, principalmente em ambientes corporativos, pode ser necessário configurar um servidor Git. O Gogs é um ótima alternativa para isso.

Criaremos um compose file utilizando as imagens do [MySQL](https://hub.docker.com/_/mysql/) e do [Gogs](https://hub.docker.com/r/gogs/gogs/). Por isso é interessante baixa-las do Docker Hub para seu repositório local, caso ainda não tenha feito:

{% highlight bash %}
  docker pull mysql:5.7
  docker pull gogs/gogs
{% endhighlight %}

## Compose file

Crie o arquivo __docker-compose.yml__ e adicione o conteúdo:

{% highlight yml %}
  version: '3'
  services:
    gogs:
      image: gogs/gogs
      container_name: gogs
      ports:
        - "10022:22"
        - "10080:3000"
      networks:
        - gogs-network
      volumes:
        - "/var/gogs/gogs-volume:/data"
      depends_on:
        - "mysql"
    mysql:
      image: mysql:5.7
      container_name: mysql
      restart: always
      environment:
        MYSQL_ROOT_PASSWORD: "root"
        MYSQL_DATABASE: "gogs"
      networks: 
        - "gogs-network"
      volumes:
        - "/var/gogs/mysql-volume:/var/lib/mysql"
  networks:
    gogs-network:
      driver: "bridge"
{% endhighlight %}

No diretório onde o arquivo foi criado execute o comando:

{% highlight bash %}
  docker-compose up -d
{% endhighlight %}

Acesse a interface de configuração do Gogs pelo navegador, acessando a url [http://localhost:10080](http://localhost:10080) e prossiga com as configurações iniciais da ferramenta.

## Primeiro acesso ao Gogs e configuração inicial pela interface

Ao acessar pela primeira vez a interface do Gogs pela interface disponível em [http://localhost:10080](http://localhost:10080), será possível visualizar um formulário para efetuar a configuração inicial da ferramenta. Alguns valores podem ser um pouco confusos ao efetuar essa configuração então segue um guia de como fazê-los baseado na configuração de nossos containers:

+ __Repository root path__: Deixe o valor padrão (/home/git/gogs-repositories), pois o script de inicialização do Gogs (start.sh) possui um link simbólico para este diretório. É claro que é possível alterar depois pelo arquivo de configuração, mas inicialmente é preferível inicia-lo assim.

+ __Run user__: Deixe o valor padrão aqui também, pois o script build.sh já possui um apontamento para o usuário git.

+ __Domain__: Informe o IP do Docker host, ou o DNS deste host na rede, para que seja possível acessar o Gogs pela rede.

+ __SSH Port__: Informe a porta do Docker Host para qual a porta 22 do container foi mapeada. No nosso caso será a porta 10022.

+ __HTTP Port__: Ao contrário da configuração anterior, a porta a ser informada aqui não é a correspondente no Docker Host, mas sim a do próprio container. No nosso caso deixaremos a porta 3000.

+ __Application URL__: Esta é uma combinação do __Domain__ e da porta HTTP, desta vez a do prório Docker Host. No nosso caso será `http://{IP}:10080`, substituindo `{IP}` pelo IP do Docker Host.

## Entendendo o compose file

Apesar da análise não estar na ordem em que as configurações foram escritas, preferi faze-la desta forma para facilitar o entendimento.

### Configurando a rede

Primeiramente é necessário garantir que os containers estejam na mesma rede. Isso porque em cada execução o docker vincula um IP diferente ao container iniciado. Por esse motivo a comunicação entre eles deverá ser feita pelos nomes de cada um e não diretamente pelo IP. O fato é que não é possível fazer este vínculo por nome utilizando a rede padrão. 

Para resolver isso, então, na sessão `networks` definimos uma rede, a qual damos o nome de `gogs-network` e que será vinculada tanto ao container do Gogs quanto ao do MySQL.

### Configurando o MySQL

Na sessão `mysql` fazemos a configuração do banco de dados que armazenará os dados de configuração do Gogs. Em `image` definimos a imagem e versão (mysql:5.7) e em `container_name` damos um nome para o container referente ao MySQL que será criado quando executarmos o compose file. Este nome é importante porque será por ele, e não pelo IP, que faremos o vínculo da base de dados com o Gogs. 

Em seguida, em `restart`, definimos o comportamento do MySQL ao executarmos este arquivo e então chegamos nas configurações sensíveis da base de dados.

Em `environment` configuramos as variáveis de ambiente do MySQL. As duas variáveis utilizadas no script são variáveis definidas pela própria imagem do MySQL e que, quando vinculadas, refletem na configuração da base de dados. `MYSQL_ROOT_PASSWORD` é a variável que define a senha do usuário root do MySQL e `MYSQL_DATABASE` é a variável que define o nome do schema que será criado ao inicializar o container.

Em `networks` vinculamos a rede que definimos anteriormente.

E em `volumes` definimos um volume, onde os dados serão escritos no Docker Host. Neste caso eles serão escritos no diretório `/var/gogs/mysql-volume` no Docker Host.

### Configurando o Gogs

Na sessão `gogs` é onde faremos a configuração do nosso servidor Git. Assim como na configuração do MySQL apontamos a imagem e damos um nome ao container que será criado na execução do compose file.

Em `ports` definimos as portas que serão vinculadas à suas correspondentes no container. Neste caso vinculamos as portas 10022 e 10080 do Docker Host às portas 22 e 3000 do container, respectivamente. Nesta ordem, também, elas representam as portas que responderão às chamadas SSH e HTTP, possibilitando o acesso aos repositórios por meio destes protocolos.

Em `networks`, assim como fizemos na configuração do MySQL, vinculamos a rede que criamos, de forma que, estando ambos na mesma rede, garantimos que será possível realizar a comunicação entre eles.

Em `volumes` configuramos o diretório onde os arquivos do Gogs, inclusive os próprios códigos-fontes dos projetos que enviarmos para este servidor, serão escritos. Neste caso eles serão escritos em `/var/gogs/gogs-volume`.

E, finalmente, em `depends_on` vinculamos o valor `mysql`. Precisamos desta configuração para garantir que o container do Gogs, ao executarmos o compose file, seja iniciado somente quando o container do MySQL estiver disponível, evitando assim possíveis problemas na tentativa de tentar acessar a base de dados "antes da hora".

---
layout: post
title:  "Configurando um ambiente de desenvolvimento Node.js"
date:   2019-08-16 12:00:00 -0400
categories: node 
---

Os passos para instalação e configuração devem ser executados via linha de comando, utilizando um gerenciador de pacotes. 

Se você usa uma distribuição de Linux é provável que já possua um gerenciador de pacotes padrão. Se utiliza Windows ou Mac OSX recomendarei um gerenciador para cada um destes sistemas operacionais.

Para garantir que os passos para instalação dos gerenciadores de pacote permaneçam atualizados deixarei apenas os links para a página contendo o processo de instalação de cada um. Com ele instalado poderemos prosseguir com a configuração do ambiente.

## 1. Gerenciador de pacotes

### Mac OSX

Usuários de Mac OSX podem optar pelo [Homebrew](https://brew.sh/index_pt-br). Este é um ótimo gerenciador de pacotes e amplamente utilizado pelos usuários desta plataforma.

O processo de instalação é descrito na própria página inicial do Homebrew.

### Windows

Para usuários de Windows recomendo a utilização do [Chocolatey](https://chocolatey.org). 

O processo de instalação é bem simples e pode ser realizado via `cmd.exe` ou via `PowerShell.exe`. Para instalá-lo, execute os passos descritos na [página de instalação](https://chocolatey.org/install) do Chocolatey.

## 2. NVM

Você poderia pular esta etapa e instalar uma versão do Node.js diretamente. No entanto, recomendo que instale primeiro o NVM e, por meio dele, configure as versões de Node.js que precisar.

#### Mas, afinal, o que é e para que serve o NVM?

O NVM (Node Version Manager) é um gerenciador de versões do Node.js. 

Ele é útil quando precisamos ter várias versões diferentes no Node.js instaladas em nossa máquina.

#### E por que eu precisaria de mais de uma versão do Node.js instalada na minha máquina?

Se você trabalha com vários projetos que utilizam Node.js, pode ser que a versão necessária para cada um deles seja diferente. Nesse caso, o NVM vai te ajudar a controlar qual versão será utilizada em cada momento.

#### E como eu instalo o NVM?

Isso depende do Sistema Operacional e do gerenciador de pacotes utilizado:

**Linux** via **curl**:

```sh
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
```

**Mac OSX** via **Homebrew**:

```sh
brew install nvm
```

**Windows** via **Chocolatey**:

```sh
choco install nvm
```

Após a instalação recomendo reiniciar o terminal para garantir que as configurações do NVM sejam carregadas.

## 3. Node.js

Com o NVM instalado basta executar o comando a seguir para instalar a última versão estável.

Se você utiliza **Windows**:

```sh
nvm install latest
```

Se utiliza **Linux** ou **Mac OSX**:

```sh
nvm install node --lts
```

E para selecioná-la como versão padrão:

```sh
nvm use --lts
```

Para verificar se o Node.js foi instalado e configurado corretamente:

```sh
node -v
```

A saída do comando anterior deve ser a versão instalada em sua máquina.

## 4. Yarn

A instalação do Yarn é opcional. Particularmente, eu gosto de utilizá-lo, mas fica a seu critério (ou à de seu time).

#### Mas, afinal, o que é e pra que serve o Yarn?

O Yarn é um gerenciador de dependências. É uma opção ao gerenciador padrão do Node.js - o npm.

Ele te possibilitará, entre outras coias, instalar, desinstalar e configurar as dependências do seu projeto.

#### E como eu instalo o Yarn?

Isso também depende do Sistema Operacional utilizado. 

Felizmente o Yarn disponibiliza uma página onde encontramos o processo de instalação descrito com detalhes.

Para instalá-lo, visite a [página de instalação do Yarn](https://yarnpkg.com/lang/en/docs/install/), seleciona o Sistema Operacional e siga o processo referente ao gerenciador de pacotes que você utiliza.

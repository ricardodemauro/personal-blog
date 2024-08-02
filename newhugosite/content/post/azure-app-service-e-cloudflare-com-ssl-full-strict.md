+++
categories = ["Azure", "devops", "pt", "https", "owasp", "security", "websecurity"]
date = 2019-12-15T20:36:45Z
description = ""
draft = true
image = "https://res-1.cloudinary.com/dgoa5llvh/image/upload/q_auto/cloudflare-ssl-cover.png"
slug = "azure-app-service-e-cloudflare-com-ssl-full-strict"
tags = ["Azure", "devops", "pt", "https", "owasp", "security", "websecurity"]
title = "Azure App Service e Cloudflare com SSL Full (strict)"

+++


[[english version](__GHOST_URL__/azure-app-service-and-cloudflare-with-full-ssl-strict/)]

Vamos configurar o Cloudflare como proxy reverso e Azure Web Apps como serviço de web. Buscando a melhor configuração de segurança que o Cloudflare oferece na camada gratuíta e cadeado verde.

Ao final teremos a seguinte configuração:

- CloudFlare como proxy reverso
- Azure Web App como serviço web
- SSL válido (cadeado verde)
- SSL full trust entre Cloudflare e Azure Web Apps (Cloudflare validando certificado do lado do servidor)
- Domínios para aplicação */* (A Name) e *https://www.rmauro.com.br* (C Name)

Nosso objetivo além do SSL válido (cadeado verde), é criptografia ponta a ponta.
Criptografia entre o Cloudflare e o usuário e entre Cloudflare e o Azure Web App.

![illustration-strict-ssl--2-](https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/illustration-strict-ssl--2-.png)

## Pré-requisitos

- Conta no **Cloudflare**
- **Azure Web Apps** com aplicativo rodando (pode ser o *blank* nativo do App Service)
- **App Service Plan** com plano mínimo *Basic*
- [OpenSSL](https://www.openssl.org/) instalado
- Domínio configurado no Cloudflare

Obs.: Camadas *shared* e *free* do *Azure App Service Plan* não permitem realizar a configuração SSL.

## Conhecendo o ambiente

![cf-blog-1-1](https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-1-1.png)

*Blog [Ghost](https://github.com/YannickRe/Ghost-Azure) rodando no App Service.

Domínio https://ghost-azure5ae4.azurewebsites.net*

![cf-blog-2](https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-2.png)
*Aplicativo Web App Service*

![cf-blog-3](https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-3.png)
*Cloudflare criado*

## Configurando o domínio do Cloudflare

Primeiro vá até a aba de *Custom domains* no Azure Web App e copie o IP do *web app*.

![cf-blog-7](https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-7.png)

Vá para o *Cloudflare* e na aba *DNS* e configure conforme abaixo.

- Primeiro registro
    - Type **A**
    - Name **@**
    - Target **IP do Web App**
    - Proxy status: DNS only
- Segundo registro
    - Type **C**
    - Name **www**
    - Target **@**
    - Proxy status: DNS only

![cf-blog-23](https://res-1.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-23.png)

Por último configure o registro TXT.

![cf-blog-8](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-8.png)
*O registro TXT mostra ao Azure que você é dono do domínio a ser configurado.*

>Após tudo configurado iremos apagar esse registro.

Pronto. Devemos ter algo como:
![cf-blog-9](https://res-1.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-9.png)

## Configurando o domínio do Azure Web App

Estamos prontos para configurar o domínio do Azure App Service.

1. Abrir a blade **Custom domains**
2. Clicar em **Add custom domain**
3. Inserir nosso domínio em **Custom domain**
4. Clidar em **Validate**
5. Validar o domínio

![cf-blog-10](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-10.png)

Devemos executarmos os passos o domínio principal `@` e para o sub-domínio `www`.

Obs.: Para o domínio principal (A Name - rmauro.com.br) utilizar o record type `A record`. 
Para o domínio secondário (subdomínio) (C Name - www.rmauro.com.br) utilizar o record type `C record`

Após validado clicar em **Add custom domain**

![cf-blog-11](https://res-3.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-11.png)

Devemos ter algo como:

![cf-blog-12-1](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-12-1.png)

Quando tentarmos acessar o site devemos receber o cadeado vermelho. Isso porque o certificado digital retornado é o do *.azurewebsites.net. e não o nosso - por enquanto.

![cf-blog-13](https://res-1.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-13.png)

### Configurando certificados

No cloudflare iremos utilizar o modelo de certificado digital **Full (strict)**. Isso faz com o que o **Cloudflare** valide o certificado na comunicação com o servidor, nesse caso o Azure Web App.

Uma camada a mais de verificação, tornando nosso aplicativo ainda mais seguro.

### Configurando encryption mode e gerando as chaves

Abra o **Cloudflare** na aba **SSL/TLS** e a sub-aba **Overview** selecione o tipo **Full (strict)**.

![cf-blog-14](https://res-1.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-14.png)

Agora iremos criar o certificado digital confiável pelo Cloudflare e para configurarmos no Azure.

Vá para a aba **Origin Server** e clique em **Create certificate**

![cf-blog-15-1](https://res-1.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-15-1.png)

Deixa as configurações padrões.
- *Let Cloudflare generate a private key and a CSR*
- *Private Key RSA*
- *List of hostnames* os configurados nos passos anteriores.

![cf-blog-16](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-16.png)

Copie o conteúdo texto para o *notepad* e salve como:
- *Origin Certificate* -> *rmauro.com.br.pem*
- *Private Key* -> *rmauro.com.br.key*

![cf-blog-17](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-17.png)

#### Configurando o Azure Web App com o certificado 

Primeiro vamos gerar a chave em formato **pfx** utilizando o **openssl**.

Abrir o *command prompt* e navegue até a pasta das chaves e execute o seguinte comando:

```bash
> openssl pkcs12 -inkey rmauro.com.br.key -in rmauro.com.br.pem -export -out rmauro.com.br.pfx
```

Esse comando irá solicitar um *passowrd*. Entre com um password forte para nossa chave.

No portal do Azure navegue até a sub blade *Custom domains* de novo. Agora vamos subir o certificado digital que acabamos de criar e configurá-lo com nosso domínio (Cadeado verde).

![cf-blog-18-2](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-18-2.png)

1. Clique em *Add Binding*
2. Selecione o domínio *rmauro.com.br*
3. Clique em *Upload PFX Certificate*
4. Selecione o PFX gerado a pouco (rmauro.com.br.pfx)
5. Insira a senha do certificado quando solicitado

Pronto, agora temos o certificado para utilizarmos em nosso site.

![cf-blog-19-1](https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-19-1.png)

Agora selecione o certificado *Cloudflare Origin Certificate* e o *TLS/SSL Type* - *SNI SSL* e clique em Add Binding.

Faça o mesmo para o sub-domínio *www*.

Devemos ter algo como:

![cf-blog-20-1](https://res-3.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-20-1.png)

### Habilitado Cloudflare como DNS and Proxy

Volte para o Cloudflare e configure o DNS com Proxy para os domínios.

![cf-blog-21-1](https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/cf-blog-21-1.png)

Pronto... cadeado verde e criptografia ponta a ponta.




+++
categories = ["security", "hack", "owasp", "sql-injection", "pt"]
date = 2019-12-03T01:58:29Z
description = ""
draft = false
image = "https://res-5.cloudinary.com/dgoa5llvh/image/upload/q_auto/sql-injection-cover.jpg"
slug = "sql-injection-na-pratica"
tags = ["security", "hack", "owasp", "sql-injection", "pt"]
title = "SQL Injection na Prática - Executando o Ataque"

+++


## Primeiro, você sabe (ou lembra-se) o que é SQL Injection?

*(for [english version](__GHOST_URL__/practice-of-sql-injection/))*

Segundo OWASP.org (https://www.owasp.org/index.php/SQL_Injection).

> A SQL injection attack consists of insertion or "injection" of a SQL query via the input data from the client to the application. A successful SQL injection exploit can read sensitive data from the database, modify database data.

Ou seja, é uma técnica de invasão de serviços web através de comandos SQL.
Desde que o serviço web (*website*, *webservice*, *REST API*, etc) esteja sucetível ao ataque *SQL Injection*, você consegue executar comandos SQL não previstos no sistema. 

Por exemplo, podemos acessar a tabela de usuários do sistema e obter informações não previstas pela API.

**Nota: Mesmo nos dias de hoje podemos encontrar em serviços web (sites e web services) esse tipo de falha! :(**

##O nosso laboratório (site com falha proposital)

>https://rmauro-failure.herokuapp.com/

**Atenção:** Entenda que executar esse ataque é contra a lei. Não faça isso no mundo real a menos que você seja contratado para isso (white hat hacker), mesmo para fins de estudo.

O que estamos fazendo aqui é um teste no **meu site de teste**, que produzi para este fim. 

Nesse caso podemos :) - Manda vê!
Ok? Você está liberado para executar os testes neste site.

###Se familiarizando com o site vulnerável

Abra o site https://rmauro-failure.herokuapp.com/

![1-1](https://res-1.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/1-1.png)

Este é o *website* que vamos trabalhar, na caixa de texto (*enter a product name*) é onde podemos realizar buscas por nome de produto e também executar nosso *ataque*.

**ATACAR!!! GO, GO, GO.**

###Estudando o sistema *rmauro-failure*

Primeiro insira no campo de busca o valor `Hammer` e faça uma busca.

![2](https://res-5.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/2.png)

Vamos pensar um pouco e tentar entender como funciona nos bastidores - *code behind* se me permitem.

![sql-inje](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/sql-inje.jpg)

1. O sistema coleta o valor informado, `Hammer`, do campo de busca;
2. Envia para o *formador*, ou *concatenador*, de `SQL` concatenando a `String` enviada com o resto da query;
3. Quando formado, o **comando SQL** é enviado para ao banco de dados, qualquer banco de dados relacional;
4. O banco de dados processa sua solicitação e retorna ao programa com o resultado;
5. Após executado pelo banco de dados, o programa faz o *parse* do resultado para objeto(s) em memória, processa e envia para o chamador, neste caso a tela;
6. O Browser mostra o retorno (HTML) na tela.

Este é o fluxo básico do sistema. Simples, certo? 

### *Hammer time* - hora de atacar o sistema

Primeiro vamos executar alguns testes.

1. Coloque no campo de busca o valor `'` (aspas simples) e aperte o botão de busca, `Search`.

![3](https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/3.png)

![4](https://res-3.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/4.png)

O resultado deve ser um **erro** como este. **Perfeito!**

Isso significa que o sistema está montando a `Query SQL` dinamicamente sem nenhum tratamento nos parâmetros de entrada.

Vamos pensar um pouco. Qual será a query gerada e enviada ao banco de dados? 

```sql
SELECT ? ? ? FROM ? WHERE ? '''
```

Algo próximo disso que causou a explosão!

Não conhecemos qual a tabela utilizada ou os campos da tabela, porém sabemos como é a estrutura básica de um comando `SQL SELECT`.

####Vamos explorar um pouco mais
- Agora teste o campo de busca com `mmer`.

![5](https://res-4.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/5.png)

Perceba que houve retorno, então podemos inferir que a query é algo como.

```sql
SELECT ? FROM ? WHERE ? LIKE '%mmer%'
```

Chegamos a conclusão que possuímos controle sobre o que escrito na parte final da `query`, logo onde vai o `mmer`, as demais partes do comando não podemos controlar.

>Mas essa pequena parte pode causar desastre.

####Descobrindo qual o banco de dados estamos trabalhando

1- A nossa query é formado por... ok, já sabemos disso.
```sql
SELECT ? FROM ? WHERE ? LIKE '%mmer%'
```
2- Agora vamos modificá-la para...

```sql
SELECT ? FROM ? WHERE ? LIKE '%';--%'
```
Isso irá causar o termino do comando SQL, provando que podemos modificá-lo conforme quisermos.

3- Vamos testar. Escreva no campo de busca `';--`

![6](https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/6.png)

Cada vez mais interessante. Agora obtivemos todos os registros da base (igual a antes) com a nova query.

4 - Legal, vamos tentar descobrir qual é o banco de dados em questão (MySql, Sql Server, Oracle, PostgreSql, etc). Para descobrimos vamos executar uma função que só funciona em um banco de dados específico (algo não standand como SELECT / INSERT ou DELETE). Algo como a função `Sleep`.

> Não se esqueça que esse sistema foi projetado por mim, logo eu sei que o banco de dados é *PostgreSql*, então vamos executar a função `pg_sleep()` (sleep do **PostgreSql**. 

> Assim não perdemos tempo testando varias possiblidades para diversos bancos de dados.

5 - Execute no campo de busca o valor `mmer%';SELECT pg_sleep(10);--`

```sql
SELECT ? FROM ? WHERE ? LIKE '%mmer%';SELECT pg_sleep(10);--%'
```

Perceba que demora precisamente 10 segundos para retornar a resposta. **Perfeito!**

Mas e se não estiver funcionado? 
Fácil, só trocar a função `sleep` do PostgreSql por outra especifico de outro banco de dados até encontrar o banco que estamos trabalhando. Nesse caso não tem mágica.

####Descobrindo quais tabelas existem no banco de dados

Legal até aqui, não é?! O que mais podemos fazer com nossa query? Tudo, ou quase tudo...

```sql
SELECT ? FROM ? WHERE ? LIKE '%mmer%';SELECT pg_sleep(10);--%'
```
Está é a última query que executamos. Pense agora nas possibilidades.

Já que sabemos que o banco de dados é **PostgreSql**. Agora vamos tentar encontrar **tabelas**.

Primeiro precisamos entender a estrutura de retorno do SQL, as colunas. 
O nosso retorno deve ser algo em torno de três colunas. 

Três colunas porque o produto mostrado na tela contem um `Id`, `Nome` e um `Preço`.

(3 | Hammer Drills | 22.3)

Ok, ok. Podem não ser três colunas exatamente, mas vamos tentar com 3 por enquanto (lembre-se que projetei o sistema).

Agora vamos utilizar o comando `UNION` do `SQL`. Isso nos possibilita juntar dois resultados em um único resultado ou `Dataset`.

![union-sql-1](https://res-3.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/union-sql-1.png)

Vamos usar isso para retornar os dados de produtos **e** algo que nos interessa.

```sql
SELECT ?, ?, ? FROM ? WHERE ? LIKE '%mmer%'
```

Vamos modificar nossa *query* para utilizar o `UNION` e trazer os valores **1, 2, 3**.

```sql
SELECT ?, ?, ? FROM ? WHERE ? LIKE '%mmer%' UNION SELECT '1', '2', '3';--
```

![7](https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/7.png)

Isso produziu o retorno que já conhecemos mais uma linha contendo os valores "1", "2" e "3", também validando nossa hipótese de três colunas.

Vamos partir para as **tabelas** agora.

```sql
SELECT ?, ?, ? FROM ? WHERE ? LIKE '%mmer%' UNION SELECT '1', '2', '3';--
```
*Nosso comando atual.*

O comando abaixo é capaz de retornar as tabelas do `Schema` do banco de dados *PostgreSql*.

```sql
SELECT table_name FROM information_schema.tables WHERE table_schema='public'
```

![sql-schema](https://res-3.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/sql-schema.png)

*Resultado esperado. Tabelas do sistema*

Vamos introduzir a nossa `Query`.

```sql
SELECT table_name FROM information_schema.tables WHERE table_schema='public'
```
Coloque no campo de busca `mmer%'UNION  SELECT "table_name", "table_schema", "table_catalog" FROM information_schema.tables WHERE table_schema='public'--`

Formando a `QUERY`:

```sql
select ?, ?, ? from ?
UNION 
SELECT "table_name", "table_catalog", "table_schema" FROM information_schema.tables WHERE table_schema='public'
```

![result-tables](https://res-2.cloudinary.com/ht54oxr6q/image/upload/q_auto/v1/ghost-blog-images/result-tables.png)

Encontramos as tabelas **Product** e **Users**.

Dai para frente é só extrapolar as possiblidades.

##Código Fonte do Website
> https://github.com/bootcamp-bbq/SqlInjection-Lab.git

##Referências utilizadas:

> https://www.owasp.org/index.php/SQL_Injection
> https://youtu.be/ciNHn38EyRc


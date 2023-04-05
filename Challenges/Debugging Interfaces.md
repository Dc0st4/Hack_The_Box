# Debugging Interfaces

**Acessamos a interface de depuração serial assíncrona do dispositivo embarcado enquanto ele estava operacional e capturamos algumas mensagens que estavam sendo transmitidas por ele. Você pode decodificá-los?**

O zip contém um arquivo .SAL, para ler este arquivo é necessario um software Analisador Lógico, no caso o saleae

Ao abrir o arquivo e olhar em hexadecimal é possivel notar muitos erros. Com base nas informações sobre Comunicação serial assíncrona, sabemos que cada dado que está sendo enviado está em ASCII e existe um bit de início e fim. Então é preciso converter os valores em ASCII.

Agora é necessario resolver o erro de enquadramento. Um erro de enquadramento ocorre quando o bit que está sendo lido é muito rápido ou muito lento. Se eles estiverem sendo lidos muito rápido ou muito devagar, eles darão valores diferentes.

Olhando o gráfico, vemos que a taxa de bits é muito baixa, pois a config esta lendo apenas 8 bits. 31 bits são lidos como 8 bits devido a taxa de bits lenta causando erro de enquadramento. Cada intervalo mais curto é de 32,02 microssegundos, então para saber a taxa de bits real:
~~~
 Taxa de bits (bit/s) = 1 segundo / (32,02 x 10^(-6)) segundos = 31.230,480949406621 = 31.230
~~~
Ao mudar a taxa de bits no software para 31.230 a flag é revelada
 ~~~
 HTB{ ∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎}
 ~~~     

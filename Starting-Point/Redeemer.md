# Redeemer

#### Tags
`Linux` `Redis` `Enumeration` 
`Penetration Tester Level 1` 
`Attack/Anonymous/Guest Access`

## Questões

1. **Qual porta TCP está aberta na máquina?**
**R:** `6379`

2. **Qual serviço está sendo executado na porta que está aberta na máquina?**
**R:** `redis`

3. **Que tipo de banco de dados é o redis? Escolhe entre as seguintes opções: (i) Banco de dados na memória, (ii) Banco de dados tradicional**
**R:** `Banco de dados na memória`

4. **Qual utilitário de linha de comando é usado para interagir com o servidor Redis? Digite o nome do programa que você inseririra no terminal sem nenhum argumento **
**R:** `redis-cli`

5. **Qual sinalizador é usado com o utilitária de linha de comando Redis para especificar o nome do host?**
**R:** `-h`

6. **Uma vez conectado a um servidor Redis, qual comando é usado para obter as informações e estatísticas sobre o servidor Redis?**
**R:** `info`

7. **Qual é a versão do servidor redis que esta sendo usada na maquina de destino?**
**R:** `5.0.7`

8. **Qual comando é usado para selecionar o banco de dados desejado no Redis?**
**R:** `select`

9. **Quantas chaves estão presentes dentro do banco de dados com indice 0?**
**R:** `4`

10. **Qual comando é usado para obter todas as chaves em um banco de dados?**
**R:** `keys *`


## **Envie a flag**
_write-up:_
~~~shell
 redis-cli -h 10.129.22.9
 info
 select 0
 keys * 
 get flag
"∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎"
~~~

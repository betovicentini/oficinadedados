# Oficina de dados PPGBOT 2021

Oficina do PPGBOT-Inpa. Scripts links e dicas para suas análises e seus projetos.

# Passos & Dicas

1. Se ainda não fez, veja abaixo como começar com o git e ligar este repositório ao seu computador. Ou se preferir use a interface do github para acrescentar arquivos e páginas.
2. Scripts na pasta scripts
3. Nomes de arquivos na usando *snake-case.R*, que tenham nomes informativos e tenham um comentário no cabeçalho informando o que ele faz (se colocar scripts aqui, coloque eles bem comentados).
4. Para cada script ou conjunto de scripts e análises pode ser criada um arquivo explicativo em markdown (*.md) que fica na pasta *docs* e aparece como uma [página web aqui](https://github.com/betovicentini/oficinadedados/blob/main/docs/index.md). Você pode acrescentar páginas, apenas referencie elas no index.md com um link.

# Instalar este repositório na sua máquina (local)

1. Instale o software **git** na sua máquina (esse software pode se usado para se comunicar com diferentes repositórios remotos, não é da github, nem gitlab, etc...)

*Linux:
```
sudo apt install git
git --version
```
Windows:  
- [Download](https://git-scm.com/download/win)
- [Como instalar](https://phoenixnap.com/kb/how-to-install-git-windows)

Mac: Use o [Homebrew](https://brew.sh/) para fazer essa instalação
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
brew doctor
brew install git
```

2. Até aqui você já pode clonar(=baixar) repositórios públicos online no seu computador ou pode simplesmente usar o controlador de versões (git) na sua máquina. Mas nós vamos utilizar o github, então prossiga...

3. Para poder mandar arquivos ou modificações ao repositório remoto (no github.com) precisa de uma conta e um método de autenticação.
  - Crie a conta no github.com;
  - Defina uma **SSH-KEY** no seu computador e coloque ela na sua conta do github, isso permite você se conectar com os seus repositórios remoto sem precisar fazer o login ou digitar a senha
    - [Aqui tem instruções gerais](https://docs.github.com/en/github/authenticating-to-github/testing-your-ssh-connection)
    -  [Aqui para usuários windows](https://phoenixnap.com/kb/generate-ssh-key-windows-10)

4. Clone o repositório da disciplina no seu computador. Importante usar o link SSH do código para clonar, o link do HTTPS vai sempre pedir o usuário e senha. Então clone com o comando abaixo:
```
#vá para a pasta no seu computador onde quer criar a pasta do repósitorio
#(usando o terminal bash no seu sistema)
#digite

git clone git@github.com:betovicentini/oficinadedados.git

#Isso deve ter criado uma pasta chamada oficinadedados
#entre na pasta e verifique o status
cd oficinadedados

git status

```
- Pode ver o conteúdo na pasta no seu administrador de arquivos e pastas. No windows, se quiser ver os arquivos escondidos (.git), precisa desabilitar a opção nas preferências. Note, no entanto, que você não deve mexer dentro da pasta .git. Apenas o arquivo .gitignore pode ser editado sem problema algum, mas entenda o que ele faz (indica os arquivos que devem ser ignorados no repositório git, mesmo estando na pasta do projeto)
- Note que o arquivo anexado não está 'committed'  nem 'stagged' e que você está no ramo principal do seu projeto (main ou master)
- Adicione ele ao repositório local
```
git commit -a -m "aqui qualquer nota que você queira para essa submissao"

git status

```
- Note que agora o arquivo novo já está no local, mas o teu repositório local está `a frente` do 'origin'  ou repositório remoto. Para enviar a mudança para git hub basta digitar (se a SSH foi cadastrada) e estará atualizado.

```
git push

```

- Se quiser atualizar o seu repositório local com mudanças que outros fizeram no repositório remoto, basta digitar:

```
git pull

```

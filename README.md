# Construindo um bot básico com o RASA  

## Instalação

- Instale o pip
```
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python get-pip.py
```

- Instale o virtualenv
```
pip install virtualenv
```

- Crie um diretório para os arquivos do bot
```
mkdir my-bot
cd my-bot
```

- Crie um virtualenv
```
python3 -m venv myvenv
```

- Ative o virtualenv
```
source myvenv/bin/activate
```

- Instale o Rasa Core
```
pip install rasa_core
```

- Instale o Rasa NLU
```
pip install rasa_nlu[tensorflow]
```
## Rasa NLU

**Intent:** é a intenção/objetivo do usuário, se o usuário diz "a python cerrado vai até quando?", o objetivo é descobrir a duração da Python Cerrado.

**Entity:** são informações que podem ser extraídas das mensagens do usuário, por exemplo, sexo, idade, etc.

**Actions:** são ações performardas pelo bot.

**Stories:** são exemplos de interações entre o bot e o usuário, definidas por ações e intenções.

### Arquivo de treino

No arquivo de treino são colocados vários exemplos de entradas do usuário mapeadas com suas respectivas intenções e entidades. Quanto maior a varidade de exemplos, melhor será a capacidade de processamento de linguagem natural do bot. É importante também que a quantidade de exemplos entre as intenções seja balanceada.

```
## intent:greet
- oi
- olá

## intent:what
- o que é python cerrado
- sobre o que é a python cerrado
- o que acontece na python cerrado

## intent:schedule
- o que tem na python cerrado [hoje](day)
- o que tem na python cerrado [amanhã](day)
- o que tem na python cerrado dia [30](day)
- o que tem na python cerrado dia [29](day)

## intent:bye
- tchau
- xau
```

O nome de uma intenção se inicia com "## intent:" e as possíveis entradas do usuário para essa intenção são descritas abaixo. A intenção `schedule` também utiliza entidades. O formato é [palavra] (nome_da_entidade), portanto, o exemplo de entrada do usuário é "o que tem na python cerrado hoje" e "hoje" é a entidade de nome "day".

Agora vamos definir o pipeline a ser usado para treinar o modelo NLU, aqui usaremos o spacy para fazer isso.

### 


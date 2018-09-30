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

- Instale o Rasa NLU e o Spacy
```
pip install rasa_nlu[spacy]
python -m spacy download pt_core_web_md
python -m spacy link pt_core_web_md pt
```
## Rasa NLU

O Rasa NLU é uma ferramenta de processamento de linguagem natural, a qual recebe uma entrada do usuário e tenta inferir a inteção e extrair entidades.

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
- o que tem na python cerrado dia [29](day)
- o que tem na python cerrado dia [30](day)

## intent:bye
- tchau
- xau
```

O nome de uma intenção se inicia com "## intent:" e as possíveis entradas do usuário para essa intenção são descritas abaixo. A intenção `schedule` também utiliza entidades. O formato é [palavra] (nome_da_entidade), portanto, o exemplo de entrada do usuário é "o que tem na python cerrado dia 29" e "29" é a entidade de nome "day".

Para definir o pipeline a ser usado para treinar o modelo NLU, criaremos um arquivo yml com o seguinte conteúdo:

```yml
language: pt
pipeline: "spacy_sklearn"
```

Execute o seguinte comando para treinar o modelo

```
python -m rasa_nlu.train -c nlu_config.yml --data nlu.md -o models --fixed_model_name nlu --project current --verbose
```

Crie o seguinte arquivo para testar o funcionamento do bot:


```python
from rasa_nlu.model import Interpreter
import json

interpreter = Interpreter.load("./models/current/nlu")

def ask_question(text):
  result = interpreter.parse(text)
  print(json.dumps(result, indent=2))

ask_question("o que tem na python cerrado dia 29?")
ask_question("o que tem na python cerrado dia 30?")
```

## Rasa Core

O Rasa Core é uma ferramenta de gerenciamento de fluxo de diálogo, ele tenta construir um modelo de probabilidade que decide o conjunto de ações a serem executadas de acordo com as entradas do usuário.

Crie um arquivo domain.yml. Nele serão listadas as intenções, entidades, ações e os templates. Templates são respostas que o bot pode usar para responder o usuário.

```yml
intents:
 - greet
 - what
 - schedule
 - bye

entities:
 - day

templates:
 utter_greet:
  - "Olá, bom dia"
 utter_what:
  - "A python cerrado é um encontro da comunidade python do centro-oeste"
 utter_schedule_29:
  - "No sábado vão rolar palestras e lightning talks"
 utter_schedule_30:
  - "No domingo vão rolar tutorias"
 utter_bye:
  - "Tchau! Bom evento"

actions:
 - utter_greet
 - utter_what
 - utter_schedule_29
 - utter_schedule_30
 - utter_bye
```

Agora, crie um arquivo stories.md, onde serão colocados os exemplos de interação:


```
## story1              
* greet              
  - utter_greet
* what
  - utter_what
* utter_schedule_29{"day":"29"}
  - utter_schedule_29
* utter_schedule_30{"day":"30"}
  - utter_schedule_30
* bye               
  - utter_bye
```

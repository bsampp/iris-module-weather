# Previsão do tempo

Módulo independente para a IRIS que consulta os endpoints públicos do Open-Meteo para transformar um nome de local em coordenadas e obter a previsão meteorológica.

## Instalação na IRIS

Requer a versão experimental da IRIS com **Comunidade > Catálogo > Instalar por
fonte**, Git instalado e internet. O runtime usa `httpx`, já incluído nas
dependências atuais da IRIS; o instalador não instala dependências adicionais.

No modal, informe `bsampp/iris-module-weather` (ou acrescente `#v0.1.1`).
Também é possível usar a URL completa:

```text
https://github.com/bsampp/iris-module-weather.git#v0.1.1
```

Ou, no ambiente da IRIS:

```powershell
python -m modules install "https://github.com/bsampp/iris-module-weather.git#v0.1.1"
```

Se o repositório estiver privado, clone com autenticação separadamente e informe
a pasta local. O instalador Git espera acesso público sem autenticação interativa.

Este módulo usa a chave `weather.forecast`, a mesma do exemplo incluído no
catálogo. Remova a instalação existente antes de testar esta fonte. Se houver
uma cópia de desenvolvimento com essa chave, preserve-a em outro local antes
de removê-la da descoberta. O instalador recusa duplicatas e preserva as cópias.

A instalação copia os arquivos para os dados do usuário e preserva a origem.
Instalar por URL não adiciona uma entrada ao catálogo oficial. Não há atualização
automática nem isolamento do código Python.

## Desenvolvimento e forks

Um fork pode ser instalado pela sua URL, mantendo `module.json`, `main.py` e
`README.md` na raiz. Preserve `weather.forecast` para uma variante que substitui
a original; use uma chave própria, como `community.weather`, se as duas precisarem
coexistir. Ajuste também nome e chamada para identificar a variante.

Para testar fora da IRIS, use Python 3.11 ou superior em um ambiente virtual:

```powershell
python -m pip install -r requirements.txt
python -m unittest discover -s tests -v
```

Os testes simulam as respostas HTTP e não consultam a internet. Na IRIS,
`python -m modules validate <pasta>` valida sem importar código e
`python -m modules check <pasta>` também importa o runtime.

## Autoria

O manifesto declara `module.author` como `bsampp`. A IRIS exibe esse campo no
catálogo e na lista de instalados; o dono de um fork não substitui a autoria
automaticamente. Instalações da versão anterior preservam seus arquivos e
precisam ser reinstaladas para receber a metadata de autor desta versão.

## Origem

Extraído de `catalog/modules/weather_forecast` da IRIS, revisão `b4c9ffb`,
preservando implementação e chave pública. A licença da IRIS ainda está pendente;
este exemplo não atribui uma nova licença ao código de origem.

## Como informar o local

Há duas formas, em ordem de prioridade:

1. informar uma cidade, município ou código postal como argumento da requisição;
2. preencher `Local padrão` na aba `Configurações` da rota deste módulo.

Quando `Local padrão` está vazio, `should_request_argument(variables)` faz a Home abrir o campo de argumento. Se uma chamada direta ainda chegar sem as duas formas, a execução é interrompida com a orientação para informar o local ou configurar a rota. Quando o local padrão está preenchido, selecionar o módulo executa a previsão diretamente. O argumento da requisição sempre substitui temporariamente o local padrão, sem alterar o valor salvo.

## Exemplos

- comando com argumento: `previsão Campinas`;
- busca manual no campo de argumentos: `São Paulo`;
- configuração persistente: definir `Local padrão` como `Recife`.

## O que este módulo testa

- manifesto instalado e runtime Python;
- variável opcional e editável `default_location`;
- variável obrigatória e editável `forecast_days`;
- decisão condicional por `should_request_argument(variables)`;
- solicitação do argumento quando a Home detecta `search_arguments`;
- sugestões remotas de cidades com rótulo, valor e descrição;
- geocodificação por nome ou código postal;
- cache em memória da localização selecionada;
- requisição HTTP real em background;
- timeout e erros de conexão sem bloquear nem derrubar a UI;
- validação de local ausente, local desconhecido e quantidade de dias;
- condições atuais e resumo diário;
- tradução dos códigos meteorológicos WMO para português;
- resposta estruturada com `success`, `message` e `result`;
- registro de sucesso ou erro no histórico;
- ausência de chave de API ou segredo.

## Configurações

### Local padrão

Texto opcional usado somente quando a execução não recebe argumento. Pode ser alterado ou apagado na rota do módulo.

### Dias da previsão

Número inteiro entre 1 e 7. O valor padrão é 3.

## Serviços externos

- geocodificação: [Open-Meteo Geocoding API](https://open-meteo.com/en/docs/geocoding-api);
- previsão: [Open-Meteo Weather Forecast API](https://open-meteo.com/en/docs).

Os endpoints públicos utilizados não exigem credencial no fluxo implementado. O uso continua sujeito à disponibilidade, licença e políticas do Open-Meteo.

## Limitações

- requer conexão com a internet;
- nomes ambíguos usam a opção escolhida na lista ou o primeiro resultado da geocodificação;
- a previsão é uma estimativa do modelo meteorológico e pode divergir das condições locais;
- o cache de localizações existe apenas durante o processo atual;
- o módulo não armazena histórico meteorológico próprio.

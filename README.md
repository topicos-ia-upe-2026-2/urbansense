# UrbanSense AI

*Evento:* AI Glasses Brasil  
*Disciplina:* Tópicos Avançados de IA - 2026.2  
*Equipe acadêmica:* Pedro Martins, Gabriel Lopes e Emanuele Maria

## 1. Problema e usuários-alvo

O registro de irregularidades urbanas, como descarte irregular de lixo e buracos nas vias, exige que o cidadão interrompa seu deslocamento, pegue o celular, abra um aplicativo, fotografe, classifique o problema e informe sua localização. Esse processo pode consumir de 60 a 120 segundos e desestimula principalmente quem circula com as mãos ocupadas. Como consequência, muitas ocorrências não são comunicadas, e os registros municipais tendem a ser esparsos e imprecisos.

O UrbanSense atende a três perfis:
- *Usuário primário:* entregadores, mototaxistas, ciclistas, agentes comunitários, motoristas e pedestres, que identificam problemas durante seus trajetos.
- *Usuário receptor:* ouvidorias e secretarias municipais de infraestrutura e limpeza urbana, que recebem ocorrências georreferenciadas, classificadas por IA e acompanhadas de imagem.
- *Beneficiário final:* população, conselhos de bairro e gestão municipal, que podem utilizar os registros para priorizar e planejar ações de manutenção.

A captura ocorre durante o deslocamento, no momento em que a irregularidade é visualizada, sem exigir interação contínua com o smartphone.

## 2. Solução e escopo

O UrbanSense é um sistema de identificação e registro de irregularidades urbanas que integra óculos inteligentes Meta Ray-Ban, aplicativo móvel e uma API de visão computacional. A câmera captura imagens periodicamente quando o aplicativo detecta movimento; o GPS fornece as coordenadas. A API processa as imagens com o modelo YOLO11n, identifica *resíduos (Trash)* e *buracos (pothole)*, associa a localização e encaminha a ocorrência ao órgão responsável por e-mail. O usuário recebe retorno por áudio nos óculos.

| Contemplado | Fora do escopo |
|---|---|
| Captura periódica condicionada ao movimento | Vídeo contínuo e análise de todos os quadros |
| Detecção de resíduos e buracos com YOLO11n | Inclusão de outras categorias |
| Georreferenciamento por GPS | Rastreamento contínuo do usuário |
| Confirmação por áudio e contingências | Acompanhamento da resolução da ocorrência |

A proposta reduz a fricção do registro manual e fornece evidências visuais e geográficas para apoiar a triagem municipal.

## 3. Artigo-base

Título: Automatic pothole detection, publicado na Transportation Research Procedia, v. 74, p. 1164–1170, nos anais da TRANSCOM 2023: 15th International Scientific Conference on Sustainable, Modern and Safe Transport.
Autores: Eva Lieskovská, Maroš Jakubec, Boris Bučko e Katarína Zábovská.
Ano: 2023.
DOI: https://doi.org/10.1016/j.trpro.2023.11.257
Método: o conjunto de dados do artigo é composto por imagens de 1920 × 1080 pixels do pavimento de uma via do distrito industrial de uma cidade, extraídas de vídeos e nomeadas como Vid (ID do dia) _ (direção) _ (ID do quadro). Foram anotadas 1052 imagens, com 1896 buracos e 232 tampas de bueiro (esta segunda classe foi adicionada para evitar que tampas circulares fossem confundidas com buracos), em condições de boa visibilidade. Os dados foram divididos em 70% para treinamento, 15% para validação e 15% para teste. Para a detecção de objetos, foram comparados cinco métodos:
Faster R-CNN: detector de dois estágios amplamente utilizado, com precisão comparável à de outros detectores de última geração. Uma rede separada identifica as propostas de regiões, no lugar da busca seletiva. No experimento, apresentou o pior desempenho.
Sparse R-CNN: utiliza um conjunto esparso e fixo de propostas de objetos treináveis, sem etapa de supressão de não máximos, e apresentou resultados comparáveis aos do YOLOv3 em mAP.
YOLOv3: detector de estágio único em tempo real, com backbone Darknet53, FPN para fusão de mapas de características, YOLO head para a detecção final em uma única etapa e bloco SPP para aumentar a precisão.
YOLOv5: versão com melhorias em relação às anteriores, com backbone CSP-Darknet53 e camada Focus (reduz parâmetros e tamanho do modelo), neck PAN (melhora o fluxo de informação e a localização), bloco C3 e ativação SiLU no lugar da Mish. Foi usada a implementação de Qi et al. (2022).
YOLOv7: obteve a maior precisão entre os modelos testados (0,880 mAP@0.5 e 40% de mAP@[0.5:0.95]), com tamanho de modelo bem menor que o do YOLOv3 e dos modelos R-CNN. Utiliza bag-of-freebies treináveis (treinamento mais pesado sem deixar a inferência mais lenta), E-ELAN no backbone, neck com FPN + PAN e as cabeças Lead Head e Auxiliary Head (supervisão profunda).
Componentes utilizados ou adaptados: do artigo serão aproveitados o paradigma de detecção de estágio único da família YOLO (backbone, neck e head com fusão de características em várias escalas), a abordagem de treinamento supervisionado com divisão em treinamento, validação e teste, e as métricas mAP@0.5 e mAP@[0.5:0.95]. A adaptação consiste em substituir o YOLOv7 pelo YOLO11n e trocar as classes do artigo (buraco e tampa de bueiro) pelas classes do projeto (resíduos e buracos), mantendo a detecção de buracos como ponto de contato direto com o artigo.

## 4. Modelo pré-treinado

Nome e versão: YOLO11n (variante nano da família Ultralytics YOLO11), arquivo de pesos yolo11n.pt.
Origem dos pesos: Ultralytics, obtidos pela biblioteca ultralytics e pelo repositório oficial do projeto, pré-treinados no conjunto COCO.
Licença: AGPL-3.0, com licença corporativa alternativa oferecida pela Ultralytics. O uso neste trabalho é acadêmico.
Tarefa original: detecção de objetos no conjunto COCO, com 80 classes. O modelo tem cerca de 2,6 milhões de parâmetros, o que o torna leve para treinamento em GPU gratuita e adequado a um cenário de execução embarcada.
Relação com o artigo: o artigo compara YOLOv3, YOLOv5 e YOLOv7 na detecção de buracos, e o YOLO11n pertence à mesma família de detectores de estágio único, com a mesma estrutura de backbone, neck e head. Como o modelo não é o mesmo do artigo, a correspondência metodológica se justifica por: (i) mesma tarefa, detecção de buracos em imagens de via pública; (ii) mesmo paradigma de detecção e mesmas métricas de avaliação (mAP@0.5 e mAP@[0.5:0.95]); e (iii) o YOLO11n ser uma evolução direta das versões avaliadas, o que permite aplicar o ajuste fino (fine-tuning) a partir de pesos pré-treinados para as classes do projeto.


## 5. Conjunto de dados

O conjunto de dados utilizado pelo projeto foi obtido na plataforma Figshare, no repositório intitulado: *"Pothole detection using computer vision in challenging conditions"* (https://doi.org/10.6084/m9.figshare.21214400).

### 5.1 Classes

O conjunto possui **2 classes**, anotadas por caixas delimitadoras (bounding boxes) no formato YOLO (`classe x_centro y_centro largura altura`, valores normalizados entre 0 e 1):

| ID | Classe | Descrição | Caixas | % |
|----|--------|-----------|-------:|--:|
| 0 | Buraco (*pothole*) | Depressões e falhas no pavimento | 3.166 | 87,8% |
| 1 | Tampa de bueiro (*manhole cover*) | Tampas metálicas circulares na via, que não são defeitos | 442 | 12,2% |

A classe 1 funciona como contraexemplo: tampas de bueiro se parecem com buracos em escuro/sombra, e incluí-las reduz falsos positivos. Os nomes das classes foram inferidos pela inspeção visual das anotações, pois o conjunto não traz arquivo de definição (.yaml) e os rótulos vêm apenas como 0 e 1.

### 5.2 Composição

São **2.099 imagens** (JPEG, 1920 × 1080 px), cada uma com um arquivo `.txt` de anotação, totalizando **3.608 caixas** (média de 1,7 por imagem, máximo de 10). Do total, 589 imagens (28%) não têm objetos anotados e servem como exemplos negativos. Os quadros foram extraídos de vídeos de câmera veicular e organizados por condição de captura:

| Pasta | Condição | Imagens | Buracos | Tampas de bueiro |
|-------|----------|--------:|--------:|-----------------:|
| Clear_weather | Tempo claro, dia | 1.052 | 1.896 | 232 |
| Evening | Entardecer | 250 | 286 | 53 |
| Night | Noite | 310 | 220 | 42 |
| Rain | Chuva / pavimento molhado | 286 | 400 | 75 |
| Sunset | Pôr do sol | 201 | 364 | 40 |

O conjunto original não define divisão em treino, validação e teste. Ela é feita por `prepare_dataset.py`.

### 5.3 Características das imagens

- **Ponto de vista:** câmera fixada atrás do para-brisa, com visão frontal da via. O capô do carro aparece na parte inferior do quadro e as imagens trazem data e hora sobrepostas no canto inferior direito.
- **Cor:** imagens RGB coloridas. Durante o dia predominam o cinza-azulado do asfalto, o verde da vegetação e o azul do céu. Ao entardecer e no pôr do sol, o céu e a luz ficam alaranjados e mais quentes.
- **Iluminação:**
  - *Tempo claro:* boa luminosidade, com sombras de árvores e de viadutos sobre a via.
  - *Chuva:* céu encoberto, asfalto molhado e reflexos. Gotas no para-brisa degradam parte dos quadros.
  - *Noite:* imagens muito escuras (brilho médio cerca de 5 vezes menor que o das imagens diurnas). A via é iluminada apenas pelos faróis, com áreas estouradas e muito ruído.
- **Cenário:** vias urbanas e industriais, com pavimento irregular, remendos, rachaduras, carros estacionados, vegetação e viadutos ao fundo.
- **Tamanho dos objetos:** as caixas são pequenas em relação ao quadro. A mediana é de cerca de 90 × 37 px em imagens de 1920 × 1080 px, e 88% dos buracos têm área menor que 96² px. Por isso o redimensionamento para a entrada do modelo deve ser feito com cuidado, para não perder os objetos menores.

## 6. Arquitetura e checkpoints

- *Câmera:* os óculos Meta Ray-Ban são acessados pelo Meta Wearables Device Access Toolkit. O aplicativo gerencia a sessão e o fluxo de vídeo, captura fotos e decodifica imagens HEVC em thread dedicada. Quando os óculos não estão disponíveis, utiliza a câmera do celular.
- *IA:* YOLO11n é carregado uma única vez na API e reutilizado entre requisições. A saída contém classe, confiança e caixa delimitadora; uma regra determinística converte o resultado em mensagem textual e resposta por voz.
- *Backend:* API em Python/FastAPI, com o modelo residente em memória por singleton thread-safe. A implantação utiliza Docker Compose em uma VPS Oracle Cloud Free dedicada.
- *Áudio:* a API retorna uma mensagem de feedback que o aplicativo reproduz nos alto-falantes dos óculos.

## 7. Plano de adaptação e avaliação

O treinamento foi realizado no Google Colab com GPU NVIDIA T4, utilizando Python, PyTorch, Ultralytics e o dataset do Figshare. O pipeline mantém o preparo das anotações e o ajuste fino do YOLO11n. Foram avaliadas execuções com 50, 100 e 200 épocas, resolução de 640 pixels e parada antecipada com paciência de 10 épocas.

A avaliação considera tempo de execução, mAP@0.5 e mAP@[0.5:0.95], no resultado geral e por classe. O baseline é o treinamento local em CPU, mantendo arquitetura, dados e configuração comparáveis. Nos experimentos, o treinamento em CPU levou aproximadamente 250-300 segundos por época, enquanto a GPU T4 levou cerca de 15 segundos.

O melhor resultado de validação reportado foi de aproximadamente *81,8% de mAP@0.5* (95,9% para buracos e 67,8% para resíduos). O conjunto de teste possui apenas uma instância de resíduos, o que impede uma avaliação estatisticamente confiável dessa classe; por isso, a análise principal utiliza a validação. O treinamento apresentou saturação próxima desse resultado, com melhor época em 92 e parada antecipada na época 102.

*Limitações:* o conjunto de dados é pequeno e não representa plenamente condições reais, como sombras dinâmicas, borramento por movimento, diferentes pavimentações e variações de iluminação. Há também inconsistência na anotação de resíduos. A captura em intervalos pode deixar de registrar ocorrências entre duas imagens. Como trabalhos futuros, prevê-se coleta de imagens em campo, revisão padronizada das anotações, balanceamento das classes e avaliação de um modelo multimodal (VLM) para auditoria contextual.

### Divisão das atividades

- *Emanuele:* adaptação do pipeline para o Colab, configuração do ambiente e integração do treinamento com GPU.
- *Gabriel:* preparação do conjunto de dados, revisão das anotações e treinamento do YOLO11n.
- *Pedro:* avaliação dos resultados, comparação com o baseline em CPU, análise das métricas e documentação.

O princípio de privacidade do projeto é *“registrar o problema, não a pessoa”*: o objetivo é mapear a irregularidade urbana, e não identificar quem a causou.

## 11. Como executar (treinamento e avaliação)

O treinamento é um ambiente isolado de estudo e prova de conceito; não integra o aplicativo nem o backend de produção. Todo o código está em dois notebooks em `src/`.

### Estrutura

```text
src/
├── pre-processamento.ipynb   # validação do dataset e divisão 70/15/15
└── finetuning.ipynb          # fine-tuning do YOLO11n, avaliação e registro
data/                         # dataset (não versionado)
runs/                         # saídas do Ultralytics
artifacts/                    # evidências (curvas, métricas, pip_freeze)
```

### 1. Ambiente

```powershell
python -m venv .venv
.venv\Scripts\python.exe -m pip install -r requirements.txt
```

Os notebooks localizam a raiz do projeto procurando uma pasta chamada `urbansense`; mantenha esse nome ao clonar.

### 2. Dataset (não está no repositório)

1. Baixe o dataset no Figshare: https://doi.org/10.6084/m9.figshare.21214400
2. Extraia as imagens e anotações (`.jpg` + `.txt` no formato YOLO) em `data/dataset/`.

### 3. Pré-processamento

Execute `src/pre-processamento.ipynb`. Ele valida as imagens e anotações, divide em treino/validação/teste (70/15/15, semente 42, estratificada) e gera `data/Potholes_dataset_dividido/` com `data.yaml` e `manifest.csv`.

### 4. Fine-tuning e avaliação

Execute `src/finetuning.ipynb`. Parâmetros no próprio notebook (YOLO11n pré-treinado, 640 px, batch 8, paciência 10, semente 42). Ele treina, avalia o `best.pt` no conjunto de teste, salva métricas por classe, curvas e o registro do experimento em `artifacts/`. Para GPU (por exemplo, Colab com T4), altere `DEVICE` e `BATCH_SIZE` na célula de configuração.

### 5. Resultados e pesos

Os resultados do fine-tuning (curvas, matrizes de confusão, métricas e imagens com detecções) estão versionados em `runs/potholes-yolo11n-finetuning-v1/` (treino/validação) e `runs/potholes-yolo11n-finetuning-v1-test/` (teste). O registro do experimento, as curvas e o `pip_freeze.txt` ficam em `artifacts/potholes-yolo11n-finetuning-v1/`.

Os arquivos de pesos (`*.pt`) não são versionados. Eles são gerados ao executar `src/finetuning.ipynb` e ficam em `runs/potholes-yolo11n-finetuning-v1/weights/best.pt`. Para usar: `YOLO("best.pt")` com a biblioteca `ultralytics`.

# UrbanSense AI

**Evento:** AI Glasses Brasil  
**Disciplina:** Tópicos Avançados de IA — 2026.2  
**Desenvolvedores:** Carlos Henrique Jeronimo Dias, Gabriel Lopes de Albuquerque e Márcio Ferreira Lima  
**Equipe acadêmica:** Pedro Martins, Gabriel Lopes e Emanuele Maria

## 1. Problema e usuários-alvo

O registro de irregularidades urbanas, como descarte irregular de lixo e buracos nas vias, exige que o cidadão interrompa seu deslocamento, pegue o celular, abra um aplicativo, fotografe, classifique o problema e informe sua localização. Esse processo pode consumir de 60 a 120 segundos e desestimula principalmente quem circula com as mãos ocupadas. Como consequência, muitas ocorrências não são comunicadas, e os registros municipais tendem a ser esparsos e imprecisos.

O UrbanSense atende a três perfis:
- **Usuário primário:** entregadores, mototaxistas, ciclistas, agentes comunitários, motoristas e pedestres, que identificam problemas durante seus trajetos.
- **Usuário receptor:** ouvidorias e secretarias municipais de infraestrutura e limpeza urbana, que recebem ocorrências georreferenciadas, classificadas por IA e acompanhadas de imagem.
- **Beneficiário final:** população, conselhos de bairro e gestão municipal, que podem utilizar os registros para priorizar e planejar ações de manutenção.

A captura ocorre durante o deslocamento, no momento em que a irregularidade é visualizada, sem exigir interação contínua com o smartphone.

## 2. Solução e escopo

O UrbanSense é um sistema de identificação e registro de irregularidades urbanas que integra óculos inteligentes Meta Ray-Ban, aplicativo móvel e uma API de visão computacional. A câmera captura imagens periodicamente quando o aplicativo detecta movimento; o GPS fornece as coordenadas. A API processa as imagens com o modelo YOLO11n, identifica **resíduos (Trash)** e **buracos (pothole)**, associa a localização e encaminha a ocorrência ao órgão responsável por e-mail. O usuário recebe retorno por áudio nos óculos.

| Contemplado | Fora do escopo |
|---|---|
| Captura periódica condicionada ao movimento | Vídeo contínuo e análise de todos os quadros |
| Detecção de resíduos e buracos com YOLO11n | Inclusão de outras categorias |
| Georreferenciamento por GPS | Rastreamento contínuo do usuário |
| Confirmação por áudio e contingências | Acompanhamento da resolução da ocorrência |

A proposta reduz a fricção do registro manual e fornece evidências visuais e geográficas para apoiar a triagem municipal.

## 3. Artigo-base

**Lieskovská, Eva; Jakubec, Maroš; Bučko, Boris; Zábovská, Katarína (2023).** *Automatic pothole detection*. Transportation Research Procedia, v. 74, p. 1164–1170. TRANSCOM 2023. DOI: https://doi.org/10.1016/j.trpro.2023.11.314.

O estudo utiliza 1.052 imagens de pavimento, com 1.896 buracos e 232 tampas de bueiro, divididas em 70% para treinamento, 15% para validação e 15% para teste. Compara Faster R-CNN, Sparse R-CNN, YOLOv3, YOLOv5 e YOLOv7. O YOLOv7 alcançou o melhor resultado, com mAP@0.5 de 0,880 e mAP@[0.5:0.95] de 0,40.

O UrbanSense adota o mesmo paradigma de detecção de objetos da família YOLO e avaliação por métricas mAP, mas utiliza YOLO11n e adapta as classes para resíduos e buracos. A detecção de buracos constitui o principal ponto de correspondência com o artigo.

## 4. Modelo pré-treinado

Foi utilizado o **YOLO11n**, variante nano da Ultralytics, com pesos `yolo11n.pt` pré-treinados no COCO (80 classes). O modelo possui aproximadamente 2,6 milhões de parâmetros e é distribuído sob licença AGPL-3.0, com opção de licença corporativa.

Embora o COCO não contenha uma classe específica para buracos, seus pesos fornecem representações visuais úteis para o ajuste fino (*fine-tuning*) com o conjunto de dados do projeto. A arquitetura mantém o paradigma de detector de estágio único, com backbone, neck e head.

## 5. Conjunto de dados

O conjunto foi obtido pela plataforma Roboflow:
- **Workspace:** `gabriel-lopes-de-albuquerque`
- **Projeto:** `garbage-detection-bw9fl-uek6r`
- **Versão:** 1
- **Total:** 1.021 imagens — 850 para treinamento, 116 para validação e 55 para teste.
- **Classes:** `Trash` (descarte irregular e acúmulo de resíduos) e `pothole` (buracos e avarias no pavimento).

As anotações originais são heterogêneas: `Trash` utiliza caixas retangulares e `pothole` utiliza polígonos de segmentação. Como o detector trabalha com *bounding boxes*, a etapa `prepare_dataset.py` converte cada polígono na menor caixa retangular que o contém. Essa conversão é necessária para que as anotações de buracos não sejam descartadas durante o treinamento.

## 6. Arquitetura e checkpoints

- **Câmera:** os óculos Meta Ray-Ban são acessados pelo Meta Wearables Device Access Toolkit. O aplicativo gerencia a sessão e o fluxo de vídeo, captura fotos e decodifica imagens HEVC em thread dedicada. Quando os óculos não estão disponíveis, utiliza a câmera do celular.
- **IA:** YOLO11n é carregado uma única vez na API e reutilizado entre requisições. A saída contém classe, confiança e caixa delimitadora; uma regra determinística converte o resultado em mensagem textual e resposta por voz.
- **Backend:** API em Python/FastAPI, com o modelo residente em memória por singleton thread-safe. A implantação utiliza Docker Compose em uma VPS Oracle Cloud Free dedicada.
- **Áudio:** a API retorna uma mensagem de feedback que o aplicativo reproduz nos alto-falantes dos óculos.

## 7. Plano de adaptação e avaliação

O treinamento foi realizado no Google Colab com GPU NVIDIA T4, utilizando Python, PyTorch, Ultralytics e Roboflow. O pipeline mantém o preparo das anotações e o ajuste fino do YOLO11n. Foram avaliadas execuções com 50, 100 e 200 épocas, resolução de 640 pixels e parada antecipada com paciência de 10 épocas.

A avaliação considera tempo de execução, mAP@0.5 e mAP@[0.5:0.95], no resultado geral e por classe. O baseline é o treinamento local em CPU, mantendo arquitetura, dados e configuração comparáveis. Nos experimentos, o treinamento em CPU levou aproximadamente 250–300 segundos por época, enquanto a GPU T4 levou cerca de 15 segundos.

O melhor resultado de validação reportado foi de aproximadamente **81,8% de mAP@0.5** (95,9% para buracos e 67,8% para resíduos). O conjunto de teste possui apenas uma instância de resíduos, o que impede uma avaliação estatisticamente confiável dessa classe; por isso, a análise principal utiliza a validação. O treinamento apresentou saturação próxima desse resultado, com melhor época em 92 e parada antecipada na época 102.

**Limitações:** o conjunto de dados é pequeno e não representa plenamente condições reais, como sombras dinâmicas, borramento por movimento, diferentes pavimentações e variações de iluminação. Há também inconsistência na anotação de resíduos. A captura em intervalos pode deixar de registrar ocorrências entre duas imagens. Como trabalhos futuros, prevê-se coleta de imagens em campo, revisão padronizada das anotações, balanceamento das classes e avaliação de um modelo multimodal (VLM) para auditoria contextual.

### Divisão das atividades

- **Emanuele:** adaptação do pipeline para o Colab, configuração do ambiente e integração do treinamento com GPU.
- **Gabriel:** preparação do conjunto de dados, revisão das anotações e treinamento do YOLO11n.
- **Pedro:** avaliação dos resultados, comparação com o baseline em CPU, análise das métricas e documentação.

## 8. Exceções e contingências

| Cenário | Comportamento |
|---|---|
| Sem conectividade | Imagem, GPS e metadados são armazenados localmente para reenvio. O usuário recebe aviso por voz. |
| GPS indisponível | Utiliza a última posição conhecida, marcada com precisão reduzida; o fluxo não é bloqueado. |
| Nenhuma irregularidade detectada | Captura manual é encaminhada para validação humana; captura automática é descartada silenciosamente. |
| Permissão de câmera negada | O aplicativo solicita acesso; se negado, desativa a captura silenciosa. |
| Bateria dos óculos abaixo de 15% | Captura automática é pausada; permanecem disponíveis capturas manuais. |
| Óculos desconectados ou dobradiça fechada | O fluxo dos óculos é encerrado e o aplicativo passa a usar a câmera do celular. |

## 9. Decisões técnicas e trade-offs

1. **Inferência no servidor:** o YOLO é executado na API FastAPI, e não no dispositivo. Isso evita consumo adicional de bateria, distribuição dos pesos e diferenças de desempenho entre aparelhos. Em contrapartida, exige conectividade e adiciona latência, mitigadas pela fila local de reenvio.
2. **Modelo especializado:** o YOLO11n foi ajustado para as classes necessárias ao projeto. Modelos genéricos sem ajuste foram descartados por não contemplarem a taxonomia urbana. O desempenho, porém, depende da qualidade e representatividade do dataset.
3. **Captura condicionada ao movimento:** imagens são capturadas a cada 10 segundos durante caminhada ou corrida; em repouso, câmera e GPS ficam suspensos. Isso economiza bateria, mas pode deixar ocorrências entre capturas sem registro.
4. **Backend unificado em Python:** FastAPI e inferência compartilham o mesmo ecossistema, simplificando integração e implantação. O acoplamento pode exigir ajustes de escalabilidade conforme aumente a carga.
5. **VPS dedicada:** a API foi implantada em VPS Oracle Cloud Free com Docker Compose. A opção foi preferida a PaaS gratuitas que apresentaram *cold start* de aproximadamente dois minutos; na VPS, as respostas ficaram em torno de 5 a 10 segundos. A contrapartida é administrar o servidor Linux e os contêineres.

## 10. Originalidade e comparação com soluções existentes

**Colab:** permite que cidadãos registrem problemas urbanos e acompanhem solicitações, mas depende de interação manual pelo celular, incluindo fotografia, classificação e descrição.

**Fiscalize Fortaleza:** encaminha denúncias à fiscalização municipal e fornece protocolo rastreável, mas exige identificação e preenchimento de formulário.

**Diferencial do UrbanSense:** combina captura sem as mãos durante o deslocamento, uso de um wearable já utilizado pelo cidadão, classificação automática especializada em resíduos e buracos, georreferenciamento e encaminhamento com retorno por voz. Em vez de depender exclusivamente de denúncias digitadas ou de sensores instalados pela prefeitura, utiliza a circulação cotidiana dos cidadãos como fonte distribuída de observações.

O princípio de privacidade do projeto é **“registrar o problema, não a pessoa”**: o objetivo é mapear a irregularidade urbana, e não identificar quem a causou.

## 11. Repositório de treinamento — Garbage Detection Training

O repositório de treinamento é um ambiente isolado de estudo e prova de conceito; não integra diretamente o aplicativo nem o backend de produção.

### Estrutura principal

```text
src/
├── pipeline/
│   ├── download_dataset.py
│   ├── prepare_dataset.py
│   ├── train.py
│   ├── resume_train.py
│   └── evaluate.py
├── app/
│   ├── predict.py
│   └── serve.py
├── resultados/
├── colab_train.ipynb
├── data/       # dataset baixado; não versionar
└── runs/       # saídas locais; não versionar
```

### Instalação no Windows

```powershell
cd src
python -m venv .venv
.venv/Scripts/python.exe -m pip install -r requirements.txt
```

Copie `.env.example` para `.env` e informe a chave da API do Roboflow, disponível em https://app.roboflow.com/settings/api.

### Treinamento no Colab

1. Abra `colab_train.ipynb` no Google Colab.
2. Selecione **Ambiente de execução → Alterar o tipo de ambiente de execução → GPU**.
3. Execute todas as células e informe a chave do Roboflow quando solicitada.
4. Baixe o modelo e os gráficos gerados pela última célula.

O notebook instala as dependências, baixa o conjunto de dados e prepara as anotações automaticamente. Para uma nova rodada, ajuste `EPOCHS`.

### Treinamento local e avaliação

```powershell
.venv/Scripts/python.exe pipeline/download_dataset.py
.venv/Scripts/python.exe pipeline/train.py
.venv/Scripts/python.exe pipeline/evaluate.py
```

Para retomar um treinamento interrompido, execute `pipeline/resume_train.py`. O YOLO salva checkpoints ao final de cada época.

### Teste de inferência

```powershell
.venv/Scripts/python.exe app/predict.py caminho/para/sua_foto.jpg
```

A imagem com as detecções será salva em `runs/predictions/`. Por padrão, é utilizado `runs/garbage-detector/weights/best.pt`.

### Pipeline resumido

1. `download_dataset.py`: baixa e organiza o conjunto de dados.
2. `prepare_dataset.py`: corrige os caminhos do `data.yaml`, converte polígonos em caixas delimitadoras e remove caches de anotações.
3. `train.py`: executa o treinamento do YOLO.
4. `evaluate.py`: gera métricas e avaliação visual.


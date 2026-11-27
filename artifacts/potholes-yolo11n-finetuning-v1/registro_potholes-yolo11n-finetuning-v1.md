# Registro do experimento — Fine-tuning inicial

Responsável: PEDRO
Data: 2026-11-27 15:33:51 Hora Padrão de Buenos Aires
Tipo de execução: Fine-tuning inicial (YOLO11n)

## Configuração

- Modelo de origem: yolo11n.pt
- Pesos pré-treinados: sim
- Épocas máximas: 50
- Tamanho de imagem: 640
- Batch: 8
- Paciência: 10
- Dispositivo: cpu
- Semente: 42
- Dataset: c:\Workspace\Study\UPE\urbansense\data\Potholes_dataset_dividido\data.yaml

## Resultados no conjunto de teste

- Precision: 0.7027
- Recall: 0.6077
- mAP50: 0.6710
- mAP50-95: 0.2909

## Análise dos resultados

Preencher após analisar:

- Distribuição e quantidade de exemplos por classe:
- Classes com poucas anotações:
- Falsos positivos observados:
- Falsos negativos observados:
- Qualidade/consistência das caixas anotadas:
- Sinais de overfitting ou underfitting nas curvas:
- Limitações de tempo/desempenho por treinamento em CPU:

## Ações para a próxima execução

1. Revisar os erros nas imagens de teste e validação, classificando falsos positivos e falsos negativos.
2. Conferir o balanceamento das classes e a quantidade de instâncias por subconjunto.
3. Auditar as anotações YOLO para identificar caixas ausentes, incorretas ou inconsistentes.
4. Ajustar hiperparâmetros com base no conjunto de validação, sem utilizar o teste para seleção.
5. Executar nova rodada identificada com outro nome, preservando esta execução.
6. Comparar os resultados utilizando as mesmas métricas e protocolo.

## Artefatos gerados

- Configuração: c:\Workspace\Study\UPE\urbansense\artifacts\potholes-yolo11n-finetuning-v1\configuracao_treino.json
- Ambiente: c:\Workspace\Study\UPE\urbansense\artifacts\potholes-yolo11n-finetuning-v1\ambiente.json
- Dependências: c:\Workspace\Study\UPE\urbansense\artifacts\potholes-yolo11n-finetuning-v1\pip_freeze.txt
- Histórico de treinamento: c:\Workspace\Study\UPE\urbansense\artifacts\potholes-yolo11n-finetuning-v1\results.csv
- Métricas de teste: c:\Workspace\Study\UPE\urbansense\artifacts\potholes-yolo11n-finetuning-v1\metricas_teste.json
- Métricas por classe: c:\Workspace\Study\UPE\urbansense\artifacts\potholes-yolo11n-finetuning-v1\metricas_por_classe.csv
- Comparação visual: c:\Workspace\Study\UPE\urbansense\artifacts\potholes-yolo11n-finetuning-v1\comparacao_verdade_vs_previsao_v1.png
- Pesos ajustados: c:\Workspace\Study\UPE\urbansense\runs\potholes-yolo11n-finetuning-v1\weights\best.pt
- Diretório do treinamento: c:\Workspace\Study\UPE\urbansense\runs\potholes-yolo11n-finetuning-v1

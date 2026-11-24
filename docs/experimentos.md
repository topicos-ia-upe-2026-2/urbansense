# Registro de experimentos

> Documente cada execução relevante: data, configuração, hiperparâmetros, dados usados,
> métricas obtidas e observações. Isso fundamenta as seções de resultados da E2, E3 e E4.

## Baseline

| Data | Descrição | Configuração | Métricas | Observações |
|---|---|---|---|---|
| — | Avaliação do YOLO11n original, sem fine-tuning, utilizando o conjunto de teste. | `yolo11n.pt`; imgsz=640; dataset de potholes. | — | Avaliação ainda não realizada. |

## Versões ajustadas (fine-tuning)

| Data | Descrição | Hiperparâmetros | Dados | Métricas | Observações |
|---|---|---|---|---|---|
| 21/11/2026 | Fine-tuning do YOLO11n com pesos pré-treinados. | epochs=50; imgsz=640; batch=8; patience=10; seed=42; device=CPU. | `Potholes_dataset_dividido` | Precision: 0,7027; Recall: 0,6077; mAP50: 0,6710; mAP50-95: 0,2909. | Primeira execução ajustada. Análise dos erros e curvas de treinamento pendente. |

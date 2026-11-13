# Relatório técnico — UrbanSense

**Grupo:** GXX | **Integrantes:** Pedro Martins, Gabriel Lopes e Emanuele Maria | **2026.2**

## 1. Problema e contexto

O UrbanSense busca simplificar o registro de irregularidades urbanas, como descarte irregular de resíduos e buracos nas vias públicas. A solução utiliza óculos inteligentes Meta Ray-Ban para capturar imagens durante o deslocamento do usuário, reduzindo a necessidade de interação com o smartphone.

As ocorrências são identificadas por inteligência artificial, georreferenciadas e encaminhadas aos órgãos municipais responsáveis.

## 2. Fundamentação científica

O projeto utiliza técnicas de visão computacional e aprendizagem profunda, com a arquitetura YOLO11n, baseada em Redes Neurais Convolucionais (CNN).

O modelo foi adaptado por fine-tuning para reconhecer duas classes: resíduos (Trash) e buracos (Pothole).

**Artigo-base:** Pendente.

## 3. Modelo e adaptação

- **Modelo:** YOLO11n (2,58 milhões de parâmetros).
- **Pesos iniciais:** Pré-treinados no COCO.
- **Adaptação:** Fine-tuning para duas classes.
- **Dataset:** Roboflow, versão 1.
- **Treinamento:** Google Colab com GPU NVIDIA T4.
- **Resultado:** mAP50 de 81,8% na validação.

## 4. Dados

Dataset composto por 1.021 imagens, distribuídas em:

| Partição | Imagens |
|---|---:|
| Treinamento | 850 |
| Validação | 116 |
| Teste | 55 |

As classes utilizadas são resíduos e buracos. A divisão foi mantida conforme disponibilizada na origem.

**Licença do dataset:** Pendente de confirmação.

## 5. Experimentos

Foram realizados treinamentos com 50, 100 e 200 épocas para avaliar a evolução do desempenho.

O melhor resultado foi de 81,8% de mAP50, com parada antecipada na época 102 e melhor desempenho na época 92.

**Baseline, hiperparâmetros e demais métricas:** Pendente de consolidação em `experimentos.md`.

## 6. Aplicação

O sistema integra os óculos Meta Ray-Ban a um aplicativo e uma API desenvolvida com FastAPI.

O fluxo consiste na captura periódica de imagens durante o deslocamento, detecção automática das irregularidades pelo YOLO11n, associação das coordenadas geográficas e encaminhamento das ocorrências.

O sistema também contempla armazenamento local para situações de ausência de conectividade.

**Infraestrutura:** VPS Oracle Cloud Free, com implantação utilizando Docker Compose.

## 7. Resultados e análise de falhas

O modelo alcançou:
- **mAP50 geral:** 81,8%.
- **Buracos:** 95,9%.
- **Resíduos:** 67,8%.

O desempenho inferior na identificação de resíduos está relacionado, entre outros fatores, ao desbalanceamento e à inconsistência das anotações do dataset.

O aumento do número de épocas não proporcionou ganhos relevantes, indicando limitações relacionadas principalmente aos dados utilizados.

**Comparação com baseline e latência operacional:** Pendente.

## 8. Limitações e uso responsável

As principais limitações envolvem a representatividade do dataset, o desbalanceamento das classes, a variabilidade ambiental e a dependência de conectividade para realizar inferências no servidor.

O sistema prioriza o registro das irregularidades, não a identificação de indivíduos. As imagens devem ser tratadas considerando a privacidade de pessoas e informações potencialmente identificáveis.

Como trabalhos futuros, prevê-se a coleta de imagens em campo, padronização das anotações, balanceamento das classes e integração com um modelo de visão e linguagem (VLM).

## 9. Referências

Pendente de consolidação das referências bibliográficas utilizadas no projeto.
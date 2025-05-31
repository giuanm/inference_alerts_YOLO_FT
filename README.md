# PT

### * Below we have the English version of the README after the Portuguese README

![Estilete_Preview](https://github.com/user-attachments/assets/72882f4d-dac0-4c9e-9bd6-4f1857b60b94)

# Uso do Fine-tuning de Modelo YOLOv8L para Detecção de Objetos Cortantes

Este repositório contém o código e a documentação para o sistema de inferência e alerta desenvolvido como parte do Hackathon da Pós-Graduação FIAP POS TECH em Inteligência Artificial para Desenvolvedores. O projeto visa processar vídeos de câmeras de segurança, detectar objetos cortantes (facas e estiletes) utilizando um modelo de Deep Learning treinado e gerar alertas quando uma detecção relevante ocorrer.

Este projeto é a **segunda parte** da solução, focada na aplicação prática do modelo treinado e na implementação do pipeline de processamento de vídeo e alerta.

## Tecnologias

As principais tecnologias e bibliotecas utilizadas nesta etapa do projeto incluem:

*   **Python:** Linguagem de programação principal para o pipeline de inferência.
*   **Ultralytics (YOLOv8):** Biblioteca central para carregar o modelo treinado e executar a inferência (detecção de objetos) em cada frame do vídeo.
*   **OpenCV (`opencv-python-headless`):** Biblioteca essencial para ler frames do vídeo de entrada, manipular imagens e escrever o vídeo de saída com as detecções visualizadas.
*   **NumPy:** Suporte a operações numéricas e manipulação de arrays (utilizado em conjunto com OpenCV e Ultralytics).
*   **SMTP (`smtplib`, `email.message`):** Bibliotecas padrão do Python utilizadas na tentativa inicial de envio de alerta por e-mail (funcionalidade adaptada para registro em log).

## Serviços Utilizados

*   **GitHub:** Hospedagem do repositório e controle de versão.
*   **Google Colab:** Ambiente de notebook baseado em nuvem (utilizando GPUs Tesla T4) para executar o pipeline de inferência.
*   **Google Drive:** Armazenamento dos vídeos de teste de entrada, o modelo treinado (`.pt`) e os resultados da inferência (vídeo processado e arquivo de log).

## Processo de Desenvolvimento da Inferência e Alerta

O desenvolvimento desta etapa focou em criar um pipeline eficiente para aplicar o modelo treinado a dados de vídeo e implementar o sistema de alerta.

1.  **Integração do Modelo Treinado:** O pipeline utiliza o modelo YOLOv8L treinado na etapa anterior do projeto (cujas métricas de validação são detalhadas no [Repositório de Treinamento]([LINK DO SEU REPOSITÓRIO GITHUB DE TREINAMENTO])). Este modelo é carregado no início da execução.
    *   **Desafio Superado (Problema de Carregamento do Modelo):** Uma dificuldade significativa foi depurar um problema inesperado onde o modelo treinado não carregava ou não funcionava corretamente no ambiente de inferência, apesar de excelentes métricas de treino. Após extensa investigação, identificou-se que a incompatibilidade de versão da biblioteca `ultralytics` entre os ambientes de treino e inferência era a causa. Fixar a versão `8.3.120` em ambos os notebooks resolveu o problema, permitindo que o modelo treinado fosse finalmente utilizado.

2.  **Pipeline de Processamento de Vídeo:** Utilização do OpenCV para ler os vídeos de teste frame a frame. Cada frame é então passado para o modelo YOLOv8 para detecção de objetos.

3.  **Visualização das Detecções:** As detecções retornadas pelo modelo são desenhadas diretamente nos frames do vídeo (bounding boxes e labels) utilizando as funcionalidades da biblioteca Ultralytics.

4.  **Sistema de Alerta:** Implementação de uma lógica para gerar alertas com base nas detecções.
    *   **Limiar de Confiança:** Apenas detecções com confiança acima de um `CONFIDENCE_THRESHOLD` configurável são consideradas válidas para alerta.
    *   **Lógica de Cooldown:** Um mecanismo de cooldown por classe (`COOLDOWN_SECONDS`) foi implementado para evitar múltiplos alertas em frames consecutivos para o mesmo tipo de objeto.
    *   **Registro em Log:** Devido a desafios na configuração de envio de e-mail via `smtplib` (restrições de provedores como Gmail), o sistema de alerta foi adaptado para registrar as detecções relevantes em um arquivo de log (`.log`) no Google Drive.

5.  **Geração de Vídeo de Saída:** Os frames processados (com as detecções desenhadas) são compilados em um novo arquivo de vídeo (`.mp4`) utilizando o OpenCV, permitindo a visualização do desempenho do modelo.

6.  **Análise de Desempenho no Vídeo (Domain Shift):** Apesar das métricas de validação excelentes do modelo treinado (P=0.938, R=0.948, mAP50=0.966, mAP50-95=0.802 - detalhado no [Repositório de Treinamento]([LINK DO SEU REPOSITÓRIO GITHUB DE TREINAMENTO])), o desempenho visual no vídeo de teste pode apresentar limitações (alguns falsos positivos, misses, misclassificações). Isso se deve ao **Domain Shift**, ou seja, as diferenças nas condições das imagens de treino/validação e os frames do vídeo real (iluminação, ângulos, fundos, movimento, etc.). O modelo é excelente nos dados que viu, mas o vídeo apresenta variações que ele não aprendeu completamente a generalizar.

## Artefatos Gerados

*   `hackathon_inference_FINAL.ipynb`: Notebook Jupyter (Google Colab) contendo o código principal para o pipeline de inferência e alerta.
*   `requirements.txt`: Arquivo listando as dependências Python.
*   `video_processed_final_delivery.mp4`: Arquivo de vídeo de saída com as detecções desenhadas (gerado após a execução do notebook).
    ![Vídeo Processado Exemplo]\[[LINK PARA IMAGEM/GIF DE UM TRECHO DO VÍDEO PROCESSADO COM DETECÇÕES - Use a mesma imagem/GIF do outro README ou uma diferente e representativa]]
*   `detection_alerts_final_delivery.log`: Arquivo de texto contendo os alertas registrados durante o processamento do vídeo (gerado após a execução do notebook).

## Primeiros Passos

1.  Clone este repositório GitHub.
2.  Certifique-se de possuir o arquivo do modelo treinado final (`model_final_delivery.pt` ou o nome que você usou). Este arquivo é gerado pelo processo descrito no [Repositório de Treinamento]([LINK DO SEU REPOSITÓRIO GITHUB DE TREINAMENTO]). Faça upload deste arquivo para o seu Google Drive (ex: na pasta raiz do projeto).
3.  Faça download dos vídeos de teste fornecidos no desafio e coloque-os em uma pasta no seu Google Drive (ex: `PosTech_IA_Devs_Hackathon/videos_teste`).
4.  Abra o notebook de inferência (`hackathon_inference_FINAL.ipynb`) no Google Colab (certifique-se de selecionar o ambiente com GPU).
5.  Execute as células iniciais para montar o Google Drive e instalar as bibliotecas (verificando a versão correta do ultralytics, `8.3.120`).
6.  Na Célula 4, ajuste os caminhos `MODEL_PATH` e `VIDEO_TEST_PATHS` para apontar para os arquivos corretos no seu Google Drive. Ajuste o `CONFIDENCE_THRESHOLD` e `COOLDOWN_SECONDS` se desejar.
7.  Execute as células restantes (6 e 7).

## Como Usar

Após seguir os passos em "Primeiros Passos" e executar o notebook de inferência:

1.  O script processará cada vídeo listado em `VIDEO_TEST_PATHS`.
2.  O vídeo processado, com as detecções desenhadas, será salvo na pasta `processed_videos` dentro do seu diretório do projeto no Google Drive.
3.  Os alertas de detecção (para detecções acima do limiar e com cooldown) serão registrados no arquivo de log (`detection_alerts_final_delivery.log`) na pasta raiz do seu projeto no Google Drive.

## Funcionalidades

*   Processamento de vídeo.
*   Execução de inferência de detecção de objetos (YOLOv8) em frames de vídeo.
*   Filtragem de detecções por limiar de confiança.
*   Lógica de cooldown para alertas por classe.
*   Registro de alertas em arquivo de log.
*   Geração de vídeo de saída com visualização das detecções.

## Próximos Passos e Melhorias Futuras

Para evoluir este MVP para um sistema de produção e melhorar seu desempenho no mundo real:

*   **Melhoria do Dataset (Domain Adaptation):** Adicionar mais dados de treinamento que sejam representativos das condições de vídeos de segurança reais, incluindo frames anotados de vídeos de teste ou vídeos similares. Esta é a melhoria mais impactante para o problema de Domain Shift.
*   **Object Tracking:** Implementar algoritmos de rastreamento para identificar e seguir instâncias individuais de objetos ao longo do tempo, permitindo alertas mais precisos e análise de comportamento (ex: objeto X detectado por Y segundos na área Z).
*   **Detecção de Objetos Negativos:** Aumentar o conjunto de dados com imagens sem objetos cortantes para reduzir falsos positivos.
*   **Sistema de Alerta Robusto:** Implementar um sistema de alerta de nível de produção (ex: integração com plataformas de monitoramento, notificações push, banco de dados de eventos).

## Links

*   Repositório GitHub (Inferência e Alerta): [[[LINK DESTE REPOSITÓRIO GITHUB](https://github.com/giuanm)](https://github.com/giuanm/inference_alerts_YOLO_FT)]
*   Repositório GitHub (Treinamento e Dados): [[[LINK DESTE REPOSITÓRIO GITHUB](https://github.com/giuanm)](https://github.com/giuanm/FT_YOLO_sharp_objects)]
    *   Em caso de bugs sensíveis como vulnerabilidades de segurança, por favor, entre em contato diretamente com [SEU EMAIL PARA CONTATO DE SEGURANÇA] em vez de usar o issue tracker. Valorizamos seu esforço para melhorar a segurança e privacidade deste projeto!

## Versionamento

1.0 (Versão do modelo treinado)

## Autores

*   **Francisco Giuan**: giuanm ([[LINK PARA SEU PERFIL NO GITHUB](https://github.com/giuanm)])

Por favor, sigam no GitHub e juntem-se a nós!
Obrigado por visitar e boa codificação!


# EN

# Fine-tuning of YOLOv8L Model for Sharp Object Detection

This repository contains the code and documentation for the inference and alert system developed as part of the FIAP POS TECH Artificial Intelligence for Developers Graduate Program Hackathon. The project aims to process security camera videos, detect sharp objects (knives and box cutters) using a trained Deep Learning model, and generate alerts when a relevant detection occurs.

This project represents the **second part** of the solution, focusing on the practical application of the trained model and the implementation of the video processing and alerting pipeline.

## Technologies

The main technologies and libraries used in this stage of the project include:

*   **Python:** Main programming language for the inference pipeline.
*   **Ultralytics (YOLOv8):** Central library for loading the trained model and performing inference (object detection) on each video frame.
*   **OpenCV (`opencv-python-headless`):** Essential library for reading input video frames, manipulating images, and writing the output video with visualized detections.
*   **NumPy:** Support for numerical operations and array manipulation (used in conjunction with OpenCV and Ultralytics).
*   **SMTP (`smtplib`, `email.message`):** Standard Python libraries used in the initial attempt to send alerts via email (functionality adapted to log due to email provider restrictions).

## Services Used

*   **GitHub:** Repository hosting and version control.
*   **Google Colab:** Cloud-based notebook environment (using Tesla T4 GPUs) for running the inference pipeline.
*   **Google Drive:** Storage for input test videos, the trained model (`.pt`), and inference results (processed video and log file).

## Inference and Alert System Development Process

The development of this stage focused on creating an efficient pipeline to apply the trained model to video data and implement the alerting system.

1.  **Trained Model Integration:** The pipeline uses the YOLOv8L model trained in the previous stage of the project (whose validation metrics are detailed in the [Training Repository]([LINK DO SEU REPOSITÓRIO GITHUB DE TREINAMENTO])). This model is loaded at the beginning of the execution.
    *   **Challenge Overcome (Model Loading Issue):** A significant difficulty was debugging an unexpected problem where the trained model would not load or function correctly in the inference environment, despite excellent training metrics. After extensive investigation, it was identified that a version incompatibility of the `ultralytics` library between the training and inference environments was the likely cause. Fixing the version to `8.3.120` in both notebooks resolved the issue, allowing the trained model to be finally used.

2.  **Video Processing Pipeline:** Use of OpenCV to read test videos frame by frame. Each frame is then passed to the YOLOv8 model for object detection.

3.  **Detection Visualization:** Detections returned by the model are drawn directly onto the video frames (bounding boxes and labels) using the functionalities of the Ultralytics library.

4.  **Alerting System:** Implementation of logic to generate alerts based on detections.
    *   **Confidence Threshold:** Only detections with confidence above a configurable `CONFIDENCE_THRESHOLD` are considered valid for alerting.
    *   **Cooldown Logic:** A per-class cooldown mechanism (`COOLDOWN_SECONDS`) was implemented to prevent repeated alerts on consecutive frames for the same type of object.
    *   **Log Registration:** Due to challenges in configuring email sending via `smtplib` (email provider restrictions), the alerting system was adapted to log relevant detections to a log file (`.log`) on Google Drive.

5.  **Output Video Generation:** The processed frames (with drawn detections) are compiled into a new video file (`.mp4`) using OpenCV, allowing visualization of the model's performance.

6.  **Performance Analysis on Test Video (Domain Shift):** Despite the excellent validation metrics of the trained model (P=0.938, R=0.948, mAP50=0.966, mAP50-95=0.802 - detailed in the [Training Repository]([LINK DO SEU REPOSITÓRIO GITHUB DE TREINAMENTO])), visual performance on the test video may show limitations (some false positives, misses, misclassifications). This is due to **Domain Shift**, meaning differences in the conditions between training/validation images and the real video frames (lighting, angles, backgrounds, motion, etc.). The model is excellent on the data it saw, but the video presents a slightly different "world" it hasn't fully learned to generalize to.

## Generated Artifacts

*   `hackathon_inference_FINAL.ipynb`: Jupyter Notebook (Google Colab) containing the main code for the inference and alerting pipeline.
*   `requirements.txt`: File listing Python dependencies.
*   `video_processed_final_delivery.mp4`: Output video file with detections drawn (generated after notebook execution).
    ![Processed Video Example]\[[LINK PARA IMAGEM/GIF DE UM TRECHO DO VÍDEO PROCESSADO COM DETECÇÕES - Use the same image/GIF as in the other READMEs or a different representative one]]
*   `detection_alerts_final_delivery.log`: Text file containing the alerts logged during video processing (generated after notebook execution).

## Getting Started

1.  Clone this GitHub repository.
2.  Ensure you have the final trained model file (`model_final_delivery.pt` or the name you used). This file is generated by the process described in the [Training Repository]([LINK DO SEU REPOSITÓRIO GITHUB DE TREINAMENTO]). Upload this file to your Google Drive (e.g., in your project's root folder).
3.  Download the test videos provided in the challenge and place them in a folder on your Google Drive (e.g., `PosTech_IA_Devs_Hackathon/videos_teste`).
4.  Open the `hackathon_inference_FINAL.ipynb` notebook in Google Colab (ensure you select the environment with a GPU).
5.  Execute the initial cells to mount Google Drive and install libraries (checking for the correct ultralytics version, `8.3.120`).
6.  In Cell 4, adjust the `MODEL_PATH` and `VIDEO_TEST_PATHS` to point to the correct files on your Google Drive. Adjust `CONFIDENCE_THRESHOLD` and `COOLDOWN_SECONDS` if desired.
7.  Execute the remaining cells (6 and 7).

## How to Use

After following the steps in "Getting Started" and executing the inference notebook:

1.  The script will process each video listed in `VIDEO_TEST_PATHS`.
2.  The processed video, with detections drawn, will be saved in the `processed_videos` folder within your project directory on Google Drive.
3.  Detection alerts (for detections above the threshold and respecting cooldown) will be logged to the `detection_alerts_final_delivery.log` file in your project's root folder on Google Drive.

## Next Steps and Future Improvements

To evolve this MVP into a production system and improve its real-world performance:

*   **Dataset Improvement (Domain Adaptation):** Add more training data representative of real security video conditions, including annotated frames from test videos or similar footage. This is the most impactful improvement for the Domain Shift issue.
*   **Object Tracking:** Implement tracking algorithms to identify and follow individual sharp object instances over time, enabling more precise alerts and behavior analysis (e.g., object X detected in area Z for Y seconds).
*   **Negative Object Detection:** Significantly increase the dataset with images that do *not* contain sharp objects to reduce false positives.
*   **Robust Alerting System:** Implement a production-level alerting system (e.g., integration with monitoring platforms, push notifications, event database).

## Links

*   Repositório GitHub (Inferência e Alerta): [[[LINK DESTE REPOSITÓRIO GITHUB](https://github.com/giuanm)](https://github.com/giuanm/inference_alerts_YOLO_FT)]
*   Repositório GitHub (Treinamento e Dados): [[[LINK DESTE REPOSITÓRIO GITHUB](https://github.com/giuanm)](https://github.com/giuanm/FT_YOLO_sharp_objects)]
    *   In case of sensitive bugs like security vulnerabilities, please contact [SEU EMAIL PARA CONTATO DE SEGURANÇA] directly instead of using the issue tracker. We value your effort to improve the security and privacy of this project!

*   **Francisco Giuan**: giuanm ([[LINK PARA SEU PERFIL NO GITHUB](https://github.com/giuanm)])

Please follow on GitHub and join us!
Thanks for visiting and happy coding!

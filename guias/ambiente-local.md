# Ambiente local (já montado)

Este fork roda num computador que **já tem** as ferramentas do fluxo instaladas e testadas na GPU. Na fase 1 e na etapa de preparar, **confira o que existe e reaproveite**. Não crie ambiente novo nem reinstale nada sem a pessoa pedir.

## O que já existe
| Ferramenta | Onde roda | Uso no fluxo |
|---|---|---|
| Python 3.10 do sistema (`python`) com PyTorch 2.8 + CUDA 12.8 | GPU NVIDIA | base de todos os modelos |
| WhisperX (faster-whisper + alinhamento + pyannote) | GPU | transcrição com tempo por palavra (preparar) |
| Demucs | GPU | separar voz e fundo (preparar, sfx) |
| CLAP (`laion/clap-htsat-unfused`, via `transformers`) | GPU | busca de sons por descrição (sfx) |
| SAM2.1, Depth Anything V2, BEN2 (via `transformers`/`ben2`) | GPU | recorte, profundidade, remoção de fundo (motion, pós) |
| YuNet (`cv2.FaceDetectorYN`) | CPU | rosto para a câmera; reduza o quadro para ~640 px |
| MediaPipe | CPU | rosto e pontos do rosto (sem GPU no Windows) |
| PySceneDetect | CPU | cortes de cena (referências) |
| FFmpeg com NVENC (`h264_nvenc`, `hevc_nvenc`, `av1_nvenc`) | GPU | render e conversões |
| Node.js (`npx`) e as skills do HyperFrames | — | animações |

Os modelos baixados ficam no cache do Hugging Face e do PyTorch; os de rosto (YuNet `.onnx`, MediaPipe `.tflite`) numa pasta `models/` do estúdio. Pergunte o caminho na primeira vez e anote no `montagem.md`.

## Regras
- **Use o Python do sistema**, não um ambiente novo. Um ambiente novo baixaria o PyTorch de novo, quase sempre a versão só de CPU, e a transcrição ficaria muito mais lenta.
- **Antes de transcrever ou rodar um modelo**, confira: `python -c "import torch; print(torch.cuda.is_available())"`. Se der `False`, pare e avise; não siga na CPU sem dizer.
- **Depois de qualquer `pip install`**, confira de novo. Pacotes que dependem de `torch` podem trocar a versão CUDA pela de CPU.
- **Não instale outro OpenCV.** Já existe o `opencv-python-headless` 5.x. Pacotes que pedem `opencv-python` ou `opencv-contrib-python` (MediaPipe, PySceneDetect) foram instalados com `--no-deps`; faça igual. O aviso do `pip check` sobre esses dois é esperado.
- **Scripts de etapa** ficam na pasta de ferramentas e chamam o `python` do sistema.
- **Render de vídeo:** prefira os codificadores NVENC no FFmpeg (`-c:v h264_nvenc`, `hevc_nvenc` ou `av1_nvenc`). Se der erro de versão de driver, avise: o FFmpeg pede driver NVIDIA recente.
- **HyperFrames:** use as skills `hyperframes`, `hyperframes-cli` e `hyperframes-registry` que já estão disponíveis. A versão do pacote continua fixa por vídeo (ver `hyperframes.md`).
- **Ferramenta nova** (não listada acima): explique, peça o "sim", instale sem trocar o PyTorch nem o OpenCV e atualize esta lista.

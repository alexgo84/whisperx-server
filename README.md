<h1 align="center">WhisperX Server</h1>

This is a simple Server Application that receives audio file paths via the endpoint `POST transcribe`, it then uses Whisper ([WhisperX](https://github.com/m-bain/whisperX) to Transcribe and allign the transcription and outputs the result (`transcript.txt` and `subtitles.srt` files) to the directory of the input file, or to the directory that was indicated in the request. An additional JSON file `transcription_metadata.json` with some metadata will be added as well.

For the server specification (request structure and response behavior) see the OpenAPI specificaiton in `swagger.yaml`.

This server will use the Whisper model size `large-v2`. To improve performance on account of accuracy, change the value of `WHISPER_MODEL` in `run.sh` to either of `["large", "large-v2", "medium", "small", "tiny"]

For any other documentation refer to [WhisperX readme](https://github.com/m-bain/whisperX).

### Some Notes:

- In order to not spend resources on loading the model - we load it once (lazily) and reuse it afterwards. As a result - the model type and size is specified via the environment variable WHISPER_MODEL.
- The server will only process one video at a time and reject incoming requests while processing is taking place. We can change this to a queue like behaviour in the future.
- The correct Python version to run this is 3.8. Avoid unpleasantness by sticking to 3.8.
- Note that Python 3.8 should be used to install dependecies (pip with Python 3.8 was used succesfully)
- After installing the pre-requirsites as indicated in the WhisperX repository, run the Server by executing the script `run_gpu.sh` to execute with CUDA or `run_cpu.sh` for running on the CPU (slow).
- For convenience sake - here's a curl command to trigger the endopint:

For Transcribing a file in the original language:
```
curl -X POST http://<machine_IP>:8080/transcribe \
    -H "Content-Type: application/json" \
    -d '{"audioPath": "examples/sample_file.mp4"}'
```

- In order to translate to English, add the query parameter `"task": "translate"`
```
curl -X POST http://<machine_IP>:8080/transcribe \
    -H "Content-Type: application/json" \
    -d '{"audioPath": "examples/sample_file.mp4", "task": "translate"}'
```


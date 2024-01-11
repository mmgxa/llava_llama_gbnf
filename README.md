<div align="center">

# Deploying LLaVA with Constraint-based Sampling on CPU

</div>

# Overview
In this repository, we deploy a LLaVA model on a CPU backend using llama.cpp. The model is intended to describe a car and the ouput is constrained to a JSON file - using the GBNF notation - that lists some properties of the car.

# Demo
[![Demo](https://img.youtube.com/vi/X2XkdQR5nrQ/hqdefault.jpg)](https://www.youtube.com/embed/X2XkdQR5nrQ)

## Conversion

To convert the LLaVA model into GGUF, we first need to download the models (including the CLIP model) and then perform the conversion.


```bash
sudo apt install build-essential cmake 
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp
make -j 16

python3 ./examples/llava/llava-surgery.py -m ../llava-v1.5-7b
python3 ./convert.py ../llava-v1.5-7b --outtype q8_0
```

## Grammar File
To generate the GBNF file, we can use an [online site](https://grammar.intrinsiclabs.ai/).

Our input file is listed [here](./grammar/input) and the resulting car.gbnf file is located [here](./grammar/car.gbnf).


## Inference
To run the model, we can execute

```bash
./llava-cli -m ../llava-v1.5-7b/ggml-model-q8_0.gguf --mmproj ../llava-v1.5-7b/mmproj-model-f16.gguf --temp 0.1 --grammar-file ../car.gbnf  --image ../car1.jpeg -p 'Describe the car; JSON:'
```

Note that the [q5_k model on HuggingFace](https://huggingface.co/mys/ggml_llava-v1.5-7b) did not follow the grammar rules (at all!) and the q8_0 quantized model above was the most efficient model.
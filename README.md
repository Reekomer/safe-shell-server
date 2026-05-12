# LINUX
## 1. Update system and install build tools
apt-get update && apt-get install -y build-essential cmake git curl libcurl4-openssl-dev libssl-dev

## 2. Clone the official repository
git clone https://github.com/ggml-org/llama.cpp.git
cd llama.cpp

## 3. Configure CMake for a "Fat Binary"
### This tells Nvidia's compiler (nvcc) to support everything from 
### older Pascal cards up to modern Hopper cards.
cmake -B build -DGGML_CUDA=ON -DCMAKE_CUDA_ARCHITECTURES="80;86;89;90" -DBUILD_SHARED_LIBS=OFF -DLLAMA_CURL=ON

## 4. Compile the server! (Takes about 3-5 minutes)
cmake --build build --config Release -j $(nproc) --target llama-server

# MACOS
## 1. Update system and install build tools
### macOS uses xcode-select for the base compilers (clang), and brew for cmake/git
xcode-select --install
brew update && brew install cmake git curl

## 2. Clone the official repository
git clone https://github.com/ggml-org/llama.cpp.git
cd llama.cpp

## 3. Configure CMake for Apple Metal
### On Apple Silicon, Metal is often detected automatically, but it is 
### best practice to explicitly force it on using -DGGML_METAL=ON.
### (No long list of architectures is needed for Apple Silicon!)
cmake -B build -DGGML_METAL=ON

## 4. Compile the server! (Takes about 1-2 minutes)
### macOS uses `sysctl -n hw.logicalcpu` instead of `nproc` to count CPU cores
cmake --build build --config Release -j $(sysctl -n hw.logicalcpu) --target llama-server
# Development Environments Docker

Boilerplate `Dockerfile` for running development environments inside docker container using Alpine

# Core

- Programming language
- Tmux
- Neovim, LSP, and DAP

# Installation

- `docker build -t <image name> --target dev -f <folder/Dockerfile> .`
- `docker run -it --name <container name> <image name> bash`
- open `nvim`, run `:PackerInstall` and restart `nvim`
- edit `~/.dotfiles/neovim/.config/nvim/lua/LSP/main.lua` and uncomment LSP
- edit `~/.dotfiles/neovim/.config/nvim/lua/DAP/main.lua` and uncomment DAP
- run `:TSInstall <programming language>` and exit `nvim`
- stop the docker container and commit with `docker commit <container name> <new image name>`
- ready to reuse with bind port and volume

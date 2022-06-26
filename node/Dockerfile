FROM node:17.9.1-alpine AS base
WORKDIR /root

# # update repo packages
RUN echo -e "http://dl-cdn.alpinelinux.org/alpine/edge/main\nhttp://dl-cdn.alpinelinux.org/alpine/edge/community" > /etc/apk/repositories

FROM base AS dev
# # development env
# ## utilities
RUN apk add --no-cache bash curl git

# ## install tmux, lazygit, neovim, tree-sitter, fd, ripgrep, stow, and packer
RUN apk add --no-cache tmux lazygit neovim tree-sitter tree-sitter-cli fd ripgrep stow
RUN git clone --depth 1 https://github.com/wbthomason/packer.nvim ~/.local/share/nvim/site/pack/packer/start/packer.nvim

# ## install pnpm
RUN npm i --location=global pnpm

# ## install lsp, linter and dap
RUN npm i --location=global typescript typescript-language-server eslint_d @fsouza/prettierd vscode-langservers-extracted @volar/vue-language-server svelte-language-server
# RUN git clone https://github.com/microsoft/vscode-node-debug2.git ~/.DAP/vscode-node-debug2
# RUN cd ~/.DAP/vscode-node-debug2 && npm i
# RUN cd ~/.DAP/vscode-node-debug2 && NODE_OPTIONS=--no-experimental-fetch npm run build
# for using chrome debug inside docker container, you have to add flag `--network host`
# `docker run --network host <image name> <command>`
# RUN git clone https://github.com/Microsoft/vscode-chrome-debug ~/.DAP/vscode-chrome-debug
# RUN cd ~/.DAP/vscode-chrome-debug && npm i
# RUN cd ~/.DAP/vscode-chrome-debug && npm run build
# TODO: create clean up script after build DAP
# NOTE: because DAP create large size on build image, I decide to bind volume on `compose.yml`

# ## clone dotfiles (bash, neovim, lazygit, and tmux)
RUN git clone --depth 1 https://github.com/anasrar/.dotfiles.git ~/.dotfiles && cd ~/.dotfiles && ./stow.sh bash && ./stow.sh neovim && ./stow.sh lazygit && ./stow.sh tmux

# ## compile neovim plugin
RUN nvim --headless -c 'autocmd User PackerComplete quitall' -c 'PackerSync'
RUN nvim --headless -c 'autocmd User PackerComplete quitall' -c 'PackerSync'

# ## tree-sitter parser
RUN wget https://github.com/anasrar/nvim-treesitter-parser-bin/releases/download/alpine/all.zip
RUN unzip -j all.zip *.so -d ~/.local/share/nvim/site/pack/packer/start/nvim-treesitter/parser/

# ## tmux color and UTF8
ENV TERM xterm-256color
RUN echo "alias tmux='tmux -u'" >> ~/.bashrc

# ## bashrc
RUN echo "source ~/.bashrc_personal" >> ~/.bashrc

# ## change shell to bash for tmux
RUN echo "set -g default-shell \"/bin/bash\"" >> ~/.tmux.conf
RUN echo "set -g default-command \"/bin/bash\"" >> ~/.tmux.conf

# ## login bash
CMD ["bash"]

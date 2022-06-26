FROM golang:alpine AS base
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

# ## install lsp, linter, and dap
RUN go install golang.org/x/tools/gopls@latest
RUN curl -sSfL https://raw.githubusercontent.com/golangci/golangci-lint/master/install.sh | sh -s -- -b $(go env GOPATH)/bin v1.46.2
RUN go install github.com/go-delve/delve/cmd/dlv@latest
# if you debugging from docker, you have to add flag `--privileged`
# `docker run --privileged <image name> <command>`

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

# ## change shell to bash
RUN echo "set -g default-shell \"/bin/bash\"" >> ~/.tmux.conf
RUN echo "set -g default-command \"/bin/bash\"" >> ~/.tmux.conf

# ## login bash
CMD ["bash"]
